# Modeling-and-Control-of-Robotic-Manipulators

**_Due to university intellectual property restrictions and data privacy policies, the code for this project cannot be uploaded to a public GitHub repository._**
## ROS-Controlled Robotic Arm Motion Planning and Simulation with MATLAB

The project aims to develop a robust control system for a UR robotic arm using MATLAB and ROS, focusing on precise trajectory planning and execution. Through the integration of inverse kinematics, simulation in Gazebo, and communication with ROS, the system will enable users to specify end-effector poses and execute smooth motions while monitoring real-time joint states and velocities. By incorporating both topic-based and action-based interfaces, the project seeks to provide a comprehensive solution for controlling and simulating robotic arm movements, with applications in research, automation, and industrial settings. 
Here are 4 main parts listed below:

### 1.Robot Motion Control
1. **Inverse Kinematics**: Generate an inverse kinematics solver object (`iksolver`) for the rigid body tree object (`ur`).
2. **Define a Target Pose**: Define a target pose in terms of a translation and rotation by Euler angles.
3. **Determine Target Configuration**: Determine the inverse kinematics solution (`targetConf`) with the initialized `iksolver` object for the target pose of the end effector link frame 'ee_link'.

### 2.ROS
4. **Simulate UR Robot Motion in Gazebo**: Start the UR robot motion simulation in Gazebo using specified launch files.
5. **Connect to ROS**: Establish a connection to the ROS master on the local host from within MATLAB.
6. **Show Topics**: Query and obtain information about ROS topics, particularly the '/ur10/joint_states' topic.
7. **Instantiate Subscriber**: Create a subscriber in MATLAB for the '/ur10/joint_states' topic to receive joint state messages.

### 3.Trajectory Control via Topic Interface
8. **Inspect ROS Message Structure**: Understand the structure of trajectory commands published on the '/ur10/vel_based_pos_traj_controller/command' topic.
9. **Perform Point-to-Point Motion**: Execute a point-to-point motion from the current configuration to the target configuration.
10. **Monitor Joint State Temporal Evolution**: Replace `pause` with a rated loop that subscribes to '/ur10/joint_states' topic to monitor joint state evolution.
11. **Plot Joint State and Velocity**: Plot joint state and velocity versus time.
12. **Prepare Motion via Two Way-Points**: Compose a trajectory with multiple way-points.
13. **Perform Motion via Two Way-Points**: Command, record, and plot joint motion through multiple way-points.

### 4.Trajectory Control via Action Server and Client
14. **Inspect Action Structure**: Understand the structure of the action message and client-server interaction.
15. **Display Actions on ROS Network**: Confirm the existence of an action for trajectory control and inspect its topic.
16. **Instantiate Action Client**: Create an action client for trajectory following.
17. **Inspect Goal Message Structure**: Understand the structure of the goal message for trajectory following.
18. **Inspect Helper Function**: Understand a helper function for generating trajectory goal messages.
19. **Follow Trajectory (Blocking)**: Send trajectory command via action client and wait for completion.
20. **Follow Trajectory (Non-blocking + Monitoring)**: Send trajectory command via action client without waiting for completion and monitor feedback.
21. **Plot Joint States and Velocities**: Plot joint states and velocities for the commanded sequence of way-points.

## Detailed steps :

**Inverse Kinematics**

1.	Inverse Kinematics
Generate an inverse kinematics solver object iksolver for the rigid body tree object ur.

2.	Define a target pose
Define a target pose in terms of a transform tformTargetPose that results from a translation  and a rotation by Euler angles .

3.  Determine the targetConf with the initialized ikSolver object above
Determine the inverse kinematics solution targetConf with the method ik for the target pose of the end effector link frame 'ee_link' with a unit error weight vector .

**ROS**

4.    Simulate the UR robot motion in Gazebo
To simulate the UR robot motion in Gazebo invoke the following launch files:
		roslaunch ur_launch ur10_sim_gazebo.launch rqt:=false
The launch file starts Gazebo (hidden), RViz and the ROS controllers without the RQT interface.	

5.    Connect to ROS
Connect to the ROS master on the local host from within Matlab with rosinit

6.    Show topics
Query the topics that are currently available on the network and obtain information about the topic '/ur10/joint_states' in particular the message type.
You notice that the JointStatePublisher publishes on the topic '/ur10/joint_states'

7.    Instantiate a subscriber for the topic above and receive messages
Instantiate a subscriber in Matlab for this topic and receive a message for the joint states.
Inspect the field jointstate.Position which denotes the vector of joint variables. The first six elements correspond to joints shoulder_pan_joint to wrist_3_joint.

**Trajectory Control via Topic Interface**

8.    Information about ros message and helper function
Trajectory commands of message type trajectory_msgs/JointTrajectory are published on the topic  /ur10/vel_based_pos_traj_controller/command. Instantiate a message object and inspect the message type structure

9.    Perform a point to point motion
Perform a point-to-point motion from the current configuration to the target configuration from task 3.

10.    Execute the rated loop that monitors the temporal evaluation of the joint state vector
The motion command is executed in a fire and forget manner. Replace the pause command with a rated loop that monitors the temporal evolution of the joint state vector by subscribing to the /ur10/joint_states topic.
 
The helper function
	function [ jointState, jointVel ] = JointStateMsg2JointState( robot, jointMsg )	
converts a joint state message into joint state and joint velocity vectors.

12.    Plot the joint state and joint velocity
Plot the joint state and joint velocity vector versus time as shown in figure 3. The joint motion profile is quintic to guarantee continuity of joint state, velocities and accelerations at way-points.

13.    Prepare a motion via two way-points
A general trajectory is composed of a sequence of way-points. The joint trajectory commands accept multiple joint configurations with time stamps to be traversed in that order. Command a trajectory  composed of the intermediate way-point  and final way-point .

14.	Perform motion via two way-points
Command, record and plot the joint motion through multiple way-points with the same code as for the point to point motion. Notice, that 
	function [ jointTrajectoryMsg ] = JointVec2JointTrajectoryMsg(robot, q, t, qvel, qacc)
accepts, a matrix of way-points q and a vector of time instances t. The motion is shown in figure 4. Notice, that the joint velocities at the intermediate way-point go to zero. This is desirable for joints  which either stop or undergo a motion reversal at the first way-point. However, it seems awkward that joints  come to a stop before continuing their movement.

15.	Modify the joint trajectory from task 12 and perform task 12 and 13
Modify the joint trajectory command by imposing a velocity vector  for the first way-point. Notice, that for a smooth motion the joint velocities  should assume their maximum at the first way-point. The maximum velocity equals is twice as large as the average velocity given by

**Trajectory Control via Action Server and Client**

15.    Display the current list of actions on the ROS network
Display the current list of actions on the ROS network. Confirm that there is an action '/ur10/vel_based_pos_traj_controller/follow_joint_trajectory' and inspect the topic.
Notice, that goal message is of type control_msgs/FollowJointTrajectoryGoal.

16.	Instantiate an action client and an empty goal message
Instantiate an action client followJointTrajectoryActClient  for the action '/ur10/vel_based_pos_traj_controller/follow_joint_trajectory'.

17.    Inspect the structure of the goal message
Inspect the structure of followJointTrajectoryMsg. The field followJointTrajectoryMsg.Trajectory corresponds to	the message type that you previously used for the topic interface. The message contains additional specification for path, goal and time tolerance, to monitor the accuracy of the commanded motion. 

18.    Inspect the helper function 
The helper function 
	function [ followJointTrajectoryMsg ] = JointVec2FollowJointTrajectoryMsg(robot, q, t, qvel, qacc)
accepts a matrix of way-points q (optional joint velocities qvel and accelerations qacc) and a vector of time instances t  as input parameters and generates the corresponding followJointTrajectoryMsg.

19.    Follow a trajectory via the action client (blocking)
Generate a followJointTrajectoryMsg from the previous waypoint sequence and inspect the message structure.
Send the trajectory command message followJointTrajectoryMsg 
via the action client followJointTrajectoryActClient and wait for the action server to complete the trajectory. Inspect the resultMsg and resultState.

20.	Follow a trajectory via the action client (non-blocking + monitoring)
Instead of waiting for the completion of the trajectory use sendGoal to continue. Replace the topic interface commands
	jointTrajectoryPub.send(JointVec2JointTrajectoryMsg(ur10,q,t,qvel));
with the equivalent action interface command
	followJointTrajectoryMsg=JointVec2FollowJointTrajectoryMsg(ur10,q,t,qvel);
	followJointTrajectoryActClient.sendGoal(followJointTrajectoryMsg);
Observe the command line output of the activation, feedback and result callback functions.

21.    Plot the joint states and joint velocities
Plot the joint states and joint velocities for the commanded sequence of way-points.
19. **Follow Trajectory (Blocking)**: Send trajectory command via action client and wait for completion.
20. **Follow Trajectory (Non-blocking + Monitoring)**: Send trajectory command via action client without waiting for completion and monitor feedback.
21. **Plot Joint States and Velocities**: Plot joint states and velocities for the commanded sequence of way-points.

