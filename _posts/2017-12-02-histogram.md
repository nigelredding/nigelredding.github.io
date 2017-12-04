---
layout: post
title: Random binary trees and histograms
---

Suppose you have a book (with $n$ words total, not the unique count), and you want to 
make a histogram counting the frequencies of each word. If we allow a very small 
chance of failure, we can do this using $O(n)$ bits of memory and $O(n \log n)$ running time.

Let $h$ be a $p=64$ bit hash function. Create a tree where each node is given by
```go
type Tree struct {
        word  string
        hashval  uint64
        count uint64
        left  *Tree
        right *Tree
}
```
When we create a node, we set ``word`` to the word we want, ```hashval``` to the the hash
of the word, and ```count``` to ```1```. We call the first three items of the struct
the "word data".

We start by putting the word data of the first word of the book in a root node.
Then, we iterate through every word of the book. We insert into the binary tree in the usual
way with respect to ```hashval```, except that if we hit a hash of the same value, we increase
the counter by ```1```. If the hash is less than the root hash, insert on the left. If they are equal,
increase ```count``` by one. If it's greater, insert on the right. Repeat recusively.

Then we've got our book in our tree. $O(n)$ bits of space were used. How long did it take to insert
each item? We'll set up some machinery to solve this problem. 

## Random Binary trees

Suppose $S=\\{0,1,\ldots,n-1\\}$. For each permutation $\pi$ of $S$
there is an $n$-tuple $S_\pi = (\pi(0), \pi(1), \ldots, \pi(n-1))$. 
There is also a corresponding binary tree $T_\pi$, which is the binary tree formed by inserting all the entries in the order of $S_\pi$. Clearly different permutations can correspond to the same binary tree.

Fix some $x \in S$. For each $i \in S \setminus \{x\}$, we can let $I_i$ be the random variable given by
\\[ 
I_i(\mu)  =
\begin{cases} 
	1 & \text{if $i$ appears in the search path for $x$ in $T_\mu$} \\ 
	0 & \text{otherwise} 
\end{cases} 
\\]

$$
f(x) = \left\{
        \begin{array}{ll}
            -x & \quad x \leq 0 \\
            x & \quad x > 0
        \end{array}
    \right.
$$


Because of the uniformity of our hash values, we can see that
the expected depth of any given node is $O(\log n)$ and
hence it takes an expected $O(n \log n)$ to insert all the words.

Forget about the book for a second and just consider its set of unique words $S=\{s_1,\ldots,s_m\}$. We still have $n$ words in total, just $m \leq n$ unique words. For each permutation $\pi$ of $S$, there is a tuple \\[ S_\pi = (s_{\pi(1)}, \ldots, s_{\pi(m)}). \\] This tuple $S_\pi$ corresponds to a resulting tree $T_\pi$, where $s_{\pi(1)}, \ldots, s_{\pi(n)\}$ are inserted in order. Different permutations can correspond to the same tree.

Suppose our book corresponds to a permutation $\pi$, and we've inserted our book into the tree. Then we have a tree $T_\pi$. We want to calculate the expected value of any given node. Fix a given item $x \in S$.   