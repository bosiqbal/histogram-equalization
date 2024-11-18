import cv2
import numpy as np
import matplotlib.pyplot as plt
from google.colab import files

def upload_image():
    """Upload an image file."""
    print("Please upload your image file:")
    uploaded = files.upload()
    image_path = next(iter(uploaded))
    return image_path

def calculate_histogram(image):
    """Calculate the histogram of the image."""
    height, width = image.shape[:2]
    histogram = [0] * 256

    for i in range(height):
        for j in range(width):
            r, g, b = image[i, j]
            grayscale = (r + g + b) // 3
            grayscale = np.clip(grayscale, 0, 255)
            histogram[grayscale] += 1
    
    return histogram

def calculate_cumulative_histogram(histogram, total_pixels):
    """Calculate the cumulative histogram and mapping."""
    cumulative_histogram = [0] * 256
    cumulative_sum = 0

    for i in range(256):
        cumulative_sum += histogram[i]
        cumulative_histogram[i] = (cumulative_sum * 255) // total_pixels
    
    return cumulative_histogram

def apply_histogram_equalization(image, mapping):
    """Apply histogram equalization to the image."""
    height, width = image.shape[:2]
    equalized_image = np.zeros_like(image, dtype=np.uint8)

    for i in range(height):
        for j in range(width):
            r, g, b = image[i, j]
            grayscale = (r + g + b) // 3
            grayscale = np.clip(grayscale, 0, 255)
            new_value = mapping[grayscale]
            equalized_image[i, j] = [new_value, new_value, new_value]
    
    return equalized_image

def calculate_equalized_histogram(mapping, original_histogram):
    """Calculate the histogram of the equalized image."""
    equalized_histogram = [0] * 256

    for i in range(256):
        equalized_histogram[mapping[i]] += original_histogram[i]
    
    return equalized_histogram

def plot_results(original_image, equalized_image, original_histogram, equalized_histogram):
    """Plot the original image, equalized image, and histograms."""
    plt.figure(figsize=(12, 6))

    plt.subplot(1, 2, 1)
    plt.imshow(original_image.astype(np.uint8))
    plt.title('Original Image')
    plt.axis('off')

    plt.subplot(1, 2, 2)
    plt.imshow(equalized_image)
    plt.title('Equalized Image')
    plt.axis('off')

    plt.tight_layout()
    plt.show()

    plt.figure(figsize=(12, 8))

    plt.subplot(2, 1, 1)
    plt.bar(range(256), original_histogram, color='blue', alpha=0.6)
    plt.title('Histogram of Original Image')
    plt.xlabel('Pixel Intensity')
    plt.ylabel('Frequency')

    plt.subplot(2, 1, 2)
    plt.bar(range(256), equalized_histogram, color='green', alpha=0.6)
    plt.title('Histogram of Equalized Image')
    plt.xlabel('Pixel Intensity')
    plt.ylabel('Frequency')

    plt.tight_layout()
    plt.show()

# Main Program
image_path = upload_image()
original_image = cv2.imread(image_path)
image_rgb = cv2.cvtColor(original_image, cv2.COLOR_BGR2RGB)
image_rgb = image_rgb.astype(np.uint16)

# Calculate histogram and cumulative histogram
original_histogram = calculate_histogram(image_rgb)
height, width = image_rgb.shape[:2]
total_pixels = height * width
cumulative_histogram = calculate_cumulative_histogram(original_histogram, total_pixels)

# Apply histogram equalization
equalized_image = apply_histogram_equalization(image_rgb, cumulative_histogram)

# Calculate histogram of the equalized image
equalized_histogram = calculate_equalized_histogram(cumulative_histogram, original_histogram)

# Plot results
plot_results(image_rgb, equalized_image, original_histogram, equalized_histogram)
