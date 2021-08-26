
# Project 7: CodeBowl
CodeBowl is a 1v1, turn-based, tile-based, risk-management game with RPG elements. Based loosely around ball sports like soccer/american football but with ability to knock players out of the game or kill them permanently. Inspired by classic board game: Blood Bowl. 
- 1v1: this game is a 2 player competitive game
- Turn-Based: User1 chooses actions for players on their team, then User2 chooses actions for players on their team
- Tile-Based: teams of 7vs7 players spread out on 20Lx11W tile grid
- Risk-management: all actions players attempt have probabilities of failure. Strategy and careful team-building will decrease those chances
- RPG Elements: teams are built and persist with players having stats that affect their abilities. Your players may die and you may buy new players with money from games
- Win Condition: be user with highest score after all rounds complete or user that eliminates all opponents players from field

# High Level Non-Functional Requirements
- sound OOP design
- web application
- multi-user
- persistent storage
- attention to common web application security concerns
- attention to privacy concerns with varying resource access for different types of users
- networked gameplay mode with 2 simultaneous players per game

# App Modes / High Level Functional Requirements
- login mode
- register mode
- manage team mode
- show leaderboard mode
- game mode with synchronous game moves and asynchronous chat 
- show game results mode

# User Data / Privacy Requirements
- persistent user data
- guests can spectate matches and have access to match history (leaderboard mode)
- Coaches can create teams
- CSRF
- if using SQL backend, prevent SQL injection


### Mapping of CodeBowl elements to quick start stepping stone concepts
- SS3(Forms API, Validation) 
    - registration page
        - username, first name, last name (char limit)
        - password (requires special characters? MUST WRITE OWN VALIDATOR)
        - email(real email?)
    - team creation page 
        - player stats must add to 12 (from set: strong, agile, balanced)
        - name (char limit)
        - image upload (size, filetype, filename) 
- SS3(Middleware, Security) -> sessions 
    - auto-login middleware for registered users using sessions
    - remember CSS 'theme' ?
    - preload user's images (team image, player images)
    - SQL-injection prevention for all text fields
    - CSRF tokens for all forms
    - restrictions on user-uploaded content
- SS4 (Drivers, Workers...)
    - synchronous chatroom on landing page to organize games using websockets
        - https://www.youtube.com/watch?v=8hxr3T5cUbo&t=1510s 
        - chatroom for 2 players in middle of game also
    - queue for handling multiple actions on a turn that might arrive
    - email system for forgotten password
    - use amazon s3 for CDN for images?
  
### Models
- User
   - String: first name
   - String: last name
   - String: email
   - String: username
   - String: password
   - Team team
- Team
   - String: name
   - Image: image 
   - Player[] players
- Player
   - String: name
   - String: image
   - int: strength
   - int: agility
   - int : movement 
   - bool: isKnockedDown
   - bool: isDead
- Game
   - Tiles[][] grid
   - int round
   - Team team1
   - Team team2
   - int team1Score
   - int team2Score
   - User CurrentUser
- Tile
 - ?Player player
 - boolean: hasBall
    
### User Interaction Flows
- login  -> control panel -> manage team -> restart team -> control panel
                          \-> search for game -> game start -> control panel
- register -> create team -> control panel

### Game Flow
#### High-Level
1. Initial Setup
2. 6 turns per user occur, alternating between users. If User1 kills all players of User2, game is immediately over with User1 as Winner. 
3. Halftime Setup. User who played offense initially now plays defense and User who played defense now plays offense.
4. 6 turns per user occur, alternating between users. If User1 kills all players of User2, game is immediately over with User1 as Winner. 
5. Game ends. Winner is user with most points or draw if even points. 

#### Setup
1. (on initial setup): 50% roll to determine who plays offense and defense
2. User who plays defense places their players on their half of the board
3. User who plays offense places their players on their half of the board
4. Ball is placed randomly on offense half
5. Offense player starts their turn

#### Turn Flow
1. Perform a single action for each player on their team from the following choices: 
    - Move: move <= the number of `Movement Per Turn` points for that player.
    - Attack: attempt to knock down / kill an adjacent opponent player.
2. Some events cause current user's turn to end immediately and other user's turn to begin
    - score goal
    - attack fails
    - move fails 
    - pick up ball fails


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
- if you move OUT OF a square that is adjacent to an enemy you have a chance to get knocked down
    - if players have equal agility: (60% safe, 40% knocked down) 
    - for each point higher in agility +10% safe, -10% knocked down)
    - for each point lower in agilty than target -10% safe, +10% knocked down 

### Game Mode Combat Mechanics
- a player may only attack adjacent enemies
- if players have equal strength: (50% knock opponent down, 25% no effect, 25% self gets knocked down) 
- for each net point advantage, +10% knock down opponent, -5% no effect, -5% self gets knocked down)
- if multiple allies are adjacent to target enemy, chances for successful knockdown go up 
    - add 1 net point advantage for every ally with equal strength to target
    - add 2 net point advantage for every ally with higher strength to target 
    - add .5 net advantage for every ally with lower strength than target (final net advantage points are math.floor()'d)
- upon successful knockdown, rolls for death. target has 10% chance to die. dead players leave the game but return next game. 

### Game Mode Ball Mechanics
- picking up a ball is based on agility. 3 agility gives 60% chance success to pick up ball. 
 for each point of agility higher than 3, add +10% chance. for each point lower than 3 -10% chance. 
- if player fails to pick up ball, ball scatters to random adjacent square 
    -  
- if player holding ball is knocked down, ball scatters randomly within 3x3 grid
- if player attempts to pick ball up and fails, ball scatters randomly 1 adjacent square
