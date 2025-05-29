```python
import numpy as np
import cv2 as cv

rostro = cv.CascadeClassifier('haarcascade_frontalface_alt2.xml')
cap = cv.VideoCapture(0)  # Usa '0' en lugar de '1' si solo tienes una cámara

count = 0

while True:
    ret, frame = cap.read()
    if not ret:
        break  # Si no se lee correctamente el frame, salir del bucle

    gray = cv.cvtColor(frame, cv.COLOR_BGR2GRAY)
    rostros = rostro.detectMultiScale(gray, 1.3, 5)

    for (x, y, w, h) in rostros:
        m = int(h / 2)
        cv.rectangle(frame, (x, y), (x + w, y + h), (0, 255, 0), 2)
        count += 1  # Corregido el error de 'cpunt'

    cv.imshow('Rostros', frame)

    # Salir con la tecla 'q'
    if cv.waitKey(1) & 0xFF == ord('q'):
        break

# Liberar la cámara y cerrar ventanas
cap.release()
cv.destroyAllWindows()
```