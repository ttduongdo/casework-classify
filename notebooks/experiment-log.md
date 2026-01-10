# 1. BERT

=== Centroid classifier on Z (TEST) ===
Accuracy      : 0.8030
F1 (macro)    : 0.8032
F1 (weighted) : 0.8046

Full classification report:
              precision    recall  f1-score   support

           0      0.930     0.789     0.854       152
           1      0.768     0.817     0.792       191
           2      0.851     0.836     0.844       171
           3      0.827     0.851     0.839       202
           4      0.918     0.834     0.874       187
           5      0.673     0.710     0.691       162
           6      0.768     0.793     0.781       184
           7      0.792     0.785     0.788       209
           8      0.837     0.815     0.826       189
           9      0.630     0.753     0.686       190
          10      0.811     0.828     0.819       186
          11      0.821     0.788     0.804       198
          12      0.939     0.926     0.932       215
          13      0.890     0.801     0.843       201
          14      0.779     0.764     0.772       199
          15      0.692     0.787     0.737       174
          16      0.800     0.749     0.774       203

    accuracy                          0.803      3213
   macro avg      0.807     0.802     0.803      3213
weighted avg      0.809     0.803     0.805      3213


# 2. Qwen3 8B
## Log
=== Centroid classifier on Z (contrastive embedding) ===
Accuracy      : 0.8136
F1 (macro)    : 0.8155
F1 (weighted) : 0.8162

Classification report (truncated):
              precision    recall  f1-score   support

           0      0.907     0.770     0.833       152
           1      0.886     0.812     0.847       191
           2      0.778     0.842     0.809       171
           3      0.835     0.851     0.843       202
           4      0.784     0.872     0.825       187
           5      0.770     0.722     0.745       162
           6      0.747     0.804     0.775       184
           7      0.836     0.780     0.807       209
           8      0.877     0.794     0.833       189
           9      0.806     0.763     0.784       190
          10      0.924     0.785     0.849       186
          11      0.868     0.798     0.832       198
          12      0.905     0.926     0.915       215
          13      0.938     0.821     0.875       201
          14      0.669     0.884     0.762       199
          15      0.957     0.759     0.846       174
          16      0.592     0.808     0.683       203

    accuracy                          0.814      3213
   macro avg      0.828     0.811     0.815      3213
weighted avg      0.827     0.814     0.816      3213

## Conclusion
- Switch to an Encoder model instead of a Decoder since Encoder models understand context from both directions
- DeBERTa: Disentangled Attention mechanism


# 3. Gradient Boosting for Decisions with CatBooost/XGBoost
## Issue
- Centroid evaluation approach assumes that the classes form perfect separable spheres when in reality the top agencies are not entirely independent and constituent emails may involve several top agencies at once.
- Use Gradient Booosting to learn complex decision boundaries on existing Z embeddings. (DL for learning, Gradient Boosting for decisions)

## Log
0:	learn: 0.3343626	test: 0.3203478	best: 0.3203478 (0)	total: 13.9ms	remaining: 41.6s
bestTest = 0.822960563
bestIteration = 169
Shrink model to first 170 iterations.

CatBoost Accuracy: 0.8167
CatBoost F1 Macro: 0.8160

Full Classification Report:
              precision    recall  f1-score   support

           0      0.843     0.776     0.808       152
           1      0.888     0.827     0.856       191
           2      0.800     0.842     0.821       171
           3      0.815     0.871     0.842       202
           4      0.848     0.866     0.857       187
           5      0.755     0.741     0.748       162
           6      0.728     0.815     0.769       184
           7      0.827     0.756     0.790       209
           8      0.862     0.825     0.843       189
           9      0.756     0.784     0.770       190
          10      0.807     0.812     0.810       186
          11      0.837     0.803     0.820       198
          12      0.929     0.916     0.923       215
          13      0.893     0.831     0.861       201
          14      0.786     0.829     0.807       199
          15      0.869     0.764     0.813       174
          16      0.685     0.793     0.735       203

    accuracy                          0.817      3213
   macro avg      0.819     0.815     0.816      3213
weighted avg      0.820     0.817     0.817      3213

## Conclusion
- +0.3% gain --> hit Embedding Ceiling
- possiblle that shrinking data to dimensions is leaving out information
