 
import pygame
import random

# Inicialización de pygame
pygame.init()

# Dimensiones de la ventana del juego
WIDTH = 800
HEIGHT = 600

# Colores
BLACK = (0, 0, 0)
WHITE = (255, 255, 255)

# Crear la ventana del juego
window = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Five Nights at Freddy's + Mario Bros")

# Clase del personaje principal (Mario)
class Mario(pygame.sprite.Sprite):
    def __init__(self):
        pygame.sprite.Sprite.__init__(self)
        self.image = pygame.image.load("mario.png")
        self.rect = self.image.get_rect()
        self.rect.centerx = WIDTH // 2
        self.rect.bottom = HEIGHT - 10
        self.speed_x = 0

    def update(self):
        self.speed_x = 0
        keystate = pygame.key.get_pressed()
        if keystate[pygame.K_LEFT]:
            self.speed_x = -5
        if keystate[pygame.K_RIGHT]:
            self.speed_x = 5
        self.rect.x += self.speed_x

        # Limitar movimiento del personaje dentro de la ventana
        if self.rect.right > WIDTH:
            self.rect.right = WIDTH
        if self.rect.left < 0:
            self.rect.left = 0

# Clase de los enemigos (animatrónicos de Five Nights at Freddy's)
class Animatronic(pygame.sprite.Sprite):
    def __init__(self):
        pygame.sprite.Sprite.__init__(self)
        animatronics = ["freddy.png", "bonnie.png", "chica.png", "foxy.png"]
        self.image = pygame.image.load(random.choice(animatronics))
        self.rect = self.image.get_rect()
        self.rect.x = random.randrange(WIDTH - self.rect.width)
        self.rect.y = random.randrange(-100, -40)
        self.speed_y = random.randrange(1, 3)

    def update(self):
        self.rect.y += self.speed_y
        if self.rect.top > HEIGHT + 10:
            self.rect.x = random.randrange(WIDTH - self.rect.width)
            self.rect.y = random.randrange(-100, -40)
            self.speed_y = random.randrange(1, 3)

# Inicialización del jugador (Mario)
mar = Mario()
all_sprites = pygame.sprite.Group()
all_animatronics = pygame.sprite.Group()
all_sprites.add(mar)

for _ in range(8):
    animatronic = Animatronic()
    all_sprites.add(animatronic)
    all_animatronics.add(animatronic)

# Bucle principal del juego
running = True
clock = pygame.time.Clock()
while running:
    clock.tick(60)  # FPS limitado a 60

    # Eventos
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

    # Actualizar
    all_sprites.update()

    # Verificar colisiones entre Mario y los animatrónicos
    hits = pygame.sprite.spritecollide(mar, all_animatronics, False)
    if hits:
        running = False

    # Dibujar en la ventana del juego
    window.fill(BLACK)
    all_sprites.draw(window)
    pygame.display.flip()

# Salir del juego
pygame.quit()
