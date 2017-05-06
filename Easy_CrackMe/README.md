# Reversing_Kr_Easy_Crackme-Writeup
Download the PE file and open it up with IDA
![_startrev](https://cloud.githubusercontent.com/assets/27059441/25770235/f0550fca-3238-11e7-8d88-a4bd9ef8fe58.PNG)

this is what we see the first time IDA opens.
If we scroll down a little bit we see that the value at [esp+68h+var_63] is being compared to 61h.

![_secondrev](https://cloud.githubusercontent.com/assets/27059441/25770329/6bde7f1c-323b-11e7-81e6-22d30bc5365a.PNG)

61h is hexadecimal for ascii character 'a'. And in this case the value at [esp+68h+var_63] (esp+5h) is the second character of our input. Ok so now we now that the second character must be 'a'. Otherwise the code will jump to loc_401135 in which there is a code that prints Error Message then exits. We dont want that. If we scroll down a bit further we see c function strncmp.

![_secondrevedited](https://cloud.githubusercontent.com/assets/27059441/25770441/234b128a-323e-11e7-970e-2101f89af8a4.PNG)


the parameters for _Strncmp function are pushed to the stack. ecx is containing the address of the start of the 3rd character of our input. We see that 3rd and 4th characters are being compared to '5y'. And then there is another cmp instruction in which dl and bl registers are being compared. So now we see our substring of 2nd to 13th characters must be 'a5yR3versing'. A bit further down there is this code 

![_fin](https://cloud.githubusercontent.com/assets/27059441/25770481/2d28931c-323f-11e7-84be-b205ef27255c.PNG)

which compares the first character of our input to 45h which is hexadecimal value of ascii character 'E'. Then it prints the string "Congratulations!!!". So we now know that the flag is 'Ea5yR3versing'.


![flag](https://cloud.githubusercontent.com/assets/27059441/25770500/bec8ac9e-323f-11e7-9703-7db86844ff6b.PNG)



reversing.kr Easy_Crackme Challenge
