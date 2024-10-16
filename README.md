# Tic-Tac-Toe
import numpy as np

class TicTacToe:
    def __init__(self):
        self.board = np.zeros((3, 3), dtype=int)  # 0 = empty, 1 = player, -1 = AI
        self.current_player = 1  # Player starts

    def print_board(self):
        print("\n".join([" ".join(['O' if cell == 1 else 'X' if cell == -1 else '.' for cell in row]) for row in self.board]))
        print()

    def is_winner(self, player):
        # Check rows, columns, and diagonals
        return any(np.all(self.board[i, :] == player for i in range(3)) or
                   np.all(self.board[:, i] == player for i in range(3)) for player in (1, -1)) or \
               (np.all(np.diag(self.board) == player) or np.all(np.diag(np.fliplr(self.board)) == player))

    def is_draw(self):
        return np.all(self.board != 0)

    def get_empty_positions(self):
        return [(r, c) for r in range(3) for c in range(3) if self.board[r, c] == 0]

    def minimax(self, depth, is_maximizing):
        if self.is_winner(-1):
            return 10 - depth
        if self.is_winner(1):
            return depth - 10
        if self.is_draw():
            return 0

        if is_maximizing:
            best_score = -np.inf
            for (r, c) in self.get_empty_positions():
                self.board[r, c] = -1
                score = self.minimax(depth + 1, False)
                self.board[r, c] = 0
                best_score = max(score, best_score)
            return best_score
        else:
            best_score = np.inf
            for (r, c) in self.get_empty_positions():
                self.board[r, c] = 1
                score = self.minimax(depth + 1, True)
                self.board[r, c] = 0
                best_score = min(score, best_score)
            return best_score

    def best_move(self):
        best_score = -np.inf
        move = None
        for (r, c) in self.get_empty_positions():
            self.board[r, c] = -1
            score = self.minimax(0, False)
            self.board[r, c] = 0
            if score > best_score:
                best_score = score
                move = (r, c)
        return move

    def play(self):
        while True:
            self.print_board()
            if self.current_player == 1:
                row, col = map(int, input("Enter your move (row and column): ").split())
                if self.board[row, col] != 0:
                    print("Invalid move! Try again.")
                    continue
            else:
                row, col = self.best_move()

            self.board[row, col] = self.current_player
            if self.is_winner(self.current_player):
                self.print_board()
                if self.current_player == 1:
                    print("You win!")
                else:
                    print("AI wins!")
                break
            if self.is_draw():
                self.print_board()
                print("It's a draw!")
                break

            self.current_player *= -1  # Switch players

if __name__ == "__main__":
    game = TicTacToe()
    game.play()
