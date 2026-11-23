# Contains Duplicate

| Link | Difficulty | Topic | Status |
|------|------------|-------|--------|
| [LeetCode 217](https://leetcode.com/problems/contains-duplicate/) | Easy | Array & Hashing | Solved on my own ✅ |

## Problem

Given an integer array `nums`, return `true` if any value appears **at least twice**, and `false` if every element is distinct.

## Quick Revision

| | Approach | One-liner | Time | Space |
|---|----------|-----------|------|-------|
| Mine | Sort + Compare Neighbours | After sorting, duplicates sit next to each other, so check `nums[i] == nums[i-1]`. | O(n log n) | O(1) |
| Better | Hash Set (count + insert) | Remember every value seen; if the current one is already in the set, it's a duplicate. | O(n) | O(n) |
| Best | Hash Set (single insert) | `insert()` tells you whether the value was new, so one hash operation does both the check and the add. | O(n) | O(n) |

---

## My Approach: Sort + Compare Neighbours

> After sorting, duplicates sit next to each other, so check `nums[i] == nums[i-1]`.

```cpp
bool containsDuplicate(vector<int>& nums) {
    sort(nums.begin(), nums.end());
    int start = nums[0];
    if (nums.size() == 1)
        return false;
    for (int i = 1; i < nums.size(); i++) {
        if (nums[i] == start) {
            return true;
        } else {
            start = nums[i];
        }
    }
    return false;
}
```

- **Time:** O(n log n), from the sort
- **Space:** O(1) extra, but it **changes the input array**

## Review Notes

- ✅ Correct. `start` always holds the previous element, so `nums[i] == start` is the same check as `nums[i] == nums[i-1]`.
- ⚠️ `nums[0]` is read **before** the size check. LeetCode guarantees `nums.length >= 1`, so it passes, but on an empty array this would be undefined behaviour. When a guard is needed, put it before the first access.
- ✂️ The `size() == 1` check isn't needed: with one element, the loop never runs and the function returns `false` anyway.
- ✂️ `start` can be dropped by comparing with `nums[i-1]` directly.

**Cleaned-up version of my code:**

```cpp
bool containsDuplicate(vector<int>& nums) {
    sort(nums.begin(), nums.end());
    for (int i = 1; i < nums.size(); i++) {
        if (nums[i] == nums[i - 1]) return true;
    }
    return false;
}
```

---

## Better Approach: Hash Set (count + insert)

> Remember every value seen; if the current one is already in the set, it's a duplicate.

```cpp
bool containsDuplicate(vector<int>& nums) {
    unordered_set<int> seen;
    for (int x : nums) {
        if (seen.count(x)) return true;
        seen.insert(x);
    }
    return false;
}
```

- **Time:** O(n) on average
- **Space:** O(n)
- **Why better:** it drops the sort's O(n log n) cost and doesn't change the input.

---

## Best Approach (Optimized): Hash Set (single insert)

> `insert()` tells you whether the value was new, so one hash operation does both the check and the add.

```cpp
bool containsDuplicate(vector<int>& nums) {
    unordered_set<int> seen;
    for (int x : nums) {
        if (!seen.insert(x).second) return true;  // .second is false if x was already there
    }
    return false;
}
```

- **Time:** O(n) on average
- **Space:** O(n)
- **Why best:** same complexity as the Better approach, but with one hash lookup per element instead of two (`count` then `insert`), and it still stops at the first duplicate. This is the answer to give in an interview.

---

## Key Takeaways

- **Sorting brings equal elements together**, so many "find duplicate / pair" problems become a single linear scan after a sort.
- **Hash set = O(1) "have I seen this?" lookup.** It trades memory for speed.
- **Trade-off to say out loud in interviews:** sorting uses O(1) space but is slower and changes the input; hashing is O(n) time but uses O(n) space.
- Start the loop at `i = 1` so `nums[i-1]` never reads out of bounds.
