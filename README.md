import pygame
import time
import random

# Initialize pygame
pygame.init()

# Screen dimensions
WIDTH, HEIGHT = 800, 600
CELL_SIZE = 20

# Colors
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
RED = (255, 0, 0)
GREEN = (0, 255, 0)
BLUE = (0, 0, 255)

# Setup display
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption('Snake Game')

# Clock for controlling the frame rate
clock = pygame.time.Clock()

# Snake setup
snake_pos = [100, 50]
snake_body = [[100, 50], [90, 50], [80, 50]]
direction = 'RIGHT'
change_to = direction
speed = 15

# Food setup
food_pos = [random.randrange(1, WIDTH//CELL_SIZE) * CELL_SIZE, 
            random.randrange(1, HEIGHT//CELL_SIZE) * CELL_SIZE]
food_spawn = True

# Score
score = 0

# Function to display the score
def show_score(choice, color, font, size):
    font = pygame.font.SysFont(font, size)
    score_surface = font.render(f'Score: {score}', True, color)
    score_rect = score_surface.get_rect()
    if choice == 1:
        score_rect.midtop = (WIDTH // 10, 15)
    else:
        score_rect.center = (WIDTH // 2, HEIGHT // 2)
    screen.blit(score_surface, score_rect)

# Game over function
def game_over():
    screen.fill(BLACK)
    show_score(0, RED, 'times new roman', 50)
    pygame.display.flip()
    time.sleep(2)
    pygame.quit()
    quit()

# Main logic
while True:
    # Event handling
    for event in pygame.event.get():
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_UP and direction != 'DOWN':
                change_to = 'UP'
            elif event.key == pygame.K_DOWN and direction != 'UP':
                change_to = 'DOWN'
            elif event.key == pygame.K_LEFT and direction != 'RIGHT':
                change_to = 'LEFT'
            elif event.key == pygame.K_RIGHT and direction != 'LEFT':
                change_to = 'RIGHT'
    
    # Change direction
    direction = change_to

    # Update snake position
    if direction == 'UP':
        snake_pos[1] -= CELL_SIZE
    if direction == 'DOWN':
        snake_pos[1] += CELL_SIZE
    if direction == 'LEFT':
        snake_pos[0] -= CELL_SIZE
    if direction == 'RIGHT':
        snake_pos[0] += CELL_SIZE

    # Snake body growing mechanism
    # If food is eaten
    if snake_pos == food_pos:
        score += 1
        food_spawn = False
    else:
        snake_body.pop()

    if not food_spawn:
        food_pos = [random.randrange(1, WIDTH//CELL_SIZE) * CELL_SIZE, 
                    random.randrange(1, HEIGHT//CELL_SIZE) * CELL_SIZE]
    food_spawn = True

    # Growing snake's body
    snake_body.insert(0, list(snake_pos))

    # Game over conditions
    if (snake_pos[0] < 0 or snake_pos[0] >= WIDTH or
        snake_pos[1] < 0 or snake_pos[1] >= HEIGHT):
        game_over()
    
    # Check for collision with itself
    for block in snake_body[1:]:
        if snake_pos == block:
            game_over()

    # Refresh screen
    screen.fill(BLACK)

    # Draw snake
    for pos in snake_body:
        pygame.draw.rect(screen, GREEN, pygame.Rect(pos[0], pos[1], CELL_SIZE, CELL_SIZE))

    # Draw food
    pygame.draw.rect(screen, RED, pygame.Rect(food_pos[0], food_pos[1], CELL_SIZE, CELL_SIZE))

    # Show score
    show_score(1, WHITE, 'times new roman', 20)

    # Refresh game screen
    pygame.display.update()

    # Frame Per Second / Refresh Rate
    clock.tick(speed)
