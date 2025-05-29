```python
import cv2 as cv
import numpy as np

c1 = 40
c2 = 300
c3 = 120
c4 = 400

v1 = 60
v2 = 320
v3 = 100
v4 = 360

m1 = 120
m2 = 340
m3 = 200
m4 = 400

t1 = 40
t2 = 280
t3 = 140
t4 = 300

cc1 = 180
cc2 = 320
cc3 = 200
cc4 = 340

ca1 = 200
ca2 = 360
ca3 = 240
ca4 = 400

ll11 = 70
ll12 = 400

ll21 = 130
ll22 = 400

ll31 = 195
ll32 = 402

t11 = 180
t12 = 320
t13 = 200
t14 = 320
t15 = 190
t16 = 300

t21 = 200
t22 = 360
t23 = 220
t24 = 340
t25 = 240
t26 = 360

x = 5
y = 0

while True:
    img = img = np.ones((500,500,3), dtype=np.uint8) * 255
    
    c1 += x
    c2 += y
    c3 += x
    c4 = 400

    v1 += x
    v2 += y
    v3 += x
    v4 += y

    m1 += x
    m2 += y
    m3 += x
    m4 += y

    t1 += x
    t2 += y
    t3 += x
    t4 += y

    cc1 += x
    cc2 += y
    cc3 += x
    cc4 += y

    ca1 += x
    ca2 += y
    ca3 += x
    ca4 += y

    ll11 += x
    ll12 += y

    ll21 += x
    ll22 += y

    ll31 += x
    ll32 += y

    t11 += x
    t12 += y
    t13 += x
    t14 += y
    t15 += x
    t16 += y
    
    t21 += x
    t22 += y
    t23 += x
    t24 += y
    t25 += x
    t26 += y

    #Cabina
    cv.rectangle(img, (c1,c2), (c3,c4), (139,0,0), -1)
    #Ventana
    cv.rectangle(img, (v1,v2), (v3,v4), (1,255,255), -1)
    #Motor
    cv.rectangle(img, (m1,m2), (m3,m4), (0,165,255), -1)
    #Techito
    cv.rectangle(img, (t1,t2),(t3,t4), (0,255,255), -1)
    #Chimenea chiquita
    cv.rectangle(img, (cc1, cc2), (cc3,cc4), (0,255,255), -1)
    #Cuadro azul de adelante 
    cv.rectangle(img,(ca1,ca2), (ca3,ca4), (230,216,173), -1)
    #Llantas
    cv.circle(img, (ll11,ll12), 30, (0,0,255), -1)
    cv.circle(img, (ll21,ll22), 29, (0,0,255), -1)
    cv.circle(img, (ll31,ll32), 25, (0,0,255), -1)
    #Triangulitos
    pts = np.array([[t11,t12], [t13,t14], [t15,t16]], np.int32) 
    cv.polylines(img, [pts], True, (0,255,255), 8)
    
    pts2 = np.array([[t21,t22], [t23,t24],[t25,t26]], np.int32)
    cv.polylines(img, [pts2], True, (0,255,255), 8)
  
    cv.imshow('img', img)
    cv.waitKey(50)

cv.imshow('img', img)
cv.waitKey(0)
cv.destroyAllWindows()
```