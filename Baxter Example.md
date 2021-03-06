

[TOC]

# Baxter Example

## Joint Position Keyboard Example

### Summary 

The joint position keyboard example demonstrates basic joint position control. Each key of PC is mapped to either increasing or decreasing the angles of a particular joint on Baxter's arms. Each arm is represented by one side of the keyboard with corresponding increase/decrease joint key pairings within the rows.

### Usage

```
# Verify that the robot is enabled from an RSDK terminal session
$ rosrun baxter_tools enable_robot.py -e  (rbe)
# Start the joint position keyboard example program.
$ rosrun baxter_examples joint_position_keyboard.py
```

```
  key bindings: 
  Esc: Quit
  ?: Help
  /: left: gripper calibrate
  ,: left: gripper close
  m: left: gripper open
  y: left_e0 decrease
  o: left_e0 increase
  u: left_e1 decrease
  i: left_e1 increase
  6: left_s0 decrease
  9: left_s0 increase
  7: left_s1 decrease
  8: left_s1 increase
  h: left_w0 decrease
  l: left_w0 increase
  j: left_w1 decrease
  k: left_w1 increase
  n: left_w2 decrease
  .: left_w2 increase
  b: right: gripper calibrate
  c: right: gripper close
  x: right: gripper open
  q: right_e0 decrease
  r: right_e0 increase
  w: right_e1 decrease
  e: right_e1 increase
  1: right_s0 decrease
  4: right_s0 increase
  2: right_s1 decrease
  3: right_s1 increase
  a: right_w0 decrease
  f: right_w0 increase
  s: right_w1 decrease
  d: right_w1 increase
  z: right_w2 decrease
  v: right_w2 increase
```

### Code Walkthrough

```python
import argparse 
import rospy
import baxter_interface
import baxter_external_devices

from baxter_interface import CHECK_VERSION
# This imports the baxter interface for accessing the limb and the gripper class. The baxter_external_devices is imported to use its getch function, that captures the key presses on the keyboard. It is not necessary to check the version in custom programs.

def map_keyboard():
    left = baxter_interface.Limb('left')
    right = baxter_interface.Limb('right')
    grip_left = baxter_interface.Gripper('left')
    grip_right = baxter_interface.Gripper('right')
    lj = left.joint_names()
    rj = right.joint_names()
    # Two instances of the Limb class are created for each of Baxter's limbs. Similarly two instances of the Gripper class. joint_names() method returns an array of joints in that limb.

    def set_j(limb, joint_name, delta):
        current_position = limb.joint_angle(joint_name)
        joint_command = {joint_name: current_position + delta}
        limb.set_joint_positions(joint_command)
    # The set_j() function is invoked whenever a valid key press occurs. The limb refers to the limb instance of baxter's limbs for the corresponding side. delta refers to the required displacement of that joint. The the joint command message is updated for that corresponding joint to indicate the new position. set_joint_positions() method published the joint commands to the position controller.
     bindings = {
     #   key: (function, args, description)
         '9': (set_j, [left, lj[0], 0.1], "left_s0 increase"),
         '6': (set_j, [left, lj[0], -0.1], "left_s0 decrease"),
         '8': (set_j, [left, lj[1], 0.1], "left_s1 increase"),
         '7': (set_j, [left, lj[1], -0.1], "left_s1 decrease"),
         'o': (set_j, [left, lj[2], 0.1], "left_e0 increase"),
         'y': (set_j, [left, lj[2], -0.1], "left_e0 decrease"),
         'i': (set_j, [left, lj[3], 0.1], "left_e1 increase"),
         'u': (set_j, [left, lj[3], -0.1], "left_e1 decrease"),
         'l': (set_j, [left, lj[4], 0.1], "left_w0 increase"),
         'h': (set_j, [left, lj[4], -0.1], "left_w0 decrease"),
         'k': (set_j, [left, lj[5], 0.1], "left_w1 increase"),
         'j': (set_j, [left, lj[5], -0.1], "left_w1 decrease"),
         '.': (set_j, [left, lj[6], 0.1], "left_w2 increase"),
         'n': (set_j, [left, lj[6], -0.1], "left_w2 decrease"),
         ',': (grip_left.close, [], "left: gripper close"),
         'm': (grip_left.open, [], "left: gripper open"),
         '/': (grip_left.calibrate, [], "left: gripper calibrate"),
 
         '4': (set_j, [right, rj[0], 0.1], "right_s0 increase"),
         '1': (set_j, [right, rj[0], -0.1], "right_s0 decrease"),
         '3': (set_j, [right, rj[1], 0.1], "right_s1 increase"),
         '2': (set_j, [right, rj[1], -0.1], "right_s1 decrease"),
         'r': (set_j, [right, rj[2], 0.1], "right_e0 increase"),
         'q': (set_j, [right, rj[2], -0.1], "right_e0 decrease"),
         'e': (set_j, [right, rj[3], 0.1], "right_e1 increase"),
         'w': (set_j, [right, rj[3], -0.1], "right_e1 decrease"),
         'f': (set_j, [right, rj[4], 0.1], "right_w0 increase"),
         'a': (set_j, [right, rj[4], -0.1], "right_w0 decrease"),
         'd': (set_j, [right, rj[5], 0.1], "right_w1 increase"),
         's': (set_j, [right, rj[5], -0.1], "right_w1 decrease"),
         'v': (set_j, [right, rj[6], 0.1], "right_w2 increase"),
         'z': (set_j, [right, rj[6], -0.1], "right_w2 decrease"),
         'c': (grip_right.close, [], "right: gripper close"),
         'x': (grip_right.open, [], "right: gripper open"),
         'b': (grip_right.calibrate, [], "right: gripper calibrate"),
      }
 # The bindings is a dictionary that holds the set of characters in the keyboard and their corresponding joints.

	
    done = False
    print("Controlling joints. Press ? for help, Esc to quit.")
    while not done and not rospy.is_shutdown():
        c = baxter_external_devices.getch()
        if c:
         	#catch Esc or ctrl-c
         	if c in ['\x1b', '\x03']:
         		done = True
         		rospy.signal_shutdown("xample finished.")
    # The done variable captures whether "esc" or "ctrl-c" was hit. The while loop iterates as long as the "esc" or "ctrl-c" is hit or ros-shutdown signal is given. c captures the keyboard input. 
    		 else:
                 print("key bindings: ")
                 print("  Esc: Quit")
                 print("  ?: Help")
                 for key, val in sorted(bindings.items(),
                                        key=lambda x: x[1][2]):
                     print("  %s: %s" % (key, val[2]))
                        
def main():
    epilog = """
    See help inside the example with th '?' key for key bindings.
    """
    arg_fmt = argparse.RawDescriptionHelpFormatter
    parser = argparse.ArgumentParser(formater_class=arg_fmt,
                                     description=main.__doc__,
                                     epilog=epilog)
    parser.parse_args(rospy.myargv()[1:])
    
    print("Initializing node...")
    rospy.init_node("rsdk_joint_position_keyboard")
    print("getting robot state...")
    rs = baxter_interface.RobotEnable(CHECK_VERSION)
    init_state = rs.state().enabled
    # init_state captures Baxter's initial state. This is to make sure that Baxter is sent back to the same state after the program exits.
	
    def clean_shutdown():
        print("\nExiting example...")
        if not init_state:
            print("Disabling robot...")
            rs.disable()
    rospy.on_shutdown(clean_shutdown)
    print("Enabling robot...")
    rs.enable()
    map_keyboard()
    print("Done.")
    
if __name__ == '__main__':
    main()
           
```

## Display an image to Baxter's head

Display an image (e.g. .png or .jpg) to Baxter's head display. Baxter display resolution is 1024 * 600 pixels.

```
$ rosrun baxter_examples xdisplay_image.py --file=`rospack find baxter_examples`/share/images/baxterworking.png
# Replace the --file argument with the path to your own image.
```

### Code Walkthrough

```python
import os
import sys
import argparse

import rospy

import cv2
import cv_bridge
# The cv2 library is imported to read images as cv2 messages and perform various processing on it. The cv_bridge is used to convert between OpenCV and ROS images.
from sensor_msgs.msg import (
	image,
)

def send_image(path):
    im = cv2.imread(path)
    # The imread() is an OpenCV function that is used to load images as 2D numpy arrays. It is important to read the images as 2D numpy arrays in OpenCV 2.0 in order to execute various OpenCV algorithms on them.
    msg = cv_bridge.cvBridge().cv2_to_imgmsg(img, encoding="bgr8")
    # The OpenCV image has to be converted into ROS image message in order to send them as ROS messages or perform any ROS functions on it. The cv_bridge is used to convert between ROS and OpenCV images. The cv2_to_image converts the OpenCV image to ROS image.
    pub = rospy.Publisher('/robot/xdisplay', Image, latch=True)
    # The ROS image is published to the /robot/xdisplay topic
    pub.publish(msg)
    # Sleep to allow for image to be published
    rospy.sleep(1)
def main():
    # Pass the relative or absolut file path to an image file on your computer, and the example will read and convert the image using cv_bridge, sending it to the screen as a standard ROS Image Message.
    epilog = 
    arg_fmt = argparse.RawDescriptionHelpFormatter
    parser = argparse.ArgumentParser(formatter_class=arg_fmt,
                                     description=main.__doc__,
                                     epilog=epilog)
    required = parser.add_argument_group('required arguments')
    required.add_argument(
    	'-f', '--file', metavar='PATH', required=True,
    	help='Path to image file to send'
    )
    parser.add_argument(
    	'-d', '--delay', metavar='SEC', type=float, default=0.0,
    	help='Time in seconds to wait before publishing image'
    )
    args = parser.parse_args(rospy.myargv()[1:])
    # The path of the image to be loaded, and the delay in publishing the images are captured as user arguments.
    rospy.init_node('rsdk_xdisplay_image', anonymous=True)
    
    if not os.access(args.file, os.R_OK):
        rospy.logerr("Cannot read file at '%s'" % (args.file,))
        return 1
    # The rospy node is initialized. Then the readability of the path entered by the user is verified.
    if args.delay > 0:
        rospy.loginfo(
        	"Waiting for $s second(s) before publishing image to face" % (args.delay,)
        )
        rospy.sleep(args.delay)
        
    send_image(args.file)
    return 0

if __name__ == '__main__':
    sys.exit(main())
```



## Gripper Control 

Uses the keyboard or joystick to control Baxters grippers. Position, velocity, holding, and moving force can be controlled and sensed. Both logitech and xbox game controllers are supported.

```
$ rosrun baxter_examples gripper_keybiard.py
# You will have to calibrate both grippers before using any of the other commands using C/c commands.

# You can monitor the changes you are making using the following rostopics which you can monitor from a different shell:
$ rostopic echo /robot/end_effector/left_gripper/command
$ rostopic echo /robot/end_effector/right_gripper/command
```

### Code Walthrough

```python
import argparse
import rospy

import baxter_interface
import baxter_external_devices
# This imports the baxter interface for accessing the limb and the gripper class. The baxter_external_devices is imported to use its getch function that captures the key presses on the keyboard.
from baxter_interface import CHECK_VERSION

def map_keyboard():
    # initialize interfaces
    print("Getting robot state... ")
    rs = baxter_interface.RobotEnable(CHECK_VERSION)
    init_state = rs.state().enabled
    left = baxter_interface.Gripper('left', CHECK_VERSION)
    right = baxter_interface.Gripper('right', CHECK_VERSION)
    # The init_state variable captures the current state of the robot. 
    def clean_shutdown():
        if not init_state:
            print("Disabling robot...")
            rs.disable()
        print("Exiting example.")
    rospy.on_shutdown(clean_shutdown)
    # On shutdown request, Baxter's state is sent back to its initial state.
    def capability_warning(gripper, cmd):
        msg = ("%s %s - not capable of '%s' command" %
               (gripper.name, gripper.type(), cmd))
        rospy.logwarn(msg)
        # This function is invoked when a gripper control command is called with custom grippers. It displays a warning message.
    def offset_position(gripper, offset):
        if gripper.type() != 'electric':
            capability_warning(gripper, 'command_position')
            return
        current = gripper.position()
        gripper.command_position(current + offset)
        # The gripper tyoe is checked for an electric gripper. The command_position() sets the gripper to the commanded position. It moves the fingers from close(0) to open(100) state based on the passed double vale.
	def offset_holding(gripper, offset):
        if gripper.type() != 'electric':
            capability_warning(gripper, 'set_holding_force')
            return
        current = gripper.parameters()['holding_force']
        gripper.set_holding_force(current + offset)
        # set_holding_force() method is used to set the force of the grippers when the fingers come in contact with an object between them.
	def offset_moving(gripper, offset):
        if gripper.type() != 'electric':
            capability_warning(gripper, 'set_moving_force')
            return
        current = gripper.parameters()['moving_force']
        gripper.set_moving_force(current + offset)
        # set_moving_force() method is used to set the force of the grippers immediately before the fingers come in contact with an object between them.
	def offset_velocity(gripper, offset):
        if gripper.type() != 'electric':
            capability_warning(gripper, 'set_velocity')
            return
        current = gripper.parameters()['velocity']
        gripper.set_velocity(current + offset)
        # set_velocity() method is used to set the velocity at which the grippers would open/close.
	def offset_dead_band(gripper. offset):
        if gripper.type() != 'electric':
            capability_warning(gripper, 'set_dead_band')
            return
        current = gripper.parameters()['dead_zone']
        gripper.set_dead_band(current + offset)
        # set_dead_band() method is used to set the dead zone of the grippers. Precisely, it refers to the minimum distance between the gripper fingers when they are in closed state.
	 bindings = {
     #   key: (function, args, description)
         'r': (left.reboot, [], "left: reboot"),
         'R': (right.reboot, [], "right: reboot"),
         'c': (left.calibrate, [], "left: calibrate"),
         'C': (right.calibrate, [], "right: calibrate"),
         'q': (left.close, [], "left: close"),
         'Q': (right.close, [], "right: close"),
         'w': (left.open, [], "left: open"),
         'W': (right.open, [], "right: open"),
         '[': (left.set_velocity, [100.0], "left:  set 100% velocity"),
         '{': (right.set_velocity, [100.0], "right:  set 100% velocity"),
         ']': (left.set_velocity, [30.0], "left:  set 30% velocity"),
         '}': (right.set_velocity, [30.0], "right:  set 30% velocity"),
         's': (left.stop, [], "left: stop"),
         'S': (right.stop, [], "right: stop"),
         'z': (offset_dead_band, [left, -1.0], "left:  decrease dead band"),
         'Z': (offset_dead_band, [right, -1.0], "right:  decrease dead band"),
         'x': (offset_dead_band, [left, 1.0], "left:  increase dead band"),
         'X': (offset_dead_band, [right, 1.0], "right:  increase dead band"),
         'f': (offset_moving, [left, -5.0], "left:  decrease moving force"),
         'F': (offset_moving, [right, -5.0], "right:  decrease moving force"),
         'g': (offset_moving, [left, 5.0], "left:  increase moving force"),
         'G': (offset_moving, [right, 5.0], "right:  increase moving force"),
         'h': (offset_holding, [left, -5.0], "left:  decrease holding force"),
         'H': (offset_holding, [right, -5.0], "right:  decrease holding force"),
         'j': (offset_holding, [left, 5.0], "left:  increase holding force"),
         'J': (offset_holding, [right, 5.0], "right:  increase holding force"),
         'v': (offset_velocity, [left, -5.0], "left:  decrease velocity"),
         'V': (offset_velocity, [right, -5.0], "right:  decrease velocity"),
         'b': (offset_velocity, [left, 5.0], "left:  increase velocity"),
         'B': (offset_velocity, [right, 5.0], "right:  increase velocity"),
         'u': (offset_position, [left, -15.0], "left:  decrease position"),
         'U': (offset_position, [right, -15.0], "right:  decrease position"),
         'i': (offset_position, [left, 15.0], "left:  increase position"),
         'I': (offset_position, [right, 15.0], "right:  increase position"),
     }
    
    done = False
    print("Enabling robot...")
    rs.enable()
    print("Controlling grippers.Press ? for help, Esc to quit.")
    while not done and not rospy.is_shutdown():
        c = baxter_external_devices.getch()
        if c:
            if c in ['\x1b', '\x03']:
                done = True
        elsif c in bindings:
            cmd = bindings[c]
            cmd[0](*cmd[1])
            print("command: %s" % (cmd[2],))
        else:
            print("key bindings: ")
            print("  Esc: Quit")
            print("  ?: Help")
            for key, val in sorted(bindings.items(),
                                   key=lamda x: x[1][2]):
                print("  %s: %s" % (key, val[2]))
	rospy.signal_shutdown("Example finished.")

def main():
    epilog = 
    arg_fmt = argsparse.RawDescriptionHelpFormatter
    parser = argparse.ArgumentParser(formatter_class=arg_fmt,
                                     description=main.__doc__,
                                     epilog=epilog)
    parser.parse_args(rospy.myargv()[1:])
    
    print("Initializing node... ")
    rospy.init_node("rsdk_gripper_keyboard")
    map_keyboard()
    
if __name__ == '__main__':
    main()
        
```



## Head Movement Example

The Head "Wobbler" Example randomly moves the head to demonstrate using the hand pan and nod interfaces.

Baxter's head can move up and down in a nod_motion, and rotate left-to-right in a pan_motion. The head nod motion is a single action that can be triggered to run, but cannot be set to a specific angle. The pand motion swings Baxters 'face' to a settable angle, rotating around the vertical axes of the head.

```
$ rosrun baxter_examples head_wobbler.py
# Baxter's head will nod and begin panning left and right to random angles.
```

### baxter_interafce APIs

- Head class: head.py
- command_nod()
- set_pan(<angle>)

### Code Walkthrough

```python
import argparse
import random

import rospy
import baxter_interface
from baxter_interface import CHECK_VERSION

class Wobbler(object):
    def __init__(self):
        self._done = False
        self._head = baxter_interface.Head()
        # The _head is an object of the Head class within the baxtre_interface. This creates a subscriber to the topic robot/head/head_state and publishers to the topics robot/head/command_head_pan, robot/head/command_head_nod. The object _head, would be used to control the head nodding and panning.
        print("Getting robot state... ")
        self._re = baxter_interface.RobotEnable(CHECK_VERSION)
        self._init_state = self._rs.state().enabled
        print("Enabling robot... ")
        self._rs.enable()
        print("Running. Ctrl-c to quit")
        #The enable() performs the actual enabling of the robot. It is important to note that the robot should be in enabled state before attempting to move its joints.
        
	def clean_shutdown(self):
        # Exits example cleanly by moving head to neutral position and maintaining start state
        print("\nExiting example...")
        if self._done:
            self.set_neutral()
        if not self._init_state and self._rs.state().enabled:
            print("Disabling robot...")
            self._rs.disable()
            
	def set_neutral(self):
        # Sets the head back into a neutral pose
        self._head.set_pan(0.0)
    
    def wobble(self):
        self.set_neutral()
        # Before the robot starts to wobble its head, it moves its head to the neutral position. 
        # Performs the wobbling
        self._head.command_node()
        command_rate = rospy.Rate(1)
        control_rate = rospy.Rate(100)
        start = rospy.get_time()
        while not rospy.is_shutdown() and (rospy.get_time() - start < 10.0):
            angle = random.uniform(-1.5, 1.5)
            while (not rospy.is_shutdown() and
                   not (abs(self._head.pan() - angle) <=
                       baxter_interface.HEAD_PAN_ANGLE_TOLERANCE)):
                #This ensures that it lopps till the current head pan position and the random angle generated above is within the HEAD_PAN_ANGLE_TOLERANCE
                self._head.set_pan(angle, speed=30, timeout=0)
                control_rate.sleep()
            command_rate.sleep()
        self._done = True
        rospy.signal_shutdown("Example finished.")
        #Once the wobbling is completed, the _done variable is updated as True and a shutdown signal is sent.
def main():
    arg_fmt = argparse.RawDescriptionHelpFormatter
    parser = argparse.ArgumentParser(formatter_class=arg_fmt,
                                     description=main.__doc__)
    parse.parse_args(rospy.myargv()[1:])
    
    print("initializing node... ")
    rospy.init_node("rsdk_head_wobbler")
    
    wobbler = Wobbler()
    rospy.on_shutdown(wobbler.clean_shutdown)
    print("Wobbling... ")
    wobbler.wobble()
    print("Done.")
    
if __name__ =='__main__':
    main()
    
```



## Head Action Client Example

The Head Action Client Example moves the head through a set of head positions and velocities to showcase an interface for the Head Action Server.

```
$ rosrun baxter_interface head_action_server.py
$ rosrun baxter_examples head_action_client.py
```

### Code Walkthrough

```python
import sys
import argparse

import rospy
import actionlib

from control_msgs.msg import (
	SingleJointPositionAction,
	SingleJointPositionGoal,
)
import baxter_interface

from baxter_interface import CHECK_VERSION

class HeadClient(object):
    def __init__(self):
        ns = 'robot/head/head_action'
        self._client = actionlib.SimpleActionClient(
        	ns,
        	SingleJointPositionAction
        )
        self._goal = SingleJointPositionGoal()
        
        # Wait 10 seconds for the head action server to start or exit
        if not self._client.wait_for_server(rospy.Duration(10.0)):
            rospy.logerr("Exiting - Head Action Server Not Found")
            rospy.signal_shutdown("Action Server not found")
            sys.exit(1)
        self.clear()
        
	def command(self, position, velocity):
        self._goal.position = position
        self._goal.max_velocity = velocity
        self.client.send_goal(self.goal)
        # HeadClient.command() takes as arguments a position and velocity. They are packed into the goal object and sent to the Head Action Server.
	def stop(self):
        self._client.cancel_goal()
        
    def wait(self, timeout=5.0):
        self._client.wait_for_result(timeout=rospy.Duration(timeout))
        return self._client.get_result()
    
    def clear(self):
        slef._goal = SingleJointPositionGoal()

def main():
    arg_fmt = argparse.RawDescriptionHelpFormatter
    parser = argparse.ArgumentParser(formatter_class=arg_fmt,
                                     description=main.__doc__)
    parser.parse_args(rospy.myargv()[1:])
    # The beginning of the main function parses any parameters passed into this example when it is invoked from the command line.
    print("Initializing node...")
    rospy.init_node("rsdk_head_action_client")
    print("Getting robot state...")
    rs = baxter_interface.RobotEnable(CHECK_VERSION)
    print("Enabling robot...")
    rs.enable()
    print("Running. Ctrl-c to quit")
    hc = HeadClient()
    hc.command(position=0.0, velocity=100.0)
    hc.wait()
    hc.command(position=1.57, velocity=10.0)
    hc.wait()
    hc.command(position=0.0, velocity=80.0)
    hc.wait()
    hc.command(position=-1.0, velocity=40.0)
    hc.wait()
    hc.command(position=0.0, velocity=60.0)
    print hc.wait()
    print "Exiting - Head Action Test Example Complete"
    
if __name__ == "__main__":
    main()
```

## IK Service Example

The IK Test example shows the very basics of calling the on-robot Inverse-Kinematics (IK) Service to obtain a joint angles solution for a given endpoint Cartesian point & orientation. Inverse Kinematics is used to convert between the Cartesian (x, y, z, roll, pitch, yaw) sace representation, to actual controllable 7-DOF joint states.

```
$ rosrun baxter_examples ik_service_client.pyp -l left
```

### Code Walkthrough

```python
import argparse
import sys
import rospy

from geometry_msgs.msg import (
	PoseStamped,
	Pose,
	Point,
	Quaterntion,
)
from std_msgs.msg import Header

from baxter_core_msgs.srv import (
	SolvePositionIK,
	SolvePositionIKRequest,
)
# The geometry message types are imported to build the request message for the IK service. The custom request and response message types are imported from the baxter_core_msgs package.
def ik_test(limb):
    rospy.init_node("rsdk_ik_service_client")
    ns = "ExternalTools/" + limb + "/PositionKinematicsNode/IKservice"
    iksvc = rospy.ServiceProxy(ns, SolvePositionIK)
    hdr = Header(stamp=rospy.Time.now(), frame_id='base')
    
    poses = {
         'left': PoseStamped(
             header=hdr,
             pose=Pose(
                 position=Point(
                     x=0.657579481614,
                     y=0.851981417433,
                     z=0.0388352386502,
                 ),
                 orientation=Quaternion(
                     x=-0.366894936773,
                     y=0.885980397775,
                     z=0.108155782462,
                     w=0.262162481772,
                 ),
             ),
         ),
         'right': PoseStamped(
             header=hdr,
             pose=Pose(
                 position=Point(
                     x=0.656982770038,
                     y=-0.852598021641,
                     z=0.0388609422173,
                 ),
                 orientation=Quaternion(
                     x=0.367048116303,
                     y=0.885911751787,
                     z=-0.108908281936,
                     w=0.261868353356,
                 ),
             ),
         ),
     }
 
    ikreq.pose_stamp.append(poses[limb])
    
    try:
        rospy.wait_for_service(ns, 5.0)
        resp = iksvc(ikreq)
	except (rospy.ServiceException, rospy.ROSException), e:
        rospy.logerr("Service call failed: %s" % (e,))
        return 1
    # With a timeout of 5 seconds, the IK service is called along with the IK request message. The resp objects captures the response message which contains the joint positions. It throws an error on timeout.
    if (resp.isValid[0]):
        print("SUCCESS - Valid Joint Solution Found:")
        # Format solution into Limb API-compatible dictionary
        limb_joints = dict(zip(resp.joints[0].name, resp.joints[0].position))
        print limb_joints
    else:
        print("INVALID POSE - No Valid Joint Solution Found.")
    return 0    
	# The isValid[0] field is boolean variable that indicates whether a successfull solution for the IK was found or not. 
def main():
    arg_fmt = argparse.RawDescriptionHelpFormatter
    parse = argparse.ArgumentParser(formatter_class=arg_fmt,
                                    description=main.__doc__)
    parser.add_argument(
    	'-1', '--limb', choices=['left', 'right'], required=True,
    	help="the limb to test"
    )
    args = parser.parse_args(rospy.myargv()[1:])
    return ik_test(args.limb)

if __name == '__main__':
    sys.exit(main())
```

## Input and Outputs on Baxter

The Digital and Analog IO components include most of the buttons and On/Off LEDs on the robot. These smaple programs show how to use the python interfaces and provide a demonstration of using the command and status messages of the components. 

```
# Start the DigitalIO example.This example will blink the LED on the Left Navigator on and then off while printing the status before and after.
$ rosrun baxter_examples digital_io_blink.py
# To run the AnalogIO example.This example will run the robot's fans (which are Analog Outputs) from 0 to 100 and back down again in increments of 10.
$ rosrun baxter_examples analog_io_rampup.py
```

### Code Walkthrough

```python
import argparse
import rospy
import baxter_interface.digital_io as DIO

def test_interface(io_compoent='left_itb_light_outer'):
    rospy.loginfo("Blinking Digital output: %s", io_compoent)
    b = DIO.DigitalIO(io_component)
    
    print "Initial state: ", b.state
    # The io_component is initialized to be as "left_itb_light_outer" by default.
    b.set_output(True)
    rospy.sleep(1)
    print "New state: ", b.state
    
    # reset output
    b.set_output(False)
    rospy.sleep(1)
    print "Final state:", b.state
    # The output of the digital io component is toggled between True and False, and the corresponding output is displayed using the state() function.
    
def main():
    epilog = 
    arg_fmt = argparse.RawDescriptionHelpFormatter
    parser = argparse.ArgumentParser(formatter_class=arg_fmt,
                                     description=main.__doc__,
                                     epilog=epilog)
    parser.add_argument(
    	'-c', '--component', dest='component_id',
    	default='left_irb_light_outer',
    	help=('name of Digital IO component to use'
             ' (default: left_itb_light_outer)')
    )
    args = parser.parse_args(rospy.myargv()[1:])
    
    rospy.init_node('rskd_digital_io_blink', anoymous=True)
    is_component = rospy.get_param('-component_id', args.compoent_id)
    test_interface(io_component)
if __name__ ='__main__':
    main()
```

## Joint Position Example

The Joint Position Control demonstrate how to use position control to move and sense the arm baased on joint angles. Examples include keyboard or joystick based user control of arm angles, along with complementary example programs to record and playback joint positions.

### Keyboard Control 

```
$ rosrun baxter_examples joint_position_keyboard.py
```

### Recording Joint Positions

```
$ rosrun baxter_examples joint_recorder.py -f <filename>
```

Note: You can open and close the grippers while recording by using Baxter's cuff buttons: Oval=Close, Circle=Open

After you have finished recording, stop the joint_position program using <Ctrl-C>  or hit one of the function 1 or 2 buttons on the game controller (e.g. Select/Select).

### Playback Recordings

```
$ rosrun baxter_examples joint_position_file_playback.py -f <filename>
```

### Code Walkthrough

#### Joint Position Recorder

**Interface -** JointRecorder.record()

```python
import argparse
import rospy
import baxter_interface
from baxter_examples import JointRecorder
from baxter_interface import CHECK_VERSION

def main():
    epilog = 
    arg_fmt = argparse.RawDescriptionHelpFormatter
    parser = argparse.ArgumentParser(formatter_class=arg_fmt,
                                     description=main.__doc__,
                                     epilog=epilog)
    required = parser.add_argument_group('required arguments')
    required.add_argument(
    	'-f', '--file', dest='filename', required=True,
         help='the file name to record to'
    )
    parser.add_argument(
    	'-r', '--record-rate', type=int, default=100, metavar="RECORDRATE",
    	help='rate at which to record (default: 100)'
    )
    args = parser.parse_args(rospy.myargv()[1:])
    
    print("Initializing node... ")
    rospy.init_node("rsdk_joint_recorder")
    print("Getting robot state... ")
    rs = baxter_interface.RobotEnable(CHECK_VERSION)
    print("Enabling robot... ")
    rs.enable()

    recorder = JointRecorder(args.filename, args.record_rate)
    rospy.on_shutdown(recorder.stop)

    print("Recording. Press Ctrl-C to stop.")
    recorder.record()

    print("\nDone.")

if __name__ == '__main__':
    main()
```

#### Joint Position File Playack 

**Interfaces -** 

- Gripper.error()
- Gripper.reset()
- Gripper.calibrated()
- Gripper.type()
- Gripper.calibrate()
- Gripper.command_positions(<*double*>)
- Limb.move_to_joint_positions(<*Joint command*>)
- Limb.set_joint_positions(<*Joint Command*>)

```python
import argparse
import sys
import rospy
import baxter_interface
from baxter_interface import CHECK_VERSION

def clean_line(line, names):
    """
    Cleans a single line of recorded joint positions
    @param line: the line described in a list to process
    @para names: joint name keys
    """
    #convert the line of strings to a float or None
    line = [try_float(x) for x in line.rstrip().split(',')]
    #zip the values with the joint names
    combined = zip(names[1:], line[1:])
    #take out any tuples theat have a none value
    cleaned = [x for x in combined if x[1] is not None]
    #convert it to a dictionary with only valid commands
    command = dict(cleaned)
    left_command = dict((key, command[key]) for key in command.keys()
                         if key[:-2] == 'left_')
    right_command = dict((key, command[key]) for key in command.keys()
                         if key[:-2] == 'right_')
    return (command, left_command, right_command, line)

def map_file(filename, loops=1):
	left = baxter_interface.Limb('left')    
    right = baxter_interface.Limb('right')
    grip_left = baxter_interface.Gripper('left', CHECK_VERSION)
    grip_right = baxter_interface.Gripper('right', CHECK_VERSION)
    rate = rospy.RATE(1000)
    if grip_left.error():
         grip_left.reset()
    if grip_right.error():
         grip_right.reset()
    if (not grip_left.calibrated() and
         grip_left.type() != 'custom'):
         grip_left.calibrate()
    if (not grip_right.calibrated() and
         grip_right.type() != 'custom'):
         grip_right.calibrate()
#error() method returns if the gripper is in an error state. 
	print("Playing back: %s" % (filename,))
    with open(filename, 'r') as f:
        lines = f.readlines()
	keys = lines[0].rstrip().split(',')
    l = 0
# If specified, repeat the file playback 'loops' number of times
	while loops < 1 or l < loops:
        i = 0
        l += 1
        print("Moving to start position...")
        
        _cmd, lcmd_start, rcmd_start, _raw = clean_line(lines[1], keys)
        left.move_to_joint_positions(lcmd_start)
        right.move_to_joint_positions(rcmd_start)
        start_time = rospy.get_time()
    	
        for values in lines[1:]:
            i += 1
            loopstr = str(loops) if loops > 0else "forever"
            sys.stdout.write("\r Record %d of %d, loop %d of %s" %
                             (i, len(lines) - 1, l, loopstr))
            sys.stdout.flush()
            cmd, lcmd, rcmd, values = clean_line(values, keys)
            while (rospy.get_time() - start_time) < values[0]:
                if rospy.is_shutdown():
                    print("\n Aborting - ROS shutdown")
                    return False
                if len(lcmd):
                    left.set_joint_positions(lcmd)
                if len(rcmd):
                    right.set_joint_positions(rcmd)
                if ('left_gripper' in cmd and
                    grip_left.type() != 'custom'):
                   grip_left.command_position(cmd['left_gripper'])
                if ('right_gripper' in cmd and
                    grip_right.type() != 'custom'):                    
                 grip_right.command_position(cmd['right_gripper'])
                rate.sleep()
        print
    return True
def main():
    epilog = 
    arg_fmt = argparse.RawDescriptionHelpFormatter
    parser = argparse.argumentParser(formatter_class=arg_fmt,
                                     description=main.__doc__,
                                     epilog=epilog)
    parser.add_argument(
        '-f', '--file', metavar='PATH', required=True,
        help='path to input file'
    )
    parser.add_argument(
        '-l', '--loops', type=int, default=1,
        help='number of times to loop the input file. 0=infinite.'
    )
    args = parser.parse_args(rospy.myargv()[1:])
    print("Initializing node... ")
    rospy.init_node("rsdk_joint_position_file_playback")
    print("Getting robot state... ")
    rs = baxter_interface.RobotEnable(CHECK_VERSION)
    init_state = rs.state().enabled

    def clean_shutdown():
        print("\nExiting example...")
        if not init_state:
            print("Disabling robot...")
            rs.disable()
    rospy.on_shutdown(clean_shutdown)

    print("Enabling robot... ")
    rs.enable()

    map_file(args.file, args.loops)


if __name__ == '__main__':
    main()
```

## Joint Torque Control

This example shows joint torque control usage. After moving to neutral, the robot will enter torque control mode, applying torques representing virtual springs holds to their start position.

```
$ rosrun baxter_examples joint_torque_springs.py
# In another terminal
$ rosrun rqt_reconfigure rqt_reconfigure
```

### Code Walkthrough

**interfaces**-

- Limb.set_joint_torques(<*double*>)
- Limb.move_to_neutral()
- Limb.joint_angles
- Limb.joint_velocities
- Limb.set_command_timeout(<*double*>)
- Limb.exit_control_mode()

```python
import argparse
import rospy
from dynamic_reconfigure.server import(
	Server,
)
from std_msgs.msg impoer (
	Empty,
)

import baxter_interface

from baxter_example.cfg import (
	JointSpringsExampleConfig,
)
from baxter_interface import CHECK_VERSION

class JointSprings(object):
    def __init__(self, limb, reconfig_server):
        self._dyn = reconfig_server
        self._rate = 1000.0
        self._missed_cmds = 20.0
        
        #creat limb instance
        self._limb = baxter_interface.Limb(limb)
        
        #initialize parameters
        self._springs = dict()
        self._damping = dict()
        self._start_angles = dict()
        
        #creat cuff disable publisher
		cuff_ns = 'robot/limb/' + limb + '/suppress_cuff_interaction'
        self._pub_cuff_disable = rospy.Publisher(cuff_ns, Empty, queue_size=1)
        
        #verify robot is enabled 
        print("Getting robot state... ")
        self._rs = baxter_interface.robotEnable(CHECK_VERSION)
        self._init_state = self._rs.state().enabled
        print._rs.enable()
        print("Running. Ctrl-c to quit")
 def _update_parameters(self):
    for joint in self._limb.joint_names():
        self._springs[joint] = self._dyn.config[joint[-2:] + '_spring_stiffness']                                                   
        self._damping[joint] = self._dyn.config[joint[-2:] + '_damping_coefficient'                                       
	def _update_forces(self):
		"""
		Calculates the current angular difference between the start position and the current joint position applying the joint torque string forces as defined on the dynamic reconfigure server.
		"""
		# get latest spring constants
		self._update_parameters()
		# disable cuff interaction
		self._pub_disable.publish()
		# calculate current forces
		for joint in self._start_angles.keys():
			# spring portion
			cmd[joint] = self._springs[joint] * (self._start_angles[joint] - 
			cur_pos[joint])
			# damping portion
			cmd[joint] -= self._damping[joint] * cur_vel[joint]
		# command new joint torques
		self._limb.set_joint_torques(cmd)
	def move_to_neutral(self):
		# Moves the limb to neutral location
		self._limb.move_to_neutral()
	def attach_springs(self):
		"""
       Switches to joint torque mode and attached joint springs to current joint positions.
	    """
	    self._start_angles = self._limb.joint_angles()
	    
	    # set control rate
	    control_rate = rospy.Rate(self._rate)
	     # for safety purposes, set the control rate command timeout.
        # if the specified number of command cycles are missed, the robot
        # will timeout and disable
        self._limb.set_command_timeout((1.0 / self._rate) * self._missed_cmds)
        # loop at specified rate commanding new joint torques
        while not rospy.is_shutdown():
        	if not self._rs.state().enabled:
        		rospy.logerr("Joint torque example failed to meet specified control rate timeout.")
        		break
        	self._update_forces()
        	control_rate.sleep()
       
       def clean_shutdown(self):
       print("\nExiting example...")
       self._limb.exit_control_mode()
       if not self._init_state and self._rs.state().enabled:
       		print("disabling robot...")
       		self._rs.disable()
       		
def main():
	arg_fmt = argparse.RawDescriptionHelpFormatter
    parser = argparse.ArgumentParser(formatter_class=arg_fmt,                                     description=main.__doc__)
    parser.add_argument(
        '-l', '--limb', dest='limb', required=True, choices=['left', 'right'],
        help='limb on which to attach joint springs'
    )
    args = parser.parse_args()
    print("Initializing node... ")
    rospy.init_node("rsdk_joint_torque_springs_%s" % (args.limb,))
    dynamic_cfg_srv = Server(JointSpringsExampleConfig,
                             lambda config, level: config)
    js = JointSprings(args.limb, dynamic_cfg_srv)
    # register shutdown callback
    rospy.on_shutdown(js.clean_shutdown)
    js.move_to_neutral()
    js.attach_springs()

if __name__ == "__main__":
    main()
```

## Joint Trajectory Playback 

Enable the robot joint trajectory interface, parse a file created using the joint position recorder example, and send the resulting joint trajectory to the action server.

```
# Verify that the robot is enabled
$ rosrun baxter_tools enable_robot.py -e
# Record a joint position file using the joint_recorder.py
$ rosrun baxter_examples joint_recorder.py -f <example_file>
# Move the arms while holding the cuffs.Press any key to exit when done recording.
# Start joint tarjectory controller.
$ rosrun baxter_interface joint_trajectory_action_server.py --mode velocity
# in another RSDK terminal session, run the joint trajectory playback example.
$ rosrun baxter_examples joint_trajectory_file_playback.py -f <example_file>
# Both arms will be commanded to repeat the trajectory recorded during the joint position recording.
```

The difference between this example and the joint_position playback example is that the trajectory controller has the ability to honor the velocities (due to the timestamps) to more accurately repeating the recorded tarjectory.

### Code Walkthrough

```python
import argparse
import operator
import sys

from bisect import bisect
from copy import copy
from os import path

import rospy

import actionlib

from control_msgs.msg import (
	FollowJointTrajectoryAction,
    FollowJointTrajectoryGoal,
)
from trajectory_msgs.msg import (
	JointTrajectoryPoint,
)

import baxter_interface

from naxter_interface import CHECK_VERSION

class Trajectory(object):
    def __init__(self):
        #create our action server clients
        self._left_client = actionlib.SimpleActionClient(
            'robot/limb/left/follow_joint_trajectory',
            FollowJointTrajectoryAction,
        )
        self._right_client = actionlib.SimpleActionClient(
            'robot/limb/right/follow_joint_trajectory',
            FollowJointTrajectoryAction,
        )
        
        # verify joint trajectory action servers are available
        l_server_up = self._left_client.wait_for_server(rospy.Duration(10.0))
        r_server_up = self._right_client.wait_for_server(rospy.Duration(10.0))
        if not l_server_up or not r_server_up:
            msg = ("Action server not available."
                  	" Verify action server availablity.")
            rospy.logerr(msg)
            rospy.signal_shutdown(msg)
            sys.exit(1)
            
            #create our goal request
            self._l_goal = FollowJointTrajectoryGoal()
            self._r_goal = FollowJointTrajectoryGoal()
            
            #limb interface - current angles needed for start move
            self._l_arm = baxter_interface.Limb('left')
            self._r_arm = baxter_interface.Limb('right')
            
            #gripper interface - for gripper command playback
        	self._l_gripper = baxter_interface.Gripper('left', CHECK_VERSION)
        	self._r_gripper = baxter_interface.Gripper('right', CHECK_VERSION)            
```

## Joint Velocity Puppet Example

Use puppet as an example of controlling Baxter using joint velocity control.

```
$ rosrun baxter_examples joint_velocity_puppet.py -l right
```

Grab the other arm (e.g. the left in this case) at the cuff and move it around using '0G' mode to puppet the arm specified on the command line.

### Code Walkthrough

```python
import argparse
import sys
import rospy

from std_msgs.msg import (
	UInt16,
)

import baxter_interface
from baxter_interface import CHECK_VERSION

class Puppeteer(object):
    def __init__(self, limb, amplification=1.0):
        puppet_arm = {"left": "right", "right": "left"}
        self._control_limb = limb
        self._puppet_limb = puppet_arm[limb]
        self._control_arm = baxter_interface.limb.Limb(self._control_limb)
        self._puppet_arm = baxter_interface.limb.Limb(self._puppet_limb)
        self._amp = amplification
        # The side of the limb that would be controlled is captured as _control_limb while the other limb is the _puppet_limb. Two instances of the Limb class for both the limbs are created.
        print("Getting robot state... ")
        self._rs = baxter_interface.RobotEnable(CHECK_VERSION)
        self._init_state = self._rs.state().enabled
        print("Enabling robot... ")
        self._rs.enable()
        
	def _reset_control_modes(self):
        rate = rospy.Rate(100)
        for _ in xrange(100):
            if rospy.is_shutdown():
                return False
            self._control_arm.exit_control_mode()
            self._puppet_arm.exit_control_mode()
            rate.sleep()
        return True
    # The default controller is the position controller. When the method exit_control_mode() is invoked, the default controllers are switched on from the current controllers.
    def set_neutral(self):
        print("Moving to neutral pose...")
        self._control_arm.move_to_netral()
        self._puppet_arm.move_to_neutral()
        
    def clean_shutdown(self):
        print("\nExiting example...")
        self._reset_control_modes()
        self.set_neutral()
        if not self._init_state:
            print("Disabling robot...")
            self._rs.disable()
        return True
    def puppet(self):
        self.set_neutral()
        rate = rospy.Rate(100)
        control_joint_names = self._control_arm.joint_names()
        puppet_joint_names = self._puppet_arm.joint_names()
		print ("Puppeting:\n"
              "  Grab %s cuff and move arm.\n"
              "  Press Ctrl-C to stop...") % (self._control_limb,)
        while not rospy.is_shutdown():
            cmd = {}
            for idx, name in enumerate(puppet_joint_names):
                v = self._control_arm.joint_velocity(
                    control_joint_names[idx])
                if name[-2:] in ('s0', 'e0', 'w0', 'w2'):
                    v = -v
                cmd[name] = v * self._amp
            self._puppet_arm.set_joint_velocities(cmd)
            rate.sleep()
def main():
	max_gain = 3.0
    min_gain = 0.1
    arg_fmt = argparse.RawDescriptionHelpFormatter
    parser = argparse.ArgumentParser(formatter_class=arg_fmt,
                                     description=main.__doc__)
    required = parser.add_argument_group('required arguments')
    required.add_argument(
        "-l", "--limb", required=True, choices=['left', 'right'],
        help="specify the puppeteer limb (the control limb)"
    )
    parser.add_argument(
        "-a", "--amplification", type=float, default=1.0,
        help=("amplification to apply to the puppeted arm [%g, %g]"
              % (min_gain, max_gain))
    )
    args = parser.parse_args(rospy.myargv()[1:])
    if (args.amplification < min_gain or max_gain < args.amplification):
        print("Exiting: Amplification must be between: [%g, %g]" %
              (min_gain, max_gain))
        return 1

    print("Initializing node... ")
    rospy.init_node("rsdk_joint_velocity_puppet")

    puppeteer = Puppeteer(args.limb, args.amplification)
    rospy.on_shutdown(puppeteer.clean_shutdown)
    puppeteer.puppet()

    print("Done.")
    return 0

if __name__ == '__main__':
    sys.exit(main())
```

## Wobbler Example

There are two modes of control for Baxter's arms, joint position and joint velocity control. The example shows how to set velocity control mode and read joint states at 1000Hz.

```
$ rosrun baxter_examples joint_velocity_wobbler.py
```

### Code Walkthrough

```python
import argparse
import math
import random

import rospy

from std_msgs.msg import (
	UInt16,
)

import baxter_interface

from baxter_interface import CHECK_VERSION

class Wobbler(object):
    def __init__(self):
        self._pub_rate = rospy.Publisher('robot/joint_state_publish_rate',
                                         UInt16, queue_size=10)
        self._left_arm = baxter_interface.limb.Limb("left")
        self._right_arm = baxter_interface.limb.Limb("right")
        self._left_joint_names = self._left_arm.joint_names()
        self._right_joint_names = self._right_arm.joint_names()
        # control parameters
        self._rate = 500.0  # Hz

        print("Getting robot state... ")
        self._rs = baxter_interface.RobotEnable(CHECK_VERSION)
        self._init_state = self._rs.state().enabled
        print("Enabling robot... ")
        self._rs.enable()

        # set joint state publishing to 500Hz
        self._pub_rate.publish(self._rate)
        
    def _reset_control_modes(self):
        rate = rospy.Rate(100)
        for _ in xrange(100):
            if rospy.is_shutdown():
                return False
            self._left_arm.exit_control_mode()
            self._right_arm.exit_control_mode()
            self._pub_rate.publish(100)
            rate.sleep()
        return True
    def set_neutral(self):
        """
        Sets both arms back into a neutral pose.
        """
        print("Moving to neutral pose...")
        self._left_arm.move_to_neutral()
        self._right_arm.move_to_neutral()

    def clean_shutdown(self):
        print("\nExiting example...")
        #return to normal
        self._reset_control_modes()
        self.set_neutral()
        if not self._init_state:
            print("Disabling robot...")
            self._rs.disable()
        return True
    
    def wobble(self):
        self.set_neutral()
        rate = rospy.Rate(100)
        start = rospy.Time.now()
        def make_v_func():
            # make_v_func() is called for the number of joint_names in right limb. 
            """
            returns a randomly parameterized cosine function to control a
            specific joint.
            """
            period_factor = random.uniform(0.3, 0.5)
            amplitude_factor = random.uniform(0.1, 0.2)

            def v_func(elapsed):
                w = period_factor * elapsed.to_sec()
                return amplitude_factor * math.cos(w * 2 * math.pi)
            return v_func
        v_funcs = [make_v_func() for _ in self._right_joint_names]

        def make_cmd(joint_names, elapsed):
            return dict([(joint, v_funcs[i](elapsed))
                         for i, joint in enumerate(joint_names)])

        print("Wobbling. Press Ctrl-C to stop...")
        while not rospy.is_shutdown():
            self._pub_rate.publish(self._rate)
            elapsed = rospy.Time.now() - start
            cmd = make_cmd(self._left_joint_names, elapsed)
            self._left_arm.set_joint_velocities(cmd)
            cmd = make_cmd(self._right_joint_names, elapsed)
            self._right_arm.set_joint_velocities(cmd)
            rate.sleep()


def main():
    """RSDK Joint Velocity Example: Wobbler
    Commands joint velocities of randomly parameterized cosine waves
    to each joint. Demonstrates Joint Velocity Control Mode.
    """
    arg_fmt = argparse.RawDescriptionHelpFormatter
    parser = argparse.ArgumentParser(formatter_class=arg_fmt,
                                     description=main.__doc__)
    parser.parse_args(rospy.myargv()[1:])

    print("Initializing node... ")
    rospy.init_node("rsdk_joint_velocity_wobbler")

    wobbler = Wobbler()
    rospy.on_shutdown(wobbler.clean_shutdown)
    wobbler.wobble()

    print("Done.")

if __name__ == '__main__':
    main()
```

## IK Pick and Place Demo

### Code Walkthrough

This example demonstrates the usage of spawning objects in Gazebo, and using Inverse Kinematics calls to move the arm im position in order to pick up and block from the table, and place it back on the table. A list of gripper tip positions are hard-coded in the main() function and these are passed to the PickAndPlace() method. It is here that the joint positions are retrieved from the  Inverse Kinematics calls and executed as move_to_joint() commands.

```python
import argparse
import struct
import sys
import copy

import rospy
import rospkg

from gazebo_msgs.srv import (
	SpawnModel,
	DeleteModel,
)
from geometry_msgs.msg import(
	PoseStamped,
	Pose,
	Point,
	Quaternion,
)
from std_msgs.msg import(
	Header,
	Empty,
)
from baxter_core_msgs.srv import(
	SolvePositionIK,
	SolvePositionIKRequest,
)
import baxter_interface

class PickAndPlace(object):
    def __init__(self, limb, hover_distance = 0.15, verbose=True):
        self._limb_name = limb # string
        self._hover_distance = hover_distance # in meters
        self._verbose = verbose # bool
        self._limb = baxter_interface.Limb(limb)
        self._gripper = baxter_interface.Gripper(limb)
        ns = "ExternalTools/" + limb + "/PositionKinematicsNode/IKService"
        self._iksvc = rospy.ServiceProxy(ns, SolvePositionIK)
        rospy.wait_for_service(ns, 5.0)
        # Verify robot is enabled
        print("Getting robot state... ")
        self._rs = baxter_interface.RobotEnable(baxter_interface.CHECK_VERSION)
        self._init_state = self._rs.state().enabled
        print("Enabling robot... ")
        self._rs.enable()
        
	def move_to_start(self, start_angles=None):
        print("Moving the {0} arm to start pose...".format(self._limb_name))
        if not start_angles:
            start_angles:
                start_angles = dict(zip(self._joint_name, [0]*7))
        self._guarded_move_to_joint_position(start_angles)
        self.gripper_open()
        rospy.sleep(1.0)
        print("Running. Ctrl-c to quit")
    def ik_request(self, pose):
        hdr = Header(stamp=rospy.Time.now(), frame_id='base')
        ikreq = SlovePositionIKRequest()
        ikreq.pose_stamp.append(PoseStamped(header=hdr, pose=pose))
        try:
            resp = self._iksvc(ikreq)
        except (rospy.ServiceException, rospy.ROSException), e:
            rospy.logerr("Service call failed: %s" % (e,))
            return False
       	# Check if result valid and type of seed ultimately used to get solution
        # convert rospy's string representation of unit8[]'s to int's
        resp_seeds = struct_unpack('<%dB' % len(resp.result_type), resp.result_type)
        limb_joints = {}
        if (resp_seeds[0] != resp.RESULT_INVALID):
            seed_str = {
                ikreq.SEED_USER: 'User Provided Seed',
                ikreq.SEED_CURRENT: 'Current Joint Angles',
                ikreq.SEED_NS_MAP: 'Nullspace Setpoints',                
            }.get(resp_seeds[0], 'None')
            if self._verbose:
                print("IK Solution SUCCESS - Valid Joint Solution Found from Seed Type: {0}".format(
                (seed_str)))
            #Format solution into Limb API-compatible dictionary
            limb_joints = dict(zip(resp.joints[0].name, resp.joints[0].position))
            if self._verbose:
                print("IK Joint Solution:\n{0}".format(limb_joints))
                print("----------------")
        else:
            rospy.logerr("INVALID POSE - No Valid Joint Solution Solution Found.")
            return False
       	return limb_joints
    # This method is the essential function for requesting an Inverse Kinematics solution with the desired end effector pose. ik_request() will return the solved joint angles if there was a solution, or false if there was a problem with the IK solver or no solution was found.
    def _guarded_move_to_joint_position(self, joint_angles):
        if joint_angles:            self._limb.move_to_joint_positions(joint_angles)
        else:
            rospy.logerr("No Joint Angles provided for move_to_joint_positions. Staying put.")
    def gripper_open(self):
        self._gripper.open()
        rospy.sleep(1.0)
    
    def gripper_close(self):
        self._gripper.close()
        rospy.sleep(1.0)
        
    def _approach(self, pose):
        approach = copy.deepcopy(pose)
        # approach with a pose the hover-distance above the requested pose
        approach.position.z = approach.position.z + self._hover_distance
        joint_angles = self.ik_request(approach)
        self._guarded_move_to_joint_position(jpint_angles)
        
	def _retract(self):
        current_pose = self._limb.endpoint_pose()
        ik_pose = Pose()
        ik_pose.position.x = current_pose['position'].x
        ik_pose.position.y = current_pose['position'].y
        ik_pose.position.z = current_pose['position'].z + self._hover_distance
        ik_pose.orientation.x = current_pose['orientation'].x
        ik_pose.orientation.y = current_pose['orientation'].y
        ik_pose.orientation.z = current_pose['orientation'].z
        ik_pose.orientation.w = current_pose['orientation'].w
        joint_angles = self.ik_request(ik_pose)
        self._guarded_move_to_joint_position(joint_angles)
    def _servo_to_pose(self, pose):
        # servo down to release
        joint_angles = self.ik_request(pose)
        self._guarded_move_to_joint_position(joint_angles)

    def pick(self, pose):
        # open the gripper
        self.gripper_open()
        # servo above pose
        self._approach(pose)
        # servo to pose
        self._servo_to_pose(pose)
        # close gripper
        self.gripper_close()
        # retract to clear object
        self._retract()

    def place(self, pose):
        # servo above pose
        self._approach(pose)
        # servo to pose
        self._servo_to_pose(pose)
        # open the gripper
        self.gripper_open()
        # retract to clear object
        self._retract()
def load_gazebo_models(table_pose=Pose(position=Point(x=1.0, y=0.0, z=0.0)),
                       table_reference_frame="world",
                       block_pose=Pose(position=Point(x=0.6725, y=0.1265, z=0.7825)),
                       block_reference_frame="world"):
    # Get Models' Path
    model_path = rospkg.RosPack().get_path('baxter_sim_examples')+"/models/"
    # Load Table SDF
    table_xml = ''
    with open (model_path + "cafe_table/model.sdf", "r") as table_file:
        table_xml=table_file.read().replace('\n', '')
    # Load Block URDF
    block_xml = ''
    with open (model_path + "block/model.urdf", "r") as block_file:
        block_xml=block_file.read().replace('\n', '')
    # Spawn Table SDF
    rospy.wait_for_service('/gazebo/spawn_sdf_model')
    try:
        spawn_sdf = rospy.ServiceProxy('/gazebo/spawn_sdf_model', SpawnModel)
        resp_sdf = spawn_sdf("cafe_table", table_xml, "/",
                             table_pose, table_reference_frame)
    except rospy.ServiceException, e:
        rospy.logerr("Spawn SDF service call failed: {0}".format(e))
    # Spawn Block URDF
    rospy.wait_for_service('/gazebo/spawn_urdf_model')
    try:
        spawn_urdf = rospy.ServiceProxy('/gazebo/spawn_urdf_model', SpawnModel)
        resp_urdf = spawn_urdf("block", block_xml, "/",
                               block_pose, block_reference_frame)
    except rospy.ServiceException, e:
        rospy.logerr("Spawn URDF service call failed: {0}".format(e))
        
def delete_gazebo_models():
    # This will be called on ROS Exit, deleting Gazebo models
    # Do not wait for the Gazebo Delete Model service, since
    # Gazebo should already be running. If the service is not
    # available since Gazebo has been killed, it is fine to error out
    try:
        delete_model = rospy.ServiceProxy('/gazebo/delete_model', DeleteModel)
        resp_delete = delete_model("cafe_table")
        resp_delete = delete_model("block")
    except rospy.ServiceException, e:
        rospy.loginfo("Delete Model service call failed: {0}".format(e))
                
def main():
    """RSDK Inverse Kinematics Pick and Place Example
    A Pick and Place example using the Rethink Inverse Kinematics
    Service which returns the joint angles a requested Cartesian Pose.
    This ROS Service client is used to request both pick and place
    poses in the /base frame of the robot.
    Note: This is a highly scripted and tuned demo. The object location
    is "known" and movement is done completely open loop. It is expected
    behavior that Baxter will eventually mis-pick or drop the block. You
    can improve on this demo by adding perception and feedback to close
    the loop.
    """
    rospy.init_node("ik_pick_and_place_demo")
    # Load Gazebo Models via Spawning Services
    # Note that the models reference is the /world frame
    # and the IK operates with respect to the /base frame
    load_gazebo_models()
    # Remove models from the scene on shutdown
    rospy.on_shutdown(delete_gazebo_models)

    # Wait for the All Clear from emulator startup
    rospy.wait_for_message("/robot/sim/started", Empty)

    limb = 'left'
    hover_distance = 0.15 # meters
    # Starting Joint angles for left arm
    starting_joint_angles = {'left_w0': 0.6699952259595108,
                             'left_w1': 1.030009435085784,
                             'left_w2': -0.4999997247485215,
                             'left_e0': -1.189968899785275,
                             'left_e1': 1.9400238130755056,
                             'left_s0': -0.08000397926829805,
                             'left_s1': -0.9999781166910306}
    pnp = PickAndPlace(limb, hover_distance)
    # An orientation for gripper fingers to be overhead and parallel to the obj
    overhead_orientation = Quaternion(
                             x=-0.0249590815779,
                             y=0.999649402929,
                             z=0.00737916180073,
                             w=0.00486450832011)
    block_poses = list()
    # The Pose of the block in its initial location.
    # You may wish to replace these poses with estimates
    # from a perception node.
    block_poses.append(Pose(
        position=Point(x=0.7, y=0.15, z=-0.129),
        orientation=overhead_orientation))
    # Feel free to add additional desired poses for the object.
    # Each additional pose will get its own pick and place.
    block_poses.append(Pose(
        position=Point(x=0.75, y=0.0, z=-0.129),
        orientation=overhead_orientation))
    # Move to the desired starting angles
    pnp.move_to_start(starting_joint_angles)
    idx = 0
    while not rospy.is_shutdown():
        print("\nPicking...")
        pnp.pick(block_poses[idx])
        print("\nPlacing...")
        idx = (idx+1) % len(block_poses)
        pnp.place(block_poses[idx])
    return 0

if __name__ == '__main__':
    sys.exit(main())
```

