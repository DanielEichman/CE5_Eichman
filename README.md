CE5_Eichman
===========
##Task 1 (5pts all or nothing)
###Assembly Program
```
addi $S0 $0 44 #loads the value 44 into $S0
addi $S1 $0 -37 #loads the value -37 into $S1
add $S2 $S0 $S1 #Adds $S0 and $S1 and stores it in $S2
sw $s2 84 ($0) #this stores the value at $s2 in to memory loaction 84
```


##Task 2 (35pts)
###Machine Code
```
addi $S0 $0 44 #0x2010002C
addi $S1 $0 -37 #0x2011FFDB
add $S2 $S0 $S1 #0x02119020
sw $s2 84 ($0) #0xAC120054
```
###Waveform
![image](https://raw.githubusercontent.com/DanielEichman/CE5_Eichman/master/Task2.JPG)
###Signal Descriptions
This makes sense because at the end of the program $S0 should have the value 44, $S1 should have the value -37, and $S2 should have 44 -37 =7. $S0, registar 16, has the value 0x2C which is 44.  $S1, registar 17, has the value 0xFFDB which is -37.  $S2, registar 18, has the value 0x07 which is 7.  
##Task 3 (60pts)
###Schematic Modification
![image](https://raw.githubusercontent.com/DanielEichman/CE5_Eichman/master/Schematic.jpg)
To do a OR immediate function a zero extender is needed to extend the 16 bit immediate value to 32 bits. You want to extend it with zero's because the function is OR and if we extend it with one's then 32:16 will always be true. Because of this there are now three inputs to the ALU and a different MUX will be needed to select the right ScrB. Also the ALUScr will need to be 2 bits long. 
###ALU Decoder Table Modification
![image](https://raw.githubusercontent.com/DanielEichman/CE5_Eichman/master/ALU_Decoder_Table.JPG)
###Main Decoder Modification
![image](https://raw.githubusercontent.com/DanielEichman/CE5_Eichman/master/Main_Decoder_Table.JPG)
###Functionality (40 pts)
###VHDL Modifications
###Testing Waveform
