Initialize Pygame
pygame.init()

Constants
SCREEN_WIDTH = 800 SCREEN_HEIGHT = 600 PLAYER_WIDTH = 100 PLAYER_HEIGHT = 80 BULLET_WIDTH = 50 BULLET_HEIGHT = 50 ENEMY_WIDTH = 100 ENEMY_HEIGHT = 80 ENEMY_SPEED = 1

Load sound effects
shoot_sound = pygame.mixer.Sound("fe.wav") enemy_die_sound = pygame.mixer.Sound("fd.mp3") # Sound for enemy defeat defeat_sound = pygame.mixer.Sound("s.mp3") # Sound for player defeat victory_sound = pygame.mixer.Sound("g.mp3") # Load victory sound

Set up the display
screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT)) pygame.display.set_caption("Shooting Game")

Load images
shooter_image = pygame.image.load("4.png") bullet_image = pygame.image.load("7.png") imposter_image = pygame.image.load("2.png") start_image = pygame.image.load("j.jpeg") # Load your start screen image

Load background images
background1 = pygame.image.load("v.png") # Replace with your background image background2 = pygame.image.load("s.png") # Replace with your background image background3 = pygame.image.load("f.png") # Replace with your background image

Scale images if necessary
shooter_image = pygame.transform.scale(shooter_image, (PLAYER_WIDTH, PLAYER_HEIGHT)) bullet_image = pygame.transform.scale(bullet_image, (BULLET_WIDTH, BULLET_HEIGHT)) imposter_image = pygame.transform.scale(imposter_image, (ENEMY_WIDTH, ENEMY_HEIGHT)) start_image = pygame.transform.scale(start_image, (SCREEN_WIDTH, SCREEN_HEIGHT)) # Scale start image background1 = pygame.transform.scale(background1, (SCREEN_WIDTH, SCREEN_HEIGHT)) background2 = pygame.transform.scale(background2, (SCREEN_WIDTH, SCREEN_HEIGHT)) background3 = pygame.transform.scale(background3, (SCREEN_WIDTH, SCREEN_HEIGHT))

Function to draw a rectangle with rounded corners
def draw_rounded_rect(surface, color, rect, radius): # Create a surface for the rounded rectangle rounded_rect = pygame.Surface(rect.size, pygame.SRCALPHA) pygame.draw.rect(rounded_rect, color, (0, 0, rect.width, rect.height), border_radius=radius) surface.blit(rounded_rect, rect.topleft)

Player class
class Player: def init(self): self.rect = pygame.Rect(SCREEN_WIDTH // 2, SCREEN_HEIGHT - PLAYER_HEIGHT - 10, PLAYER_WIDTH, PLAYER_HEIGHT)

Insert Code
Run
Copy code
def move(self, dx):
    self.rect.x += dx
    if self.rect.x < 0:
        self.rect.x = 0
    if self.rect.x > SCREEN_WIDTH - PLAYER_WIDTH:
        self.rect.x = SCREEN_WIDTH - PLAYER_WIDTH

def draw(self):
    screen.blit(shooter_image, self.rect)
Bullet class
class Bullet: def init(self, x, y): self.rect = pygame.Rect(x, y, BULLET_WIDTH, BULLET_HEIGHT)

Insert Code
Run
Copy code
def move(self):
    self.rect.y -= 5

def draw(self):
    screen.blit(bullet_image, self.rect)
Enemy class
class Enemy: def init(self): self.rect = pygame.Rect(random.randint(0, SCREEN_WIDTH - ENEMY_WIDTH), 0, ENEMY_WIDTH, ENEMY_HEIGHT)

Insert Code
Run
Copy code
def move(self):
    self.rect.y += ENEMY_SPEED

def draw(self):
    screen.blit(imposter_image, self.rect)
Function to display the start screen
def start_screen(): font = pygame.font.Font(None, 74) start_text = font.render("", True, (0, 0, 0)) start_text_rect = start_text.get_rect(center=(SCREEN_WIDTH // 2, SCREEN_HEIGHT // 2 - 50)) button_font = pygame.font.Font(None, 48) button_text = button_font.render("Click to Start", True, (255, 255, 255)) button_width = 300 button_height = 50 button_rect = pygame.Rect(0, 0, button_width, button_height) button_rect.center = (SCREEN_WIDTH // 2, SCREEN_HEIGHT // 2 + 50)

Insert Code
Run
Copy code
while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
            exit()
        if event.type == pygame.MOUSEBUTTONDOWN:
            mouse_x, mouse_y = pygame.mouse.get_pos()
            if button_rect.left <= mouse_x <= button_rect.right and button_rect.top <= mouse_y <= button_rect.bottom:
                return  # Start the game

    screen.blit(start_image, (0, 0))  # Background image
    screen.blit(start_text, start_text_rect)  # Draw start text
    draw_rounded_rect(screen, (255, 0, 0), button_rect, 20)  # Draw button rectangle with rounded corners
    button_text_rect = button_text.get_rect(center=button_rect.center)
    screen.blit(button_text, button_text_rect)  # Draw button text
    pygame.display.flip()
Function to display the background selection screen
def background_selection_screen(): font = pygame.font.Font(None, 74) title_text = font.render("Select Background", True, (0, 0, 0)) title_rect = title_text.get_rect(center=(SCREEN_WIDTH // 2, 50)) backgrounds = [background1, background2, background3] selected_background = None buttons = [] button_colors = [(3, 155, 229), (67, 160, 71), (121, 85, 72)] # Light Blue, Dark Green, Light Brown

Insert Code
Run
Copy code
for i, bg in enumerate(backgrounds):
    button_rect = pygame.Rect(100, 150 + i * 100, 600, 80)
    buttons.append(button_rect)

while selected_background is None:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
            exit()
        if event.type == pygame.MOUSEBUTTONDOWN:
            mouse_x, mouse_y = pygame.mouse.get_pos()
            for i, button_rect in enumerate(buttons):
                if button_rect.collidepoint(mouse_x, mouse_y):
                    selected_background = backgrounds[i]

    screen.fill((255, 255, 255))  # White background
    screen.blit(title_text, title_rect)
    for i, bg in enumerate(backgrounds):
        draw_rounded_rect(screen, button_colors[i], buttons[i], 20)  # Draw button rectangle with rounded corners and specified colors
        button_text = font.render(f"Background {i + 1}", True, (255, 255, 255))
        button_text_rect = button_text.get_rect(center=buttons[i].center)
        screen.blit(button_text, button_text_rect)  # Draw button text
    pygame.display.flip()
return selected_background
Function to display the victory screen
def display_victory_screen(enemies_killed): font = pygame.font.Font(None, 74) text = font.render("Victory!", True, (2, 48, 32)) message_font = pygame.font.Font(None, 36) message = message_font.render(f"You killed {enemies_killed} enemies!", True, (0, 0, 0)) next_stage_message = message_font.render("Going to the next stage...", True, (0, 0, 0))

Insert Code
Run
Copy code
# Draw the popup panel
screen.fill((255, 255, 255))  # White background
pygame.draw.rect(screen, (0, 0, 0), (100, 100, 600, 400))  # Popup panel
pygame.draw.rect(screen, (255, 255, 255), (105, 105, 590, 390))  # Inner white rectangle
screen.blit(text, (SCREEN_WIDTH // 2 - text.get_width() // 2, 150))
screen.blit(message, (SCREEN_WIDTH // 2 - message.get_width() // 2, 250))
screen.blit(next_stage_message, (SCREEN_WIDTH // 2 - next_stage_message.get_width() // 2, 300))

pygame.display.flip()
pygame.time.delay(2000)  # Show for 2 seconds
Function to create confetti effect
def create_confetti(): for _ in range(100): # Number of confetti pieces x = random.randint(0, SCREEN_WIDTH) y = random.randint(0, SCREEN_HEIGHT) color = (random.randint(0, 255), random.randint(0, 255), random.randint(0, 255)) pygame.draw.circle(screen, color, (x, y), random.randint(5, 10))

Function to display game over screen
def game_over_screen(enemies_killed): font = pygame.font.Font(None, 74) text = font.render("You Lose!", True, (255, 0, 0)) message_font = pygame.font.Font(None, 36) message = message_font.render(f"You killed {enemies_killed} enemies!", True, (0, 0, 0)) restart_text = message_font.render("Press R to Restart or Q to Quit", True, (0, 0, 0)) screen.fill((255, 255, 255)) # White background screen.blit(text, (SCREEN_WIDTH // 2 - text.get_width() // 2, SCREEN_HEIGHT // 2 - text.get_height() // 2 - 50)) screen.blit(message, (SCREEN_WIDTH // 2 - message.get_width() // 2, SCREEN_HEIGHT // 2)) screen.blit(restart_text, (SCREEN_WIDTH // 2 - restart_text.get_width() // 2, SCREEN_HEIGHT // 2 + 50))

Insert Code
Run
Copy code
# Draw a rounded rectangle for the restart and quit options
restart_button_rect = pygame.Rect(250, SCREEN_HEIGHT // 2 + 100, 300, 50)
draw_rounded_rect(screen, (255, 255, 255), restart_button_rect, 20)  # Draw button rectangle with rounded corners
restart_button_text = message_font.render("Restart", True, (255, 255, 255))
restart_button_text_rect = restart_button_text.get_rect(center=restart_button_rect.center)
screen.blit(restart_button_text, restart_button_text_rect)  # Draw button text

pygame.display.flip()
waiting = True
while waiting:
    defeat_sound.play()  # Play defeat sound
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
            exit()
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_r:
                waiting = False
                return True  # Restart game
            if event.key == pygame.K_q:
                pygame.quit()
                exit()
Main game loop
def main(background): clock = pygame.time.Clock() player = Player() bullets = [] enemies = [Enemy() for _ in range(5)] defeated_enemies = 0 total_enemies_to_defeat = 30 # Enemies to defeat to complete a stage running = True

Insert Code
Run
Copy code
while running:
    screen.blit(background, (0, 0))  # Draw the selected background
    # Display the score
    font = pygame.font.Font(None, 36)
    score_text = font.render(f"Killed: {defeated_enemies} / {total_enemies_to_defeat}", True, (255, 255, 255))
    screen.blit(score_text, (10, 10))  # Display score at the top-left corner

    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

    keys = pygame.key.get_pressed()
    if keys[pygame.K_LEFT]:
        player.move(-5)
    if keys[pygame.K_RIGHT]:
        player.move(5)
    if keys[pygame.K_SPACE]:
        if len(bullets) < 5:  # Limit the number of bullets
            bullets.append(Bullet(player.rect.centerx - BULLET_WIDTH // 2, player.rect.top))
            shoot_sound.play()  # Play shooting sound

    # Update bullets
    for bullet in bullets[:]:
        bullet.move()
        if bullet.rect.y < 0:
            bullets.remove(bullet)

    # Update enemies
    for enemy in enemies[:]:
        enemy.move()
        if enemy.rect.y > SCREEN_HEIGHT:
            enemies.remove(enemy)
            enemies.append(Enemy())
        for bullet in bullets[:]:
            if bullet.rect.colliderect(enemy.rect):
                bullets.remove(bullet)
                enemies.remove(enemy)
                enemy_die_sound.play()  # Play enemy dying sound
                defeated_enemies += 1
                enemies.append(Enemy())
                break

    # Check for collision between player and enemies
    for enemy in enemies:
        if player.rect.colliderect(enemy.rect):
            defeat_sound.play()  # Play defeat sound when player collides
            running = False  # End the game if the player is hit

    # Check for stage completion
    if defeated_enemies >= total_enemies_to_defeat:
        victory_sound.play()  # Play victory sound
        create_confetti()  # Create confetti effect
        display_victory_screen(defeated_enemies)  # Display victory screen
        defeated_enemies = 0  # Reset defeated enemies for the next stage
        total_enemies_to_defeat += 30  # Increase difficulty for the next stage
        # Reset bullets when going to the next stage
        bullets.clear()
        # Increase the number of enemies for the next stage
        enemies = [Enemy() for _ in range(5 + (total_enemies_to_defeat // 30))]  # Increase enemies based on stages

    # Draw everything
    player.draw()
    for bullet in bullets:
        bullet.draw()
    for enemy in enemies:
        enemy.draw()

    pygame.display.flip()
    clock.tick(60)

# Game over screen
if not running:
    if game_over_screen(defeated_enemies):
        main(background)  # Restart the game with the same background
if name == "main": start_screen() # Show the start screen selected_background = background_selection_screen() # Show background selection screen main(selected_background) # Start the game with the selected background

explain this code step by step how to do

BLACKBOXAI

This code is a simple shooting game built using the Pygame library in Python. Below is a step-by-step explanation of how the code works, including its structure and functionality.

Step 1: Import Libraries and Initialize Pygame
python
Insert Code
Run
Copy code
import pygame
import random

# Initialize Pygame
pygame.init()
The code starts by importing the necessary libraries: pygame for game development and random for generating random numbers.
pygame.init() initializes all the Pygame modules.
Step 2: Define Constants
python
Insert Code
Run
Copy code
# Constants
SCREEN_WIDTH = 800
SCREEN_HEIGHT = 600
PLAYER_WIDTH = 100
PLAYER_HEIGHT = 80
BULLET_WIDTH = 50
BULLET_HEIGHT = 50
ENEMY_WIDTH = 100
ENEMY_HEIGHT = 80
ENEMY_SPEED = 1
Constants are defined for screen dimensions, player and enemy sizes, bullet sizes, and enemy speed. These values are used throughout the game to maintain consistency.
Step 3: Load Sound Effects
python
Insert Code
Run
Copy code
# Load sound effects
shoot_sound = pygame.mixer.Sound("fe.wav")
enemy_die_sound = pygame.mixer.Sound("fd.mp3")
defeat_sound = pygame.mixer.Sound("s.mp3")
victory_sound = pygame.mixer.Sound("g.mp3")
Sound effects for shooting, enemy defeat, player defeat, and victory are loaded using pygame.mixer.Sound.
Step 4: Set Up the Display
python
Insert Code
Run
Copy code
# Set up the display
screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
pygame.display.set_caption("Shooting Game")
A display window is created with the specified width and height, and a title is set for the window.
Step 5: Load Images
python
Insert Code
Run
Copy code
# Load images
shooter_image = pygame.image.load("4.png")
bullet_image = pygame.image.load("7.png")
imposter_image = pygame.image.load("2.png")
start_image = pygame.image.load("j.jpeg")
Images for the player (shooter), bullets, enemies, and the start screen are loaded.
Step 6: Scale Images
python
Insert Code
Run
Copy code
# Scale images if necessary
shooter_image = pygame.transform.scale(shooter_image, (PLAYER_WIDTH, PLAYER_HEIGHT))
bullet_image = pygame.transform.scale(bullet_image, (BULLET_WIDTH, BULLET_HEIGHT))
imposter_image = pygame.transform.scale(imposter_image, (ENEMY_WIDTH, ENEMY_HEIGHT))
start_image = pygame.transform.scale(start_image, (SCREEN_WIDTH, SCREEN_HEIGHT))
The loaded images are scaled to fit the defined dimensions.
Step 7: Define Helper Functions
Draw Rounded Rectangle
python
Insert Code
Run
Copy code
def draw_rounded_rect(surface, color, rect, radius):
    ...
This function draws a rectangle with rounded corners on a given surface.
Step 8: Define Classes
Player Class
python
Insert Code
Run
Copy code
class Player:
    def __init__(self):
        self.rect = pygame.Rect(SCREEN_WIDTH // 2, SCREEN_HEIGHT - PLAYER_HEIGHT - 10, PLAYER_WIDTH, PLAYER_HEIGHT)

    def move(self, dx):
        ...
    
    def draw(self):
        ...
The Player class represents the player character. It has methods to move and draw the player on the screen.
Bullet Class
python
Insert Code
Run
Copy code
class Bullet:
    def __init__(self, x, y):
        ...
    
    def move(self):
        ...
    
    def draw(self):
        ...
The Bullet class represents bullets fired by the player. It has methods to move and draw bullets.
Enemy Class
python
Insert Code
Run
Copy code
class Enemy:
    def __init__(self):
        ...
    
    def move(self):
        ...
    
    def draw(self):
        ...
The Enemy class represents enemy characters. It has methods to move and draw enemies.
Step 9: Define Game Screens
Start Screen
python
Insert Code
Run
Copy code
def start_screen():
    ...
This function displays the start screen with a button to begin the game.
Background Selection Screen
python
Insert Code
Run
Copy code
def background_selection_screen():
    ...
This function allows the player to select a background for the game.
Victory Screen
python
Insert Code
Run
Copy code
def display_victory_screen(enemies_killed):
    ...
This function displays a victory message when the player defeats enough enemies.
Game Over Screen
python
Insert Code
Run
Copy code
def game_over_screen(enemies_killed):
    ...
This function displays a game over message when the player is defeated.
Step 10: Main Game Loop
python
Insert Code
Run
Copy code
def main(background):
    ...
This function contains the main game loop where the game logic is executed. It handles player movement, bullet firing, enemy spawning, collision detection, and rendering of all game elements.
Step 11: Game Execution
