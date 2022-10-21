# snake-game

import pygame
import random

pygame.init()
stat_x = 100
stat_y = 100
width = 600
height = 600
box_width = 20


class Cube(object):
    def __init__(self, pos_x, pos_y, colour):
        self.pos_x = pos_x
        self.pos_y = pos_y
        self.colour = colour

    def draw(self, screen):
        pygame.draw.rect(screen, self.colour, (stat_x + self.pos_x * box_width + 1, stat_y + self.pos_y * box_width + 1,
                                               box_width - 2, box_width - 2))


class Snake(object):
    body = []

    def __init__(self, pos_x, pos_y, colour):
        self.pos_x = pos_x
        self.pos_y = pos_y
        self.colour = colour
        self.body.append((self.pos_x, self.pos_y))

    def update(self, dx, dy):
        self.body.append((self.pos_x, self.pos_y))
        self.pos_x += dx
        self.pos_y += dy
        self.body.pop(0)

        if self.pos_x > 19:
            self.pos_x = 0
        if self.pos_x < 0:
            self.pos_x = 19
        if self.pos_y > 19:
            self.pos_y = 0
        if self.pos_y < 0:
            self.pos_y = 19

    def draw(self, screen):
        for i in self.body:
            c = Cube(i[0], i[1], (0, 0, 200))
            c.draw(screen)

    def add(self, pos_x, pos_y):
        self.body.insert(0, (pos_x, pos_y))

    def reset(self):
        left = self.body.pop(0)
        self.body = [left]


def draw_grid(screen):
    screen.fill((0, 0, 0))
    for i in range(20):
        for j in range(20):
            pygame.draw.rect(screen, (255, 255, 255), (stat_x + i*box_width, stat_y + j*box_width, box_width, box_width), 1)
    pygame.draw.rect(screen, (255, 0, 0), (stat_x - 4, stat_y - 4, box_width * 20 + 8, box_width * 20 + 8), 5)


def random_snack_pos(snake):
    pos_x = random.randrange(0, 19)
    pos_y = random.randrange(0, 19)
    if (pos_x, pos_y) in snake.body:
        x, y = random_snack_pos(snake)
        return x, y
    return pos_x, pos_y


def crash(screen, score):
    screen.fill((0, 0, 0))
    font = pygame.font.Font('freesansbold.ttf', 32)
    text = font.render('Game Over', True, (255, 0, 0), (0, 0, 0))
    screen.blit(text, (200, 100))
    score_txt = "Score : " + str(score)
    screen.blit(font.render(score_txt, True, (255, 255, 255), (0, 0, 0)), (220, 440))
    pygame.display.update()


def check(snake):
    final_list = []
    for pos in snake.body:
        if pos not in final_list:
            final_list.append(pos)
    if len(snake.body) != len(final_list):
        # snake.reset()
        return True
    return False


def main():
    win = pygame.display.set_mode((width, height))
    pygame.display.set_caption("Snake Game")
    icon = pygame.image.load(r"D:\anaconda.png")
    pygame.display.set_icon(icon)
    font = pygame.font.SysFont('comicsansms.ttf', 50)
    text = font.render('SNAKE', True, (255, 255, 255), (0, 0, 0))
    s = Snake(10, 10, (0, 255, 255))
    snack_x, snack_y = random_snack_pos(s)
    snack = Cube(snack_x, snack_y, (245, 19, 95))
    score = 0
    dx = 1
    dy = 0
    run = True
    clock = pygame.time.Clock()
    while run:
        pygame.time.delay(100)
        clock.tick(10)
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                run = False
            keys = pygame.key.get_pressed()
            if keys[pygame.K_LEFT] and not (dx == 1 and dy == 0):
                dx = -1
                dy = 0
            elif keys[pygame.K_RIGHT] and not (dx == -1 and dy == 0):
                dx = 1
                dy = 0
            elif keys[pygame.K_UP] and not (dx == 0 and dy == 1):
                dx = 0
                dy = -1
            elif keys[pygame.K_DOWN] and not (dx == 0 and dy == -1):
                dx = 0
                dy = 1
        if (snack_x, snack_y) in s.body:
            score += 1
            s.add(snack_x + dx, snack_y + dy)
            snack_x, snack_y = random_snack_pos(s)
            snack = Cube(snack_x, snack_y, (245, 19, 95))
        if check(s):
            crash(win, score)
        else:
            draw_grid(win)
            snack.draw(win)
            s.update(dx, dy)
            s.draw(win)
            win.blit(text, (220, 40))
            score_txt = "Score : " + str(score)
            win.blit(font.render(score_txt, True, (255, 255, 255), (0, 0, 0)), (220, 540))
            pygame.display.update()


main()
