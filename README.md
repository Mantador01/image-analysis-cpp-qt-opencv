# 🖼️ Image Analysis — C++ (Qt + OpenCV)

This repository contains my TP1 project for the **Image Analysis / Image Processing** course at **Université Claude Bernard Lyon 1**.  
It provides both a **text-based console UI** and a **Qt GUI** to run custom implementations of classic image-processing operations and compare them against **OpenCV**.

---

## 🎯 Goals

- Re-implement core image-processing operators **from scratch** (median, mean, Gaussian blur, Sobel).  
- Build **histogram processing** (equalization & contrast stretching) with explicit LUTs.  
- Implement **geometric transforms** (zoom, reduce, rotate, flip), including **zoom-at-point**.  
- Provide **two UIs** (console and Qt) that reuse the same underlying functions.  
- Compare results and histograms against **OpenCV** implementations.

---

## 🧭 Project Structure

```
code/
├─ Convolution/                  # generic convolution + mean/gaussian/sobel filters
├─ Filtrage_median/              # median filter (manual implementation)
├─ histogramProcessing/          # histogram, cumulative histogram, equalization, stretching
├─ Transformation/               # zoom, reduce, rotate, flip, zoom-at-point
├─ Interface/                    # console UI module
├─ test/                         # Qt GUI (test_module)
├─ donnee/ donnee2/              # input images
└─ resultat/                     # generated results (images, hist plots, LUTs)
```

> A ready-to-use **build/** folder is included with CMake presets and example outputs.

---

## 🧪 Features

### 1) Filters (manual implementations)
- **Median filter** (odd-sized kernels, nth_element)
- **Mean filter** (box filter via convolution)
- **Gaussian blur** (analytical kernel, normalized)
- **Sobel** (3×3 kernels in X/Y and gradient magnitude)

> All custom filters can be compared to OpenCV: `cv::medianBlur`, `cv::blur`, `cv::GaussianBlur`, `cv::Sobel`.

### 2) Histogram processing
- `calculateHistogram` / `calculateCumulativeHistogram`
- `equalizeHistogram` (LUT-based equalization)
- `stretchHistogram` (contrast stretching with min/max)
- Optional plotting for histograms and LUTs (saved in `resultat/`).

### 3) Geometric transformations
- `zoomImage(scale)` (bilinear)
- `reduceImage(scale)` (bilinear downscale)
- `rotateImage(angle)` (manual resampling with bounds)
- `flipImage(flipCode)` (vertical/horizontal/both)
- `zoomImageAtPoint(scale, center)` (ROI-based zoom)

---

## 🧰 Two User Interfaces

### A) Console UI (text mode)
- Interactive menu (`./ui_module`): load image, choose operation, write outputs to `resultat/`.

### B) Qt GUI (bonus)
- Load any image.
- Choose filter/transform from combo box.
- **Extra parameter** field for sigma, angle, scale, flip code, etc.
- Live preview with `QLabel` + `QPixmap`.
- Uses the same underlying functions as the console UI.

> **Note:** You need a local Qt installation to run the GUI.

---

## 🧪 Example Results (from the report)

- Histogram similarity vs. OpenCV:
  - **Mean**: 0.945531  
  - **Gaussian**: 0.972912  
  - **Sobel**: 0.999997

- Geometry samples (see `TP1/resultat/`):
  - `zoomed_at_center.png` (zoom-at-point, scale 2)
  - `reduced_image.png` (scale 0.5)
  - `rotated_image.png` (45°)
  - `flipped_image.png` (horizontal)

---

## 🧩 Code Excerpt (Qt main)

```cpp
// (Simplified) GUI actions
QObject::connect(applyButton, &QPushButton::clicked, [&]() {
  QString filterType = filterComboBox->currentText();
  int kernelSize = kernelSizeSpinBox->value();
  QString param = extraParameter->text();

  if (filterType == "Zoom" || filterType == "Reduce") {
    double scale = param.toDouble();
    processedImage = (filterType == "Zoom")
        ? zoomImage(loadedImage, scale)
        : reduceImage(loadedImage, scale);
  } else if (filterType == "Zoom at Point") {
    double scale = param.toDouble();
    processedImage = zoomImageAtPoint(loadedImage, scale);
  } else if (filterType == "Rotate") {
    double angle = param.toDouble();
    processedImage = rotateImage(loadedImage, angle);
  } else if (filterType == "Flip") {
    int flipCode = param.toInt();
    processedImage = flipImage(loadedImage, flipCode);
  } else if (filterType == "Equalize Histogram") {
    equalizeHistogram(loadedImage, processedImage);
  } else {
    applyFilter(filterType, kernelSize, imageLabel); // median/mean/gaussian/sobel
    return;
  }

  // display result
  QImage qImage = cvMatToQImage(processedImage);
  imageLabel->setPixmap(QPixmap::fromImage(qImage).scaled(
      imageLabel->size(), Qt::KeepAspectRatio));
});
```

---

## ⚙️ Build & Run

### Requirements
- **C++17**
- **CMake**
- **OpenCV 4.x**
- **Qt 5/6** (for the GUI)

### Build (console + GUI)
```bash
cd code/build
cmake ..
make
```

### Run
```bash
# Console UI (text)
./ui_module

# Individual modules
./filtrage_median
./convolution
./convolution_hist
./geometric_transformations
./histogram_processing

# Qt GUI (bonus)
./test_module
```
> For both UIs, input images are in `../donnee/*.png` and `../donnee2/*.png`.  
> Outputs are written to `../resultat/`.

---

## 🧠 Notes & Troubleshooting

- **Borders handling**: all manual filters avoid outer margins by radius.  
- **GUI display issues**: ensure the `QLabel` is updated with a scaled `QPixmap`.  
- **Zoom vs. Resize**: the project implements both a classic resize and a **zoom-at-point** (ROI).  
- **Performance**: median and Gaussian are implemented naively for clarity; OpenCV is faster.

---

## 👨‍💻 Authors

**Alexandre COTTIER** — Master’s student in Computer Science *(ID3D)*, Université Claude Bernard Lyon 1  
**Vu Anh Duy** — Co-author

📍 Lyon, France  
🔗 GitHub: https://github.com/Mantador01  
🔗 LinkedIn: https://www.linkedin.com/in/alexandre-cottier-72ab20227/

---

## 📜 License

This repository is intended for **educational and research purposes**.  
You may reuse and adapt the code with attribution.
