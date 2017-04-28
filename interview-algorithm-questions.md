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
     var arr = unsorted.sort(function(a, b){return a - b;});
     // Get two possible largest products
     var product1 = arr[0] * arr[1] * arr[arr.length - 1];
     var product2 = arr[arr.length - 1] * arr[arr.length - 2] * arr[arr.length - 3];
     // Do the comparison
     if(product1 >= product2) {return product1;}
     else {return product2;}
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
   Array.from(new Set(arr)); // [1, 2, 3, 5, 9, 8]

   // ES5 implementation
   function uniqueArray(arr) {
     for(let i = 0; i < arr.length; i++) {
       let lastPos = arr.lastIndexOf(arr[i]);
       // if the current value is not unique, remove its last occurrence
       if(i != lastPos) {
         arr.splice(lastPos, 1);
         // To check again whether the current value is unique
         i--;
       }
     }
     return arr;
   }

   // Alternative ES5 implementation
   function uniqueArrayAtl(arr) {
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
       product[i] = array.reduce(function(initial, value) {
         return initial * value;
       }, 1);
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

## The MIT License (MIT)

Copyright (c) 2016 Kenny Chan

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.