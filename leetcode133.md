# LeetCode 133 Clone Graph

### Question

[Origin](https://leetcode.com/problems/clone-graph/description/)

Given a reference of a node in a connected undirected graph.

Return a deep copy (clone) of the graph.

Each node in the graph contains a value (`int`) and a list (`List[Node]`) of its neighbors.

```python
class Node:
    def __init__(self, val = 0, neighbors = None):
        self.val = val
        self.neighbors = neighbors if neighbors is not None else []
```

### Answer

```python
class Solution:
    def cloneGraph(self, node: 'Node') -> 'Node':
        if not node: return node
        
        q, clones = deque([node]), {node.val: Node(node.val, [])}
        while q:
            cur = q.popleft() 
            cur_clone = clones[cur.val]            

            for ngbr in cur.neighbors:
                if ngbr.val not in clones:
                    clones[ngbr.val] = Node(ngbr.val, [])
                    q.append(ngbr)
                    
                cur_clone.neighbors.append(clones[ngbr.val])
                
        return clones[node.val]
```

### Explaination

To solve this problem we need two things:

1. BFS to traverse the graph
2. A hash map to keep track of already visited and already cloned nodes

We push a node in the queue and make sure that the node is already cloned. Then we process neighbors. If a neighbor is already cloned and visited, we just append it to the current clone neighbors list, otherwise, we clone it first and append it to the queue to make sure that we can visit it in the next tick.

Time: **O(V + E)** - for BFS       
Space: **O(V)** - for the hashmap        

`dequeue` python [tutorial](https://clay-atlas.com/blog/2020/12/19/python-cn-package-collections-deque/)
