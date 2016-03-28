# CFD Simulation Case Setup

**The created CFD domain is now read into the CFD package of interest to setup the CFD simulation. It should be noted that the current tutorial has a significant difference compared to other available CFD tutorials online! This tutorial is structured and developed based on a generic and methodological approach to set up a CFD simulation. The first principals and reasonings for each setting is discussed at each step. Potential alterations and modifications to perform similar analysis for different flow conditions are also addressed and discussed. Hence, in the end user will have the capability of applying potential modifications, improvements or extending the application of the current CFD simulation to a more complex problem of interest, rather than having a one time successful run of a specific simulation with specific and strictly pre-defined boundary conditions.**

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

In the following section the details for the above-mentioned four steps of the CFD simulation setup process for the case study of **2D Laminar Flow Over a Flat plate** are explained in great details. As a general guideline, to setup a CFD simulation in OpenFoam it is recommended to start with files from one of the provided default tutorials in OpenFoam located in `OpenFoam/run/tutorials`. Users should find appropriate tutorial such that the general physics of the chosen tutorial is close to the problem of interest of users. This way, the majority of the required physical settings are pre-set and the others can be changed/modified accordingly. For example for the problem of **2D Laminar Flow Over a Flat plate** the tutorial of **2D over an airfoil** located at `OpenFoam/run/tutorials/incompressible/simpleFoam/airFoil2D` is used. Using the previously developed file, the above-mentioned four steps for CFD simulation development is done via editing the corresponding files, where the settings are defined and are located in the main working directory.

**1. Setup Model/s:**

The main assumption in the classical problem of a two dimensional flow over a flat plate is that the free stream flow is uniform and steady. Therefore, the steady model is chosen to develop this CFD simulation. This along with other numerical schemes settings are set in a dictionary file called "fvSchemes" located at `\system` folder. To set the solver to be steady users define `steadyState` for the `ddtSchemes` and the rest of the settings will remain unchanged:

```C++
ddtSchemes
{
    default         steadyState;
}
```
In cases where the flow properties rapidly changes with respect to time the Transient model should be chosen in this step. Various available time schemes along with other implementable option within "fvSchemes" dictionary file in OpenFoam can be found [here](http://cfd.direct/openfoam/user-guide/fvschemes/).

In the current problem the focus is to investigate laminar flow and boundary layer behavior when a uniform flow passes over a flat plate. The appropriate model for this problem is set in a dictionary file called `RASPropertise` (RAS stands for Reynolds Averages Stress) located at `\constant` folder:

```C++
RASModel        laminar;

turbulence      off;

printCoeffs     off;
```

It is important to note that the critical Reynolds number for flows over flat plate is about 1.24E+6. In cases that the Reynolds number, based on flat plate length (as the length scale), is greater than this critical value the model the appropriate turbulent model should be selected at this step. Various available turbulence models along with other options within `RASPropertise` dictionary file in OpenFoam can be found [here](http://cfd.direct/openfoam/user-guide/turbulence/).

**2. Setup Working Fluid/s & Solid/s:**  

The choice of working fluid is problem dependent. In the absence of obligations to define a specific working fluid such as air or water, it is suggested that the users define the working fluid such that the important non-dimensional groups of interest, such as Reynolds number in this problem, is matched the desired flow conditions. This strategy removes the uncertainty in the choice of the working fluid and will solidify the bases for expected physical observations. The material properties in OpenFoam can be defined in a dictionary file `transportPropertise` located in `constant` folder. In this file the material is set to be `Newtonian` fluid with a defined kinematic viscosity **\nu**. It should be noted that the numbers in the [] set the dimension of the flow field variable of interest and the other number sets it's value:

```C++
transportModel  Newtonian;

nu              nu [ 0 2 -1 0 0 0 0 ] 0.01;
```
Considering that the flat plate length in 1 [m] and user will define a unit constant uniform velocity (1 [m/s]), the value of **\nu** will be defined as the inverse of Reynolds number. Therefore, changing this value would set the nominal Reynolds number of the flow based on the flat plate length. For other cases users should define the working fluid via this process. Various available transport models along with other options within `transportPropertise` dictionary file in OpenFoam can be found [here](http://cfd.direct/openfoam/user-guide/transport-rheology/#x40-2210007.3.1).

**3. Setup Boundary and Zone Conditions:**   
In OpenFoam the type of boundaries within the CFD domain are defined in the `boundary` file located at `\constant\polyMesh`. Whether the users use blockMesh or the convert command to create the CFD domain and discretize the `boundary` file will be created in this process. Furthermore values for the field variables, such as velocity **U** and pressure **p**, at these boundaries are set in `U` and `p` files located at `0` folder located in the working directory respectively. The `0` folder includes the values of the field variables at boundaries for the time `t=0`.

As discussed earlier in the CFD domain's creation and discretization section, the CFD domain has four faces as boundaries. The boundary and zone conditions settings to develop CFD simulation for **2D Laminar Flow Over a Flat plate** case in OpenFoam are as follows:

**Inlet:** The flow enters from the inlet face of the CFD domain with constant velocity and uniform atmospheric pressure (i.e. zero gauge pressure). To set the type of this boundary edit `boundary` file in `\constant\polyMesh`. Set the type of face to `patch`, which is a condition that contains no geometric or
topological information about the mesh:

```C++
inlet
{
    type            patch;
    nFaces          100;
    startFace       20100;
}
```
Set the field variables at this boundary edit `U` and `p` files in `\0` folder to a constant uniform velocity in the streamwise direction (x-direction) with zero pressure gradient:.

```C++
// In U file at 0 folder:
inlet
{
    type            fixedValue;
    value           uniform (1 0 0);
}

// In p file at 0 folder:
inlet
{
    type            zeroGradient;
}
```

**Outlet and Top Surface:** The flow exits the CFD domain from the outlet and top face of the domain and reach atmospheric pressure (i.e. zero gauge pressure). To set the type of this boundary edit `boundary` file in `\constant\polyMesh` and set the type of face to `patch`, which is a condition that contains no geometric or topological information about the mesh:

```C++
outlet
{
    type            patch;
    nFaces          100;
    startFace       19900;
}

free_surface
{
    type            patch;
    nFaces          100;
    startFace       19800;
}
```
Set the field variables at this boundary edit `U` and `p` files in `\0` to a constant uniform atmospheric pressure with zero velocity gradient:

```C++
// In U file at 0 folder:
outlet
{
    type            zeroGradient;
}

// In p file at 0 folder:
outlet
{
    type            fixedValue;
    value           uniform 0;
}

// In U file at 0 folder:
free_surface
{
    type            zeroGradient;
}

// In p file at 0 folder:
free_surface
{
    type            fixedValue;
    value           uniform 0;
}
```
If in a problem of interest, there exist a specific pressure difference between the inlet and outlet or other surfaces, that magnitude can be defined in corresponding faces of the domain.

**Flat Plate:** The flow is bounded by the flat plate on the bottom of the CFD domain and interacts with it based on the no-slip boundary condition. To set the type of this boundary edit `boundary` field in `\constant\polyMesh` and set the type of face to `wall`:

```C++
flat_plate
{
    type            wall;
    inGroups        1(wall);
    nFaces          100;
    startFace       20000;
}
```
Set the field variables at this boundary edit "U" and "p" files in `\0` to a fixed value of zero velocity with zero pressure gradient, which represents the fundamental assumptions of the uniform laminar flow over a flat plate:

```C++
// In U file at 0 folder:
flat_plate
{
    type            fixedValue;
    value           uniform (0 0 0);
}

// In p file at 0 folder:
flat_plate
{
    type            zeroGradient;
}

```

If the shear forces and formed boundary layer becomes turbulent in this region user should modify this boundary conditions and provide required mesh resolution to capture the phenomena or set this boundary to free slip condition such that fluid elements would not interact with wall region. For low reynolds number flow, similar to the current problem of interest, a reasonable mesh resolution is sufficient to capture the laminar boundary layer region.

Since this problem is two dimensional the boundary type and flow field variables for the `frontToBack` boundary, which defines the third dimension of the flow field are set to `empty`:

```C++
frontAndBackPlanes
{
    type            empty;
    inGroups        1(empty);
    nFaces          20000;
    startFace       20200;
}

// In U and p files at 0 folder:
frontAndBack
{
    type            empty;
}
```

For more detail on definitions and settings for defining CFD domain boundary conditions in OpenFoam readers are referred to [here](http://cfd.direct/openfoam/user-guide/boundaries/).

**4. Setup Solution methods:**   
In this step, it is highly recommended to use the default/suggested solution methods set in `fvSolution` file located in `\systems`, unless based on physics of the problem the user is aware of any specific choices. Upon non-smooth convergence and potential divergence of the CFD simulation user can modify and examine various solution methods.

The `fvSolution` file includes the solution methods for velocity and pressure fields and the required under relaxation factors for flow field variables.

Now all boundary conditions and settings for the CFD simulation are fully defined. User can **initialize** the solution through an educated guess to start the iteration process. In OpenFoam the initialization of the velocity and pressure fields is set on top of the `U` and `p` files located in the `0` folder:

```C++
// In U file at 0 folder:
internalField   uniform (1 0 0);

// In p file at 0 folder:
internalField   uniform 0;
```
The solution initialization would incept the flow field variables, such as velocity and pressure, based on the defined values by user. For the current problem the CFD domain is recommended to be initialize by values of velocity and pressure at the inlet.

Iteration process for solving the flow field governing equation now shall start till converged solution is obtained. In OpenFoam the iteration can initiated by running a command line using the name of the application of choice. The name of the application and set marching time steps to solve the flow field governing equations  are set in `controlDict` dictionary file located in `\system folder`:

```C++
application     simpleFoam;

startFrom       startTime;

startTime       0;

stopAt          endTime;

endTime         10;

deltaT          0.005;
```
For this problem a 10 seconds time interval is set with time steps of 0.005 seconds. The outputs will be written every 1 second till either the end of time interval or set tolerances for the residual of field variables are reached. The iterations can be started by running the command `simpleFoam`.
