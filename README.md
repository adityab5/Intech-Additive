# Open Book Test - Intech Additive Solution

---

## Question a) String Compression

**Answer:**
In the given code snippet string compression is implemented using the run-length encoding (RLE) compression where consecutive identical characters are replaced with the character followed by its count. For example, `'aabcccccaaa'` becomes `'a2b1c5a3'`.

Now, let’s see the memory optimization analysis in this approach.

```cpp
void compress(string s)
{
    int num = s.length();
    int i=0; 
    while ( i < num) {
        int repetition = 1;
        while (s[i] == s[i+1] && i < num-1 ) {
            repetition++;
            i++;
        }
        cout << s[i] << repetition;
        i++;
    }
}

int main()
{
    string str = "aaaabbcddddd";
    compress(str);
}
```

**Note:** I have used Code Block extension of Google Docs to include code snippets which give proper formatting.

In this provided code: `string str = "aaaabbcddddd"`, `str` is stored in the stack memory and when we are calling the `compress(str)` function, a new copy of the content of `str` is made and allocated on the heap buffer. So each time we have two copies of string data in memory – one inside `main` (`str`) and another inside `compress` (`s`) – which creates the main memory inefficiency here.

This can be solved by passing the string by **const reference**.

So in the function declaration we can use:

```cpp
void compress(const string& s)
```

Using this, when the string parameter comes for the function call then no copy of string is made and thus only a reference is passed. It means that heap memory is now only used once for `str` in `main` function.

According to me this is the most important memory optimization here.

---

### Bonus 1

> The answer should be taken into second compressor and compress further.
> Example: `a2b2c1a3c3` should become `ab2c1ac3`.

For this we are using the compressed string and identify patterns in the first compressed output and compress repeated character-count pairs.

**My Logic:**

* A number appearing multiple times consecutively can be grouped.
* `b2` appears once, so stays as it is.
* Pattern recognition: look for consecutive identical numbers following different characters.

**Implementation idea:**

* To find character-number pairs, we will parse the first compressed string.
* Then we’ll put consecutive pairs in the same count group.
* Then we’ll use the simplified notation to reconstruct.

This requires a different algorithm than RLE because we're compressing based on **count values** and not character repetition.

---

### Bonus 2: `decompress2`

`ab2c1ac3` should return `aabbcaaaccc`.

For this we have to decompress `ab2c1ac3` to `aabbcaaaccc`.

It’s simple to parse the compressed string, like:

**Implementation idea:**

* Character by character, parse the compressed string.
* Verify if the next character after a letter is a digit.
* Repeat the character that many times if there is a digit.
* Assume `count = 1` if there is no next digit.

We also have to think about the testing edge cases also.

---

## Question b) Linked List

**Answer:**
I have referred to the given code in the link to find the nth element of a linked list.

Now, we need to find the k-th element from the end of a singly linked list without knowing its length beforehand.

**Example:**

* Input: List: `1->2->3->4->5` and `k = 2`
* Output: `4`

### Naive Method

I’ll use two passes:

1. In 1st pass, we’ll calculate the length of nodes of the linked list to calculate the length of linked list.
2. In 2nd pass, I’ll calculate the `(length - k)`-th node from start to find the k-th element from end.

Time Complexity: **O(n)**

### Code

```cpp
// C++ program to find Nth node from end of linked list
#include <bits/stdc++.h>
using namespace std;

// Linked list node
struct Node {
    int data;
    Node* next;

    Node(int new_data) {
        data = new_data;
        next = nullptr;
    }
};

// Function to find the Nth node from the end of a linked list
int findNthFromLast(Node* head, int N) {
    int length = 0;

    Node* temp = head;

    // Count number of nodes
    while (temp != nullptr) {
        length++;
        temp = temp->next;
    }

    // If N is greater than length
    if (N > length || N <= 0)
        return -1;

    // Move to (length - N)th node (0-indexed)
    temp = head;
    for (int i = 1; i < length - N + 1; i++)
        temp = temp->next;

    return temp->data;
}

int main() {
    // Linked list: 1 -> 2 -> 3 -> 4 -> 5
    Node* head = new Node(1);
    head->next = new Node(2);
    head->next->next = new Node(3);
    head->next->next->next = new Node(4);
    head->next->next->next->next = new Node(5);

    // Find 2nd node from last
    cout << findNthFromLast(head, 2);

    return 0;
}
```

**Note:** I have used Code Block extension of Google Docs to include code snippets which give proper formatting.

---

### Bonus 1

We have to minimize the number of times we run through the loop.

#### Optimal Method (Two Pointers)

I’ll use **two pointer approach** to reduce the number of iterations.

**Algorithm:**

* Move the `fast` pointer `k` nodes ahead using two pointers, `fast` and `slow`, both of which begin at the head.
* Until `fast` reaches the end, move both pointers together.
* `slow` is at the k-th node from the end when `fast` is at the final node.

**Pseudocode:**

```plaintext
function findKthFromLast(head, k):
    if head is null:
        return null
    
    fast = head
    slow = head
    
    // Move fast pointer k steps ahead
    for i = 0 to k-1:
        if fast is null:
            return null  // it means that k is larger than list length
        fast = fast.next
    
    // Moving both pointers until fast reaches end of list
    while fast.next is not null:
        fast = fast.next
        slow = slow.next
    
    return slow
```

**Note:** I have used Code Block extension of Google Docs to include code snippets which give proper formatting.

Time Complexity: **O(n)**

It will look like time complexity of both the approaches is same but in the second approach of two pointers we are reducing the number of loops like a single pass which is what our aim was in the given question and also we don’t need to find the length of the list before.

---

## Question c) Stack Minimum

**Answer:**

In order to support a `min()` function on a stack in **O(1)**, we must keep track of the minimum at all times, not just the value that is currently at the top.

I will store a pair rather than just the value in the stack:

* `value`: the value that was actually pushed.
* `currentMin`: the current minimum of all elements.

Every time we push:

```text
currentMin = min(x, previous currentMin)
```

That way:

* `push(x)` → O(1)
* `pop()`   → O(1)
* `top()`   → O(1)
* `min()`   → O(1) (I’ll simply return the top element's currentMin)

I don’t need a separate min stack; I just store running min along with each element.

### Code

```cpp
#include <bits/stdc++.h>
using namespace std;

class MinStack {
    // Each element stores: {value, current minimum up to this element}
    stack<pair<int, int>> st;

public:
    // Push element x onto stack
    void push(int x) {
        if (st.empty()) {
            // When stack is empty, x is the minimum
            st.push({x, x});
        } else {
            int currentMin = min(x, st.top().second);
            st.push({x, currentMin});
        }
    }

    // Remove top element
    void pop() {
        if (!st.empty()) {
            st.pop();
        }
    }

    // Get top element
    int top() {
        if (st.empty()) {
            throw runtime_error("Stack is empty");
        }
        return st.top().first;
    }

    // Get minimum element in O(1)
    int getMin() {
        if (st.empty()) {
            throw runtime_error("Stack is empty");
        }
        return st.top().second;
    }

    // Check if stack is empty
    bool empty() {
        return st.empty();
    }
};

int main() {
    MinStack s;
    s.push(5);
    s.push(3);
    s.push(7);
    s.push(2);

    cout << "Current min: " << s.getMin() << endl; // 2
    s.pop(); // pop 2
    cout << "Current min: " << s.getMin() << endl; // 3
    s.pop(); // pop 7
    cout << "Current min: " << s.getMin() << endl; // 3
    s.pop(); // pop 3
    cout << "Current min: " << s.getMin() << endl; // 5

    return 0;
}
```

**Note:** I have used Code Block extension of Google Docs to include code snippets which give proper formatting.

**Explanation:**

* `getMin()` reads `st.top().second` which will be in constant time.
* There are no loops, no traversal and thus **O(1)** per operation.

---

### Bonus 1: Real-world use case where stack is better than array

**Answer:**
I would like to take an example of **undo/redo functionality** in a text editor (like VS Code, Word, etc.)

**Details:**

* Every time I type/delete something, that action is **pushed** onto an undo stack.
* When I press **undo**, the last action is **popped** from the stack and reverted.
* If I then press **redo**, it may be pushed onto another stack (redo stack).

A stack is better than using a normal array for undo/redo functionality because the required behavior is **LIFO (Last In, First Out)**, where the most recent action must always be reverted first, so here a stack is preferable to using a regular array.

---

## Question d) Trapping Rainwater – Analog Version

**Answer:**

I have gone through the given code of the infamous problem of trapping rain water.

**Example:**

* Input: `[2, 1, 3, 0, 1, 2, 3]`
* Output: `7` units

This approach works in **O(n)** time.

And now, I have given an equation of function that is bounded, and I have to predict how many units of water get trapped.

**Solution:**

As I was unfamiliar with the methods of solving the analog version, I spent time and read numerous sources that explained how discrete algorithms could be expanded into continuous functions.

It occurred to me that it is essentially the same idea: at discrete points we add water; at the continuous points along the continuous curve `f(x)` we take water into account.

The identical principles as in that GFG solution are applicable:

* At each position `x`, consider the **maximum on the left** and the **maximum on the right** side of the curve.
* The water height at `x` is basically the **minimum** of these two heights.
* The depth of water at `x` is simply the difference between this water level and the roof height `f(x)`.

So if:

* `L(x)` = maximum height of the function to the **left** of `x`.
* `R(x)` = maximum height to the **right** of `x`.

Then the depth of the water at the position `x` becomes:

```text
water(x) = min(L(x), R(x)) – f(x)
```

The total water is calculated by integrating this over the entire roof:

```text
Total Water = ∫ (min(L(x), R(x)) – f(x)) dx
```

This is just the GeeksforGeeks discrete solution's **continuous (analog) version** of its logic.

According to most of the blogs, for actual roofs in the real world:

* We sample the continuous curve at very small intervals.
* Transform it to a high resolution array.
* Use the same GeeksforGeeks algorithm.
* Multiply the sum by the sampling width `Δx`.

This will be a pretty close estimate of the amount of water on a continuous roof.

**Declaration:**
I understood the discrete solution by referring to the GeeksforGeeks article. Then I extended the idea to analog roofs by reading online resources explaining how the same logic can be applied to continuous functions using integration or fine-grained sampling.

---

## Question e) Coin Change Problem

**Answer:**

I would like to make the understanding of the problem more clear, like suppose someone is a cashier who needs to give exact change to customers using the **minimum number of coins**.

**Example:**

* Available coins: `[1, 2, 5, 8, 10]`
* Change needed: `7`
* Answer: `[5, 2]` (2 coins total - this is optimal!)

So here the question is: **Why Is This Tricky?**

One may suppose that you should use as large a coin as you can. But that is not always the right thing to do as **greedy** always picks the largest coin possible.

Example:

```text
Coins = [1, 3, 4], Amount = 6
Greedy: 4 + 1 + 1 = 3 coins (wrong output)
Optimal: 3 + 3 = 2 coins (correct output)
```

---

### For this optimal solution we’ll here use **DP approach**

**Core Idea:** Build solutions for **small amounts first**, then use them for **larger amounts**.

Define:

```text
dp[amount] = min number of coins for that amount
```

For each amount:

```text
For each coin:
    dp[amount] = min(dp[amount], dp[amount - coin] + 1)
```

---

### Visual Example

Coins = `[1, 2, 5]`, Amount = `7`

```text
Amount:     0   1   2   3   4   5   6   7
Min Coins:  0   1   1   2   2   1   2   2
```

How we got `dp[7]`:

* Try coin `1`: `dp[6] + 1 = 2 + 1 = 3`
* Try coin `2`: `dp[5] + 1 = 1 + 1 = 2`  (best answer)
* Try coin `5`: `dp[2] + 1 = 1 + 1 = 2`

**Answer:** `2` coins

* Time Complexity: `O(amount × number_of_coins)`
* Space Complexity: `O(amount)`

**Conclusion:**
Greedy is fast, but it can give wrong answers for arbitrary sets of coins. Dynamic programming ensures the minimum number of coins, avoids repetitive work, and builds solutions from smaller amounts upward by reusing previously calculated results.

---

### Bonus Question: Remove One Digit using Greedy Approach

**Problem:** Remove exactly ONE digit to get the **largest** possible number.

**Answer:** My logic will be to scan left to right then remove the first digit that is smaller than the digit after it.

#### Code

```cpp
#include <iostream>
#include <string>
using namespace std;

int removeDigit(int n) {
    string s = to_string(n);

    // Find first digit where current < next
    for (int i = 0; i < s.length() - 1; i++) {
        if (s[i] < s[i + 1]) {
            return stoi(s.substr(0, i) + s.substr(i + 1));
        }
    }

    // All digits are in non-increasing order -> remove last digit
    return stoi(s.substr(0, s.length() - 1));
}

int main() {
    int n = 3821;
    cout << removeDigit(n);
    return 0;
}
```

**Note:** I have used Code Block extension of Google Docs to include code snippets which give proper formatting.

Time Complexity: **O(n)**

**Why did we use the Greedy method?**

According to me, mainly three reasons:

1. Make a **local optimum decision** - simply drop the first “bad” digit I find.
2. **No regrets** - once I make that choice, I don’t look back.
3. Left-to-right scan ensures we preserve most significant high digits.

Greedy works since the earlier digits are more significant. The first digit that is smaller than the next is the one to remove.

---

**Declaration:**
I am still trying to figure out what dynamic programming and greedy algorithms are. To get a bare minimum of the basics I went to GeeksforGeeks, and read a host of posts on greedy algorithms, and read a bunch of online tutorials on DP so I would get to see how all of these ideas relate to the coin-change problem. I began implementing the ideas in solving the assignment after revising the basics.

---

## Question f) Dot product and Cross product

**Answer:**

### Dot Product

A single scalar number can be obtained by performing the dot product on two vectors.

Let **A = (Ax, Ay, Az)** and **B = (Bx, By, Bz)**:

```text
A ⋅ B = AxBx + AyBy + AzBz
```

It also means:

```text
A ⋅ B = |A||B|cos(θ)
```

Therefore, the dot product indicates the degree of **similarity** between two directions.

If:

* Dot > 0 and angle < 90° → vectors point roughly in the same direction
* Dot = 0 → vectors are perpendicular
* Dot < 0 and angle > 90° → vectors point in opposing directions

#### Use Cases of Dot Product in Graphics

* Finding projection of one vector onto another
* Computing angles between directions
* Lighting calculations (Lambertian shading)

  * Dot product between surface normal **N** and light direction **L** gives brightness:
    `I = k · (N⋅L)`

**References I studied:**

* [https://www.geeksforgeeks.org/dot-product-two-vectors/](https://www.geeksforgeeks.org/dot-product-two-vectors/)
* [https://www.khanacademy.org/math/linear-algebra/vectors-and-spaces](https://www.khanacademy.org/math/linear-algebra/vectors-and-spaces)

---

### Cross Product

The cross product takes two vectors and returns a new vector that is:

* perpendicular to both
* direction based on right-hand rule
* magnitude = area of the parallelogram formed by the vectors

For **A × B**, the formula is:

```text
A × B = (AyBz − AzBy, AzBx − AxBz, AxBy − AyBx)
```

#### Use Cases of Cross Product in Graphics

* Finding surface normals
* Checking orientation (clockwise vs counterclockwise)
* Computing torques and rotations

**References I studied:**

* [https://www.geeksforgeeks.org/cross-product-two-vector/](https://www.geeksforgeeks.org/cross-product-two-vector/)
* [https://www.khanacademy.org/math/linear-algebra/vectors-and-spaces](https://www.khanacademy.org/math/linear-algebra/vectors-and-spaces)

---

### Bonus: Intersection Tests Used in Graphics

#### Ray–Plane Intersection

Ray:

```text
R(t) = O + tD
```

Plane:

```text
N⋅P = d
```

Intersection happens when:

```text
t = (d − N⋅O) / (N⋅D)
```

#### Ray–Triangle Intersection

Given triangle vertices `V0, V1, V2`:

```text
O + tD = V0 + u(V1 − V0) + v(V2 − V0)
```

Final conditions:

* `u ≥ 0`
* `v ≥ 0`
* `u + v ≤ 1`
* `t > 0`

This detects intersections and gives exact hit points.

---

## Question g) Code I’m Proud Of

**Answer:**

I believe that one of the codes that I am the most proud of is my solution to finding the k-th node from the end of a linked list. I first wrote a two-pass method and then improved it into a one-pass two-pointer solution later.

**Problem:**
Given a singly linked list, return the node that is k positions from the end.
Example: For `1->2->2->3->4->5` and `k = 2`, the answer is `4`.

### My One-Pass Solution (Two Pointer Method)

```cpp
Node* findKthFromEnd(Node* head, int k) {
    if (!head || k <= 0) return nullptr;
    Node* fast = head;
    Node* slow = head;

    // Move fast pointer k steps ahead
    for (int i = 0; i < k; i++) {
        if (!fast) return nullptr;  // k > length
        fast = fast->next;
    }

    // Move both pointers until fast reaches the end
    while (fast) {
        fast = fast->next;
        slow = slow->next;
    }

    return slow;
}
```

**Note:** I have used Code Block extension of Google Docs to include code snippets which give proper formatting.

### Why I’m Proud of This Code

* It requires **O(n)** time but doesn’t need two passes.
* It uses a clean **two-pointer technique**, which is very common in interviews.
* It handles all edge cases: empty list, `k = 0`, and `k > length`.
* I first wrote the slower version and then optimized it after learning the two-pointer approach, which shows my ability to refactor and improve.

---

## Question h) Debugging Random Crashes

**Answer:**

### Initial Observation

**Symptoms:**

* Crashes are random and non-deterministic.
* It never crashes in the same place.
* Even when a debugger is attached, the crash location keeps moving.

This is often an indication of:

* Memory corruption
* Race conditions
* Hardware/architecture issues

rather than simple logic bugs.

---

### Memory Corruption Issues

**A. Buffer overflow/underflow:**

```c
char buffer[10];
strcpy(buffer, "this is too long ...");
```

This scribbles on the neighboring memory and may go on to explode later.

**B. Use-after-free:**

```c
int* pointer = (int*)malloc(100);
free(pointer);
// ...
*pointer = 5;  // Writing on memory that is already free!
```

**C. Double free:**
Releases allocator metadata twice, and further allocations subsequently can be completely unpredictable and cause crashes.

**D. Uninitialized memory:**
Sometimes pointers seem valid, other times they are garbage and crash positions become random.

---

### Race Conditions (Multithreading)

**A. Data races:**
Two threads are writing and reading the same memory without proper synchronization → bizarre, unpredictable behavior.

**B. Lack of synchronization:**
Bad locking logic can lead to state changes between checks.

---

### Stack Corruption

**A. Stack overflow:**
Qverwriting return address via huge local arrays or deep recursion.

**B. Invalid return pointer:**
Crash may occur later when function locals are accessed after the function has returned.

---

### Hardware/System Issues

* Bad RAM
* Thermal issues
* Power problems
* Rare cosmic ray bit flips

All of these can cause random crashes, though they are less common compared to software bugs.

---

### Compiler/Optimization Bugs

The compiler may rearrange or even drop code when undefined behavior is present, leading to bizarre behavior.

---

### Investigation Strategy

**Phase 1: Reproduce at scale**

* Vary thread counts
* Vary memory usage patterns

**Phase 2: Use tracing tools:**

* AddressSanitizer (ASan)
* UndefinedBehaviorSanitizer (UBSan)
* ThreadSanitizer (TSan)
* Valgrind

**Phase 3:**
Use core dumps, `gdb`, logs, canaries and memory poison patterns to locate corruption points.

---

### Deep Dive: Computer Architecture Perspective

* Cache coherence can cause stale data or reordered operations between runs.
* CPU reordering may affect how instructions appear to run.
* ASLR changes memory layout every run, so an overflow may corrupt different addresses.
* Virtual memory, non-deterministic scheduling, and page faults contribute to Heisenbugs.
* CPU pipelines and speculative execution may dereference bad pointers before checks.

**Conclusion:**
Most of the time, random crashes indicate **corruption**. The point where it crashed is only an effect, not the true cause. We must go back to locate where memory or state was first corrupted.

---

## DECLARATION

I declare that I have done the above work by myself and not worked with anyone or got help from any individual on the internet.

**Name:** Aditya Bhushan
