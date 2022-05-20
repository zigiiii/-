from pygame import*
from random import randint

class Game_sprite():
    def __init__(self, img, w, h, x, y):
        self.image = transform.scale(img, (w, h))
        self.rect = self.image.get_rect()
        self.rect.x = x
        self.rect.y = y
    
    def update(self):
        screen.blit(self.image, self.rect)

class Bird(Game_sprite):
    def __init__(self):
        super().__init__(image.load('bird.png'),34 , 24,10,200)
        self.costumes =[image.load('b1.png'), image.load('b2.png'), image.load('b3.png')]
        self.gravity = 0
    def update(self):

        self.rect.y += self.gravity

        if self.gravity <= 7:
            self.gravity += 0.5
        super().update()

class PipeDown(Game_sprite):
    def __init__(self):
        super().__init__(image.load('pipedown.png'),52 ,320 ,550,200)

    def update(self):
        global score
        if self.rect.x <= -60:
            self.rect.x = randint(400, 500)
            self.rect.y = randint(200, 400)
            score += 5
        self.rect.x -= 5
        
        super().update()
class PipeUp(Game_sprite): 
    def __init__(self):
        super().__init__(image.load('pipeup.png'),52 ,320 ,550,200)

    def update(self, pipedown):
        self.rect.x = pipedown.rect.x
        self.rect.y = pipedown.rect.y - 320 - 150
        super().update()
class Base(Game_sprite):
    pass 
screen = display.set_mode((400, 500))
display.set_caption('FlappyBird')
background = transform.scale(image.load('background.png'), (400,500))
bird = Bird()
pipedown = PipeDown()
pipeup = PipeUp()

score = 0
font.init()
f = font.Font(None, 36)
scoretext = f.render(str(score), 1, (255, 0, 0))

highscore = 0
with open('highscore.txt', 'r') as file:
    highscore = int(file.read())

hightext = f.render('Highscore: ' + str(highscore), 1, (255, 0, 0))

clock = time.Clock()
finish = False
game = True
while game:
    clock.tick(60)
    for e in event.get():
        if e.type == QUIT:
            game = False

        if e.type == KEYDOWN:
            if e.key == K_SPACE:
                if not(finish):
                    bird.gravity = -7
                else:
                    score = 0
                    bird = Bird()
                    pipedown = PipeDown()
                    pipeup = PipeUp()
                    finish = False
                   

    if not(finish):
        if score > highscore:
            highscore = int(score)
            hightext = f.render('Highscore: ' + str(highscore), 1, (255, 0, 0))

        if bird.rect.colliderect(pipedown.rect):
            finish = True
        if bird.rect.colliderect(pipeup.rect):
            finish = True

        score += 0.1
        scoretext = f.render(str(int(score)), 1, (255, 0, 0))

        screen.blit(background, (0, 0))
        bird.update()
        pipedown.update()
        pipeup.update(pipedown)
        screen.blit(scoretext, (10,10))
        screen.blit(hightext, (10,450))
        if bird.rect.y > 500:
            finish = True

    display.update()

with open('highscore.txt', 'w') as file:
    file.write(str(highscore))
    
