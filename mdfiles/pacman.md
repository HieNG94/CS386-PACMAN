
## Source: HieNG94/CS386-PACMAN/pacman.py
```pthon
class Game:
    def __init__(self):
        pg.display.set_caption('PAC-MAN')
        self.surface = pg.display.set_mode((WINWIDTH, WINHEIGHT))
        self.game_surface = pg.Surface((GAME_WIDTH, GAME_HEIGHT))
        self.finished = False
        self.state = 'open'
        self.mainClock = pg.time.Clock()
        self.highScore = 0
        self.currentLife = 3
        self.check_portal = [0, 0, 0]
        self.laser_done = True
        self.starting_pos = Point()
        self.e1_start_pos = Point()
        self.e2_start_pos = Point()
        self.e3_start_pos = Point()
        self.e4_start_pos = Point()
        self.spawn_pos = Point()
        self.current = time.time()
        self.power = False
        self.level = 0
        self.e1_speed = ENEMIES_SPEED
        self.up = False
        self.ghosts = 0
        self.hit = False
        self.pause = False
        self.reset = False
        self.warning = False
        self.sound_check_1 = False
        self.sound_check_2 = False
        self.player = Player(self, self.starting_pos, Vector())
        self.blinky = Enemies(self, self.e1_start_pos, ENEMIES_SPEED, guard=441)
        self.pinky = Enemies(self, self.e2_start_pos, ENEMIES_SPEED, guard=411)
        self.inky = Enemies(self, self.e3_start_pos, ENEMIES_SPEED, guard=421)
        self.clyde = Enemies(self, self.e4_start_pos, ENEMIES_SPEED, guard=431)
        self.laser = Laser(self, self.player.player.x, self.player.player.y,
                           self.player.direction)
        #################################### image ####################################################################
        # logo
        self.logo = pg.image.load('image/logo.png')
        self.logo2 = pg.image.load('image/logo2.png')
        # life
        self.heart = pg.image.load('image/heart.png')
        self.heart = pg.transform.scale(self.heart, (40, 40))
        # fruits
        self.strawberry = pg.image.load('image/strawberry.png')
        self.cherry = pg.image.load('image/cherry.png')
        self.apple = pg.image.load('image/apple.png')
        self.kiwi = pg.image.load('image/kiwi.png')
        # portals
        self.blue_left = pg.image.load('image/portal_left_b.png')
        self.blue_left = pg.transform.scale(self.blue_left, (20, 40))
        self.blue_right = pg.image.load('image/portal_right_b.png')
        self.blue_right = pg.transform.scale(self.blue_right, (20, 40))
        self.blue_up = pg.image.load('image/portal_up_b.png')
        self.blue_up = pg.transform.scale(self.blue_up, (40, 20))
        self.blue_down = pg.image.load('image/portal_down_b.png')
        self.blue_down = pg.transform.scale(self.blue_down, (40, 20))
        self.green_left = pg.image.load('image/portal_left_g.png')
        self.green_left = pg.transform.scale(self.green_left, (20, 40))
        self.green_right = pg.image.load('image/portal_right_g.png')
        self.green_right = pg.transform.scale(self.green_right, (20, 40))
        self.green_up = pg.image.load('image/portal_up_g.png')
        self.green_up = pg.transform.scale(self.green_up, (40, 20))
        self.green_down = pg.image.load('image/portal_down_g.png')
        self.green_down = pg.transform.scale(self.green_down, (40, 20))
        self.purple_left = pg.image.load('image/fixed_portal_left.png')
        self.purple_left = pg.transform.scale(self.purple_left, (20, 50))
        self.purple_right = pg.image.load('image/fixed_portal_right.png')
        self.purple_right = pg.transform.scale(self.purple_right, (20, 50))
        # background
        self.background = pg.image.load('image/background.png')
        self.background = pg.transform.scale(self.background, (WINWIDTH, WINHEIGHT))
        self.sub_win_1 = pg.image.load('image/sub_win_1.png')
        self.sub_win_1 = pg.transform.scale(self.sub_win_1, (600, 600))
        self.sub_win_2 = pg.image.load('image/sub_win_2.png')
        self.sub_win_2 = pg.transform.scale(self.sub_win_2, (600, 400))
        # maze
        self.brick = pg.image.load('image/brick.png')
        self.brick = pg.transform.scale(self.brick, (30, 30))
        # pacman
        self.pacman_left = [pg.image.load('image/pacman_left_1.png'), pg.image.load('image/pacman_left_2.png'),
                            pg.image.load('image/pacman_left_3.png'), pg.image.load('image/pacman_left_4.png')]
        self.pacman_right = [pg.image.load('image/pacman_right_1.png'), pg.image.load('image/pacman_right_2.png'),
                             pg.image.load('image/pacman_right_3.png'), pg.image.load('image/pacman_right_4.png')]
        self.pacman_up = pg.image.load('image/pacman_up.png')
        self.pacman_down = [pg.image.load('image/pacman_down_1.png'), pg.image.load('image/pacman_down_2.png'),
                            pg.image.load('image/pacman_down_3.png'), pg.image.load('image/pacman_down_4.png')]
        # blinky
        self.blinky_left = pg.image.load('image/blinky_left.png')
        self.blinky_left = pg.transform.scale(self.blinky_left, (30, 30))
        self.blinky_right = pg.image.load('image/blinky_right.png')
        self.blinky_right = pg.transform.scale(self.blinky_right, (30, 30))
        self.blinky_up = pg.image.load('image/blinky_up.png')
        self.blinky_up = pg.transform.scale(self.blinky_up, (30, 30))
        self.blinky_down = pg.image.load('image/blinky_down.png')
        self.blinky_down = pg.transform.scale(self.blinky_down, (30, 30))
        # pinky
        self.pinky_left = pg.image.load('image/pinky_left.png')
        self.pinky_left = pg.transform.scale(self.pinky_left, (30, 30))
        self.pinky_right = pg.image.load('image/pinky_right.png')
        self.pinky_right = pg.transform.scale(self.pinky_right, (30, 30))
        self.pinky_up = pg.image.load('image/pinky_up.png')
        self.pinky_up = pg.transform.scale(self.pinky_up, (30, 30))
        self.pinky_down = pg.image.load('image/pinky_down.png')
        self.pinky_down = pg.transform.scale(self.pinky_down, (30, 30))
        # inky
        self.inky_left = pg.image.load('image/inky_left.png')
        self.inky_left = pg.transform.scale(self.inky_left, (30, 30))
        self.inky_right = pg.image.load('image/inky_right.png')
        self.inky_right = pg.transform.scale(self.inky_right, (30, 30))
        self.inky_up = pg.image.load('image/inky_up.png')
        self.inky_up = pg.transform.scale(self.inky_up, (30, 30))
        self.inky_down = pg.image.load('image/inky_down.png')
        self.inky_down = pg.transform.scale(self.inky_down, (30, 30))
        # clyde
        self.clyde_left = pg.image.load('image/clyde_left.png')
        self.clyde_left = pg.transform.scale(self.clyde_left, (30, 30))
        self.clyde_right = pg.image.load('image/clyde_right.png')
        self.clyde_right = pg.transform.scale(self.clyde_right, (30, 30))
        self.clyde_up = pg.image.load('image/clyde_up.png')
        self.clyde_up = pg.transform.scale(self.clyde_up, (30, 30))
        self.clyde_down = pg.image.load('image/clyde_down.png')
        self.clyde_down = pg.transform.scale(self.clyde_down, (30, 30))
        # ghosts in power mode
        self.ghosts_blue_white = [pg.image.load('image/ghost_blue_power_mode.png'),
                                  pg.image.load('image/ghost_white_power_mode.png')]
        self.ghosts_blue_white[0] = pg.transform.scale(self.ghosts_blue_white[0], (CELL_SIZE, CELL_SIZE))
        self.ghosts_blue_white[1] = pg.transform.scale(self.ghosts_blue_white[1], (CELL_SIZE, CELL_SIZE))
        self.eyes_left = pg.image.load('image/eyes_left.png')
        self.eyes_left = pg.transform.scale(self.eyes_left, (CELL_SIZE, CELL_SIZE))
        self.eyes_right = pg.image.load('image/eyes_right.png')
        self.eyes_right = pg.transform.scale(self.eyes_right, (CELL_SIZE, CELL_SIZE))
        # level 250 screen
        self.split = pg.image.load('image/split_screen.png')
        self.split = pg.transform.scale(self.split, (GAME_WIDTH // 2, GAME_HEIGHT))
        ########################################## sound ##############################################################
        self.coin_sound = pg.mixer.Sound('sound/coin.wav')
        self.pause_sound = pg.mixer.Sound('sound/pause.wav')
        self.lv_sound = pg.mixer.Sound('sound/level.wav')
        self.go_sound = pg.mixer.Sound('sound/gameover.wav')
        self.lostlife_sound = pg.mixer.Sound('sound/lostlife.wav')
        self.eatfruit_sound = pg.mixer.Sound('sound/eatfruit.wav')
        self.eatghost_sound = pg.mixer.Sound('sound/eatghost.wav')
        self.laser_sound = pg.mixer.Sound('sound/laser.wav')
        self.tele_sound = pg.mixer.Sound('sound/tele.wav')
        self.reload_sound = pg.mixer.Sound('sound/reload.wav')
        self.gun_empty_sound = pg.mixer.Sound('sound/gun_empty.wav')
        self.portal_open_sound = pg.mixer.Sound('sound/portal_open.wav')

    ##################################### game process ################################################################
    def play(self):
        while not self.finished:
            if self.state == 'open':
                self.get_sound()
                self.sound_check_1 = True
                self.sound_check_2 = False
                self.open_event()
                self.open_update()
            elif self.state == 'highscore':
                self.open_event()
                self.open_update()
                self.show_highscore()
            elif self.state == 'instruction':
                self.open_event()
                self.open_update()
                self.instruction()
            elif self.state == 'playing':
                self.play_event()
                self.play_update(self)
            elif self.state == 'gameover':
                self.gameover_event()
                self.gameover_update()
            self.mainClock.tick(60)

    ############################################# Opening ############################################################
    def open_event(self):
        for event in pg.event.get():
            if event.type == pg.QUIT:
                pg.quit()
                sys.exit()
            elif event.type == pg.KEYDOWN:
                if event.key == pg.K_SPACE:
                    self.load()
                    self.state = 'playing'
                elif event.key == pg.K_F2:
                    self.state = 'highscore'
                elif event.key == pg.K_F1:
                    self.state = 'instruction'
            elif self.state in ['highscore', 'instruction']:
                if event.type == pg.KEYUP and event.key == pg.K_ESCAPE:
                    self.state = 'open'
            elif event.type == pg.KEYUP and event.key == pg.K_ESCAPE and self.state == 'open':
                self.finished = True

    def instruction(self):
        instruction_win = pg.Surface((600, 600))
        instruction_win.blit(self.sub_win_1, (0, 0))
        self.draw_text('MOVEMENT:', instruction_win, (50, 40), TEXT_SIZE, TEXT_FONT, WHITE)
        self.draw_text('    [UP], [DOWN], [LEFT], [RIGHT] or [W], [S], [A], [D]', instruction_win, (50, 80),
                       TEXT_SIZE + 5, 'arial', WHITE)
        self.draw_text('PORTAL:', instruction_win, (50, 120), TEXT_SIZE, TEXT_FONT, WHITE)
        self.draw_text('    LAUNCH:', instruction_win, (50, 160), TEXT_SIZE, TEXT_FONT, WHITE)
        self.draw_text('        [Z], [X] or [K], [L]', instruction_win, (150, 160), TEXT_SIZE + 5, 'arial', WHITE)
        self.draw_text('    RESET:', instruction_win, (50, 200), TEXT_SIZE, TEXT_FONT, WHITE)
        self.draw_text('        [C] or [J]', instruction_win, (150, 200), TEXT_SIZE + 5, 'arial', WHITE)
        self.draw_text('[ESC] TO PAUSE DURING GAME', instruction_win, (50, 240), TEXT_SIZE + 5, 'arial', WHITE)
        self.draw_text('[F9] TO RESTART', instruction_win, (50, 280), TEXT_SIZE + 5, 'arial', WHITE)
        self.draw_text('WARNING: CANNOT RESUME GAME IF PRESSED [F9]', instruction_win, (50, 320), TEXT_SIZE + 5,
                       'arial', ORANGE)
        self.draw_text('[ESC]', instruction_win, (50, 530), TEXT_SIZE, TEXT_FONT, WHITE)
        pg.draw.rect(instruction_win, RED, [0, 0, 600, 20])
        pg.draw.rect(instruction_win, DARKORANGE, [0, 0, 20, 600])
        pg.draw.rect(instruction_win, DEEPSKYBLUE, [580, 0, 20, 600])
        pg.draw.rect(instruction_win, PINK, [0, 580, 600, 20])
        self.surface.blit(instruction_win, (WINWIDTH // 2 - 300, WINHEIGHT // 2 - 300))
        pg.display.update()

    def show_highscore(self):
        score_win = pg.Surface((600, 600))
        score_win.blit(self.sub_win_1, (0, 0))
        score_list = []
        with open('score.txt', 'r') as file:
            for line in file:
                current_line = line[:-1]
                score_list.append(current_line)
        file.close()
        for i in range(len(score_list)):
            self.draw_text('{}: '.format(i + 1), score_win, (50, 40 * (i + 1)), TEXT_SIZE + 20, TEXT_FONT,
                           WHITE)
            self.draw_text('{}'.format(score_list[i]), score_win, (120, 40 * (i + 1)), TEXT_SIZE + 20, TEXT_FONT,
                           WHITE)
        self.draw_text('[ESC]', score_win, (50, 530), TEXT_SIZE, TEXT_FONT, WHITE)
        pg.draw.rect(score_win, RED, [0, 0, 600, 20])
        pg.draw.rect(score_win, DARKORANGE, [0, 0, 20, 600])
        pg.draw.rect(score_win, DEEPSKYBLUE, [580, 0, 20, 600])
        pg.draw.rect(score_win, PINK, [0, 580, 600, 20])
        self.surface.blit(score_win, (WINWIDTH // 2 - 300, WINHEIGHT // 2 - 300))
        pg.display.update()

    def open_update(self):
        self.surface.blit(self.background, (0, 0))
        file = open('score.txt', 'r')
        self.logo = pg.transform.scale(self.logo, (WINWIDTH - 250, 150))
        self.surface.blit(self.logo, (WINWIDTH // 2 - 460, WINHEIGHT // 2 - 350))
        self.logo2 = pg.transform.scale(self.logo2, (WINWIDTH - 100, 350))
        self.surface.blit(self.logo2, (WINWIDTH // 2 - 550, WINHEIGHT // 2 + 150))
        self.draw_text('PRESS [SPACE] TO START', self.surface, [WINWIDTH // 2 - 105, WINHEIGHT // 2 + 60], TEXT_SIZE,
                       TEXT_FONT, YELLOW)
        self.draw_text('PRESS [F1] TO HELP', self.surface, [WINWIDTH // 2 - 85, WINHEIGHT // 2 + 100], TEXT_SIZE,
                       TEXT_FONT, YELLOW)
        self.draw_text('PRESS [F2] TO HIGHSCORE', self.surface, [WINWIDTH // 2 - 110, WINHEIGHT // 2 + 140], TEXT_SIZE,
                       TEXT_FONT, YELLOW)
        self.draw_text('PRESS [ESC] TO QUIT', self.surface, [WINWIDTH // 2 - 90, WINHEIGHT // 2 + 180], TEXT_SIZE,
                       TEXT_FONT, YELLOW)
        self.draw_text('HIGH SCORE: {}'.format(file.readline().rstrip()), self.surface, [20, 20], TEXT_SIZE,
                       TEXT_FONT, WHITE)
        file.close()
        if self.state == 'open':
            pg.display.update()

    ################################################# Playing ########################################################
    def play_event(self):
        movement = {K_a: Vector(-1, 0), K_d: Vector(1, 0), K_w: Vector(0, -1), K_s: Vector(0, 1)}
        portals = {K_c: 0, K_z: 1, K_x: 2}
        translate_arrow = {K_LEFT: K_a, K_RIGHT: K_d, K_UP: K_w, K_DOWN: K_s}
        translate_portal = {K_j: K_c, K_k: K_z, K_l: K_x}
        shooting_keys = (K_j, K_z, K_k, K_x, K_l, K_c)
        left_right_up_down = (K_LEFT, K_a, K_RIGHT, K_d, K_UP, K_w, K_DOWN, K_s)
        for event in pg.event.get():
            if event.type == pg.QUIT:
                self.finished = True
            if event.type == pg.KEYDOWN:
                k = event.key
                if k in left_right_up_down:
                    if k in translate_arrow.keys():
                        k = translate_arrow[k]
                    self.player.velocity = movement[k]
                if k in shooting_keys and self.player.currentScore != 0:
                    if k in translate_portal.keys():
                        k = translate_portal[k]
                    if event.key not in [K_c, K_j] and (self.check_portal[1] != 1 or self.check_portal[2] != 1):
                        self.laser_sound.play()
                    if event.key not in [K_c, K_j] and self.check_portal[1] == 1 and self.check_portal[2] == 1:
                        self.gun_empty_sound.play()
                    elif event.key in [K_c, K_j]:
                        self.reload_sound.play()
                    if self.check_portal[portals[k]] == 0 and self.laser_done:
                        self.laser = Laser(self, self.player.player.x, self.player.player.y,
                                           self.player.direction, portals[k])
                        self.laser.draw_laser(self)
                        if portals[k] == 0:
                            for i in range(len(self.check_portal)):
                                self.check_portal[i] = 0
                            for y in range(ROW):
                                for x in range(COL):
                                    if maze[y][x] in portal_value:
                                        maze[y][x] = 1
                            self.laser_done = True
                        else:
                            self.check_portal[portals[k]] = 1
                            self.laser_done = False
                if event.key == pg.K_ESCAPE:
                    self.pause = not self.pause
                    self.pause_sound.play()

    def play_update(self, game):
        if not self.pause:
            self.surface.blit(self.background, (0, 0))
            if self.power:
                self.draw_text('POWER!!', self.game_surface, [GAME_WIDTH // 2 - 40, GAME_HEIGHT // 2 - 45], TEXT_SIZE,
                               TEXT_FONT, DARKORANGE)
            else:
                self.draw_text('POWER!!', self.game_surface, [GAME_WIDTH // 2 - 40, GAME_HEIGHT // 2 - 45], TEXT_SIZE,
                               TEXT_FONT, BLACK)
            self.surface.blit(self.game_surface, (SIDE_BUFFER, BUFFER))
            self.draw_text('SCORE: {}'.format(self.player.currentScore), self.surface, [SIDE_BUFFER, 10],
                           TEXT_SIZE + 10, TEXT_FONT, WHITE)
            temp_list = []
            if self.currentLife == 0:
                with open('score.txt', 'r') as file:
                    for line in file:
                        current_line = line[:-1]
                        temp_list.append(int(current_line))
                file.close()
                temp_list.sort()
                if temp_list[0] < game.player.currentScore:
                    temp_list[0] = game.player.currentScore
                temp_list.sort(reverse=True)
                with open('score.txt', 'w') as file:
                    for i in temp_list:
                        file.write('%s\n' % i)
                file.close()
                self.go_sound.play()
                self.state = 'gameover'
            self.draw_text('LIFE: ', self.surface, [WINWIDTH - 300, 10],
                           TEXT_SIZE + 10, TEXT_FONT, RED)
            for i in range(self.currentLife):
                self.surface.blit(self.heart, (WINWIDTH - 220 + (60 * i), 10))
            self.draw_text('LEVER: {}'.format(self.level), self.surface, (WINWIDTH - 500, 10), TEXT_SIZE + 10,
                           TEXT_FONT,
                           WHITE)
            self.draw_text('PORTAL: ', self.surface, [WINWIDTH - 300, 100], TEXT_SIZE + 20, TEXT_FONT, WHITE)
            if self.check_portal[1] == 0:
                flag1 = 'O'
            else:
                flag1 = 'X'
            self.draw_text(flag1, self.surface, [WINWIDTH - 300, 150], TEXT_SIZE + 100, TEXT_FONT, GREEN)
            if self.check_portal[2] == 0:
                flag2 = 'O'
            else:
                flag2 = 'X'
            self.draw_text(flag2, self.surface, [WINWIDTH - 150, 150], TEXT_SIZE + 100, TEXT_FONT, BLUE)
            if self.check_portal[2] == 1 and self.check_portal[1] == 1:
                flag3 = 'RELOAD'
            else:
                flag3 = ''
            self.draw_text(flag3, self.surface, [WINWIDTH - 310, 300], TEXT_SIZE + 50, TEXT_FONT, IVORY)
            self.draw_maze()
            if self.level >= 250:
                self.game_surface.blit(self.split, (GAME_WIDTH // 2, 0))
            self.player.update(self)
            if not self.power:
                if self.blinky.direction.x == -1 and self.blinky.direction.y == 0:
                    self.blinky.update(self, self.blinky_left)
                elif self.blinky.direction.x == 1 and self.blinky.direction.y == 0:
                    self.blinky.update(self, self.blinky_right)
                elif self.blinky.direction.x == 0 and self.blinky.direction.y == -1:
                    self.blinky.update(self, self.blinky_up)
                elif self.blinky.direction.x == 0 and self.blinky.direction.y == 1:
                    self.blinky.update(self, self.blinky_down)
                else:
                    self.blinky.update(self, self.blinky_right)
            elif self.blinky.state == 'fleeing':
                if self.blinky.direction.x == -1 and self.blinky.direction.y == 0 or \
                        self.blinky.direction.x == 0 and self.blinky.direction.y == -1:
                    self.blinky.update(self, self.eyes_left)
                else:
                    self.blinky.update(self, self.eyes_right)
            elif self.power:
                if self.warning:
                    self.blinky.update(self, self.ghosts_blue_white[self.blinky.walkCount // 6])
                elif not self.warning:
                    self.blinky.update(self, self.ghosts_blue_white[0])
            self.draw_text('Blinky:', self.surface, [WINWIDTH - 300, 450], TEXT_SIZE + 15,
                           TEXT_FONT, RED)
            self.draw_text('  {}'.format(self.blinky.state), self.surface, [WINWIDTH - 300, 500], TEXT_SIZE + 15,
                           TEXT_FONT, RED)
            if not self.power:
                if self.pinky.direction.x == -1 and self.pinky.direction.y == 0:
                    self.pinky.update(self, self.pinky_left)
                elif self.pinky.direction.x == 1 and self.pinky.direction.y == 0:
                    self.pinky.update(self, self.pinky_right)
                elif self.pinky.direction.x == 0 and self.pinky.direction.y == -1:
                    self.pinky.update(self, self.pinky_up)
                elif self.pinky.direction.x == 0 and self.pinky.direction.y == 1:
                    self.pinky.update(self, self.pinky_down)
                else:
                    self.pinky.update(self, self.pinky_left)
            elif self.pinky.state == 'fleeing':
                if self.pinky.direction.x == -1 and self.pinky.direction.y == 0 or \
                        self.pinky.direction.x == 0 and self.pinky.direction.y == -1:
                    self.pinky.update(self, self.eyes_left)
                else:
                    self.pinky.update(self, self.eyes_right)
            elif self.power:
                if self.warning:
                    self.pinky.update(self, self.ghosts_blue_white[self.pinky.walkCount // 6])
                elif not self.warning:
                    self.pinky.update(self, self.ghosts_blue_white[0])
            self.draw_text('Pinky:', self.surface, [WINWIDTH - 300, 550], TEXT_SIZE + 15,
                           TEXT_FONT, PINK)
            self.draw_text(self.pinky.state, self.surface, [WINWIDTH - 300, 600], TEXT_SIZE + 15, TEXT_FONT, PINK)
            if not self.power:
                if self.inky.direction.x == -1 and self.inky.direction.y == 0:
                    self.inky.update(self, self.inky_left)
                elif self.inky.direction.x == 1 and self.inky.direction.y == 0:
                    self.inky.update(self, self.inky_right)
                elif self.inky.direction.x == 0 and self.inky.direction.y == -1:
                    self.inky.update(self, self.inky_up)
                elif self.inky.direction.x == 0 and self.inky.direction.y == 1:
                    self.inky.update(self, self.inky_down)
                else:
                    self.inky.update(self, self.inky_left)
            elif self.inky.state == 'fleeing':
                if self.inky.direction.x == -1 and self.inky.direction.y == 0 or \
                        self.inky.direction.x == 0 and self.inky.direction.y == -1:
                    self.inky.update(self, self.eyes_left)
                else:
                    self.inky.update(self, self.eyes_right)
            elif self.power:
                if self.warning:
                    self.inky.update(self, self.ghosts_blue_white[self.inky.walkCount // 6])
                elif not self.warning:
                    self.inky.update(self, self.ghosts_blue_white[1])
            self.draw_text('Inky:', self.surface, [WINWIDTH - 300, 650], TEXT_SIZE + 15,
                           TEXT_FONT, DEEPSKYBLUE)
            self.draw_text(self.inky.state, self.surface, [WINWIDTH - 300, 700], TEXT_SIZE + 15, TEXT_FONT, DEEPSKYBLUE)
            if not self.power:
                if self.clyde.direction.x == -1 and self.clyde.direction.y == 0:
                    self.clyde.update(self, self.clyde_left)
                elif self.clyde.direction.x == 1 and self.clyde.direction.y == 0:
                    self.clyde.update(self, self.clyde_right)
                elif self.clyde.direction.x == 0 and self.clyde.direction.y == -1:
                    self.clyde.update(self, self.clyde_up)
                elif self.clyde.direction.x == 0 and self.clyde.direction.y == 1:
                    self.clyde.update(self, self.clyde_down)
                else:
                    self.clyde.update(self, self.clyde_right)
            elif self.clyde.state == 'fleeing':
                if self.clyde.direction.x == -1 and self.clyde.direction.y == 0 or \
                        self.clyde.direction.x == 0 and self.clyde.direction.y == -1:
                    self.clyde.update(self, self.eyes_left)
                else:
                    self.clyde.update(self, self.eyes_right)
            elif self.power:
                if self.warning:
                    self.clyde.update(self, self.ghosts_blue_white[self.clyde.walkCount // 6])
                elif not self.warning:
                    self.clyde.update(self, self.ghosts_blue_white[1])
            self.draw_text('Clyde:', self.surface, [WINWIDTH - 300, 750], TEXT_SIZE + 15,
                           TEXT_FONT, DARKORANGE)
            self.draw_text(self.clyde.state, self.surface, [WINWIDTH - 300, 800], TEXT_SIZE + 15, TEXT_FONT, DARKORANGE)
            if not self.laser_done:
                self.laser.draw_laser(self)
            self.ghosts = 0
            if self.blinky.dead:
                self.ghosts += 1
            if self.pinky.dead:
                self.ghosts += 1
            if self.inky.dead:
                self.ghosts += 1
            if self.clyde.dead:
                self.ghosts += 1
            if self.ghosts == 4 and not self.up:
                self.level += 1
                self.up = True
                self.lv_sound.play()
            elif self.ghosts != 4 and not self.clyde.dead:
                self.up = False
            pg.display.update()
        else:
            self.draw_text('PAUSED', self.surface, (WINWIDTH // 2 - 300, WINHEIGHT // 2 - 100), TEXT_SIZE + 100,
                           TEXT_FONT, ORANGE)
            self.draw_text('[F9] TO RETRY', self.surface, (WINWIDTH // 2 - 300, WINHEIGHT // 2 + 50), TEXT_SIZE + 30,
                           TEXT_FONT, ORANGE)
            for event in pg.event.get():
                if event.type == pg.KEYDOWN and event.key == pg.K_F9:
                    self.finished = True
            pg.display.flip()

    ############################################### game over ########################################################
    def gameover_event(self):
        for event in pg.event.get():
            if event.type == pg.QUIT:
                self.finished = True
            elif event.type == pg.KEYDOWN:
                if event.key == pg.K_F1:
                    self.finished = True
                elif event.key == pg.K_F2:
                    self.state = 'open'

    def gameover_update(self):
        gameover_win = pg.Surface((600, 400))
        gameover_win.blit(self.sub_win_2, (0, 0))
        self.draw_text('GAME OVER', gameover_win, (50, 100), TEXT_SIZE + 70, 'times', YELLOW)
        self.draw_text('PRESS [F1] TO RETRY', gameover_win, (100, 250), TEXT_SIZE, 'arial', IVORY)
        self.draw_text('PRESS [F2] TO MAIN MENU', gameover_win, (100, 280), TEXT_SIZE, 'arial', IVORY)
        pg.draw.rect(gameover_win, RED, [0, 0, 600, 10])
        pg.draw.rect(gameover_win, DARKORANGE, [0, 0, 10, 600])
        pg.draw.rect(gameover_win, DEEPSKYBLUE, [590, 0, 10, 600])
        pg.draw.rect(gameover_win, PINK, [0, 390, 600, 10])
        self.surface.blit(gameover_win, (WINWIDTH // 2 - 300, WINHEIGHT // 2 - 200))
        pg.display.update()

    ############################################### Other Functions ##################################################
    @staticmethod
    def draw_text(word, surface, pos, size, font, text_color):
        font = pg.font.SysFont(font, size)
        text = font.render(word, True, text_color)
        surface.blit(text, pos)

    def load(self):
        self.surface.fill(BLACK)
        self.draw_maze()
        self.player = Player(self, self.starting_pos, Vector())
        self.blinky = Enemies(self, self.e1_start_pos, self.e1_speed, guard=441)
        self.pinky = Enemies(self, self.e2_start_pos, ENEMIES_SPEED, guard=411)
        self.inky = Enemies(self, self.e3_start_pos, ENEMIES_SPEED, guard=421)
        self.clyde = Enemies(self, self.e4_start_pos, ENEMIES_SPEED, guard=431)

    def draw_maze(self):
        for x in range(0, GAME_WIDTH // CELL_SIZE):
            for y in range(0, GAME_HEIGHT // CELL_SIZE):
                if maze[y][x] == 1:
                    self.game_surface.blit(self.brick, (x * CELL_SIZE, y * CELL_SIZE))
                elif maze[y][x] == 5:
                    pg.draw.rect(self.game_surface, BLACK,
                                 pg.Rect(x * CELL_SIZE, y * CELL_SIZE, CELL_SIZE, CELL_SIZE))
                    pg.draw.rect(self.game_surface, GREY,
                                 pg.Rect(x * CELL_SIZE, y * CELL_SIZE + 15, CELL_SIZE, CELL_SIZE - 25))
                    pg.draw.rect(self.game_surface, WHITE,
                                 pg.Rect(x * CELL_SIZE, y * CELL_SIZE + 10, CELL_SIZE, CELL_SIZE - 28))
                elif maze[y][x] == 11:
                    self.game_surface.blit(self.blue_right, (x * CELL_SIZE + 10, y * CELL_SIZE - 10))
                elif maze[y][x] == 12:
                    self.game_surface.blit(self.blue_down, (x * CELL_SIZE - 10, y * CELL_SIZE + 10))
                elif maze[y][x] == 13:
                    self.game_surface.blit(self.blue_left, (x * CELL_SIZE, y * CELL_SIZE - 10))
                elif maze[y][x] == 14:
                    self.game_surface.blit(self.blue_up, (x * CELL_SIZE - 10, y * CELL_SIZE))
                elif maze[y][x] == 21:
                    self.game_surface.blit(self.green_right, (x * CELL_SIZE + 10, y * CELL_SIZE - 10))
                elif maze[y][x] == 22:
                    self.game_surface.blit(self.green_down, (x * CELL_SIZE - 10, y * CELL_SIZE + 10))
                elif maze[y][x] == 23:
                    self.game_surface.blit(self.green_left, (x * CELL_SIZE, y * CELL_SIZE - 10))
                elif maze[y][x] == 24:
                    self.game_surface.blit(self.green_up, (x * CELL_SIZE - 10, y * CELL_SIZE))
                elif maze[y][x] == 41:
                    self.e1_start_pos = Point(x * CELL_SIZE, y * CELL_SIZE)
                    pg.draw.rect(self.game_surface, BLACK, pg.Rect(x * CELL_SIZE, y * CELL_SIZE, CELL_SIZE, CELL_SIZE))
                elif maze[y][x] == 42:
                    self.e2_start_pos = Point(x * CELL_SIZE, y * CELL_SIZE)
                    pg.draw.rect(self.game_surface, BLACK, pg.Rect(x * CELL_SIZE, y * CELL_SIZE, CELL_SIZE, CELL_SIZE))
                elif maze[y][x] == 43:
                    self.e3_start_pos = Point(x * CELL_SIZE, y * CELL_SIZE)
                    pg.draw.rect(self.game_surface, BLACK, pg.Rect(x * CELL_SIZE, y * CELL_SIZE, CELL_SIZE, CELL_SIZE))
                elif maze[y][x] == 44:
                    self.e4_start_pos = Point(x * CELL_SIZE, y * CELL_SIZE)
                    pg.draw.rect(self.game_surface, BLACK, pg.Rect(x * CELL_SIZE, y * CELL_SIZE, CELL_SIZE, CELL_SIZE))
                elif maze[y][x] == 8:
                    self.spawn_pos = Point(x * CELL_SIZE, y * CELL_SIZE)
        for x in range(0, GAME_WIDTH // CELL_SIZE):
            for y in range(0, GAME_HEIGHT // CELL_SIZE):
                if maze[y][x] in walkable_path:
                    pg.draw.rect(self.game_surface, BLACK,
                                 pg.Rect(x * CELL_SIZE - 10, y * CELL_SIZE - 10,
                                         CELL_SIZE + 20, CELL_SIZE + 20))
                    if maze[y][x] == 2:
                        self.starting_pos = Point(x * CELL_SIZE, y * CELL_SIZE)
                    elif maze[y][x] == -3:
                        self.game_surface.blit(self.purple_left, (x * CELL_SIZE, y * CELL_SIZE - 10))
                    elif maze[y][x] == 3:
                        self.game_surface.blit(self.purple_right, (x * CELL_SIZE + 10, y * CELL_SIZE - 10))

    def get_sound(self):
        if self.state == 'open':
            if not self.sound_check_1:
                pg.mixer.music.load('sound/intro.mp3')
                pg.mixer.music.play(-1)
                pg.mixer.music.set_volume(0.2)
```
