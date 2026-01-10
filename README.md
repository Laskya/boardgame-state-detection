# Man, Don't Get Angry - board game detection

CV project 2. Takes a video of a game of *Man, Don't Get Angry* (with our own coin
extension) and detects what is going on on the board frame by frame - the board,
the pawns, the die and the coin tokens.

Jakub Laskowski (160287), Jakub Górniak (160326)

## How to run

```bash
pip install -r requirements.txt
```

Put the videos in a `videos/` folder next to the notebook, open
`mdga_detection.ipynb` and run the cells from top to bottom. The last cell goes
through every video and writes the annotated result to `output/`.

The report is in `report.ipynb`.
