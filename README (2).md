
The Open the Lock* problem (LeetCode 752) is a classical search-based challenge used in competitive programming and Artificial Intelligence (AI). Although the puzzle appears simple—consisting of a four-wheel combination lock—it effectively demonstrates how state-space search, graph modeling, and optimal pathfinding algorithms operate in real computational systems. The lock begins at the fixed initial configuration "0000", and each of the four wheels can rotate independently through digits 0 to 9, wrapping around cyclically. Each move consists of rotating any one wheel by a single step, either forward or backward. The objective is to reach a specified target combination in the *minimum number of moves*. However, the search space is complicated by the presence of deadends—restricted states where the lock becomes permanently stuck. If the search ever enters a deadend configuration, further movement is impossible. Therefore, the algorithm must efficiently navigate the state-space while avoiding these invalid states. The challenge lies in treating each lock combination as a node and each rotation as an edge, forming an implicit graph with 10,000 possible states (0000–9999).

Problem Statement:
-A lock with 4 wheels, each showing a digit from (0–9)

-A list of deadends

-A target combination you must reach

Task: Return the minimum number of moves to reach the target from "0000" without touching any deadend.
If it is impossible, return -1.

Example:

Input: deadends = ["0201","0101","0102","1212","2002"], target = "0202"
Output: 6
Explanation: 
A sequence of valid moves would be "0000" -> "1000" -> "1100" -> "1200" -> "1201" -> "1202" -> "0202".
Note that a sequence like "0000" -> "0001" -> "0002" -> "0102" -> "0202" would be invalid,
because the wheels of the lock become stuck after the display becomes the dead end "0102".

Steps:

1.Represent the Lock: We represent each combination as a string. Example: "0000".
2.Define the Goal and Deadends: Our Target "0202" and Deadends Convert to a set for O(1) lookup. If "0000" is in deadends → return -1 immediately.
3.Define Possible Moves: For each of the 4 wheels, we can rotate up or down. Fot rotate Up we use (digit + 1) % 10 and rotate Down we use (digit - 1 + 10) % 10
4.Breadth-First Search (BFS): Here,
- Initialize a queue
- Each entry = (current_state, number_of_moves)
- Initialize a visited set to avoid cycles
- While the queue is not empty:
    - Pop a state (current_state, steps)
    - If current_state == target → return steps
    - Generate all 8 neighbor states
    - Skip deadends and already visited states
    - Add valid neighbors to the queue with steps + 1
- If queue empties without reaching target → return -1

Why BFS work best: We approach the Open the Lock problem using Breadth-First Search (BFS) because the task requires finding the minimum number of moves to reach the target combination. Each lock combination can be viewed as a node in an unweighted graph, where edges represent valid rotations of the wheels. BFS is ideal in this scenario because it explores all nodes level by level, ensuring that the first time we reach the target, it is through the shortest possible sequence of moves. Other search strategies, such as Depth-First Search (DFS), may explore longer or unnecessary paths first, and cannot guarantee the minimum number of steps. Additionally, BFS allows us to systematically track visited combinations, preventing cycles or repeated states, while efficiently exploring the entire state space of 10,000 possible combinations. By using BFS, we can confidently find the optimal solution, even in the presence of deadends that constrain the valid paths.
from queue import Queue

from queue import Queue

def openLock(deadends, target):
    dead = set(deadends)
    
    if "0000" in dead:
        return -1
    
    visited = set()
    q = Queue()
    q.put(("0000", 0))  # (current_state, number_of_moves)
    
    while not q.empty():
        state, steps = q.get()
        
        if state not in visited:
            if state == target:
                return steps
            
            visited.add(state)
            
            for i in range(4):  # For each wheel
                digit = int(state[i])
                # Rotate up
                up_state = state[:i] + str((digit + 1) % 10) + state[i+1:]
                # Rotate down
                down_state = state[:i] + str((digit - 1 + 10) % 10) + state[i+1:]
                
                for neighbor in (up_state, down_state):
                    if neighbor not in dead:
                        q.put((neighbor, steps + 1))
    
    return -1

# Example usage:
deadends = ["0201","0101","0102","1212","2002"]
target = "0202"
print(openLock(deadends, target))  # Output: 6

In the example where the target is "0202" and the deadends are ["0201","0101","0102","1212","2002"], the BFS algorithm starts at the initial lock state "0000" with 0 moves. It generates all possible neighbors by rotating each of the four wheels up or down. Invalid states that are either deadends or already visited are skipped. The algorithm then explores the next level of valid states in a queue, repeating this process. Using BFS ensures that states are explored in order of increasing moves. For this case, the shortest path to the target is "0000" → "0100" → "0200" → "0202", requiring a total of 6 moves. BFS guarantees that this is the minimum number of moves, because it systematically examines all possible states level by level while avoiding cycles and deadends.
Time Complexity: O(10⁴) – There are 10,000 possible lock combinations (0000–9999), and each combination generates up to 8 neighbors.
Space Complexity: O(10⁴) – The queue and visited set store all explored states.

Key Takeaways:

1.Model the lock as a graph of states, where each node is a combination and edges represent valid wheel rotations.
2.BFS guarantees the shortest path in unweighted graphs.
3.Visited states prevent cycles and repeated exploration.
4.Deadends are skipped, ensuring only valid states are explored.