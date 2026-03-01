For each number, check if its complement (`target - num`) has already been seen. If yes, you've found your pair. If no, store the current number and its index.

---

```Python
def two_sum(nums: list[int], target: int) -> list[int]: 
	seen = {} # value -> index 
	for i, num in enumerate(nums): 
		complement = target - num if complement in seen: 
			return [seen[complement], i] 
		seen[num] = i 
	return []
```

### Example

nums = [2, 7, 11, 15], target = 9

i=0: num=2, complement=7 -> not in seen -> seen={2:0}
i=1: num=7, complement=2 -> found! -> return [0, 1]


### Explanation Line by Line

**`def two_sum(nums: list[int], target: int) -> list[int]:`** Defines the function. Takes a list of integers and a target integer. The `-> list[int]` is a type hint saying it returns a list of integers.

---

**`seen = {}`** Creates an empty dictionary. This is your "memory" — as you walk through the array, you store numbers you've already visited here. Format will be `{number: index}`.

---

**`for i, num in enumerate(nums):`** Loops through the list. `enumerate` gives you both the index `i` and the value `num` at each step, so you don't have to write `nums[i]` manually.

---

**`complement = target - num`** Calculates what number you _need_ to pair with the current number to reach the target. For example, if `target=9` and `num=2`, then `complement=7` — you need a 7 somewhere.

---

**`if complement in seen:`** Checks if that needed number was already encountered in a previous iteration. Dictionary lookups are O(1), which is why this is fast.

---

**`return [seen[complement], i]`** You found your pair. `seen[complement]` is the index where the complement was previously seen, and `i` is the current index. Returns both as a list.

---

**`seen[num] = i`** The complement wasn't found yet, so store the current number and its index in `seen` for future iterations to look up.

---

**`return []`** Fallback if no valid pair exists. In LeetCode's version of this problem this is technically unreachable since a solution is guaranteed, but it's good practice.