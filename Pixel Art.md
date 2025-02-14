```python
import cv2 as cv
import numpy as np

img = np.ones((800), dtype = npuint8) * 128

#Tamaño de la imagen
for i in range(800):  
    for j in range(750): 
         #Linea 1
        if i < 50:
            if j > 50 and j < 200 or j > 550 and j < 700:
                img[i,j] = 1
        
        #Linea 2
        if i > 50 and i < 100:
            if j < 50 or j > 200 and j < 550 or j > 700:
                img[i,j] = 1  
                
        #Linea 3
        if i > 100 and i < 150:
            if j < 50 or j > 150 and j < 250 or j > 500 and j < 600 or j > 700:
                img[i,j] = 1
                
         #linea 4
        if i > 150 and i < 200:
            if  j > 50 and j < 150 or j > 600 and j < 700:
                img[i,j] = 1 
                
        #Linea 5
        if i > 200 and i < 250:
            if j > 50 and j < 100 or j > 200 and j < 250 or j > 550 and j < 600 or j > 650 and j < 700:
                img[i,j] = 1
            if j > 150 and j < 200 or j > 500 and j < 550:
                img [i,j] = 255
        
        #Linea 6
        if i > 250 and i < 300:
            if j < 50 or j > 150 and j < 250 or j > 500 and j < 600 or j > 700:
                img[i,j] = 1
                
        #Linea 7 y 11
        if i > 300 and i < 350:
            if j < 50 or j > 250 and j < 500 or j >700:
                img[i,j] = 1
                
        #Linea 8 y 10
        if i > 350 and i < 400 or i > 450 and i < 500:
            if j < 50 or j > 200 and j < 250 or j > 500 and j < 550 or j > 700:
                img[i,j] = 1
                
        #Linea 9
        if i > 400 and i < 450:
            if j < 50 or j > 200 and j < 250 or j > 300 and j < 350 or j > 400 and j < 450 or j > 500 and j < 550 or j > 700:
                img[i,j] = 1
        
        #Linea 11 
        if i > 500 and i < 550:
            if j > 50 and j < 100 or j > 250 and j < 500 or j > 650 and j < 700:
                img[i,j] = 1
                
        #Linea 12
        if i > 550 and i < 600:
            if j > 50 and  j < 100 or j > 650 and j < 700:
                img[i,j] = 1
                
        #Linea 13
        if i > 600 and i < 650:
            if j > 100 and j < 150 or j > 600 and j < 650:
                img[i,j] = 1
                
        #Linea 14
        if i > 650 and i < 700:
            if j > 100 and j < 150 or j > 200 and j < 250 or j > 500 and j < 550 or j > 600 and j < 650:
                img[i,j] = 1
        
        #Linea 15
        if i > 700 and i < 750:
            if j > 100 and j < 150 or j > 250 and j < 500 or j > 600 and j < 650:
                img[i,j] = 1
        
        #Linea 16
        if i > 750:
            if j > 100 and j < 300 or j > 450 and j < 650:
                img[i,j] = 1
                
                
cv.imshow('LINK',img)

cv.waitKey(0)
cv.destroyAllWindows()
```