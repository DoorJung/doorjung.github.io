---
title:  "문제: 소풍"
search: false
categories: 
  - algorithm
last_modified_at: 2019-06-18T10:00:00-12:00:00
---
소풍
===

문제
---
알고리즘 문제해결전략 6.3~6.4장의 소풍 문제입니다.  
[문제](https://algospot.com/judge/problem/read/PICNIC)  
문제에 대한 자세한 설명은 위 링크에 있습니다.  

문제접근
---
이전 시간에 배웠던 완전탐색 레시피를 적용시켜 문제를 접근해보자

### 1. 시간안에 풀 수 있을까?
모든 답을 생성해 가며 답의 수를 세는 **재귀 호출 알고리즘은 답의 수에 정비례하는 시간이 걸린다**.

고로 **가장 많은 답을 가질 수 있는 경우**를 생각해보자!  
n=10이고, 10명의 학생이 모두 친구인 경우 가장 많은 답을 가진다.  
중복을 제거하여 답을 구하면 답의 개수는 9x7x5x3x1=945  
**컴퓨터가 1초에 약 1억번을 계산**하니 넉넉하지 않을까?...


### 2. 답을 만드는 과정을 여러 개의 조각으로 나누자
두 학생을 짝지어 주는걸 생각해 문제를 **n/2**개의 조각으로 나누어 **한 조각마다 두 학생을 짝지어주자**

서로 친구인 두 학생을 찾아 짝지어 주고나면 남은 학생들을 짝지어 주는 문제도 원래 문제와 같은 형태가 된다.  
이때 문제의 형태는 '아직 짝을 찾지 못한 학생들이 주어질 때 친구끼리 둘씩 짝짓는 경우의 수를 계산하라'가 된다.


###  3. 답의 일부를 만들고, 나머지 답을 재귀호출을 통해 완성
짝의 유무를 판단할 변수, 친한 친구임을 알려줄 변수가 필요할 것이다.  
중복의 제거를 위해 짝을 찾지못한 학생들 중 가장 번호가 빠른 학생을 찾아 순서를 강제하는 과정도 필요할것이다.

코드를 짠다면 대략
```c++
int countPairings() {
    for(짝지을 친구 = 짝못찾은 친구 + 1; 짝지을 친구 < n; ++짝지을 친구) {
        if(짝지을 친구가 아직 짝지어지지 않음 && 둘이 친함) {
            둘이 짝 지어줌
            재귀
            둘이 짝 지어준걸 해제
        }
    }
}
```
이런 형태가 될것이다.


### 4. 기저 사례
아직 짝을 찾지 못한 학생이 없을때, 즉 **모든 학생이 짝을 찾았을때**이다.

문제 풀이
---
```c++
#include<iostream>
using namespace std;

int n, m;
bool areFriends[10][10] = {};

//taken[i] = i번째 학생이 짝을 이미 찾았으면 true, 아니면 false
int countPairings(bool taken[10]) {
	//남은 학생들 중 가장 번호가 빠른 학생을 찾는다.
	int firstFree = -1;
	for (int i = 0; i < n; ++i) {
		if (!taken[i]) {
			firstFree = i;
			break;
		}
	}
	//기저 사례: 모든 학생이 짝을 찾았으면 한 가지 방법을 찾았으니 종료한다.
	if (firstFree == -1) return 1;
	int ret = 0;
	//이 학생과 짝지을 학생을 결정한다.
	for (int pairWith = firstFree + 1; pairWith < n; ++pairWith) {
		if (!taken[pairWith] && areFriends[firstFree][pairWith]) {
			taken[firstFree] = taken[pairWith] = true;
			ret += countPairings(taken);
			taken[firstFree] = taken[pairWith] = false;
		}
	}
	return ret;
}

int main() {
	int cases, s1, s2;
	cin >> cases;
	while (cases--) {
		cin >> n >> m;
		while (m--) {
			cin >> s1 >> s2;
			areFriends[s1][s2] = true;
		}
		bool taken[10] = {};

		cout << countPairings(taken) << endl;

		memset(areFriends, false, sizeof(areFriends));
	}
}
```

회고
---
1. 친한 친구를 판단할 방법을 처음엔 Map과 Array를 이용해 할려했는데 하다보니 점점 복잡해진다!!! 책을 보니 이차원 배열 bool areFriends로 깔끔하게 처리했다... 문제를 풀기에 급급하지 말고 단순하게 생각하는 버릇을 들여야겠다...  
2. 주 업무가 안드로이드 개발이다 보니 맨날 kotlin만 써서 C++이 가물가물하다... 간간히 C++도 공부해야겠다.  
3. 재귀함수 어렵다! 문제에 적용하는건 괜찮은데 이게 이해를 하고 쓴다기 보다는 형태를 알고 쓰는것 같다... 더 많이 풀어봐야될 듯



------------
*위 내용의 모든 저작권은 **프로그래밍 대회에서 배우는 알고리즘 문제해결전략**의 저자 구종만님께 있습니다.