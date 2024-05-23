#include <iostream>
#include <string>
#include <stack>
#include <queue>
#include<cctype>

using namespace std;

int prior(char c) {
    switch (c) {
    case '(': return 1;
    case '+': case '-': return 2;
    case '*': case '/': return 3;
    default: return 0;
    }
}

char* biggerMassive(char* Arr, int i) {
    char* Arr2 = new char[i + 1];
    for (int j = 0; j < i; j++) {
        Arr2[j] = Arr[j];
    }
    return Arr2;
}

int isItNumberorWorld(char a) {
    if (isalpha(a))
        return 1;
    if(isdigit(a))
        return 2;
    return 0;
}

char* proverkaNorm(string vvod, int* Arrlen) {
    char* Arr = new char[0];
    int len = vvod.length();
    int Arri = 0;
    short int early = 0;
    short int probel = 0;
    short int skobka = 0;
    for (int i = 0; i < len; i++) {
        if (isItNumberorWorld(vvod[i])) {
            if ((probel == 1 && early == 1) or early == 3) {
                cout << "Invalid input";
                return 0;
            }
            Arr = biggerMassive(Arr, *Arrlen);
            Arr[*Arrlen] = vvod[i];
            (*Arrlen)++;
            early = 1;
            probel = 0;
        }
        else {
            if (prior(vvod[i]) > 1) {
                if ((prior(vvod[i]) > 2 && (early == 0 or early == 2)) or early == -1) {
                    cout << "Invalid input";
                    return 0;
                }
                Arr = biggerMassive(Arr, *Arrlen);
                Arr[*Arrlen] = vvod[i];
                (*Arrlen)++;
                early = -1;
                probel = 0;
            }
            else {
                if (vvod[i] == '(') {
                    skobka++;
                    if (early == 1) {
                        Arr = biggerMassive(Arr, *Arrlen);
                        Arr[*Arrlen] = '*';
                        (*Arrlen)++;
                    }
                    Arr = biggerMassive(Arr, *Arrlen);
                    Arr[*Arrlen] = vvod[i];
                    (*Arrlen)++;
                    early = 2;
                    probel = 0;
                }
                else {
                    if (vvod[i] == ')') {
                        if ((skobka == 0) or (early == 2)) {
                            cout << "Invalid input";
                            return 0;
                        }
                        skobka--;
                        Arr = biggerMassive(Arr, *Arrlen);
                        Arr[*Arrlen] = vvod[i];
                        (*Arrlen)++;
                        early = 3;
                        probel = 0;
                    }
                    else {
                        if (vvod[i] == ' ') {
                            probel = 1;
                        }
                        else {
                            cout << "Invalid input";
                            return 0;
                        }
                    }
                }
            }
        }
    }
    if ((skobka != 0) or prior(Arr[(*Arrlen) - 1]) > 1) {
        cout << "Invalid input";
        return 0;
    }
    return Arr;
}

stack<char> proverkaPolsh(string vvod) {
    stack<char> Arr;
    int len = vvod.length();
    short int number = 2;
    bool probel = true;
    for (int i = 0; i < len; i++) {
        if (isItNumberorWorld(vvod[i])) {
            if (probel)
                number--;
            else {
                Arr.push('|');
            }
            Arr.push(vvod[i]);
            probel = false;
        }
        else {
            if (prior(vvod[i]) > 1) {
                number++;
                Arr.push(vvod[i]);
                probel = true;
            }
            else {
                if (vvod[i] == ' ') {
                    probel = true;
                    continue;
                }
                else
                {
                    while (!(Arr.empty())) {
                        Arr.pop();
                    }
                    Arr.push('?');
                    return Arr;
                }
            }
        }
        if ((number < 2) && (!((number==1) && (i == len-1)))) {
            while (!(Arr.empty())) {
                Arr.pop();
            }
            Arr.push('?');
            return Arr;
        }
    }
    if (number != 1) {
        while (!(Arr.empty())) {
            Arr.pop();
        }
        Arr.push('?');
        return Arr;
    }
    return Arr;
}

queue<char> proverkaObratPolsh(string vvod) {
    queue<char> Arr;
    int len = vvod.length();
    short int number = 0;
    bool probel = true;
    for (int i = 0; i < len; i++) {
        if (isItNumberorWorld(vvod[i])) {
            if(probel)
                number++;
            else {
                Arr.push('|');
            }
            Arr.push(vvod[i]);
            probel = false;
        }
        else {
            if (prior(vvod[i]) > 1) {
                number--;
                Arr.push(vvod[i]);
                probel = true;
            }
            else {
                if (vvod[i] == ' ')
                    probel = true;
                else {
                    while (!(Arr.empty())) {
                        Arr.pop();
                    }
                    Arr.push('?');
                    return Arr;
                }
            }
        }
        if (number < 1) {
            while (!(Arr.empty())) {
                Arr.pop();
            }
            Arr.push('?');
            return Arr;
        }
    }
    if (number != 1) {
        while (!(Arr.empty())) {
            Arr.pop();
        }
        Arr.push('?');
        return Arr;
    }
    return Arr;
}

queue<char> obratPolsha(string vvod) {
    int len = vvod.length();
    int a = 0;
    int Arrlen = 0;
    int* ptrArrlen = &Arrlen;
    char* Arr = proverkaNorm(vvod, ptrArrlen);
    queue <char> otwet;
    bool probel = true;
    otwet.push('?');
    if (Arr == 0) {
        delete[] Arr;
        return otwet;
    }
    otwet.pop();
    stack <char> numbers;
    stack <char> vivod;

    for (int i = 0; i < Arrlen; i++) {
        if (Arr[i] == '(')
            numbers.push(Arr[i]);
        else {
            if (Arr[i] == ')') {
                while (numbers.top() != '(') {
                    otwet.push(numbers.top());
                    numbers.pop();
                }
                numbers.pop();
            }
            else {
                if (prior(Arr[i]) > 1) {
                    while (numbers.size() && prior(Arr[i]) <= prior(numbers.top())) {
                        otwet.push(numbers.top());
                        numbers.pop();
                    }
                    numbers.push(Arr[i]);
                }
                else {
                    if (i > 0 && isItNumberorWorld(Arr[i - 1])) {
                        otwet.push('|');
                    }
                    otwet.push(Arr[i]);
                }
            }
        }
        cout << "-----------------------------" << endl;
        cout << "Строка:";
        for (int j = 0; j < otwet.size(); j++) {
            if (otwet.front() == '|') {
                probel == false;
                otwet.push(otwet.front());
                otwet.pop();
                continue;
            }
            if (probel)
                cout << " " << otwet.front();
            else {
                cout << otwet.front();
                probel = true;
            }
            otwet.push(otwet.front());
            otwet.pop();
        }
        cout << endl;
        cout << "Стек:";
        a = numbers.size();
        for (int j = 0; j < a; j++) {
            vivod.push(numbers.top());
            numbers.pop();
        }
        for (int j = 0; j < a; j++) {
            cout << " " << vivod.top();
            numbers.push(vivod.top());
            vivod.pop();
        }
        cout << endl;
    }
    while (numbers.size()) {
        otwet.push(numbers.top());
        numbers.pop();
        cout << "-----------------------------" << endl;
        cout << "Строка:";
        for (int j = 0; j < otwet.size(); j++) {
            if (otwet.front() == '|') {
                probel == false;
                otwet.push(otwet.front());
                otwet.pop();
                continue;
            }
            if (probel)
                cout << " " << otwet.front();
            else {
                cout << otwet.front();
                probel = true;
            }
            otwet.push(otwet.front());
            otwet.pop();
        }
        cout << endl;
        cout << "Стек:";
        a = numbers.size();
        for (int j = 0; j < a; j++) {
            vivod.push(numbers.top());
            numbers.pop();
        }
        for (int j = 0; j < a; j++) {
            cout << " " << vivod.top();
            numbers.push(vivod.top());
            vivod.pop();
        }
        cout << endl;
        cout << "-----------------------------" << endl;
    }
    return otwet;
}

int reshitObratPolsha(queue<char> que, bool obrat) {
    int a;
    int b;
    int c;
    bool probel = true;
    stack<int> vivod;
    stack<int> numbers;
    cout << "-----------------------------" << endl;
    cout << "Ввод:";
    for (int j = 0; j < que.size(); j++) {
        if (que.front() == '|') {
            probel == false;
            que.push(que.front());
            que.pop();
            continue;
        }
        if (probel)
            cout << " " << que.front();
        else {
            cout << que.front();
            probel = true;
        }
        que.push(que.front());
        que.pop();
    }
    cout << endl;
    cout << "Вывод:";
    a = numbers.size();
    for (int j = 0; j < a; j++) {
        vivod.push(numbers.top());
        numbers.pop();
    }
    for (int j = 0; j < a; j++) {
        cout << " " << vivod.top();
        numbers.push(vivod.top());
        vivod.pop();
    }
    cout << endl;
    while (!(que.empty())) {
        if (isItNumberorWorld(que.front())==2) {
            numbers.push(int(que.front()-48));
            que.pop();
        }
        else {
            if (que.front() == '|') {
                que.pop();
                a = numbers.top();
                numbers.pop();
                numbers.push(a*10+ int(que.front()-48));
                que.pop();
            }
            else {
                if (numbers.size() < 2) {
                    cout << "error";
                    numbers.pop();
                    return 0;
                }
                else {
                    b = numbers.top();
                    numbers.pop();
                    a = numbers.top();
                    numbers.pop();
                    switch (que.front()) {
                    case('+'):
                        numbers.push(a + b);
                        break;
                    case('-'):
                        if(obrat)
                            numbers.push(a - b);
                        else
                            numbers.push(b - a);
                        break;
                    case('*'):
                        numbers.push(a * b);
                        break;
                    case('/'):
                        if (obrat)
                            numbers.push(a / b);
                        else
                            numbers.push(b / a);
                        break;
                    }
                    que.pop();
                }
            }
        }
        cout << "-----------------------------" << endl;
        cout << "Ввод:";
        for (int j = 0; j < que.size(); j++) {
            if (que.front() == '|') {
                probel == false;
                que.push(que.front());
                que.pop();
                continue;
            }
            if (probel)
                cout << " " << que.front();
            else {
                cout << que.front();
                probel = true;
            }
            que.push(que.front());
            que.pop();
        }
        cout << endl;
        cout << "Вывод:";
        a = numbers.size();
        for (int j = 0; j < a; j++) {
            vivod.push(numbers.top());
            numbers.pop();
        }
        for (int j = 0; j < a; j++) {
            cout << " " << vivod.top();
            numbers.push(vivod.top());
            vivod.pop();
        }
        cout << endl;
    }
    cout << "-----------------------------" << endl;
    c = numbers.top();
    numbers.pop();
    return c;
}

int main() {
    setlocale(LC_ALL, "Rus");
    queue <char> otwet;
    stack <char> Stack;
    string vvod;
    short int a=0;
    short int what;
    short int go = 1;
    int Arrlen;
    int* ptrArrlen = &Arrlen;
    char* Arr;
    while (go) {
        cout << "Что хочешь? " << endl << "0-выход" << endl << "1-преобразовать обычное выражение в обратную польскую нотацию" << endl << "2-проверить выражение на корректность" << endl << "3-вычислить выражение" << endl;
        while (true) {
            if (!(cin >> what && what >= 0 && what <= 3))
            {
                cout << "Invalid input" << endl;
                cin.clear();
                cin.ignore(numeric_limits<streamsize>::max(), '\n');
                cout << "Try again" << endl;
            }
            else
                break;
        }
        switch (what) {
        case(1):
            cin.ignore(numeric_limits<streamsize>::max(), '\n');
            cout << "Введи выражение в обычной форме" << endl;
            getline(cin, vvod);
            otwet = obratPolsha(vvod);
            if (otwet.front() == '?') {
                cout << "Invalid input";
                otwet.pop();
                break;
            }
            cout << "Ответ: ";
            int size;
            size = otwet.size();
            for (int j = 0; j < size; j++) {
                if (j == 0) {
                    cout << otwet.front();
                    otwet.pop();
                    continue;
                }
                if (otwet.front() == '|') {
                    otwet.pop();
                    a = 1;
                    continue;
                }
                if (a == 1) {
                    cout << otwet.front();
                    otwet.pop();
                    a = 0;
                    continue;
                }
                cout << " " << otwet.front();
                otwet.pop();
            }
            cout << endl;
            break;
        //Сделано
        case(2):
            cout << "Как будет записано выражение? (1-в обычной форме, 2-в польской нотации, 3-в обратной польской нотации)" << endl;
            while (true) {
                if (!(cin >> what && what >= 1 && what <= 3))
                {
                    cout << "Invalid input" << endl;
                    cin.clear();
                    cin.ignore(numeric_limits<streamsize>::max(), '\n');
                    cout << "Try again" << endl;
                }
                else
                    break;
            }
            Arrlen = 0;
            switch (what) {
            case(1):
                cin.ignore(numeric_limits<streamsize>::max(), '\n');
                cout << "Введи выражение" << endl;
                getline(cin, vvod);
                Arr = proverkaNorm(vvod, ptrArrlen);
                if (Arr == 0) {
                    cout << "В выражении допущена ошибка" << endl;
                }
                else {
                    cout << "Ошибок не обнаружено" << endl;
                }
                delete[] Arr;
                break;
            case(2):
                cin.ignore(numeric_limits<streamsize>::max(), '\n');
                cout << "Введи выражение" << endl;
                getline(cin, vvod);
                Stack = proverkaPolsh(vvod);
                if (Stack.top() == '?') {
                    cout << "В выражении допущена ошибка" << endl;
                    Stack.pop();
                }
                else {
                    cout << "Ошибок не обнаружено" << endl;
                }
                break;
            case(3):
                cin.ignore(numeric_limits<streamsize>::max(), '\n');
                cout << "Введи выражение" << endl;
                getline(cin, vvod);
                otwet = proverkaObratPolsh(vvod);
                if (otwet.front() == '?') {
                    cout << "В выражении допущена ошибка" << endl;
                    otwet.pop();
                }
                else {
                    cout << "Ошибок не обнаружено" << endl;
                }
                break;
            }
            break;
             //Сделано
        case(3):
            cout << "Как будет записано выражение? (1-в обычной форме, 2-в польской нотации, 3-в обратной польской нотации)" << endl;
            while (true) {
                if (!(cin >> what && what >= 1 && what <= 3))
                {
                    cout << "Invalid input" << endl;
                    cin.clear();
                    cin.ignore(numeric_limits<streamsize>::max(), '\n');
                    cout << "Try again" << endl;
                }
                else
                    break;
            }
            Arrlen = 0;
            switch (what) {
            case(1):
                cin.ignore(numeric_limits<streamsize>::max(), '\n');
                cout << "Введи выражение" << endl;
                getline(cin, vvod);
                cout << "Перевод в обратную польскую конструкцию:" << endl;
                otwet = obratPolsha(vvod);
                if (otwet.front() == '?') {
                    cout << "Invalid input";
                    otwet.pop();
                    break;
                }
                cout << "Обратная польская конструкция: " << endl;
                int size;
                size = otwet.size();
                for (int j = 0; j < size; j++) {
                    if (j == 0) {
                        cout << otwet.front();
                        otwet.push(otwet.front());
                        otwet.pop();
                        continue;
                    }
                    if (otwet.front() == '|') {
                        otwet.push(otwet.front());
                        otwet.pop();
                        a = 1;
                        continue;
                    }
                    if (a == 1) {
                        cout << otwet.front();
                        otwet.push(otwet.front());
                        otwet.pop();
                        a = 0;
                        continue;
                    }
                    cout << " " << otwet.front();
                    otwet.push(otwet.front());
                    otwet.pop();
                }
                cout << "Решение выражения:" << endl;
                a = reshitObratPolsha(otwet, true);
                cout << "Ответ:" << a << endl;
                break;
            case(2):
                cin.ignore(numeric_limits<streamsize>::max(), '\n');
                cout << "Введи выражение" << endl;
                getline(cin, vvod);
                Stack = proverkaPolsh(vvod);
                if (Stack.top() == '?') {
                    cout << "В выражении допущена ошибка" << endl;
                    Stack.pop();
                    break;
                }

                while (!(Stack.empty())) {
                    otwet.push(Stack.top());
                    Stack.pop();
                }
                a = reshitObratPolsha(otwet, false);
                cout << "Ответ:" << a << endl;
                break;
            case(3):
                cin.ignore(numeric_limits<streamsize>::max(), '\n');
                cout << "Введи выражение" << endl;
                getline(cin, vvod);
                otwet = proverkaObratPolsh(vvod);
                if (otwet.front() == '?') {
                    cout << "В выражении допущена ошибка" << endl;
                    otwet.pop();
                    break;
                }
                a = reshitObratPolsha(otwet, true);
                cout << "Ответ:" << a << endl;
                break;
            }
            break;
        case(0):
            go = 0;
            break;
        }
        while (!(otwet.empty()))
            otwet.pop();
    }
    return 0;
}
