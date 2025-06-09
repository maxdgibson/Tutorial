---
title: Converting Sprites to C Arrays for Microcontroller Displays
date: 2025-06-08
authors:
  - name: Max Gibson
---

![16x16 PNG Sprite Converted to C Code](/mnt/data/b40f1f2e-1efe-4111-84e7-7ea7c5b6dd38.png)

## Introduction
This tutorial walks you through the process of taking your custom-made pixel art sprites and converting them into C-style arrays that can be imported into Arduino projects—specifically for a Tamagotchi-inspired device. Whether you're building a retro display or a handheld digital pet, this guide will show you how to go from sprite sheet to embedded code.

### Learning Objectives
- Understand how to create sprites using pixel art tools
- Learn how to extract PNG data into C arrays
- Format graphics for use in microcontroller environments (e.g., Arduino + ST7789)
- Automate batch conversions with Python

### Background Information
Sprites are small, static or animated images used to represent characters or objects in embedded displays or games. In low-resource environments (like Arduino projects), graphics are often stored as static arrays of byte data. This format allows the data to be rendered efficiently on an LCD or OLED display.

You can create your own sprite PNGs using pixel art tools like **Aseprite**, or source them online.

**Aseprite** is a paid software available on Steam. It provides a rich interface for pixel-based sprite design. The central canvas area is your design workspace, and the vertical toolbar on the right provides key tools for drawing, filling, erasing, and selecting. 

![Aseprite Workspace](/mnt/data/f51e5c5c-b319-485e-8259-1c15040d41de.png)

The display we used for this project is a 1.14" 135x240 full-view TFT IPS screen that runs over SPI using the ST7789 driver. It operates at 3.3V and is compatible with Arduino and ESP32.

![ST7789 Display Module Used](/mnt/data/0a3f234c-243c-40d3-a354-17670f72c3c5.png)

## Getting Started

### Required Downloads and Installations
- [Aseprite](https://www.aseprite.org/): For sprite design (paid)
- Python 3.x: For image processing
- `Pillow` library (Python Imaging Library fork): Install via `pip install pillow`

### Required Components
| Component Name       | Quantity |
|----------------------|----------|
| ESP32 or Arduino     | 1        |
| ST7789 Display       | 1        |
| Breadboard + Wires   | 1 set    |
| USB Cable            | 1        |

### Required Tools and Equipment
- Computer (Mac, Windows, or Linux)
- Aseprite software
- Arduino IDE

## Part 01: Creating and Converting Sprites

### Introduction
In this section, we cover how to create your sprite, then convert it into a format suitable for use in C/Arduino projects.

### Objective
- Create a sprite PNG using Aseprite
- Convert PNGs into C arrays using Python
- Store and access them in header files for microcontroller display

### Background Information
Sprites should be uniformly sized (e.g., 16x16 or 32x32 pixels) and saved as PNGs with a transparent background. These images are then processed into a byte array that represents the RGB values (often 565 format) needed by microcontroller display drivers.

### Components
- Aseprite (for sprite design)
- Python script (for conversion)

### Instructional
1. **Design Your Sprite**:
   - Open Aseprite and create a new image (e.g., 16x16).
   - Use the right-hand toolbar to draw with pencil, eraser, fill, and select tools.
   - Export each frame or sprite as a `.png` file.

2. **Convert PNGs to C Arrays**:
   - Use Python to loop through a folder of `.png` files and extract byte data.
   - This script uses `Pillow` to read pixel values and format them into hex bytes.

![Python to C Array Conversion Code](/mnt/data/e0dc5b2e-3439-439b-be3d-3f6e290eb4cd.png)

```python
# Function to process all files in a folder
def process_images_in_folder(folder_path, output_csv, req_h, req_w):
    dims = req_h * req_w
    with open(output_csv, 'w') as csvfile:
        csvfile.write(f"const unsigned char idle[][{{dims}}] PROGMEM = {{\n")
    for filename in os.listdir(folder_path):
        if filename.endswith(".png"):
            image_path = os.path.join(folder_path, filename)
            read_image_and_save_to_csv(image_path, output_csv, req_h, req_w)
    with open(output_csv, 'a') as csvfile:
        csvfile.write("};")
