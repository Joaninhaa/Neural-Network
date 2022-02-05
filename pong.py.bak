from random import uniform, choice
import numpy as np
import pygame
'''(: Neural Network to play Pong :)'''

WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
FPS = 120
TAM = 16
RES = [56*TAM, 37*TAM]

pygame.init()
pygame.font.init()

inputLayerWeights = np.array([uniform(-1, 1) for i in range(4)])
input2LayerWeights = np.array([uniform(-1, 1) for i in range(4)])

hiddenLayerWeights = np.array([uniform(-1, 1) for i in range(2)])
hidden2LayerWeights = np.array([uniform(-1, 1)  for i in range(2)])

outputLayerWeights = np.array([uniform(-1, 1)  for i in range(2)])
#inputLayerWeights, input2LayerWeights, hiddenLayerWeights, hidden2LayerWeights, outputLayerWeights

class NeuralNetwork():
    def __init__(self, playery, bally, ballx, bias = -1):
        self.py = playery
        self.by = bally
        self.bx = ballx
        self.bias = bias
        self.inputs = np.array([self.py, self.by, self.bx, self.bias])
        global inputLayerWeights, input2LayerWeights, hiddenLayerWeights, hidden2LayerWeights, outputLayerWeights
        
        self.inputLayerWeights = inputLayerWeights
        self.input2LayerWeights = input2LayerWeights

        self.hiddenLayerWeights = hiddenLayerWeights
        self.hidden2LayerWeights = hidden2LayerWeights

        self.outputLayerWeights = outputLayerWeights

    def feedforward(self):
        self.inputHiddenLayer = tanH(round(np.sum(self.inputs * self.inputLayerWeights), 6))
        self.input2HiddenLayer = tanH(round(np.sum(self.inputs * self.input2LayerWeights), 6))

        self.inputOutputLayer = tanH(round(np.sum(self.inputHiddenLayer * self.hiddenLayerWeights), 6))
        self.input2OutputLayer = tanH(round(np.sum(self.input2HiddenLayer * self.hidden2LayerWeights), 6))

        self.output = sigmoide(round(np.sum(np.array([self.inputOutputLayer, self.input2OutputLayer]) * self.outputLayerWeights),6))

        return self.output
     
    
    def refreshWeights(self, erro, alpha=0.01):
        for i in range(len(outputLayerWeights)):
            #global inputLayerWeights, input2LayerWeights, hiddenLayerWeights, hidden2LayerWeights, outputLayerWeights
            if i == 0: 
                entrada = self.inputOutputLayer
            elif i == 1:
                entrada = self.input2OutputLayer
            outputLayerWeights[i] = outputLayerWeights[i] + (alpha * entrada * erro) 
        
        for i in range(len(hiddenLayerWeights)):
            if i == 0:
                entrada = self.inputHiddenLayer
            if i == 1:
                entrada = self.input2HiddenLayer
            hiddenLayerWeights[i] = hiddenLayerWeights[i] + (alpha * entrada * erro) 
        
        for i in range(len(hidden2LayerWeights)):
            if i == 0:
                entrada = self.inputHiddenLayer
            if i == 1:
                entrada = self.input2HiddenLayer
            hidden2LayerWeights[i] = hidden2LayerWeights[i] + (alpha * entrada * erro) 

        for i in range(len(self.inputLayerWeights)):
            inputLayerWeights[i] = inputLayerWeights[i] + (alpha * self.inputs[i] * erro)
        
        for i in range(len(self.input2LayerWeights)):
            input2LayerWeights[i] = input2LayerWeights[i]  + (alpha * self.inputs[i] * erro)
    
    def draw(self, screen, font):
        txtOutput = font.render(str(self.output), True, WHITE)
        screen.blit(txtOutput, (RES[0]-txtOutput.get_width(), 32))
            
            

class Player():
    def __init__(self, x = 48, y = (RES[1]//2)-(TAM//2), color = WHITE):
        self.x = x
        self.y = y
        self.width = TAM
        self.height = TAM*6
        self.color = color
        self.speed = 25
        self.rect = pygame.Rect(self.x, self.y, self.width, self.height)
    
    def draw(self, screen):
        pygame.draw.rect(screen, self.color, self.rect)
    
    def move(self, keys):
        if keys >= 0.8 and self.y >= 0:
            self.y -= self.speed
        elif keys <= 0.5 and self.y <= RES[1]-self.height:
            self.y += self.speed
        
        self.rect.x = self.x
        self.rect.y = self.y
    
    def collision(self, rect, score):
        if self.rect.colliderect(rect.rect):
            score += 1


class Ball():
    def __init__(self, x = (RES[0]//2)-(TAM//2), y = (RES[1]//2)-(TAM//2), color = WHITE, speed = 4):
        self.x = x
        self.y = y
        self.side = TAM
        self.color = color
        self.speedx = speed
        self.speedy = speed
        self.rect = pygame.Rect(self.x, self.y, self.side, self.side)
    
    def draw(self, screen):
        pygame.draw.rect(screen, self.color, self.rect)
    
    def move(self, player, nn, score):
        nn.refreshWeights(((player.rect.y - player.height/2) - self.rect.y)/100)
        if self.rect.x >= RES[0]-self.side:
            self.speedx *= -1
        elif self.rect.x <= 0:
            self.speedx *= -1
            self.erro = ((player.y - player.height/2) - self.y)/10
            nn.refreshWeights(self.erro)
        self.rect.x += self.speedx

        if self.rect.y >= RES[1]-self.side or self.rect.y <= 0:
            self.speedy *= -1
        self.rect.y += self.speedy

        self.x = self.rect.x
        self.y = self.rect.y
        if self.rect.colliderect(player.rect):
            score += 1
            self.speedx *= -1
            

def HUD(screen, score, font, clock):
    txtScore = font.render(str(score), True, WHITE)
    txtFps = font.render(str(int(clock.get_fps())), True, WHITE)

    screen.blit(txtScore, (RES[0]-txtScore.get_width(), 0))
    screen.blit(txtFps, (RES[0]-txtFps.get_width(), TAM))

def sigmoide(x):
    return 1/(1 + np.exp(-x))

def tanH(x):
    return (np.exp(x) - np.exp(-x)) / (np.exp(x) + np.exp(-x))

def main():
    win = pygame.display.set_mode((RES[0], RES[1]))
    clock = pygame.time.Clock()
    run = True
    player = Player()
    ball = Ball()
    myFont = pygame.font.SysFont("Comic Sans MS", TAM)
    s = 0
    


    while run:
        win.fill(BLACK)
        nn = NeuralNetwork(player.y/RES[1], ball.y/RES[1], ball.x/RES[0])
        

        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                run = False
            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_ESCAPE:
                    run = False

        
        keys = nn.feedforward()
        HUD(win, s, myFont, clock)
        player.draw(win)
        player.move(keys)
        ball.draw(win)
        ball.move(player, nn, s)
        nn.refreshWeights((player.y- ball.y)/100)
        nn.draw(win, myFont)

        if player.rect.colliderect(ball.rect) and player.rect.x < ball.rect.x:
            s += 1


        pygame.display.update() 
        clock.tick(FPS)

main()
        