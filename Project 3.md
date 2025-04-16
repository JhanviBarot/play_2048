# 🎮 2048 Game in C++ - Terminal Based

## Introduction

2048 is a single-player sliding tile puzzle game created by Gabriele Cirulli in 2014. The goal of the game is to combine numbered tiles on a 4x4 grid to create a tile with the number 2048.

Benefits -> Improves strategic thinking, fun and addictive , boosts memory and attention

---

## Why we selected this game??

We wanted to select a easily executable project and learn about how we can implement a realistic game using graphics library such as SFML.

---

## Key Challenges in 2048:

1. *Dynamic Grid Compression & Merging Logic*
   - Implementing the logic to shift tiles and then merge equal ones — while avoiding multiple merges in a single move — is more intricate than Tetris block placement.

2. *Game State Tracking*
   - Maintaining current, previous (for undo), and updated states requires efficient use of *data structures* like stacks and vectors. Tetris typically does not require historical state management.

3. *Random Tile Generation with Valid Constraints*
   - After each move, a random new tile must be placed *only on empty spaces* — a non-trivial search and validation task.

4. *Undo Functionality*
   - A fully operational undo feature requires deep copying and restoration of 2D grid states, which adds significant complexity.

5. *No Predefined Patterns*
   - Tetris blocks follow a fixed set of shapes and physics. 2048 tiles evolve dynamically and depend on prior player decisions — making simulation and testing less predictable.

6. *Game Termination Conditions*
   - Checking whether no moves are left in 2048 involves scanning for adjacent equal tiles, which adds logical overhead. In Tetris, loss is simply when blocks stack beyond the visible area.

Overall, *2048 relies heavily on logic-based simulation*, precise conditionals, memory state management, and randomization — making it deceptively more complex to implement cleanly compared to a basic version of Tetris.

---

# 🎮 2048 Game in C++ - Terminal Based

Welcome to the **2048 Game** implemented in **C++** — a fully functional recreation of the popular number tile game, designed for terminal/console play. This version replicates the original 2048 mechanics while introducing features like undo, restart, and scoring — all within a single `play_2024.cpp` file.

This document will provide a **comprehensive overview** of the entire project including:
- Game Objective and User Experience
- Code Architecture and Functions
- Input Controls
- Stack Usage for Undo Mechanism
- Visual Representations
- Compilation Guide
- Educational Insights and Learning Outcomes

---

## 🔖 Metadata and Project Overview

| Property              | Value                             |
|----------------------|-----------------------------------|
| **Project Title**     | play_2048                         |
| **Language**          | C++                               |
| **Author**            | Mandeep Singh                     |
| **Maintainer**        | [Jhanvi Barot](https://github.com/JhanviBarot) |
| **License**           | GNU General Public License v2     |
| **Platform**          | Terminal                          |
| **File Structure**    | Single-file architecture          |

---

## 🎯 Game Objective

The player must slide numbered tiles on a 4x4 grid to combine them and create the **2048 tile**. With every move:
- Tiles slide in the chosen direction.
- Adjacent tiles of the same value merge.
- A new tile (2 or 4) appears randomly.

The game ends when:
- A tile reaches 2048 (win).
- No valid moves remain (loss).

---

## 📁 Folder and File Structure

```bash
play_2048/
│
├── play_2024.cpp        # Main game source code
├── LICENSE              # GNU GPL v2 License
├── README.md            # This documentation
└── images/              # UI and game-state screenshots
    ├── sample_game_ui.png
    ├── win_screen.png
    └── game_over.png
```

---

## Flowchart
![image alt](https://github.com/JhanviBarot/play_2048/blob/2c4749360172389ff283f14f79cd7f7287cd9e3a/flowchart.png)

---

## 💡 Game Features

| Feature             | Description                                                        |
|--------------------|--------------------------------------------------------------------|
| Terminal UI        | Visual grid displayed using iostream + iomanip formatting          |
| Random Generation  | New tiles (2 or 4) generated after every move                      |
| Score Calculation  | Score increases as tiles merge                                     |
| Undo Functionality | Up to 5 moves can be undone using a stack                          |
| Restart Option     | Reset the game without quitting                                    |
| Instructions Menu  | Print game rules and controls                                      |
| Win/Loss Handling  | Custom output on reaching 2048 or running out of moves             |

---

## 🎮 Game Controls

| Key | Action         |
|-----|----------------|
| `w` | Move Up        |
| `s` | Move Down      |
| `a` | Move Left      |
| `d` | Move Right     |
| `u` | Undo Move      |
| `r` | Restart Game   |
| `i` | Show Instructions |
| `q` | Quit Game      |

> Invalid keys prompt the user to re-enter a correct choice.

---

## 🛠️ Code Architecture

The game is encapsulated within a class named `play` which includes both the game state and its operations.

### Core Data Members
- `int g[4][4]` – Game grid
- `int g_copy[4][4]` – Previous state copy
- `stack<vector<vector<int>>> undo_stack` – Stack of game states
- `stack<int> score_stack` – Stack of scores

### Core Functions

| Function         | Purpose |
|------------------|---------|
| `initialize()`   | Initializes board with two '2's at random places |
| `move_left()`    | Shifts and merges tiles to the left |
| `sum_left()`     | Adds adjacent equal values (after shifting) |
| `generate_new_index()` | Adds a new tile randomly (2 or 4) |
| `check_full()`   | Checks if board is completely filled |
| `game_ends()`    | Checks if no more valid moves exist |
| `calculate_max()`| Finds the highest tile value |
| `undo()`         | Pops and restores last game state |
| `restart()`      | Resets game to initial state |

All directional movement operations (`move_` + `sum_`) follow a 3-step pattern:
1. Shift tiles.
2. Merge tiles.
3. Shift again to fill gaps.

---
## **Description of some functions used in the game**

#### `int check_full()`
- **Purpose**: Checks if the 4x4 game grid is completely filled.
- **Returns**: 
  - `1` if no cell is empty (i.e., grid is full).
  - `0` if at least one cell is empty.
- **Use**: Helps determine whether to generate a new tile or check for game over conditions.
  ```cpp
  int play :: check_full(){
	int full_flag=1;
	for(int i=0;i<4;i++){
		for(int j=0;j<4;j++){
			if(g[i][j]==0){
				full_flag=0;
				break;
			}
		}
	}
	return full_flag;
  }

#### `int game_ends()`
- **Purpose**: Determines if the game has ended when no further moves are possible.
- **Checks**:
  - If any two adjacent horizontal or vertical tiles are equal, returns `0` (game can continue).
  - If no such adjacent equal tiles exist, returns `1` (game over).
- **Use**: Called when the grid is full to decide if the player can make any more moves.
  ```cpp
  int play :: game_ends(){
	int i,j,flag=1;
	for(i=0;i<4;i++){
		for(j=0;j<3;j++){
			if(g[i][j]==g[i][j+1]){
				flag=0;
				break;
			}
		}
		if(flag==0){
				break;
		}
	}
	if(flag==1){
		for(i=0;i<3;i++){
			for(j=0;j<4;j++){
				if(g[i][j]==g[i+1][j]){
					flag=0;
					break;
				}
			}
			if(flag==0){
				break;
			}
		}
	}
	return flag;
  }

---

#### `void generate_new_index()`
- **Purpose**: Randomly places a new tile (`2` or `4`) on an empty cell of the grid.
- **Mechanism**:
  - Finds a random empty cell.
  - Places a `2` with 60% probability or a `4` with 40% probability.
- **Use**: Called after each valid move to introduce a new tile into the grid.
  ```cpp
  void play :: generate_new_index(){
	int flag=1;
	if(!check_full()){
		while(flag){
			int i=random_index(4);
			int j=random_index(4);
			if(g[i][j]==0){
				int y=rand()%10+0;
				if(y<6){
					g[i][j]=2;
				}else{
					g[i][j]=4;
				}
				flag=0;
			}
		}
	}
  }

---

#### `void initialize()`
- **Purpose**: Initializes the game grid at the start or upon restart.
- **Mechanism**:
  - Sets all cells in the grid to `0`.
  - Randomly places two tiles with the value `2` in the grid.
- **Use**: Called at the start of the game or after a restart.
  ```cpp
  void play :: initialize(){
	for(int i=0;i<4;i++){
		for(int j=0;j<4;j++){
			g[i][j]=0;
			g_copy[i][j]=0;
		}
	}
	int i=random_index(4);
	int j=random_index(4);
	g[i][j]=2;
	i=random_index(4);
	j=random_index(4);
	g[i][j]=2;
	display();
  }

---

#### `void move_up()`
- **Purpose**: Moves all non-zero tiles upward in their respective columns, filling gaps.
- **Mechanism**:
  - Iterates through columns.
  - Shifts all numbers upward by removing zeros between them.
- **Use**: Called before `sum_up()` to align tiles for merging.
  ```cpp
  void play :: move_up(){
	for(int i=0;i<4;i++){
		for(int j=0;j<4;j++){
			if(!g[j][i]){
				for(int k=j+1;k<4;k++){
					if(g[k][i]){
						g[j][i]=g[k][i];
						g[k][i]=0;
						break;
					}
				}
			}
		}
	}
  }

---

#### `void move_left()`
- **Purpose**: Moves all non-zero tiles to the left in each row, filling gaps.
- **Mechanism**:
  - Iterates through rows.
  - Shifts all numbers to the left by removing zeros between them.
- **Use**: Called before `sum_left()` to align tiles for merging.
  ```cpp
  void play :: move_left(){
	for(int i=0;i<4;i++){
		for(int j=0;j<4;j++){
			if(!g[i][j]){
				for(int k=j+1;k<4;k++){
					if(g[i][k]){
						g[i][j]=g[i][k];
						g[i][k]=0;
						break;
					}
				}
			}
		}
	}
  }
---

#### `void sum_down()`
- **Purpose**: Merges tiles in each column from bottom to top if two adjacent tiles have the same value.
- **Mechanism**:
  - Adds the values and stores the result in the lower tile.
  - Clears the upper tile and adds the resulting value to the score.
- **Use**: Called after `move_down()` and before the next `move_down()`.
 ```cpp
void play :: sum_down(){
	for(int i=0;i<4;i++){
		for(int j=3;j>0;j--){
			if(g[j][i] && g[j][i]==g[j-1][i]){
				g[j][i]=g[j][i] + g[j-1][i];
				g[j-1][i]=0;
				score+=g[j][i];
				undo_score+=g[j][i];
			}
		}
	}
}
```

---

#### `void sum_right()`
- **Purpose**: Merges tiles in each row from right to left if two adjacent tiles are equal.
- **Mechanism**:
  - Adds the values and stores the result in the right tile.
  - Clears the left tile and updates the score.
- **Use**: Called after `move_right()` and before the next `move_right()`.
 ```cpp
- void play :: sum_right(){
	for(int i=0;i<4;i++){
		for(int j=3;j>0;j--){
			if(g[i][j] && g[i][j]==g[i][j-1]){
				g[i][j]=g[i][j] + g[i][j-1];
				g[i][j-1]=0;
				score=score + g[i][j];
				undo_score+=g[i][j];
			 }
		 }
	 }
 }
```

#### 🔁 `Undo Functionality`

The game supports undoing up to **5 previous moves**, implemented using:
- A stack of 2D vectors (`undo_stack`) to store grid states.
- A parallel stack (`score_stack`) to track score history.

```cpp
if (!undo_stack.empty()) {
  g = undo_stack.top(); undo_stack.pop();
  score = score_stack.top(); score_stack.pop();
  undo_limit++;
}
```

Undo is blocked after 5 uses per session.

---

## 📊 Sample Gameplay Screenshots

### 🧩 Initial Game Grid
![image alt](https://github.com/JhanviBarot/play_2048/blob/240c0972c390ee4be634b86c8688611c027d77be/InitialGameBoard.png)
---

## 🔍 How to Compile and Run

### Requirements
- C++ compiler (e.g. g++)
- Terminal (Linux/macOS/WSL)

### Compile
```bash
g++ -o play_2048 play_2024.cpp
```

### Run
```bash
./play_2048
```

---

# Implementation using SFML

The core logic of the game (tile movement, merging, random tile spawning, and win/loss detection) is built entirely using standard C++, while SFML is used to create a smooth, visually appealing interface.

### Implementation of features is as follows :-

▪️ 4×4 Tile Grid rendered using SFML RectangleShape objects.

▪️ Responsive Input Handling with arrow keys (Up, Down, Left, Right).

▪️ Dynamic Tile Coloring based on tile values (e.g., 2, 4, 8, ..., 2048).

▪️ Tile Number Rendering using SFML Font and Text.

▪️ Win and Game Over Detection with overlay messages.

▪️ Score Display updated in real time.

▪️ Smooth Window Rendering using SFML’s game loop.

---

# 🧠 Learning Outcomes

Working with this project helps reinforce:
- 2D Array Manipulation
- OOP Concepts (class design)
- Stack usage for undo mechanisms
- Terminal-based UI design with formatting
- Game loop construction and input validation

---

# 🕳️ Loopholes in the code

In the above mentioned code system("clear") is not working because windows.h header is not included and instead of system("clear"), system("cls") needs to used.

---

# 🚀 Future Enhancements

-> We can implement a more realistic and user friendly game using garphics and UI/UX.              
-> Adding a highscore tracker

---

# 📖 License

This project is licensed under the GNU General Public License Version 2.

---

# 🧑‍💻 Contribution Guide

We welcome enhancements! To contribute:

1. Fork this repository
2. Clone to your local system
3. Create a feature branch
4. Push your changes and create a Pull Request

Suggestions:
- Add persistent high score
- Save/load functionality
- Convert to GUI with SFML or SDL

---

# ✍️ Acknowledgments

- Developed by **Mandeep Singh**
- Repository by [Jhanvi Barot](https://github.com/JhanviBarot)
- Game concept by **Gabriele Cirulli**

---

# 📌 Summary

This terminal version of 2048 is a practical, interactive C++ project ideal for:
- Understanding game development logic
- Implementing real-time data structures
- Practicing memory and state handling

Feel free to build on this and turn it into something even bigger. Happy coding!

---

...
