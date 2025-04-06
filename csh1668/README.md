# 쿼리와 쿼리

- 원본 블로그 글: https://velog.io/@tjgus1668/%EB%B0%B1%EC%A4%80-18254-%EC%BF%BC%EB%A6%AC%EC%99%80-%EC%BF%BC%EB%A6%AC

## 문제
https://www.acmicpc.net/problem/18254

## 사용 알고리즘
팬윅 트리 (Fenwick Tree, Binary Indexed Tree)
스위핑 (Sweeping)
누적합 (Prefix Sum)
제곱근 분할법 (Sqrt Decomposition)

## 풀이

### 문제 풀 때 키포인트
XOR 연산의 성질을 잘 이용해야 합니다. 특히 아래 성질이 이 문제에서 유용하게 쓰입니다.

- $x \oplus x=0$

자기 자신을 XOR하면 0이 되고 이는 즉 연산과 역연산이 같다는 것을 의미합니다.
매우매우 중요합니다!

---
### 업데이트 연산 처리 방법
우선 M개의 업데이트 연산을 먼저 보면 구간 XOR입니다. Segment Tree with Lazy Propagation으로 쉽게 해결할 수 있습니다.
https://www.acmicpc.net/problem/12844
이 문제를 푸는 걸 추천합니다.

---

### 쿼리 연산 처리 방법
1번 쿼리의 나이브 풀이를 먼저 생각해보면, $L\le i\le R$를 만족하는 모든 $i$에 대해서 $l_i\,r_i\,v$인 업데이트 연산을 추가로 수행한다고 생각할 수 있습니다. 1번 쿼리 하나의 시간복잡도는 $O(M\log N)$이고 총 $O(QM\log N)$이므로 시간 내에 불가능합니다.

업데이트 연산을 잘 묶으면 시간복잡도를 줄일 수 있을 것 같은데, $l_i\,r_i\,v$에서 $v$는 모두 같다는 점에서 착안합니다. 업데이트 연산(구체적으로는 $(l_i, r_i)$ 정보)를 $M/B$ 크기를 가지는 버킷 $B$개로 쪼갭니다.

어떤 1번 쿼리가 다음에 오는 2번 쿼리에 영향을 준다는 것은 해당 1번 쿼리로 인해 수행되는 업데이트 연산들이, 주어지는 구간 $[s, e]$에서 _덮는_(영향을 주는) 수의 개수가 홀수 개라는 것을 의미합니다.

몇 번인지는 별로 중요하지 않습니다. $x \oplus x=0$이기 때문입니다.

> $f(i, l, r)$: i번째 버킷에 속하는 $N/B$개의 업데이트 연산들이 구간 $[l, r]$에서 홀수 번 _덮는가_?

스위핑과 누적합으로 $O(BN)$에 전처리하면 위 함수를 $O(1)$에 동작하도록 만들 수 있습니다.

---

버킷을 처리할 수 있게 되었으니 이제 1번 쿼리와 2번 쿼리를 어떻게 해결할지 생각해봅시다.

- 1번 쿼리
  - 버킷 범위에 포함되면 해당 버킷의 값 $X$에 $X=X\oplus v$를 적용하면 됩니다. $O(1)$입니다.
  - 버킷 범위에 포함되지 않으면 $l_i\,r_i\,v$인 업데이트 연산을 추가로 적용하면 됩니다. $O(\log N)$입니다.
  - 최대 $B$개의 버킷을 갱신해야 하고, 최대 $2N/B$개의 업데이트 연산이 필요하므로 시간 복잡도는 $O(B+M/B\log N)$입니다.
- 2번 쿼리
  - 업데이트 연산이 적용된 값 $ans$를 구합니다. $O(\log N)$입니다.
  - 모든 버킷을 보며 추가로 $ans=ans\oplus X_i$를 적용합니다.
  - 따라서 시간 복잡도는 $O(\log N + B$)입니다.
  
따라서 총 시간 복잡도는 $O(M\log N +Q(B+M/B\log N))$이 되고 B를 적당히 150~200 쯤으로 잡으면 통과할 수 있을 것 같지만...

---

### 상수 줄이기 (펜윅 트리로 구간 XOR 구현하기)
그렇게 호락호락하게 풀리진 않았습니다. 바로 업데이트 연산의 Segment Tree with Lazy Propagation의 상수가 크기 때문입니다. 상수를 줄일 방법을 찾아야 합니다.

이리저리 방법을 생각해보다가 결국 떠오르지 않아서 구글링을 했습니다.
https://codeforces.com/blog/entry/104117?#comment-925125
여기 글의 한 댓글에서 힌트를 얻을 수 있었습니다.

모든 업데이트 쿼리가 $[1, p]$로 들어오고 모든 출력 쿼리가 $[q, N]$로 들어온다면 $x \oplus x=0$인 성질에 의해 $p$가 짝수인 업데이트 쿼리는 $q$가 짝수이고 $p \le q$인 출력 쿼리에만, 홀수는 홀수에만 영향을 준다는 특징을 이용하여 구간 업데이트 쿼리를 점 업데이트 쿼리로 바꿀 수 있습니다.

또 마찬가지로 $x \oplus x=0$이기 때문에 두 쿼리의 형태가 $[1, p]$, $[q, N]$인 제약을 해소시킬 수 있습니다.

따라서 두 개의 팬윅 트리를 만들고, 하나는 짝수 인덱스로 들어오는 업데이트/출력 쿼리를, 또 다른 하나는 홀수 인덱스로 들어오는 업데이트/출력 쿼리를 누적합의 형태로 관리하면 구간 XOR을 처리할 수 있습니다.

아래는 위 댓글의 아이디어를 바탕으로 12844를 해결한 코드입니다.
```cpp
#include <bits/stdc++.h>

using namespace std;
constexpr int MAXN = 500'005;

struct Fenwick {
    int tree[MAXN];
    void update(int i, int v) { for (; i < MAXN; i += i & -i) tree[i] ^= v; }
    int sum(int i) { int ret = 0; for (; i > 0; i -= i & -i) ret ^= tree[i]; return ret; }
    int query(int l, int r) { return sum(r) ^ sum(l - 1); }
};

struct LazyXOR {
    Fenwick O, E;
    // [p, N]
    inline void update(int p, int v) {
        if (p & 1) O.update(p, v);
        else E.update(p, v); 
    }
    // [1, q]
    inline int query(int q) {
        if (q & 1) return O.sum(q);
        else return E.sum(q);
    }
    void update(int l, int r, int v) {
        update(l, v);
        update(r + 1, v);
    }
    int query(int l, int r) {
        return query(r) ^ query(l - 1);
    }

} ft;

int main() {
    ios_base::sync_with_stdio(0); cin.tie(0); cout.tie(0);

    int N, M; cin >> N;
    for (int i = 1; i <= N; ++i) {
        int x; cin >> x;
        ft.update(i, i, x);
    }

    cin >> M;
    while (M--) {
        int op, i, j; cin >> op >> i >> j; ++i, ++j;
        if (op == 1) {
            int k; cin >> k;
            ft.update(i, j, k);
        } else {
            cout << ft.query(i, j) << '\n';
        }
    }

    return 0;
}
```
3등에 랭크될 정도로 다른 레이지세그(600~1000ms)보다 훨씬 빠르게 동작합니다. 1등 코드 역시 위 아이디어 + FastIO였습니다.


아무튼 기존의 레이지 세그를 위 팬윅 트리 버전으로 개선하면 AC를 받습니다.


난이도가 루비인 만큼 풀이 코드는 공유하지 않겠습니다.