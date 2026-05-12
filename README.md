# Chess CNN Counterfactual Explainability

Explainable AI project studying counterfactual explanations for a convolutional neural network that classifies chess positions.

This project was completed at Florida Institute of Technology for MTH 4326 – Explainable AI.

## Repository Description

Counterfactual explainability project testing how small chess-board changes affect CNN position-classification predictions.

## Project Overview

This project analyzes how stable a chess-position CNN is under small board changes.

The model classifies chess positions into three Stockfish-based evaluation classes:

- Black better
- Draw / Equal
- White better

The main goal is not to build a strong chess engine. The CNN is used as a model that can be explained.

The main question is:

What small board change is enough to change the CNN prediction?

## Main Idea

A convolutional neural network is trained on chess positions represented as 13 by 8 by 8 tensors.

After training, the model is explained using two methods:

- gradient saliency
- counterfactual search

Saliency maps show where the model is locally sensitive.

Counterfactual explanations test whether an actual input intervention changes the model prediction.

## Data

The dataset comes from publicly available Lichess PGN games.

- Source: Lichess PGN database
- Positions extracted after 20 full moves
- Dataset size: 10,000 positions
- Input format: 13 by 8 by 8 tensor
- 12 piece channels
- 1 side-to-move channel
- Labels generated using Stockfish evaluation

Label rule:

Class 0:
- Meaning: Black better
- Stockfish evaluation: eval < -1.0

Class 1:
- Meaning: Draw / Equal
- Stockfish evaluation: -1.0 <= eval <= +1.0

Class 2:
- Meaning: White better
- Stockfish evaluation: eval > +1.0

Raw PGN files and Stockfish binary are not included in this repository.

## Model

A small convolutional neural network is trained on chess-board tensors.

The CNN is not intended to be a strong chess evaluator. It is trained so that its learned decision boundaries can be inspected with explainability methods.

The model uses the board tensor as input and predicts one of the three evaluation classes.

## Baseline Explanation: Gradient Saliency

Gradient saliency is used as a baseline explanation method.

It measures how sensitive the predicted class score is to each input square.

In this project, saliency values are summed across the 13 tensor channels to create one board-level 8 by 8 importance map.

Saliency gives useful local information, but it does not prove that changing a highlighted square will actually change the model prediction.

Because of that, counterfactual testing is used as the main explanation method.

## Counterfactual Explanation Method

The main XAI method is counterfactual search.

A counterfactual explanation asks:

What minimal change to the input would make the model predict a different class?

The implemented method uses Level 1 counterfactuals:

- remove one non-king piece
- run the model again
- check if prediction changes
- measure material distance
- record which piece caused the prediction flip

Piece removal is not a legal chess move. In this project, it is used only as an intervention-based sensitivity test.

## Results

The CNN reached:

- Test accuracy: 63.73%
- Majority-class baseline: 44.60%
- Improvement over baseline: +19.13 percentage points

This means the model learned some chess-evaluation structure, even though it is not a perfect evaluator.

Counterfactual analysis showed:

- Flip rate: 76.20% on 500 sampled test positions
- Many successful counterfactuals came from pawn removals
- Most counterfactual material distances were 1 or 3
- Draw / Equal positions were especially fragile
- White and Black advantage positions produced more valid counterfactuals

## Interpretation

The high counterfactual flip rate has two meanings.

Positive side:

- The method finds many valid counterfactual examples.
- One-piece interventions often change prediction.
- This gives many examples for explanation analysis.

Important caution:

- Very high flip rate also means model fragility.
- The model sometimes changes decision too easily.
- A strong chess evaluator should not flip class too often after low-material piece removal unless the piece is tactically or positionally critical.

So the counterfactual method does not only explain the model. It also exposes weaknesses in the model decision boundary.

## Main Findings

1. The CNN learned meaningful chess-evaluation structure.

2. Gradient saliency gives a useful baseline view of local board sensitivity.

3. Counterfactuals test whether concrete piece-level interventions actually change prediction.

4. One-piece removal changed model prediction in many sampled test positions.

5. Many successful counterfactuals came from pawns, which suggests that model boundaries are often close to the original position.

6. High counterfactual validity is useful, but it also reveals model fragility.

7. The model is useful for XAI analysis, but it is not a reliable chess evaluator.

## Repository Structure

README.md
LICENSE
NOTICE.md
requirements.txt
notebooks/
    chess_counterfactual_xai.ipynb
reports/
    MTH4326_Project2_Report.pdf
slides/
    MTH4326_Project2_Slides.pdf
figures/
    class_distribution.png
    confusion_matrix.png
    saliency_map.png
    counterfactual_distance.png
    confidence_vs_distance.png
    counterfactual_piece_counts.png

The figures folder is optional. It can be used if exported plots are included separately from the notebook.

## Installation

Clone the repository:

git clone https://github.com/Darhaal/chess-cnn-counterfactual-xai.git

Go into the project folder:

cd chess-cnn-counterfactual-xai

Create a virtual environment:

python -m venv venv

Activate the virtual environment on Windows:

venv\Scripts\activate

Activate the virtual environment on macOS or Linux:

source venv/bin/activate

Install dependencies:

pip install -r requirements.txt

## Requirements

Main Python libraries used:

numpy
pandas
matplotlib
scikit-learn
python-chess
torch
torchvision
jupyter

Stockfish must be downloaded separately if labels are regenerated.

Stockfish binary is not included in this repository.

## How to Run

Open the notebook:

jupyter notebook notebooks/chess_counterfactual_xai.ipynb

Then run the notebook cells in order.

General pipeline:

1. Parse or load Lichess PGN games.
2. Extract one position after 20 full moves.
3. Evaluate the position with Stockfish.
4. Convert the board into a 13 by 8 by 8 tensor.
5. Train the CNN classifier.
6. Evaluate model performance.
7. Generate saliency maps.
8. Run one-piece-removal counterfactual search.
9. Measure flip rate and material distance.
10. Interpret model stability and limitations.

## Important Notes

Piece removal is not a legal chess move.

In this project it is used only as a model-sensitivity test.

The CNN should not be interpreted as a chess engine. Counterfactual explanations are used to inspect model behavior and expose fragile decisions.

## Limitations

There are several limitations:

- The CNN is small and trained for academic analysis.
- Stockfish labels are approximate because short evaluation time is used.
- One-piece removal is not a legal chess move.
- Counterfactual validity does not automatically mean chess validity.
- The model may be sensitive to low-material changes.
- Draw / Equal positions are difficult and often close to decision boundaries.

## License

This project is licensed under the MIT License.
