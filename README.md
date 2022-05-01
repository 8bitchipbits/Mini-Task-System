# Mini-Task-System
Mini-Task-System            
Interrupts - made easy       
The Mini-Task-System (hereafter "MTS") is a small Basic extension with which you can run up to 84 small assembler programs at the same time.

The program is available in two versions on the back of this MD: The "MINI-TASK-SYSTEM" file can be loaded normally with ".8" ​​and started with 'RUN'. The MTS basic extension is then active. The second file named "MINI-TASK-SYS.$9" is loaded absolutely, ie with ",8,1". The basic extension is started with SYS36864($9000). No matter which of the two versions you use - after activation, the following four basic commands are also available to you:

1) "←TASK, X, Y, ADR" This installs a new task. The "X" parameter specifies the number of the task that you want to use. Values ​​between 0 and 83 are permitted here. "Y" stands for the type of task. Since the MTS calls the individual programs via interrupt, this value stands for the interrupt to be used for this routine. The following values are permitted:

	0 : Switch off the task. The address is unimportant (but must be specified).
	1 : Task should run in IRQ. Here it is 'linked' to the normal system interrupt, which is called about 60 times a second. A task integrated in this way always runs cyclically, i.e. at regular time intervals.
	2 : Task should run in the NMI. The NMI is a special interrupt that is triggered whenever the 'RESTORE' button is pressed. You can easily z. B. display a help page when this button is pressed (the button is not queried) .
	3 : Task is a BRK interrupt. It is always triggered when the assembler command "BRK" is executed. This is an easy way for you to write an assembly language debugger.

The "ADR" value of the task instruction must contain the starting address of the program that is to be called. Accordingly, you can only integrate assembler programs as tasks (or possibly compiled BASIC programs).

2) "←TIMER, X" This command changes the high byte of the timer value that controls the IRQ. You can use this to change the speed of all tasks that are of type 1.
Values between 0 and 255 are allowed.
The smaller the value, the faster the IRQ tasks, but also the slower the main program. Furthermore, you should not use values ​​that are too small, otherwise the computer may crash.
How small the value must not be depends on the number and duration of the tasks that are to be callesyd. As a guide, however, the valliue should never be less than 10. To restore the normal value, you must specify the value 64 for "X".

3) "←LIST" This command lists all existing tasks on the screen. The task number, the TYPE (IRQ, NMI, BRK) and the start address of the associated program are displayed per line.

4) "←KILL" This command switches off the MTS basic extension. After deactivating the extension, you can `reactivate it at any time with "SYS36864".
36864
HINTS
* In general, you can use any number of tasks of the same type (as long as there are no more than 84 in total). If there are several tasks of the same type, they are called up one after the other. So if you have defined tasks 1, 10 and 20 as IRQ tasks, they will also be executed in this order. If you now want a fourth IRQ task to be called after task 10 and before task 20, you should give it a number between 11 and 19.

* Unfortunately, the "←LIST" command has a small error: If the MTS is started for the first time, it can happen that a series of nonsensical tasks is displayed due to memory garbage (even if they are not executed). If the MTS was previously active, but was switched off and on again in the meantime, the "←LIST" command still shows the previously installed tasks (although they may also no longer be running). To be on the safe side, you should clear the task list using the following loop every time you activate the MTS:
  10 FOR I=0 TO 83                      
  20 ←TASK,I,0,0                        
  30 NEXT                               

* Unfortunately, the "←KILL" command is also incorrect. If it is called, the BASIC extension is removed (the commands are then no longer recognized), but the initialized tasks are still active and continue to run. If you then want to be on the safe side and switch it off, you should load the "MTS-OFF" program from this MD (absolutely, with ",8,1") and call it up using SYS37888(for assembler programmers "JSR $9400"). It resets the interrupt vectors and the timer values ​​to their normal state and thus completely shuts down the running tasks. There is another way, by using the above-mentioned loop to delete the task list, and changing the timer value with the help of the "←

* Finally, a few more technical words: the MTS was created with the program "BE-E"(="Basic Extension Editor") from Magic-Disk, edition 5/92, and can also be expanded with this program (please use version "MINI-TASK-SYS.$9" for this. It should also be noted that the end of the BASIC memory is shifted to $8 FFF (dec.36863) during activation, so that for BASIC programs 4 KB fewer are available. The extension should therefore ALWAYS be called at the very beginning or before the start of the BASIC program, since all previously defined variables will be deleted by the move!

* The memory allocation of the MTS is as follows:
  $9000-$93FF  MTS                      
  $9400-$9423 MTS-OFF (when loaded,
               otherwise unused)         
  $9424-$9EFF unused                
  $9F00-$9FFF Memory for task list  
                                    (ub)
									
10 ←TIMER,200
20 ←TASK,0,1,49152 REM $C000

Example function at 49152
$C000 EE 20 D0 60 | INC $D020 ; RTS
