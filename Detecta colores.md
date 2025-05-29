```python
import cv2 as cv

img = cv.imread('OIP.jpg', 1)
hsv = cv.cvtColor(img, cv.COLOR_BGR2HSV)

#Color rojo
uba = (10, 255, 255)   # Límite superior
ubb = (0, 50, 50)      # Límite inferior

uba2 = (180, 255, 255)  # Límite superior
ubb2 = (170, 50, 50)    # Límite inferior

#Color azul
uba3 = (110,255,255)
ubb3 = (90,50,50)

#Color amarillo
uba4 = (40,255,255)
ubb4 = (20,100,100)

mask1 = cv.inRange(hsv, ubb, uba)
mask2 = cv.inRange(hsv, ubb2, uba2)

mask3 = cv.inRange(hsv,ubb3,uba3)
mask4 = cv.inRange(hsv,ubb4,uba4)

mask = mask1+mask2+mask3+mask4
res = cv.bitwise_and(img, img, mask=mask)



cv.imshow('mask3', mask3)
cv.imshow('mask4', mask4)

cv.imshow('mask1', mask1)
cv.imshow('mask2', mask2)
cv.imshow('mask', mask)
cv.imshow('r',res)

# img = cv.imread('yo.jpg', 1)
# hsv = cv.cvtColor(img, cv.COLOR_BGR2HSV)
# uba=(0, 255, 255)
# ubb=(10, 40 ,40)
# uba2=(170, 255, 255)
# ubb2=(180, 40,40)
# mask1 = cv.inRange(hsv, ubb, uba)
# mask2 = cv.inRange(hsv, ubb2, uba2)
# mask = mask1+mask2
# res = cv.bitwise_and(img, img, mask=mask)
# cv.imshow('mask1', mask1)
# cv.imshow('mask2', mask2)
# cv.imshow('mask', mask)
# cv.imshow('r',res)

# img = cv.imread('tu.jpg', 1)
# hsv = cv.cvtColor(img, cv.COLOR_BGR2HSV)
# uba=(150, 255, 255)
# ubb=(130, 40 ,40)
# uba2=(130, 255, 255)
# ubb2=(170, 40,40)
# mask1 = cv.inRange(hsv, ubb, uba)
# mask2 = cv.inRange(hsv, ubb2, uba2)
# mask = mask1+mask2
# res = cv.bitwise_and(img, img, mask=mask)
# cv.imshow('mask1', mask1)
# cv.imshow('mask2', mask2)
# cv.imshow('mask', mask)
# cv.imshow('r',res)

# img = cv.imread('si.jpg', 1)
# hsv = cv.cvtColor(img, cv.COLOR_BGR2HSV)
# uba=(150, 255, 255)
# ubb=(130, 40 ,40)
# uba2=(130, 255, 255)
# ubb2=(170, 40,40)
# mask1 = cv.inRange(hsv, ubb, uba)
# mask2 = cv.inRange(hsv, ubb2, uba2)
# mask = mask1+mask2
# res = cv.bitwise_and(img, img, mask=mask)
# cv.imshow('mask1', mask1)
# cv.imshow('mask2', mask2)
# cv.imshow('mask', mask)
# cv.imshow('r',res)

# img = cv.imread('no.jpg', 1)
# hsv = cv.cvtColor(img, cv.COLOR_BGR2HSV)
# uba=(150, 255, 255)
# ubb=(130, 40 ,40)
# uba2=(130, 255, 255)
# ubb2=(170, 40,40)
# mask1 = cv.inRange(hsv, ubb, uba)
# mask2 = cv.inRange(hsv, ubb2, uba2)
# mask = mask1+mask2
# res = cv.bitwise_and(img, img, mask=mask)
# cv.imshow('mask1', mask1)
# cv.imshow('mask2', mask2)
# cv.imshow('mask', mask)
# cv.imshow('r',res)


cv.waitKey(0)
cv.destroyAllWindows
```