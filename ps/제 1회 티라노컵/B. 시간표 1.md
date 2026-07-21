#구현
https://ps-arena.vercel.app/c/mrpt7kvk9b8znn/p/B
![[Pasted image 20260722022035.png]]![[Pasted image 20260722022043.png]]![[Pasted image 20260722022054.png]]

지문 길이 보고 바로 거른 문제인데 
이걸 풀어야 1등이 되는 걸 확인하고 이악물고 풀었음 
아이디어는 별거 없고 그냥 단순히 입력이 각 줄별로 묶음이고 이게 각 행이 요일이기에
기존 표에 제시된걸 행열을 트랜스포트했다 생각하고 풀이함

사실 풀이보다 이런 문제를 원트클 했다는게 놀라움

```cpp


#include <iostream>
using namespace std;

int main(){
    int n;
    cin>>n;

    int a[36];
    int b[36];

    for(int i=1;i<=n;i++){
        cin>>a[i];
    }
    for(int i=1;i<=n;i++){
        cin>>b[i];
    }

    int s[6][8];
    int wa[6][8];
    int wb[6][8];

    for(int i=1;i<=5;i++){
        for(int j=1;j<=7;j++){
            cin>>s[i][j];
        }
    }

    for(int i=1;i<=5;i++){
        for(int j=1;j<=7;j++){
            cin>>wa[i][j];
        }
    }

    for(int i=1;i<=5;i++){
        for(int j=1;j<=7;j++){
            cin>>wb[i][j];
        }
    }

    int result = 0;

    for(int i=1;i<=5;i++){
        for(int j=1;j<=7;j++){
            int ss = s[i][j];
            result += wa[i][j]*a[ss]+wb[i][j]*b[ss]; 
        }
    }

    cout<<result;


    return 0;
}
```