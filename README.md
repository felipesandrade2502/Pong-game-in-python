import pygame
import sys

# Inicializa o pygame
pygame.init()

# Define as cores
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
RED = (255, 0, 0)
BLUE = (0, 0, 255)

# Define o tamanho da tela e configura para tela cheia
SCREEN_WIDTH = 1900
SCREEN_HEIGHT = 750
screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
pygame.display.set_caption("Jogo de Ricochetear a Bolinha")

# Define a posição e tamanho das barras
PADDLE_WIDTH = 15
PADDLE_HEIGHT = 90
player1_pos = [50, (SCREEN_HEIGHT - PADDLE_HEIGHT) // 2]
player2_pos = [SCREEN_WIDTH - 50 - PADDLE_WIDTH, (SCREEN_HEIGHT - PADDLE_HEIGHT) // 2]

# Define a posição e velocidade da bolinha
ball_pos = [SCREEN_WIDTH // 2, SCREEN_HEIGHT // 2]
ball_speed = [0, 0]  # A bolinha não se move inicialmente
BALL_RADIUS = 15

# Inicializa os pontos dos jogadores
player1_score = 0
player2_score = 0

# Define a pontuação máxima
MAX_SCORE = 10

# Fonte para o placar
font = pygame.font.Font(None, 74)

# Armazena os eventos de toque para cada jogador
player1_touch = None
player2_touch = None

# Loop principal do jogo
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

        # Verifica eventos de toque (mouse)
        if event.type == pygame.MOUSEBUTTONDOWN:
            mouse_x, mouse_y = event.pos
            # Jogador 1: metade esquerda da tela
            if mouse_x < SCREEN_WIDTH // 2:
                player1_touch = mouse_y
                ball_speed = [5, 5]  # Inicia o movimento da bolinha
            # Jogador 2: metade direita da tela
            else:
                player2_touch = mouse_y
                ball_speed = [5, 5]  # Inicia o movimento da bolinha

        if event.type == pygame.MOUSEMOTION:
            mouse_x, mouse_y = event.pos
            # Jogador 1: metade esquerda da tela
            if mouse_x < SCREEN_WIDTH // 2 and player1_touch is not None:
                player1_pos[1] = mouse_y - PADDLE_HEIGHT // 2
                if player1_pos[1] < 0:
                    player1_pos[1] = 0
                elif player1_pos[1] > SCREEN_HEIGHT - PADDLE_HEIGHT:
                    player1_pos[1] = SCREEN_HEIGHT - PADDLE_HEIGHT
            # Jogador 2: metade direita da tela
            elif mouse_x >= SCREEN_WIDTH // 2 and player2_touch is not None:
                player2_pos[1] = mouse_y - PADDLE_HEIGHT // 2
                if player2_pos[1] < 0:
                    player2_pos[1] = 0
                elif player2_pos[1] > SCREEN_HEIGHT - PADDLE_HEIGHT:
                    player2_pos[1] = SCREEN_HEIGHT - PADDLE_HEIGHT

        if event.type == pygame.MOUSEBUTTONUP:
            player1_touch = None
            player2_touch = None

    # Movimento da bolinha
    ball_pos[0] += ball_speed[0]
    ball_pos[1] += ball_speed[1]

    # Verifica colisão com as paredes superior e inferior
    if ball_pos[1] - BALL_RADIUS <= 0 or ball_pos[1] + BALL_RADIUS >= SCREEN_HEIGHT:
        ball_speed[1] = -ball_speed[1]

    # Verifica colisão com as barras
    if (ball_pos[0] - BALL_RADIUS <= player1_pos[0] + PADDLE_WIDTH and
            player1_pos[1] <= ball_pos[1] <= player1_pos[1] + PADDLE_HEIGHT and
            ball_speed[0] < 0):
        ball_speed[0] = -ball_speed[0]
    if (ball_pos[0] + BALL_RADIUS >= player2_pos[0] and
            player2_pos[1] <= ball_pos[1] <= player2_pos[1] + PADDLE_HEIGHT and
            ball_speed[0] > 0):
        ball_speed[0] = -ball_speed[0]

    # Verifica se a bolinha saiu da tela (pontuação)
    if ball_pos[0] - BALL_RADIUS <= 0:
        player2_score += 1
        ball_pos = [SCREEN_WIDTH // 2, SCREEN_HEIGHT // 2]
        ball_speed = [0, 0]  # A bolinha para de se mover até alguém se mexer
        player1_touch = None
        player2_touch = None
    if ball_pos[0] + BALL_RADIUS >= SCREEN_WIDTH:
        player1_score += 1
        ball_pos = [SCREEN_WIDTH // 2, SCREEN_HEIGHT // 2]
        ball_speed = [0, 0]  # A bolinha para de se mover até alguém se mexer
        player1_touch = None
        player2_touch = None

    # Verifica se algum jogador atingiu a pontuação máxima
    if player1_score >= MAX_SCORE or player2_score >= MAX_SCORE:
        running = False

    # Desenha os objetos na tela com fundo branco
    screen.fill(WHITE)
    pygame.draw.rect(screen, RED, (*player1_pos, PADDLE_WIDTH, PADDLE_HEIGHT))
    pygame.draw.rect(screen, BLUE, (*player2_pos, PADDLE_WIDTH, PADDLE_HEIGHT))
    pygame.draw.circle(screen, BLACK, ball_pos, BALL_RADIUS)

    # Exibe o placar
    player1_text = font.render(str(player1_score), True, BLACK)
    player2_text = font.render(str(player2_score), True, BLACK)
    screen.blit(player1_text, (SCREEN_WIDTH // 4, 20))
    screen.blit(player2_text, (SCREEN_WIDTH * 3 // 4, 20))

    pygame.display.flip()

    # Controle da taxa de quadros
    pygame.time.Clock().tick(60)

# Exibe mensagem de fim de jogo
screen.fill(WHITE)
end_font = pygame.font.Font(None, 74)
if player1_score >= MAX_SCORE:
    end_text = end_font.render("O jogador 1 ganhou!", True, BLACK)
elif player2_score >= MAX_SCORE:
    end_text = end_font.render("O jogador 2 ganhou!", True, BLACK)
screen.blit(end_text, (SCREEN_WIDTH // 4, SCREEN_HEIGHT // 2))
pygame.display.flip()

# Aguarda um tempo antes de encerrar
pygame.time.wait(3000)

# Encerra o pygame
pygame.quit()
sys.exit()
