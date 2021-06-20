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
- The first call to fcntl:

      if (fcntl(fd, F_SETLK, &lock) < 0)
      
