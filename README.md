# homework
冒泡排序
#include<stdio.h>//头文件
void bubblesort(int* arr, int n)//对函数名为BubbleSort的一个函数的声明，该函数的参数是一个整型数组和一个整型变量。 且此函数类型为空。
{
    for (int i = 0; i < n - 1; i++)//for循环
    {
        for (int j = 0; j < n - 1 - i; j++)
        {
            if (*(arr + j) > * (arr + j + 1))//比较两个数的大小
            {
                int t = *(arr + j);//定义整型变量t
                *(arr + j) = *(arr + j + 1);//将*(arr+j+1)赋值给*(arr+j)
                *(arr + j + 1) = t;
            }
        }
    }
}

int main()//主函数
{
    int arr[10] = { 2,4,7,9,6,3,1,5,8,10 };//输入数组中的数值
    bubblesort(arr, 10);
    for (int i = 0; i < 10; i++)
    {
        printf("%d\n", arr[i]);//打印对应的数值
    }
}
选择排序
#include <stdio.h>
int main()
{
    int i, j, t, a[5];    //定义变量及数组为基本整型
    printf("请输入4个数：\n");
    for (i = 1; i < 5; i++)
        scanf_s("%d", &a[i]);    //从键盘中输入要排序的4个数字
    for (i = 1; i <= 3; i++)
        for (j = i + 1; j <= 4; j++)
            if (a[i] > a[j])    //如果前一个数比后一个数大，则利用中间变量t实现两值互换
            {
                t = a[i];
                a[i] = a[j];
                a[j] = t;
            }
    printf("排序后的顺序是：\n");
    for (i = 1; i <= 4; i++)
        printf("%5d", a[i]);    //输出排序后的数组
    printf("\n");
    return 0;
}
链表
#include<stdio.h>
#include<stdlib.h>
void whoout(struct num* p, int max, int m);
struct num* create(int max);
struct num
{
	int out;
	struct num* next;
};
struct num* head = NULL;
struct num* end = NULL;
int main()
{
	int max;
	scanf_s("%d", &max);

	struct num* p = create(max);
	whoout(p, max, 3);

	system("pause");
	return 0;
}
//出局
void whoout(struct num* p, int max, int m)
{
	struct num* pt = NULL;
	struct num* temp = head;

	while (temp->next != temp)
	{
		for (int i = 1; i < m - 1; i++) /*数到m的人出列*/
		{
			pt = temp;
			temp = temp->next;
		}
		pt->next=temp->next;  /*将p指向的结点删除，即报数为m的人出列*/
		free(temp);
		temp=pt->next;          /*p指向下一个结点，重新开始报数*/
	}
	printf("%4d\n", temp->out);

}
//约瑟夫
struct num* create(int max)
{
	for (int i = 1; i <= max; i++)
	{
		struct num* pt = (struct num*)malloc(sizeof(struct num));
		if (pt != NULL)
		{
			pt->out = i;
			pt->next = NULL;

			if (head == NULL)
			{
				head = pt;
				end = pt;
			}
			else
			{
				end->next = pt;
				end = pt;
			}
			end->next = head;
		}
	}
	return head;
}
链表
#include <stdio.h>

#define MAXCHAR 101  /*最大允许字符串长度*/

int char_to_num(char ch);  /*返回字符对应的数字*/
char num_to_char(int num);  /*返回数字对应的字符*/
long source_to_decimal(char temp[], int source);  /*返回由原数转换成的10进制数*/
int decimal_to_object(char temp[], long decimal_num, int object);  /*返回转换成目标数制后字符数组的长度*/
void output(char temp[], int length);  /*将字符数组逆序打印*/

int main()
{
	int source;  /*存储原数制*/
	int object;  /*存储目标数制*/
	int length;  /*存储转换成目标数制后字符数组的长度*/
	long decimal_num;  /*存储转换成的10进制数*/
	char temp[MAXCHAR];  /*存储待转换的数值和转换后的数值*/
	int flag = 1;  /*存储是否退出程序的标志*/
	while (flag)  /*利用输入的flag值控制循环是否结束*/
	{
		printf("转换前的数是：");
		scanf_s("%s",temp);
		printf("转换前的数制是：");
		scanf_s("%d", &source);
		printf("转换后的数制是：");
		scanf_s("%d", &object);
		printf("转换后的数是：");
		decimal_num = source_to_decimal(temp, source);
		length = decimal_to_object(temp, decimal_num, object);
		output(temp, length);
		printf("继续请输入1,否则输入0：\n");
		scanf_s("%d", &flag);
	}

	return 0;
}
/*将字符转换成数字*/
int char_to_num(char ch)
{
	if (ch >= '0' && ch <= '9')
		return ch - '0';  /*将数字字符转换成数字*/
	else
		return ch - 'A' + 10;  /*将字母字符转换成数字*/
}
char num_to_char(int num)
{
	if (num >= 0 && num <= 9)
		return (char)('0' + num - 0);  /*将0~9之间的数字转换成字符*/
	else
		return (char)('A' + num - 10);  /*将大于10的数字转换成字符*/
}
long source_to_decimal(char temp[], int source)
{
	long decimal_num = 0;  /*存储展开之后的和*/
	int length;
	int i;
	for (i = 0; temp[i] != '\0'; i++);
	length = i;
	for (i = 0; i <= length - 1; i++)  /*累加*/
		decimal_num = (decimal_num * source) + char_to_num(temp[i]);
	return decimal_num;
}
int decimal_to_object(char temp[], long decimal_num, int object)
{
	int i = 0;
	while (decimal_num)
	{
		temp[i] = num_to_char(decimal_num % object);  /*求出余数并转换为字符*/
		decimal_num = decimal_num / object;  /*用十进制数除以基数*/
		i++;
	}
	temp[i] = '\0';
	return i;
}
void output(char temp[], int length)
{
	int i;
	for (i = length - 1; i >= 0; i--)  /*输出temp数组中的值*/
		printf("%c", temp[i]);
	printf("\n");
}

