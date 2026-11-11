```cpp
#include <iostream>
#include <string>
#include <algorithm>
using namespace std;

//整理运算结果：去除无意义的0，并按题目格式输出纯小数
string format_result(string a){
    bool negative = !a.empty() && a[0]=='-';
    if(negative){
        a.erase(0,1);
    }
    int dot = a.find('.');
    if(dot != (int)string::npos){
        while(!a.empty() && a.back()=='0'){
            a.pop_back();
        }
        if(!a.empty() && a.back()=='.'){
            a.pop_back();
        }
        dot = a.find('.');
    }
    int integer_end = dot == (int)string::npos ? a.size() : dot;
    int first = 0;
    while(first + 1 < integer_end && a[first]=='0'){
        first++;
    }
    if(first > 0){
        a.erase(0,first);
    }
    if(a.size() >= 2 && a[0]=='0' && a[1]=='.'){
        a.erase(0,1);
    }
    if(a.empty() || a=="."){
        return "0";
    }
    if(negative && a!="0"){
        a = "-"+a;
    }
    return a;
}

int char_to_int(char c);

int char_to_int(char c){
    if('0'<=c&&c<='9'){
        return c-'0';
    }
    if('A'<=c&&c<='Z'){
        return c-'A'+10;
    }
    return 0;
}

char int_to_char(int i){
    if(0<=i&&i<=9){
        return (char)(i+'0');
    }
    if(10<=i&&i<=35){
        return (char)(i-10+'A');
    }
    return '0';
}

//取相反数
string get_minus(const string& a){
    if(a=="0"){
        return "0";
    }
    //负数去负号
    if(a[0]=='-'){
        return a.substr(1);
    }
    //非负数加负号
    return ("-"+a);
}

//返回a>=b
bool a_no_less_than_b(string a, string b){
    //如果a是负数
    if(a[0]=='-'){
        //b是正数，负数<正数
        if(b[0]!='-'){
            return false;
        }
        //都是负数绝对值大的反而小
        return a_no_less_than_b(get_minus(b),get_minus(a));
    }
    if(b[0]=='-'){
        return true;
    }
    int as = a.size(), bs = b.size();
    int ad = a.find('.'), bd = b.find('.');
    int ae = ad == (int)string::npos ? as : ad;
    int be = bd == (int)string::npos ? bs : bd;
    int ai = 0, bi = 0;
    while(ai + 1 < ae && a[ai]=='0') ai++;
    while(bi + 1 < be && b[bi]=='0') bi++;
    int alen = max(1,ae - ai), blen = max(1,be - bi);
    if(alen != blen) return alen > blen;
    for(int p = 0; p < alen; p++){
        int da = ae == 0 ? 0 : char_to_int(a[ai+p]);
        int db = be == 0 ? 0 : char_to_int(b[bi+p]);
        if(da != db) return da > db;
    }
    int af = ad == (int)string::npos ? 0 : as - ad - 1;
    int bf = bd == (int)string::npos ? 0 : bs - bd - 1;
    int fstart_a = ad == (int)string::npos ? as : ad + 1;
    int fstart_b = bd == (int)string::npos ? bs : bd + 1;
    for(int p = 0; p < max(af,bf); p++){
        int da = p < af ? char_to_int(a[fstart_a+p]) : 0;
        int db = p < bf ? char_to_int(b[fstart_b+p]) : 0;
        if(da != db) return da > db;
    }
    return true;
}

//两个正数相加
//a和b均为高位在前存储
string add_string(int k, string a, string b){
    if(!a.empty() && a[0]=='+') a.erase(0,1);
    if(!b.empty() && b[0]=='+') b.erase(0,1);
    int i = 0, j = 0, carry = 0, as = a.size(), bs = b.size();
    string ans;
    int ax =0,  bx = 0,  xlen = 0; //xlen为总小数部分长度
    string ai, bi; //低位在前存储 不带小数点的a和b
    //先找到小数点的位置，再给短的小数补0，让小数部分等长，记录小数部分长度
    while(ax<as&&a[ax]!='.'){
        ax++;
    }
    while(bx<bs&&b[bx]!='.'){
        bx++;
    }
    xlen = max(0,max(as-ax-1,bs-bx-1));
    ai = a.substr(0,ax)+(ax<as ? a.substr(ax+1) : "");
    bi = b.substr(0,bx)+(bx<bs ? b.substr(bx+1) : "");
    if(ai.empty()) ai = "0";
    if(bi.empty()) bi = "0";
    ai.append(xlen-max(0,as-ax-1),'0');
    bi.append(xlen-max(0,bs-bx-1),'0');

    reverse(ai.begin(),ai.end());
    reverse(bi.begin(),bi.end());
    as = ai.size();
    bs = bi.size();
    //等长的相加
    while(i<as||j<bs||carry>0){
        int sum = carry;
        if(i<as){
            sum += char_to_int(ai[i]);
            i++;
        }
        if(j<bs){
            sum += char_to_int(bi[j]);
            j++;
        }
        int d = sum%k;
        carry = sum/k;
        ans.push_back(int_to_char(d));
    }
    //如果有小数点，还原小数点
    if(xlen > 0){
        ans = ans.substr(0,xlen)+'.'+ans.substr(xlen);
    }
    reverse(ans.begin(),ans.end());
    return format_result(ans);
}

//先处理符号和大小，再对a>=b的两个非负数作减法
//这里可以复用大整数加法的代码
string minus_string(int k, string a,string b){
    if(!a.empty() && a[0]=='+') a.erase(0,1);
    if(!b.empty() && b[0]=='+') b.erase(0,1);
    //讨论a b的正负
    //a b 异号的情况，转化为大整数加法
    //a负b正,a-b=-(-a+b)
    if(a[0]=='-'&&b[0]!='-'){
        return get_minus(add_string(k, get_minus(a),b));
    }
    //a正b负 a-b = a+(-b)
    if(a[0]!='-'&&b[0]=='-'){
        return add_string(k,a,get_minus(b));
    }
    //a b 同号的情况，作大整数减法
    //a，b同负，a-b=(-b)-(-a);
    if(a[0]=='-'&&b[0]=='-'){
        return minus_string(k,get_minus(b),get_minus(a));
    }

    //a，b同正，如果a<b a - b = -(b-a)
    if(!(a_no_less_than_b(a,b))){
        return get_minus(minus_string(k,b,a));
    }

    //先去掉小数点作大整数减法，再还原小数点，最后剪除前导0和后随0
    int i = 0, j = 0, borrow = 0, as = a.size(), bs = b.size();
    string ans;
    int ax =0,  bx = 0,  xlen = 0; //xlen为总小数部分长度
    string ai, bi; //低位在前存储 不带小数点的a和b
    //先找到小数点的位置，再给短的小数补0，让小数部分等长，记录小数部分长度
    while(ax<as&&a[ax]!='.'){
        ax++;
    }
    while(bx<bs&&b[bx]!='.'){
        bx++;
    }
    xlen = max(0,max(as-ax-1,bs-bx-1));
    ai = a.substr(0,ax)+(ax<as ? a.substr(ax+1) : "");
    bi = b.substr(0,bx)+(bx<bs ? b.substr(bx+1) : "");
    if(ai.empty()) ai = "0";
    if(bi.empty()) bi = "0";
    ai.append(xlen-max(0,as-ax-1),'0');
    bi.append(xlen-max(0,bs-bx-1),'0');

    reverse(ai.begin(),ai.end());
    reverse(bi.begin(),bi.end());
    as = ai.size();
    bs = bi.size();

    //等长的相减,这里已经控制过a>=b，
    while(i<as||j<bs||borrow>0){
        int sum = -borrow;
        if(i<as){
            sum += char_to_int(ai[i]);
            i++;
        }
        if(j<bs){
            sum -= char_to_int(bi[j]);
            j++;
        }
        int d = (sum>=0) ? sum%k : (sum+k)%k;
        borrow = (sum>=0)? 0 : 1;
        ans.push_back(int_to_char(d));
    }

    //如果有小数点，还原小数点
    if(xlen > 0){
        ans = ans.substr(0,xlen)+'.'+ans.substr(xlen);
    }
    reverse(ans.begin(),ans.end());
    return format_result(ans);
}

int main() {

    int n = 0;
    cin >> n;
    for(int i = 0; i < n; i++){
        int k;
        string a,b;
        cin >> k >> a >> b;
        cout<<minus_string(k,a,b)<<'\n';
    }

    //cout<<minus_string(8,"12345677.12345676","12345677.12345676");
    return 0;
}

/*
11111 11111 11111 11121 11
00000 00001 11111 11111 1
11111 11112 22222 22200 02
*/
```
