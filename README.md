# VRS_Pre_Processing

A comprehensive toolkit for preprocessing Project Aria VRS (Visual Recording System) files, extracting videos and thumbnails, and overlaying manual annotations for research purposes.

## Overview

This project provides tools to work with Project Aria data, specifically designed for extracting and processing VRS files into usable video formats with optional image corrections and annotation overlays. It's particularly useful for researchers working with Project Aria datasets in healthcare, computer vision, and AR/VR applications.

## Prerequisites

### 1. Install Project Aria Tools

**You need to download and install projectaria_tools yourself from the official repository:**

```bash
git clone https://github.com/facebookresearch/projectaria_tools.git
cd projectaria_tools
# Follow the installation instructions in their README
```

**Place the projectaria_tools folder in the root directory of this project (./)**

### 2. Python Dependencies

Install the required Python packages:

```bash
pip install opencv-python numpy tqdm
```

## Project Structure

```
VRS_Pre_Processing/
├── projectaria_tools/          # Project Aria Tools (download separately)
├── scripts/                    # Processing scripts
│   ├── video_extractor.py     # Extract videos from VRS files
│   ├── overlap_label_to_video.py  # Overlay annotations on videos
│   └── rotate_medias.py       # Rotate videos and images
├── data/                       # Data directory (create as needed)
│   └── VRS/                   # VRS files directory
└── README.md                   # This file
```

## Usage

### 1. Extract Video (.mp4) & Thumbnails (.png) from Aria Files (.vrs)

The `video_extractor.py` script processes VRS files to extract RGB video streams with optional image corrections:

```bash
# Process all VRS files in the default directory
python scripts/video_extractor.py

# Process a specific VRS file
python scripts/video_extractor.py --vrs_file "yy_2.vrs"
```

**Features:**
- **Lens undistortion**: Corrects camera lens distortion
- **Color correction**: Applies color calibration
- **Devignetting**: Removes vignetting effects
- **Configurable output**: Adjustable resolution and quality settings

**Output Structure:**
```
data/VRS/non_patients_vrs/extracted_videos/
└── undistort_True_colorcorrect_True_devignette_True/
    ├── [vrs_filename]/
    │   ├── [vrs_filename].vrs.mp4
    │   ├── [vrs_filename].vrs.png (thumbnail)
    │   └── metadata.json
    └── ...
```

### 2. Manual Video Labeling with ELAN

Use [ELAN](https://archive.mpi.nl/tla/elan) (EUDICO Linguistic Annotator) to manually label your extracted videos:

1. Open ELAN and create a new project
2. Import your extracted MP4 files
3. Create annotation tiers for your research needs
4. Export annotations as CSV format (semicolon-separated)

**Required CSV Format:**
```csv
#starttime;#endtime;all_tiers
0.0;5.2;walking
5.2;12.8;sitting
12.8;18.5;standing
```

### 3. Overlap Labels onto Videos

The `overlap_label_to_video.py` script overlays your manual annotations onto the videos:

#### 3.1 Modify Paths
Edit the configuration section in `scripts/overlap_label_to_video.py`:

```python
# === CONFIGURATION ===
base_path = "/path/to/your/extracted_videos/folder"
video_path = f'{base_path}/your_video.mp4'
csv_path = f'{base_path}/your_annotations.csv'
output_path = f'{base_path}/labeled_output.mp4'
```

#### 3.2 Run the Script
```bash
python scripts/overlap_label_to_video.py
```

**Output:** A new video file with text overlays showing the current annotation at each frame.

### 4. Rotate Media Files (Optional)

If your videos need rotation correction, use the `rotate_medias.py` script:

```bash
python scripts/rotate_medias.py
```

**Note:** Update the `ROOT_DIR` variable in the script to point to your extracted videos directory.

## Configuration Options

### Video Extractor Options

- `--undistort`: Apply lens undistortion (default: True)
- `--color_correct`: Apply color correction (default: True)  
- `--devignette`: Apply devignetting (default: True)
- `--debug`: Process only the smallest VRS file (default: False)
- `--vrs_file`: Specify a single VRS file to process

### Output Resolution

The default output resolution is 1048x1048 pixels. You can modify this in `video_extractor.py`:

```python
dst_calib = calibration.get_linear_camera_calibration(1048, 1048, 300, sensor_label)
```

## Data Organization

Organize your VRS files in the following structure:

```
data/
└── VRS/
    ├── non_patients_vrs/      # Control group VRS files
    ├── patients_vrs/          # Patient group VRS files
    └── extracted_videos/      # Output directory (auto-created)
```

## Troubleshooting

### Common Issues

1. **Import Error**: Ensure projectaria_tools is properly installed and in the correct location
2. **VRS File Not Found**: Check the `VRS_ROOT` path in `video_extractor.py`
3. **Memory Issues**: Process VRS files individually using the `--vrs_file` flag
4. **Annotation Overlay Issues**: Verify CSV format and file paths in `overlap_label_to_video.py`

### Performance Tips

- Use `--debug` flag for testing with small files
- Process VRS files individually for large datasets
- Ensure sufficient disk space for video outputs

## Contributing

Feel free to submit issues, feature requests, or pull requests to improve this toolkit.

## License

This project is open source. Please ensure compliance with Project Aria Tools licensing when using their components.

## References

- [Project Aria Tools](https://github.com/facebookresearch/projectaria_tools) - Official toolkit for Project Aria data
- [ELAN](https://archive.mpi.nl/tla/elan) - Video annotation software
- [Project Aria](https://about.meta.com/realitylabs/projectaria/) - Meta's research platform
