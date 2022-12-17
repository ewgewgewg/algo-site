+++
title = "JS Language"
date = "2022-11-27T00:42:50-05:00"
author = "EWG"
authorTwitter = "" #do not include @
cover = ""
tags = ["", ""]
keywords = ["", ""]
description = ""
showFullContent = false
readingTime = false
hideComments = false
color = "" #color from the theme settings
+++

# Summary

An overview of JavaScript syntax. If you are a beginner, read this before the [JS Algos](/posts/js-algos) file.

# Variables, Types, and Comments

JavaScript stores information (called values) within `let` and `const` variables -- `const` variables cannot be changed, but `let` variables can be reassigned later. Variables can contain booleans (`true` or `false`), strings (of text), or numbers, or have `null` or `undefined` values. Booleans, strings, numbers, null, and undefined are called types, and can be detected by the `typeof` command. Typing on a line after `//` or on lines between `/*` and `*/` creates notes.

{{< code language="javascript" expand="Show" collapse="Hide" isCollapsed="false" >}}
const test = true
typeof test // this code results in "boolean"

let secondTest = "word"
// quotes in '' and `` are also allowed

secondTest = 8
// secondTest was just redefined

let thirdTest = null

let fourthTest
// fourthTest is undefined

{{< /code >}}

# Functions With Math

Functions process input parameters inside `()` and typically return results inside the `{}` of the function body. Functions can be assigned to variables or explicitly named as functions. Defined functions can be invoked by naming them and adding `()`, which can contain parameter values called arguments. Math can be done with `+`, `-`, `*`, `/` (as division), and `**` (to raise immediately previous number to an exponent after). These five operators can be coupled with assignment, like in `+=`. Parentheses also work as in regular math and [precedence of operations is respected as described in the MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Operator_Precedence). `Math.floor` can be used like a function to round down, while `Math.ceil` can be used to round up. `Math.max` and `Math.min` find maximums and minimums of input. `Math.abs` finds absolute value. `Math.pow` takes a base value and then an exponent and puts the first to the power of the second. `Math.random` creates an approximately random floating point (decimal) number from 0 to just less than 0 with a uniform distribution.

{{< code language="javascript" expand="Show" collapse="Hide" isCollapsed="false" >}}

const square = (value) => {

    return value  ** 2

}

function addTwoThenMultiplyByThree (value) {

    return (value + 2) * 3

}

const addThree = (value) => {
    value += 3
    return value
}

square(4) // returns 16
addTwoThenMultiplyByThree(1) // returns 9
addThree(5) // returns 8

Math.floor(2.1) // returns 2
Math.ceil(4.9) // returns 5
Math.max(1, 2, 3) // returns 3
Math.min(1, 2, 3) // returns 1
Math.abs(-1) // returns 1
Math.pow(2, 3) // returns 8
Math.random() // returns some random floating point number

{{< /code >}}

Because JavaScript tries to change types when appropriate, adding a string to a number, or adding two strings, joins characters as a string. `String` and `Number` also can take a argument each and transform them as appropriate. You can also test if `Number.isInteger`.

{{< code language="javascript" expand="Show" collapse="Hide" isCollapsed="false" >}}

const elevenString = "1" + 1 // contains "11"
const twelveString = "1" + "2" // contains "12"

const aString = String(1) // contains "1"
const aNumber = Number("1") // contains 1

const integerState = Number.isInteger(aNumber) // contains true
const notAnIntegerState = Number.isInteger("a") // contains false

{{< /code >}}

Assigment of a variable and math can happen at the same time, like so:

{{< code language="javascript" expand="Show" collapse="Hide" isCollapsed="false" >}}

let transform = 2

transform += 2 // transform now contains 4
transform -= 3 // transform now contains 1
transform *= 2 // transform now contains 2
transform /= 2 // transform now contains 1

{{< /code >}}

You can set variables to infinity and check if they are infinite.

{{< code language="javascript" expand="Show" collapse="Hide" isCollapsed="false" >}}

const small = 0
isFinite(small) // contains true

const large = Infinity
isFinite(large) // contains false

{{< /code >}}

You can also check if a value is not a number. The function will recognize valid numbers in strings.

{{< code language="javascript" expand="Show" collapse="Hide" isCollapsed="false" >}}

isNaN("a") // contains true
isNaN("-5") // contains false
isNaN(-5) // contains false

{{< /code >}}

You can convert a string to a number if you pass in the string and then the base.

{{< code language="javascript" expand="Show" collapse="Hide" isCollapsed="false" >}}

parseInt("4", 10) // contains 4
parseInt("100", 2) // contains 4

{{< /code >}}

Default parameters provide values in functions if no values are passed in.

{{< code language="javascript" expand="Show" collapse="Hide" isCollapsed="false" >}}

const add = (a = 1, b = 2) => a + b

add() // returns 3
add(2) // returns 4
add(2,3) // returns 5

{{< /code >}}

# Conditionals and Comparison Operators

Code can be optional. Code written in brackets after an `if` condition in parentheses only runs if the condition is parentheses is `true`. An `if` condition can be immediately followed by one or more `else if`, also with parentetical activation conditions, and then an `else` which serves as a catch-all and thus has no explicit conditions. Typical conditions include `===` to check if two values are truly equal, `>=`, `<=`, `>`, and `<` as relative comparators, `!` as negation, and `%`, the modulo operator, which returns what would be the remainder of a division between numbers on either side.

{{< code language="javascript" expand="Show" collapse="Hide" isCollapsed="false" >}}

const singleFizzBuzz = (value) => {

    if (!(value % 15)){
        return "FizzBuzz"
    } else if (!(value % 3){
        return "Fizz"
    } else if (!(value % 5)){
        return "Buzz
    } else {
        return value
    }

}

singleFizzBuzz(6) // returns "Fizz"

{{< /code >}}

The modulo operator can also be coupled with assignment, like `%=`.

{{< code language="javascript" expand="Show" collapse="Hide" isCollapsed="false" >}}

let number = 10
number %= 3 // stored value in number after this operation is 1

{{< /code >}}

Ternaries are short boolean conditionals that help with assignment. A condition is followed by `?`, then a value to assign if the condition is true, then `:`, then a value to assign if the condition is false.

{{< code language="javascript" expand="Show" collapse="Hide" isCollapsed="false" >}}

const value = "green"
const isValueBlue = value === "blue" ? "blue" : "not blue" // stored variable is "not blue"
const isValueGreen = value === "green" ? "green" : "not green" // stored variable is "green" 

{{< /code >}}

# Boolean Operators

`||` is or, and `&&` is and. `&&` has higher priority than `||` and will evaluate first. Most content can be used in place of true, while an empty string, zero, null, or undefined all can be used in boolean math as false. Using `!!` coerces a value to its boolean equivalant.

{{< code language="javascript" expand="Show" collapse="Hide" isCollapsed="false" >}}

const andTrue = true && true // stored variable is true
const falseFromOneFalse = false && true // stored variable is false
const falseFromBoth = false && false // stored variable is false

const orTrue = true || true // stored variable is true
const trueFromOneTrue = true || false // stored variable is true
const falseNeedsBoth = false || false // stored variable is false

const orderOfOperations = false || true && false // stored variable is false because && operates first

const typeChangeDemonstrationTruthy = !!("a" || 1) // stored variable is true
const typeChangeDemonstrationFalsy = !!("" || 0 || null || undefined) // stored variable is false

{{< /code >}}

Boolean operators can also do conditional assigment.

{{< code language="javascript" expand="Show" collapse="Hide" isCollapsed="false" >}}

let color = "blue"

true && (color = "red")

// color is "red"

false && (color = "blue")

// color is still "red"

{{< /code >}}

# Loops, Increments, and Decrements

Two basic types of repeating code blocks are `while` loops and `for` loops. A `while` loop continues until a parenthetical condition is false, and may not even run once (`do...while` loops guarantee at least one cycle). Meanwhile, `for` loops often three parts in their parenthetical, an initialized variable, a check condition that stops the loop if met, and a step condition that is expected to modify the initalized variable at the end of each loop. Alternatively, `for` loops can use a `let item of [items]` format in their parenthetical, where `[items]` can actually be any predefined iterable object, and `item`, which can be named arbitrarily in the loop, increments over everything inside. Behavior inside each loop is contained in `{}`, just like with functions. The operators `++` and `--` increase or decrease a number by 1, respectively. They can come before a number to run before a check.

{{< code language="javascript" expand="Show" collapse="Hide" isCollapsed="false" >}}

let i = 0

while(i < 4){

    i++

}

// i is 4 here

do {

    i++

} while (i < 2)

// i is 5 here because the do block runs once even though the condition is not met

for (let j = 0; j < 3; j++){

    i--

}

// i is 2 here

const series = [3, 6, 9, 12]

for (element of series){
    i += element
}

// i is 32 here

let num = 0
const go = ++num || 0 // contains 1
const stop = --num || 0 // contains 0

{{< /code >}}

The keyword `break` stops the closest outer loop, while the keyword `continue` skips to the next step of the loop.

{{< code language="javascript" expand="Show" collapse="Hide" isCollapsed="false" >}}

let count = 0

for (let i = 0; i < 6; i++){
    count++
    if(count === 3) continue
    count++
}

// count is 11 here (would be 12 without continue)

while(true){
    count++
    if (count === 20) break
}

//count is 20 here

{{< /code >}}

# Objects and Logging

Data structures called objects are useful for manipulating data. Regular objects are made to store data pairs: keys that point to values. Special objects called maps and arrays also use keys and values. Maps keys can be any data type, including another object, while regular object keys can only be strings and array keys can only be numbers called indexes (array items always have an order). Sets, another type of object, contain unique values without keys.

Dot notation is used to invoke methods, which are similar to functions--`set.add` with one parenthetical argument adds a value, and `map.set` with two arguments adds a key with its value, while `set.has` tests for presence of a parenthetical value, and `map.has` tests for presence of a parenthetical key. Meanwhile, `map.get` can take a key and return a value. Regular objects use dot or bracket notation to set and look up keys and values, while arrays use bracket notation only. Meanwhile, a special command, `console.log` prints data in parenthetical arguments to a nearby terminal (aka command line interface), which is usually what is used to run your program.

{{< code language="javascript" expand="Show" collapse="Hide" isCollapsed="false" >}}

const regularObject = {"car": 2 }
const map = new Map()
const array = [1,2]
const set = new Set([5,6])

regularObject.tree = 4
set.add(4)
array[2] = 4
map.set("tree", 4)

console.log(regularObject.car) // prints 2
console.log(regularObject.tree) // prints 4
console.log(regularObject["tree"]) // prints 4
console.log(set.has(4)) // prints true
console.log(set.has(5)) // prints true
console.log(array[0]) // prints 1 -- the leftmost location in an array is known as the 0 index
console.log(array[1]) // prints 2
console.log(array[2]) // prints 4
console.log(map.has("tree")) // prints true
console.log(map.get("tree")) // prints 4

{{< /code >}}

`Keys`, `values`, and keys and values as `entries` can be extracted with methods of of prototype term `Object`, taking the source object as the argument passed into the method. Object keys can be looped by using `for` loops can use a `let key in [items]` format in their parenthetical, where `[items]` can actually be any predefined iterable object, and `key`, which can be named arbitrarily in the loop, increments over every `key` inside.

{{< code language="javascript" expand="Show" collapse="Hide" isCollapsed="false" >}}

const sampleObject = {color1: "blue", color2: "red"}

const keys = Object.keys(sampleObject)
console.log(keys) // prints ["color1", "color2"]

const values = Object.values(sampleObject)
console.log(values) // prints ["blue", "red"]

const entries = Object.entries(sampleObject)
console.log(entries) // prints [["color1", "blue"], ["color2", "red"]]

for (let key in sampleObject){
    console.log(key) // prints "color1", then "color2"
}

{{< /code >}}

You can `delete` keys in objects.

{{< code language="javascript" expand="Show" collapse="Hide" isCollapsed="false" >}}

const sampleObject = {color1: "blue", color2: "red"}

delete sampleObject.color1

console.log(sampleObject) // prints {color2: "red"}

{{< /code >}}

Maps can be initialized with arrays of arrays of pairs, where each pair is converted into a key and value. They also have additional methods. For example, they have `size`, and can `delete` values and also iterate through their keys.

{{< code language="javascript" expand="Show" collapse="Hide" isCollapsed="false" >}}

const map = new Map([[0, 1]])
map.set(1,2)
map.set(2,3)
map.set(3,4)
map.set(4,5)

map.delete(4)
map.delete(map.keys().next().value)

console.log(map.size) // prints 3
console.log(map.has(4)) // prints false
console.log(map.has(0)) // prints false
console.log(map.has(1)) // prints true

{{< /code >}}

Sets also have `size`.

{{< code language="javascript" expand="Show" collapse="Hide" isCollapsed="false" >}}

const set = new Set()

console.log(set.size) // prints 0

{{< /code >}}

Template literal syntax -- strings created with \` marks that contain variables surrounded by `${` and `}` -- is useful for generating patterns of strings, which can enhance logging or the creation of objects or functions.

{{< code language="javascript" expand="Show" collapse="Hide" isCollapsed="false" >}}

const variable1 = "A"
const variable2 = "B"
console.log(`variable1: ${variable1}, variable2: ${variable2}`) // prints `variable1: A, variable2: B`

{{< /code >}}

# Methods of Arrays

Arrays have `length`, can `push` new values to the lowest empty index, `pop` to unfill the greatest filled index and return the popped value, `shift` the value at 0 index off and return the shift value (meaning every other value in the array is downshifted one index), or `unshift` to move in a new 0 index value and rotate every other value up one index. These `push`, `pop`, `shift`, and `unshift` all act on the array they are run from and do not require assignment to a new array.

Arrays can also transform or `map` their values into a new array. The `map` callback function, a function used as a method argument, itself contains value and index arguments for every array location. Arrays can `sort` their values in place. Explicitly comparing two arguments in a callback lets you `sort` numbers, and if the return of the callback is positive the second argument will return first, while if the return of the callback is negative the first argument will return first. A tie leaves original order. Otherwise the default is to sort even numbers as strings. Arrays can also `slice` to return only the content starting at the index matching the first argument and continuing to the end of the array, or just before the index matching the second argument. The `slice` method will always return a copy even if you slice at the 0 index. Arrays can also test `every` value for some condition and return a boolean (`true` or `false`) depending on if every test is passed.

The `fill` method takes an argument that can be used to fill whatever number of items is passed into a `new Array`, or an existing array. `fill` acts on the array it is run from and does not need to be assigned to a new variable.

The `reverse` method reverses the elements in an array, and `join`, which takes an argument, merges the elements of the array and places the value of the argument between each, all in a single string. Array methods, like all methods, can be chained when the return of one has the method of the next.

{{< code language="javascript" expand="Show" collapse="Hide" isCollapsed="false" >}}

const array = [2,4,6]
console.log(array.length) // prints 3

const popped = array.pop()
console.log(popped) // prints 6

array.push(10)
console.log(array[2]) // prints 10

array.shift()
console.log(array[0]) // prints 4

array.unshift(2)
console.log(array[0]) // prints 2

const array2 = array.map((value, index) => value + index )
console.log(array2) // prints [2, 5, 12]

array.sort((a, b) => b - a)
console.log(array) // prints [10, 4, 2]
array.sort((a,b) => a - b)
console.log(array) // prints [2, 4, 10]
array.sort()
console.log(array) // prints [10, 2, 4] because it alphabetizes numbers this way
console.log(["a","c","b"].sort()) // prints ["a", "b", "c"]

const array3 = array.slice(1)
console.log(array3) // copy starts at index 1 of the former array and prints [4, 10]

const array4 = array.slice(1, 2)
console.log(array4) // copy starts at index 1 and ends just below index 3 of former array, printing [4]

const allArrayValuesGreaterThanOneBoolean = array.every((value) => value > 1)
console.log(allArrayValuesGreaterThanOneBoolean) // prints true

array.fill(4)
console.log(array) // prints [4, 4, 4]

const array5 = new Array(5).fill(5)
console.log(array5) // prints [5, 5, 5, 5, 5]

const string = array2.reverse().join("") // prints "1252"

{{< /code >}}

The array method `concat` can to the parent array an array argument (flattened into one array), or push value arguments into the parent array. Combinations also possible.

{{< code language="javascript" expand="Show" collapse="Hide" isCollapsed="false" >}}

const array = [1, 2, 3].concat([4, 5])
console.log(array) // prints [1, 2, 3, 4, 5]

const array2 = [1, 2, 3].concat(4, 5)
console.log(array2) // prints [1, 2, 3, 4, 5]

const array3 = [1, 2, 3].concat(4, 5, [6, 7, 8])
console.log(array3) // prints [1, 2, 3, 4, 5, 6, 7, 8]

{{< /code >}}

The array method `splice` takes an insertion location, a deletion count, and any number of insertion items as arguments. It acts on the array it is run from and does not need to be part of direct assignment. It can also take negative numbers and count from the back. If no second argument is provided, all elements from the given index onwards are removed.

{{< code language="javascript" expand="Show" collapse="Hide" isCollapsed="false" >}}

const array = [1, 2, 3]

array.splice(1, 1)
console.log(array) // prints [1, 3]

array.splice(1, 0, 4)
console.log(array) // prints [1, 4, 3]

array.splice(2, 0, 5, 6)
console.log(array) // prints [1, 4, 5, 6, 3]

array.splice(-2,1)
console.log(array) // prints [1, 4, 5, 3]

array.splice(-1)
console.log(array) // prints [1, 4, 5]

{{< /code >}}

The array method `reduce` may be tricky. For every step along its parent array, it runs a callback function with two arguments, an accumulator and the current value at the given array position. The accumulator defaults to the first value in the array, or can be set in the second argument of the `reduce` method (the first argument is the callback function). The return value of the callback at each step turns into the accumulator for the next step, which is ultimately returned out of the function.

{{< code language="javascript" expand="Show" collapse="Hide" isCollapsed="false" >}}

const array = [1, 2, 3]

const arraySum = array.reduce((accumulator, value) => accumulator + value)
console.log(arraySum) // prints 6

const arraySumPlusOne = array.reduce((accumulator, value) => accumulator + value, 1)
console.log(arraySumPlusOne) // prints 7

const arraySumOnKey = array.reduce((accumulator, value) => {return { sum: accumulator.sum + value }}, { sum: 0 })
console.log(arraySumOnKey) // prints { sum: 6 }

{{< /code >}}

The spread operator `...` takes an array and spreads its items as elements of the new array.

{{< code language="javascript" expand="Show" collapse="Hide" isCollapsed="false" >}}

const primaryColors = ["red", "blue", "yellow"]

const shapes = ["square", "circle", "triangle"]

const primaryColorsAndShapes = [...primaryColors, ...shapes]
console.log(primaryColorsAndShapes) // prints ["red", "blue", "yellow", "square", "circle", "triangle"]

{{< /code >}}

You can also check if a specific value is in an array with the `includes` method.

{{< code language="javascript" expand="Show" collapse="Hide" isCollapsed="false" >}}

console.log(["red","blue"].includes("red")) // prints true

{{< /code >}}

The `filter` method takes a callback as an argument that itself offers arguments for value, index, and the array itself as it runs its body on each location in the array it is run from. Any value that passes the test appears in a result array.

{{< code language="javascript" expand="Show" collapse="Hide" isCollapsed="false" >}}

const filtered = [1, 2, 3].filter((value, index, array) => value > array[1] || index === 0)

console.log(filtered) // prints [1, 3]

{{< /code >}}

# Methods of Strings

Strings also have `length`, and can return new strings `toLowerCase`, `toUpperCase`, or after running `replace` on their own values. Strings can also find `charCodeAt` at an index (leftmost location is the zero index, and character codes are numbers for representing unique characters, like 'a', or '.'.

{{< code language="javascript" expand="Show" collapse="Hide" isCollapsed="false" >}}

const string = "Hello"
console.log(string.length) // prints 5

const lowerCaseString = string.toLowerCase()
console.log(lowerCaseString) // prints "hello"

const upperCaseString = string.toUpperCase()
console.log(upperCaseString) // prints "HELLO"

const firstReplace = string.replace("l","a")
console.log(firstReplace) // prints "Healo"

const secondReplace = string.replace(/[0-9a-zA-Z]/g,'')
console.log(secondReplace) // this regex replaces all numbers and letters with an empty string (note the g), printing ""

const thirdReplace = string.replace(/[^0-9a-zA-Z]/g,'')
console.log(thirdReplace) // this regex would replace any non-alphanumeric characters with an empty string (note the ^), but since there are none, "Hello" is printed

const lowerCaseACharCode = firstReplace.charCodeAt(2)
console.log(lowerCaseACharCode) // returns number 97 (for 'a')
const upperCaseACharCode = "A".charCodeAt(0)
console.log(upperCaseACharCode) // returns number 65 (for 'A')

console.log(string) // prints "Hello" as the original string is unmodified

{{< /code >}}

The `includes` method checks if a string is inside a parent string and returns a boolean.

{{< code language="javascript" expand="Show" collapse="Hide" isCollapsed="false" >}}

const substring1 = "abc".includes("ab")
console.log(substring1) // prints true

const substring2 = "abc".includes("ac")
console.log(substring2) // prints false

{{< /code >}}

The `trim` method removes any whitespace on either side of the string.

{{< code language="javascript" expand="Show" collapse="Hide" isCollapsed="false" >}}

console.log("  a  ".trim()) // prints("a")
console.log("    ".trim()) // prints("")
console.log("a") // prints("a")

{{< /code >}}

The `repeat` method returns the string from which the method is called times the number in the first argument.

{{< code language="javascript" expand="Show" collapse="Hide" isCollapsed="false" >}}

console.log("abc".repeat(4)) // prints("abcabcabcabc")

{{< /code >}}

# Advanced Objects

Optional chaining with `?.` prevents an error and returns `undefined` if something the code is trying to look inside is `undefined` or `null`. This is useful if you want to stop a process gracefully if data shape is not as expected, instead of crashing the program.

{{< code language="javascript" expand="Show" collapse="Hide" isCollapsed="false" >}}

const hasDepth = {

    test: {
        test2: true
    }

}

const noDepth = {

    test: {}

}

console.log(hasDepth?.test?.test2) // prints true
console.log(noDepth?.test?.test2) // prints undefined

{{< /code >}}

Functions in JavaScript can be used as classes, creating objects with class-defined structures that can store independent data. Assigning a function labeled as `function` to a variable and defining substructures inside it under `this.` allows for these substructures to change independently in class instances while always starting the same. Using the `new` keyword in front of the function variable creates a class instance. Shared methods can be created by assinging them to the prototype of the class definition. These will behave the same across all class instances but reference data specific to the instance.

{{< code language="javascript" expand="Show" collapse="Hide" isCollapsed="false" >}}

const test = function () {

    this.store = []

}

const a = new test
a.store.push(4)

const b = new test

console.log(a.store) // prints [4]

console.log(b.store) // prints []

test.prototype.lengthPlusOne = function () {
return this.store.length + 1
}

console.log(a.lengthPlusOne()) prints 2

console.log(b.lengthPlusOne()) prints 1

{{< /code >}}

# Binary Operators

In binary, or base 2 math, `&` is the bitwise AND, only keeping digits that match, while `|` is the bitwise OR, and `^` is XOR, placing a 1 in a digit in the combination of two XORed numbers when the parent elements mismatch. Meanwhile `<<` adds a number of binary 0 to the low end number on its left equal to the number on its right (`<< 1` multiplies by 2), and `>>` removes a number of binary digits from the low end of the number of its left equal to the number on its right. All of these can be coupled with assignment, such as `&=`.

{{< code language="javascript" expand="Show" collapse="Hide" isCollapsed="false" >}}

const binaryA = 2 & 2
console.log(binaryA) // prints 2

const binaryB = 2 | 0
console.log(binaryB) // prints 2

const binaryC = 2 ^ 2
console.log(binaryC) // prints 0

const binaryD = 4 << 1
console.log(binaryD) // prints 8

const binaryE = 4 >> 1
console.log(binaryE) // prints 2

let binaryF = 1
binaryF &= 1
console.log(binaryF) // prints 1

{{< /code >}}