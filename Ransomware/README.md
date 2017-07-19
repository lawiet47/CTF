Today i will examine the ransomware ctf. After we download and extract the .zip file 3 files will pop out.
a 1) readme file telling that we need to decrypt the .exe file in the attachment 2) The file we need to decrypt in order to get the
flag 3) The ransomware program.

![key](https://user-images.githubusercontent.com/27059441/28386822-7ff4592a-6cd5-11e7-8e96-5bd14a0b022e.PNG)

After executing it we see that it prints some sort of message, asks for a key(possibly the encryption key used) and then prints
some more text and exits.

After we examine the ransomware.exe file we see that it is an x86(32bit) portable executable(PE)
file. If the take a look at the contents of the program we see that it is being packed with UPX. We can see that by dumping
hex contents of the file with dumpbin.exe(a tool proviede by visual studio). 

![disasm_dump](https://user-images.githubusercontent.com/27059441/28386412-227bae2a-6cd4-11e7-939c-7c06905413ba.PNG)

Normally we would see the .text .textbss .rdata etc sections (the sections that are supposed to be in an executable image file).
After we unpacked the file if we look at the hexdump we would see the normal sections.

![dumpbin_entrypoint](https://user-images.githubusercontent.com/27059441/28386523-88659eee-6cd4-11e7-8663-709f44f03c26.PNG)

Here we see the EntryPoint of the program. After that we load up the program to the x32dbg(a handy disassembler and a decompiler like IDA).

![x32entrypoint](https://user-images.githubusercontent.com/27059441/28386685-fcbdd9e6-6cd4-11e7-9a4e-e5ea1bb7ab01.PNG)

We can see the entry point in the x32dbg too. After we walk through the disaasmbly we see the part of the program that asks us for a key

![keystring](https://user-images.githubusercontent.com/27059441/28386947-e5605c50-6cd5-11e7-909a-8d08a9807e18.PNG)

After scrolling down a bit we see that program opens the sample file for reading

![fopen](https://user-images.githubusercontent.com/27059441/28387009-1df952ba-6cd6-11e7-95d5-b68fef12c4f9.PNG)

Gets the size of the file using fseek()(calling it with SEEK_END(0x2) parameter) and ftell() functions. And then sets the position of the stream
to the beginning of the file.

![getsizeandrewind](https://user-images.githubusercontent.com/27059441/28387104-74bd5128-6cd6-11e7-9a50-a50a658d7e22.PNG)

If we scroll down we see that the program reads the file 1 byte at a time until it reaches the end (checked with feof())and then stores it in some address

![filereadloop](https://user-images.githubusercontent.com/27059441/28387336-4d651830-6cd7-11e7-87ef-40bd5d154014.PNG)

Down below we see the encryption part taken place. The program gets 1 byte from the address it stored the file's bytes in, XORs it
with the byte from the key and XORs the result with the 0xFF.

![enc_loop](https://user-images.githubusercontent.com/27059441/28387576-4f243696-6cd8-11e7-8054-7478d1d32bd6.PNG)

And it stores the final Result in an address.(kinda like bytes[i]=bytes[i]^key[i]^0xff).

![writefile](https://user-images.githubusercontent.com/27059441/28387675-ac352fc0-6cd8-11e7-8145-0f3ee07be007.PNG)

And here it opens the file for writing and begins writing the resulted bytes from the XOR process to the file.

NOW, if we sum it up, the program takes a key reads its bytes to memory, opens the file reads its bytes to memory, XORs the
file bytes the key bytes and the byte 0xFF, writes it back to the file. Now we know what the encryption(and the decryption because it is XOR therefore its symmetric meaning the encryption and the decryption key is the same) is.
Therefore we can decrypt it all we need to do is write a simple program that does exactly the same except the key part in the XOR process will be replaced by the encrypted bytes from the file.
It does not make difference because it is XOR(0x41^0x4d=0xc ==> 0x41^0xc=0x4d). We know that the encrypted file is .exe file.
And PE file's header's first few 100 bytes is taken up by the MS-DOS stub this is a small program embedded in every PE file to print the message "This program cannot be run in DOS mode".(In case if the PE file executed in an incompatible system).


![ms-dosstub](https://user-images.githubusercontent.com/27059441/28388191-912ee25a-6cda-11e7-808b-c0ac7ff913cb.PNG)

This is the unencrypted header of a PE file.

![ms-dosstub enc](https://user-images.githubusercontent.com/27059441/28388215-a6476360-6cda-11e7-8b2b-c7992c6d3c4e.PNG)

And this is the encrypted one. So if we XOR the bytes in these areas(Any area of the MS-DOS header could be chosen. I chose this because its readable) and 0xFF we should get the key.

![find_key_code](https://user-images.githubusercontent.com/27059441/28388360-2dce6108-6cdb-11e7-97fd-6c30351ba09e.PNG)

When executed the program gives us this output:

![default](https://user-images.githubusercontent.com/27059441/28388431-629220a0-6cdb-11e7-853c-88d3b712ac39.png)

The key is letsplaychess. It repeats because it is smaller than the size of the MS-DOS header message. When we run the ransomware.exe program again and pass this key to the input,
 we decrypt the file. When we check the new decrypted file for strings and search for the ones that interests us we get something like this
 
 ![flag](https://user-images.githubusercontent.com/27059441/28388734-5684b48e-6cdc-11e7-8754-961044976265.PNG)

The flag is Colle System.




