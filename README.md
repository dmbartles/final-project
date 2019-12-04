# Final Project
### E-17 Robotics and Artificial Intelligence
## Project
Create a NVIDIA Jetson Nano capable of intersection navigation to include stop sign detection, traffic light compliance across a moderately complex city 
## Team Virginia Members: 
* David Bartles 
* Kapil Parkhi
## Software and Developing Tools: 
* Github | Code Repository
* JetPack SDK | OS image, Libraries, APIs
* JupyterLab | Python
* Pytorch | Visual ML Library
* Torchvision
* Cuda Toolkit
* Sift
* balenaEtcher | 1.5.56
* Anaconda Navigator
* Ubuntu
* Numpy
* OpenCV
## Laptop/Desktop Setup:
* Windows 10 with Web Connection
## Robot:
* Waveshare JetBot AI Kit (NVIDIA Jetson Nano based)
* https://www.waveshare.com/product/robotics/jetbot.htm
## References:
* https://www.waveshare.com/wiki/JetBot_AI_Kit
* https://github.com/NVIDIA-AI-IOT/jetbot/blob/master/notebooks/basic_motion/basic_motion.ipynb
* https://github.com/NVIDIA-AI-IOT/jetbot/tree/master/notebooks/collision_avoidance
## Link to White Paper
* https://docs.google.com/document/d/1ualUyon_yFZ7DIpuCaVGY2wueg2gNCW-n8hHYbmSwiI/edit?usp=sharing
## Video Links
* Collision Detection Demo: https://youtu.be/FHMWaEPh8OI
* Sign Recognition Demo: https://youtu.be/wW9pXnQQlUw
* Lane Detection + Sign Recognition: TBD
## Link to Model Repository
Models are too large to store in Github repository
* https://drive.google.com/drive/folders/1lmA3aXEFzU0_A8EdcrEeKc5V_crltZWh?usp=sharing

## White Paper
### Part 0 - Robot Selection
We selected the NVIDIA Jetson Nano over the competitors after assessing several factors:
* The Waveshare kit is very complete and only required batteries to be completely assembled. The kit is able to complete our project goal without any additions
* The NVIDIA Jetbot has very robust documentation and has notebooks that covered remote control, lane keeping and sign detection. All of the notebooks were well commented and easily understood be novices
* The Jetbot has a relatively powerful onboard GPU that enables model building locally, which eliminates the need to migrate the data to a PC, build the model remotely and then migrate the model to the Jetbot.

### Part 1 - Setup 
Building the Robot and Initial Test
* We received the Waveshare JetBot AI Kit and inventoried the contents. During the assembly the WiFi adaptor cable head severed and required a spot weld to adhere to the WiFi board. 3.7v batteries were purchased separately and arrived several days after the kit. We used the balentaEtcher software to image the JetBot 0.3.2 to the SD card. The Waveshare JetBot wiki page on Github was used to prepare the software environment.
Several issues were encountered:
* The system logs would fill up the entire disk space and prevent Unbuntu from booting properly due to camera initialization
* Installed incorrect version of JetBot image which prevented internet connectivity
* The robot would power off unexpectedly when trying to start the camera. This was due to a bad 3.7v battery
* The camera would not initialize in JupyterLab correctly as the camera cable ribbon was installed backwards
* The camera view was not present outside of the JupyterLab
* All issues were resolved
 
Development Environment Setup
We each have a laptop as well as a desktop with a high-end GPU. This enables the team to perform the ML independently as well as perform ML quickly using the high-end GPU. Both PCs have PuTTY and Python Anaconda installed
Laptop:
* Windows 10
* Intel i5-8250u w/ integrated graphics
* 16GB RAM
* 1TB SSD
Desktop:
* Windows 10
* Liquid cooled AMD 3800x
* 32GB RAM
* 1TB NVMe SSD
* NVIDIA RTX 2080 GPU, 8GB RAM
 
Basic Motion Testing
* Waveshare JetBot image for the SD card came with basic Jupyter notebooks installed with it. The first one was basic motion. Using that we were able to successfully move the robot left and right and front etc. We were able to successfully control the motors individually using the notebook and the remote controller provided with the Waveshare kit
Move/Teleoperate the robot using ROS/SDK:
* We successfully navigated the bot remotely leveraging the included SDK and controller
 
Teleoperation Testing
* We have been able to link the controller to the JetBot directly and via a remote desktop:
* We have had some difficulty mapping the controller correctly. The bot should move left and right, however, this maps to the forward and backward. To fix this, we would have done numerical transformation of the controller sticks, however, mapping to the web browser interface was more handy.
 
Road Following / Lane Detection
* We have invested significant time in understanding road following:
* We understand the concepts of camera calibration, perspective transformation and edge finding
* We were able to leverage the the lane following notebook from the NVIDIA GitHub repository, however, we did explore several alternatives as described below.
 
### Part 2 - Research
According to the milestones regarding background knowledge, our team investigated the following subjects to enhance our understanding of robotics and machine learning:

AI
* We have reviewed the core concepts of data gathering, machine learning, deep learning and artificial intelligence. We have reviewed neural nets as well as various industry examples and implementation of intelligent robots
 
ROS
* Understood the concepts behind the ROS framework, the messaging framework used and the basic idea of being able to reuse and encapsulate programming for robot manipulation.
 
Algorithms
* We have not programmed the robot yet for line following. However, we have been able to program it for collision detection. A Pytorch ML model was used to train the model by capturing about 100 photos for “Free” and about 100 photos for “blocked” and works well. We also attempted a model with less data (20 blocked and 60 free), however that model did not perform well.
 
Models
* We have explored various ML models such as alexnet and Pytorch for collision detection
* We have explored models for edge detection
* Additionally, we now understand cross entropy functions and why they are needed
 
Datasets
* We have created several datasets to build a collision avoidance algorithm. Over 800 pictures have been captured.
 
Pytorch
* Did some research between should we use Pytorch or Tensorflow. Tensorflow has a little more learning curve and considering the time available at hand right now, decided to use Pytorch for now. Additionally, we understand how Pytorch uses CUDA cores for the GPU processing power. However, due to constraints with RTX CUDA drivers we used CPU for our epochs.
 
Other Topics
* We have explored robotic process automation as well as intelligent business automation for the use in financial services in depth and have attended several conferences this year. Thought about how lanes and a driving track can be created and obstacles can be added. Planning to use painters tape to create driving track and use wooden toys for stop sign and some other signs.
 
### Part 3 - Model Development
Below is a description of our approach to model development broken down categorically to describe our design choices
Model Execution Structure
* We decided to build two models independently; one for lane detection and following and another for sign detection. These models are distinct enough to warrant individual modules for data gathering, model building and testing.
* These two distinct models were combined into a single script for execution. We explored using the Robotic Operating System (ROS), however, for the intent of the project we found that ROS was overly complex for this use case.
* To enable models running simultaneously, we are reducing the camera frames per second to lower the load on the cuda cores while running two models in quick series.
 
Lane Detection Model Selection
* For lane detection, we explored 3 models; deep pi car, the practical IML exercise and embedded pytorch notebooks. Upon through investigation, we concluded that the most efficient approach was the embedded pytorch notebooks. The notebooks are very well detailed, well suited to our use case and function well.
* We are leveraging the resnet18 pretrained model on for lane navigation. It has functioned very well and has produced a successful model that allowed our Jetbot to navigate two turns of the course that we defined based off of our 800+ pictures.
* To build the model we created a data gathering script; 01_data_gathering_lane_detection.ipynb. This allowed us to remotely move the robot to collect data around the course without having to manually move it between frame captures
* The course was constructed using 2-inch blue painters tape to simulate roads. The color was selected to contrast the floor at the filming location. The lanes are approximately 5 inches wide and the turn radius is approximately 3 inches.
* Then, we used a modified script for model building; 02_model_development_lane_detection.ipynb. This was useful for us to change various modeling parameters to get the best model from the collected data. All modeling parameters can be found in the GitHub repository
* We developed three models prior to settling on the third model as our final one. The first two models would cross the lane at an unacceptable frequency. Prior to building the third model, the course was modified to ensure that all turns had the same radius and that all lanes had the same width. We discovered that the more consistent course greatly helped the model keep lanes more effectively.
* The model was tested using 03_testing_lane_detection.ipynb. Our current lane keeping model will correctly navigate the course 9 out of 10 attempts
 
Sign Recognition Model Selection
* For sign recognition, we are using alexnet, which is a transfer learning algorithm included with torchvision. We successfully deployed this model earlier in our project and we have a high degree of confidence with this technique.
* To build the model we created a data gathering script; 01_data_gathering_sign_recognition.ipynb. This allowed us to remotely move the robot to collect data around the course without having to manually move it between frame captures
* Toy stop signs approximately 2 inches in height and 2 inch in diameter were used to simulate real stop signs. This size sign was selected due to the relative size of the robot being nearly proportional to a passenger vehicle and road sign.
* Then, we used a modified script for model building; 02_model_development_sign_recognition.ipynb. This was useful for us to change various modeling parameters to get the best model from the collected data. All modeling parameters can be found in the GitHub repository
* Two models were developed before we settled on the final model. The previous models were extremely sensitive to background location and prevented the model from being shared between teammates. The final model was built using over 400 pictures in the location that would be where the complete model would be filmed. Note, various angles and distances were used in the frame captures.
* The model was tested using 03_testing_sign_recognition.ipynb. The current sign detection model will correctly identify the ‘stop sign’ used 10 out of 10 attempts

### Part 4 - Results
Our final versions of the models were placed in series and executed in 04_model_execution.ipynb. The model is able to navigate two turns in both directions and will recognize and stop at the signs posed on the course. The bot is limited to about 10% of its full speed to allow a slower rate of change for the steering. Faster speeds created oversteering scenarios on the turns and occasionally the straights. When both models are combined the robot is able to successfully navigate the course 10 out of 10 attempts.
 
### Part 5 - Future Enhancements
During the development of the robot we identified several opportunities for further exploration:
* Integration with ROS - While we did consider ROS, but found it overly complex for our us case. However, we believe that ROS would allow us to have more complex models and more models running in parallel.
* More complex city with multiple signs - A more complex city would allow us to test more scenarios that would further develop our skill with model development and logic coding. However, time did not allow us to add further complexity to our project.
* Multiple robot interactions, two bots on course simultaneously - This would have allowed us to expand our knowledge into detecting and classifying moving objects. Additionally, we could have created traffic logic or other traffic optimizations that would have been of more real world use.
