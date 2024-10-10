
# Stack in Java

## 1. What is a Stack?

A **Stack** is a linear data structure that follows the **LIFO** (Last In First Out) principle. This means the last element added to the stack is the first one to be removed. Stacks are used for managing data in a particular way and come in handy in various scenarios, such as function call management, expression evaluation, and more.

In Java, the `Stack` class is part of the **java.util** package and provides methods for stack operations.

### Example of Stack

```java
import java.util.Stack;

public class StackExample {
    public static void main(String[] args) {
        Stack<Integer> stack = new Stack<>();
        stack.push(10);
        stack.push(20);
        stack.push(30);

        System.out.println("Top element: " + stack.peek());  // Output: 30
        stack.pop();
        System.out.println("Top element after pop: " + stack.peek());  // Output: 20
    }
}
```

---

## 2. Why Do We Use Stack?

Stacks are used when we need to maintain an ordered collection where the most recent elements are processed first. This structure is especially useful in:

- **Expression evaluation** (infix, postfix, prefix)
- **Backtracking** (like in solving mazes or puzzles)
- **Undo/Redo operations** in text editors
- **Browser history** (tracking visited pages)
- **Function call management** (recursion and stack memory)

### Example: Balancing Parentheses

```java
public static boolean isBalanced(String expr) {
    Stack<Character> stack = new Stack<>();
    for (char c : expr.toCharArray()) {
        if (c == '(') stack.push(c);
        else if (c == ')') {
            if (stack.isEmpty()) return false;
            stack.pop();
        }
    }
    return stack.isEmpty();
}
```

This example demonstrates how a stack can be used to ensure that parentheses are balanced in an expression.

---

## 3. Scenarios Where Stack is Helpful

Here are some specific scenarios where a stack can be applied:

- **Reversing a word**: By pushing characters of a word onto the stack and popping them, you can reverse the word.
- **Depth-First Search (DFS)**: Used in graph traversal, DFS uses a stack to remember the vertices to explore next.
- **Postfix Expression Evaluation**: Compilers use stacks to evaluate postfix expressions.

---

## 4. Basic Functions of Stack

Java's `Stack` class provides several important methods:

- `push(E item)`: Adds an element to the top of the stack.
- `pop()`: Removes and returns the top element of the stack.
- `peek()`: Returns the top element without removing it.
- `isEmpty()`: Checks whether the stack is empty.
- `search(Object o)`: Returns the position of the element from the top of the stack.

### Example: Stack Methods in Action

```java
Stack<String> stack = new Stack<>();
stack.push("apple");
stack.push("banana");
stack.push("cherry");

System.out.println("Stack after pushing: " + stack);  // [apple, banana, cherry]
System.out.println("Top element (peek): " + stack.peek());  // cherry
System.out.println("Popped element: " + stack.pop());  // cherry
System.out.println("Stack after popping: " + stack);  // [apple, banana]
```

This example demonstrates common stack operations such as `push()`, `pop()`, and `peek()`.

---

## 5. Stack vs. Other Data Structures

### Stack vs. Queue

| **Stack**                 | **Queue**                    |
|---------------------------|------------------------------|
| Follows **LIFO** (Last In First Out) | Follows **FIFO** (First In First Out)  |
| `push()`, `pop()` to add/remove    | `enqueue()`, `dequeue()` to add/remove |
| Example: Call stack in recursion   | Example: CPU task scheduling           |

### Stack vs. Deque

| **Stack**                 | **Deque**                             |
|---------------------------|---------------------------------------|
| Operations only at one end | Operations at both ends (front & back)|
| No direct access to bottom elements | Can access from both ends |
| Used for LIFO operations   | Can be used as both Stack and Queue  |

### Stack vs. ArrayList

| **Stack**                 | **ArrayList**                      |
|---------------------------|------------------------------------|
| Specially designed for **LIFO** operations | Dynamic array implementation |
| Slower random access       | Faster random access               |
| `push()`, `pop()` are direct methods | No direct methods for LIFO |

---

## 6. When to Use Stack

A **stack** should be chosen when you need **last-in-first-out** (LIFO) access to the elements. Stacks are ideal in situations where the last added element must be the first one to be processed, such as:

- Managing function calls in recursion
- Keeping track of previous operations (e.g., Undo/Redo in editors)
- Parsing and evaluating expressions (mathematical or logical)

---

## 7. Best Practices and Limitations

### Best Practices

- **Use stacks** in recursive algorithms or backtracking solutions where you need to process the most recently added elements first.
- For **better performance** and flexibility, use **Deque** instead of `Stack` class, since `Deque` supports both LIFO and FIFO operations.

### Limitations

- The `Stack` class in Java is a **legacy class**. For better performance and versatility, it's recommended to use **Deque** from `java.util` as a stack replacement.

### Using Deque as Stack

```java
Deque<Integer> stack = new ArrayDeque<>();
stack.push(1);
stack.push(2);
System.out.println(stack.pop()); // Output: 2
```

---

## 8. Authentic References and Further Reading

Here are some authoritative sources where you can learn more about stacks and their use in Java:

- [Oracle Java Documentation on Stack](https://docs.oracle.com/javase/8/docs/api/java/util/Stack.html)
- [GeeksforGeeks: Stack Data Structure](https://www.geeksforgeeks.org/stack-data-structure-introduction-program/)
- [Java Stack Tutorial - JavaTPoint](https://www.javatpoint.com/java-stack)
- [Effective Java by Joshua Bloch (Memory Management)](https://www.effectivejava.com/)

---

## 9. Conclusion

Stacks are a fundamental data structure used in a variety of problem-solving scenarios, particularly when handling recursive processes or when the most recent data needs to be processed first. With their LIFO nature, they are ideal for managing tasks like backtracking, expression evaluation, and maintaining function calls.

In modern Java development, though the `Stack` class is available, it's recommended to use **Deque** for better performance and flexibility.

## [LeetCode Stack Related Problems](https://leetcode.com/problem-list/stack/)
