# Hybrix Chess

Source code for the Chombit Chess video game, an API for chess-playing engines, and a console for testing engines. Everything is written in the [Hybrix language](https://www.hybrix.dev/docs/compiler).

**What is Hybrix?** Hybrix is an educational virtual computer with an emulator and full development environment that run in your web browser. Its custom 32-bit CPU executes [2,400,000 instructions per second](https://www.hybrix.dev/docs/machine/architecture/timing) with only [256 kilobytes of RAM](https://www.hybrix.dev/docs/machine/architecture/memory_regions). Like a classic computer, these specs encourage programs that a single person can understand with minimal reliance on frameworks. Deterministic [CPU timings](https://www.hybrix.dev/docs/assembler/intro) allow performance to be measured precisely and reproducibly. These qualities make it an interesting platform for coding chess algorithms.

## Chombit Chess

A full chess video game: pieces on a perspective board, a mouse-driven wand for picking squares, legal destinations highlighted on selection, and a status bar for undo, new game, and switching either side between Human and Robot. The Robot uses [Micro-Max](https://home.hccnet.nl/h.g.muller/max-src2.html) by H.G. Muller, ported to Hybrix with his permission; the source is not yet included in this repository. Difficulty controls the search time (Easy = 2 secs, Medium = 8 secs, Hard = 30 secs). Artwork and sound resources are in the Hybrix Designer's native formats, but will be made inspectable later.

For more details about this game, see [the Hybrix website docs](https://www.hybrix.dev/ref/chess-demo).

| Filename                                                             | Description                                                   |
| -------------------------------------------------------------------- | ------------------------------------------------------------- |
| [chombit-chess/main.hyb](./chombit-chess/main.hyb)                   | Game state machine, click handling, and undo history          |
| [chombit-chess/gameboard.hyb](./chombit-chess/gameboard.hyb)         | Perspective board drawing and square hit testing              |
| [chombit-chess/hud.hyb](./chombit-chess/hud.hyb)                     | Status bar, its questions, and the about box                  |
| [chombit-chess/selection.hyb](./chombit-chess/selection.hyb)         | Piece selection and legal target marks                        |
| [chombit-chess/chess_sprites.hyb](./chombit-chess/chess_sprites.hyb) | Piece sprites and their sliding animation                     |
| [chombit-chess/wand_actor.hyb](./chombit-chess/wand_actor.hyb)       | The pointer sprite and the square or bar column under its tip |
| [chombit-chess/symbol_types.hyb](./chombit-chess/symbol_types.hyb)   | Designer symbol declarations for scenes and actor spots       |

## The engine API

The `chess_engine` class defines a plug-in contract allowing a chess **front end** such as Chombit Chess to load a **chess engine**. It connects our two chess games to the two engine implementations.

The subclass defines the chess game rules, since search algorithms often implement slightly different rules. It reports board state through a `chess_position` object. Four engine **features** are declared separately, so a subclass isn't obligated to implement them all:

| Feature           | Enables                                     |
| ----------------- | ------------------------------------------- |
| `load_position()` | Loading and saving board states, or puzzles |
| `undo_move()`     | Taking back one or more moves               |
| `collect_moves()` | Highlighting legal destinations in a GUI    |
| `find_move()`     | The search algorithm for computer play      |

The `reference_chess_engine` class implements all four features, using the orthodox chess rules including check, checkmate, and stalemate. It models the full state for a human-vs-human game. Its search algorithm is a minimal placeholder that simply returns the first legal move it finds.

| Filename                                                                         | Description                                                    |
| -------------------------------------------------------------------------------- | -------------------------------------------------------------- |
| [engine-api/chess_engine.hyb](./engine-api/chess_engine.hyb)                     | Enums, the `chess_position` record, and the plug-in base class |
| [engine-api/reference_chess_engine.hyb](./engine-api/reference_chess_engine.hyb) | Reference subclass implementing all four features              |

To write your own engine, subclass `chess_engine`, set the `can_` flags for the features you support, then fill in the hooks. If it plays well, we may incorporate it into the official Chombit Chess game as an alternate computer player.

## Chess Console

A [HASCII](https://www.hybrix.dev/docs/machine/sheets/hascii) console chess game in the spirit of the original Micro-Max stdio loop. Type a move like `e2e4` and press ENTER, or press ENTER alone to let the engine move, or ESC to stop it thinking. Each computer move reports its node count and elapsed milliseconds.

This is the simplest way to test a new `chess_engine` subclass. By setting the `node_budget` constant with `budget_ms` disabled (0), you can get fully deterministic output.

| Filename                                           | Description                            |
| -------------------------------------------------- | -------------------------------------- |
| [chess-console/main.hyb](./chess-console/main.hyb) | Text console front end for typed moves |

## License

BSD-3-Clause license; see [NOTICE.txt](./NOTICE.txt) for legal details.

## Learn More

- [About Hybrix](https://www.hybrix.dev/about)
- [Online tutorials](https://www.hybrix.dev/docs/learn)
- [Hybrix Language Reference](https://www.hybrix.dev/docs/compiler/language)
- [Hybrix ROM Player](https://www.hybrix.dev/emulator)
