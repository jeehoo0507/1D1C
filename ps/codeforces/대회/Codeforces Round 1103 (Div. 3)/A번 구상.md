이지이지
max h + 1 - min h

그냥

#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

int main(){
    ios_base::sync_with_stdio(false);
    cin.tie(NULL);

    int t1,t2;
    cin>>t1;

    int n;
    vector<int> v;
    while(t1--){
        cin>>t2;

        vector<int> v; 
        while(t2--){
            cin>>n;
            v.push_back(n);

        }
        sort(v.begin(), v.end());   

        cout<<v[v.size()-1] - v[0] +1<<"\n";
    }



    return 0;
}