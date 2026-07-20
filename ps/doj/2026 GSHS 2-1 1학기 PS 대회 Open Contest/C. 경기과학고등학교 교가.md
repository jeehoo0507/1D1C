https://doj.kr/ko/problems/290

```cpp
#include <iostream>
#include <cmath>
#include <iomanip>
using namespace std;


double distance(int x1, int x2, int y1, int y2)
{
    double x = x1 - x2;
    double y = y1 - y2;
    double ud = sqrt(x*x + y*y); 
    
    return ud;
}

int main(){
    int n, m;
    cin >> n >> m;

    int sanx[2001], sany[2001];
    int badax[2001], baday[2001];

    double mind = 1e18;

    for(int i=1;i<=n;i++)
    {   
        cin >> sanx[i] >> sany[i];
    }

    for(int  j = 1;j<=m;j++){
        cin >> badax[j] >> baday[j];

        for(int i=1;i<=n;i++)
        {

            double nmind = distance(sanx[i], badax[j], sany[i], baday[j]);
            mind = nmind<mind ? nmind : mind;
        }
    }


    cout<<fixed<<setprecision(10)<<mind;

    return 0;
}
```
