# PCTrack &#x1F345;
<br/>

> PCTrack: Accurate Object Tracking for Live Video Analytics on Resource-Constrained Edge Devices
> 
> [Xinyi Zhang](https://github.com/GrapeZ402), [Haoran Xu](https://scholar.google.com/citations?user=UOwYW7gAAAAJ&hl=en), [Chenyun Yu](https://scholar.google.com/citations?user=xlnfZAcAAAAJ&hl=en&oi=ao), [Guang Tan*](https://scholar.google.com/citations?user=JerZls4AAAAJ&hl=en&oi=ao)

## News
- [2023/11/24]: The paper is under review for ICDE-2024.
- [2023/11/17]: Trajectory prediction dataset release. 
- [2023/11/13]: Initial code. 
- [2023/11/11]: Demo release.

## Introduction
The task of live video analytics relies on real-time object tracking that typically involves computationally expensive deep learning techniques. In practice, it has become essential to process video data on edge devices deployed near the cameras. However, these edge devices often have very limited per-camera computing resources, leading to poor tracking accuracy. Our measurement study reveals three major factors contributing to the performance issue: outdated detection results, tracking error accumulation, and ignorance of new objects. To tackle these challenges, we introduce a novel approach called Predictive Object Tracking with Correction, or \ours. Our design incorporates three key innovations: 
(1) a ***Predictive Detection Propagation*** method that rapidly updates outdated object bounding boxes to match the current frame through a lightweight prediction model; 
(2) a ***Frame Difference Correction method*** that reduces tracking errors by regressing the bounding boxes based on frame difference information; and
(3) a ***New Object Detection method*** that captures newly appearing or disappearing objects by exploiting particular frame difference patterns. Experimental results show that our design significantly improves the tracking accuracy compared to state of the art methods, making it a promising solution for video analytics on practical edge devices.

## Method 

PCTrack Pipeline:

<p align='center'>
<img src="https://github.com/GrapeZ402/pctrack/blob/main/vendors/framework.png" width="720px">
</p>


## Getting Started
- [Installation](docs/install.md) 
- [Prepare Dataset](docs/data.md)
- [Train, Eval and Visualize](docs/run.md)

You can download our pretrained model for [3D semantic occupancy prediction](https://cloud.tsinghua.edu.cn/f/7b2887a8fe3f472c8566/?dl=1) and [3D scene reconstruction tasks](https://cloud.tsinghua.edu.cn/f/ca595f31c8bd4ec49cf7/?dl=1). The difference is whether use semantic labels to train the model. The models are trained on 8 RTX 3090s with about 2.5 days.  

## Try your own data
### Occupancy prediction
You can try our nuScenes [pretrained model](https://cloud.tsinghua.edu.cn/f/7b2887a8fe3f472c8566/?dl=1) on your own data!  Here we give a template in-the-wild [data](https://cloud.tsinghua.edu.cn/f/48bd4b3e88f64ed7b76b/?dl=1) and [pickle file](https://cloud.tsinghua.edu.cn/f/5c710efd78854c529705/?dl=1). You should place it in ./data and change the corresponding infos. Specifically, you need to change the 'lidar2img', 'intrinsic' and 'data_path' as the extrinsic matrix, intrinsic matrix and path of your multi-camera images. Note that the order of frames should be same to their timestamps. 'occ_path' in this pickle file indicates the save path and you will get raw results (.npy) and point coulds (.ply) in './visual_dir' for further visualization. You can use meshlab to directly visualize .ply files. Or you can run tools/visual.py to visualize .npy files. 
```
./tools/dist_inference.sh ./projects/configs/surroundocc/surroundocc_inference.py ./path/to/ckpts.pth 8
```

### Ground truth generation
You can also generate dense occupancy labels with your own data! We provide a highly extensible code to achieve [this](https://github.com/weiyithu/SurroundOcc/blob/main/tools/generate_occupancy_with_own_data/process_your_own_data.py). We provide an example [sequence](https://cloud.tsinghua.edu.cn/f/94fea6c8be4448168667/?dl=1) and you need to prepare your data like this:

```
your_own_data_folder/
├── pc/
│   ├── pc0.npy
│   ├── pc1.npy
│   ├── ...
├── bbox/
│   ├── bbox0.npy (bounding box of the object)
│   ├── bbox1.npy
│   ├── ...
│   ├── object_category0.npy (semantic category of the object)
│   ├── object_category1.npy
│   ├── ...
│   ├── boxes_token0.npy (Unique bbox codes used to combine the same object in different frames)
│   ├── boxes_token1.npy
│   ├── ...
├── calib/
│   ├── lidar_calibrated_sensor0.npy
│   ├── lidar_calibrated_sensor1.npy
│   ├── ...
├── pose/
│   ├── lidar_ego_pose0.npy
│   ├── lidar_ego_pose1.npy
│   ├── ...
```
You can generate occupancy labels with or without semantics (via acitivating --with semantic). If your LiDAR is high-resolution, e.g. RS128, LiVOX and M1, you can skip Poisson reconstruction step and the generation processe will be very fast! You can change the point cloud range and voxel size in config.yaml. You can use multithreading to boost the generation process.
```
cd $Home/tools/generate_occupancy_nuscenes
python process_your_own_data.py --to_mesh --with_semantic --data_path $your_own_data_folder$ --len_sequence $frame number$
```
You can use --whole_scene_to_mesh to generate a complete static scene with all frames at one time, then add the moving object point cloud, and finally divide it into small scenes. In this way, we can accelerate the generation process and get denser but more uneven occupancy labels. 

## Acknowledgement
Many thanks to these excellent projects:
- [BEVFormer](https://github.com/fundamentalvision/BEVFormer)
- [MonoScene](https://github.com/astra-vision/MonoScene)

Related Projects:
- [TPVFormer](https://github.com/wzzheng/TPVFormer)
- [OpenOccupancy](https://github.com/JeffWang987/OpenOccupancy)

## Bibtex
If this work is helpful for your research, please consider citing the following BibTeX entry.

```
@article{wei2023surroundocc, 
      title={SurroundOcc: Multi-Camera 3D Occupancy Prediction for Autonomous Driving}, 
      author={Yi Wei and Linqing Zhao and Wenzhao Zheng and Zheng Zhu and Jie Zhou and Jiwen Lu},
      journal={arXiv preprint arXiv:2303.09551},
      year={2023}
}
```
