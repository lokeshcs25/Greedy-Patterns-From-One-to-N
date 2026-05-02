
# Greedy Algorithm Patterns — From One to N

> A curated, structured guide to mastering **Greedy Algorithms** — from absolute basics to advanced competitive-level problems.
> Every pattern has a concept explanation, visual intuition, JavaScript template, and hand-picked LeetCode problems.

---

## 📌 Table of Contents

| # | Pattern | Difficulty |
|---|---------|------------|
| 1 | [Greedy Basics — Always Pick the Best](#1-greedy-basics--always-pick-the-best) | 🟢 Beginner |
| 2 | [Interval Scheduling & Merging](#2-interval-scheduling--merging) | 🟢 Beginner |
| 3 | [Activity Selection / Non-Overlapping Intervals](#3-activity-selection--non-overlapping-intervals) | 🟡 Intermediate |
| 4 | [Jump Game — Reach the End](#4-jump-game--reach-the-end) | 🟡 Intermediate |
| 5 | [Gas Station / Circular Array Greedy](#5-gas-station--circular-array-greedy) | 🟡 Intermediate |
| 6 | [Task Scheduling & CPU Greedy](#6-task-scheduling--cpu-greedy) | 🟡 Intermediate |
| 7 | [Greedy on Strings](#7-greedy-on-strings) | 🟡 Intermediate |
| 8 | [Two Pointer Greedy (Boats, Matching)](#8-two-pointer-greedy-boats-matching) | 🟡 Intermediate |
| 9 | [Greedy with Priority Queue / Heap](#9-greedy-with-priority-queue--heap) | 🟠 Advanced |
| 10 | [Greedy on Arrays — Maximize / Minimize](#10-greedy-on-arrays--maximize--minimize) | 🟠 Advanced |
| 11 | [Greedy + Sorting Tricks](#11-greedy--sorting-tricks) | 🟠 Advanced |
| 12 | [Advanced Greedy — Huffman, Candy, Reconstruct](#12-advanced-greedy--huffman-candy-reconstruct) | 🔴 Hard |

---

## 🧠 Core Intuition

A Greedy algorithm makes the **locally optimal choice** at each step, hoping it leads to a **globally optimal solution**. Unlike DP, it never reconsiders past decisions.

```
Greedy works when:
  Local best choice  →  Global best result
  (no "undo" needed at any step)

Greedy FAILS when:
  A locally bad choice leads to a globally better result
  → Use DP instead
```

**The Greedy Checklist — before writing code, verify:**
```
✅ Greedy Choice Property  — a locally optimal choice leads to a globally optimal solution
✅ Optimal Substructure    — optimal solution contains optimal solutions to subproblems
✅ No regret               — we never need to undo a greedy choice
```

**Keywords:** *"minimum number of"*, *"maximum number of"*, *"earliest deadline"*, *"least cost"*, *"can you reach"*, *"non-overlapping"*, *"assign tasks"*

---

## ⚠️ Greedy vs Dynamic Programming

```
Use GREEDY when:                    Use DP when:
────────────────────────────────    ──────────────────────────────────
Single pass, no backtracking        Need to explore all possibilities
Sorting + one-pass decision         Overlapping subproblems
Proof exists for local→global       Choices depend on future states
O(n log n) or O(n) expected         Can accept O(n²) or O(n×k)

Example: Activity Selection → Greedy ✅
Example: 0/1 Knapsack        → DP    ✅
```

---

## 1. Greedy Basics — Always Pick the Best

### 🔑 Concept
The simplest greedy: at every step, **pick the locally best option** (max coin, min cost, earliest time, etc.). Works when choices are independent and don't affect future options negatively.

### 📐 Visual
```
Coin Change (greedy works for standard coins):
amount = 41 cents,  coins = [25, 10, 5, 1]

Step 1: Pick largest coin ≤ 41 → 25  (remaining: 16)
Step 2: Pick largest coin ≤ 16 → 10  (remaining:  6)
Step 3: Pick largest coin ≤  6 →  5  (remaining:  1)
Step 4: Pick largest coin ≤  1 →  1  (remaining:  0)

Result: 4 coins ✅

⚠️ Note: Greedy FAILS for arbitrary coin sets → use DP
```

### 🧩 Template
```javascript
// Assign cookies — give smallest sufficient cookie to each child
function findContentChildren(greed, cookies) {
    greed.sort((a, b) => a - b);
    cookies.sort((a, b) => a - b);

    let child = 0;

    for (let cookie = 0; cookie < cookies.length && child < greed.length; cookie++) {
        if (cookies[cookie] >= greed[child]) {
            child++;    // this cookie satisfies this child
        }
    }

    return child;   // number of content children
}
```

### 📚 LeetCode Problems

| # | Problem | Difficulty |
|---|---------|------------|
| [455](https://leetcode.com/problems/assign-cookies/) | Assign Cookies | 🟢 Easy |
| [860](https://leetcode.com/problems/lemonade-change/) | Lemonade Change | 🟢 Easy |
| [1221](https://leetcode.com/problems/split-a-string-in-balanced-strings/) | Split a String in Balanced Strings | 🟢 Easy |
| [2591](https://leetcode.com/problems/distribute-money-to-maximum-children/) | Distribute Money to Maximum Children | 🟢 Easy |
| [1827](https://leetcode.com/problems/minimum-operations-to-make-the-array-increasing/) | Minimum Operations to Make the Array Increasing | 🟢 Easy |
| [2578](https://leetcode.com/problems/split-with-minimum-sum/) | Split With Minimum Sum | 🟢 Easy |

---

## 2. Interval Scheduling & Merging

### 🔑 Concept
Given a list of intervals, **merge all overlapping ones** into a single interval. Sort by start time, then greedily extend the current interval's end when overlap is found.

### 📐 Visual
```
intervals = [[1,3],[2,6],[8,10],[15,18]]

Sort by start: already sorted

[1,3] and [2,6] overlap (2 ≤ 3) → merge to [1,6]
[1,6] and [8,10] no overlap (8 > 6) → keep [8,10]
[8,10] and [15,18] no overlap → keep [15,18]

Result: [[1,6],[8,10],[15,18]] ✅
```

### 🧩 Template
```javascript
// Merge overlapping intervals
function merge(intervals) {
    if (intervals.length === 0) return [];

    intervals.sort((a, b) => a[0] - b[0]);   // sort by start time

    const result = [intervals[0]];

    for (let i = 1; i < intervals.length; i++) {
        const last = result[result.length - 1];
        const curr = intervals[i];

        if (curr[0] <= last[1]) {
            last[1] = Math.max(last[1], curr[1]);  // extend end
        } else {
            result.push(curr);                      // no overlap, add new
        }
    }

    return result;
}

// Insert a new interval into a sorted non-overlapping list
function insert(intervals, newInterval) {
    const result = [];
    let i = 0;

    // Add all intervals before newInterval
    while (i < intervals.length && intervals[i][1] < newInterval[0]) {
        result.push(intervals[i++]);
    }

    // Merge overlapping intervals
    while (i < intervals.length && intervals[i][0] <= newInterval[1]) {
        newInterval[0] = Math.min(newInterval[0], intervals[i][0]);
        newInterval[1] = Math.max(newInterval[1], intervals[i][1]);
        i++;
    }
    result.push(newInterval);

    // Add remaining intervals
    while (i < intervals.length) result.push(intervals[i++]);

    return result;
}
```

### 📚 LeetCode Problems

| # | Problem | Difficulty |
|---|---------|------------|
| [56](https://leetcode.com/problems/merge-intervals/) | Merge Intervals | 🟡 Medium |
| [57](https://leetcode.com/problems/insert-interval/) | Insert Interval | 🟡 Medium |
| [986](https://leetcode.com/problems/interval-list-intersections/) | Interval List Intersections | 🟡 Medium |
| [2446](https://leetcode.com/problems/determine-if-two-events-have-conflict/) | Determine if Two Events Have Conflict | 🟢 Easy |
| [1288](https://leetcode.com/problems/remove-covered-intervals/) | Remove Covered Intervals | 🟡 Medium |
| [2406](https://leetcode.com/problems/divide-intervals-into-minimum-number-of-groups/) | Divide Intervals Into Minimum Number of Groups | 🟡 Medium |

---

## 3. Activity Selection / Non-Overlapping Intervals

### 🔑 Concept
Select the **maximum number of non-overlapping intervals** (or equivalently, find the **minimum number to remove**). Key insight: always pick the interval with the **earliest ending time** — this leaves the most room for future intervals.

### 📐 Visual
```
intervals = [[1,2],[2,3],[3,4],[1,3]]

Sort by END time: [[1,2],[2,3],[1,3],[3,4]]

Pick [1,2]  → end=2, count=1
Pick [2,3]  → start(2) >= end(2) ✅ → end=3, count=2
Skip [1,3]  → start(1) < end(3) ✗ overlap
Pick [3,4]  → start(3) >= end(3) ✅ → end=4, count=3

Max non-overlapping = 3
Min to remove = 4 - 3 = 1 ✅
```

### 🧩 Template
```javascript
// Minimum number of intervals to remove to make rest non-overlapping
function eraseOverlapIntervals(intervals) {
    if (intervals.length === 0) return 0;

    intervals.sort((a, b) => a[1] - b[1]);   // sort by END time ← key!

    let count = 0;
    let lastEnd = intervals[0][1];

    for (let i = 1; i < intervals.length; i++) {
        if (intervals[i][0] < lastEnd) {
            count++;    // overlap → remove this interval
        } else {
            lastEnd = intervals[i][1];   // no overlap → keep it
        }
    }

    return count;
}

// Minimum number of arrows to burst all balloons
function findMinArrowShots(points) {
    points.sort((a, b) => a[1] - b[1]);   // sort by end (right boundary)

    let arrows = 1;
    let arrowPos = points[0][1];

    for (let i = 1; i < points.length; i++) {
        if (points[i][0] > arrowPos) {     // balloon starts after arrow
            arrows++;
            arrowPos = points[i][1];
        }
    }

    return arrows;
}
```

### 📚 LeetCode Problems

| # | Problem | Difficulty |
|---|---------|------------|
| [435](https://leetcode.com/problems/non-overlapping-intervals/) | Non-overlapping Intervals | 🟡 Medium |
| [452](https://leetcode.com/problems/minimum-number-of-arrows-to-burst-balloons/) | Minimum Number of Arrows to Burst Balloons | 🟡 Medium |
| [646](https://leetcode.com/problems/maximum-length-of-pair-chain/) | Maximum Length of Pair Chain | 🟡 Medium |
| [1024](https://leetcode.com/problems/video-stitching/) | Video Stitching | 🟡 Medium |
| [1326](https://leetcode.com/problems/minimum-number-of-taps-to-open-to-water-a-garden/) | Minimum Number of Taps to Open to Water a Garden | 🔴 Hard |
| [2054](https://leetcode.com/problems/two-best-non-overlapping-events/) | Two Best Non-Overlapping Events | 🟡 Medium |

---

## 4. Jump Game — Reach the End

### 🔑 Concept
Track the **farthest position reachable** at each step. If you can extend the reach, do it. If your current position exceeds the maximum reach, you're stuck. This greedy eliminates the need for BFS or DP.

### 📐 Visual
```
nums = [2, 3, 1, 1, 4]   Can we reach end?

i=0: reach = max(0, 0+2) = 2
i=1: reach = max(2, 1+3) = 4
i=2: reach = max(4, 2+1) = 4
i=3: reach = max(4, 3+1) = 4  ← reach >= 4 (last index) ✅

nums = [3, 2, 1, 0, 4]   Can we reach end?

i=0: reach = 3
i=1: reach = 3
i=2: reach = 3
i=3: reach = 3
i=4: 4 > reach=3 → STUCK ✗
```

### 🧩 Template
```javascript
// Jump Game I — can we reach the last index?
function canJump(nums) {
    let maxReach = 0;

    for (let i = 0; i < nums.length; i++) {
        if (i > maxReach) return false;           // stuck — can't reach here
        maxReach = Math.max(maxReach, i + nums[i]); // extend reach
    }

    return true;
}

// Jump Game II — minimum jumps to reach last index
function jump(nums) {
    let jumps = 0;
    let currentEnd = 0;   // end of current jump range
    let farthest = 0;     // farthest we can reach from current range

    for (let i = 0; i < nums.length - 1; i++) {
        farthest = Math.max(farthest, i + nums[i]);

        if (i === currentEnd) {    // must jump here — end of current range
            jumps++;
            currentEnd = farthest;
        }
    }

    return jumps;
}
```

### 📚 LeetCode Problems

| # | Problem | Difficulty |
|---|---------|------------|
| [55](https://leetcode.com/problems/jump-game/) | Jump Game | 🟡 Medium |
| [45](https://leetcode.com/problems/jump-game-ii/) | Jump Game II | 🟡 Medium |
| [1306](https://leetcode.com/problems/jump-game-iii/) | Jump Game III | 🟡 Medium |
| [1345](https://leetcode.com/problems/jump-game-iv/) | Jump Game IV | 🔴 Hard |
| [1871](https://leetcode.com/problems/jump-game-vii/) | Jump Game VII | 🟡 Medium |
| [2297](https://leetcode.com/problems/jump-game-viii/) | Jump Game VIII | 🟡 Medium |

---

## 5. Gas Station / Circular Array Greedy

### 🔑 Concept
Circular greedy problems where you need to find a **valid starting point**. Key insight: if total gas ≥ total cost, a solution always exists. The starting point is where the **cumulative surplus first goes negative** — reset there.

### 📐 Visual
```
gas  = [1, 2, 3, 4, 5]
cost = [3, 4, 5, 1, 2]

net  = [-2,-2,-2, 3, 3]   (gas - cost at each station)

Running sum:
  start=0: -2 (go negative) → reset, try start=1
  start=1: -2+(-2) = -4 (go negative) → reset, try start=2
  start=2: -2+(-2)+(-2) = -6 → reset, try start=3
  start=3: 3 (positive!) → continue
  start=3 to 4: 3+3=6 ✅

  total = (-2)+(-2)+(-2)+3+3 = 0 ≥ 0 → solution exists
  Answer: start at index 3 ✅
```

### 🧩 Template
```javascript
// Gas Station — find starting index for a complete circuit
function canCompleteCircuit(gas, cost) {
    let totalTank = 0;
    let currentTank = 0;
    let startStation = 0;

    for (let i = 0; i < gas.length; i++) {
        const net = gas[i] - cost[i];
        totalTank += net;
        currentTank += net;

        if (currentTank < 0) {        // can't continue from startStation
            startStation = i + 1;     // try next station as start
            currentTank = 0;          // reset current tank
        }
    }

    return totalTank >= 0 ? startStation : -1;   // -1 if impossible
}
```

### 📚 LeetCode Problems

| # | Problem | Difficulty |
|---|---------|------------|
| [134](https://leetcode.com/problems/gas-station/) | Gas Station | 🟡 Medium |
| [1014](https://leetcode.com/problems/best-sightseeing-pair/) | Best Sightseeing Pair | 🟡 Medium |
| [2202](https://leetcode.com/problems/maximize-the-topmost-element-after-k-moves/) | Maximize the Topmost Element After K Moves | 🟡 Medium |
| [2393](https://leetcode.com/problems/count-strictly-increasing-subarrays/) | Count Strictly Increasing Subarrays | 🟡 Medium |
| [2circle](https://leetcode.com/problems/minimum-number-of-refueling-stops/) | Minimum Number of Refueling Stops | 🔴 Hard |

---

## 6. Task Scheduling & CPU Greedy

### 🔑 Concept
Schedule tasks to **minimize total time** or **maximize throughput**. The greedy insight is usually: always execute the **most frequent** or **most urgent** task next. Combine with a max-heap for efficient retrieval.

### 📐 Visual
```
tasks = [A,A,A,B,B,B,C,C], n = 2  (cooldown = 2)

Greedy: always pick most frequent available task

Cycle 1: A(3) B(3) C(2) → execute A,B,C
Cycle 2: A(2) B(2) C(1) → execute A,B,C
Cycle 3: A(1) B(1)      → execute A,B,idle
              ↑ one idle slot

Total = 8 slots ✅
Formula: max(tasks.length, (maxFreq-1)*(n+1) + countOfMaxFreq)
```

### 🧩 Template
```javascript
// Task Scheduler — minimum time with cooldown n
function leastInterval(tasks, n) {
    const freq = new Array(26).fill(0);
    for (const t of tasks) freq[t.charCodeAt(0) - 65]++;

    const maxFreq = Math.max(...freq);
    const countMax = freq.filter(f => f === maxFreq).length;

    // Either all tasks fit naturally, or we need idle slots
    return Math.max(tasks.length, (maxFreq - 1) * (n + 1) + countMax);
}

// Meeting Rooms II — minimum rooms needed
function minMeetingRooms(intervals) {
    const starts = intervals.map(i => i[0]).sort((a, b) => a - b);
    const ends   = intervals.map(i => i[1]).sort((a, b) => a - b);

    let rooms = 0, endPtr = 0;

    for (let i = 0; i < starts.length; i++) {
        if (starts[i] < ends[endPtr]) {
            rooms++;           // need a new room
        } else {
            endPtr++;          // reuse a room
        }
    }

    return rooms;
}
```

### 📚 LeetCode Problems

| # | Problem | Difficulty |
|---|---------|------------|
| [621](https://leetcode.com/problems/task-scheduler/) | Task Scheduler | 🟡 Medium |
| [253](https://leetcode.com/problems/meeting-rooms-ii/) | Meeting Rooms II | 🟡 Medium |
| [1710](https://leetcode.com/problems/maximum-units-on-a-truck/) | Maximum Units on a Truck | 🟢 Easy |
| [2542](https://leetcode.com/problems/maximum-subsequence-score/) | Maximum Subsequence Score | 🟡 Medium |
| [1834](https://leetcode.com/problems/single-threaded-cpu/) | Single-Threaded CPU | 🟡 Medium |
| [2462](https://leetcode.com/problems/total-cost-to-hire-k-workers/) | Total Cost to Hire K Workers | 🟡 Medium |

---

## 7. Greedy on Strings

### 🔑 Concept
String greedy problems usually involve **building the lexicographically smallest/largest** string or checking a **feasibility condition** character by character. Common tricks: use a stack, track balance counters, or make character-by-character decisions.

### 📐 Visual
```
Remove k digits to make smallest number
num = "1432219",  k = 3

Stack greedy: remove a digit when next digit is SMALLER

  Process '1': stack=[1]
  Process '4': 4>1 → stack=[1,4]
  Process '3': 3<4 → pop 4, k=2  stack=[1,3]
  Process '2': 2<3 → pop 3, k=1  stack=[1,2]
  Process '2': 2=2 → stack=[1,2,2]
  Process '1': 1<2 → pop 2, k=0  stack=[1,2,1]
  Process '9': stack=[1,2,1,9]

Result: "1219" ✅
```

### 🧩 Template
```javascript
// Remove k digits to form smallest number
function removeKdigits(num, k) {
    const stack = [];

    for (const digit of num) {
        while (k > 0 && stack.length > 0 && stack[stack.length - 1] > digit) {
            stack.pop();
            k--;
        }
        stack.push(digit);
    }

    // Remove remaining from end
    while (k > 0) { stack.pop(); k--; }

    // Remove leading zeros and return
    const result = stack.join('').replace(/^0+/, '');
    return result || '0';
}

// Check if string of brackets can be valid
function checkValidString(s) {
    let minOpen = 0, maxOpen = 0;   // range of possible open count

    for (const ch of s) {
        if (ch === '(') { minOpen++; maxOpen++; }
        else if (ch === ')') { minOpen--; maxOpen--; }
        else { minOpen--; maxOpen++; }   // '*' can be either

        if (maxOpen < 0) return false;   // too many ')'
        minOpen = Math.max(minOpen, 0);  // can't have negative open
    }

    return minOpen === 0;
}
```

### 📚 LeetCode Problems

| # | Problem | Difficulty |
|---|---------|------------|
| [402](https://leetcode.com/problems/remove-k-digits/) | Remove K Digits | 🟡 Medium |
| [678](https://leetcode.com/problems/valid-parenthesis-string/) | Valid Parenthesis String | 🟡 Medium |
| [1663](https://leetcode.com/problems/smallest-string-with-a-given-numeric-value/) | Smallest String With a Given Numeric Value | 🟡 Medium |
| [2311](https://leetcode.com/problems/longest-binary-subsequence-less-than-or-equal-to-k/) | Longest Binary Subsequence Less Than or Equal to K | 🟡 Medium |
| [1717](https://leetcode.com/problems/maximum-score-from-removing-substrings/) | Maximum Score From Removing Substrings | 🟡 Medium |
| [316](https://leetcode.com/problems/remove-duplicate-letters/) | Remove Duplicate Letters | 🟡 Medium |

---

## 8. Two Pointer Greedy (Boats, Matching)

### 🔑 Concept
Sort the array, then use **two pointers from both ends** to greedily match or pair elements. The lightest and heaviest, weakest and strongest — always try to pair extremes first.

### 📐 Visual
```
Boats to Save People: each boat holds at most `limit` weight
people = [1, 2, 2, 3],  limit = 3

Sort: [1, 2, 2, 3]
       L           R

L=1, R=3: 1+3=4 > 3 → R alone  boats=1, R--
L=1, R=2: 1+2=3 ≤ 3 → pair!    boats=2, L++, R--
L=2, R=2: only one left         boats=3, done

Answer: 3 boats ✅
```

### 🧩 Template
```javascript
// Boats to Save People
function numRescueBoats(people, limit) {
    people.sort((a, b) => a - b);
    let left = 0, right = people.length - 1;
    let boats = 0;

    while (left <= right) {
        if (people[left] + people[right] <= limit) {
            left++;    // lightest person pairs with heaviest
        }
        right--;       // heaviest always takes a boat
        boats++;
    }

    return boats;
}

// Minimize maximum pair sum
function minPairSum(nums) {
    nums.sort((a, b) => a - b);
    let maxSum = 0;
    let left = 0, right = nums.length - 1;

    while (left < right) {
        maxSum = Math.max(maxSum, nums[left] + nums[right]);
        left++;
        right--;
    }

    return maxSum;
}
```

### 📚 LeetCode Problems

| # | Problem | Difficulty |
|---|---------|------------|
| [881](https://leetcode.com/problems/boats-to-save-people/) | Boats to Save People | 🟡 Medium |
| [1877](https://leetcode.com/problems/minimize-maximum-pair-sum-in-array/) | Minimize Maximum Pair Sum in Array | 🟡 Medium |
| [2410](https://leetcode.com/problems/maximum-matching-of-players-with-trainers/) | Maximum Matching of Players With Trainers | 🟡 Medium |
| [976](https://leetcode.com/problems/largest-perimeter-triangle/) | Largest Perimeter Triangle | 🟢 Easy |
| [2259](https://leetcode.com/problems/remove-digit-from-number-to-maximize-result/) | Remove Digit From Number to Maximize Result | 🟢 Easy |
| [2491](https://leetcode.com/problems/divide-players-into-teams-of-equal-skill/) | Divide Players Into Teams of Equal Skill | 🟡 Medium |

---

## 9. Greedy with Priority Queue / Heap

### 🔑 Concept
When greedy needs to **dynamically fetch the best available option** (not just the next sorted one), combine greedy with a **max-heap or min-heap**. Push candidates in, pop the best one at each step.

### 📐 Visual
```
Connect ropes with minimum cost:
ropes = [4, 3, 2, 6]

Always connect two shortest ropes:
  Heap: [2,3,4,6]
  Pop 2,3  → cost=5,  push 5  → [4,5,6]
  Pop 4,5  → cost=9,  push 9  → [6,9]
  Pop 6,9  → cost=15, push 15 → [15]

Total cost = 5+9+15 = 29 ✅  (greedy: min first)
```

### 🧩 Template
```javascript
// Reorganize string — no two adjacent chars same (max-heap greedy)
function reorganizeString(s) {
    const freq = new Array(26).fill(0);
    for (const ch of s) freq[ch.charCodeAt(0) - 97]++;

    // Max-heap simulation using sorted array (for interview clarity)
    // In production use a proper heap library
    const maxFreq = Math.max(...freq);
    if (maxFreq > Math.ceil(s.length / 2)) return '';   // impossible

    const result = new Array(s.length);
    let idx = 0;

    // Place most frequent chars at even indices first
    for (let i = 0; i < 26; i++) {
        while (freq[i] > 0) {
            if (idx >= s.length) idx = 1;
            result[idx] = String.fromCharCode(i + 97);
            idx += 2;
            freq[i]--;
        }
    }

    return result.join('');
}

// IPO — maximize capital (greedy with two heaps)
function findMaximizedCapital(k, w, profits, capital) {
    const n = profits.length;
    const projects = profits.map((p, i) => [capital[i], p]);
    projects.sort((a, b) => a[0] - b[0]);   // sort by min capital needed

    // Simulate max-heap with sorted array for clarity
    const available = [];
    let idx = 0;

    for (let i = 0; i < k; i++) {
        // Add all affordable projects
        while (idx < n && projects[idx][0] <= w) {
            available.push(projects[idx][1]);
            idx++;
        }
        if (available.length === 0) break;
        available.sort((a, b) => b - a);   // pick max profit
        w += available.shift();
    }

    return w;
}
```

### 📚 LeetCode Problems

| # | Problem | Difficulty |
|---|---------|------------|
| [767](https://leetcode.com/problems/reorganize-string/) | Reorganize String | 🟡 Medium |
| [502](https://leetcode.com/problems/ipo/) | IPO | 🔴 Hard |
| [1642](https://leetcode.com/problems/furthest-building-you-can-reach/) | Furthest Building You Can Reach | 🟡 Medium |
| [2542](https://leetcode.com/problems/maximum-subsequence-score/) | Maximum Subsequence Score | 🟡 Medium |
| [2812](https://leetcode.com/problems/find-the-safest-path-in-a-grid/) | Find the Safest Path in a Grid | 🟡 Medium |
| [2462](https://leetcode.com/problems/total-cost-to-hire-k-workers/) | Total Cost to Hire K Workers | 🟡 Medium |

---

## 10. Greedy on Arrays — Maximize / Minimize

### 🔑 Concept
Problems asking to **maximize or minimize** some value over an array — like maximum sum after operations, minimum cost, or best profit. Greedy scans once and makes the optimal local decision at each element.

### 📐 Visual
```
Best Time to Buy and Sell Stock II (multiple transactions):
prices = [7, 1, 5, 3, 6, 4]

Greedy: collect every upward move (profit > 0)
  1→5: profit = 4 ✅
  3→6: profit = 3 ✅
  (ignore all downward moves)

Total = 4 + 3 = 7 ✅
```

### 🧩 Template
```javascript
// Buy and sell stock — multiple transactions
function maxProfit(prices) {
    let profit = 0;

    for (let i = 1; i < prices.length; i++) {
        if (prices[i] > prices[i - 1]) {
            profit += prices[i] - prices[i - 1];   // grab every upswing
        }
    }

    return profit;
}

// Minimum number of operations to make array non-decreasing
function minOperations(nums) {
    let ops = 0;
    for (let i = 1; i < nums.length; i++) {
        if (nums[i] < nums[i - 1]) {
            ops += nums[i - 1] - nums[i];
            nums[i] = nums[i - 1];   // raise current to match previous
        }
    }
    return ops;
}
```

### 📚 LeetCode Problems

| # | Problem | Difficulty |
|---|---------|------------|
| [122](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/) | Best Time to Buy and Sell Stock II | 🟡 Medium |
| [53](https://leetcode.com/problems/maximum-subarray/) | Maximum Subarray (Kadane's) | 🟡 Medium |
| [1403](https://leetcode.com/problems/minimum-subsequence-in-non-increasing-order/) | Minimum Subsequence in Non-Increasing Order | 🟢 Easy |
| [2139](https://leetcode.com/problems/minimum-moves-to-reach-target-score/) | Minimum Moves to Reach Target Score | 🟡 Medium |
| [1599](https://leetcode.com/problems/maximum-profit-of-operating-a-centennial-wheel/) | Maximum Profit of Operating a Centennial Wheel | 🟡 Medium |
| [2611](https://leetcode.com/problems/mice-and-cheese/) | Mice and Cheese | 🟡 Medium |

---

## 11. Greedy + Sorting Tricks

### 🔑 Concept
Many greedy problems become easy once you figure out **what to sort by**. The sorting key IS the greedy insight. Common patterns: sort by ratio, sort by end time, sort by difference, sort by a custom comparator.

### 📐 Visual
```
Largest Number from array digits:
nums = [3, 30, 34, 5, 9]

Key: sort by which concatenation is larger
  "9" > "5" > "34" > "3" > "30"
  → "9534330" ✅

Custom comparator: compare (a+b) vs (b+a)
  "930" vs "309" → "930" wins → 9 before 30 ✅
```

### 🧩 Template
```javascript
// Largest number — custom sort comparator
function largestNumber(nums) {
    const strs = nums.map(String);
    strs.sort((a, b) => (b + a).localeCompare(a + b));  // key insight!

    if (strs[0] === '0') return '0';   // handle [0,0,...] edge case
    return strs.join('');
}

// Fractional knapsack — sort by value/weight ratio
function fractionalKnapsack(items, capacity) {
    // items = [{value, weight}, ...]
    items.sort((a, b) => (b.value / b.weight) - (a.value / a.weight));

    let totalValue = 0;

    for (const item of items) {
        if (capacity <= 0) break;

        if (item.weight <= capacity) {
            totalValue += item.value;
            capacity -= item.weight;
        } else {
            totalValue += item.value * (capacity / item.weight);
            capacity = 0;
        }
    }

    return totalValue;
}
```

### 📚 LeetCode Problems

| # | Problem | Difficulty |
|---|---------|------------|
| [179](https://leetcode.com/problems/largest-number/) | Largest Number | 🟡 Medium |
| [1029](https://leetcode.com/problems/two-city-scheduling/) | Two City Scheduling | 🟡 Medium |
| [1353](https://leetcode.com/problems/maximum-number-of-events-that-can-be-attended/) | Maximum Number of Events That Can Be Attended | 🟡 Medium |
| [2233](https://leetcode.com/problems/maximum-product-after-k-increments/) | Maximum Product After K Increments | 🟡 Medium |
| [2384](https://leetcode.com/problems/largest-palindromic-number/) | Largest Palindromic Number | 🟡 Medium |
| [1968](https://leetcode.com/problems/array-with-elements-not-equal-to-average-of-neighbors/) | Array With Elements Not Equal to Average of Neighbors | 🟡 Medium |

---

## 12. Advanced Greedy — Huffman, Candy, Reconstruct

### 🔑 Concept
The hardest greedy problems require **non-obvious observations** about what to optimize locally. Classic hard problems: Candy distribution (two-pass greedy), Reconstruct queue by height, Partition labels.

### 📐 Visual
```
Candy Distribution:
ratings = [1, 0, 2]

Left pass  (each child > left neighbor gets more):
  [1, 1, 2]

Right pass (each child > right neighbor gets more):
  [2, 1, 2]

Take max at each position: max([1,1,2],[2,1,2]) = [2,1,2]
Total = 5 ✅

Partition Labels:
s = "ababcbacadefegdehijhklij"

Last occurrence: a=8, b=5, c=7, d=14, e=15, f=11, g=13, h=19, i=22, j=23, k=20, l=21

Greedy: extend partition end to last occurrence of current char
  [a..a=8] but b=5,c=7 inside → end=max(8,5,7)=8 → partition [0..8]
  [d..d=14] → extend to 15 (e), 14, 15, 11 → end=15 → partition [9..15]
  ...
```

### 🧩 Template
```javascript
// Candy — two-pass greedy
function candy(ratings) {
    const n = ratings.length;
    const candies = new Array(n).fill(1);

    // Left to right: if rating[i] > rating[i-1], give one more
    for (let i = 1; i < n; i++) {
        if (ratings[i] > ratings[i - 1]) {
            candies[i] = candies[i - 1] + 1;
        }
    }

    // Right to left: if rating[i] > rating[i+1], ensure more candy
    for (let i = n - 2; i >= 0; i--) {
        if (ratings[i] > ratings[i + 1]) {
            candies[i] = Math.max(candies[i], candies[i + 1] + 1);
        }
    }

    return candies.reduce((sum, c) => sum + c, 0);
}

// Partition Labels — each letter in at most one partition
function partitionLabels(s) {
    const last = {};
    for (let i = 0; i < s.length; i++) last[s[i]] = i;

    const result = [];
    let start = 0, end = 0;

    for (let i = 0; i < s.length; i++) {
        end = Math.max(end, last[s[i]]);    // extend partition

        if (i === end) {                     // reached partition boundary
            result.push(end - start + 1);
            start = i + 1;
        }
    }

    return result;
}

// Reconstruct queue by height
function reconstructQueue(people) {
    // Sort: tallest first, among equal heights — fewer in front first
    people.sort((a, b) => a[0] !== b[0] ? b[0] - a[0] : a[1] - b[1]);

    const result = [];
    for (const p of people) {
        result.splice(p[1], 0, p);   // insert at position p[1]
    }

    return result;
}
```

### 📚 LeetCode Problems

| # | Problem | Difficulty |
|---|---------|------------|
| [135](https://leetcode.com/problems/candy/) | Candy | 🔴 Hard |
| [763](https://leetcode.com/problems/partition-labels/) | Partition Labels | 🟡 Medium |
| [406](https://leetcode.com/problems/queue-reconstruction-by-height/) | Queue Reconstruction by Height | 🟡 Medium |
| [1665](https://leetcode.com/problems/minimum-initial-energy-to-finish-tasks/) | Minimum Initial Energy to Finish Tasks | 🔴 Hard |
| [2136](https://leetcode.com/problems/earliest-possible-day-of-full-bloom/) | Earliest Possible Day of Full Bloom | 🔴 Hard |
| [1253](https://leetcode.com/problems/reconstruct-a-2-row-binary-matrix/) | Reconstruct a 2-Row Binary Matrix | 🟡 Medium |

---

## 🗺️ Pattern Decision Tree

```
Is the problem about INTERVALS (start/end times)?
├── Merge overlapping intervals → Pattern 2
├── Select maximum non-overlapping → Pattern 3
└── Minimum rooms / groups needed → Pattern 6

Can you REACH the end of an array?
└── YES → Pattern 4: Jump Game

Is it a CIRCULAR problem (gas, starting point)?
└── YES → Pattern 5: Gas Station / Circular Greedy

Is the input a STRING and you need smallest/valid result?
└── YES → Pattern 7: Greedy on Strings

Do you need to PAIR or MATCH elements optimally?
└── YES → Pattern 8: Two Pointer Greedy

Do you need to DYNAMICALLY pick the best item at each step?
└── YES → Pattern 9: Greedy + Heap

Is the problem about SORTING to unlock the greedy insight?
└── YES → Pattern 11: Greedy + Sorting Tricks

Are you MAXIMIZING or MINIMIZING a running value over an array?
└── YES → Pattern 10: Greedy on Arrays

Does the problem feel like DP but n is small or it has one direction?
├── Two-pass (left then right) → Pattern 12 (Candy)
└── Custom insertion order → Pattern 12 (Queue Reconstruction)

Is it a SIMPLE pick-the-best-at-each-step problem?
└── YES → Pattern 1: Greedy Basics
```

---

## ⚡ Quick Complexity Reference

| Pattern | Time | Space |
|---------|------|-------|
| Greedy Basics | O(n log n) | O(1) |
| Interval Merging | O(n log n) | O(n) |
| Activity Selection | O(n log n) | O(1) |
| Jump Game | O(n) | O(1) |
| Gas Station | O(n) | O(1) |
| Task Scheduling | O(n) | O(1) |
| Greedy on Strings | O(n) | O(n) |
| Two Pointer Greedy | O(n log n) | O(1) |
| Greedy + Heap | O(n log n) | O(n) |
| Greedy on Arrays | O(n) | O(1) |
| Sorting Tricks | O(n log n) | O(n) |
| Advanced (Candy, Partition) | O(n) | O(n) |

---

## 🏆 Curated Must-Solve List (Top 25)

| # | Problem | Pattern | Company Tags |
|---|---------|---------|--------------|
| [45](https://leetcode.com/problems/jump-game-ii/) | Jump Game II | Jump Game | Amazon, Google |
| [53](https://leetcode.com/problems/maximum-subarray/) | Maximum Subarray | Arrays | Amazon, Meta |
| [55](https://leetcode.com/problems/jump-game/) | Jump Game | Jump Game | Amazon, Microsoft |
| [56](https://leetcode.com/problems/merge-intervals/) | Merge Intervals | Intervals | Google, Meta |
| [57](https://leetcode.com/problems/insert-interval/) | Insert Interval | Intervals | Google, LinkedIn |
| [122](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/) | Best Time to Buy and Sell Stock II | Arrays | Amazon |
| [134](https://leetcode.com/problems/gas-station/) | Gas Station | Circular | Amazon, Google |
| [135](https://leetcode.com/problems/candy/) | Candy | Advanced | Amazon, Google |
| [179](https://leetcode.com/problems/largest-number/) | Largest Number | Sorting | Amazon |
| [253](https://leetcode.com/problems/meeting-rooms-ii/) | Meeting Rooms II | Scheduling | Google, Meta |
| [316](https://leetcode.com/problems/remove-duplicate-letters/) | Remove Duplicate Letters | Strings | Google |
| [402](https://leetcode.com/problems/remove-k-digits/) | Remove K Digits | Strings | Google |
| [406](https://leetcode.com/problems/queue-reconstruction-by-height/) | Queue Reconstruction by Height | Advanced | Google |
| [435](https://leetcode.com/problems/non-overlapping-intervals/) | Non-overlapping Intervals | Activity Selection | Google |
| [452](https://leetcode.com/problems/minimum-number-of-arrows-to-burst-balloons/) | Minimum Number of Arrows | Activity Selection | Amazon |
| [455](https://leetcode.com/problems/assign-cookies/) | Assign Cookies | Basics | Amazon |
| [502](https://leetcode.com/problems/ipo/) | IPO | Heap | Google |
| [621](https://leetcode.com/problems/task-scheduler/) | Task Scheduler | Scheduling | Meta, Amazon |
| [678](https://leetcode.com/problems/valid-parenthesis-string/) | Valid Parenthesis String | Strings | Amazon |
| [763](https://leetcode.com/problems/partition-labels/) | Partition Labels | Advanced | Amazon, Meta |
| [767](https://leetcode.com/problems/reorganize-string/) | Reorganize String | Heap | Google, Meta |
| [860](https://leetcode.com/problems/lemonade-change/) | Lemonade Change | Basics | Amazon |
| [881](https://leetcode.com/problems/boats-to-save-people/) | Boats to Save People | Two Pointer | Amazon |
| [1029](https://leetcode.com/problems/two-city-scheduling/) | Two City Scheduling | Sorting | Amazon |
| [1642](https://leetcode.com/problems/furthest-building-you-can-reach/) | Furthest Building You Can Reach | Heap | Google |

---

## 📅 Study Plan

| Week | Focus | Patterns |
|------|-------|----------|
| Week 1 | Foundation — basics & intervals | Patterns 1, 2 |
| Week 2 | Activity selection & jump games | Patterns 3, 4 |
| Week 3 | Circular problems & scheduling | Patterns 5, 6 |
| Week 4 | Strings & two-pointer greedy | Patterns 7, 8 |
| Week 5 | Heap-based & array greedy | Patterns 9, 10 |
| Week 6 | Sorting tricks & advanced | Patterns 11, 12 |
| Week 7 | Mock interviews | Top 25 Must-Solve List |

---

## 🔗 Related Repos

> Build your complete DSA pattern library:

-  [Sliding Window Patterns](https://github.com/lokeshcs25/Sliding-Window-Patterns-From-Zero-to-N)
-  [Two Pointer Patterns](https://github.com/lokeshcs25/Two-Pointer-Patterns-From-Zero-to-N)
-  [Binary Search Patterns](https://github.com/lokeshcs25/Binary-Search-Patterns-From-Zero-to-N)
-  [Bit Manipulation Patterns](https://github.com/lokeshcs25/Bit-Manipulation-Patterns-From-Zero-to-N)
-  **Greedy Algorithm Patterns** ← You are here
-   Tree Traversal Patterns — *Coming soon*
-   Dynamic Programming Patterns — *Coming soon*
-   Graph Patterns — *Coming soon*

---

## ⭐ Star this repo if it helped you!

> Part of a complete DSA patterns series. One pattern at a time, all the way to FAANG. Good luck! 🚀
