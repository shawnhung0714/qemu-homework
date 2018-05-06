# Virtual Machine HW1 Report

## Student Info

- name: 洪軒治 
- ID:P06922004

## Assignment 1

- Building Step

```bash
git checkout Question1
./configure --target-list=aarch64-linux-user --enable-debug-tcg --enable-debug
make && sudo make install
qemu-aarch 64 vadd-vm
```

- Output implementation
> As described in the summary document provided by TA, I applied qemu_log() to create the whole output area at do_syscall():syscall.c.

- Result storage preparation
> In order to save data to present in do_syscall():syscall.c, I created three GHashTable properties just in CPUARMState:target/arm/cpu.h.

- Disable Block chaining of requested blocks
> If Block chaining is still enable, inserting codes in the tb_find() or cpu_exec() will not be able to intercept the designated blocks to do calculation. The computer will execute blocks continuously without returing to VMM area.

- To find targets from a designated block is jumped to
> I put a **if block** to intercept translation block generation cpu_exec() : cpu-exec.c so as to wait the designated block is requested. If the block is requested, raise a flag **needRecordTarget**. After that, the next block will be record and count. Finally, save relevent data into GHashTable mentioned above.

- To get entries of a basic block
> The basic block can be intercepted in tb_find() function with the **last_tb** variable. Record this variable, do counting, and savw them into GHashTable output area.

- To calculate frequency whether a conditional branch is taken
> Similarly, the code can be put at tb_find(), so that branch direction can be detected. To understand which block will be the next, the easiest way is to see if current tb is the sussesor of the designated branch. Next, to identify whether current tb is on the **taken** can be done by decode of tb_exit from jmp_list_first property. After that, if this tb is **taken**, the tb_exit will be TB_EXIT_IDX1, so as to **not-taken** situation.

## Assignment 2

- Building Step

```bash
git checkout Question2
./configure --target-list=aarch64-linux-user --enable-debug-tcg --enable-debug
make && sudo make install
qemu-aarch 64 encr-vm
```

- Solution
>Although the encryption is rather easy to identify, by obseration of ascii codes of cipher and plain text, it is still difficult to put decryption in assembly level.
>
>After disassembling the binary file with Hopper Disassembler, I found it is quite easy to bypass the encryption by modifying the parameter of **encry** function.
>
>Therefore, I put some code to intercept instruction traslation to address **0x400788**, defining the value assignment of the second parameter of **encry** function, so that no encryption will be executed, and the output will be equal to the input.