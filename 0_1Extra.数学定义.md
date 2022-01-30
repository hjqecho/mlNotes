# 母函数

>参考链接：
>
>[母函数c++](https://blog.csdn.net/yu121380/article/details/79914529)

通过一个问题：

题目：有1克、2克、3克、4克的砝码各一 枚，能称出哪几种重量？

可以通过母函数$(x^0+x^1)*(x^0+x^2)*(x^0+x^3)*(x^0+x^4)$得到答案，多项式的长度对于其总数，其中包括0g。

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

> **定义1：**
>
> 设$D$​是非空集合，对$D$​中所有元素$\alpha$​​,都有一个子标集$A_\alpha$​与之对应，则这些指标集组成的集合称为以$D$​为指标组成的集合称为以$D$为指标集的集族，记作${A_\alpha|\alpha\in D}$，简称${A_\alpha}$。

例：若$D$​为自然数集，则存在${A_\alpha}$为集列$(A_1,A_2,\cdots,A_n,\cdots)$​。当$D$为线性序集时，称为集列时。

>**定义2：**
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

>**定义3：**
>
>
