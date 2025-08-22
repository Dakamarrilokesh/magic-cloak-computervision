# magic-cloak-computervision
An Invisibility Cloak project built using Python and OpenCV that makes objects disappear in real-time video streams. By detecting a specific color (red cloak) through color segmentation, the cloak is replaced with the background, creating a magical "invisible" effect — just like in the movies! 🎥✨

import cv2
import numpy as np

# Initialize video capture
cap = cv2.VideoCapture(0) # Use 0 for the default camera

# Capture background
for i in range(30):
    ret, background = cap.read()

if ret: # Check if background was captured successfully
    background = np.flip(background, axis=1)

    while cap.isOpened():
        ret, frame = cap.read()
        if not ret:
            break

        frame = np.flip(frame, axis=1)
        hsv = cv2.cvtColor(frame, cv2.COLOR_BGR2HSV) # Corrected function name

        lower_red = np.array([0, 120, 70])
        upper_red = np.array([10, 255, 255])
        mask1 = cv2.inRange(hsv, lower_red, upper_red) # Corrected function name

        lower_red = np.array([170, 120, 70])
        upper_red = np.array([180, 255, 255])
        mask2 = cv2.inRange(hsv, lower_red, upper_red)

        mask = mask1 + mask2

        mask = cv2.morphologyEx(mask, cv2.MORPH_OPEN, np.ones((3, 3), np.uint8), iterations=2)
        mask = cv2.dilate(mask, np.ones((3, 3), np.uint8), iterations=1)

        cloak_area = cv2.bitwise_and(background, background, mask=mask)

        inverse_mask = cv2.bitwise_not(mask)
        non_cloak_area = cv2.bitwise_and(frame, frame, mask=inverse_mask) # Corrected variable name

        final_output = cv2.addWeighted(cloak_area, 1, non_cloak_area, 1, 0) # Corrected function name and variable name

        cv2.imshow("Invisibility cloak", final_output)

        if cv2.waitKey(1) & 0xFF == ord('q'): # Corrected function name
            break

    # Release the capture and destroy windows outside the loop
    cap.release()
    cv2.destroyAllWindows()
else:
    print("Could not capture background. Please check your camera. ")
