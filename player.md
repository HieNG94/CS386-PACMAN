# PACMAN-PORTAL game
## Source: HieNG94/CS386-PACMAN/player.py
```
######################################################################################################################
# CS 386
# HIEN NGUYEN, 889341772
######################################################################################################################
```python
import pygame as pg
from pygame.locals import *
from vector import *
from settings import *
from maze import *
from copy import deepcopy
import time


class Player:
    def __init__(self, game, pos, velocity):
        self.game = game
        # character
        self.starting_pos = pos
        self.tracker = pos
        self.velocity = velocity
        self.direction = Vector()
        self.vec_tracker = Vector()
        self.walkCount = 0
        # coin
        self.coin_empty = True
        self.coin_count = 0
        self.coin = deepcopy(maze)
        # other
        self.tele = False
        self.currentScore = -10
        self.player = pg.Rect(self.starting_pos.x, self.starting_pos.y, 30, 30)
        self.expired = 0
        self.warn = 0
        self.strawberry_cd = 0
        self.cherry_cd = 0
        self.apple_cd = 0
        self.kiwi_cd = 0

    def move(self, game):
        button = (K_LEFT, K_a, K_RIGHT, K_d, K_UP, K_w, K_DOWN, K_s)
        # check turn and turn when the player in correct grid, position % the cell size equals 0
        if self.direction != self.velocity:
            self.turn()
        # check the maze list in maze.py
        if self.can_move(game=game):
            if self.tele:
                self.direction = Vector()
                for event in pg.event.get():
                    if pg.key.get_pressed():
                        if event.key in button:
                            self.tele = False
                    elif event.type == QUIT:
                        game.finished = True
            # pac-man moves
            self.walkCount += 1
            if self.walkCount >= 12:
                self.walkCount = 0
            self.player.left += self.direction.x * PLAYER_SPEED
            self.player.top += self.direction.y * PLAYER_SPEED
            # teleport pac-man through the fixed portal
            self.teleport(game=game)
            self.power_mode(game=game)
            # eats coin, update score, and mark posion of eaten coin in the coin list
            self.eat_coin(game=game)

    def teleport(self, game):
        if maze[self.player.top // CELL_SIZE][self.player.left // CELL_SIZE] == -3:
            if self.direction.x == -1 and self.direction.y == 0:
                self.player.left = GAME_WIDTH - CELL_SIZE
                game.tele_sound.play()
        elif maze[self.player.top // CELL_SIZE][self.player.left // CELL_SIZE] == 3:
            if self.direction.x == 1 and self.direction.y == 0:
                self.player.left = 0
                game.tele_sound.play()
        elif maze[self.tracker.y // CELL_SIZE][self.tracker.x // CELL_SIZE] in portal_value:
            gate1 = maze[self.tracker.y // CELL_SIZE][self.tracker.x // CELL_SIZE]
            gate2 = self.get_other_portal_value(gate1)
            self.get_tele_pos(gate2)
            self.get_dir_after_tele(gate2)
            self.tele = True
            game.tele_sound.play()

    @staticmethod
    def get_other_portal_value(value):
        for y in range(ROW):
            for x in range(COL):
                if maze[y][x] in portal_value:
                    if not maze[y][x] == value:
                        return maze[y][x]

    def get_tele_pos(self, portal):
        for y in range(ROW):
            for x in range(COL):
                if maze[y][x] == portal:
                    self.player.top = y * CELL_SIZE
                    self.player.left = x * CELL_SIZE

    def get_dir_after_tele(self, portal):
        if portal == 11 or portal == 21:
            self.direction = Vector(-1.0, 0.0)
        elif portal == 12 or portal == 22:
            self.direction = Vector(0.0, -1.0)
        elif portal == 13 or portal == 23:
            self.direction = Vector(1.0, 0.0)
        elif portal == 14 or portal == 24:
            self.direction = Vector(0.0, 1.0)

    def can_move(self, game):
        # update the tracker depends on the direction of pac-man
        if (self.direction.x == -1 and self.direction.y == 0) or (self.direction.x == 0 and self.direction.y == -1):
            self.tracker.x = int(self.player.left + self.direction.x)
            self.tracker.y = int(self.player.top + self.direction.y)
        elif (self.direction.x == 1 and self.direction.y == 0) or (self.direction.x == 0 and self.direction.y == 1):
            self.tracker.x = int(self.player.left + self.direction.x * TRACKER_SPEED)
            self.tracker.y = int(self.player.top + self.direction.y * TRACKER_SPEED)
        # check
        if (self.tracker.y // CELL_SIZE) in range(0, 31) and (self.tracker.x // CELL_SIZE) in range(0, 28):
            if maze[self.tracker.y // CELL_SIZE][self.tracker.x // CELL_SIZE] in walkable_path or \
                    (game.check_portal == [0, 1, 1] and
                     maze[self.tracker.y // CELL_SIZE][self.tracker.x // CELL_SIZE] in portal_value):
                return True

    def turn(self):
        if self.velocity.x == -1 and self.velocity.y == 0:
            self.vec_tracker.x = self.player.left + self.velocity.x
            self.vec_tracker.y = self.player.top + self.velocity.y
        else:
            self.vec_tracker.x = self.player.left + self.velocity.x * CELL_SIZE
            self.vec_tracker.y = self.player.top + self.velocity.y * CELL_SIZE
        if (self.velocity.x == -1 and self.velocity.y == 0) or (self.velocity.x == 1 and self.velocity.y == 0):
            if self.player.top % CELL_SIZE == 0:
                if maze[self.vec_tracker.y // CELL_SIZE][self.vec_tracker.x // CELL_SIZE] in walkable_path:
                    self.direction = self.velocity
        elif (self.velocity.x == 0 and self.velocity.y == -1) or (self.velocity.x == 0 and self.velocity.y == 1):
            if self.player.left % CELL_SIZE == 0:
                if maze[self.vec_tracker.y // CELL_SIZE][self.vec_tracker.x // CELL_SIZE] in walkable_path:
                    self.direction = self.velocity

    def power_mode(self, game):
        current = int(time.time())
        if self.warn == current:
            game.warning = True
        if not game.power:
            if maze[self.player.top // CELL_SIZE][self.player.left // CELL_SIZE] in power_pill and \
                    self.coin[self.player.top // CELL_SIZE][self.player.left // CELL_SIZE] == 6:
                game.eatfruit_sound.play()
                self.expired = current + FRUIT_DURATION
                self.warn = self.expired - 5
                game.power = True
        elif current == self.expired:
            game.warning = False
            game.power = False

    def update(self, game):
        self.move(game=game)
        self.check_coin(game=game)
        self.power_mode(game=game)
        if self.direction.x == -1 and self.direction.y == 0:
            game.pacman_left[self.walkCount // 3] = pg.transform.scale(game.pacman_left[self.walkCount // 3],
                                                                       (CELL_SIZE, CELL_SIZE))
            game.game_surface.blit(game.pacman_left[self.walkCount // 3], (self.player.left, self.player.top))
        elif self.direction.x == 1 and self.direction.y == 0:
            game.pacman_right[self.walkCount // 3] = pg.transform.scale(game.pacman_right[self.walkCount // 3],
                                                                        (CELL_SIZE, CELL_SIZE))
            game.game_surface.blit(game.pacman_right[self.walkCount // 3], (self.player.left, self.player.top))
        elif self.direction.x == 0 and self.direction.y == 1:
            game.pacman_down[self.walkCount // 3] = pg.transform.scale(game.pacman_down[self.walkCount // 3],
                                                                       (CELL_SIZE, CELL_SIZE))
            game.game_surface.blit(game.pacman_down[self.walkCount // 3], (self.player.left, self.player.top))
        else:
            game.pacman_up = pg.transform.scale(game.pacman_up, (CELL_SIZE, CELL_SIZE))
            game.game_surface.blit(game.pacman_up, (self.player.left, self.player.top))

    ############################################ Coin function ########################################################
    def eat_coin(self, game):
        current = int(time.time())
        if self.coin[self.player.top // CELL_SIZE][self.player.left // CELL_SIZE] == 6:
            game.coin_sound.play()
            if maze[self.player.top // CELL_SIZE][self.player.left // CELL_SIZE] == 411:
                self.strawberry_cd = current + FRUIT_CD
            elif maze[self.player.top // CELL_SIZE][self.player.left // CELL_SIZE] == 421:
                self.cherry_cd = current + FRUIT_CD
            elif maze[self.player.top // CELL_SIZE][self.player.left // CELL_SIZE] == 431:
                self.apple_cd = current + FRUIT_CD
            elif maze[self.player.top // CELL_SIZE][self.player.left // CELL_SIZE] == 441:
                self.kiwi_cd = current + FRUIT_CD
            self.coin[self.player.top // CELL_SIZE][self.player.left // CELL_SIZE] = 7
            if maze[self.player.top // CELL_SIZE][self.player.left // CELL_SIZE] not in power_pill:
                self.coin_count -= 1
            self.currentScore += 10

    def remake_coin(self, game):
        if self.coin_empty:
            for x in range(0, GAME_WIDTH // CELL_SIZE):
                for y in range(0, GAME_HEIGHT // CELL_SIZE):
                    if maze[y][x] in [0, 2, 8, 411, 412, 413, 414, 421, 422, 423, 424, 431, 432, 433, 434, 441, 442,
                                      443, 444]:
                        self.coin[y][x] = 6
                        if maze[y][x] not in power_pill:
                            self.coin_count += 1
            self.coin_empty = False
            self.make_coin(game=game)

    def get_fruit(self):
        current = int(time.time())
        for x in range(0, GAME_WIDTH // CELL_SIZE):
            for y in range(0, GAME_HEIGHT // CELL_SIZE):
                if maze[y][x] == 411:
                    if self.coin[y][x] == 7:
                        if current == self.strawberry_cd:
                            self.coin[y][x] = 6
                elif maze[y][x] == 421:
                    if self.coin[y][x] == 7:
                        if current == self.cherry_cd:
                            self.coin[y][x] = 6
                elif maze[y][x] == 431:
                    if self.coin[y][x] == 7:
                        if current == self.apple_cd:
                            self.coin[y][x] = 6
                elif maze[y][x] == 441:
                    if self.coin[y][x] == 7:
                        if current == self.kiwi_cd:
                            self.coin[y][x] = 6

    def make_coin(self, game):
        for x in range(0, GAME_WIDTH // CELL_SIZE):
            for y in range(0, GAME_HEIGHT // CELL_SIZE):
                if self.coin[y][x] == 6:
                    if maze[y][x] == 411:
                        strawberry = pg.transform.scale(game.strawberry, (30, 30))
                        game.game_surface.blit(strawberry, (x * CELL_SIZE, y * CELL_SIZE))
                        strawberry2 = pg.transform.scale(game.strawberry, (50, 50))
                        game.surface.blit(strawberry2, (WINWIDTH - 300, 900))
                    elif maze[y][x] == 421:
                        cherry = pg.transform.scale(game.cherry, (30, 30))
                        game.game_surface.blit(cherry, (x * CELL_SIZE, y * CELL_SIZE))
                        cherry2 = pg.transform.scale(game.cherry, (50, 50))
                        game.surface.blit(cherry2, (WINWIDTH - 230, 900))
                    elif maze[y][x] == 431:
                        apple = pg.transform.scale(game.apple, (30, 30))
                        game.game_surface.blit(apple, (x * CELL_SIZE, y * CELL_SIZE))
                        apple2 = pg.transform.scale(game.apple, (50, 50))
                        game.surface.blit(apple2, (WINWIDTH - 160, 900))
                    elif maze[y][x] == 441:
                        kiwi = pg.transform.scale(game.kiwi, (30, 30))
                        game.game_surface.blit(kiwi, (x * CELL_SIZE, y * CELL_SIZE))
                        kiwi2 = pg.transform.scale(game.kiwi, (50, 50))
                        game.surface.blit(kiwi2, (WINWIDTH - 90, 900))
                    else:
                        pg.draw.circle(game.game_surface, HONEYDEW,
                                       (x * CELL_SIZE + 15, y * CELL_SIZE + 15),
                                       CELL_SIZE // 2 - 10)

    def check_coin(self, game):
        if self.coin_count == 0:
            self.coin_empty = True
            game.level += 1
            game.lv_sound.play()
            self.remake_coin(game=game)
        else:
            self.coin_empty = False
            self.get_fruit()
            self.make_coin(game=game)
```
