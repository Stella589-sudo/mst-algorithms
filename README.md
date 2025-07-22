import heapq
from collections import defaultdict

class Graph:
    def __init__(self, vertices):
        self.V = vertices
        self.graph = defaultdict(list)
        self.edges = []
        
    def add_edge(self, u, v, w):
        self.graph[u].append((v, w))
        self.graph[v].append((u, w))
        self.edges.append((u, v, w))

class UnionFind:
    def __init__(self, size):
        self.parent = list(range(size))
        self.rank = [0] * size
        
    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])
        return self.parent[x]
    
    def union(self, x, y):
        x_root = self.find(x)
        y_root = self.find(y)
        
        if x_root == y_root:
            return False
        
        if self.rank[x_root] < self.rank[y_root]:
            self.parent[x_root] = y_root
        else:
            self.parent[y_root] = x_root
            if self.rank[x_root] == self.rank[y_root]:
                self.rank[x_root] += 1
        return True

def prim_mst(graph):
    mst = []
    visited = [False] * graph.V
    min_heap = []
    
    # Start with vertex 0
    heapq.heappush(min_heap, (0, 0, -1))  # (weight, current_vertex, parent)
    
    while min_heap:
        weight, u, parent = heapq.heappop(min_heap)
        
        if visited[u]:
            continue
            
        visited[u] = True
        if parent != -1:
            mst.append((parent, u, weight))
            
        for v, w in graph.graph[u]:
            if not visited[v]:
                heapq.heappush(min_heap, (w, v, u))
    
    return mst

def kruskal_mst(graph):
    mst = []
    uf = UnionFind(graph.V)
    
    # Sort all edges by weight
    sorted_edges = sorted(graph.edges, key=lambda x: x[2])
    
    for u, v, w in sorted_edges:
        if uf.union(u, v):
            mst.append((u, v, w))
        if len(mst) == graph.V - 1:
            break
    
    return mst

def print_mst(mst):
    total_weight = 0
    print("Edge \tWeight")
    for u, v, w in mst:
        print(f"{u} - {v}\t{w}")
        total_weight += w
    print(f"Total weight: {total_weight}")

# Example usage
if __name__ == "__main__":
    g = Graph(4)
    g.add_edge(0, 1, 10)
    g.add_edge(0, 2, 6)
    g.add_edge(0, 3, 5)
    g.add_edge(1, 3, 15)
    g.add_edge(2, 3, 4)
    
    print("Prim's MST:")
    prim_result = prim_mst(g)
    print_mst(prim_result)
    
    print("\nKruskal's MST:")
    kruskal_result = kruskal_mst(g)
    print_mst(kruskal_result)
