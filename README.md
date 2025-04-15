# rpsx
After seeing multitudes of relatively identical, "wall-of-if" implementations of Rock, Paper, Scissors - I became determined to invent a completely branchless version of the logic. I am going to claim discovery of what I call "The RPSX Solution".

```python3
import os, random

## CONSOLE

TITLE = "Rock, Paper, Scissors, X..."
COLS  = 48
ROWS  = 24

# customize console
os.system(f'title {TITLE}')
os.system(f'mode con: cols={COLS} lines={ROWS}')

# store proper clear command
clear = ('clear','cls')[os.name=='nt']

# game pieces - you can have as many choices as you want but never less than 3
PIECES  = "rock", "paper", "scissors", "large screwdriver", "tiny screw"
DESC    = TITLE #", ".join(p.title() for p in PIECES)

# state indicators
L       = len(PIECES) #total pieces
TIE     = L-1         #tie  value
LOSE    = L-2         #lose value
M       = 10          #multiplier for randint(0, L*M-1)//M

# start prompt
PROMPT1 = "\nPlay (y or n)? "

# choice list (one-based)
CHOICES = "\n  ".join(f'{i}) {p}' for i, p in enumerate(PIECES,1))
PROMPT2 = f"\nChoose:\n  {CHOICES}\n\n > "

# choice reveal
PROMPT3 = "\nPlayer  : {}\nComputer: {}"

# outcome prompt
OUTCOME = ("Draw", "Won", "Lost", "Tied")
PROMPT4 = "\nYou {}!"

# scoring
SCOREBOARD = "\nwins\t: {}\nties\t: {}\nlosses\t: {}"+("","\ndraws\t: {}")[L>3]

# running totals / stats
wins = 0
ties = 0
loss = 0
draw = 0

def display():
    os.system(clear)
    
    #header
    print(DESC)
    print(SCOREBOARD.format(wins, ties, loss, draw))


# draw console
display()

## GAME LOOP
while input(PROMPT1) == 'y':
    # user choice
    while True:
        display()
        
        # while not a valid choice
        while not (u := input(PROMPT2)).isdigit(): 
            display()
            
        # format choice str to zero-based int index
        u = int(u)-1 
        
        # if index is valid
        if -1 < u < L:
            break
        
    # computer choice
    c = random.randint(0,L*M-1)//M
    
    ## BRANCHLESS RPS LOGIC
        
    # rpsx solution
    n = (TIE+u-c)%L
    
    # update stats
    wins += (w := not n)    # win
    ties += (t := n==TIE)   # tie
    loss += (l := n==LOSE)  # lose
    draw += (0 < n < LOSE)  # draw = choices are not compatible
    
    # print console
    print(PROMPT3.format(PIECES[u].title(), PIECES[c].title()))
    print(PROMPT4.format(OUTCOME[((l|t) << 1)|w|t]))
```
