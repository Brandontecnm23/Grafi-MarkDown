``` python
import numpy as np
from OpenGL.GL import *
from OpenGL.GLU import *
from OpenGL.GLUT import *
from PIL import Image
import sys

#Barquito de minecraft todo bonito que se mueve con las flechas - VERSIÓN CORREGIDA
class MinecraftBoatMinimal:
    def __init__(self):
        self.x, self.y, self.z = 0.0, -0.5, 0.0
        self.rotation_y = 0.0
        self.target_rotation = 0.0  # Para rotación suave
        self.camera_distance = 10.0
        self.move_speed = 0.2
        self.rotation_speed = 5.0 
        self.keys = {
            'w': False, 's': False,
            'up': False, 'down': False, 'left': False, 'right': False
        }
        self.boat_texture = None  # ARREGLADO: Indentación correcta
        self.water_texture = None

    def load_textures(self):
        """Load textures from uploaded image files with improved scaling"""
        try:
            self.water_texture = self.load_texture("A.jpeg")
            self.boat_texture = self.load_texture("R.jpeg")
            print("Textures loaded successfully!")
        except Exception as e:
            print(f"Error loading textures: {e}")
            print("Running without textures...")

    def load_texture(self, filename):
        """Load a single texture from file with proper scaling"""
        try: 
            image = Image.open(filename)
            image = image.convert("RGB")
             
            original_width, original_height = image.size
            print(f"Original image size for {filename}: {original_width}x{original_height}")
             
            def next_power_of_2(n):
                power = 1
                while power < n:
                    power *= 2
                return min(power, 1024)   
            
            new_width = next_power_of_2(original_width)
            new_height = next_power_of_2(original_height)
             
            if new_width != original_width or new_height != original_height:
                print(f"Resizing {filename} from {original_width}x{original_height} to {new_width}x{new_height}")
                image = image.resize((new_width, new_height), Image.Resampling.LANCZOS)
             
            image = image.transpose(Image.FLIP_TOP_BOTTOM)
            img_data = np.array(image, dtype=np.uint8)
             
            texture_id = glGenTextures(1)
            glBindTexture(GL_TEXTURE_2D, texture_id)
             
            glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_REPEAT)
            glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_REPEAT)
             
            glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR_MIPMAP_LINEAR)
            glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR)
             
            glTexImage2D(GL_TEXTURE_2D, 0, GL_RGB, image.width, image.height, 
                        0, GL_RGB, GL_UNSIGNED_BYTE, img_data)
             
            gluBuild2DMipmaps(GL_TEXTURE_2D, GL_RGB, image.width, image.height, 
                             GL_RGB, GL_UNSIGNED_BYTE, img_data)
            
            print(f"Loaded texture: {filename} (Final size: {image.width}x{image.height})")
            return texture_id
        except FileNotFoundError:
            print(f"Texture file not found: {filename}")
            return None
        except Exception as e:
            print(f"Failed to load texture {filename}: {e}")
            return None

    def update_camera(self):
        """Configura la cámara para enfocarse en el bote correctamente"""
        glLoadIdentity()
        gluLookAt(self.x, self.y + 2, self.z + self.camera_distance, 
                  self.x, self.y, self.z, 
                  0, 1, 0)

    def update_movement(self):
        """Handle movement based on pressed keys - ARREGLADO"""
        # Manejo del zoom de cámara
        if self.keys.get('w', False):
            self.camera_distance = max(3.0, self.camera_distance - self.move_speed)
        if self.keys.get('s', False):
            self.camera_distance = min(20.0, self.camera_distance + self.move_speed)
         
        # Movimiento del barco con rotación suave
        if self.keys.get('left', False):   
            self.x -= self.move_speed
            self.target_rotation = 90   
        elif self.keys.get('right', False):  
            self.x += self.move_speed
            self.target_rotation = -90   
        elif self.keys.get('up', False):   
            self.z -= self.move_speed
            self.target_rotation = 0   
        elif self.keys.get('down', False):   
            self.z += self.move_speed
            self.target_rotation = 180
        
        # NUEVO: Rotación suave para evitar que se vea "chueco"
        diff = self.target_rotation - self.rotation_y
        if abs(diff) > 180:
            if diff > 0:
                diff -= 360
            else:
                diff += 360
        
        if abs(diff) > 1:
            self.rotation_y += diff * 0.1  # Interpolación suave
            if self.rotation_y >= 360:
                self.rotation_y -= 360
            elif self.rotation_y < 0:
                self.rotation_y += 360

    def draw_boat(self):
        """Dibuja un bote realista usando primitivas de OpenGL con texturas mejoradas"""
        glPushMatrix()
        glTranslatef(self.x, self.y, self.z)
        glRotatef(self.rotation_y, 0, 1, 0)

        if self.boat_texture:
            glEnable(GL_TEXTURE_2D)
            glBindTexture(GL_TEXTURE_2D, self.boat_texture)
            glColor3f(1.0, 1.0, 1.0)  
        else:
            glDisable(GL_TEXTURE_2D)
            glColor3f(0.6, 0.3, 0.1)  

        # Casco principal
        glPushMatrix()
        glTranslatef(0, -0.2, 0)
        glScalef(2.5, 0.4, 1.2)
        self.draw_boat_hull()
        glPopMatrix()

        # Proa (frente)
        glPushMatrix()
        glTranslatef(2.3, -0.15, 0)
        glScalef(0.5, 0.3, 0.8)
        self.draw_triangular_prism()
        glPopMatrix()

        # Popa (atrás)
        glPushMatrix()
        glTranslatef(-2.3, -0.1, 0)
        glScalef(0.4, 0.5, 1.0)
        self.draw_cube()
        glPopMatrix()

        # Asientos
        glPushMatrix()
        glTranslatef(-0.8, 0.1, 0)
        glScalef(0.3, 0.2, 0.9)
        self.draw_cube()
        glPopMatrix()

        glPushMatrix()
        glTranslatef(0.8, 0.1, 0)
        glScalef(0.3, 0.2, 0.9)
        self.draw_cube()
        glPopMatrix()

        # Bordes
        glPushMatrix()
        glTranslatef(0, 0.0, 0.6)
        glScalef(2.0, 0.3, 0.1)
        self.draw_cube()
        glPopMatrix()

        glPushMatrix()
        glTranslatef(0, 0.0, -0.6)
        glScalef(2.0, 0.3, 0.1)
        self.draw_cube()
        glPopMatrix()

        glDisable(GL_TEXTURE_2D)
        glPopMatrix()

    def draw_cube(self):
        """Dibuja un cubo básico con coordenadas de textura mejoradas - ARREGLADO"""
        tex_scale = 1.0  # ARREGLADO: Variable definida localmente
        
        glBegin(GL_QUADS)

        vertices = np.array([
            [-1, -1, -1], [1, -1, -1], [1, 1, -1], [-1, 1, -1],  # Front
            [-1, -1, 1], [1, -1, 1], [1, 1, 1], [-1, 1, 1]       # Back
        ])
        
        tex_coords = [
            [0, 0], [tex_scale, 0], [tex_scale, tex_scale], [0, tex_scale]
        ]
        
        faces = [
            [0, 1, 2, 3],  # Front
            [4, 5, 6, 7],  # Back
            [0, 1, 5, 4],  # Bottom
            [2, 3, 7, 6],  # Top
            [0, 3, 7, 4],  # Left
            [1, 2, 6, 5]   # Right
        ]
        
        for face in faces:
            for i, vertex in enumerate(face):
                glTexCoord2f(*tex_coords[i])
                glVertex3f(*vertices[vertex])
        
        glEnd()

    def draw_boat_hull(self):
        """Dibuja el casco del bote con coordenadas de textura mejoradas"""
        glBegin(GL_QUADS)
        
        vertices = np.array([
            [-1, 1, -1], [1, 1, -1], [1, 1, 1], [-1, 1, 1],
            [-0.7, -1, -0.7], [0.7, -1, -0.7], [0.7, -1, 0.7], [-0.7, -1, 0.7]
        ])
        
        tex_scale = 1.0  
        tex_coords = [[0, 0], [tex_scale, 0], [tex_scale, tex_scale], [0, tex_scale]]
        
        # Caras del casco
        faces = [
            [0, 1, 2, 3],  # Top
            [4, 5, 6, 7],  # Bottom
            [0, 1, 5, 4],  # Front
            [2, 3, 7, 6],  # Back
            [0, 3, 7, 4],  # Left
            [1, 2, 6, 5]   # Right
        ]
        
        for face in faces:
            for i, vertex in enumerate(face):
                glTexCoord2f(*tex_coords[i])
                glVertex3f(*vertices[vertex])
        
        glEnd()

    def draw_triangular_prism(self):
        """Dibuja la parte frontal triangular del bote con texturas"""
        glBegin(GL_QUADS)
        vertices = [
            [-1, -1, -1], [0.5, -1, -1], [0.5, 1, -1], [-1, 1, -1],
            [-1, -1, 1], [0.5, -1, 1], [0.5, 1, 1], [-1, 1, 1]
        ]
        
        tex_scale = 0.3
        tex_coords = [[0, 0], [tex_scale, 0], [tex_scale, tex_scale], [0, tex_scale]]
        faces = [
            [0, 1, 2, 3], [4, 5, 6, 7], 
            [0, 1, 5, 4], [2, 3, 7, 6], 
            [0, 3, 7, 4], [1, 2, 6, 5]
        ]
        
        for face in faces:
            for i, vertex in enumerate(face):
                glTexCoord2f(*tex_coords[i])
                glVertex3f(*vertices[vertex])
        
        glEnd()

    def draw_water(self):
        """Dibuja el agua con textura mejorada (A.jpeg)"""
        if self.water_texture:
            glEnable(GL_TEXTURE_2D)
            glBindTexture(GL_TEXTURE_2D, self.water_texture)
            glColor3f(0.8, 0.9, 1.0)  
        else:
            glDisable(GL_TEXTURE_2D)
            glColor3f(0.1, 0.3, 0.9)  
            
        glBegin(GL_QUADS)
        water_size = 50
        water_vertices = [
            [-water_size, -1.1, -water_size], 
            [water_size, -1.1, -water_size], 
            [water_size, -1.1, water_size], 
            [-water_size, -1.1, water_size]
        ]
        
        water_tex_repeat = 10 
        tex_coords = [
            [0, 0], 
            [water_tex_repeat, 0], 
            [water_tex_repeat, water_tex_repeat], 
            [0, water_tex_repeat]
        ]
        
        for i, vertex in enumerate(water_vertices):
            glTexCoord2f(*tex_coords[i])
            glVertex3f(*vertex)
        glEnd()
        
        glDisable(GL_TEXTURE_2D)

def reshape(width, height):
    """Ajusta la perspectiva y el viewport para que el bote se vea correctamente"""
    if height == 0:
        height = 1
    glViewport(0, 0, width, height)
    glMatrixMode(GL_PROJECTION)
    glLoadIdentity()
    gluPerspective(45, width / height, 0.1, 100.0)
    glMatrixMode(GL_MODELVIEW)

def keyboard(key, x, y):
    """Handle key press events - ARREGLADO"""
    global boat
    
    try:
        if isinstance(key, bytes):
            key_char = key.decode('utf-8').lower()
            key_code = ord(key_char)
        else:
            key_code = key
            key_char = chr(key).lower()
        
        if key_code == 27:  # ESC key
            print("Exiting...")
            sys.exit(0)
        
        # SIMPLIFICADO: Solo manejo de minúsculas
        if key_char in boat.keys:
            boat.keys[key_char] = True
        
    except (ValueError, UnicodeDecodeError) as e:
        print(f"Special key pressed: {key} (error: {e})")
    
    glutPostRedisplay()

def keyboard_up(key, x, y):
    """Handle key release events - ARREGLADO"""
    global boat
    
    try:
        if isinstance(key, bytes):
            key_char = key.decode('utf-8').lower()
        else:
            key_char = chr(key).lower()
        
        if key_char in boat.keys:
            boat.keys[key_char] = False
            
    except (ValueError, UnicodeDecodeError) as e:
        print(f"Special key released: {key} (error: {e})")
    
    glutPostRedisplay()

def special_keys(key, x, y):
    """Handle special keys (arrows) - ARREGLADO"""
    global boat
    
    if key == GLUT_KEY_UP:
        boat.keys['up'] = True
    elif key == GLUT_KEY_DOWN:
        boat.keys['down'] = True
    elif key == GLUT_KEY_LEFT:
        boat.keys['left'] = True
    elif key == GLUT_KEY_RIGHT:
        boat.keys['right'] = True
    
    glutPostRedisplay()

def special_keys_up(key, x, y):
    """Handle special key releases - ARREGLADO"""
    global boat
    
    if key == GLUT_KEY_UP:
        boat.keys['up'] = False
    elif key == GLUT_KEY_DOWN:
        boat.keys['down'] = False
    elif key == GLUT_KEY_LEFT:
        boat.keys['left'] = False
    elif key == GLUT_KEY_RIGHT:
        boat.keys['right'] = False
    
    glutPostRedisplay()

def display():
    """Función de renderizado - ARREGLADO"""
    global boat
    
    glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT)
    
    boat.update_movement()
    boat.update_camera()
    boat.draw_water()
    boat.draw_boat()
    
    glutSwapBuffers()

def timer(value):
    """Animation timer for smooth movement"""
    glutPostRedisplay()
    glutTimerFunc(16, timer, 0)  # ~60 FPS

boat = None

def main():
    global boat
    
    try:
        glutInit(sys.argv)
        glutInitDisplayMode(GLUT_DOUBLE | GLUT_RGB | GLUT_DEPTH)
        glutInitWindowSize(1000, 800)
        window = glutCreateWindow(b"Bote de Minecraft - OpenGL (VERSION CORREGIDA)")

        glEnable(GL_DEPTH_TEST)
        glClearColor(0.5, 0.8, 1.0, 1.0)  # Sky blue background
        
        glEnable(GL_LIGHTING)
        glEnable(GL_LIGHT0)
        
        light_pos = [5.0, 5.0, 5.0, 1.0]
        light_ambient = [0.3, 0.3, 0.3, 1.0]
        light_diffuse = [0.8, 0.8, 0.8, 1.0]
        light_specular = [1.0, 1.0, 1.0, 1.0]
        
        glLightfv(GL_LIGHT0, GL_POSITION, light_pos)
        glLightfv(GL_LIGHT0, GL_AMBIENT, light_ambient)
        glLightfv(GL_LIGHT0, GL_DIFFUSE, light_diffuse)
        glLightfv(GL_LIGHT0, GL_SPECULAR, light_specular)
        
        glEnable(GL_COLOR_MATERIAL)
        glColorMaterial(GL_FRONT_AND_BACK, GL_AMBIENT_AND_DIFFUSE)

        boat = MinecraftBoatMinimal()
        boat.load_textures()
        
        glutDisplayFunc(display)
        glutReshapeFunc(reshape)
        glutKeyboardFunc(keyboard)
        glutKeyboardUpFunc(keyboard_up)
        glutSpecialFunc(special_keys)
        glutSpecialUpFunc(special_keys_up)
        
        glutTimerFunc(16, timer, 0)
        
        print("\n=== CONTROLES CORREGIDOS ===")
        print("W - Acercar cámara al bote")
        print("S - Alejar cámara del bote") 
        print("Flecha ARRIBA - Mover bote hacia ADELANTE")
        print("Flecha ABAJO - Mover bote hacia ATRÁS")
        print("Flecha IZQUIERDA - Mover bote hacia la IZQUIERDA")
        print("Flecha DERECHA - Mover bote hacia la DERECHA")
        print("ESC - Salir")
        print("\n=== ARREGLOS REALIZADOS ===")
        print("✓ Indentación corregida (línea 15)")
        print("✓ Variable tex_scale definida en draw_cube()")
        print("✓ Rotación suave para evitar movimiento 'chueco'")
        print("✓ Manejo de teclas simplificado")
        print("✓ Interpolación suave entre rotaciones")
        print("✓ Código más limpio y organizado")
        
        glutMainLoop()
        
    except Exception as e:
        print(f"Error initializing OpenGL: {e}")
        print("Make sure you have PyOpenGL installed: pip install PyOpenGL PyOpenGL_accelerate")
        sys.exit(1)

if __name__ == "__main__":
    main()
``` 