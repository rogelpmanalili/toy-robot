Simulate Toy Robot 

A simple program to simulate a toy robot moving on a square tabletop, of dimensions 5 units x 5 units. There are no other obstructions on the table surface. The robot is free to roam around the surface of the table, but must be prevented from falling to destruction. Any movement that would result in the robot falling from the table must be prevented, however further valid movement commands must still be allowed. All commands should be discarded until a valid place command has been executed.


How to install and use:
The program is written using plain HTML and Javascript. No installation is required. Copy/download the file"toy_robot.htm" from Github. Open in any web browser and/or use Console (Developer Tool).


Commands:
All commands should provide output indicating whether or not they succeeded.

PLACE X,Y,DIRECTION
X and Y are integers that indicate a location on the tabletop.
DIRECTION is a string indicating which direction the robot should face. It it one of the four cardinal directions: NORTH, EAST, SOUTH or WEST.

MOVE
Instructs the robot to move 1 square in the direction it is facing.

LEFT
Instructs the robot to rotate 90° anticlockwise/counterclockwise.

RIGHT
Instructs the robot to rotate 90° clockwise.

REPORT
Outputs the robot's current location on the tabletop and the direction it is facing.    