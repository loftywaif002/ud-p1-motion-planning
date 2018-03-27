#Udacity flying car - 3d motion planning

#Prerequisites to run the project

1. Latest version of the Simulator. Link -> [Udacity FCND Simulator](https://github.com/udacity/FCND-Simulator-Releases/releases).

2. Python 3.6

3. [Miniconda](https://conda.io/miniconda.html).

#Details



#Starter Code Explanation
Both backyard_flyer_solution.py and motion_planning.py are similar except their path finiding mechanism that the drone using to get to its goal.
In motion_planning.py, we have a new state called Planning. It is being used in state_callback method().
```python
if self.armed:
    self.plan_path()
elif self.flight_state == States.PLANNING:
    self.takeoff_transition()
```
Inside [`state_callback()`](./motion_planning.py#L61-L72) method, we are checking if the drone is armed, if it is, then we are calculatng the waypoints and then we are asking the drone to takeoff. In [`motion_planning.py`](./motion_planning.py)have [`plan_path()`](./motion_planning.py#L114-168) method to calculate the waypoints for the drone to get to the goal, that we are using in this [`method`](./motion_planning.py#L61-L72).

#The [`plan_path()`](./motion_planning.py#L114-168) method is using several helper functions that is defined inside [`planning_utils.py`](./planning_utils.py)file to create the grid, calculating cost and heuristic as well as implmenting A* algorithm to calculate waypoints.


#plan_path() -> explanation

