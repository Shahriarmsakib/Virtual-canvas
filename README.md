# Airbrush: Touchless Drawing on a Virtual Canvas

![Python](https://img.shields.io/badge/python-3.9%2B-blue)
![OpenCV](https://img.shields.io/badge/OpenCV-4.7-green)

Airbrush turns a webcam into a drawing tablet. A colored marker on the fingertip is tracked in live video with classical image processing, and its path is drawn onto a virtual canvas. On-screen buttons let you change colors or clear the canvas without touching anything.

*Course project for EE 241 (Advanced Digital Image Processing), UC Riverside, Jan–Mar 2023. Demo video: [`EE-241-Demo.mp4`](EE-241-Demo.mp4) · report: [`Final-Report.pdf`](Final-Report.pdf) · proposal: [`Project Proposal.pdf`](Project%20Proposal.pdf)*

## How it works

```
webcam frame ──► HSV conversion ──► color threshold (mask) ──► erode / open / dilate
                                                                     │
       canvas ◄── draw stroke / change color / clear ◄── marker centre ◄── largest contour
```

1. **Capture:** frames come from the webcam through `cv2.VideoCapture`, mirrored for natural interaction.
2. **Segment:** each frame is converted to HSV, which separates hue from brightness and makes color thresholding robust to lighting. Pixels inside the marker's HSV range form a binary mask. The range is tunable live in a *Target Color* window.
3. **Clean up:** erosion, morphological opening and dilation (5×5 kernel) remove speckle noise and fill small holes in the mask.
4. **Localize:** the largest contour in the mask is taken as the marker, and its centroid (from image moments) is the pen position.
5. **Interact:** moving the pen over a color swatch changes the stroke color, moving it over *Clear All* wipes the canvas, and anywhere else it draws.

## Run it

```bash
pip install -r requirements.txt     # numpy, opencv-contrib-python
python main.py
```

- You need a built-in or external webcam.
- The default marker color is **blue**. Change it by adjusting the HSV sliders in the *Target Color* window.
- Move the marker at a steady pace; very fast motion causes gaps in the stroke.
- Press `q` to quit.

Tested with Python 3.9.6, NumPy 1.24.2 and opencv-contrib-python 4.7.0.72.
