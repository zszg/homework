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
