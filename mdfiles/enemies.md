
## Source: HieNG94/CS386-PACMAN/enemies.py
```python
class Enemies:
    def __init__(self, game, pos, speed, guard=0):
        self.game = game
        self.starting_pos = pos
        self.tracker = pos
        self.speed = speed
        self.guard = guard
        self.checkpoint = guard
        self.reach = 0
        self.des = Point()
        self.checker = Point()
        self.direction = Vector()
        self.velocity = Vector()
        self.walkable = []
        self.current = int(time.time())
        self.pop_i = 0
        self.spawn_time = self.current + SPAWN_TIME
        self.state_time = 0
        self.next = self.current + IMMUNE_TIME
        self.movement = {1: Vector(-1.0, 0.0), 2: Vector(1.0, 0.0), 3: Vector(0.0, -1.0), 4: Vector(0.0, 1.0)}
        self.enemy = pg.Rect(self.starting_pos.x, self.starting_pos.y, 30, 30)
        self.state = 'shopping'
        self.revive = 0
        self.dead = False
        self.start = True
        self.start_spawn = True
        self.speed_up = False
        self.walkCount = 0
        self.tele = False
        self.away = 0

    def move(self, game):
        self.walkCount += 1
        if self.walkCount >= 12:
            self.walkCount = 0
        self.current = int(time.time())
        # stop chasing mode when pacman in power mode
        if game.power and self.state == 'chasing':
            self.state = random.choice(['wandering', 'shopping'])
        # spawn
        if maze[self.enemy.top // CELL_SIZE][self.enemy.left // CELL_SIZE] in [41, 42, 43, 44]:
            if not self.dead:
                if maze[self.enemy.top // CELL_SIZE][self.enemy.left // CELL_SIZE] in [41, 42]:
                    if self.start:
                        self.start = False
                        self.spawn_time = self.current + SPAWN_TIME
                    self.spawn(game=game)
                else:
                    if self.current == self.spawn_time:
                        self.start_spawn = False
                        self.spawn(game=game)
                    if not self.start_spawn:
                        self.spawn(game=game)
                self.state_time = self.current + 20
                self.state = 'shopping'
            elif self.current == self.revive:
                self.dead = False
        else:
            # change mode while not fleeing
            if self.state != 'fleeing' and not self.tele:
                # get random mode
                if self.current == self.state_time:
                    self.state = random.choice(['wandering', 'chasing', 'shopping'])
                    self.state_time = self.current + 15
                # wandering mode
                if self.state == 'wandering':
                    self.wandering_mode(game=game)
                    self.reach = 0
                # chasing mode
                elif self.state == 'chasing':
                    if not game.player.tele and \
                            maze[game.player.player.top // CELL_SIZE][
                                game.player.player.left // CELL_SIZE] in walkable_path:
                        self.chasing_mode(game=game)
                    else:
                        self.wandering_mode(game=game)
                    self.reach = 0
                #shopping mode
                elif self.state == 'shopping':
                    self.shopping_mode(game=game)
            # fleeing mode
            elif self.state == 'fleeing':
                self.fleeing_mode()
            elif self.tele:
                self.away += 1
                self.wandering_mode(game=game)
                if self.away == 90:
                    self.tele = False
            if self.can_move():
                if self.guard == 441:
                    if self.enemy.left % CELL_SIZE == 0 and self.enemy.top % CELL_SIZE == 0:
                        if game.player.coin_count in range(150, 250):
                            self.speed = 3
                        elif game.player.coin_count in range(50, 150):
                            self.speed = 5
                        elif game.player.coin_count in range(10, 50):
                            self.speed = 6
                        else:
                            self.speed = ENEMIES_SPEED
                self.enemy.left += self.direction.x * self.speed
                self.enemy.top += self.direction.y * self.speed
            self.teleport()

    def spawn(self, game):
        self.enemy.left = game.spawn_pos.x
        self.enemy.top = game.spawn_pos.y
        random.seed(time.time())
        self.direction = self.movement[random.randint(1, 2)]

    def teleport(self):
        if maze[self.enemy.top // CELL_SIZE][self.enemy.left // CELL_SIZE] == -3:
            if self.direction.x == -1 and self.direction.y == 0:
                self.enemy.left = GAME_WIDTH - CELL_SIZE
                self.tele = True
                self.away = 0
        elif maze[self.enemy.top // CELL_SIZE][self.enemy.left // CELL_SIZE] == 3:
            if self.direction.x == 1 and self.direction.y == 0:
                self.enemy.left = 0
                self.tele = True
                self.away = 0
        elif maze[self.tracker.y // CELL_SIZE][self.tracker.x // CELL_SIZE] in portal_value:
            gate1 = maze[self.tracker.y // CELL_SIZE][self.tracker.x // CELL_SIZE]
            gate2 = self.get_other_portal_value(gate1)
            self.get_tele_pos(gate2)
            self.get_dir_after_tele(gate2)
            self.tele = True
            self.away = 0

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
                    self.enemy.top = y * CELL_SIZE
                    self.enemy.left = x * CELL_SIZE

    def get_dir_after_tele(self, portal):
        if portal == 11 or portal == 21:
            self.direction = Vector(-1.0, 0.0)
        elif portal == 12 or portal == 22:
            self.direction = Vector(0.0, -1.0)
        elif portal == 13 or portal == 23:
            self.direction = Vector(1.0, 0.0)
        elif portal == 14 or portal == 24:
            self.direction = Vector(0.0, 1.0)

    def can_move(self):
        # update the tracker depends on the direction of pac-man
        if (self.direction.x == -1 and self.direction.y == 0) or (self.direction.x == 0 and self.direction.y == -1):
            self.tracker.x = int(self.enemy.left + self.direction.x)
            self.tracker.y = int(self.enemy.top + self.direction.y)
        elif (self.direction.x == 1 and self.direction.y == 0) or (self.direction.x == 0 and self.direction.y == 1):
            self.tracker.x = int(self.enemy.left + self.direction.x * TRACKER_SPEED)
            self.tracker.y = int(self.enemy.top + self.direction.y * TRACKER_SPEED)
        # check
        if maze[self.tracker.y // CELL_SIZE][self.tracker.x // CELL_SIZE] in walkable_path:
            return True

    def update(self, game, img):
        self.move(game=game)
        game.game_surface.blit(img, (self.enemy.left, self.enemy.top))
        self.catch(game=game)
        self.power_catch(game=game)

    ################################################ wandering mode ###################################################
    def wandering_mode(self, game):
        if self.enemy.left % CELL_SIZE == 0 and self.enemy.top % CELL_SIZE == 0:
            self.check_pos(game=game)
            for i in range(0, len(self.walkable)):
                if self.movement[self.walkable[i]] == self.direction:
                    if self.walkable[i] == 1:
                        self.pop_i = 2
                    elif self.walkable[i] == 2:
                        self.pop_i = 1
                    elif self.walkable[i] == 3:
                        self.pop_i = 4
                    elif self.walkable[i] == 4:
                        self.pop_i = 3
            for j in range(0, len(self.walkable)):
                if self.walkable[j] == self.pop_i:
                    self.walkable.pop(j)
                    break
            self.direction = self.movement[random.choice(self.walkable)]
            self.pop_all()

    def pop_all(self):
        while not len(self.walkable) == 0:
            self.walkable.pop()

    def check_pos(self, game):
        for p in self.movement:
            self.velocity = self.movement[p]
            if (self.velocity.x == -1 and self.velocity.y == 0) or (self.velocity.x == 0 and self.velocity.y == -1):
                self.checker.x = int(self.enemy.left + self.velocity.x)
                self.checker.y = int(self.enemy.top + self.velocity.y)
            elif (self.velocity.x == 1 and self.velocity.y == 0) or (self.velocity.x == 0 and self.velocity.y == 1):
                self.checker.x = int(self.enemy.left + self.velocity.x * CELL_SIZE)
                self.checker.y = int(self.enemy.top + self.velocity.y * CELL_SIZE)
            # check
            if (self.checker.y // CELL_SIZE) in range(0, 31) and (self.checker.x // CELL_SIZE) in range(0, 28):
                if maze[self.checker.y // CELL_SIZE][self.checker.x // CELL_SIZE] in walkable_path or \
                        (game.check_portal == [0, 1, 1] and
                         maze[self.checker.y // CELL_SIZE][self.checker.x // CELL_SIZE] in portal_value):
                    self.walkable.append(p)

    ############################################### shopping mode #####################################################
    def shopping_mode(self, game):
        e_grid_x = int(self.enemy.left // CELL_SIZE)
        e_grid_y = int(self.enemy.top // CELL_SIZE)
        flag = True
        if self.enemy.left % CELL_SIZE == 0 and self.enemy.top % CELL_SIZE == 0:
            # pinky path
            if self.guard == 411:
                if self.reach == 0:
                    self.des = self.get_checkpoint(411)
                    if e_grid_x * CELL_SIZE == self.des.x * CELL_SIZE and \
                            e_grid_y * CELL_SIZE == self.des.y * CELL_SIZE:
                        flag = False
                        self.reach = 1
                elif self.reach == 1:
                    self.des = self.get_checkpoint(412)
                    if e_grid_x * CELL_SIZE == self.des.x * CELL_SIZE and \
                            e_grid_y * CELL_SIZE == self.des.y * CELL_SIZE:
                        flag = False
                        self.reach = 2
                elif self.reach == 2:
                    self.des = self.get_checkpoint(413)
                    if e_grid_x * CELL_SIZE == self.des.x * CELL_SIZE and \
                            e_grid_y * CELL_SIZE == self.des.y * CELL_SIZE:
                        flag = False
                        self.reach = 3
                elif self.reach == 3:
                    self.des = self.get_checkpoint(414)
                    if e_grid_x * CELL_SIZE == self.des.x * CELL_SIZE and \
                            e_grid_y * CELL_SIZE == self.des.y * CELL_SIZE:
                        flag = False
                        self.reach = 4
                elif self.reach == 4:
                    self.des = self.get_checkpoint(411)
                    if e_grid_x * CELL_SIZE == self.des.x * CELL_SIZE and \
                            e_grid_y * CELL_SIZE == self.des.y * CELL_SIZE:
                        flag = False
                        self.reach = 1
                if flag:
                    path = self.bfs(Point(e_grid_x * CELL_SIZE, e_grid_y * CELL_SIZE),
                                    Point(self.des.x * CELL_SIZE, self.des.y * CELL_SIZE))
                    cell = path[1]
                    self.get_direction(e_grid_x, e_grid_y, cell)
            # inky path
            elif self.guard == 421:
                if self.reach == 0:
                    self.des = self.get_checkpoint(421)
                    if e_grid_x * CELL_SIZE == self.des.x * CELL_SIZE and \
                            e_grid_y * CELL_SIZE == self.des.y * CELL_SIZE:
                        flag = False
                        self.reach = 1
                elif self.reach == 1:
                    self.des = self.get_checkpoint(422)
                    if e_grid_x * CELL_SIZE == self.des.x * CELL_SIZE and \
                            e_grid_y * CELL_SIZE == self.des.y * CELL_SIZE:
                        flag = False
                        self.reach = 2
                elif self.reach == 2:
                    self.des = self.get_checkpoint(423)
                    if e_grid_x * CELL_SIZE == self.des.x * CELL_SIZE and \
                            e_grid_y * CELL_SIZE == self.des.y * CELL_SIZE:
                        flag = False
                        self.reach = 3
                elif self.reach == 3:
                    self.des = self.get_checkpoint(424)
                    if e_grid_x * CELL_SIZE == self.des.x * CELL_SIZE and \
                            e_grid_y * CELL_SIZE == self.des.y * CELL_SIZE:
                        flag = False
                        self.reach = 4
                elif self.reach == 4:
                    self.des = self.get_checkpoint(421)
                    if e_grid_x * CELL_SIZE == self.des.x * CELL_SIZE and \
                            e_grid_y * CELL_SIZE == self.des.y * CELL_SIZE:
                        flag = False
                        self.reach = 1
                if flag:
                    path = self.bfs(Point(e_grid_x * CELL_SIZE, e_grid_y * CELL_SIZE),
                                    Point(self.des.x * CELL_SIZE, self.des.y * CELL_SIZE))
                    cell = path[1]
                    self.get_direction(e_grid_x, e_grid_y, cell)
            # clyde path
            elif self.guard == 431:
                if self.reach == 0:
                    self.des = self.get_checkpoint(431)
                    if e_grid_x * CELL_SIZE == self.des.x * CELL_SIZE and \
                            e_grid_y * CELL_SIZE == self.des.y * CELL_SIZE:
                        flag = False
                        self.reach = 1
                elif self.reach == 1:
                    self.des = self.get_checkpoint(432)
                    if e_grid_x * CELL_SIZE == self.des.x * CELL_SIZE and \
                            e_grid_y * CELL_SIZE == self.des.y * CELL_SIZE:
                        flag = False
                        self.reach = 2
                elif self.reach == 2:
                    self.des = self.get_checkpoint(433)
                    if e_grid_x * CELL_SIZE == self.des.x * CELL_SIZE and \
                            e_grid_y * CELL_SIZE == self.des.y * CELL_SIZE:
                        flag = False
                        self.reach = 3
                elif self.reach == 3:
                    self.des = self.get_checkpoint(434)
                    if e_grid_x * CELL_SIZE == self.des.x * CELL_SIZE and \
                            e_grid_y * CELL_SIZE == self.des.y * CELL_SIZE:
                        flag = False
                        self.reach = 4
                elif self.reach == 4:
                    self.des = self.get_checkpoint(431)
                    if e_grid_x * CELL_SIZE == self.des.x * CELL_SIZE and \
                            e_grid_y * CELL_SIZE == self.des.y * CELL_SIZE:
                        flag = False
                        self.reach = 1
                if flag:
                    path = self.bfs(Point(e_grid_x * CELL_SIZE, e_grid_y * CELL_SIZE),
                                    Point(self.des.x * CELL_SIZE, self.des.y * CELL_SIZE))
                    cell = path[1]
                    self.get_direction(e_grid_x, e_grid_y, cell)
            elif self.guard == 441:
                self.wandering_mode(game=game)

    @staticmethod
    def get_checkpoint(value):
        for x in range(0, COL):
            for y in range(0, ROW):
                if maze[y][x] == value:
                    return Point(x, y)

    ################################################ fleeing mode #####################################################
    def fleeing_mode(self):
        if self.enemy.left == 13 * CELL_SIZE and self.enemy.top == 11 * CELL_SIZE:
            self.speed_up = False
            self.speed = ENEMIES_SPEED
            self.revive = self.current + DEAD_TIME
            if self.guard == 411:
                self.enemy.left = 16 * CELL_SIZE
                self.enemy.top = 13 * CELL_SIZE
            elif self.guard == 421:
                self.enemy.left = 16 * CELL_SIZE
                self.enemy.top = 15 * CELL_SIZE
            elif self.guard == 431:
                self.enemy.left = 11 * CELL_SIZE
                self.enemy.top = 15 * CELL_SIZE
            elif self.guard == 441:
                self.enemy.left = 11 * CELL_SIZE
                self.enemy.top = 13 * CELL_SIZE
        elif self.enemy.left % CELL_SIZE == 0 and self.enemy.top % CELL_SIZE == 0:
            path = self.bfs(Point(self.enemy.left, self.enemy.top), Point(13 * CELL_SIZE, 11 * CELL_SIZE))
            cell = path[1]
            self.get_direction(self.enemy.left // CELL_SIZE, self.enemy.top // CELL_SIZE, cell)
            if not self.speed_up:
                self.speed = 5
                self.speed_up = True

    ################################################ chasing mode #####################################################
    def chasing_mode(self, game):
        if self.enemy.left == game.player.player.left and self.enemy.top == game.player.player.top:
            self.state = random.choice(['wandering', 'shopping'])
        elif self.enemy.left % CELL_SIZE == 0 and self.enemy.top % CELL_SIZE == 0 and \
                game.player.player.left % CELL_SIZE == 0 and game.player.player.top % CELL_SIZE == 0:
            path = self.bfs(Point(self.enemy.left, self.enemy.top),
                            Point(game.player.player.left, game.player.player.top))
            cell = path[1]
            self.get_direction(self.enemy.left // CELL_SIZE, self.enemy.top // CELL_SIZE, cell)

    ########################################### pathfinder ###########################################################
    @staticmethod
    def bfs(start, end):
        queue = [start]
        path = []
        visited = []
        while queue:
            current = queue[0]
            queue.pop(0)
            visited.append(current)
            if current.x == end.x and current.y == end.y:
                break
            else:
                for scan in [Vector(0, -1), Vector(0, 1), Vector(-1, 0), Vector(1, 0)]:
                    if current.x + scan.x * CELL_SIZE in range(0, GAME_WIDTH) and \
                            current.y + scan.y * CELL_SIZE in range(0, GAME_HEIGHT):
                        next_node = Point(current.x + scan.x * CELL_SIZE, current.y + scan.y * CELL_SIZE)
                        if next_node not in visited:
                            if maze[next_node.y // CELL_SIZE][next_node.x // CELL_SIZE] in walkable_path:
                                if maze[next_node.y // CELL_SIZE][next_node.x // CELL_SIZE] == -3:
                                    path.append({'Current': current, 'Next': next_node})
                                    current = Point(current.x + scan.x * CELL_SIZE,
                                                    current.y + scan.y * CELL_SIZE)
                                    visited.append(current)
                                    next_node = Point(GAME_WIDTH - CELL_SIZE, current.y + scan.y * CELL_SIZE)
                                elif maze[next_node.y // CELL_SIZE][next_node.x // CELL_SIZE] == 3:
                                    path.append({'Current': current, 'Next': next_node})
                                    current = Point(current.x + scan.x * CELL_SIZE,
                                                    current.y + scan.y * CELL_SIZE)
                                    visited.append(current)
                                    next_node = Point(0, current.y + scan.y * CELL_SIZE)
                                queue.append(next_node)
                                path.append({'Current': current, 'Next': next_node})
        shortest_path = [end]
        while end != start:
            for step in path:
                if step['Next'] == end:
                    end = step['Current']
                    shortest_path.insert(0, step['Current'])
        return shortest_path

    ################################################## other function ##################################################
    def catch(self, game):
        self.current = int(time.time())
        if not game.power and self.state != 'fleeing':
            if not game.hit:
                if self.enemy.left // CELL_SIZE == game.player.player.left // CELL_SIZE and \
                        self.enemy.top // CELL_SIZE == game.player.player.top // CELL_SIZE:
                    game.currentLife -= 1
                    pg.mixer.music.set_volume(0.0)
                    game.lostlife_sound.play()
                    game.hit = True
                    self.next = self.current + IMMUNE_TIME
                    time.sleep(2)
                    pg.mixer.music.set_volume(0.2)
            elif self.current == self.next:
                game.hit = False

    def power_catch(self, game):
        if game.power:
            if not self.state == 'fleeing':
                if self.enemy.left // CELL_SIZE == game.player.player.left // CELL_SIZE and \
                        self.enemy.top // CELL_SIZE == game.player.player.top // CELL_SIZE:
                    game.eatghost_sound.play()
                    self.dead = True
                    self.state = 'fleeing'
                    if game.ghosts == 0:
                        game.player.currentScore += 200
                    if game.ghosts == 1:
                        game.player.currentScore += 400
                    if game.ghosts == 2:
                        game.player.currentScore += 800
                    if game.ghosts == 3:
                        game.player.currentScore += 1600

    def get_direction(self, e_gid_x, e_grid_y, cell):
        if e_gid_x * CELL_SIZE < cell.x and e_grid_y * CELL_SIZE == cell.y:
            self.direction = Vector(1.0, 0.0)
        elif e_gid_x * CELL_SIZE > cell.x and e_grid_y * CELL_SIZE == cell.y:
            self.direction = Vector(-1.0, 0.0)
        elif e_gid_x * CELL_SIZE == cell.x and e_grid_y * CELL_SIZE < cell.y:
            self.direction = Vector(0.0, 1.0)
        elif e_gid_x * CELL_SIZE == cell.x and e_grid_y * CELL_SIZE > cell.y:
            self.direction = Vector(0.0, -1.0)
```
