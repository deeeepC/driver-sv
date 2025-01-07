## Features added:
- 478 face keypoints detection
- Direct iris keypoint detection for gaze score estimation
- Improved head pose estimation using the dynamical canonical face model
- Fixed euler angles function and wrong returned values
- Using time variables to make the code more modular and machine agnostic

**NOTE**: the old mediapipe version can still be found in the "dlib-based" repository branch.

## How Does It Work?

This script searches for the driver face, then use the mediapipe library to predict 478 face and iris keypoints.
The enumeration and location of all the face keypoints/landmarks

With those keypoints, the following scores are computed:

- **EAR**: Eye Aspect Ratio, it's the normalised average eyes aperture, and it's used to see how much the eyes are opened or closed
- **Gaze Score**: L2 Norm (Euclidean distance) between the center of the eye and the pupil, it's used to see if the driver is looking away or not
- **Head Pose**: Roll, Pitch and Yaw of the head of the driver. The angles are used to see if the driver is not looking straight ahead or doesn't have a straight head pose (is probably unconscious)
- **PERCLOS**: PERcentage of CLOSure eye time, used to see how much time the eyes are closed in a minute. A threshold of 0.2 is used in this case (20% of a minute) and the EAR score is used to estimate when the eyes are closed.

The driver states can be classified as:
- **Normal**: no messages are printed
- **Tired**: when the PERCLOS score is > 0.2, a warning message is printed on screen
- **Asleep**: when the eyes are closed (EAR < closure_threshold) for a certain amount of time, a warning message is printed on screen
- **Looking Away**: when the gaze score is higher than a certain threshold for a certain amount of time, a warning message is printed on screen
- **Distracted**: when the head pose score is higher than a certain threshold for a certain amount of time, a warning message is printed on screen


### Gaze Score Estimation
The gaze score gives information about how much the driver is looking away without turning his head.

To understand this, the distance between the eye center and the position of the pupil is computed. The result is then normalised by the eye width that can be different depending on the driver physionomy and distance from the camera.