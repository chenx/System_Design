# 52 JavaScript Interview Questions

https://dev.to/m_midas/52-frontend-interview-questions-javascript-59h6

### 1. JS data types?

Number, Boolean, String, Object, Date, undefined, null, Symbol, BigInt.

### 3. 4 ways to declare a varaible?

```
  foo = 123; 
  var foo = 123; // Similar to the first method.
  let a = 123; 
  const a = 123; 
```

### 5. Arrow functions and the differences from regular functions.

- Arrow functions cannot use the arguments object.
- They have a different syntax.
- Arrow functions do not have their own this context. When referencing this, an arrow function takes the context from the surrounding scope.
- Arrow functions cannot be used as constructor functions. In other words, they cannot be invoked with the new keyword.

### 6. What is a closure and why are they needed? 

A closure is a function along with all the external variables that it has access to. For example, there is a function that has a nested function which will close over and retain the variables from its parent.
```
function parent() { 
    const a = 5; 
    return function child() { 
        console.log(5); // child closes over the variable 'a'; 
    } 
}
```

### 9. How to check for the presence of a property in an object? 

```
const obj = {
  year: 2023,
  name: "John"
}

console.log(obj.hasOwnProperty("year")) // true
console.log("year" in obj) // true
console.log("ye" in obj) // false
```

### 10. How to access an object property?

```
const obj = {
  year: 2023,
  name: "John"
}

console.log(obj['year']) // 2023
console.log(obj.name) // John
```

### 12. What are the ways to create an object? 

### 15. How to check if an object is an array? Array.isArray()

### 16. What is the purpose of the spread operator?

The spread operator (...) is used to unpack arrays or objects.
It allows you to expand elements that are iterable, such as arrays and strings. 

### 17. How to avoid reference dependency when copying an object? 

If the object does not contain nested objects, use spread operator or Object.assign() method

```
const obj = {
 firstName: 'John',
 lastName: 'Johnson'
}

const copy = {...obj}
// or
const copy = Object.assign({}, obj)
```

If the object contains nested objects:

```
const obj = {
    data: {
        id: 1
    }
}

const copy = JSON.parse(JSON.stringify(obj))
```

Or use the lodash library's deepClone() function, or the global window.structuredClone().

### 18. How to change the context of a function? 

- Using the bind() method, which returns a new function with the bound context.
- Using call() and apply() methods. The main difference is that call() accepts a sequence of arguments, while apply() accepts an array of arguments as the second parameter.

### 20. What is destructuring?

Destructuring is a syntax that allows us to unpack arrays and objects into multiple variables.

### 27. What's the difference between Function Expression and Function Declaration? 

Function Declaration is the traditional way of declaring a function.

```
function foo() {
    console.log('Hello World');
}
```

Function Expression:

```
let foo = function() {
    console.log('Hello World');
}

```

### 29. How can you get a list of keys and a list of values from an object?

You can use Object.keys() to get a list of keys and Object.values() to get a list of values.

Object.entries() will return [key, value] pairs.

### 30. Provide an example of new functionality in ES6. 

- let and const
- arrow functions
- default parameters
- spread operator (...)
- destructuring
- Map
- Class inheritance

### 33. What are generators? 

Generators produce a sequence of values one by one as needed. Generators work well with objects and make it easy to create data streams.

### 37. What are WeakSet and WeakMap and how do they differ from Map and Set? 

- keys in WeakMap must be objects, not primitive values.

### 40. How to check which class an object was created from? 

```
class Person {}
const person = new Person()
console.log(person instanceof Person) // true
```

### 42. What is a pure function?

A pure function is a function that satisfies two conditions:

- Every time the function is called with the same set of arguments, it returns the same result.
- It has no side effects, meaning it does not modify variables outside the function.

### 43. What is a higher-order function?

A higher-order function is a function that takes another function as an argument or returns a function as a result.

### 45. Write your own implementation of the bind method.

To implement it, we can use closure and the apply() method to bind the function to the context.

```
function bind(context, func) {
  return function(...args) {
    func.apply(context, args)
  }
}
```

### 46. Write a calculator function with methods plus, minus, multiply, divide, and get. The function must work through optional chaining.

```
function calculator() {
  let result = 0;

  function plus(val) {
    result += val;
    return this;
  }

  function minus(val) {
    result -= val;
    return this;
  }

  function divide(val) {
    result /= val;
    return this;
  }

  function multiply(val) {
    result *= val;
    return this;
  }

  function get() {
    console.log(result);
    return this;
  }

  return { plus, minus, divide, multiply, get };
}

let calc = calculator();
calc.plus(2).minus(1).plus(19).divide(2).multiply(3).get(); // 30
```

### 47. Write a randomSort function that takes an array of numbers and sorts the array in random order. 
```
function randomSort(array) {
  return array.sort(() => {
    return 0.5 - Math.random();
  });
}
const arr = [2, 1, 3, -2, 9]
console.log(randomSort(arr)) // [-2, 2, 1, 3, 9]
console.log(randomSort(arr)) // [2, 1, -2, 9, 3]
```

### 48. Write a deleteGreatestValue function that takes a two-dimensional array of numbers and removes the greatest number from each nested array. 
```
function deleteGreatestValue(array) {
  for (let i = 0; i < array.length; i++) {
    const max = Math.max(...array[i]);
    const maxIndex = array[i].indexOf(max);
    array[i].splice(maxIndex, 1);
  }
  return array;
}

const arr = [[1, 4, 4], [2, 6, 3], [9, 2, 7]]
console.log(deleteGreatestValue(arr)) // [[1, 4], [2, 3], [2, 7]]
```

The splice() method of Array instances changes the contents of an array by removing or replacing existing elements and/or adding new elements in place.

The splice() method returns an array containing the deleted elements. If no elements are removed, it returns an empty array. 

```
// https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/splice
splice(start)
splice(start, deleteCount)
splice(start, deleteCount, item1)
splice(start, deleteCount, item1, item2)
splice(start, deleteCount, item1, item2, /* …, */ itemN)
```

### 49. Write a sortPeople function that takes an array of strings names and an array of numbers heights, where names[i] == heights[i]. It should sort the names array based on the heights array. 

```
function sortPeople(names, heights) {
  const array = [];
  for (let [i, name] of names.entries()) {
    array.push([name, heights[i]]);
  }
  return array.sort((a, b) => b[1] - a[1]).map(([name]) => name);
}
const names = ['John', 'Maria', 'Alexa', 'Robert']
const heights = [180, 160, 165, 187]
console.log(sortPeople(names, heights)) // ['Robert', 'John', 'Alexa', 'Maria']
```

### 50. Write a subsets function that takes an array of numbers nums and returns all possible variations of arrays from those numbers. 

```
function subsets(nums) {
  let result = [[]];

  for (let num of nums) { // Iterate through each number in the nums array
    const currentSize = result.length; // Get the current size of result to use it in the loop.

    for (let i = 0; i < currentSize; i++) {
      let subArray = [...result[i], num]; // Create a new subarray by adding the current number to the result[i] element.
      result.push(subArray);
    }
  }

  return result; // Return all possible variations of arrays from the numbers.
}
```

### 51. How to reverse a linked list?

```
function reverseLinkedList(node) {
  let result = null; // Initialize the result variable with null, it will hold the reversed list.
  let root = head; // Initialize the root variable with head, pointing to the start of the list.

  // While root is not null (until we reach the end of the list)
  while (root) {
    if (result) { // If result already has elements
      result = new ListNode(root.val, result); // Create a new list node with the current value root.val and a pointer to the next node result. Update result.
    } else { // If result doesn't have any elements yet...
      result = new ListNode(root.val, null); // Create a new list node with the current value root.val and null as the pointer to the next node. Update result.
    }
    root = root.next; // Move to the next element in the list.
  }
  return result; // Return the reversed list.
}
```

A better method:

```
function reverseLinkedList(head) {
    let h = null;

    while (head !== null) {
        const tmp = head;
        head = head.next;
        tmp.next = h;
        h = tmp;
    }
    return h;
}
```

### 52. How to sort a linked list? 

```
function sortList (head) {
  if (!head) {
    return null;
  }
  let root = head;
  let arr = [];

  while(root){
    arr.push(root.val);
    root = root.next;
  }
  arr.sort((a, b) => a - b);

  let node = new ListNode(arr[0]);
  head = node;

  let temp = head;

  for(let i = 1; i < arr.length; i++){
    let node = new ListNode(arr[i]);
    temp.next = node;
    temp = temp.next;       
  }
  return head;
};
```

Insertion sort:

```
class Node {
    constructor(value = 0, next = null) {
        this.value = value;
        this.next = next;
    }
}

function printLinkedList(head) {
    while (head !== null) {
        console.log(head.value + ' ');
        head = head.next;
    }
}

function createLinkedList(array) {
    const head = new Node();
    let tail = head;
    for (const value of array) {
        tail.next = new Node(value);
        tail = tail.next;
    }
    return head.next;
}

function sortLinkedList(list) {
    const head = new Node();
    let tail;

    while (list !== null) {
        const nextNode = list;
        list = list.next;

        // insertion sort.
        let inserted = false; 
        for (tail = head; tail.next !== null; tail = tail.next) {
            if (tail.next.value >= nextNode.value) {
                nextNode.next = tail.next;
                tail.next = nextNode;
                inserted = true;
                break;
            }
        }
        if (! inserted) { // insert at the end.
            tail.next = nextNode;
            nextNode.next = null;
        }
    }
    return head.next;
}

const list = createLinkedList([3,2,4,5,1]);
const list2 = list;
printLinkedList(list2);

console.log("sort list:");
printLinkedList(sortLinkedList(list));
```

### 53. Permutation

```
function permutation(array) {
    const result = [];
    perm(array, 0);
    return result;

    function perm(array, pos) {
        if (pos == array.length) {
            result.push([...array]);
            return;
        }

        for (let i = pos; i < array.length; ++ i) {
            swap(array, i, pos);
            perm(array, pos + 1);
            swap(array, i, pos);
        }
    }

    function swap(array, i, j) {
        const tmp = array[i];
        array[i] = array[j];
        array[j] = tmp;
    }
}

console.log(permutation([1,2,3]));
```

### 54. Combination

```
function combination(array, k) {
    const result = [];
    const curCombination = [];
    comb(array, k, 0, curCombination);
    return result;

    function comb(array, k, pos, curCombination) {
        if (curCombination.length == k) {
            result.push([...curCombination]);
            return;
        }

        for (let i = pos; i < array.length; ++ i) {
            curCombination.push(array[i]);
            comb(array, k, i + 1, curCombination);
            curCombination.pop();
        }
    }
}

console.log(combination([1,2,3,4], 3));
```
