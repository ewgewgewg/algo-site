+++
title = "JS Algos"
date = "2022-11-26T15:36:56-05:00"
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

A review of algorithm implementation in JavaScript. Adapted from the [Tech Interview Handbook](https://www.techinterviewhandbook.org/grind75?hours=19) and the [LeetCode](https://www.leetcode.com) website. If you are a JavaScript beginner, read [JS Language](/posts/js-language) first.

# Arrays

To efficiently find indexes of two array values that sum to a target, create a map, then iterate linearly. At every index, if `target - value` is a map key, return the current index, and the index under the map key. Otherwise, add `value` as a key to the map, pointing to the current index. Because of action order at each step, the map always contains all previous values for direct lookup.

{{< code language="javascript" title="[Two Sum](https://leetcode.com/problems/two-sum/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof nums === "object" (array of numbers)
// typeof target === "number"

const twoSum = (nums, target) => {

    const map = new Map()

    for (let i = 0; i < nums.length; i++){
        if(map.has(target-nums[i])){
            return [i, map.get(target-nums[i])]
        }

        map.set(nums[i],i)
    }

}{{< /code >}}

To find 0 or the greatest positive difference between a number in an array and a number that appears earlier in the array, which can be analogized to buying and selling stock, initialize a `low` 'price' to the first number in the array, and the `best` difference to 0. Then loop through the array. At every step, replace `best` if appropriate with the `price` at the current index minus `low`, then update `low` if the current `price` is lower.

{{< code language="javascript" title="[Best Time to Buy and Sell Stock](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof prices === "object" (array of numbers)

const maxProfit = (prices) => {

    let low = prices[0]
    let best = 0

    for (let price of prices){
        best = Math.max(best, price-low)
        low = Math.min(low, price)
    }

    return best

}{{< /code >}}

The [Boyer-Moore majority vote algorithm](https://en.wikipedia.org/wiki/Boyer%E2%80%93Moore_majority_vote_algorithm) finds a majority element in an array in linear time when a majority element is guaranteed. After initializing `count` as 1 and `val` as the value at the 0 index, the rest of the array is looped. If the value at the new index is the same as `val`, increase `count` by 1, otherwise decrease `count` by one. If `count` reaches 0, increment the index by 1, set `val` to the value at that index, and set `count` to 1. After as many pairs of different values are removed as possible, the `val` remanining is the majority element. 

{{< code language="javascript" title="[Majority Element](https://leetcode.com/problems/majority-element/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof nums === "object" (array of numbers)

const majorityElement = (nums) => {

    let count = 1
    let val = nums[0]

    for (let i = 1; i < nums.length; i++){
        if(nums[i] === val){
            count++
        } else {
            count--
        }

        if(!count){
            i++
            val = nums[i]
            count = 1
        }
    }

    return val

}{{< /code >}}

To see if a duplicate is in an array in linear time and linear space, loop the array, and at each step check to see if the value being processed is in a set. If it is, return `true`, else add the number to the set. Return `false` if no pairs are found in the array. Alternatively, sorting in n log(n) time allows an O(1) space solution by stepping through every adjacent pair in the sorted array and returning `true` if any pair matches.

{{< code language="javascript" title="[Contains Duplicate -- O(n) time, O(n) space](https://leetcode.com/problems/contains-duplicate/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof nums === "object" (array of numbers)

const containsDuplicate = (nums) => {

    const seen = new Set()

    for (let num of nums){
        if (seen.has(num)) return true
        seen.add(num)
    }

    return false

}{{< /code >}}

{{< code language="javascript" title="[Contains Duplicate -- O(n log(n)) time, O(1) space](https://leetcode.com/problems/contains-duplicate/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof nums === "object" (array of numbers)

const containsDuplicate = (nums) => {

    nums.sort((a,b)=>a-b)

    for (let i = 1; i < nums.length; i++){
        if (nums[i] === nums[i-1]) return true
    }

    return false

}{{< /code >}}

An array of objects which each contain `start` and `end` times for meetings can be sorted by starting times to check for overlapping times. After the sort by starting times, check if any ending time is less than an immediately previous starting time. This returns `false`. Else return `true`. It is not necessary to sort secondarily by ending times because if any indentical starting times are found, `false` will naturally be returned.

{{< code language="javascript" title="[Meeting Rooms](https://leetcode.com/problems/meeting-rooms/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof intervals === "object" (array of objects with start and end numbers)

const canAttendMeetings = (intervals) => 

    intervals.sort((a,b)=>a.start-b.start)

    for (let i = 1; i < intervals.length; i++){
        if (intervals[i].start < intervals[i-1].end) return false
    }

    return true

}{{< /code >}}

To move all zeros to the end of an array while preserving the order of nonzero elements, create a `nextNonzeroLocation` and assign the 0 index of the array, then iterate through the array with a different pointer. When this second pointer finds a nonzero, swap the value with the value at the `nextNonzeroLocation` and increment `nextNonzeroLocation` by 1. This will ensure that only 0 numbers will be 'behind' the leading pointer, so steadily adding nonzeroes to the `nextNonzeroLocation` will never displace a value that needs its relative order preserved.

{{< code language="javascript" title="[Move Zeroes](https://leetcode.com/problems/move-zeroes/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof nums === "object" (array of numbers)

const moveZeroes = (nums) => {

        let nextNonZeroLocation = 0

		for(let i=0; i < nums.length; i++){
			if(nums[i] != 0) {
				[nums[i], nums[nextNonZeroLocation]] = [nums[nextNonZeroLocation], nums[i]]
                nextNonZeroLocation++
			}
		}

        return nums

}{{< /code >}}

How to square the numbers in a non-decreasing array but keep them in sorted order? A linear time solution (as opposed to squaring each and sorting the whole list) is possible. An implementation with three pointers is relatively simple: the `low` pointer starts at the lowest index of the array, the `high` pointer starts at the highest, and an iterator `i` moves a loop from the last index of the base array to the first. At every step choose the larger in absolute value of the value at `low` and the `value` at high, then square it, place it at the `i` position, and increment `low` or decrement `high` depending on which pointer was chosen.

{{< code language="javascript" title="[Squares of a Sorted Array](https://leetcode.com/problems/squares-of-a-sorted-array/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof nums === "object" (array of numbers)

const sortedSquares = (nums) => {

	const result = []

	let low = 0
    let high = nums.length - 1

	for (let i = nums.length - 1; i > -1; i--) {
		if (Math.abs(nums[low]) >= Math.abs(nums[high])) {
			result[i] = nums[low] ** 2
			low++
		} else {
			result[i] = nums[high] ** 2
			high--
		}
	}

	return result

}{{< /code >}}

In an array of intervals that do not overlap, with the intervals themselves represented as arrays of [start, end] numbers, and sorted by start, how to add a new [start, end] by updating, merging, or inserting as appropriate? One method is to increment from the beginning of the array until the end is reached or the end value of the current array interval stops being less than the start value of the interval to be inserted. Then, again while there are array items to iterate, the interval to be inserted has its start and end values updated to be more mimumum and more maximum as appropriate, until the start of the array interval to be processed is larger than the end of the (modified or unmodifed) interval to be inserted.

{{< code language="javascript" title="[Insert Interval](https://leetcode.com/problems/insert-interval/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof intervals === "object" (array of arrays with two numbers)
// typeof newInterval === "object" (array with two numbers)

const insert = (intervals, newInterval) => {

    let i = 0

    while (i < intervals.length && intervals[i][1] < newInterval[0]) {
        i++
    }

    const sliceMark = i

    while (i < intervals.length && newInterval[1] >= intervals[i][0]) {
        newInterval[0] = Math.min(intervals[i][0], newInterval[0])
        newInterval[1] = Math.max(intervals[i][1], newInterval[1])
        i++
    }

    return intervals.slice(0, sliceMark).concat([newInterval]).concat(intervals.slice(i))

}{{< /code >}}

To find all unique triplets of numbers in an array that sum to 0, you can first sort the array in ascending order. A loop with three pointers can then help the problem. The `i` pointer in the definition of the loop will crawl from the 0 index to two less than the last index, while for every step in the loop, `left` and `right` pointers can be defined starting at the leftmost and rightmost extremes to the right of the `i` pointer. When the value at `i` is equal to the value at `i-1`, the step in the loop can be skipped (to guard against duplicate triplets), and if the value at `i` is ever positive, the loop can break (because the values at the three pointers will be positive). Otherwise, as long as `left < right`, check to see if the sum of the values at `i`, `left`, and `right` are 0. If the value is too low, increment `left` by 1, and if too high decrement `right` by 1. If the sum is 0, push the value, and increment past any duplicate values innerward from `left` and `right` so both pointers point to new values. Whether or not the sum is 0, check again if `left < right` to continue the inner loop.

{{< code language="javascript" title="[3Sum](https://leetcode.com/problems/3sum/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof nums === "object" (array of numbers)

const threeSum = (nums) => {

    nums.sort((a,b)=>a-b)

    const result = []
    for (let i = 0; i < nums.length - 2; i++){
        if (nums[i] > 0) break
        if (i > 0 && nums[i] === nums[i-1]) continue
        let left = i + 1
        let right = nums.length - 1

        while(left < right){
            const sum = nums[i] + nums[left] + nums[right]
            if (sum < 0) left += 1
            else if (sum > 0) right -= 1
            else {
                result.push([nums[i], nums[left], nums[right]])
                while(left < right && nums[left] === nums[left+1]) left++
                while(left < right && nums[right] === nums[right-1]) right--
                left++
                right--
            }
        }
    }
    
    return result

}{{< /code >}}

To find the product at each array location of every value in the array except the value at that location, a linear time approach with two passes involves computing the rolling product forwards of every value up to that at a given index, and then rolling over the same array backwards, multiplying the value given in the first pass by a `backwardsRollingProduct` of every value above the given index. Make sure to update the `backwardsRollingProduct` only by multiplying from the original array (though it should be used at every step to update the target value in the final array).

{{< code language="javascript" title="[Product of Array Except Self](https://leetcode.com/problems/product-of-array-except-self/)" expand="Show" collapse="Hide" isCollapsed="false" >}}

const productExceptSelf = (nums) => {

    const result = []
    result[0] = 1

    for (let i = 1; i < nums.length; i++) {
        result[i] = nums[i - 1] * result[i - 1]
    }

    let backwardsRollingProduct = 1
    for (let i = nums.length - 1; i > -1; i--) {
        result[i] *= backwardsRollingProduct
        backwardsRollingProduct *= nums[i]
    }

    return result

}{{< /code >}}

From an array of integers, how to return all unique combinations to a given sum? After creating a `results` array, this problem can be solved in a exhaustive recursive manner. You can use helper function `go` with parameters of remaining array items (starting with all of them), a remaining `target` (starting with the original target), and an array with a possible valid combination summing to the `target` (starting as an empty array). Inside the helper function, if `target` is negative, return immediately, and if `target` is 0, push the third parameter's combination into the `results` array before returning. Else, loop the candidates in the first parameter, running a new `go` call at each step with the first parameter slicing the candidates at the index of the loop, the second parameter reducing `target` by the value at the index in the loop, and third parameter adding the current value to the possible valid combination. This method can use a given number as many times as is helpful.

{{< code language="javascript" title="[Combination Sum](https://leetcode.com/problems/combination-sum/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof candidates === "object" (array of numbers)
// typeof target === "number"

combinationSum = (candidates, target) => {
    
    const results = []

    const go = (remainingCandidates, remainingTarget, possibleCombination) => {
        if (remainingTarget < 0) return
        if (remainingTarget === 0){
            results.push(possibleCombination)
            return
        }
        for(let i = 0; i < remainingCandidates.length; i++){
            go(remainingCandidates.slice(i),remainingTarget-remainingCandidates[i],[...possibleCombination,remainingCandidates[i]])
        }
    }
    
    go(candidates,target,[])
    
    return results

}{{< /code >}}

How to merge intervals with two numbers like `[start, end]` in a larger array of such pairs? If the array has a length, you can sort the array by each `start`. Next, you can seed a `result` array with the first item in the original array. A loop through the remaining items in the original array has two cases. If the end of the last item in the `result` array is greater or equal to the start of the item being processed in the loop, replace the end of the last item in the `result` array by the greater of itself or the end of the item being processed in the loop. If there is no overlap, just push the interval being processed by the loop.

{{< code language="javascript" title="[Merge Intervals](https://leetcode.com/problems/merge-intervals/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof intervals === "object" (array of arrays with two numbers)
const merge = (intervals) => {

    if(!intervals.length) return []
    intervals.sort((a,b)=> a[0] - b[0])
    const result = [intervals[0]]

    for(let i = 1; i < intervals.length; i++){
        if(result[result.length-1][1] >= intervals[i][0]){
            result[result.length-1][1] = Math.max(intervals[i][1],result[result.length-1][1])
        } else {
            result.push(intervals[i])
        } 
    }

    return result

}{{< /code >}}

# Stacks

To see if a string containing only `(){}[]` characters closes validly, create a dictionary where closing brackets point to opening brackets, and instantiate a stack. Then, iterate over the string, pushing opening brackets to the stack and popping the stack if a closing bracket is found which can close the stack's top item. If a closing bracket is found that does not close the stack's top item, or the stack is empty when a closing bracket is found, or the stack still has length when the string is fully iterated, return `false` -- otherwise `true` should be returned.

{{< code language="javascript" title="[Valid Parentheses](https://leetcode.com/problems/valid-parentheses/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof s === "string"

const isValid = (s) => {

    const dictionary = {')':'(',']':'[','}':'{'}
    const stack = []

    for (let c of s){
        if(dictionary[c]){
            if(!stack.length ||
            stack[stack.length-1] !== dictionary[c]
            ){
                return false
            }
            stack.pop()
        } else {
            stack.push(c)
        }
    }

    return !stack.length

}{{< /code >}}

In JavaScript, a stack can be modeled by an array that only adds and removes values from one end, while a queue can be modeled by an array that only adds values on one end and removes them on the other end. To create `push`, `pop`, `peek`, and `empty` methods for a constructed queue, create a queue function and within, assign two arrays that will be treated as stacks under the `this` keywords. Implement the `push` method by adding a `push` method to the prototype of the `queue` function, which pushes to the first stack. Implement the `empty` method similarly by protoype, and by checking if both stacks are empty. The `peek` method, meanwhile, helps with `pop` -- in `peek`, check if the second stack is empty. If so, pop from the first stack (where new values are added) and push to the second stack until the first stack is empty. This naturally brings the first value added to the first stack to the top of the second stack. The `pop` method can simply pop the top of the second stack after a `peek`.

{{< code language="javascript" title="[Implement Queue Using Stacks](https://leetcode.com/problems/implement-queue-using-stacks/)" expand="Show" collapse="Hide" isCollapsed="false" >}}

const MyQueue = function() {

    this.stack1 = []
    this.stack2 = []

};

MyQueue.prototype.push = function(x) {

    this.stack1.push(x)

};

MyQueue.prototype.pop = function() {

    this.peek()
    return this.stack2.pop()

}

MyQueue.prototype.peek = function() {

    if(!this.stack2.length){
        while(this.stack1.length){
            const test = this.stack1.pop()
            this.stack2.push(test)
        }
    }
    return this.stack2[this.stack2.length-1]

}

MyQueue.prototype.empty = function() {

    return !this.stack1.length && !this.stack2.length

}{{< /code >}}

In two strings of lowercase letters and #, where # represents a backspace, are the strings equal once backspaces are considered? A solution involves parsing from the end of both strings, towards the front. At each step in the reverse traversal, a string that has not sucessfully reached its forward end checks for the presence of `#` at the location, and a `backspace` counter started at 0. If `#` is present, the `backspace` counter increments by 1, otherwise it decrements by 1, and in either case, the string pointer moves one step towards the front of the string. While the string has not reached its forward end, and the `backspace` counter is at least 1, or the string pointer is pointed at a `#`, the subloop continues. Once the subloop is completed for both strings, any value at the string indexes should match. If they do, both indices can decrement, otherwise the algorithm returns `false`. If the parent loop completes, return `true`. 

{{< code language="javascript" title="[Backspace String Compare](https://leetcode.com/problems/backspace-string-compare/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof s === "string"
// typeof t === "string"

const backspaceCompare = (s, t) => {

    let sIndex = s.length-1
    let tIndex = t.length-1

    while(sIndex > -1 || tIndex > -1){
        sIndex = backwards(s, sIndex)
        tIndex = backwards(t, tIndex)
        
        if(s[sIndex] !== t[tIndex]){
            return false
        } else {
            sIndex--
            tIndex--
        }
    }

    return true

};

const backwards = (string, index) => {

    let backspace = 0
    while(index > -1 && (string[index] === '#' || backspace > 0)){
        backspace += (string[index] === '#' ? 1 : -1)
        index--
    }
    return index

}

}{{< /code >}}

[Reverse Polish Notation](https://en.wikipedia.org/wiki/Reverse_Polish_notation) involves  pushing numerical values to a stack, and otherwise running a detected operator on the two values found behind it in the stack. When values are used they can be popped, and the new value after operation is pushed to the stack as if it was in the original token series (this is a way the notation represents parentheticals).

{{< code language="javascript" title="[Evaluate Reverse Polish Notation](https://leetcode.com/problems/evaluate-reverse-polish-notation/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof tokens === "object" (array of strings)

const evalRPN = (tokens) => {

    const stack = []

    for (let token of tokens){
        if(!'+-*/'.includes(token)) stack.push(Number(token))
        else {
            const b = stack.pop()
            const a = stack.pop()

            if(token === '+'){
                stack.push(a + b)
            } else if (token === '-'){
                stack.push(a - b)
            } else if (token === '*'){
                stack.push(a*b)
            } else {
                const test = a/b
                if(test < 0) stack.push(Math.ceil(test))
                else stack.push(Math.floor(test))
            }
        }
    }

    return stack[0]

}{{< /code >}}

To create a stack that can push, pop, find the top value, and find the minimum value, all in constant time, you can seed two empty arrays in the starter function, `values` and `minimums`. To push values, you can always push a new value to the `values` array, but only push to the `minimums` array if that array is empty or the value is less than or equal to the previous minimum. To pop, you can always pop from the `values` array but only pop from the `minimums` array if the last value there matches the value popped from `values`. Top just checks the end of the `values` array, while getting the minimum just checks the end of the `minimums` array.

{{< code language="javascript" title="[Min Stack](https://leetcode.com/problems/min-stack/description/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof val === "number"

const MinStack = function() {
    this.values = []
    this.minimums = []
}

MinStack.prototype.push = function(val) {
    if(!this.minimums.length || val <= this.minimums[this.minimums.length-1]){
        this.minimums.push(val)
    }
    this.values.push(val)
}

MinStack.prototype.pop = function() {
    const test = this.values.pop()
    if(test === this.minimums[this.minimums.length-1]){
        this.minimums.pop()
    }
    return test
}

MinStack.prototype.top = function() {
    return this.values[this.values.length-1]
}

MinStack.prototype.getMin = function() {
    return this.minimums[this.minimums.length-1]
}{{< /code >}}

# Linked Lists

LeetCode implements a singly-linked list like this:

{{< code language="javascript" expand="Show" collapse="Hide" isCollapsed="false" >}}

function ListNode(val, next) {

    this.val = (val===undefined ? 0 : val)
    this.next = (next===undefined ? null : next)

}
{{< /code >}}

...or sometimes this:

{{< code language="javascript" expand="Show" collapse="Hide" isCollapsed="false" >}}

function ListNode(val) {

    this.val = val
    this.next = null

}
{{< /code >}}

To merge two linked lists, declare an empty `link` node, and create an extra pointer to this node. While neither list is empty, select the minimum value from the heads of either list, append this to the pointer, then redefine both the pointer and the head of the list with that minimum value their respective `.next` values. Once one list is empty, append whichever list was not emptied to the pointer, and use `link.next` as the return value.

{{< code language="javascript" title="[Merge Two Sorted Lists](https://leetcode.com/problems/merge-two-sorted-lists/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof list1 === "object" (linked list)
// typeof list2 === "object" (linked list)

const mergeTwoLists = (list1, list2) => {

    const link = new ListNode()
    let pointer = link

    while(list1 && list2){
        if(list1.val < list2.val){
            pointer.next = list1
            list1 = list1.next
        } else {
            pointer.next = list2
            list2 = list2.next
        }
        pointer = pointer.next
    }

    if(list1){
        pointer.next = list1
    } else {
        pointer.next = list2
    }

    return link.next

}
{{< /code >}}

Cycles in linked lists can be detected by setting a `slow` pointer to the `.next` of the head, if available, and setting a `fast` pointer to the `.next.next` of the head. At each step in a while loop attempt to advance the `slow` pointer by 1 step and the `fast` pointer by 2. Then check for equality of the pointers. If the pointers point to exactly the same reference, they will return `true`, which signals a cycle, and the `fast` pointer overtaking the `slow`. If the `fast` pointer reaches an empty reference before this happens, don't be in the loop and return `false`.

{{< code language="javascript" title="[Linked List Cycle](https://leetcode.com/problems/linked-list-cycle/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof head === "object" (linked list)

const hasCycle = (head) => {

    let slow = head?.next
    let fast = head?.next?.next

    while(fast){
        if (slow === fast) return true
        slow = slow?.next
        fast = fast?.next?.next
    }

    return false

}{{< /code >}}

{{< anchor "Reverse Linked List" >}}To reverse a linked list, create a null `link`, then perform the following steps while the original linked list still has a filled `head` node:

    1) Save the `head` of the original linked list as `next`.
    2) Assign `link` to `head.next` (`link` builds the leading part of the original linked list in reversed order).
    3) Assign `head` to `link` (head now points through `.next` to the original `link`).
    4) Assign the saved `next` to `head`.

{{< code language="javascript" title="[Reverse Linked List](https://leetcode.com/problems/reverse-linked-list/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof head === "object" (linked list)

const reverseList = (head) => {

    let link = null

    while(head){
        const next = head.next
        head.next = link
        link = head
        head = next
    }

    return link

}{{< /code >}}

{{< anchor "Middle of the Linked List" >}}To find the middle node in a linked list (second of the two if the number of nodes is even), create `fast` and `slow` pointers and start them at `head`. While there is a `fast.next`, assign `slow` to the next node and `next` two nodes forward along the list. Once this loop resolves `slow` should point to the middle node by the problem definition.

{{< code language="javascript" title="[Middle of the Linked List](https://leetcode.com/problems/middle-of-the-linked-list/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof head === "object" (linked list)

const middleNode = (head) => {
    
   let slow = head
   let fast = head

   while(fast?.next){
         slow = slow.next
         fast = fast.next?.next
   }

   return slow

}{{< /code >}}

To find if a linked list contains a palindrome of values, it is necessary to compare values from either end of the list, stepping towards the middle. The head of the list is readily available, but to get to the other end effectively, a traversal is needed. Perform a traversal similar to [Middle of the Linked List](/posts/js-algos#middle-of-the-linked-list), just above, which should put a slow pointer at approximately the middle node. The suffix can the be reversed in a pattern similar to [Reverse Linked List](/posts/js-algos#reverse-linked-list). Finally, both the original head and the suffix can be stepped through together to see if each pair of numbers matches. Make sure your implementation accounts for the possibility of an odd number of nodes and does not check the middle node against anything.

{{< code language="javascript" title="[Palindrome Linked List](https://leetcode.com/problems/palindrome-linked-list/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof head === "object" (linked list)

const isPalindrome = (head) => {  
  if (!head.next) return true
  
  let slow = head
  let fast = head
  
  while (fast) {
    slow = slow.next
    fast = fast.next?.next
  }
  
  slow = reverse(slow)
  
  while (slow) {
    if (slow.val !== head.val) return false
    slow = slow.next
    head = head.next
  }
  
  return true
};

const reverse = (head) => {
  let link = null
  
  while (head) {
    const next = head.next
    head.next = link
    link = head
    head = next
  }
  
  return link
}

{{< /code >}}

# Strings

To detect if a string is a palindrome, sanitize the string as appropriate (for example, removing spaces and standardizing capitalization), then initialize string `start` and `end` pointers. While `start` is at a lower index than `end` check to see if the values at each location match, then move each pointer one index closer to the center. Any mismatch allows the function to immediately return `false` while otherwise `true` should be the return.

{{< code language="javascript" title="[Valid Palindrome](https://leetcode.com/problems/valid-palindrome/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof s === "string"

const isPalindrome = (s) => {

    let start = 0
    s = s.replace(/[^0-9a-zA-Z]/g,'').toLowerCase()
    let end = s.length - 1

    while(start < end){
        if(s[start] !== s[end]) return false
        start++
        end--
    }

    return true

}{{< /code >}}

Two strings are valid anagrams of each other if they have exactly the same letter count. A test involves creating an array with zeroed indices for every letter. Loop over one string, incrementing at the appropriate array index as letters are read. Then loop over the second index decrementing at the appropriate array index as letters are read. If the strings are anagrams, the resulting array should be filled with zeroes.

{{< code language="javascript" title="[Valid Anagram](https://leetcode.com/problems/valid-anagram/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof s === "string"
// typeof t === "string"

const isAnagram = (s, t) => {

    const arr = new Array(26).fill(0)

    for (let i = 0; i < s.length; i++){
        arr[s.charCodeAt(i)-97]++
    }

    for (let i = 0; i < t.length; i++){
        arr[t.charCodeAt(i)-97]--
    }

    return arr.every(a=>!a)

}{{< /code >}}

The longest palindrome from a case-sensitive string of letters is equivalent to the number of matching pairs plus one, or the length of the string, whichever is shorter. In other versions of the problem, the string may need to be filtered or transformed.

{{< code language="javascript" title="[Longest Palindrome](https://leetcode.com/problems/longest-palindrome/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof s === "string"

const longestPalindrome = (s) => {

    const upper = new Array(26).fill(0)
    const lower = new Array(26).fill(0)

    for(let i = 0; i < s.length; i++){
        if(s.charCodeAt(i) < 97){
            upper[s.charCodeAt(i)-65]++
        } else {
            lower[s.charCodeAt(i)-97]++
        }
    }

    let pairs = 0
    for (let u of upper){
        pairs += Math.floor(u/2)
    }
    for (let l of lower){
        pairs += Math.floor(l/2)
    }

    return Math.min(pairs*2+1, s.length)

}{{< /code >}}

To find the longest common prefix in an array of strings, any string can be chosen as the comparator, since the longest common prefix cannot be longer than any arbitrarily chosen string. Cycle through character locations from left to right for every string in the array, starting with the leftmost character in each string. Every time the comparator at a given index matches the character in every other string at that same index, add that character to the `prefix` result. As soon as there is a mismatch, or once the double loop ends with no mismatches, return `prefix`.

{{< code language="javascript" title="[Longest Common Prefix](https://leetcode.com/problems/longest-common-prefix/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof strs === "array of strings"

const longestCommonPrefix = (strs) => {

  let prefix = []

  for (let i = 0; i < strs[0].length; i++) {
    for (let j = 1; j < strs.length; j++) {
      if (strs[0][i] !== strs[j][i]) return prefix.join("")
    }

    prefix.push(strs[0][i])
  }

  return prefix.join("")

}{{< /code >}}

To find the longest substring without repeating characters, you can use two pointers, a leading pointer and a `backPointer`, both of which can start at 0. Advance the leading pointer as in a loop, and at each location in the string add the character code of the character to an object of all character codes in the visited string. Then, while a duplicate character code is detected, subtract the character pointed to by the `backPointer` from the table, and increment the back pointer. Once the substring between the leading pointer and `backPointer` has no repeated characters, use its length to replace a `best` string length as needed.

{{< code language="javascript" title="[Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
//typeof s === "string"

const lengthOfLongestSubstring = (s) => {

    const charCount = new Array(127).fill(0)

    let best = 0
    let backPointer = 0

    for(let i = 0; i < s.length; i++){
        charCount[s.charCodeAt(i)]++

        while(charCount[s.charCodeAt(i)] === 2){
            charCount[s.charCodeAt(backPointer)]--
            backPointer++
        }

        best = Math.max(best,i+1-backPointer)
    }

    return best

}{{< /code >}}

# Binary Trees

One of the ways LeetCode has implemented a binary tree node is like this:

{{< code language="javascript" expand="Show" collapse="Hide" isCollapsed="false" >}}

function TreeNode(val, left, right) {

     this.val = (val===undefined ? 0 : val)

     this.left = (left===undefined ? null : left)
     this.right = (right===undefined ? null : right)

}{{< /code >}}

Inverting a binary tree means to 'turn it over' as if it was a page in a book, creating what the original would look like from behind. This means swapping every pair of `left` and `right` references. A recrusive implementation step returns immediately on a `null` node, and otherwise returns after using a holding variable for one of the `left` or `right` references to help complete the second half of the swap.

{{< code language="javascript" title="[Invert Binary Tree](https://leetcode.com/problems/invert-binary-tree/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof root === "object" (binary tree node)

const invertTree = (root) => {

    if(!root) return root
    const hold = root.right
    root.right = root.left
    root.left = hold

    invertTree(root.left)
    invertTree(root.right)

    return root

}{{< /code >}}

A height-balanced binary tree differs in depth by no more than one at each node. To test if a binary tree is height-balanced, do depth-first search from the root node, returning 0 on each base case of a `null` root, and otherwise check if the differences in return height values from the left or right nodes is not greater than 1. Because each hopeful step of the depth-first search then returns the maximum of the right or left depth plus 1, the full depth under any node will always bubble up. If a difference greater than one bubbles up, -1 can be returned preferentially as a signal for a non-height balanced tree.

{{< code language="javascript" title="[Balanced Binary Tree](https://leetcode.com/problems/balanced-binary-tree/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof root === "object" (binary tree node)

const isBalanced = (root) => {

    const height = (root) => {
        if(!root) return 0
        const left = height(root.left)
        const right = height(root.right)

        if(left === -1 || right === -1 || Math.abs(left - right) > 1) return -1

        return 1 + Math.max(left,right)
    }

    return height(root) !== -1

}{{< /code >}}

To find the diameter of a binary tree, or the maximum distance between any two nodes, recursion and depth-first search lead to a solution. At every node, find the maximum distance under the right and left nodes, then update the max if the sum of these is greater than the max. Return the greater of the left or right plus 1 (the plus 1 accounts for the edge between the returning node and the next node up). The base case, reached when recursion decends to find no node, returns 0.

{{< code language="javascript" title="[Diameter of Binary Tree](https://leetcode.com/problems/diameter-of-binary-tree/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof root === "object" (binary tree node)

const diameterOfBinaryTree = (root) => {

    let max = 0
    
    const go = (loc) => {
        if(!loc) return 0

        let left = go(loc.left)
        let right = go(loc.right)

        max = Math.max(max,left+right)

        return 1+Math.max(left,right)
    }
    
    go(root)
    return max

}{{< /code >}}

To find the maximum depth of a binary tree, counted in number of nodes, you can use recursion with a base case of returning 0 on an empty node. On every other node, the greater of the depth of the left side of the tree plus 1 or the depth of the right side of the tree plus 1 should be returned.

{{< code language="javascript" title="[Maximum Depth of Binary Tree](https://leetcode.com/problems/maximum-depth-of-binary-tree/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof root === "object" (binary tree node)

const maxDepth = (root) => {

        if (!root) return 0

        return 1 + Math.max(maxDepth(root.left), maxDepth(root.right))
    
}{{< /code >}}

Two binary trees have the same structure and values if at every node, their values match, and their left and right subtrees match. This lends nicely to a recursive structure which can return `true` on matching empty nodes and bubble a `true` all the way up to the final return only if everything matches.

{{< code language="javascript" title="[Same Tree](https://leetcode.com/problems/same-tree/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof p === "object" (binary tree node)
// typeof q === "object" (binary tree node)

const isSameTree = (p, q) => {
    
        if(!p && !q) return true
        if(p && !q || q && !p || q.val !== p.val) return false

        return isSameTree(p.left,q.left) && isSameTree(p.right,q.right)

}{{< /code >}}

To detect if a binary tree is symmetric, first note that an empty tree is symmetric. Next, generate two pointers, one to the `.left` node and the other to the `.right` node of the head, and put these in a recursive call. These `.left` and `.right` nodes are in locations that should be symmetric and identical. If both are empty, the base case can bubble up `true`, if they are different by presence/absence or value, the base case can bubble upp `false`, and otherwise, two more recrusive calls can be generated, comparing the outer two nodes that are children of the pair that were checked, and also comparing the inner two nodes. Any `false` in a base case should ultimately return `false` in the parent function, otherwise the function returns `true`.

{{< code language="javascript" title="[Symmetric Tree](https://leetcode.com/problems/symmetric-tree/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof root === "object" (binary tree node)

const isSymmetric = (root) => {

    if (!root) return true

    return equal(root.left,root.right)

}

const equal = (l, r) => {
    if (!l && !r) return true
    if (!l || !r || l.val !== r.val) return false
    return equal(l.right,r.left) && equal(l.left,r.right)
}

{{< /code >}}

To find if a tree `subRoot` is truly a subroot of a tree `root`, one approach is to check if any node in or below `root` decends to a structure that matches `subRoot`. However, this approach is )O(`root`*`subRoot`). Constructing a `subRoot` serialization and then checking if this is a substring of `root` can be done with any string-matching algorithm to be used at the end, including the JavaScript-native `includes` method, which could theoretically have an implementation that is in O(`root` + `subRoot`) time at the cost of a precompute table, like the [Knuth-Morris-Pratt String Matching Algorithm](https://en.wikipedia.org/wiki/Knuth%E2%80%93Morris%E2%80%93Pratt_algorithm).

{{< code language="javascript" title="[Subtree of Another Tree](https://leetcode.com/problems/subtree-of-another-tree/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof root === "object" (binary tree node)
// typeof subRoot === "object" (binary tree node)

const isSubtree = (root, subRoot) => stringBuilder(root).includes(stringBuilder(subRoot))

const stringBuilder = (node) => {
    if(!node) return '.'
    const s = `<${stringBuilder(node.left)} + ${node.val} + ${stringBuilder(node.right)}>`
    return s
}{{< /code >}}

Returning a level order traversal of binary tree nodes (array of array of values for each level) can be done with three variables. Assuming there is a `root`, define `result` as an empty array inside an array, define a `level` array that contains `root`, and define a `nextLevel` array that is empty. The `level` array will be treated as a queue and repeatedly processed starting from the left, with each value pushed into the last inner array in `result`, and with each present `.left` and `.right` node being pushed in that order to the `nextLevel` array. When `level` is empty and `nextLevel` has length, push an empty array into `result` to represent the next level and replace `level` with `nextLevel`.

{{< code language="javascript" title="[Binary Tree Level Order Traversal](https://leetcode.com/problems/binary-tree-level-order-traversal/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof root === "object" (binary tree node)

const levelOrder = (root) => {

    if(!root) return []
    const result = [[]]
    let level = [root]
    let nextLevel = []

    while(level.length){
        const test = level.shift()
        result[result.length-1].push(test.val)

        if(test.left) nextLevel.push(test.left)
        if(test.right) nextLevel.push(test.right)

        if(!level.length && nextLevel.length){
            result.push([])
            level = nextLevel
            nextLevel = []
        }
    }
    return result

}{{< /code >}}

To find the lowest common ancestor of two uniquely-valued binary tree nodes, an implementation involves an inner function `go`. Run this function taking an argument of the `root` of the tree. When `go` runs, if there is no argument, return `0`, else create a `count` variable and add to it the results of running `go` on the `.left` and `.right` of the current node, as well as `1` if the `.val` of the current node if one of the two values being investigated. If `count` is then `2`, set a `result` variable outside of `go` to the current node, and return `0` so that no higher nodes will falsely be recorded as the lowest common ancestor. Otherwise return `count`.

{{< code language="javascript" title="[Lowest Common Ancestor of a Binary Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof root === "object" (binary tree node)
// typeof p === "object" (binary tree node)
// typeof q === "object" (binary tree node)

const lowestCommonAncestor = (root, p, q) => {

    let result
    
    const go = (node) => {
        if(!node) return 0
        let count = 0
        count += go(node.left)
        count += go(node.right)
        count += (node.val === p.val || node.val === q.val)
        if(count === 2){
            result = node
            return 0
        }
        return count
    }
    
    go(root)
    
    return result

}{{< /code >}}

# Binary Search

Sorted information can be investigated in logarithmic (log(n)) time. Binary search is logarithmic. Each step divides searchable space in half--much faster than a linear search.

In a basic kind of binary search on an array, `start` and `end` variables point to the first and last filled indexes of the array. While `end` is not smaller than `start`, define a new `mid` as the midpoint between `start` and `end` (rounding allowed). `Mid` matching a target value allows immediate return of the value. If the target value is less than the mid index value, use `mid-1` as the new `end`, and if the target value is greater, use `mid+1` as the new `start`. Next, test if the new `end` is smaller than `start` to try another search step. If the search ends without finding the target value, returning -1 can indicate this (returning something else is better if negative targets are allowed).

{{< code language="javascript" title="[Binary Search](https://leetcode.com/problems/binary-search/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof nums === "object" (array of numbers)
// typeof target === "number"

const search = (nums, target) => {

    let start = 0
    let end = nums.length - 1

    while(start <= end){
        const mid = Math.floor((end-start)/2) + start
        if (target === nums[mid]) return mid

        if (nums[mid] > target){
            end = mid - 1
        } else {
            start = mid + 1
        }
    }

    return -1

}{{< /code >}}

A version of binary search involves finding the first instance of a change in a series (such as first bad version). The `start` and `end` indices are initialized as before, as is `mid` within a while loop, and since there is a guarantee of a change the loop will end by finding an index. The test condition uses an externally-provided function to see if the mid index has the change -- if so, `end` is set to `mid` (as this is a candidate for the first change), and if this index is the only index remaining, `mid` can be returned. Else the mid index and everything lower cannot be the result, so `start` can be `mid+1` for the next loop.

{{< code language="javascript" title="[First Bad Version](https://leetcode.com/problems/first-bad-version/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof isBadVersion === "function"
// typeof n === "number"

const solution = function(isBadVersion) {

    return function(n) {
        
        let start = 1
        let end = n

        while(true){

            const mid = start+Math.floor((end-start)/2)

            if(isBadVersion(mid)){
                end = mid
                if(start === end) return mid
            } else {
                start = mid + 1
            }

        }
        
    }
}{{< /code >}}

How to find a number in an array that was divided into two segments that were swapped and reattached, but was before that sorted? To solve this in logarithmic time, begin as if the array was sorted normally, and declare `left` and `right` pointers at either end of the array. While `left` is less than or equal to `right`, define a `mid` pointer between the two (round either way consistently). If `mid` is the value being searched for, you can return the index. Otherwise, notice that one of the section above and the section below the `mid` must be in sorted order. To detect which, check if the `left`-most value is less than the value at `mid` -- this means the left side is sorted. Otherwise the right side is sorted. If the left side is sorted, and the searched-for value is less than the value at `mid` but greater or equal to the `left`-most value, the left side is the side to investigate -- update the right pointer to `mid-1`. Otherwise, if the left side is sorted, the left pointer should go to `mid+1` by process of elimination. If instead it is the right side that is sorted, use similar code to check if the the searched-for value is on the sorted or non-sorted side. You can return `-1` if it is never found.

{{< code language="javascript" title="[Search in Rotated Sorted Array](https://leetcode.com/problems/search-in-rotated-sorted-array/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof nums === "object" (array of numbers)
// typeof target === "number"

const search = (nums, target) => {

  let left = 0
  let right = nums.length - 1

  while (left <= right) {
    const mid = left + Math.ceil((right - left) / 2)
    if (nums[mid] === target) return mid

    if (nums[left] < nums[mid]) {
      if (nums[left] <= target && target < nums[mid]) {
        right = mid - 1
      } else {
        left = mid + 1
      }
    } else {
      if (nums[mid] < target && target <= nums[right]) {
        left = mid + 1
      } else {
        right = mid - 1
      }
    }
  }

  return -1

}{{< /code >}}

To create a structure that can store multiple values at a `key` and return a given one based on `timestamp`, set the data structure variable to a `function () {}` that contains `this.map`. Adding `set` to the prototype of the data structure, involves checking if the `key` already exists on the `map`. If not, set the `key` to an empty array. Otherwise, push an object with the `timestamp` and `value` to the `key`. Adding `get` can involve binary search. If the key does not exist, you can return an empty string, else get the `timestamps` array to be searched off of the key, and set `left` and `right` to the edges of the array. While `left` is less than `right`, compute `mid`, and check if the `timestamp` at `mid` is less than the `timestamp` coming from the `set` argument. If so, set `left` to `mid+1`, else `right` can be `mid`. When the pointer cross, check if `timestamp` at `left` is less than or equal to the `timestamp` coming from the `set` argument. If so, return its `value`. Otherwise, do the same for `left-1`, and if a `timestamp` there also does not work, you can return an empty string.

{{< code language="javascript" title="[Time Based Key-Value Store](https://leetcode.com/problems/time-based-key-value-store/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof timestamp === "number"

const TimeMap = function() {
  this.map = new Map()
}

TimeMap.prototype.set = function(key, value, timestamp) {
  if (!this.map.has(key)) this.map.set(key, [])
  this.map.get(key).push({ timestamp, value })
}

TimeMap.prototype.get = function(key, timestamp) {
    if (!this.map.has(key)) return ""
    const timestamps = this.map.get(key)
    let left = 0
    let right = timestamps.length - 1
    
    while (left < right) {
        const mid = left + Math.floor((right - left) / 2)
        if (timestamps[mid].timestamp < timestamp) {
            left = mid + 1
        } else {
            right = mid
        }
    }
    
    if (timestamps[left] && timestamps[left].timestamp <= timestamp) return timestamps[left].value
    if (timestamps[left - 1] && timestamps[left - 1].timestamp <= timestamp) return timestamps[left - 1].value

    return ""
}{{< /code >}}

# Graphs

A two-dimensional array can represent a screen of pixels. To represent the 'flood fill' tool in a paint editor, identify the coordinates at which you wish to begin flood fill. If that location is already the desired color, stop the process. Otherwise, you can begin a depth-first search at that location, replacing its color with the target color, and generating recursive calls up, down, left, and right. In each of these recursive calls, if the coordinates are out of bounds or the color is not the color being overwritten, handle this base case by not recursing further from that point. Otherwise, change the color at the location and generate more recursive calls up, down, left, and right.

{{< code language="javascript" title="[Flood Fill](https://leetcode.com/problems/flood-fill/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof nums === "object" (array of numbers)
// typeof target === "number"

const floodFill = (image, sr, sc, color) => {

    const start = image[sr][sc]
    if (start === color) return image

    const go = (r,c) => {
        if(r < 0 || c < 0 || r === image.length || c === image[0].length || image[r][c] !== start) return
        image[r][c] = color
        go(r+1,c)
        go(r-1,c)
        go(r,c-1)
        go(r,c+1)
    }

    go(sr,sc)
    return image

}{{< /code >}}

How to modify a binary matrix to return the closest 0 for each cell? This is a problem that can be solved with breadth-first search. After creating a `queue`, a triplet with `row`, `column`, and `distance` from a 0 can be pushed to the `queue` for every 0 found in the array (so all distances start as 0). Every other value can be set as Infinity to make it clear which cells have not yet been processed. Then, while the `queue` has a length, items can be shifted off the front, the cells they point to can be updated with a `distance`, and new items can be pushed to the `queue` and processed with distance + 1 and coordinates just up, down, left, and right (but not out of bounds). After shifting, do not further process a queue item if has a non-infinity value at the time it is shifted in.

{{< code language="javascript" title="[01 Matrix](https://leetcode.com/problems/01-matrix/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof mat === "object" (array of arrays of numbers)

const updateMatrix = (mat) => {
        
    const queue = []
    
    for(let row = 0; row < mat.length; row++) {
        for(let column = 0; column < mat[0].length; column++) {

            if(!mat[row][column]){
                queue.push([row, column, 0])
            }
            mat[row][column] = Infinity
        }
    }

    const directions = [[-1, 0], [0, -1], [1, 0], [0, 1]]
    
    while(queue.length) {
        let [row, column, distance] = queue.shift()
        if(mat[row][column] !== Infinity) continue
        
        mat[row][column] = distance
        
        for(let [x, y] of directions) {
            let newRow = x + row;
            let newColumn = y + column;
            
            if(newRow >= 0 && newRow < mat.length && newColumn >= 0 &&
            newColumn < mat[0].length && mat[newRow][newColumn] === Infinity) {
                    queue.push([newRow, newColumn, distance + 1])
            }
        }
    }
    return mat

}{{< /code >}}

To clone a graph of unique values without loops, if the graph exists, you can use recursion with a map. If any value is present in the map you can return whatever is stored there. Otherwise, make a copy of `currentNode`, set it by value in the map, and loop the reference to its children to recursively copy these. 

{{< code language="javascript" title="[Clone Graph](https://leetcode.com/problems/clone-graph/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// function Node(val, neighbors) {
//     this.val = val === undefined ? 0 : val;
//     this.neighbors = neighbors === undefined ? [] : neighbors;
//  }

const cloneGraph = (node) => {
    if (!node) return null
    const seen = new Map()

    const copy = (currentNode) => {
        if(seen.has(currentNode.val)) return seen.get(currentNode.val)
        const copyNode = new Node(currentNode.val, [])

        seen.set(copyNode.val, copyNode)
        for (let neighbor of currentNode.neighbors) {
            copyNode.neighbors.push(copy(neighbor))
        }

        return copyNode

    }

    return copy(node)
    
}{{< /code >}}

To detect if every course can be taken when prerequisites are given in an array of arrays of number pairs that look like `[course, prerequisite]`, an adjacency list can be created that lets all courses under a prerequisite be pointed to from that prerequisite. Then, every course can be investigated to see if any of its descendants (from the perspective of adjacency list lookups) contain its value. This would mean a circular dependency and be the case to return `false`. In the depth-first searches starting from each course, caching previously seen course values prevents duplicate work, and just before any course's descendants are investigated, it makes sense to store a `flag` with that course's number, so that lower calls can check their value against the set of flags to see if returning `false` is needed. On any level of the recursive process, once the lower-level calls are completed, the `flag` should be removed, since it would no longer represent a valid ancestor.

{{< code language="javascript" title="[Course Schedule](https://leetcode.com/problems/course-schedule/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof numCourses === "number"
// typeof prerequisites === "object" (array of arrays with two numbers)

const canFinish(numCourses, prerequisites) {

  const cache = new Set()
  const flag = new Set()
  const adjacencyList = Array(numCourses).fill().map(r => [])
  
  for (let [course, prerequisite] of prerequisites) {
    adjacencyList[prerequisite].push(course)
  }
  
  const dfs = (course) => {
    if (cache.has(course)) return true
    if (flag.has(course)) return false
    
    flag.add(course)
    for (let nextCourse of adjacencyList[course]) {
      if (!dfs(nextCourse)) return false
    }
    flag.delete(course)
    cache.add(course)
    return true
  }

  for (let i = 0; i < numCourses; i++) {
    if (!dfs(i)) return false
  }

  return true
  
}{{< /code >}}

In order to count the number of islands in a grid (unique groups of '1's connected up, down, left, or right to other '1's), an approach involves iterating through all the values of the grid. When a '1' is found, increment a `count` variable, then run a `flood` function to check if the location is in bounds and a '1'. If so, 'sink' the space by assigning a '0', then do depth-first search and run `flood` up, down, left, and right. Every connected '1' should disappear the first time one is found in the looping.

{{< code language="javascript" title="[Number of Islands](https://leetcode.com/problems/number-of-islands/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof grid === "object" (array of arrays of numbers)

const numIslands = (grid) => {

  let count = 0
  
  const flood = (i, j) => {
      if (i < 0 || j < 0 || i === grid.length || 
      j === grid[0].length || grid[i][j] === '0') return
      grid[i][j] = '0'
      const directions = [[0,1],[0,-1],[1,0],[-1,0]]
      for (let [y, x] of directions) flood(i+y,j+x)
  }
  
  for (let i = 0; i < grid.length; i++){
      for (let j = 0; j < grid[0].length; j++){
          if (grid[i][j] === '1') {
              count++
              flood(i,j)
          }
      }
  }
    
    return count

}{{< /code >}}

In a grid where `1` represents a fresh orange, `2` represents a rotten orange, `0` represents an empty space, and every minute every fresh orange next to a rotten orange beomes rotten, how many minutes, if possible, does it take for all oranges to turn rotten? This problem can be solved with breadth-first search. First, iterate the values in the grid to count the number of oranges into a `oranges` variable. In the same iteration, for all rotten oranges, push arrays of 3 numbers each into a `queue`-- the orange's coordinates, and and a `minutesToRotten` of 0. Temporarily assign rotten oranges in the grid as `1`s to make `queue` processing easier. For `queue` processing, as long as `queue` has a length, shift off the first item, make sure its coordinates are on the grid and point to a fresh `1` orange, then decrease the `oranges` counter by 1, assign the grid location as `2`, maximize a `depth` variable by comparing it with `minutesToRotten`, and push new `queue` triplets up, down, left, and right if those are the adjacencies allowed by the question, with the new `minutesToRotten` increased `+1` compared to the spawning item. When `queue` has no length, return `depth` if the `oranges` variable is 0, else return `-1` to show that all oranges will never become rotten under the constraints.

{{< code language="javascript" title="[Rotting Oranges](https://leetcode.com/problems/rotting-oranges/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof grid === "object" (array of arrays of numbers)

const orangesRotting = function(grid) {

    let oranges = 0
    let depth = 0
    const queue = []
    for (let i = 0; i < grid.length; i++){
        for (let j = 0; j < grid[0].length; j++){
            if(grid[i][j]) oranges++
            if(grid[i][j] === 2){
                grid[i][j] = 1
                queue.push([i,j,0])
            }
        }
    }

    const dirs = [[-1,0],[1,0],[0,1],[0,-1]]
    while(queue.length){
        const cur = queue.shift()
        if(cur[0] < 0 || cur[1] < 0 || cur[0] === grid.length
          || cur[1] === grid[0].length || grid[cur[0]][cur[1]] !== 1){
            continue
        }
        depth = Math.max(depth,cur[2])
        oranges--
        grid[cur[0]][cur[1]] = 2
        for (let dir of dirs){
            queue.push([cur[0]+dir[0],cur[1]+dir[1],cur[2]+1])
        }
    }
    
    return oranges ? -1 : depth

}{{< /code >}}

Given an array of `account` arrays where the item at each inner array's first index is a `name`, and every other index contains an `email`, how to merge `accounts` with shared emails? Two arrays might be connected by a third if that third shares emails on both other lists, and the chain can get even longer. In order to solve, first build a `nameLookup` and `adjacencyList` by looping through each starting `account`, extracting the `name` from the 0th index, and at each step looping through the emails associated with each starting `account`. Assign a `name` to each `email` in `nameLookup`, and use the `email` at the 1st index to create adjacency relationships -- adding each 2nd and further index email to an `adjacencyList` value array of the 1st index `email` key, and adding the 1st index `email` to the `adjacencyList` array of every other email in the `account`. Reorganization complete, `email` keys in the `adjacencyList` can then be looped. For any `email` that has not been `seen` in this phase of the algorithm, a depth-first search can be run, first adding the `email` to `seen`, then seeding a new `emails` array with the `email` key being processed, then looping items in the array associated with the `email` in the `adjacencyList`. For every next email on this list that has not been `seen`, an additional `dfs` can run, and the results can be pushed to the `emails` array before returning it. When `dfs` is done, the `emails` array returned to the outer `adjacencyList` loop is a `joinedAccount`. Sort as needed, and use `nameLookup` to add a `name` to the front of the array before pushing it to `result`.

{{< code language="javascript" title="[Accounts Merge](https://leetcode.com/problems/accounts-merge/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof accounts === "object" (array of arrays of strings)

const accountsMerge = (accounts) => {  
    
    const nameLookup = {}
    const adjacencyList = {}
    
    for (let account of accounts) {
        const name = account[0]
        for (let i = 1; i < account.length; i++) {
            nameLookup[account[i]] = name
            if (!adjacencyList[account[i]]) adjacencyList[account[i]] = []

            if (i != 1) {
                adjacencyList[account[1]].push(account[i])
                adjacencyList[account[i]].push(account[1])
            }
        }
    }
    
    const result = []
    const seen = new Set()
    
    const dfs = (email) => {
        seen.add(email)
        const emails = [email]
        for (let nextEmail of adjacencyList[email]){
            if(!seen.has(nextEmail)) emails.push(...dfs(nextEmail))
        }        
        return emails
    }
    
    for (let email in adjacencyList) {
        if (!seen.has(email)) {
            const joinedAccount = dfs(email)
            joinedAccount.sort()
            result.push([nameLookup[joinedAccount[0]], ...joinedAccount])
        }
    }
    
    return result
}{{< /code >}}

# Binary Search Trees

Binary search trees are binary trees that have the property that every node bisects the search space -- you can tell which side of a node to go down for further investigation based on its value. In order to find the lowest common ancestor of two nodes, consider that as you decend the tree, as long as the current value is less than the lower value of the two target nodes, or greater than the upper value of the two target nodes, both target nodes will be on the same side of the next step down of the tree. Decend recursively in the direction of both nodes until you no longer can, and return the stopping value.

{{< code language="javascript" title="[Lowest Common Ancestor of a Binary Search Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof root === "object" (binary search tree node)
// typeof p === "object" (binary search tree node)
// typeof q === "object" (binary search tree node)

const lowestCommonAncestor = (root, p, q) => {

    const large = Math.max(p.val,q.val)
    const small = Math.min(p.val,q.val)

    while(root.val > large || root.val < small){
        if(root.val > large) root = root.left
        else if (root.val < small) root = root.right

    }

    return root

}{{< /code >}}

Converting an array of numbers in ascending order into a height-balanced binary search tree can be handled by bisection and recursion. The height of the tree never has to be explictly measured. As long as there is at least one value in an examined range of the array, the central node of the segment of the array can become the head node of the segment of the binary search tree, and all numbers lower in the segment can be the subject of a recursive call to fill the `.left` node, while all numbers higher can fill `.right`. 

{{< code language="javascript" title="[Convert Sorted Array to Binary Search Tree](https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof nums === "object" (array of numbers)

const sortedArrayToBST = (nums) => {
    return recursion(0, nums.length - 1, nums)
}

const recursion = (left, right, nums) => {

    if(left > right) return null

    const node = new TreeNode()
    const mid = Math.floor((left+right)/2)

    node.val = nums[mid]
    node.left = recursion(left, mid - 1, nums)
    node.right = recursion(mid + 1, right, nums)

    return node

}{{< /code >}}

To check if a binary tree is a valid binary search tree, recursion is valuable. Set `left` and `right` default parameters of -Infinity and Infinity on the function, and run it so that an empty node returns `true`, while otherwise two recursive calls are spawned. One takes the `.left` node with a `left` of `left` and a `right` of `root.val`, while the other takes the `.right` node with a `right` of `right` and a `left` of `root.val`. Then return `true` only if the left node and the right node return `true`, and `root.val` is between `left` and `right`. 

{{< code language="javascript" title="[Validate Binary Search Tree](https://leetcode.com/problems/validate-binary-search-tree/description/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
//typeof root === "object" (binary search tree node)

const isValidBST = (root, left = -Infinity, right = Infinity) => {
    if (!root) return true
    return (isValidBST(root.left, left, root.val)
    && isValidBST(root.right,root.val, right)
    && left < root.val && root.val < right)
    
}{{< /code >}}

# Hash Tables

Whether one group of characters in a string is completely included in a different string can be described as being able to cut letters out of a magazine for a ransom note. If the only characters that can appear are lower case letters, then these can be added by character code into an array from the 'magazine' string, and then what appears in the 'note' string can be subtracted. If any count in the array goes negative, the ransom note cannot be completed.  

{{< code language="javascript" title="[Ransom Note](https://leetcode.com/problems/ransom-note/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof ransomNote === "string"
// typeof magazine === "string"

const canConstruct = function(ransomNote, magazine) {

    const arr = new Array(26).fill(0)

    for (let i = 0; i < magazine.length; i++){
        arr[magazine.charCodeAt(i)-97]++
    }

    for (let i = 0; i < ransomNote.length; i++){
        arr[ransomNote.charCodeAt(i)-97]--
        if(arr[ransomNote.charCodeAt(i)-97] < 0) return false
    }

    return true

}{{< /code >}}

# Dynamic Programming

If someone can climb one or two steps at a time, how many ways can they get to the top? Dynamic programming helps recognize that the number of ways to reach any given step acts as a multiplier on the ways to reach steps further up. A linear solution involves noticing the one way of getting to the '0th' step, adding the number of ways to get to the '0th' step to the 1st and 2nd steps, then 'stepping' from the 1st step to the 2nd and 3rd steps, and adding counts so on and so on. Only three locations need to be available at a time.

{{< code language="javascript" title="[Climbing Stairs](https://leetcode.com/problems/climbing-stairs/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof n === "number"

const climbStairs = (n) => {

    let steps = [1,0,0]
    let count = 0

    while(count < n){
        let next0 = steps[1] + steps[0]
        let next1 = steps[2] + steps[0]
        
        steps = [next0, next1, 0]
        count++
    }

    return steps[0]

}{{< /code >}}

To find the maximum sum of a subarray (consecutive numbers) in an array with a length, you can initialize a `best` variable and a `currentMax` variable both to the value of the first number in the array. Then, looping the rest of the numbers, at every step you can first update `currentMax` to be the better of `currentMax` plus the pointed value, or just the pointed value (this will remove negative values of `currentMax`). As the second part of each array step, update `best` to be `currentMax` if `currentMax` is ever the greater of the two. At the end of the loop, return `best`. This is a version of [Kadane's algorithm](https://en.wikipedia.org/wiki/Maximum_subarray_problem#Kadane's_algorithm).

{{< code language="javascript" title="[Maximum Subarray](https://leetcode.com/problems/maximum-subarray/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof nums === "object" (array of numbers)

const maxSubArray = (nums) => {

    let best = nums[0]
    let currentMax = nums[0]

    for (let i = 1; i < nums.length; i++){
        currentMax = Math.max(currentMax+nums[i], nums[i])
        best = Math.max(best,currentMax)
    }

    return best

}{{< /code >}}

To find the fewest number of coins that sum to a value, with an infinite amount of coins but specific coin types, a `dp` array can be used with length equal to `1 + amount` of the goal value. In this dynamic programming approach, seed the 0th location in `dp` with 0 to indicate it takes 0 coins (the value) to reach a partial amount of 0 (the index). Then, loop through the `dp` array for each available coin, and when a given value is noninfinite, update the value at the location a coin amount up from the index to be the minimum of itself or the source location plus 1. Each new coin can fill in new intervals from each available starting index. At the end, you can return a finite value at the last location in `dp`, or -1.

{{< code language="javascript" title="[Coin Change](https://leetcode.com/problems/coin-change/description/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof coins === "object" (array of numbers)
// typeof amount === "number"

const coinChange = (coins, amount) => {

    const dp = new Array(amount+1).fill(Infinity)
    dp[0] = 0

    for (let coin of coins){
        for (let i = 0; i < dp.length-coin; i++){
            dp[i+coin] = Math.min(dp[i+coin], dp[i]+1)
        }
    }

    if(isFinite(dp[amount])) return dp[amount]
    return -1

}{{< /code >}}

# Binary

To sum two binary strings, set indices to point to the rightmost (lowest) element in both. Set a `carry` variable to 0. Loop until both indices have reached the leftmost end of their strings. At each step in the loop, add the numerical sum of valid index values to `carry` (ignoring any index that is less than 0). Next, and also in each loop, push `carry%2` to a `result` array, and reset `carry` to its own base 2 overflow. Once both strings have been looped, add any final value in `carry` to the `result` array, if a final value exists, then reverse the array and join it into the result string.

{{< code language="javascript" title="[Add Binary](https://leetcode.com/problems/add-binary/) -- influenced by [hi-malik's code and explanation](https://leetcode.com/problems/add-binary/solutions/1679423/well-detailed-explaination-java-c-python-easy-for-mind-to-accept-it/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof a === "string"
// typeof b === "string"

const addBinary = (a, b) => {
    const result = []
    
    let aIndex = a.length-1
    let bIndex = b.length-1
    let carry = 0

    while(aIndex !== -1 || bIndex !== -1){
        if(bIndex > -1){
            carry += Number(b[bIndex])
            bIndex--
        }
        if (aIndex > -1){
            carry += Number(a[aIndex])
            aIndex--
        }

        result.push(carry%2)
        carry = Math.floor(carry/2)
    }

    if (carry) result.push(1)
    
    return result.reverse().join('')

}{{< /code >}}

To count the number of 1 in a binary representation of numbers from 0 to some arbitrary number, seed a results array with a 0 (there are 0 of 1 digits in 0). Then loop upwards as far as needed, seeding each next value in the results array with the value stored at half of it, rounded down, plus 1 if the leftmost digit in the new number is filled. Why look at the location half of it, rounded down? Because that is the equivalent of a bitshift right, so we only need to worry afterwards about one additional digit.

{{< code language="javascript" title="[Counting Bits](https://leetcode.com/problems/counting-bits/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof n === "number"

const countBits = (n) => {

    const results = [0]

    for (let i = 1; i <= n; i++){
        results[i] = results[i >> 1] + (i & 1)
    } 

    return results
       
}{{< /code >}}

A string's [Hamming weight](https://en.wikipedia.org/wiki/Hamming_weight) is the number of its nonzeros. In binary, Hamming weight is all of the 1s. From the target number, we can run a loop that increments a counter at every step, and also replaces the original number with the bitwise AND of itself and itself - 1. This guarantees that the number of loop steps while there is a number is the Hamming weight. Why minus 1 on an odd number removes 1 nonzero should be self-explanatory (the bitwise AND here does nothing), while the bitwise AND with an even original interacts with the minus 1 to create the same effect because the subtraction will cascade to be pullled from the lowest avaiable 1 on the original, and all 1s created by the process will be wiped out by the bitwise AND.

{{< code language="javascript" title="[Number of 1 Bits](https://leetcode.com/problems/number-of-1-bits/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof n === "number"

const hammingWeight = (n) => {

    let weight = 0

    while(n){
        n &= n-1
        weight++
    }

    return weight

}{{< /code >}}

If in an array of numbers all numbers appear twice except a single number that appears once, a bitwise XOR trick can detect the lonely number in linear time. By XORing the entire series and returning the result, because XOR will return 0 on each digit for each matched digits, even identical numbers that are not adjacent will eventually cancel each other out.

{{< code language="javascript" title="[Single Number](https://leetcode.com/problems/single-number/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof nums === "object" (array of numbers)

const singleNumber = (nums) => nums.reduce((a,b)=>a^b)

{{< /code >}}

To find a missing number in an array of numbers that would otherwise include all values between 0 and a given n, XOR all values in the array together with all values from 0 to the n. With the XOR, all doubled values should cancel out, leaving the missing value.

{{< code language="javascript" title="[Missing Number](https://leetcode.com/problems/missing-number/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof nums === "object" (array of numbers)

const missingNumber = (nums) => {

    let result = nums.length

    for(let i = 0; i < nums.length; i++){
        result ^= i ^ nums[i]
    }

    return result

}{{< /code >}}

In a 32-bit unsigned integer, what is the number represented by the reversal of the bits? In order to solve, start a `result` variable at 0, then loop all 32 digits of the binary version of the number to be reversed. At each step, double the `result` variable (a bitshift up by 1, but doubling with `*` in JavaScript as opposed to a direct bitshift prevents negative overflow). Then fill the new ones place on `result` with the presence or absence of the lowest digit remaining on the original number. Finally, for each step, bitshift down the number being read by 1.

{{< code language="javascript" title="[Reverse Bits](https://leetcode.com/problems/reverse-bits/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof n === "number"

const reverseBits = (n) => {

  let result = 0
  let count = 32

  while (count--) {
    result *= 2
    result += n & 1
    n >>= 1
  }

  return result
}

{{< /code >}}

# Math

One way of converting a Roman numeral to a number involves creating a lookup object that links Roman numeral keys to their numerical values. A separate lookup object indicates which Roman numeral keys can have negative values by coming just before specific Roman numerals of greater value. A `sum` variable is created, and then, in a loop reading the full Roman numeral from left to right, the numeral at the index is read into the `sum` variable as if it is positive. If the next index is filled and contains a valid greater numeral, add twice the negative of the numeral at the current index to `sum` to help reach the final true value.

{{< code language="javascript" title="[Roman to Integer](https://leetcode.com/problems/roman-to-integer/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof s === "string"

const romanToInt = (s) => {

    const look = {
        'I': 1,
        'V': 5,
        'X': 10,
        'L': 50,
        'C': 100,
        'D': 500,
        'M': 1000
    }

    const negs = {
        'I': new Set(['V','X']),
        'X': new Set(['L','C']),
        'C': new Set(['D','M'])
    }

    let sum = 0

    for (let i = 0; i < s.length; i++){
        sum += look[s[i]]

        if(negs[s[i]]?.has(s[i+1])){
           sum -= 2 * look[s[i]] 
        }

    }

    return sum

}{{< /code >}}

If a number is a palindrome, the reverse of the number is the same as the number. To reverse a number, create a `reverse` number set to 0, then loop through each digit of the original number. To do this, at every step, multiply the `reverse` by 10, and then add the lowest remaining digit of the original number to `reverse`. A way of finding the lowest remaining digit is by creating a copy of the original number, checking the ones place, and then dividing by 10 (rounding down) until the number is gone.

{{< code language="javascript" title="[Palindrome Number](https://leetcode.com/problems/palindrome-number/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof x === "number"

const isPalindrome = (x) => {

  if (x < 0) return false

  let reverse = 0
  for(let i = x; i > 0; i = Math.floor(i/10)){
      reverse = reverse * 10 + i%10
  }

  return reverse === x

}{{< /code >}}

# Heaps

To find the k closest points to the origin, sort the points by the sum of the x distance squared and the y distance squared from the origin. No need to take square root because relative order will be the same either way. Return the k closest points. This algorithm can be done with a priority queue (which could always keep the lowest point on top based on its distance) but this is not a native data struture in JavaScript.

{{< code language="javascript" title="[K Closest Points to Origin](https://leetcode.com/problems/k-closest-points-to-origin/description/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof points === "object" (array of arrays with two numbers)
// typeof k === "number"

const kClosest=(points,k)=>points.map(p=>[...p,p[0]**2+p[1]**2]).sort((a,b)=>a[2]-b[2]).slice(0,k).map(p=>[p[0],p[1]])

{{< /code >}}

# Tries

A trie represents words with as much overlapping prefixes as possible. To insert a word, start with an object, and for every letter of the word, set the letter's key in the outer object (if never seen there before) to a new object, and then go into that object. When the word is over, in the object of the final letter, add a `.end` key. Searching is similar. Descend from the top object without being able to insert anything on misses and return true only if a `.end` key is found. Checking if a prefix (`startsWith`) is available is a relaxed search from the top that doesn't care about `.end`.

{{< code language="javascript" title="[Implement Trie (Prefix Tree)](https://leetcode.com/problems/implement-trie-prefix-tree/description/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof word === "string"
// typeof prefix === "string"

const Trie = function() {
    this.trie = {}
}

Trie.prototype.insert = function(word) {
    let trie = this.trie
    for (let letter of word) {
        !trie[letter] && ( trie[letter] = {} )
        trie = trie[letter]
    }
    trie.end = true
}

Trie.prototype.search = function(word) {
    let trie = this.trie
    for (let letter of word) {
        if (!trie[letter]) return false
        trie = trie[letter]
    }
    return !!trie.end
}

Trie.prototype.startsWith = function(prefix) {
    let trie = this.trie
    for (let letter of prefix) {
        if (!trie[letter]) return false
        trie = trie[letter]
    }
    return true
}{{< /code >}}

# Recursion

To find all possible permutations of an array of integers, you can use a helper function `go`. This takes two arguments: `remainingUnusedNumbers`,which starts as the input array, and `buildingPermutation`, which starts as an empty array. If `remainingUnusedNumbers` is empty, the recursive base case is reached -- push `buildingPermutation` to a `results` array outside `go` and return. Else loop `remainingUnusedNumbers`, at every step generating a new `go` function slicing out the highlighted number and adding that number to a consistant end of the `buildingPermutation` array.

{{< code language="javascript" title="[Permutations](https://leetcode.com/problems/permutations/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof nums === "object" (array of numbers)

const permute = (nums) => {

    const results = []
    
    const go = (remainingUnusedNumbers,buildingPermutation) => {
        if(!remainingUnusedNumbers.length){
            results.push(buildingPermutation)
            return
        }
        for (let i = 0; i < remainingUnusedNumbers.length; i++){
            go(remainingUnusedNumbers.slice(0,i).concat(remainingUnusedNumbers.slice(i+1)),[remainingUnusedNumbers[i],...buildingPermutation])
        }
    }
    
    go(nums,[])
    
    return results

}{{< /code >}}