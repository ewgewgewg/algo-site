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

To find 0 or the greatest positive difference between a number in an array and a number that appears earlier in the array, which can be analogized to buying and selling stock, initialize a `low` "price" to the first number in the array, and the `best` difference to 0. Then loop through the array. At every step, replace `best` if appropriate with the `price` at the current index minus `low`, then update `low` if the current `price` is lower.

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

To move all zeros to the end of an array while preserving the order of nonzero elements, create a `nextNonzeroLocation` and assign the 0 index of the array, then iterate through the array with a different pointer. When this second pointer finds a nonzero, swap the value with the value at the `nextNonzeroLocation` and increment `nextNonzeroLocation` by 1. This will ensure that only 0 numbers will be "behind" the leading pointer, so steadily adding nonzeroes to the `nextNonzeroLocation` will never displace a value that needs its relative order preserved.

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
// typeof intervals === "object" (array of arrays with 2 numbers)
// typeof newInterval === "object" (array with 2 numbers)

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
// typeof intervals === "object" (array of arrays with 2 numbers)
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

In an array where red objects are represented as `0`, white objects are represented as `1`, and blue objects are represented as `2`, how to sort in-place so that red comes before white which comes before blue? Declare a `nextRedLocation` pointing to the 0 index and a `nextBlueLocation` pointing to the last valid index. Then loop the array. While the loop index is less than or equal to `nextBlueLocation`, check if the value at the index is `0`. If so, swap the value at the current location with the value at the `nextRedLocation` and move up `nextRedLocation` by 1. Next, in the same space in the loop, check if the value at the index is `2`. If so, swap the value with that at the `nextBlueLocation`, decrease `nextBlueLocation` by 1, and decrease the loop index by 1 in case the value moved needs transport as a red.

{{< code language="javascript" title="[Sort Colors](https://leetcode.com/problems/sort-colors/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof nums === "object" (array of numbers)

const sortColors = (nums) => {

    let nextRedLocation = 0
    let nextBlueLocation = nums.length - 1

    for (let i = 0; i <= nextBlueLocation; i++){

        if (nums[i] === 0){
            [nums[nextRedLocation], nums[i]] = [nums[i],nums[nextRedLocation]]
            nextRedLocation++
        }
        if (nums[i] === 2){
            [nums[nextBlueLocation], nums[i]] = [nums[i], nums[nextBlueLocation]]
            nextBlueLocation--
            i--
        }
        

    }
    
}{{< /code >}}

With a series of container heights arranged along an axis, how can you choose a pair such that the minimum of the two, times the distance between them, is the maximum for all possible pairs? Notice that if you start candidate pairs as the leftmost and rightmost, the only way to increase the possible area contained between them is if an inner height is greater than an outer height. The specific algorithm involves defaulting the `best` area to the area provided by the outermost pairs, then repeatedly choosing the smaller of the edge heights, and moving that pointer one step inwards before checking to see if the `best` area should update. If the pointers meet, `best` can no longer be improved.

{{< code language="javascript" title="[Container With Most Water](https://leetcode.com/problems/container-with-most-water/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof h === "object" (array of numbers)

const maxArea = (h) => {

    let best = 0
    let left = 0
    let right = h.length - 1
    
    while(left < right){
        const candidate = Math.min(h[left], h[right], h[left]) * (right-left)
        if (candidate > best) best = candidate

        if(h[left] < h[right]) left++
        else right--
    }
    
    return best

}{{< /code >}}

Given two arrays of equal length, one that gives the amount of gas added to a car at a given index station, and the other that gives the cost to travel from that index's station to the next index, is it possible to travel to all stations if they lie on a circular route, and if so, from what index should one start? A foundation for a linear solution lies in noticing that at every index the travel cost of gas can be subtracted from added gas to give the `changeInGasAtIndex` after one leaves it. If the `sum` of all `changeInGasAtIndex` is negative, it will not be possible to complete a loop because there is not enough total gas available. Otherwise, to determine what station index to leave from, you can loop the stations and keep a running total of gas in `tank`. Start a `station` marker at the 0 index. if the `tank` every goes negative, set the `station` marker to the next index. Return the `station` indicated at the end of the loop. Backtracking is not necessary because every sucessful movement from index to index must at a minimum involve 0 gas remaining.

{{< code language="javascript" title="[Gas Station](https://leetcode.com/problems/gas-station/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof gas === "object" (array of numbers)
// typeof cost === "object" (array of numbers)

const canCompleteCircuit = (gas, cost) => {
    
    let sum = 0
    let tank = 0
    let station = 0

    for (let i = 0; i < gas.length; i++) {
        const changeInGasAtIndex = gas[i] - cost[i]
        tank += changeInGasAtIndex
        sum += changeInGasAtIndex
        if(tank < 0) {
            tank = 0
            station = i + 1
        }
    }
    return sum > -1 ? station : -1
}{{< /code >}}

To determine the number of longest consecutive elements in an array, notice that you can start a search at every number where the prior number in the array does not exist. To set up for this, create a set with all `numbers`, then loop the array. Whenever the original number does not have an immediate prior, step through `next` numbers consecutively until `next` cannot be found, then update the `best` length as appropriate.

{{< code language="javascript" title="[Longest Consecutive Sequence](https://leetcode.com/problems/longest-consecutive-sequence/description/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof nums === "object" (array of numbers)

const longestConsecutive = (nums) => {

    let best = 0
    const numbers = new Set(nums)
    
    for (let number of nums){
        if (numbers.has(number-1)) continue
        let next = number + 1
        while (numbers.has(next)) next++
        best = Math.max(best, next-number)
    }

    return best

}{{< /code >}}

To rotate an array to the right by a certain number of steps, note that this means taking that number of elements from the right and adding them to the left of the array. You can do this in-place by determining how many elements must move. This value can be found by dividing the number of steps to right move the array by the length of the array and taking the remainder (modulo operation). Then simply splice off that number of elements from the back of the array and unshift them to the front.

{{< code language="javascript" title="[Rotate Array](https://leetcode.com/problems/rotate-array/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof nums === "object" (array of numbers)
// typeof k === "number"

const rotate = (nums, k) => {
    k %= nums.length
    nums.unshift(...nums.splice(-k))
}{{< /code >}}

To find the maximum length with equal `0` and `1` in an array that only contains `0` and `1`, you can solve the problem linearly by counting `extraOnes`. Set that variable to `0`, set a `best` to `0`, and create a `map` that indicates there are `0` `extraOnes` at the `-1` index. Then iterate the array. At every step, keep a running tally of `extraOnes` by incrementing if a `1` is found and decrementing if a `0` is found. Then, if that number has not yet been seen in the `map`, add it to the `map` with a value of the current index. Otherwise, update `best` if the index minus the first time that many `extraOnes` were found (use the `map`) is greater than `best`.

{{< code language="javascript" title="[Contiguous Array](https://leetcode.com/problems/contiguous-array/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof nums === "object" (array of numbers)

const findMaxLength = (nums) => {
    
    const map = new Map()
    map.set(0, -1)

    let best = 0
    let extraOnes = 0
    for (let i = 0; i < nums.length; i++){
        extraOnes += nums[i] ? 1 : -1

        if (!map.has(extraOnes)){
            map.set(extraOnes, i)
            continue
        }

        best = Math.max(best, i - map.get(extraOnes))
    }
    
    return best

}{{< /code >}}

Given an array of numbers, how can the number of subarrays that sum a given integer be found? (Subarrays are contiguous.) One linear method is to create a `map` to store the number of ways to sum to a given number already seen (starting it with a key of 0 pointing to a value of 1). Then iterate the array, keeping track of the `sum` of all numbers so far seen. At every step, if the `sum` counting the newly-seen number can be found in the `map`, the `result` can be increased by the number stored in the `map`. Then add a tally for the `sum` seen at the current location to the `map`.

{{< code language="javascript" title="[Subarray Sum Equals K](https://leetcode.com/problems/subarray-sum-equals-k/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof nums === "object" (array of numbers)
// typeof k === "number"

const subarraySum = (nums, k) => {

  const map = new Map([[0,1]])

  let sum = 0
  let result = 0
  for (number of nums) {
    sum += number
    if (map.has(sum - k)) result += map.get(sum - k)
    map.set(sum, map.get(sum) + 1 || 1)
  }
  return result
  
}{{< /code >}}

Given an array containing arrays with paired starting and ending times for meetings, how many meeting rooms are needed? A key to understanding this question is realizing that the number of meeting roms needed is the maximum number of overlapping meetings at any time. To solve, you can first handle the edge case where there are no meetings and return `0`. Else, split the input array into two sorted arrays, one that sorts the times of the `starts` from earliest to latest, and the other that sorts the times of the `ends` from earliest to latest. Set a `startPointer` and an `endPointer` both to 0, then loop as long as `startPointer` is less than the length of the input array. Each round, if the `starts` value at `startPointer` is less than the `ends` value at `endPointer`, this means a meeting is starting before the active one is ending. In this case, you can increase a `result` counter, starting at 0, by 1, and then increment the `startPointer` by 1 before the next round of the loop (since the meeting that just started has just been processed). In the other case, if the `startPointer` is pointing to a time greater or equal to the `endPointer`, this means there is no overlap between whatever meeting is starting and ending, and you can increment both pointers. The reason `result` ends at the correct value is because when `result` increments, the `startPointer` runs ahead by one step, meaning that overlaps of the same degree will not be double-counted, and only if the `startPointer` needs to run ahead even further will a new meeting room result be tallied.

{{< code language="javascript" title="[Meeting Rooms II](https://leetcode.com/problems/meeting-rooms-ii/) -- code adapted from [svalak's Python code and explanation](https://svalaks.medium.com/leetcode-253-meeting-rooms-ii-ed40f55663a5)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof intervals === "object" (array of arrays with 2 numbers)

const minMeetingRooms = (intervals) => {
  
  if (!intervals?.length) return 0

  const starts = intervals.sort((a,b) => a[0] - b[0]).map(a => a[0])
  const ends = intervals.sort((a,b) => a[1] - b[1]).map(a => a[1])
  let startPointer = 0
  let endPointer = 0

  let result = 0
  while (startPointer < intervals.length){
      if (starts[startPointer] < ends[endPointer]){
        result++
      } else {
        endPointer++
      }
      startPointer++
  }
  
  return result

}{{< /code >}}

To find three numbers that sum closest to a target number in an array, one solution involves first sorting the array from least to greatest. Then, you can set a `best` to `Infinity`, and create a loop that takes a `firstPointer` from the 0 index to two less than the final index. At every step, the `secondPointer` starts in each loop as `firstPointer+1`, while the `thirdPointer` starts at the last index of the array. At this space in the outer loop, while `secondPointer` is less than `thirdPointer`, find the `sum` of the values at the three indices. Return the `sum` directly if it matches the target, or update `best` if the absolute value of the target less the `sum` is a smaller value than the target less `best`. Otherwise, move the `secondPointer` forwards until it points to a new value or crosses the `thirdPointer`, and pull the `thirdPointer` back until it does the same. Then check again if `secondPointer` is less than `thirdPointer`, and if so, go back to the step where you find the sum again. If the outer loop finishes, return `best`.

{{< code language="javascript" title="[3 Sum Closest](https://leetcode.com/problems/3sum-closest/) -- code adapted from [casmith1987 code and explanation](https://leetcode.com/problems/3sum-closest/solutions/1467590/javascript-99/5)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof nums === "object" (array of numbers)
// typeof target === "number"

const threeSumClosest = (nums, target) => {

  nums.sort((a,b) => a-b)
  let best = Infinity
  
  for (let firstPointer = 0; firstPointer < nums.length - 2; firstPointer++) {
    if (firstPointer > 0 && nums[firstPointer] === nums[firstPointer - 1]) continue

    let secondPointer = firstPointer + 1
    let thirdPointer = nums.length - 1
    while (secondPointer < thirdPointer) {
      const sum = nums[firstPointer] + nums[secondPointer] + nums[thirdPointer]
	  
      if (sum === target) return sum
      if (Math.abs(target - best) > Math.abs(target - sum)) best = sum
	  
      if (sum < target) {
        secondPointer++
        while(secondPointer < thirdPointer && nums[firstPointer] === nums[firstPointer - 1]) secondPointer++
      } else {
        thirdPointer--
        while (secondPointer < thirdPointer && nums[thirdPointer] === nums[thirdPointer + 1]) thirdPointer--
      }
    }
  }

  return best

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

To create an array that gives the distance between a given number in an input array and the next greater number, which can be understood as number of days until a greater temperature, you can create a `stack` and loop the input array. While the `stack` has length and the current value in the input array is greater than the value indicated at end of the stack, you can pop the index of the `dayWithTemperatureJustDefeated` from the end of the `stack`, assigning to that index in a `result` array the positive difference between the currrent loop index and the index of the `dayWithTemperatureJustDefeated`. Then you can push the current loop index to the stack. After the traversal of the input array, any indicies that could not be popped should be used to assign a value to the `result` array that indicates no later greater value could be found, which in the LeetCode version of the question is 0.

{{< code language="javascript" title="[Daily Temperatures](https://leetcode.com/problems/daily-temperatures/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof temperatures === "object" (array of numbers)

const dailyTemperatures = (temperatures) => {

    const stack = []
    const result = []

    for (let i = 0; i < temperatures.length; i++) {
        while (stack.length && temperatures[i] > temperatures[stack[stack.length - 1]]){
            const dayWithTemperatureJustDefeated = stack.pop()
            result[dayWithTemperatureJustDefeated] = i - dayWithTemperatureJustDefeated 
        }
        stack.push(i)
    }

    for (let index of stack){
        result[index] = 0
    }

    return result

}{{< /code >}}

If you have an encoded string where numbers followed by brackets multiply the contents of the brackets, how to return the decoded string? One way to solve this involves a `repeatStack` and a `stringStack`. Both are arrays, and `repeatStack` starts empty, while `stringStack` starts with an empty string. A `latestRepeats` is also declared as an empty string. As the encoded string is looped, the stacks are used to deode. Numbers are added as string characters to the `latestRepeats` variable. The character `[` requires the `latestRepeats` to be turned in to a number type and pushed to the `repeatStack`, while `latestRepeats` is reset to an empty string, and the `stringStack` also take an empty string. The character `]` requires both `repeatStack` and `stringStack` to pop, and the contents of the popped value from `stringStack` be added to new last value of `stringStack` a number of times equal to the value popped from `repeatStack`. For any other character processed from the enoded string, add it to the last element of `stringStack`. After the encoded string is fully processed, return what should be the only value in `stringStack`.

{{< code language="javascript" title="[Decode String](https://leetcode.com/problems/decode-string/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof s === "string"

const decodeString = (s) => {

    const repeatStack = []
    const stringStack = [""]
    let latestRepeats = ""

    for (let character of s){
        if (character === "["){
            repeatStack.push(Number(latestRepeats))
            latestRepeats = ""
            stringStack.push("")
        } else if (character === "]"){
            const string = stringStack.pop()
            const repeat = repeatStack.pop()
            stringStack[stringStack.length-1] += string.repeat(repeat)
        } else if (Number.isInteger(Number(character))) {
            latestRepeats += character
        } else {
            stringStack[stringStack.length-1] += character
        }
    }
    return stringStack[0]

}{{< /code >}}

An array of integers where positive numbers head right, negative numbers head left, and 0 does not exist can be imagined as a field of asteroids. What is the state of the array after collisions, when in collisions smaller numbers are removed, and in cases of equal numbers both are removed? This problem can be solved with a `stack`. After the `stack` is created, loop the input array. Push every positive element to the array. In cases of negative elements, as long as there is a positive most recent (rightmost) item on the `stack`, pop `stack` elements until the absolute value of the negative element is smaller or equal to the most recent item on the `stack`. If the 'asteroid' moving left is smaller, continue to the next step in the outermost loop, and if the 'asteroids' are equal, pop the most recent item on the stack before continuing to the next step in the outermost loop. Otherwise, the asteroid moving to the left has cleared the rightwards 'asteroids' on the `stack`, and should be pushed to it.

{{< code language="javascript" title="[Asteroid Collision](https://leetcode.com/problems/asteroid-collision/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof asteroids === "object" (array of numbers)

const asteroidCollision = (asteroids) => {

    const stack = []

    for (let asteroid of asteroids){
        if (asteroid > 0){
            stack.push(asteroid)
            continue
        }

        const absAsteroid = Math.abs(asteroid)
        let addAsteroid = true
        while(stack.length && stack[stack.length-1] > 0){
            if(stack[stack.length-1] > absAsteroid){
                addAsteroid = false
                break
            } else if (stack[stack.length-1] === absAsteroid){
                addAsteroid = false
                stack.pop()
                break
            }

            stack.pop()
        }

        if (addAsteroid) stack.push(asteroid)

    }

    return stack

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

To create a cache that evicts the least recent key when a new key is added that goes over a cache `capacity`, we can take advantage of the properties of the JavaScript Map object. One strategy involves declaring a function and assigning it to a variable. When this function is instanced, for example, with  `new LRUCache(4)`, assign the argument `capacity` to `this` (`capacity` is 4 in the example) and also assign a new Map under `this`. For `get`, if there is no key, you can return `-1`, else get, delete, and reset the key, and return the value. This makes it so that Map's internal ordering notes this key as most recent. The `set` method is similar -- simply `delete` the key if it already exists, `set` it again, and then use JavaScript map functions to `delete` the first key added to the Map. Note that this and many other solutions currently (December 2022) often time out on LeetCode but do periodically pass. The primary difference between this and [Cjamm's solution](https://leetcode.com/problems/lru-cache/solutions/1157689/javascript-map/) is that this code will always attempt to delete keys in either method.

{{< code language="javascript" title="[LRU Cache](https://leetcode.com/problems/lru-cache/) -- closely adapted from [Cjamm's code](https://leetcode.com/problems/lru-cache/solutions/1157689/javascript-map/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof capacity === "number"

const LRUCache = function(capacity) {
    this.capacity = capacity
    this.map = new Map()
}

LRUCache.prototype.get = function(key) {
    if(!this.map.has(key)) return -1
    const value = this.map.get(key)
    this.map.delete(key)
    this.map.set(key, value)
    return value
}

LRUCache.prototype.put = function(key, value) {
    this.map.delete(key)
    this.map.set(key, value)
    if (this.map.size > this.capacity) this.map.delete(this.map.keys().next().value)
}

{{< /code >}}

To remove the nth node from the end of a list, you can count the `length` of the list by iterating, then subtract from `length` the nth item to be removed to find the number of the item to be removed starting from the front. Finally, with a new pointer from the front of the list, iterate until one step before the item to be removed, and from that item, replace the `.next` with `.next.next` if available, otherwise `null`. If the item to be removed is at the `head` of the list, simply return the `.next` from the `head`.

{{< code language="javascript" title="[Remove Nth Node From End of List](https://leetcode.com/problems/remove-nth-node-from-end-of-list/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof head === "object" (linked list)
// typeof n === "number"

const removeNthFromEnd = (head, n) => {

  let counterCopy = head

  let length = 0
  while (counterCopy) {
    length++
    counterCopy = counterCopy.next
  }
  
  let countToRemove = length - n
  if (!countToRemove) return head.next

  let removerCopy = head
  while (countToRemove > 1) {
    countToRemove--
    removerCopy = removerCopy.next
  }

  removerCopy.next = removerCopy.next?.next || null
  return head

}{{< /code >}}

How to swap every two adjacent nodes in a linked list? You can run a function recursively -- if there is no `.next` simply return `head`, otherwise save the head as `nodeToBeMovedBack`, save the `.next.next` as the `recursiveCallArgument`, move the `head` to `head.next`, and take the `nodeToBeMovedBack` as the new `head.next`. Finally, assign the recursive call to `head.next.next`.

{{< code language="javascript" title="[Swap Nodes in Pairs](https://leetcode.com/problems/swap-nodes-in-pairs/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof head === "object" (linked list)

const swapPairs = (head) => {

    if(head?.next){
        const nodeToBeMovedBack = head
        const recursiveCallArgument = head.next.next
        head = head.next
        head.next = nodeToBeMovedBack
        head.next.next = swapPairs(recursiveCallArgument)
    }

    return head

}{{< /code >}}

To change a linked list so that all odd-indexed elements are followed by all even-indexed elements, if the linked list has a length, you can set three pointers. First, an `odd` starting at the `head`, second, an `even` starting at `head.next`, and third, an extra pointer staying at `headEven`. While there is an `even.next` (aka what should be the next `odd`), set the `.next` of both `odd` and `even` to their `.next.next` and then set `odd` and `even` themselves to their `.next`. Make sure to do `odd` first so the links for `even` are not broken. Once there is no more `even.next`, set `odd.next` to `headEven` and retun this.

{{< code language="javascript" title="[Odd Even Linked List](https://leetcode.com/problems/odd-even-linked-list/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof head === "object" (linked list)

const oddEvenList = (head) => {

    if (head === null) return head
    
    let odd = head
    let even = head.next
    const headEven = even

    while (even?.next) {
        odd.next = odd.next.next
        odd = odd.next
        even.next = even.next.next
        even = even.next
    }
    odd.next = headEven

    return head

}{{< /code >}}

If two linked lists represent numbers in reversed order, how to add them into a new linked list? You can create a `link` pointer to help hold the eventual head, a copy `joined` pointer to iterate through the digits of the result, a `pointer1` and `pointer2` to point to the first node in each input list, and a `carry` variable starting at 0 to handle overflow. While `pointer1` or `pointer2` are not null, add the sum of the values at both, if any, plus `carry`, to a `sum`. The tens place of this value becomes `carry` for the next step, and the ones place is added as the value to the next `joined` node. Then `pointer1` and `pointer2` move to their `.next`, if available. At the end of the while loop, if there is still a value in `carry`, add it as one final node, and then return the `.next` of the original `link`.

{{< code language="javascript" title="[Add Two Numbers](https://leetcode.com/problems/add-two-numbers/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof l1 === "object" (linked list)
// typeof l2 === "object" (linked list)

const addTwoNumbers = (l1, l2) => {

    const link = new ListNode()
    let joined = link

    let carry  = 0
    let pointer1 = l1
    let pointer2 = l2

    while(pointer1 || pointer2) {
        const sum = carry + (pointer1?.val || 0) + (pointer2?.val || 0)
        carry = Math.floor(sum / 10)
        joined.next = new ListNode(sum % 10)
        joined = joined.next
        pointer1 = pointer1?.next || null
        pointer2 = pointer2?.next || null
    }

    joined.next = carry ? new ListNode(carry) : null

    return link.next

}{{< /code >}}

How to return a linked list after sorting its nodes in ascending order? Recursion is a solution. The base case, where the input does not have a `.next`, involves returning the input. Else you can use `fast` and `slow` pointers to find a middle pointer with `slow`. Set a `breakpoint` at `slow` and assign its `.next` as the `second` half of the series, then make `breakpoint`'s own `.next` null, which separates out the `first` half. Make a recursive call for both halves to ensure they are sorted, and then compare the heads of each repeatedly, adding whichever is lower but present to a new series of nodes building off an empty `link`. When both heads are exhausted, return `link.next`.

{{< code language="javascript" title="[Sort List](https://leetcode.com/problems/sort-list/) -- code adapted from [grs's code](https://leetcode.com/problems/sort-list/solutions/496007/javascript-merge-sort/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof head === "object" (linked list)

const sortList = (head) => {
    if (!head?.next) return head
    
    let fast = head
    let slow = head
    while (fast.next?.next) {
        fast = fast.next.next
        slow = slow.next
    }
    
    const first = head
    const breakpoint = slow
    const second = slow.next
    breakpoint.next = null

    let sortedFirst = sortList(first)
    let sortedSecond = sortList(second)
    
    const link = new ListNode()
    let pointer = link
    while (sortedFirst || sortedSecond) {
        if (!sortedSecond || sortedFirst?.val < sortedSecond?.val) {
            pointer.next = sortedFirst
            sortedFirst = sortedFirst.next
        } else {
            pointer.next = sortedSecond
            sortedSecond = sortedSecond.next
        }
        pointer = pointer.next
    }
    pointer.next = null
    
    return link.next

}{{< /code >}}

To reorder a linked list in-place such that the second value would become the old last value, and the fourth value would become the old second-to-last value, you can find the `endOfFrontPart` value with fast and slow pointers, then assign its `.next` as the leading part of the back half, the first `inorderBackNode`. Then you can make `endOfFrontPart`'s `.next` null to pinch off the front of the linked list from the back. The linked list starting with `inorderBackNode` can then be `reversed`. In the final part of the algorithm, create an empty `link` node and a copy of the original linked list head as `forward`. While `reversed` exists, assign the `.next` of `link` to `forward`, advance both `link` and `forward` to their `.next`, then assign the `.next` of `link` to `reversed` and advance both `link` and `reversed` to their `.next`. Finally, to account for a possible odd number of nodes, assign `link.next` to `forward`.

{{< code language="javascript" title="[Reorder List](https://leetcode.com/problems/reorder-list/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof head === "object" (linked list)

const reorderList = (head) => {

    let fast = head
    let slow = head
    while(fast?.next){
        fast = fast.next.next
        slow = slow.next
    }

    let endOfFrontPart = slow
    let inorderBackNode = endOfFrontPart.next
    endOfFrontPart.next = null

    let reversed = null
    while (inorderBackNode){
        const next = inorderBackNode.next
        inorderBackNode.next = reversed
        reversed = inorderBackNode
        inorderBackNode = next
    }

    let link = new ListNode()
    let forward = head
    while (reversed){
            link.next = forward
            forward = forward.next
            link = link.next

            link.next = reversed
            reversed = reversed.next
            link = link.next
    }

    link.next = forward

}{{< /code >}}

To rotate a linked list to the right by a certain number of places, you can note that an empty list returns itself. Otherwise, a next step is to count the `length` of the list. Update the rotation count to its remainder after dividing by the `length`. If the new rotation count is `0` or the `length`, you can return the original linked list. Otherwise, you can save the `last` node, and with the new rotation count, iterate a pointer from the beginning of the list until you are the rotation count of spaces away from the `last` node. Save this location as the `result`, set the previous node's `.next` to `null`, and set the `last`'s `.next` to the original head. Then return `result`.

{{< code language="javascript" title="[Rotate List](https://leetcode.com/problems/rotate-list/)" expand="Show" collapse="Hide" isCollapsed="false" >}}

const rotateRight = (head, k) => {
  if (!head) return head
  let length = 1
  let pointer = head
  while (pointer.next) {
    length++
    pointer = pointer.next
  }

  k %= length
  if (!k || k === length) return head

  const last = pointer
  let pointer2 = head

  for (let i = 0; i < length-k-1; i++){
      pointer2 = pointer2.next
  }

  const result = pointer2.next
  pointer2.next = null
  last.next = head

  return result
  
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

To convert a stringified number to a 32-bit signed integer, you can remove whitespace, parse the number with base 10, return `0` if the result is not a number, and clamp the value to the available space if the number is too great or too small.

{{< code language="javascript" title="[String to Integer (atoi)](https://leetcode.com/problems/string-to-integer-atoi/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof str === "string"

const myAtoi = (str) => {
    
    const integer = parseInt(str.trim(), 10)

    if (isNaN(integer)) {
        return 0
    } else if (integer >= Math.pow(2, 31)) {
        return Math.pow(2, 31) - 1   
    } else if (integer < Math.pow(-2, 31)) {
        return Math.pow(-2, 31)
    } else {
        return integer
    }
}{{< /code >}}

To find the longest palindromic substring of a string, you can simply iterate the string, and for every location make new pointer pairs expand outwards in both directions, continuing if each pointer pair matches and updating the `result` string if appropriate. Each location on the string being looped needs two expansions, one that does not test the active index, using the index just before and just after as the first pair (checking odd length strings), and one that uses the active index with the next index as the first pair of the location, and expands outwards from there (checking even length strings). If any index is out of bounds, loops should end as needed.

{{< code language="javascript" title="[Longest Palindromic Substring](https://leetcode.com/problems/longest-palindromic-substring/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof s === "string"

const longestPalindrome = (s) => {

    let result = s[0]

    for (let i = 0; i < s.length; i++){

        let left = i - 1
        let right = i + 1
        while(left >= 0 && right < s.length && s[left] === s[right]){
            if(right-left+1 > result.length) result = s.slice(left, right+1)
            left--
            right++
        }

        left = i
        right = i + 1
        while(left >= 0 && right < s.length && s[left] === s[right]){
            if(right-left+1 > result.length) result = s.slice(left, right+1)
            left--
            right++
        }
    }

    return result

}{{< /code >}}

How to return all start indicies of an anagram of one string in another string? First, if the parent string is shorter than the string that may appear inside as an anagram, you can immediately return an empty array. Then you can create a `countMap` of the string to be made into an anagram. Separately, create a count of the number of unique `letters` in the potential anagram. The final two variables needed are a `result` array and a count of `numberOfAnagramLettersFullySeen`. The remainder of the algorithm is a linear pass through the larger parent string. At every new location, if the letter being pointed to is in the `countMap`, decrease the value at the appropriate letter in the `countMap` by 1. If this `newCount` is `0`, that means you can increment `numberOfAnagramLettersFullySeen` by 1. In the same step in the loop through the parent string, check the location further left by the length of the anagram string, if available -- this location should no longer be part of the examined string. Increase `countMap` and decrease `numberOfAnagramLettersFullySeen` as needed based on what might have been in the location no longer being considered. As the third and final step for each location in the loop, push the index less the length of the anagram string plus 1 to the `results` array if the `numberOfAnagramLettersFullySeen` is the same as the number of unique `letters`. We can only ever consider strings of the required length.

{{< code language="javascript" title="[Find All Anagrams in a String](https://leetcode.com/problems/find-all-anagrams-in-a-string/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof s === "string"
// typeof p === "string"

const findAnagrams = (s, p) => {

    if (s.length < p.length) return []

    const countMap = new Map()
    let letters = 0
    for (let character of p){
        if (!countMap.has(character)){
            letters++
            countMap.set(character, 0)
        } 
        countMap.set(character, countMap.get(character) + 1)
    }

    const results = []
    let numberOfAnagramLettersFullySeen = 0
    
    for (let i = 0; i < s.length; i++){
        if (countMap.has(s[i])){
            const newCount = countMap.get(s[i]) - 1
            countMap.set(s[i], newCount)
            if (newCount === 0) numberOfAnagramLettersFullySeen++
        }
        if (i >= p.length && countMap.has(s[i-p.length])){
            const newCount = countMap.get(s[i-p.length]) + 1
            countMap.set(s[i-p.length], newCount)
            if (newCount === 1) numberOfAnagramLettersFullySeen--
        }
        if (numberOfAnagramLettersFullySeen === letters) results.push(i-p.length+1)
    }
    
    return results
    
}{{< /code >}}

To group an array's anagrams into arrays within a result array, one method starts with creating a container for the `groups`, then looping the strings in the input array. At every step find a `standardAnagram` key for the string by turning it into an array, sorting it, and joining it. Add this key to `groups` if it does not already exist, then add the original string as a value. Once the loop is complete, return all the array values in `groups`.

{{< code language="javascript" title="[Group Anagrams](https://leetcode.com/problems/group-anagrams/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof strs === "object" (array of strings)

const groupAnagrams = (strs) => {

    const groups = {}
    
    for (let str of strs){
        const standardAnagram = str.slice().split('').sort().join('')
        if (!groups[standardAnagram]) groups[standardAnagram] = []
        groups[standardAnagram].push(str)
    }
    
    return Object.values(groups)

}{{< /code >}}

Given a string and a count of times you can change a character, what is the longest substring (aka contiguous string inside the parent) that contains all the same letter? One solution involves a `counts` object, setting a `startIndex` to 0, and a `greatestCountSeen` to 0. Loop the input string. At every step, update the `counts` object to include the count of the current character. Then for that character's count, udate `greatestCountSeen` if needed. Next, if the `greatestCountSeen` plus the number of times you can change a character is less than the range from the current index to the `startIndex`, inclusive, remove a count from the character at `startIndex` and increase `startIndex` by 1. Finally, update a `best` if the length of the substring between `startIndex` and the current index, inclusive, is better. Whenever `greatestCountSeen` is updated, you have reached a location with a candidate for the new `best`, so you increment `startIndex` until you have a valid possible length.

{{< code language="javascript" title="[Longest Repeating Character Replacement](https://leetcode.com/problems/longest-repeating-character-replacement/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof s === "string"
// typeof k === "number"

const characterReplacement = (s, k) => {
    
    const counts = {}
    let startIndex = 0
    let greatestCountSeen = 0
    let best = 0
    for (let i = 0; i < s.length; i++){
        counts[s[i]] = counts[s[i]] + 1 || 1

        if(counts[s[i]] > greatestCountSeen) greatestCountSeen = counts[s[i]]

        while(i - startIndex + 1 > k + greatestCountSeen){
            counts[s[startIndex]]--
            startIndex++
        }

        best = Math.max(best, i - startIndex + 1)

    }
    return best

}{{< /code >}}

To find the largest number that can be made out of non-negative integers in an array, you can stringify the numbers and sort them by paired concatenations, such that numbers that when leading the pair make for larger joined numbers, end up on the left.

{{< code language="javascript" title="[Largest Number](https://leetcode.com/problems/largest-number/) -- code slightly modified from [eddyhdzg's code and explanation](https://leetcode.com/problems/largest-number/solutions/864518/javascript-typescript-solution/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof nums === "object" (array of numbers)

const largestNumber = (nums) => {
    const result = nums
    .map(value => String(value))
    .sort((a, b) =>  a.concat(b) > b.concat(a) ? -1 : 1)
    .join("")

    if(result[0] === "0") return "0"

    return result

}{{< /code >}}

How can you encode and decode an array of strings into and out of a single string? To encode, you can prefix each string with its length and a separator symbol, and then join them. To decode, you can read numbers until you get to the separator symbol, slice the number of characters after the separator symbol equal to the number just read, and add it to a `result` array. Then as long as the string being decoded still has length, read the next characters for numbers until a separator symbol is reached, etc.

{{< code language="javascript" title="[Encode and Decode Strings](https://leetcode.com/problems/encode-and-decode-strings/) -- code adapted from [Mini Chang's Python code and explanation](https://medium.com/@miniChang8/leetcode-encode-and-decode-strings-4dde7e0efa1c)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof strs === "object" (array of strings)
// typeof s === "string"

const encode = (strs) => {

  const result = []
  for (let string of strs){
    const encoded = String(string.length) + '/' + string
    result.push(encoded)
  }
  return result.join("")

}

const decode = (s) => {

  const result = []
  let i = 0
  while (i < s.length){
      let numberIndex = i
      while (s[numberIndex] !== '/'){
        numberIndex++
      }
      let size = Number(s.slice(i,numberIndex))
      const word = s.slice(numberIndex + 1, numberIndex + 1 + size)
      i = numberIndex + 1 + size
      result.push(word)
  }

  return result

}{{< /code >}}

# Binary Trees

One of the ways LeetCode has implemented a binary tree node is like this:

{{< code language="javascript" expand="Show" collapse="Hide" isCollapsed="false" >}}

function TreeNode(val, left, right) {

     this.val = (val===undefined ? 0 : val)

     this.left = (left===undefined ? null : left)
     this.right = (right===undefined ? null : right)

}{{< /code >}}

Inverting a binary tree means to "turn it over" as if it was a page in a book, creating what the original would look like from behind. This means swapping every pair of `left` and `right` references. A recrusive implementation step returns immediately on a `null` node, and otherwise returns after using a holding variable for one of the `left` or `right` references to help complete the second half of the swap.

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

To find an array of all the rightmost elements from top to bottom in a binary tree, run a helper function `go` with the `root` node and the current `depth` (starting at 0). In `go`, if there is no node, return immediately. Else place the `.val` of the current node at the `depth` index of `results` and call `go` twice, with the `.left` and then the `.right` nodes, each with `depth+1` as the second argument. Because rights are being called after lefts, each row will be viewed from left to right even though the depth-first search nature of the algorithm means that a row may not be finished before a call goes down to the next level.

{{< code language="javascript" title="[Binary Tree Right Side View](https://leetcode.com/problems/binary-tree-right-side-view/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof root === "object" (binary tree node)

const rightSideView = (root) => {

    const result = []

    const go = (node, depth) => {
        if (!node) return
        result[depth] = node.val
        go(node.left, depth+1)
        go(node.right, depth+1)
    }

    go(root,0)
    return result
    
}{{< /code >}}

How to construct a binary tree from arrays with unique values containing the `preorder` and `inorder` traversal? Note that in the `preorder` traversal, head nodes appear before children nodes. This means that the 0th location in the `preorder` array should be the root value of the binary tree. To construct the tree formally, return a helper function `construct` with four arguments -- the leftmost and rightmost indicies of the `preorder` and `inorder` arrays. If either left is greater than than their right, `construct` can immediately return a `null` node. Else, get the current root `node` `value` from the `value` of `preorder` at `preorderLeft`. Start a new `node` with the `value`, and find the `index` of that `value` in `inorder`. Count the number of `leftNodes` by subtracting the `inorderLeft` from `index`, then build out the `.left` and `.right` nodes by running two new `construct` invocations with updated bounds.

{{< code language="javascript" title="[Construct Binary Tree from Preorder and Inorder Traversal](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/description/) -- code adapted from [jeantimex's code and explanation](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/solutions/34553/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof preorder === "object" (array of numbers)
// typeof inorder === "object" (array of numbers)

const buildTree = (preorder, inorder) => {
    
    const construct = (preorderLeft, preorderRight, inorderLeft, inorderRight) => {
        if (preorderRight < preorderLeft || inorderRight < inorderLeft) return null

        const value = preorder[preorderLeft]
        const node = new TreeNode(value)
        const index = mapValueToInorderIndex.get(value)

        const leftNodes = index - inorderLeft
        node.left  = construct(preorderLeft + 1, preorderLeft + leftNodes, inorderLeft, index - 1)
        node.right = construct(preorderLeft + leftNodes + 1, preorderRight, index + 1, inorderRight)

        return node
    }

    const mapValueToInorderIndex = new Map()
    for (let i = 0; i < inorder.length; i++){
        mapValueToInorderIndex.set(inorder[i], i)
    }
    
    return construct(0, preorder.length - 1, 0, inorder.length - 1)

}{{< /code >}}

To find all root-to-leaf paths in a binary tree that sum to a target value, you can create a `results` array and then use recursion with a helper function. Run the helper function with the `root`, set a `sum` of 0, and a `valsSoFar` as an empty array. No `root` is the base case so then you can just return. Otherwise, create a `currentVals` array out of `valsSoFar` and the value at the current node, and create a `newSum` by adding `sum` to the value at the current node. Then, if current node is a leaf node (has no descendents), and the `newSum` is the target sum, you can push `currentVals` to `results`. Else recurse on the `.left` and `.right` of the current node.

{{< code language="javascript" title="[Path Sum II](https://leetcode.com/problems/path-sum-ii/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof root === "object" (binary tree node)
// typeof targetSum === "number"

const pathSum = (root, targetSum) => {

    const results = []
    
    const go = (root, sum = 0, valsSoFar = []) => {
        if(!root) return
        const currentVals = [...valsSoFar, root.val]
        const newSum = sum + root.val

        if(!root.left && !root.right && targetSum === newSum) results.push(currentVals)
        go(root.left, newSum, currentVals)
        go(root.right, newSum, currentVals)
    }
    
    go(root)
    
    return results

}{{< /code >}}

How to find the maximum width of a binary tree? One solution involves depth-first search. Create an empty `leftmostLocations` array, then conduct a depth-first search starting with the `root` node, a `widthLocation` of 0, and a `depth` of 0. The base case is a null node, but otherwise, a helper function can update the `leftmostLocations` array with the current `widthLocation` at the `depth` index if the `depth` has never before been seen. You can then create an `indexNormalizedToLeftmost` but subtracting the leftmost location at the given depth from the `widthLocation` (which helps with large LeetCode test cases), update a `best` with `indexNormalizedToLeftmost+1` if appropriate, and run the helper on the `.left` and `.right` of the current `node`, also passing down the doubled `indexNormalizedToLeftmost` and the doubled `indexNormalizedToLeftmost` with a 1 added after the multiplication, respectively. The third argument for both depth-first search calls is simply `depth+1`.

{{< code language="javascript" title="[Maximum Width of a Binary Tree](https://leetcode.com/problems/maximum-width-of-binary-tree/) -- code adapted from [linfongi's code](https://leetcode.com/problems/maximum-width-of-binary-tree/solutions/154200/javascript-dfs-solution/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof root === "object" (binary tree node)

const widthOfBinaryTree = (root) => {
    
  const leftmostLocations = []
  let best = 0

  const dfs = (node, widthLocation, depth) => {
    if (!node) return
    if (depth === leftmostLocations.length) {
      leftmostLocations[depth] = widthLocation
    }
    
    const indexNormalizedToLeftmost = widthLocation - leftmostLocations[depth]
    best = Math.max(best, indexNormalizedToLeftmost + 1)
    dfs(node.left, indexNormalizedToLeftmost*2, depth + 1)
    dfs(node.right, indexNormalizedToLeftmost*2+1, depth + 1)
  }
  
  dfs(root, 0, 0)
  return best

}{{< /code >}}

To return the values of the nodes of a binary tree in zigzag order, that is, a first row left-to-right, followed by a second row right-to-left, and third row left-to-right, etc., you can perform a level-order traversal and keep track of the row number to reverse the values of every other row. If there is input, create a `results` array, fill a `row` array with the starting node, and declare an empty `nextRow` array. While `row` has a length, create a `values` array and loop the nodes in `row`, pushing every value seen to `values`, and pushing every `.left` and `.right` node seen, in that order, to `nextRow`. If the `results` array is an odd length, reverse the `values` array, and in either case, next push it to the `results` array. Then set `row` as `nextRow` and set `nextRow` as an empty array. This is the last part of the outer cycle. It continues if the new `row` contains any values.

{{< code language="javascript" title="[Binary Tree Zigzag Level Order Traversal](https://leetcode.com/problems/binary-tree-zigzag-level-order-traversal/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof root === "object" (binary tree node)

const zigzagLevelOrder = (root) => {
    if (!root) return []

    const results = []
    let row = [root]
    let nextRow = []

    while(row.length){

        let values = []
        
        for (let node of row){
            values.push(node.val)
            if (node.left) nextRow.push(node.left)
            if (node.right) nextRow.push(node.right)
        }
        if (results.length % 2) values = values.reverse()

        results.push(values)
        row = nextRow
        nextRow = []
    }

    return results
    
}{{< /code >}}

To find all possible ways a binary tree sums to a given number on a downwards path (no bending at a node), you can run a recursive call with default parameters for `sumsToHere`, which starts as an empty array, as well as a `result` count that starts at 0. If there is no node, return `result`, else push a 0 value to `sumsToHere` and loop the `sumsToHere` array. At every step, add the value at the current node, and then check if the new value at the `sumsToHere` location is equal to target value. If so, add 1 to the result. Next, run the parent recursively on the `.left` and `.right` of the current node, and add their values to `result`. Make sure to copy the `sumsToHere` array, rather than put it in directly. Finally, return `result`. 

{{< code language="javascript" title="[Path Sum III](https://leetcode.com/problems/path-sum-iii/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof root === "object" (binary tree node)
// targetSum === "number"

const pathSum = (root, targetSum, sumsToHere = [], result = 0) => {

        if(!root) return result

        sumsToHere.push(0)
        for(let i = 0; i < sumsToHere.length; i++){
            sumsToHere[i] += root.val
            if(sumsToHere[i] === targetSum) result++
        }

        result += pathSum(root.left, targetSum, sumsToHere.slice(), 0)
        result += pathSum(root.right, targetSum, sumsToHere.slice(), 0)

        return result
        
}{{< /code >}}

To return an array of nodes that all are a certain distance from a target node in a binary tree, first note that an empty root returns an empty array. Otherwise, you can traverse the array from the root until you find the `targetNode`, adding reverse `.parent` links at every step. Then you can run reursion from the `targetNode`, branching in the `.left`, `.right`, and `.parent` directions until either an end of the tree is reached, or a node of the desired distance from the `targetNode` is found. When such a node is found, you can push it to `results`.

{{< code language="javascript" title="[All Nodes Distance K in Binary Tree](https://leetcode.com/problems/all-nodes-distance-k-in-binary-tree/) -- code adapted from [fbecker11's code](https://leetcode.com/problems/all-nodes-distance-k-in-binary-tree/solutions/843575/javascript-dfs/)" expand="Show" collapse="Hide" isCollapsed="false" >}}

const distanceK = (root, target, k) => {
  if(!root) return []

  const addReverseLinksAndReturnTargetNode  = (root, parent) => {
        if(!root) return null
        root.parent = parent
        if(root === target){    
        return root 
        }    
        return addReverseLinksAndReturnTargetNode(root.left, root) ||
         addReverseLinksAndReturnTargetNode(root.right, root)    
  }

  const targetNode = addReverseLinksAndReturnTargetNode(root, null)  
  const results = []

  const getResults = (node, distance) => {
    if(!node || node.visited) return
    if(!distance){
        results.push(node.val)
        return
    }   
    node.visited = true
    getResults(node.left, distance-1)
    getResults(node.right, distance-1)
    getResults(node.parent, distance-1)
  }

  getResults(targetNode, k)
  return results

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

To find if a number exists in a matrix of ordered numbers such that each row is sorted in ascending order and rows that have greater indicies contain greater values, two rounds of binary search can be conducted. First, find the row that would contain the number by using the maximum and minimum row indicies as `yRight` and `yLeft`. Test if the value at the end of the row of a floored `mid` is less than the target to increase `yLeft` to `mid+1`, else `yRight` becomes `mid`. Once the candidate row has been determined, you can return `false` immediately if the target value is outside the bounds of the row. Else, conduct a second binary search on the row. If the search is narrowed down the a single candidate that is not the target value, return `false`, else return `true`.

{{< code language="javascript" title="[Search a 2D Matrix](https://leetcode.com/problems/search-a-2d-matrix/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof matrix === "object" (array of arrays of numbers)
// typeof target === "number"

const searchMatrix = (matrix, target) => {

    let yLeft = 0
    let yRight = matrix.length - 1

    while (yLeft < yRight){
        const mid = yLeft + Math.floor((yRight-yLeft)/2)
        if (matrix[mid][matrix[0].length-1] < target){
            yLeft = mid + 1
        } else {
            yRight = mid
        }
    }
    
    if (target > matrix[yLeft][matrix[0].length-1] || target < matrix[yLeft][0]) return false
    
    let xLeft = 0
    let xRight = matrix[0].length - 1

    while(xLeft < xRight){
        const mid = xLeft + Math.floor((xRight-xLeft)/2)
        if (matrix[yLeft][mid] < target){
            xLeft = mid + 1
        } else {
            xRight = mid
        }
    }

    return matrix[yLeft][xLeft] === target

}{{< /code >}}

# Graphs

A two-dimensional array can represent a screen of pixels. To represent the "flood fill" tool in a paint editor, identify the coordinates at which you wish to begin flood fill. If that location is already the desired color, stop the process. Otherwise, you can begin a depth-first search at that location, replacing its color with the target color, and generating recursive calls up, down, left, and right. In each of these recursive calls, if the coordinates are out of bounds or the color is not the color being overwritten, handle this base case by not recursing further from that point. Otherwise, change the color at the location and generate more recursive calls up, down, left, and right.

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
// typeof prerequisites === "object" (array of arrays with 2 numbers)

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

In order to count the number of islands in a grid (unique groups of "1"s connected up, down, left, or right to other "1"s), an approach involves iterating through all the values of the grid. When a '1' is found, increment a `count` variable, then run a `flood` function to check if the location is in bounds and a "1". If so, "sink" the space by assigning a "0", then do depth-first search and run `flood` up, down, left, and right. Every connected "1" should disappear the first time one is found in the looping.

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

How to find if a word exists in a grid of characters when letters of the word can be connected up, down, left, or right? You can iterate through every space on the board. For every space, run a helper function `go` taking the coordinates and a `letterIndex` variable starting at `0` as arguments. The `letterIndex` represents the index of the next letter in the target word that must match. Inside `go`, return `false` if the letter at the coordinates does not match the letter at the `letterIndex` of the word. Then, if we have reached the end of the word, return `true`. Otherwise, temporarily overwrite the coordinates on the grid with a nonletter character, and run recrsive calls of `go` up, down, left, and right on valid coordinates and the next `letterIndex`. If any of these return `true` the parent `go` function can also return `true`. Else restore the letter at the current grid coordinates and return `false`. This is backtracking--you only need one `true` path to return true overall, otherwise you can "pack up" the current path and try an adjacent path. If any `go` in the original grid iteration returns `true`, the overall function can return `true`, otherwise return `false`.

{{< code language="javascript" title="[Word Search](https://leetcode.com/problems/word-search/description/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof board === "object" (array of arrays of strings of 1 character)
// typeof word === "string"

const exist = (board, word) => {

  const directions = [[-1, 0], [1, 0], [0, 1], [0, -1]]
  const go = (i, j, letterIndex) => {
    if (board[i][j] !== word[letterIndex]) return false
    if (letterIndex === word.length - 1) return true

    board[i][j] = "."
    for (const [x, y] of directions) {
      const nextI = i + x
      const nextJ = j + y
      if (nextI >= 0 && nextI < board.length && nextJ >= 0 && nextJ < board[0].length) {
        if (go(nextI, nextJ, letterIndex + 1)) return true
      }
    }
    board[i][j] = word[letterIndex]
    return false
  }

  for (let i = 0; i < board.length; i++) {
    for (let j = 0; j < board[0].length; j++) {
      if (go(i, j, 0)) return true
    }
  }

  return false

}{{< /code >}}

To find all roots of trees of minimum height from a connected components graph with consistant connections that may be "bent" to start from any node, notice that a list of responses will always be the most "central." To solve, run a number of rounds where each round removes nodes that only have one connection. The last round of pruning should contain valid root nodes. Notice than whenever there are less than 3 nodes, every node can be a root node. Otherwise, create a `connectionCounts` array with length equal to the number of nodes, filled to start with 0s, and a `connectionLists` array of the same length filled with empty arrays. For each edge pair connecting two nodes, increment each node index by 1 in  `connectionCounts`, and push the opposite to each node index in `connectionLists`. Now, create another array `seen` with length equal to the number of nodes, and fill it with false, create a `seenCount` starting as 0, and an empty array as a `queue`. For every `connectionCount` of 1, flip the `seen` boolean to true at that location, increment `seenCount` by 1, and push the index to the `queue`. For the final part of the algorithm, repeatedly process the `queue`. Create a `nextQueue` at the start of each processing round, and loop the nodes in the queue. For each node, loop the `adjacent` nodes. Ignore the `adjacent` node if it has been `seen`. Otherwise, decrease its `connectionCount` by 1, then check if this value is 1. If so, it is now a leaf at the edge of the graph, so mark it as `seen`, increment the `seenCount`, and push it to the `nextQueue`. Once the `queue` is processed, if `seenCount` equals the total number of nodes, return `nextQueue`, else replace `queue` with `nextQueue` and start the next round of `queue` processing.

{{< code language="javascript" title="[Minimum Height Trees](https://leetcode.com/problems/minimum-height-trees/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof n === "number"
// typeof edges === "object" (array of arrays with 2 numbers)

const findMinHeightTrees = (n, edges) => {

    if (n < 3) return [...Array(n)].map((_,i)=> i)
    
    const connectionCounts = Array(n).fill(0)
    const connectionLists = [...Array(n)].map(() => [])
    
    for(let [node1, node2] of edges) {
        connectionCounts[node1]++
        connectionLists[node1].push(node2)
        connectionCounts[node2]++
        connectionLists[node2].push(node1)
    }
    
    const seen = Array(n).fill(false)
    let seenCount = 0
    let queue = []

    for(let i = 0; i < n; i++) {
        if(connectionCounts[i] === 1) {
            seen[i] = true
            seenCount++
            queue.push(i)
        }
    }
    
    while(true) {
        const nextQueue = []
        
        for(let node of queue) {
            for(let adjacent of connectionLists[node]) {
                if(seen[adjacent]) continue
                if(--connectionCounts[adjacent] === 1) {
                    seen[adjacent] = true
                    seenCount++
                    nextQueue.push(adjacent)
                }
            }
        }
        if(seenCount === n) return nextQueue       
        queue = nextQueue
    }
}{{< /code >}}

To find a list of coordinates on a grid where a downwards slope (up, down, left, and right) can be found that reaches both the upper or left edges of the grid, as well as the lower or right (which can be thought of as water flowing to two oceans), consider depth-first search. Create two new grids the same shappe as the original grid, and fill both with `false`. Then loop the original array. At every location on the upper or left edge of the graph, begin a depth-first search process that places `true` at that location in one of the helper grids, and then continues planting `true` in every direction up, down, left, or right direction until an edge is reached, another `true` is reached, or a value in the given direction on the original grid is less than the original gird value at the location that is trying to be reached. (This would indicate the slope in the given direction is not sloping down towards the upper or left sides of the grid.) Fill the other helper grid by doing the indentical process spawning fom the bottom or the right side of the grid. Finally, add to a `results` array every location that is `true` on both helper grids, and return `results`.

{{< code language="javascript" title="[Pacific Atlantic Water Flow](https://leetcode.com/problems/pacific-atlantic-water-flow/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof grid === "object" (array of arrays of numbers)

const pacificAtlantic = (heights) => {

    const Atlantic = [...Array(heights.length)].map(() => new Array(heights[0].length).fill(false))
    const Pacific = [...Array(heights.length)].map(() => new Array(heights[0].length).fill(false))
    const directions = [[1, 0], [-1, 0], [0, -1], [0, 1]]

    const dfs = (i, j, seen) => {
        seen[i][j] = true
        for (let direction of directions) {
            const newI = i + direction[0]
            const newJ = j + direction[1]
            if (newI < 0 || newJ < 0 || newI === heights.length || newJ === heights[0].length || seen[newI][newJ]) continue
            if (heights[newI][newJ] >= heights[i][j]) {
                dfs(newI, newJ, seen)
            }
        }
    }
    
    for (let i = 0; i < heights.length; i++) {
        for (let j = 0; j < heights[0].length; j++) {
            if (i === 0 || j === 0) {
                dfs(i, j, Pacific)
            }
            if (i === heights.length - 1 || j === heights[0].length - 1) {
                dfs(i, j, Atlantic)
            }
        }
    }
    const results = []
    for (let i = 0; i < heights.length; i++) {
        for (let j = 0; j < heights[0].length; j++) {
            if (Pacific[i][j] && Atlantic[i][j]) {
                results.push([i, j])
            }
        }
    }
    return results

}{{< /code >}}

In a grid that contains a start location, food, empty spaces, and obstacles, what is the shortest number of steps to any food cell? You can use breadth-first search starting at the start location and pushing new legal adjacent locations (not off the board or previously seen) until a food is seen. If you keep a counter of number of steps away from the origin, you can return this when you reach the food symbol. If you never do you can return `-1`.

{{< code language="javascript" title="[Shortest Path to Get Food](https://leetcode.com/problems/shortest-path-to-get-food/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof grid === "object" (array of arrays of 1 character)

const getFood = (grid) => {

    const queue = []
    
    for(let row = 0; row < grid.length; row++) {
        for(let column = 0; column < grid[0].length; column++) {
            if(grid[row][column] === "*"){
                queue.push([row, column, 0])
                break
            }
        }
    }

    const directions = [[-1, 0], [0, -1], [1, 0], [0, 1]]
    const seen = new Set()
    
    while(queue.length) {
        let [row, column, distance] = queue.shift()
        if(grid[row][column] === "#") return distance
        const lookup =`${row}+${column}`
        if(seen.has(lookup) continue
        seen.add(lookup)
                
        for(let [x, y] of directions) {
            let newRow = x + row;
            let newColumn = y + column;
            
            if(newRow >= 0 && newRow < grid.length && newColumn >= 0 &&
            newColumn < grid[0].length && grid[newRow][newColumn] !== "X") {
                    queue.push([newRow, newColumn, distance + 1])
            }
        }
    }

    return -1

}{{< /code >}}

Given a list of edges (connected node pairs), does the collection make up a valid tree? In order to determine this, you can turn the edge list into an `adjacencyList`, and then run a depth-first search on the `adjacencyList`, starting from an arbitrary node. Add each node to a `seen` set as you visit it, and make sure not to backtrack as you proceed along a depth-first search. If ever a duplicate node is detected, return `false`, and if at the end of the traversal, the number of nodes `seen` is not equal to the number of nodes detected (indicateing split graphs) return `false`. Else return `true`.

{{< code language="javascript" title="[Graph Valid Tree](https://leetcode.com/problems/graph-valid-tree/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof n === "number"
// typeof edges === "object" (array of arrays with 2 numbers)

const validTree = (n, edges) => {

    const adjacencyList = [...Array(n)].map(() => [])
    const seen = new Set()

    for (let edge of edges){
        const [node1, node2] = edge
        adjacencyList[node1].push(node2)
        adjacencyList[node2].push(node1)
    }

    const dfs = (current, last) => {
        if(seen.has(current)) return false
        seen.add(current)

        for(let next of adjacencyList[current]){
            if(next === last) continue
            if(!dfs(next, current)) return false
        }
        return true
    }

    if (!dfs(0, -1)) return false

    return n === seen.size

}{{< /code >}}

How to finish all courses given an array of their prerequisites, if this is possible? You can first construct an `adjacencyList` using prerequisites as keys and all courses that are a direct requirement of that `prerequisite` pushed to an array that is the key value. As the `adjacencyList` is being constructed, build up an `indegrees` array the size of the number of nodes, increasing the indegree by 1 of any direct consequent `course` whenever a new `prerequisite` is detected. Then, take all courses with an indegree of 0 and add them to a `queue`. Process the `queue` in a breadth-first search manner, repeatedly shifting out the top `course` from the queue, subtracting one from the indegree of any course it points to, and then adding to the `queue` any courses that now have an indegree of zero (for a later step in the breadth-first search). Finally, push the `queue` course being processed to the `result`, and move on to the next `course` in the `queue`, if any. If at the end not all nodes are processed, you can return an empty array to indiate not all courses can be reached. Otherwise, return `result`.

{{< code language="javascript" title="[Course Schedule II](https://leetcode.com/problems/course-schedule-ii/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof numCourses === "number"
// typeof prerequisites === "object" (array of arrays of 2 numbers)

const findOrder = (numCourses, prerequisites) => {

  const adjacencyList = new Map()
  const indegrees = new Array(numCourses).fill(0)

  for (const [course, prerequisite] of prerequisites) {
    if (!adjacencyList.has(prerequisite)) adjacencyList.set(prerequisite, [])
    adjacencyList.get(prerequisite).push(course)
    indegrees[course]++
  }

  const queue = []
  for (let i = 0; i < indegrees.length; i++) {
    if (!indegrees[i]) queue.push(i)
  }

  const result = []
  while (queue.length) {
    const course = queue.shift()
    if (adjacencyList.has(course)) {
      for (const nextCourse of adjacencyList.get(course)) {
        indegrees[nextCourse]--
        if (!indegrees[nextCourse]) queue.push(nextCourse)
      }
    }
    result.push(course)
  }

  return numCourses === result.length ? result : []

}{{< /code >}}

To count the number of connected components in a undirected graph, one method involves converting connections or edges into an `adjacencyList` that maps out every connection between two nodes. Then every node on the `adjacencyList` can be iterated. If the node has been `visited` it can be passed over, but if it has not yet been `visited`, a `result` counter starting at 0 can increase by 1. Then, if the `result` counter incremented, perform a depth-first search starting at the iterated node and continuing to each node that can be reached from it through any number of `adjacencyList` connections that has not yet been `visited`. Mark each as `visited` and only stop (base case) on a node that has already been `visited` to prevent infinite loops. The depth-first search will allow only the first visit to a new connected component to add to the `result` tally.

{{< code language="javascript" title="[Number of Connected Components in a Undirected Graph](https://leetcode.com/problems/number-of-connected-components-in-an-undirected-graph/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof n === "number"
// typeof edges === "object" (array of arrays with 2 numbers)

const countComponents = (n, edges) => {

  const adjacencyList = Array(n).fill().map(r => [])
  const visited = new Set()
  
  for (let [node1, node2] of edges) {
    adjacencyList[node1].push(node2)
    adjacencyList[node2].push(node1)
  }
  
  const dfs = (node) => {
    if (visited.has(node)) return
    visited.add(node)
    for (let nextNode of adjacencyList[node]) {
      dfs(nextNode)
    }
  }

  let result = 0

  for (let i = 0; i < n; i++) {
    if (visited.has(i)) continue
    result++
    dfs(i)
  }

  return result

}{{< /code >}}

To determine how many moves to takes to get from position `0,0` to position `x,y` on an infinite chess board, first you can choose to investigate absolute value of both x and y to take advantage of the problem's symmetry. Then, create a `queue` seeded with the origin point, and perform breadth-first search with a `nextQueue`. For every item processed from the `queue`'s front, if it is at the desired end position, return a `moves` counter that starts at 0. Else, consider the eight directions a knight can move -- changing x or y coordinates 1 space or 2 spaces, and then changing whichever coordinate remains whichever of 1 or 2 was not already chosen. If a position has already been `seen` or is below -2, it can be ignored, otherwise add the coordinates to the `nextQueue`. Once the `queue` is emptied, replace it with `nextQueue`, make `nextQueue` empty, and increase the `moves` counter by 1.

{{< code language="javascript" title="[Minimum Knight Moves](https://leetcode.com/problems/minimum-knight-moves/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof x === "number"
// typeof y === "number"

const minKnightMoves = (x, y) => {
    const absX = Math.abs(x)
    const absY = Math.abs(y)

    const directions = [[1,2],[2,1],[-1,-2],[-2,-1],[-1,2],[2,-1],[1,-2],[-2,1]]
    const seen = new Set()
    seen.add(`0-0`)

    let moves = 0
    let queue = [[0,0]]
    let nextQueue = []

    while(queue.length){
        const current = queue.shift()
        if(absX === current[0] && absY === current[1]) return distance
        for (let direction of directions){
            const newX = current[0] + direction[0]
            const newY = current[1] + direction[1]
            if(newX < -2 || newY < -2 || seen.has(`${newX}+${newY}`)){
              continue
            }
            seen.add(`${newX}+${newY}`)
            nextQueue.push([newX, newY])
        }
        if(!queue.length){
            moves++
            queue = nextQueue
            nextQueue = []
        }
    }

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

To find the Kth smallest element in a binary search tree, you can search the tree with an inorder depth-first search from left to right, and find the Kth value. Every time you reach a value, decrease by 1 a counter equal to `k`, and then, at the location where you decremented, check if `k` has reached 0. If so, return the value at the node, and use a nonallowed value to return otherwise, making sure that only the correct value bubbles up to the final return statement.

{{< code language="javascript" title="[Kth Smallest Element in a BST](https://leetcode.com/problems/kth-smallest-element-in-a-bst/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof root === "object" (binary search tree node)
// typeof k === "number"

const kthSmallest = (root, k) => {
    const dfs = (node) => {
        if(!node) return -1

        const leftResult = dfs(node.left)
        if (leftResult > -1) return leftResult

        if (--k === 0) return node.val

        const rightResult = dfs(node.right)
        if (rightResult > -1) return rightResult

        return -1
    }
    return dfs(root)

}{{< /code >}}

To find the inorder successor of a node in a binary search tree, there are two basic cases. First, if the node for which the successor is being searched has a `.right`, the higher parts of the tree do not need to be considered, and the result is one `.right` followed by as many `.left` as are available. Otherwise, you can declare a `successor` variable and set it to null, then navigate through nodes starting at the root as long as the active node is not the node for which the successor is being considered. To do this, at every step in this section option for the algorithm, if the value at the current node is greater than the node for which the `successor` is trying to be found, set `successor` to the active node and then the active node to the `.left`. Otherwise, if a `.right` is available, you can go to the `.right` without moving the `successor` (because doing that would put the `successor` behind the node searched for, not ahead of it). In a case where there is no further `.right`, but the value is still less, not only do you not have to move the `successor`, but you can also `break`.

{{< code language="javascript" title="[Inorder Successor in BST](https://leetcode.com/problems/inorder-successor-in-bst/) -- code adapted from [chihungyu1116's code and explanation](https://github.com/chihungyu1116/leetcode-javascript/blob/master/285%20Inorder%20Successor%20in%20BST.js)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof root === "object" (binary search tree node)
// typeof p === "object" (binary search tree node)

const inorderSuccessor = (root, p) => {

    if(p.right){
        p = p.right
        while(p.left) p = p.left
        return p
    }

    const successor = null

    while(root !== p){
        if (root.val > p.val){
            successor = root
            root = root.left
        } else if (root.right){
            root = root.right
        } else {
            break
        }
    }

    return successor

}{{< /code >}}

# Hash Tables

Whether one group of characters in a string is completely included in a different string can be described as being able to cut letters out of a magazine for a ransom note. If the only characters that can appear are lower case letters, then these can be added by character code into an array from the "magazine" string, and then what appears in the "note" string can be subtracted. If any count in the array goes negative, the ransom note cannot be completed.  

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

If someone can climb one or two steps at a time, how many ways can they get to the top? Dynamic programming helps recognize that the number of ways to reach any given step acts as a multiplier on the ways to reach steps further up. A linear solution involves noticing the one way of getting to the 0th step, adding the number of ways to get to the 0th step to the 1st and 2nd steps, then "stepping" from the 1st step to the 2nd and 3rd steps, and adding counts so on and so on. Only three locations need to be available at a time.

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

{{< code language="javascript" title="[Coin Change](https://leetcode.com/problems/coin-change/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
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

Can a non-empty array containing only positive integers be split into two subsets that are equal? To determine, you can collect the total `sum` and immediately retrun false if that `sum` is an odd number. Else, you can use dynamic programming to see if there is some combination of numbers that sums to half the total sum. Make a `dp` array equal to one more than the `sum`, seed the 0 location (representing a sum of 0) with `true`, and for each number in the original array, loop the `dp` array from right to left, checking if the current value location minus the value being considered is `true`, and if so, updating the current value location to `true`. Looping from left to right would reuse numbers. At the end, return the boolean status of the right end of the array.

{{< code language="javascript" title="[Partition Equal Subset Sum](https://leetcode.com/problems/partition-equal-subset-sum/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof nums === "object" (array of numbers)

canPartition = (nums) => {
    
    const sum = nums.reduce((a,b)=>a+b)

    if(sum % 2) return false
    const dp = new Array(sum/2 + 1).fill(false)
    dp[0] = true
    
    for(let num of nums) {
        for(let i = sum; i >= num; i--){
            dp[i] = dp[i] || dp[i - num]
        }
    }
    
    return dp[sum/2]

}{{< /code >}}

How many ways are there for a robot in an array, that can only go to the right and down, to get from the upper-left to the lower-right? This problem can be solved with the help of a `dp` array. Create it as the length of a row, `n`, and fill all locations with 0, but update the 0th index with 1, to show there is 1 way for the robot to get to (be in) the upper-left. Then, loop for the number of rows `m`, and in each of those loops, create a `nextDP` array and loop each location in the row. For each location in the inner loop, update that location in `nextDP` as the sum of any values in locations just above (just above is represented by the `dp` array) and just to the left. At the end of each inner loop, replace `dp` with `nextDP`. Return the value at the rightmost index in `dp` at the end of the outer loop. Note that for the first inner loop, `dp` represents a theoretical `m-1` row that allows the 0th location in the 0th row to be seeded correctly.

{{< code language="javascript" title="[Unique Paths](https://leetcode.com/problems/unique-paths/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof m === "number"
// typeof n === "number"

const uniquePaths = (m, n) => {

    let dp = new Array(n).fill(0)
    dp[0] = 1

    for (let i = 0; i < m; i++){
        let nextDP = []
        for (let j = 0; j < n; j++){
            let up = dp[j]
            let left = j ? nextDP[j-1] : 0
            nextDP[j] = up+left
        }
        dp = nextDP
    }

    return dp[dp.length-1]

}{{< /code >}}

To find the best maximum sum of nonadjacent values in an array (which in LeetCode is themed stealing from nonadjacent houses), consider a linear approach that uses two helper variables, a `back1Best` and a `back2Best`. Initialize both to 0, and iterate through the array from left to right. At each step, save a copy of `back1Best`, update `back1Best` to be the max of itself or the current value plus `back2Best`, then update `back2Best` to be the saved copy of the old `back1Best`. At the end of the array iteration, `back1Best` will contain the maximum sum.

{{< code language="javascript" title="[House Robber](https://leetcode.com/problems/house-robber/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
typeof nums === "object" (array of numbers)

const rob = (nums) => {

    let back1best = 0
    let back2best = 0

    for (let house of nums){
        const temp = back1best
        back1best = Math.max(house + back2best, back1best)
        back2best = temp
    }
    return back1best

}{{< /code >}}

To find the maximum product in any subarray (contiguous series) of an array, you can use three helpers, a `minimum`, a `maximum`, and a `best`. Intialize each to the first value, then loop the remainder, at every step updating the `minimum` and `maximum` to be the new `maximum` or `minimum` of the current value in the loop, the value indicated in the loop times the `maximum`, or the value indicated in the loop times the `minimum`. Then update `best`. The `minimum` is saved because negative numbers multiplied turn positive.

{{< code language="javascript" title="[Maximum Product Subarray](https://leetcode.com/problems/maximum-product-subarray/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof nums === "object" (array of numbers)

const maxProduct = (nums) => {
    
    let maximum = nums[0]
    let minimum = nums[0]
    let best = nums[0]
    
    for(let i = 1; i < nums.length; i++){
        const maximumCopy = maximum
        maximum = Math.max(nums[i], maximum*nums[i], minimum*nums[i])
        minimum = Math.min(nums[i], maximumCopy*nums[i], minimum*nums[i])
        best = Math.max(best, maximum)
    }
    return best

}{{< /code >}}

To find the longest strictly increasing subsequence in an array (numbers have to be in order, but do not need to be adjacent), binary search can help insert the `lowestValueReachingCount` into a helper array. To do this, for every number in the input array, declare a `left` of 0 and a `right` equal to the current `best` length (starting at 0). Then, while `left` is not `right` create a while loop, generating a `mid` between `left` and `right` at every round. If the value at index `mid` in `lowestValueReachingCount` is less than the number being investigated in the outer loop, this means the longest strictly increasing subsequence ending at the original array number being investigated is at least equal to `mid+1`, so set the new `left` to `mid+1` and continue. Else, `right` can be `mid`. When the `left` and `right` pointers meet, update the location in `lowestValueReachingCount` with the number indicated in the outer loop (because if it were not the `lowestValueReachingCount`, the `mid+1` process would have moved the index up), and update `best` with the location index found in the binary search, plus 1, if appropriate.

{{< code language="javascript" title="[Maximum Product Subarray](https://leetcode.com/problems/longest-increasing-subsequence/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof nums === "object" (array of numbers)

const lengthOfLIS = (nums) => {

    const lowestValueReachingCount = new Array(nums.length).fill(0)
    let best = 0
    
    for (let num of nums){
        let left = 0
        let right = best
        while(left !== right){
            const mid = left + Math.floor((right-left)/2)
            if (lowestValueReachingCount[mid] < num) left = mid + 1
            else right = mid
        }
        lowestValueReachingCount[right] = num
        best = Math.max(best,right+1)
    }
    
    return best

}{{< /code >}}

Given a starting position at the 0th index of an array, if the value at any position indicates the maximum range one can "hop" further to the right, can any series of hops reach the final value? In order to solve, you can linearly traverse the array starting at the 1st index, keeping track of a maximum `range`, which starts at the value at the 0th index. If at any point the index of the traversal is larger than the index given by the `range`, return `false`, otherwise update the `range` to be the best of itself or the index plus the value at the index. If the entire array loop is completed without returning `false`, you can return `true`.

{{< code language="javascript" title="[Jump Game](https://leetcode.com/problems/jump-game/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof nums === "object" (array of numbers)

const canJump = (nums) => {

    let range = nums[0]
    
    for(let i = 1; i < nums.length; i++){
        if(range < i) return false

        range = Math.max(range, i + nums[i])
    }
    return true

}{{< /code >}}

To find the maximum area of a square all of "1" inside a 2D input array, you can create a 2D array for dynamic programming with length and width both equal to that of the input array plus 1. Fill all locations with 0. Then loop through the input matrix, and every time you find a "1", replace the equivalent location in the dynamic programming array (which should be 1 ahead in both the x and y dimensions) with the minimum of its left, upper, and upper-left location plus 1. This is a candidate for the `best` square, so update a `best` variable with the value assigned to the dynamic programming array location, if appropriate. At the end of reviewing the input matrix, return the square of `best`. 

{{< code language="javascript" title="[Maximal Square](https://leetcode.com/problems/maximal-square/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof matrix === "object" (array of arrays of numbers)

const maximalSquare = (matrix) => {
    const dp = [...Array(matrix.length+1)].map(_ => [...Array(matrix[0].length+1)].fill(0))
    
    let best = 0
    for (let i = 0; i < matrix.length; i++) {
        for (let j = 0; j < matrix[0].length; j++) {
            if (matrix[i][j] === "1") {
                const last = Math.min(dp[i][j], dp[i][j+1], dp[i+1][j])
                dp[i+1][j+1] = last + 1
                best = Math.max(best, dp[i+1][j+1])
            }
        }
    }
    
    return best ** 2

}{{< /code >}}

To determine the number of ways a string of numbers can be decoded as letters, where "A" is "1" and so on until "Z" is "26", you can notice that if the string has no length or the first digit is "0", there are 0 possible mappings. Otherwise, you can create a small `dp` array with length 2, and both digits initially filled with 1. The first 1 gives a hypothetical number of ways for the -1 digit of the string to be decoded (useful for a later loop), while the second 1 gives the number of ways for the 0 index digit of the string to be decoded (since we know it is not 0). Next, loop the string, starting at the 1st index. At every step, create a `next` variable starting at 0, and add the value of the 0 index of the `dp` array to `next` if the last and current indicies of the input array together range from "10" to "26". Because only a single 2-digit number is possible, this means you can add to `next` the value/number of combinations from two indicies ago (this is how the hypothetical 1 makes sense). Then, if the current index of the input array is not "0", you can add to `next` the value/number of combinations from one index ago -- "0" does not add anything because it is the only digit that must combine with the previous digit to count, so does not add any possibilities that rely on the previous digit being an ending digit. At the end of each step of the loop, change the 0 index of the `dp` array to the value in the 1 index, and then use `next` to update the 1 index. At the end of the loop, return the value in the 1 index.

{{< code language="javascript" title="[Decode Ways](https://leetcode.com/problems/decode-ways/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof s === "string"

const numDecodings =(s)=> {

  if (!s.length || s[0] === "0") return 0

  const dp = [1, 1]
    
  for (let i = 1; i < s.length; i++) {
      let next = 0
    if (s[i-1] === '1' || s[i-1] === '2' && s[i] <= '6') {
      next += dp[0]
    }
    if (s[i] !== '0') {
      next += dp[1]
    }
    dp[0] = dp[1]
    dp[1] = next
  }

  return dp[1]

}{{< /code >}}

# Binary

To sum two binary strings, set indices to point to the rightmost (lowest) element in both. Set a `carry` variable to 0. Loop until both indices have reached the leftmost end of their strings. At each step in the loop, add the numerical sum of valid index values to `carry` (ignoring any index that is less than 0). Next, and also in each loop, push `carry%2` to a `result` array, and reset `carry` to its own base 2 overflow. Once both strings have been looped, add any final value in `carry` to the `result` array, if a final value exists, then reverse the array and join it into the result string.

{{< code language="javascript" title="[Add Binary](https://leetcode.com/problems/add-binary/) -- code adapted from [hi-malik's code and explanation](https://leetcode.com/problems/add-binary/solutions/1679423/well-detailed-explaination-java-c-python-easy-for-mind-to-accept-it/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
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

}{{< /code >}}

To find the single duplicate number in an array of otherwise consecutive numbers starting at 1, you can use a `fast` and a `slow` pointer. Assign `slow` to start as the value of the 0th index (which itself is an index in the array), and `fast` to the value at the index equal to the value at the 0th index. Then, continue updating `fast` and `slow` at this pace until `fast` is equal to `slow`. This means `fast` has caught up to `slow` in the circular pattern. To figure out what number is repeated, or, in other words, what number is indicated from two other indicies, restart one of the pointers at 0 and move each pointer to the value indicated at the index in tandem until they point to the same number. This is the result. This math works because to get to the first match, the `slow` pointer had to travel to the location indicated by two values, then some further distance, while the `fast` pointer had to travel both of these, plus a distance equal to the distance from the start to the location indicated by two values, plus the "further" distance again. (The `fast` must travel twice as far as `slow`.) Restarting one pointer to the 0th index and then moving them both forward step by step until they match means that both will meet the location indicated by two values (because both pointers are at the start of the phase that will end at the desired value).

{{< code language="javascript" title="[Find the Duplicate Number](https://leetcode.com/problems/find-the-duplicate-number/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof nums === "object" (array of numbers)

const findDuplicate = (nums) => {

    let fast = nums[nums[0]]
    let slow = nums[0]
    while (fast !== slow){
        fast = nums[nums[fast]]
        slow = nums[slow]
    }
     
    fast = 0
    while (fast !== slow) {
        fast = nums[fast]
        slow = nums[slow]
    }
    
    return fast

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

To pick a random index from an input array, weighted by the integer values in the indicies (indicies with greater values have proportionally more share), you can save a pair of variables to `this`: the `total` of input array, and a `runningTotals` array of equal length to the input array. Fill each index of `runningTotals` with the sum of every value from that position to the left in the input array. In the selection method, generate a `random` integer from 0 up to but not including the `total`, then conduct binary search, with a floored `mid` index, moving the `left` pointer to `mid+1` each time the `random` value is greater than the value at `mid`.  Else you can move `right` to `mid`.

{{< code language="javascript" title="[Random Pick with Weight](https://leetcode.com/problems/random-pick-with-weight/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof w === "object" (array of numbers)

const Solution = function(w) {
    this.total = 0
    this.runningTotals = []
    for(let value of w) {
        this.total += value
        this.runningTotals.push(this.total)
    }
}

Solution.prototype.pickIndex = function() {
    const random = Math.random() * this.total
    let left = 0
    let right = this.runningTotals.length-1
    while(left < right){
        const mid = left + Math.floor((right-left)/2)
        if(random > this.runningTotals[mid]){
            left = mid + 1
        } else {
            right = mid
        }
    }
    return left
}{{< /code >}}

To write a function that raises a number to a power, you can use a recursive algorithm. Notice that any power of 0 returns a 1. Declare a temporary `result` as 1 and and an `absPower` as the absolute value of the power. If `absPower` is odd you can multiply the `result` by the number being raised, and reduce the `absPower` by 1. Then multiply `result` by a recusive call of the function that squares the number being raised and uses half the `absPower` as the power. Finally, if the original power was negative, return 1 over the `result`, else return the `result`. 

{{< code language="javascript" title="[Pow(x, n)](https://leetcode.com/problems/powx-n/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof x === "number"
// typeof n === "number"

const myPow = (x, n) => {

    if (n===0) return 1
    
    let result = 1
    let absPower = Math.abs(n)

    if(absPower%2){
        result *= x
        absPower--
    }

    result *= myPow(x**2, absPower/2)
    
    if (n < 0) return 1/result
    return result

}{{< /code >}}

To reverse an integer as if it is a signed 32-bit value, you can start a `reverse` variable at 0. Declare a `clamp` at 2 to the 31 power. Then, while the input value still exists, multiply reverse by 10, add the value of the 0s place of the input value to `reverse`, and check if the `reverse` value is less than negative `clamp` or greater than `clamp-1`. Finally, divide the input value by 10 and remove any decimal. Once the input value no longer exists, return `reverse`.

{{< code language="javascript" title="[Reverse Integer](https://leetcode.com/problems/reverse-integer/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof x === "number"

const reverse = (x) => {

  let reverse = 0
  const clamp = Math.pow(2, 31)

  while (x) {
    reverse *= 10
    reverse += x % 10
    if ( reverse < -clamp || reverse > clamp - 1) return 0
    if (x > 0) x = Math.floor(x/10)
    else x = Math.ceil(x/10)
  }

  return reverse

}{{< /code >}}

# Heaps

To find the k closest points to the origin, sort the points by the sum of the x distance squared and the y distance squared from the origin. No need to take square root because relative order will be the same either way. Return the k closest points. This algorithm can be done with a priority queue (which could always keep the lowest point on top based on its distance) but this is not a native data struture in JavaScript.

{{< code language="javascript" title="[K Closest Points to Origin](https://leetcode.com/problems/k-closest-points-to-origin/description/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof points === "object" (array of arrays with 2 numbers)
// typeof k === "number"

const kClosest=(points,k)=>points.map(p=>[...p,p[0]**2+p[1]**2]).sort((a,b)=>a[2]-b[2]).slice(0,k).map(p=>[p[0],p[1]])

{{< /code >}}

If there are some number of tasks, assigned letters that can be duplicates, how long will it take to complete all tasks if tasks assigned the same letter must be separated by some `cooldown` number of spaces? To solve, you can fill a `letterCounts` array, sort this from greatest to least, and note the `countOfMaximumSizeTypes` (in other words, how many letters are in first place for maximum count). The remainder of the algorithm is just math. Note that the count of the most commmon letter minus 1 represents the number of full cooldown rounds that must take place, where the size of a cooldown round is `cooldown+1` (because the item being cooled down must be included. Added to the product, because the last instance of the most common letter must also be included in the time, is simply the `countOfMaximumSizeTypes`, which represents a final partial cooldown round. However, if the number of tasks is greater than the product plus sum, simply return the number of tasks.

{{< code language="javascript" title="[Task Scheduler](https://leetcode.com/problems/task-scheduler/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof tasks === "object" (array of 1 character strings)
// typeof cooldown === "number"

const leastInterval = (tasks, cooldown) => {

  const letterCounts = new Array(26).fill(0)
  for (let task of tasks) {
    letterCounts[task.charCodeAt(0) - 65]++
  }
  letterCounts.sort((a,b)=>b-a)
  const countOfMaximumSizeTypes = letterCounts.filter((value ,index, array)=>value===array[0]).length

  return Math.max((letterCounts[0] - 1) * (cooldown + 1) + countOfMaximumSizeTypes, tasks.length)
}{{< /code >}}

To find some number of most frequent strings in an array, sorted by frequency from most common and then by lexicographical order, you can start by creating a `counts` object that stores the count of a given word. Then you can sort the unique words first by their count from greatest to smallest, and then as a tiebreaker, by their lexicographical order (the default `sort`). Then return however many from the front of this list is desired.

{{< code language="javascript" title="[Top K Frequent Words](https://leetcode.com/problems/top-k-frequent-words/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof words === "object" (array of strings)
// typeof k === "number"

const topKFrequent = (words, k) => {
    
    const counts = {}
    for (let word of words) {
        counts[word] = counts[word] + 1 || 1
    }

    return Object.keys(counts).sort((a,b) => { 
        return counts[b] === counts[a] ? ( a > b || -1 ) : counts[b] - counts[a]
    }).slice(0, k)

}{{< /code >}}

To find the `k` closest elements to a given value in a sorted array of numbers, with the smaller range being chosen for a tiebreaker, one solution method is binary search. Create `left` and `right` index pointers with `left` being `k-1` and `right` being the rightmost index of the array. Then, while `left` is less than `right`, create a floored `mid`, and check if the target value minus the value at `mid-k+1` is greater than the value at `mid+1` minus the target value. Because we are only interested in `k` values, not `k`, and given that the values are sorted, the result must be adjacent numbers, one of the two compared numbers must not be included in the result. We can set `left` to `mid+1` if the test passes because that means the value at the low end has a greater "delta" with the target value, else we can set `right` to `mid`, which meets the condition of the tiebreaker. When the binary search resolves, return the `k` points ending at `mid`. 

{{< code language="javascript" title="[Find K Closest Elements](https://leetcode.com/problems/find-k-closest-elements/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof arr === "object" (array of numbers)
// typeof k === "number"
// typeof x === "number"

const findClosestElements = (arr, k, x) => {
    
  let left = k - 1
  let right = arr.length - 1

  while (left < right) {
    let mid = left + Math.floor((right-left)/2)
    if (x - arr[mid-k+1] > arr[mid+1] - x) {
      left = mid + 1
    } else {
      right = mid
    }
  }
  return arr.slice(left - k + 1, left + 1)

}{{< /code >}}

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

How to determine if a string is composed of any combination (including repeats) of words in a `wordDict`? Create a `cache` map, and then run a helper function `go` which takes as an argument the string to be tested. If the string is empty, return `true`, and if the string in `go` is in the `cache`, return whatever boolean the cache has. Else, slice at every location in the string argument starting with the 1st index. When the string up to the index is in the `wordDict` and the remainder of the string returns `true` from `go`, set the `cache` for the string to `true` and return `true`. Else set the `cache` for the string to `false` and return `false`.

{{< code language="javascript" title="[Word Break](https://leetcode.com/problems/word-break/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof s === "string"
// typeof wordDict === "object" (array of strings)

const wordBreak = (s, wordDict) => {

    const cache = new Map()

    const go = (s) => {
        if (cache.has(s)) return cache.get(s)
        if (!s) return true
        for (let i = 1; i <= s.length; i++) {
            if (
                wordDict.includes(s.slice(0,i)) &&
                go(s.slice(i))
            ){
                cache.set(s, true)
                return true
            }
        }
        cache.set(s, false)
        return false
    }

    return go(s)

}{{< /code >}}

To create a trie that can search for words that include `.` characters that can match any letter, add words as in [Implement Trie (Prefix Tree)](https://leetcode.com/problems/implement-trie-prefix-tree/description/) but modify the search. At the length of a detected word plus 1, as before return the boolean value of if there is a `.end`, but wrap this as a depth-first recursive search in a helper function. After the `.length` check, proceed down a level as normal if the character at that location in the input word is not a `.`, otherwise loop the characters at the trie location and recursively call `go` for every character, returning `true` if any return `true`, and making sure to gracefully fail if `end` becomes an argument for `go`. Return `false` if the pattern is never found.

{{< code language="javascript" title="[Design Add and Search Words Data Structure](https://leetcode.com/problems/design-add-and-search-words-data-structure/)" expand="Show" collapse="Hide" isCollapsed="false" >}}

const WordDictionary = function() {
    this.trie = {}
}

WordDictionary.prototype.addWord = function(word) {
    let trie = this.trie
    for (let letter of word) {
        !trie[letter] && ( trie[letter] = {} )
        trie = trie[letter]
    }
    trie.end = true
}

WordDictionary.prototype.search = function(word) {
    
    const go = (trie, depth) => {
        if (word.length === depth) return !!trie.end
        if (word[depth] !== '.'){
            return !!(trie[word[depth]] && go(trie[word[depth]], depth+1))
        } else {
            const characterSteps = Object.keys(trie)
            for (let characterStep of characterSteps){
                if (go(trie[characterStep], depth+1)) return true
            }
            return false
        }
    }
    
    return go(this.trie, 0)

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

How to find all subsets of an array? Create an array `results` and fill it with a single empty array. Then loop the array you wish to find all subsets for. For each value, create an empty `next` array, and loop the contents of `results`. The `results` array should have every possible subset up to but not including the currently reviewed element. Therefore, push the concatenation of every array in `results` with the currently reviewed element into `next`, and when the inner loop is done, concatenate `results` and `next`.

{{< code language="javascript" title="[Subsets](https://leetcode.com/problems/subsets/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof nums === "object" (array of numbers)

const subsets = (nums) => {

    let results = [[]]

    for(let num of nums){
        const next = []
        for(let array of results){
            next.push(array.concat(num))
        }
        results = results.concat(next)
    }

    return results

}{{< /code >}}

To find all letter combinations of a phone number, you can return an empty array if there is no number, else seed a new `results` array with an empty string. Create a `letterLookup` mapping digits to the letters they might represent, then loop through each `digit` of the input number. For each digit, create a `next` array, and loop through each `possibleLetterCombination` stored in `results`. Within each `possibleLetterCombination`, for each `newLetter` returned by the `letterLookup` for the `digit`, push `possibleLetterCombination + newLetter` to `next`. When the `results` array is looped through (one of every valid `newLetter` has been added to every `possibleLetterCombination` in `results`, so `next` should have about three times more combinations than `results`), replace `results` with `next`. When all `digits` are exhausted `results` should contain the answer.

{{< code language="javascript" title="[Letter Combinations of a Phone Number](https://leetcode.com/problems/letter-combinations-of-a-phone-number/description/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof digits === "string"

const letterCombinations = (digits) => {

    if (!digits) return []
    let results = [""]
    const letterLookup = {
        "2":"abc",
        "3":"def",
        "4":"ghi",
        "5":"jkl",
        "6":"mno",
        "7":"pqrs",
        "8":"tuv",
        "9":"wxyz"
    }
    for (let digit of digits){
        const letters = letterLookup[digit]
        const next = []
        for (let possibleLetterCombination of results){
            for (let newLetter of letters){
                next.push(possibleLetterCombination + newLetter)
            }
        }
        results = next
    }

    return results

}{{< /code >}}

How to modify an array of integers such that the number increases to its next greatest permutation, or moves to the lowest permutation if it is on the highest? Note that the `upperReplacedDigit` that needs to move is the first number from left to right that is lower than a number to its right. If there is no such number, the permutation is at a maximum and the next permutation is simply the reverse. Otherwise, start again from the right (the lowest digit) and find the first number that is greater than the `upperReplacedDigit` but a lower digit. If no such number is found, again, this a sign the array is at a maximum and the next permutation is the reverse. Otherwise, swap the `upperReplacedDigit` with its `update`, and then reverse every digit lower than the index of the original `upperReplacedDigit`. This is because those numbers were in increasing order and we want to move them to their lowest-value permutation.

{{< code language="javascript" title="[Next Permutation](https://leetcode.com/problems/next-permutation/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof nums === "object" (array of numbers)

const nextPermutation = (nums) => {

    let upperReplacedDigit = nums.length - 1
    while(upperReplacedDigit && nums[upperReplacedDigit] <= nums[--upperReplacedDigit]){}
    
    let update = nums.length - 1
    while(nums[update] <= nums[upperReplacedDigit]){
        if(update === upperReplacedDigit) return nums.reverse()
        update--
    }

    [nums[upperReplacedDigit], nums[update]] = [nums[update], nums[upperReplacedDigit]]
    const reverse = nums.splice(upperReplacedDigit+1).reverse()
    nums.splice(upperReplacedDigit+1,0,...reverse)
}{{< /code >}}

To generate all strings of valid parentheses off of an input number, recursion can be used. One method involves running a helper function that keeps track of `openCount` and `closeCount` (both starting at 0), as well as a `builder` string (starting as empty). At each step in the helper function, immediately return if `openCount` is ever greater than the input number, and if both the `openCount` and `closeCount` are equal to the input number, push `builder` to `results` and return. Else run a recursive call of the helper function with one new `(` (noting this in the `openCount` and `builder` arguments), and if `closeCount` is less than `openCount`, run a recursive call of the helper function with one new `)` (noted by `closeCount` and `builder`).

{{< code language="javascript" title="[Generate Parentheses](https://leetcode.com/problems/generate-parentheses/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof n === "number"

const generateParenthesis = (n) => {

    const results = []

    const go = (openCount, closeCount, builder) => {
        if (n < openCount) return
        if (n === openCount && openCount === closeCount) return results.push(builder)
        go(openCount + 1, closeCount, builder + "(")
        if (closeCount < openCount) go(openCount, closeCount + 1, builder + ")")
    }

    go(0, 0, "")

    return results

}{{< /code >}}

# Matricies

How to return elements of a matrix in spiral order? (The top row left-to-right, continued by the left side going down, continued by the bottom row right-to-left, continued by the right side going up, continued by the second-to-top row left-to-right, etc.) If the matrix is empty, return an empty matrix. Then, create a `result` matrix, and start four pointers, `left`, `right`, `top`, and `bottom`, which indicate the edges of the matrix, inclusive, that still need to be investigated. While the size of the matrix has not been fully explored, step across, down, back, and up the array by using the pointers, pushing to `result`. Pull each pointer one step closer to the middle of the array after its fourth of the while loop is complete. Be careful not to move pointers in quarters where no elements are left to be pushed.

{{< code language="javascript" title="[Spiral Matrix](https://leetcode.com/problems/spiral-matrix/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof matrix === "object" (array of arrays of numbers)

const spiralOrder = (matrix) => {
    if (!matrix.length || !matrix[0].length) return [];
    
    const result = []
    let left = 0
    let right = matrix[0].length - 1
    let top = 0
    let bottom = matrix.length - 1
    let count = matrix.length * matrix[0].length
    
    while (true) {
        for (let i = left; i <= right; i++) {
            result.push(matrix[top][i])
            count--
        }
        top++

        for (let i = top; i <= bottom; i++) {
            result.push(matrix[i][right])
            count--
        }
        if(!count) break
        right--

        for (let i = right; i >= left; i--) {
            result.push(matrix[bottom][i])
            count--
        }
        bottom--

        for (let i = bottom; i >= top; i--) {
            result.push(matrix[i][left])
            count--
        }
        if(!count) break      
        left++
    }
    
    return result

}{{< /code >}}

To determine is a Sudoku board is valid, you can loop the board, filling a `seen` set as you go. For every location with a number, check and then add three strings to the set: a `row` location with the `value`, a `column` location with the `value`, and a 3x3 `box` location to the `value`. If any duplicate is found, immmediately return `false`, else you can return `true`.

{{< code language="javascript" title="[Valid Sudoku](https://leetcode.com/problems/valid-sudoku/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof board === "object" (array of arrays of 1 character strings)

const isValidSudoku = (board) => {

    const seen = new Set()

    for (let i = 0; i < 9; i++) {
      for (let j = 0; j < 9; j++) {
        const value = board[i][j]
        if(value != '.') {
            const row = `row ${i} ${value}`
            const column = `column ${j} ${value}`
            const box = `box ${Math.floor(i/3)} ${Math.floor(j/3)} ${value}`
        
            if(seen.has(row) || seen.has(column) || seen.has(box)) return false

            seen.add(row)
            seen.add(column)
            seen.add(box)

          }
      }
    }
    return true

}{{< /code >}}

To rotate a 2D square array in-place, 90 degrees to the right, you can notice that points can be moved in sets of four, which means that once a "triangle-like" quarter of the array starts cycles from all its points, the transformation is complete. To find the "triangle-like" array, create and inner an outer loop. The outer loop descends through every row of the input matrix (in a standard way), while the inner loop crosses rows by starting at the column index, and ending at 1 less than the column index, minus an additional value equal to the row. To understand why the inner loop starts and ends where it does, consider that the inner loop must end at least one step before the end of row because otherwise there would not be room for the start of the next clockwise side to start at a corner. The `-1` to both sides for each step into the matrix then makes sense because each step into the matrix removes the leftmost and the rightmost columns. Finally, for the rotation itself, save the value you find at the given location. Move into this position the value found at the `[sideLength-1-column][row]` of itself, and continue this pattern all four-step cycles are completed starting from every place the "triangle" has. Used the saved value to fill the last of the four positions.

{{< code language="javascript" title="[Rotate Image](https://leetcode.com/problems/rotate-image/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof matrix === "object" (array of arrays of numbers)

const rotate = (matrix) => {

    const sideLength = matrix.length
    for(let row = 0; row < sideLength; row++){ 
        for(let column = row; column < sideLength - 1 - row; column++){

            const startValue = matrix[row][column]

            matrix[row][column] = matrix[sideLength-1-column][row]
            matrix[sideLength-1-column][row] = matrix[sideLength-1-row][sideLength-1-column]
            matrix[sideLength-1-row][sideLength-1-column] = matrix[column][sideLength-1-row]
            matrix[column][sideLength-1-row] = startValue

        }  
    }
    
    return matrix

}{{< /code >}}

To set 0 to all rows and columns in a matrix that contain at least one 0, in-place, parse through every row or column and if a 0 is noted, mark every nonzero element in the row or column with an intermediate value like a "c". Then parse though whichever you did not first check, row or column, and if a 0 is noted, mark every nonzero element in the row or column with a 0. Then go through the matrix one final time and update every intermediate value to a 0 (it can no longer confuse the second scan).

{{< code language="javascript" title="[Rotate Image](https://leetcode.com/problems/rotate-image/)" expand="Show" collapse="Hide" isCollapsed="false" >}}
// typeof matrix === "object" (array of arrays of numbers)

const setZeroes = (matrix) => {
    for (let i = 0; i < matrix.length; i++){
        let clear = false
        for(let j = 0; j < matrix[0].length; j++){
            if (!matrix[i][j]){
                clear = true
                break
            }
        }
        if (!clear) continue
        for(let j = 0; j < matrix[0].length; j++){
           if(typeof matrix[i][j] === "number" && matrix[i][j]) matrix[i][j] = "c" 
        }
    }
    
    for (let j = 0; j < matrix[0].length; j++){
        let clear = false
        for(let i = 0; i < matrix.length; i++){
            if (!matrix[i][j]){
                clear = true
                break
            }        
        }
        if (!clear) continue
        for(let i = 0; i < matrix.length; i++){
           matrix[i][j] = 0
        }
    }
    
    
    for (let i = 0; i < matrix.length; i++){
        for(let j = 0; j < matrix[0].length; j++){
            if(matrix[i][j] === "c") matrix[i][j] = 0
        }
    }
    
    return matrix

}{{< /code >}}

# Queues

To design a hit counter that counts hits in the last 300 seconds, you can assign a function to a variable that contains a `map` under `this`. To add a hit, add a count to the second provided in the `map`, creating a new entry if that time has not before been seen. To look up hits, check 300 values starting at the second being checked and working backwards.

{{< code language="javascript" title="[Design Hit Counter](https://leetcode.com/problems/design-hit-counter/)" expand="Show" collapse="Hide" isCollapsed="false" >}}

const HitCounter = function () {
  this.map = new Map()
}

HitCounter.prototype.hit = function (timestamp) {
  if (!this.map.has(timestamp)){
    this.map.set(timestamp,1)
  } else {
    this.map.set(timestamp, this.map.get(timestamp)+1)
  }
}

HitCounter.prototype.getHits = function (timestamp) {
  let result = 0
  for (let i = 0; i < 300; i++) {
    if (this.map.has(timestamp-i)){
      result += this.map.get(timestamp-i)
    }
  }
  return result

}{{< /code >}}