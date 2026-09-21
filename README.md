--------------------------------------------------------------------------------
cFS Message Sender Practice
--------------------------------------------------------------------------------

This is a short & simple practice task for cFS. Included is the program
"msg-sender". When this program is run, it will ask the user to enter a message.
This message should be a single word, all in lowercase, with no numbers or 
capital letters.

Then, for each character of the word (except null terminator), it will convert
it to its numeric position in the alphabet (a = 1, b = 2, ... z = 26). It will
store this number as an unsigned 16-bit integer in network order (big endian).

Following that, it will form a packet for each number, which consists of 2
bytes, 0x0A 0x0D, followed by the number. 

Finally, it will send a start message, which is 0x0A 0x0D 0x1E00, each packet
as described above, followed by an end message, 0x0A 0x0D 0x1F00. Each packet
will be sent to loopback (127.0.0.1) port 50040  0.5 seconds apart. After all 
packets are sent, the program terminates

For example, if you entered the word "abcd", it would send the following 
messages:

    1) 0x0A 0x0D 0x1E00
    2) 0x0A 0x0D 0x0100
    3) 0x0A 0x0D 0x0200
    4) 0x0A 0x0D 0x0300
    5) 0x0A 0x0D 0x0400
    6) 0x0A 0x0D 0x1F00

To compile the message sender, move to the directory that contains the file
message-sender.cpp, and run the following commands:

g++ -o msg-sender ./message-sender.cpp
sudo chmod +x ./msg-sender
./msg-sender

--------------------------------------------------------------------------------
Task
--------------------------------------------------------------------------------
Your task is to write two cFS applications

The first application should continuously listen to loopback port 50040 for
messages from message-sender. It should receive entire words and convert the 
characters from their numeric form to ASCII characters. Finally, it should send
the word it receives to the second application in a command packet.

The second application should wait to receive such a command from the first 
application. When it receives this command, it should print the word the command
packet contains.

If you complete the task successfully, then any message you send with 
msg-sender will be printed to the event log by the second application. Try it
several times.

--------------------------------------------------------------------------------
Limitations
--------------------------------------------------------------------------------

1) You may not use any OS-specific functions. You must use the
cFS/cFE API. The only exception is if the API does not contain a function you
need. See the ci lab application and the cFS/CFE/OSAL documentation and API
files for reference.

2) None of the read function calls may be blocking. If you attempt to read
from the socket and nothing is available, you must continue.

3) You may not use malloc, calloc, or realloc. You must use cFS/cFE functions
for dynamic memory allocation and deallocation.

--------------------------------------------------------------------------------
Considerations
--------------------------------------------------------------------------------
Remember that the numbers are sent in big endian. Your machine is most likely
little endian, so you must convert to little endian to do any operations.

There are OSAL functions that do this, but you may use functions such as ntohs 
for this.

If the msg-sender program doesn't run, try chmod +x msg-sender.

msg-sender will always send a start and end byte, but well written flight 
software should be fault tolerant. Try and write your code to handle edge cases.
For example, what would happen if you received an end byte, and then another
packet that was an encoded character? What would happen if you received 
a packet that was not a start byte, end byte, or encoded character? Such
situations can occur for a variety or reasons in the real world.


