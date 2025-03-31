## [문제]
<hr/>

[https://www.acmicpc.net/problem/1345](https://boj.kr/1345)

## [풀이]
<hr/>

![](https://velog.velcdn.com/images/awj1052/post/d33746a9-3ede-4df3-aac9-e93ac5305be4/image.png)

수치해석을 사용했다.
증분탐색으로 $n^2$개의 구간으로 쪼갰다.
처음엔 51개의 구간으로 쪼갰는데, 시간이 여유롭고 일반화를 위해 $n$에 대한 식으로 결정했다.
$S$를 만족시키는 $d$의 판별은 단조성을 보장하지 않는다고 생각했다.
최소값을 위해 false에서 true로 바뀌는 구간을 주목하여 lower와 upper를 설정했다.
그 다음엔 이분법을 사용하여 근사오차 $1e-10$까지 수행했다.


## [코드]
<hr/>

```python
import sys, math
r=sys.stdin.readline

n,a0=map(int,r().split())
if n == 0:
    print(0)
    exit()
s=list(map(int,r().split()))
s = [a0] + s
c = []
for i in range(n):
    c.append(s[i+1]-s[i])
cs = set(c)
if len(cs) > 2 or min(c) < 0:
    print(-1)
    exit()
if len(cs) == 1:
    print(c[0])
    exit()
if abs(c[0]-c[1]) > 1:
    print(-1)
    exit()
l=min(c)
def b(k):
    x=s[0]
    for i in range(1,n+1):
        x+=k
        if math.floor(x)!=s[i]: return 0
    return 1
j=0
q=n**2
for i in range(1,q+1):
    if b(l+i/q):
        j=i
        break
if j==0:
    print(-1)
    exit()
u=l+j/q
l=l+(j-1)/q
m=(l+u)/2
while 1:
    if b(m): u=m
    else: l=m
    lm=(l+u)/2
    if abs(lm-m)<=1e-10: break
    m=lm
print(m)
```

> 2 0
1 1

이런 반례때문에 35%에서 고생했다..
assert(j != 0) 걸어서 RE로 확신했다.
