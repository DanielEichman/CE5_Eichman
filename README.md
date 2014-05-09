CE5_Eichman
===========
##Task 1 (5pts all or nothing)
###Assembly Program
```
addi $S0 $0 44 #loads the value 44 into $S0
addi $S1 $0 -37 #loads the value -37 into $S1
add $S2 $S0 $S1 #Adds $S0 and $S1 and stores it in $S2
sw $s2 84 ($0) #this stores the value at $s2 in to memory location 84
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
This makes sense because at the end of the program $S0 should have the value 44, $S1 should have the value -37, and $S2 should have 44 -37 =7. $S0, register 16, has the value 0x2C which is 44.  $S1, register 17, has the value 0xFFDB which is -37.  $S2, register 18, has the value 0x07 which is 7.  
##Task 3 (60pts)
###Schematic Modification
![image](https://raw.githubusercontent.com/DanielEichman/CE5_Eichman/master/Schematic.jpg)

To do a OR immediate function a zero extender is needed to extend the 16 bit immediate value to 32 bits. You want to extend it with zero's because the function is OR and if we extend it with one's then 32:16 will always be true. Because of this there are now three inputs to the ALU and a different MUX will be needed to select the right ScrB. Also the ALUScr will need to be 2 bits long. 
###ALU Decoder Table Modification
![image](https://raw.githubusercontent.com/DanielEichman/CE5_Eichman/master/ALU_Decoder_Table.JPG)
###Main Decoder Modification
![image](https://raw.githubusercontent.com/DanielEichman/CE5_Eichman/master/Main_Decoder_Table.JPG)
###VHDL Modifications
#####Zero Extender 
The first thing you have to do is create a zero extender, as it is needed to properly calculate the OR
```
architecture behave of zeroext is--extends zeros to the last 15 bits
begin
  y <= X"0000" & a; 
end;
```
```
ze: zeroext port map(instr(15 downto 0), zeroexted);
```
#####Four Way MUX
The next thing you have to do is create a 4 way mux, as it is needed to determin SrcB.
```
architecture behave of mux4 is -- this is a four input mux
begin
  y <= d0 when s = "00" else 
		 d1 when s = "01" else
		 d2 when s = "10" else
		 d3;
end;
```
```
srcbmux: mux4 generic map(32) port map(writedata, signimm,zeroexted,"00000000000000000000000000000000", alusrc, srcb);
```
#####Main Decoder
There are two things that have to change with this. First is adding the ORI command. Along with that you must change control signals to meet the requirements. Secondly alusrc was changed from 1 bit to 2. There are many many other locations where you have to redeclare the signal as an STD_LOGIC_VECTOR (1 downto 0) which I will not show. 
```
begin
  process(op) begin
    case op is
      when "000000" => controls <= "1100000010"; -- Rtype
      when "100011" => controls <= "1001001000"; -- LW
      when "101011" => controls <= "0001010000"; -- SW
      when "000100" => controls <= "0000100001"; -- BEQ
      when "001000" => controls <= "1001000000"; -- ADDI
      when "000010" => controls <= "0000000100"; -- J
		when "001101" => controls <= "1010000011"; -- ORI 
      when others   => controls <= "----------"; -- illegal op
    end case;
  end process;

  regwrite <= controls(9);
  regdst   <= controls(8);
  alusrc   <= controls(7)&controls(6);
  branch   <= controls(5);
  memwrite <= controls(4);
  memtoreg <= controls(3);
  jump     <= controls(2);-- way to confuse everyone and make this backwards than in the table
  aluop    <= controls(1 downto 0);
end;
```
#####ALU Decoder
The following line was added to the ALU Decoder so that when 11 is received from the ALUOp the ALU will be set to OR.
```
when "11" => alucontrol <= "001"; -- ORI set ALU to A or B
```
###Testing Waveform
![image](https://raw.githubusercontent.com/DanielEichman/CE5_Eichman/master/Task3.JPG)

It works! After implementing the following instruction:
```
instr <= X"36538000"; --ori $S3, $S2, x8000
```
This ORed the value at $S2 (0007) and the immediate value of (8000) then stored it in $S3 (memory location 19). As you can see from the image the result stored was (8007).
###Documentation
C3C Sean Bapty helped be explain how to edited the schematic and explain why we need a zero extender.
