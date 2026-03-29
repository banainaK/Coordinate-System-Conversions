# Coordinate-System-Conversions

I am currently working on a game development project in Unreal Engine, and if there's one thing that I would say was the hardest part of my project, it is converting coordinates and rotations between different coordinate systems. 

For context, to describe the problem I was facing: 
I had matrices corresponding to rotation and displacement in a robot's coordinate frame. To better explain what these matrices are, I am going to attempt to explain linear algebra concepts used in robotics. 

In robotics, every link or joint has its own coordinate frame (also called **body frame**), which describes rotation and displacement with respect to its frame. 
To express this rotation and displacement in terms of the **world frame**, we use a **homogeneous matrix**. To explain what this means, consider this setup:

<img width="452" height="558" alt="image" src="https://github.com/user-attachments/assets/04ff3e44-0bc8-4da8-8a02-956c61735851" />

Link: https://fr.mathworks.com/help/robotics/ug/estimate-pose-of-fixed-camera-relative-to-robot-base.html

Now, let the **Robot Base Frame** be **Frame A** in the image below and the **End Effector Frame** be **Frame B**. 
We can see from this that with respect to the robot frame:

$X_b$ points in the opposite direction as $X_a$: 

$$X_b = \begin{bmatrix} -1 & 0 & 0 \\ \end{bmatrix}$$

$Y_b$ points in the same direction as $Y_a$: 

$$Y_b = \begin{bmatrix} 0 & 1 & 0 \\ \end{bmatrix}$$ 

$Z_b$ points in the opposite direction as $Z_a$: 

$$Z_b = \begin{bmatrix} 0 & 0 & -1 \\ \end{bmatrix}$$ 

Therefore, we can represent the **body frame's** rotation with respect to the **world frame** or its **rotation matrix** as: 

$$R = \begin{bmatrix} X_b & Y_b & Z_b \\ \end{bmatrix}$$ 

$$R = \begin{bmatrix} 
-1 & 0 & 0 \\ 
0 & 1 & 0 \\ 
0 & 0 & -1  \\
\end{bmatrix}$$

![IMG_DF5266B3BE8B-1](https://github.com/user-attachments/assets/01e8716c-debf-47ce-8b05-baa327d9c36b)



