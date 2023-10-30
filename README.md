import pygame
import sys

# Inicialización de pygame
pygame.init()

# Configuración de la pantalla
SCREEN_WIDTH = 800
SCREEN_HEIGHT = 600
screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
pygame.display.set_caption("Juego de Lucha con Barras de Vida")

# Colores
WHITE = (255, 255, 255)
RED = (255, 0, 0)
BLUE = (0, 0, 255)

# Jugadores
player1_x, player1_y = 100, 450  # Ajustar la posición vertical
player2_x, player2_y = 600, 450  # Ajustar la posición vertical
player1_speed = 5
player2_speed = 5

# Disparos de los jugadores
player1_bullets = []
player2_bullets = []
bullet_speed = 10

# Barras de vida
player1_life = 100
player2_life = 100
life_reduction = 10  # Reducción de vida por disparo

# Disparos requeridos para eliminar a un jugador
required_hits = 3
player1_hits = 0
player2_hits = 0

# Función para disparar
def shoot(player_x, player_y, bullets):
    bullet = pygame.Rect(player_x, player_y + 20, 10, 5)
    bullets.append(bullet)

# Cargar el fondo (si el archivo "fondo.png" está en la misma carpeta)
try:
    background = pygame.image.load("fondo.jpg")
    background = pygame.transform.scale(background, (SCREEN_WIDTH, SCREEN_HEIGHT))
except pygame.error:
    background = pygame.Surface((SCREEN_WIDTH, SCREEN_HEIGHT))
    background.fill(WHITE)



# Bucle principal del juego
running = True
shoot_cooldown = 0  # Tiempo de enfriamiento entre disparos

while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

    keys = pygame.key.get_pressed()

    # Movimiento del jugador 1
    if keys[pygame.K_a]:
        player1_x -= player1_speed
    if keys[pygame.K_d]:
        player1_x += player1_speed
    if keys[pygame.K_w]:
        player1_y -= player1_speed
    if keys[pygame.K_s]:
        player1_y += player1_speed

    # Movimiento del jugador 2
    if keys[pygame.K_LEFT]:
        player2_x -= player2_speed
    if keys[pygame.K_RIGHT]:
        player2_x += player2_speed
    if keys[pygame.K_UP]:
        player2_y -= player2_speed
    if keys[pygame.K_DOWN]:
        player2_y += player2_speed

    # Disparos de los jugadores
    if keys[pygame.K_SPACE] and shoot_cooldown == 0:
        shoot(player1_x, player1_y, player1_bullets)
        shoot_cooldown = 30  # Tiempo de enfriamiento entre disparos

    if keys[pygame.K_RETURN] and shoot_cooldown == 0:
        shoot(player2_x, player2_y, player2_bullets)
        shoot_cooldown = 30  # Tiempo de enfriamiento entre disparos

    # Reducir el tiempo de enfriamiento entre disparos
    if shoot_cooldown > 0:
        shoot_cooldown -= 1

    # Actualizar disparos
    player1_bullets = [bullet for bullet in player1_bullets if bullet.x < SCREEN_WIDTH]
    player2_bullets = [bullet for bullet in player2_bullets if bullet.x > 0]

    for bullet in player1_bullets:
        bullet.x += bullet_speed
        if pygame.Rect(player2_x, player2_y, 50, 50).colliderect(bullet):
            player2_life -= life_reduction
            player1_bullets.remove(bullet)
            player1_hits += 1
    for bullet in player2_bullets:
        bullet.x -= bullet_speed
        if pygame.Rect(player1_x, player1_y, 50, 50).colliderect(bullet):
            player1_life -= life_reduction
            player2_bullets.remove(bullet)
            player2_hits += 1

    # Dibujar el fondo
    screen.blit(background, (0, 0))

    # Dibujar jugadores, disparos y barras de vida
    pygame.draw.rect(screen, RED, (player1_x, player1_y, 50, 50))  # Jugador 1 (rojo)
    pygame.draw.rect(screen, BLUE, (player2_x, player2_y, 50, 50))  # Jugador 2 (azul)
    for bullet in player1_bullets:
        pygame.draw.rect(screen, RED, bullet)
    for bullet in player2_bullets:
        pygame.draw.rect(screen, BLUE, bullet)

    # Barras de vida
    pygame.draw.rect(screen, RED, (10, 10, player1_life, 20))
    pygame.draw.rect(screen, BLUE, (10, 40, player2_life, 20))

    pygame.display.update()

    # Control de la finalización del juego cuando se alcanzan los disparos requeridos
    if player1_hits >= required_hits:
        font = pygame.font.Font(None, 36)
        text = font.render("¡Jugador 1 gana!", True, RED)
        text_rect = text.get_rect(center=(SCREEN_WIDTH // 2, SCREEN_HEIGHT // 2))
        screen.blit(text, text_rect)
        pygame.display.update()

        # Esperar una respuesta para jugar de nuevo
        waiting_for_response = True
        while waiting_for_response:
            for event in pygame.event.get():
                if event.type == pygame.KEYDOWN:
                    if event.key == pygame.K_r:  # Presionar 'r' para jugar de nuevo
                        player1_hits = 0
                        player2_hits = 0
                        player1_life = 100
                        player2_life = 100
                        player1_x, player1_y = 100, 450
                        player2_x, player2_y = 600, 450
                        player1_bullets = []
                        player2_bullets = []
                        waiting_for_response = False
                    if event.key == pygame.K_q:  # Presionar 'q' para salir del juego
                        running = False
                        waiting_for_response = False
    elif player2_hits >= required_hits:
        font = pygame.font.Font(None, 36)
        text = font.render("¡Jugador 2 gana!", True, BLUE)
        text_rect = text.get_rect(center=(SCREEN_WIDTH // 2, SCREEN_HEIGHT // 2))
        screen.blit(text, text_rect)
        pygame.display.update()

        # Esperar una respuesta para jugar de nuevo
        waiting_for_response = True
        while waiting_for_response:
            for event in pygame.event.get():
                if event.type == pygame.KEYDOWN:
                    if event.key == pygame.K_r:  # Presionar 'r' para jugar de nuevo
                        player1_hits = 0
                        player2_hits = 0
                        player1_life = 100
                        player2_life = 100
                        player1_x, player1_y = 100, 450
                        player2_x, player2_y = 600, 450
                        player1_bullets = []
                        player2_bullets = []
                        waiting_for_response = False
                    if event.key == pygame.K_q:  # Presionar 'q' para salir del juego
                        running = False
                        waiting_for_response = False

# Salir del juego
pygame.quit()
sys.exit()

