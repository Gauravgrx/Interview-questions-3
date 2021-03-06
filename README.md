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


iii) Greedy algorithm to find minimum number of coin

// C++ program to find minimum 
// number of denominations 
#include <bits/stdc++.h> 
using namespace std; 

// All denominations of Indian Currency 
int deno[] = { 1, 2, 5, 10, 20, 
			50, 100, 500, 1000 }; 
int n = sizeof(deno) / sizeof(deno[0]); 

void findMin(int V) 
{ 
	sort(deno, deno + n); 

	// Initialize result 
	vector<int> ans; 

	// Traverse through all denomination 
	for (int i = n - 1; i >= 0; i--) { 

		// Find denominations 
		while (V >= deno[i]) { 
			V -= deno[i]; 
			ans.push_back(deno[i]); 
		} 
	} 

	// Print result 
	for (int i = 0; i < ans.size(); i++) 
		cout << ans[i] << " "; 
} 


int main() 
{ 
	int n = 93; 
	cout << "Following is minimal"
		<< " number of change for " << n 
		<< ": "; 
	findMin(n); 
	return 0; 
} 


iv) Fractional Knapsack Problem


#include <bits/stdc++.h> 

using namespace std; 

// Structure for an item which stores weight and corresponding 
// value of Item 
struct Item 
{ 
	int value, weight; 

	// Constructor 
	Item(int value, int weight) : value(value), weight(weight) 
	{} 
}; 

// Comparison function to sort Item according to val/weight ratio 
bool cmp(struct Item a, struct Item b) 
{ 
	double r1 = (double)a.value / a.weight; 
	double r2 = (double)b.value / b.weight; 
	return r1 > r2; 
} 

// Main greedy function to solve problem 
double fractionalKnapsack(int W, struct Item arr[], int n) 
{ 
	// sorting Item on basis of ratio 
	sort(arr, arr + n, cmp); 

	int curWeight = 0; // Current weight in knapsack 
	double finalvalue = 0.0; // Result (value in Knapsack) 

	// Looping through all Items 
	for (int i = 0; i < n; i++) 
	{ 
		// If adding Item won't overflow, add it completely 
		if (curWeight + arr[i].weight <= W) 
		{ 
			curWeight += arr[i].weight; 
			finalvalue += arr[i].value; 
		} 

		// If we can't add current Item, add fractional part of it 
		else
		{ 
			int remain = W - curWeight; 
			finalvalue += arr[i].value * ((double) remain / arr[i].weight); 
			break; 
		} 
	} 

	// Returning final value 
	return finalvalue; 
} 


int main() 
{ 
	int W = 50; // Weight of knapsack 
	Item arr[] = {{60, 10}, {100, 20}, {120, 30}}; 

	int n = sizeof(arr) / sizeof(arr[0]); 

	cout << "Maximum value we can obtain = "
		<< fractionalKnapsack(W, arr, n); 
	return 0; 
} 

v) Minimmum number of platform required for a railway

 
#include <algorithm> 
#include <iostream> 

using namespace std; 

// Returns minimum number of platforms reqquired 
int findPlatform(int arr[], int dep[], int n) 
{ 
	// Sort arrival and departure arrays 
	sort(arr, arr + n); 
	sort(dep, dep + n); 

	// plat_needed indicates number of platforms 
	// needed at a time 
	int plat_needed = 1, result = 1; 
	int i = 1, j = 0; 

	// Similar to merge in merge sort to process 
	// all events in sorted order 
	while (i < n && j < n) { 
		// If next event in sorted order is arrival, 
		// increment count of platforms needed 
		if (arr[i] <= dep[j]) { 
			plat_needed++; 
			i++; 
		} 

		// Else decrement count of platforms needed 
		else if (arr[i] > dep[j]) { 
			plat_needed--; 
			j++; 
		} 

		// Update result if needed 
		if (plat_needed > result) 
			result = plat_needed; 
	} 

	return result; 
} 

int main() 
{ 
	int arr[] = { 900, 940, 950, 1100, 1500, 1800 }; 
	int dep[] = { 910, 1200, 1120, 1130, 1900, 2000 }; 
	int n = sizeof(arr) / sizeof(arr[0]); 
	cout << "Minimum Number of Platforms Required = "
		<< findPlatform(arr, dep, n); 
	return 0; 
} 


vi)  Job Sequecing Problem


#include<iostream> 
#include<algorithm> 
using namespace std; 

// A structure to represent a job 
struct Job 
{ 
char id;	 // Job Id 
int dead; // Deadline of job 
int profit; // Profit if job is over before or on deadline 
}; 

// This function is used for sorting all jobs according to profit 
bool comparison(Job a, Job b) 
{ 
	return (a.profit > b.profit); 
} 

// Returns minimum number of platforms reqquired 
void printJobScheduling(Job arr[], int n) 
{ 
	// Sort all jobs according to decreasing order of prfit 
	sort(arr, arr+n, comparison); 

	int result[n]; // To store result (Sequence of jobs) 
	bool slot[n]; // To keep track of free time slots 

	// Initialize all slots to be free 
	for (int i=0; i<n; i++) 
		slot[i] = false; 

	// Iterate through all given jobs 
	for (int i=0; i<n; i++) 
	{ 
	// Find a free slot for this job (Note that we start 
	// from the last possible slot) 
	for (int j=min(n, arr[i].dead)-1; j>=0; j--) 
	{ 
		// Free slot found 
		if (slot[j]==false) 
		{ 
			result[j] = i; // Add this job to result 
			slot[j] = true; // Make this slot occupied 
			break; 
		} 
	} 
	} 

	// Print the result 
	for (int i=0; i<n; i++) 
	if (slot[i]) 
		cout << arr[result[i]].id << " "; 
} 

 
int main() 
{ 
	Job arr[] = { {'a', 2, 100}, {'b', 1, 19}, {'c', 2, 27}, 
				{'d', 1, 25}, {'e', 3, 15}}; 
	int n = sizeof(arr)/sizeof(arr[0]); 
	cout << "Following is maximum profit sequence of jobsn"; 
	printJobScheduling(arr, n); 
	return 0; 
} 



7)  Dyanamic Programming

i) Maximum Product Subarray

#include <bits/stdc++.h> 
using namespace std; 

// Function to find maximum product subarray 
int maxProduct(int* arr, int n) 
{ 
	// Variables to store maximum and minimum 
	// product till ith index. 
	int minVal = arr[0]; 
	int maxVal = arr[0]; 

	int maxProduct = arr[0]; 

	for (int i = 1; i < n; i++) { 

		// When multiplied by -ve number, 
		// maxVal becomes minVal 
		// and minVal becomes maxVal. 
		if (arr[i] < 0) 
			swap(maxVal, minVal); 

		// maxVal and minVal stores the 
		// product of subarray ending at arr[i]. 
		maxVal = max(arr[i], maxVal * arr[i]); 
		minVal = min(arr[i], minVal * arr[i]); 

		// Max Product of array. 
		maxProduct = max(maxProduct, maxVal); 
	} 

	// Return maximum product found in array. 
	return maxProduct; 
} 

 
int main() 
{ 
	int arr[] = { -1, -3, -10, 0, 60 }; 

	int n = sizeof(arr) / sizeof(arr[0]); 

	cout << "Maximum Subarray product is "
		<< maxProduct(arr, n) << endl; 

	return 0; 
} 


ii) Longest increasing subsequence


#include<bits/stdc++.h> 
using namespace std; 
	
/* lis() returns the length of the longest 
increasing subsequence in arr[] of size n */
int lis( int arr[], int n ) 
{ 
	int lis[n]; 

	lis[0] = 1; 

	/* Compute optimized LIS values in 
	bottom up manner */
	for (int i = 1; i < n; i++ ) 
	{ 
		lis[i] = 1; 
		for (int j = 0; j < i; j++ ) 
			if ( arr[i] > arr[j] && lis[i] < lis[j] + 1) 
				lis[i] = lis[j] + 1; 
	} 

	// Return maximum value in lis[] 
	return *max_element(lis, lis+n); 
} 
	
int main() 
{ 
	int arr[] = { 10, 22, 9, 33, 21, 50, 41, 60 }; 
	int n = sizeof(arr)/sizeof(arr[0]); 
	printf("Length of lis is %d\n", lis( arr, n ) ); 

	return 0; 
}


iii)  Longest Common subsequence


#include<bits/stdc++.h> 
using namespace std; 

int max(int a, int b); 

/* Returns length of LCS for X[0..m-1], Y[0..n-1] */
int lcs( char *X, char *Y, int m, int n ) 
{ 
	int L[m + 1][n + 1]; 
	int i, j; 
	
	/* Following steps build L[m+1][n+1] in 
	bottom up fashion. Note that L[i][j] 
	contains length of LCS of X[0..i-1] 
	and Y[0..j-1] */
	for (i = 0; i <= m; i++) 
	{ 
		for (j = 0; j <= n; j++) 
		{ 
		if (i == 0 || j == 0) 
			L[i][j] = 0; 
	
		else if (X[i - 1] == Y[j - 1]) 
			L[i][j] = L[i - 1][j - 1] + 1; 
	
		else
			L[i][j] = max(L[i - 1][j], L[i][j - 1]); 
		} 
	} 
		
	/* L[m][n] contains length of LCS 
	for X[0..n-1] and Y[0..m-1] */
	return L[m][n]; 
} 

/* Utility function to get max of 2 integers */
int max(int a, int b) 
{ 
	return (a > b)? a : b; 
} 

 
int main() 
{ 
	char X[] = "AGGTAB"; 
	char Y[] = "GXTXAYB"; 
	
	int m = strlen(X); 
	int n = strlen(Y); 
	
	cout << "Length of LCS is "
		<< lcs( X, Y, m, n ); 
	
	return 0; 
}


iv)  0-1 knapsack Problem


#include <stdio.h> 

// A utility function that returns 
// maximum of two integers 
int max(int a, int b) 
{ 
	return (a > b) ? a : b; 
} 

// Returns the maximum value that 
// can be put in a knapsack of capacity W 
int knapSack(int W, int wt[], int val[], int n) 
{ 
	int i, w; 
	int K[n + 1][W + 1]; 

	// Build table K[][] in bottom up manner 
	for (i = 0; i <= n; i++) { 
		for (w = 0; w <= W; w++) { 
			if (i == 0 || w == 0) 
				K[i][w] = 0; 
			else if (wt[i - 1] <= w) 
				K[i][w] = max( 
					val[i - 1] + K[i - 1][w - wt[i - 1]], 
					K[i - 1][w]); 
			else
				K[i][w] = K[i - 1][w]; 
		} 
	} 

	return K[n][W]; 
} 

int main() 
{ 
	int val[] = { 60, 100, 120 }; 
	int wt[] = { 10, 20, 30 }; 
	int W = 50; 
	int n = sizeof(val) / sizeof(val[0]); 
	printf("%d", knapSack(W, wt, val, n)); 
	return 0; 
} 


v) Edit distance 


#include <bits/stdc++.h> 
using namespace std; 

// Utility function to find the minimum of three numbers 
int min(int x, int y, int z) 
{ 
	return min(min(x, y), z); 
} 

int editDistDP(string str1, string str2, int m, int n) 
{ 
	// Create a table to store results of subproblems 
	int dp[m + 1][n + 1]; 

	// Fill d[][] in bottom up manner 
	for (int i = 0; i <= m; i++) { 
		for (int j = 0; j <= n; j++) { 
			// If first string is empty, only option is to 
			// insert all characters of second string 
			if (i == 0) 
				dp[i][j] = j; // Min. operations = j 

			// If second string is empty, only option is to 
			// remove all characters of second string 
			else if (j == 0) 
				dp[i][j] = i; // Min. operations = i 

			// If last characters are same, ignore last char 
			// and recur for remaining string 
			else if (str1[i - 1] == str2[j - 1]) 
				dp[i][j] = dp[i - 1][j - 1]; 

			// If the last character is different, consider all 
			// possibilities and find the minimum 
			else
				dp[i][j] = 1 + min(dp[i][j - 1], // Insert 
								dp[i - 1][j], // Remove 
								dp[i - 1][j - 1]); // Replace 
		} 
	} 

	return dp[m][n]; 
} 

int main() 
{ 
	// your code goes here 
	string str1 = "sunday"; 
	string str2 = "saturday"; 

	cout << editDistDP(str1, str2, str1.length(), str2.length()); 

	return 0; 
} 


vi) Maximum sum Increasing subsequence


#include <bits/stdc++.h> 
using namespace std; 

/* maxSumIS() returns the maximum 
sum of increasing subsequence 
in arr[] of size n */
int maxSumIS(int arr[], int n) 
{ 
	int i, j, max = 0; 
	int msis[n]; 

	/* Initialize msis values 
	for all indexes */
	for ( i = 0; i < n; i++ ) 
		msis[i] = arr[i]; 

	/* Compute maximum sum values 
	in bottom up manner */
	for ( i = 1; i < n; i++ ) 
		for ( j = 0; j < i; j++ ) 
			if (arr[i] > arr[j] && 
				msis[i] < msis[j] + arr[i]) 
				msis[i] = msis[j] + arr[i]; 

	/* Pick maximum of 
	all msis values */
	for ( i = 0; i < n; i++ ) 
		if ( max < msis[i] ) 
			max = msis[i]; 

	return max; 
} 


int main() 
{ 
	int arr[] = {1, 101, 2, 3, 100, 4, 5}; 
	int n = sizeof(arr)/sizeof(arr[0]); 
	cout << "Sum of maximum sum increasing "
			"subsequence is " << maxSumIS( arr, n ) << endl; 
	return 0; 
} 


vii) Matrix chain multiplication 


#include <climits>
#include <iostream>
using namespace std;

// function
int MatrixChainOrder(int* p, int n)
{
	n++;
	int m[n][n];
	int i, j, k, L, q;

	// initialize dp array with zero
	for (i = 0; i < n; i++)
		m[i][i] = 0;

	// from L = 3 to L = n-1
	for (L = 2; L < n; L++) 
	{
		for (i = 1; i < n - L + 1; i++) 
		{
			j = i + L - 1;
		
			// make m[i][j] = INT_MAX
			m[i][j] = INT_MAX;
			for (k = i; k <= j - 1; k++) 
			{
				q = m[i][k] + m[k + 1][j]
					+ p[i - 1] * p[k] * p[j];
			
				// if q is less than m[i][j]
				// make m[i][j] = q;
				if (q < m[i][j]) {
					m[i][j] = q;
				}
			}
		}
	}

	// return ans as m[1][n-1]
	return m[1][n - 1];
}


int main()
{
	int arr[] = { 1, 2, 3, 4 };
	int n = sizeof(arr) / sizeof(arr[0]);

	cout << "Minimum number of multiplications is "
		<< MatrixChainOrder(arr, n - 1);
}


viii)  Maximum path sum in matrix

#include <bits/stdc++.h>
using namespace std;
#define N 100

// No of rows and columns
int n, m;

// Declaring the matrix of maximum
// 100 rows and 100 columns
int a[N][N];

// Variable visited is used to keep
// track of all the visited positions
// Variable dp is used to store
// maximum sum till current position
vector<vector<int> > dp(N, vector<int>(N)),
	visited(N, vector<int>(N));

// For storing current sum
int current_sum = 0;

// For continuous update of
// maximum sum required
int total_sum = 0;

// Function to Input the matrix of size n*m
void inputMatrix()
{
	n = 3;
	m = 3;
	a[0][0] = 500;
	a[0][1] = 100;
	a[0][2] = 230;
	a[1][0] = 1000;
	a[1][1] = 300;
	a[1][2] = 100;
	a[2][0] = 200;
	a[2][1] = 1000;
	a[2][2] = 200;
}

// Function to calculate maximum sum of path
int maximum_sum_path(int i, int j)
{
	// Checking boundary condition
	if (i == n - 1 && j == m - 1)
		return a[i][j];

	// Checking whether or not (i, j) is visited
	if (visited[i][j])
		return dp[i][j];

	// Marking (i, j) is visited
	visited[i][j] = 1;

	// Updating the maximum sum till
	// the current position in the dp
	int& total_sum = dp[i][j];

	// Checking whether the position hasn't
	// visited the last row or the last column.
	// Making recursive call for all the possible
	// moves from the current cell and then adding the
	// maximum returned by the calls and updating it.
	if (i < n - 1 & j < m - 1) {
		int current_sum = max(maximum_sum_path(i, j + 1),
							max(
								maximum_sum_path(i + 1, j + 1),
								maximum_sum_path(i + 1, j)));
		total_sum = a[i][j] + current_sum;
	}

	// Checking whether
	// position has reached last row
	else if (i == n - 1)
		total_sum = a[i][j]
					+ maximum_sum_path(i, j + 1);

	// If the position is in the last column
	else
		total_sum = a[i][j]
					+ maximum_sum_path(i + 1, j);

	// Returning the updated maximum value
	return total_sum;
}


int main()
{
	inputMatrix();

	// Calling the implemented function
	int maximum_sum = maximum_sum_path(0, 0);

	cout << maximum_sum;
	return 0;
}


ix) Coin change


#include<bits/stdc++.h> 

using namespace std; 

int count( int S[], int m, int n ) 
{ 
	int i, j, x, y; 

	// We need n+1 rows as the table 
	// is constructed in bottom up 
	// manner using the base case 0 
	// value case (n = 0) 
	int table[n + 1][m]; 
	
	// Fill the enteries for 0 
	// value case (n = 0) 
	for (i = 0; i < m; i++) 
		table[0][i] = 1; 

	// Fill rest of the table entries 
	// in bottom up manner 
	for (i = 1; i < n + 1; i++) 
	{ 
		for (j = 0; j < m; j++) 
		{ 
			// Count of solutions including S[j] 
			x = (i-S[j] >= 0) ? table[i - S[j]][j] : 0; 

			// Count of solutions excluding S[j] 
			y = (j >= 1) ? table[i][j - 1] : 0; 

			// total count 
			table[i][j] = x + y; 
		} 
	} 
	return table[n][m - 1]; 
} 

int main() 
{ 
	int arr[] = {1, 2, 3}; 
	int m = sizeof(arr)/sizeof(arr[0]); 
	int n = 4; 
	cout << count(arr, m, n); 
	return 0; 
} 


x) Subset sum 

#include <stdio.h> 

// Returns true if there is a subset of set[] 
// with sun equal to given sum 
bool isSubsetSum(int set[], int n, int sum) 
{ 
	// The value of subset[i][j] will be true if 
	// there is a subset of set[0..j-1] with sum 
	// equal to i 
	bool subset[n + 1][sum + 1]; 

	// If sum is 0, then answer is true 
	for (int i = 0; i <= n; i++) 
		subset[i][0] = true; 

	// If sum is not 0 and set is empty, 
	// then answer is false 
	for (int i = 1; i <= sum; i++) 
		subset[0][i] = false; 

	// Fill the subset table in botton up manner 
	for (int i = 1; i <= n; i++) { 
		for (int j = 1; j <= sum; j++) { 
			if (j < set[i - 1]) 
				subset[i][j] = subset[i - 1][j]; 
			if (j >= set[i - 1]) 
				subset[i][j] = subset[i - 1][j] 
							|| subset[i - 1][j - set[i - 1]]; 
		} 
	} 

	/* // uncomment this code to print table 
	for (int i = 0; i <= n; i++) 
	{ 
	for (int j = 0; j <= sum; j++) 
		printf ("%4d", subset[i][j]); 
	printf("\n"); 
	}*/

	return subset[n][sum]; 
} 

int main() 
{ 
	int set[] = { 3, 34, 4, 12, 5, 2 }; 
	int sum = 9; 
	int n = sizeof(set) / sizeof(set[0]); 
	if (isSubsetSum(set, n, sum) == true) 
		printf("Found a subset with given sum"); 
	else
		printf("No subset with given sum"); 
	return 0; 
} 


xi) Cutting a Rod

#include<stdio.h>
#include<limits.h>

// A utility function to get the maximum of two integers
int max(int a, int b) { return (a > b)? a : b;}

/* Returns the best obtainable price for a rod of length n and
price[] as prices of different pieces */
int cutRod(int price[], int n)
{
int val[n+1];
val[0] = 0;
int i, j;

// Build the table val[] in bottom up manner and return the last entry
// from the table
for (i = 1; i<=n; i++)
{
	int max_val = INT_MIN;
	for (j = 0; j < i; j++)
		max_val = max(max_val, price[j] + val[i-j-1]);
	val[i] = max_val;
}

return val[n];
}

int main()
{
	int arr[] = {1, 5, 8, 9, 10, 17, 17, 20};
	int size = sizeof(arr)/sizeof(arr[0]);
	printf("Maximum Obtainable Value is %dn", cutRod(arr, size));
	getchar();
	return 0;
}


xii) Egg dropping puzzle


#include <bits/stdc++.h>
using namespace std;

// A utility function to get 
// maximum of two integers 
int max(int a, int b) 
{ 
	return (a > b) ? a : b; 
} 

/* Function to get minimum 
number of trials needed in worst 
case with n eggs and k floors */
int eggDrop(int n, int k) 
{ 
	/* A 2D table where entery 
	eggFloor[i][j] will represent 
	minimum number of trials needed for 
	i eggs and j floors. */
	int eggFloor[n + 1][k + 1]; 
	int res; 
	int i, j, x; 

	// We need one trial for one floor and 0 
	// trials for 0 floors 
	for (i = 1; i <= n; i++) { 
		eggFloor[i][1] = 1; 
		eggFloor[i][0] = 0; 
	} 

	// We always need j trials for one egg 
	// and j floors. 
	for (j = 1; j <= k; j++) 
		eggFloor[1][j] = j; 

	// Fill rest of the entries in table using 
	// optimal substructure property 
	for (i = 2; i <= n; i++) { 
		for (j = 2; j <= k; j++) { 
			eggFloor[i][j] = INT_MAX; 
			for (x = 1; x <= j; x++) { 
				res = 1 + max( 
							eggFloor[i - 1][x - 1], 
							eggFloor[i][j - x]); 
				if (res < eggFloor[i][j]) 
					eggFloor[i][j] = res; 
			} 
		} 
	} 

	// eggFloor[n][k] holds the result 
	return eggFloor[n][k]; 
} 

int main() 
{ 
	int n = 2, k = 36; 
	printf("\nMinimum number of trials "
		"in worst case with %d eggs and "
		"%d floors is %d \n", 
		n, k, eggDrop(n, k)); 
	return 0; 
} 


xiii) Word Break Problem 


#include <iostream> 
#include <string.h> 
using namespace std; 

/* A utility function to check whether a word is present in dictionary or not. 
An array of strings is used for dictionary. Using array of strings for 
dictionary is definitely not a good idea. We have used for simplicity of 
the program*/
int dictionaryContains(string word) 
{ 
	string dictionary[] = {"mobile","samsung","sam","sung","man","mango", 
						"icecream","and","go","i","like","ice","cream"}; 
	int size = sizeof(dictionary)/sizeof(dictionary[0]); 
	for (int i = 0; i < size; i++) 
		if (dictionary[i].compare(word) == 0) 
		return true; 
	return false; 
} 

// Returns true if string can be segmented into space separated 
// words, otherwise returns false 
bool wordBreak(string str) 
{ 
	int size = str.size(); 
	if (size == 0) return true; 

	// Create the DP table to store results of subroblems. The value wb[i] 
	// will be true if str[0..i-1] can be segmented into dictionary words, 
	// otherwise false. 
	bool wb[size+1]; 
	memset(wb, 0, sizeof(wb)); // Initialize all values as false. 

	for (int i=1; i<=size; i++) 
	{ 
		// if wb[i] is false, then check if current prefix can make it true. 
		// Current prefix is "str.substr(0, i)" 
		if (wb[i] == false && dictionaryContains( str.substr(0, i) )) 
			wb[i] = true; 

		// wb[i] is true, then check for all substrings starting from 
		// (i+1)th character and store their results. 
		if (wb[i] == true) 
		{ 
			// If we reached the last prefix 
			if (i == size) 
				return true; 

			for (int j = i+1; j <= size; j++) 
			{ 
				// Update wb[j] if it is false and can be updated 
				// Note the parameter passed to dictionaryContains() is 
				// substring starting from index 'i' and length 'j-i' 
				if (wb[j] == false && dictionaryContains( str.substr(i, j-i) )) 
					wb[j] = true; 

				// If we reached the last character 
				if (j == size && wb[j] == true) 
					return true; 
			} 
		} 
	} 

	/* Uncomment these lines to print DP table "wb[]" 
	for (int i = 1; i <= size; i++) 
		cout << " " << wb[i]; */

	// If we have tried all prefixes and none of them worked 
	return false; 
} 

int main() 
{ 
	wordBreak("ilikesamsung")? cout <<"Yes\n": cout << "No\n"; 
	wordBreak("iiiiiiii")? cout <<"Yes\n": cout << "No\n"; 
	wordBreak("")? cout <<"Yes\n": cout << "No\n"; 
	wordBreak("ilikelikeimangoiii")? cout <<"Yes\n": cout << "No\n"; 
	wordBreak("samsungandmango")? cout <<"Yes\n": cout << "No\n"; 
	wordBreak("samsungandmangok")? cout <<"Yes\n": cout << "No\n"; 
	return 0; 
} 
























