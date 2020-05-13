
老师布置的作业
```
#include <stdio.h>
#include<stdlib.h>
#include<math.h>
int MRFun_58(int a_58, int m_58, int n_58);

int main()
{
	int a_58, m_58, n_58;
	printf("请输入数字(a, m, n):");
	scanf("%d%d%d", &a_58, &m_58, &n_58);
	printf("最终结果：%d\n", MRFun_58(a_58, m_58, n_58));
	system("pause");
	return 0;
}
int MRFun_58(int a_58, int m_58, int n_58)//模重复平方算法函数
{
	int mm_58[20];
	int sum = 0;
	int i = 0, j, tmp, b, a = 1;
	tmp = m_58;
	b = a_58;
	while (tmp)//用位移实现十进制转二进制
	{
		mm_58[i] = tmp & 0x01;
		tmp = tmp >> 1;
		i++;
	}
	j = i;
	printf("模重复平方算法过程：\n");
	for (i = 0; i < j; i++)
	{
		if (mm_58[i] == 1)
		{
			a = (a * b) % n_58;
		}
		b = (b * b) % n_58;
		sum += mm_58[i] * pow(2, i);
		printf("i = %2d, m = %2d, a = %2d, b = %2d\n", i, sum, a, b);
	}
	return a;
}
```
