# Face Mask Detector

A computer-vision demo that detects faces in a video stream and classifies each detected face as **Mask** or **No Mask**. Face locations are found with OpenCV's pretrained Caffe SSD face detector; a TensorFlow/Keras MobileNetV2 classifier predicts mask status.

The repository includes a trained mask-classification model, the face-detection model, and a sample video. You can run inference with either the sample video or your system camera. The training dataset is not committed because of its size; provide it locally if you want to train the classifier.

> This is an educational computer-vision project. Predictions may be wrong and should not be used as the sole basis for safety or access-control decisions.

## Requirements

- Python 3.11 (recommended; TensorFlow 2.15.1 is pinned in `requirements.txt`)
- Windows, macOS, or Linux
- A webcam for camera mode
- Several GB of available memory if training from the included dataset

## Setup

Open a terminal in the project directory:

```powershell
cd "C:\path\to\Face-Mask-Detector-main"
```

Create and activate a virtual environment, then install dependencies:

```powershell
py -3.11 -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

If PowerShell blocks virtual-environment activation, run the commands using the environment's Python executable directly instead:

```powershell
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
```

On macOS/Linux, create and activate the environment with:

```bash
python3.11 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

## Run the detector

Run commands from the project directory so the face-detector files can be found.

### Use your system camera

```powershell
python detect_mask_video.py --camera
```

The default camera device index is `0`. Press **q** while the video window is active to quit. If you have more than one camera, change `video_source` in `detect_mask_video.py` to the appropriate device index (for example, `1`).

If the camera cannot be opened, allow camera access for your terminal/Python in the operating system's privacy settings, close other applications using the camera, and check that the device index is correct.

### Use the bundled sample video

```powershell
python detect_mask_video.py
```

The detector reads `Mask Detector.mp4` and exits when the video ends or when you press **q**.

### Run using the included virtual environment

If `.venv` is already set up in this checkout, you can run without activating it:

```powershell
.\.venv\Scripts\python.exe .\detect_mask_video.py --camera
```

Omit `--camera` to use the sample video.

## Train the mask classifier

Training uses images under `Data_Set` and the pretrained MobileNetV2 feature extractor. The dataset is excluded from Git, so you need to supply the images locally in this structure before training:

```text
Data_Set/
├── with_mask/
└── without_mask/
```

The original local dataset contains about 3,800 images across those two folders. To train:

```powershell
python mask_detector.py
```

The script uses an 80/20 stratified train/test split, data augmentation, and 20 training epochs. It prints a classification report and writes:

- `mask_detector.h5` — trained classifier used by the video and camera detector
- `plot.png` — training and validation loss/accuracy plot

On its first run, TensorFlow may need to download MobileNetV2's ImageNet weights. Training loads and preprocesses the dataset in memory, so allow several GB of free RAM. The included `mask_detector.h5` is already trained, so you do not need to train before running inference.

## Project files

| Path | Purpose |
| --- | --- |
| `detect_mask_video.py` | Runs face-mask inference on the sample video or system camera |
| `mask_detector.py` | Trains the MobileNetV2-based mask classifier |
| `mask_detector.h5` | Trained mask-classification model used for inference |
| `mask_detector.model` | Legacy copy of the trained classifier |
| `face_detector/deploy.prototxt` | Caffe face-detector network configuration |
| `face_detector/res10_300x300_ssd_iter_140000.caffemodel` | Pretrained Caffe face-detector weights |
| `Data_Set/with_mask/` | Locally supplied labeled images of people wearing masks (not committed) |
| `Data_Set/without_mask/` | Locally supplied labeled images of people not wearing masks (not committed) |
| `Mask Detector.mp4` | Sample video input |
| `requirements.txt` | Python dependencies |
| `mask_detector.ipynb` | Notebook version of classifier training |
| `detect_mask_video.ipynb` | Notebook version of video inference |
| `LICENSE` | Project license |

## Troubleshooting

- **`ModuleNotFoundError` when starting the script:** install the dependencies into the same Python environment used to run the script with `python -m pip install -r requirements.txt`.
- **Camera window does not open:** check operating-system camera permissions, make sure another app is not using the webcam, and try a different camera device index.
- **Model or face-detector file not found:** run the command from the project directory and check that the model files listed above are present.
- **Training cannot find images:** confirm that both `Data_Set/with_mask/` and `Data_Set/without_mask/` exist and contain images.
- **TensorFlow installation fails:** use Python 3.11 and install from `requirements.txt` in a fresh virtual environment.

## License

See [LICENSE](LICENSE).
