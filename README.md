# Udacity flying car - 3d motion planning

![flying drone](./flying.gif)

# Prerequisites for this project

1. Latest version of the Simulator. Link -> [Udacity FCND Simulator](https://github.com/udacity/FCND-Simulator-Releases/releases).

2. Python 3.6

3. [Miniconda](https://conda.io/miniconda.html).


# Starter Code Explanation

Both backyard_flyer_solution.py and motion_planning.py are similar except their path finiding mechanism that the drone is using to get to its goal.
In motion_planning.py, we have a new state called Planning. It is being used in state_callback method().
```python
if self.armed:
    self.plan_path()
elif self.flight_state == States.PLANNING:
    self.takeoff_transition()
```
Inside [`state_callback()`](./motion_planning_original.py#L61-L72) method, we are checking if the drone is armed, if it is, then we are calculatng the waypoints and then we are asking the drone to takeoff. In [`motion_planning_original.py`](./motion_planning_original.py)have [`plan_path()`](./motion_planning_original.py#L114-168) method to calculate the waypoints for the drone to get to the goal, that we are using in this [`method`](./motion_planning_original.py#L61-L72).

The [`plan_path()`](./motion_planning_original.py#L114-168) method is using several helper functions defined inside [`planning_utils_original.py`](./planning_utils_original.py)file to create the grid, calculating cost and heuristic as well as implmenting A* algorithm to calculate waypoints.


# [`plan_path()`](./motion_planning.py#L114-168) -> Explanation

1. In this method the grid is created by loading the map from the csv file in ([line 133](./motion_planning.py#L133))

```python
 data = np.loadtxt('colliders.csv', delimiter=',', dtype='Float64', skiprows=2)
```
2. Then we are creating grid using [`create_grid`](./planning_utils_original.py#L31) method in the planning_utils.py file.

3. Then the starting position is defined as the grid center

```python
 grid_start = (-north_offset, -east_offset)
```
4. Then we set the goal to 10 north and east from our local position

```python
 grid_goal = (-north_offset + 10, -east_offset + 10)
```

5. To find waypoints, we are using [A* Algorithm](./planning_utils_original.py#L142)

6. Then we send the waypoints to the simulator with [send_waypoints](./motion_planning_original.py#L109)

# Extended path planning Algorithm (My Implementation)

#Rubric point
### In the starter code, we assume that the home position is where the drone first initializes, but in reality, you need to be able to start planning from anywhere. Modify your code to read the global home location from the first line of the `colliders.csv.` file and set that position as global home (`self.set_home_position()`)
Step 1: 

The home position of the Drone is getting read in [motion_planning.py line 128](./motion_planning.py#L128). Here we are using the method read_file [`read_file`](./planning_utils.py#L128-L138) that is defined in `planning_utils.py`.

We are reading [collider.csv](./collider.csv) file to get the data and set the home position using this read_file method defined in the planning.utils.py file.

read_file method defined in planning_utils.py:

```python
def read_file(file):
  with open(file) as fp: 
   line = fp.readline()
   first_line_list = line.split()
   return float(first_line_list[1].replace(',','')),float(first_line_list[3])
```

Step 2: In this step, we transform the coordinates of the Drone using the attibutes defined in [UdaciDrone Api](https://udacity.github.io/udacidrone/docs/drone-attributes.html).
Here we retrive the current position using global_position and convert it to local position using the utility function `global_to_local()` and also set global home using global_home() method. 

The Code for the transformation is defined here.[line 132](./motion_planning.py#L132)

#Rubric point
### In the starter code, the start point for planning is hardcoded as map center. Change this to be your current local position.
#Solution:
Step 3: 
First we define a grid for a particular target altitude and safety margin around obstacles, in this case 5.This altidute and safety margin is our offset that we will use next.

In the starter code, the start point for planning is hardcoded as map center. We change this to be our current local position. 

To do that , We set the starting position by subtracting the calculated offset from the local positions or previously calculated NED positions in Step 2 and we have our starting position in the grid.

This is done here:
[line 146](./motion_planning.py#Line141) to [line 149](./motion_planning.py#Line#147)

#Rubric Point
### In the starter code, the goal position is hardcoded as some location 10 m north and 10 m east of map center. Modify this to be set as some arbitrary position on the grid given any geodetic coordinates (latitude, longitude)
#Solution:
Step 4: To set the goal coordinates, the comman line argument parsing library argparse is used to add parameters like latitude, longitude and altidues in [motion_planning.py](./motion_planning.py#L206-L208) to get the goal coordinates from the user.

```python
   parser.add_argument('--goal_latitude', type=str, help="goal latitude")
   parser.add_argument('--goal_longitude', type=str, help="goal longitude")
   parser.add_argument('--goal_altitude', type=str, help="goal altitude")
```
Then we convert the arguments from string to floats and create an array of floats(np array).
And we set the [goal_position argument](./motion_planning.py#L214) 

Passing goal positions from command line example:
python motion_planning.py --goal_latitude 37.88  --goal_longitude -122.3 --goal_altitude 4

#Rubric Point
### Write your search algorithm. Minimum requirement here is to add diagonal motions to the A* implementation provided, and assign them a cost of sqrt(2). However, you're encouraged to get creative and try other methods from the lessons and beyond!
#Solution:
Step 5: For Diagonal movements, actions are added in the [planning_utils.py]
(./planning_utils.py#L78-L81) file. After adding diagonal movements, the course of the path changes. The [valid_actions](./planning_utils.py#L92-L99) method is modified to add these actions.

Then we simply use the A* algotrithm to search for possible path.

Step 6: We also test for colinearity in our path using the function [get_collinear_path](./motion_planning.py#Line169) defined in [planning_utils.py](./planning_utils.py#L6).

Then we simple use the send_waypoints to send data to the simulator and the drone should start to fly!

