<p align="center">
  <img src="https://img.shields.io/badge/42-cub3D-blue?style=for-the-badge" alt="42 cub3D"/>
</p>

<h1 align="center">cub3D</h1>

<p align="center">
  <em>A raycasting engine inspired by Wolfenstein 3D</em>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Language-C-00599C?style=flat-square&logo=c&logoColor=white" alt="C"/>
  <img src="https://img.shields.io/badge/Graphics-MiniLibX-purple?style=flat-square" alt="MiniLibX"/>
  <img src="https://img.shields.io/badge/Build-Makefile-orange?style=flat-square" alt="Makefile"/>
  <img src="https://img.shields.io/badge/Resolution-1920x1080-green?style=flat-square" alt="1920x1080"/>
</p>

---

## About

**cub3D** is a 3D graphical engine built from scratch in C using raycasting, developed as part of the [42 School](https://42.fr/) curriculum. Inspired by the legendary [Wolfenstein 3D](https://en.wikipedia.org/wiki/Wolfenstein_3D) (1992) — the first true FPS — this project renders a first-person perspective view of a maze defined by a 2D map, using the [Digital Differential Analyzer (DDA)](https://en.wikipedia.org/wiki/Digital_differential_analyzer_(graphics_algorithm)) raycasting algorithm.

> *This project is a deep dive into computer graphics fundamentals: raycasting, texture mapping, and real-time rendering — all without a modern graphics API.*

## Authors

| Author | GitHub |
|--------|--------|
| **tkafanov** | [@timofeykafanov](https://github.com/timofeykafanov) |
| **sopperma** | [@SilasLovelace](https://github.com/SilasLovelace) |

---

## Features

- **Raycasting engine** — real-time 3D rendering from a 2D map using DDA
- **Textured walls** — four directional wall textures (N/S/E/W) loaded from `.xpm` files
- **Configurable colors** — customizable floor and ceiling RGB colors
- **Smooth movement** — WASD movement with collision detection against walls
- **Camera rotation** — left/right arrow key rotation with configurable FOV (60 degrees)
- **Map parser** — robust `.cub` file parser with extensive validation
- **Wall collision** — step-based collision detection preventing the player from walking through walls
- **1920x1080 resolution** — full HD rendering
- **Clean error handling** — descriptive error messages for invalid maps, textures, and configurations

---

## Architecture

```
.cub file --> Map Parser --> Validator --> Game Loop --> Raycaster --> Display
```

```
cub3D/
├── includes/
│   ├── cub3d.h              # Main header (includes & libraries)
│   ├── structs.h             # Data structures (ray, map, player, MLX)
│   ├── macros.h              # Constants (speed, FOV, resolution)
│   └── functions.h           # Function prototypes
├── srcs/
│   ├── main.c                # Entry point & argument validation
│   ├── init/
│   │   └── init.c            # Memory initialization (singleton pattern)
│   ├── map_tools/            # Map parsing & validation
│   │   ├── file_reader.c     # Read .cub file into memory
│   │   ├── map_parser.c      # Parse textures, colors, and map grid
│   │   ├── create_map.c      # Build the 2D map array
│   │   ├── get_data.c        # Extract resource paths & colors
│   │   ├── validation.c      # Map validity checks (walls, player)
│   │   ├── validation_utils.c
│   │   └── utils.c           # Parsing helpers
│   ├── game/                 # Game loop & input handling
│   │   ├── run_game.c        # MLX initialization & main loop
│   │   ├── handlers.c        # Keyboard press/release handlers
│   │   ├── moving.c          # WASD movement with collision
│   │   ├── rotation.c        # Camera rotation
│   │   └── images.c          # Texture loading from .xpm files
│   ├── raycaster/            # Core rendering engine
│   │   ├── raycaster.c       # DDA raycasting & textured wall drawing
│   │   └── raycaster_utils.c # Ray parameter helpers
│   ├── display/
│   │   └── display.c         # Pixel buffer & screen rendering
│   ├── close/
│   │   └── close.c           # Clean shutdown & resource cleanup
│   └── freeing/
│       └── freeing.c         # Memory deallocation
├── libft/                    # Custom C library (42 libft)
├── textures/                 # Wall texture .xpm files
├── testMaps/
│   ├── valid/                # Valid test maps
│   └── invalid/              # Invalid maps for error testing
└── Makefile
```

### How It Works

1. **Map Parsing** — Reads a `.cub` scene file containing wall texture paths, floor/ceiling colors, and a 2D map grid where `1` = wall, `0` = open space, and `N/S/E/W` = player spawn + orientation.
2. **Validation** — Ensures the map is fully enclosed by walls, has exactly one player, valid textures exist, and RGB colors are in range.
3. **MLX Initialization** — Creates the window, loads `.xpm` textures into memory, and sets up the image buffer.
4. **Game Loop** — On each frame, processes key input (movement & rotation), then re-renders the scene.
5. **Raycasting** — For each vertical column of pixels, casts a ray from the player's position. The DDA algorithm steps through the grid to find the nearest wall, calculates the perpendicular distance (to correct fisheye), and determines the wall slice height and texture column.
6. **Rendering** — Draws ceiling, textured wall strip, and floor for each column, then pushes the image buffer to the window.

---

## Getting Started

### Prerequisites

- **GCC** or **CC** compiler
- **GNU Make**
- **MiniLibX** with X11 libraries
- **Linux** (X11-based rendering)

#### Install dependencies:

```bash
# Ubuntu / Debian
sudo apt-get install libx11-dev libxext-dev libmlx-dev

# Fedora
sudo dnf install libX11-devel libXext-devel
```

### Build

```bash
# Clone the repository
git clone <repository-url>
cd cube

# Compile
make

# Run with a map file
./cub3D testMaps/valid/test1.cub
```

### Makefile Targets

| Target | Description |
|--------|-------------|
| `make` / `make all` | Build the project |
| `make clean` | Remove object files |
| `make fclean` | Remove object files and binary |
| `make re` | Full recompile |

---

## Usage

### Map File Format (`.cub`)

A `.cub` file defines the scene configuration:

```
NO ./textures/north.xpm
SO ./textures/south.xpm
WE ./textures/west.xpm
EA ./textures/east.xpm

F 0,0,0
C 0,0,50

        111111111111111111
        100000000110000001
        101100000111000001
111111111011000001110000001
100000000011000001110111111
111101111111110111000000001
110001111101010111000000001
100000000000000011000000001
110000011101010111110111N001
111101111110101011011110001
111111111111111111111111111
```

| Element | Description |
|---------|-------------|
| `NO`, `SO`, `WE`, `EA` | Paths to north/south/west/east wall textures (`.xpm`) |
| `F R,G,B` | Floor color (0-255 per channel) |
| `C R,G,B` | Ceiling color (0-255 per channel) |
| `1` | Wall |
| `0` | Open space |
| `N`, `S`, `E`, `W` | Player spawn position and facing direction |

### Controls

| Key | Action |
|-----|--------|
| `W` | Move forward |
| `S` | Move backward |
| `A` | Strafe left |
| `D` | Strafe right |
| Left arrow | Rotate camera left |
| Right arrow | Rotate camera right |
| `ESC` | Exit the game |

### Map Validation

The parser validates:
- Map is fully enclosed by walls (no holes)
- Exactly one player spawn point
- All textures exist and are valid `.xpm` files
- RGB color values are within range (0-255)
- No invalid characters in the map grid
- File has `.cub` extension

See `testMaps/invalid/` for 30+ edge case test maps.

---

## Configuration

Key constants can be adjusted in `includes/macros.h`:

| Constant | Default | Description |
|----------|---------|-------------|
| `SCREEN_WIDTH` | 1920 | Window width in pixels |
| `SCREEN_HEIGHT` | 1080 | Window height in pixels |
| `FOV` | ~60 degrees | Field of view (in radians: `M_PI / 3`) |
| `SPEED` | 0.2 | Player movement speed |
| `ROT_SPEED` | ~4.5 degrees | Camera rotation speed per frame |
| `TEXTURE_SIZE` | 1024 | Expected texture resolution |

---

## Key Concepts Learned

- **Raycasting** with the DDA (Digital Differential Analyzer) algorithm
- **Texture mapping** — sampling wall textures based on ray-wall intersection point
- **Fisheye correction** — using perpendicular distance instead of Euclidean distance
- **MiniLibX** — low-level graphics library for window management and pixel-level rendering
- **Collision detection** — step-based wall collision with axis-separated checks
- **Scene file parsing** — robust file format parsing with extensive error handling
- **Double buffering** — rendering to an off-screen image before displaying
- **Trigonometry** in practice — sin, cos, and atan2 for movement and ray angles

---

## Limitations

- Linux only (X11 / MiniLibX dependency)
- No sprite rendering
- No doors or interactive elements
- No minimap overlay
- No mouse look
- Fixed resolution (configurable via macros, not at runtime)

---

## License

This project was developed as part of the 42 School curriculum. Feel free to use it as a reference, but please respect the [42 School's academic integrity rules](https://www.42.fr/).

---

<p align="center">
  Made at <strong>42 Vienna</strong>
</p>

