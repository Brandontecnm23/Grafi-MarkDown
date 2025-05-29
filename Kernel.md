```python
import cv2 as cv
import numpy as np

# Cargar imagen en escala de grises
img = cv.imread('gatito.jpg', 0)

# Escalar la imagen usando interpolación
scale_img = cv.resize(img, None, fx=2, fy=2, interpolation=cv.INTER_LINEAR)

# Aplicar convolución con un filtro de suavizado (Kernel)
kernel = np.array([[0.3, 0.3, 0.3], [0.3, 0.3, 0.3], [0.3, 0.3, 0.3]], dtype=np.float32)
convol_img = cv.filter2D(scale_img, -1, kernel)

# Mostrar las imágenes
cv.imshow('Imagen original', img)
cv.imshow('Imagen escalada', scale_img)
cv.imshow('Imagen convolucionada', convol_img)
cv.waitKey(0)
cv.destroyAllWindows()
```