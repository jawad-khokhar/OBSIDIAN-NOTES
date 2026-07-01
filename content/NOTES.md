### 1. When should a Member of a Class be a Pointer?

Up until now, we talked about making the _whole object_ a pointer in `main`. But you are asking about putting a pointer **inside** the class definition as a member variable, like this:

```cpp
class Student {
private:
    int* grades; // <-- When do we do this?
};
```

You make a class member a pointer in **three specific situations**:
#### Situation A: When the size of the data is completely unknown until the program runs

If your class holds an array of items, but you don't know how big that array needs to be until a user inputs a number, you _cannot_ use a standard fixed array. You must use a pointer to dynamically allocate the array later.

```cpp
class Playlist {
private:
    string* songs; // Pointer to an array of songs
    int capacity;

public:
    Playlist(int size) {
        capacity = size;
        songs = new string[capacity]; // Allocate exactly 'size' memory slots on the heap!
    }
    
    ~Playlist() {
        delete[] songs; // Clean up memory when the playlist dies
    }
};
```

#### Situation B: When a member variable needs to share or point to an external object

If your class needs to refer to another object that exists independently out in your program, you use a pointer. If you use a regular variable, it makes a disconnected copy. A pointer lets you point to the _exact original_.
```cpp
class Engine { /* ... */ };

class Car {
private:
    Engine* myEngine; // Points to an engine built somewhere else
public:
    void installEngine(Engine* eng) { myEngine = eng; }
};
```


### So... _Where_ do we actually need `Box*`?

If the code above works perfectly without pointers, why do we ever use `Box*`? You only switch from a normal `Box` to a pointer `Box*` in **three specific situations**:

#### Situation 1: When you need the object to survive outside the function

If you want this function to create the Box, but you want to pass it back to `main()` so it stays alive for the rest of your program, you **cannot** use the stack. You _must_ use `Box*` and return it:
```cpp
Box* createBoxForLater() {
    Box* b = new Box(50); 
    return b; // The function ends, but the Box survives on the heap!
}
```

#### Situation 2: Polymorphism and Inheritance (Object-Oriented Programming)

If you have a base class named `Shape` and a derived class named `Box`, and you want to create an array that can hold boxes, circles, and triangles all mixed together, you **must** use a pointer array (`Shape*`). Regular object arrays cannot handle mixed types.

```cpp
Shape* inventory[3];
inventory[0] = new Box(50);    // Box pointer
inventory[1] = new Circle(10); // Circle pointer
```

#### Situation 3: Building Dynamic Data Structures

If you are building your own **Linked List, Stack, Queue, or Binary Tree**, the data nodes must have pointers to link to each other in memory. You cannot link them together using normal static variables.
```cpp
class Node {
public:
    Box data;
    Node* nextNode; // Points to the next node block in the chain
};
```