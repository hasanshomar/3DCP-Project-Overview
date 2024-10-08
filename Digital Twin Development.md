# Digital Twin Development

_Purpose:_
The digital twin will accurately replicate the real-world 3D concrete printing process, including physics, external forces, and all other relevant components. This virtual environment will be used to validate prints, detect issues, and understand their sources before proceeding to actual physical printing.

---

<details>

<summary>Step 1</summary>

### **1. Robot Model Creation and Launch in Gazebo**

The objective is to create a virtual representation of the ABB IRB 1660 robot and launch it in the Gazebo simulation environment. This is a crucial step in developing the digital twin, as it provides the foundational model that will be used for all subsequent simulation, control, and analysis tasks.

1. **ROS 2 Workspace Setup:**
   - The ROS 2 workspace is the environment where all the files, nodes, and configurations related to the digital twin will reside. Setting up a dedicated workspace ensures that all components of the project are organized, and easily accessible for development and testing. This will ensure that any dependent files will be easily allocated and accesible 
     - Create a dedicated ROS 2 workspace for the project (for example, `ros2_irb1660`).
     - Make sure that all necessary dependencies, such as ROS 2 Humble and Gazebo, are installed and properly configured.
     - Initialize the workspace, create all necessary directories and configure (build and source) the workspace as needed.

2. **URDF File Creation:**
   - The URDF (Unified Robot Description Format) file is essential for defining the physical and kinematic structure of the ABB IRB 1660 within the simulation. It describes the robot's geometry, joint connections, and physical properties, making it possible to simulate realistic motion and interactions in the digital environment.
     - Write the URDF file, specifying the robot's links (individual parts) and joints (connections between parts) to mirror the physical robot.
     - Include details such as the kinematic chain (the sequence of joints and links), which is crucial for calculating the robot's movements and simulating its behavior accurately.
     - Incorporate physical properties like mass, inertia, and joint limits to ensure that the simulation reflects real-world dynamics, allowing for more accurate testing and validation of the robot's performance.
     - **Testing:**
       - After visualizing the URDF in Rviz to ensure that the model is correctly defined, with all parts and connections in place, the robot is ready to be launched into the actual working environment; Gazebo.
       - Also if needed, debug and correct any issues related to link orientations, joint connections, or physical properties to prevent errors in the simulation. This information can be found from the STL files of the parts which can be opened through Solidworks or Blender or any other 3D Vizualization tool.

3. **Launch Files:**
   - Launch files are basically used for automating the process of starting up the simulation with all necessary components and beginning any other operations that include an action of the robot. These files make sure that the robot model is correctly loaded into Gazebo and can be easily visualized and tested.
     - Create a `spawn_robot_gazebo.launch.py` file (or whatever name is suitable) to load the robot model into Gazebo, specifying the necessary parameters such as the URDF file location and initial conditions.
     - If visualization in Rviz is needed (for example, to monitor the robot’s joint states or debug issues), create a `display.launch.py` file as well. This is a good step to have ready in case any changes are made to the URDF file later on for validation of the model. 
       - Once the above is done, it's time to launch the robot in Gazebo and verify that it appears correctly within the simulated environment. Here, the focus should be on checking for correct orientation, scale, and overall appearance.
       - As mentioned above, use Rviz, if needed, to further inspect the robot model and ensure that all visual aspects are as expected.

4. **Building the Network of Files:**
   - Proper organization and configuration of the workspace are critical for the smooth operation of the simulation and future development. There are several files that are required in a ROS2 package, most of which are automatically added when building the package, however, double checking that everything that's required is there is useful and necessary. By making sure that all files are correctly set up, this prevents errors during compilation and runtime, which will ease the development process and any changes that are made later on.
     - Organize the workspace to include all relevant URDF files, 3D meshes in the correct file format (.stl or .dev), configuration files, and launch files. This structure allows for easy access and modification as the project evolves.
     - Verify that all paths in the `CMakeLists.txt` (which controls the build process) and `package.xml` (which defines dependencies) files are correct. This step ensures that the ROS 2 build system can locate and compile all necessary files without issues.
     - Build and source the workspace to resolve any dependencies or errors, making sure that the project is ready for simulation.

</details>

---






<details>

<summary>Step 2</summary>

### **2. Making the Robot Moveable**

The objective is to enable the movement of the ABB IRB 1660 model within Gazebo, allowing for testing of motion and control algorithms. This step is important for simulating the robot's behavior under various conditions, which is essential for validating the digital twin's accuracy and preparing for real-world operations. A developed controller will mimic the real-life ABB robot controller, allowing the robot to be manually moved or to follow a path specified in an uploaded file containing some Geo-based information.

1. **Determine Control Strategy:**
   - **Options:**
     - **Joint Position Controller:** This is the simplest option and is ideal for initial testing and basic movement. It allows for direct control of each joint's position, making it easier to validate basic kinematics and ensure that the robot responds correctly to commands.
     - **Velocity or Effort Controllers:** These provide a more dynamic and realistic simulation by controlling the speed or force applied to each joint. These controllers are closer to how real-world robots operate and are better suited for complex motion tasks, where the robot’s interaction with its environment needs to be accurately simulated.
   - **Considerations:**
     - For initial testing and simple movement, start with a joint position controller to verify basic functionality.
     - For simulating real-world operations, where the robot needs to follow complex paths automatically, a combination of controllers may be used. This includes both manual control (akin to the "teach mode" in the real robot) and automated control for following preloaded paths.

      (_Source of above: ChatGPT_)

2. **Set Up Controllers:**
   - Controllers are basicallt the bridge between the commands given to the robot and the actual motion that occurs. Properly setting up controllers is needed so that the robot responds accurately to commands, whether they are simple joint positions or complex trajectories. In this case, the setup must accommodate both manual control and automated path-following, mimicking the real ABB controller’s functionality.
     - Controllers are typically configured in a YAML file, where you specify parameters like joint names, control types, and PID settings. This configuration is crucial for tuning the robot’s response to match real-world behavior.
     - Include JointStateController: This controller is essential for providing feedback on the robot’s current joint states, such as positions, velocities, and efforts. It allows for monitoring and adjusting the robot’s movements during simulation.
     - **Add Specific Controllers:**
       - **Manual Control Mode:** Set up a controller that allows for manual joint manipulation, mimicking the “teach mode” of the real robot. This can be done through a simple joint position controller or a more sophisticated interface that allows operators to guide the robot by hand or through a joystick.
       - **Automated Path-Following:** Set up a `JointTrajectoryController` that allows the robot to follow a predefined path. This is where the uploaded file (similar to a G-code or other path specification) will dictate the robot's movements automatically, just as in the real-world scenario.
     - Update Gazebo Launch Files: Modify the existing Gazebo launch files to initialize the controllers when the simulation starts. This ensures that the robot is ready to move, whether in manual mode or following an automated path.
     - Use Controller Spawner: Utilize the `controller_spawner` node to load the controllers during the simulation. This node takes care of initializing and managing the controllers, ensuring they are correctly integrated into the simulation.

      (_Source of above: ChatGPT_)

3. **Testing Movement:**
   - Once the Controllers are set up it's important to test the robot’s movement to ensure that the controllers are functioning correctly and that the robot moves as expected. This step verifies that the simulation accurately reflects the robot’s physical behavior and identifies any issues that need to be addressed.
     - **Manual Control Testing:** Using ROS 2 command-line tools or a custom node to manually control the robot’s joints, simulating the "teach mode" operation. This allows for testing of the robot’s manual movement capabilities and ensuring that operators can guide the robot accurately by applying the necessary joint translations. 
     - **Automated Path Testing:** Upload a path file to the simulation and use the `JointTrajectoryController` to test the robot’s ability to follow the path automatically. This step is crucial for validating the automated control aspect, ensuring that the robot can perform complex tasks without manual intervention.
     - **Visualize in Gazebo:** Watch the robot’s movement in Gazebo to ensure that it follows the commands accurately. This visual feedback is critical for identifying any discrepancies between the expected and actual behavior.

</details>

---






<details>

<summary>Step 3</summary>

### **3. Integrating Forward and Inverse Kinematics**

This step is a continuation of the previous step. The objective here is to implement both forward and inverse kinematics, allowing for manual control of the robot’s joints and automated path-following based on a desired end-effector position. This step is crucial for enabling both flexible manual operation and precise automated control, reflecting the real-world capabilities of the ABB robot in the digital twin.

1. **Forward Kinematics Implementation:**
     - The purpose of implementing forward kinematics is to allow manual control over the robot’s joints, providing operators with the flexibility to position the robot as needed. This is particularly important for tasks such as setting up the robot for specific operations, testing individual joint movements, and validating the physical model’s accuracy. Similar to how done in real-life in the lab, a controller should be able to be used to manually move the robot into any wanted position within the physical space restrictions of the robot. 
   - Forward kinematics is fundamental for understanding the robot's movement. By knowing the position of each joint, forward kinematics calculates the exact position of the robot’s end-effector (the printing nozzle) in space. This is essential for both manual and automated operations.
     - **Kinematics Library or ROS Packages:** Implement a kinematics library or utilize existing ROS packages like `moveit2`, which provides a well-established framework for handling kinematics.
     - **Node Development:** Develop or adapt a ROS 2 node that can accept joint commands (e.g., angles or displacements) and compute the corresponding position of the robot’s end-effector in Cartesian space (X, Y, Z coordinates).
     - Test the forward kinematics in the Gazebo simulation to ensure that when joint commands are given, the end-effector reaches the expected position. This is crucial for validating the accuracy of the kinematic model and ensuring that the robot moves as intended.

2. **Inverse Kinematics (IK) Setup:**
     - The purpose of implementing inverse kinematics is to allow the robot to follow a predefined path or reach specific points in space with its end-effector. This is essential for tasks like automated printing, where the robot must follow a precise trajectory based on a design file or set of instructions. This step is the more important one with respect to the overall objective of this part of the project as it will help mimic the real-world process of 3D printing concrete where a geo-code is fed into the controller for the robot which the robot can subscribe to, convert to understandable language, and translate in the form of motion. Important to consider though is that similar to the above step, the controller should be useable for beginning and terminating such operations. 
   - Inverse kinematics is key to automated control. It allows you to specify a desired position and orientation for the end-effector, and the IK solver calculates the necessary joint angles to achieve that position. This functionality is crucial for executing complex paths and operations that require precise control of the robot’s movements.
     - Use an IK solver, potentially from `moveit2` or a custom implementation. The IK solver is responsible for determining the correct joint angles that will position the end-effector at a desired point in space.
     - Create or modify ROS 2 nodes to take Cartesian coordinates (X, Y, Z) as input and solve for the corresponding joint angles. These nodes will be used to command the robot to move its end-effector to specific locations.
     - Define a series of test paths in space, such as straight lines, curves, or complex shapes. Use the IK solver to generate the necessary joint commands and verify that the robot can follow these paths accurately in the simulation.
       - **Validation:** Ensure that the robot reaches the desired positions with minimal error, adjusting the IK solver’s parameters if necessary to improve accuracy.

3. **Simulating a Print Path:**
     - The objective here is to simulate the robot’s printing process by loading a G-code file or another form of path instruction into the digital twin. The robot should be able to follow this path as it would during a real 3D printing operation.
   - Simulating a print path is crucial for validating the digital twin’s ability to replicate real-world printing operations. By ensuring that the robot can follow a G-code or similar path accurately, you can test the entire printing process virtually before moving to physical printing, reducing risks and errors.
     - Develop a script or ROS 2 node that reads the G-code file or other path instruction formats and translates this data into joint commands using the IK solver. This process involves parsing the file, extracting the necessary coordinates, and calculating the joint movements required to follow the path.
     - Load the path data into the simulation and run the robot through the print process. Monitor the robot’s ability to follow the path smoothly, adjusting the IK solver or controller settings as necessary to ensure that the robot moves without jerks, delays, or inaccuracies.
       - **Path Optimization:** If the robot encounters difficulties in following the path, refine the path planning algorithm or make adjustments to the robot’s movement parameters to achieve smoother and more accurate motion.

</details>

---






<details>

<summary>Step 4</summary>

### **4. Virtual Environment Setup**

The objective here is to set up the digital environment, including all necessary lab components, within Gazebo. This step is critical for creating a comprehensive simulation that replicates the real-world 3D printing setup, allowing for thorough testing and validation before physical operations begin.

1. **Modeling Lab Components:**
   - Accurately modeling the lab components is essential for creating a realistic simulation environment. Each component, from the mixer to the data collection tools, plays an important role in the 3D printing process, and their virtual counterparts must replicate their real-world functionality to provide meaningful simulation results.
   - **Components:**
     - _Mixer with Sensors:_ The mixer must be represented with its relevant sensors (e.g., temperature, consistency) to monitor the mixing process. These sensors provide data that can influence the printing process, making their accurate simulation crucial.
     - _Printing Table:_ The printing table is where the actual printing occurs. Its virtual model should reflect the real table’s dimensions, surface properties, and position relative to the robot.
     - _Data Collection Tools (e.g., Camera):_ Tools like cameras are essential for monitoring the print process in real-time. Their placement and functionality in the virtual environment must mirror their real-world setup to provide accurate data for analysis.
     - _Other Relevant Equipment:_ Any additional tools or components used in the lab (e.g., curing lights, ventilation systems) should also be modeled to create a complete simulation environment.
   - The process of how to set this up involves the creation of individual URDF file for all necessary components, programming the connective network of any components that are linked or have any linked operations such as the mixer and the printer. 

2. **Functionality Simulation:**
   - As explained above, it’s not enough to simply model the physical components; their functionality must also be accurately simulated. This includes sensor readings, actuator movements, and any other dynamic aspects of the components. Simulating these functions ensures that the digital twin behaves as the real system would, allowing for meaningful analysis and testing.
     - The goal is to mimic the functionality of each lab component in the virtual environment, ensuring that they behave as they would in the real world. This includes simulating sensor data, actuator responses, and interactions between components.
     - Use existing Gazebo plugins or develop custom plugins to simulate the functionality of each component. For example, a plugin could simulate the temperature readings from a sensor or the movement of an actuator in the mixer.
     - Where Gazebo plugins fall short, custom ROS nodes can be developed to simulate more complex interactions or to process and relay sensor data between components. These nodes can also handle tasks such as triggering events based on sensor readings or controlling actuators based on specific conditions.
     - Conduct simulation runs to test the functionality of each component. This involves checking that sensors provide realistic readings, actuators move as expected, and components interact correctly with one another and with the robot.
     - Use the simulation results to adjust the models or functionality as needed. If a sensor is providing incorrect data or an actuator isn’t moving as it should, make the necessary adjustments to the URDF/SDFormat files, plugins, or ROS nodes.
     - Ensure that the virtual environment provides realistic feedback that can be used for further development and testing of the robot’s movements and the overall 3D printing process. The accuracy of this simulation is critical for identifying potential issues and optimizing the process before physical trials begin.

      (_Source of above: ChatGPT_)

</details>

---







<details>

<summary>Step 5</summary>

### **5. Fluid Simulation for Concrete**

Objective:
To simulate the behavior of the concrete being printed, accounting for factors like viscosity, flow rate, and deposition accuracy. This step is critical for accurately replicating the printing process in the digital twin, enabling precise control and analysis of the material deposition.

*Need further details on this...*

</details>

---







<details>

<summary>Step 6</summary>

### **6. Verification and Testing**

</details>
