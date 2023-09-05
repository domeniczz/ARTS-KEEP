Year 2023, August, Week 34

## Algorithm

[Blind 75 Must Do](https://leetcode.com/list?selectedList=r327z1f1): [Two Sum](https://leetcode.com/problems/two-sum/)

```java
public static int[] twoSum(int[] nums, int target) {
    /**
     * Key is the number & Value is the index of the number in the int array
     */
    HashMap<Integer, Integer> map = new HashMap<>();
    for (int i = 0; i < nums.length; ++i) {
        int complement = target - nums[i];
        if (map.containsKey(complement)) {
            return new int[] { map.get(complement), i };
        }
        map.put(nums[i], i);
    }
    throw new IllegalArgumentException("No two sum solution");
}
```

The solution utilizes a [HashMap](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/HashMap.html) to store the visited numbers and their indices, which allows for constant time $O(1)$ lookups. This results in a **time complexity of $O(n)$**, where $n$ is the length of the array. The result is more efficient compared to a brute-force solution with a time complexity of $O(n^2)$. The **space complexity is also $O(n)$** due to the HashMap.

This question is pretty easy, but there're still something we can learn and explore.

## Review

### Fukushima Wastewater

**Nature**: [Is Fukushima wastewater release safe? What the science says](https://www.nature.com/articles/d41586-023-02057-y#%3A~%3Atext%3DTEPCO%20says%20that%20the%20resulting%2Cfor%20tritium%20in%20drinking%20water)

### [Incubation](https://en.wikipedia.org/wiki/Incubation)

**Medium**: [How to program in your sleep?](https://bellmar.medium.com/how-to-program-in-your-sleep-58f448a90afb)

I've learned that if you have stuck with a problem for hours, just try setting it aside and go for a walk or doing something to clear your brain. Come back to the problem again after a while, you may discover a new perspective on the problem.

## Technique

[LongAdder](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/atomic/LongAdder.html) in Java is good to use in concurrent scenario, especially high-contention scenario.

How it works under the hood?

`LongAdder` employs a technique called "striping", where it uses multiple variables (an array of `Cell` objects) to distribute contention. When a thread attempts to increment or add to the `LongAdder`, it initially tries to perform the operation on the base value. If it detects contention (i.e., other threads are also trying to update the same value), it uses a hash of the thread's ID to index into the array of `Cell` objects and perform the operation there instead. This reduces the contention by spreading the **updates across multiple `Cell`s**. Summing up the value of a `LongAdder` involves **iterating over these cells and summing their values along with the base value**.

`LongAdder` performs **better** than [AtomicLong](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/atomic/AtomicLong.html) in **high-contention scenarios**. Because `AtomicLong` relies on a single shared variable for updates. When multiple threads try to update the same `AtomicLong` instance concurrently, they compete for the same underlying memory location. This leads to a situation where threads often have to retry their operations due to failed atomic compare-and-swap (CAS) operations, resulting in performance degradation.

## Thoughts

I often find myself in situations where a relatively straightforward problem can hold me up for over an hour, or even several hours. However, once I identify the root cause, I come to realize how simple the solution actually was. When I come across a bug while programming, I tend to review the code I've written recently repeatedly, yet I struggle to uncover anything amiss. I continually reassure myself that the code is robust, only to eventually discover that I've been following a mistaken concept or incorrect approach.

**Lesson learned**: Think out of the silo. Clearing the thoughts and taking a break after exhaustively searching for the cause of the bug might help.