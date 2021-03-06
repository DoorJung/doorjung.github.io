---
title:  "많이 등장하는 완전 탐색 유형"
search: true
categories: 
  - algorithm
last_modified_at: 2019-06-28T10:00:00-12:00:00
---
많이 등장하는 완전 탐색 유형
=============

모든 순열 만들기
---
고등학교 수학시간에 배운 **순열**! 그 친구 맞다.

**순열**: 주어진 N개의 원소를 일렬로 줄 세운 것

순열은 순열자체의 문제도 많은 편이고 다른 문제의 부분 문제로도 나타나기 때문에 코드를 한 번 신경써서 작성해 보면 좋다고 한다.  
단, 가능한 순열의 수는 **N!**이 되는데 **N이 10을 넘어가면** 시간 안에 모든 순열을 생성하기 어려우므로 완전 탐색 말고 다른 방법을 생각해야 한다고 되어있다.  
C++사용자는 STL에 포함된 **next_permutation()**함수에서 모든 순열을 순서대로 생성할수 있다고 한다! 나중에 실제로 사용해서 문제를 풀어봐야겠다.

모든 조합 만들기
---
고등학교 수학시간에 배운 **조합**! 그 친구 맞다.

**조합**: 서로 다른 N개의 원소 중에서 R개를 순서 없이 골라낸 것

앞에서 본 코드 6.2가 이를 재귀 호출로 구현한 좋은 예라고 한다!

### 코드 6.2 n개의 원소 중 m개를 고르는 모든 조합을 찾는 알고리즘
```c++
{% raw %}
//n: 전체 원소의 수
//picked: 지금까지 고른 원소들의 번호
//toPick: 더 고를 원소의 수
//일 때, 앞으로 toPick개의 원소를 고르는 모든 방법을 출력한다.
void pick(int n, vector<int>& picked, int toPick) {
	if (toPick == 0) {
		for (int nIndex = 0; nIndex < picked.size(); ++nIndex) {
			cout << picked[nIndex] << " ";
		}

		cout << endl;
		return;
	}
	// 고를 수 있는 가장 작은 번호를 계산한다.
	int smallest = picked.empty() ? 0 : picked.back() + 1;
	// 이 단계에서 원소 하나를 고른다.
	for (int next = smallest; next < n; ++next) {
		picked.push_back(next);
		pick(n, picked, toPick - 1);
		picked.pop_back();
	}
}
{% endraw %}
```

2^n가지 경우의 수 만들기
---
n개의 질문에 대한 이지선다의 답을 할 수 있다고 할 때 존재할수있는 답의 모든 조합의 수는 2^n가지이다.   
16장에서 배우는 기술을 사용하면 각 조합을 하나의 n비트 정수로 표현한다고 생각하면 재귀 호출을 사용할 것 없이 1차원 for문 하나로 이 조합들을 간단하게 모두 시도할 수 있다고 한다!

회고
---
1. 오랜만의 포스팅이다! 회사일에 개인프로젝트에 동아리에 스터디에 할게 너무 많아서 알고리즘을 약간 소홀히해 진도가 안나갔었다ㅠㅠ 다음엔 바쁘더라고 문제 하나정도는 풀어서 감을 잃지 말아야겠다.  
2. 완전 탐색이 끝났다! 다음 포스팅에 바로 분할정복으로 넘어가진 않고 백준 문제를 풀어보면서 감을 익힐 것이다. 괜찮은 문제가 나오면 포스팅할지도...?  

------------
*위 내용의 모든 저작권은 **프로그래밍 대회에서 배우는 알고리즘 문제해결전략**의 저자 구종만님께 있습니다.