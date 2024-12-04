# a
import pygame
import random

# Inicializando o Pygame
pygame.init()

# Definindo as cores
PRETO = (0, 0, 0)
BRANCO = (255, 255, 255)
VERDE = (0, 255, 0)
AZUL = (0, 0, 255)
CINZA = (169, 169, 169)

# Definindo as dimensões da tela
LARGURA_TELA = 800
ALTURA_TELA = 600

# Criando a tela
tela = pygame.display.set_mode((LARGURA_TELA, ALTURA_TELA))
pygame.display.set_caption('Jogo de Moto')

# Definindo o relógio (FPS)
clock = pygame.time.Clock()
FPS = 60

# Definindo a classe da moto
class Moto:
    def __init__(self):
        self.x = LARGURA_TELA // 2
        self.y = ALTURA_TELA - 100
        self.largura = 50
        self.altura = 100
        self.velocidade = 10
        self.imagem = pygame.image.load('moto.png')  # Coloque uma imagem de moto aqui
        self.imagem = pygame.transform.scale(self.imagem, (self.largura, self.altura))
        
    def mover(self, direcao):
        if direcao == 'esquerda' and self.x > 0:
            self.x -= self.velocidade
        elif direcao == 'direita' and self.x < LARGURA_TELA - self.largura:
            self.x += self.velocidade

    def desenhar(self, tela):
        tela.blit(self.imagem, (self.x, self.y))

# Definindo a classe dos obstáculos
class Obstaculo:
    def __init__(self):
        self.largura = random.randint(50, 100)
        self.altura = 30
        self.x = random.randint(0, LARGURA_TELA - self.largura)
        self.y = -30
        self.velocidade = 5
        
    def mover(self):
        self.y += self.velocidade
        
    def desenhar(self, tela):
        pygame.draw.rect(tela, CINZA, (self.x, self.y, self.largura, self.altura))

# Função para exibir a pontuação
def exibir_pontuacao(pontuacao):
    fonte = pygame.font.SysFont(None, 36)
    texto = fonte.render(f'Pontuação: {pontuacao}', True, BRANCO)
    tela.blit(texto, (10, 10))

# Função principal
def main():
    moto = Moto()
    obstaculos = []
    pontuacao = 0
    rodando = True
    direcao = None
    
    while rodando:
        for evento in pygame.event.get():
            if evento.type == pygame.QUIT:
                rodando = False
            elif evento.type == pygame.KEYDOWN:
                if evento.key == pygame.K_LEFT:
                    direcao = 'esquerda'
                elif evento.key == pygame.K_RIGHT:
                    direcao = 'direita'
            elif evento.type == pygame.KEYUP:
                if evento.key in [pygame.K_LEFT, pygame.K_RIGHT]:
                    direcao = None
        
        if direcao:
            moto.mover(direcao)
        
        # Criando obstáculos
        if random.randint(1, 60) == 1:  # Chance de gerar um obstáculo
            obstaculos.append(Obstaculo())
        
        # Movendo e desenhando os obstáculos
        for obstaculo in obstaculos[:]:
            obstaculo.mover()
            if obstaculo.y > ALTURA_TELA:
                obstaculos.remove(obstaculo)
                pontuacao += 1
            
            # Verificar colisão
            if (moto.x < obstaculo.x + obstaculo.largura and
                moto.x + moto.largura > obstaculo.x and
                moto.y < obstaculo.y + obstaculo.altura and
                moto.y + moto.altura > obstaculo.y):
                rodando = False  # Colisão, fim de jogo
        
        # Atualizando a tela
        tela.fill(VERDE)
        moto.desenhar(tela)
        
        for obstaculo in obstaculos:
            obstaculo.desenhar(tela)
        
        # Exibindo a pontuação
        exibir_pontuacao(pontuacao)
        
        # Atualizando a tela
        pygame.display.update()
        
        # Controlando o FPS
        clock.tick(FPS)
    
    # Exibindo o fim de jogo
    tela.fill(PRETO)
    fonte_fim = pygame.font.SysFont(None, 48)
    texto_fim = fonte_fim.render(f'Fim de Jogo! Pontuação: {pontuacao}', True, BRANCO)
    tela.blit(texto_fim, (LARGURA_TELA // 4, ALTURA_TELA // 2))
    pygame.display.update()
    
    pygame.time.wait(2000)
    pygame.quit()

# Iniciando o jogo
if __name__ == "__main__":
    main()
