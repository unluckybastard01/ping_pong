import pygame
from pygame import *
import sys


init()
mixer.init()
font.init()


WIDTH, HEIGHT = 800, 600
screen = display.set_mode((WIDTH, HEIGHT))
display.set_caption("Ping Pong")


score_font = font.Font(None, 70)
score_left = 0
score_right = 0


bg_image = transform.scale(image.load("istockphoto-472296663-170667a.jpg"), (WIDTH, HEIGHT))
ball_image = transform.scale(image.load("1.png"), (60, 70))
paddle_img1 = transform.scale(image.load("top-view-man1.png"), (80, 100))
paddle_img2 = transform.scale(image.load("top-view-man2.png"), (80, 100))


mixer.music.load("Tobu_-_Candyland_64401768.mp3")
mixer.music.set_volume(0.5)
mixer.music.play(-1)


class GameSprite(sprite.Sprite):
    def __init__(self, player_image, player_x, player_y, player_speed):
        super().__init__()
        self.image = player_image
        self.speed = player_speed
        self.rect = self.image.get_rect()
        self.rect.x = player_x
        self.rect.y = player_y

    def reset(self):
        screen.blit(self.image, (self.rect.x, self.rect.y))


class Player(GameSprite):
    def update_l(self):
        keys = key.get_pressed()
        if keys[K_w] and self.rect.top > 0:
            self.rect.y -= self.speed
        if keys[K_s] and self.rect.bottom < HEIGHT:
            self.rect.y += self.speed

    def update_r(self):
        keys = key.get_pressed()
        if keys[K_UP] and self.rect.top > 0:
            self.rect.y -= self.speed
        if keys[K_DOWN] and self.rect.bottom < HEIGHT:
            self.rect.y += self.speed


class Ball(GameSprite):
    def __init__(self, player_image, player_x, player_y, speed_x, speed_y):
        super().__init__(player_image, player_x, player_y, 0)
        self.speed_x = speed_x
        self.speed_y = speed_y
        self.start_x = player_x
        self.start_y = player_y

    def update(self):
        global score_left, score_right
        
        self.rect.x += self.speed_x
        self.rect.y += self.speed_y


        if self.rect.top <= 0 or self.rect.bottom >= HEIGHT:
            self.speed_y *= -1

        if self.rect.left <= 0:
            score_right += 1
            self.reset_position()
        elif self.rect.right >= WIDTH:
            score_left += 1
            self.reset_position()

    def reset_position(self):
        self.rect.x = self.start_x
        self.rect.y = self.start_y
        self.speed_x *= -1 


left_paddle = Player(paddle_img1, -10, HEIGHT // 2 - 50, 7)
right_paddle = Player(paddle_img2, WIDTH - 70, HEIGHT // 2 - 50, 7)
ball = Ball(ball_image, WIDTH // 2, HEIGHT // 2, 5, 5)

clock = time.Clock()
running = True


while running:
    for e in event.get():
        if e.type == QUIT:
            running = False

    screen.blit(bg_image, (0, 0))

    left_paddle.update_l()
    right_paddle.update_r()
    ball.update()

    if sprite.collide_rect(left_paddle, ball) or sprite.collide_rect(right_paddle, ball):
        ball.speed_x *= -1

    left_paddle.reset()
    right_paddle.reset()
    ball.reset()

    score_text = score_font.render(f"{score_left}    {score_right}", True, (255, 255, 255))
    text_x = WIDTH // 2 - score_text.get_width() // 2
    screen.blit(score_text, (text_x, 60))

    display.update()
    clock.tick(60)

quit()
sys.exit()
