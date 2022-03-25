# OOP
## What Is an Object?
An object is a software bundle of related state and behavior. Software objects are often used to model the real-world objects that you find in everyday life.

## What Is a Class?
A class is a blueprint or prototype from which objects are created.

## What Is Inheritance?
Inheritance provides a powerful and natural mechanism for organizing and structuring your software.

## What Is an Interface?
An interface is a contract between a class and the outside world. When a class implements an interface, it promises to provide the behavior published by that interface.

## What Is a Package?
A package is a namespace for organizing classes and interfaces in a logical manner. Placing your code into packages makes large software projects easier to manage.


# Exceptions
## What Is an Exception?
An exception is an event that occurs during the execution of a program that disrupts the normal flow of instructions.

## The Catch or Specify Requirement

Valid Java programming language code must honor the Catch or Specify Requirement. This means that code that might throw certain exceptions must be enclosed by either of the following:

A try statement that catches the exception. The try must provide a handler for the exception, as described in Catching and Handling Exceptions.
A method that specifies that it can throw the exception. The method must provide a throws clause that lists the exception, as described in Specifying the Exceptions Thrown by a Method.

Code that fails to honor the Catch or Specify Requirement will not compile.
Not all exceptions are subject to the Catch or Specify Requirement. To understand why, we need to look at the three basic categories of exceptions, only one of which is subject to the Requirement.

The Three Kinds of Exceptions
-----------------------------

- The first kind of exception is the checked exception. These are exceptional conditions that a well-written application should anticipate and recover from. For example, suppose an application prompts a user for an input file name, then opens the file by passing the name to the constructor for java.io.FileReader. Normally, the user provides the name of an existing, readable file, so the construction of the FileReader object succeeds, and the execution of the application proceeds normally. But sometimes the user supplies the name of a nonexistent file, and the constructor throws java.io.FileNotFoundException. A well-written program will catch this exception and notify the user of the mistake, possibly prompting for a corrected file name.
Checked exceptions are subject to the Catch or Specify Requirement. All exceptions are checked exceptions, except for those indicated by Error, RuntimeException, and their subclasses.

- The second kind of exception is the error. These are exceptional conditions that are external to the application, and that the application usually cannot anticipate or recover from. For example, suppose that an application successfully opens a file for input, but is unable to read the file because of a hardware or system malfunction. The unsuccessful read will throw java.io.IOError. An application might choose to catch this exception, in order to notify the user of the problem — but it also might make sense for the program to print a stack trace and exit.
Errors are not subject to the Catch or Specify Requirement. Errors are those exceptions indicated by Error and its subclasses.

- The third kind of exception is the runtime exception. These are exceptional conditions that are internal to the application, and that the application usually cannot anticipate or recover from. These usually indicate programming bugs, such as logic errors or improper use of an API. For example, consider the application described previously that passes a file name to the constructor for FileReader. If a logic error causes a null to be passed to the constructor, the constructor will throw NullPointerException. The application can catch this exception, but it probably makes more sense to eliminate the bug that caused the exception to occur.
Runtime exceptions are not subject to the Catch or Specify Requirement. Runtime exceptions are those indicated by RuntimeException and its subclasses.

Errors and runtime exceptions are collectively known as unchecked exceptions.

## How to Throw Exceptions
This section covers the throw statement and the Throwable class and its subclasses.

## The try-with-resources Statement
This section describes the try-with-resources statement, which is a try statement that declares one or more resources. A resource is as an object that must be closed after the program is finished with it. The try-with-resources statement ensures that each resource is closed at the end of the statement.

## Unchecked Exceptions — The Controversy
Here's the bottom line guideline: If a client can reasonably be expected to recover from an exception, make it a checked exception. If a client cannot do anything to recover from the exception, make it an unchecked exception.

## Advantages of Exceptions
Advantage 1: Separating Error-Handling Code from "Regular" Code
Advantage 2: Propagating Errors Up the Call Stack
Advantage 3: Grouping and Differentiating Error Types


# Basic IO

## I/O Streams

- Byte Streams handle I/O of raw binary data. Programs use byte streams to perform input and output of 8-bit bytes. All byte stream classes are descended from InputStream and OutputStream.

    ```java
    note: Always Close Streams

    in = new FileInputStream("xanadu.txt");
    out = new FileOutputStream("outagain.txt");
    int c;

    while ((c = in.read()) != -1) {
        out.write(c);
    }
    ```

- Character Streams handle I/O of character data, automatically handling translation to and from the local character set. The Java platform stores character values using Unicode conventions. Character stream I/O automatically translates this internal format to and from the local character set. In Western locales, the local character set is usually an 8-bit superset of ASCII.

    ```java
    inputStream = new FileReader("xanadu.txt");
    outputStream = new FileWriter("characteroutput.txt");

    int c;
    while ((c = inputStream.read()) != -1) {
        outputStream.write(c);
    }

    // also: Line-Oriented I/O
    inputStream = new BufferedReader(new FileReader("xanadu.txt"));
    outputStream = new PrintWriter(new FileWriter("characteroutput.txt"));

    String l;
    while ((l = inputStream.readLine()) != null) {
        outputStream.println(l);
    }
    ```

- Buffered Streams optimize input and output by reducing the number of calls to the native API.

    ```java
    inputStream = new BufferedReader(new FileReader("xanadu.txt"));
    outputStream = new BufferedWriter(new FileWriter("characteroutput.txt"));
    ```

- Scanning and Formatting allows a program to read and write formatted text.
Programming I/O often involves translating to and from the neatly formatted data humans like to work with. To assist you with these chores, the Java platform provides two APIs. The scanner API breaks input into individual tokens associated with bits of data. The formatting API assembles data into nicely formatted, human-readable form.

- I/O from the Command Line describes the Standard Streams and the Console object.
    ```java
    InputStreamReader cin = new InputStreamReader(System.in);
    ```

    - Console

        A more advanced alternative to the Standard Streams is the Console. This is a single, predefined object of type Console that has most of the features provided by the Standard Streams, and others besides. The Console is particularly useful for secure password entry. The Console object also provides input and output streams that are true character streams, through its reader and writer methods.

        ```java
        Console c = System.console();
        if (c == null) {
            System.err.println("No console.");
            System.exit(1);
        }
        String login = c.readLine("Enter your login: ");
        char [] oldPassword = c.readPassword("Enter your old password: ");    
        ```

- Data Streams handle binary I/O of primitive data type and String values.

    Data streams support binary I/O of primitive data type values (boolean, char, byte, short, int, long, float, and double) as well as String values. All data streams implement either the DataInput interface or the DataOutput interface. This section focuses on the most widely-used implementations of these interfaces, DataInputStream and DataOutputStream.

    Then DataStreams opens an output stream. Since a DataOutputStream can only be created as a wrapper for an existing byte stream object, DataStreams provides a buffered file output byte stream.

    ```java
    out = new DataOutputStream(new BufferedOutputStream(
                new FileOutputStream(dataFile)));
    for (int i = 0; i < prices.length; i ++) {
        out.writeDouble(prices[i]);
        out.writeInt(units[i]);
        out.writeUTF(descs[i]);
    }
    ```

    The writeUTF method writes out String values in a modified form of UTF-8. This is a variable-width character encoding that only needs a single byte for common Western characters.

    Now DataStreams reads the data back in again. First it must provide an input stream, and variables to hold the input data. Like DataOutputStream, DataInputStream must be constructed as a wrapper for a byte stream.
    ```java
    in = new DataInputStream(new
                BufferedInputStream(new FileInputStream(dataFile)));
    double price;
    int unit;
    String desc;
    double total = 0.0;
    try {
        while (true) {
            price = in.readDouble();
            unit = in.readInt();
            desc = in.readUTF();
            System.out.format("You ordered %d" + " units of %s at $%.2f%n",
                unit, desc, price);
            total += unit * price;
        }
    } catch (EOFException e) {
    }
    ```

- Object Streams handle binary I/O of objects.

    Just as data streams support I/O of primitive data types, object streams support I/O of objects. Most, but not all, standard classes support serialization of their objects. Those that do implement the marker interface Serializable.

    The object stream classes are ObjectInputStream and ObjectOutputStream. These classes implement ObjectInput and ObjectOutput, which are subinterfaces of DataInput and DataOutput. That means that all the primitive data I/O methods covered in Data Streams are also implemented in object streams. So an object stream can contain a mixture of primitive and object values. The ObjectStreams example illustrates this. ObjectStreams creates the same application as DataStreams, with a couple of changes. First, prices are now BigDecimalobjects, to better represent fractional values. Second, a Calendar object is written to the data file, indicating an invoice date.

    If readObject() doesn't return the object type expected, attempting to cast it to the correct type may throw a ClassNotFoundException. In this simple example, that can't happen, so we don't try to catch the exception. Instead, we notify the compiler that we're aware of the issue by adding ClassNotFoundException to the main method's throws clause.

## File I/O (Featuring NIO.2)

### What is a Path?

A file system stores and organizes files on some form of media, generally one or more hard drives, in such a way that they can be easily retrieved. Most file systems in use today store the files in a tree (or hierarchical) structure. At the top of the tree is one (or more) root nodes. Under the root node, there are files and directories (folders in Microsoft Windows). Each directory can contain files and subdirectories, which in turn can contain files and subdirectories, and so on, potentially to an almost limitless depth.

The Path Class introduces the cornerstone class of the java.nio.file package.
As its name implies, the Path class is a programmatic representation of a path in the file system. A Path object contains the file name and directory list used to construct the path, and is used to examine, locate, and manipulate files.

A Path instance reflects the underlying platform. In the Solaris OS, a Path uses the Solaris syntax (/home/joe/foo) and in Microsoft Windows, a Path uses the Windows syntax (C:\home\joe\foo). A Path is not system independent. You cannot compare a Path from a Solaris file system and expect it to match a Path from a Windows file system, even if the directory structure is identical and both instances locate the same relative file.

The file or directory corresponding to the Path might not exist. You can create a Path instance and manipulate it in various ways: you can append to it, extract pieces of it, compare it to another path. At the appropriate time, you can use the methods in the Files class to check the existence of the file corresponding to the Path, create the file, open it, delete it, change its permissions, and so on.

Path Operations looks at methods in the Path class that deal with syntactic operations.

- Creating a Path

A Path instance contains the information used to specify the location of a file or directory. At the time it is defined, a Path is provided with a series of one or more names. A root element or a file name might be included, but neither are required. A Path might consist of just a single directory or file name.
```java
Path p1 = Paths.get("/tmp/foo");
Path p2 = Paths.get(args[0]);
Path p3 = Paths.get(URI.create("file:///Users/joe/FileTest.java"));
```

- Retrieving Information about a Path

```java
System.out.format("toString: %s%n", path.toString());
System.out.format("getFileName: %s%n", path.getFileName());
System.out.format("getName(0): %s%n", path.getName(0));
System.out.format("getNameCount: %d%n", path.getNameCount());
System.out.format("subpath(0,2): %s%n", path.subpath(0,2));
System.out.format("getParent: %s%n", path.getParent());
System.out.format("getRoot: %s%n", path.getRoot());
```

- Converting a Path
```java
Path fullPath = inputPath.toAbsolutePath();
```

- Joining Two Paths
```java
Paths.get("foo").resolve("/home/joe");
```

- Creating a Path Between Two Paths
```java
Path p1 = Paths.get("joe");
Path p2 = Paths.get("sally");
// Result is ../sally
Path p1_to_p2 = p1.relativize(p2);
```

- Comparing Two Paths
```java
Path path = ...;
Path otherPath = ...;
Path beginning = Paths.get("/home");
Path ending = Paths.get("foo");
if (path.equals(otherPath)) {
    // equality logic here
} else if (path.startsWith(beginning)) {
    // path begins with "/home"
} else if (path.endsWith(ending)) {
    // path ends with "foo"
}
```

### File Operations
File Operations introduces concepts common to many of the file I/O methods.

- Releasing System Resources

Many of the resources that are used in this API, such as streams or channels, implement or extend the java.io.Closeable interface. A requirement of a Closeable resource is that the close method must be invoked to release the resource when no longer required. Neglecting to close a resource can have a negative implication on an application's performance. The try-with-resources statement, described in the next section, handles this step for you.

- Varargs  

Several Files methods accept an arbitrary number of arguments when flags are specified. For example, in the following method signature, the ellipses notation after the CopyOption argument indicates that the method accepts a variable number of arguments, or varargs, as they are typically called:
Path Files.move(Path, Path, CopyOption...)
When a method accepts a varargs argument, you can pass it a comma-separated list of values or an array (CopyOption[]) of values.
```java
Path source = ...;
Path target = ...;
Files.move(source, target, REPLACE_EXISTING, ATOMIC_MOVE);
```

- Atomic Operations

Several Files methods, such as move, can perform certain operations atomically in some file systems.
An atomic file operation is an operation that cannot be interrupted or "partially" performed. Either the entire operation is performed or the operation fails. This is important when you have multiple processes operating on the same area of the file system, and you need to guarantee that each process accesses a complete file.

### What Is a Glob?

Two methods in the Files class accept a glob argument, but what is a glob?
You can use glob syntax to specify pattern-matching behavior.

A glob pattern is specified as a string and is matched against other strings, such as directory or file names. Glob syntax follows several simple rules:

    An asterisk, *, matches any number of characters (including none).
    Two asterisks, **, works like * but crosses directory boundaries. This syntax is generally used for matching complete paths.
    A question mark, ?, matches exactly one character.
    Braces specify a collection of subpatterns. For example:
        {sun,moon,stars} matches "sun", "moon", or "stars".
        {temp*,tmp*} matches all strings beginning with "temp" or "tmp".
    Square brackets convey a set of single characters or, when the hyphen character (-) is used, a range of characters. For example:
        [aeiou] matches any lowercase vowel.
        [0-9] matches any digit.
        [A-Z] matches any uppercase letter.
        [a-z,A-Z] matches any uppercase or lowercase letter.
    Within the square brackets, *, ?, and \ match themselves.
    All other characters match themselves.
    To match *, ?, or the other special characters, you can escape them by using the backslash character, \. For example: \\ matches a single backslash, and \? matches the question mark.

Here are some examples of glob syntax:

    *.html – Matches all strings that end in .html
    ??? – Matches all strings with exactly three letters or digits
    *[0-9]* – Matches all strings containing a numeric value
    *.{htm,html,pdf} – Matches any string ending with .htm, .html or .pdf
    a?*.java – Matches any string beginning with a, followed by at least one letter or digit, and ending with .java
    {foo*,*[0-9]*} – Matches any string beginning with foo or any string containing a numeric value


- Checking a File or Directory shows how to check a file's existence and its level of accessibility.
exists(Path, LinkOption...) and the notExists(Path, LinkOption...)     
```java
boolean isRegularExecutableFile = Files.isRegularFile(file) &&
         Files.isReadable(file) && Files.isExecutable(file);

// & is bitwise comparison, in this scenario it's "equal"
boolean isRegularExecutableFile = Files.isRegularFile(file) &
         Files.isReadable(file) & Files.isExecutable(file);
```

- Deleting a File or Directory.
```java
Files.delete(path);
```

- Copying a File or Directory.
```java
Files.copy(source, target, REPLACE_EXISTING);
```

- Moving a File or Directory.
```java
Files.move(source, target, REPLACE_EXISTING);
```

- Managing Metadata 

    - **size(Path)**: Returns the size of the specified file in bytes.
    - **isDirectory(Path, LinkOption)**: Returns true if the specified Path locates a file that is a directory.
    - **isRegularFile(Path, LinkOption...)**: Returns true if the specified Path locates a file that is a regular file.
    - **isSymbolicLink(Path)**: Returns true if the specified Path locates a file that is a symbolic link.
    - **isHidden(Path)**: Returns true if the specified Path locates a file that is considered hidden by the file system.
    - **getLastModifiedTime(Path, LinkOption...)** / **setLastModifiedTime(Path, FileTime)**: Returns or sets the specified file's last modified time.
    - **getOwner(Path, LinkOption...)** / **setOwner(Path, UserPrincipal)**: Returns or sets the owner of the file.
    - **getPosixFilePermissions(Path, LinkOption...)** / **setPosixFilePermissions(Path, Set<PosixFilePermission>)**: Returns or sets a file's POSIX file permissions.
    - **getAttribute(Path, String, LinkOption...)** / **setAttribute(Path, String, Object, LinkOption...)**: Returns or sets the value of a file attribute.

- Reading, Writing and Creating Files shows the stream and channel methods for reading and writing files.

- Random Access Files shows how to read or write files in a non-sequentially manner.

Random access files permit nonsequential, or random, access to a file's contents. To access a file randomly, you open the file, seek a particular location, and read from or write to that file.

- Creating and Reading Directories covers API specific to directories, such as how to list a directory's contents.

```java
Set<PosixFilePermission> perms =
    PosixFilePermissions.fromString("rwxr-x---");
FileAttribute<Set<PosixFilePermission>> attr =
    PosixFilePermissions.asFileAttribute(perms);
Files.createDirectory(file, attr);

createTempDirectory(Path, String, FileAttribute<?>...)

// Listing a Directory's Contents
Path dir = ...;
try (DirectoryStream<Path> stream = Files.newDirectoryStream(dir)) {
    for (Path file: stream) {
        System.out.println(file.getFileName());
    }
} catch (IOException | DirectoryIteratorException x) {
    // IOException can never be thrown by the iteration.
    // In this snippet, it can only be thrown by newDirectoryStream.
    System.err.println(x);
}
```

- Links, Symbolic or Otherwise covers issues specific to symbolic and hard links.

As mentioned previously, the java.nio.file package, and the Path class in particular, is "link aware." Every Path method either detects what to do when a symbolic link is encountered, or it provides an option enabling you to configure the behavior when a symbolic link is encountered
```java
Files.createSymbolicLink(newLink, target);
Files.createLink(newLink, existingFile);
```

- Walking the File Tree demonstrates how to recursively visit each file and directory in a file tree.

To walk a file tree, you first need to implement a FileVisitor. A FileVisitor specifies the required behavior at key points in the traversal process: when a file is visited, before a directory is accessed, after a directory is accessed, or when a failure occurs. The interface has four methods that correspond to these situations:
  - **preVisitDirectory**: Invoked before a directory's entries are visited.
  - **postVisitDirectory**: Invoked after all the entries in a directory are visited. If any errors are encountered, the specific exception is passed to the method.
  - **visitFile**: Invoked on the file being visited. The file's BasicFileAttributes is passed to the method, or you can use the file attributes package to read a specific set of attributes. For example, you can choose to read the file's DosFileAttributeView to determine if the file has the "hidden" bit set.
  - **visitFileFailed**: Invoked when the file cannot be accessed. The specific exception is passed to the method. You can choose whether to throw the exception, print it to the console or a log file, and so on.

#### Considerations When Creating a FileVisitor

A file tree is walked depth first, but you cannot make any assumptions about the iteration order that subdirectories are visited.
If your program will be changing the file system, you need to carefully consider how you implement your FileVisitor.

For example, if you are writing a recursive delete, you first delete the files in a directory before deleting the directory itself. In this case, you delete the directory in postVisitDirectory.

If you are writing a recursive copy, you create the new directory in preVisitDirectory before attempting to copy the files to it (in visitFiles). If you want to preserve the attributes of the source directory (similar to the UNIX cp -p command), you need to do that after the files have been copied, in postVisitDirectory. The Copy example shows how to do this.

If you are writing a file search, you perform the comparison in the visitFile method. This method finds all the files that match your criteria, but it does not find the directories. If you want to find both files and directories, you must also perform the comparison in either the preVisitDirectory or postVisitDirectory method. The Find example shows how to do this.

You need to decide whether you want symbolic links to be followed. If you are deleting files, for example, following symbolic links might not be advisable. If you are copying a file tree, you might want to allow it. By default, walkFileTree does not follow symbolic links.

The visitFile method is invoked for files. If you have specified the FOLLOW_LINKS option and your file tree has a circular link to a parent directory, the looping directory is reported in the visitFileFailed method with the FileSystemLoopException. The following code snippet shows how to catch a circular link and is from the Copy example:

- Finding Files shows how to search for files using pattern matching.

PathMatcher & FileVisitor
```java
PathMatcher matcher =
    FileSystems.getDefault().getPathMatcher("glob:*.{java,class}");

Path filename = ...;
if (matcher.matches(filename)) {
    System.out.println(filename);
}
```

```java
public class Find {

    public static class Finder extends SimpleFileVisitor<Path> {
        private final PathMatcher matcher;
        private int numMatches = 0;

        Finder(String pattern) {
            matcher = FileSystems.getDefault().getPathMatcher("glob:" + pattern);
        }

        // Compares the glob pattern against
        // the file or directory name.
        void find(Path file) {
            Path name = file.getFileName();
            if (name != null && matcher.matches(name)) {
                numMatches++;
                System.out.println(file);
            }
        }

        // Prints the total number of
        // matches to standard out.
        void done() {
            System.out.println("Matched: " + numMatches);
        }

        // Invoke the pattern matching
        // method on each file.
        @Override
        public FileVisitResult visitFile(Path file, BasicFileAttributes attrs) {
            find(file);
            return CONTINUE;
        }

        // Invoke the pattern matching
        // method on each directory.
        @Override
        public FileVisitResult preVisitDirectory(Path dir, BasicFileAttributes attrs) {
            find(dir);
            return CONTINUE;
        }

        @Override
        public FileVisitResult visitFileFailed(Path file, IOException exc) {
            System.err.println(exc);
            return CONTINUE;
        }
    }
}
```


- Watching a Directory for Changes shows how to use the watch service to detect files that are added, removed or updated in one or more directories.
The WatchService API is fairly low level, allowing you to customize it. You can use it as is, or you can choose to create a high-level API on top of this mechanism so that it is suited to your particular needs.

Here are the basic steps required to implement a watch service:

Create a WatchService "watcher" for the file system.
```java
WatchService watcher = FileSystems.getDefault().newWatchService();
```

For each directory that you want monitored, register it with the watcher. When registering a directory, you specify the type of events for which you want notification. You receive a WatchKey instance for each directory that you register.
```java
import static java.nio.file.StandardWatchEventKinds.*;

Path dir = ...;
try {
    WatchKey key = dir.register(watcher,
                           ENTRY_CREATE,
                           ENTRY_DELETE,
                           ENTRY_MODIFY);
} catch (IOException x) {
    System.err.println(x);
}
```

Implement an infinite loop to wait for incoming events. When an event occurs, the key is signaled and placed into the watcher's queue.
Retrieve the key from the watcher's queue. You can obtain the file name from the key.
Retrieve each pending event for the key (there might be multiple events) and process as needed.
Reset the key, and resume waiting for events.
Close the service: The watch service exits when either the thread exits or when it is closed (by invoking its closed method).
```java
for (;;) {

    // wait for key to be signaled
    WatchKey key;
    try {
        key = watcher.take();
    } catch (InterruptedException x) {
        return;
    }

    for (WatchEvent<?> event: key.pollEvents()) {
        WatchEvent.Kind<?> kind = event.kind();

        // This key is registered only
        // for ENTRY_CREATE events,
        // but an OVERFLOW event can
        // occur regardless if events
        // are lost or discarded.
        if (kind == OVERFLOW) {
            continue;
        }

        // The filename is the
        // context of the event.
        WatchEvent<Path> ev = (WatchEvent<Path>)event;
        Path filename = ev.context();

        // Verify that the new
        //  file is a text file.
        try {
            // Resolve the filename against the directory.
            // If the filename is "test" and the directory is "foo",
            // the resolved name is "test/foo".
            Path child = dir.resolve(filename);
            if (!Files.probeContentType(child).equals("text/plain")) {
                System.err.format("New file '%s'" +
                    " is not a plain text file.%n", filename);
                continue;
            }
        } catch (IOException x) {
            System.err.println(x);
            continue;
        }

        // Email the file to the
        //  specified email alias.
        System.out.format("Emailing file %s%n", filename);
        //Details left to reader....
    }

    // Reset the key -- this step is critical if you want to
    // receive further watch events.  If the key is no longer valid,
    // the directory is inaccessible so exit the loop.
    boolean valid = key.reset();
    if (!valid) {
        break;
    }
}
```

# Concurrency

## Threads
- Defining and Starting a Thread
```java
Runnable or extend Thread
(new Thread(new HelloRunnable())).start();
class HelloThread extends Thread { run() { ... } }
```

- Pausing Execution with Sleep
```java
for (int i = 0; i < importantInfo.length; i++) {
    //Pause for 4 seconds
    Thread.sleep(4000);
    //Print a message
    System.out.println(importantInfo[i]);
}
```

- Interrupts

An interrupt is an indication to a thread that it should stop what it is doing and do something else. It's up to the programmer to decide exactly how a thread responds to an interrupt, but it is very common for the thread to terminate. This is the usage emphasized in this lesson.
A thread sends an interrupt by invoking interrupt on the Thread object for the thread to be interrupted. For the interrupt mechanism to work correctly, the interrupted thread must support its own interruption.

```java
try {
  ...
} catch (InterruptedException e) {
    // We've been interrupted: no more messages.
    return;
}
// The Interrupt Status Flag:
if (Thread.interrupted()) {
    throw new InterruptedException();
}
```

- Joins
The join method allows one thread to wait for the completion of another. If t is a Thread object whose thread is currently executing,

```java
t.join();
```

## Synchronization
Threads communicate primarily by sharing access to fields and the objects reference fields refer to. This form of communication is extremely efficient, but makes two kinds of errors possible: thread interference and memory consistency errors. The tool needed to prevent these errors is synchronization.

### Synchronized Methods

The Java programming language provides two basic synchronization idioms: synchronized methods and synchronized statements. The more complex of the two, synchronized statements, are described in the next section. This section is about synchronized methods.
To make a method synchronized, simply add the synchronized keyword to its declaration:
```java
public class SynchronizedCounter {
    private int c = 0;
    public synchronized void increment() {
        c++;
    }
    public synchronized void decrement() {
        c--;
    }
    public synchronized int value() {
        return c;
    }
}
```

If count is an instance of SynchronizedCounter, then making these methods synchronized has two effects:
  - First, it is not possible for two invocations of synchronized methods on the same object to interleave. When one thread is executing a synchronized method for an object, all other threads that invoke synchronized methods for the same object block (suspend execution) until the first thread is done with the object.
  - Second, when a synchronized method exits, it automatically establishes a happens-before relationship with any subsequent invocation of a synchronized method for the same object. This guarantees that changes to the state of the object are visible to all threads.

Note that constructors cannot be synchronized — using the synchronized keyword with a constructor is a syntax error. Synchronizing constructors doesn't make sense, because only the thread that creates an object should have access to it while it is being constructed.

### Intrinsic Locks and Synchronization

Synchronization is built around an internal entity known as the intrinsic lock or monitor lock. (The API specification often refers to this entity simply as a "monitor.") Intrinsic locks play a role in both aspects of synchronization: enforcing exclusive access to an object's state and establishing happens-before relationships that are essential to visibility.

- Synchronized Statements

```java
public void addName(String name) {
    synchronized(this) {
        lastName = name;
        nameCount++;
    }
    nameList.add(name);
}
```

- Deadlock

Deadlock describes a situation where two or more threads are blocked forever, waiting for each other.

Example: two objects with synchronized methods calling another synchronized method on the other object.

- Starvation

Starvation describes a situation where a thread is unable to gain regular access to shared resources and is unable to make progress. This happens when shared resources are made unavailable for long periods by "greedy" threads. For example, suppose an object provides a synchronized method that often takes a long time to return. If one thread invokes this method frequently, other threads that also need frequent synchronized access to the same object will often be blocked.

- Livelock

A thread often acts in response to the action of another thread. If the other thread's action is also a response to the action of another thread, then livelock may result. As with deadlock, livelocked threads are unable to make further progress. However, the threads are not blocked — they are simply too busy responding to each other to resume work. This is comparable to two people attempting to pass each other in a corridor: Alphonse moves to his left to let Gaston pass, while Gaston moves to his right to let Alphonse pass. Seeing that they are still blocking each other, Alphone moves to his right, while Gaston moves to his left. They're still blocking each other, so...

- Guarded Blocks

Threads often have to coordinate their actions. The most common coordination idiom is the guarded block. Such a block begins by polling a condition that must be true before the block can proceed. There are a number of steps to follow in order to do this correctly.
o.wait()
o.notifyAll()

- Immutable Objects

An object is considered immutable if its state cannot change after it is constructed. Maximum reliance on immutable objects is widely accepted as a sound strategy for creating simple, reliable code.

Immutable objects are particularly useful in concurrent applications. Since they cannot change state, they cannot be corrupted by thread interference or observed in an inconsistent state.

### High Level Concurrency Objects
- Lock Objects
```java
Lock lock = new ReentrantLock();
boolean locked = lock.tryLock();
lock.unlock();
```

- Executors

In all of the previous examples, there's a close connection between the task being done by a new thread, as defined by its Runnable object, and the thread itself, as defined by a Thread object. This works well for small applications, but in large-scale applications, it makes sense to separate thread management and creation from the rest of the application. Objects that encapsulate these functions are known as executors. The following subsections describe executors in detail.

The java.util.concurrent package defines three executor interfaces:
  - Executor, a simple interface that supports launching new tasks.
```java
e.execute(r);  
```
  - ExecutorService, a subinterface of Executor, which adds features that help manage the life cycle, both of the individual tasks and of the executor itself.
The ExecutorService interface supplements execute with a similar, but more versatile submit method. Like execute, submit accepts Runnable objects, but also accepts Callable objects, which allow the task to return a value. The submit method returns a Future object, which is used to retrieve the Callable return value and to manage the status of both Callable and Runnable tasks.
ExecutorService also provides methods for submitting large collections of Callable objects. Finally, ExecutorService provides a number of methods for managing the shutdown of the executor. To support immediate shutdown, tasks should handle interrupts correctly.  
  
  - ScheduledExecutorService, a subinterface of ExecutorService, supports future and/or periodic execution of tasks.
The ScheduledExecutorService interface supplements the methods of its parent ExecutorService with schedule, which executes a Runnable or Callable task after a specified delay. In addition, the interface defines scheduleAtFixedRate and scheduleWithFixedDelay, which executes specified tasks repeatedly, at defined intervals.

        ExecutorService executorService = Executors.newFixedThreadPool(5);

- Thread Pools

A simple way to create an executor that uses a fixed thread pool is to invoke the newFixedThreadPool factory method in java.util.concurrent.Executors This class also provides the following factory methods:
    - The newCachedThreadPool method creates an executor with an expandable thread pool. This executor is suitable for applications that launch many short-lived tasks.
    - The newSingleThreadExecutor method creates an executor that executes a single task at a time.
    Several factory methods are ScheduledExecutorService versions of the above executors.


        ExecutorService executorService = Executors.newFixedThreadPool(5);

- Fork/Join

The fork/join framework is an implementation of the ExecutorService interface that helps you take advantage of multiple processors. It is designed for work that can be broken into smaller pieces recursively. The goal is to use all the available processing power to enhance the performance of your application.

As with any ExecutorService implementation, the fork/join framework distributes tasks to worker threads in a thread pool. The fork/join framework is distinct because it uses a work-stealing algorithm. Worker threads that run out of things to do can steal tasks from other threads that are still busy.

The center of the fork/join framework is the ForkJoinPool class, an extension of the AbstractExecutorService class. ForkJoinPool implements the core work-stealing algorithm and can execute ForkJoinTask processes.
Basic Use

The first step for using the fork/join framework is to write code that performs a segment of the work. Your code should look similar to the following pseudocode:

```java
if (my portion of the work is small enough)
  do the work directly
else
  split my work into two pieces
  invoke the two pieces and wait for the results
```

```java
@Data
class IncrementTask extends RecursiveAction {
  public static final int THRESHOLD = 3;
  final long[] array;
  final int lo, hi;

  protected void compute() {
    if (hi - lo < THRESHOLD) {
      for (int i = lo; i < hi; ++i) {
        array[i]++;
      }
    } else {
      int mid = (lo + hi) >>> 1;
      invokeAll(
          new IncrementTask(array, lo, mid),
          new IncrementTask(array, mid, hi));
    }
  }
}
```

- Concurrent Collections

The java.util.concurrent package includes a number of additions to the Java Collections Framework. These are most easily categorized by the collection interfaces provided:

  - **BlockingQueue** defines a first-in-first-out data structure that blocks or times out when you attempt to add to a full queue, or retrieve from an empty queue.
  - **ConcurrentMap** is a subinterface of java.util.Map that defines useful atomic operations. These operations remove or replace a key-value pair only if the key is present, or add a key-value pair only if the key is absent. Making these operations atomic helps avoid synchronization. The standard general-purpose implementation of ConcurrentMap is ConcurrentHashMap, which is a concurrent analog of HashMap.
  - **ConcurrentNavigableMap** is a subinterface of ConcurrentMap that supports approximate matches. The standard general-purpose implementation of ConcurrentNavigableMap is ConcurrentSkipListMap, which is a concurrent analog of TreeMap.

All of these collections help avoid Memory Consistency Errors by defining a happens-before relationship between an operation that adds an object to the collection with subsequent operations that access or remove that object.

- Atomic Variables

The **java.util.concurrent.atomic** package defines classes that support atomic operations on single variables. All classes have get and set methods that work like reads and writes on volatile variables. That is, a set has a happens-before relationship with any subsequent get on the same variable. The atomic compareAndSet method also has these memory consistency features, as do the simple atomic arithmetic methods that apply to integer atomic variables.

```java
class AtomicCounter {
    private AtomicInteger c = new AtomicInteger(0);
    public void increment() {
        c.incrementAndGet();
    }
    public void decrement() {
        c.decrementAndGet();
    }
    public int value() {
        return c.get();
    }
}
```

# Collections
The Java platform includes a collections framework. A collection is an object that represents a group of objects (such as the classic Vector class). A collections framework is a unified architecture for representing and manipulating collections, enabling collections to be manipulated independently of implementation details.

The primary advantages of a collections framework are that it:
  - Reduces programming effort by providing data structures and algorithms so you don't have to write them yourself.
  - Increases performance by providing high-performance implementations of data structures and algorithms. Because the various implementations of each interface are interchangeable, programs can be tuned by switching implementations.
  - Provides interoperability between unrelated APIs by establishing a common language to pass collections back and forth.
  - Reduces the effort required to learn APIs by requiring you to learn multiple ad hoc collection APIs.
  - Reduces the effort required to design and implement APIs by not requiring you to produce ad hoc collections APIs.
  - Fosters software reuse by providing a standard interface for collections and algorithms with which to manipulate them.

## Set
A collection that contains no duplicate elements. More formally, sets contain no pair of elements e1 and e2 such that e1.equals(e2), and at most one null element. As implied by its name, this interface models the mathematical set abstraction.
Some set implementations have restrictions on the elements that they may contain. For example, some implementations prohibit null elements, and some have restrictions on the types of their elements. Attempting to add an ineligible element throws an unchecked exception, typically NullPointerException or ClassCastException.

Implementations:
- **AbstractSet**: skeletal implementation of the Set interface to minimize the effort required to implement this interface
- **ConcurrentSkipListSet**: A scalable concurrent NavigableSet implementation based on a ConcurrentSkipListMap. The elements of the set are kept sorted according to their natural ordering, or by a Comparator provided at set creation time, depending on which constructor is used.
- **CopyOnWriteArraySet**: A Set that uses an internal CopyOnWriteArrayList for all of its operations
- **EnumSet**: A specialized Set implementation for use with enum types. All of the elements in an enum set must come from a single enum type that is specified, explicitly or implicitly, when the set is created. Enum sets are represented internally as bit vectors.
- **HashSet**: This class implements the Set interface, backed by a hash table (actually a HashMap instance). It makes no guarantees as to the iteration order of the set
- **LinkedHashSet**: Hash table and linked list implementation of the Set interface, with predictable iteration order. This implementation differs from HashSet in that it maintains a doubly-linked list running through all of its entries.
- **TreeSet**: A NavigableSet implementation based on a TreeMap. The elements are ordered using their natural ordering, or by a Comparator provided at set creation time, depending on which constructor is used. This implementation provides guaranteed log(n) time cost for the basic operations (add, remove and contains).

## Queue
A collection designed for holding elements prior to processing. Besides basic Collection operations, queues provide additional insertion, extraction, and inspection operations. Each of these methods exists in two forms: one throws an exception if the operation fails, the other returns a special value (either null or false, depending on the operation). The latter form of the insert operation is designed specifically for use with capacity-restricted Queue implementations; in most implementations, insert operations cannot fail.

## Deque
A linear collection that supports element insertion and removal at both ends. The name deque is short for "double ended queue" and is usually pronounced "deck". Most Deque implementations place no fixed limits on the number of elements they may contain, but this interface supports capacity-restricted deques as well as those with no fixed size limit.

Implementations:
- **ArrayBlockingQueue**: A bounded blocking queue backed by an array. This queue orders elements FIFO (first-in-first-out).
- **ArrayDeque**: Resizable-array implementation of the Deque interface.
- **ConcurrentLinkedDeque**: An unbounded concurrent deque based on linked nodes. Concurrent insertion, removal, and access operations execute safely across multiple threads.
- **ConcurrentLinkedQueue**: An unbounded thread-safe queue based on linked nodes. This queue orders elements FIFO (first-in-first-out).
- **DelayQueue**: An unbounded blocking queue of Delayed elements, in which an element can only be taken when its delay has expired.
- **LinkedBlockingDeque**: An optionally-bounded blocking deque based on linked nodes.
- **LinkedBlockingQueue**: An optionally-bounded blocking queue based on linked nodes.
- **LinkedList**: Doubly-linked list implementation of the List and Deque interfaces. Implements all optional list operations, and permits all elements (including null).
- **LinkedTransferQueue**: An unbounded TransferQueue based on linked nodes.
- **PriorityBlockingQueue**: An unbounded blocking queue that uses the same ordering rules as class PriorityQueue and supplies blocking retrieval operations.
- **PriorityQueue**: An unbounded priority queue based on a priority heap. The elements of the priority queue are ordered according to their natural ordering, or by a Comparator provided at queue construction time, depending on which constructor is used.
- **SynchronousQueue**: A blocking queue in which each insert operation must wait for a corresponding remove operation by another thread, and vice versa.

## List
An ordered collection (also known as a sequence). The user of this interface has precise control over where in the list each element is inserted. The user can access elements by their integer index (position in the list), and search for elements in the list.

- **ArrayList**: Resizable-array implementation of the List interface. Implements all optional list operations, and permits all elements, including null. In addition to implementing the List interface, this class provides methods to manipulate the size of the array that is used internally to store the list. (This class is roughly equivalent to Vector, except that it is unsynchronized.)
- **CopyOnWriteArrayList**: A thread-safe variant of ArrayList in which all mutative operations (add, set, and so on) are implemented by making a fresh copy of the underlying array.
- **Stack**: The Stack class represents a last-in-first-out (LIFO) stack of objects. It extends class Vector with five operations that allow a vector to be treated as a stack. The usual push and pop operations are provided, as well as a method to peek at the top item on the stack, a method to test for whether the stack is empty, and a method to search the stack for an item and discover how far it is from the top.
- **Vector**: The Vector class implements a growable array of objects. Like an array, it contains components that can be accessed using an integer index. However, the size of a Vector can grow or shrink as needed to accommodate adding and removing items after the Vector has been created.

## Map
An object that maps keys to values. A map cannot contain duplicate keys; each key can map to at most one value.

- **ConcurrentHashMap**: A hash table supporting full concurrency of retrievals and high expected concurrency for updates.
- **ConcurrentSkipListMap**: A scalable concurrent ConcurrentNavigableMap implementation. The map is sorted according to the natural ordering of its keys, or by a Comparator provided at map creation time, depending on which constructor is used.
- **EnumMap**: A specialized Map implementation for use with enum type keys. All of the keys in an enum map must come from a single enum type that is specified, explicitly or implicitly, when the map is created. Enum maps are represented internally as arrays. This representation is extremely compact and efficient.
- **HashMap**: Hash table based implementation of the Map interface. This implementation provides all of the optional map operations, and permits null values and the null key. (The HashMap class is roughly equivalent to Hashtable, except that it is unsynchronized and permits nulls.) This class makes no guarantees as to the order of the map; in particular, it does not guarantee that the order will remain constant over time.
- **Hashtable**: This class implements a hash table, which maps keys to values. Any non-null object can be used as a key or as a value.
To successfully store and retrieve objects from a hashtable, the objects used as keys must implement the hashCode method and the equals method.
- **LinkedHashMap**: Hash table and linked list implementation of the Map interface, with predictable iteration order. This implementation differs from HashMap in that it maintains a doubly-linked list running through all of its entries.
- **Properties**: The Properties class represents a persistent set of properties. The Properties can be saved to a stream or loaded from a stream. Each key and its corresponding value in the property list is a string.
- **TreeMap**: A Red-Black tree based NavigableMap implementation. The map is sorted according to the natural ordering of its keys, or by a Comparator provided at map creation time, depending on which constructor is used.
- **WeakHashMap**: Hash table based implementation of the Map interface, with weak keys. An entry in a WeakHashMap will automatically be removed when its key is no longer in ordinary use.


# Nested classes
Terminology: Nested classes are divided into two categories: non-static and static. Non-static nested classes are called inner classes. Nested classes that are declared static are called static nested classes.
```java
class OuterClass {
    ...
    class InnerClass {
        ...
    }
    static class StaticNestedClass {
        ...
    }
}
```

Why Use Nested Classes?
Compelling reasons for using nested classes include the following:
  - It is a way of logically grouping classes that are only used in one place: If a class is useful to only one other class, then it is logical to embed it in that class and keep the two together. Nesting such "helper classes" makes their package more streamlined.
  - It increases encapsulation: Consider two top-level classes, A and B, where B needs access to members of A that would otherwise be declared private. By hiding class B within class A, A's members can be declared private and B can access them. In addition, B itself can be hidden from the outside world.
  - It can lead to more readable and maintainable code: Nesting small classes within top-level classes places the code closer to where it is used.

## Inner Classes
As with instance methods and variables, an inner class is associated with an instance of its enclosing class and has direct access to that object's methods and fields. Also, because an inner class is associated with an instance, it cannot define any static members itself.
Objects that are instances of an inner class exist within an instance of the outer class. Consider the following classes:
```java
class OuterClass {
    ...
    class InnerClass {
        ...
    }
}
```

An instance of InnerClass can exist only within an instance of OuterClass and has direct access to the methods and fields of its enclosing instance.
To instantiate an inner class, you must first instantiate the outer class. Then, create the inner object within the outer object with this syntax:
```java
OuterClass outerObject = new OuterClass();
OuterClass.InnerClass innerObject = outerObject.new InnerClass();
```
There are two special kinds of inner classes: local classes and anonymous classes.

## Static Nested Classes
As with class methods and variables, a static nested class is associated with its outer class. And like static class methods, a static nested class cannot refer directly to instance variables or methods defined in its enclosing class: it can use them only through an object reference


# Generics
Why Use Generics?
In a nutshell, generics enable types (classes and interfaces) to be parameters when defining classes, interfaces and methods. Much like the more familiar formal parameters used in method declarations, type parameters provide a way for you to re-use the same code with different inputs. The difference is that the inputs to formal parameters are values, while the inputs to type parameters are types.

Code that uses generics has many benefits over non-generic code:
  - Stronger type checks at compile time.
  - Elimination of casts.
  - Enabling programmers to implement generic algorithms.

## Generic Methods
Generic methods are methods that introduce their own type parameters. This is similar to declaring a generic type, but the type parameter's scope is limited to the method where it is declared. Static and non-static generic methods are allowed, as well as generic class constructors.

## Bounded Type Parameters
There may be times when you want to restrict the types that can be used as type arguments in a parameterized type. For example, a method that operates on numbers might only want to accept instances of Number or its subclasses. This is what bounded type parameters are for.

Multiple Bounds:

The preceding example illustrates the use of a type parameter with a single bound, but a type parameter can have multiple bounds:
```java
<T extends B1 & B2 & B3>
```

## Generic Methods and Bounded Type Parameters
Bounded type parameters are key to the implementation of generic algorithms. Consider the following method that counts the number of elements in an array T[] that are greater than a specified element elem.
```java
public static <T> int countGreaterThan(T[] anArray, T elem) {
    int count = 0;
    for (T e : anArray)
        if (e > elem)  // compiler error
            ++count;
    return count;
}
```

## Generics, Inheritance, and Subtypes

### Generic Classes and Subtyping
You can subtype a generic class or interface by extending or implementing it. The relationship between the type parameters of one class or interface and the type parameters of another are determined by the extends and implements clauses.

### Type Inference
 Type inference is a Java compiler's ability to look at each method invocation and corresponding declaration to determine the type argument (or arguments) that make the invocation applicable. The inference algorithm determines the types of the arguments and, if available, the type that the result is being assigned, or returned. Finally, the inference algorithm tries to find the most specific type that works with all of the arguments.

### Wildcards
In generic code, the question mark (?), called the wildcard, represents an unknown type. The wildcard can be used in a variety of situations: as the type of a parameter, field, or local variable; sometimes as a return type (though it is better programming practice to be more specific). The wildcard is never used as a type argument for a generic method invocation, a generic class instance creation, or a supertype.

### Upper Bounded Wildcards
To declare an upper-bounded wildcard, use the wildcard character ('?'), followed by the extends keyword, followed by its upper bound. Note that, in this context, extends is used in a general sense to mean either "extends" (as in classes) or "implements" (as in interfaces).

### Unbounded Wildcards
The unbounded wildcard type is specified using the wildcard character (?), for example, List<?>. This is called a list of unknown type. There are two scenarios where an unbounded wildcard is a useful approach:
   - If you are writing a method that can be implemented using functionality provided in the Object class.
   - When the code is using methods in the generic class that don't depend on the type parameter. For example, List.size or List.clear. In fact, Class<?> is so often used because most of the methods in Class<T> do not depend on T.

### Lower Bounded Wildcards
The Upper Bounded Wildcards section shows that an upper bounded wildcard restricts the unknown type to be a specific type or a subtype of that type and is represented using the extends keyword. In a similar way, a lower bounded wildcard restricts the unknown type to be a specific type or a super type of that type.
A lower bounded wildcard is expressed using the wildcard character ('?'), following by the super keyword, followed by its lower bound: <? super A>.

To write the method that works on lists of Integer and the supertypes of Integer, such as Integer, Number, and Object, you would specify List<? super Integer>. The term List<Integer> is more restrictive than List<? super Integer> because the former matches a list of type Integer only, whereas the latter matches a list of any type that is a supertype of Integer.
The following code adds the numbers 1 through 10 to the end of a list:
```java
public static void addNumbers(List<? super Integer> list) {
   for (int i = 1; i <= 10; i++) {
       list.add(i);
   }
}
```

Example with predicates:
```java
// it's useful to allow simpler predicates of lower types
List.removeIf(Predicate<? super E> filter)
```


### Guidelines for Wildcard Use
One of the more confusing aspects when learning to program with generics is determining when to use an upper bounded wildcard and when to use a lower bounded wildcard. This page provides some guidelines to follow when designing your code.
For purposes of this discussion, it is helpful to think of variables as providing one of two functions:

    An "In" Variable
    An "in" variable serves up data to the code. Imagine a copy method with two arguments: copy(src, dest). The src argument provides the data to be copied, so it is the "in" parameter.

    An "Out" Variable
    An "out" variable holds data for use elsewhere. In the copy example, copy(src, dest), the dest argument accepts data, so it is the "out" parameter.

Wildcard Guidelines:
  - An "in" variable is defined with an upper bounded wildcard, using the extends keyword.
  - An "out" variable is defined with a lower bounded wildcard, using the super keyword.
  - In the case where the "in" variable can be accessed using methods defined in the Object class, use an unbounded wildcard.
  - In the case where the code needs to access the variable as both an "in" and an "out" variable, do not use a wildcard.

### Type Erasure
Generics were introduced to the Java language to provide tighter type checks at compile time and to support generic programming. To implement generics, the Java compiler applies type erasure to:
   - Replace all type parameters in generic types with their bounds or Object if the type parameters are unbounded. The produced bytecode, therefore, contains only ordinary classes, - interfaces, and methods.
   - Insert type casts if necessary to preserve type safety.
   - Generate bridge methods to preserve polymorphism in extended generic types.


# Design Patterns
## Creational
Creational patterns are ones that create objects, rather than having to instantiate objects directly. This gives the program more flexibility in deciding which objects need to be created for a given case.
 - Abstract factory groups object factories that have a common theme.

 - Builder constructs complex objects by separating construction and representation.

 - Factory method creates objects without specifying the exact class to create.

 - Prototype creates objects by cloning an existing object.

 - Singleton restricts object creation for a class to only one instance.

# Structural
These concern class and object composition. They use inheritance to compose interfaces and define ways to compose objects to obtain new functionality.
 - Adapter allows classes with incompatible interfaces to work together by wrapping its own interface around that of an already existing class.

 - Bridge decouples an abstraction from its implementation so that the two can vary independently.
 ```java
 // Logger has two implementations: info and warning
 @FunctionalInterface
 interface Logger {
     void log(String message);

     static Logger info() {
         return message -> System.out.println("info: " + message);
     }
     static Logger warning() {
         return message -> System.out.println("warning: " + message);
     }
 }

 abstract class AbstractAccount {
     private Logger logger = Logger.info();

     public void setLogger(Logger logger) {
         this.logger = logger;
     }

     // the logging part is delegated to the Logger implementation
     protected void operate(String message, boolean result) {
         logger.log(message + " result " + result);
     }
 }
 ```

 - Composite composes zero-or-more similar objects so that they can be manipulated as one object.

 - Decorator dynamically adds/overrides behaviour in an existing method of an object.
 ```java
 // abstract decorator class - note that it implements Window
 abstract class WindowDecorator implements Window {
     private final Window windowToBeDecorated; // the Window being decorated

     public WindowDecorator (Window windowToBeDecorated) {
         this.windowToBeDecorated = windowToBeDecorated;
     }
     @Override
     public void draw() {
         windowToBeDecorated.draw(); //Delegation
     }
     @Override
     public String getDescription() {
         return windowToBeDecorated.getDescription(); //Delegation
     }
 }

 // The first concrete decorator which adds vertical scrollbar functionality
 class VerticalScrollBarDecorator extends WindowDecorator {
     public VerticalScrollBarDecorator (Window windowToBeDecorated) {
         super(windowToBeDecorated);
     }

     @Override
     public void draw() {
         super.draw();
         drawVerticalScrollBar();
     }

     private void drawVerticalScrollBar() {
         // Draw the vertical scrollbar
     }

     @Override
     public String getDescription() {
         return super.getDescription() + ", including vertical scrollbars";
     }
 }

 // The second concrete decorator which adds horizontal scrollbar functionality
 class HorizontalScrollBarDecorator extends WindowDecorator {
     public HorizontalScrollBarDecorator (Window windowToBeDecorated) {
         super(windowToBeDecorated);
     }

     @Override
     public void draw() {
         super.draw();
         drawHorizontalScrollBar();
     }

     private void drawHorizontalScrollBar() {
         // Draw the horizontal scrollbar
     }

     @Override
     public String getDescription() {
         return super.getDescription() + ", including horizontal scrollbars";
     }
 }
 ```

 - Facade provides a simplified interface to a large body of code.

 - Flyweight reduces the cost of creating and manipulating a large number of similar objects.
```c#
 // Defines Flyweight object that repeats itself.
public class Flyweight
{
    public string CompanyName { get; set; }
    public string CompanyLocation { get; set; }
    public string CompanyWebsite { get; set; }
    // Bulky data
    public byte[] CompanyLogo { get; set; }
}

public static class FlyweightPointer
{
    public static readonly Flyweight Company = new Flyweight
    {
        CompanyName = "Abc",
        CompanyLocation = "XYZ",
        CompanyWebsite = "www.example.com"
        // Load CompanyLogo here
    };
}

public class MyObject
{
    public string Name { get; set; }
    public string Company => FlyweightPointer.Company.CompanyName;
}
```

 - Proxy provides a placeholder for another object to control access, reduce cost, and reduce complexity.

Example: cache, lazy loading

## Behavioral
Most of these design patterns are specifically concerned with communication between objects.
 - Chain of responsibility delegates commands to a chain of processing objects.

 - Command creates objects that encapsulate actions and parameters.

 - Interpreter implements a specialized language.

 - Iterator accesses the elements of an object sequentially without exposing its underlying representation.

 - Mediator allows loose coupling between classes by being the only class that has detailed knowledge of their methods.

 - Memento provides the ability to restore an object to its previous state (undo).

 - Observer is a publish/subscribe pattern, which allows a number of observer objects to see an event.

 - State allows an object to alter its behavior when its internal state changes.

 - Strategy allows one of a family of algorithms to be selected on-the-fly at runtime.

 - Template method defines the skeleton of an algorithm as an abstract class, allowing its subclasses to provide concrete behavior.

 - Visitor separates an algorithm from an object structure by moving the hierarchy of methods into one object.
