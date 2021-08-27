
# Project 7: CodeBowl
CodeBowl is a 1v1, turn-based, tile-based, risk-management game with RPG elements. Based loosely around ball sports like soccer/american football but with ability to knock players out of the game or kill them permanently. Inspired by classic board game: Blood Bowl. 
- 1v1: this game is a 2 player competitive game
- Turn-Based: Turns alternate between users where a user chooses actions for their team players then the other user chooses actions for their team players for 16 total turns
- Tile-Based: teams of 7 players spread out on 20Lx11W tile grid on their respective sides of the board
- Risk-management: all actions attempted by players have probabilities of failure. Strategy and careful team-building will decrease those chances
- RPG Elements: teams are built and persist with players having stats that affect their abilities
- Win Condition: kill all opponent team members or score more points than opponent by carrying the ball to the enemy goal zone

Codebowl is based off of team ball sports like soccer or american football. The game is divided into two sets of 8 turns called 'halves' where one team plays as 'offense' and the other team plays as 'defense'. At the beginning of each half, the ball is placed on a random tile on the offense's side of the board. The objective of the offensive team is to pick up the ball and run with the ball to the defense side's goal zone, which is the final set of tiles behind the defensive team. The objective of the defensive team is to prevent the offensive team from scoring before all turns for the half are finished.

![Code Bowl Initial State](images/codeBowlKickOff.png)

During a user's turn, each player on their team may perform an action: either moving or attacking an enemy play in an adjacent square. Turns alternate between users: when all players on a team have performed an action, the other team performs their turn. As we mentioned earlier, this is a risk management game, and there is some probability that a turn ends prematurely when certain events happen, for example when a player attempts to pick up a loose ball but fails, fails when attempting an attack, or gets tripped when moving past an adjacent enemy. 
In these cases, a user's turn ends immediately and the other user may perform actions for each of their team players. 

A couple events can occur that cause the teams roles to switch also, where the offensive team becomes the defensive team. 
- a team scores a point
- a team drops the ball and the other team picks it up
- when halftime is reached, the original offensive team in the first turn becomes the defensive team and vice versa


# High Level Non-Functional Requirements
- sound OOP design
- 3-tier architecture
    - Client: browser, desktop app, mobile app, VR, or combinations
    - Server: Ruby on Rails, Django, Laravel, node.js
    - Database: SQL
- attention to common web application security concerns
- attention to privacy concerns with varying resource access for different types of users
- networked gameplay mode with 2 simultaneous players per game

# App Modes / High Level Functional Requirements
- login mode
- register mode
- user control panel mode
- show leaderboard mode
- game mode with synchronous game moves and asynchronous chat 
- show game results mode

# User Data / Privacy Requirements
- persistent user data
- guests can spectate matches and have access to match history (leaderboard mode)
- Coaches can create teams
- CSRF
- if using SQL backend, prevent SQL injection

    
# User Mode Sequences
- login  -> control panel -> manage team -> restart team -> control panel
                          \-> search for game -> game start -> control panel
- register -> create team -> control panel

# Game Flow
### High-Level Flow
1. Initial Setup
2. 6 turns per user occur, alternating between users. If User1 kills all players of User2, game is immediately over with User1 as Winner. 
3. Halftime Setup. User who played offense initially now plays defense and User who played defense now plays offense.
4. 6 turns per user occur, alternating between users. If User1 kills all players of User2, game is immediately over with User1 as Winner. 
5. Game ends. Winner is user with most points or draw if even points. 

### Setup Flow
1. (on initial setup): 50% roll to determine who plays offense and defense
2. User who plays defense places their players on their half of the board
3. User who plays offense places their players on their half of the board
4. Ball is placed randomly on offense half
5. Offense player starts their turn

### Turn Flow
1. Perform a single action for each player on their team from the following choices: 
    - Move: move <= the number of `Movement Per Turn` points for that player.
    - Attack: attempt to knock down / kill an adjacent opponent player.
2. Some events cause current user's turn to end immediately and other user's turn to begin
    - score goal
    - attack fails
    - move fails 
    - pick up ball fails

# Game Mode Requirements
### Home Screen Mode

### Login Mode

### Registration Mode

### User Control Panel Mode

### Game Play Mode

### Team Creation Mode
- pick name
- upload team image
- create 7 players
    - upload image
    - pick name
    - distribute 12 points among 3 stats: Strength, Agility, Movement 

### Game Mode Movement Mechanics
- user may select any of their players that has not finished their action to move
- movement begins with a left click of a friendly player to select it then left clicks on squares you wish to move within the movement allowance, then end with a click of submit
- right click cancels movement if not submitted and refreshes squares selected
- moving over the ball attempts to pick the ball up
- moving into an enemy attempts a combat attack
- you may not move into a square with a knocked down player
- all adjacent squares around opponents are 'tackle zones'. if you move OUT OF a square that is adjacent to an enemy you have a chance to get knocked down
    - if your players has equal agility to opponent with highest agility creating tackle zone: (60% safe, 40% knocked down) 
    - for each point higher agility than highest enemy agility, +10% safe, -10% knocked down)
    - for each point lower in agilty than highest enemy agility, -10% safe, +10% knocked down 

### Game Mode Combat Mechanics
- a player may only attack adjacent enemies
- if players have equal strength: (50% knock opponent down, 25% no effect, 25% self gets knocked down) 
- for each net point advantage, +10% knock down opponent, -5% no effect, -5% self gets knocked down)
- if multiple allies are adjacent to target enemy, chances for successful knockdown go up 
    - add 1 net point advantage for every ally not in any tackle zones with equal strength to target
    - add 2 net point advantage for every ally not in tacklezones with higher strength to target 
    - add .5 net advantage for every ally not in tacklezones with lower strength than target (final net advantage points are math.floor()'d)
- upon successful knockdown, rolls for death. Dead players are removed from the game and from the User's team permanently!
    - if attacker strength is equal to target armor: 5% chance to die
    - if attacker strength is greater than target armor: +5% for every point higher
        - EX: attacker Strength 5, target armor 5: 5% chance to die   

### Game Mode Ball Mechanics
- picking up a ball is based on agility. 
    - 3 agility gives 60% chance success to pick up ball. 
    - for each point of agility higher than 3, add +10% chance up to 90%. 
    - for each point lower than 3 -10% chance down to 30%. 
    - if any opponents are adjacent to ball, -10% chance
- if player fails to pick up ball, ball scatters to random adjacent square and User's turn is ended immediately
- if player holding ball is knocked down, ball scatters randomly within 3x3 grid from knocked down player
    -  if ball lands on a player, regardles of team, player makes a roll to pick up ball. If fails, ball scatters within 3x3 grid from that point again

