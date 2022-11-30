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

To reverse a linked list, create a null `link`, then perform the following steps while the original linked list still has a filled `head` node:

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

To find the middle node in a linked list (second of the two if the number of nodes is even), create `fast` and `slow` pointers and start them at `head`. While there is a `fast.next`, assign `slow` to the next node and `next` two nodes forward along the list. Once this loop resolves `slow` should point to the middle node by the problem definition.

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

# Binary Trees

LeetCode implements a binary tree node like this:

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