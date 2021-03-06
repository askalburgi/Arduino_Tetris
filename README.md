Arduino_Tetris
==============

My Arduino project for my CMPUT 274 class. 

The project wasn't actually just me, my partner Zhengyang Li should get some credit as well. 

Below is the README we submitted to the course markers.

++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
INTRODUCTION

Creating ~Tetris was a good project. They say that good projects dedicate proper time for planning and
implementation in addition to writing and testing. We think that we did that really well.
The majority of our planning was with how we were going to enable the Arduino to "see" the board and how
we should represent or store the data.

	grid.
	grid is a 2 dimensional array (implemented on line 29,) of length 20 (rows) and 16 (columns)
	that keeps track of where blocks are so that new blocks will not overlap. grid is always
	updated along with the display from printblock() and removeblock(). In the case of killrow(),
	where a row is filled and removed, the grid is computed and drawn separate of printblock()
	and removeblock(), and drawboard() then takes care of updating the display. grid was an
	extremely beneficial way to keep track of where blocks can go as well. This can be seen in
	the down(), left(), right() and turnblock() functions. These are explained furthur below,
	but essentially they check if the block can move to the next state and only allow that
	movement if it is possible. We are really pleased with this implementation of keeping track
	of the board. It does have it's down falls however. This is mentioned below in the issues section.

	block struct.
	The block struct has four components: row, col, rotation and type. rotation and type are of
	type int. rotation has bounds [1,4], one for each of the possible four rotations of any given
	block. type has bounds [1,7], one for each of the possible seven blocks: square (1), L (2),
	mirrored L (3), steps right (4), steps left (5), four-long (6) and T (7). row and col are of
	type char* (arrays) each of length 4. This is because each type of block has four components.
	We created a system where each component, of the four, within each block is numbered, the rows
	and columns of these parts are stored in the arrays. The numbers that each component are labelled
	to match the index of the array. The documentation for the labelling can be seen in the attached
	.jpg file. We thought this was a really good way to store the data of our blocks. Everytime down(),
	left(), right() or turnblock() is ran, the values in the two arrays can be manipulated and then
	we can check the grid with them. The issues with this implementation are also in the issues section.

Description of the major functions can be found below the instructions on how to play and how to make.
Sorry if it's too descriptive by the way, we thought it's better to write more for nothing that less for
something. At the very bottom, you can find explaination of the attaching picture files. 
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++


++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
INSTRUCTIONS - How to Play

The game is very simple to play, it's Tetris. When you load up the game you are met with a splash screen.
Once you start the game you are to use the buttons to play ~Tetris. There is a button for making the 
block go left, one for right and another to make the block turn. Try to fill as many rows as possible! 
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++


++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
INSTRUCTIONS - How to re-Make

If you want to remake this code, it is important that you get the logic down first and formost. Mentioned
breifly above, the grid and block struct are essential in determining how much you can confuse yourself
while working on this. If you have good logic - it is easy, if you don't - it isn't. Here is the process
we used for building this project. 

	1. Plan out the grid 
	2. Plan out the blocks and block struct
	3. Plan out loop()
	4. Write and test a simple program where one type of block appears and falls, make sure they stack
		This program should update the grid and the display
	5. Add a function that checks if the stack got to the top to that program, display a splashscreen
	6. Add a left button and function, without adding different kinds of blocks yet
	7. Add a right button and function, again without adding different kinds of blocks 
	8. Add code to the earlier check function in step 5 that checks if any rows are filled
		Add a function to remove such rows and update the display
	9. Add all the other blocks. 
	10. Add a turn button and function
	11. Add extra's such as difficulty and proper start and end splashscreens
	
It is important to note that you must test in between each step. Especially after steps 4, 6, 7 and 10. 
These are where the most bugs arose. All the wiring for the buttons and display is standard. Pictures 
on how the blocks were planned, the wiring and how the grid was used that are also found in the folder
is explained at the end. 
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++


++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
FUNCTIONS EXPLAINED - file: test.cpp

grindprinter():
	Prints the entire grid to the Serial Monitor
		This function was used as a testing function for debugging. It allowed us
		to count which row and column the blocks are in, because it is impossible
		to do it on the display.

blockprinter():
	Prints all the data stored in the block to the Serial Monitor
		This function allowed us to check the block before and after it was updated,
		this way we can see where the error in the update was. This was extremely
		useful when testing turnblock() since each case updates a different way,
		so much of it was wrong.

rowprinter():
	Prints the row of the grid that is passed in to the Serial Monitor
		This function wasn't as useful as the two above but again allowed us furthur
		see what we needed.

All of the above were a big help for debugging, and there were plenty of bugs.
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++


++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
FUNCTIONS EXPLAINED - file: checkingfunctions.cpp

checkrow():
	Checks if the game should be ended and if rows need to be removed
		This function runs everytime a block cannot move down anymore, i.e. whenever
		down() returns false. It then runs a loop checking the grid for anything
		in the top row (row[0]), if there is anything then it will run endgame().
		Otherwise it will run through all the rows checking for a filled one, calling
		killrow() if it finds something. If it does call killrow(), it also updates
		the score and sets rowkilled to 1 - which leads to the eventual calling of the
		drawboard() function. There are a few things that need to be explained here.
		Firstly, why is there a need for two separate sections of loops, one for
		checking row[0] and one for the grid? After implementing the second section
		of checkrow() a strange bug appeared that we couldn't figure out. The if
		statement checking if the spot of the grid is filled would never return true.
		To fix this, we just separated it. Secondly, why is there a need for rowkilled?
		This is due to the case when multiple rows need to be taken out, we don't want
		to call drawboard() multiple times (once for each full row). So instead we
		created rowkilled and have it tell us if we need to draw the board, this way
		we can call it at the end.

endgame():
	Creates the end game screen and waits for button press to play again
		This function is pretty clear, took a while to figure out where we had to
		set the curser for each line though. Here (as well as everywhere we want
		to call the grid) the fact that score was kept as a global variable was really
		useful. We didn't have to pass anything!

killrow():
	Copies all the rows down starting from the row that is passed in
		This function requires the use of memcpy() which is in the string.h library.
		We originally foundtried to do it by setting the array's equal to each other
		but C doesn't allow that. We didn't want to run for loops because we wanted
		the process to be quick so that users wouldn't experience an awkward lag.

drawboard():
	Draws grid to the display
		This function runs on two loops and prints Black and White squares. Pretty
		simple. Originally we were worried about the O(n^2) runtime but it worked
		pretty quickly so we left it. Plus we don't know a quicker way to do it.

getRdm();
	Creates a random number of [1,7] and returns a block with the type of that number
		This function is also very obvious So we'll tell a story instead, we originally
		didn't want to return a block. Now that we understand memory locations and stuff
		passing bigger types seems elementary. But there were some scope issues that we
		didn't understand coming from loop() so eventually we just went back to the way
		it is now.

Most of the above are called through checkrow, hence the name checkingfunctions.cpp
The outlier is getRdm(), but we didn't want to leave it in the main file or give it it's own.
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
