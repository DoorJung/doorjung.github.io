---
title:  "문제: 시계 맞추기"
search: true
categories: 
  - algorithm
last_modified_at: 2019-06-21T10:00:00-12:00:00
---
시계 맞추기
===

문제
---
알고리즘 문제해결전략 6.8~6.9장의 시계 맞추기 문제입니다.  
[문제](https://algospot.com/judge/problem/read/CLOCKSYNC)  
문제에 대한 자세한 설명은 위 링크에 있습니다.  

문제접근
---
완전탐색 레시피를 적용시켜 문제를 접근해봅시다!

### 1. 시간안에 풀 수 있을까?
시간복잡도를 구하기위해 모든 방법의 수를 생각해 봅시다.
얼핏보면 무한개가 나오지 않을까 할 수 있지만 한 스위치를 4번 이상 누르면 제자리로 돌아오기 때문에 스위치를 누르는 횟수는 **0에서 3사이의 정수**로 한정을 해줄 수 있다.
10개의 스위치가 있으므로 전체 경우의 수는 **4^10 = 1048576** 이다.

시간 제한은 10초 이므로 완전탐색으로 해결할 수 있는 문제이다!


### 2. 답을 만드는 과정을 여러 개의 조각으로 나누자
10개의 스위치를 생각해 문제를 모두 **열 조각**으로 나눈 후 **각 조각에서 한 스위치를 누를 횟수를 정하자**.

주어진 게임판에 블록을 한개 내려놓고 남은 흰 칸들을 재귀 호출을 이용해 덮으면 문제를 풀 수 있겠다.


###  3. 답의 일부를 만들고, 나머지 답을 재귀호출을 통해 완성
스위치의 상태(몇시에 가있는지)를 확인할 값과 스위치와 시계의 연결 유무를 판단할 값이 필요할 것이다.  
형태는 대략 스위치를 누름->재귀->스위치를 누르지 않은상태 이런형태가 될 것이다.  
**스위치를 누르지 않은 상태**는 **4번 스위치를 누른 것**과 같으므로 이점을 유의해 처리하자.  
또한 불가능하다면 -1을 출력하는 것에 유의해 처리하자. 스위치를 누른 횟수가 매우 크다면 -1을 출력하기로 하면 될것이다.

코드를 짠다면 대략
```c++
int areAligned() {
    스위치를 누름
    재귀
    스위치를 누르지 않은상태로 돌림
}
```
이런 형태가 될것이다.


### 4. 기저 사례
**2번**에서 우리가 조각을 정한것 처럼 이 문제는 **depth가 10**으로 정해져 10번을 돌리면 되겠다.

문제 풀이
---
```c++
{% raw %}
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

#define INF 9999
#define SWITCH 10
#define CLOCK 16

//linked[i][j]='x' i번 스위치와 j번 시계가 연결되어 있다
//linked[i][j]='.' i번 스위치와 j번 시계가 연결되어 있지 않다
//"................"은 {'.', '.', '.', '.', '.', '.', '.', '.', '.', '.', '.', '.', '.', '.', '.', '.', '\0'}과 같으므로
//1을 더해주지 않으면 "initializer-string for char array is too long" 에러를 낸다.  
const char linked[SWITCH][CLOCK + 1] = {
	"xxx.............",
	"...x...x.x.x....",
	"....x.....x...xx",
	"x...xxxx........",
	"......xxx.x.x...",
	"x.x...........xx",
	"...x..........xx",
	"....xx.x......xx",
	".xxxxx..........",
	"...xxx...x...x.."
};
//모든 시계가 12시를 가리키고 있는지 확인
bool areAligned(const vector<int> & clocks) {
	for (int i = 0; i < CLOCK; i++)
		if (clocks[i] != 12)
			return false;
	return true;
}
//switch번 스위치를 누른다
void push(vector<int> &clocks, int swtch) {
	for (int clock = 0; clock < CLOCK; clock++)
		if (linked[swtch][clock] == 'x') {
			clocks[clock] += 3;
			if (clocks[clock] == 15) //3, 6, 9, 12까지 이므로
				clocks[clock] = 3;
		}
}
//clocks:현재 시계들의 상태
//swtch:이번에 누를 스위치의 번호가 주어질 때, 남은 스위치들을 눌러서 clocks를 12시로 맞출 수 있는 최소 횟수를 반환
//만약 불가능하다면 INF 이상의 큰 수를 반환
int solve(vector<int> &clocks, int swtch) {
	//depth는 10
	if (swtch == SWITCH) return areAligned(clocks) ? 0 : INF;
	//이 스위치를 0번 누르는 경우부터 세번 누르는 경우까지를 모두 시도(12시면 0번, 3시면 3번)
	int result = INF;
	for (int cnt = 0; cnt < 4; cnt++) {
		result = min(result, cnt + solve(clocks, swtch + 1));
		push(clocks, swtch);
	}
	//push(clocks, swtch)가 네번 호출되었으니 clocks는 원래와 같은 상태
	return result;
}



int main(void) {
	int cases;
	vector<int> clocks(CLOCK);

	cin >> cases;
	for (int i = 0; i < cases; i++) {
		//각 시계가 가리키고 있는 시간 입력
		for (int j = 0; j < CLOCK; j++)
			cin >> clocks[j];
		int result = solve(clocks, 0);
		//불가능 할 경우 -1출력
		if (result == INF)
			cout << -1 << endl;
		else
			cout << result << endl;
	}
	return 0;
}
{% endraw %}
```

회고
---
1. linked배열을 생성할때 왜 CLOCK + 1의 크기를 지정하는지 이해가 안되서 찾아보니 "................"은 {'.', '.', '.', '.', '.', '.', '.', '.', '.', '.', '.', '.', '.', '.', '.', '.', '\0'}과 같으므로 1을 더해주지 않으면 "initializer-string for char array is too long" 에러를 낸다는 사실을 알았다.  
2. 나의 풀이는 수행시간이 740ms나 걸렸지만 알고스팟에 어떤분은 수행시간이 0ms밖에 되지 않았다!! 빨리 다른알고리즘을 배워 레벨업하고싶다.  

------
*위 내용의 모든 저작권은 **프로그래밍 대회에서 배우는 알고리즘 문제해결전략**의 저자 구종만님께 있습니다.