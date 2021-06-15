      0:00
      okay good morning everyone we're here
      00:02
      for as you can see an introduction to
      00:04
      Linux IBC by micro ask Michael kursk I
      00:08
      hope I'm pronouncing that Ryan is the
      00:11
      author of the highly praised book at the
      00:12
      Linux programming interface a gaiden
      00:15
      reference for system programming on
      00:16
      Linux and UNIX since 2004 he has been
      00:19
      the maintainer of the Linux man pages
      00:21
      project Michael lives in Munich Germany
      00:24
      and works as an editor and writer at lwn
      00:27
      net thank you very much for coming and
      00:29
      talking to us today Michael over to you
      00:31
      thank you
      00:32
      morning that's quite a lot of you that's
      00:37
      good okay i don't--hey much more about
      00:41
      myself that was a nice introduction so
      00:44
      I'm here to talk about Linux IPC I've
      00:48
      got a very limited amount of time it's
      00:50
      actually a lot of IPC so I'm going to be
      00:53
      reasonably quick and my goal is Regis to
      00:56
      give you a flavor of the kinds of IPC
      00:58
      there are on Linux a few code examples
      01:01
      and some discriminating factors that you
      01:03
      might use to choose between different
      01:06
      IPC methods IPC inter-process
      01:10
      communication in case you didn't know
      01:13
      you might be in the wrong place okay so
      01:20
      I think that's all being said I'm on the
      01:24
      linux mint page of materials that
      01:25
      mentioned yeah i've been the lensman
      01:27
      page Montagna
      01:28
      for well it's coming up to ten years
      01:30
      soon a lot of the diagrams you'll see do
      01:35
      come from their book which is nicely
      01:37
      product placed there
      01:39
      I've got two copies you can feel free to
      01:42
      browse afterwards if you'd like more
      01:45
      information online if you want it I'm
      01:49
      going to assume that you can read a bit
      01:50
      of C and you have an idea about some
      01:55
      basic system calls in Linux units for
      01:58
      read/write open but you don't need much
      02:01
      more than that to follow what I'm going
      02:04
      to do today so the first thing to know
      02:09
      is there is a lot of IPC on Linux that's
      02:13
      I think it's nearly a complete list it's
      02:17
      as complete as I could make it anyway
      02:19
      so that's something like about 20 or so
      02:22
      different things there if you want to
      02:24
      try and understand the first thing that
      02:26
      helps to start with try and classify it
      02:29
      that's a first attempt at classification
      02:31
      where I've divided mechanisms into
      02:34
      communication things that are about
      02:36
      exchanging data synchronization which is
      02:39
      about coordinating activities of
      02:41
      processes and signals which are don't
      02:46
      quite followed by the category but can
      02:48
      be both of course you what's happening
      02:51
      here oh hang on a second
      03:00
      sorry
      03:10
      of course you can use communication
      03:13
      methods for synchronization if you are
      03:15
      tricky about it you can even use
      03:17
      synchronizing it synchronization here
      03:19
      this to communicate data so you can flip
      03:22
      bits and semaphores backwards and
      03:23
      forwards as I mean of the means of
      03:25
      exchanging bits between two processes
      03:27
      it's just why does this keep happening
      03:32
      okay
      03:34
      something's wrong here that just my
      03:36
      slides are frustrating for I'm not sure
      03:38
      why so if you try and break that
      03:43
      taxonomy down a little bit further
      03:45
      there's a whole bunch of communication
      03:47
      methods which again I've tried to break
      03:50
      down categories broadly two main
      03:54
      categories data transfer methods which
      03:56
      are about writing data from one process
      04:01
      and reading it into another so there's a
      04:03
      transfer involved where user space sends
      04:06
      the data to the kernel and then another
      04:08
      process gets that data from the kernel
      04:10
      so there's two copies involved the you
      04:15
      can divide those data frames for me
      04:16
      that's further into two or two main
      04:19
      categories byte stream and message byte
      04:23
      stream the data is just a nun delimited
      04:26
      sequence of bytes and the reader can
      04:30
      read as many bytes they like regardless
      04:32
      of how many bytes a writer wrote
      04:34
      conversely I think we just put that down
      04:40
      conversely you've got okay let's go
      04:47
      backwards usually for any pop okay I'll
      05:05
      take the set as well okay so we had byte
      05:13
      stream the converse is message oriented
      05:19
      communication where a reader or sorry a
      05:21
      writer writes a whole unit of data and
      05:24
      the reader reads exactly that unit so
      05:26
      there's no content of partial read
      05:28
      you're reading multiple messages at a
      05:29
      time
      05:30
      a reader right one message a writer
      05:33
      right one message a reader reads their
      05:35
      message in between there's one other
      05:38
      data transfer mechanism which I won't
      05:40
      talk about much today pseudo terminals
      05:41
      which have a rather specialized use
      05:44
      they're not a general data communication
      05:47
      technique
      05:58
      now this is not something I sit up who's
      06:05
      an expert in libreoffice wherewhere is
      06:11
      like a lease excuse me folks I'm sorry
      06:17
      about this slideshow shot seems
      06:33
      change sides sometimes you do
      06:37
      okay maybe we're going to some jointer
      06:54
      so to determine one so so much well
      06:56
      there are quite specialized as a Makura
      06:58
      method of data communication the other
      07:01
      general category of data data trend of
      07:04
      data communication methods is shared
      07:07
      memory and the concept there is that
      07:09
      you've got a bunch of processes or a
      07:11
      bunch of threads that are communicating
      07:13
      by sharing some physical pages of memory
      07:16
      and the communication just consists of
      07:18
      writing some down into their memory then
      07:19
      it's visible to everyone
      07:24
      synchronization again is a number of
      07:27
      categories that you can divide things
      07:30
      into semaphores event file descriptors
      07:35
      which I might find a brief amount of
      07:37
      time to talk about depending how we go
      07:39
      today file locks again which I might
      07:41
      find a brief amount of time to talk
      07:42
      about few taxes which I won't talk about
      07:45
      because there are very low-level
      07:46
      mechanism a few takers are used to
      07:49
      implement the same force or POSIX
      07:50
      semaphores and some of the thread
      07:53
      synchronization methods and then there's
      07:55
      a bunch of thread related
      07:57
      synchronization methods mutexes
      07:59
      condition variables barriers read what
      08:02
      readwrite blocks and I won't go into
      08:04
      those today so this is an indication
      08:13
      we're going to try and cover today the
      08:14
      thus the bold blue stars are the ones I
      08:17
      want to find some in some time on and
      08:18
      the pale blue stars are the ones I might
      08:21
      get to if we have some time and if I
      08:24
      don't you can always ask me questions
      08:25
      later
      08:28
      synchronization methods may no one
      08:30
      should talk about stem ofourse
      08:32
      made we might find time to rough file
      08:34
      locks and event file descriptors as well
      08:37
      these are the business I'm not covering
      08:39
      and I think I've already explained in
      08:41
      most cases why so communication
      08:45
      techniques the original communication
      08:48
      technique on Linux was a pipe okay a
      08:51
      pipe which everyone similar with where
      08:54
      you have a command like alias you have
      08:57
      the pipe symbol and you run it through
      08:59
      to it okay command like WC to fine at
      09:02
      number of files in your directory
      09:04
      a pipe is a byte stream so a writer can
      09:08
      write an arbitrary number of bytes in
      09:10
      there a reader can read those bytes in
      09:12
      any science unit they like single bytes
      09:15
      at a time two bytes at a time 100 bytes
      09:17
      at a time or so on
      09:18
      it's unidirectional in other words that
      09:21
      only goes one way through the pipe a
      09:24
      pipe good a pipe has a right end that's
      09:30
      the end of things go into reading where
      09:32
      the data comes out so a pipe is just a
      09:37
      buffer in the kernel you can't see call
      09:42
      it you can just do sequential reads and
      09:46
      I see this already it's reader it's it's
      09:50
      a unidirectional one thing because it's
      09:52
      a byte stream you can't have multiple
      09:54
      readers and writers I mean technically
      09:56
      you can but because data might be a
      10:00
      dispersion between writers or readers
      10:03
      would compete to read the bytes
      10:04
      practically it's it's difficult to do
      10:06
      anything useful with multiple readers or
      10:08
      multiple writers so the way you create a
      10:12
      pipe is for the pipe system call what
      10:15
      you do is you give it oh that's a type
      10:18
      over there that should say filed is -
      10:20
      there you give it a to binary oven to
      10:23
      just pass that as an argument - pipe
      10:26
      what it does is create this pipe and the
      10:30
      two array elements are the file
      10:32
      descriptors the point to the right end
      10:36
      of the pipe and the reading of the pilot
      10:38
      easy-to-remember
      10:39
      zero is sustained to dinner if you like
      10:41
      one to stand it out it's the same
      10:46
      convention if you like it for those two
      10:48
      arraignments and then you can just write
      10:51
      into the pipe with a writable read from
      10:55
      the reader scripter with the read system
      10:57
      call pipes are anonymous they don't have
      11:02
      a name in the file system
      11:05
      then the question arises as well if two
      11:06
      processors want to actually talk to each
      11:08
      other using a pipe what do they do well
      11:11
      the trick is they have to be related
      11:14
      processes and by rated I mean that one
      11:18
      process creates the pipe then does a
      11:20
      fork which creates another process that
      11:24
      duplicates the file descriptors of a
      11:26
      parent in the child so then you end up
      11:28
      with this situation here we've got a
      11:30
      parent that's got two descriptors that
      11:32
      refer to the pipe and a child that's got
      11:35
      two descriptors that likewise refer to
      11:37
      the pipe at that point the two processes
      11:42
      could communicate now one of these you
      11:47
      need to do is close the ends of the pipe
      11:50
      that you don't use okay remember set of
      11:52
      pipe is unidirectional well if you want
      11:54
      to communicate from the parent to the
      11:56
      child the parent would close its read
      11:59
      description for the pipe excuse me the
      12:07
      parent would close its read descriptor
      12:10
      for the pipe and the child would close
      12:13
      its write descriptor one thing I should
      12:19
      say I've admitted the error checking
      12:20
      from all of these system calls and my
      12:22
      examples you should never do that in a
      12:24
      real program but my slides have a
      12:26
      limited space
      12:31
      okay closing those descriptors is
      12:34
      actually quite important okay
      12:37
      closing this descriptor here and the
      12:39
      parent Theory descriptor and the right
      12:40
      descriptor and the child because that's
      12:42
      the way that the processes are neither
      12:44
      into the pipe can tell whether the
      12:46
      process is on the other end of the pipe
      12:48
      so the child can tell that the parent is
      12:52
      going away if all the descriptors here
      12:55
      are closed then the child will seen into
      12:58
      file on a read conversely the parent can
      13:02
      tell that the child's going away if all
      13:05
      of the follow if all of the file
      13:07
      descriptors here are closed then a write
      13:09
      into the pipe will fail with a signal or
      13:12
      an e pipe error
      13:13
      so the next thing you should do is
      13:17
      having formed each process should close
      13:20
      the end of the pipe that it doesn't need
      13:31
      so there's a more complete example of
      13:35
      how you might use a pipe again the steps
      13:37
      there I talked about re pipe to create
      13:40
      the pipe fork to create the child
      13:43
      process the two processes then share
      13:45
      descriptors then they each closed the
      13:50
      descriptors they don't need then they do
      13:53
      some data transfer okay
      13:54
      the parent here is writing a string into
      13:56
      the pipe the child is reading that data
      14:01
      from the pipe inside a loop and writing
      14:04
      to standard out and then at the end the
      14:08
      parent closes the file descriptor at
      14:10
      that point the child's next read will
      14:13
      see in the file a zero return from re
      14:15
      are from from the read any questions at
      14:21
      this point
      14:31
      so if the pipes empty a reed blocks and
      14:36
      if a pipe is full a right blocks there's
      14:42
      one other thing to know about pipes a
      14:44
      pipe if you do a write to a paper and
      14:47
      there are multiple writers to a pipe
      14:48
      then the data can be broken up so that
      14:53
      if you've got say two processes of both
      14:55
      try and write 10,000 bytes to a pipe the
      14:59
      writes will not be written as 10,000
      15:01
      byte pieces potentially that broken up
      15:04
      into pieces of the science of pipe buff
      15:08
      pipe buff is according to POSIX at least
      15:11
      512 bytes in other words if you write
      15:14
      anything less than or less than or equal
      15:16
      to 512 bytes it's guaranteed to be
      15:18
      atomic on every unique system on Linux
      15:23
      and many other systems actually pipe
      15:25
      puff is actually 4096 if you write more
      15:28
      than those number of bytes and someone
      15:31
      else also trying to write to the pipe
      15:32
      then potentially your rights will be
      15:34
      broke up into pieces of arbitrary size
      15:37
      that's one of the reasons why I said
      15:39
      it's doesn't usually make sense to have
      15:41
      multiple writers to a pipe I won't go
      15:48
      into it but the the scenario that I
      15:52
      showed right back to the beginning there
      15:54
      where you've got a filter writing to a
      15:58
      pipe and another filter reading from the
      16:00
      pipe in other words these and the
      16:01
      standard file descriptors 0 & 1 you can
      16:05
      accomplish that using the Jew system
      16:07
      call I've just given a link there of an
      16:09
      example of how to do that to set up
      16:13
      exactly that situation shown in a
      16:16
      diagram you can find that there
      16:21
      pipes have a limited capacity the
      16:25
      fullness was to option up until learns
      16:28
      to point six point 1 1 it was 4096 bytes
      16:32
      since then it's 64 are 64 K bytes and
      16:37
      even more recently still you can
      16:39
      actually control the capacity of a pipe
      16:41
      using an F control system call
      16:44
      I forget which version that was in it's
      16:46
      that it's in the main page in general
      16:50
      though you should design your
      16:51
      applications not to care about the size
      16:54
      of a pipe if you are doing that poorly
      16:58
      there's something wrong with your
      16:59
      application design pipes are nice they
      17:08
      go into views between related processes
      17:10
      so one of the other interprocess
      17:13
      communication mechanism that they came
      17:14
      on soon afterwards was named pipes a
      17:17
      named pipe is just like a pipe with the
      17:21
      difference that it exists as a file and
      17:23
      the file system you create it using the
      17:26
      make FIFO function and then having done
      17:31
      there you can then open the named pipe
      17:33
      and read and write just as with a normal
      17:36
      pipe and exactly the same rules apply
      17:41
      there are a few special rules around
      17:43
      opening the file or opening the FIFO
      17:45
      because it only makes sense for there to
      17:49
      be two processes on the FIFO opens are
      17:52
      synchronized in other words if someone
      17:54
      comes along to open a FIFO for reading
      17:56
      they're open will block until another
      17:59
      person comes along to open it for
      18:00
      writing ok and then they're both ready
      18:02
      to communicate there are ways around
      18:05
      that you can open a file FIFO reader for
      18:09
      reading in non-blocking mode sometimes
      18:12
      it's useful for read it to get ready to
      18:14
      wait for a writer to come along ok but
      18:17
      in general writes opens are synchronized
      18:22
      let's pipes any questions the pipes and
      18:25
      main pipes
      18:34
      okay
      18:47
      there are on Linux or most modern UNIX
      18:52
      systems - : kinds of IPC that go by the
      18:57
      name system 5 IDC and positive side PC
      19:00
      they both provide three kinds of
      19:02
      communication shared memory message
      19:07
      queues and stammer fours I'm only going
      19:09
      to talk about the POSIX variations of
      19:11
      these IPC methods the system 5 versions
      19:15
      still exists are still usable but their
      19:18
      interfaces are rather unpleasant to use
      19:20
      in certain respects so in general if
      19:22
      you're writing new applications you
      19:24
      should use the POSIX methods so onion
      19:28
      staff is that POSIX message to choose
      19:29
      now okay pause its message choose its
      19:34
      message oriented communication a writer
      19:36
      writes a message a reader reads a whole
      19:38
      message as I said before no partial
      19:41
      reads and so on messages have priorities
      19:46
      so you write a message into a positive
      19:49
      skew the messages are queued up in order
      19:52
      of priority the reader will read the
      19:53
      highest priority message first there is
      19:58
      another interesting and somewhat unique
      20:01
      feature about positive message to choose
      20:03
      there's a notification feature whereby
      20:06
      you can say when a new message arrives
      20:09
      on this queue that was empty tell me
      20:12
      about it in some way and there are
      20:13
      various ways you can choose to get
      20:15
      informed and I'll talk about those in a
      20:17
      moment
      20:19
      so the API looks pretty much how you
      20:23
      expect justify the way that all these
      20:26
      slides will appear on the net very soon
      20:28
      probably by tomorrow at a location I'll
      20:30
      show you at the end and I suppose
      20:32
      they'll appear on the LCA side as well
      20:35
      yeah
      20:37
      okay so the API looks like what you'd
      20:41
      expect you've got an open call for
      20:44
      opening and creating message queue it's
      20:46
      very much like opening and creating a
      20:47
      file close unlink for removing a path
      20:51
      name sin for sending a message to the
      20:55
      queue received to receive a message and
      20:56
      then there are a few odd-looking calls
      20:59
      at the end
      21:10
      you
      21:13
      a few older people's the end you can set
      21:17
      and get cue attributes using these two
      21:20
      functions and this function sets up that
      21:23
      notification technique that I was
      21:25
      talking about so the way you create a q
      21:30
      mq open you give it the name of the
      21:33
      queue you want open flags to control how
      21:35
      the open happens the permissions mode if
      21:38
      you're creating a new queue some
      21:40
      attributes for queue if you want to set
      21:42
      some attributes the name looks like this
      21:44
      it's slash and then some name it looks
      21:47
      like a path name with only one component
      21:50
      that's just a positive standard way of
      21:52
      doing things it's visible at the pseudo
      21:56
      file system I'll show you briefly later
      21:58
      on about how to actually see them this
      21:59
      is choosing that file system and you get
      22:02
      back a message queue descriptor which is
      22:04
      the thing that you use for referring to
      22:07
      that message queue in the rest of the
      22:08
      API these flags if you're familiar with
      22:16
      the open system call for opening a POSIX
      22:19
      message to look quite similar to an open
      22:21
      system call create a queue if it doesn't
      22:23
      exist created exclusively other words I
      22:26
      want to make sure I'm the one that
      22:28
      created if there is already who by the
      22:29
      name that exists give me an error open
      22:33
      for reading writing or reading and
      22:35
      writing open for normal blocking I own
      22:39
      the mode set the permissions on the
      22:42
      queue attributes attributes for the
      22:45
      queue I won't talk too much about
      22:46
      attributes but null will give you some
      22:48
      reasonable defaults so here's how to
      22:55
      open a new message queue for writing in
      22:59
      you open here's the name of Mike you
      23:01
      created exclusively for writing
      23:05
      permissions 0 so teller read write just
      23:08
      for the owner default attributes opening
      23:12
      existing two for reading name of the
      23:15
      queue read-only and I don't need the
      23:17
      rest of the arguments if I don't need
      23:22
      the queue anymore
      23:23
      I can unlink the name just like unlink
      23:25
      near file message choose like pipes have
      23:34
      eliminated the limited capacity you can
      23:36
      control that using the attributes that I
      23:38
      talked about before which and which I'll
      23:39
      briefly mention later on the two system
      23:43
      calls for performing i/o on the cube
      23:45
      receive lots of there's no messages in
      23:48
      the queue send blocks of to choose full
      23:51
      own on block ok do non-blocking i/o so
      23:55
      in cases where would normally block
      23:57
      instead of blocking give me an error
      24:00
      instead so if I try and read from nbq
      24:02
      I'll get an error instead of a blocking
      24:13
      sending okay to send a message you give
      24:20
      a cue descriptor a pointer to your
      24:21
      message the length of the message and
      24:23
      the priority for their message all that
      24:27
      I think is fairly straightforward zero
      24:29
      is the lowest priority anything greater
      24:31
      than zero is a higher priority so some
      24:41
      examples there of sending okay we open a
      24:44
      queue as before here's our message send
      24:49
      to the queue that message size of the
      24:52
      message and zero is the priority okay
      24:58
      again I've got some URLs pointing to
      25:00
      bigger examples that contain these code
      25:02
      samples
      25:11
      oh well there's a type over there that
      25:14
      should say MQ receive here I've written
      25:17
      into scene that should say and can
      25:18
      receive reading the same arguments a
      25:24
      descriptor a buffer to hold the message
      25:29
      the size of it uh fur and a pointer to
      25:33
      an integer which returns the priority of
      25:35
      the message that you've read so you can
      25:37
      find out what the priority was and as
      25:40
      the return value of the system call the
      25:42
      number of bytes in the message so here
      25:46
      you say how big the buffer is and here
      25:48
      you find out how many bytes were
      25:49
      actually used in the buffer when the
      25:51
      certain core returned or when the
      25:53
      function returned
      25:54
      oh I've done it again okay still the
      26:03
      same type are there but down here I've
      26:05
      got the right example here receiving a
      26:09
      message from the descriptor buffer size
      26:12
      of it buffer which I've I've got two
      26:16
      typos that should be about size there
      26:19
      and then to get the priority here
      26:24
      what it means of you and my father did
      26:27
      enough to receive this you'll get an
      26:29
      error
      26:32
      so I own from memory I think that's the
      26:36
      one yeah and will the missus be
      26:39
      partially keys you know you just want
      26:44
      you don't know there's no there's no
      26:46
      partial consumption of messages I just
      26:52
      would mention actually as well one of
      26:54
      the edge of each you set on to is the
      26:56
      maximum message size so also if you try
      26:59
      and write MSS is too big potentially
      27:01
      will get an error as well okay a message
      27:08
      to you notification is quite a cute
      27:10
      trick with message choose one process
      27:13
      could say I want to get messages only
      27:15
      notifications when a new message appears
      27:18
      on an empty queue
      27:20
      okay notification only works if the
      27:22
      queue is already empty at the time
      27:24
      another message arrives and what you do
      27:28
      is you say MQ notify give it a
      27:30
      descriptor and then a structure that
      27:33
      says how you want to be notified and
      27:37
      there are I won't go into the details
      27:39
      but there is an example in the main page
      27:43
      you can choose how you want to be
      27:45
      notified the two main ways of being
      27:47
      notified are one sending me a signal or
      27:50
      to start a new thread in my process and
      27:53
      give us some information about the
      27:55
      message of those just come in it's a
      28:00
      single shot mechanism once you've been
      28:03
      notified once if you want to be notified
      28:05
      again you have to do another MQ notify
      28:07
      to reestablish the notification
      28:09
      mechanism and you should do it before
      28:12
      emptying the queue it was a few Enki the
      28:15
      queue and then try and re-enable the
      28:17
      mechanism there may be a small one or
      28:20
      there is a small wonder there where if
      28:21
      another message arrives before you re
      28:23
      enable notification that she wasn't
      28:25
      empty and you'll never get a
      28:26
      notification
      28:30
      I mentioned there was an attribute
      28:34
      structure associated with each message
      28:36
      queue you can set some flags on the
      28:38
      message queue you can set the mixed
      28:40
      message maximum number of messages on
      28:42
      the queue the maximum size of messages
      28:44
      and you can find out how many messages
      28:46
      are currently in the queue and what I've
      28:48
      just shown there is the various
      28:50
      functions that get or send that
      28:53
      information
      29:02
      the I mention the other so yes just in
      29:06
      to clarify human making fire length of
      29:08
      messages BQ equal you by the number of
      29:11
      messages in the community hours
      29:12
      you couldn't live on sign for us because
      29:15
      I am yes you you you yeah I'm going to
      29:23
      come back to end of it later on this is
      29:24
      one of the special things about message
      29:26
      shoes on on POSIX sorry on on Linux
      29:29
      there actually miss Chu descriptors
      29:32
      actually file descriptors so you can use
      29:34
      Paul that's not a standard thing that
      29:37
      it's a nice feature of the Linux
      29:39
      implementation okay I mentioned that
      29:43
      there was a file system that could show
      29:45
      you your POSIX message queues you can
      29:48
      mount that file system by doing
      29:51
      something like this make a directory for
      29:52
      the mount point then use a mount - t
      29:55
      mount the message to filesystem none
      30:00
      here and in the mountain point and then
      30:05
      you can list the contents of that
      30:07
      directory and see your message choose so
      30:09
      I've got one key there my mq and if you
      30:13
      cat that file you get certain
      30:15
      information about the message to the
      30:18
      number of bytes and the queue the
      30:20
      current notification method and the
      30:21
      queue I forget what to is I think it is
      30:24
      notification by thread so you've got 0 1
      30:27
      and 2 0 is no notification one is
      30:31
      signals to his thread notification and
      30:35
      if some process has set itself up to be
      30:39
      the receiver of notifications for their
      30:41
      message to you can find out the PID of
      30:44
      that process there much more detail in
      30:49
      the MQ overview page
      30:54
      any questions or any other questions
      30:56
      proposal to introduce the messages given
      31:01
      priority and guarantee are they believe
      31:03
      it at a given priority there'll be an
      31:06
      order yeah that's a good point I should
      31:11
      have had it you have that on the slide
      31:18
      does anyone actually move I'm having
      31:23
      trouble to think of some concrete
      31:24
      examples there are people are i-i've
      31:26
      using some applications but sorry maybe
      31:29
      a bit of people I was going to change
      31:32
      system life variables so those
      31:35
      attributes help change them up to a
      31:37
      limit but then you have to system-wide
      31:40
      change okay so you my tiny little
      31:43
      messages and this small message queue so
      31:48
      this is certainly Eunice okay so would
      31:53
      you do five
      31:54
      yeah no it's certainly in the last
      31:56
      organization where I was working people
      31:59
      were trying to use some views positive
      32:00
      message excuse for some of their
      32:01
      applications I didn't mean I think I
      32:08
      raced past it or maybe able to mention
      32:10
      it okay there are process limits and as
      32:13
      as you mentioned there are system-wide
      32:16
      limits as well they control the
      32:17
      operation of message choose again the
      32:19
      details are in the manual page okay
      32:23
      shared memory but the general idea of
      32:27
      shared memory is all a bunch of
      32:30
      processes are sharing the same physical
      32:32
      pages of memory when one process updates
      32:35
      the contents of that memory it's
      32:37
      immediately visible to any other process
      32:38
      on the system oops so communication is
      32:44
      just copying into memory it's it's
      32:47
      efficient because if you compare with
      32:49
      data transfer with data transfer what
      32:51
      you're doing is you're copying some data
      32:52
      from user space into the kernel and then
      32:55
      you're copying it back out during the
      32:58
      read operation okay and copying
      33:00
      in now the colonel is a relatively
      33:02
      expensive operation the only or the
      33:09
      trick thing though is with shared memory
      33:10
      you've got to somehow synchronize your
      33:12
      access because synchronization comes for
      33:15
      free with data transfer methods someone
      33:17
      reads someone writes with shared memory
      33:20
      is known as a control on the access to
      33:22
      the bar so this this is the situation
      33:27
      then you've got you've got two processes
      33:29
      with their page tables and some page
      33:32
      table but some of the page table entries
      33:34
      need to process point to the same
      33:37
      physical pages of memory there are three
      33:46
      types of shared memory that all try and
      33:48
      race through shared anonymous mappings
      33:51
      shared file mappings and share a
      33:54
      positive shared memory there is a full
      33:56
      twin again which I'm going to leave out
      33:58
      with the system five shared memory
      34:00
      because like the rest of the system five
      34:02
      IPC mechanisms the interface is not so
      34:05
      pretty
      34:08
      ok the difference from these three
      34:10
      methods shared anonymous method mappings
      34:13
      allow related processes to detect by
      34:16
      related I mean processes that are
      34:18
      related by for shared file mapping can
      34:22
      be use between unrelated processes and
      34:24
      they're backed by a file and the
      34:26
      traditional file system POSIX shared
      34:29
      memory is for unrelated processes
      34:31
      without creating a file in the
      34:33
      traditional file system so you may not
      34:36
      want to bother doing i/o on a file in
      34:38
      which case you can use a POSIX shared
      34:41
      memory instead all of these system calls
      34:45
      sorry all these mechanisms make use of
      34:48
      the same system call em n em it's a
      34:52
      rather complex system call takes a bit
      34:54
      of time to break down it's got six
      34:56
      arguments those six arguments look like
      35:03
      this
      35:03
      you've got let's see what's the best way
      35:07
      over first of all you want to create a
      35:09
      mapping dn says where
      35:12
      they're mapping to be in my memory
      35:14
      normally you specify that as null and
      35:16
      the system chooses a default value for
      35:18
      you to place the mapping lens is how
      35:22
      long the mapping to be process will
      35:24
      protections on one of the mapping go on
      35:26
      to be able to only read from the memory
      35:28
      or read and write or press read and
      35:30
      write and execute flags are various
      35:35
      flags that control the operation of the
      35:36
      call we'll come back to some of those
      35:38
      flags later on if T is a file descriptor
      35:42
      which is used for final mappings again
      35:44
      we'll come back to that later on and
      35:45
      offset is also used for file mappings
      35:50
      what you get back once the system call
      35:53
      is the address where the mapping was
      35:55
      actually placed and even if you do
      35:57
      explicitly specify an address here you
      36:00
      may not get back exactly the same
      36:02
      address there because at least the
      36:04
      kernel will round up to a page boundary
      36:06
      and made something override your request
      36:09
      for some reason so once you've made the
      36:18
      system call ad looks just like an easy
      36:21
      pointer it's just a pointer to the start
      36:23
      of the map region but anytime you make a
      36:28
      change to the memory and the location
      36:31
      with that pointer points that changes
      36:33
      visible to every other process that
      36:34
      shares the mapping so I said there were
      36:39
      three types of mappings here's the first
      36:41
      one a shared anonymous mapping it works
      36:46
      between related processes and related is
      36:48
      related in the same sense as Pyke as I
      36:51
      as a pipe you don't need a file
      36:54
      descriptor and an offset because we're
      36:57
      not making use of files so those two
      36:59
      icons are specified as minus 1 and 0 and
      37:02
      what you do is you say I want to create
      37:05
      a mapping and then I do a form and the
      37:08
      consequence of a 4 is all page table
      37:12
      entry in the child process are
      37:14
      duplicates of the parent page table
      37:16
      entries pointing to the same physical
      37:18
      pages of memory for that mapping
      37:22
      okay in this case here I've said okay
      37:25
      whatever length of the mapping I want I
      37:27
      want to be able to read and write the
      37:29
      contents of that memory shared says okay
      37:33
      I'm going to share this mapping with
      37:34
      other processes anonymous says there is
      37:38
      no file connected with this mapping so
      37:42
      you get a zero initialized block of that
      37:44
      size and the parent and the child after
      37:47
      that four share that memory at address
      37:50
      for length by it's probably I'll set
      37:58
      everything I need to say about this I
      37:59
      did have a few more slides just to break
      38:00
      this down are there any questions about
      38:05
      what that system for those two system
      38:08
      pools together do okay a shared file
      38:19
      mapping dinair is you open a file you
      38:25
      map the contents of that file at the
      38:27
      memory and then anytime you make changes
      38:30
      to the memory in that region not only
      38:33
      they share with other processes that are
      38:36
      also automatically updated to the file
      38:39
      okay so the file provides a kind of
      38:42
      persistent storage backing up the shared
      38:46
      memory so share file there's two steps
      38:50
      open plus M map
      38:57
      with a shared anonymous mapping the
      39:00
      mapping was initialized to zero but when
      39:02
      you open a file and map it the contents
      39:04
      of memory are initialized from the file
      39:07
      and as I said any changes you make the
      39:10
      file or to the memory are automatically
      39:12
      carried through the file and all the
      39:15
      processes that map the mapping share
      39:18
      those updates in the memory and their
      39:22
      updates are propagated through to the
      39:23
      file so that's showing the situation
      39:31
      here we've got multiple processes
      39:33
      they're sharing the same net pages the
      39:35
      i/o to the file is managed by the kernel
      39:38
      ok processes by and large don't do
      39:41
      anything to make this happen there is a
      39:43
      system call called M sync which says
      39:45
      make sure that the current contents of
      39:48
      memory are forced out to disk if you
      39:52
      want to ensure that actually happened
      39:54
      recently so create a shared mapping open
      40:01
      the file that you want to map open it
      40:05
      for reading and writing in this case
      40:06
      because I want to update the mapping map
      40:09
      I know little system chooses an address
      40:12
      size of the mapping that automat from
      40:15
      that file I want to map it for reading
      40:19
      in writing I want to share my updates
      40:21
      with other processes here's the file
      40:24
      descriptor of the file that I want to
      40:26
      map and 0 there is the offset it says
      40:29
      from what point in the file the wall on
      40:32
      a map so 0 here says on a map let's
      40:34
      start of the file but you could specify
      40:37
      a mapping that started at some other
      40:39
      point in the file as well it'll need to
      40:41
      be page aligned within the file and at
      40:47
      some point you probably only the file
      40:48
      descriptor anymore and you can just
      40:49
      throw it away
      40:54
      I gained I think I've water that already
      41:04
      and gained as a result of the system
      41:06
      call you get the address at which the
      41:07
      mapping was placed in memory any
      41:12
      questions about shared memory mappings
      41:15
      or should a shared file and I think I
      41:17
      should say why some supporters so I
      41:22
      think it's for another question of which
      41:24
      ones don't support it I think most file
      41:25
      systems do support it I can't offhand
      41:29
      I don't know I can't think of any column
      41:32
      file system that doesn't so the failure
      41:36
      yeah fast does have to support it but I
      41:38
      am a number of theories that you the
      41:41
      different uses it can be funny as a film
      41:44
      the other things notice you should relax
      41:46
      to the file full length that you want to
      41:49
      write it was often do as is that you
      41:52
      Falls up you won't know until the next
      41:55
      part of life I think you don't mean
      41:59
      debit hm I think you mean no no most of
      42:02
      the whole derivative you'll hear see
      42:04
      psycho anime yeah and it's not going to
      42:07
      eat
      42:09
      Yeah right
      42:13
      depending on your over commit policy
      42:15
      yeah okay POSIX shared memory is the
      42:21
      third variation there the difference
      42:24
      between the two previous ones it's for
      42:26
      sharing memory between unrelated
      42:28
      processes with the difference that you
      42:31
      don't need to create a file and do file
      42:33
      i/o okay you may not want you may not
      42:41
      need this file about and you don't want
      42:44
      the kernel to go through the extra work
      42:46
      of syncing all of the updates to a file
      42:49
      but you do want unrelated processes to
      42:52
      communicate okay in that case you can
      42:55
      use POSIX shared memory to achieve that
      43:00
      scenario okay
      43:06
      the API looks a little bit familiar from
      43:09
      POSIX semaphores
      43:11
      shared memory open okay open or create
      43:14
      the shared memory object in there to map
      43:17
      W into memory if you don't need the
      43:19
      object anymore unlink it with shared
      43:23
      memory unlink then there are some other
      43:25
      system calls that are also useful with
      43:27
      POSIX shared memory F step allows you to
      43:31
      find out the size of a shared memory
      43:34
      object okay if stat is the
      43:37
      general-purpose system call for getting
      43:39
      attributes of a file well you can apply
      43:42
      F stack to the file the script that you
      43:45
      get back from shared memory open to find
      43:47
      out the size of an existing object F
      43:51
      truncate again you can apply to this
      43:53
      file descriptor to change the size of
      43:55
      the mapping and you can change the
      43:58
      ownership and the permissions on a
      44:01
      shared memory object as well so opening
      44:06
      a shared memory object you say share
      44:08
      memory open name the object Flags the
      44:12
      control the open and the permissions
      44:14
      that are going to be placed on the
      44:16
      object so that'll open an existing
      44:19
      object will open and create a new object
      44:23
      the name looks just like for POSIX
      44:25
      semaphores you say slash some name you
      44:30
      can see that name in a dedicated temp
      44:33
      file system and slash dev slash sh m you
      44:39
      get back a file descriptor that you
      44:40
      using the rest of the API ok the flags
      44:45
      most of them look pretty familiar
      44:47
      create the object if it doesn't exist
      44:49
      make it an exclusive create door
      44:52
      meth-head this mapping to read-only
      44:54
      access or for reading and writing and if
      44:58
      it's an existing object I can truncate
      45:00
      it to 0 length and I can set the
      45:03
      permissions whoops
      45:14
      okay unlike the open system call this
      45:18
      map this argument is always required but
      45:21
      if you're not creating a new object use
      45:25
      must specify mode as zero but you can't
      45:28
      omit it
      45:32
      so an example they're creating and
      45:37
      mapping a new shared memory object size
      45:39
      fights account a shared memo pin' give
      45:43
      my path name I want to create it
      45:45
      exclusively for reading and writing
      45:48
      permissions for reading and writing by
      45:50
      the owner only I change the size of the
      45:54
      object to begin with a new object has
      45:56
      zero size so I say F truncate that file
      46:00
      descriptor to my desired size and then I
      46:04
      say map that object add an address
      46:08
      chosen by the system here's its size
      46:10
      again here is the permission that I want
      46:14
      on their memory I want a map and share
      46:17
      so other people other processes can see
      46:19
      my updates here's the file to screw it
      46:22
      on up to map and man from offset zero in
      46:27
      their shared memory object and I get
      46:31
      back the interests where the mapping is
      46:32
      placed
      46:36
      again I think I've run through all of
      46:39
      those bits and pieces haven't I any
      46:47
      questions about the head
      46:52
      okay now if you've got an existing
      46:54
      shared memory object things look
      46:59
      slightly different you say open it
      47:00
      there's the path name I'm going to read
      47:04
      and write that object zero for the mode
      47:09
      because I'm not creating the object so I
      47:11
      have to specify zero there as the
      47:13
      permissions then I need to find out how
      47:18
      big that object is because someone else
      47:20
      is already creative oven sized it so I
      47:22
      make an F static call which passes one
      47:25
      of these static structures as a second
      47:27
      argument and as a result I get back the
      47:31
      size of that object and I use that in my
      47:34
      m-met all to specify having my mapping
      47:36
      is and then it looks very much the same
      47:40
      as before
      47:55
      okay the thing is then you've got to
      47:59
      process that share memory how do they
      48:03
      synchronize their access so that one
      48:06
      process doesn't try to an update doesn't
      48:08
      say at the same time is another process
      48:09
      well one process doesn't try and read
      48:11
      data from the shared memory at the same
      48:14
      time another process is in the middle of
      48:15
      updating their data so you need a
      48:19
      synchronization technique there are two
      48:24
      commonly used synchronization techniques
      48:27
      for synchronizing between processes on
      48:29
      on Linux or UNIX systems generally
      48:33
      semaphores which the technique usually
      48:36
      used with shared memory and firewalls
      48:38
      which are the technique usually used for
      48:40
      synchros xs2 files and what I want to
      48:44
      right now is POSIX semaphores okay a
      48:48
      positive symbol for is just an integer
      48:50
      that is held inside the kernel and it
      48:53
      has a special property that if you try
      48:54
      and update the integer in a way that
      48:56
      would push its value below zero then the
      48:59
      kernel just blocks to call until such a
      49:04
      time as the operation can be done
      49:06
      without pushing the semaphores value
      49:08
      below zero so for example if the
      49:11
      simplest value is currently zero and
      49:13
      someone tries to decrease this to minus
      49:15
      one the call block until someone else
      49:19
      comes along and adds one to the value of
      49:20
      the semaphore then your operation will
      49:23
      proceed and you can decrease the symbol
      49:26
      again to zero okay two fundamental
      49:29
      operations for A+ semaphore Symposium
      49:33
      any increment the semaphore by one sim
      49:35
      weight decrement symbol for by one sorry
      49:39
      about that
      49:39
      these tigers will be fixed by the time
      49:42
      the slides ago up okay
      49:45
      so post has increased weight is decrease
      49:48
      and the weight operation is the one that
      49:51
      can block
      49:55
      okay assume for just an abstract thing
      50:00
      it just represents whatever shared
      50:02
      resource you care to associate with it
      50:04
      it could be a bunch of printers it could
      50:07
      be a bunch of buffers and memory that
      50:09
      are being shared between processes your
      50:14
      semaphore value basically represents the
      50:16
      number of available resources of that
      50:17
      type that you're sharing so a semaphore
      50:21
      can have an arbitrary or an initial
      50:23
      value than a some arbitrary number if
      50:25
      you're sharing five buffers between
      50:26
      processes
      50:27
      you're still fours initial value might
      50:28
      be five and then you decrease it as
      50:30
      those buffers are allocated out to
      50:32
      processors and if they're all allocated
      50:37
      out the semaphore value becomes zero and
      50:39
      no one else can allocate a buffer until
      50:42
      one of the processes releases the symbol
      50:45
      increases the value of the semaphore
      50:48
      a very common way of using semaphore is
      50:54
      though is just zero and one in other
      50:57
      words a binary semaphore in other words
      50:59
      there's a single resource and the same
      51:02
      for only has the value zero and one the
      51:07
      example that is relevant there for us is
      51:09
      shared memory normally you only want one
      51:12
      process to update the shared memory at a
      51:14
      time so this the simple is value is
      51:18
      either 0 or 1 there's two types of
      51:23
      positive semaphores there's one are
      51:25
      called unnamed semaphores or anonymous
      51:27
      semaphores
      51:30
      the way to share those between processes
      51:32
      is to put them into the same piece of
      51:35
      shared memory so you can do something
      51:37
      like creating shared memory object and
      51:38
      embedding a sim for inside that shared
      51:41
      memory object the other flavor is named
      51:45
      7/4 and they these exist independently
      51:49
      of anything else now though there are
      51:51
      objects that you create and manipulate
      51:53
      and then destroy or unlink then you've
      51:59
      got a bunch of operations for performing
      52:02
      on the same force okay for unnamed
      52:04
      semaphores
      52:05
      these the operations you can perform
      52:07
      semmen it says I want to initiate
      52:09
      initialize the semaphore to a given
      52:13
      value and you specify as well I think
      52:17
      all the peach shared parameter and the P
      52:20
      share permanence is what kind of sharing
      52:24
      do I want for the simple are you going
      52:26
      to say well this semaphore is going to
      52:27
      be shared between threads so I specify
      52:30
      zero here or it's going to be non zero
      52:33
      which means I share between processes
      52:34
      and depending on what you're wanting to
      52:37
      do there this is the hint to the kernel
      52:39
      which may help but optimize the use of
      52:42
      the semaphore okay depending whether
      52:45
      you're sharing between processes or
      52:47
      threads symposium of the semaphore sim
      52:53
      weight decrease one from the value of
      52:56
      the semaphore sim destroy I finish with
      52:59
      the sim for free all the resources
      53:01
      associated with the sim four so they can
      53:04
      be reused by the system so an example of
      53:11
      how you might use semaphores if you
      53:15
      tried to use shared memory as a kind of
      53:17
      pipe you could have a reader and writer
      53:21
      and implement this sort of scenario
      53:23
      where I've got a writer here that is
      53:26
      reading data from somewhere else writing
      53:29
      into the shared memory and another
      53:32
      process which is reading from the shared
      53:33
      memory copying their data out and
      53:35
      perhaps writing it ah another type of
      53:41
      should be saying copy the data block
      53:43
      from shared memory to standard out help
      53:47
      me fix this file okay now you can the
      53:52
      point here is that you wanted two
      53:54
      processes to have synchronized access to
      53:59
      memory so that this process here doesn't
      54:01
      try and read at the same time as this
      54:03
      process is trying to write that's one
      54:05
      thing you want to achieve you could do
      54:07
      that with a single semaphore but not
      54:09
      only that you want to enforce lockstep
      54:11
      assets in other words first of all
      54:14
      Reiter has a tune then the reader then
      54:16
      the writer then the reader then the
      54:18
      writer then the reader otherwise you
      54:20
      might have a situation where a writer
      54:21
      writes two buffers in their memory one
      54:24
      on top of the other before the reader
      54:25
      has had a chance to read one of those
      54:27
      buffers okay and the way to do that then
      54:30
      is with two semaphores which I've called
      54:33
      the writer semaphore and the reader
      54:35
      semaphore okay you set things up so that
      54:39
      initially the writer semaphore is
      54:41
      available it has the value 1 and the
      54:43
      writer says okay I'm going to wait till
      54:45
      the simple flow value is 1 then 1 of
      54:48
      this 1 or decrease it and update the
      54:50
      shared memory and then I'll tell the
      54:54
      reader that it is semaphore is now
      54:56
      available okay it's said before we
      54:58
      assume to begin with has the value 0 and
      55:00
      the reader is going through a similar
      55:03
      sort of lutely
      55:04
      it says I'm going away from my semaphore
      55:05
      to be available then I'll read the data
      55:08
      from the shared memory and then I'll
      55:12
      tell the writer they can have a turn so
      55:15
      you've constantly got the semaphores
      55:16
      flipping backwards and forwards to allow
      55:19
      the two processes to alternate through
      55:21
      access ok I just will look at a little
      55:28
      bit code that does this so here's a
      55:32
      buffer or here's the data structure I'm
      55:35
      going to use the structure piece of
      55:37
      shared memory to other to allow this
      55:40
      sort of data transfer the buffer has the
      55:44
      two semaphores and better than silent
      55:46
      the writer semaphore on the reader
      55:48
      semaphore and then there is the data
      55:52
      buffer itself and the counter that says
      55:55
      how much data is actually being used
      55:56
      inside that buffer then my writer
      56:01
      process looks like this I create a block
      56:05
      of positive shared memory in the usual
      56:07
      way with shared memory open size with M
      56:10
      truncate mapping of the memory and then
      56:13
      inside that block of memory I initialize
      56:16
      the two semaphores
      56:17
      I initialize the reader semaphore okay
      56:21
      they're both
      56:22
      share the reader semaphore against the
      56:25
      value 0
      56:25
      the writer semaphore gets the value 1
      56:28
      and going back to that diagram that
      56:31
      means the writer is going to be the
      56:33
      first one to get a turn because when the
      56:35
      reader tries to wait for it's 7 or it
      56:38
      will block okay and then we just go into
      56:45
      a loop where the writer is reading some
      56:48
      bytes from standard input its wait for
      56:52
      it 7 for to become available read some
      56:55
      data from standard input puts it into
      56:58
      the buffer heart of the shared memory
      57:02
      updates the count inside that okay going
      57:07
      back here and we're going to count and a
      57:09
      buffer updates the count and the buffer
      57:15
      and then sends to the reader
      57:17
      there's something there for you to do
      57:18
      now and it posts its semaphore and it
      57:22
      just goes round and around that loop at
      57:25
      the end it's going to want it's going to
      57:29
      break out of the loop when there is no
      57:31
      more data to read okay so when the
      57:34
      rehear returns in the file and at that
      57:36
      point it'll just do one more wait to
      57:38
      wait for the reader to finish its last
      57:40
      access to the memory and then the writer
      57:44
      can now do all the cleanup is required
      57:46
      like destroying shared memory and
      57:47
      semaphores the reader okay it's just
      57:53
      similar thing but in Reverse it opens
      57:55
      and exists things share memory objects
      58:01
      it opens an existing shared memory
      58:03
      object maps into memory and then goes
      58:06
      into a loop where it waits for its
      58:07
      semaphore and then it writes data from
      58:13
      the buffer count bytes oh okay that's a
      58:19
      little type of there as well sorry I was
      58:22
      trying to traumatic handling code and I
      58:24
      just miss trimming out that piece
      58:26
      and then it tells the writer it's now
      58:29
      your turn
      58:31
      and this is the loop of termination
      58:33
      piece here which says well if the buffer
      58:36
      that I've received just now has zero
      58:38
      length that means the writer had nothing
      58:39
      yield for me so I'll just break out of
      58:41
      the loop and then I'll tell the writer
      58:43
      I'm finished now and that was
      58:46
      synchronized with that blast wait it was
      58:50
      performed by the writer so the right I
      58:52
      know the reader is finished
      58:55
      any questions
      58:59
      is there any key speeds your first sim
      59:08
      force of you amusingly being called the
      59:10
      go-to of synchronized programming no now
      59:16
      anybody can manipulate or anybody's got
      59:20
      permissions on the object it can
      59:23
      manipulate the same or in any direction
      59:24
      so it's a different for example from a
      59:27
      mutex where when you're acquiring mutex
      59:29
      you own the mutex and only you can
      59:31
      release mutex latency when compared to a
      59:42
      pipe I try not to make any statements
      59:46
      about performance the reason the reason
      59:48
      it's not it's not a lot being glue it's
      59:51
      like every example depends on your
      59:54
      application the only the only sensible
      59:58
      thing to do is to benchmark your
      60:01
      particular application you know to say
      60:03
      you know semaphores are faster than some
      60:06
      other mechanism all shared memories
      60:08
      faster than pipes it all depends on your
      60:11
      use case
      60:16
      having said that mutexes are pretty fast
      60:19
      if you talk about threaded programming
      60:21
      list
      60:30
      that's unnamed semaphores name
      60:33
      semaphores again the story last I look
      60:36
      pretty familiar here how long do I have
      60:38
      by the way great ok name symbols looks
      60:49
      very similar to the POSIX API as we've
      60:51
      seen already you've got an open call
      60:54
      that says open and possibly create me an
      60:57
      object unless is get rid of the path
      61:01
      name for that object so you say sim open
      61:05
      the name of the object you want open
      61:07
      flags that control the call permissions
      61:11
      for the object you're creating and here
      61:13
      you can specify the initial value of the
      61:15
      new semaphore you're creating those last
      61:18
      two arguments are only needed if you are
      61:22
      if you are creating a new semaphore
      61:26
      again the name has this full session
      61:29
      some name again you can see the
      61:32
      semaphores the name semaphore see credit
      61:35
      in this temp if s and /dev /s hm you get
      61:42
      back a point into a sim T which is just
      61:45
      a reference to the semaphore and use
      61:47
      that in the rest of the API so the flags
      61:50
      you can specify their create ok I want
      61:52
      to create the object it doesn't exist
      61:54
      created exclusively I want to make sure
      61:56
      I am the one that created this and as I
      62:00
      said the other two arguments here only
      62:02
      needed if you're creating a new
      62:03
      semaphore Oh
      62:08
      and that's as much as I have to say that
      62:10
      semaphores any questions then we are get
      62:17
      it time to get to file locks and other
      62:20
      pieces okay so next IPC mechanism
      62:31
      sockets socket sir
      62:34
      really is a very big topic so I'm not
      62:37
      going to try and go into the details of
      62:38
      the eight guys but just to give you some
      62:41
      very high-level picture of how sockets
      62:43
      or how the API pieces work together and
      62:48
      and then mention some notable features
      62:49
      about sockets as a method of inter
      62:51
      process communication the very first
      62:57
      style is what the very first I think
      62:59
      publish talking about sockets that means
      63:01
      way to the outside world had this
      63:03
      wonderful sentence a socket is an end
      63:05
      point of communication which completely
      63:07
      baffled me when I read it because the
      63:11
      next thing instead of said you need two
      63:13
      of them okay you create two sockets and
      63:16
      then you join them together or you send
      63:19
      messages between them sockets are unlike
      63:25
      pipes for example that bi-directional
      63:28
      with a socket you can both read and
      63:30
      write data on the socket you create them
      63:35
      with a socket system call and you
      63:37
      specify three things in their system
      63:40
      call and I'll talk about two of them in
      63:41
      a moment a domain and a type you will
      63:44
      specify a protocol but for most for all
      63:48
      the cases I'm going to talk about the
      63:49
      protocol is always zero but there are
      63:51
      some cases we can specify a non zero
      63:53
      protocol usefully you can make a file
      63:56
      descriptor which refers to the socket so
      63:58
      it's it looks like a similar sort of
      64:01
      thing through what you get from a pipe
      64:03
      in terms of a files file descriptor
      64:06
      so I said their sockets have a domain
      64:10
      and a type well at the main is specifies
      64:16
      that essentially the range of
      64:17
      communication for that socket and whoops
      64:23
      is it the communication on a single host
      64:26
      or as a communication across a network
      64:28
      that's the thing that's specified by a
      64:30
      domain and coupled with that then is the
      64:33
      method of how you're going to identify
      64:34
      the song others this socket okay either
      64:39
      gonna have a method that identifies the
      64:41
      socket on a single host or it identifies
      64:43
      the socket in the context of a network
      64:48
      there are three common domains that are
      64:52
      used by almost all other available all
      64:54
      UNIX systems and most other systems
      64:56
      nowadays as well the UNIX sorry I'll
      65:01
      rephrase that
      65:02
      these two are available most systems
      65:04
      this one is a very only on UNIX systems
      65:07
      normally UNIX domain it's for
      65:10
      communication on a single host the
      65:13
      address of a UNIX domain socket is just
      65:16
      the path name okay so that's how
      65:20
      applications identify each other's
      65:22
      sockets
      65:23
      IP for domain I affine it is for
      65:26
      communication over an ipv4 network the
      65:29
      address that's used for identifying
      65:31
      sockets is an IP fee i pv for host
      65:34
      address 32 bits and a port number the
      65:37
      port number they're just distinguishes
      65:39
      okay you've got 20 different
      65:41
      applications on the same host you need a
      65:43
      way of distinguishing those various
      65:45
      application will that connect the
      65:46
      different port numbers okay and so when
      65:50
      I want to
      65:50
      oops
      65:53
      when I want to connect to a particular
      65:55
      application on an ipv4 host I fit
      65:58
      specify the IP address of that machine
      66:00
      and
      66:02
      the port number where I know that
      66:03
      application is received is connected i
      66:07
      pv e6 it's the same idea but across ipv6
      66:11
      networks 128 but address this time plus
      66:15
      a port number now the other thing that
      66:21
      you've got when you create a socket
      66:22
      using the socket system call is the type
      66:27
      the type tells you about the semantics
      66:30
      of the communication and you've got two
      66:34
      main types available
      66:35
      you've got stream sockets which are byte
      66:38
      stream and you've got data grammar
      66:40
      sockets which are message oriented some
      66:43
      systems including Linux provide other
      66:47
      socket types one socket ID View final
      66:51
      notes with UNIX domain sockets is
      66:53
      sequential package socket and a screen
      66:56
      shil packet socket as I'll mention a bit
      66:57
      later on is sort of like a cross between
      66:59
      these two it it falls somewhere in
      67:01
      between so as a stream socket it's a
      67:07
      byte stream
      67:10
      it's a byte stream so therefore
      67:12
      necessarily connection-oriented you've
      67:14
      got to it's like a telephone call you
      67:17
      have two sockets that are connect to
      67:19
      each other and they just streaming bytes
      67:21
      to one another readers can read any
      67:25
      arbitrary number of bytes writers can
      67:27
      write any arbitrary number advice
      67:29
      there's no connection between what the
      67:31
      reader must read versus what the write
      67:34
      has written you guys yeah so you can do
      67:41
      things in both directions there's two
      67:42
      channels on a socket yeah a stream
      67:47
      socket is reliable what that means is
      67:50
      either the data arrives intact or
      67:52
      doesn't arrive at all in other words
      67:54
      things just broke down completely in
      67:57
      tech means it arrived in order and
      67:59
      unduplicated need to the main this is
      68:03
      accomplished over the TCP protocol
      68:07
      just too slow okay then you've got
      68:15
      diagram sockets it's a message oriented
      68:20
      type of communication
      68:21
      it's connectionless it's like the postal
      68:23
      system you can fire out a message using
      68:27
      your unit your data gram socket you can
      68:30
      fire a limb as to any other socket any
      68:32
      other diagram socket that whose address
      68:36
      you know it's unreliable what that means
      68:42
      is messages may be duplicators they may
      68:46
      may arrive out of order well they might
      68:48
      not arrive at all your application level
      68:52
      logic has to handle that possibility in
      68:55
      the Internet domain diagram sockets are
      69:00
      implemented using the UDP protocol
      69:07
      this is the type of socket that I
      69:09
      mentioned it's not so commonly used
      69:11
      because not all systems have it but a
      69:13
      screen shal packet socket is a bit like
      69:15
      a mixture of those two sockets types
      69:18
      that I've talked about just already it's
      69:19
      message oriented communication so whole
      69:22
      messages are read and written but it's
      69:24
      connection oriented and reliable okay
      69:27
      and in the on linux that's only
      69:31
      available on the UNIX domain one I'll
      69:35
      rephrase have on looks you have
      69:37
      sequential packets on the UNIX domain
      69:39
      you do have them also in the Internet
      69:41
      domain if using something for the SCTP
      69:44
      transport protocol okay which is an
      69:47
      alternative to UDP and TCP but it's not
      69:51
      so widely available as the traditional
      69:54
      protocols you just know what yeah is
      70:01
      that is it only use sequential packet
      70:04
      yeah no because it's not so widely
      70:07
      available but if you're writing just a
      70:10
      Linux specific applications absolutely
      70:12
      no reasonably can't use a UNIX domain
      70:13
      sequential packet socket
      70:20
      said it's that visible down the back
      70:22
      because I've got also dispersion in two
      70:25
      pieces okay this is just the idea is
      70:29
      just to show you here how all the system
      70:31
      calls that are working with stream
      70:34
      sockets I use together so I've just to
      70:38
      make it a bit more visible and cut the
      70:39
      diagram in two pieces and so the top
      70:42
      half is this piece here to do stream or
      70:49
      into communication with sockets you
      70:51
      normally have what are called an active
      70:53
      and a passive socket an active socket is
      70:59
      the one who starts up the connection if
      71:03
      you like the permissive sorry the
      71:06
      passive socket is the one that waits for
      71:09
      a connection okay so if you're in a way
      71:13
      for a connection there's various things
      71:14
      you have to do first you have to create
      71:15
      your socket binds is give my socket an
      71:19
      address so that other people know where
      71:21
      I am
      71:22
      listen says well I may have multiple
      71:26
      clients that are going to connect to me
      71:27
      and I may not be able to say that main
      71:30
      that may not be able to respond them all
      71:33
      immediately to their connection requests
      71:35
      I'm going to set up a queue of length in
      71:39
      to another those incoming connection
      71:42
      requests to queue up and wait until I've
      71:45
      got a chance to deal with them and then
      71:49
      accept says I want to accept the
      71:52
      connection so commonly never a an
      71:56
      application that performs an accept and
      71:58
      a loop if it wants to deal with multiple
      72:00
      clients it will do it except to accept
      72:04
      one connection duly i/o and their
      72:06
      connection perhaps and then go around a
      72:08
      loop begin to accept the next connection
      72:13
      accept blocks until an active socket
      72:17
      comes along okay an active client says
      72:20
      I'm gonna credit socket and then I'm
      72:22
      going to connect to the address that was
      72:24
      specified here by the bind at that point
      72:28
      the exceeding um
      72:30
      and the two processes can in communicate
      72:32
      using traditional system calls read and
      72:35
      write backwards and forwards when I've
      72:38
      done they can close their ends of the
      72:40
      solvent that's the same direction that
      72:48
      I've just gone through and in summary
      72:51
      form any questions okay for i/o you've
      72:58
      got read and write and close they're
      73:01
      also too sore there a number of socket
      73:03
      specific system calls but to the you
      73:05
      might use with stream sockets are send
      73:06
      and receive I just like write and read
      73:09
      except they've got an extra flags
      73:10
      argument that allows you to add some
      73:13
      socket specific behavior to this to the
      73:16
      i/o operation any questions about stream
      73:24
      sockets when you're using 13b to the
      73:31
      times when you say routes the time so
      73:33
      cure you right in say you know yeah no
      73:38
      problem
      73:39
      it wouldn't be common to do so but
      73:41
      there's actually no reason why you can't
      73:50
      okay data Graham sockets are a whole lot
      73:53
      simpler in a way because there's no
      73:55
      connection involved all it happens is
      73:57
      that two processes create their sockets
      74:00
      at least one of them has to bind to an
      74:03
      address and then they can send data to
      74:07
      and from each other
      74:09
      using two special sockets system calls
      74:12
      receive from and send to send to says I
      74:15
      wanna send data and you specify an
      74:17
      address receive from says okay give me
      74:21
      some data that's being sent to me
      74:23
      receive from has the competitor also
      74:26
      that's what is receiving the down to
      74:28
      receive the sender's address
      74:30
      so after received from you can typically
      74:33
      turn straight around and do a send to
      74:36
      back to the address that you've just
      74:37
      discovered and again when the two
      74:42
      processes are finished they can close
      74:43
      their sockets in questions
      74:54
      okay again the same motion summarized so
      75:00
      I just want to mention a few noteworthy
      75:02
      points about sockets reasons why you
      75:06
      might choose to use sockets for
      75:09
      particular purposes I said that UNIX I
      75:12
      said that data gram sockets are not
      75:14
      reliable well in the UNIX domain they
      75:17
      are because it's all happening on the
      75:19
      same host system there's no problems
      75:22
      with networks to worry about
      75:24
      so UNIX domain sockets at least on Linux
      75:27
      I think every unit system are reliable
      75:30
      UNIX domain data gram sockets point to
      75:36
      the secret packets of UNIX you know if
      75:40
      you know it is a reliable that's what
      75:43
      this
      75:49
      I uh III think in theory there is no
      75:56
      reason or practice there is no reason
      76:00
      yeah is that there's a portable yeah
      76:03
      that's nice
      76:05
      you might want to stick to diagram
      76:07
      sockets yes i i'm not sure certainly in
      76:13
      the early unix systems that i came
      76:15
      across
      76:16
      there was never really stated guarantee
      76:19
      about UNIX domain sockets statigram
      76:21
      sockets being reliable
      76:22
      I never thought back then to actually
      76:25
      test that and other systems I don't have
      76:27
      them anymore it's a long time ago but
      76:30
      you know every modern system live there
      76:34
      will test UNIX domain sockets are
      76:36
      reliable you can adjust to any data
      76:38
      Graham sockets are reliable another
      76:45
      interesting thing you can do with the
      76:47
      UNIX domain sockets which is quite
      76:49
      special is you can pass file descriptors
      76:52
      across UNIX domain socket and what I
      76:54
      mean by this is you've got two processes
      76:57
      one of them opens a file and then
      77:00
      creates a connection to another process
      77:04
      using a socket and then uses a special
      77:08
      system call a special soccer related
      77:10
      system call send message to actually
      77:14
      pass the file descriptor that's just
      77:17
      open to the other process so it's a way
      77:20
      in effect passing access rights to a
      77:23
      file object from one process to another
      77:25
      and then actually finds a lot of uses in
      77:29
      different in different places
      77:34
      involve sorry for all sorts of file
      77:38
      descriptors I think among others for
      77:43
      instance systemd uses as technique as
      77:45
      well for hanging its pieces together I
      77:48
      forget the details Network sorry listen
      77:57
      when you're passing our script is kept
      78:00
      processors yup with visions prevent them
      78:08
      no this is a short-answer visual at all
      78:12
      and replication control so you know the
      78:15
      two processes of creative the socket one
      78:17
      assumes that they've decided they want
      78:19
      to pass the file description between
      78:20
      them so you know the application is not
      78:22
      going to pass a file descriptor to some
      78:25
      other random process these are processes
      78:28
      that are designed to work via so there's
      78:31
      no need for access control as such the
      78:33
      applications already enforced have yeah
      78:44
      so internet domain sockets are the only
      78:47
      method of IPC over a network so that's
      78:51
      the reason why right my wife why you
      78:52
      might wanna use Internet domain sockets
      78:55
      UDP sockets have the characteristic that
      78:58
      you can broadcast a Datagram to multiple
      79:02
      receivers or multicast
      79:05
      data gram to selected receivers one
      79:10
      other system call that I didn't mention
      79:12
      so far but which again might be useful
      79:15
      socket pair is a system core which
      79:18
      creates essentially a bi-directional
      79:19
      pipe okay it looks just like a pipe call
      79:23
      you give it an array of two file
      79:25
      descriptors it gives you back a reading
      79:27
      important I'll give you about two ends
      79:29
      of a soccer pair that you can invoke and
      79:33
      to process communicate in a
      79:35
      bi-directional fashion using that socket
      79:41
      pair
      79:47
      12 train is it
      79:50
      we have 5-10 live in science okay we'll
      79:54
      have five minutes question
      79:55
      okay so you've got a good another 20
      80:01
      minutes I try to have a little think
      80:02
      about whether I'm going to try and
      80:03
      squeeze anything more other than a few
      80:08
      discriminating factors between IPC
      80:10
      mechanisms now I think I'll just see how
      80:20
      I go with these pieces okay there are
      80:23
      some other criteria that you might use
      80:25
      for selecting between IPC mechanisms and
      80:28
      I just want look at some of those there
      80:30
      are some obvious things consistently
      80:32
      with your application design
      80:34
      functionality that it's consistent with
      80:36
      what you want but there are the criteria
      80:39
      as well first of all every IP saab
      80:42
      object has an identifier which is the
      80:46
      thing that identifies you how do you
      80:48
      find the IBC object and a handle which
      80:51
      is the thing that you use to refer to
      80:52
      the object when you are actually working
      80:55
      with it so if we talked we took that
      80:58
      analogy back to files
      81:00
      the idea is the path name of a file a
      81:02
      handle is the file descriptor okay
      81:06
      there's an analogy in most cases with IP
      81:09
      each kind of IDC object as well so what
      81:14
      I've got there is for various IBC
      81:17
      methods a table that says well how
      81:20
      objects identified and what kind of
      81:23
      handle do you actually use to refer to
      81:25
      them when you're working with them
      81:27
      inside a program so pipes for example
      81:30
      have no name by foes have a file system
      81:33
      path name UNIX domain sockets have a
      81:36
      file system path name Internet domain
      81:39
      sockets have a an address and a port
      81:41
      number and so on and so on when you're
      81:45
      working with those objects inside a
      81:47
      program well many objects are accessed
      81:50
      via a file descriptor there are some
      81:53
      other possible handles there I talked
      81:57
      already about a message queue descriptor
      82:00
      1778 salmon score
      82:02
      T star for semaphores I haven't talked
      82:09
      about system v IPC but they have their
      82:12
      own special identifiers which are weird
      82:15
      and wonderful one of the relevances of
      82:21
      all that is the things that use file
      82:24
      descriptors can be multiplexed so any of
      82:29
      those techniques there that uses a file
      82:32
      descriptor for data communication so
      82:34
      pipes fibers sockets where owing message
      82:41
      queues okay message juice a message
      82:44
      queue descriptor really is a file
      82:45
      descriptor
      82:46
      any of those techniques can be used with
      82:49
      pole select an D pole so you can walk
      82:52
      you can monitor multiple descriptors at
      82:55
      the same time to see if I always
      82:56
      possible on any of them okay you often
      83:00
      have a situation we've got an a network
      83:03
      server for instance that is handling
      83:05
      multiple clients at the same time it
      83:07
      wants to find out whether there's
      83:09
      something to read from all of those
      83:11
      clients well it can monitor all of those
      83:13
      clients at once using one of these
      83:15
      mechanisms yeah I'm sorry I don't have
      83:23
      time to go into there
      83:24
      I told me you know this does exist it's
      83:26
      a mechanism for monitoring more than
      83:30
      files for the sink is it possible to do
      83:32
      i io now on any of these file
      83:34
      descriptors I think you know I think
      83:39
      there are now examples and all of those
      83:41
      manual pages so you should be able to
      83:43
      piece it together it's just worth
      83:47
      knowing they exist
      83:52
      so you can use the Snickers with all of
      83:55
      those IPC mechanisms and this is one
      83:57
      more good reason why people avoid system
      83:59
      5 message queues because they don't use
      84:01
      file descriptors and you can't use these
      84:04
      techniques another discriminating factor
      84:10
      for why you might choose particular IPC
      84:12
      mechanism is how is the access the
      84:14
      mechanism controlled well there are
      84:17
      various possibilities it could be based
      84:19
      on user and group IDs and permissions
      84:21
      masks it could be based on with the
      84:24
      processor related via fork or it could
      84:27
      be something else
      84:28
      it could be something determined by the
      84:31
      application for instance which is what
      84:33
      happens with Internet domain sockets so
      84:38
      again I've calculated there for various
      84:40
      IPC mechanisms what the mechanisms that
      84:45
      defines accessibility for a lot of
      84:48
      mechanisms it is a permissions master in
      84:50
      other words a user and group ID that are
      84:52
      associated with the object and
      84:54
      read/write permissions for three
      84:56
      different classes of user somewhat which
      84:59
      like pipes it's related processes and
      85:06
      then where are we
      85:09
      I have socket on this table here for
      85:11
      sockets any process can access the
      85:14
      socket but the applications determine
      85:17
      who is who they're going to communicate
      85:18
      with I was magic
      85:25
      get the actual yeah the resides
      85:30
      yep I'm great you can and you can lie
      85:36
      about it then you can spoof if you have
      85:39
      a sufficient privileges so it's it's
      85:41
      then you have sufficient privilege
      85:44
      yeah I'll but sorry I need to pause
      85:53
      there that's for UNIX domain sockets
      85:56
      yeah so where where else I was talking
      85:58
      about Internet domain yeah yeah okay so
      86:08
      for semaphores for unnamed semaphores it
      86:11
      depends on what the permissions are the
      86:13
      underlying memory how the memory is
      86:15
      being shared another discriminating
      86:23
      factor for how you might choose an IPC
      86:25
      is based on its persistence okay how
      86:29
      long does the IPC object live well it
      86:32
      could just have process subsistence what
      86:34
      that means is the object only exists as
      86:37
      long as at least one process is
      86:39
      connected to it it could have kernel
      86:42
      assistance which means it lives as long
      86:44
      as this root of the kernel lives and
      86:48
      system reboots then the IPC object has
      86:51
      gone or it could have file system
      86:53
      persistence which means that actually
      86:55
      might live even across a reboot so for
      87:01
      instance some of these ideas in make
      87:04
      another's onion process resistance pipes
      87:08
      and fibers the data and the pipe or a
      87:12
      FIFO only exists as long as at least one
      87:14
      process is connected to that FIFO okay
      87:18
      that's notable by the way a FIFO has a
      87:20
      name in a file system but that doesn't
      87:22
      mean that its data is persistently
      87:24
      process actually has the FIFO open
      87:30
      various other mechanisms have processed
      87:33
      assistants POSIX message choose and
      87:36
      semaphores and opossums share memory
      87:39
      have a kernel persistence so what that
      87:41
      means is I can I pop ten minutes I can
      87:46
      create a positive message to put some
      87:49
      messages on it and then disconnect from
      87:52
      the positive message to my process
      87:54
      contaminate those messages will still
      87:56
      stay there until some other process
      87:58
      comes along opens the message queue for
      88:00
      reading and read the messages but
      88:03
      they'll disappear if the system if the
      88:05
      system reboots there's one type of
      88:11
      object which you might loosely say has
      88:13
      filesystem persistence which is memory
      88:16
      mapped files okay if you map a file into
      88:19
      memory as a shared file mapping in the
      88:22
      updates that you make to that file are
      88:25
      either the argument you make to the
      88:28
      memory are pushed through to the file
      88:29
      well obviously if I push through the
      88:32
      file the file survives of revange
      88:34
      okay you could then remap that file
      88:37
      after the system restarts
      88:45
      any questions on those discriminating
      88:47
      factors and I think I will stop there
      88:49
      and take questions
      88:50
      okay sorry more look at the car
      89:02
      essentially it's the Colonel's paging
      89:05
      mechanism which just keeps things
      89:07
      instead in sync so the match file is
      89:13
      essentially the paging store for that
      89:16
      memory which means that who knows when
      89:19
      and precisely updates but you can
      89:22
      control it with this M sync system call
      89:25
      does that apply the process exists and
      89:29
      it's beautifully other other pages
      89:32
      updated the person sees killed I always
      89:37
      do an M Singh it's never been quite
      89:41
      clear to me reading documentation and
      89:43
      standards what is guaranteed but if you
      89:46
      do an M Singh you're supposed to have a
      89:47
      guarantee yeah just an Singh focusing in
      90:00
      privacy you probably don't care and more
      90:03
      an issue
      90:03
      it's
      90:04
      Tina so to me that assistance is
      90:08
      processed and stronger because you want
      90:11
      to help leggings actually kidnap crash
      90:14
      yeah it's a whole other end order that
      90:17
      you don't want things settle system-wide
      90:19
      so two sides crashed independently help
      90:22
      actually things fifteen that you don't
      90:24
      once system life is this led there yeah
      90:28
      it all depends on your use case yeah you
      90:31
      you don't necessarily want ten the sort
      90:36
      of assistance the last little bit of
      90:38
      questions so that I'm getting from there
      90:41
      they're not hearing the questions very
      90:42
      well in the Navy can I just get people
      90:44
      to put up their hands and I run the mic
      90:45
      over so who has questions okay so what
      90:55
      techniques do you use to to ensure that
      90:57
      there's a single instance of an
      90:59
      application running you know like say
      91:01
      Firefox for instance yes that's that's
      91:05
      kind of a kind of a separate question
      91:09
      but the point is you want to create some
      91:16
      you need to have some proxy to ensure
      91:18
      that in a way so some all people that
      91:20
      you create that only one one process can
      91:22
      create the British no way of doing that
      91:24
      on on on UNIX systems is to create a
      91:29
      file and place an exclusive lock on us
      91:34
      but you know you could create one of
      91:37
      these persistent IPC objects and if if
      91:39
      there's already one there
      91:41
      you could use that as your proxy for
      91:43
      deciding the process doesn't exist doing
      91:46
      things with a file has a few virtues
      91:48
      because people do things like writing a
      91:50
      process ID into the file to
      91:55
      so that if someone comes along and reads
      91:57
      the file they can say well actually it's
      91:59
      locked at the moment by this process so
      92:01
      I know where and then I can no actually
      92:04
      well maybe that process died you know
      92:06
      the PID was this but there's no process
      92:08
      with PID Anya this locked file is
      92:09
      actually in Bella or just delete it
      92:12
      that's the traditional unix way of doing
      92:15
      things
      92:21
      Oh
      92:26
      um this may be a little bit easy but a
      92:30
      while back I did a little bit of work
      92:32
      with Ubuntu 10.4 and I tried to see if
      92:38
      the socket was writable using selected
      92:41
      is to do with soccer - soccer yeah and I
      92:44
      got a sand message coral failure saying
      92:47
      bad descriptor error okay so so there's
      92:54
      a mistake I think what you're talking
      92:58
      about is a mistake if you bought to make
      92:59
      with the selectins pole system of course
      93:02
      what those system calls say I I think I
      93:06
      actually sent myself they tell you
      93:07
      whether a file descriptor is readable or
      93:09
      writable well what is really meant by
      93:11
      readable writable is a read call could
      93:14
      be performed without blocking or a write
      93:17
      call could be performed without blocking
      93:19
      now there might mean either that the
      93:22
      data can be written or the rifling is
      93:24
      going to fail straight away for some
      93:25
      reason and that's what select and pole
      93:28
      are telling you either the right will
      93:31
      succeed immediately or it's going to
      93:34
      fail because the descriptors are
      93:36
      writable for some reason we can use a
      93:40
      seam message lend to the macro and we're
      93:45
      just something in this later today while
      93:48
      using the CMS Geel and macro episode in
      93:52
      order to do what I do to fix the problem
      93:55
      with the maybe the language of the
      93:59
      selector I don't think so so that'll
      94:02
      help me to say without knowing more
      94:03
      about what you doing but I don't I don't
      94:06
      think so really
      94:07
      applications just if they using select
      94:08
      and pol need to be build around the
      94:11
      possibility that you know reading this
      94:13
      means it all it is is the system call
      94:16
      won't block okay which doesn't mean dot
      94:18
      all be transformed prints
      94:20
      it means either data or transferred or
      94:23
      the support has an error condition
      94:42
      sorry it's more of a statement than
      94:44
      question but I'd like to point out that
      94:46
      for the internet domain sockets I'm kind
      94:49
      of flying the flag for the ipv6 guys we
      94:52
      writing the server-side and you can use
      94:55
      the address family unspeak to support
      94:59
      either ipv4 or ipv6 so that your client
      95:02
      can connect using a fi Network a fine
      95:06
      insects yeah that's my ipv6 bug lot
      95:11
      lines it's a big topic any other
      95:16
      questions thank you very much
      95:28
      we have one really light color question
      95:30
      here as how do these compare to
      95:35
      something like D bus the bus is really a
      95:40
      much higher level sort of thing it's a
      95:42
      much it's a much higher level
      95:44
      abstraction where you I'm not know D bus
      95:48
      expert but the point is it's it's it's a
      95:51
      really it's a high level mechanism for
      95:53
      processes to discover each other and and
      95:59
      set up parameters for how they're going
      96:01
      to do things but it's D bus for instance
      96:04
      has very poor bandwidth and
      96:07
      responsiveness so you might use D
      96:10
      Buster's sing synchronize some stuff
      96:12
      between your desktop applications but if
      96:15
      you want to pass vast volumes of data
      96:18
      quickly you wouldn't use D bus for that
      96:22
      some shows as long as around a lot of
      96:27
      messages you know it's a very good
      96:32
      abstraction for you've got a bunch of
      96:35
      unrated processes they want to discover
      96:37
      each other okay any other final
      96:40
      questions okay um well can you all join
      96:45
      me in thanking Michael for a fantastic
      96:50
      we'd like to see you again
