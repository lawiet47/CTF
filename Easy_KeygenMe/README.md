In this challenge there is an algorithm for a key. Basically you must find the correct key for a serial they provided us. In this case the serial is 5B134977135E7D13. So open up the exeutable with IDA.
![_keygen_first](https://cloud.githubusercontent.com/assets/27059441/25770791/b20eff64-3247-11e7-9d9a-c0e0b5f610c0.PNG)

![_keygen_fscanf](https://cloud.githubusercontent.com/assets/27059441/25770800/ed6fb724-3247-11e7-9d32-251a45181d96.PNG)


first the program asks us to input the key. Then moves 10h, 20h, 30h values to the stack. Then we scroll down and we see this code

![_keygen_loop](https://cloud.githubusercontent.com/assets/27059441/25770804/0a5881ea-3248-11e7-947e-ab7ab657990a.PNG)

Seems like a loop. So what this loop does is that it compares the esi to the value 3 if it is greater than or equal to 3 it will break. Then it moves some values to the ecx and edx registers we see that the addresses which the values are located in are changing depending on the esi and ebp. If you look at the stack you will see values 10h,20h,30h are located at address [esp+ebp+13ch+var_12c] and the key we entered is located at the address [esp+ebp+13ch+var_130]. Then ecx and the edx are being subjected to xor operation. Then the ebp and the esi are incremented in order to get the next addresses of the array([10h,20h,30h])
and the input respectively. This operation continues until we xor all of the elements of the input.
Then the program jumps to a place where the serial is being asked to input

![_keygen_fscanf2](https://cloud.githubusercontent.com/assets/27059441/25770901/bc3166f0-324a-11e7-8c5e-ea3d3b1abb13.PNG)

After we input the serial the program enters another loop.

![_keygen_loop2](https://cloud.githubusercontent.com/assets/27059441/25770916/febf65f8-324a-11e7-9485-6a5f8d8a3f4a.PNG)

So lets figure out what this loop does. If we look at the bigger picture we see that all this loops does is that it iterates through the xor'ed input and compares each hexadecimal value of the input and the serial.

So now we know that the key must be something which its xor will give us the serial. So the key must be 4B337967336E6D33. When converted to ascii it gives us the value 'K3yg3nm3'.

