---
layout: single
title: "[C++] getline()과 stringstream을 이용해서 string을 split하기 "
categories: BOJ
tags: string cpp c++ 문자열 getline() sstream split
---

어떤 문제를 풀다가 문자열 처리에 익숙하지 않아서 막혔다…

여러 줄의 입력이 들어오는데, 어떤 경우에는 ‘ ‘(공백)으로 분리된 입력이 3개 들어오고,

또 어떤 경우에는 ‘ ‘로 분리된 입력이 1개 들어오고 이런 식인 문제가 있었는데,

이런 입력을 어떻게 처리해줘야 할지 모르겠어서 헤맸다..

구글링해서 getline()으로 한 줄을 string으로 입력받고, sstream을 이용해서 split해서 vector에 저장하는 방식을 찾았다.

이제 헤매지 않으려고 정리하는 글

```cpp
#include <iostream>
#include <sstream>
#include <vector>

using namespace std;

int main() {
    string line;
    vector<string> v;
    char delimiter = ' ';
    
    getline(cin, line);
    
    stringstream f(line);
    string tmp;
    while(getline(f, tmp, delimiter)) {
        v.push_back(tmp);
    }

    for(string s : v) cout << s << '\n';

    return 0;
}
```

이 코드를 실행하면

![0724_1](/assets/images/0724_1.png)

이렇게 된다

오늘은 이렇게만 쓰고, 나중에 stringstream에 대해 함 공부해봐야겠다 (언제..?)