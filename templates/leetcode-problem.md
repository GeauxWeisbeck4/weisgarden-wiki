---
tags:
  - leetcode
solutionLink:
problemLink:
timeSolved:
problemNumber:
problemTopics:
difficulty:
optimization:
complexity:
selfSolved: false
---
# (Problem #) Title of Problem

# Solution Submissions
1. [Submission One Link](https://example.com)
2. [Submission Two Link](https://example.com)
## Problem Description
###### [Problem Link](https://example.com)
- Description
	- Problem Examples




## Answer
##### Example Solution:

```javascript
/**

* @param {number} x

* @param {number} y

* @param {number} z

* @return {number}

*/

var findClosest = function(x, y, z) {

	const xLength = Math.abs(x - z);

	const yLength = Math.abs(y - z);

	if (xLength < yLength) {

		return 1;

	} else if (xLength > yLength) {

		return 2;

	} else {

		return 0;

	}

};
```

- ## Time Complexity
	- Example: O(1)

# [Editorial Notes Link](https://example.com)
### Editorial Solution

### Other Solutions

1. [Solution Link One](https://example.com)
2. 