# Frame Interpolation for Android

An Android application for video frame interpolation using quantized deep learning models (IFRNet and RIFE) optimized for Qualcomm Snapdragon Neural Processing Engine (SNPE).

## Overview

This project provides an end-to-end solution for deploying frame interpolation models on Android devices with Qualcomm Snapdragon processors. It includes:

- **Android Application**: A mobile app that performs video frame interpolation using SNPE
- **Model Quantization Pipeline**: Tools to convert and optimize PyTorch models (IFRNet and RIFE) to INT8 precision for efficient on-device inference
- **SNPE Integration**: Leverages Qualcomm's SNPE SDK for accelerated inference on DSP, GPU, or CPU

Frame interpolation creates smooth slow-motion effects by generating intermediate frames between existing video frames, effectively increasing the frame rate (e.g., 30fps → 60fps).

## Features

- **Dual Model Support**: Choose between IFRNet-Small and RIFE Lite models
- **Hardware Acceleration**: Runtime selection between DSP, GPU, and CPU
- **Efficient Processing**: Optimized for mobile performance with INT8 quantization
- **Video Playback**: Side-by-side comparison of original and interpolated videos
- **Easy-to-use Interface**: Simple spinner-based video selection and runtime configuration

## Project Structure

```
FrameInterpolation/
├── frameinterpolation/           # Android application module
│   ├── src/main/
│   │   ├── java/                 # Java source code
│   │   ├── assets/               # Model files (.dlc) and test videos
│   │   └── res/                  # Android resources
│   └── build.gradle              # App-level build configuration
├── snpe-release/                 # SNPE SDK library (AAR)
├── Generate_Model/               # Model preparation and quantization tools
│   ├── quantize_ifrnet_s.py      # IFRNet quantization script
│   ├── quantize_rife_lite.py     # RIFE quantization script
│   ├── model_prep.ipynb          # Jupyter notebook for model pipeline
│   ├── ifrnet/                   # IFRNet source code and checkpoints
│   ├── rife/                     # RIFE source code and checkpoints
│   ├── frames/calib/             # Calibration dataset for quantization
│   └── artifacts/                # Output directory for quantized models
├── build.gradle                  # Project-level build configuration
└── README.md                     # This file
```

## Prerequisites

### For Android Development

- **Android Studio**: Arctic Fox or later
- **Android SDK**: API Level 25+ (Android 7.1.1 or higher)
- **NDK**: Version 21.4.7075529
- **Gradle**: 8.13.0+
- **Java**: JDK 11

### For Model Quantization

- **Python**: 3.10
- **PyTorch**: 2.9.1
- **ONNX**: 1.17.0
- **AIMET**: AI Model Efficiency Toolkit
- **OpenCV**: For image/video processing
- **NumPy**: For numerical operations
- **Protobuf**: 3.20.2

## Getting Started

### 1. Clone the Repository

```bash
git clone https://github.com/yourusername/FrameInterpolation.git
cd FrameInterpolation
```

### 2. Set Up SNPE SDK

The project uses SNPE release AAR library in the `snpe-release/` directory.

### 3. Build and Run the Android App

1. Open the project in Android Studio
2. Connect an Android device with a Qualcomm Snapdragon processor (e.g. SM8650)
3. Select the `frameinterpolation` run configuration
4. Click **Run** (Shift+F10)

### 4. Using the Application

1. **Select a Video**: Use the dropdown spinner to choose a video from the assets folder
2. **Choose Runtime**: Select DSP (recommended), GPU, or CPU for inference
3. **Process Video**: Model generates interpolated frames
4. **Sync Playback**: Use the sync button to play both videos simultaneously for comparison

## Model Preparation

To quantize and prepare models for deployment, see the detailed instructions in [`Generate_Model/README.md`](Generate_Model/README.md).

### Quick Start

1. **Prepare Calibration Data**: Place frame pairs in `Generate_Model/frames/calib/`
2. **Download Pre-trained Models**:
   - IFRNet: [IFRNet GitHub](https://github.com/ltkong218/IFRNet)
   - RIFE: [Practical-RIFE GitHub](https://github.com/hzwer/Practical-RIFE)
3. **Run Quantization**:
   ```bash
   cd Generate_Model
   python quantize_ifrnet_s.py
   # or
   python quantize_rife_lite.py
   ```
4. **Convert to SNPE Format**: Use SNPE tools to convert ONNX models to DLC format
5. **Deploy**: Copy `.dlc` files to `frameinterpolation/src/main/assets/`

## Supported Models

### IFRNet-Small
- **Paper**: [IFRNet: Intermediate Feature Refine Network for Efficient Frame Interpolation (CVPR 2022)](https://arxiv.org/abs/2205.14620)
- **Input Size**: 720×1280 (H×W)
- **Outputs**: 3-channel RGB interpolated frame
- **Advantages**: Fast inference, compact model size, state-of-the-art accuracy

### RIFE Lite (v4.25)
- **Paper**: [Real-Time Intermediate Flow Estimation for Video Frame Interpolation](https://github.com/hzwer/Practical-RIFE)
- **Input Size**: 720×1280 (H×W)
- **Outputs**: 3-channel RGB interpolated frame
- **Advantages**: Excellent for animation and post-processing, actively maintained

## Results
- Summary: IFRNet-Small is faster and provides better image quality than RIFE Lite on our test hardware.
- Benchmark (average inference per frame, input size 720×1280, DSP):
  - IFRNet-Small: ~400 ms/frame
  - RIFE Lite: ~600 ms/frame
- Test conditions and notes:
  - Measurements are approximate and were observed on Snapdragon 8 Gen 3
  - Image quality comparison is based on visual inspection of interpolated frames: IFRNet-Small produced fewer artifacts and crisper details in our tests.

## Hardware Requirements

### Recommended
- **Device**: Tested on Snapdragon 8 Gen 3 (SM8650)
- **Android Version**: 7.1.1 (API 25) or higher

### Runtime Performance
- **DSP**: Fastest, lowest power consumption (recommended)
- **GPU**: Good balance of speed and compatibility
- **CPU**: Fallback option, slower but universally compatible

## Application Architecture

The app is structured around several key components:

- **`SNPEActivity`**: Main activity handling UI and orchestration
- **`FrameInterpolation`**: Core inference engine that interfaces with SNPE
- **`VideoFrameExtractor`**: Extracts frames from input videos
- **`VideoComposer`**: Combines interpolated frames into output video
- **`PrePostProcess`**: Handles image preprocessing and postprocessing
- **`ModelConstants`**: Defines model-specific configurations (input/output dimensions, etc.)

## Performance Tips

1. **Use DSP Runtime**: For best performance
2. **Optimize Video Resolution**: Lower resolution videos process faster
3. **Pre-process Videos**: Ensure input videos are properly formatted
4. **Cache Management**: The app automatically cleans up temporary files

## Troubleshooting

### Model Loading Fails
- Ensure `.dlc` files are in the `assets` folder
- Verify SNPE library is properly included

### Slow Inference
- Try DSP runtime instead of CPU
- Reduce input video resolution

### Build Errors
- Verify NDK version matches `build.gradle`
- Clean and rebuild project
- Check Gradle sync completed successfully

## License

This project contains code and models from multiple sources:

- **IFRNet**: Original PyTorch implementation (see `Generate_Model/ifrnet/LICENSE`)
- **RIFE**: Practical-RIFE implementation (see `Generate_Model/rife/LICENSE`)
- **Android Application**: BSD-3-Clause-Clear (Copyright © 2024 Qualcomm Innovation Center, Inc.)

See individual license files for detailed terms.

## Citation

The original papers:

### IFRNet
```bibtex
@inproceedings{kong2022ifrnet,
  title={IFRNet: Intermediate Feature Refine Network for Efficient Frame Interpolation},
  author={Kong, Lingtong and Jiang, Boyuan and Luo, Donghao and Chu, Wenqing and Huang, Xiaoming and Tai, Ying and Wang, Chengjie and Yang, Jie},
  booktitle={Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition},
  year={2022}
}
```

### RIFE
```bibtex
@inproceedings{huang2020rife,
  title={RIFE: Real-Time Intermediate Flow Estimation for Video Frame Interpolation},
  author={Huang, Zhewei and Zhang, Tianyuan and Heng, Wen and Shi, Boxin and Zhou, Shuchang},
  booktitle={arXiv preprint arXiv:2011.06294},
  year={2020}
}
```

## Acknowledgments

- **Qualcomm SNPE Team**: For the Neural Processing Engine SDK
- **IFRNet Authors**: For the excellent frame interpolation research and code
- **RIFE Authors**: For the practical and efficient RIFE implementation
- **AIMET Team**: For the quantization toolkit

## Future Work

- Curate and expand calibration dataset for quantization
- Add interpolated frame image quality metrics
- Model architecture optimizations

**Note**: This project is designed for research purposes. Performance may vary based on device capabilities and model selection.