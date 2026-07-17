https://dojoi.xyz/ko/problems/154?contest=cmpsite1204b2jrhhbs7o1y92

```cpp.title
#include <iostream>
using namespace std;

int main(){
    int n;
    string s;
    cin >>n>> s;

    int c6=0,c7=0;

    for(int i=0;i<s.size();i++){
        if(s[i]=='6') c6++;
        else if(s[i]=='7') c7++;
    }

    if(c6>=c7) for(int i=0;i<c6;i++) cout << 6;
    else for(int i=0;i<c7;i++) cout << 7;

    return 0;
}
```