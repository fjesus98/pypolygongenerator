# -*- coding: utf-8 -*-

import numpy as np
import pandas as pd
from dxfwrite import DXFEngine as dxf

ft = input("""Localizacion del archivo y tipo de metodo

[1] Angulos internos
[2] Concervacion de azimut

""") # "Datos de Campo2.csv",1

ci = input("Coordenada inicial: ") # 623629.18,2125781.57
rdc = input("Radiaciones Si[1],No[2]: ")

def cal_az(num):
    while num > 360: num = num - 360
    return(num)

def cal_er(grd):
    err_A = (len(grd)-2)*180 - sum(grd)
    err_A = int(err_A*3600)
    comp = grd + err_A/len(grd)
    return comp,err_A

def cal_rb(gr):
    rm = []
    for i in gr:
        grd = int(i),int((i-int(i))*60),int(((i-int(i))*60-int((i-int(i))*60))*60)
        if i < 90: rm.append("%i %i %i NE" % (grd[0],grd[1],grd[2]))
        if i >= 90 and i < 180: rm.append("%i %i %i SE" % (grd[0]-90,grd[1],grd[2]))
        if i >= 180 and i < 270: rm.append("%i %i %i SW" % (grd[0]-180,grd[1],grd[2]))
        if i >= 270 and i < 360: rm.append("%i %i %i NW" % (grd[0]-270,grd[1],grd[2]))
    return pd.DataFrame(rm,columns=["Rumbo"])

def cal_me(loc,tipo):

    df = pd.read_csv(loc)

    if tipo == 1:
        az = input("Azimut inicial [Grados,Minutos,Segundos]: ") # 4,18,0
        grados,err_A = cal_er(df["Grados"] + df["Minutos"]/60. + df["Segundos"]/3600.)
        azimut = az[0] + az[1]/60. + az[2]/3600.
        grados[0] = cal_az(grados[0] + azimut)
        for i in range(len(grados)-1):
            grados[i+1] = cal_az(grados[i] + 180 + grados[i+1])
        
        print "Error angular:               ", err_A
        return df,grados,err_A,cal_rb(grados)
    
    elif tipo == 2:
        grados = df["Grados"] + df["Minutos"]/60. + df["Segundos"]/3600.
        return df,grados,0,cal_rb(grados)

cd,gr,err_A,rb = cal_me(ft[0],ft[1])

def cal_rb2(x1,y1,x2,y2):
    if y2-y1 == 0:
        if x2>x1: return '90 0 0 NE'
        else: return '90 0 0 NW'
    else:
        xy = (x2-x1)/(y2-y1)
        if xy < 0: xy = xy*(-1)
        i = np.arctan(xy)*180/np.pi
        if x2 < x1: rx = "W"
        if y2 < y1: ry = "S"
        if x2 > x1: rx = "E"
        if y2 > y1: ry = "N"
        grd = (int(i),int((i-int(i))*60),int(((i-int(i))*60-int((i-int(i))*60))*60),ry,rx)
        grd = "%i %i %i %s%s" % grd
        return grd

def cal_es(df):
    if df < 100: return 100
    elif df < 200: return 200
    elif df < 500: return 500
    elif df < 750: return 750
    elif df < 1000: return 1000
    elif df < 1500: return 1500
    elif df < 2000: return 2000
    elif df < 2500: return 2500
    elif df < 3000: return 3000
    elif df < 5000: return 5000
    elif df < 10000: return 10000

def cal_cd(xy,rd=None):

    plano = dxf.drawing('plano.dxf')
    plano.add_layer("PA", color=3, linetype='DASHED')
    plano.add(dxf.polyline(xy,layer="PA"))

    if rd == None:
        ar = np.array(xy).T
        cx = (max(ar[0]) + min(ar[0]))/2
        cy = (max(ar[1]) + min(ar[1]))/2
        dx = (max(ar[0]) - cx)/0.0925
        dy = (max(ar[1]) - cy)/0.108

    else:
        ar = np.array(rd).T
        plano.add_layer("PR", color=2)
        plano.add(dxf.polyline(rd,layer="PR"))
        cx = (max(ar[0]) + min(ar[0]))/2
        cy = (max(ar[1]) + min(ar[1]))/2
        dx = (max(ar[0]) - cx)/0.0925
        dy = (max(ar[1]) - cy)/0.108

    med = np.array((0.0925,0.098,0.032,0.108,0.118,0.118,0.235,0.29))*cal_es(max(dx,dy))

    plano.add(dxf.rectangle((cx-med[0],cy-med[3]),med[0]*2,med[3]*2))
    plano.add(dxf.rectangle((cx+med[1],cy-med[3]),med[2]*2,med[3]*2))
    plano.add(dxf.rectangle((cx-med[4],cy-med[5]),med[7],med[6]))
    
    print "Escala utilizada en el plano: ", cal_es(max(dx,dy))

    plano.save()

def cal_pr(d,az):
    Px = d*np.sin(np.radians(az))
    Py = d*np.cos(np.radians(az))
    return(Px,Py)

px,py = cal_pr(cd["Distancia"],gr)
Ex,Ey,Per = sum(px),sum(py),sum(cd["Distancia"])
ErrL = np.sqrt(Ex**2+Ey**2)
Pres = 1/(ErrL/Per)

PCx = px - cd["Distancia"]*Ex/Per
PCy = py - cd["Distancia"]*Ey/Per

cd["X"],cd["Y"] = ci[0] + PCx[0], ci[1] + PCy[0]
xy = [[ci[0], ci[1]],[ci[0], ci[1]]]

for i in range(len(cd)-1):
    cd.loc[i+1,"X"] = cd.loc[i,"X"] + PCx[i+1]
    cd.loc[i+1,"Y"] = cd.loc[i,"Y"] + PCy[i+1]
    xy.insert(i+1,[cd.loc[i,"X"],cd.loc[i,"Y"]])

cr = []

for i in range(len(cd)):
    cr.append([cd.loc[i,"Est"],cd.loc[i,"PV"],round(cd.loc[i,"Distancia"],2),
               rb.loc[i,"Rumbo"],round(cd.loc[i,"X"],2),round(cd.loc[i,"Y"],2)])

cf = pd.DataFrame(cr,columns=["Est","PV","Distancia","Rumbo","X","Y"])

print "Precicion del levantamiento: ", Pres, """
Error lineal:                """, ErrL

if rdc == 1:
    ag = input("Localizacion del archivo: ") # "Radiaciones.csv"
    rd = pd.read_csv(ag)

    rd["Az"] = rd["Grados"] + rd["Minutos"]/60. + rd["Segundos"]/3600.
    rd["X"],rd["Y"],cr = 0,0,[]

    for i in range(len(rd)):
        for j in range(len(cf)):
            if rd.loc[i,"Est"] == cf.loc[j,"Est"]:
                rd.loc[i,"Az"] = cal_az(rd.loc[i,"Az"]+az[j])
                PX,PY = cal_pr(rd.loc[i,"Distancia"],rd.loc[i,"Az"])
                rd.loc[i,"X"] = XY[j][0] + PX
                rd.loc[i,"Y"] = XY[j][1] + PY

    XY = zip(rd["X"].tolist(),rd["Y"].tolist() + [rd.loc[0,"X"],rd.loc[0,"Y"]])

    for i in range(len(rd)-1):
        dist = np.sqrt((rd.loc[i+1,"X"] - rd.loc[i,"X"])**2 + (rd.loc[i+1,"Y"] - rd.loc[i,"Y"])**2)
        azmt = cal_rb2(rd.loc[i,"X"],rd.loc[i,"Y"],rd.loc[i+1,"X"],rd.loc[i+1,"Y"])
        cr.append([i,i+1,round(dist,2),azmt,rd.loc[i+1,"X"],rd.loc[i+1,"Y"]])

    cr[-1][1] = 0
    cr = pd.DataFrame(cr,columns=["Est","PV","Distancia","Rumbo","X","Y"])

    cal_cd(xy,rd=XY)
    cr.to_csv("Cuadro de construccion.csv",index=False)

elif rdc == 2:
    cal_cd(xy)
    cf.to_csv("Cuadro de construccion.csv",index=False)
