https://dojoi.xyz/ko/problems/153?contest=cmpsite1204b2jrhhbs7o1y92

```cpp.title
#include <iostream>
using namespace std;

int main() {
    int n;
    string s;
    int result =0;
    cin>>n>>s;
    for(int i=0;i<s.length();i++){
        if(s[i]=='6'||s[i]=='7') result++;
    }

    cout<<result<<endl;
    return 0;
}
```