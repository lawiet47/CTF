In this challenge there is an algorithm for a key. Basically you must find the correct key for a serial they provided us. In this case the serial is 5B134977135E7D13. So open up the exeutable with IDA.
![_keygen_first](https://cloud.githubusercontent.com/assets/27059441/25770791/b20eff64-3247-11e7-9d9a-c0e0b5f610c0.PNG)

![_keygen_fscanf](https://cloud.githubusercontent.com/assets/27059441/25770800/ed6fb724-3247-11e7-9d32-251a45181d96.PNG)


first the program asks us to input the key. Then moves 10h, 20h, 30h values to the stack. Then we scroll down and we see this code

![_keygen_loop](https://cloud.githubusercontent.com/assets/27059441/25770804/0a5881ea-3248-11e7-947e-ab7ab657990a.PNG)

Seems like a loop. So what this loop does is that it compares the esi to the value 3 if it is greater than or equal to 3 it will break. Then it moves some values to the ecx and edx registers we see that the addresses which the values are located in are changing depending on the esi and ebp. If you look at the stack you will see values 10h,20h,30h.

![_keygen_stack](https://cloud.githubusercontent.com/assets/27059441/25771649/2325760c-3260-11e7-8405-61836695c57b.PNG)


Then ecx and the edx are being subjected to xor operation. The ebp and the esi are incremented in order to get the next addresses of the array([10h,20h,30h]). After that, program stores the xor'ed values in some location on the stack.This is the case when we enter AAAAAAAAAAAAAAAA. So for the first 'A' '\x41'xor10='\x51'(in ascii 5=>35 1=>31). The second '\x41'xor20='\x61' and the third '\x41'xor30='\x71'.

![_keygen_stack_serial](https://cloud.githubusercontent.com/assets/27059441/25771678/d1353d4a-3260-11e7-91cd-2f7e55e758a3.PNG)

So we see the values 35 and 31 on the stack.

This it just keeps repeating until there are no more 'A' s left to xor
Then the program jumps to a place where the serial is being asked to input

![_keygen_fscanf2](https://cloud.githubusercontent.com/assets/27059441/25770901/bc3166f0-324a-11e7-8c5e-ea3d3b1abb13.PNG)

After we input the serial the program enters another loop.

![_keygen_loop2](https://cloud.githubusercontent.com/assets/27059441/25770916/febf65f8-324a-11e7-9485-6a5f8d8a3f4a.PNG)

So lets figure out what this loop does. If we look at the bigger picture we see that all this loops does is that it iterates through the xor'ed input and compares each hexadecimal value of the input and the serial.

So now we know that the key must be something which its xor will give us the serial. So the key must be 4B337967336E6D33. When converted to ascii it gives us the value 'K3yg3nm3'.

