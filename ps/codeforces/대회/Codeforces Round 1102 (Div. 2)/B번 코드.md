```
#include <iostream>

using namespace std;

void solving() {
    long long n;
    cin >> n;
    
    if (n == 10){
        cout << -1 << "\n";
    } else {
        int namu = n % 12;
        long long a = 0;
        
        if(namu <= 9){
            a = namu;
        }else if (namu == 10){
            a = 22;
        }else if (namu == 11){
            a = 11;
        }
        
        long long b = n - a;
        cout << a << " " << b << "\n";
    }
}

int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(NULL);
    
    int t;
    cin >> t;
    while (t--){
        solving();
    }
    
    return 0;
}
```

회문수를 먼저 찾으면됨
모듈러로