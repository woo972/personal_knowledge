---
tags: algorithm, data_structure, coding_test
---
## 접근 방법
---
- Brute Force: enumerate all possible solutions
- Greedy: keep track of the best answer so far
- Use a Hash Table
	- Tip: 문제에서는 주로 Input array에서 item이 얼마나 많이 등장했는지 체크해야할 때 사용
- 
### 팁
- `O(n^2)` : 이중 루프
- `O(nlogn)` : 정렬, 힙
- `O(n)` : 루프 
- `O(logn)` : 바이너리 서치
- `O(1)` : 해쉬
- 이중~삼중루프가 필요한 문제는 정렬로 풀 수 있는지 고민
- 정렬이 필요한 문제는 정렬 후 이중 포인터, 혹은 바이너리서치가 가능한지 확인
- 정렬이 필요한 문제는 한번의 루프와 해쉬를 조합하여 풀 수 있는 지 고민
## 문제 목록
---
### Amazon
- https://leetcode.com/problems/copy-list-with-random-pointer/  #linked_list #hash_table #tree
- https://leetcode.com/problems/merge-two-sorted-lists/ #linked_list
- https://leetcode.com/problems/valid-anagram/ #hash_table #string #sort
- https://leetcode.com/problems/binary-tree-level-order-traversal #binary_tree #bfs
- https://leetcode.com/problems/search-insert-position/ #binary_search 
- https://leetcode.com/problems/the-kth-factor-of-n/submissions/ #math
- https://leetcode.com/problems/optimal-partition-of-string #string #set
- https://leetcode.com/problems/3sum/ #sorting #two_pointer
	- https://leetcode.com/problems/two-sum/ #hash_table
- https://leetcode.com/problems/count-complete-tree-nodes/ #divide_conquer #dfs
## Appendix
---
- [Coding Interview Preparation | Codinginterview](https://www.codinginterview.com/)
- [Cracking the top Amazon coding interview questions (educative.io)](https://www.educative.io/blog/crack-amazon-coding-interview-questions)
- [Top Interview 150 - Study Plan - LeetCode](https://leetcode.com/studyplan/top-interview-150/)






