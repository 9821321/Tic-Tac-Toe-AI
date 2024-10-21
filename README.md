# Tic-Tac-Toe-AI
!pip install pygame
import pygame
import math
# Initialize Pygame
pygame.init()
# Colors
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
RED = (255, 0, 0)
BLUE = (0, 0, 255)
# Screen dimensions
WIDTH, HEIGHT = 300, 300
LINE_WIDTH = 5
SQUARE_SIZE = WIDTH // 3
# Set up the display
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption('Tic-Tac-Toe')
# Fonts
font = pygame.font.Font(None, 100)
# Initialize the board
board = [[' ' for _ in range(3)] for _ in range(3)]
# Draw the game board
def draw_board():
    screen.fill(WHITE)
    
    # Draw vertical lines
    pygame.draw.line(screen, BLACK, (SQUARE_SIZE, 0), (SQUARE_SIZE, HEIGHT), LINE_WIDTH)
    pygame.draw.line(screen, BLACK, (SQUARE_SIZE * 2, 0), (SQUARE_SIZE * 2, HEIGHT), LINE_WIDTH)
    
    # Draw horizontal lines
    pygame.draw.line(screen, BLACK, (0, SQUARE_SIZE), (WIDTH, SQUARE_SIZE), LINE_WIDTH)
    pygame.draw.line(screen, BLACK, (0, SQUARE_SIZE * 2), (WIDTH, SQUARE_SIZE * 2), LINE_WIDTH)
    # Check if there is a winner
def check_winner(board, player):
    for row in range(3):
        if board[row][0] == board[row][1] == board[row][2] == player:
            return True
    for col in range(3):
        if board[0][col] == board[1][col] == board[2][col] == player:
            return True
    if board[0][0] == board[1][1] == board[2][2] == player:
        return True
    if board[0][2] == board[1][1] == board[2][0] == player:
        return True
    return False
    # Check if the board is full
def is_board_full(board):
    for row in board:
        for cell in row:
            if cell == ' ':
                return False
    return True
    # Minimax algorithm for the AI
def minimax(board, depth, is_maximizing):
    if check_winner(board, 'O'):
        return 1
    if check_winner(board, 'X'):
        return -1
    if is_board_full(board):
        return 0
    
    if is_maximizing:
        best_score = -math.inf
        for i in range(3):
            for j in range(3):
                if board[i][j] == ' ':
                    board[i][j] = 'O'
                    score = minimax(board, depth + 1, False)
                    board[i][j] = ' '
                    best_score = max(score, best_score)
        return best_score
    else:
        best_score = math.inf
        for i in range(3):
            for j in range(3):
                if board[i][j] == ' ':
                    board[i][j] = 'X'
                    score = minimax(board, depth + 1, True)
                    board[i][j] = ' '
                    best_score = min(score, best_score)
        return best_score
# AI move
def ai_move(board):
    best_score = -math.inf
    move = None
    for i in range(3):
        for j in range(3):
            if board[i][j] == ' ':
                board[i][j] = 'O'
                score = minimax(board, 0, False)
                board[i][j] = ' '
                if score > best_score:
                    best_score = score
                    move = (i, j)
    board[move[0]][move[1]] = 'O'
    # Draw X and O on the board
def draw_symbols():
    for row in range(3):
        for col in range(3):
            if board[row][col] == 'X':
                text = font.render('X', True, RED)
                screen.blit(text, (col * SQUARE_SIZE + 30, row * SQUARE_SIZE + 15))
            elif board[row][col] == 'O':
                text = font.render('O', True, BLUE)
                screen.blit(text, (col * SQUARE_SIZE + 30, row * SQUARE_SIZE + 15))
                # Get row and column from mouse position
def get_mouse_position(pos):
    x, y = pos
    row = y // SQUARE_SIZE
    col = x // SQUARE_SIZE
    return row, col
    # Game loop
def main():
    running = True
    player_turn = True
    
    while running:
        draw_board()
        draw_symbols()
        pygame.display.update()
        
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                running = False
            
            if event.type == pygame.MOUSEBUTTONDOWN and player_turn:
                mouse_pos = pygame.mouse.get_pos()
                row, col = get_mouse_position(mouse_pos)
                
                if board[row][col] == ' ':
                    board[row][col] = 'X'
                    player_turn = False
                    
                    if check_winner(board, 'X'):
                        print("Player Wins!")
                        running = False
                    elif is_board_full(board):
                        print("It's a draw!")
                        running = False
                    
                    # AI makes a move after player
                    if not player_turn and not check_winner(board, 'X') and not is_board_full(board):
                        ai_move(board)
                        player_turn = True
                        
                        if check_winner(board, 'O'):
                            print("AI Wins!")
                            running = False
                        elif is_board_full(board):
                            print("It's a draw!")
                            running = False
    
    pygame.quit()
if __name__ == "__main__":
    main()
