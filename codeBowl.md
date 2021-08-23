
# Project 7: CodeBowl
Turn-based, tile-based, risk-management game with RPG elements. Based loosely around ball sports like soccer/american football but with ability to knock players out of the game or kill them permanently. Inspired by classic board game: Blood Bowl. 
- teams of 7vs7 spread out on 20Lx11W grid
- team members have varying stats (movement per action, armor, strength, agility)
- goals are to score points by getting ball into enemy goal zone or remove opponents from field
- winner is player with highest score after all roundsor player that eliminates all opponents from field


# High Level Non-Functional Requirements
- sound OOP design
- web application
- multi-user
- persistent storage
- attention to common web application security concerns
- attention to privacy concerns with varying resource access for different types of users
- networked gameplay with 2 simultaneous players per game

# App Modes / High Level Functional Requirements
- login mode
- register/create account mode
- create team mode
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
1. players for each team are spread out in preset way on either side (maybe add user placement eventually). 
2. coin toss occurs to see which team 'attacks' first
3. attacking team has ball put on random square on their side
4. attacking team players each perform an action (move or attack) until they get knocked down, drop the ball, use all turns, score a point, or end turn early with button
5. defending team players each perform an action (move or attack) until they get knocked down, or use all turns, have point scored by enemy team, or end turn early with button
6. game play continues until round limit (10) is reached with 1 turn for each team per round, or all players of a team are dead
7. winner is team with higher score when game ends

### Team Creation
- pick name
- upload team image
- create 7 players
    - upload image
    - pick name
    - distribute 12 points among 3 stats: Strength, Agility, Movement 

### Movement Mechanics
- user may select any of their players that has not finished movement/action to move
- movement begins with a left click of a friendly player to select it then left clicks on squares you wish to move within the movement allowance, then end with a click of submit
- right click cancels movement if not submitted and refreshes squares selected
- if you move OUT OF a square that is adjacent to an enemy you have a chance to get knocked down
    - if players have equal agility: (60% safe, 40% knocked down) 
    - for each point higher in agility +10% safe, -10% knocked down)
    - for each point lower in agilty than target -10% safe, +10% knocked down 

### Combat Mechanics
- a player may only attack adjacent enemies
- if players have equal strength: (50% knock opponent down, 25% no effect, 25% self gets knocked down)
- for each net point advantage, +10% knock down opponent, -5% no effect, -5% self gets knocked down)
- if multiple allies are adjacent to target enemy, chances for successful knockdown go up 
    - add 1 net point advantage for every ally with equal strength to target
    - add 2 net point advantage for every ally with higher strength to target 
    - add .5 net advantage for every ally with lower strength than target (final net advantage points are math.floor()'d)
- upon successful knockdown, rolls for death. target has 10% chance to die. dead players leave the game but return next game. 

### Ball Mechanics
- picking up a ball is based on agility. 3 agility gives 60% chance with. for each point of agility higher than 3, add +10% chance. for each point lower than 3 -10% chance. 
- if player holding ball is knocked down, ball scatters randomly within 3x3 grid
- if player attempts to pick ball up and fails, ball scatters randomly 1 adjacent square
