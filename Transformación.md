```python
import cv2 as cv
import numpy as np
import math

img = cv.imread("gatito.jpg",0)
x,y = img.shape

scale_x, scale_y = 2,2

scaled_img = np.zeros((int(x * scale_y), int (y * scale_x)), dtype=np.uint8)

for i in range(x):
    for j in range(y):
        scaled_img[i*2, j*2] = img [i,j]
        
center = (y//2, x//2)
angle = 45
M = cv.getRotationMatrix2D(center,angle,1.0)
rotated_img = cv.warpAffine(scaled_img,M,(y,x))

cv.imshow('Imagen rotada', img)
cv.imshow('Imagen original', img)
cv.imshow('Imagen escalada (modo row)', scaled_img)
cv.waitKey(0)
cv.destroyAllWindows()
```