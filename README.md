# graph-stl
generic graph data structure with bfs implemented using stl

//graph_bfs.cpp
#include <iostream>
#include <typeinfo>
#include <vector>
#include <list>
#include <stack>
#include <queue>
#include <fstream>
#include <algorithm>
using namespace std;

template<typename T >
struct node
{	
	public:
		node(int v, T w) : val(v), weight(w)
		{
		
		}
		
		T getVal()
		{
			return this->val;
		}

		template<typename U>
		friend ostream& operator<<(ostream& o, const node<U>& n);
		
		template<typename U>
		friend bool operator<(const node<U>& lhs, const node<U>& rhs);
		
		template<typename P>
		friend struct MyLess;
	
	private:
		int val;
		T weight;
};

template<typename T>
ostream& operator<<(ostream& o, const node<T>& n)
{
	return o << n.val << " - " << n.weight ;
}

/*template<typename T>
bool operator<(const node<T>& lhs, const node<T>& rhs)
{
	return lhs.val < rhs.val;
}*/

template<typename P>
struct MyLess
{
	bool operator()(const P& lhs, const P& rhs) 
	{
		if(lhs.val==rhs.val)
			return lhs.weight  < rhs.weight ;
			
		else
			return lhs.val  < rhs.val ;
	}
};

template<typename T1,typename T2 = node<T1> >  //T1 is of component type , T2 is of node type.
class graph
{
	private:
		vector<list<T2> > v;
		int num;
		stack<int> stk;
		queue<int> q;
		vector<int> visited;
	public:
		graph(int n) : num(n), v(n), visited(num, 0)
		{	
		
		}

	
	void add_edge(int src ,int val2, T1 weight)
	{
		node<T1> n(val2, weight);
		v[src].push_back(n);
		//v[src].sort();
		v[src].sort(MyLess<T2>());
	}

	void create()
	{
		fstream f("graph.txt");  //attached at the end.
		int src;
		int dstn;
		int wght;
		while(f >> src >> dstn >> wght)
		{
			add_edge(src,dstn,wght);
		}
			
	}

	void bfs_disp(int start)
	{
		cout << "\nResult of BFS:\n";
		bfs_iterator<T2> b(v, start, q, visited);
	}

	template<typename I>
	class bfs_iterator
	{
		public:
			bfs_iterator(vector<list<I> > &v,int start, queue<int> &q, vector<int> &visited) : ptr(&v[start].front()), sv(start), num(v.size()) 
			{
				bfs_traversal(v, q, visited);
				for(int i = 0; i < visited.size(); ++i)
				{
					if(visited[i] == 0)
					{
						sv = i; 
						ptr = &(v[i].front());	
						bfs_traversal(v, q, visited);
					}
				}
				
			}
			
		 	bfs_iterator& operator++()
			{	
				if(v[sv].size() > 1)
				{
					int val = q.front();
					ptr = &(v[val].front());
				}
				return *this;
			}
			
			void bfs_traversal(auto &v, auto &q, auto &visited)
			{	
				q.push(sv);
				visited[sv] = 1;
				while(!q.empty())
				{
					int val = q.front();
					cout << val << "\n";
					q.pop();
					
					++ptr;
				
					for_each(v[val].begin(), v[val].end(), [&q, &visited](auto e){
						if(visited[e.getVal()] == 0)
						{
							q.push(e.getVal());
							visited[e.getVal()] = 1;
						}
					});
				}
			}
		
		private:
			I* ptr;
			int sv;
			int num;
			int count = 0;
	};
};


int main()
{
	graph<int> g(4);
	g.create();
	g.bfs_disp(0);
}

// ------------------end of graph_bfs.cpp-----------------


/*  graph.txt  

0 3 4
0 1 2
0 1 4
1 3 5
3 2 7

*/
