```cpp title:A
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

int modul(int x,int y){
    int namu = x%y;
    return namu;
}




int main(){
    ios_base::sync_with_stdio(false);
    cin.tie(NULL);

    int t1;
    int t2;
    int result;
    
    cin>>t1;
    while(t1--)
    {
        cin>>t2;
        vector<int> v;  
        while(t2--)
        {
            int a;
            cin>>a;
            v.push_back(a);
        }
        sort(v.begin(), v.end(), greater<int>());

        result = 0;
        if(v.size()>2){
            for(int i=0;i<v.size()-2;i++)
            {
                if(v[i+2]==modul(v[i],v[i+1]));
                else{ 
                    result++;
                    break;
                }
            }
        }
        if(result == 0) cout<< v[0] << " " << v[1] << "\n";
        else cout<<"-1\n";
    }

   return 0;
}
```