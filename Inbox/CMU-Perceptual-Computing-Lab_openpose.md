CMU-Perceptual-Computing-Lab/openpose

Forget about the OpenPose library code, just compile the library and use the demo `./build/examples/openpose/openpose.bin`.

In order to learn how to use it, run `./build/examples/openpose/openpose.bin --help` in your favorite command-line interface tool and read all the available flags (check only the flags for `examples/openpose/openpose.cpp` itself, i.e., the section `Flags from examples/openpose/openpose.cpp:`). We detail some of them in the following sections.

In Ubuntu, Mac, and other Unix systems, use any command-line interface, such as `Terminal` or `Terminator`. In Windows, open the `PowerShell` (recommended) or Windows Command Prompt (CMD). They can be open by pressing the Windows button + X, and then A. Feel free to watch any Youtube video tutorial if you are not familiar with these non-GUI tools. Make sure that you are in the **root directory of the project** (i.e., in the OpenPose folder, not inside `build/` nor `windows/` nor `bin/`).

## <a id="user-content-running-on-images-video-or-webcam"></a>[](#running-on-images-video-or-webcam)Running on Images, Video or Webcam

See [doc/quick_start.md#quick-start](https://github.com/CMU-Perceptual-Computing-Lab/openpose/blob/master/doc/quick_start.md#quick-start).

## <a id="user-content-pose--face--hands"></a>[](#pose--face--hands)Pose + Face + Hands

See [doc/quick_start.md#quick-start](https://github.com/CMU-Perceptual-Computing-Lab/openpose/blob/master/doc/quick_start.md#quick-start).

## <a id="user-content-maximum-accuracy-configuration"></a>[](#maximum-accuracy-configuration)Maximum Accuracy Configuration

See [doc/quick_start.md#maximum-accuracy-configuration](https://github.com/CMU-Perceptual-Computing-Lab/openpose/blob/master/doc/quick_start.md#maximum-accuracy-configuration).

## <a id="user-content-reducing-latencylag"></a>[](#reducing-latencylag)Reducing Latency/Lag

In general, there are 3 ways to reduce the latency (with some drawbacks each one):

- Reducing `--output_resolution`: It will slightly reduce the latency and increase the FPS. But the quality of the displayed image will deteriorate.
- Reducing `--net_resolution` and/or `--face_net_resolution` and/or `--hand_net_resolution`: It will increase the FPS and reduce the latency. But the accuracy will drop, specially for small people in the image. Note: For maximum accuracy, follow [doc/quick_start.md#maximum-accuracy-configuration](https://github.com/CMU-Perceptual-Computing-Lab/openpose/blob/master/doc/quick_start.md#maximum-accuracy-configuration).
- Enabling `--disable_multi_thread`: The latency should be reduced. But the speed will drop to 1-GPU speed (as it will only use 1 GPU). Note that it's practical only for body, if hands and face are also extracted, it's usually not worth it.

## <a id="user-content-kinect-20-as-webcam-on-windows-10"></a>[](#kinect-20-as-webcam-on-windows-10)Kinect 2.0 as Webcam on Windows 10

Since the Windows 10 Anniversary, Kinect 2.0 can be read as a normal webcam. All you need to do is go to `device manager`, expand the `kinect sensor devices` tab, right click and update driver of `WDF kinectSensor Interface`. If you already have another webcam, disconnect it or use `--camera 2`.

## <a id="user-content-json-output-with-no-visualization"></a>[](#json-output-with-no-visualization)JSON Output with No Visualization

The following example runs the demo video `video.avi` and outputs JSON files in `output/`. Note: see [doc/output.md](https://github.com/CMU-Perceptual-Computing-Lab/openpose/blob/master/doc/output.md) to understand the format of the JSON files.

```
# Only body
./build/examples/openpose/openpose.bin --video examples/media/video.avi --write_json output/ --display 0 --render_pose 0
# Body + face + hands
./build/examples/openpose/openpose.bin --video examples/media/video.avi --write_json output/ --display 0 --render_pose 0 --face --hand

```

## <a id="user-content-json-output--rendered-images-saving"></a>[](#json-output--rendered-images-saving)JSON Output + Rendered Images Saving

The following example runs the demo video `video.avi`, renders image frames on `output/result.avi`, and outputs JSON files in `output/`. Note: see [doc/output.md](https://github.com/CMU-Perceptual-Computing-Lab/openpose/blob/master/doc/output.md) to understand the format of the JSON files.

```
./build/examples/openpose/openpose.bin --video examples/media/video.avi --write_video output/result.avi --write_json output/

```

## <a id="user-content-hands"></a>[](#hands)Hands

```
# Fast method for speed
./build/examples/openpose/openpose.bin --hand
# Best results found with 6 scales
./build/examples/openpose/openpose.bin --hand --hand_scale_number 6 --hand_scale_range 0.4
# Adding tracking to Webcam (if FPS per GPU > 10 FPS) and Video
./build/examples/openpose/openpose.bin --video examples/media/video.avi --hand --hand_detector 3
# Multi-scale + tracking is also possible
./build/examples/openpose/openpose.bin --video examples/media/video.avi --hand --hand_scale_number 6 --hand_scale_range 0.4 --hand_detector 3

```

## <a id="user-content-rendering-face-and-hands-without-pose"></a>[](#rendering-face-and-hands-without-pose)Rendering Face and Hands without Pose

```
# CPU rendering (faster)
./build/examples/openpose/openpose.bin --render_pose 0 --face --face_render 1 --hand --hand_render 1
# GPU rendering
./build/examples/openpose/openpose.bin --render_pose 0 --face --face_render 2 --hand --hand_render 2

```

## <a id="user-content-debugging-information"></a>[](#debugging-information)Debugging Information

```
# Basic information
./build/examples/openpose/openpose.bin --logging_level 3
# Showing all messages
./build/examples/openpose/openpose.bin --logging_level 0

```

## <a id="user-content-selecting-some-gpus"></a>[](#selecting-some-gpus)Selecting Some GPUs

The following example runs the demo video `video.avi`, parallelizes it over 2 GPUs, GPUs 1 and 2 (note that it will skip GPU 0):

```
./build/examples/openpose/openpose.bin --video examples/media/video.avi --num_gpu 2 --num_gpu_start 1

```

## <a id="user-content-heat-maps-storing"></a>[](#heat-maps-storing)Heat Maps Storing

The following command will save all the body part heat maps, background heat map and Part Affinity Fields (PAFs) in the folder `output_heatmaps_folder`. It will save them on PNG format. Instead of individually saving each of the 67 heatmaps (18 body parts + background + 2 x 19 PAFs) individually, the library concatenate them vertically into a huge (width x #heatmaps) x (height) matrix. The PAFs channels are multiplied by 2 because there is one heatmpa for the x-coordinates and one for the y-coordinates. The order is body parts + bkg + PAFs. It will follow the sequence on POSE\_BODY\_PART_MAPPING in [include/openpose/pose/poseParameters.hpp](https://github.com/CMU-Perceptual-Computing-Lab/openpose/blob/master/include/openpose/pose/poseParameters.hpp).

```
./build/examples/openpose/openpose.bin --video examples/media/video.avi --heatmaps_add_parts --heatmaps_add_bkg --heatmaps_add_PAFs --write_heatmaps output_heatmaps_folder/

```


## <a id="user-content-main-flags"></a>[](#main-flags)Main Flags

We enumerate some of the most important flags, check the `Flags Detailed Description` section or run `./build/examples/openpose/openpose.bin --help` for a full description of all of them.

- `--face`: Enables face keypoint detection.
- `--hand`: Enables hand keypoint detection.
- `--video input.mp4`: Read video.
- `--camera 3`: Read webcam number 3.
- `--image_dir path_to_images/`: Run on a folder with images.
- `--ip_camera http://iris.not.iac.es/axis-cgi/mjpg/video.cgi?resolution=320x240?x.mjpeg`: Run on a streamed IP camera. See examples public IP cameras [here](http://www.webcamxp.com/publicipcams.aspx).
- `--write_video path.avi`: Save processed images as video.
- `--write_images folder_path`: Save processed images on a folder.
- `--write_keypoint path/`: Output JSON, XML or YML files with the people pose data on a folder.
- `--process_real_time`: For video, it might skip frames to display at real time.
- `--disable_blending`: If enabled, it will render the results (keypoint skeletons or heatmaps) on a black background, not showing the original image. Related: `part_to_show`, `alpha_pose`, and `alpha_pose`.
- `--part_to_show`: Prediction channel to visualize.
- `--display 0`: Display window not opened. Useful for servers and/or to slightly speed up OpenPose.
- `--num_gpu 2 --num_gpu_start 1`: Parallelize over this number of GPUs starting by the desired device id. By default it uses all the available GPUs.
- `--model_pose MPI`: Model to use, affects number keypoints, speed and accuracy.
- `--logging_level 3`: Logging messages threshold, range \[0,255\]: 0 will output any message & 255 will output none. Current messages in the range \[1-4\], 1 for low priority messages and 4 for important ones.

## <a id="user-content-flag-description"></a>[](#flag-description)Flag Description

Each flag is divided into flag name, default value, and description.

1.  Debugging/Other

- DEFINE\_int32(logging\_level, 3, "The logging level. Integer in the range \[0, 255\]. 0 will output any opLog() message, while 255 will not output any. Current OpenPose library messages are in the range 0-4: 1 for low priority messages and 4 for important ones.");
- DEFINE\_bool(disable\_multi_thread, false, "It would slightly reduce the frame rate in order to highly reduce the lag. Mainly useful for 1) Cases where it is needed a low latency (e.g., webcam in real-time scenarios with low-range GPU devices); and 2) Debugging OpenPose when it is crashing to locate the error.");
- DEFINE\_int32(profile\_speed, 1000, "If PROFILER_ENABLED was set in CMake or Makefile.config files, OpenPose will show some runtime statistics at this frame number.");

2.  Producer

- DEFINE_int32(camera, -1, "The camera index for cv::VideoCapture. Integer in the range \[0, 9\]. Select a negative number (by default), to auto-detect and open the first available camera.");
- DEFINE\_string(camera\_resolution, "-1x-1", "Set the camera resolution (either `--camera` or `--flir_camera`). `-1x-1` will use the default 1280x720 for `--camera`, or the maximum flir camera resolution available for `--flir_camera`");
- DEFINE_string(video, "", "Use a video file instead of the camera. Use `examples/media/video.avi` for our default example video.");
- DEFINE\_string(image\_dir, "", "Process a directory of images. Use `examples/media/` for our default example folder with 20 images. Read all standard formats (jpg, png, bmp, etc.).");
- DEFINE\_bool(flir\_camera, false, "Whether to use FLIR (Point-Grey) stereo camera.");
- DEFINE\_int32(flir\_camera_index, -1, "Select -1 (default) to run on all detected flir cameras at once. Otherwise, select the flir camera index to run, where 0 corresponds to the detected flir camera with the lowest serial number, and `n` to the `n`-th lowest serial number camera.");
- DEFINE\_string(ip\_camera, "", "String with the IP camera URL. It supports protocols like RTSP and HTTP.");
- DEFINE\_uint64(frame\_first, 0, "Start on desired frame number. Indexes are 0-based, i.e., the first frame has index 0.");
- DEFINE\_uint64(frame\_step, 1, "Step or gap between processed frames. E.g., `--frame_step 5` would read and process frames 0, 5, 10, etc..");
- DEFINE\_uint64(frame\_last, -1, "Finish on desired frame number. Select -1 to disable. Indexes are 0-based, e.g., if set to 10, it will process 11 frames (0-10).");
- DEFINE\_bool(frame\_flip, false, "Flip/mirror each frame (e.g., for real time webcam demonstrations).");
- DEFINE\_int32(frame\_rotate, 0, "Rotate each frame, 4 possible values: 0, 90, 180, 270.");
- DEFINE\_bool(frames\_repeat, false, "Repeat frames when finished.");
- DEFINE\_bool(process\_real_time, false, "Enable to keep the original source frame rate (e.g., for video). If the processing time is too long, it will skip frames. If it is too fast, it will slow it down.");
- DEFINE\_string(camera\_parameter_path, "models/cameraParameters/flir", "String with the folder where the camera parameters are located. If there is only 1 XML file (for single video, webcam, or images from the same camera), you must specify the whole XML file path (ending in .xml).");
- DEFINE\_bool(frame\_undistort, false, "If false (default), it will not undistort the image, if true, it will undistortionate them based on the camera parameters found in `camera_parameter_path`");

3.  OpenPose

- DEFINE\_string(model\_folder, "models/", "Folder path (absolute or relative) where the models (pose, face, ...) are located.");
- DEFINE\_string(prototxt\_path, "", "The combination `--model_folder` \+ `--prototxt_path` represents the whole path to the prototxt file. If empty, it will use the default OpenPose ProtoTxt file.");
- DEFINE\_string(caffemodel\_path, "", "The combination `--model_folder` \+ `--caffemodel_path` represents the whole path to the caffemodel file. If empty, it will use the default OpenPose CaffeModel file.");
- DEFINE\_string(output\_resolution, "-1x-1", "The image resolution (display and output). Use "-1x-1" to force the program to use the input image resolution.");
- DEFINE\_int32(num\_gpu, -1, "The number of GPU devices to use. If negative, it will use all the available GPUs in your machine.");
- DEFINE\_int32(num\_gpu_start, 0, "GPU device start number.");
- DEFINE\_int32(keypoint\_scale, 0, "Scaling of the (x,y) coordinates of the final pose data array, i.e., the scale of the (x,y) coordinates that will be saved with the `write_json` & `write_keypoint` flags. Select `0` to scale it to the original source resolution; `1`to scale it to the net output size (set with `net_resolution`); `2` to scale it to the final output size (set with `resolution`); `3` to scale it in the range \[0,1\], where (0,0) would be the top-left corner of the image, and (1,1) the bottom-right one; and 4 for range \[-1,1\], where (-1,-1) would be the top-left corner of the image, and (1,1) the bottom-right one. Non related with `scale_number` and `scale_gap`.");
- DEFINE\_int32(number\_people_max, -1, "This parameter will limit the maximum number of people detected, by keeping the people with top scores. The score is based in person area over the image, body part score, as well as joint score (between each pair of connected body parts). Useful if you know the exact number of people in the scene, so it can remove false positives (if all the people have been detected. However, it might also include false negatives by removing very small or highly occluded people. -1 will keep them all.");
- DEFINE\_bool(maximize\_positives, false, "It reduces the thresholds to accept a person candidate. It highly increases both false and true positives. I.e., it maximizes average recall but could harm average precision.");
- DEFINE\_double(fps\_max, -1., "Maximum processing frame rate. By default (-1), OpenPose will process frames as fast as possible. Example usage: If OpenPose is displaying images too quickly, this can reduce the speed so the user can analyze better each frame from the GUI.");

4.  OpenPose Body Pose

- DEFINE_int32(body, 1, "Select 0 to disable body keypoint detection (e.g., for faster but less accurate face keypoint detection, custom hand detector, etc.), 1 (default) for body keypoint estimation, and 2 to disable its internal body pose estimation network but still still run the greedy association parsing algorithm");
- DEFINE\_string(model\_pose, "BODY_25", "Model to be used. E.g., `BODY_25` (fastest for CUDA version, most accurate, and includes foot keypoints), `COCO` (18 keypoints), `MPI` (15 keypoints, least accurate model but fastest on CPU), `MPI_4_layers` (15 keypoints, even faster but less accurate).");
- DEFINE\_string(net\_resolution, "-1x368", "Multiples of 16. If it is increased, the accuracy potentially increases. If it is decreased, the speed increases. For maximum speed-accuracy balance, it should keep the closest aspect ratio possible to the images or videos to be processed. Using `-1` in any of the dimensions, OP will choose the optimal aspect ratio depending on the user's input value. E.g., the default `-1x368` is equivalent to `656x368` in 16:9 resolutions, e.g., full HD (1980x1080) and HD (1280x720) resolutions.");
- DEFINE\_int32(scale\_number, 1, "Number of scales to average.");
- DEFINE\_double(scale\_gap, 0.25, "Scale gap between scales. No effect unless scale_number > 1. Initial scale is always 1. If you want to change the initial scale, you actually want to multiply the `net_resolution` by your desired initial scale.");
- DEFINE\_double(upsampling\_ratio, 0., "Upsampling ratio between the `net_resolution` and the output net results. A value less or equal than 0 (default) will use the network default value (recommended).");

5.  OpenPose Body Pose Heatmaps and Part Candidates

- DEFINE\_bool(heatmaps\_add_parts, false, "If true, it will fill op::Datum::poseHeatMaps array with the body part heatmaps, and analogously face & hand heatmaps to op::Datum::faceHeatMaps & op::Datum::handHeatMaps. If more than one `add_heatmaps_X` flag is enabled, it will place then in sequential memory order: body parts + bkg + PAFs. It will follow the order on POSE\_BODY\_PART_MAPPING in `src/openpose/pose/poseParameters.cpp`. Program speed will considerably decrease. Not required for OpenPose, enable it only if you intend to explicitly use this information later.");
- DEFINE\_bool(heatmaps\_add_bkg, false, "Same functionality as `add_heatmaps_parts`, but adding the heatmap corresponding to background.");
- DEFINE\_bool(heatmaps\_add_PAFs, false, "Same functionality as `add_heatmaps_parts`, but adding the PAFs.");
- DEFINE\_int32(heatmaps\_scale, 2, "Set 0 to scale op::Datum::poseHeatMaps in the range \[-1,1\], 1 for \[0,1\]; 2 for integer rounded \[0,255\]; and 3 for no scaling.");
- DEFINE\_bool(part\_candidates, false, "Also enable `write_json` in order to save this information. If true, it will fill the op::Datum::poseCandidates array with the body part candidates. Candidates refer to all the detected body parts, before being assembled into people. Note that the number of candidates is equal or higher than the number of final body parts (i.e., after being assembled into people). The empty body parts are filled with 0s. Program speed will slightly decrease. Not required for OpenPose, enable it only if you intend to explicitly use this information.");

6.  OpenPose Face

- DEFINE_bool(face, false, "Enables face keypoint detection. It will share some parameters from the body pose, e.g. `model_folder`. Note that this will considerable slow down the performance and increse the required GPU memory. In addition, the greater number of people on the image, the slower OpenPose will be.");
- DEFINE\_int32(face\_detector, 0, "Kind of face rectangle detector. Select 0 (default) to select OpenPose body detector (most accurate one and fastest one if body is enabled), 1 to select OpenCV face detector (not implemented for hands), 2 to indicate that it will be provided by the user, or 3 to also apply hand tracking (only for hand). Hand tracking might improve hand keypoint detection for webcam (if the frame rate is high enough, i.e., >7 FPS per GPU) and video. This is not person ID tracking, it simply looks for hands in positions at which hands were located in previous frames, but it does not guarantee the same person ID among frames.");
- DEFINE\_string(face\_net_resolution, "368x368", "Multiples of 16 and squared. Analogous to `net_resolution` but applied to the face keypoint detector. 320x320 usually works fine while giving a substantial speed up when multiple faces on the image.");

7.  OpenPose Hand

- DEFINE_bool(hand, false, "Enables hand keypoint detection. It will share some parameters from the body pose, e.g. `model_folder`. Analogously to `--face`, it will also slow down the performance, increase the required GPU memory and its speed depends on the number of people.");
- DEFINE\_int32(hand\_detector, 0, "Kind of hand rectangle detector. Analogous to `--face_detector`.");
- DEFINE\_string(hand\_net_resolution, "368x368", "Multiples of 16 and squared. Analogous to `net_resolution` but applied to the hand keypoint detector.");
- DEFINE\_int32(hand\_scale_number, 1, "Analogous to `scale_number` but applied to the hand keypoint detector. Our best results were found with `hand_scale_number` = 6 and `hand_scale_range` = 0.4.");
- DEFINE\_double(hand\_scale_range, 0.4, "Analogous purpose than `scale_gap` but applied to the hand keypoint detector. Total range between smallest and biggest scale. The scales will be centered in ratio 1. E.g., if scaleRange = 0.4 and scalesNumber = 2, then there will be 2 scales, 0.8 and 1.2.");

8.  OpenPose 3-D Reconstruction

- DEFINE_bool(3d, false, "Running OpenPose 3-D reconstruction demo: 1) Reading from a stereo camera system. 2) Performing 3-D reconstruction from the multiple views. 3) Displaying 3-D reconstruction results. Note that it will only display 1 person. If multiple people is present, it will fail.");
- DEFINE\_int32(3d\_min_views, -1, "Minimum number of views required to reconstruct each keypoint. By default (-1), it will require max(2, min(4, #cameras-1)) cameras to see the keypoint in order to reconstruct it.");
- DEFINE\_int32(3d\_views, -1, "Complementary option for `--image_dir` or `--video`. OpenPose will read as many images per iteration, allowing tasks such as stereo camera processing (`--3d`). Note that `--camera_parameter_path` must be set. OpenPose must find as many `xml` files in the parameter folder as this number indicates.");

9.  Extra algorithms

- DEFINE_bool(identification, false, "Experimental, not available yet. Whether to enable people identification across frames.");
- DEFINE_int32(tracking, -1, "Experimental, not available yet. Whether to enable people tracking across frames. The value indicates the number of frames where tracking is run between each OpenPose keypoint detection. Select -1 (default) to disable it or 0 to run simultaneously OpenPose keypoint detector and tracking for potentially higher accurary than only OpenPose.");
- DEFINE\_int32(ik\_threads, 0, "Experimental, not available yet. Whether to enable inverse kinematics (IK) from 3-D keypoints to obtain 3-D joint angles. By default (0 threads), it is disabled. Increasing the number of threads will increase the speed but also the global system latency.");

10. OpenPose Rendering

- DEFINE\_int32(part\_to_show, 0, "Prediction channel to visualize: 0 (default) for all the body parts, 1 for the background heat map, 2 for the superposition of heatmaps, 3 for the superposition of PAFs, 4-(4+#keypoints) for each body part heat map, the following ones for each body part pair PAF.");
- DEFINE\_bool(disable\_blending, false, "If enabled, it will render the results (keypoint skeletons or heatmaps) on a black background, instead of being rendered into the original image. Related: `part_to_show`, `alpha_pose`, and `alpha_pose`.");

11. OpenPose Rendering Pose

- DEFINE\_double(render\_threshold, 0.05, "Only estimated keypoints whose score confidences are higher than this threshold will be rendered. Note: Rendered refers only to visual display in the OpenPose basic GUI, not in the saved results. Generally, a high threshold (> 0.5) will only render very clear body parts; while small thresholds (~0.1) will also output guessed and occluded keypoints, but also more false positives (i.e., wrong detections).");
- DEFINE\_int32(render\_pose, -1, "Set to 0 for no rendering, 1 for CPU rendering (slightly faster), and 2 for GPU rendering (slower but greater functionality, e.g., `alpha_X` flags). If -1, it will pick CPU if CPU_ONLY is enabled, or GPU if CUDA is enabled. If rendering is enabled, it will render both `outputData` and `cvOutputData` with the original image and desired body part to be shown (i.e., keypoints, heat maps or PAFs).");
- DEFINE\_double(alpha\_pose, 0.6, "Blending factor (range 0-1) for the body part rendering. 1 will show it completely, 0 will hide it. Only valid for GPU rendering.");
- DEFINE\_double(alpha\_heatmap, 0.7, "Blending factor (range 0-1) between heatmap and original frame. 1 will only show the heatmap, 0 will only show the frame. Only valid for GPU rendering.");

12. OpenPose Rendering Face

- DEFINE\_double(face\_render_threshold, 0.4, "Analogous to `render_threshold`, but applied to the face keypoints.");
- DEFINE\_int32(face\_render, -1, "Analogous to `render_pose` but applied to the face. Extra option: -1 to use the same configuration that `render_pose` is using.");
- DEFINE\_double(face\_alpha_pose, 0.6, "Analogous to `alpha_pose` but applied to face.");
- DEFINE\_double(face\_alpha_heatmap, 0.7, "Analogous to `alpha_heatmap` but applied to face.");

13. OpenPose Rendering Hand

- DEFINE\_double(hand\_render_threshold, 0.2, "Analogous to `render_threshold`, but applied to the hand keypoints.");
- DEFINE\_int32(hand\_render, -1, "Analogous to `render_pose` but applied to the hand. Extra option: -1 to use the same configuration that `render_pose` is using.");
- DEFINE\_double(hand\_alpha_pose, 0.6, "Analogous to `alpha_pose` but applied to hand.");
- DEFINE\_double(hand\_alpha_heatmap, 0.7, "Analogous to `alpha_heatmap` but applied to hand.");

14. Display

- DEFINE_bool(fullscreen, false, "Run in full-screen mode (press f during runtime to toggle).");
- DEFINE\_bool(no\_gui_verbose, false, "Do not write text on output images on GUI (e.g., number of current frame and people). It does not affect the pose rendering.");
- DEFINE_int32(display, -1, "Display mode: -1 for automatic selection; 0 for no display (useful if there is no X server and/or to slightly speed up the processing if visual output is not required); 2 for 2-D display; 3 for 3-D display (if `--3d` enabled); and 1 for both 2-D and 3-D display.");

15. Command Line Inteface Verbose

- DEFINE\_double(cli\_verbose, -1.f, "If -1, it will be disabled (default). If it is a positive integer number, it will print on the command line every `verbose` frames. If number in the range (0,1), it will print the progress every `verbose` times the total of frames.");

16. Result Saving

- DEFINE\_string(write\_images, "", "Directory to write rendered frames in `write_images_format` image format.");
- DEFINE\_string(write\_images_format, "png", "File extension and format for `write_images`, e.g., png, jpg or bmp. Check the OpenCV function cv::imwrite for all compatible extensions.");
- DEFINE\_string(write\_video, "", "Full file path to write rendered frames in motion JPEG video format. It might fail if the final path does not finish in `.avi`. It internally uses cv::VideoWriter. Flag `write_video_fps` controls FPS. Alternatively, the video extension can be `.mp4`, resulting in a file with a much smaller size and allowing `--write_video_with_audio`. However, that would require: 1) Ubuntu or Mac system, 2) FFmpeg library installed (`sudo apt-get install ffmpeg`), 3) the creation temporarily of a folder with the same file path than the final video (without the extension) to storage the intermediate frames that will later be used to generate the final MP4 video.");
- DEFINE\_double(write\_video\_fps, -1., "Frame rate for the recorded video. By default, it will try to get the input frames producer frame rate (e.g., input video or webcam frame rate). If the input frames producer does not have a set FPS (e.g., image\_dir or webcam if OpenCV not compiled with its support), set this value accordingly (e.g., to the frame rate displayed by the OpenPose GUI).");
- DEFINE\_bool(write\_video\_with\_audio, false, "If the input is video and the output is so too, it will save the video with audio. It requires the output video file path finishing in `.mp4` format (see `write_video` for details).");
- DEFINE\_string(write\_video_3d, "", "Analogous to `--write_video`, but applied to the 3D output.");
- DEFINE\_string(write\_video_adam, "", "Experimental, not available yet. Analogous to `--write_video`, but applied to Adam model.");
- DEFINE\_string(write\_json, "", "Directory to write OpenPose output in JSON format. It includes body, hand, and face pose keypoints (2-D and 3-D), as well as pose candidates (if `--part_candidates` enabled).");
- DEFINE\_string(write\_coco_json, "", "Full file path to write people pose data with JSON COCO validation format. If foot, face, hands, etc. JSON is also desired (`--write_coco_json_variants`), they are saved with different file name suffix.");
- DEFINE\_int32(write\_coco\_json\_variants, 1, "Add 1 for body, add 2 for foot, 4 for face, and/or 8 for hands. Use 0 to use all the possible candidates. E.g., 7 would mean body+foot+face COCO JSON.");
- DEFINE\_int32(write\_coco\_json\_variant, 0, "Currently, this option is experimental and only makes effect on car JSON generation. It selects the COCO variant for cocoJsonSaver.");
- DEFINE\_string(write\_heatmaps, "", "Directory to write body pose heatmaps in PNG format. At least 1 `add_heatmaps_X` flag must be enabled.");
- DEFINE\_string(write\_heatmaps_format, "png", "File extension and format for `write_heatmaps`, analogous to `write_images_format`. For lossless compression, recommended `png` for integer `heatmaps_scale` and `float` for floating values. See `doc/output.md` for more details.");
- DEFINE\_string(write\_keypoint, "", "(Deprecated, use `write_json`) Directory to write the people pose keypoint data. Set format with `write_keypoint_format`.");
- DEFINE\_string(write\_keypoint_format, "yml", "(Deprecated, use `write_json`) File extension and format for `write_keypoint`: json, xml, yaml & yml. Json not available for OpenCV < 3.0, use `write_json` instead.");

17. Result Saving - Extra Algorithms

- DEFINE\_string(write\_bvh, "", "Experimental, not available yet. E.g., `~/Desktop/mocapResult.bvh`.");

18. UDP Communication

- DEFINE\_string(udp\_host, "", "Experimental, not available yet. IP for UDP communication. E.g., `192.168.0.1`.");
- DEFINE\_string(udp\_port, "8051", "Experimental, not available yet. Port number for UDP communication.");