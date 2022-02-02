# 母函数

>参考链接：
>
>[母函数c++](https://blog.csdn.net/yu121380/article/details/79914529)

通过一个问题：

题目：有1克、2克、3克、4克的砝码各一 枚，能称出哪几种重量？

可以通过母函数$(x^0+x^1)*(x^0+x^2)*(x^0+x^3)*(x^0+x^4)$​得到答案，指数的砝码的g数，系数是得到这个g数总共的方案数，多项式的长度对于其总数，其中包括0g。

更改题目：

输入砝码个数以及质量，质量不重复，输出目标方案的数量以及有哪些方法

例如：

  用1g、2g、3g的砝码称出10g的方案数以及方案（砝码个数不做限制）

```c++
#include <iostream>
#include <cstring>
#include <cstdlib>
using namespace std;
#include <vector>

/*
输入砝码个数以及质量，质量不重复，输出目标方案的数量以及有哪些方法
例如：
    用1g、2g、3g的砝码称出10g的方案数以及方案（砝码个数不做限制）
使用母函数
 */

#define max_pol 100 //最大多项式个数
#define max_num 100 //最大砝码个数

int main()
{
    int w_num[max_num];              //砝码个数
    int pol[max_pol], temp[max_pol]; //多项式以及临时存储，pol存的数是系数，下标是指数
    int num, target;                 //砝码数量和目标大小
    cout << "砝码个数:";
    cin >> num;
    vector<vector<vector<int>>> goucheng(max_pol); //每个项的构成，是三个嵌套的vector
    cout << "砝码质量:";
    for (int i = 1; i <= num; i++)
    {
        cin >> w_num[i];
    }
    cout << "目标质量:";
    cin >> target;
    memset(pol, 0, sizeof(pol));                                    //置零
    for (int i = 0, _temp = 0; i <= target; _temp++, i += w_num[1]) //初始化第一个多项式，将有的地方系数置一
    {
        pol[i] = 1;                   //初始化系数为一
        vector<int> tempV(num + 1);   //临时容器用来存储项的构成，这里是最小容器
        tempV[1] += _temp;            //存入构成，这里存入w_num[1]的个数
        goucheng[i].push_back(tempV); //存入上一级容器，就是中等容器
        if (i == target)              //等于目标质量则输出
        {
            for (vector<vector<int>>::iterator it = goucheng[i].begin(); it != goucheng[i].end(); it++) //遍历第i个中间容器，即等于target的中等容器
            {
                for (int j = 1; j <= num; j++) //输出构成
                {
                    if ((*it)[j]) //不是零才输出
                    {
                        cout << (*it)[j] << "个" << w_num[j] << "g"
                             << " ";
                    }
                }
                cout << endl;
            }
        }
    }
    memset(temp, 0, sizeof(temp)); //临时空间置零
    for (int i = 2; i <= num; i++) //生成第i个多项式，从第二个开始
    {
        for (int j = 0; j <= target; j++) //遍历pol多项式的每一项
        {
            for (int k = 0, _temp = 0; k + j <= target; _temp++, k += w_num[i]) //遍历第i个多项式的每一项
            {
                temp[k + j] += pol[j]; //幂运算
                if (k != 0)            //为第一项（即x^0项）时不输出
                {
                    if (k + j == target) //当幂运算结果等于目标时输出
                    {
                        for (vector<vector<int>>::iterator it = goucheng[j].begin(); it != goucheng[j].end(); it++) //遍历中等容器
                        {
                            if (!(*it)[i])
                            {
                                for (int l = 1; l <= num; l++) //最小容器的每一个值
                                {
                                    if ((*it)[l]) //不是零才输出
                                    {
                                        cout << (*it)[l] << "个" << w_num[l] << "g"
                                             << " ";
                                    }
                                }
                                //输出另一个数
                                cout << _temp << "个" << w_num[i] << "g"
                                     << " ";
                                cout << endl;
                            }
                        }
                    }
                    for (vector<vector<int>>::iterator it = goucheng[j].begin(); it != goucheng[j].end(); it++) //更改构成项
                    {
                        vector<int> tempV(*it);
                        tempV[i] += _temp;
                        int flag = 1;
                        for (vector<vector<int>>::iterator it = goucheng[k + j].begin(); it != goucheng[k + j].end(); it++) //去重
                        {
                            int count = 0;
                            for (int l = 1; l <= num; l++) //最小容器的每一个值
                            {
                                if ((*it)[l] == tempV[l])
                                {
                                    count++;
                                }
                                else
                                {
                                    break;
                                }
                            }
                            if (count == num)
                            {
                                flag = 0;
                            }
                        }
                        if (flag)
                        {
                            goucheng[k + j].push_back(tempV);
                        }
                    }
                }
            }
        }
        for (int j = 0; j <= target; j++) //将临时转到多项式上，临时清空准备下一次
        {
            pol[j] = temp[j];
            temp[j] = 0;
        }
    }
    cout << pol[target] << endl;
    system("pause");
    return 0;
}

```

# 集合

> **定理1：**
>
> 设$D$​是非空集合，对$D$​中所有元素$\alpha$​​,都有一个子标集$A_\alpha$​与之对应，则这些指标集组成的集合称为以$D$​为指标组成的集合称为以$D$为指标集的集族，记作${A_\alpha|\alpha\in D}$，简称${A_\alpha}$。

例：若$D$​为自然数集，则存在${A_\alpha}$为集列$(A_1,A_2,\cdots,A_n,\cdots)$​。当$D$为线性序集时，称为集列时。

>**定理2：**
>
>对于集族${A_\alpha}$​​，若存在$\alpha$​​，$such\ that(s.t.)\ x\in A_\alpha$​​​​，则称
>$$
>x\in\bigcup_{\alpha\in D}A_\alpha
>$$
>即
>$$
>\bigcup_{\alpha\in D}A_\alpha=\{x|\exist\alpha\in D,x\in A_\alpha\}
>$$
>若任意$\alpha$​，满足$x\in A_\alpha$​​，则称
>$$
>x\in\bigcap_{\alpha\in D}A_\alpha
>$$
>即
>$$
>\bigcap_{\alpha\in D}A_\alpha=\{x|\forall\alpha\in D,x\in A_\alpha\}
>$$

根据定义2，有以下定义：

>**定理3：**
>
>对于集族$A_n$和集合$B$，有：
>
>（1）若$\forall\alpha\in D$，有$B\subset A_\alpha$，则
>$$
>B\subset\bigcap_{\alpha\in D}A_\alpha
>$$
>（2）若$\forall\alpha\in D$，有$A_\alpha\subset B$，则
>$$
>\bigcup_{\alpha\in D}A_\alpha \subset B
>$$

**证明：**

（1）若$x\in B$，则$\forall\alpha\in D$，$x\in A_\alpha$，由无穷交的定义，$x\in\bigcap_{\alpha\in D}A_\alpha$，得证。

（2）若$x\in\bigcup_{\alpha\in D}A_\alpha$​，由无穷并的定义，$\exist\alpha$​，$s.t.\ x\in A_\alpha$​，由$A_\alpha\subset B$​​，得$x\in B$​，得证。

由以上的可以看出$\bigcap$​可以看成是$\forall$​（任意），$\bigcup$​可以看成是$\exist$​（存在）。

>**定理4：**
>
>对于一列集合$A_n$和集合$B$，
>$$
>\begin{align}
>&(1)B\cap(\bigcup_{n=1}^{\infty}A_n)=\bigcup_{n=1}^{\infty}(A_n\cap B),B\cup(\bigcap_{n=1}^{\infty}A_n)=\bigcap_{n=1}^{\infty}(A_n\cup B)\\
>&(2)B\cap(\bigcap_{n=1}^{\infty}A_n)=\bigcap_{n=1}^{\infty}(A_n\cap B),B\cup(\bigcup_{n=1}^{\infty}A_n)=\bigcup_{n=1}^{\infty}(A_n\cup B)
>\end{align}
>$$

**证明：**

（1）若$x\in B\cap(\bigcup_{n=1}^{\infty}A_n)$​，则$x\in B$​且$x\in\bigcup_{n=1}^{\infty}A_n$​，则$\exist n,x\in A_n$​，故$x\in A_n\cap B$​，由此$x\in\bigcup_{n=1}^\infty(A_n\cap B)$​，从而$B\cap(\bigcup_{n=1}^{\infty}A_n)\subset\bigcap_{n=1}^{\infty}(A_n\cap B)$。

若$x\in\bigcup_{n=1}^{\infty}(A_n\cap B)$，则$\exist n，s.t.\ x\in A_n\cap B$，从而$x\in A_n$且$x\in B$，由此$x\in B\cap(\bigcup_{n=1}^{\infty}A_n)$，由此$\bigcup_{n=1}^{\infty}(A_n\cap B)\subset B\cap(\bigcup_{n=1}^{\infty}A_n)$。

综上，$B\cap(\bigcup_{n=1}^{\infty}An)=\bigcup_{n=1}^{\infty}(A_n\cap B)$成立。

（2）若$x\in B\cap(\bigcap_{n=1}^{\infty}A_n)$​，则$x\in B$​且$x\in\bigcap_{n=1}^{\infty}A_n$​，从而$\forall n,s.t.\ x\in A_n\cap B$​，因此$x\in\bigcap_{n=1}^{\infty}(A_n\cap B),B\cap(\bigcap_{n=1}^{\infty}A_n)\subset\bigcap_{n=1}^{\infty}(A_n\cap B)$。

若$x\in\bigcap_{n=1}^{\infty}(A_n\cap B)$，则$\forall n,s.t.\ x\in A_n\cap B$，因此$x\in\bigcap_{n=1}^{\infty}A_n$，从而$x\in B\cap(\bigcap_{n=1}^{\infty}A_n),\bigcap_{n=1}^{\infty}(A_n\cap B)\subset B\cap(\bigcap_{n=1}^{\infty}A_n)$。

综上，$B\cap(\bigcap_{n=1}^{\infty}A_n)=\bigcap_{n=1}^{\infty}(A_n\cap B)$​成立。

> **定理4：**
>
> 对于集合$A，B$属于集合A而不属于集合$B$的元素称为$A$减$B$的差集，记作$A\setminus B=\set{x\in A且x\notin B}$；当$B\subset A$时，则$A\setminus B$也记作$C_AB$​；当我们讨论的集合都是$X$的子集时，称$X$为全集，$C_XB$一般记作$B^c$，称作$B$的补集。

注：以下讨论中，一般只使用$A\setminus B$​与$B^c$​两种符号，$C_AB$​一般不使用，因此任何情况下都可以用$A\setminus B$​代替；此外，$A\setminus B$​在有些地方也记作$A-B$​。

> **定理5：**
>
> 设$S$是全集，集合$A\subset S$，则有：
> $$
> \begin{align}
> &(1)A\cap A^c=\Phi,A\cup A^c=S\\
> &(2)若x\in S,则x\notin A\Leftrightarrow x\in A^c,x\in A\Leftrightarrow x\notin A^c\\
> &(3)(A^c)^c=A
> \end{align}
> $$

**证明：**

（1）若$\exists x\in A\cap A^c$，则$x\in A$且$x\in A^c$，由此得出$x\notin A$，矛盾，从而得到$A\cap A^c=\Phi$。显然$A\cup A^c\subset S$，而$\forall x\in S$，若$x\notin A$，则有$x\in\set{x|x\in S且x\notin A}=A^c$，故$s\subset A\cup A^c$，综上$A\cup A^c=S$。

（2）若$x\notin A$，由$A^c$的定义得，$x\in A^c$，故$\Rightarrow$成立，同理$\Leftarrow$也成立。

（3）若$x\in S$​，则$x\in A\Leftrightarrow x\notin A^c\Leftrightarrow x\in(A^c)^c$，因此得证。



> **定理6：**
>
> 若集合$A,B\subset S$，则有
> $$
> \begin{align}
> &(1)A\subset B\Leftrightarrow B^c\subset A^c\\
> &(2)A\setminus B=A\cap B^c
> \end{align}
> $$

**证明：**

（1）若$\forall x\in A$，有$x\in B$，取逆否命题：若$x\notin B(x\in B^c)$，有$x\notin A(x\in A^c)$，可得到$A\subset B\Leftrightarrow B^c\subset A^c$​。

（2）若$x\in A\setminus B$​，则$x\in A且x\notin B$​，由定理5的（2）可知$x\notin B\Leftrightarrow x\in B^c$，得证$A\setminus B\sub A\cap B^c$。

若$x\in A\cap B^c$，则$x\in A且x\in B^c$，同理可的$x\in B^c\Leftrightarrow x\notin B$，可得$A\cap B^c\subset A\setminus B$。

综上，$A\setminus B=A\cap B^c$​。

> 定理7：
> $$
> (A\setminus B)\cap C=A\cap C\setminus B\cap C,(A\setminus B)\setminus C=A\setminus(B\cup C)
> $$

**证明：**

若$x\in(A\setminus B)\cap C$​，则$x\in C且x\in A$​，故$x\in A\cap C$​，另外$x\notin B$​，因此$x\notin B\cap C$​，故$(A\setminus B)\cap C\subset A\cap C\setminus B\cap C$​；若$x\in A\cap C\setminus B\cap C$​，则$x\in A$，且$x\in C$，但$x\notin B\cap C$​​​，因为$x\in C$，所以$x\notin B$​，因此$A\cap C\setminus B\cap C\subset(A\setminus B)\cap C$​​。

若$x\in(A\setminus B)\setminus C$，则$x\in A,x\notin B,x\notin C$，于是$x\notin B\cup C$，因此$(A\setminus B)\setminus C\subset A\setminus(B\cup C)$。若$x\in A\setminus(B\cup C)$，则$x\in A,x\notin B\cup C$，则$x\notin B,x\notin C$，因此$A\setminus(B\cup C)\subset(A\setminus B)\setminus C$。​ 

> **定理8：(De Morgan公式)**
>
> 若集合$A,B\subset S$，则有$(A\cup B)^c=A^c\cap B^c$，$(A\cap B)=A^c\cup B^c$

**证明：**

