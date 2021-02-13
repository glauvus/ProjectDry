# ProjectDry
Popular greek card game developed using PHP and a kinda RESTful API

Preview using two browsers:
![alt text](https://i.imgur.com/PjdwUZ6.gif)

Table of Contents
=================
   * [Install](#install)
      * [Requirements](#requirements)
      * [Install Guide](#install-guide)
   * [Game Description](#game-description)
   * [Project Description](#project-description)
   * [API Documentation](#api-documentation)
      * [Methods](#methods)
         * [Board](#board)
            * [Board Reading](#board-reading)
            * [Board Initialization](#board-initialization)
         * [Card](#card)
            * [Card Reading](#card-reading)
            * [Card Moving](#card-moving)
         * [Players](#players)
            * [Player Reading](#player-reading)
            * [Player initialization](#player-initialization)
         * [Status](#status)
            * [Status Reading](#status-reading)
      * [Entities](#entities)
         * [Board](#board-1)
         * [Players](#players)
         * [Game_status](#game_status)


# Demo Page

Please, visit the webpage: 
https://users.iee.ihu.gr/~it175103/ADISE19_ProjectDry/



# Install

## Requirements

* Apache2
* Mysql Server
* php

## Install Guide

 * Clone the project to a local folder <br/>
  `$ git clone https://github.com/glauvus/ProjectDry`

 * Make sure that the folder is reachable from Apache Server. It may be necessary to determine the settings below.

 * A Mysql database with the name 'project_db' needs to be created and loaded with the data of the file 'db/schema.sql'.

 * The file 'lib/config_local.php' needs to be created containing the following lines:
```
    <?php
	$DB_PASS = 'password';
	$DB_USER = 'username';
    ?>
```

# Game Description

'Dry' is a card game played with a deck of 52 cards by 2 to 4 players (in groups of 2). In the very first round, 4 cards are dealt face up in the center of the table, and in each round every player is given 4 cards. The players then take turns playing any card on their hands without constraints.

If a player plays a card with the same number as the one on the top of the table stack, or a J card, they get the stack. If the stack consists of only one card, they win a what is called 'Dry'.

For every 'dry' the player wins 10 points, for every A, K, Q, J, 10 (except from 10 of diamonds) and 2 of clubs wins 1 point, and 2 points for the 10 of diamonds.

Player with the most cards wins an extra 3 points.

# Project Description

The database consists of 4 tables: board, board_empty, players, game_status

The following requirements were satisfied:

- Connection initialization-authentication.
- Game rule checking.
- Player turn identification.
- DeadLock identification (game ending or player quitting).
- Score counting / Winner anouncement.
- API development.
- GUI development.
- Full storing of game status in Mysql.


# API Documentation

## Methods


### Board
#### Board Reading

```
GET /board/
```

Returns the [Board](#Board) (actually the hand of the player who made the request and the table stack).

#### Board Initialization
```
POST /board/
```

Initializes the Board. Resets everything.
Returns the [Board](#Board).

### Card
#### Card Reading

```
PUT /board/card/:x
```

Plays the card with identifier x from hand to table, and if needs to, gets the stack from the table. Of course, it checks for the presence of the card in the players hand and for the player's turn according to token.
Returns the new values of the [Board](#Board-1).

Json Data:

| Field             | Description                 | Required   |
| ----------------- | --------------------------- | ---------- |
| `x`               | Identifier of card x                | yes        |


### Players

#### Player Reading
```
GET /players/:p
```

Returns data of player p or data of all players if p is omitted. Value of p is either 1 or 2.

#### Player initialization
```
PUT /players/:p
```

Returns data of player p and a token. The token is used by the player throughout the game.

Json Data:

| Field             | Description                 | Required   |
| ----------------- | --------------------------- | ---------- |
| `username`        | Username of player p. | yes        |



### Status

#### Status Reading
```
GET /status/
```

Returns the [Game_status](#Game_status).



## Entities


### Board
---------

Board is a table with the structure shown below:


| Attribute                | Description                                  | Values                              |
| ------------------------ | -------------------------------------------- | ----------------------------------- |
| `card_id`                      | Card's identifier              | 1...52                                |
| `c_value`                      | Card's value              | 1...13                                |
| `c_score`                | Card's score                      | 0...2                             |
| `c_position`            | Card's position on board                         | 'deck', 'stack', 'top', 'hand1', 'hand2', 'won1', 'won2', 'dry1', 'dry2'                       |
| `c_order`                  | Card's order in the deck           | 1...52, null       |


### Players
---------

Players is a table with the structure shown below:


| Attribute                | Description                                  | Values                              |
| ------------------------ | -------------------------------------------- | ----------------------------------- |
| `p_id`               | Player's identifier                                 | 1, 2                              |
| `username`            | Player's username                | String                             |
| `token  `                | Player's secret token. Returned only when player logs in | HEX |
| `last_action`            | Player's last action                | Timestamp                             |


### Game_status
---------

Game Status is a table with the structure shown below:


| Attribute                | Description                                  | Values                              |
| ------------------------ | -------------------------------------------- | ----------------------------------- |
| `s_id`               | Status identifier             | Integer    |
| `status`               | Status             | 'not active', 'initialized', 'started', 'ended', 'aborted'     |
| `p_turn`                 | Current player's id       | 1, 2, null                              |
| `result`                 |  Winner's id |1, 2, null                              |
| `last_change`            | Last change on game status         | Timestamp |
