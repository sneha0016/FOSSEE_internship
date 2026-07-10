# Dataset

Annotated Lego brick images, exported from Roboflow (auto-label pipeline, v4). 2,155 images total, 36MB.

Each `batch_XX/` or `extension/` folder is a Roboflow export and contains, per image:
- `<name>.jpg` — the image
- `<name>.txt` — YOLO-format label (normalized bounding box + class index)
- `_annotations.coco.json` — COCO-format annotations for the whole batch

## Class breakdown

| Class | Batches | Images |
|---|---|---|
| Red | batch_01, batch_02 | 492 |
| Blue | batch_01, batch_02 | 464 |
| Grey | batch_01, extension | 479 |
| White | batch_01, batch_02 | 475 |
| Pearl | batch_01 | 245 |
| **Total** | | **2,155** |

Red and blue are included as reliable reference classes. Grey, white, and pearl are the classes flagged as underperforming in the original training run (see main README — annotation noise, scale mismatch, insufficient discriminative signal at low contrast). This is the expanded, auto-labeled re-collection aimed at fixing those three.

The remaining classes from the original 11-class set (black, yellow, green, orange, tan, flat plate) aren't in this export — add them here following the same `dataset/<class>/batch_XX/` structure if/when they're re-collected or re-annotated.

## Usage

Multiple batch folders per class are kept separate (rather than merged) so you can track which annotation pass a given image came from — useful if a later batch fixes annotation issues present in an earlier one. Merge them at training time via your data loader or a symlink/manifest step, not by physically combining the folders.
