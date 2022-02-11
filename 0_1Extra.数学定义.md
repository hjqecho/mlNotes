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

> 参考链接：
>
> [集合中的上极限与下极限](https://zhuanlan.zhihu.com/p/116829467)
>
> [集合论](https://zhuanlan.zhihu.com/p/111137112)

## 1.集合的并、交运算

> **定义1.1：**
>
> 设$D$​是非空集合，对$D$​中所有元素$\alpha$​​,都有一个子标集$A_\alpha$​与之对应，则这些指标集组成的集合称为以$D$​为指标组成的集合称为以$D$为指标集的集族，记作${A_\alpha|\alpha\in D}$，简称${A_\alpha}$。

例：若$D$​为自然数集，则存在${A_\alpha}$为集列$(A_1,A_2,\cdots,A_n,\cdots)$​。当$D$为线性序集时，称为集列时。

>**定义1.2：**
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

根据定义1.2，有以下结论：

>**定理1.1：**
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

（1）若$x\in B$，则$\forall\alpha\in D$，$x\in A_\alpha$，由无穷交的定义，$x\in\underset{\alpha\in D}{\bigcap}A_\alpha$，得证。

（2）若$x\in\underset{\alpha\in D}{\bigcup}A_\alpha$，由无穷并的定义，$\exist\alpha$，$s.t.\ x\in A_\alpha$，由$A_\alpha\subset B$，得$x\in B$，得证。

由以上的可以看出$\bigcap$​可以看成是$\forall$​（任意），$\bigcup$​可以看成是$\exist$​（存在）。

>**定理1.2：**
>
>对于一列集合$A_n$和集合$B$，
>$$
>\begin{align}
>&(1)B\cap(\bigcup_{n=1}^{\infty}A_n)=\bigcup_{n=1}^{\infty}(A_n\cap B),B\cup(\bigcap_{n=1}^{\infty}A_n)=\bigcap_{n=1}^{\infty}(A_n\cup B)\\
>&(2)B\cap(\bigcap_{n=1}^{\infty}A_n)=\bigcap_{n=1}^{\infty}(A_n\cap B),B\cup(\bigcup_{n=1}^{\infty}A_n)=\bigcup_{n=1}^{\infty}(A_n\cup B)
>\end{align}
>$$

**证明：**

（1）若$x\in B\cap(\underset{n=1}{\overset{\infty}{\bigcup}}A_n)$​，则$x\in B$​且$x\in\underset{n=1}{\overset{\infty}{\bigcup}}A_n$​，则$\exist n,x\in A_n$​，故$x\in A_n\cap B$​，由此$x\in\underset{n=1}{\overset{\infty}{\bigcup}}(A_n\cap B)$​，从而$B\cap(\underset{n=1}{\overset{\infty}{\bigcup}}A_n)\subset\underset{n=1}{\overset{\infty}{\bigcup}}(A_n\cap B)$​。

若$x\in\underset{n=1}{\overset{\infty}{\bigcup}}(A_n\cap B)$，则$\exist n，s.t.\ x\in A_n\cap B$，从而$x\in A_n$且$x\in B$，由此$x\in B\cap(\underset{n=1}{\overset{\infty}{\bigcup}}A_n)$，由此$\underset{n=1}{\overset{\infty}{\bigcup}}(A_n\cap B)\subset B\cap(\underset{n=1}{\overset{\infty}{\bigcup}}A_n)$。

综上，$B\cap(\underset{n=1}{\overset{\infty}{\bigcup}}A_n)=\underset{n=1}{\overset{\infty}{\bigcup}}(A_n\cap B)$成立。

（2）若$x\in B\cap(\underset{n=1}{\overset{\infty}{\bigcap}}A_n)$，则$x\in B$且$x\in\underset{n=1}{\overset{\infty}{\bigcap}}A_n$，从而$\forall n,s.t.\ x\in A_n\cap B$，因此$x\in\underset{n=1}{\overset{\infty}{\bigcap}}(A_n\cap B),B\cap(\underset{n=1}{\overset{\infty}{\bigcap}}A_n)\subset\underset{n=1}{\overset{\infty}{\bigcap}}(A_n\cap B)$。

若$x\in\underset{n=1}{\overset{\infty}{\bigcap}}(A_n\cap B)$，则$\forall n,s.t.\ x\in A_n\cap B$，因此$x\in\underset{n=1}{\overset{\infty}{\bigcap}}A_n$，从而$x\in B\cap(\underset{n=1}{\overset{\infty}{\bigcap}}A_n),\underset{n=1}{\overset{\infty}{\bigcap}}(A_n\cap B)\subset B\cap(\underset{n=1}{\overset{\infty}{\bigcap}}A_n)$。

综上，$B\cap(\underset{n=1}{\overset{\infty}{\bigcap}}A_n)=\underset{n=1}{\overset{\infty}{\bigcap}}(A_n\cap B)$​​​成立。

> **定理1.3：**
>
> 对于集列$A_n$和$B_n$​，有
> $$
> (\bigcup_{m=1}^{\infty}A_m)\cap(\bigcup_{n=1}^{\infty}B_n)=\bigcup_{m,n>1}A_m\cap B_n
> $$
>
> $$
> (\bigcap_{m=1}^{\infty}A_m)\cup(\bigcap_{n=1}^{\infty}B_n)=\bigcap_{m,n>1}A_m\cup B_n
> $$

## 2.集合的差、补运算

> **定义2.1：**
>
> 对于集合$A，B$属于集合A而不属于集合$B$的元素称为$A$减$B$的差集，记作$A\setminus B=\set{x\in A且x\notin B}$；当$B\subset A$时，则$A\setminus B$也记作$C_AB$​；当我们讨论的集合都是$X$的子集时，称$X$为全集，$C_XB$一般记作$B^c$，称作$B$的补集。

注：以下讨论中，一般只使用$A\setminus B$​与$B^c$​两种符号，$C_AB$​一般不使用，因此任何情况下都可以用$A\setminus B$​代替；此外，$A\setminus B$​在有些地方也记作$A-B$​。

> **定理2.1：**
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

> **定理2.2：**
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

> **定理2.3：**
> $$
> (A\setminus B)\cap C=A\cap C\setminus B\cap C,(A\setminus B)\setminus C=A\setminus(B\cup C)
> $$

**证明：**

若$x\in(A\setminus B)\cap C$​，则$x\in C且x\in A$​，故$x\in A\cap C$​，另外$x\notin B$​，因此$x\notin B\cap C$​，故$(A\setminus B)\cap C\subset A\cap C\setminus B\cap C$​；若$x\in A\cap C\setminus B\cap C$​，则$x\in A$，且$x\in C$，但$x\notin B\cap C$​​​，因为$x\in C$，所以$x\notin B$​，因此$A\cap C\setminus B\cap C\subset(A\setminus B)\cap C$​​。

若$x\in(A\setminus B)\setminus C$，则$x\in A,x\notin B,x\notin C$，于是$x\notin B\cup C$，因此$(A\setminus B)\setminus C\subset A\setminus(B\cup C)$。若$x\in A\setminus(B\cup C)$，则$x\in A,x\notin B\cup C$，则$x\notin B,x\notin C$，因此$A\setminus(B\cup C)\subset(A\setminus B)\setminus C$。​ 

> **定理2.4：(De Morgan公式)**
>
> 若集合$A,B\subset S$​，则有$(A\cup B)^c=A^c\cap B^c$​，$(A\cap B)^c=A^c\cup B^c$​

**证明：**

若$x\in(A\cup B)^c$​，则$x\notin A\cup B$​，故$x\notin A$​且$x\notin B$​，故$x\in A^c$​且$x\in B^c$​，得$(A\cup B)^c\subset A^c\cap B^c$​。

同上可证$A^c\cap B^c\subset(A\cup B)^c$，综合可得$(A\cup B)^c=A^c\cap B^c$

若$(A\cap B)^c$，则$x\notin A\cap B$，故$x\notin A$或$x\notin B$，故$x\in A^c$或$x\in B^c$，得$(A\cap B)^c\subset A^c\cup B^c$

同上可证$A^c\cup B^c\subset(A\cap B)^c$，综合可得$(A\cap B)^c=A^c\cup B^c$

> **定理2.5：**
>
> 设$\set{A_\alpha}$为指标集族，$S$​为全集，则
> $$
> (\bigcup_{\alpha\in D}A_\alpha)^c=\bigcap_{a\in\alpha}A_\alpha^c\\
> (\bigcap_{\alpha\in D}A_\alpha)^c=\bigcup_{a\in D}A_\alpha^c
> $$

> **定理2.6：**
> $$
> A\setminus(\bigcup_{\alpha\in D}A_\alpha)=\bigcap_{\alpha\in D}A\setminus A_\alpha\\
> A\setminus(\bigcap_{\alpha\in D}A_\alpha)=\bigcup_{\alpha\in D}A\setminus A_\alpha
> $$

**证明：**

首先，$A\setminus A_\alpha=A\cap(A\setminus A_\alpha)=(A\cap A)\setminus(A\cap A_\alpha)=A\setminus(A\cap A_\alpha)$​​​，将$(\bigcup_{\alpha\in D}A_\alpha)$看成一个整体可得
$$
A\setminus(\bigcup_{\alpha\in D}A_\alpha)=A\setminus(\bigcup_{\alpha\in D}A_\alpha\cap A)\xrightarrow{定理1.2}A\setminus[\bigcup_{\alpha\in D}(A_\alpha\cap A)]\\
A\setminus(\bigcap_{\alpha\in D}A_\alpha)=A\setminus(\bigcap_{\alpha\in D}A_\alpha\cap A)\xrightarrow{定理1.2}A\setminus[\bigcap_{\alpha\in D}(A_\alpha\cap A)]
$$

把A看做全集
$$
A\setminus[\bigcup_{\alpha\in D}(A_\alpha\cap A)]\xrightarrow{定理2.3}A\cap[\bigcup_{\alpha\in D}(A_\alpha\cap A)]^c\xrightarrow{定理2.6}\bigcap_{\alpha\in D}A_\alpha^c=\bigcap_{\alpha\in D}A\setminus A_\alpha
$$

> **定义2.2：**
>
> 对于集合$A,B$​，$(A\setminus B)\cup(B\setminus A)$​称为它们的对称差，记作$A\triangle B$​。

> **定理2.7：**
>
> （1）$A\triangle B=(A\cup B)\setminus (A\cap B)=(A\cap B^c)\cup(B\cap A^c)=A^c\triangle B^c$
>
> （2）$A\triangle B=\Phi\Leftrightarrow A=B$
>
> （3）$A\triangle B=B\triangle A$
>
> （2）$(A\triangle B)\triangle C=A\triangle(B\triangle C)$

## 3.集列的极限

>**定义3.1：**
>
>对于集列${A_n}$，若$\forall i$，都有$A_i\subset A_{i+1}$，则称${A_n}$是单调递增的集列；若$\forall i$，都有$A_{i+1}\subset A_i$，则称${A_n}$是单调递减的集列。

> **定义3.2：**
>
> 对于集列${A_n}$​，称
> $$
> \bigcap_{n=1}^{\infty}\bigcup_{j=n}^{\infty}A_j
> $$
> 为集列${A_n}$​的上极限，记作
> $$
> \overline{\lim_{n\rightarrow\infty}}A_n或\limsup\limits_{n\rightarrow\infty}A_n
> $$
> 称
> $$
> \bigcup_{n=1}^{\infty}\bigcap_{j=n}^{\infty}A_j
> $$
> 为集列${A_n}$​的下极限，记作
> $$
> \underline{\lim}\limits_{n\rightarrow\infty}A_n或\liminf\limits_{n\rightarrow\infty}A_n
> $$

对于$\underset{n=1}{\overset{\infty}{\bigcap}}\underset{j=n}{\overset{\infty}{\bigcup}}A_j$中的$\underset{j=n}{\overset{\infty}{\bigcup}}A_j$随着$j$的初始值$n$不断增大$\underset{j=n}{\overset{\infty}{\bigcup}}A_j$也是非增的，可以将$\underset{j=n}{\overset{\infty}{\bigcup}}A_j$看做是一个非增的集列，它们的并集叫上极限；对于$\underset{n=1}{\overset{\infty}{\bigcup}}\underset{j=n}{\overset{\infty}{\bigcup}}A_j$中的$\underset{j=n}{\overset{\infty}{\bigcap}}A_j$随着$j$的初始值$n$不断增大$\underset{j=n}{\overset{\infty}{\bigcap}}A_j$也是非减的，可以将$\underset{j=n}{\overset{\infty}{\bigcap}}A_j$看做是一个非减的集列，他们的交集叫下极限。

![image-20220205174515463](assets/0_1Extra.数学定义.assets/image-20220205174515463.png)

通过以下例子来理解：

存在以下集列$\set{A_n}_{n=1}^\infty$​：
$$
A_1=\set{1,a}\\A_2=\set{0,b}\\A_3=\set{1,b}\\A_4=\set{0,b}\\A_5=\set{1,b}\\\cdots
$$
观察上极限$\underset{n=1}{\overset{\infty}{\bigcap}}\underset{k=n}{\overset{\infty}{\bigcup}}A_k$​​和下极限$\underset{n=1}{\overset{\infty}{\bigcup}}\underset{k=n}{\overset{\infty}{\bigcap}}A_k$​​的情况​。

**上极限**

根据公式$[上极限]\underset{n=1}{\overset{\infty}{\bigcap}}\underset{k=n}{\overset{\infty}{\bigcup}}A_k$​是对于$A_k$​先并后交。先观察$\underset{k=n}{\overset{\infty}{\bigcup}}A_k$​：当$n=1$​时，可以将$\overset{\infty}{\underset{k=n=1}{\bigcup}}A_k$​近似看做所有集合的并集（简记$N_1=\set{0,1,a,b}$​），那么$N_1$​中必然包含所有的样本点；当$n=2$​时，$\overset{\infty}{\underset{k=n=2}{\bigcup}}A_k$​，可以看做除去$A_1$​的所有集合的并集（简记$N_2=\set{0,1,b}$​）。

当$N_1$​与$N_2$​取交集时，$A_1$​就可以不再考虑，如果$a$​仅属于$A_1$​的话，那么$A_1\bigcup A_2$​中就不再包含$a$​。以此类推，在后面的$A_3、A_4\cdots$​，若$a$​只属于有限个$A_k$​​​，​那么在取上极限的过程中会被抛弃，所以在本列中上极限为$\set{0,1,b}$（$a$只在$A_1$中存在，所以早就被踢出去了）。

所以上极限可以理解成为**在无穷个**$A_j$**集合中都存在的元素的集合**。那么$\set{A_n}$的上极限和取交集有什么区别？通过这个列子就可以看出，对$\set{A_n}$取交集及$\overset{\infty}{\underset{n=1}{\bigcap}}A_n$会取到空集，因为没有一个元素是所有集合都有的。区别就在于上极限中的元素$\omega$​​只需要属于无穷多个集合即可，不需要每个集合里都有。

因此上极限也可以这样表述：
$$
\limsup\limits_{x\rightarrow\infty}A_n=\set{\omega|\omega属于无穷多个A_n}
$$
**下极限**

根据公式$[下极限]\underset{n=1}{\overset{\infty}{\bigcup}}\underset{k=n}{\overset{\infty}{\bigcap}}A_k$​​​​​是对于$A_k$​​​​​先交后并。先观察$\underset{k=n}{\overset{\infty}{\bigcap}}A_k$​​​​​：当$n=1$​​​​​时，可以将$\overset{\infty}{\underset{k=n=1}{\bigcap}}A_k$​​​​​近似看做所有集合的交集（简记$M_1=\O$​​​）；当$n=2$​​​时，$\overset{\infty}{\underset{k=n=2}{\bigcap}}A_k$​​​，可以看做除去$A_1$​​​的所有集合的交集（简记$M_2=\set{b}$​​​​​​​），最终下极限为$\set{b}$。

此时发现下极限中没有元素$\set{0,1}$。其原因是：虽然它们存在于无数多个集合中，但同样的，也有无数个集合内没有元素$\set{0}$或$\set{1}$​​，这就涉及到了上极限和下极限的一个关键区别。

下极限中的元素也可以理解为**只在有限个集合里不存在**（比如改例子中的$\set{b}$，只不存在于$A_1$），可以看做下极限比上极限更加的严格，所以这也暗含了下极限的元素一定存在于无数多的集合中，因此下极限的元素一定存在于上极限中。

因此下极限也可以这样表述：
$$
\liminf\limits_{x\rightarrow \infty}A_n=\set{\omega|\omega至多不属于优先多个A_n}
$$


> **定理3.1：**
>
> 对于集列${A_n}$​，
> $$
> \begin{align}
> &(1)x\in\overline{\lim\limits_{n\rightarrow\infty}}A_n\Leftrightarrow\forall m，存在n\ge m，使得x\in A_n\\
> &(2)x\in\underline{\lim}\limits_{n\rightarrow\infty}A_n\Leftrightarrow\exists m，当n\ge m时，总有x\in A_n
> \end{align}
> $$

**证明：**

（1）若$x\in\overline{\lim\limits_{n\rightarrow\infty}}A_n$，则$x\in\underset{n=1}{\overset{\infty}{\bigcap}}\underset{j=n}{\overset{\infty}{\bigcup}}A_j$，则$\forall m$，都有$x\in\underset{j=m}{\overset{\infty}{\bigcup}}A_j$（因为$x$属于交集$\underset{n=1}{\overset{\infty}{\bigcap}}$，所以在后面的$\underset{j=n}{\overset{\infty}{\bigcup}}A_j$中必定含有$x$，所以$A_j$在大于等于$m$时必定会存在有$x\in A_j$），所以存在$n\ge m$时，有$x\in A_n$；若存在$n\ge m$，使得$x\in A_n$，则意味着$x\in\overset{\infty}{\underset{j=m}{\bigcup}}A_j$（因为在$\overset{\infty}{\underset{j=m}{\bigcup}}A_j$中也包含$A_n$），对于任意的$m$都成立。所以$x\in\underset{n=1}{\overset{\infty}{\bigcap}}\underset{j=n}{\overset{\infty}{\bigcup}}A_j=x\in\overline{\lim\limits_{n\rightarrow\infty}}A_n$。

（2）![image-20220207123004386](assets/0_1Extra.数学定义.assets/image-20220207123004386.png)

若若$x\in\underline{\lim}\limits_{n\rightarrow\infty}A_n$，则$x\in\underset{n=1}{\overset{\infty}{\bigcup}}\underset{j=n}{\overset{\infty}{\bigcap}}A_j$，则$\exists m$，都有$x\in\underset{j=m}{\overset{\infty}{\bigcap}}A_j$，所以对$\forall n$当$n\ge m$时，$x\in A_n$(因为是交集，所有在大于$m$的所有集合中都含有元素$x$)；当任意$n\ge m$时，有$x\in A_n$，所以$x\in\underset{j=n}{\overset{\infty}{\bigcap}}A_j$，存在有一个$m$使得其成立，则有$x\in\underset{n=1}{\overset{\infty}{\bigcup}}\underset{j=n}{\overset{\infty}{\bigcap}}A_j=\underline{\lim}\limits_{n\rightarrow\infty}A_n$。

上极限描集中的元素是属于原集列的任意子列的元素，或属于集列中无限个集合的元素；下极限描述的元素是仅仅不属于集列中有限个集合的元素。因此，显然地有$\underline{\lim}\limits_{n\rightarrow\infty}A_n\subset\overline{\lim\limits_{n\rightarrow\infty}}A_n$。​

> **定义3.3:**
>
> 若对于集列$\set{A_n}$，$\underline{\lim}\limits_{n\rightarrow\infty}A_n=\overline{\lim\limits_{n\rightarrow\infty}}A_n$，则称它是收敛集列，定义它的极限为$\lim\limits_{n\rightarrow\infty}A_n=\underline{\lim}\limits_{n\rightarrow\infty}A_n=\overline{\lim\limits_{n\rightarrow\infty}}A_n$

> **定理3.2：**
>
> 若集列$\set{A_n}$单调递减，则$\lim\limits_{n\rightarrow\infty}A_n=\underset{n=1}{\overset{\infty}{\bigcap}}A_n$；若集列$\set{A_n}$单调递增，则$\lim\limits_{n\rightarrow\infty}A_n=\underset{n=1}{\overset{\infty}{\bigcup}}A_n$。​

**证明：**

若集列$\set{A_n}$​单调递减，则$\overline{\lim\limits_{n\rightarrow\infty}}A_n=\underset{n=1}{\overset{\infty}{\bigcap}}\underset{j=n}{\overset{\infty}{\bigcup}}A_j=\underset{n=1}{\overset{\infty}{\bigcap}}A_n$​，$\underline{\lim}\limits_{n\rightarrow\infty}A_n=\underset{n=1}{\overset{\infty}{\bigcup}}\underset{j=n}{\overset{\infty}{\bigcap}}A_j=\underset{n=1}{\overset{\infty}{\bigcup}}\underset{j=1}{\overset{\infty}{\bigcap}}A_j=\underset{n=1}{\overset{\infty}{\bigcap}}A_n$​。若集列$\set{A_n}$​单调递增，则$\overline{\lim\limits_{n\rightarrow\infty}}A_n=\underset{n=1}{\overset{\infty}{\bigcap}}\underset{j=n}{\overset{\infty}{\bigcup}}A_j=\underset{n=1}{\overset{\infty}{\bigcap}}\underset{j=1}{\overset{\infty}{\bigcup}}A_j=\underset{j=1}{\overset{\infty}{\bigcup}}A_j$​，$\underline{\lim}\limits_{n\rightarrow\infty}A_n=\underset{n=1}{\overset{\infty}{\bigcup}}\underset{j=n}{\overset{\infty}{\bigcap}}A_j=\underset{n=1}{\overset{\infty}{\bigcup}}A_n$​。​

~~4.映射与双射~~

> ~~**定义4.1：**~~
>
> ~~设$A,B$​​为两个非空集合，若有一个对应法则$f$​​，使得$\forall x\in A$​​，有唯一一个$y\in B$​​与之对应，则称$f$​​为从$A$​​到$B$​​的映射，记作$f:A\rightarrow B$​​，对应元素的关系写作$y=f(x)$​​，集合$A$​​称为$f$​​的定义域，记作$D(f)$​​，$f(A)=\set{f(x)|x\in A}$​​是$f$​​的值域，记作$R(f)$​​​​​。~~

~~注：定义域的英文是Domain，值域是Range。只有$R(f)\subset B$​，不一定有$R(f)=B$​​​​。~~

# 集合系

集合组成的集合就是集合系。

> **定义1.1：**
>
> $\pi$​​系：如果$X$​​​上的非空集合系$\mathscr{P}$​​对**交的运算是封闭**的，即
> $$
> A,B\in\mathscr{P}\Rightarrow A\cap B\in\mathscr{P}
> $$
> 则称$\mathscr{P}$为$\pi$系。

例：对于实数集$\mathbf{R}$​​，有$\mathscr{P}\overset{def}{=}\set{(-\infty,a]:a\in\mathbf{R}}$​​​，对于有限交的运算是封闭的，组成一个实数空间$\mathbf{R}$​​上的$\pi$​​系​​

<img src="assets/0_1Extra.数学定义.assets/image-20220210180223610.png" alt="image-20220210180223610" style="zoom:25%;" />

> **定义1.2：**
>
> 半环：在满足$\pi$系的基础上还要满足以下条件，对于任意的$A,B\in \mathscr{L}$且$\pmb{\lceil A\supset B\rfloor}$，存在**有限个两两不相交**的$\set{C_k\in\mathscr{L},k=1,\cdots,n}$，使得$A\setminus B=\overset{n}{\underset{k=1}{\bigcup}}C_k$。

半环就是做完差之后，得到的值能够被一个或多个不相交集合完全填充。

<img src="assets/0_1Extra.数学定义.assets/image-20220210180445278.png" alt="image-20220210180445278" style="zoom:25%;" />

例：对任何$a,b\in \mathbf{R}$，令$\mathscr{L}\overset{def}{=}\set{(a,b]:a,b\in \mathbf{R}}$，所以对任何$(a,b],(c,d]\in\mathscr{L}$，$(a,b]\setminus(c,d]$可由$\mathscr{L}$中至多两个不交集合之并，所以是半环。 ​

<img src="assets/0_1Extra.数学定义.assets/image-20220207164326875.png" alt="image-20220207164326875" style="zoom:25%;" />

> **定义1.3:**
>
> 环：非空集合系$\mathscr{R}$对并和差的运算是封闭的，即：
> $$
> A,b\in\mathscr{R}\Rightarrow A\cup B,A\setminus B\in\mathscr{R}
> $$

例：
$$
\mathscr{R}\overset{def}{=}\bigcup_{n=1}^{\infty}\set{\bigcup_{k=1}^{n}(a_k,b_k]:a_k,b_k\in\mathbf{R}}
$$
可以得出集合若$A\in\mathscr{R}$​，则$\exists n$​使得$A=\underset{k=1}{\overset{n}{\bigcup}}(a_k,b_k]其中a_k,b_k\in\mathbf{R}$​

