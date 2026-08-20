회문수
12의 배수
인데 

메모리가 생각보다 많은거 같기도

12의 배수를 다빼고 남은게 회문수인지 찾아도 될꺼같은데
너무 많네...

그래도 생각나는건  음....
아니 근데 어차피 미지수 n개라 너무 많은데

? 개어렵네 그냥 break 중간에 걸어야할듯 큰수는 어차피 안나오는건 안나오누

```
#include <iostream>

#include <cmath>

  

using namespace std;

  

int haw(long long int n){

string n1 = to_string(n);

for(int i=0;i<ceil((double)(n1.length())/2);i++){

if(n1[i] == n1[n1.length()-1-i]);

else{

return 0;

}

}

return 1;

}

  

int main(){

ios_base::sync_with_stdio(false);

cin.tie(NULL);

  

int t;

cin>>t;

while(t--){

long long int a;

cin>>a;

  

int result=0;

for(long long int i=0;i<=a;i+=12){

if(haw(a-i)){

cout<< a-i <<" "<<i<<"\n";

result++;

break;

}

if (i > 40000) break;

}

if(result==0) cout<<"-1\n";

  

}

return 0;

}
```

틀린 코드

뭔가 휘문수 기반으로 탐색해야하는듯