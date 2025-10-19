1. Purpose

The objective of this project is to develop a supervised deep learning system for automated carotid stenosis assessment. The system predicts:

Polygon vertex coordinates delineating carotid artery and plaque regions.

Segmentation masks to visualize plaque and lumen areas.

This provides quantitative stenosis measurements and visual interpretability, supporting clinical evaluation and reducing manual annotation workload.

2. Methodology
2.1 Dataset

Dataset consists of triplets:

x.jpg: original carotid image

xcarotid.jpg: cropped/annotated carotid region

x.txt: normalized polygon coordinates (class + 5 (x,y) pairs)

Data splits:

Training: 10,876 triplets + 61 sample triplets

Validation: 330 triplets

Test: 220 triplets

2.2 Preprocessing

Images resized to 128×128, normalized to [0,1].

Polygon coordinates scaled accordingly.

Masks generated from polygon annotations to supervise segmentation.

Minor augmentations applied to improve generalization (rotation, flip, scaling).

2.3 Model Architecture

U-Net style CNN with dual outputs:

Mask branch: reconstructs carotid artery and plaque masks with skip connections.

Vertex branch: predicts 10 normalized polygon coordinates.

Regularization with L2 penalties, batch normalization, and dropout.

Loss functions:

Vertex: combined MSE + Huber + centroid + scale loss

Mask: BCE + Dice loss

Polygon-mask consistency enforced

2.4 Training

Optimizer: Adam, LR=1e-3

Batch size: 180, trained for 50 epochs

Callbacks: EarlyStopping, ReduceLROnPlateau, ModelCheckpoint, TensorBoard

2.5 Post-processing & Visualization

Predicted polygons clipped inside masks for anatomical validity.

Grad-CAM heatmaps highlight clinically relevant regions, avoiding red for clarity.

Visual overlays prepared for all test images.

3. Results
3.1 Quantitative Metrics

From 220 test triplets, predicted mask coverage:

Metric	Mean (%)	Std (%)
Plaque Area Percentage	3.01	0.53
Lumen Area Percentage	3.14	0.68
Carotid Artery Coverage	6.15	–

Metrics computed on 128×128 predicted masks.

Saved in /content/carotid/test/predicted/carotid_area_percentages.csv.

3.2 Visual Interpretation

Predicted polygons and masks closely match ground truth.

Grad-CAM confirms model attends to plaque regions.

Robust predictions even under variable image quality.
