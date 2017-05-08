# Algorithm Questions in JavaScript

## Array

1. Given an array of integers, find the largest product yielded from three of the integers:

   ```javascript
   var unsortedArray = [-10, 7, 29, 30, 5, -10, -70];
   computeProduct(unsortedArray); // 21000
   ```

   **Solution:**

   ```javascript
   function computeProduct(unsorted) {
     // Sort the array ascending
     var arr = unsorted.sort((a, b) => a - b);
     // Get two possible largest products
     var product1 = arr[0] * arr[1] * arr[arr.length - 1];
     var product2 = arr[arr.length - 1] * arr[arr.length - 2] * arr[arr.length - 3];
     // Do the comparison
     return product1 >= product2 ? product1 : product2;
   }
   ```

2. Being told that an unsorted array contains (n - 1) of n consecutive numbers (where the bounds are defined), find the missing number in O(n) time:

   ```javascript
   var arr = [2, 5, 1, 4, 9, 6, 3, 7];
   var upperBound = 9;
   var lowerBound = 1;
   findMissingNumber(arr, upperBound, lowerBound); // 8
   ```

   **Solution:**

   ```javascript
   function findMissingNumber(arr, upperBound, lowerBound) {
     for(let i = lowerBound; i <= upperBound; i++) {
       if(arr.indexOf(i) === -1) {return i;}
     }
   }

   // The above solution is in O(n^2) time, which is not so efficient.
   // A faster solution:
   function findMissingNumber(arr, upperBound, lowerBound) {
     var sum = 0, sumInTheory = 0;
     // Find sum of the array
     for(let value of arr) {
       sum += value;
     }
     // Find theoretical sum of the array
     sumInTheory = (arr.length + 1) * (upperBound + lowerBound) / 2;
     return sumInTheory - sum;
   }
   ```

3. Removing duplicates of an array and returning an array of only unique elements:

   ```javascript
   var arr = [1, 2, 3, 5, 1, 5, 9, 1, 2, 8];
   ```

   **Solution:**

   ```javascript
   // ES6 implementation
   // A set object stores unique values of any type.
   [...new Set(arr)]; // [1, 2, 3, 5, 9, 8]
   // Or Array.from(new Set(arr));

   // ES5 implementation
   function uniqueArray(arr) {
     for(let i = 0; i < arr.length; i++) {
       let lastPos = arr.lastIndexOf(arr[i]);
       // if the current value is not unique, remove its last occurrence
       if(i !== lastPos) {
         arr.splice(lastPos, 1);
         // To check again whether the current value is unique
         i--;
       }
     }
     return arr;
   }

   // Alternative ES5 implementation
   function uniqueArrayAlt(arr) {
     var hashmap = {};
     var unique = [];
     for(let value of arr) {
       if(!hashmap.hasOwnProperty(value)) {
         // If the current value is unique
         hashmap[value] = 1;
         unique.push(value);
       }
     }
     return unique;
   }
   ```

4. Given an array of integers, find the largest difference between two elements, such that the element of lesser value must come before the greater element:

   ```javascript
   var arr = [7, 8, 4, 9, 9, 15, 3, 1, 10];
   findLargestDifference(arr); // 11
   ```

   **Solution:**

   ```javascript
   function findLargestDifference(arr) {
     // If there is only one element, there is no difference
     if(arr.length <= 1) return -1;
     
     var largestDifference = -1;
     for(let i = 0; i < arr.length; i++) {
       for(let j = i; j < arr.length; j++) {
         if((arr[j] - arr[i]) > largestDifference)
           largestDifference = arr[j] - arr[i];
       }
     }
     return largestDifference;
   }

   // Faster solution:
   function findLargestDifference(arr) {
     if(arr.length <= 1) return -1;
     var currentMin = arr[0];
     var largestDifference = -1;
     for(let i = 1; i < arr.length; i++) {
       if(arr[i] < currentMin) {
         currentMin = arr[i];
       } else if(arr[i] - currentMin > largestDifference) {
         largestDifference = arr[i] - currentMin;
       }
     }
     return largestDifference;
   }
   ```

5. Given an array of integers, return an output array such that `output[i]` is equal to the product of all the elements in the array other than itself. Solve this in O(n) without division:

   ```javascript
   var firstArr = [2, 2, 4, 1];
   var secondArr = [0, 0, 0, 2];
   var thirdArr = [-2, -2, -3, 2];
   productExceptSelf(firstArr); // [8, 8, 4, 16]
   productExceptSelf(secondArr); // [0, 0, 0, 0]
   productExceptSelf(thirdArr); // [12, 12, 8, -12]
   ```

   **Solution:**

   ```javascript
   function productExceptSelf(arr) {
     var product = [];
     for(let i = 0; i < arr.length; i++) {
       let array = arr.slice();
       array.splice(i, 1);
       product[i] = array.reduce((initial, current) => initial * current, 1);
     }
     return product;
   }
   ```

6. Find the intersection of two arrays. An intersection would be the common elements that exists within both arrays. In this case, these elements should be unique:

   ```javascript
   var firstArr = [2, 2, 4, 1];
   var secondArr = [1, 2, 0, 2];
   intersection(firstArr, secondArr); // [2, 1]
   ```

   **Solution:**

   ```javascript
   function intersection(firstArr, secondArr) {
     // If one of the arrays is empty, there is no intersection
     if(firstArr.length === 0 || secondArr.length === 0)
       return -1;
     
     // Define a hashmap to handle duplicate elements
     var hashmap = {};
     var result = [];
     for(let value of firstArr) {
       if(hashmap[value]) {continue;}
       if(secondArr.indexOf(value) !== -1) {
         hashmap[value] = 1;
         result.push(value);
       }
     }
     return result;
   }

   // Alternative solution: use Array's forEach() method instead of for loop
   function intersection(firstArr, secondArr) {
     if(firstArr.length === 0 || secondArr.length === 0)
       return -1;
     
     var hashmap = {};
     var result = [];
     firstArr.forEach(function(e) {
       if(!hashmap[e] && secondArr.indexOf(e) !== -1) {
         hashmap[e] = 1;
         result.push(e);
       }
     });
     return result;
   }
   ```

## Strings

1. Given a string, reverse a given sentence:

   ```javascript
   var str = 'Welcome to this JavaScript Guide!';
   var reverseSentence = reverseBySeparator(str, '');
   var reverseWord = reverseBySeparator(str, ' ');
   ```

   **Solution:**

   ```javascript
   function reverseBySeparator(str, sep) {
     return str.split(sep).reverse().join(sep);
     // toString() cannot be used because it will add commas
   }
   ```

2. Given two strings, return `true` if they are anagrams of one another:

   ```javascript
   var first = 'Mary';
   var second = 'Army';

   isAnagram(first, second); // true
   ```

   **Solution:**

   ```javascript
   function isAnagram(first, second) {
     // Check if the parameters are strings
     if(typeof first !== 'string' || typeof second !== 'string') return -1;
     // Check if the strings have same length
     if(first.length !== second.length) return false;
     // Change both strings to lower case, convert them to arrays to sort them, and convert back to strings to compare the result
     var firstStr = first.toLowerCase().split('').sort().join('');
     var secondStr = second.toLowerCase().split('').sort().join('');
     return firstStr === secondStr;
   }
   ```

3. Check if a given string is isomorphic:

   ```javascript
   isIsomorphic('egg', 'add'); // true
   isIsomorphic('paper', 'title'); // true
   isIsomorphic('kick', 'side'); // true
   ```

   **Solution:**

   ```javascript
   function isIsomorphic(str1, str2) {
     // If either of two parameters is not string, return -1
     if(typeof str1 !== 'string' || typeof str2 !== 'string') return -1;
     // If the two strings have different length, return false
     if(str1.length !== str2.length) return false;
     var letterMap = {};
     for(let i = 0; i < str1; i++) {
       if(letterMap[str1[i]] === undefined) {
         letterMap[str1[i]] = str2[i];
       }
       else if(letterMap[str1[i]] !== letterMap[str2[i]]) {
         // If a letter exists in letterMap, but does not map to the corresponding letter from the second string, return false
         return false;
       }
     }
     return true;
   }
   ```


## Stacks and Queues

1. Implement enqueue and dequeue using only two stacks:

   **Solution:**

   ```javascript
   var inputStack = [];
   var outputStack = [];

   // For enqueue, use push()
   function enqueue(inputStack, e) {
     inputStack.push(e);
   }
   // For dequeue, use shift()
   function dequeue(outputStack) {
     return outputStack.shift();
   }
   ```

2. Create a function that will evaluate if a given expression has balanced parentheses - using stacks:

   ```javascript
   var exp1 = '{{}}{}{}';
   var exp2 = '{}{{}';

   isBalanced(exp1); // true
   isBalanced(exp2); // false
   isBalanced(''); // true
   ```

   **Solution:**

   ```javascript
   function isBalanced(str) {
     let stack = [];
     // Traverse the string expression:
     // * if the current character is an opening bracket, push it to stack
     // * if the current character is a closing bracket, check if the top character in stack matches it. If so then fine, if close return false
     // * else continue
     for(let ch of str) {
       if(ch === '{' || ch === '(' || ch === '[') {
         stack.push(ch);
       } else if(ch === '}') {
         if(stack[stack.length - 1] === '{') stack.pop();
         else return false;
       } else if(ch === ')') {
         if(stack[stack.length - 1] === '(') stack.pop();
         else return false;
       } else if(ch === ']') {
         if(stack[stack.length - 1] === '[') stack.pop();
         else return false;
       }
     }
     // If the array is not empty, it is not balanced (there are extra opening bracket)
     return stack.length === 0;
   }
   ```


## Recursion

1. Write a recursion function that returns the binary string of a given decimal number:

   ```javascript
   decimalToBinary(3); // 11
   decimalToBinary(8); // 1000
   decimalToBinary(1000); // 1111101000
   ```

   **Solution:**

   ```javascript
   // Use the built toString() method
   function decimalToBinary(n) {
     return (n).toString(2);
   }
   // non-recursion method
   function decimalToBinary(n) {
     var stack = [];
     while(n) {
       stack.push(n % 2);
       n = Math.floor(n / 2);
     }
     return stack.reverse().join('');
   }
   // Recursion method
   function decimalToBinary(n) {
     if(n >= 1) {
       if(n % 2 === 0) {
         return decimalToBinary(n / 2) + 0;
       } else {
         return decimalToBinary((n - 1) / 2) + 1;
       }
     } else {
       // Exit condition
       return '';
     }
   }
   ```

2. Write a recursive function that performs a binary search:

   ```javascript

   ```


## Numbers

1. Given an integer, determine if it is a power of 2. If so, return `true`, else return `false`:

   ```javascript
   isPowerOfTwo(4); // true
   isPowerOfTwo(1); // true
   isPowerOfTwo(0); // false
   ```

   **Solution:**

   ```javascript
   function isPowerOfTwo(n) {
     // Check if n is integer
     if(!Number.isInteger(n) || n <= 0) return false;
     for(let i = 0; ; i++) {
       // If 2 ** i is already larger than n, return false
       if(2 ** i > Number.MAX_SAFE_INTEGER || 2 ** i > n) return false;
       if(2 ** i === n) return true;
     }
   }
   // Better solution
   function isPowerOfTwo(n) {
     // Check if n is integer
     if(!Number.isInteger(n) || n <= 0) return false;
     // If log2(n) is integer, return true
     else if(Number.isInteger(Math.log2(n))) return true;
     else return false;
   }
   ```


## Javascript

1. Explain what is hoisting in Javascript:

   Hoisting is the concept in which Javascript, by default, moves all declarations to the top of the current scope. As such, a variable can be used before it has been declared. Note that Javascript only hoists declarations and not initializations.

2. Describe the functionality of the `use strict;` directive:

   the `use strict` directive defines that the Javascript should be executed in `strict mode`. One major benefit that strict mode provides is that it prevents developers from using undeclared variables. Older versions of javascript would ignore this directive declaration.

   ```javascript
   // Example of strict mode
   "use strict";

   catchThemAll();
   function catchThemAll() {
     x = 3.14; // Error will be thrown
     return x * x;
   }
   ```

3. Explain "event bubbling" and how one may prevent it:

   Event bubbling is the concept in which an event triggers at the deepest possible element, and triggers on parent elements in nesting order. As a result, when clicking on a child element one may exhibit the handler of the parent activating.

   One way to prevent event bubbling is using "event.stopPropagation()" or "event.cancelBubble" on IE < 9.

4. What is the difference between `==` and `===` in JS?

   `===` is known as a strict operator. The key difference between `==` and `===` is that the strict operator matches for both value and type, as opposed to just the value.

   ```javascript
   // Example of comparators
   0 == false; // true
   0 === false; // false

   2 == '2'; // true
   2 === '2'; // false
   ```


5. What is the difference between `null` and `undefined`?

   In Javascript, null is an assignment value, and can be assigned to a variable representing that it has no value. Undefined, on the other hand, represents that a variable has been declared but there is no value associated with it.


6. How does prototypal inheritance differ from classical inheritance?

   In classical inheritance, classes are immutable, may or may not support multiple inheritance, and may contain interfaces, final classes, and abstract classes. In contrast, prototypes are much more flexible in the sense that they may be mutable or immutable. The object may inherit from multiple prototypes, and only contains objects.


## The MIT License (MIT)

Copyright (c) 2016 Kenny Chan

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
