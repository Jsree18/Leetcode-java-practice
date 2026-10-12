# Leetcode-java-practice

LeetCode 2897 : Apply Operations on Array to Maximize Sum of Squares

Language: Java
Approach: Greedy Algorithm + Bit Counting

1. Understanding the Problem

We are given an integer array nums and an integer k.
We can repeatedly choose two different positions in the array and perform these operations:

* Replace the first number with the bitwise AND of the two numbers.
* Replace the second number with the bitwise OR of the two numbers.

The goal is to maximize the sum of squares of the first k elements after performing these operations.

The answer must be returned modulo 1,000,000,007 because the result can be very large.

Example

Input
nums = [2, 6, 5, 8]
k = 2

One optimal result for the first two elements is 15 and 6.

15² + 6² = 225 + 36 = 261

Output
261

The challenge is to distribute the available binary bits between the first k elements so that their sum of squares is as large as possible.

2. Understanding the Main Idea

Computers represent numbers using binary digits: 0 and 1.

For example:
Decimal number	Binary representation
2	0010
6	0110
5	0101
8	1000

Every 1 represents a set bit.

The AND and OR operations allow existing set bits to be redistributed between numbers. The important observation is that we can count how many copies of each bit are available instead of repeatedly simulating every operation.

For the example [2, 6, 5, 8]:

* Bit position 3 is available once.
* Bit position 2 is available twice.
* Bit position 1 is available twice.
* Bit position 0 is available once.

We can use these counts to build the first k numbers.

The first constructed number uses all four available bit positions:
8 + 4 + 2 + 1 = 15

After using one copy of each of those bits, the remaining available bits can form the second number: 4 + 2 = 6

Their squared sum is 261.

3. Complete Java Solution

import java.util.*;
class Solution {
    public int maxSum(List<Integer> nums, int k) {
        int MOD = 1_000_000_007;
        int[] bitCount = new int[31];
        for (int num : nums) {
            for (int bit = 0; bit < 31; bit++) {
                if ((num & (1 << bit)) != 0) {
                    bitCount[bit]++;
                }
            }
        }
        long result = 0;
        for (int i = 0; i < k; i++) {
            long num = 0;
            for (int bit = 30; bit >= 0; bit--) {
                if (bitCount[bit] > 0) {
                    num |= (1L << bit);
                    bitCount[bit]--;
                }
            }
            result = (result + num * num) % MOD;
        }
        return (int) result;
    }
}

4. Line-by-Line Code Explanation

Part A — Prepare the program

Line 1
import java.util.*;

Meaning: Imports Java utility classes, including List.
Why it is needed: The input is a List<Integer>, so Java needs to recognize the List type.
Contribution: Allows the method to receive the input list.

Line 2
class Solution {

Meaning: Creates a class named Solution.
Why it is needed: LeetCode expects the solution method to be inside this class.
Contribution: Provides the structure that contains our program.

Line 3
public int maxSum(List<Integer> nums, int k) {

Meaning: Defines the method that solves the problem.
* public allows the testing system to call the method.
* int is the return type.
* maxSum is the method name.
* List<Integer> nums contains the input numbers.
* int k tells us how many leading elements contribute to the answer.
Contribution: Receives the input and provides the entry point for the algorithm.

Line 4
int MOD = 1_000_000_007;

Meaning: Stores the modulo value required by the problem.
Why it is needed: The answer can become very large, so the problem requires the result modulo 1,000,000,007.
The underscores simply make the number easier to read.
Contribution: Defines the value used to keep the answer within the required range.

Line 5
int[] bitCount = new int[31];

Meaning: Creates an array containing 31 counters, all initially zero.
Why it is needed: We need to record how many times each bit position contains a 1.
For example, bitCount[2] = 2 means that two input numbers contain a 1 at bit position 2.

Contribution: Stores the available number of copies of each bit.
Part B — Count the available bits

Line 6
for (int num : nums) {

Meaning: Visits each number in the input list, one at a time.
For [2, 6, 5, 8], the variable num takes the values 2, 6, 5, and 8.
Contribution: Ensures that every input number is examined.

Line 7
for (int bit = 0; bit < 31; bit++) {

Meaning: Checks each bit position from 0 to 30 for the current number.
Why it is needed: A number may contain several 1 bits, and we must inspect each position.
Contribution: Makes sure no relevant bit position is skipped.

Line 8
if ((num & (1 << bit)) != 0) {
Meaning: Checks whether the current bit position contains a 1.

Let’s break it down:
* 1 << bit creates a check for one specific bit position.
* num & (1 << bit) checks whether that bit is set in num.
* != 0 means the bit was found.

For example, 6 in binary is 110. Its bit positions 1 and 2 contain 1; position 0 contains 0.
Contribution: Identifies which bits are available in the current number.

Line 9
bitCount[bit]++;

Meaning: Increases the counter for the bit that was found.
For example, if bit position 2 is present in another input number, its counter increases from 1 to 2.
Contribution: Records how many copies of that bit are available across the entire array.

Lines 10–13
}
}
}

Meaning: Closes the if condition and both loops.
Contribution: Finishes counting all set bits in all input numbers.
After this stage, bitCount contains the information needed to construct the answer.

Part C — Prepare the running total

Line 14
long result = 0;

Meaning: Creates a variable named result and initializes it to zero.
Why long? It can store larger values than an int, which is useful when calculating squares.
Contribution: Stores the running sum of squares.

Line 15
for (int i = 0; i < k; i++) {

Meaning: Repeats the construction process k times.
The counter starts at zero and increases by one after every iteration.
Contribution: Ensures that we construct exactly k numbers.

Part D — Construct each number

Line 16
long num = 0;

Meaning: Starts a new number at zero.
Why it is needed: Every iteration must construct a fresh number rather than continue using the previous one.
Contribution: Stores the number currently being built.

Line 17
for (int bit = 30; bit >= 0; bit--) {

Meaning: Checks bit positions from 30 down to 0.
Why it is needed: We want to consider the higher-value bits first while constructing each number.
Contribution: Makes sure every bit position is considered in the construction stage.

Line 18
if (bitCount[bit] > 0) {

Meaning: Checks whether at least one copy of the current bit remains available.
If its count is zero, there is no copy left to use.
Contribution: Prevents the algorithm from using a bit more times than it is available.

Line 19
num |= (1L << bit);

Meaning: Adds the current bit to the number being constructed.
Breaking it down:
* 1L is the number one represented as a long.
* 1L << bit moves the 1 to the required bit position.
* |= combines that bit with the current number using bitwise OR and saves the result back in num.

For example, if num is 8 and we add the bit worth 4, the result becomes 12.
Contribution: Builds the new number using an available bit.

Line 20
bitCount[bit]--;

Meaning: Decreases the available count of the bit by one.
Why it is needed: Once a copy is used, it must no longer be counted as available for future numbers.
Contribution: Keeps track of the remaining bits for the next iteration.

Lines 21–22
}
}

Meaning: Closes the if condition and the inner loop.
Contribution: Completes construction of one number using the bits that remain available.

Part E — Square the number and update the answer

Line 23
result = (result + num * num) % MOD;

Meaning: Squares the newly constructed number and adds it to the running total.
For example, if num = 15:
num * num = 15 * 15 = 225

The expression then adds 225 to result and takes the remainder after division by MOD.

Contribution: Adds each constructed number’s square to the final answer while applying the required modulo.

Line 24
}

Meaning: Closes the outer loop.
Contribution: Allows the process to continue until all k numbers have been constructed and counted.

Part F — Return the answer

Line 25
return (int) result;

Meaning: Returns the final result as an int.
The cast (int) converts the long value to the method’s required return type. Because the value has been reduced modulo 1,000,000,007, it fits in an int.
Contribution: Gives the computed answer back to LeetCode.

Lines 26–27
}
}

Meaning: Closes the method and the class.
Contribution: Completes the Java program structure.

5. Dry Run — Follow the Values
Input:
nums = [2, 6, 5, 8]
k = 2

After counting the bits:

Bit position	Available copies
3	1
2	2
1	2
0	1

First number
The algorithm uses one copy of every available bit: 8 + 4 + 2 + 1 = 15
Its square is: 15 × 15 = 225
The used bit counts decrease by one.

Second number

The remaining available bits are positions 2 and 1: 4 + 2 = 6
Its square is: 6 × 6 = 36

Final calculation 
225 + 36 = 261

Output: 261

6. Time and Space Complexity
Time complexity: O(n + k)

We examine 31 bit positions for each of the n input numbers and 31 positions for each of the k constructed numbers. Since 31 is a fixed constant, the overall time complexity is O(n + k).

Auxiliary space complexity: O(1)

The algorithm uses a fixed-size array of 31 counters and a few variables. The counter array does not grow with the input size.

7. Key Takeaway

Instead of repeatedly applying AND and OR operations to the array, the solution counts the available set bits and uses those counts to construct the first k numbers.

Each part of the code has a clear responsibility:

1. Count available bits.
2. Construct each target number.
3. Square each number and update the result.
4. Return the final sum modulo 1,000,000,007.

This approach reduces repeated operations to a direct calculation based on the binary representation of the input numbers.
