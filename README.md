#define _CRT_SECURE_NO_WARNINGS 

#include<stdio.h>
#include<windows.h>
#include<stdlib.h>
#include<time.h>
#include<conio.h>
#include<queue>
#include<ctype.h>
#define A 17	//地图的高
#define B 17	//地图的宽
#define C 10	//雷的总数
using namespace std;

//全局变量
DWORD a, b;//DWORD=double word
char map[A][B], news, spare;//
int BoomTotalNum, floatx, floaty, flag[A][B], flagnum, mode, slect[A][B], game;//定义变量

//颜色属性
const WORD FORE_BLUE = FOREGROUND_BLUE;	//蓝色文本属性
const WORD FORE_GREEN = FOREGROUND_GREEN;	//绿色文本属性
const WORD FORE_RED = FOREGROUND_RED;	//红色文本属性

//开垦地图结构体 
struct node {//struct inode──字符设备驱动相关的重要结构介绍
	int x;
	int y;//定义整型变量
};
queue <node> dui;//queue列、node节点

//打印位置
void position(int x, int y) {//对应位置
	COORD pos = { x,y };
	HANDLE Out = GetStdHandle(STD_OUTPUT_HANDLE);//传递出对应位置信息
	SetConsoleCursorPosition(Out, pos);
}

//隐藏光标 
void Hide() {
	HANDLE handle = GetStdHandle(STD_OUTPUT_HANDLE);
	CONSOLE_CURSOR_INFO CursorInfo;
	GetConsoleCursorInfo(handle, &CursorInfo);//获取控制台光标信息  
	CursorInfo.bVisible = false; //隐藏控制台光标  
	SetConsoleCursorInfo(handle, &CursorInfo);//设置控制台光标状态   
}

//初始化
void Beginning() {
	while (!dui.empty()) {
		dui.pop();
	}
	game = 1;
	//BoomTotalNum=C;
	floatx = A / 2;
	floaty = B / 2;
	flagnum = 0;
	BoomTotalNum = C;
	mode = 0;
	HANDLE handle_out = GetStdHandle(STD_OUTPUT_HANDLE);    //获得标准输出设备句柄  
	CONSOLE_SCREEN_BUFFER_INFO csbi;                        //定义窗口缓冲区信息结构体  
	GetConsoleScreenBufferInfo(handle_out, &csbi);          //获得窗口缓冲区信息
	int x, y;
	srand((unsigned)time(0));
	for (int i = 0; i < A; i++) for (int j = 0; j < B; j++) {
		map[i][j] = ' ';
		flag[i][j] = 0;
		slect[i][j] = 0;
	}
	while (BoomTotalNum) {
		x = rand() % A;
		y = rand() % B;
		if (map[x][y] == ' ') {
			map[x][y] = '@';
			BoomTotalNum--;
		}
	}
	SetConsoleTextAttribute(handle_out, FORE_GREEN);
	for (int i = 0; i < A; i++) {
		for (int j = 0; j < B; j++) printf("█");
		printf("\n");
	}
	position(floaty * 2, floatx);
	SetConsoleTextAttribute(handle_out, FORE_RED);
	printf("");	//光标位置
	position(44, 9);
	printf("扫雷模式");
	position(44, 5);
	printf("剩余雷数：%d ", C - flagnum);
	SetConsoleTextAttribute(handle_out, FORE_GREEN);
	position(5, 22);
	printf("按“空格”切换模式");
	position(5, 23);
	printf("按“Enter”确认");
	position(5, 24);
	printf("按“方向键”选择方块");

}

//打印地图的一块儿 
void Lump(int xx, int yy) {
	switch (map[xx][yy]) {
	case '1': printf("①"); break;	//周围雷的数量（下同） 
	case '2': printf("②"); break;
	case '3': printf("③"); break;
	case '4': printf("④"); break;
	case '5': printf("⑤"); break;
	case '6': printf("⑥"); break;
	case '7': printf("⑦"); break;
	case '8': printf("⑧"); break;
	case ' ':
		if (xx == floatx && yy == floaty) {
			if (flag[xx][yy] == 0) {
				if (mode % 2 == 0) printf("");
				else printf("");
			}
			else printf("");
		}
		else {
			if (flag[xx][yy] == 0) printf("█");
			else printf("");
		}
		break;
	case '@':
		if (xx == floatx && yy == floaty) {
			if (flag[xx][yy] == 0) {
				if (mode % 2 == 0) printf("");
				else printf("");
			}
			else printf("");
		}
		else {
			if (flag[xx][yy] == 0) printf("█");
			else printf("");
		}
		break;
	case 'x': if (floatx == xx && floaty == yy) printf(""); else printf("  "); break;	//已经挖开的空白
	}
}

//移动光标
void Move() {
	HANDLE handle_out = GetStdHandle(STD_OUTPUT_HANDLE);    //获得标准输出设备句柄  
	CONSOLE_SCREEN_BUFFER_INFO csbi;                        //定义窗口缓冲区信息结构体  
	GetConsoleScreenBufferInfo(handle_out, &csbi);          //获得窗口缓冲区信息
	int xxx, yyy;
	xxx = floatx;
	yyy = floaty;
	switch (news) {
	case 72: floatx--; break;	//上 
	case 80: floatx++; break;	//下 
	case 75: floaty--; break;	//左 
	case 77: floaty++; break;	//右 
	}
	if (floatx == -1) floatx = A - 1; floatx %= A;	//两端穿模处理 
	if (floaty == -1) floaty = B - 1; floaty %= B;

	position(yyy * 2, xxx);
	SetConsoleTextAttribute(handle_out, FORE_GREEN);
	Lump(xxx, yyy);	//删除原位置

	if (map[floatx][floaty] == 'x') {
		position(floaty * 2, floatx);
		printf("  ");
	}

	position(floaty * 2, floatx);
	SetConsoleTextAttribute(handle_out, FORE_BLUE);
	Lump(floatx, floaty);	//更新新位置 
}

//插旗和排雷模式切换 
void Mode() {
	HANDLE handle_out = GetStdHandle(STD_OUTPUT_HANDLE);    //获得标准输出设备句柄  
	CONSOLE_SCREEN_BUFFER_INFO csbi;                        //定义窗口缓冲区信息结构体  
	GetConsoleScreenBufferInfo(handle_out, &csbi);          //获得窗口缓冲区信息
	mode++;
	SetConsoleTextAttribute(handle_out, FORE_BLUE);
	position(floaty * 2, floatx);
	if (mode % 2 == 0) printf("");
	else printf("");

	position(44, 9);
	if (mode % 2 == 0) {
		SetConsoleTextAttribute(handle_out, FORE_BLUE);
		printf("扫雷模式");
	}
	else {
		SetConsoleTextAttribute(handle_out, FORE_RED);
		printf("插旗模式");
	}
}

//该点周围地雷数 
int Boomnum(int xx, int yy) {//利用if语句进行判断
	int num = 0;
	if ((xx - 1 >= 0) && (yy - 1 >= 0) && (map[xx - 1][yy - 1] == '@')) num++;
	if ((xx - 1 >= 0) && (yy + 0 >= 0) && (map[xx - 1][yy] == '@')) num++;
	if ((xx - 1 >= 0) && (yy + 1 < B) && (map[xx - 1][yy + 1] == '@')) num++;
	if ((xx + 0 >= 0) && (yy - 1 >= 0) && (map[xx][yy - 1] == '@')) num++;
	if ((xx + 0 >= 0) && (yy + 1 < B) && (map[xx][yy + 1] == '@')) num++;
	if ((xx + 1 < A) && (yy - 1 >= 0) && (map[xx + 1][yy - 1] == '@')) num++;
	if ((xx + 1 < A) && (yy + 0 >= 0) && (map[xx + 1][yy] == '@')) num++;
	if ((xx + 1 < A) && (yy + 1 < B) && (map[xx + 1][yy + 1] == '@')) num++;
	return num;
}

//更新地图 
void Open() {
	node c;
	node d;
	while (!dui.empty()) {
		dui.pop();
	}
	c.x = floatx;
	c.y = floaty;
	dui.push(c);
	slect[c.x][c.y] = 1;
	while (!dui.empty()) {
		c = dui.front();
		dui.pop();
		if (Boomnum(c.x, c.y) != 0) {
			map[c.x][c.y] = (Boomnum(c.x, c.y) + 48);
			continue;
		}
		else {
			map[c.x][c.y] = 'x';
			if ((c.x - 1 >= 0) && (c.y - 1 >= 0) && (map[c.x - 1][c.y - 1] == ' ') && (slect[c.x - 1][c.y - 1] == 0)) {
				d.x = c.x - 1;
				d.y = c.y - 1;
				dui.push(d);
				slect[d.x][d.y] = 1;
			}
			if ((c.x - 1 >= 0) && (c.y - 0 >= 0) && (map[c.x - 1][c.y] == ' ') && (slect[c.x - 1][c.y] == 0)) {
				d.x = c.x - 1;
				d.y = c.y - 0;
				dui.push(d);
				slect[d.x][d.y] = 1;
			}
			if ((c.x - 1 >= 0) && (c.y + 1 < B) && (map[c.x - 1][c.y + 1] == ' ') && (slect[c.x - 1][c.y + 1] == 0)) {
				d.x = c.x - 1;
				d.y = c.y + 1;
				dui.push(d);
				slect[d.x][d.y] = 1;
			}
			if ((c.x - 0 >= 0) && (c.y - 1 >= 0) && (map[c.x][c.y - 1] == ' ') && (slect[c.x][c.y - 1] == 0)) {
				d.x = c.x - 0;
				d.y = c.y - 1;
				dui.push(d);
				slect[d.x][d.y] = 1;
			}
			if ((c.x - 0 >= 0) && (c.y + 1 < B) && (map[c.x][c.y + 1] == ' ') && (slect[c.x][c.y + 1] == 0)) {
				d.x = c.x - 0;
				d.y = c.y + 1;
				dui.push(d);
				slect[d.x][d.y] = 1;
			}
			if ((c.x + 1 < A) && (c.y - 1 >= 0) && (map[c.x + 1][c.y - 1] == ' ') && (slect[c.x + 1][c.y - 1] == 0)) {
				d.x = c.x + 1;
				d.y = c.y - 1;
				dui.push(d);
				slect[d.x][d.y] = 1;
			}
			if ((c.x + 1 < A) && (c.y - 0 >= 0) && (map[c.x + 1][c.y] == ' ') && (slect[c.x + 1][c.y] == 0)) {
				d.x = c.x + 1;
				d.y = c.y - 0;
				dui.push(d);
				slect[d.x][d.y] = 1;
			}
			if ((c.x + 1 < A) && (c.y + 1 < B) && (map[c.x + 1][c.y + 1] == ' ') && (slect[c.x + 1][c.y + 1] == 0)) {
				d.x = c.x + 1;
				d.y = c.y + 1;
				dui.push(d);
				slect[d.x][d.y] = 1;
			}
		}
	}
}

int main() {


Relife:	//重玩处
	HANDLE handle_out = GetStdHandle(STD_OUTPUT_HANDLE);    //获得标准输出设备句柄  
	CONSOLE_SCREEN_BUFFER_INFO csbi;                        //定义窗口缓冲区信息结构体  
	GetConsoleScreenBufferInfo(handle_out, &csbi);          //获得窗口缓冲区信息

	Hide();		//隐藏光标
	Beginning();//初始化地图
	a = GetTickCount();
	while (1) {
		if (_kbhit() != 0) {
			spare = _getch();

			//按其他
			if ((spare != (-32)) && (spare != 13) && (spare != ' ')) continue;//跳过 

			//按Enter
			if (spare == 13) {	//确认 
				//排雷
				if (mode % 2 == 0) {
					if (map[floatx][floaty] == '@' && flag[floatx][floaty] == 0) {
						break;	//触雷
						game = 0;
					}

					if (flag[floatx][floaty] == 1) continue;	//有旗跳过
					Open();
					position(0, 0);
					SetConsoleTextAttribute(handle_out, FORE_GREEN);
					for (int i = 0; i < A; i++) {
						for (int j = 0; j < B; j++) Lump(i, j);
						printf("\n");
					}
					position(floaty * 2, floatx);
					SetConsoleTextAttribute(handle_out, FORE_BLUE);
					Lump(floatx, floaty);
				}

				//插拔旗
				else {

					//不能插旗的地方
					if (map[floatx][floaty] == 'x' || (map[floatx][floaty] > '0' && map[floatx][floaty] < '9'))
						continue;	//跳过

					//插旗
					if (flag[floatx][floaty] == 0) {
						flagnum++;
						flag[floatx][floaty] = 1;
						position(floaty * 2, floatx);
						SetConsoleTextAttribute(handle_out, FORE_BLUE);
						Lump(floatx, floaty);
					}

					//拔旗 
					else {
						flagnum--;
						flag[floatx][floaty] = 0;
						position(floaty * 2, floatx);
						SetConsoleTextAttribute(handle_out, FORE_BLUE);
						Lump(floatx, floaty);
					}
				}
			}

			//按空格
			if (spare == ' ') Mode();	//切换模式 

			//按方向键 
			if (spare == -32) {
				news = _getch();
				Move();	//移动光标
			}
			for (int i = 0; i < A; i++) for (int j = 0; j < B; j++) if (map[i][j] == 'x' || (map[i][j] > '0' && map[i][j] < '9')) game++;
			if (game == A * B - C + 1) break;
			else game = 1;
			SetConsoleTextAttribute(handle_out, FORE_RED);
			position(44, 5);
			printf("剩余雷数：%d ", C - flagnum);
		}
		else Sleep(10);
		b = GetTickCount64();
		SetConsoleTextAttribute(handle_out, FORE_RED);
		position(44, 7);
		printf("用时：");	//用时 
		if ((b - a) / 60000 < 10) printf("0");
		printf("%d:", (b - a) / 60000);
		if (((b - a) / 1000) % 60 < 10) printf("0");
		printf("%d:", ((b - a) / 1000) % 60);
		if (((b - a) / 10) % 100 < 10) printf("0");
		printf("%d", ((b - a) / 10) % 100);
	}
	SetConsoleTextAttribute(handle_out, FORE_RED);
	position(5, 5);
	if (game == 1) printf("游戏结束！");
	else printf("恭喜通关！");
	position(5, 8);
	printf("任意键重玩");
	scanf_s("%c %c", &spare, &spare);
	system("cls");
	position(0, 0);
	goto Relife;
}

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

