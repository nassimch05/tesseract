# Function to preprocess the frame for better OCR results
def preprocess_frame_for_ocr(frame):
    # Convert to grayscale
    gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
    # Apply a threshold to make the text more distinct
    _, thresh = cv2.threshold(gray, 150, 255, cv2.THRESH_BINARY + cv2.THRESH_OTSU)
    # Return the processed frame
    return thresh

# Clear previous results
extracted_subtitles.clear()
frame_count = 0

# Reopen the video file
video_capture = cv2.VideoCapture(video_file_path)

while True:
    # Read frame
    success, frame = video_capture.read()
    if not success:
        break

    # Save the frame at the specified interval
    if frame_count % frame_interval == 0:
        # Preprocess the frame
        processed_frame = preprocess_frame_for_ocr(frame)
        
        # Perform OCR on the processed frame
        text = pytesseract.image_to_string(processed_frame, lang='eng')
        
        # If text is detected, save it to the list
        if text.strip():
            extracted_subtitles.append(text.strip())

    # Increment frame count
    frame_count += 1

# Release the video capture
video_capture.release()

# Combine extracted subtitles into a single string
combined_subtitles = "\n".join(extracted_subtitles)
combined_subtitles[:500]  # Displaying the first 500 characters of extracted subtitles for review
