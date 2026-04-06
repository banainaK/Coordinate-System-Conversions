# Coordinate-System-Conversions

I am currently working on a game development project in Unreal Engine, and if there's one thing that I would say was the hardest part of my project, it was converting coordinates and rotations between different coordinate systems. 

For context, to describe the problem I was facing: 
I had matrices corresponding to rotation and displacement in a robot's coordinate frame. To better explain what these matrices are, I am going to attempt to explain linear algebra concepts used in robotics. 

## Section 1: The Homogeneous Matrix

In robotics, every link or joint has its own coordinate frame (also called **body frame**), which describes rotation and displacement with respect to its frame. 
To express this rotation and displacement in terms of the **world frame**, we use a **homogeneous matrix**. To explain what this means, consider this setup:

![JPEG image-499B-84D6-A5-0](https://github.com/user-attachments/assets/5744ccee-8da6-4111-81d6-2affaa41623b)

In robotics, usually this camera is defined in terms of **OpenCV convention** where Z = forward, X = right, Y = down

Now, let the **Robot Base Frame** be **Frame A** in the image below and the **Camera Frame** be **Frame B**. 
We can see from the image below that with respect to the **Robot Base Frame**, the **Camera Frame**:

$X_b$ points in the same direction as $Y_a$: 

$$X_b = \begin{bmatrix} 0 & 1 & 0 \\ \end{bmatrix}$$

$Y_b$ points in the opposite direction from $Z_a$: 

$$Y_b = \begin{bmatrix} 0 & 0 & -1 \\ \end{bmatrix}$$ 

$Z_b$ points in the same direction as $X_a$: 

$$Z_b = \begin{bmatrix} 1 & 0 & 0 \\ \end{bmatrix}$$ 

![IMG_A0E6FC345A34-1](https://github.com/user-attachments/assets/71d04744-7a04-4f50-8f6b-668a26ddd852)

Therefore, we can represent the **body frame's** rotation with respect to the **world frame** or its **rotation matrix** as: 

$$R = \begin{bmatrix} X_b & Y_b & Z_b \\ \end{bmatrix}$$ 

$$R = \begin{bmatrix} 
0 & 0 & 1 \\ 
1 & 0 & 0 \\ 
0 & -1 & 0  \\
\end{bmatrix}$$

Now, combining this rotation matrix with the displacement of the end effector frame from the world frame, we get the **homogenous matrix**

$$H = \begin{bmatrix} 
R & d \\ 
0 & 1  \\ 
\end{bmatrix}$$

$$H = \begin{bmatrix} 
0 & 0 & 1 & d_x \\ 
1 & 0 & 0 & d_y  \\ 
0 & -1 & 0 & d_z \\
0 & 0 & 0 & 1 \\
\end{bmatrix}$$

where R is our 3x3 **rotation matrix** and d is a 3 x 1 **displacement vector**, representing the displacement of our end effector frame from the world frame. 

## Section 2: Game Engine Coordinate Systems
Ok, so we have the above **homogenous matrix**, how would we go about converting this to a different coordinate system? For example, how would we convert this to Unreal Engine's Coordinate System? 

First, let's start by understanding Unreal Engine's coordinate system. 

Unreal Engine uses a **left-handed** coordinate system in comparison to our robot world frame, which uses a **right-handed** coordinate system. 

We see that the Unreal Engine coordinate system is defined to be: Z = up, Y = forward, X = right. 

![IMG_C0C3F3D62D3C-1](https://github.com/user-attachments/assets/7e9a7dec-7f3f-4226-9030-ddc2686581c8)

What about rotation? Most game engines handle rotation using Euler Angles (Roll, Pitch, Yaw) or Quaternions. 

**Roll** is defined as the rotation about the X-axis, **Pitch** is rotation about the Y-axis, and **Yaw** is rotation about the Z-axis. 

Using Euler angles is usually not recommended because they can lead to unexpected behavior where the values you read back differ from what you assigned. That's why it is usually recommended to use Quaternions, which game engines internally use to store Euler Angles. 

However, it is important to recognize that not all game engines store Euler Angles in the same way. 
For example, Unity's **Vector3** (a type of struct) stores Euler angles in Yaw, Roll, Pitch order, whereas Unreal Engine's **FRotator**(also a type of struct) stores Euler Angles in Pitch, Yaw, and Roll order. 

This just means Unity applies rotation about the Z -> X -> Y axis, whereas Unreal Engine applies rotation about the Y -> Z -> X axis. 

## Section 3: The Linear Algebra

So our goal is to take coordinates from our camera frame -> robot world frame -> Unreal Engine's coordinate system

We already have the **transformation** that takes us from the camera frame to the world frame. This is just our homogenous matrix:

$$H = \begin{bmatrix} 
0 & 0 & 1 & d_x \\ 
1 & 0 & 0 & d_y  \\ 
0 & -1 & 0 & d_z \\
0 & 0 & 0 & 1 \\
\end{bmatrix}$$

Now, how do we go from the robot world frame to Unreal Engine's coordinate system? 

Where this gets tricky is that, in our world frame: Z = up, X = forward, Y = right. In Unreal Engine, Z = up, Y = forward, X = right. 

So, **both** the X and Y axes need to be swapped. 

### Intrinsic and Extrinsic Rotations

---- Explain transformations --- 

Extrinsic rotation is just rotation about the x, y, or z axis. 

![JPEG image-4926-9736-8B-0](https://github.com/user-attachments/assets/cc83580f-a3df-46ce-b188-f1d5362da4d7)

Intrinsic rotation is rotation about a rotated coordinate system (X, Y, Z), which rotates with respect to the coordinate system we are familiar with (x, y, z). 

Let us consider a scenario to understand this better. 

I use this example from this article: https://dominicplein.medium.com/extrinsic-intrinsic-rotation-do-i-multiply-from-right-or-left-357c38c1abfd

Suppose we rotate (x, y, z) by 45 degrees about the z-axis to get a new coordinate system, A, and then we rotate A by 45 degrees about the z-axis to get coordinate system, B. 

How would we define our coordinates in B with respect to the original coordinate system? 






















