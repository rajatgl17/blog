---
layout: post
title: Why databases use B+ tree for indexing? - Part I 
---

So the first question is - "What is indexing and why we need it?"

Let us start with, how anyone (human/machine) searches. Say you have a table (let's call it `FavMovie`), with structure:

|S.No.| Name  | Movie  |
| ------------ | ------------ | ------------ |
| 1 | Rahul  | 3 Idiots  |
| 2 | Ayush | Raaz  |
| 3 | Nitin   | 3 Idiots  |
| 4 | Pradeep  | Masti  |
| 5 |  Raghav | DDLJ  |

*[Visualize above table with millions of rows]*  
*[S.No. in numerical order]*

And someone asks you to tell `FavMovie` of `Nitin`. Since there is no order/pattern in the `Name` column, you are left with no option other than to search one by one from starting (a full column scan) matching each `Name` with `Nitin` Right?

But if you have one more table (say `NameIndex`), with structure:

| Name | S.No.  |
| ------------ | ------------ |
| Ayush  | 2  |
| Nitin   |  3 |
| Pradeep   | 5  |
|  Raghav  | 4  |
| Rahul  | 1  |

*[Name in lexicographical order]*

Then in this case, what you can do is since you know in  `NameIndex` table, column `Name` is in lexicographical order, you will search the name `Nitin` first and then in `Favourite Movie` table look for the corresponding `S.No.` which is in numerical order.

*[When column are in numerical/lexicographical order you don't need to do full column scan O(n), instead Binary search O(log n) will be way better]*  
*[While writing/inserting data in `FavMovie`, there will be extra cost to maintain `NameIndex`]*

So, using one additional table we were able to improve over search speed on the column `Name`. That's exactly what is indexing.


From [Wikipedia](https://en.wikipedia.org/wiki/Database_index "Wikipedia"):

> A database index is a data structure that improves the speed of data retrieval operations on a database table at the cost of additional writes and storage space to maintain the index data structure.


From the definition above, we move to our second question - "Which data structure is used for indexing?"

Again taking help from a Wikipedia article - [Search data structure](https://en.wikipedia.org/wiki/Search_data_structure "Search data structure"), which lists all data structures used for searching and their amortized analysis. Below table is the extract from the article:

| S.No. | Data Structure | Insert | Delete | Get at index | Search | Space usage |
| ----- | ------ | ------------ | ------------ | ------------ | ------------ | ------------ |
| 1 | Unsorted array | O(1) | O(1) | O(1) | O(n) | O(n) |
| 2 | Sorted array | O(n) | O(n) | O(1) | O(log n) | O(n) |
| 3 | Stack | O(1) | O(1) |  | O(n) | O(n) |
| 4 | Queue | O(1) | O(1) |  | O(n) | O(n) |
| 5 | Unsorted linked list | O(1) | O(1) | O(n) | O(n) | O(n) |
| 6 | Sorted linked list | O(n) | O(1) | O(n) | O(n) | O(n) |
| 7 | Binary search tree (Self-balancing)| O(log n) | O(log n) | N/A | O(log n) | O(n) |
| 8 | Heap | O(log n) | O(log n) | N/A | O(n) | O(n) |
| 9 | Hash table | O(1) | O(1) | N/A | O(1) | O(n) |
| 10 | B-tree | O(log n) | O(log n) | N/A | O(log n) | O(n) |

Let us, now analyze each data structure:

1. Unsorted array - Basically table is an Array of rows. Therefore searching in an unsorted array is equivalent to full scan i.e. O(n) search time. Therefore we can discard it.

2. Sorted array -  In a sorted array, searching will be quite efficient O(log n), but the problem is to maintain the index data structure, there will be a huge impact on insertion and deletion - O(n). Therefore we can discard it as well.

3. Stack - Getting the last inserted element will take O(1) time but for other search purposes, just like an unsorted array it also takes O(n) search time. Therefore we can discard it as well.

4. Queue - Similar to stack (just FIFO instead of LIFO) it also takes O(n) search time. Therefore we can discard it as well.

5. Unsorted linked list - Takes O(n) search time. Therefore we can discard it as well.

6. Sorted linked list - Takes O(n) search time. Therefore we can discard it as well.

7. Binary search tree - O(log n) search time, also insertion and deletion overhead is also O(log n). Best among all, till now. Let us shortlist this.

8. Heap - O(n) search time, so we can discard this.

9. Hash table - As per the above table it is way better than even binary search tree, with O(1) search/insertion/deletion. Must for shortlisting. Right? Let us shortlist this as well.

10. B-Tree - For worst-case analysis, it too has the same properties as of a binary search tree. Since we have shortlisted BST, we must shortlist this as well.


**So, from above we have shortlisted three data structures:**
- Binary search tree (BST)
- Heap
- B-Tree

Let's wrap this post here and compare the above three in the [next post]({% post_url 2020-04-19-why-b-tree-index-part-2 %}).
