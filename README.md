# 算法宝典
## 动态规划

### 最大连续子序和

Problem Description:

Given an integer array nums, find the contiguous subarray (containing at least one number) which has the largest sum and return its sum.

> Example:
`Input: [-2,1,-3,4,-1,2,1,-5,4]`
`Output: 6`
`Explanation: [4,-1,2,1] has the largest sum = 6.`

> Follow up:
If you have figured out the O(n) solution, try coding another solution using the divide and conquer approach, which is more subtle.

**题解**
最大子序列和求解问题，先定义最大和maxsubbarray等于数组的第一个元素，另定义一个动态变化的整数值thissubarray，从数组的第一个元素开始，向后推进求和，如果存在子序列和大于原定义的最大子序列和maxsubarray，则更新maxsubarray的值，如果thissubarray的值小于0，则说明它对总和的增长无贡献（且会减小总和maxsubarray的值），所以应该弃掉前面的子序列和（即令thissubarray=0），这时最大子序列和对应的子序列不包含前面的元素。

```c++
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int size=nums.size();
        int maxsubarray=nums[0],thissubarray=0;
        for(int i=0;i<size;++i)
        {
            thissubarray+=nums[i];
            if(thissubarray>maxsubarray)
                maxsubarray=thissubarray;
            if(thissubarray<0)
                thissubarray=0;
        }
        return maxsubarray;
    }
};
```
### 超级楼梯

Problem Description:

有一楼梯共M级，刚开始时你在第一级，若每次只能跨上一级或二级，要走上第M级，共有多少种走法？

Input:

输入数据首先包含一个整数N，表示测试实例的个数，然后是N行数据，每行包含一个整数M（1<=M<=40）,表示楼梯的级数。

Output:

对于每个测试实例，请输出不同走法的数量

~~~c++
/*
通过分析不难看出该问题就是一道求解斐波那契数列的问题，对于第二级楼梯有一种走法，第三级楼梯有两种走法，对于大于第三级的楼梯走法的总数总可以看成前两级楼梯的走法种数之和，例如对于第6级楼梯，我可以先走前四级楼梯，最后一步跨上两级到达，我还可以先走五级楼梯，最后再一步一级到达；
*/
#include<iostream>
using namespace std;
int a[50];
int main()
{
    a[0]=0,a[1]=1;
    for(int i=2; i<50; i++)
        a[i]=a[i-1]+a[i-2];
    int n;
    cin>>n;
    while(n--)
    {
        int m;
        cin>>m;
        cout<<a[m]<<endl;
    }
}
~~~

### 剪绳子

给你一根长度为 n 的绳子，请把绳子剪成整数长度的 m 段（m、n都是整数，n>1并且m>1），每段绳子的长度记为 k[0],k[1]...k[m-1] 。请问 k[0]*k[1]*...*k[m-1] 可能的最大乘积是多少？例如，当绳子的长度是8时，我们把它剪成长度分别为2、3、3的三段，此时得到的最大乘积是18。

示例 1：

> 输入: 2
> 输出: 1
> 解释: 2 = 1 + 1, 1 × 1 = 1
> 示例 2:

> 输入: 10
> 输出: 36
> 解释: 10 = 3 + 3 + 4, 3 × 3 × 4 = 36
> 提示：

> 2 <= n <= 58

**方法一：动态规划**

```C++
//用时0ms,空间：5.9MB
class Solution {
public:
    int cuttingRope(int n) {
        int a[59] = { 0 };
        if (n < 4)
            return n - 1;
        for (int i = 1; i <= n; i++)
        {
            a[i] = i;//初始为原长
            for (int j = 1; j < i; j++)
                a[i] = max(a[i], a[i - j] * a[j]);//比较减j时，与不减时的乘积大小，保留最大值
        }
        return a[n];
    }
};
```

**方法二：数学推导**

根据题意，任意一段长为n绳子都可以细分为a段（$n=n_1+n_2+n_3+...+n_a$）; 

要使$S=n_1\times n_2\times n_3\times...\times n_a$值最大，由下面式子：

$$\frac{n_1+n_2+n_3+...+n_a}{a}>=\sqrt[a]{n_1n_2n_3...n_a}$$     得出:

当且仅当$n_1=n_2=n_3=...=n_a$时等号才成立，意思就是当把长度为N的绳子细分成数段等长为 x 的绳子时，他们的乘积 $S$ 取得最大值，且乘积值为$x^a$ 

由此我们就将问题转化为求解每段绳子长度x为何值时，$S$才能取得最大值 要求$S=x^a$的最大值，我们需要对式子稍微变换一下;

 由$a=\frac{n}{x}$,得:$$S=x^{\frac{n}{x}}$$

对两边取$ln$得:  $$lnS=\frac{n}{x}lnx$$ 

两边同时对x求导，得$$\frac{1}{S}S'=-\frac{n}{x^2}lnx+\frac{1}{x}\times \frac{n}{x}=(1-lnx)\times \frac{n}{x^2}$$ 令$S'=0$时,$1-lnx=0$，

解得$x=e$

 $$ S=\begin{cases} 大于0, x\in(0 , e)\\ 小于0,x\in(e,+\infty)\\ \end{cases} $$

 因为$x$为正整数,当$S$值为最大时，$x$的可能取值有2或者3；

 当$x=2$时，$S(2)=2^\frac{n}{2}$ 

当$x=3$时，$S(3)=3^\frac{n}{3}$ 

上面两个等式右边式子同时取6次方，不难判断出$$S(3)=9^n>S(2)=8^n(n是正整数)$$

 所以我们可以得出结论:**只有尽可能地将绳子等分为多段长度为3的绳子，它们长度的乘积$S$才能取得最大** 这种剪法可能有三种情况： 

第一种： n刚好能被3整除，则此时乘积$$S=3^a$$ 

第二种： n除以3之后，余下一段长度为1的绳子，此时我们应该分出一段长度为3的绳子与余下的长度为1的绳子**重新拼接回来**，拼接后的绳子长度为4，则 $$S=3^{a-1}\times4 $$

第三种： n除以3之后，余下一段长度为2的绳子，则余下的绳子不必再剪，所有绳子的乘积为$$S=3^a\times2$$

```cpp
/*
执行用时：0 ms
内存消耗：6 MB
*/
class Solution {
public:
    int cuttingRope(int n) {
        if(n<4)
            return n-1;
        else
        {
            int a=n/3;//记录倍数
            int b=n%3;//记录余数
            if(b==0)
                return pow(3,a);
            else if(b==1)
                return pow(3,a-1)*4;
            else
                return pow(3,a)*2;
        }
    }
};
```

## 深度优先搜索

### 数独

题目描述(poj 2676)：Sudoku is a very simple task. A square table with 9 rows and 9 columns is divided to 9 smaller squares 3x3 as shown on the Figure. In some of the cells are written decimal digits from 1 to 9. The other cells are empty. The goal is to fill the empty cells with decimal digits from 1 to 9, one digit per cell, in such way that in each row, in each column and in each marked 3x3 subsquare, all the digits from 1 to 9 to appear. Write a program to solve a given Sudoku-task.
![img]("C:%5CUsers%5CASUS%5CDocuments%5C56e7e6182093bbca488d6c73ba833af6")
![数独](https://imgconvert.csdnimg.cn/aHR0cDovL3Bvai5vcmcvaW1hZ2VzLzI2NzZfMS5qcGc?x-oss-process=image/format,png#pic_center)

Input

The input data will start with the number of the test cases. For each test case, 9 lines follow, corresponding to the rows of the table. On each line a string of exactly 9 decimal digits is given, corresponding to the cells in this line. If a cell is empty it is represented by 0.

Output

For each test case your program should print the solution in the same format as the input data. The empty cells have to be filled according to the rules. If solutions is not unique, then the program may print any one of them.

Sample Input

```
1
103000509
002109400
000704000
300502006
060000050
700803004
000401000
009205800
804000107
```

Sample Output

```
143628579
572139468
986754231
391542786
468917352
725863914
237481695
619275843
854396127
```

**题解思路：**
**第一步：**
首先定义一个$9\times9$的二维数组a来记录输入的数独表；

然后再三个数组（x[10][10], y[10][10], rect[10][10]）所有元素都初始化为0；

数组x[10][10]用来标记九行中某一行九个不同的数字:；
**例如**：如果数独表中第 i 行的数字2已经存在，则x[i][2]的值将会从0变为1，表示第 i 行已经存在数字2；

数组y[10][10]则是标志九列中某一列九个不同数字；
**例如**：如果数独表中第 j 列的数字2已经存在，则x[j][2]的值将会从0变为1，表示第 j 行已经存在数字2；

数组rect[10][10]的功能则是标志九个$3\times3$小方块中每个小方块的九个不同数字；
**例如**：如果第 m 个小方块中数字2已经存在，则rect[m][2]的值将会从0变为1，表示第 m 个小方块已经存在数字2；
![2](C:%5CUsers%5CASUS%5CDocuments%5C20200503113413304.png)

**第二步：**
在输入数字内容的同时将不为0的位置对应的行数组、列数组、小方块数组中的数字标记为1（表示此数字已经存在，便于下一步填充表格时判断数字是否可用），输入完成之后，从数独表格的左上角第一个元素开始进行深度优先搜索；

**第三步：**
套用深度优先搜索模板：
1、判断是否越界，横向越界则深搜下一行，纵向越界则说明已经填充完表格中所有的位置（==此时说明该数独已经实现==），因为我们是横向深搜，纵向越界只有一种可能（即从第一行第一列至第九行第九列的位置已被填充）

2、在元素位置不越界的前提下判断该位置是否已被输入时填充（值不为0），如果是，则跳过，进行下一个位置的深搜；如果否，则从数字‘1’到‘9’尝试9个数字，被用来填充的数字==必须满足==对应的行数组x、列数组y以及小方块数组rect皆不存在该数字，满足要求的数字通常有多个，我们用最小的可填充数字填充该位置；

3、每填充完一个位置都要判断一下该表格是否能被完整填充，如果在表格被填充完之前已经达到尽头（意思是该行该列该小方块把数字1~9皆包含了），这时就要返回尽头的上一级，尝试上一级的另一个可能被填充的数字，然后在进行下一步（上一次的尽头处）判断是否有可填充数字，如果没有，再返回上一级尝试另一个为尝试过的可填充数字，如果==上一级==的所有可填充数字都尝试过了，仍然不能越过尽头，则返回==上上一级==的位置，尝试上上一级中其它备选的可填充数字，也就是利用回溯的方法，不断尝试，直到数独表格被填充完（此时直接回到主函数将表格输出）

4、如果数独表格能被填充完，则输出第一个已被填充的表格；

```c++
/*内存：248K	时间：407MS
题解：
从右上角开始按行遍历数组，利用回溯法，以及深度优先搜索的方法求解，如果求解过程中发现某一选择无法通过，则回溯到上一级，更换此处坐标，直到其后续选择均满足为止，此时就可输出数独
*/
#include<iostream>
#include<set>
#include<vector>
#include<string>
#include<iomanip>
#include<map>
#include<algorithm>
#include<bitset>
using namespace std;
vector<int>b(9,0);
int rect[10][10];//九块矩阵
vector<vector<int>>a(9, b);
int x[10][10];//行标志
int y[10][10];//列标志
bool dfs(int i, int j)
{
	bool judge = false;//判断数独是否成立
	if (i == 9)
		return true;
	if (j == 9)
		judge = dfs(i + 1, 0); 
	else
	{
		if (a[i][j] == 0)
		{
			for (int m = 1; m <= 9; m++)
			{
				int h = (i / 3) * 3 + (j / 3);
				if (x[i][m] == 0 && y[j][m] == 0 && rect[h][m] == 0)
				{
					a[i][j] = m;
					x[i][m] = 1;
					y[j][m] = 1;
					rect[h][m] = 1;
					judge = dfs(i, j + 1);
					if (judge)//如果后面成立则通过,如果不通过则回溯(改变m的值)
					{
						return true;
					}
					else
					{
						a[i][j] = 0;
						x[i][m] = 0;
						y[j][m] = 0;
						rect[h][m] = 0;
					}
				}
			}
		}
		else//如果是事先填过的坐标，则跳过
		{
			judge = dfs(i, j + 1);
			return judge;
		}
		return false;//如果不成立，则继续回溯
	}
}
int main()
{
	int n;
	cin >> n;
	while (n--)
	{
		memset(x, 0, sizeof(x));
		memset(y, 0, sizeof(y));
		memset(rect, 0, sizeof(rect));
		for (int i = 0; i < 9; i++)
		{
			string str;
			cin >> str;
			for (int j = 0; j < 9; ++j)
			{
				a[i][j] = str[j] - '0';
				if (a[i][j] != 0)
				{
					x[i][a[i][j]] = 1;
					y[j][a[i][j]] = 1;//标志行和列用过的数字
					int h = (i / 3) * 3 + (j / 3);
					rect[h][a[i][j]] = 1;
				}
			}
		}
		dfs(0, 0);//实现数独

​		for (int i = 0; i < 9; i++)
​		{
​			for (int j = 0; j < 9; ++j)
​				cout << a[i][j];
​			cout << endl;
​		}
​	}
​	return 0;
}
```



### 棋盘翻转

题目描述（poj 1753）：Flip game is played on a rectangular 4x4 field with two-sided pieces placed on each of its 16 squares. One side of each piece is white and the other one is black and each piece is lying either it's black or white side up. Each round you flip 3 to 5 pieces, thus changing the color of their upper side from black to white and vice versa. The pieces to be flipped are chosen every round according to the following rules:

1. Choose any one of the 16 pieces.
2. Flip the chosen piece and also all adjacent pieces to the left, to the right, to the top, and to the bottom of the chosen piece (if there are any).


![img](C:%5CUsers%5CASUS%5CDocuments%5C14b4b3ec0b5261bea3a5ad9f1313252c)Consider the following position as an example:

bwbw
wwww
bbwb
bwwb
Here "b" denotes pieces lying their black side up and "w" denotes pieces lying their white side up. If we choose to flip the 1st piece from the 3rd row (this choice is shown at the picture), then the field will become:

bwbw
bwww
wwwb
wwwb
The goal of the game is to flip either all pieces white side up or all pieces black side up. You are to write a program that will search for the minimum number of rounds needed to achieve this goal.

Input

The input consists of 4 lines with 4 characters "w" or "b" each that denote game field position.

Output

Write to the output file a single integer number - the minimum number of rounds needed to achieve the goal of the game from the given position. If the goal is initially achieved, then write 0. If it's impossible to achieve the goal, then write the word "Impossible" (without quotes).

Sample Input

```
bwwb
bbwb
bwwb
bwww
```

Sample Output

```
4
```

~~~c++
/*
题解：
这里使用的是暴力枚举，因为棋盘只有十六个格，所以最多可以翻转十六次（如果重复翻转同一个格子，相当于最初的状态），所以可以规定翻转次数，从最少的0次开始（即判断初始棋盘是否符合），直到最初满足题目要求的翻转次数出现（最少翻转次数）
*/
#include<iostream>
#include<set>
#include<vector>
#include<string>
#include<iomanip>
#include<map>
#include<algorithm>
#include<bitset>
using namespace std;
vector<string>a(4);//棋盘
int step = 1;
//上下右左四个方向
int x[5] = { -1,1 ,0,0,0 };
int y[5] = { 0,0, 1,-1,0 };
bool judge()//判断棋盘颜色是否一致
{
	char c = a[0][0];
	for (int i = 0; i < 4; ++i)
	{
		for (int j = 0; j < 4; ++j)
		{
			if (c != a[i][j])
				return false;//如果存在不一样的棋子，直接返回“错误”
		}
	}
	return true;
}
void flip(int i, int j)//翻转
{
	for (int s = 0; s < 5; s++)
	{
		int m = i + x[s];
		int n = j + y[s];
		if (m >= 0 && m <= 3 && n >= 0 && n <= 3)//保证在界内
		{
			if (a[m][n] == 'w')//翻转
				a[m][n] = 'b';
			else
				a[m][n] = 'w';
		}
	}
}
bool fun(int i,int j,int sum)//实现棋盘的函数
{
	if (i == 4 || sum > step)
		return false;
	flip(i, j);
	if (step == sum)
	{
		if (judge())
			return true;
	}
	//如果上述不满足则继续翻转下一位
	{
		bool jud = false;
		if (j == 3)
			jud = fun(i + 1, 0, sum + 1);
		else
			jud = fun(i, j + 1, sum + 1);
		if (!jud)
		{
			flip(i, j);//还原
			if (j == 3)//再翻转下一个
				jud = fun(i + 1, 0, sum);
			else
				jud = fun(i, j + 1, sum);

			if (jud)
				return true;
			else
				return false;
		}
		else
			return true;
	}
}
int main()
{
	for (int i = 0; i < 4; i++)
		cin >> a[i];
	vector<string>a1 = a;
	if (judge())//先判断一开始的棋盘是否已经满足
	{
		cout << 0 << endl;
	}
	else
	{
		for (; step <= 16; step++)//最多只可能翻转十六次
		{
			if (fun(0, 0, 1))
				break;
		}
		if (step <= 16)//如果可以实现则输出最小次数
		{
			cout << step << endl;
		}
		else
		{
			cout << "Impossible" << endl;
		}
	}
}
~~~

## 宽度优先搜索

### 二叉树的最近公共祖先

**题目描述**

给定一个二叉树, 找到该树中两个指定节点的最近公共祖先。

百度百科中最近公共祖先的定义为：“对于有根树 T 的两个结点 p、q，最近公共祖先表示为一个结点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（一个节点也可以是它自己的祖先）。”

例如，给定如下二叉树:  root = [3,5,1,6,2,0,8,null,null,7,4]

![二叉树](C:%5CUsers%5CASUS%5CDocuments%5Cbinarytree.png)

示例 1:

```
输入: root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 1
输出: 3
解释: 节点 5 和节点 1 的最近公共祖先是节点 3。
```

示例 2:

```
输入: root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 4
输出: 5
解释: 节点 5 和节点 4 的最近公共祖先是节点 5。因为根据定义最近公共祖先节点可以为节点本身。
```

输入: root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 4
输出: 5
解释: 节点 5 和节点 4 的最近公共祖先是节点 5。因为根据定义最近公共祖先节点可以为节点本身。


说明:

> 所有节点的值都是唯一的。
> p、q 为不同节点且均存在于给定的二叉树中。

**方法一**

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 32 ms	14.3 MB*/
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if(root==NULL||root==p||root==q)//如果为空或者找到目的根节点，则直接返回
            return root;
        else
        {
            TreeNode* left=lowestCommonAncestor(root->left,p,q);//查找左分支
            TreeNode* right=lowestCommonAncestor(root->right,p,q);//查找右分支
            if(left!=NULL&&right!=NULL)//如果左右分支都含有目的根节点，则返回该根节点
                return root;
            else
                return left==NULL?right:left;//如果两个目的节点都在一个分支上，则返回该分支即可
        }
    }
};
```



## 最小生成树


**Problem Description**

In 2100, since the sea level rise, most of the cities disappear. Though some survived cities are still connected with others, but most of them become disconnected. The government wants to build some roads to connect all of these cities again, but they don’t want to take too much money. 



**Input**

The first line contains the number of test cases.
Each test case starts with three integers: n, m and k. n (3 <= n <=500) stands for the number of survived cities, m (0 <= m <= 25000) stands for the number of roads you can choose to connect the cities and k (0 <= k <= 100) stands for the number of still connected cities.
To make it easy, the cities are signed from 1 to n.
Then follow m lines, each contains three integers p, q and c (0 <= c <= 1000), means it takes c to connect p and q.
Then follow k lines, each line starts with an integer t (2 <= t <= n) stands for the number of this connected cities. Then t integers follow stands for the id of these cities.

 

**Output**

For each case, output the least money you need to take, if it’s impossible, just output -1.

**Sample Input**

```
1
6 4 3
1 4 2
2 6 1
2 3 5
3 4 33
2 1 2
2 1 3
3 4 5 6
```

**Sample Output**

```
1
```

**方法一**

使用了并查集与Kruskal算法

```c++
//G++ 耗时748ms
//C++ 超时
#include<iostream>
#include<algorithm>
using namespace std;
int parent[501];//记录每个城市对应的父城市（根节点）
struct node
{
	int city1;//表示连通城市1和城市2需要花费的钱（pay）
	int city2;
	int pay;
}set[25001];//记录城市路径（树）

int find_root(int city)//查找根节点
{
	if (parent[city] == city)//如果根节点为其本身则返回
		return city;
	else//否则不断查找根节点，直至满足上述条件
		return find_root(parent[city]);
}

bool Union(int city1, int city2)//判断两座城市是否可连通（是否形成环路）,否，则连通
{
	int root_x = find_root(city1);//找到两座城市的根节点
	int root_y = find_root(city2);
	//下面是用了并查集的优化思想，即将树的较高根节点连接到树的较低根节点之上，以减小树的级数（可视为高度）
	if (root_x != root_y)
	{
		if (root_x > root_y)
			parent[root_x] = root_y;
		else
			parent[root_y] = root_x;
		return true;
	}
	else
		return false;
}

bool cmp(node a, node b)
{
	return a.pay < b.pay;
}
int main()
{
	int test;//测试实例个数
	scanf("%d", &test);
	while (test--)
	{
		int n, m, k;
		cin >> n >> m >> k;
		int route = 0;//route记录已连接的路径条数
		for (int i = 1; i <= n; i++)
			parent[i] = i;//将每座城市的根节点初始化为自己，以便于后面判断根节点的高度
		for (int i = 1; i <= m; i++)//输入并记录可连通的路径和花销
		{
			scanf("%d%d%d", &set[i].city1, &set[i].city2, &set[i].pay);
		}
		for (int i = 1; i <= k; i++)
		{
			int sum;//记录已经连通的城市个数
			scanf("%d", &sum);
			int city1, city_i;//city1是给定的第一座城市,city_i值可变，表示后续的第二、三、四...座城市
			sum--;
			scanf("%d", &city1);
			while (sum--)
			{
				scanf("%d", &city_i);
				if (Union(city1, city_i))//完成初始的连接
					route++;
			}
		}
		sort(set + 1, set + m + 1, cmp);//根据给出的路径的花销从小到大排序
		int menoy = 0;//计算总花销
		for (int i = 1; i <= m; i++)
		{
			if (Union(set[i].city1, set[i].city2))
			{
				route++;
				menoy += set[i].pay;
			}
			if (route == n - 1)
				break;
		}
		printf("%d\n", (route == n - 1) ? menoy : -1);
	}
	return 0;
}

```

**方法二**



## 思维

### 蚂蚁军队

题目描述：An army of ants walk on a horizontal pole of length l cm, each with a constant speed of 1 cm/s. When a walking ant reaches an end of the pole, it immediatelly falls off it. When two ants meet they turn back and start walking in opposite directions. We know the original positions of ants on the pole, unfortunately, we do not know the directions in which the ants are walking. Your task is to compute the earliest and the latest possible times needed for all ants to fall off the pole.

Input

The first line of input contains one integer giving the number of cases that follow. The data for each case start with two integer numbers: the length of the pole (in cm) and n, the number of ants residing on the pole. These two numbers are followed by n integers giving the position of each ant on the pole as the distance measured from the left end of the pole, in no particular order. All input integers are not bigger than 1000000 and they are separated by whitespace.

Output

For each case of input, output two numbers separated by a single space. The first number is the earliest possible time when all ants fall off the pole (if the directions of their walks are chosen appropriately) and the second number is the latest possible such time.

Sample Input

```
2
10 3
2 6 7
214 7
11 12 7 13 176 23 191
```

Sample Output

```
4 8
38 207
```

**题解思路**
这道题相对比较简单，其实容易卡住人的点就是一个思维的转变；

针对于每一个输入实例，我们可以先观察任意两只蚂蚁，题目所说的，当两只蚂蚁相遇时，它们就会转变前进的方向，然后一直前进，直到到达杆的尽头；

然后要求我们根据给定的初始位置分别设定两只蚂蚁的前进方向，来求得这两只蚂蚁能行走的最大路程和最小路程；

其实这句话我们可以这样理解：当两只蚂蚁相遇时，它们**不会转变前进方向**，而是绕过对方继续前行；这种情况下它们行走的总路程与原题的情况并无差别！

其实这很容易理解，因为对于任意两只蚂蚁并无任何差别，是否转向还是一样要行走那么多路程，你可以理解为当它们相遇时，一只蚂蚁走了另一只蚂蚁本该行走的路程，但它们的总路程是不变的；

只要理解了上面，接下来就容易解决了;

在这个前提下，我们可以把距离蚂蚁初始化位置**最近**的一端设置为蚂蚁的前进方向，当所有的蚂蚁都这般设置之后，它们的路程总和也就能达到最小值了！

一样的道理，我们把距离蚂蚁的初始化位置**最远**的一端设置为蚂蚁的前进方向，就可求得总路程的最大值了！

```c++
/*
题解：当两只蚂蚁相遇时，调转方向，其实等同于按原先方向前进，因为每只蚂蚁都一样，在同一个点（相遇时）到两端距离一样
*/
#include<iostream>
#include<cmath>
#include<string>
#include<iomanip>
#include<map>
#include<algorithm>
#include<bitset>
#include<vector>
using namespace std;
int L, n;
vector<int>a(n);
void fun()
{
	int Min = 0, Max = 0;
	for (int i = 0; i < n; i++)
	{
		/*
		分别比较每一只蚂蚁与杆两端的距离，选取较短的那方为蚂蚁前进的方向
		再选所有蚂蚁前进路程的最大值（保证每一只蚂蚁都能通过），即为总时间的最小值
		*/
		Min = max(Min, min(a[i], L - a[i]));
	}
	for (int i = 0; i < n; i++)
	{
		/*
		分别比较每一只蚂蚁离杆两端的距离，每一只都取数值较大的一端为前进方向
		*/
		Max = max(Max, max(a[i], L - a[i]));
	}
	cout << Min << " " << Max << endl;
}
int main()
{	
	int sum;
	cin >> sum;
	while (sum--)
	{
	    cin >> L >> n;
		for (int i = 0; i < n; i++)
		{
			int z;
			cin >> z;
			a.push_back(z);
		}
		fun();
		a.clear();
	}
	return 0;
}
```

### 电话游戏

**题目描述**：

A telephone number is a sequence of exactly 1111 digits such that its first digit is 8.

Vasya and Petya are playing a game. Initially they have a string ss of length nn (nn is odd) consisting of digits. Vasya makes the first move, then players alternate turns. In one move the player must choose a character and erase it from the current string. For example, if the current string 1121, after the player's move it may be 112, 111 or 121. The game ends when the length of string ss becomes 11. If the resulting string is a telephone number, Vasya wins, otherwise Petya wins.

You have to determine if Vasya has a winning strategy (that is, if Vasya can win the game no matter which characters Petya chooses during his moves).

**Input**

The first line contains one integer nn ($13≤n<10^5$, n is odd) — the length of string ss.

The second line contains the string s (|s|=n) consisting only of decimal digits.

**Output**

If Vasya has a strategy that guarantees him victory, print YES.

Otherwise print NO.

Examples

**Input**

```
13
8380011223344
```

**Output**

```
YES
```

**Input**

```
15
807345619350641
```

**Output**

```
NO
```

**Note**

In the first example Vasya needs to erase the second character. Then Petya cannot erase a character from the remaining string 880011223344 so that it does not become a telephone number.

In the second example after Vasya's turn Petya can erase one character character 8. The resulting string can't be a telephone number, because there is no digit 8 at all.

~~~c++
/*
题解：这时一个博弈论的问题，对方为了限制我方获胜，一定会把前导8消去，如果前导8的个数多于单方操作数，则无论对方如何操作，等数串长度为11时必定会有一个8在首位置；
*/
#include<iostream>
#include<cmath>
#include<string>
using namespace std;
int main()
{
	int n;
	string str;
	cin >> n >> str;
	int sum = 0;//记录前端8 的个数
	int count = (n - 11) / 2;//记录单方操作次数（n总为奇数）
	for (int i = 0; i < (n - 10); i++)
	{
		if (str[i] == '8')
			sum++;
	}
	if (sum > count)
		cout << "YES" << endl;
	else
		cout << "NO" << endl;
	return 0;
}
~~~

### **石头游戏**

[原题链接](https://leetcode-cn.com/problems/stone-game/)

**题目描述**
亚历克斯和李用几堆石子在做游戏。偶数堆石子排成一行，每堆都有正整数颗石子 piles[i] 。
游戏以谁手中的石子最多来决出胜负。石子的总数是奇数，所以没有平局。
亚历克斯和李轮流进行，亚历克斯先开始。 每回合，玩家从行的开始或结束处取走整堆石头。 这种情况一直持续到没有更多的石子堆为止，此时手中石子最多的玩家获胜。
假设亚历克斯和李都发挥出最佳水平，当亚历克斯赢得比赛时返回 true ，当李赢得比赛时返回 false 。

**示例：**

> 输入：[5,3,4,5] 
> 输出：true

**解释：**

> 亚历克斯先开始，只能拿前 5 颗或后 5 颗石子 。
> 假设他取了前 5 颗，这一行就变成了 [3,4,5] 。 
> 如果李拿走前 3颗，那么剩下的是 [4,5]，亚历克斯拿走后 5 颗赢得 10 分。 
> 如果李拿走后 5 颗，那么剩下的是 [3,4]，亚历克斯拿走后 4颗赢得 9 分。
> 这表明，取前 5 颗石子对亚历克斯来说是一个胜利的举动，
> 所以我们返回 true 。


**提示：**

> 2 <= piles.length <= 500 
> piles.length 是偶数。
> 1 <= piles[i] <= 500
> sum(piles) 是奇数。

**解题思路**
首先我们先来抓住题目的几个关键点：
1、石头的堆数是偶数，这一点说明双方进行的轮次相等
2、石头的总数是奇数，也就是说双方最后总能取出不一样的石头数（必分胜负）
3、玩家亚历克斯为先手，当先手获胜时返回true，否则为false;
4、亚历克斯和李两个玩家都发挥出最佳水平
5、玩家只能选择首尾两端的石头堆

我们先以上述的输入为例：
> 输入：[5,3,4,5] 
> 输出：true

这个输入样例总能够分出石头总数不一样的两堆：
第一种：

|        | 亚历克斯 | 李       |
| ------ | -------- | -------- |
| 第一轮 | 取第一堆 | 取第四堆 |
| 第二论 | 取第三堆 | 取第二堆 |

第二种：
|        | 亚历克斯 | 李       |
| ------ | -------- | -------- |
| 第一轮 | 取第四堆 | 取第一堆 |
| 第二轮 | 取第三堆 | 取第二堆 |
......
其实第二种情况的分析并无太多意义，对于每一种输入样例，先手（亚历克斯）==总能决定==自己选择偶数堆（即第二、四堆）还是奇数堆（即第一、三堆）；

证明：如果亚历克斯先选的第一堆，则剩下第二、三、四堆，无论李怎么选择，总会将奇数堆（第三堆）暴露在两端；
如果亚历克斯先选第四堆，则可供李选择的有第一、二、三堆，无论李如何选择，总会将偶数堆（第二堆）暴露于两端；

由于石头总数是奇数，所以对于偶数堆和奇数堆的石头数总是不一样的，所以先手（亚历克斯）总会先判断到底是偶数堆石头数更多还是奇数堆石头总数更多，然后再去选择必胜的一条路径！

所以这个游戏就变得非常容易；

**代码如下：**

```cpp
class Solution {
public:
    bool stoneGame(vector<int>& piles) {
        return true;
    }
};
```

## 数学

### 生日蛋糕

**题目描述**：

7月17日是Mr.W的生日，ACM-THU为此要制作一个体积为Nπ的M层生日蛋糕，每层都是一个圆柱体。
设从下往上数第i(1 <= i <= M)层蛋糕是半径为$R_i$, 高度为Hi的圆柱。当i < M时，要求$R_i > R_i+1且H_i > H_i+1$。
由于要在蛋糕上抹奶油，为尽可能节约经费，我们希望蛋糕外表面（最下一层的下底面除外）的面积Q最小。
令Q = Sπ
请编程对给出的N和M，找出蛋糕的制作方案（适当的Ri和Hi的值），使S最小。
（除Q外，以上所有数据皆为正整数）

**Input**

```
有两行，第一行为N（N <= 10000），表示待制作的蛋糕的体积为Nπ；第二行为M(M <= 20)，表示蛋糕的层数为M。
```

**Output**

```
仅一行，是一个正整数S（若无解则S = 0）。
```

**Sample Input**

```
100
2
```

**Sample Output**

```
68
```

**解题思路**

[原文链接]: https://blog.csdn.net/weixin_46295292/article/details/105853440

![在这里插入图片描述](C:%5CUsers%5CASUS%5CDocuments%5C20200430090255757.png)

蛋糕的体积：$V=Nπ=πR_1^2H_1+πR_2^2H_2+...+πR_n^2H_n$

**则**:
$$
N=R_1^2H_1+R_2^2H_2+...+R_n^2H_n
$$
**其中**
$$
(R_1>R_2>R_3>...>R_n且H_1>H_2>H_3>...>H_n)
$$
蛋糕的表面积：
$$
Q=Sπ=πR_1^2+2πR_1H_1+2πR_2H_2+...+2πR_nH_n
$$


**则**:
$$
S=R_1^2+2R_1H_1+2R_2H_2+...+2R_nH_n
$$
**其中**
$$
(R_1>R_2>R_3>...>R_n且H_1>H_2>H_3>...>H_n)
$$
**解题思路：**
**深度优先搜索：**
从底层开始搜索，当底层半径和高度确定时，则搭建剩余层蛋糕所需的最小体积（min_v）和最大体积(max_v)就可以确定了，因此，每当我们搭建完一层时，应该比较一下max_v,min_v和剩余可用体积（$V-V_i$）此时有三种情况：
第一种情况、

**如果搭建完剩余层所需的最小体积（$min_v$）比剩余可用体积（$V-V_i,其中V_i$表示搭建底层蛋糕已经使用的体积）还要大，则说明题目所固定的体积不够用，此时应该减小底层固定的半径$R_i和H_i$，直到它们满足第三种情况**

第二种情况、

**如果搭建完剩余蛋糕层所需最大体积（$max_v$）比剩余可用体积（$V-V_i$）还要小的话，则说用题目所固定的体积==用不完==，此时应该增大底层固定的半径$R_i和H_i$，直到它们满足第三种情况** 

第三种情况、
**如果搭建完剩余蛋糕层所需的最小体积（$min_v$）和最大体积（$max_v$）满足（$min_v<=(V-V_i)<=max_v$），则说明从第一层至当前层所搭建的蛋糕层符合题目要求，==继续向上层搭建==**

由题目给定的数值，我们无需考虑π值，则上述的体积可用直接用整数值表示，比如：
    当前已用体积$V_i=R_1^2H_1+R_2^2H_2+...R_i^2H_i$
    当前剩余体积$V-V_i=N-(R_1^2H_1+R_2^2H_2+...R_i^2H_i)=R_{i+1}^2H_{i+1}+R_{i+2}^2H_{i+2}+...R_n^2H_n$

   **1.首先确定第一层半径R和高度H的最大值最小值：**
 总共有M层蛋糕，且每一层的半径和高度都是==整数==$(R_1>R_2>R_3>...>R_n且H_1>H_2>H_3>...>H_n)$，则最高层的半径和高度至少为1，由此可以确定第一层的半径和高度皆不小于M;

 由$N=R_1^2H_1+R_2^2H_2+...+R_n^2H_n$ 得：

**当$M=1,H_1=1$时,$R_1$最大，且此时**
$$
R_1=\sqrt{N}
$$
**,所以R_1的取值范围为**
$$
[M,\sqrt{N}]
$$
**当$R_1$的值确定时**
$$
H_1=\frac{N}{R_1^2}
$$
**同时$H_1>=M$**

**2.依次确定上一层的半径和高的范围**

> $M-i+1<=R_i<=R_{i-1}-1;$
> $M-i+1<=H_i<=H_{i-1}-1;$

**3.根据已确定的半径和高度判断是否有可能满足题目要求**
即分析上述的三种情况，$max_v 和 min_v $和 V 三者之间的关系，若不满足则不断改变当前 R 和 H 的值，直到满足为止

**4.确定最小的表面积**
1.将第一次符合题目要求[^1]的表面积（$min_s$）记录，然后在遍历其它情况，如果在深度优先搜索的过程中发现已搭建的蛋糕表面积==大于==之前记录的==最小表面积==（$min_s$）则这种情况无需继续遍历，此时应该改变R和H的值，再进行另一种情况的遍历。
2.如果确立一种新的情况则判断当前蛋糕变面积和之前确立的最小表面积的值进行比较，如果当前$S<min_s$,则交换它们的值，（即更新最小表面积）

**5.当枚举完所有情况时返回最小表面积的值**

```C++
#include<iostream>
#include<cmath>
#include<string>
#include<iomanip>
#include<map>
#include<algorithm>
#include<queue>
#include<vector>
using namespace std;
int N, M;//分别表示体积和层数
int min_s = 0x7fffffff;//初始化为最大值
inline int min_v(int m)//此函数用于计算搭建剩余蛋糕层所需的最小体积
{
	int v = 0;
	//将剩余层的半径设为最小值，即从最高层到当前的上一层分别为1,2,3,4...M-m
	//每一层高度H取值和R一样,才能保证V最小；
	for (int R = M - m; R >= 1; R--)
	{
		int H = R;
		v += R * R * H;
	}
	return v;
}

inline int max_v(int r, int h, int m)//此函数用于计算搭建剩余蛋糕层所需的最大体积
{
	int v = 0;
	//由于上层半径和高度必须必下层半径和高度小，所以每一层半径和高度的最大取值应是其下层的半径或高度减一
	for (int R = r, H = h; m <= M; R--, H--,m++)
	{
		//只有将剩余的每一层的半径和高度设为最大值，才能保证求得的V最大
		v += R * R * H;
	}
	return v;
}
inline void dfs(int r, int h, int m, int s, int v)//深度优先搜索，其中m表示当前在搭建的层数，s表示已经搭建的蛋糕层的表面积,v表示已经使用的体积
{
	if (m == M && N == v && s < min_s)
	{
		min_s = s;//更新最小表面积的数值
		return;
	}
	//如果预估蛋糕的最小面积大于前面所确立最小面积，则无需继续深索
	if (r == 0 || s + 2 * (N - v) / r > min_s)
		return;

	//如果预估搭建剩余层所需的最小体积大于剩余可用体积，则说明规定的体积不够用，返回
	if (min_v(m) > N - v)//第一种情况
		return;

	//如果预估搭建剩余层所需的最大体积小于剩余可用体积，则说明无法达到规定的体积，返回
	if (max_v(r, h, m) < N - v)//第二种情况
		return;

	//如果上述条件均不满足，则说明满足第三种情况，则继续深索
	for (int R = r - 1; R >= M - m; R--)//枚举下一层蛋糕的半径
		for (int H = h - 1; H >= M - m; H--)//枚举下一层蛋糕的高
		{
			dfs(R, H, m + 1, s + 2 * R * H, v + R * R * H);//深搜下一层
		}
}
int main()
{
	scanf_s("%d%d", &N, &M);
	for (int r1 = M; r1 * r1 <= N; r1++)//枚举所有可能的半径和高度
		for (int h1 = N / (r1 * r1); h1 >= M; h1--)
		{
			int s = r1 * r1 + r1 * h1 * 2;//表示第一层的表面积
			int v = r1 * r1 * h1;//表示第一层使用的体积
			dfs(r1, h1, 1, s, v);//固定了第一层的半径和高度之后，往上层去探索
		}
	if (min_s == 0x7fffffff)
		cout << 0 << endl;
	else
		cout << min_s << endl;
	return 0;
}
```


