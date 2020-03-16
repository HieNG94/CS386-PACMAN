
## Source:HieNG94/CS386-PACMAN/laser.py
```python
class Laser:
    def __init__(self, game, x, y, direction, portal_num=0):
        self.game = game
        self.direction = direction
        self.portal_num = portal_num
        # laser and portal
        self.laser_tracker = Point()
        self.laser_beam = Point(x, y)
        self.portal = 0

    def draw_laser(self, game):
        # check if the laser hit the wall
        if not game.laser_done:
            # update laser tracker
            if (self.direction.x == -1 and self.direction.y == 0) or (self.direction.x == 0 and self.direction.y == -1):
                self.laser_tracker.x = int(self.laser_beam.x + self.direction.x)
                self.laser_tracker.y = int(self.laser_beam.y + self.direction.y)
            elif (self.direction.x == 1 and self.direction.y == 0) or (self.direction.x == 0 and self.direction.y == 1):
                self.laser_tracker.x = int(self.laser_beam.x + self.direction.x * TRACKER_SPEED)
                self.laser_tracker.y = int(self.laser_beam.y + self.direction.y * TRACKER_SPEED)
            # check on maze in maze.py
            if self.laser_tracker.y // CELL_SIZE in range(0, 31) and self.laser_tracker.x // CELL_SIZE in range(
                    0, 28):
                # hit a wall
                if maze[self.laser_tracker.y // CELL_SIZE][self.laser_tracker.x // CELL_SIZE] == 1:
                    # hit the wall
                    self.make_portal()
                    game.portal_open_sound.play()
                    game.laser_done = True
                # cancel the portal if 2 laser hit the same spot
                elif self.check_portal():
                    maze[self.laser_tracker.y // CELL_SIZE][self.laser_tracker.x // CELL_SIZE] = 1
                    game.check_portal = [0, 0, 0]
                    game.laser_done = True
                    game.reload_sound.play()
                # cannot hit the ghost's room gate
                elif maze[self.laser_tracker.y // CELL_SIZE][self.laser_tracker.x // CELL_SIZE] == 5:
                    game.check_portal[self.portal_num] = 0
                    game.laser_done = True
                    game.gun_empty_sound.play()
                # free space
                elif maze[self.laser_tracker.y // CELL_SIZE][self.laser_tracker.x // CELL_SIZE] in walkable_path:
                    self.laser_beam.x += self.direction.x * LASER_SPEED
                    self.laser_beam.y += self.direction.y * LASER_SPEED
                    # laser can travel through the fixed portals
                    if maze[self.laser_beam.y // CELL_SIZE][self.laser_beam.x // CELL_SIZE] == -3:
                        if self.direction.x == -1 and self.direction.y == 0:
                            self.laser_beam.x = GAME_WIDTH - CELL_SIZE
                    elif maze[self.laser_beam.y // CELL_SIZE][self.laser_beam.x // CELL_SIZE] == 3:
                        if self.direction.x == 1 and self.direction.y == 0:
                            self.laser_beam.x = 0
                    # draw 2 laser
                    if self.portal_num == 1:
                        pg.draw.rect(game.game_surface, BLUE,
                                     (self.laser_beam.x + 10, self.laser_beam.y + 10, 10, 10))
                    elif self.portal_num == 2:
                        pg.draw.rect(game.game_surface, GREEN, (self.laser_beam.x + 10, self.laser_beam.y + 10, 10, 10))

    # assign portal position
    def make_portal(self):
        if self.portal_num == 1:
            if self.direction.x == 1 and self.direction.y == 0:
                maze[self.laser_tracker.y // CELL_SIZE][self.laser_tracker.x // CELL_SIZE] = 11
            elif self.direction.x == 0 and self.direction.y == 1:
                maze[self.laser_tracker.y // CELL_SIZE][self.laser_tracker.x // CELL_SIZE] = 12
            elif self.direction.x == -1 and self.direction.y == 0:
                maze[self.laser_tracker.y // CELL_SIZE][self.laser_tracker.x // CELL_SIZE] = 13
            elif self.direction.x == 0 and self.direction.y == -1:
                maze[self.laser_tracker.y // CELL_SIZE][self.laser_tracker.x // CELL_SIZE] = 14
        elif self.portal_num == 2:
            if self.direction.x == 1 and self.direction.y == 0:
                maze[self.laser_tracker.y // CELL_SIZE][self.laser_tracker.x // CELL_SIZE] = 21
            elif self.direction.x == 0 and self.direction.y == 1:
                maze[self.laser_tracker.y // CELL_SIZE][self.laser_tracker.x // CELL_SIZE] = 22
            elif self.direction.x == -1 and self.direction.y == 0:
                maze[self.laser_tracker.y // CELL_SIZE][self.laser_tracker.x // CELL_SIZE] = 23
            elif self.direction.x == 0 and self.direction.y == -1:
                maze[self.laser_tracker.y // CELL_SIZE][self.laser_tracker.x // CELL_SIZE] = 24

    def check_portal(self):
        if maze[self.laser_tracker.y // CELL_SIZE][self.laser_tracker.x // CELL_SIZE] in portal_value:
            return True
```
