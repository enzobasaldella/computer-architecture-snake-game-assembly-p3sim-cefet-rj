# Snake Game — P3 Assembly

A version of the classic Snake game written in Assembly for the educational 16-bit P3 processor. Developed as a Computer Architecture course project, it runs in the P3 simulator and demonstrates interrupts, memory-mapped I/O, subroutines, and direct memory management.

> [!NOTE]
> Movement is driven by the simulator's timer interrupt. Direction changes use the P3 interrupt vectors `INT0` through `INT3`, rather than ordinary terminal input.

## Features

- Automatic snake movement triggered by timer interrupts
- Direction controls with prevention of immediate 180-degree turns
- Wall, self, and food collision detection
- Snake growth and score updates after eating food (`*`)
- Pseudo-random food placement using arithmetic on a stored random state
- A text-based playfield rendered through memory-mapped output

## Controls

The program assigns the following P3 interrupt vectors to movement:

| Interrupt | Direction |
| --- | --- |
| `INT0` | Up |
| `INT1` | Right |
| `INT2` | Down |
| `INT3` | Left |

Trigger these interrupts using the P3 simulator's interrupt controls or configured key mappings. The program accepts at most one valid direction change per movement cycle and ignores attempts to reverse directly into the opposite direction.

The snake starts moving to the right.

## How it works

The program uses an interrupt vector table and several routines to manage gameplay:

1. `Main` initializes the stack and screen, draws the board, places the first food item, and enables the timer.
2. Timer interrupt `INT15` invokes `StartGame`, which advances the snake and rearms the timer.
3. Direction interrupts update the current direction if the requested turn is allowed.
4. `MoveSnake` updates the head position, checks for food, redraws the body, and checks collisions.
5. Eating food updates the score, sets a growth flag, and generates another food position.
6. A wall or self-collision stops the timer and displays the game-over screen.

The snake's segment positions are stored in two fixed-size tables, `SnakeBodyRow` and `SnakeBodyCol`. During movement, the program shifts stored positions to update the body. This is **not** a circular-buffer implementation.

## Requirements

- Linux for the bundled `p3as-linux` assembler
- A Java runtime capable of launching the bundled `p3sim.jar` simulator
- A graphical environment for the P3 simulator

Both the assembler and simulator are included in the repository.

## Build and run

From the repository root, give the assembler executable permission (first run only):

```bash
chmod +x p3as-linux
```

Assemble the source file:

```bash
./p3as-linux snake-game.as
```

This generates `snake-game.exe`, the P3 machine-code file. Start the simulator with that program:

```bash
java -jar p3sim.jar snake-game.exe
```

Start execution in the simulator if it is not already running, then use the interrupt controls listed above to change direction.

> [!TIP]
> If the program is not automatically loaded from the command-line argument, open `p3sim.jar` without an argument and load `snake-game.exe` through the simulator's interface.

## Project structure

```text
.
├── snake-game.as   # Complete game implementation in P3 Assembly
├── p3as-linux      # Bundled assembler for Linux
├── p3sim.jar       # Bundled P3 simulator
└── README.md       # Project documentation
```

The assembler generates `snake-game.exe` locally; it is not part of the committed source files.

## Implementation notes

- Display output uses the P3 memory-mapped cursor and character-output addresses.
- Timer and directional interrupts are defined in the interrupt vector table.
- Snake positions use fixed-size tables of 100 entries; the program does not enforce a separate maximum-length check.
- The score is displayed in two character positions without explicit overflow handling.
- The repository does not include an automated test suite; behavior can be checked interactively in the simulator.
