# linux-ipc
# comming soon

program vs process
==================

- Program is a **set of instructions designed to complete a specific task**. 
- Process is an **instance of  program that is under execution**.

OR

- Program itself is  some thing static,sequence of instruction stored in Disc.
- process is nothing but **"program in action"**.
note:- 
- Program is a passive entity as it resides in the secondary memory. 
- Process is a active entity as it is created during execution and loaded into the main memory.
 
Shared files
============
here one process (producer) creates and writes to a file.
another process (consumer) reads from this same file:

              writes  +-----------+  reads
     producer-------->| disk file |<-------consumer
                      +-----------+

The obvious challenge in using this IPC mechanism is that a race condition might arise: 
[the producer and the consumer might access the file at exactly the same time]
To avoid a race condition, the file must be locked in a way that prevents a conflict between a write operation and any another operation, 
whether a read or a write.

The locking API in the standard system library can be summarized as follows:

- A producer should gain an exclusive lock on the file before writing to the file. An exclusive lock can be held by one process at most, which rules out a race condition because no other process can access the file until the lock is released.
- A consumer should gain at least a shared lock on the file before reading from the file. Multiple readers can hold a shared lock at the same time, but no writer can access a file when even a single reader holds a shared lock.

The standard I/O library includes a utility function named **fcntl** that can be used to inspect and manipulate both exclusive and shared locks on a file.

For file locking, Linux provides the library function **flock**, which is a thin wrapper around fcntl.
# Producer End
    #include <stdio.h>
    #include <stdlib.h>
    #include <fcntl.h>
    #include <unistd.h>
    #include <string.h>

    #define FileName "data.dat"
    #define DataString "Now is the winter of our discontent\nMade glorious summer by this sun of York\n"

    void report_and_exit(const char* msg) {
      perror(msg);
      exit(-1); /* EXIT_FAILURE */
    }

    int main() {
      struct flock lock;
      lock.l_type = F_WRLCK;    /* read/write (exclusive versus shared) lock */
      lock.l_whence = SEEK_SET; /* base for seek offsets */
      lock.l_start = 0;         /* 1st byte in file */
      lock.l_len = 0;           /* 0 here means 'until EOF' */
      lock.l_pid = getpid();    /* process id */

      int fd; /* file descriptor to identify a file within a process */
      if ((fd = open(FileName, O_RDWR | O_CREAT, 0666)) < 0)  /* -1 signals an error */
        report_and_exit("open failed...");

      if (fcntl(fd, F_SETLK, &lock) < 0) /** F_SETLK doesn't block, F_SETLKW does **/
        report_and_exit("fcntl failed to get lock...");
      else {
        write(fd, DataString, strlen(DataString)); /* populate data file */
        fprintf(stderr, "Process %d has written to data file...\n", lock.l_pid);
      }

      /* Now release the lock explicitly. */
      lock.l_type = F_UNLCK;
      if (fcntl(fd, F_SETLK, &lock) < 0)
        report_and_exit("explicit unlocking failed...");

      close(fd); /* close the file: would unlock if needed */
      return 0;  /* terminating the process would unlock as well */
    }
    
The main steps are:-
- declares a variable of type struct flock.[represents a lock]

      lock.l_type = F_WRLCK; /* exclusive lock (read-write) rather than a shared (read-only) lock*/ 
- If the producer gains the lock, then no other process will be able to write or read the file until the producer releases the lock, either explicitly with the appropriate call to fcntl or implicitly by closing the file.
- However, the locking API allows only designated bytes to be locked.
- The first call to fcntl:tries to lock the file exclusively,

      if (fcntl(fd, F_SETLK, &lock) < 0)
      
- In general, the fcntl function returns -1  [failure.]
- F_SETLK [means setlock]is non blocking returns immediately, either granting the lock or indicating failure.
- if the flag used instead F_SETLKW (W is for wait) , the call to fcntl would block until gaining the lock was possible.
- the first argument ,fd is the file descriptor.
- the second argument specifies the action to be taken (F_SETLK for setting the lock), and 
- the third argument is the address of the lock structure (&lock).

If the producer gains the lock, the program writes two text records to the file.

- After writing to the file, the producer changes the lock structure's **l_type** field to the unlock value ,and calls **fcntl** to perform the unlocking operation. The program finishes up by closing the file and exiting.

      lock.l_type = F_UNLCK;
# Consumer end:-
      #include <stdio.h>
      #include <stdlib.h>
      #include <fcntl.h>
      #include <unistd.h>

      #define FileName "data.dat"

      void report_and_exit(const char* msg) {
        perror(msg);
        exit(-1); /* EXIT_FAILURE */
      }

      int main() {
        struct flock lock;
        lock.l_type = F_WRLCK;    /* read/write (exclusive) lock */
        lock.l_whence = SEEK_SET; /* base for seek offsets */
        lock.l_start = 0;         /* 1st byte in file */
        lock.l_len = 0;           /* 0 here means 'until EOF' */
        lock.l_pid = getpid();    /* process id */

        int fd; /* file descriptor to identify a file within a process */
        if ((fd = open(FileName, O_RDONLY)) < 0)  /* -1 signals an error */
          report_and_exit("open to read failed...");

        /* If the file is write-locked, we can't continue. */
        fcntl(fd, F_GETLK, &lock); /* sets lock.l_type to F_UNLCK if no write lock */
        if (lock.l_type != F_UNLCK)
          report_and_exit("file is still write locked...");

        lock.l_type = F_RDLCK; /* prevents any writing during the reading */
        if (fcntl(fd, F_SETLK, &lock) < 0)
          report_and_exit("can't get a read-only lock...");

        /* Read the bytes (they happen to be ASCII codes) one at a time. */
        int c; /* buffer for read bytes */
        while (read(fd, &c, 1) > 0)    /* 0 signals EOF */
          write(STDOUT_FILENO, &c, 1); /* write one byte to the standard output */

        /* Release the lock explicitly. */
        lock.l_type = F_UNLCK;
        if (fcntl(fd, F_SETLK, &lock) < 0)
          report_and_exit("explicit unlocking failed...");

        close(fd);
        return 0;
      }
 
- the consumer program first checks whether the file is exclusively locked and only then tries to gain a shared lock. 

      lock.l_type = F_WRLCK;
      ...
      fcntl(fd, F_GETLK, &lock); /* sets lock.l_type to F_UNLCK if no write lock */
      if (lock.l_type != F_UNLCK)
        report_and_exit("file is still write locked...");
- **F_GETLK** operation specified in the fcntl call checks for a lock  
- if the file is not currently locked, then the consumer tries to gain a shared (read-only) lock (**F_RDLCK**).
- **F_RDLCK** lock does prevent any other process from writing to the file, but allows other processes to read from the file.
- a shared lock can be held by multiple processes. After gaining a shared lock, the consumer program reads the bytes one at a time from the file, prints the bytes to the standard output, releases the lock, closes the file, and terminates.       

# Shared memory
- Two or more process can access the common memory. And communication is done via this shared memory where changes made by one process can be viewed by another process.

SYSTEM CALLS USED ARE:



- **ftok()**: is use to generate a unique key.

- **shmget()**: int shmget(key_t,size_tsize,intshmflg); upon successful completion, shmget() returns an identifier for the shared memory segment.

- **shmat()**: Before you can use a shared memory segment, you have to attach yourself to it, using shmat(). 

       void *shmat(int shmid ,void *shmaddr ,int shmflg);
      *shmid* is shared memory id. 
      *shmaddr* specifies specific address to use but we should set it to zero and, OS will automatically choose the address.

- **shmdt()**: When you’re done with the shared memory segment, your program should detach itself from it using shmdt(). 

      int shmdt(void *shmaddr);

- **shmctl()**: when you detach from shared memory,it is not destroyed. So, to destroy
- **shmctl()** is used. 

      shmctl(int shmid,IPC_RMID,NULL);



The problem with pipes, fifo and message queue – is that for two process to exchange information. The information has to go through the kernel.

- Server reads from the input file.
- The server writes this data in a message using either a pipe, fifo or message queue.
- The client reads the data from the IPC channel,again requiring the data to be copied from kernel’s IPC buffer to the client’s buffer.
- Finally the data is copied from the client’s buffer.
- A total of four copies of data are required (2 read and 2 write). So, shared memory provides a way by letting two or more processes share a memory segment. With Shared Memory the data is only copied twice – from input file into shared memory and from shared memory to the output file.

# SHARED MEMORY FOR WRITER PROCESS

       #include <iostream>
       #include <sys/ipc.h>
       #include <sys/shm.h>
       #include <stdio.h>
       using namespace std;

       int main()
       {
        // ftok to generate unique key
        key_t key = ftok("shmfile",65);

        // shmget returns an identifier in shmid
        int shmid = shmget(key,1024,0666|IPC_CREAT);

        // shmat to attach to shared memory
        char *str = (char*) shmat(shmid,(void*)0,0);

        cout<<"Write Data : ";
        gets(str);

        printf("Data written in memory: %s\n",str);

        //detach from shared memory
        shmdt(str);

        return 0;
       }
# SHARED MEMORY FOR READER PROCESS
     #include <iostream>
     #include <sys/ipc.h>
     #include <sys/shm.h>
     #include <stdio.h>
     using namespace std;

     int main()
     {
      // ftok to generate unique key
      key_t key = ftok("shmfile",65);

      // shmget returns an identifier in shmid
      int shmid = shmget(key,1024,0666|IPC_CREAT);

      // shmat to attach to shared memory
      char *str = (char*) shmat(shmid,(void*)0,0);

      printf("Data read from memory: %s\n",str);

      //detach from shared memory
      shmdt(str);

      // destroy the shared memory
      shmctl(shmid,IPC_RMID,NULL);

      return 0;
     }

# Message Queues
A message queue is a linked list of messages stored within the kernel and identified by a message queue identifier. 
- A new queue is created or an existing queue opened by **msgget()**. 
- New messages are added to the end of a queue by **msgsnd()**. 
- when the message is added to a queue. Messages are fetched from a queue by **msgrcv()**.
- we can fetch messages based on their type field.Each message is given an identification or type so that processes can select the appropriate message.

System calls used for message queues: 
 

- **ftok()**: is use to generate a unique key.
- **msgget()**: either returns the message queue identifier for a newly created message queue or returns the identifiers for a queue which exists with the same key value.
- **msgsnd()**: Data is placed on to a message queue by calling msgsnd().
- **msgrcv()**: messages are retrieved from a queue.
- **msgctl()**: It performs various operations on a queue. Generally it is use to destroy message queue.

# MESSAGE QUEUE FOR WRITER PROCESS 
         // C Program for Message Queue (Writer Process)
         #include <stdio.h>
         #include <sys/ipc.h>
         #include <sys/msg.h>
         #define MAX 10

         // structure for message queue
         struct mesg_buffer {
          long mesg_type;
          char mesg_text[100];
         } message;

         int main()
         {
          key_t key;
          int msgid;

          // ftok to generate unique key
          key = ftok("progfile", 65);

          // msgget creates a message queue
          // and returns identifier
          msgid = msgget(key, 0666 | IPC_CREAT);
          message.mesg_type = 1;

          printf("Write Data : ");
          fgets(message.mesg_text,MAX,stdin);

          // msgsnd to send message
          msgsnd(msgid, &message, sizeof(message), 0);

          // display the message
          printf("Data send is : %s \n", message.mesg_text);

          return 0;
         }
# MESSAGE QUEUE FOR READER PROCESS
     // C Program for Message Queue (Reader Process)
     #include <stdio.h>
     #include <sys/ipc.h>
     #include <sys/msg.h>

     // structure for message queue
     struct mesg_buffer {
      long mesg_type;
      char mesg_text[100];
     } message;

     int main()
     {
      key_t key;
      int msgid;

      // ftok to generate unique key
      key = ftok("progfile", 65);

      // msgget creates a message queue
      // and returns identifier
      msgid = msgget(key, 0666 | IPC_CREAT);

      // msgrcv to receive message
      msgrcv(msgid, &message, sizeof(message), 1, 0);

      // display the message
      printf("Data Received is : %s \n",
          message.mesg_text);

      // to destroy the message queue
      msgctl(msgid, IPC_RMID, NULL);

      return 0;
     }
# fork() and pipe()
P1 takes a string and passes it to P2. P2 concatenates the received string with another string without using string function and sends it back to P1 for printing.
Examples:

Other string is: forgeeks.org

Input  : www.geeks
Output : www.geeksforgeeks.org
        
Input :  www.practice.geeks
Output : practice.geeksforgeeks.org
# Explanation:

- To create child process we use fork(). 
   - fork() returns :

       - <0 fail to create child (new) process
       - =0 for child process
       - >0 i.e process ID of the child process to the parent process. When >0 parent process will execute.
- **pipe()** is used for passing information from one process to another. pipe() is unidirectional therefore, for two-way communication between processes, two pipes can be set up, one for each direction.

Example:

     int fd[2];
     pipe(fd);
     fd[0]; //-> for using read end
     fd[1]; //-> for using write end
 
Inside Parent Process : 
- We firstly close the reading end of first pipe (fd1[0]) then write the string though writing end of the pipe (fd1[1]).
- Now parent will wait until child process is finished. 
-  After the child process, parent will close the writing end of second pipe(fd2[1]) and read the string through reading end of pipe (fd2[0]).

Inside Child Process : 
- Child reads the first string sent by parent process by closing the writing end of pipe (fd1[1]) and 
- after reading concatenate both string and passes the string to parent process via fd2 pipe and will exit.

       // C program to demonstrate use of fork() and pipe()
       #include<stdio.h>
       #include<stdlib.h>
       #include<unistd.h>
       #include<sys/types.h>
       #include<string.h>
       #include<sys/wait.h>

       int main()
       {
        // We use two pipes
        // First pipe to send input string from parent
        // Second pipe to send concatenated string from child

        int fd1[2]; // Used to store two ends of first pipe
        int fd2[2]; // Used to store two ends of second pipe

        char fixed_str[] = "forgeeks.org";
        char input_str[100];
        pid_t p;

        if (pipe(fd1)==-1)
        {
         fprintf(stderr, "Pipe Failed" );
         return 1;
        }
        if (pipe(fd2)==-1)
        {
         fprintf(stderr, "Pipe Failed" );
         return 1;
        }

        scanf("%s", input_str);
        p = fork();

        if (p < 0)
        {
         fprintf(stderr, "fork Failed" );
         return 1;
        }

        // Parent process
        else if (p > 0)
        {
         char concat_str[100];

         close(fd1[0]); // Close reading end of first pipe

         // Write input string and close writing end of first
         // pipe.
         write(fd1[1], input_str, strlen(input_str)+1);
         close(fd1[1]);

         // Wait for child to send a string
         wait(NULL);

         close(fd2[1]); // Close writing end of second pipe

         // Read string from child, print it and close
         // reading end.
         read(fd2[0], concat_str, 100);
         printf("Concatenated string %s\n", concat_str);
         close(fd2[0]);
        }

        // child process
        else
        {
         close(fd1[1]); // Close writing end of first pipe

         // Read a string using first pipe
         char concat_str[100];
         read(fd1[0], concat_str, 100);

         // Concatenate a fixed string with it
         int k = strlen(concat_str);
         int i;
         for (i=0; i<strlen(fixed_str); i++)
          concat_str[k++] = fixed_str[i];

         concat_str[k] = '\0'; // string ends with '\0'

         // Close both reading ends
         close(fd1[0]);
         close(fd2[0]);

         // Write concatenated string and close writing end
         write(fd2[1], concat_str, strlen(concat_str)+1);
         close(fd2[1]);

         exit(0);
        }
       }
https://www.geeksforgeeks.org/input-output-system-calls-c-create-open-close-read-write/
