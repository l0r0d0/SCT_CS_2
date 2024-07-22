# Image Encryption Script Explanation

This document provides a detailed explanation of a Bash script designed to encrypt an image by swapping its pixels. The script leverages ImageMagick tools to perform the encryption.

## Overview

The script performs the following operations:
1. Checks for the correct number of arguments.
2. Validates the existence of the input image file.
3. Retrieves the dimensions of the input image.
4. Creates a copy of the input image for encryption.
5. Swaps pixels in the image to create an "encrypted" version.
6. Saves the modified image with a new filename.


```sh
#!/bin/bash

# Check if correct number of arguments are provided
if [ "$#" -ne 1 ]; then
    echo "Usage: $0 <image>"
    exit 1
fi

# Input image
input_image="$1"
output_image="encrypted_$1"

# Check if input image exists
if [ ! -f "$input_image" ]; then
    echo "Error: File $input_image does not exist."
    exit 1
fi

# Get image dimensions
width=$(identify -format "%w" "$input_image")
height=$(identify -format "%h" "$input_image")

# Create a copy of the input image for encryption
cp "$input_image" "$output_image"

# Function to swap two pixels
swap_pixels() {
    local x1=$1
    local y1=$2
    local x2=$3
    local y2=$4

    # Get pixel colors
    color1=$(convert "$output_image" -format "%[pixel:p{$x1,$y1}]" info:)
    color2=$(convert "$output_image" -format "%[pixel:p{$x2,$y2}]" info:)

    # Swap pixels
    convert "$output_image" -fill "$color1" -draw "point $x2,$y2" "$output_image"
    convert "$output_image" -fill "$color2" -draw "point $x1,$y1" "$output_image"
}

# Read and process each pixel
for ((x=0; x<$width; x++)); do
    for ((y=0; y<$height; y++)); do
        # Random coordinates for swapping
        rand_x=$((RANDOM % width))
        rand_y=$((RANDOM % height))

        # Swap current pixel with a random pixel
        swap_pixels $x $y $rand_x $rand_y
    done
done

echo "Image encrypted and saved as $output_image"
