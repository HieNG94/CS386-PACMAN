
## Source:HieNG94/CS386-PACMAN/enemies.py
```python
def main():
    done = False
    g = Game()
    g.play()
    win = pg.display.set_mode((WINWIDTH, WINHEIGHT))
    while not done:
        win.blit(g.background, (0, 0))
        pacman_image = pg.image.load('image/pacman_image.png')
        pacman_image = pg.transform.scale(pacman_image, (200, 250))
        win.blit(pacman_image, (20, 300))
        g.draw_text('[SPACE] MAIN MENU   /   [ESC] QUIT', win, (WINWIDTH // 2 - 350, WINHEIGHT // 2 - 50),
                    TEXT_SIZE + 20,
                    TEXT_FONT, ORANGE)
        pg.display.update()
        for event in pg.event.get():
            if event.type == pg.QUIT:
                done = True
            if event.type == pg.KEYDOWN:
                if event.key == pg.K_SPACE:
                    main()
                elif event.key == pg.K_ESCAPE:
                    done = True
    pg.quit()
    sys.exit()


if __name__ == '__main__':
    main()
```
