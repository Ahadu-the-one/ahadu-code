import turtle
from random import randint

SIZE = 500

#setup the screen
SCREEN = turtle.Screen()
SCREEN.title("snake")
SCREEN.setup(SIZE + 50, SIZE + 50)
SCREEN.bgcolor("black")

#setup the food
food = turtle.Turtle()
food.up()
food.shape("circle")
food.color("yellow")
food.ht()

#this function return a random position in the screen to put the food in later
def getRandPos():
    return (randint(-SIZE//2, SIZE//2), randint(-SIZE//2, SIZE//2))

#initialise randomly the position of the food
food_coor = getRandPos()

#setup the turtle
snake = turtle.Turtle()
snake.up()
snake.shape("circle")
snake.color("green")
snake.ht()

#initialise the snake
#(the snake is a list of the coordinates of the part of the body - ordered from head to tail)
snake_coor = [(0, 0)]

stamps = []

#which direction for moving
dir_x = 0
dir_y = 0

stop = False

#clean and redraw the screen
def actualise_display():
    tracer = SCREEN.tracer()
    SCREEN.tracer(0)
    
    food.clearstamps(1)
    snake.clearstamps(len(snake_coor))
    
    food.goto(food_coor[0], food_coor[1])
    food.stamp()

    for x, y in snake_coor:
        snake.goto(x, y)
        snake.stamp()
    
    SCREEN.tracer(tracer)
    
#refresh the snake position and check if died
def actualise_pos():
    global snake_coor, food_coor, stop
    avance()
    if isSelfCollision() or isBorderCollision():
        stop = True
    if isFoodCollision():
        append()
        food_coor = getRandPos() 

#main loop :
# - refresh snake position (machine representation)
# - refresh drawings (graphic represenation)
def loop():
    if stop:
        gameOver()
        return
    actualise_pos()
    actualise_display()
    SCREEN.ontimer(loop, 100)
    
#check if the snake eats it self 
def isSelfCollision():
    global snake_coor
    return len(set(snake_coor))<len(snake_coor)

#check if the snake eat food
def isFoodCollision():
    sx, sy = snake_coor[0]
    fx, fy = food_coor
    distance = ((sx-fx)**2 + (sy-fy)**2)**.5
    return distance<20

#check if the snake eat the border of the screen
def isBorderCollision():
    x, y = snake_coor[0]
    return not (-SIZE//2-50<x<SIZE//2+50) or not (-SIZE//2-50<y<SIZE//2+50)

#move the snake in the dir (using the dir global variables declared up)
def avance():
    global snake_coor
    x, y = snake_coor[0]
    x += dir_x*20
    y += dir_y*20
    snake_coor.insert(0, (x, y))
    snake_coor.pop(-1)

#append a new cell to the snake
def append():
    global snake_coor
    a = snake_coor[-1][:]
    snake_coor.append(a)

#change the snake dir
def setDir(x, y):
    global dir_x, dir_y
    dir_x = x
    dir_y = y
    
def right(): setDir(1, 0)
def left(): setDir(-1, 0)
def up(): setDir(0, 1)
def down(): setDir(0, -1)

#display game over
def gameOver():
    d = turtle.Turtle()
    d.up()
    d.ht()
    d.color("blue")
    d.write("GAME OVER\nScore : %04d" % (len(snake_coor)), align="center", font=("Arial", 30, "bold"))
    SCREEN.onclick(lambda*a:[SCREEN.bye(),exit()])

#attach events to keys
SCREEN.onkeypress(up, "w")
SCREEN.onkeypress(down, "s")
SCREEN.onkeypress(right, "d")
SCREEN.onkeypress(left, "a")
SCREEN.listen()
loop() #run the main loop
turtle.mainloop()
