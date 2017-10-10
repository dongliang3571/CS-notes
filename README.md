# CS-notes

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
