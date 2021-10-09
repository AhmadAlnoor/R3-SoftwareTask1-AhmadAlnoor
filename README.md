# R3-SoftwareTask1-AhmadAlnoor
Repository of R3 Software Task 1

Introduction:
The purpose of this assignment is to use an arduino which takes the analog values from a 10 kilo-ohms potientiometer and outputs a value between 0 to 99 using 2 7-segment LEDs. 

Components:
This project makes use of the following components:

1	 Arduino Uno R3
1	 10 kilo ohms potentiometer
2	7-Segment Decoders
2	Cathode 7 Segment Display
16	1 kilo ohms resistors

Components set up:
-Potentiometer
The potentiometer has 3 pins, Ground, VCC, and output voltage pin to read the voltage. The Ground and the VCC pins are connected to Arduino's ground and VCC pins and the output pin is connected to Arduino's analog pin A5 to read the value of the potentiometer. Arduino will convert the analog voltage recieved from the potientiometer and convert it to values between 0 and 1023 with 0 being potentiometer at 0 and 1023 being potentiometer at 10k. 

- 7-segment display (Cathode)
The 7-segment display can output numbers between 0 and 9 by lightining up the appropriate LEDs. The 7-segment display is controlled by the 7-segment decoder which makes the appropriate LEDs HIGH and LOW to display the appropriate numbers. The 7-segment display contains 7 LEDs. The LEDs are labelled from a-g. Each LED has a 1 kilo ohm current limiting resistor connected to it so that the LEDs are not overloaded with excess current. The 7-segment LED selected for this project is a cathode  7-segment display which means the LEDs share a common ground. The COMM (ground) pins of the 7-segment display are connected to the arudino ground in order to make the 7-segment display function.

- 7-segment Decoder
The 7-segment decoders has 14 pins in total. The 7-segment decoder takes 4 inputs and turns on the appropriate LEDs on the 7-segment display. The decoder also has an Enable pin (PIN 5) which must be set to LOW in order to make the decoder function normally. When the decoder has the following pin out:

PIN 1,2,6,7 	Data input pins 1,2,3,0 respectively
PIN 3		Lamp Test-> Turns on on all segments when LOW, normal operation when HIGH
PIN 4		Blanking test -> Turns off all segments when LOW, normal operation when HIGH
PIN 5		Latch Enable -> Stores the cuurent state when HIGH, normal operation when LOW
PIN 8		Ground pin
PIN 9 -15 	Outputs for the 7-segment display
PIN 16		Supply voltage pin

Below shows the input sent to the 7-segment decoder and the respective output to the 7-segment display

Input to 7-segment decoder			7-segment display output
0000						0		
0001						1
0010						2
0011						3
0100						4
0101						5
0110						6
0111						7
1000						8
1001						9
from 1010 to 1111					NP
In this project, 2 7-segment decoder were used to control 2 7-segment displays.
The first 7-segment decoder controlled the ones decimal number and second controlled the display that output tens decimal number.

-Arduino Uno R3
The Arduino Uno R3 is the brain that controls the whole operation. The following code was uploaded to the Arduino that takes anlog values from potentiometer, converts it between 0 to 1023, determines a linear relationship between the analog values 0 to 1023 and the 2 7-segment display values 0 to 99, and sends appropriate values to the appropriate 7-segment decoders to makes the 7-segment display output correct numbers.

Code:
The code makes use of the following libraries and variables:
#include <stdint.h>     		To declare integers with exact size for memory management

uint16_t pot_value;			Unsigned 16-bit integer variable of size 65,536 that is used to store values between 0 to 1023 recieved from the potentiometer

uint8_t pot_pin = A5; 		Unsigned 8-bit integer variable that stores the Pinout of the potentiometer

uint8_t left_segment_pins[] = {9,10,11,12};  Pinouts of where the data input pins of the left 7-segment decoder is connected to Arduino pins

uint8_t right_segment_pins[] = {5,6,7,8}; Pinouts of where the data input pins of the right 7-segment decoder is connected to Arduino pins
 
uint8_t right_data_input = 4;	
uint8_t left_data_input = right_data_input;  Number of data input pins used in this project

uint32_t pot_to_seg;		Conversion variable that stores 7-segment numbers between 0 to 99 after converting it from potentiometer values

uint8_t seg_to_binary;		Conversion variable that stores binary version of the 0 to 99 7-segment values


uint8_t right_count; 		Keeps track of the ones decimal place number

In the void setup():
-pot_pin is set to INPUT so that Arduino constantly reads from the potentiometer
-left_segment_pins[]  and right_segment_pins[] are set as output so that left 7-segment decoder and right 7-segment decoder can be written to.
-left_segment_pins[]  and right_segment_pins[] are set to LOW so that both 7-segment display output 00 at the start.

In the void loop()
The first line of code in the infinite loop reads the value from the PIN that the potentiometer is connected to. Then, the value is stored in pot_value.
Next the equation:
	(99/1023)*pot_value was used to determine a linear relationship between the 0 to 99 7-segement values and 0 to 1023 pot values. The equation was determined as below:
Since linear equation has the form :	y = mx + b
where m is the slope and b is the y-intercept, y is the 7-segment values (0 to 99) and x is the potentiometer read value (0 to 1023)
In this example, b = 0 since the relationship starts at y = x = 0
The slope "m" is determine     m = (y2 - y1) / (x2 - x1)  = (99 -0 )/ (1023 - 0) = 99/1023 = m
Therefor, the linear relationship between the two values is:
	y = 99/1023*x  or  pot_to_seg =  (99/1023)*pot_value

Now that we have converted the potentiometer values to the 7-segment values, we must now program the 7-segments appropriately
The right segment displays the ones value between 0 and 9. If the value becomes greater than 9 then the right segment must be reset to zero and the left segment
must display the tenth number.
To program the right segment, the remainder of the 0 to 99 must be taken. Since we are interested with only the values of 0 to 9, taking the 10 remainder helps us achieve this.
For example: if we recieved a potentiometer value that was converted into 78, then, 78 % 10 = 8 which is the value that must be displayed to the right segment.
This value is stored into right_count which is then converted into binary. Arduino has a built-in function bitRead(decimal,i) that reads the decimal number and only outputs the i-th bit
For example bitRead(8,3) will first convert 8 to 1000 and then output the "0" to the right of the "1". 
Now since we need to send 4-bits to the decoder to turn on the appropriate LEDs in the 7-segment display, a for-loop is used to first convert the decimal value to binary one bit at a time and then write to the digital pin after the bit has been converted.

Similarly, the left segment is programmed the same way as the right segment. Except the converted value from the potentiometer is divided by 10 to get the tenth value.
For example : Taking the above 78 as an example once again, 78/10 = 7 which is the value that we need to program the left segment.
The for-loop once again is used to convert the decimal after the division to binary value and send the i-th bit of the binary value to the i-th bit of the 7-segment decoder.

The above steps are repeated until the microcontroller is disconnected.
 tinkerCad link: https://www.tinkercad.com/things/585y8oRRCAH-r3-softwaretask1-ahmadalnoor/editel

