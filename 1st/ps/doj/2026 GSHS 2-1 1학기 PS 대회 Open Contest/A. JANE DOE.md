https://doj.kr/ko/problems/292

```cpp
#include <iostream>
using namespace std;

int main(){
    int n;
    cin>>n;
    int count = n/2 ; 

    if(n%2==0){
        cout << count <<endl;
        for(int i=0;i<n/2;i++){
            cout << "2" << " ";
        }
    }
    else{
        cout << count <<endl;
        for(int i=0;i<n/2-1;i++){
            cout<< "2" << " ";
        }

        cout << "3" ;
    }


    return 0;
}
```
