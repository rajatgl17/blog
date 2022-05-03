---
title: Why databases use B+ tree for indexing? - Part II
layout: post
description: Explained why tree data structures are better than hash for indexing ind databases
author: Rajat Goel
---

In the previous article we have seen/shortlisted three data structures that can be used for indexing purpose. If you haven't seen my previous post, you can find it [here]({{site.baseurl}}{% post_url 2020-04-19-why-b-tree-index-part-1 %}).

Let us do a recap, before we begin.

| S.No. | Data Structure | Insert | Delete | Get at index | Search | Space usage |
| ------------ | ------------ | ------------ | ------------ | ------------ | ------------ | ------------ |
| 1 | Binary search tree (Self-balancing)| O(log n) | O(log n) | N/A | O(log n) | O(n) |
| 2 | Hash table | O(1) | O(1) | N/A | O(1) | O(n) |
| 3 | B-tree | O(log n) | O(log n) | N/A | O(log n) | O(n) |

For the sake of simplicity lets club `Binary search tree` and `B-tree` as `Tree`, and compare tree with hash table initially.

So, on first look, `Hash table` beats `Tree` on all fronts. Insertion, deletion and search, all are much faster in `Hash table` O(1), then the `Tree` O(log n).

And yes databases too supports `Hash table` based index, but majorly `Tree` based indexes are used. We will first see why `Tree` based indexes are better and then we will see when and where `Hash table` based indexes are used.

### Why `Tree` better than `Hash table` for indexing?
We know in `Hash table` search can be done in O(1) time, but there is a catch.  `Hash table` doesn't support (or support with good enough performance) all operaations. Below table summarizes some of them:


| Operation/Data structure | Hash table | Tree | 
| ----- | ------ | ----- |
| **Equal (=)** | Yes | Yes |
| **RANGE (>,<,Between)** | No | Yes |
| **Sort** | No | Yes |


So as we have seen in the above table, many of the much needed operation required in databases, are not supported by `Hash table`. That's why, `Tree` based indexes are majorly used in databases.

### Why B+ Tree and not any other tree?
Firstly we all know, for databases data is persisted in disk and all the data has to be read from disk only. Reading from disk is a much heavier operation than reading from main memory. More the number of trips from main memory to disk, more will be the latency.

Secondly, for disk there is concept of allocation unit size (you must have seen this configuration while formatting a disk). So what is the meaning of allocation unit size. A disk configured with allocation size of 4K means, each read and write will be in multiple of 4K. So even when you write a file of 1K on disk, it takes size of 4K on disk. A file of size 7K will take 8K on disk. Similarlily, while reading every thing will be in multiple of 4K only.

Indexes are also presisted in disk only, they are loaded into main memory whever required. Small indexes can be fully loaded in main memory, but we cant do same for large indexes. Now visualize a very large self balancing binary search tree persisted on disk. While traversing a binary search tree there will be a lot of trips required to the disk, which will add a lot of latency. This is the shortcoming in most of the data structures from `Tree` family,  solved by `B-Tree` family.

From [Wikipedia](https://en.wikipedia.org/wiki/B-tree):

> Unlike other self-balancing binary search trees, the B-tree is well suited for storage systems that read and write relatively large blocks of data, such as disks.

In B+ Tree, there are multiple children node per internal node (persisted in single or continous allocation unit), which heavily reduces the number of trips to the disk and thus less latency. In short B-Trees provides much better performance as compared to most of other Trees when data is in disk, and thus it one of the most used  datastructure for indexing needs.
