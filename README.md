# Khmer Handwritten OCR Dataset

![Task: OCR](https://img.shields.io/badge/Task-OCR-blue) ![Language: Khmer](https://img.shields.io/badge/Language-Khmer-red) ![Format: JPEG](https://img.shields.io/badge/Format-JPEG-green) ![Origin: CADT_Capstone](https://img.shields.io/badge/Origin-CADT_Capstone-orange)

A curated collection of Khmer handwritten data sourced from **high school lessons, homework, and exams**. This dataset was compiled and processed by students from the **Cambodia Academy of Digital Technology (CADT)** for a capstone project. It is specifically designed for training Khmer handwritten OCR systems, such as **TrOCR**.

## Team & Credits

This dataset is the result of a collaborative effort by the following members:

- **Ly Leab** — Main contributor
- **Lay Sopanha** — Compiler and normalizer; built the data engineering pipeline to consolidate formats, clean annotations, and normalize into a unified structure.
- **Narith Sopheakleap** — Contributor
- **Hinge Sothida** — Contributor
- **Sok Sothika** — Contributor

---

## Dataset Structure

The repository is organized as follows:

```text
khmer-ocr-dataset/
├── crops/                # Directory containing all image files (.jpg)
│   ├── crop_001.jpg
│   ├── crop_002.jpg
│   └── ...
├── labels.csv            # Ground truth mapping (file_name, text)
└── README.md             # This documentation
```

### Statistics
- **Content source:** High school academic materials (lessons, homework, exams)
- **Total samples:** 154 pages or ~3.9k snippets
- **Vocabulary:** Khmer Unicode (abugida script)
- **Encoding:** UTF-8

---

## Data Format

### Images
- **Format:** RGB JPEG (`.jpg`)
- **Background:** Masked to white (`#FFFFFF`) to reduce noise.
- **Content:** Handwritten Khmer script (lines or individual words).

### Labels (`labels.csv`)
A CSV file containing the mapping between images and text.
- **Header:** `file_name,text`
- **Encoding:** UTF-8

**Example:**

| file_name | text |
| :--- | :--- |
| `img_001.jpg` | សួស្ដី |
| `img_002.jpg` | ព្រះរាជាណាចក្រកម្ពុជា |
| `img_003.jpg` | ខ្ញុំស្រលាញ់អ្នក |

> **Note:** The dataset does not contain a pre-defined train/test split. It is recommended to shuffle and split the data using a fixed random seed (e.g., `42`) to ensure reproducibility.

---

## Usage

### 1. Installation
Clone the repository. If the dataset contains many images, ensure you have Git LFS installed if applicable.

```bash
git clone https://github.com/LaySopanha/Khmer-Handwritten-Dataset.git
cd Khmer-Handwritten-Dataset
```

### 2. Python Quick Start
Display an image and its label:

```python
import pandas as pd
from PIL import Image
from pathlib import Path

# Config
DATA_ROOT = Path("crops")
CSV_PATH = "labels.csv"

# Load Data
df = pd.read_csv(CSV_PATH)
row = df.iloc[0]

# Display
image_path = DATA_ROOT / row["file_name"]
text = row["text"]

print(f"Filename: {row['file_name']}")
print(f"Label: {text}")

img = Image.open(image_path).convert("RGB")
img.show()
```

### 3. PyTorch Dataset Example
Here is a copy-paste ready Dataset class for training:

```python
import torch
from torch.utils.data import Dataset
from PIL import Image
import pandas as pd
import os

class KhmerOCRDataset(Dataset):
    def __init__(self, root_dir, csv_file, transform=None):
        self.root_dir = root_dir
        self.df = pd.read_csv(csv_file)
        self.transform = transform

    def __len__(self):
        return len(self.df)

    def __getitem__(self, idx):
        if torch.is_tensor(idx):
            idx = idx.tolist()

        img_name = os.path.join(self.root_dir, self.df.iloc[idx, 0])
        image = Image.open(img_name).convert("RGB")
        text = self.df.iloc[idx, 1]

        if self.transform:
            image = self.transform(image)

        return image, text

# Usage
# dataset = KhmerOCRDataset(root_dir='crops', csv_file='labels.csv')
```

---

## Preprocessing Notes

Khmer is a complex script (Abugida). When using this data for training:

1.  **Normalization:** Ensure your tokenizer handles Khmer Unicode correctly. You may need to normalize Zero Width Spaces (`\u200b`) or specific Coeng characters depending on your model architecture.
2.  **Filtering:** While we have normalized the raw inputs, some handwriting may be highly stylized or messy due to the nature of student homework/exams.

---

## Integration

If you are using this with the main TrOCR training repository:
1.  Set `DATA_ROOT` to point to the `crops/` folder.
2.  Set `CSV_PATH` to point to `labels.csv`.

---

## License

This dataset is licensed under **Creative Commons Attribution 4.0 International (CC BY 4.0)**.

---

## Citation

If you use this dataset in your research, please cite:

```bibtex
@misc{khmer_ocr_dataset,
  author       = {Leab, Ly and Sopanha, Lay and Sopheakleap, Narith and Sothida, Hinge and Sothika, Sok},
  title        = {Khmer Handwritten OCR Dataset (CADT Capstone)},
  year         = {2025},
  publisher    = {GitHub},
  journal      = {GitHub repository},
  howpublished = {\url{https://github.com/LaySopanha/Khmer-Handwritten-Dataset}}
}
```
