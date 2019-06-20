---
title:  "최적화 문제"
search: true
categories: 
  - algorithm
last_modified_at: 2019-06-20T10:00:00-12:00:00
---
최적화 문제
===

최적화 문제
---
지금까지 우리가 다뤘던 [소풍](https://doorjung.github.io/algorithm/post-algorithm-study-2/)과 [게임판 덮기](https://doorjung.github.io/algorithm/post-algorithm-study-3/)와 같은 문제는 방법의 수를 구하는 문제였다.  
만약 'n개의 사과 중에서 r개를 골라 무게의 합을 최대화 하는 방법을 찾아라' 같은 문제가 주어진다면 어떻게할까?  
이러한 문제들을 통칭해 **최적화 문제**라고 한다.

**최적화 문제**: **특정 기준**에 따라 **가장 좋은** 답을 찾아 내는 문제

**최적화 문제**를 해결하기 위한 방법은 여러가지이다.  
이 장에서 우리는 앞서 배운 **완전 탐색**을 이용해 문제에 접근해 보도록하자  
단순히 **가능한 답을 모두 생성**해 보고 그중 **가장 좋은 것**을 찾아내면되기 때문이다!

예제: 여행하는 외판원 문제
===

문제
---
알고리즘 문제해결전략 6.7장의 예제인 여행하는 외판원 문제입니다.  
[문제](https://algospot.com/judge/problem/read/TSP1)  
문제에 대한 자세한 설명은 위 링크에 있습니다.

책의 예제와 링크의 문제는 살짝 다른 점이 있는데  
첫 번째로는  
책은 모든 도시를 방문하고 다시 시작 도시로 돌아와 종료하지만 링크에서는 **모든 도시를 방문하고 바로 종료**를 한다.  
두 번째로는  
도시의 수 n의 제한이 책은 2<=n<=10인 반면 링크에는 **3<=n<=8** 으로 되어있다.

알고스팟에서 실제 체점이 가능한 [링크](https://algospot.com/judge/problem/read/TSP1)의 문제로 풀이를 할것이니 이점을 유의하시길 바랍니다!


문제접근
---
완전탐색 레시피를 적용시켜 문제를 접근해봅시다!

### 1. 시간안에 풀 수 있을까?
도시의 개수 n이 8일 때,  
첫 번쨰 도시는 결정되어 있지만, 나머지 7개 도시는 임의의 순서대로 선택될 수 있으므로 7개의 도시를 나열하는 방법인 순열을 사용해 계산하면 가능한 모든 경우의 수는 **7! = 5070**이다.  
**컴퓨터가 1초에 약 1억번을 계산**하니 문제의 시간 제한인 1초만에 충분히 풀 수 있는 문제다!


### 2. 답을 만드는 과정을 여러 개의 조각으로 나누자
n개의 도시로 구성된 경로를 **n개의 조각**으로 나눠, 앞에서부터 **도시를 하나씩 추가**해 경로를 만들어가자.

경로를 하나씩 추가해 가는 과정을 재귀호출을 이용하면 문제를 풀 수 있겠다.


###  3. 답의 일부를 만들고, 나머지 답을 재귀호출을 통해 완성
지금까지 만든 경로를 저장할 값, 어떤 도시를 방문했는지를 나타내는 값이 필요할 것이다.
또한 가장 짧은 경로를 구하기 위해 그 과정을 처리할 함수가 필요할 것이다.

코드를 짠다면 대략
```c++
{% raw %}
double shortestPath() {
    기저 사례
    for(방문할 도시; 방문할 도시 < 도시의 개수; ++방문할 도시) {
        방문한 도시라면 다음 도시로 넘어감, 안했다면 이하 코드 실행
        경로에 추가, 방문한 도시에 추가
        재귀
        경로에 후보들중 가장 짧은지 확인
        경로 해제, 방문한 도시에 해제
    }
}
{% raw %}
```
이런 형태가 될것이다.


### 4. 기저 사례
기저 사례는 **모든 도시를 다 방문했을 때**이다.

문제 풀이
---
```c++
{% raw %}
#include <iostream>
#include <algorithm>
#include <vector>

using namespace std;
#define MAX 8

int n; //도시의 수
double dist[MAX][MAX]; //두 도시간의 거리를 저장하는 배열

//path:지금까지 만든 경로
//visited: 각 도시의 방문 여부
//currentLength: 지금까지 만든 경로의 길이
//나머지 도시들을 모두 방문하는 경로들 중 가장 짧은 것의 길이를 반환
double shortestPath(vector<int> &path, vector<bool> &visited, double currentLength) {
	//기저 사례:모든 도시를 다 방문했을 때는 종료한다
	if (path.size() == n)
		return currentLength;

	double ret = numeric_limits<double>::max(); //매우 큰 값으로 초기화
	//다음 방문할 도시를 전부 시도
	for (int next = 0; next < n; next++) {
		if (visited[next]) continue;
		int here = path.back();
		path.push_back(next);
		visited[next] = true;
		//나머지 경로를 재귀 호출을 통해 완성하고 가장 짧은 경로의 길이를 얻는다
		double cand = shortestPath(path, visited, currentLength + dist[here][next]);
		ret = min(ret, cand);
		visited[next] = false;
		path.pop_back();
	}
	return ret;
}

int main(void) {
	int cases;
	double ret;

	cin >> cases;

	while (cases--)	{
		cin >> n;

		for (int j = 0; j < n; j++) {
			for (int k = 0; k < n; k++) {
				cin >> dist[j][k];
			}
		}
		
		double answer = numeric_limits<double>::max();

		for (int j = 0; j < n; j++) {
			vector<int> path(1, j);
			vector<bool> visited(n, false);
			visited[j] = true;
			ret = shortestPath(path, visited, 0);
			answer = min(answer, shortestPath(path, visited, 0));
		}
		printf("%.10f\n", answer);
	}
	return 0;
}
{% endraw %}
```

회고
---
1. visited의 생각을 하는데 시간이 오래 걸렸다. 방문 여부를 체크안해주면 똑같은 곳을 가는 경우가 생기기때문에 필요하다!  
2. 알고리즘 공부에 걸리는 시간이 생각보다 길다. 한 개념을 이해하고 그것을 바탕으로 문제를 푸는것 만으로 어마어마한 시간이 걸린다.  
앞으론 1일 1포스팅을 목표로 하는것 보다는 시간 배분을 잘하여 때에 따라 결정해야겠다. 사실 다른 컨텐츠도 업로드하고 싶기도 해서...ㅎ  



------------
*위 내용의 모든 저작권은 **프로그래밍 대회에서 배우는 알고리즘 문제해결전략**의 저자 구종만님께 있습니다.