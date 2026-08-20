#구현
https://ps-arena.vercel.app/c/mrpt7kvk9b8znn/p/A
### 문제
![[Pasted image 20260722020302.png]]
![[Pasted image 20260722020307.png]]

### 구상
이 문제는 구상이랄께 없었다 처음에 보고 가방 꺼내기 dp나 브루트포스인 줄 알고 당황했지만
태그가 달려있어 빠르게 풀 수 있었다.

```cpp
#include <iostream>
using namespace std;

int main(){
    int a, b;
    cin>> a >> b;

    int ans1 = b%a;
    int ans2 = b/a; 
    cout<< ans2 << " "<<ans1;

    return 0;
}
```
