# Coordinate-System-Conversions

I am currently working on a game development project in Unreal Engine, and if there's one thing that I would say was the hardest part of my project, it was converting coordinates and rotations between different coordinate systems. 

For context, to describe the problem I was facing: 
I had matrices corresponding to rotation and displacement in a robot's coordinate frame. To better explain what these matrices are, I am going to attempt to explain linear algebra concepts used in robotics. 

## Section 1: The Homogeneous Matrix

In robotics, every link or joint has its own coordinate frame (also called **body frame**), which describes rotation and displacement with respect to its frame. 
To express this rotation and displacement in terms of the **world frame**, we use a **homogeneous matrix**. To explain what this means, consider this setup:

<img width="452" height="558" alt="image" src="https://github.com/user-attachments/assets/04ff3e44-0bc8-4da8-8a02-956c61735851" />

Link: https://fr.mathworks.com/help/robotics/ug/estimate-pose-of-fixed-camera-relative-to-robot-base.html

Now, let the **Robot Base Frame** be **Frame A** in the image below and the **End Effector Frame** be **Frame B**. 
We can see from the image below that with respect to the **Robot Base Frame**, the **End Effector Frame**:

$X_b$ points in the opposite direction from $X_a$: 

$$X_b = \begin{bmatrix} -1 & 0 & 0 \\ \end{bmatrix}$$

$Y_b$ points in the same direction as $Y_a$: 

$$Y_b = \begin{bmatrix} 0 & 1 & 0 \\ \end{bmatrix}$$ 

$Z_b$ points in the opposite direction from $Z_a$: 

$$Z_b = \begin{bmatrix} 0 & 0 & -1 \\ \end{bmatrix}$$ 

![IMG_2493FF478148-1](https://github.com/user-attachments/assets/cb66dbb6-673f-4a85-bd1e-3e607b66d5ca)

Therefore, we can represent the **body frame's** rotation with respect to the **world frame** or its **rotation matrix** as: 

$$R = \begin{bmatrix} X_b & Y_b & Z_b \\ \end{bmatrix}$$ 

$$R = \begin{bmatrix} 
-1 & 0 & 0 \\ 
0 & 1 & 0 \\ 
0 & 0 & -1  \\
\end{bmatrix}$$

Now, combining this rotation matrix with the displacement of the end effector frame from the world frame, we get the **homogenous matrix**

$$H = \begin{bmatrix} 
R & d \\ 
0 & 1  \\ 
\end{bmatrix}$$

$$H = \begin{bmatrix} 
-1 & 0 & 0 & d_x \\ 
0 & 1 & 0 & d_y  \\ 
0 & 0 & -1 & d_z \\
0 & 0 & 0 & 1 \\
\end{bmatrix}$$

where R is our 3x3 **rotation matrix** and d is a 3 x 1 **displacement vector**, representing the displacement of our end effector frame from the world frame. 

## Section 2: Game Engine Coordinate Systems
Ok, so we have the above **homogenous matrix**, how would we go about converting this to a different coordinate system? For example, how would we convert this to Unreal Engine's Coordinate System? 

Unreal Engine uses a **left-handed** coordinate system in comparison to our robot frame, which uses a **right-handed** coordinate system. 

We see that the Unreal Engine coordinate system is defined to be: Z = up, Y = forward, X = right. We designate these frames as our **world frame**, whereas our end effector frame is a **local frame**.

![IMG_C0C3F3D62D3C-1](https://github.com/user-attachments/assets/7e9a7dec-7f3f-4226-9030-ddc2686581c8)

What about rotation? Most game engines handle rotation using Euler Angles (Roll, Pitch, Yaw) or Quaternions. Roll is defined as the rotation about the X-axis, Pitch is rotation about the Y-axis, and Yaw is rotation about the Z-axis. 

Using Euler angles is usually not recommended because they can lead to unexpected behavior where the values you read back differ from what you assigned. Therefore, it is recommended to use Quaternions, which game engines use to internally store Euler Angles. 

However, it is important to recognize that not all game engines store Euler Angles in the same way. For example, Unity's **Vector3** (a type of struct) stores Euler angles in Yaw, Roll, Pitch order, whereas Unreal Engine's **FRotator**(also a type of struct) stores Euler Angles in Pitch, Yaw, and Roll order. 

This just means Unity applies rotation about the z-axis, then the x-axis, and then the y-axis, whereas Unreal Engine applies rotation about the y-axis, then the z-axis, and then the x-axis. 












