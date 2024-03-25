Uses two TLS callbacks ([[Thread Local Storage (TLS)]]) and one timing anti-debugging technique.
We can see callbacks and main function in the exports view of ida.
In the main function there is a jump and then the program checks these parameters:
```
1. Get a pointer to the [TEB] (located at fs:18h)
2. Get a pointer to the [PEB] (located at TEB+30h)
3. Check the BeingDebugged flag (located at PEB+2)
4. Check the NtGlobalFlag (located at PEB + 68)
```
It can bypass by using anti debugging plugins for debuggers. (Scyllahide)
The timing anti debugging technique which is used, implemented based on `rdtsc` instruction.
This instruction reads the current time, and stores it in EAX before moving it to EBX.
Then the program performs a few operations before calling `rdtsc` again.
If the time was greater than one second, the program stops.
Also we can see that the code is obfuscated.
We can not see any string in the main executable. But after an XOR, the code de-obfuscates.
Observing de-obfuscated the code, we see that it does a timing analysis again, and checks if the time elapsed is more than 1 second. We can easily circumvent this by changing the value of EAX to 0.
After we have bypassed the final anti-debugging hurdle, this piece of code is ran.
It’s another XOR loop, that XORs the value in EAX with the value 0x4B, and stores the result in the EDI register by calling stosb `STOS/STOSB/STOSW/STOSD–Store String`. Again we put a breakpoint at the end of the program, and allow the loop to complete it execution, before looking at the string residing at the address pointed to by EDI. We right click EDI and follow dump to see the flag!
flag -> Tr0lling_Ant1_D3buGGeR_trickz_R_fun!
submitting flag -> HTB{Tr0lling_Ant1_D3buGGeR_trickz_R_fun!}