```python
import numpy as np
import cv2


def generar_punto_elipse(a, b, t):
    x = int(a * 2* np.cos(t) + 200) 
    y = int(b * np.sin(t) + 200)
    return (x, y)


img_width, img_height = 600, 600


imagen = np.zeros((img_height, img_width, 3), dtype=np.uint8)

a = 200  
b = 100  
num_puntos = 1000

t_vals = np.linspace(0, 2 * np.pi, num_puntos)

for t in t_vals:
   
    imagen = np.zeros((img_height, img_width, 3), dtype=np.uint8)
    
    punto = generar_punto_elipse(a, b, t)
    
    cv2.circle(imagen, punto, radius=100, color=(0, 255, 0), thickness=-1)
    
    
    for t_tray in t_vals:
        pt_tray = generar_punto_elipse(a, b, t_tray)
        cv2.circle(imagen, pt_tray, radius=1, color=(255, 255, 255), thickness=-1)
 
    cv2.imshow('img', imagen)
    
    cv2.waitKey(10)

cv2.destroyAllWindows()
```