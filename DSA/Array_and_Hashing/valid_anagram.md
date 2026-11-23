# Valid Anagram

| Link | Difficulty | Topic | Status |
|------|------------|-------|--------|
| [LeetCode 242](https://leetcode.com/problems/valid-anagram/) | Easy | Array & Hashing | Solved on my own ✅ |

## Problem

Given two strings `s` and `t`, return `true` if `t` is an anagram of `s`, meaning it uses exactly the same letters the same number of times, possibly in a different order. Otherwise return `false`.

## Quick Revision

| | Approach | One-liner | Time | Space |
|---|----------|-----------|------|-------|
| Mine | Sort Both + Compare | Two anagrams contain the same letters, so after sorting they become the exact same string. | O(n log n) | O(n) |
| Better | Hash Map Count | Count each character of `s` up and of `t` down in a map; anagrams leave every count at 0. | O(n) | O(k) |
| Best | Count Array [26] | Same counting idea, but with a fixed 26-slot array indexed by `c - 'a'`, so space is constant. | O(n) | O(1) |

---

## My Approach: Sort Both + Compare

> Two anagrams contain the same letters, so after sorting they become the exact same string.

```cpp
bool isAnagram(string s, string t) {
    if (s.size() != t.size())
        return false;
    sort(s.begin(), s.end());
    sort(t.begin(), t.end());
    for (int i = 0; i < s.size(); i++) {
        if (s[i] != t[i])
            return false;
    }
    return true;
}
```

- **Time:** O(n log n), from the sort
- **Space:** O(n) for the copies of the strings (they're passed by value)

## Review Notes

- ✅ Correct. Checking the lengths first is a cheap early exit.
- ℹ️ The strings are passed **by value** (copies), so sorting them doesn't change the caller's strings. That's fine here.
- ✂️ The manual loop can be replaced with `return s == t;`, since `std::string` compares character by character for you.

**Cleaned-up version of my code:**

```cpp
bool isAnagram(string s, string t) {
    if (s.size() != t.size()) return false;
    sort(s.begin(), s.end());
    sort(t.begin(), t.end());
    return s == t;
}
```

---

## Better Approach: Hash Map Count

> Count each character of `s` up and of `t` down in a map; anagrams leave every count at 0.

```cpp
bool isAnagram(string s, string t) {
    if (s.size() != t.size()) return false;
    unordered_map<char, int> count;
    for (int i = 0; i < s.size(); i++) {
        count[s[i]]++;
        count[t[i]]--;
    }
    for (auto& [ch, c] : count) {
        if (c != 0) return false;
    }
    return true;
}
```

- **Time:** O(n) on average
- **Space:** O(k), where k = number of distinct characters
- **Why better:** it drops the sort's O(n log n) cost. It also works for **any** characters, which answers LeetCode's follow-up: *"What if the inputs contain Unicode characters?"*

---

## Best Approach (Optimized): Count Array [26]

> Same counting idea, but with a fixed 26-slot array indexed by `c - 'a'`, so space is constant.

```cpp
bool isAnagram(string s, string t) {
    if (s.size() != t.size()) return false;
    int count[26] = {0};
    for (int i = 0; i < s.size(); i++) {
        count[s[i] - 'a']++;
        count[t[i] - 'a']--;
    }
    for (int c : count) {
        if (c != 0) return false;
    }
    return true;
}
```

- **Time:** O(n)
- **Space:** O(1), because the array is always 26 ints whatever the input size
- **Why best:** for the given constraint (lowercase `a–z` only), an array is faster than a hash map: no hashing and no memory allocation. If the input could contain Unicode, use the Better approach instead.

---

## Key Takeaways

- **Anagram = same character frequencies.** Counting frequencies is the core pattern, and it comes back in **Group Anagrams**.
- **`c - 'a'` maps `'a'..'z'` to `0..25`**, which is the standard trick for indexing a size-26 array.
- **Check the lengths first.** It's a cheap early exit and makes the single-loop counting version safe.
- **Known small alphabet → array; unknown/large alphabet → hash map.**
