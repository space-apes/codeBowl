
# Project 7: Board Game
Project 7 involves the design, implementation, and testing of a networked multiplayer board game following the 3-tier software architecture (client, server, database). 
You may choose to either to make the classic board game 'checkers' or 'CodeBowl'. 

# High Level Non-Functional Requirements
- multi-user networked system
- 3-tier architecture
    - Client: browser, desktop app, mobile app, VR, or ability to use multiple clients
    - Server: Ruby on Rails, Django, Laravel, node.js
    - Database: SQL
- sound OOP design with reasoning for all design choices
- attention to common security concerns
- attention to privacy concerns with varying resource access for different types of users
    - guests
    - registered users   

# High Level Functional Requirements
- home screen 
    - explanation of services offered
    - registered users/guests can view leaderboard with user wins/losses/draws
    - registered users/guests can choose games currently happening and spectate them live 
    - registered users/guests can see which registered users are currently logged in 
- login screen
    - guests may log in as registered users 
- register screen
    - guests may create accounts  
- user control panel screen
    - private chat with any other registered user currently logged in
    - search for opponent to play live game and initiate live game mode with that opponent 
    - (optional) play vs AI
    - see personal win stats
- game results screen at end of each game 
- spectate game mode
- game mode with synchronous game moves and asynchronous chat following 'checkers' or 'codebowl' 

# Checkers Game Description
Checkers is a 1v1, competitive, turn-based, tile-based game where you move your pieces with the goal of eliminating all opponent pieces or force the opponent into a position where they can not make a valid move. 

1. The board is set up with 8x8 alternating black and white square tiles. 12 pieces from each team are placed on opposing sides of the board like so: 
![initial board](images/checkersInitialState.png)
2. At the beginning one of the two players is randomly selected to control the black pieces. The other player plays the red pieces.
3. The player controlling black pieces makes a valid move with one of their pieces
4. The player controlling the red pieces makes a valid move with one of their pieces
5. Players alternate making valid moves with 1 of their pieces untilone of the following occur: 
    - one team captures all of their opponent's pieces. the remaining team is the winner
    - one team forces another team into a position where the other team can not make valid moves. The team that can still make valid moves is the winner
    - both teams are unable to make any valid moves. the team with the most 'king' pieces is the winner. 

# valid moves 
- regular pieces move diagonally along black squares towards opponent side
<div>
    <img src="images/checkersValidMoveBasicSetup.png">
    ![regular piece valid move setup](images/checkersValidMoveBasicSetup.png) 
    ![regular piece valid move resolved](images/checkersValidMoveBasicResolved.png)
</div>

- if an opponent piece is diagonal and adjacent and the tile behind the opponent piece is unoccupied, you may move over the opponent piece. This 'captures' the opponent piece and it is is removed from the board for the rest of the game
- 

# king piece











# CodeBowl Game Description
CodeBowl is a 1v1, turn-based, tile-based, risk-management board game with RPG elements. Based around team ball sports like soccer/american football but with ability to knock players out of the game. 
- 1v1: this game is a 2 player competitive game
- Turn-Based: Turns alternate between users where a user chooses actions for their team players then the other user chooses actions for their team players for 16 total turns
- Tile-Based: teams of 7 players spread out on 20Lx11W tile grid on their respective sides of the board
- Risk-management: all actions attempted by players have probabilities of failure. Strategy and careful team-building will decrease those chances
- RPG Elements: players having stats that affect their abilities
- Win Conditions: score more points than opponent by carrying ball to goal before turn limit or kill all opponent players.

The game is divided into two sets of 8 turns called 'halves' where one team plays as 'offense' and the other team plays as 'defense'. At the beginning of each half, the ball is placed on a random tile on the offense's side of the board. The objective of the offensive team is to pick up the ball and run with the ball to the defense side's goal zone. The objective of the defensive team is to prevent the offensive team from scoring before all turns for the half are finished. 

![Code Bowl Initial State](images/codeBowlKickOff.png)
<br>
<i> After both users place their players, the ball is placed on a random tile on the offense team's half of the board and the offensive team begins its first turn. </i>

During a turn the user attempts an action for each player on their team, moving across the board, moving into the loose ball to attempt picking it up, or moving into an adjacent opponent player to attempt an attack. However, some events cause the turn to end before all players can perform an action: when a player attempts to pick up a loose ball but fails, attempts an attack but fails, or gets tripped when moving past an adjacent enemy. In these cases, the turn ends and the other user may perform actions for each of their team players. Turns alternate between users: when all players on a team have performed an action, the other team performs their turn.

Finally, the strategic element of this game comes ways that users can increase chances that their player's action will succeed: through careful placement of their players and attention to their player stats: movement, strength, agility. 

### Combat:
An attack between 2 players will have a 50% chance to knock the player down, a 25% chance to have no effect, and a 25% chance to knock the attacker down ending that user's turn prematurely. If a player attempts an attack and one of his teammates is also adjacent to the target player, there is a 75% chance to knock the player down, a 20% chance to have no effect, and a 5% chance to fail the attack. A successful knockdown moves the attacker into the tile where the target was, and the target back one tile in the direction of the attack.

![Blue Player Attacks Red Player With No Ally](images/codeBowlAttackSetupNoAlly.png)
<br>
<i> Blue player sets up an attack on red player with 50% chance success </i>

![Blue Player Attacks Red Player With Ally](images/codeBowlAttackSetupAlly.png)
<br>
<i> Blue player sets up attack on red player with 75% chance success because another blue player is adjacent to target</i>

### Knockdown and Injury:
If a player fails an attack or receives a successful attack they are knocked down. This means they drop the ball if they have it and that they must 'stand up' their next action, causing a -3 penalty to their movement and an inability to trip players or perform an attack. Whenever a player is knocked down there is also a chance the player will be injured and removed from the game: with a 10% base chance and +5% for each point of strength the attacker has. 

![Blue Player Successfully Attacks Red Player With Ally](images/codeBowlAttackSuccessAlly.png)
<br>
<i> Blue player successfully attacks red player and knocks them down.</i>

![Blue Player Attacks Red Player With Ally with Injury](images/codeBowlAttackSuccessAllyInjury.png)
<br>
<i> Blue player successfully attacks red player. Unlucky red player is injured and removed from this game.</i>

### Movement:
Players can move multiple tiles on their action up to the number of their movement stat. Each step is a transition to an adjacent tile on the board. When players attempt to move near opponent players, they may be tripped triggering their knockdown and injury. When a player with 0 Agility moves OUT OF a tile adjacent to an enemy, there is a 60% chance the move will be successful and 40% chance they are tripped. Every point of agility the player moving has increases chances of success by 10%. 

![Blue Player safe move](images/codeBowlSuccessfulMove.png)
<br>
<i> Blue player has 4 movement stat so sets up move of 4 tiles. There is no risk in this move because the path does not move past tiles adjacent to any opponents </i>

![Blue Player risky move](images/codeBowlRiskyMove.png)
<br>
<i> Blue player sets up risky move, where first step is safe, but 2nd and 3rd steps are adjacent to enemy, each one with 40% chance to be knocked down </i>

![Blue Player risky move fail](images/codeBowlRiskyMoveFail.png)
<br>
<i> Blue player succeeds first easy step and second risky step, but fails the third step is tripped and knocked down by the opponent player</i>

### Ball:
Players may move on to a loose ball in order to attempt picking it up. A player with base 0 agility has a 60% chance to successfully pick up the ball and each point of agility increases the success rate by 10%. If they fail the pick up, the ball scatters to a random adjacent square and the user's turn ends immediately. If the ball scatters to a tile with a player on it, that player attempts to pick up the ball. This continues until the ball is successfully picked up or lands on an empty tile. 

### Optional Advanced Game Features:
- when knocking down a player, attacker may select 3 tiles in the direction of the attack for the target to end up
- players who are knocked outside of board are instantly injured
- when attacking, a non-successful attack may perform a knock-back with no knockdown
- if the ball or a player holding the ball end up outside of the left and right border of board, place the ball on a random tile
- when attacking, only allies that are not adjacent to other opponents may assist you and increase chances of success
- attack success is based off of difference of strength between attacker and target
- picking up ball is based on difference of agility of attempter and opponent player adjacent to ball with highest agility
- players may gain experience that can be used to purchase special abilities that affect their success rates 
- add an 'armor' stat that offers resistance to being injured






