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
