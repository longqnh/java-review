# Java Review

## Java Core
1. Abstract class vs Interface
   
   |                                       | Abstract Class                                       | Interface                                          |
   |---------------------------------------|------------------------------------------------------|----------------------------------------------------|
   | **Inheritance**                       | A class can extends only one abstract class          | A Class can implements multiple interfaces         |
   | **Constructor**                       | Can have constructors                                | Cannot have constructors                           |
   | **Fields**                            | Can have instance variables (fields)                 | Fields are implicitly `public`, `static`, `final`  |
   | **Methods**                           | Can have abstract and concrete methods               | All methods are implicitly `public`, `abstract`    |
   | **Access Modifiers**                  | Can have various access modifiers                    | All methods are implicitly `public`                |
   | **Default Method Implementation**     | Can have method implementations (non-abstract)       | Methods are implicitly `abstract` (unless default) |
   | **Final Variables**                   | Can have final variables                             | Fields are implicitly `final`                      |
   | **Multiple Inheritance**              | Does not support multiple inheritance directly       | Supports multiple inheritance through interfaces   |
   | **Constructors in Subclasses**        | Subclasses must call the constructor of the abstract class using `super()` | No constructors to be called in implementing classes |
   | **Default Method Override**           | Can choose to override or not override default methods | Must provide an implementation for all interface methods (including default methods) |
   | **Use Cases**                         | Used when common code needs to be shared among multiple subclasses      | Used when multiple unrelated classes need to share a common set of methods     |


2. Multiple inheritance:
    - Java does not support multiple inheritance through classes to avoid the complexities and ambiguities that can arise from the diamond problem. The diamond problem occurs when a class inherits from two classes that have a common ancestor. Consider the following scenario:
        ````java
        class A {
            void foo() {
                System.out.println("A");
            }
        }
        
        class B extends A {
            void foo() {
                System.out.println("B");
            }
        }
        
        class C extends A {
            void foo() {
                System.out.println("C");
            }
        }
        
        class D extends B, C { // This is not allowed in Java
        }
    - If Java allowed multiple inheritance through classes, and D inherited from both B and C, there would be ambiguity in deciding which version of foo to inherit, as both B and C extend from A with their own implementation of foo. This situation leads to the diamond problem, where the compiler cannot determine the correct method to use, resulting in potential confusion and maintenance issues.


3. Can a Java class implements 2 interfaces that having same method?
    - Yes, it's possible in Java. However, the class must provide an implementation for the method only once, as duplicate method signatures are not allowed. If a class implements two interfaces with the same method signature, it must provide a single implementation for that method.
    

4. Object class methods:
   - `toString()`: Returns a string representation of the object. The default implementation returns a string consisting of the class name followed by the "@" character and the object's hashcode.
   - `equals(Object obj)`: Indicates whether some other object is "equal to" this one. The default implementation in the Object class compares memory addresses (object references), but it is often overridden in subclasses to compare the actual content. 
   - `hashCode()`: Returns a hash code value for the object. The default implementation returns the object's memory address in integer form. It's used in conjunction with the equals method for hash-based collections.
   - `getClass()`: Returns the runtime class of the object. It is commonly used to get the class type of an object at runtime.
   - `clone()`: Creates and returns a copy of the object. To use clone(), a class must implement the Cloneable interface, and its clone method should be overridden.
   - `notify(), notifyAll(), wait()`: These methods are used for inter-thread communication and synchronization. They are related to the Java threading mechanism and are used in conjunction with the synchronized keyword.
   - `finalize()`: Called by the garbage collector before an object is reclaimed. This method can be overridden to perform cleanup operations before an object is garbage collected.
    

5. Why do we must override `hashCode` together with `equals`?
   - Consistency with `equals`, if two objects are equal then their hash codes must be the same
   - Contract for `hashCode`: The contract for hashCode is that equal objects must have the same hash code. If you override equals without overriding hashCode, you might violate this contract, leading to unexpected behavior when using hash-based collections
   - Performance in Hash-Based Collections: Many hash-based data structures, like HashMap and HashSet, use hash codes to distribute objects across buckets. If hashCode is not overridden, objects that are considered equal by your equals implementation might end up in different buckets, leading to inefficient behavior and potential performance issues.
   - Example
   ```java
   public class Person {
        private String name;
        private int age;
   
        @Override
        public boolean equals(Object obj) {
           if (this == obj) return true;
           if (obj == null || getClass() != obj.getClass()) return false;
           Person person = (Person) obj;
           return age == person.age && Objects.equals(name, person.name);
        }
   
        // Without overriding hashCode, inconsistent behavior may occur in hash-based collections
        // @Override
        // public int hashCode() {
        //     return Objects.hash(name, age);
        // }
   }
   
   public class Main {
      public static void main(String[] args) {
         Person person1 = new Person("John", 25);
         Person person2 = new Person("John", 25);
   
         System.out.println(person1.equals(person2));  // true
   
         // Without overriding hashCode, hash-based collections may behave unexpectedly
         // Inconsistent behavior when used as keys in HashMap or elements in HashSet
         // HashMap<Person, String> map = new HashMap<>();
         // map.put(person1, "Value1");
         // map.put(person2, "Value2");
         // System.out.println(map.size());  // Inconsistent behavior: prints 2 or 1 depending on hashCode
      }
   } 
   ```


6. What are differences between `String s = "abc"` and `String s = new String("abc")`?
   - `String s = "abc"`:
        - Creates a string literal "abc" in the string pool. 
        - If the string "abc" does not exist in the pool, a new entry is added. Otherwise, the existing reference is reused.
        - The variable s holds a reference to the string literal in the string pool.
   - `String s = new String("abc")`:
        - Creates a new String object in the heap memory using the constructor that takes a String parameter.
        - Regardless of whether the string "abc" already exists in the pool, a new String object is created.
        - The variable s holds a reference to the newly created String object in the heap.
   ```java
    // Using string literal
    String s1 = "abc";
    String s2 = "abc";
    
    // Using new String()
    String s3 = new String("abc");
    String s4 = new String("abc");
    
    // Checking references
    System.out.println(s1 == s2);  // true (both reference the same string literal in the pool)
    System.out.println(s1 == s3);  // false (different references: literal vs. new String)
    System.out.println(s3 == s4);  // false (different references: two new String objects)
   

7. Checked Exception vs Unchecked Exception:
   
   |                                   | Checked Exception                                            | Unchecked Exception                                           |
   |-----------------------------------|--------------------------------------------------------------|--------------------------------------------------------------|
   | **Compilation Requirement**       | Must be explicitly declared or handled in the code           | Compiler does not enforce catching or declaring exceptions   |
   | **Examples**                      | - `IOException`, `SQLException`                              | - `NullPointerException`, `ArrayIndexOutOfBoundsException` |
   | **Inheritance from `Exception`**  | Extends `Exception` class                                    | Extends `RuntimeException` class                              |
   | **Forced Handling**               | Requires mandatory handling (try-catch or throws clause)     | No enforced handling required                                 |
   | **Scenario Examples**             | - File I/O operations, Database operations                   | - Accessing an element beyond array bounds, Dereferencing a null object reference                    |
   | **Common Use Cases**              | - External factors like I/O operations                       | - Developer mistakes or logic errors                          |
   | **Intentional Propagation**       | Encourages developers to handle specific exceptions          | Propagation indicates a serious programming error or issue   |
   | **Checked by Compiler**           | Checked exceptions are checked by the compiler               | Compiler does not check unchecked exceptions                  |
   | **Handling in Code**              | Must be handled using `try-catch` or `throws`                | Optional handling; may be caught or allowed to propagate      |


## Java Collections
1. ArrayList vs LinkedList:
   
   |                           | ArrayList                                     | LinkedList                                   |
   |---------------------------|-----------------------------------------------|---------------------------------------------|
   | **Underlying Data Structure** | Dynamic resizing array                     | Doubly linked list (point to next and prev pointer)                          |
   | **Random Access**         | O(1) time complexity for retrieval by index   | O(n) time complexity for retrieval by index |
   | **Insertion and Deletion** | Slower for middle operations due to shifting  | Faster, especially for middle operations    |
   | **Memory Usage**          | Generally less memory per element             | More memory per element                     |
   | **Iterating Over Elements** | Faster due to better cache locality           | Slower due to traversal                     |
   | **Use Cases**             | Stable list size, frequent random access     | Frequent insertions/deletions, less random access |
   | **Concurrency**           | Not synchronized by default                   | Not synchronized by default                 |
   | **Implementation Complexity** | Simpler implementation                    | More complex implementation                 |


2. ArrayList vs Vector:
   - ArrayList and Vector share same properties except when handling concurrency. ArrayList is not synchronized while Vector is synchronized and thread-safe.
   

3. HashSet vs LinkedHashSet vs TreeSet:

   |                                | HashSet                                       | LinkedHashSet                                | TreeSet                                     |
   |------------------------------|-----------------------------------------------|----------------------------------------------|---------------------------------------------|
   | **Underlying Data Structure** | Hash table                                    | Hash table with linked list                  | Red-Black Tree                               |
   | **Ordering of Elements**      | No specific order (unordered)                 | Maintains insertion order                    | Sorted order based on natural ordering or comparator |
   | **Null Elements**            | Allows a single null element                  | Allows a single null element                | Does not allow null elements                 |
   | **Performance (Average)**    | O(1) time complexity for add, remove, contains | O(1) time complexity for add, remove, contains | O(log n) time complexity for add, remove, contains |
   | **Iteration Performance**    | Faster due to no ordering                     | Slightly slower due to maintaining order    | Slower compared to HashSet, but maintains sorted order |
   | **Use Cases**                | General-purpose set with no ordering requirement | When maintaining insertion order is important | When elements need to be stored in sorted order |

4. HashMap vs HashTable:

   |                                | HashMap                                       | HashTable                                   |
   |--------------------------------|-----------------------------------------------|--------------------------------------------|
   | **Synchronization**            | Not synchronized by default                    | Synchronized, thread-safe                  |
   | **Null Keys and Values**       | Allows one null key and multiple null values   | Does not allow null keys or values         |
   | **Performance (Average)**      | Generally faster due to lack of synchronization | Slower due to synchronization overhead    |
   | **Iteration Performance**      | Faster due to lack of synchronization overhead | Slower due to synchronization overhead    |
   | **Implementation ** | Implements `Map` interface              | Extends `Dictionary` class           |
   | **Concurrency Use Case**       | Better suited for scenarios with no concurrent modifications | Suitable for scenarios with concurrent modifications |


## Multithreading

