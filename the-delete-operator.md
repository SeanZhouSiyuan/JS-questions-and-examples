## The delete Operator

The JavaScript `delete` operator removes a property from an object.

**Example**

```javascript
var person = {
  name: 'John',
  age: 26,
  gender: 'Male'
};
delete person.gender; // true
delete person.salary; // true even though person.salary is not defined
person.gender; // undefined
```

Note that deleting a property that does not exist also returns `true`.

The expression returns `true` for all configurable properties. If it is used on an non-configurable property, the expression will return `false`. In strict mode, it will throw an error.

**Example - non-configurable property**

```javascript
// Any property defined with var, let or const
// is non-configurable
var bar = 10;
delete bar; // false
bar; // 10

var fn(){}
delete fn; // false

'use strict';
var person = {};
Object.defineProperty(person, 'name', {configurable: false});
delete person.name; // TypeError: Cannot delete property 'name' of #<Object>
```

**Example - configurable property**

```javascript
baz = 5;
// baz becomes a property of the global scope,
// and can be deleted since it is declared without var.
delete baz; // true
baz; // ReferenceError: baz is not defined

var arr = [1, 2, 3, 4];
delete arr[3]; // true
arr; // [1, 2, 3, undefined × 1]
```

Also note that if an object has a property which can be found on the object's prototype chain, deleting this property returns `true`, but the property is still available since it is available in the prototype chain.

**Example - prototype chain**

```javascript
function Person(name, age, gender) {
  this.name = name;
  this.age = age;
  this.gender = gender;
}
Person.prototype.name = 'name';
var foo = new Person('John', 25, 'Male');
// The name property exists in both foo and its prototype chain
foo.name;        // "John"
delete foo.name; // true
foo.name;        // "name"
```