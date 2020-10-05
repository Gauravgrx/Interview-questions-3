# Interview-questions-3

5) Graphs

ix) SCC in graph using kosaRaju's mathod 


#include <iostream> 
#include <list> 
#include <stack> 
using namespace std; 

class Graph 
{ 
	int V; // No. of vertices 
	list<int> *adj; // An array of adjacency lists 

	// Fills Stack with vertices (in increasing order of finishing 
	// times). The top element of stack has the maximum finishing 
	// time 
	void fillOrder(int v, bool visited[], stack<int> &Stack); 

	// A recursive function to print DFS starting from v 
	void DFSUtil(int v, bool visited[]); 
public: 
	Graph(int V); 
	void addEdge(int v, int w); 

	// The main function that finds and prints strongly connected 
	// components 
	void printSCCs(); 

	// Function that returns reverse (or transpose) of this graph 
	Graph getTranspose(); 
}; 

Graph::Graph(int V) 
{ 
	this->V = V; 
	adj = new list<int>[V]; 
} 

// A recursive function to print DFS starting from v 
void Graph::DFSUtil(int v, bool visited[]) 
{ 
	// Mark the current node as visited and print it 
	visited[v] = true; 
	cout << v << " "; 

	// Recur for all the vertices adjacent to this vertex 
	list<int>::iterator i; 
	for (i = adj[v].begin(); i != adj[v].end(); ++i) 
		if (!visited[*i]) 
			DFSUtil(*i, visited); 
} 

Graph Graph::getTranspose() 
{ 
	Graph g(V); 
	for (int v = 0; v < V; v++) 
	{ 
		// Recur for all the vertices adjacent to this vertex 
		list<int>::iterator i; 
		for(i = adj[v].begin(); i != adj[v].end(); ++i) 
		{ 
			g.adj[*i].push_back(v); 
		} 
	} 
	return g; 
} 

void Graph::addEdge(int v, int w) 
{ 
	adj[v].push_back(w); // Add w to v’s list. 
} 

void Graph::fillOrder(int v, bool visited[], stack<int> &Stack) 
{ 
	// Mark the current node as visited and print it 
	visited[v] = true; 

	// Recur for all the vertices adjacent to this vertex 
	list<int>::iterator i; 
	for(i = adj[v].begin(); i != adj[v].end(); ++i) 
		if(!visited[*i]) 
			fillOrder(*i, visited, Stack); 

	// All vertices reachable from v are processed by now, push v 
	Stack.push(v); 
} 

// The main function that finds and prints all strongly connected 
// components 
void Graph::printSCCs() 
{ 
	stack<int> Stack; 

	// Mark all the vertices as not visited (For first DFS) 
	bool *visited = new bool[V]; 
	for(int i = 0; i < V; i++) 
		visited[i] = false; 

	// Fill vertices in stack according to their finishing times 
	for(int i = 0; i < V; i++) 
		if(visited[i] == false) 
			fillOrder(i, visited, Stack); 

	// Create a reversed graph 
	Graph gr = getTranspose(); 

	// Mark all the vertices as not visited (For second DFS) 
	for(int i = 0; i < V; i++) 
		visited[i] = false; 

	// Now process all vertices in order defined by Stack 
	while (Stack.empty() == false) 
	{ 
		// Pop a vertex from stack 
		int v = Stack.top(); 
		Stack.pop(); 

		// Print Strongly connected component of the popped vertex 
		if (visited[v] == false) 
		{ 
			gr.DFSUtil(v, visited); 
			cout << endl; 
		} 
	} 
} 
 
int main() 
{ 
	// Create a graph given in the above diagram 
	Graph g(5); 
	g.addEdge(1, 0); 
	g.addEdge(0, 2); 
	g.addEdge(2, 1); 
	g.addEdge(0, 3); 
	g.addEdge(3, 4); 

	cout << "Following are strongly connected components in "
			"given graph \n"; 
	g.printSCCs(); 

	return 0; 
} 

X) Dijstra's algorithm


#include <limits.h> 
#include <stdio.h> 

// Number of vertices in the graph 
#define V 9 

// A utility function to find the vertex with minimum distance value, from 
// the set of vertices not yet included in shortest path tree 
int minDistance(int dist[], bool sptSet[]) 
{ 
	// Initialize min value 
	int min = INT_MAX, min_index; 

	for (int v = 0; v < V; v++) 
		if (sptSet[v] == false && dist[v] <= min) 
			min = dist[v], min_index = v; 

	return min_index; 
} 

// A utility function to print the constructed distance array 
void printSolution(int dist[]) 
{ 
	printf("Vertex \t\t Distance from Source\n"); 
	for (int i = 0; i < V; i++) 
		printf("%d \t\t %d\n", i, dist[i]); 
} 

// Function that implements Dijkstra's single source shortest path algorithm 
// for a graph represented using adjacency matrix representation 
void dijkstra(int graph[V][V], int src) 
{ 
	int dist[V]; // The output array. dist[i] will hold the shortest 
	// distance from src to i 

	bool sptSet[V]; // sptSet[i] will be true if vertex i is included in shortest 
	// path tree or shortest distance from src to i is finalized 

	// Initialize all distances as INFINITE and stpSet[] as false 
	for (int i = 0; i < V; i++) 
		dist[i] = INT_MAX, sptSet[i] = false; 

	// Distance of source vertex from itself is always 0 
	dist[src] = 0; 

	// Find shortest path for all vertices 
	for (int count = 0; count < V - 1; count++) { 
		// Pick the minimum distance vertex from the set of vertices not 
		// yet processed. u is always equal to src in the first iteration. 
		int u = minDistance(dist, sptSet); 

		// Mark the picked vertex as processed 
		sptSet[u] = true; 

		// Update dist value of the adjacent vertices of the picked vertex. 
		for (int v = 0; v < V; v++) 

			// Update dist[v] only if is not in sptSet, there is an edge from 
			// u to v, and total weight of path from src to v through u is 
			// smaller than current value of dist[v] 
			if (!sptSet[v] && graph[u][v] && dist[u] != INT_MAX 
				&& dist[u] + graph[u][v] < dist[v]) 
				dist[v] = dist[u] + graph[u][v]; 
	} 

	// print the constructed distance array 
	printSolution(dist); 
} 

 
int main() 
{ 
	/* Let us create the example graph discussed above */
	int graph[V][V] = { { 0, 4, 0, 0, 0, 0, 0, 8, 0 }, 
						{ 4, 0, 8, 0, 0, 0, 0, 11, 0 }, 
						{ 0, 8, 0, 7, 0, 4, 0, 0, 2 }, 
						{ 0, 0, 7, 0, 9, 14, 0, 0, 0 }, 
						{ 0, 0, 0, 9, 0, 10, 0, 0, 0 }, 
						{ 0, 0, 4, 14, 10, 0, 2, 0, 0 }, 
						{ 0, 0, 0, 0, 0, 2, 0, 1, 6 }, 
						{ 8, 11, 0, 0, 0, 0, 1, 0, 7 }, 
						{ 0, 0, 2, 0, 0, 0, 6, 7, 0 } }; 

	dijkstra(graph, 0); 

	return 0; 
} 

xi) Bellman Ford Agorithm


#include <bits/stdc++.h> 
using namespace std; 

// The main function that finds shortest 
// distances from src to all other vertices 
// using Bellman-Ford algorithm. The function 
// also detects negative weight cycle 
// The row graph[i] represents i-th edge with 
// three values u, v and w. 
void BellmanFord(int graph[][3], int V, int E, 
				int src) 
{ 
	// Initialize distance of all vertices as infinite. 
	int dis[V]; 
	for (int i = 0; i < V; i++) 
		dis[i] = INT_MAX; 

	// initialize distance of source as 0 
	dis[src] = 0; 

	// Relax all edges |V| - 1 times. A simple 
	// shortest path from src to any other 
	// vertex can have at-most |V| - 1 edges 
	for (int i = 0; i < V - 1; i++) { 

		for (int j = 0; j < E; j++) { 
			if (dis[graph[j][0]] + graph[j][2] < 
							dis[graph[j][1]]) 
				dis[graph[j][1]] = 
				dis[graph[j][0]] + graph[j][2]; 
		} 
	} 

	// check for negative-weight cycles. 
	// The above step guarantees shortest 
	// distances if graph doesn't contain 
	// negative weight cycle. If we get a 
	// shorter path, then there is a cycle. 
	for (int i = 0; i < E; i++) { 
		int x = graph[i][0]; 
		int y = graph[i][1]; 
		int weight = graph[i][2]; 
		if (dis[x] != INT_MAX && 
				dis[x] + weight < dis[y]) 
			cout << "Graph contains negative"
					" weight cycle"
				<< endl; 
	} 

	cout << "Vertex Distance from Source" << endl; 
	for (int i = 0; i < V; i++) 
		cout << i << "\t\t" << dis[i] << endl; 
} 

 
int main() 
{ 
	int V = 5; // Number of vertices in graph 
	int E = 8; // Number of edges in graph 

	// Every edge has three values (u, v, w) where 
	// the edge is from vertex u to v. And weight 
	// of the edge is w. 
	int graph[][3] = { { 0, 1, -1 }, { 0, 2, 4 }, 
					{ 1, 2, 3 }, { 1, 3, 2 }, 
					{ 1, 4, 2 }, { 3, 2, 5 }, 
					{ 3, 1, 1 }, { 4, 3, -3 } }; 

	BellmanFord(graph, V, E, 0); 
	return 0; 
} 

Xii) Floyd Warshall Algorithm

 
#include <bits/stdc++.h> 
using namespace std; 

// Number of vertices in the graph 
#define V 4 

/* Define Infinite as a large enough 
value.This value will be used for 
vertices not connected to each other */
#define INF 99999 

// A function to print the solution matrix 
void printSolution(int dist[][V]); 

// Solves the all-pairs shortest path 
// problem using Floyd Warshall algorithm 
void floydWarshall (int graph[][V]) 
{ 
	/* dist[][] will be the output matrix 
	that will finally have the shortest 
	distances between every pair of vertices */
	int dist[V][V], i, j, k; 

	/* Initialize the solution matrix same 
	as input graph matrix. Or we can say 
	the initial values of shortest distances 
	are based on shortest paths considering 
	no intermediate vertex. */
	for (i = 0; i < V; i++) 
		for (j = 0; j < V; j++) 
			dist[i][j] = graph[i][j]; 

	/* Add all vertices one by one to 
	the set of intermediate vertices. 
	---> Before start of an iteration, 
	we have shortest distances between all 
	pairs of vertices such that the 
	shortest distances consider only the 
	vertices in set {0, 1, 2, .. k-1} as 
	intermediate vertices. 
	----> After the end of an iteration, 
	vertex no. k is added to the set of 
	intermediate vertices and the set becomes {0, 1, 2, .. k} */
	for (k = 0; k < V; k++) 
	{ 
		// Pick all vertices as source one by one 
		for (i = 0; i < V; i++) 
		{ 
			// Pick all vertices as destination for the 
			// above picked source 
			for (j = 0; j < V; j++) 
			{ 
				// If vertex k is on the shortest path from 
				// i to j, then update the value of dist[i][j] 
				if (dist[i][k] + dist[k][j] < dist[i][j]) 
					dist[i][j] = dist[i][k] + dist[k][j]; 
			} 
		} 
	} 

	// Print the shortest distance matrix 
	printSolution(dist); 
} 

/* A utility function to print solution */
void printSolution(int dist[][V]) 
{ 
	cout<<"The following matrix shows the shortest distances"
			" between every pair of vertices \n"; 
	for (int i = 0; i < V; i++) 
	{ 
		for (int j = 0; j < V; j++) 
		{ 
			if (dist[i][j] == INF) 
				cout<<"INF"<<"	 "; 
			else
				cout<<dist[i][j]<<"	 "; 
		} 
		cout<<endl; 
	} 
} 

 
int main() 
{ 
	/* Let us create the following weighted graph 
			10 
	(0)------->(3) 
		|	 /|\ 
	5 |	 | 
		|	 | 1 
	\|/	 | 
	(1)------->(2) 
			3	 */
	int graph[V][V] = { {0, 5, INF, 10}, 
						{INF, 0, 3, INF}, 
						{INF, INF, 0, 1}, 
						{INF, INF, INF, 0} 
					}; 

	// Print the solution 
	floydWarshall(graph); 
	return 0; 
}  


Xiii)  MST using prim's algorithm 


#include <bits/stdc++.h>
using namespace std;

// Number of vertices in the graph 
#define V 5 

// A utility function to find the vertex with 
// minimum key value, from the set of vertices 
// not yet included in MST 
int minKey(int key[], bool mstSet[]) 
{ 
	// Initialize min value 
	int min = INT_MAX, min_index; 

	for (int v = 0; v < V; v++) 
		if (mstSet[v] == false && key[v] < min) 
			min = key[v], min_index = v; 

	return min_index; 
} 

// A utility function to print the 
// constructed MST stored in parent[] 
void printMST(int parent[], int graph[V][V]) 
{ 
	cout<<"Edge \tWeight\n"; 
	for (int i = 1; i < V; i++) 
		cout<<parent[i]<<" - "<<i<<" \t"<<graph[i][parent[i]]<<" \n"; 
} 

// Function to construct and print MST for 
// a graph represented using adjacency 
// matrix representation 
void primMST(int graph[V][V]) 
{ 
	// Array to store constructed MST 
	int parent[V]; 
	
	// Key values used to pick minimum weight edge in cut 
	int key[V]; 
	
	// To represent set of vertices included in MST 
	bool mstSet[V]; 

	// Initialize all keys as INFINITE 
	for (int i = 0; i < V; i++) 
		key[i] = INT_MAX, mstSet[i] = false; 

	// Always include first 1st vertex in MST. 
	// Make key 0 so that this vertex is picked as first vertex. 
	key[0] = 0; 
	parent[0] = -1; // First node is always root of MST 

	// The MST will have V vertices 
	for (int count = 0; count < V - 1; count++)
	{ 
		// Pick the minimum key vertex from the 
		// set of vertices not yet included in MST 
		int u = minKey(key, mstSet); 

		// Add the picked vertex to the MST Set 
		mstSet[u] = true; 

		// Update key value and parent index of 
		// the adjacent vertices of the picked vertex. 
		// Consider only those vertices which are not 
		// yet included in MST 
		for (int v = 0; v < V; v++) 

			// graph[u][v] is non zero only for adjacent vertices of m 
			// mstSet[v] is false for vertices not yet included in MST 
			// Update the key only if graph[u][v] is smaller than key[v] 
			if (graph[u][v] && mstSet[v] == false && graph[u][v] < key[v]) 
				parent[v] = u, key[v] = graph[u][v]; 
	} 

	// print the constructed MST 
	printMST(parent, graph); 
} 

int main() 
{ 
	/* Let us create the following graph 
		2 3 
	(0)--(1)--(2) 
	| / \ | 
	6| 8/ \5 |7 
	| / \ | 
	(3)-------(4) 
			9	 */
	int graph[V][V] = { { 0, 2, 0, 6, 0 }, 
						{ 2, 0, 3, 8, 5 }, 
						{ 0, 3, 0, 0, 7 }, 
						{ 6, 8, 0, 0, 9 }, 
						{ 0, 5, 7, 9, 0 } }; 

	// Print the solution 
	primMST(graph); 

	return 0; 
} 


6) Greedy

i) N meeting in one room 


#include <bits/stdc++.h> 
using namespace std; 

// Structure for storing starting time, 
// finishing time and position of meeting. 
struct meeting { 
	int start; 
	int end; 
	int pos; 
}; 

// Comparator function which can compare 
// the second element of structure used to 
// sort pairs in increasing order of second value. 
bool comparator(struct meeting m1, meeting m2) 
{ 
	return (m1.end < m2.end); 
} 

// Function for finding maximum meeting in one room 
void maxMeeting(int s[], int f[], int n) 
{ 
	struct meeting meet[n]; 
	for (int i = 0; i < n; i++) 
	{ 
		// Starting time of meeting i. 
		meet[i].start = s[i]; 
		
		// Finishing time of meeting i 
		meet[i].end = f[i]; 
		
		// Original position/index of meeting 
		meet[i].pos = i + 1; 
	} 

	// Sorting of meeting according to their finish time. 
	sort(meet, meet + n, comparator); 

	// Vector for storing selected meeting. 
	vector<int> m; 

	// Initially select first meeting. 
	m.push_back(meet[0].pos); 

	// time_limit to check whether new 
	// meeting can be conducted or not. 
	int time_limit = meet[0].end; 

	// Check for all meeting whether it 
	// can be selected or not. 
	for (int i = 1; i < n; i++) { 
		if (meet[i].start >= time_limit) 
		{ 
			// Push selected meeting to vector 
			m.push_back(meet[i].pos); 
			
			// Update time limit. 
			time_limit = meet[i].end; 
		} 
	} 

	// Print final selected meetings. 
	for (int i = 0; i < m.size(); i++) { 
		cout << m[i] << " "; 
	} 
} 

int main() 
{ 
	// Starting time 
	int s[] = { 1, 3, 0, 5, 8, 5 }; 
	
	// Finish time 
	int f[] = { 2, 4, 6, 7, 9, 9 }; 
	
	// Number of meetings. 
	int n = sizeof(s) / sizeof(s[0]); 

	// Function call 
	maxMeeting(s, f, n); 

	return 0; 
} 


ii) Activity selection problem

 
#include <bits/stdc++.h> 
using namespace std; 

// A job has a start time, finish time and profit. 
struct Activitiy 
{ 
	int start, finish; 
}; 

// A utility function that is used for sorting 
// activities according to finish time 
bool activityCompare(Activitiy s1, Activitiy s2) 
{ 
	return (s1.finish < s2.finish); 
} 

// Returns count of the maximum set of activities that can 
// be done by a single person, one at a time. 
void printMaxActivities(Activitiy arr[], int n) 
{ 
	// Sort jobs according to finish time 
	sort(arr, arr+n, activityCompare); 

	cout << "Following activities are selected n"; 

	// The first activity always gets selected 
	int i = 0; 
	cout << "(" << arr[i].start << ", " << arr[i].finish << "), "; 

	// Consider rest of the activities 
	for (int j = 1; j < n; j++) 
	{ 
	// If this activity has start time greater than or 
	// equal to the finish time of previously selected 
	// activity, then select it 
	if (arr[j].start >= arr[i].finish) 
	{ 
		cout << "(" << arr[j].start << ", "
			<< arr[j].finish << "), "; 
		i = j; 
	} 
	} 
} 
 
int main() 
{ 
	Activitiy arr[] = {{5, 9}, {1, 2}, {3, 4}, {0, 6}, {5, 7}, {8, 9}}; 
	int n = sizeof(arr)/sizeof(arr[0]); 
	printMaxActivities(arr, n); 
	return 0; 
} 








