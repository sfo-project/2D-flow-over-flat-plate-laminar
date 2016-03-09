# CFD Simulation Case Setup

**The created CFD domain is now read into the CFD package of interest to setup the CFD simulation. It should be noted that the current tutorial has a significant difference compared to other available CFD tutorials online! This tutorial is structured and developed based on a generic and methodological approach to set up a CFD simulation. The first principals and reasonings for each setting is discussed at each step. Potential alterations and modifications to perform similar analysis are also addressed and discussed. Hence, in the end user will have the capability of applying potential modifications, improvements or extending the application of the current CFD simulation to a more complex problem of interest, rather than having a one time successful run of a specific simulation with specific and strictly pre-defined boundary conditions.**

> **_In simple words: Current tutorial teaches users to fish, rather than giving them a fish._**

## Setting up a CFD simulation has following four steps:

1. ###### Setup Model/s:   
According to the physics of the flow field user will select required model/s to simulate the flow.

2. ###### Setup Working Fluid/s & Solid/s:   
User will define the physical and thermodynamical properties of the working fluid/s and solid/s in the problem.    

3. ###### Setup Boundary & Zone Conditions:    
Solving the governing equations of the flow (i.e. system of partial differential equations) requires well-defined boundary conditions within the CFD domain. These conditions are selected and defined in this step.

4. ###### Setup Solution Methods:    
In CFD simulations the governing equations of the flow are solve numerically. Based on the physics of the problem appropriate numerical schemes and solution methods are selected at this step.

In the following section the details for the above four steps for the CFD simulation setup for **Laminar Flow in Circular Pipe** are explained in great details. It should be noted that the path for defining conditions and other settings are provided in `command line` format. Users can access exact same settings and options by following the provided path via the tree of progress or pull down menu in ANSYS FLUENT. The summary of the steps to take for CFD simulation setup for problem of 2D laminar flow over a flat plate are as follows:

 1-  `/define/models/steady`   
 2-  `/define/models/solver/pressure-based`    
 3-  `/define/models/viscous/laminar`    
 4-  `/define/material/change-create`    
 5-  `/define/boundary-conditions/fluid`   
 6-  `/define/boundary-conditions/velocity-inlet`    
 7-  `/define/boundary-conditions/pressure-outlet`   
 8-  `/define/boundary-conditions/pressure-outlet`   
 9-  `/define/boundary-conditions/wall`    
 10-  `solve/set/discretization-schem`    
 1- `solve/set/under-relaxation`   
 12- `/solve/initialize/compute-defaults/velocity-inlet`    
 13- `solve/iterate`

Following is the step-by-step explanation for each of the above command/setting procedure:

**1. Setup Model/s:**
* The main assumption in the classical problem of flow over a flat plate is the free stream flow is uniform and steady. Therefore, the steady model is chosen to develop the CFD simulation: `/define/models/steady` (To access this option from pull down menu or tree of progress the path is `/define/general/steady`). In cases where the flow properties rapidly changes with respect to time the Transient model should be chosen in this step.  

* In this problem we are considering that the flow is in subsonic regime. Therefore, variation of density with respect to the pressure can be neglected. As a result of this assumption one can define the working fluid to be incompressible: `/define/models/solver/pressure-based` (To access this option from pull down menu or tree of progress the path is `/define/general/pressure-based`). In cases that the speed of the flow enters sonic and supersonic regimes ( Mach # > 0.3 ), the changes in density (i.e. compressibility) of the flow will be an important factor and the solver must be defined as density-based.

* In the current problem the focus is to investigate laminar flow and boundary layer behavior when a uniform flow passes a flat plate. Therefore, the flow regime is laminar and the appropriate model for that is selected via :`/define/models/viscous/laminar`. It is important to note that the critical Reynolds number for flows over flat plate is about 1.24E+6. In cases that the Reynolds number, based on flat plate length, is grater than this critical value the model will still be viscous, however the appropriate turbulent model should be selected at this step.

**2. Setup Working Fluid/s & Solid/s:**  
* The choice of working fluid is problem dependent. In the absence of obligations to define a specific working fluid such as air or water, it is suggested that the users define the working fluid such that the important non-dimensional groups of interest, such as Reynolds number in this problem, is matched the desired flow conditions. This strategy removes the uncertainty in the choice of the working fluid and will solidify the bases for expected physical observations. For this problem using `/define/material/change-create` command, user can define a new material named reynolds_100 and set the density of it to be 100 [kg/m3] and viscosity to be 1 [kg/m-s]. Based on the 1 [m] length of the flat plate the Reynolds number for this flow field will be equal to 100. In most of the CFD packages, air is defined is the default working fluid. Hence, users should modify the working fluid via this menu using the pre-defined materials or defining a new material with unique physical and thermodynamical properties.

**3. Setup Boundary and Zone Conditions:**    
* In this problem the entire CFD domain is filled with the defined working fluid. This working fluid is selected form the defined material/s in the previous step: `/define/boundary-conditions/fluid`. For this problem users only need to assign the newly defined working fluid, the rest of the options remained unchanged.

* The flow enters from the inlet face of the CFD domain with constant velocity of 1 [m/s] in x-direction. User sets the velocity-inlet condition to the face named inlet by defining the direction and magnitude of the velocity: `/define/boundary-conditions/velocity-inlet`.
In cases where the incoming velocity into the CFD domain is not uniform, one can define the incoming velocity with the pre-defined directions or generate a User Define Function (UDF) to describe the velocity profile of interest.

* In this problem the flow can exit the CFD domain from the outlet face or free surface face and it's pressure will be equal to atmospheric pressure. User sets the pressure-outlet condition to these faces: `/define/boundary-conditions/pressure-outlet`. This simulates the physics of the problem of flow over a flat plate. Thus, it is assumed that gauge pressure at these two boundaries are equal to 0. If in the problem of interest, there exist a specific pressure difference between the inlet and outlet or other surfaces, that magnitude can be defined in corresponding faces of the domain.

* The flow is bounded by the flat plate on the bottom of the CFD domain and interacts with it based on the no-slip boundary condition. User assign the no-slip boundary condition to the bottom of the CFD domain named flat plate via `/define/boundary-conditions/wall` command. If the shear forces and formed boundary layer becomes turbulent in this region user should either provide required mesh resolution to capture the phenomena or set this boundary to free slip condition such that fluid elements would not interact with wall region. For low reynolds number flow, similar to the current problem of interest, a reasonable mesh resolution is sufficient to capture the laminar boundary layer region.

**4. Setup Solution methods:**   
In this step, it is highly recommended to use the default options and settings, unless based on physics of the problem the user is aware of any specific choices. Upon non-smooth convergence and potential divergence of the CFD simulation user can modify and examine various solution methods. To modify the solution methods and controls use the following commands respectively:

`solve/set/discretization-schem`

`solve/set/under-relaxation`

Now all boundary conditions and settings for the CFD simulation are defined. User can **initialize** the solution through an educated guess to start the iteration process: `/solve/initialize/compute-defaults/velocity-inlet`
Solution initialization would incept the flow field variables, such as velocity and pressure, based on the defined values by user. For the current problem the CFD domain is recommended to be initialize by values of velocity and pressure at the pipe's inlet.

Iteration process for solving the flow field governing equation now shall start till converged solution is obtained:`solve/iterate`. A general rule of thumb for converged solution is to have continuity residuals of 10E-3. More details about commenting on validity of solution and convergence criteria will be discussed in next section.
