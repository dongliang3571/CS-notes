# CS-notes

### 

Difference between HTTP and Socket Connection

HTTP Connection

HTTP connection is a protocol that runs on a socket.
HTTP connection is a higher-level abstraction of a network connection.
With HTTP connection the implementation takes care of all these higher-level details and simply send HTTP request (some header information) and receive HTTP response from the server.

Socket Connection

Socket is used to transport data between systems. It simply connects two systems together, an IP address is the address of the machine over an IP based network.
With socket connection you can design your own protocol for network connection between two systems.
With Socket connection you need to take care of all the lower-level details of a TCP/IP connection.

### Virtual memory

Virtual memory is a layer of abstraction provided to each process. The computer has, say, 2GB of physical RAM, addressed from 0 to 2G. A process might see an address space of 4GB, which it has entirely to itself. The mapping from virtual addresses to physical addresses is handled by a memory management unit, which is managed by the operating system. Typically this is done in 4KB "pages".

This gives several features:

A process can not see memory in other processes (unless the OS wants it to!)
Memory at a given virtual address may not be located at the same physical address
Memory at a virtual address can be "paged out" to disk, and then "paged in" when it is accessed again.
Your textbook defines virtual memory (incorrectly) as just #3.

Even without any swapping, you particularly need to be aware of virtual memory if you write a device driver for a device which does DMA (direct memory access). Your driver code runs on the CPU, which means its memory accesses are through the MMU (virtual). The device probably does not go through the MMU, so it sees raw physical addresses. So as a driver writer you need to ensure:

Any raw memory addresses you pass to the hardware are physical, not virtual.
Any large (multi page) blocks of memory you send are physically contiguous. An 8K array might be virtually contiguous (through the MMU) but two physically separate pages. If you tell the device to write 8K of data to the physical address corresponding to the start of that array, it will write the first 4K where you expect, but the second 4K will corrupt some memory somewhere. 

### Concurrency, Parallelism

This great talk Rob Pike - ('Concurrency Is Not Parallelism')[https://vimeo.com/49718712] shall answer your question. He was talking mostly about concurrency in Go, but the concept of concurrency (and its difference to parallelism) was well conveyed. Very recommended to watch.

"Concurrency is about dealing with lots of things at once. Parallelism is about doing lots of things at once." - Rob Pike

Concurrency: You are running an operating system, in which you can have independent drivers inside the kernel (mouse driver, keyboard driver, display driver, etc). Those are not necessarily being run in parallel. If you only have one processor, only one can be run at a time, and it will still be able to run without any problem.

Parallelism: You want to convert a colored image to black and white. The job can be done by calculating each pixel's RGB value to average of its neighbors. Since you can divide the task to smaller subtask (for example, each core is responsible to calculate specific region of the image), you can run them in parallel with multi-core CPU/GPU.

Three pairs of antonyms:

- Parallel is the opposite of serial.
- Concurrent is the opposite of sequential.
- Vector is the opposite of scalar.

### Mutex, Semaphore

http://www.geeksforgeeks.org/mutex-vs-semaphore/

https://stackoverflow.com/questions/4039899/when-should-we-use-mutex-and-when-should-we-use-semaphore

Using Mutex:

A mutex provides mutual exclusion, either producer or consumer can have the key (mutex) and proceed with their work. As long as the buffer is filled by producer, the consumer needs to wait, and vice versa.

At any point of time, only one thread can work with the entire buffer. The concept can be generalized using semaphore.

Using Semaphore:

A semaphore is a generalized mutex. In lieu of single buffer, we can split the 4 KB buffer into four 1 KB buffers (identical resources). A semaphore can be associated with these four buffers. The consumer and producer can work on different buffers at the same time.

Misconception:

There is an ambiguity between binary semaphore and mutex. We might have come across that a mutex is binary semaphore. But they are not! The purpose of mutex and semaphore are different. May be, due to similarity in their implementation a mutex would be referred as binary semaphore.

Strictly speaking, a mutex is locking mechanism used to synchronize access to a resource. Only one task (can be a thread or process based on OS abstraction) can acquire the mutex. It means there is ownership associated with mutex, and only the owner can release the lock (mutex).

Semaphore is signaling mechanism (“I am done, you can carry on” kind of signal). For example, if you are listening songs (assume it as one task) on your mobile and at the same time your friend calls you, an interrupt is triggered upon which an interrupt service routine (ISR) signals the call processing task to wakeup.

### Compiler

Assembly language is, so to say, a human-readable form of expressing the instructions a processor executes (which are binary data and very hard to manage by a human). So, if the machine instructions are not generated by a human, using assembly step is not necessary, though it sometimes does happen for convenience. If a program is compiled from a language such as C++, the compiler may generate machine code directly, without going through the intermediate stage of assembly code. Still, many compilers provide an option of generating assembly code in order to make it easier for a human to inspect what gets generated.

Many modern languages, for example Java and C# are compiled into so-called bytecode. This is code that the CPU does not execute directly, but rather an intermediate form, which may get compiled to machine code just-in-time (JIT-ted) when the program is executed. In such a case, CPU-dependent machine code gets generated but usually without going through human-readable assembly code.

### Data Structure, abstract data type

In computer science, a data structure is a particular way of organizing data in a computer so that it can be used efficiently.

Data structures can implement one or more particular abstract data types (ADT), which specify the operations that can be performed on a data structure and the computational complexity of those operations. In comparison, a data structure is a concrete implementation of the specification provided by an ADT.

Examples of data structure:

  - array
  - list
  - record(tuplem struct)
  - class (A class is a data structure that contains data fields, like a record, as well as various methods which operate on the contents of the record.)
  - heap
  
Examples of abstract data type:
  - Stack
  - Queue
  - Priority Queue

### HashMap implementation

```java
package co.robl;

/**
 * Created by Robbie on 17/11/2014.
 * Simple Implementation of a HashMap
 */
public class RobsHashMap {

    /* The initial size of the bucket array */
    private int BUCKET_ARRAY_SIZE = 256;
    private Node bucketArray[] = new Node[BUCKET_ARRAY_SIZE];

    /* Constructors */
    public RobsHashMap(){}

    public RobsHashMap(int initialSize){
        this.BUCKET_ARRAY_SIZE = initialSize;
    }

    /**
     * Used to put a key-value pair into the data structure.
     * @param key Key string that is used identify the key-value pair
     * @param value Value string in which the key string maps to.
     */
    public void put(String key, String value){
        /* Get the hash code */
        int hash = Math.abs(key.hashCode() % BUCKET_ARRAY_SIZE);
        /* Create the Node to add to the linked list */
        Node entry = new Node(key,value);

        /* Insert the node to the bucket array at the hash index */
        if(bucketArray[hash] == null){
            /* No collision detected. Insert the node. */
            bucketArray[hash] = entry;
        }else{
            /* Collision detected. We must place the node at the end of the linked list. */
            Node current = bucketArray[hash];
            while(current.next != null){
                /* Check if the key already exists */
                if(current.getKey().equals(entry.getKey())){
                    /* Replace the keys value with the new one */
                    current.setValue(entry.getValue());
                    return;
                }
                current = current.next;
            }
            /* When the code gets here current.next == null */
            /* Insert the node */
            current.next = entry;
        }
    }

    /**
     * Returns the value that is mapped to the given key.
     * @param key The key string which is used to search for the value it
     *            is mapped to.
     * @return The value string
     */
    public String get(String key){
        /* Get the hash */
        int hash = Math.abs(key.hashCode() % BUCKET_ARRAY_SIZE);
        /* Search for key in linked list */
        Node n = bucketArray[hash];
        /* Traverse linked list */
        while(n != null){
            if(n.getKey().equals(key)){
                return n.getValue();
            }
            n = n.getNext();
        }
        /* Not found? then return null */
        return null;
    }


    /* This is the simple object that we use to store each key-value pair */
    class Node{
        private String key;
        private String value;
        private Node next;

        public Node(){}

        public Node(String key, String value){
            this.key = key;
            this.value = value;
        }

        public String getKey() {
            return key;
        }

        public void setKey(String key) {
            this.key = key;
        }

        public String getValue() {
            return value;
        }

        public void setValue(String value) {
            this.value = value;
        }

        public Node getNext() {
            return next;
        }

        public void setNext(Node next) {
            this.next = next;
        }
    }
}
```

### Different hashmap implementation

http://www.cs.rmit.edu.au/online/blackboard/chapter/05/documents/contribute/chapter/05/linear-probing.html
