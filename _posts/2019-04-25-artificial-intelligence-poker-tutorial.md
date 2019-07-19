## Intro
This tutorial is made with two target audiences in mind: (1) Those with an interest in poker who want to understand how AI poker agents are made and (2) Those with an interest in coding who want to better understand poker and code poker agents from scratch. 

## Table of Contents
[1. Poker Background](#1-poker-background)<br>
[2. History of Solving Poker](#2-history-of-solving-poker)<br>
[3. Game Theory -- Equilibrium and Regret](#3-game-theory----equilibrium-and-regret)<br>
[4. Solving Toy Poker Games from Normal Form to Extensive Form](#4-solving-toy-poker-games-from-normal-form-to-extensive-form)<br>
[5. Trees in Games](#5-trees-in-games)<br>
[6. Toy Games and Python Implementation](#6-toy-games-and-python-implementation)<br>
[7. Counterfactual Regret Minimization (CFR)](#7-counterfactual-regret-minimization-cfr)<br>
[8. Game Abstraction](#8-game-abstraction)<br>
[9. Agent Evaluation](#9-agent-evaluation)<br>
[10. CFR Advances](#10-cfr-advances)<br>
[11. Deep Learning and Poker](#11-deep-learning-and-poker)<br>
[12. AI vs. Humans -- What Can Humans Learn?](#12-ai-vs-humans----what-can-humans-learn)<br>
[13. Multiplayer Games](#13-multiplayer-games)<br>
[14. Opponent Exploitation](#14-opponent-exploitation)<br>
[15. Other Games (Chess, Atari, Go, Starcraft, Dota, Hanabi)](#15-other-games-chess-atari-go-starcraft-hanabi)<br>

## 1. Poker Background

### Brief History
Poker began at some point in the early 20th century and grew extensively in the early 2000s thanks to the beginnings of online poker (the first hand ever was dealt on January 1, 1998), which lead to an accountant and amateur poker player named Chris Moneymaker investing $39 in an online satellite tournament that won him a $10,000 seat at the World Series of Poker Main Event in 2003, alongside 838 other entrants. Moneymaker went on to defeat professional poker player Sam Farha at the final table of the tournament and won $2.5 million. A poker boom was sparked and led to massive player pools on the Internet and in subsequent World Series’ of Poker.

![Chris Moneymaker](https://pnimg.net/w/articles/4/5a5/a09111f94a.jpg)
https://www.pokernews.com/strategy/teaching-poker-to-beginners-with-chris-moneymaker-29704.htm

After the human poker boom, computers also started getting in on the poker action. Researchers began to study solving Texas Hold’em games in 2003, and since 2006, there has been an Annual Computer Poker Competition (ACPC) at the AAAI Conference on Artificial Intelligence in which poker agents compete against each other in a variety of poker formats. In early 2017, for the first time, a NLHE poker agent defeated and is considered superior to top poker players in the world in 1v1 games.

### Basic Rules
Poker is a card game that, in its standard forms, uses a deck of 52 cards composed of four suits (Clubs :clubs:, Diamonds :diamonds:, Hearts :heart:, and Spades ::spades::) and 13 ranks (Two through Ten, Jack, Queen, King, and Ace). A dealer button rotates around the table indicating who is the “dealer”. This is decided at random for the first hand and rotates clockwise after that. All actions begin to the left of the hand’s current dealer player.

We will focus on two player games and ignore any fees (also known as rake) such that the games will be zero-sum (all money won by one player is lost by the other). The two players play a match of independent games, also called hands, while alternating who is the dealer.

Each game goes through a series of betting rounds that result in either one player folding and the other winning the pot by default or both players going to “showdown”, in which the best hand wins the pot. The pot accumulates all bets throughout the hand. The goal is to win as many chips from the other player as possible.

Betting options available throughout each round are: fold, check, call, bet, and raise. Bets and raises generally represent strong hands. A bluff is a bet or raise with a weak hand and a semi-bluff is a bet or raise made with a currently non-strong hand that has good potential to improve. 

**Fold:** Not putting in any chips and "quitting" the hand by throwing the cards away and declining to match the opponent's bet or raise. Done only after an opponent bet or raise. <br>
**Check:** A pass, only possible if acting first or if all previous players have also checked<br>
**Call:** Matching the exact amount of a previous bet or raise<br>
**Bet:** Wagering money (putting chips into the pot) when either first to act or the opponent has checked, which the opponent then has to call or raise to stay in the pot<br>
**Raise:** Only possible after a bet, adding more money to the pot, which must be at least the amount of the previous bet or raise (effectively calling and betting together)

### Kuhn (1-card) Poker
Kuhn Poker is the most basic useful poker game that is used in computer poker research. It was solved analytically by hand by Kuhn in 1950 [6]. Each player is dealt one card privately and begins with two chips. In the standard form, the deck consists of only three cards – an Ace, a King, and a Queen, but can be modified to contain any number such that the cards are simply labeled 1 through n, with a deck of size n. Our first experiment will use a deck size of 100 to compare different CFR algorithm implementations.

Players each ante 1 chip (although most standard poker games use blinds, this basic game does not) and rotate acting first, and the highest card is the best hand. With only 1 chip remaining for each player, the betting is quite simple. The first to act has the option to bet or check. If he bets, the opponent can either call or fold. If the opponent folds, the bettor wins one chip. If the opponent calls, the player with the higher card (best hand) wins two chips.

If the first to act player checks, then the second player can either check or bet. If he checks, the player with the best hand wins one chip. If he bets, then the first player can either fold and player two will win one chip, or he can call, and the player with the best hand will win two chips.

#### All Kuhn Poker Sequences


### Leduc Poker
Leduc Poker is a simple toy game that has more in common strategically with regular Texas Hold'em. 

**Deck size:** 6 cards -- 2 Jacks, 2 Queens, 2 Kings<br>
**Rounds:** 2 rounds -- 1 private card preflop, 1 community flop card<br>
**Blinds/Antes:** $1 ante<br>
**Betting structure:** All bets $2 in the first round and $4 in the second round with a limit of one bet and one raise per round<br>
**Starting 

Each betting round follows the same format. The first player to act has the option to check or bet. When betting the player adds chips into the pot and action moves to the other player. When a player faces a bet, they have the option to fold, call or raise. When folding, a player forfeits the hand and all the money in the pot is awarded to the opposing player. When calling, a player places enough chips into the pot to match the bet faced and the betting round is concluded. When raising, the player must put more chips into the pot than the current bet faced and action moves to the opposing player. If the first player checks initially, the second player may check to conclude the betting round or bet. In Leduc Hold’em there is a limit of one bet and one raise per round. The bets and raises are of a fixed size. This size is two chips in the first betting round and four chips in the second.

#### Example Leduc Poker Hand

### Texas Hold'em Poker
Each hand starts with the dealer player posting the small blind and the non-dealer player posting the big blind. The blinds define the stakes of the game (for example, a $1-$2 stakes game has blinds of $1 and $2) and the big blind is generally double the small blind. They are called blinds because they are forced bets that must be posted “blindly”. The player to the left of the big blind, in this case the dealer player, begins the first betting round by folding, calling, or raising. (In some games antes are used instead of or in addition to blinds, which involves each player posting the same ante amount in the pot before the hand.)

Each hand in Texas Hold’em consists of four betting rounds. Betting rounds start with each player receiving two private cards, called the “preflop” betting round, then can continue with the “flop” of three community cards followed by a betting round, the “turn” of one community card followed by a betting round, and a final betting round after the fifth and final community card, called the “river”. Community cards are shared and are dealt face up.

In no limit betting, the minimum bet size is the smaller of the big blind or a bet faced by the player and the maximum bet size is the amount of chips in front of the player. In the case of a two-player game, the dealer button pays the small blind and acts first preflop and then last postflop.
In limit betting, bets are fixed in advance based on the stakes of the game and the blinds. For example, with 2-4 blinds, the bets preflop and on the flop are 4 and on the turn and river, they are doubled to 8. In limit betting, there is a maximum of four bets and raises per betting round per player, which, in addition to the limited available set of actions, makes limit-based games significantly smaller than their no-limit counterparts.
On each round, players combine their private cards with the community cards to form the best possible 5-card poker hand, which could include 0, 1, or 2 private cards.

#### Example Texas Hold'em Poker Hand


### Summary of Games


## 2. History of Solving Poker
In 1951, John Nash wrote: "The study of n-person games for which the accepted ethics of fair play imply non-cooperative playing is, of course, an obvious direction in which to apply this theory. And poker is the most obvious target. The analysis of a more realistic poker game than our very simple model should be an interesting affair."

A main interest in games like poker comes from the fact that it is a game of imperfect information. Unlike games of perfect information (e.g. chess) where all information is visible to all players, in poker there is hidden information (opponent player private cards). 

The history of poker solving techniques have gone from rule/formula based to simulation based to, most recently, game theoretical. Game theoretical techniques have evolved by (a) using Monte Carlo techniques so that we don't need to traverse the entire game tree to update the game information and strategies and (b) abstraction


A short timeline of the major milestones in computer poker research is given here:

1998: Opponent Modelling in Poker, Billings et al. (Alberta)
A basic rule-based system was developed in 1999 in the University of Alberta Computer Poker Research Group (CPRG), which took an effective hand-strength as input and outputted a (fold, call, raise) probability triple, using a form of basic opponent modelling. 

2000: Abstraction Methods for Game-Theoretic Poker, Shi and Littman

2003: Approximating Game-Theoretic Optimal Strategies for Full-scale Poker, Billings et al. (Alberta)

2005: Optimal Rhode Island Poker, Gilpin and Sandholm (CMU)

2007: Regret Minimization in Games with Incomplete Information, Zinkevich et al (Alberta)
This hugely important paper introduced the Counterfactual Regret Minimization algorithm, which is the main algorithm used today for finding game-theoretic optimal strategies in poker games. 

MCCFR

2008-9: Man vs. Machine Limit Texas Hold'em Competitions

2015: Heads-up Limit Hold'em Poker is Solved, Bowling et al (Alberta)

2015: Brains vs. AI No-Limit Texas Hold'em Competition

2017: DeepStack: Expert-Level Artificial Intelligence in No-Limit Poker (DeepMind)

2017: Libratus and Man vs. Machine Competition

2019: Superhuman AI for Multiplayer Poker, Brown and Sandholm (CMU)


## 3. Game Theory -- Equilibrium and Regret
Let's look at some important game theory concepts before we get into actually solving for poker strategies. 

What does it mean to "solve" a poker game? In the 2-player setting, this means to find a Nash Equilibrium strategy for the game. If both players are playing this strategy, neither would want to change to a different strategy, since neither could do better with any other strategy (assuming that the opponent's strategy stays fixed). 

Intuition for this in poker can be explained using a simple all-in game where one player must either fold or bet all his chips and the second player must either call or fold if the first player bets all the chips. In this scenario, the second player may begin the game with a strategy of calling a low percentage of hands. After seeing the first player go all-in very frequently, he may increase that percentage. This could lead the first player to reduce his all-in percentage. Once the all-in percentage and the call percentage stabilize such that neither player can unilaterally change his strategy to increase his profit, then the equilibrium strategies have been reached.

But what if the opponent, for example, keeps calling this low percentage of hands and seems to be easy to exploit? The game theoretic solution would not fully take advantage of this opportunity. The **best response strategy** is the one that maximally exploits the opponent by always performing the highest expected value play against their fixed strategy (and an exploitative strategy is one that exploits an opponent's non-equilibrium play). In the above example, this could mean raising all hands after seeing the opponent calling with a low percentage of hands. However, this strategy can itself be exploited. 

### Normal Form Games
Normal Form is writing the strategies and game payouts in matrix form. The Player 1 strategies are in the rows and Player 2 strategies are in the columns. The payouts are written in terms of P1, P2. When the sum of the payouts is 0, this is called a zero-sum game. Poker is an example of a zero-sum game since whatever one player wins the other player loses (assuming no rake, i.e, house commission). 

Here is a game example:

| P1/2  | Action 1  | Action 2  | Action 3  |
|---|---|---|---|
| Action 1  | 10, 2  | 8, 1  | 3, -1  |
| Action 2  | 5, 8  | 4, 0  | -1, 1  |
| Action 3  | 7, 3  | 5, -1  | 0, 3  |

A dominated strategy is one that is strictly worse than an alternative strategy. We can see that Player 1's strategy of Action 1 dominates Actions 2 and 3 because all of the values are strictly higher. 

When P2 chooses Action 1, P1 earns 10 with Action 1, 5 with Action 2, and 7 with Action 3
When P2 chooses Action 2, P1 earns 8 with Action 1, 4 with Action 2, and 5 with Action 3
When P2 chooses Action 3, P1 earns 7 with Action 1, 5 with Action 2, and 0 with Action 3

We also see that Action 1 dominates Action 2 for Player 2 and that Action 1 weakly dominates Action 3 for Player 2, since if Player 1 chooses Action 3, Player 2's Action 1 and Action 3 would be equal, rather than Action 1 being strictly greater. 

We can eliminate strictly dominated strategies and then arrive at the reduced Normal Form game: 

| P1/2  | Action 1  | Action 3  |
|---|---|---|
| Action 1  | 10, 2  | 3, -1  |

In this case, Player 2 prefers to play Action 1 since 2 > -1, so we have a Nash Equilibrium with both players playing Action 1 pure strategy and the payouts will be 10 to Player 1 and 2 to Player 2. 

| P1/2  | Action 1  |
|---|---|
| Action 1  | 10, 2  |

Here's one more example of a game. This time with two people who are going to watch something together. P1 has a preference to watch tennis and P2 prefers Power Rangers. If they don't agree, then they won't watch anything and will have payouts of 0. If they do agree, then the person who gets to watch their preferred show has a higher reward than the other, but both are positive. 

| P1/2  | Tennis  | Power Rangers   |
|---|---|---|
| Tennis  | 3, 2  | 0, 0  |
| Power Rangers  | 0, 0  | 2, 3  |

In this case, neither player can eliminate a strategy. For Player 1, if Player 2 chooses Tennis then he also prefers Tennis, but if Player 2 chooses Power Rangers, then he prefers Power Rangers as well (both of these are Nash Equilbrium). 

Let's call P(P1 Tennis) = p and P(P1 Power Rangers) = 1 - p. 

If Player 2 chooses Tennis, Player 2 earns $$ p*(2) + (1-p)*(0) = 2p $$

If Player 2 chooses Power Rangers, Player 2 earns $$ p*(0) + (1-p)*(3) = 3 - 3p $$

We can set these equal because a player will only play a mixed strategy when both strategies are equal (otherwise they would play a single best strategy), so $$ 2p = 3 - 3p ==> 5p = 3 ==> p = 3/5$$. Therefore $$1 - p = 2/5$$ and Player 1's strategy is to choose Tennis $$3/5$$ and Power Rangers $$2/5$$. This is a mixed strategy equilibrium because there is a probability distribution over which strategy to play. 

By symmetry, P2's strategy is to choose Tennis 2/5 and Power Rangers 3/5. 

So we have Tennis,Tennis occurring $$3/5 * 2/5 = 6/25$$
Power Rangers, Power Rangers $$2/5 * 3/5 = 6/25$$
Tennis, Power Rangers $$3/5 * 3/5 = 9/25$$
Power Rangers, Tennis $$2/5 * 2/5 = 4/25$$

The final payouts to each player are $$6/25 * (3) + 6/25 * (2) = 30/25 = 1.2$$. This would have been higher if they had avoided the 0,0 payouts!

### Rock Paper Scissors
We can also think about this concept in Rock-Paper-Scissors. We define a win as +1, a tie as 0, and a loss as -1. The game matrix for the game is shown below in Normal Form:

| P1/2  | Rock  | Paper  | Scissors  |
|---|---|---|---|
| Rock  | 0, 0  | -1, 1  | 1, -1  |
| Paper  | 1, -1  | 0, 0  | -1, 1  |
| Scissors  | -1, 1  | 1, -1  | 0, 0  |

Player 1 is the row player and Player 2 is the column player. The payouts are written in terms of P1, P2. So for example P1 Paper and P2 Rock corresponds to a reward of 1 for P1 and -1 for P2 since Paper beats Rock. 

The equilibrium strategy is to play each action with 1/3 probability each. We can see this because if any player played anything other than this distribution, then you could exploit them by always playing the strategy that beats the strategy that they most favor. 

We can also work it out mathematically. Let P1 play Rock r%, Paper p%, and Scissors s%. The utility of P2 playing Rock is $$0*(r) + -1 * (p) + 1 * (s)$$. The utility of P2 playing Paper is $$1 * (r) + 0 * (p) + -1 * (s)$$. The utility of P2 playing Scissors is $$-1 * (r) + 1 * (p) + 0 * (s)$$. 

We can figure out the best strategy with this system of equations (the second because all probabilities must add up to 1):

$$
\begin{cases} -p + s = r - s = -r + p \\ r + p + s = 1  \end{cases}
$$

$$-p + s = r - s ==> 2s = p + r
r - s = - r + p ==> 2r = s + p$$

$$-p + s = -r + p ==> s + r = 2p$$

$$r + s + p = 1
r + s = 1 - p$$

$$1 - p = 2p 
1 = 3p
p = 1/3$$

$$r + s + p = 1
s + p = 1 - r$$

$$1 - r = 2r 
1 = 3r
1/3 = r$$

$$1/3 + 1/3 + s = 1
s = 1/3$$

Zero-sum, dominated strategy, pure vs. mixed strategy

The equilibrium strategy is to play each action with 1/3 probability. If you deviate from this strategy, you can be exploited by your opponent always playing the action that beats your most favored action. For example, if you play Rock 50%, Paper 25%, and Scissors 25%, your opponent can play Paper 100%. He will win half the time, draw half the time, and lose half the time, resulting in an average profit per game of $$1*0.5 + 0*0.25 + (-1)*0.25 = 0.25$$. 

If your opponent plays the equilibrium strategy of Rock 1/3, Paper 1/3, Scissors 1/3, then he will have the following EV. EV = $$1*(1/3) + 0*(1/3) + (-1)*(1/3) = 0 $$. While in RPS, the equilibrium playing player cannot show a profit in expectation, in poker this is not the case.


| P1/P2  | Rock 50%  | Paper 25% | Scissors 25% |
|---|---|---|---|
| Rock 0%  | 0  | 0  | 0  | 
| Paper 100%  | 0.5*1 = 1  | 0.25*0 = 0  | 0.25*(-1) = -0.25  | 
| Scissors 0%  | 0  | 0  | 0  |



| P1/P2  | Rock 50%  | Paper 25%  | Scissors 25%  |
|---|---|---|---|
| Rock 1/3  | 0  |   |   |
| Paper 1/3  |   |   |   |
| Scissors 1/3  |   |   |   |

### Regret
When I think of regret related to poker, the first thing that comes to mind is often "Wow you should've played way more hands in 2010 when poker was so easy". Others may regret big folds or bluffs or calls that didn't work out well. 

Here we will look at the mathematical concept of regret. Regret is a measure of how well you could have done compared to some alternative. Alternatively phrased, what you would have done in some situation instead.  

$$ Regret = u(Alternative Strategy) - u(Current Strategy) $$

We can give another example from Rock Paper Scissors: 

We play rock and opponent plays paper ⇒ u(rock,paper) = -1
Regret(scissors) = u(scissors,paper) - u(rock,paper) = 1-(-1) = 2
Regret(paper) = u(paper,paper) - u(rock,paper) = 0-(-1) = 1
Regret(rock) = u(rock,paper) - u(rock,paper) = -1-(-1) = 0

We play scissors and opponent plays paper ⇒ u(scissors,paper) = 1
Regret(scissors) = u(scissors,paper) - u(scissors,paper) = 1-1 = 0
Regret(paper) = u(paper,paper) - u(scissors,paper) = 0-1 = -1
Regret(rock) = u(rock,paper) - u(scissors,paper) = -1-1 = -2

We play paper and opponent plays paper ⇒ u(paper,paper) = 0
Regret(scissors) = u(scissors,paper) - u(paper,paper) = 1-0 = 1
Regret(paper) = u(paper,paper) - u(paper,paper) = 0-0 = 0
Regret(rock) = u(rock,paper) - u(paper,paper) = -1-0 = -1

So we prefer alternative actions with high regret. 

To generalize, 

### Regret Matching
Regret matching is playing a strategy in proportion to the accumulated regrets. The algorithm works like this:
1. Initialize regret for each action to 0
2. Set the strategy as: 
$$
\text{strategy\_action}_{i} = \begin{cases} \frac{R_{i}^{+}}{\sum_{k=1}^nR_{k}^{+}}, & \mbox{if at least 1 positive regret} \\ \frac{1}{n}, & \mbox{if all regrets negative} \end{cases}
$$

So let's consider Player 1 playing a fixed RPS strategy of Rock 40%, Paper 30%, Scissors 30% and Player 2 playing using regret matching. 

Let's look at a sequence of plays in this scenario.

| P1  | P2  | New Regrets  | New Total Regrets  | Strategy  | P2 Profits
|---|---|---|---|---|---|
| S  | S  | [1,0,-1]   | [1,0,-1]  | [1,0,0]  | 0  | 
| P  | R  | [0,1,2]  | [1,1,1]  | [1/3, 1/3, 1/3]  | 1  |
| S  | P  | [2,0,1]  | [3,1,2]  | [1/2, 1/6, 1/3]  | 0  |
| P  | R  | [0,1,2]  | [3,2,4]  | [3/10, 1/5, 2/5]  | -1  |
| R  | S  | [1,2,0]  | [4,4,4]  | [1/3,1/3,1/3]  | -2  |
| R  | R  | [0,1,-1]  | [4,5,3]  | [1/3,5/12,1/4]  | -2  |
| P  | P  | [-1,0,1]  | [3,5,4]  | [1/4,5/12,1/3]  | -2  |
| S  | P  | [2,0,1]  | [5,5,5]  | [1/3, 1/3, 1/3]  | -3  |
| R  | R  | [0,1,-1]  | [5,6,4]  | [1/3, 2/5, 4/15]  | -3  |
| R  | P  | [-1,0,-2]  | [4,6,2]  | [1/3,1/2,1/6]  | -2  |

Here are 10,000 sample runs of this scenario. We know that the best strategy is to play 100% Paper to exploit the opponent over-playing Rock. Depending on the run and how the regrets accumulate, the regret matching can figure this out immediately or it can take some time. 

<img src="/assets/rps_fast1.png" width="500">

<img src="/assets/rps_slow1.png" width="500">

Add automation/make clearer

### Bandits

Exploitation vs. equilibrium
Bandits

Also regret in poker which is actually advantage

## 4. Solving Toy Poker Games from Normal Form to Extensive Form
**Kuhn Poker** is the most basic poker game with interesting strategic implications. 

The game in its standard form is played with 3 cards in {A, K, Q} and 2 players. Each player starts with $2 and places an ante (i.e., forced bet before the hand) of $1. And therefore has $1 left to bet with. Each player is then dealt 1 card and 1 round of betting ensues. 

- 2 players, each is dealt a card in {A, K, Q}
- Each antes 1 at the start of the hand
- Each has 1 remaining for betting
- There is one betting round
- The highest card is the best (i.e., A $>$ K $>$ Q)

Action starts with P1, who can Bet 1 or Check
- If P1 bets, P2 can either Call or Fold
- If P1 checks, P2 can either Bet or Check
- If P2 bets after P1 checks, P1 can then Call or Fold

- If a player folds to a bet, the other player wins the pot of 2 (profit of 1)
- If both players check, the highest card player wins the pot of 2 (profit of 1)
- If there is a bet and call, the highest card player wins the pot of 4 (profit of 2)

The following sequences are possible:

| P1  | P2  | P1  | Pot size  | Result  | History
|---|---|---|---|---|---|
| Check  | Check  | --   | $2  | High card wins $1  | kk  | 
| Check  | Bet $1  | Call $1  | $4  | High card wins $2  | kbc  |
| Check  | Bet $1  | Fold  | $2  | P2 wins $1 | kbf  |
| Bet $1  | Call $1  | --  | $4  | High card wins $2  | bc  |
| Bet $1  | Fold  | --  | $2  | P1 wins $1  | bf  |

### Analytical Solution 
Let's first look at P1's opening action. P1 should never bet the K card here because if he bets the K, P2 with Q will always fold (since the lowest card can never win) and P2 with A will always call (since the best card will always win). 
By checking the K always, P1 can try to induce a bluff from P2 when P2 has the Q. 

**P1 initial action**
Therefore we assign P1's strategy:
- Bet Q: $x$
- Bet K: $0$
- Bet A: $y$

**P2 after P1 bet**
After P1 bets, P2 should always call with the A and always fold the Q as explained above. 

Therefore we assign P2's strategy after P1 bet:
- Call Q: $0$
- Call K: $a$
- Call A: $1$

**P2 after P1 check**
After P1 checks, P2 should never bet with the K for the same reason as P1 should never initially bet with the K. 
P2 should always bet with the A because it is the best hand and there is no bluff to induce by checking. 

Therefore we assign P2's strategy after P1 check:
- Bet Q: $b$
- Bet K: $0$
- Bet A: $1$

**P1 after P1 check and P2 bet**
This case is similar to P2's actions after P1's bet. P1 can never call here with the worst hand (Q) and must always call with the best hand (A). 

Therefore we assign P1's strategy after P1 check and P2 bet:
- Call Q: $0$
- Call K: $z$
- Call A: $1$

So we now have 5 different variables $$x, y, z, a, b$$ to represent the unknown probabilities. 

**Solving** 

**Solving for $$a$$**
We start by solving for $$a$$, how often P2 should call with a K facing a bet from P1. 
P2 should call $$a$$ to make P1 indifferent to bluffing (i.e., betting or checking) with card Q. 

If P1 checks with card Q, he will always fold afterwards (because it is the worst card and can never win). 

$$ \text{P1 check with Q} = 0 $$     


If P1 bets with card Q, 

$$
\begin{align}
u(\text{P1 bet with Q}) = (\text{P2 has A and always calls/wins}) + \\ (\text{P2 has K and folds}) + (\text{P2 has K and calls/wins}) \\
&= (1/2)(-1) + (1/2)[(a)(-1) + (1-a)(2)] \\
&= -1/2 - 1/2 * a + (1-a) \\
&= 1/2 - 3/2 * a
\end{align}
$$

Setting the probabilities of betting with Q and checking with Q equal, we have:
$$
\begin{align}
   0 &= \frac{1}{2} - \frac{3}{2} * a \\        
   \frac{3}{2} * a  &= \frac{1}{2} \\
    a &= \frac{1}{3}
\end{align}
$$

Kuhn normal form
Kuhn extensive form, linear programming
Resutls with other poker agents playing worse strategies exploitable

## 5. Trees in Games
Minimax tictactoe, why this doesn't work for poker
Perfect info game and subgames
MCTS
Exploitation vs. exploration
RL terminology
In imperfect info we need to know about the overall strategy
Call with enough hands to make opponent worse off in allin hands

## 6. Toy Games and Python Implementation
Kuhn
Leduc
Python implementation of the games
Ideally connected to ACPC server

## 7. Counterfactual Regret Minimization (CFR)
External sampling CFR detailed code and walk through (how values change over many iterations)
How is this related to Q learning?
Use RPS to show why average strategy
Proofs of regret minimization 
Kuhn results with 3 card and 10 card and 100 card

## 8. Game Abstraction
Card abstraction, in code also
Betting abstraction

## 9. Agent Evaluation
Best response, code
vs. human
Agent vs. agent

## 10. CFR Advances
AVIAT variance reduction
Monte Carlo variations
New stuff like Linear CFR
CFR+ (use RPS to show difference)
Endgame solving (RPS example)

## 11. Deep Learning and Poker
Libratus
DeepStack
Deep CFR
Pluribus

## 12. AI vs. Humans -- What Can Humans Learn?
Examples from Brokos book
Paper with Sam
Theme ideas from toy games that can translate to full games

## 13. Multiplayer Games
3 player Kuhn, Leduc
Pluribus stuff

## 14. Opponent Exploitation
Paper with Sam

## 15. Other Games (Chess, Atari, Go, Starcraft, Hanabi)
RL stuff
