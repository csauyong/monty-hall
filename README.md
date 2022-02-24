
# Monty Hall Problem

## Scenario

Suppose you're on a game show, and you're given the choice of three doors: Behind one door is a car; behind the others, goats. You pick a door, say No. 1, and the host, who knows what's behind the doors, opens another door, say No. 3, which has a goat. He then says to you, "Do you want to pick door No. 2?" Is it to your advantage to switch your choice?

## Simulation

We first simulate the scenario using Python to approximate the winning probability in either case.


```python
import numpy as np

stay_win = 0
switch_win = 0
num_trial = 10000

stay_results = []
switch_results = []
for i in range(num_trial):
  doors = ['car', 'goat', 'goat']
  np.random.shuffle(doors)
  my_door_index = np.random.randint(0, 3)
  stay_result = doors.pop(my_door_index)  # remains with the choice
  doors.remove('goat')
  switch_result = doors[0]  # switch to the unrevealed door
  # Put results into result lists
  if stay_result == 'car':
    stay_win += 1
  if switch_result == 'car':
    switch_win += 1

print(f'Winning probability for staying with initial choice = {stay_win/num_trial}.')
print(f'Winning probability for switching to new choice = {switch_win/num_trial}.')
```

    Winning probability for staying with initial choice = 0.3307.
    Winning probability for switching to new choice = 0.6693.


From simulation,

* Winning probability for staying with initial choice $\approx \frac{1}{3}$.
* Winning probability for switching to new choice $\approx \frac{2}{3}$.

We can test this hypothesis by calcuting the analytical solution.

## Analytical solution

We approach the problem with Bayes Theorem. Assume we choose door 1 initially. 

If we do not switch, the problem simply reduces to randomly choosing one correct door out of 3 doors, so the probability = $\frac{1}{2}$.

If we switch, then consider the probability $P(A|B)$ where

*   A = door 1 has car behind
*   B = host opens up door 2 (or 3 WLOG)

P(B) = \frac{1}{2}

$$P(A \cap B) = \frac{1}{3} \times \frac{1}{2} = \frac{1}{6}$$

$P(A|B) = \frac{1}{6} \div \frac{1}{2} = \frac{1}{3}$

Consider event C = door 3 has car behind. Given B, $C = \bar A$. Therefore, 

$P(C|B) = 1 - P(A|B) = \frac{2}{3} > P(A|B)$

Therefore, we should always switch.


## Game Theory

We can transform the Monty Hall problem into an extensive card game. The events that happen in sequence are:

1.   The player select 1 of J, Q, K by chance.
2.   The host discard J or Q.
3.   If the player chooses K in the end, he wins and receive payoff $1.

Without loss of generality, the player now has 2 information sets, namely J and Q, for each of which he has to formulate a strategy. The total available strategies for him are: 

- SS (always switch)
- SK (switch when seeing J, keep when seeing Q)
- KS (switch when seeing Q, keep when seeing J)
- KK (always keep)

Calculating the expected payoff, we have the following zero-sum game matrix, where the player is the row player.

|        | Seeing J      | Seeing Q      |
| ----   | -----------   | ----------    |
| SS     | $\frac{2}{3}$ | $\frac{2}{3}$ |
| SK     | $\frac{1}{3}$ | $\frac{2}{3}$ |
| KS     | $\frac{2}{3}$ | $\frac{1}{3}$ |
| KK     | $\frac{1}{3}$ | $\frac{1}{3}$ |

The maximin strategy for player (row) is SS, whereas any strategy is a minimax strategy of host (column). Therefore, the player should always switch.
