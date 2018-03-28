# Udacity flying car - 3d motion planning

![flying drone](./flying.gif)

# Prerequisites for this project

1. Latest version of the Simulator. Link -> [Udacity FCND Simulator](https://github.com/udacity/FCND-Simulator-Releases/releases).

2. Python 3.6

3. [Miniconda](https://conda.io/miniconda.html).


# Starter Code Explanation

Both backyard_flyer_solution.py and motion_planning.py are similar except their path finiding mechanism that the drone using to get to its goal.
In motion_planning.py, we have a new state called Planning. It is being used in state_callback method().
```python
if self.armed:
    self.plan_path()
elif self.flight_state == States.PLANNING:
    self.takeoff_transition()
```
Inside [`state_callback()`](./motion_planning.py#L61-L72) method, we are checking if the drone is armed, if it is, then we are calculatng the waypoints and then we are asking the drone to takeoff. In [`motion_planning.py`](./motion_planning.py)have [`plan_path()`](./motion_planning.py#L114-168) method to calculate the waypoints for the drone to get to the goal, that we are using in this [`method`](./motion_planning.py#L61-L72).

The [`plan_path()`](./motion_planning.py#L114-168) method is using several helper functions defined inside [`planning_utils.py`](./planning_utils.py)file to create the grid, calculating cost and heuristic as well as implmenting A* algorithm to calculate waypoints.


# [`plan_path()`](./motion_planning.py#L114-168) -> Explanation

1. In this method, the grid is created by loading the map from the csv file in ([line 133](./motion_planning.py#L133))

```python
 data = np.loadtxt('colliders.csv', delimiter=',', dtype='Float64', skiprows=2)
```
2. Then we are creating grid using [`create_grid`](./planning_utils.py#L31) method in the planning_utils.py file.

3. Then the starting position is defined as the grid center

```python
 grid_start = (-north_offset, -east_offset)
```
4. Then we set the goal to 10 north and east from our local position

```python
 grid_goal = (-north_offset + 10, -east_offset + 10)
```

5. To find waypoints, we are using [A* Algorithm](./planning_utils.py#L142)

6. Then we send the waypoints to the simulator with [send_waypoints](./motion_planning.py#L109)

# Extended path planning Algorithm

Step 1: First we read the first line from the [collider.csv](./collider.csv) file to get the data and set the home position using read_file method defined in the planning.utils.py file.

```python
def read_file(file):
  with open(file) as fp: 
   line = fp.readline()
   first_line_list = line.split()
   return float(first_line_list[1].replace(',','')),float(first_line_list[3])
```


Step 2: Then we retrive the current position using global_position and convert it to local to set current local position.

Step 3: Then we set the starting position by subtrating the offset from the local position from [line 141](./motion_planning.py#Line141) to [line 147](./motion_planning.py#Line#147)

Step 4: Then we set the global goal position using  set_goal method that is being called inside the main method. It simple returns an array that is passed in the [global_to_local method](./motion_planning.py#L155) in line 155.

```python
  def set_goal(lon,lat,alt):
        array = []
        array.append(lon)
        array.append(lat)
        array.append(alt)
        return array
```

Step 5:  Then we simple use the A* algotrithm to search for possible path.

Step 6: Then we test for colinearity in our path using the function [get_collinear_path](./motion_planning.py#Line6)

Then we simple use the send_waypoints to send data to the simulator and the drone should start to fly!

