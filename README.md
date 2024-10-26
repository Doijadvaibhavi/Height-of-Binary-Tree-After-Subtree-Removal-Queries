# Height-of-Binary-Tree-After-Subtree-Removal-Queries

You are given the root of a binary tree with n nodes. Each node is assigned a unique value from 1 to n. You are also given an array queries of size m.

You have to perform m independent queries on the tree where in the ith query you do the following:

Remove the subtree rooted at the node with the value queries[i] from the tree. It is guaranteed that queries[i] will not be equal to the value of the root.
Return an array answer of size m where answer[i] is the height of the tree after performing the ith query.

Note:

The queries are independent, so the tree returns to its initial state after each query.
The height of a tree is the number of edges in the longest simple path from the root to some node in the tree.
 

Example 1:


Input: root = [1,3,4,2,null,6,5,null,null,null,null,null,7], queries = [4]
Output: [2]
Explanation: The diagram above shows the tree after removing the subtree rooted at node with value 4.
The height of the tree is 2 (The path 1 -> 3 -> 2).
Example 2:


Input: root = [5,8,9,2,1,3,7,4,6], queries = [3,2,4,8]
Output: [3,2,3,2]
Explanation: We have the following queries:
- Removing the subtree rooted at node with value 3. The height of the tree becomes 3 (The path 5 -> 8 -> 2 -> 4).
- Removing the subtree rooted at node with value 2. The height of the tree becomes 2 (The path 5 -> 8 -> 1).
- Removing the subtree rooted at node with value 4. The height of the tree becomes 3 (The path 5 -> 8 -> 2 -> 6).
- Removing the subtree rooted at node with value 8. The height of the tree becomes 2 (The path 5 -> 9 -> 3).
 

Constraints:

The number of nodes in the tree is n.
2 <= n <= 105
1 <= Node.val <= n
All the values in the tree are unique.
m == queries.length
1 <= m <= min(n, 104)
1 <= queries[i] <= n
queries[i] != root.val

# SOLUTION

* JAVA CODE
  
class Solution {

    private int[] heights;
    
    private int[] d;
    
    private int[] l;
    
    private int[] r;
    
    private int lenLeaves;

    public int[] treeQueries(TreeNode root, int[] queries) {
    
        heights = new int[50000];
        
        d = new int[100001];
        
        l = new int[100001];
        
        r = new int[100001];
        
        lenLeaves = 0;

        search(root, 0);

        int n = lenLeaves;
        
        int[] maxl = new int[n];
        
        int[] maxr = new int[n];

        for (int i = 1; i < n; i++) {
        
            maxl[i] = Math.max(maxl[i-1], heights[i-1]);
            
        }

        for (int i = n-2; i >= 0; i--) {
        
            maxr[i] = Math.max(maxr[i+1], heights[i+1]);
            
        }

        int[] ret = new int[queries.length];
        
        for (int i = 0; i < queries.length; i++) {
        
            int query = queries[i];
            
            int maxxl = maxl[l[query]];
            
            int maxxr = maxr[r[query]];
            
            ret[i] = Math.max(Math.max(maxxl, maxxr), d[query]-1);
        }

        return ret;
    }

    private void search(TreeNode p, int h) {
    
        d[p.val] = h;

        if (p.left == null && p.right == null) {
        
            heights[lenLeaves] = h;
            
            l[p.val] = r[p.val] = lenLeaves;
            
            lenLeaves++;
            
            return;
            
        }

        l[p.val] = lenLeaves;
        

        if (p.left != null) {
        
            search(p.left, h + 1);
            
        }
        
        if (p.right != null) {
        
            search(p.right, h + 1);
            
        }

        r[p.val] = lenLeaves - 1;
        
    }
    
}


Complexity
Time complexity: O(n)

Space complexity: O(n)

Explained step by step

Data Structure Setup:

# Arrays initialization
heights = [0] * 50000  # Store leaf node heights
d = [0] * 100001       # Store depth of each node
l = [0] * 100001       # Store left boundary index
r = [0] * 100001       # Store right boundary index
len_leaves = 0         # Track number of leaf nodes
Tree Traversal Function:
def search(p: TreeNode, h: int):
    d[p.val] = h  # Record node depth
    
    # Handle leaf node
    if not p.left and not p.right:
        heights[len_leaves] = h
        l[p.val] = r[p.val] = len_leaves
        len_leaves += 1
        return
        
    l[p.val] = len_leaves  # Set left boundary
    
    # Traverse children
    if p.left: search(p.left, h + 1)
    if p.right: search(p.right, h + 1)
    
    r[p.val] = len_leaves - 1  # Set right boundary
Maximum Height Arrays Construction:
n = len_leaves
maxl = [0] * n  # Left-to-right maximum heights
maxr = [0] * n  # Right-to-left maximum heights


# Build prefix and suffix maximums
for i in range(1, n):
    maxl[i] = max(maxl[i-1], heights[i-1])
    maxr[n-i-1] = max(maxr[n-i], heights[n-i])
Query Processing:
ret = []
for query in queries:
    # Find max height excluding queried node's subtree
    maxxl = maxl[l[query]]  # Max height to left
    maxxr = maxr[r[query]]  # Max height to right
    ret.append(max(max(maxxl, maxxr), d[query]-1))
The solution uses DFS to process the tree and record node information
For each node, we track:
Its depth
Left and right boundaries of its subtree in the leaf sequence
Heights of leaf nodes
We build prefix and suffix maximum arrays to quickly find maximum heights
For each query:
We find maximum height outside the queried node's subtree
Compare with (depth-1) to get the final result


