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

JavaScript stores information (called values) within `let` and `const` variables -- `const` values cannot be changed, but `let` variables can be reassigned later. Variables can contain booleans (`true` or `false`), strings (of text), or numbers, or have `null` or `undefined` values. Booleans, strings, numbers, null, and undefined are called types, and can be detected by the `typeof` command. Typing on a line after `//` on lines between `/*` and `*/` creates notes.

{{< code language="javascript" id="1" expand="Show" collapse="Hide" isCollapsed="false" >}}
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

Functions process input parameters inside `()` and typically return results inside the `{}` of the function body. Functions can be defined as variables or explicitly named as functions. Defined functions can be invoked by naming them and adding `()`, which can contain parameter values called arguments. Math can be done with `+`, `-`, `*`, `/` (as division), and `**` (to raise immediately previous number to an exponent after). Parentheses also work as in regular math.

{{< code language="javascript" id="2" expand="Show" collapse="Hide" isCollapsed="false" >}}

const square = (value) => {

    return value  ** 2

}

function addTwoThenMultiplyByThree (value) {

    return (value + 2) * 3

}

square(4) // returns 16
addTwoThenMultiplyByThree(1) // returns 9

{{< /code >}}

# Conditionals and Comparison Operators

Code can be optional. Code written in brackets after an `if` condition in parentheses only runs if the condition is parentheses is `true`. An `if` condition can be immediately followed by one or more `else if`, also with parentetical activation conditions, and then an `else` which serves as a catch-all and thus has no explicit conditions. Typical conditions include `===` to check if two values are truly equal, `>=`, `<=`, `>`, and `<` as relative comparators, `!` as negation, and `%`, the modulo operator, which returns what would be the remainder of a division between numbers on either side.

{{< code language="javascript" id="3" expand="Show" collapse="Hide" isCollapsed="false" >}}

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

# Loops, Increments, and Decrements

Two basic types of repeating code blocks are `while` loops and `for` loops. A `while` loop continues until a parenthetical condition is false, and may not even run once (`do...while` loops guarantee at least one cycle). Meanwhile, `for` loops have three parts in their parenthetical, an initialized variable, a check condition that stops the loop if met, and a step condition that is expected to modify the initalized variable at the end of each loop. Behavior inside each loop is contained in `{}`, just like with functions. The operators `++` and `--` increase or decrease a number by 1, respectively.

{{< code language="javascript" id="3" expand="Show" collapse="Hide" isCollapsed="false" >}}

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

{{< /code >}}

# Objects and Logging

Data structures called objects are useful for manipulating data. Regular objects are made to store data pairs: keys that point to values. Special objects called maps and arrays also use keys and values. Maps keys can be any data type, including another object, while regular object keys can only be strings and array keys can only be numbers called indexes (array items always have an order). Sets, another type of object, contain unique values without keys.

Dot notation is used to invoke methods, which are similar to functions--`set.add` with one parenthetical argument adds a value, and `map.set` with two arguments adds a key with its value, while `set.has` tests for presence of a parenthetical value, and `map.has` tests for presence of a parenthetical key. Meanwhile, `map.get` can take a key and return a value. Regular objects use dot or bracket notation to set and look up keys and values, while arrays use bracket notation only. Meanwhile, a special command, `console.log` prints data in parenthetical arguments to a nearby terminal (aka command line interface), which is usually what is used to run your program.

{{< code language="javascript" id="4" expand="Show" collapse="Hide" isCollapsed="false" >}}

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

# Methods of Arrays

Arrays have `length`, can `push` new values to the lowest empty index, `pop` to unfill the greatest filled index and return the popped value, `shift` the value at 0 index off and return the shift value (meaning every other value in the array is downshifted one index), or `unshift` to move in a new 0 index value and rotate every other value up one index.

Arrays can also transform or `map` their values into a new array, `sort` their values in place, and `slice` to return only the content between certain indexes. Arrays can also test `every` value for some condition and return a boolean (`true` or `false`) depending on if every test is passed.

{{< code language="javascript" id="5" expand="Show" collapse="Hide" isCollapsed="false" >}}

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

const array3 = array.slice(1)
console.log(array3) // copy starts at index 1 of the former array and prints [4, 10]

const array4 = array.slice(1, 2)
console.log(array4) // copy starts at index 1 and ends just below index 3 of former array, printing [4]

const allArrayValuesGreaterThanOneBoolean = array.every((value) => value > 1)
console.log(allArrayValuesGreaterThanOneBoolean) // prints true

{{< /code >}}

# Methods of Strings

Strings also have `length`, and can return new strings `toLowerCase`, `toUpperCase`, or after running `replace` of their own values. Strings can also find `charCodeAt` at an index (leftmost location is the zero index, and character codes are numbers for representing unique characters, like 'a', or '.'.

{{< code language="javascript" id="6" expand="Show" collapse="Hide" isCollapsed="false" >}}

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

const thirdReplace = string.replace(/[^0-9a-za-z]/g,'')
console.log(thirdReplace) // this regex would replace any non-alphanumeric characters with an empty string (note the ^), but since there are none, "Hello" is printed

const lowerCaseACharCode = firstReplace.charCodeAt(2)
console.log(lowerCaseACharCode) // returns number 97 (for 'a')

console.log(string) // prints "Hello" as the original string is unmodified

{{< /code >}}
