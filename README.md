# JUEGO-DE-LA-SERPIENTE
import turtle
import time
import random

delay = 0.07
score = 0
high_score = 0

# Configuración de la ventana
wn = turtle.Screen()
wn.title("Juego de la Serpiente 🐍")
wn.bgcolor("black")
wn.setup(width=600, height=600)
wn.tracer(0)

# Tamaño de celda igual al movimiento de la serpiente
tamaño_celda = 20

# Dibujar cuadrícula centrada
tablero = turtle.Turtle()
tablero.speed(0)
tablero.penup()
tablero.hideturtle()

colores_cesped = ["forestgreen", "limegreen"]  # tablero tipo césped

for x in range(-280, 300, tamaño_celda):
    for y in range(-280, 300, tamaño_celda):
        tablero.goto(x, y)
        tablero.pendown()
        tablero.begin_fill()
        tablero.color(colores_cesped[((x//tamaño_celda)+(y//tamaño_celda)) % 2])
        for _ in range(4):
            tablero.forward(tamaño_celda)
            tablero.left(90)
        tablero.end_fill()
        tablero.penup()

# Cabeza de la serpiente
head = turtle.Turtle()
head.speed(0)
head.shape("square")
head.color("orange")  # color inicial llamativo
head.penup()
head.goto(0, 0)
head.direction = "stop"

# Comida
food = turtle.Turtle()
food.speed(0)
food.shape("circle")
food.color("red")
food.penup()
food.goto(0, 100)

segments = []

# Texto del marcador
pen = turtle.Turtle()
pen.speed(0)
pen.color("yellow")  # color llamativo
pen.penup()
pen.hideturtle()
pen.goto(0, 260)
pen.write("Puntos: 0  Récord: 0", align="center", font=("Courier", 24, "bold"))

# Funciones de movimiento
def go_up():
    if head.direction != "down":
        head.direction = "up"

def go_down():
    if head.direction != "up":
        head.direction = "down"

def go_left():
    if head.direction != "right":
        head.direction = "left"

def go_right():
    if head.direction != "left":
        head.direction = "right"

def move():
    if head.direction == "up":
        y = head.ycor()
        head.sety(y + tamaño_celda)
    if head.direction == "down":
        y = head.ycor()
        head.sety(y - tamaño_celda)
    if head.direction == "left":
        x = head.xcor()
        head.setx(x - tamaño_celda)
    if head.direction == "right":
        x = head.xcor()
        head.setx(x + tamaño_celda)

# Controles del teclado
wn.listen()
wn.onkeypress(go_up, "Up")
wn.onkeypress(go_down, "Down")
wn.onkeypress(go_left, "Left")
wn.onkeypress(go_right, "Right")

# Colores llamativos para los segmentos de la serpiente
colores_serpiente = ["orange", "yellow", "red", "blue", "purple", "pink", "lime"]

# Bucle principal del juego
while True:
    wn.update()

    # Colisiones con los bordes
    if head.xcor()>280 or head.xcor()<-280 or head.ycor()>280 or head.ycor()<-280:
        time.sleep(1)
        head.goto(0,0)
        head.direction = "stop"

        for segment in segments:
            segment.goto(1000, 1000)
        segments.clear()

        score = 0
        pen.clear()
        pen.write("Puntos: {}  Récord: {}".format(score, high_score),
                  align="center", font=("Courier", 24, "bold"))

    # Colisión con la comida
    if head.distance(food) < 20:
        x = random.randrange(-280, 281, tamaño_celda)
        y = random.randrange(-280, 281, tamaño_celda)
        food.goto(x, y)

        new_segment = turtle.Turtle()
        new_segment.speed(0)
        new_segment.shape("square")

        # Elegir color cíclico para segmentos
        index_color = len(segments) % len(colores_serpiente)
        new_segment.color(colores_serpiente[index_color])

        new_segment.penup()
        segments.append(new_segment)

        score += 10
        if score > high_score:
            high_score = score

        pen.clear()
        pen.write("Puntos: {}  Récord: {}".format(score, high_score),
                  align="center", font=("Courier", 24, "bold"))

    # Mover cuerpo de la serpiente
    for index in range(len(segments)-1, 0, -1):
        x = segments[index-1].xcor()
        y = segments[index-1].ycor()
        segments[index].goto(x, y)

    if len(segments) > 0:
        x = head.xcor()
        y = head.ycor()
        segments[0].goto(x, y)

    move()

    # Colisión con el propio cuerpo
    for segment in segments:
        if segment.distance(head) < 20:
            time.sleep(1)
            head.goto(0,0)
            head.direction = "stop"

            for segment in segments:
                segment.goto(1000, 1000)
            segments.clear()

            score = 0
            pen.clear()
            pen.write("Puntos: {}  Récord: {}".format(score, high_score),
                      align="center", font=("Courier", 24, "bold"))

    time.sleep(delay)
