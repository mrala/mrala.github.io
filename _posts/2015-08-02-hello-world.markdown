# Header1

Test post

```python
#!/usr/bin/env python

"""
Monte Carlo Tic-Tac-Toe Player
"""

import random
import poc_ttt_gui
import poc_ttt_provided as provided

# Constants for Monte Carlo simulator
#  do not change their names.
NTRIALS = 10        # Number of trials to run
SCORE_CURRENT = 1.0 # Score for squares played by the current player
SCORE_OTHER = 1.0   # Score for squares played by the other player
    

def mc_trial(board, player):
    """
    plays out a random game
    """
    squares = board.get_empty_squares()
    while not board.check_win():
        move = squares[random.randrange(len(squares))]
        board.move(move[0], move[1], player)
        squares.remove(move)
        player = provided.switch_player(player)
    

    
def mc_update_scores(scores, board, player):
    """
    function to score completed game
    """
    board_coordinates = [(row, col) for row in xrange(board.get_dim()) for col in xrange(board.get_dim())]

    winner = board.check_win()
    if winner is not provided.DRAW:
        if winner is player:
            for pos in board_coordinates:
                if board.square(pos[0], pos[1]) is player:
                    scores[pos[0]][pos[1]] += SCORE_CURRENT
                elif board.square(pos[0], pos[1]) is provided.switch_player(player):
                    scores[pos[0]][pos[1]] -= SCORE_OTHER
        else:
            for pos in board_coordinates:
                if board.square(pos[0], pos[1]) is player:
                    scores[pos[0]][pos[1]] -= SCORE_CURRENT
                elif board.square(pos[0], pos[1]) is provided.switch_player(player):
                    scores[pos[0]][pos[1]] += SCORE_OTHER
    
def get_best_move(board, scores):
    """
    returns the best move
    """
    current_best = ((), 0)
    for pos in board.get_empty_squares():
        if current_best[1] is 0:
            current_best = (pos, scores[pos[0]][pos[1]])
        elif scores[pos[0]][pos[1]] > current_best[1]:
            current_best = (pos, scores[pos[0]][pos[1]])
    
    return current_best[0]
def mc_move(board, player, trials):
    """
    runs Monte Carlo simulation to list scores
    for each available space based on number of trials
    """
    scores = [[0 for dummy_col in xrange(board.get_dim())] for dummy_row in xrange(board.get_dim())]
    for dummy_trial in xrange(trials):
        cloned_board = board.clone()
        mc_trial(cloned_board, player)
        mc_update_scores(scores, cloned_board, player)
        
    return get_best_move(board, scores)
     
poc_ttt_gui.run_gui(3, provided.PLAYERX, mc_move, NTRIALS, False)
```