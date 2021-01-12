import pygame as pg
import sys
import time
from pygame.locals import *


XO = 'x'


wygrany = None

remis = None

# szerokosc i wysokosc planszy
szerokosc = 400

wysokosc = 400

#kolor tła
white = (255, 255, 255)

#kolor linii
line_color = (0, 0, 0)

# ustawienie 3x3
board = [[None] * 3, [None] * 3, [None] * 3]

pg.init()

fps = 30

CLOCK = pg.time.Clock()

screen = pg.display.set_mode((szerokosc, wysokosc + 100), 0, 32)

pg.display.set_caption("Kółko i Krzyżyk")

plansza = pg.image.load("tictactoe.png")
obraz_x = pg.image.load("x.png")
y_img = pg.image.load("o.png")

# resizing obrazków
plansza = pg.transform.scale(plansza, (szerokosc, wysokosc + 100))
obraz_x = pg.transform.scale(obraz_x, (80, 80))
obraz_o = pg.transform.scale(y_img, (80, 80))


def game_initiating_window():
    # obrazek na obrazku
    screen.blit(plansza, (0, 0))

    pg.display.update()
    time.sleep(3)
    screen.fill(white)

    # rysowanie linii pionowych
    pg.draw.line(screen, line_color, (szerokosc / 3, 0), (szerokosc / 3, wysokosc), 7)
    pg.draw.line(screen, line_color, (szerokosc / 3 * 2, 0), (szerokosc / 3 * 2, wysokosc), 7)

    # rysowanie linii poziomych
    pg.draw.line(screen, line_color, (0, wysokosc / 3), (szerokosc, wysokosc / 3), 7)
    pg.draw.line(screen, line_color, (0, wysokosc / 3 * 2), (szerokosc, wysokosc / 3 * 2), 7)
    draw_status()


def draw_status():

    global remis

    if wygrany is None:
        message = XO.upper() + " RUCH"
    else:
        message = wygrany.upper() + " WYGRYWA!"
    if remis:
        message = "REMIS!"


    font = pg.font.Font(None, 30)


    text = font.render(message, 1, (255, 255, 255))


    screen.fill((0, 0, 0), (0, 400, 500, 100))
    text_rect = text.get_rect(center=(szerokosc / 2, 500 - 50))
    screen.blit(text, text_rect)
    pg.display.update()


def check_win():
    global board, wygrany, remis


    for row in range(0, 3):
        if ((board[row][0] == board[row][1] == board[row][2]) and (board[row][0] is not None)):
            wygrany = board[row][0]
            pg.draw.line(screen, (250, 0, 0),
                         (0, (row + 1) * wysokosc / 3 - wysokosc / 6),
                         (szerokosc, (row + 1) * wysokosc / 3 - wysokosc / 6),
                         4)
            break


    for col in range(0, 3):
        if ((board[0][col] == board[1][col] == board[2][col]) and (board[0][col] is not None)):
            wygrany = board[0][col]
            pg.draw.line(screen, (250, 0, 0), ((col + 1) * szerokosc / 3 - szerokosc / 6, 0), \
                         ((col + 1) * szerokosc / 3 - szerokosc / 6, wysokosc), 4)
            break


    if (board[0][0] == board[1][1] == board[2][2]) and (board[0][0] is not None):

        wygrany = board[0][0]
        pg.draw.line(screen, (250, 70, 70), (50, 50), (350, 350), 4)

    if (board[0][2] == board[1][1] == board[2][0]) and (board[0][2] is not None):

        wygrany = board[0][2]
        pg.draw.line(screen, (250, 70, 70), (350, 50), (50, 350), 4)

    if (all([all(row) for row in board]) and wygrany is None):
        remis = True
    draw_status()


def drawXO(row, col):
    global board, XO


    if row == 1:
        posx = 30


    if row == 2:

        posx = szerokosc / 3 + 30

    if row == 3:
        posx = szerokosc / 3 * 2 + 30

    if col == 1:
        posy = 30

    if col == 2:
        posy = wysokosc / 3 + 30

    if col == 3:
        posy = wysokosc / 3 * 2 + 30


    board[row - 1][col - 1] = XO

    if (XO == 'x'):


        screen.blit(obraz_x, (posy, posx))
        XO = 'o'

    else:
        screen.blit(obraz_o, (posy, posx))
        XO = 'x'
    pg.display.update()


def user_click():
    x, y = pg.mouse.get_pos()

    if (x < szerokosc / 3):
        col = 1

    elif (x < szerokosc / 3 * 2):
        col = 2

    elif (x < szerokosc):
        col = 3

    else:
        col = None

    if (y < wysokosc / 3):
        row = 1

    elif (y < wysokosc / 3 * 2):
        row = 2

    elif (y < wysokosc):
        row = 3

    else:
        row = None

    if (row and col and board[row - 1][col - 1] is None):
        global XO
        drawXO(row, col)
        check_win()


def reset_game():
    global board, wygrany, XO, remis
    time.sleep(3)
    XO = 'x'
    remis = False
    game_initiating_window()
    wygrany = None
    board = [[None] * 3, [None] * 3, [None] * 3]


game_initiating_window()

while (True):
    for event in pg.event.get():
        if event.type == QUIT:
            pg.quit()
            sys.exit()
        elif event.type is MOUSEBUTTONDOWN:
            user_click()
            if (wygrany or remis):
                reset_game()
    pg.display.update()
    CLOCK.tick(fps)
