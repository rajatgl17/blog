---
layout: post
title: Why databases use B+ tree for indexing? - Part II (WIP - not complete yet)
---

In the previous article we have seen/shortlisted three data structures that can be used for indexing purpose. If you haven't seen my previous post, you can find it [here]({% post_url 2020-04-19-why-b-tree-index-part-1 %}).

Let us do a recap, before we begin.

| S.No. | Data Structure | Insert | Delete | Get at index | Search | Space usage |
| ------------ | ------------ | ------------ | ------------ | ------------ | ------------ | ------------ |
| 1 | Binary search tree (Self-balancing)| O(log n) | O(log n) | N/A | O(log n) | O(n) |
| 2 | Hash table | O(1) | O(1) | N/A | O(1) | O(n) |
| 3 | B-tree | O(log n) | O(log n) | N/A | O(log n) | O(n) |

For the sake of simplicity lets club `Binary search tree` and `B-tree` as `Tree`, and compare tree with hash table initially.

So, on first look, `Hash table` beats `Tree` on all fronts. Insertion, deletion and search, all are much faster in `Hash table` O(n), then the `Tree` O(log n).

And yes databases too supports `Hash table` based index, but majorly `Tree` based indexes are used. We will first see why `Tree` based indexes are better and then we will see when and where `Hash table` based indexes are used.

### Why `Tree` better than `Hash table` for indexing?
We know in `Hash table` search can be done in O(1) time, but there is a catch.  `Hash table` doesn't support (or support with good enough performance) all operaations. Below table summarizes some of them:


| Operation/Data structure | Hash table | Tree | 
| ----- | ------ | ----- |
| **Equal (=)** | Yes | Yes |
| **RANGE (>,<,Between)** | No | Yes |
| **Sort** | No | Yes |


So as we have seen in the above table, many of the much needed operation required in databases, are not supported by `Hash table`. That's why, `Tree` based indexes are majorly used in databases.
