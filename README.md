# Man, Don't Get Angry - board game detection from video

A computer vision pipeline that takes a video of a game of *Man, Don't Get Angry*
(with our own "coin" extension) and figures out what is happening on the board,
frame by frame.

Jakub Laskowski (160287), Jakub Górniak (160326)

## Overview

The input is a top-down (or slightly angled) video of a game. For every frame we
detect and track:

- **The board** - found with contour analysis, then rectified with a perspective
  warp and split into a grid.
- **Pawns** - 16 of them in 4 colours (yellow, green, blue, pink), tracked per tile
  with HSV colour analysis and a temporal filter.
- **The die** - found outside the board and read (1-6) with a few different pip
  counting methods that vote on the result.
- **Coin tokens** - red tokens outside the board, matched between frames by
  euclidean distance.

From that we recognise the game events:

- Dice roll / roll in progress
- Pawn entering the board (roll of 6)
- Capture / coin collection
- Game start (all pawns in the home bases)
- Win / game end (all 4 pawns of one player in the finish zone)

## Repository structure

```
boardgame-state-detection/
├── mdga_detection.ipynb   # the detection pipeline
├── report.ipynb           # report: methods, dataset, results
├── report.pdf             # exported report
├── videos/                # input videos (not tracked)
└── output/                # annotated videos (generated)
```

## Requirements

- Python 3.9+
- See [requirements.txt](requirements.txt)

```bash
git clone https://github.com/Laskya/boardgame-state-detection.git
cd boardgame-state-detection

pip install -r requirements.txt
```

## Usage

1. Put the videos in a `videos/` folder next to the notebook.
2. Open `mdga_detection.ipynb` and run the cells from top to bottom.
3. The last cell goes through every video in `videos/` and writes an annotated copy
   to `output/`.

For a single video use `process_video`:

```python
events = process_video("videos/easy_2.mp4", "output/easy_2.mp4", display_interval=30)
```

Each output frame shows the original view with the detections drawn on it and a
sidebar with the current game state, dice value, token count and the pawn positions
of each player.

## Methods

| Component | Approach |
| --------- | -------- |
| Board detection | Three binarisations (Otsu, fixed, adaptive) + contour analysis, perspective warp, grid from projection peaks |
| Pawn detection | HSV fill ratio per tile (centre vs border) with temporal locking and "ghost" removal for angled views |
| Dice detection | HSV/brightness mask, four pip counting methods (adaptive, fixed, blob detector, Otsu) combined by majority vote |
| Token detection | Red HSV mask (two ranges), circularity filter, distance based tracking |

The full write-up with the dataset description and the results is in
[report.ipynb](report.ipynb).
