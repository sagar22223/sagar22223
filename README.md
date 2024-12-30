import pygame
import random

# Initialize Pygame
pygame.init()

# Screen dimensions
SCREEN_WIDTH, SCREEN_HEIGHT = 800, 600
screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
pygame.display.set_caption("Metro Escape: Chase to Freedom")

# Colors
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
RED = (255, 0, 0)
BLUE = (0, 0, 255)
GREEN = (0, 255, 0)

# Clock
clock = pygame.time.Clock()
FPS = 60

# Player settings
player_size = 50
player_x = SCREEN_WIDTH // 2
player_y = SCREEN_HEIGHT - player_size - 10
player_velocity = 5

# Obstacle settings
obstacle_width, obstacle_height = 50, 50
obstacle_velocity = 7
obstacle_list = []

# Game variables
score = 0
game_over = False
font = pygame.font.Font(None, 36)

# Load assets
player_image = pygame.Surface((player_size, player_size))
player_image.fill(BLUE)

obstacle_image = pygame.Surface((obstacle_width, obstacle_height))
obstacle_image.fill(RED)

# Functions
def draw_player(x, y):
    screen.blit(player_image, (x, y))

def draw_obstacles(obstacles):
    for obs in obstacles:
        screen.blit(obstacle_image, (obs[0], obs[1]))

def move_obstacles(obstacles):
    for obs in obstacles:
        obs[1] += obstacle_velocity
    return [obs for obs in obstacles if obs[1] < SCREEN_HEIGHT]

def detect_collision(player_rect, obstacles):
    for obs in obstacles:
        if player_rect.colliderect(pygame.Rect(obs[0], obs[1], obstacle_width, obstacle_height)):
            return True
    return False

# Main game loop
while not game_over:
    screen.fill(WHITE)

    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            game_over = True

    # Player movement
    keys = pygame.key.get_pressed()
    if keys[pygame.K_LEFT] and player_x > 0:
        player_x -= player_velocity
    if keys[pygame.K_RIGHT] and player_x < SCREEN_WIDTH - player_size:
        player_x += player_velocity
    if keys[pygame.K_UP] and player_y > 0:
        player_y -= player_velocity
    if keys[pygame.K_DOWN] and player_y < SCREEN_HEIGHT - player_size:
        player_y += player_velocity

    # Generate obstacles
    if random.randint(1, 30) == 1:
        obstacle_x = random.randint(0, SCREEN_WIDTH - obstacle_width)
        obstacle_y = -obstacle_height
        obstacle_list.append([obstacle_x, obstacle_y])

    obstacle_list = move_obstacles(obstacle_list)

    # Check for collisions
    player_rect = pygame.Rect(player_x, player_y, player_size, player_size)
    if detect_collision(player_rect, obstacle_list):
        game_over = True

    # Update score
    score += 1

    # Draw elements
    draw_player(player_x, player_y)
    draw_obstacles(obstacle_list)

    # Display score
    score_text = font.render(f"Score: {score}", True, BLACK)
    screen.blit(score_text, (10, 10))

    # Refresh screen
    pygame.display.flip()
    clock.tick(FPS)

# End game
pygame.quit()
