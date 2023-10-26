
# 47 路灯照明：模拟

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

void solve_method(const vector<int>& ints) {
    int n = ints.size();
    vector<bool> bytes_((n - 1) * 100, false);

    for (int i = 0; i < n; ++i) {
        int pos = i * 100;
        int left = max(pos - ints[i], 0);
        int right = min(pos + ints[i], static_cast<int>(bytes_.size()));

        for (int k = left; k < right; ++k) {
            bytes_[k] = true;
        }
    }

    int count = count_if(bytes_.begin(), bytes_.end(), [](bool v) { return !v; }); // !!!
    cout << count << endl;
}

int main() {
    int n;
    cin >> n;

    vector<int> ints(n);
    for (int i = 0; i < n; ++i) {
        cin >> ints[i];
    }

    solve_method(ints);

    return 0;
}
```


# 46 找终点：贪心，模拟

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

int min_step(vector<int> &A) {
    const int SIZE = 105;
    int ans = SIZE;
    int n = A.size();
    for (int i = 1; i < n / 2; ++i) {
        int step = 0;
        int j = i;
        while (true) {
            step += 1;
            if (j == n - 1) {
                ans = min(ans, step);
                break;
            }
            if (j >= n) {
                step = SIZE;
                break;
            }
            j += A[j];
        }
    }
    return (ans == SIZE) ? -1 : ans;
}

int main() {
    string line;
    cin >> line;
    getline(cin, line);
    stringstream ss(line);
    int n;
    vector<int> A;
    while (ss >> n)
    {
        A.push_back(n);
    }
    int ans = min_step(A);
    cout << ans << endl;
    return 0;
}
```

# 44 非严格递增连续数字序列：brute
substring 而非常见的 longest not-decreasing subsequence

```cpp
#include <iostream>
#include <string>

using namespace std;

void solve_method(const string& line) {
    int curLen = 0, maxLen = 0;

    char last = 'a';
    for (char cur : line) {
        if (isdigit(cur)) {
            if (curLen == 0 || cur >= last) {  // 1     3     6
                curLen += 1;                   // last  cur
            } else { // !!! 很容易漏掉 curLen 的反面
                if (curLen > maxLen) { // 6     5    2
                    maxLen = curLen;   // last  cur
                }
                curLen = 1;
            }
            last = cur;
        } else {
            if (curLen > maxLen) { // 1  3     f
                maxLen = curLen;  //    last  cur
            }
            curLen = 0;
            last = 'a';
        }
    }
    maxLen = max(curLen, maxLen);

    cout << maxLen << endl;
}

int main() {
    string line;
    getline(cin, line);
    solve_method(line);
    return 0;
}
```


# 42 洞穴探险：字符串

```cpp
#include <iostream>
#include <string>
#include <sstream>

using namespace std;

void solve_method(string history) {
    int index = 0, x = 0, y = 0, max_val = 0;
    int l, r;

    while (true) {
        history = history.substr(index);
        l = history.find("(");
        r = history.find(")");

        if (l == string::npos) {
            break;
        }

        string substring = history.substr(l + 1, r - l - 1);
        stringstream ss(substring);
        string a_str, b_str;
        getline(ss, a_str, ',');
        getline(ss, b_str);

        if (a_str[0] != '0' && b_str[0] != '0') {
            int a = stoi(a_str);
            int b = stoi(b_str);
            // if (a >= 1000 || b >= 1000) continue;

            int len_val = a * a + b * b;
            if (max_val < len_val) {
                max_val = len_val;
                x = a;
                y = b;
            }
        }
        index = r + 1;
    }
    cout << "(" << x << ", " << y << ")" << endl;
}

int main() {
    string history;
    getline(cin, history);
    solve_method(history);
    return 0;
}
```

# 39 矩形相交的面积：数学

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

int main() {
    vector<vector<int>> points;
    for (int i = 0; i < 3; ++i) {
        int x1, y1, w, h;
        cin >> x1 >> y1 >> w >> h;
        int x2 = x1 + w;
        int y2 = y1 - h; // !!!
        points.push_back({x1, y1, x2, y2});
    }

    vector<int> xs, ys;
    for (const auto& point : points) {
        xs.push_back(point[0]);
        xs.push_back(point[2]);
        ys.push_back(point[1]);
        ys.push_back(point[3]);
    }

    vector<vector<int>> nums;
    int min_x = *min_element(xs.begin(), xs.end());
    int min_y = *min_element(ys.begin(), ys.end());
    for (const auto& point : points) {
        nums.push_back({
            point[0] - min_x,
            point[1] - min_y,
            point[2] - min_x,
            point[3] - min_y
        });
    }

    int max_x = *max_element(xs.begin(), xs.end());
    int max_y = *max_element(ys.begin(), ys.end());
    vector<vector<int>> dp(max_x - min_x + 1, vector<int>(max_y - min_y + 1, 0));

    for (const auto& num : nums) {
        for (int i = min(num[2], num[0]); i < max(num[2], num[0]); ++i) {
            for (int j = min(num[3], num[1]); j < max(num[3], num[1]); ++j) {
                dp[i][j] += 1;
            }
        }
    }

    int ret = 0;
    for (const auto& row : dp) {
        for (int cell : row) {
            if (cell == 3) {
                ret += 1;
            }
        }
    }

    cout << ret << endl;

    return 0;
}
```



# 38 数组组成的最小数字：排序

```cpp
int main() {
    string line, s; getline(cin, line);
    stringstream ss(line);
    vector<int> A;
    while (getline(ss, s, ',')) {
        A.push_back(stoi(s));
    }

    sort(A.begin(), A.end());
    if (A.size() > 3) A.erase(A.begin() + 3, A.end());  
    sort(A.begin(), A.end(), [](const int a, const int b) {
        return to_string(a) < to_string(b); // 总长度决定后，比较字典序决定顺序
    });

    string builder = "";
    for (int x : A) builder += to_string(x);
    cout << builder << endl;
    return 0;
}
```

# 37 分糖果：数学

```cpp
#include <iostream>

void solveMethod(int x) {
    int count = 0;
    while (x != 1) {
        if (x == 3) { // !!!
            count += 2;
            break;
        }
        if (x % 2 != 0) {
            if ((x + 1) / 2 % 2 == 0) { // !!!
                x += 1;
            } else {
                x -= 1;
            }
            count += 1;
        }
        x /= 2;
        count += 1;
    }
    cout << count << endl;
}

int main() {
    int x;
    cin >> x;
    solveMethod(x);
    return 0;
}
```

# 36 数组合并：数组

```cpp
#include <iostream>
#include <vector>
#include <sstream>

using namespace std;

string solve_method(int k, int n, vector<string> strings) {
    string builder;
    vector<vector<int>> lists;
    for (string str : strings) {
        vector<int> list;
        stringstream ss(str);
        int num;
        char ch;
        while (ss >> num) {
            list.push_back(num);
            ss >> ch;
        }
        lists.push_back(list);
    }
    int index = 0;
    while (!lists.empty()) {
        vector<int>& linked_list = lists[index];
        for (int i = 0; i < k; i++) {
            if (linked_list.empty()) {
                lists.erase(lists.begin() + index);
                index--;
                break;
            }
            // `front` `erase` !!!
            builder += to_string(linked_list.front()) + ",";
            linked_list.erase(linked_list.begin());
        }
        index++;
        if (index >= lists.size()) {
            index = 0;
        }
    }
    if (!builder.empty()) {
        builder.pop_back();
    }
    return builder;
}

int main() {
    int k, n;
    cin >> k;
    cin >> n;
    vector<string> strings(n);
    for (int i = 0; i < n; i++) {
        cin >> strings[i];
    }
    string res = solve_method(k, n, strings);
    cout << res << endl;
    return 0;
}
```

# 35 最少交换次数：DP

line : string, 
ss : stringstream,
s : string, 
N : len, 
n : num, 
A : nums

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

int main() {
    vector<int> nums;
    int k, n;
    string line;

    getline(cin, line);
    cin >> k;

    stringstream ss(line);
    while (ss >> n) {
        nums.push_back(n);
    }

    vector<int> new_nums;
    int N = nums.size();
    int m = 0;
    for (int num : nums) {
        int new_num = (num < k) ? 1 : 0;
        new_nums.push_back(new_num);
        m += new_num;
    }

    vector<int> dp;
    for (int i = 0; i < N; i++) {
        int sum = 0; // [i, i+j] : 目标是全1的子数组
        for (int j = 0; j < m; j++) {
            if (i + j < N) {
                sum += new_nums[i + j];
            }
        }
        dp.push_back(sum);
    }
    // dp = [sum(new_nums[i : i + m]) for i in range(n)]

    int result = m - *max_element(dp.begin(), dp.end());

    cout << result << endl;

    return 0;
}
```

# 31 相对开音节：字符串

```cpp
#include <iostream>
#include <vector>
#include <sstream>
using namespace std;

bool isVowel(char c) {
    return c == 'a' || c == 'e' || c == 'i' || c == 'o' || c == 'u';
}

bool isWord(char c) {
    return isalpha(c) && islower(c);
}

void solveMethod(const string& line) {
    stringstream ss(line);
    string word;
    int count = 0;

    while (ss >> word) {
        string chars = word;
        bool is_all_lower_alpha = true;

        for (char ch : word) {
            if (!isalpha(ch) || !islower(ch)) {
                is_all_lower_alpha = false;
                break;
            }
        }

        if (is_all_lower_alpha) {
            int i = 0, j = chars.length() - 1;
            while (i < j) {
                swap(chars[i], chars[j]);
                i++;
                j--;
            }
        }

        if (chars.length() < 4) {
            continue;
        }

        for (size_t i = 0; i < chars.length() - 3; i++) {
            if (isWord(chars[i]) && isWord(chars[i + 1]) && isWord(chars[i + 2]) && isWord(chars[i + 3])) {
                if (!isVowel(chars[i]) && isVowel(chars[i + 1]) && !isVowel(chars[i + 2]) && chars[i + 2] != 'r' && chars[i + 3] == 'e') {
                    count++;
                }
            }
        }
    }

    cout << count << endl;
}

int main() {
    string line;
    getline(cin, line);
    solveMethod(line);
    return 0;
}
```

# 28 VLAN资源池：字符串，排序，双指针

```cpp
#include <iostream>
#include <sstream>
#include <set>
#include <vector>
#include <string>
#include <algorithm>
using namespace std;

int main() {
    // input
    string input_str;
    getline(cin, input_str);

    int request;
    cin >> request;
    cin.ignore();  // 清除输入缓冲区的换行符

    unordered_set<int> result_set;
    stringstream ss(input_str);
    string segment;
    while (getline(ss, segment, ',')) {
        size_t dash_pos = segment.find('-');
        if (dash_pos != string::npos) {
            int start = stoi(segment.substr(0, dash_pos));
            int end = stoi(segment.substr(dash_pos + 1));
            for (int i = start; i <= end; i++) {
                result_set.insert(i);
            }
        } else {
            result_set.insert(stoi(segment));
        }
    }

    // deal
    result_set.erase(request);

    vector<int> result_list(result_set.begin(), result_set.end());
    sort(result_list.begin(), result_list.end());
    
    // output
    int start = result_list[0];
    int last = start;
    vector<pair<int, int>> output_list;

    for (size_t i = 1; i < result_list.size(); i++) {
        int cur = result_list[i];
        if (cur == last + 1) {
            last = cur;
        } else {
            output_list.push_back(make_pair(start, last));
            start = last = cur;
        }
    }
    output_list.push_back(make_pair(start, last)); // 出loop后补上最后一个!!!

    string output_str;
    for (const auto& pair : output_list) {
        if (pair.first == pair.second) {
            output_str += to_string(pair.first) + ",";
        } else {
            output_str += to_string(pair.first) + "-" + to_string(pair.second) + ",";
        }
    }
    output_str.pop_back();  // 移除最后一个逗号!!!

    cout << output_str << endl;

    return 0;
}
```
# 27 磁盘容量：排序
`vector<int> copy(old_vec)`
`stable_sort(A.begin(), A.end(), [](const auto &a, const auto &b) {return ?});`

```cpp
#include<bits/stdc++.h>
using namespace std;
using ll = long long;

unordered_map<char, int> mp{{'M', 1}, {'G', 1024}, {'T', 1024*1024}};

ll helper(string s) {
    ll pre = 0, ans = 0;
    for (char c : s) {
        if (c <= '9' && c>= '0') pre = 10 * pre + (c - '0');
        else {
            ans += pre * mp[c];
            pre = 0;
        }
    }
    return ans;
}

int main() {
    int N; cin >> N;
    vector<pair<ll, int>> A;
    vector<string> S; 
    for (int i = 0; i < N; ++i) {
        string s; cin >> s;
        S.push_back(s);
        ll x = helper(s); 
        A.push_back({x, i});
    }

    sort(A.begin(), A.end(), [](const auto &a, const auto &b) {
        return a.first == b.first ? a.second < b.second : a.first < b.first;
    });

    for (int i = 0; i < N; ++i) cout << S[A[i].second] << endl;

    return 0;
}
```

# 26 出错的或电路：位运算 

```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    int length;
    cin >> length;

    string s1, s2;
    cin >> s1 >> s2;

    int count1 = 0, count0 = 0, count01 = 0;
    for (int i = 0; i < length; i++) {
        if (s1[i] == '1') count1++;
        if (s2[i] == '0') count0++;
        if (s1[i] == '1' && s2[i] == '0') count01++;
    }

    long long result = static_cast<long long>(count1 - count01) * (count0 - count01) + static_cast<long long>(length - count1) * count01;
    /* 
    11,00 的通用情况（s1中'1'|1中的'1'、去和s1中 '0'|0 的'0'情况交换，方案总数是乘积） 
    + 
    10 的特殊状况（s1拿出任意1个0、换'1'|0中的'1'，就能变 0|0 令结果改变，加上这一部分）
    */
    cout << result << endl;

    return 0;
}

```

# 20 分苹果：位运算，数组遍历，贪心

```cpp
```

# 18 排序

偏简单，注意 lambda `[&h](const...)`


# 17 模拟
```cpp
#include <iostream>
#include <vector>
#include <sstream>

void solve_method(const string& line) {
    istringstream iss(line);
    int sum_ = 0;
    int n;
    vector<int> nums;

    while (iss >> n) {
        sum_ += n;
        nums.push_back(n);
    }

    vector<int> res(nums.size(), 0);

    int j = 0;
    for (int i = 1; i < 300; ++i) {
        if (j == nums.size()) {
            j = 0;
        }
        if (i % 7 == 0 || to_string(i).find('7') != string::npos) {
            res[j] += 1;
        }
        int sum1 = 0;
        for (int num : res) {
            sum1 += num;
        }
        if (sum_ == sum1) {
            break;
        }
        ++j;
    }

    for (int i = 0; i < res.size(); ++i) {
        cout << res[i];
        if (i < res.size() - 1) {
            cout << " ";
        }
    }
    cout << endl;
}

int main() {
    string line;
    getline(cin, line);
    solve_method(line);
    return 0;
}
```

7和13跳过了，在宿舍做的，双指针

# 16 栈
```cpp
#include <iostream>
#include <vector>
#include <string>

vector<char> solve_method(const string& num1, int num2) {
    vector<char> res_list;
    int count = 0;
    size_t i = 0;
    while (i < num1.length()) {
        count = i;
        while (!res_list.empty() && res_list.back() > num1[i] && num2 != 0) {
            res_list.pop_back();
            num2 -= 1;
        } // 单调递增
        res_list.push_back(num1[i]);
        if (num2 == 0) {
            break;
        } // 先push当前值，再退出
        i += 1;
    }
    res_list.insert(res_list.end(), num1.begin() + count + 1, num1.end()); // 不能删了，剩余接到末尾
    if (num2 > 0) {
        res_list.erase(res_list.end() - num2, res_list.end());
    } // 已经递增但还可以删，就删末尾
    return res_list;
}

int main() {
    string n1;
    int n2;
    cin >> n1;
    cin >> n2;
    vector<char> result = solve_method(n1, n2);
    // 删除前导零
    size_t non_zero_index = 0;
    while (non_zero_index < result.size() && result[non_zero_index] == '0') {
        non_zero_index++;
    }
    if (non_zero_index == result.size()) {
        cout << "0" << endl;
    } else {
        for (size_t i = non_zero_index; i < result.size(); i++) {
            cout << result[i];
        }
        cout << endl;
    }
    return 0;
}
```

# 15 排序
```cpp

static bool cmp(int a, int b){
        string A = to_string(a) + to_string(b);
        string B = to_string(b) + to_string(a);
        return A < B; // !
    }
```

# 3 细节太多，中心扩散、找最长连续子串
```cpp
#include <iostream>
#include <vector>

using namespace std;

// 寻找要替换的元素索引
int find_index_to_replace(const vector<int>& nums, int target) {
    int max_val = INT_MIN;  // 最大匹配数初始化为负无穷
    int mid_dist = 0;  // 到中点距离初始化为0
    int result = -1;  // 要替换的索引初始化为-1

    for (int index = 0; index < nums.size(); ++index) {  // 遍历nums中的元素及其索引
        if (nums[index] != 0) {  // 如果当前元素不为0，则跳过该元素
            continue;
        }

        // 计算左边匹配的数量
        int left_count = count_left_match(nums, target, index, max_val);
        if (left_count > 4) {  // 如果左边匹配的数量大于4，则跳过该元素
            continue;
        }

        // 计算右边匹配的数量
        int right_count = count_right_match(nums, target, index, max_val);
        int total_count = left_count + right_count;  // 计算总匹配数量
        int dist_to_mid = abs(index - nums.size() / 2);  // 计算当前索引到中点的距离

        // 更新最大匹配数和要替换的索引
        if (total_count > max_val || (total_count == max_val && dist_to_mid < mid_dist)) {
            max_val = total_count;
            result = index;
            mid_dist = dist_to_mid;
        }
    }

    return result;  // 返回要替换的元素索引
}

// 计算左边匹配的数量
int count_left_match(const vector<int>& nums, int target, int index, int max_val) {
    int left = index - 1;
    int left_count = 0;
    while (left >= 0 && nums[left] == target && left_count < max_val - 1) {
        left_count++;
        left--;
    }
    return left_count;
}

// 计算右边匹配的数量
int count_right_match(const vector<int>& nums, int target, int index, int max_val) {
    int right = index + 1;
    int right_count = 0;
    while (right < nums.size() && nums[right] == target && right_count < max_val - 1) {
        right_count++;
        right++;
    }
    return right_count;
}

int main() {
    int target;
    cin >> target;  // 输入目标值

    vector<int> nums;
    int x;
    while (cin >> x) {
        nums.push_back(x);
    }

    int index_to_replace = find_index_to_replace(nums, target);  // 调用函数获取要替换的元素索引
    cout << index_to_replace << endl;  // 输出要替换的元素索引

    return 0;
}

```


# 2 模拟编码；真碰到这道题的话，放到最后做，细节太多
```cpp
#include <iostream>
#include <bitset>
#include <sstream>

string solve_method(int num) {
    bitset<32> binary(num);
    string binary_str = binary.to_string();
    size_t pos = binary_str.find('1');
    if (pos == string::npos) return "00"; // !
    binary_str = binary_str.substr(pos);

    size_t length = binary_str.size();
    stringstream builder;
    for (size_t i = length; i > 0; i -= 7) {
        size_t start = i >= 7 ? i - 7 : 0;
        string bin_ = binary_str.substr(start, i - start); // !
        if (bin_.size() < 7) {
            bin_ = string(7 - bin_.size(), '0') + bin_;
        }
        bin_ = (i - 7 <= 0 ? '0' : '1') + bin_;
        stringstream hex_stream;
        hex_stream << hex << uppercase << stoi(bin_, nullptr, 2);
        string hex_ = hex_stream.str();
        if (hex_.size() < 2) {
            hex_ = '0' + hex_;
        }
        builder << hex_;
    }
    return builder.str();
}

int main() {
    int num;
    cin >> num;
    cout << solve_method(num) << endl;
    return 0;
}
```

# 5 prefix sum
```cpp
#include <iostream>
#include <vector>
#include <numeric>
using namespace std;

int solve_method(const vector<int>& nums) {
    int left_sum = 0;
    int right_sum = accumulate(nums.begin(), nums.end(), 0);  // Calculate the sum of array elements

    for (size_t i = 0; i < nums.size(); ++i) {
        right_sum -= nums[i];  // In each loop, subtract the current element from the right side elements
        if (left_sum == right_sum) {  // Judge whether the sum of the left side elements is equal to the sum of the right side elements
            return i;
        }
        left_sum += nums[i];  // Add the current element to the left side element sum
    }
    return -1;
}

int main() {
    // cin->line->strinstrem
    //              | | |
    //              x x x
    vector<int> nums;
    string input_str;
    getline(cin, input_str);
    stringstream ss(input_str);
    string item;
    while (getline(ss, item, ',')) {
        nums.push_back(stoi(item));  // Convert the input string array to an integer array
    }
    cout << solve_method(nums) << endl;
    return 0;
}

```

# 4 排序，模拟
```cpp
#include <iostream>
#include <string>
#include <sstream>
#include <map>
#include <vector>
#include <algorithm>
using namespace std;

int main() {
    map<string, vector<int>> student_dict;
    string line;
    
    while (getline(cin, line)) {
        stringstream ss(line);
        string x;
        while (getline(ss, x, ';')) {
            stringstream sx(x);
            string y;
            getline(sx, y, ',');
            int score;
            sx >> score;

            student_dict[y].push_back(score);
        }
    }

    map<string, vector<pair<string, int>>> class_dict;
    for (auto& p : student_dict) {
        const string& s = p.first;
        const vector<int>& scores = p.second; // !

        if (scores.size() == 2) {
            string cid = s.substr(0, 5);
            int sum = scores[0] + scores[1];
            class_dict[cid].push_back({s, sum});
        }
    }

    if (class_dict.empty()) { // !
        cout << "NULL" << endl;
        return 0;
    }

    for (auto& p : class_dict) {
        const string& cid = p.first;
        vector<pair<string, int>>& students = p.second;

        sort(students.begin(), students.end(), [](const auto& lhs, const auto& rhs) {
            return lhs.second != rhs.second ? lhs.second > rhs.second : lhs.first < rhs.first;
        });

        cout << cid << endl;
        for (size_t i = 0; i < students.size(); ++i) { // !
            if (i > 0) cout << ';'; // !
            cout << students[i].first;
        }
        cout << endl;
    }
    return 0;
}

// bonus
struct Comparator {
    bool operator()(const pair<string, int>& lhs, const pair<string, int>& rhs) const {
        return lhs.second != rhs.second ? lhs.second > rhs.second : lhs.first < rhs.first;
    }
};
//  Comparator comp; sort(students.begin(), students.end(), comp);
```