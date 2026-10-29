# AI Based Maze Navigation and Pathfinding System

CS23533 Foundations of Artificial Intelligence – mini project, Department of Cyber Security,
Rajalakshmi Engineering College (2026–2027, odd semester).

Team: Alagappan R (2116241901003), Aswin J (2116241901010). Supervisor: Dr. S. Bhuvana.

The project has two parts:

* **Pathfinding** – BFS, DFS, Dijkstra, Greedy best-first and A* (Manhattan, Euclidean or zero
  heuristic) on a fully known maze, compared on nodes expanded, path cost and search time.
* **Navigation** – an agent follows its A* plan through a maze that changes after planning
  (new walls appear). It senses the cells within a sensing range, re-plans with A* from where it
  stands when its route is blocked, and is compared with an agent that keeps its fixed plan.

## Setup

```
python -m venv .venv && . .venv/bin/activate
pip install -r requirements.txt          # Python 3.11 or newer
```

## Run

```
python app.py                            # interactive GUI
python -m unittest discover -s tests -v  # 24 automated tests
python bench/run_experiments.py          # experiments E1–E4 -> results/*.json (under a minute)
python bench/plot_results.py             # charts of the report -> figures/*.png
python app.py --load my_maze.txt         # open a saved maze (validated; a bad file is rejected with its reason)
python app.py --load my_maze.txt --strict          # also require a matching my_maze.txt.sha256 digest
python app.py --mode dynamic --walls 3 --sense 5   # dynamic navigation: 3 new walls, sensing range 5
```

GUI keys: `1`–`5` BFS, DFS, Dijkstra, Greedy, A* · `C` compare four algorithms ·
`D` dynamic navigation with one new wall · `N` with three new walls · `G` new maze · `T` maze type ·
`+`/`-` size · `SPACE` run/pause the search animation · mouse click toggles a wall
(`S` or `E` then a click moves the start or the goal) · `W` saves the maze to `saved/` with its SHA-256 digest.

Headless screenshot, for example:

```
SDL_VIDEODRIVER=dummy python app.py --shot out.png --mode dynamic --walls 3 --seed 9
```

## Layout

```
mazesolver/maze.py        maze model, generators (perfect, braided, open, weighted), validated loader, SHA-256 digests
mazesolver/search.py      BFS, DFS, Dijkstra, Greedy, A*; SearchResult with nodes expanded and time
mazesolver/dynamic.py     new walls after planning, sensing range, fixed plan vs re-planning A*
app.py                    Pygame GUI
bench/run_experiments.py  experiments E1, E2, E3, E4
bench/plot_results.py     charts of the report from results/*.json
tests/                    unit tests
results/                  raw experiment output (JSON)
```

## Security notes

* Maze files are checked before use: at most 201 × 201 cells, only the symbols `# . ~ S G`,
  rectangular rows, exactly one start and one goal, neither inside a wall.
* Saved mazes get a SHA-256 digest in a `.sha256` file next to them; a file that no longer matches
  its digest is rejected, and `--strict` rejects files without a digest.
* Nothing is unpickled or executed from a file; mazes are plain text parsed by a whitelist.
* Navigation runs are capped at ten moves per free cell, and the GUI caps the maze side at 101
  cells, new walls at 10 and the sensing range at 50.
