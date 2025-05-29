```python
import numpy as np
import cv2 as cv
import mediapipe as mp

# Inicializar MediaPipe Hands
mp_hands = mp.solutions.hands
hands = mp_hands.Hands(min_detection_confidence=0.5, min_tracking_confidence=0.5)

# Captura de video desde la cámara
cap = cv.VideoCapture(0)

# Parámetros para la pelota
ball_pos = np.array([320, 240], dtype=np.float32)  # Posición inicial al centro
ball_vel = np.array([5, 5], dtype=np.float32)  # Velocidad inicial en x, y

while True:
    ret, frame = cap.read()
    if not ret:
        break

    # Obtener dimensiones correctamente (altura, anchura)
    height, width = frame.shape[:2]
    
    frame = cv.flip(frame, 1)  # Voltear la imagen para reflejar la vista del usuario
    frame_rgb = cv.cvtColor(frame, cv.COLOR_BGR2RGB)

    # Detección de manos
    results = hands.process(frame_rgb)

    # Verificar si hay manos detectadas
    if results.multi_hand_landmarks:
        for hand_landmarks in results.multi_hand_landmarks:
            for landmark in hand_landmarks.landmark:
                hx, hy = int(landmark.x * width), int(landmark.y * height)  # Ajuste correcto

                # Si la pelota está cerca de la mano, cambiar dirección (rebote)
                if np.linalg.norm(ball_pos - np.array([hx, hy])) < 40:
                    ball_vel += np.random.uniform(-2, 2, size=2)  # Variación aleatoria en el rebote
                    ball_vel *= -1

    # Actualizar posición de la pelota
    ball_pos += ball_vel

    # Rebotar en los bordes de la pantalla (corrección de dimensiones)
    if ball_pos[0] <= 20 or ball_pos[0] >= width - 20:
        ball_vel[0] *= -1
    if ball_pos[1] <= 20 or ball_pos[1] >= height - 20:
        ball_vel[1] *= -1

    # Dibujar la pelota
    cv.circle(frame, (int(ball_pos[0]), int(ball_pos[1])), 20, (0, 255, 0), -1)

    # Dibujar un borde para visualizar el área de juego
    cv.rectangle(frame, (20, 20), (width-20, height-20), (234, 43, 34), 5)

    # Mostrar la imagen
    cv.imshow('Pelota en movimiento', frame)

    # Salir con la tecla 'q'
    if cv.waitKey(30) & 0xFF == ord('q'):
        break

cap.release()
hands.close()  # Liberar MediaPipe correctamente
cv.destroyAllWindows()
```