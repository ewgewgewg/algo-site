+++
title = "Js Algos"
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

A review of algorithm implementation in JavaScript. Adapted from the [Tech Interview Handbook](https://www.techinterviewhandbook.org/grind75?hours=19) and the [LeetCode](https://www.leetcode.com) website.

# Arrays

To efficiently find indexes of two array values that sum to a target, create a map, then iterate linearly. At every index, if `target - value` is a map key, return the current index, and the index under the map key. Othewise, add `value` as a key to the map, pointing to the current index. Because of action order at each step, the map always contains all previous values for direct lookup.

{{< code language="javascript" title="[Two Sum](https://leetcode.com/problems/two-sum/)" id="1" expand="Show" collapse="Hide" isCollapsed="false" >}}const twoSum = (nums, target) => {

    const map = new Map()

    for (let i = 0; i < nums.length; i++){
        if(map.has(target-nums[i])){
            return [i, map.get(target-nums[i])]
        }

        map.set(nums[i],i)
    }

}{{< /code >}}

# Stacks

To see if a string containing only `(){}[]` characters closes validly, create a dictionary where closing brackets point to opening brackets, and instantiate a stack. Then, iterate over the string, pushing opening brackets to the stack and popping the stack if a closing bracket is found which can close the stack's top item. If a closing bracket is found that does not close the stack's top item, or the stack is empty when a closing bracket is found, or the stack still has length when the string is fully iterated, return `false` -- otherwise `true` should be returned.

{{< code language="javascript" title="[Valid Parentheses](https://leetcode.com/problems/valid-parentheses/description/)" id="2" expand="Show" collapse="Hide" isCollapsed="false" >}}
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

# Linked Lists
