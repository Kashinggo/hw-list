实测抽中题目为：
- 100->告警抑制（模拟）、数字的排列/数字反转打印（模拟）
- 200->字符串化繁为简（并查集）

均未在练习记录内

# 64 排队游戏：二分查找

```cpp
#include <iostream>
#include <vector>
#include <set>
#include <map>
#include <algorithm>

using namespace std;

int calculate_diss(int n, int m, int k, set<int>& pricks_idx, vector<int>& all_ability) {
    int dissatisfaction = 0;
    map<int, int> pricks_abilities;  // 记录刺头学生的能力和数量
    int prick_count = 0;

    for (int i = 0; i < n; ++i) {
        int ability = all_ability[i];

        if (pricks_idx.count(i)) {
            prick_count++;
            pricks_abilities[ability]++;
        } else {
            // 使用二分查找找到当前学生能力在刺头学生中的排名
            auto it = pricks_abilities.lower_bound(ability);
            int rank = distance(pricks_abilities.begin(), it);

            // 计算不满意程度
            dissatisfaction += prick_count - rank;
        }
    }

    return dissatisfaction > k ? 1 : 0;
}

int main() {
    int n, m, k;
    cin >> n >> m >> k;
    set<int> pricks_idx;
    for (int i = 0; i < m; ++i) {
        int idx;
        cin >> idx;
        pricks_idx.insert(idx - 1);
    }
    vector<int> all_ability(n);
    for (int i = 0; i < n; ++i) {
        cin >> all_ability[i];
    }

    int result = calculate_diss(n, m, k, pricks_idx, all_ability);
    cout << result << endl;

    return 0;
}
```

---100分题型

# 101 勾股数：数学
(m - n)^3

```cpp
#include <iostream>
using namespace std;

bool relatively_prime(int x, int y) {
    return gcd(x, y) == 1;
}

void solve_method(int n, int m) {
    int count = 0;
    for (int a = n; a < m - 1; a++) {
        for (int b = a + 1; b < m; b++) {
            for (int c = b + 1; c <= m; c++) {
                if (relatively_prime(a, b) && relatively_prime(b, c) && relatively_prime(a, c) && a * a + b * b == c * c) {
                    count++;
                    cout << a << " " << b << " " << c << endl;
                }
            }
        }
    }
    if (count == 0) {
        cout << "Na" << endl;
    }
}

int main() {
    int n, m;
    cin >> n >> m;
    solve_method(n, m);
    return 0;
}
```

# 100 最长公共后缀：模拟

```cpp
#include <iostream>
#include <string>
#include <vector>

using namespace std;

int main() {
    string input;
    getline(cin, input);

    // 去掉方括号和双引号，然后分割字符串
    input.erase(remove(input.begin(), input.end(), '['), input.end()); // 移动到末尾、删除
    input.erase(remove(input.begin(), input.end(), ']'), input.end());
    input.erase(remove(input.begin(), input.end(), '\"'), input.end());
    vector<string> strings;
    size_t pos = 0;
    while ((pos = input.find(',')) != string::npos) {
        strings.push_back(input.substr(0, pos));
        input.erase(0, pos + 1);
    }
    strings.push_back(input);

    string pre = strings[0];
    for (size_t i = 1; i < strings.size(); i++) {
        const string& str = strings[i];
        size_t j = 1;
        while (j <= min(pre.size(), str.size()) && pre[pre.size() - j] == str[str.size() - j]) { // !!!
            j++;
        }

        if (j == 1) {
            pre = "@Zero";
            break;
        }

        pre = pre.substr(pre.size() - j + 1);
    }

    cout << pre << endl;

    return 0;
}
```

# 99 求符合条件元组个数：回溯/DFS

```cpp
#include <iostream>
#include <vector>
#include <set>
#include <algorithm>

using namespace std;

int res = 0;
int target = 0;
set<vector<int>> numsSet;

void combine(const vector<int>& nums, int n, vector<int> lst, int index, int total) {
    if (n == 0) {
        if (total == target) {
            sort(lst.begin(), lst.end());
            numsSet.insert(lst);
            res += 1;
        }
    } else {
        for (int i = index; i < nums.size(); i++) {
            if (i > index && nums[i] == nums[i - 1]) {
                continue;
            }
            if (total + nums[i] > target) {
                break;
            }
            lst.push_back(nums[i]); // in
            combine(nums, n - 1, lst, i + 1, total + nums[i]); // try
            lst.pop_back(); // out
        }
    }
}

int main() {
    vector<int> nums;
    int k;

    string line;
    getline(cin, line);
    istringstream iss(line);
    int num;
    while (iss >> num) {
        nums.push_back(num);
    }

    cin >> k >> target;

    sort(nums.begin(), nums.end());
    combine(nums, k, {}, 0, 0);

    cout << res << endl;

    return 0;
}
```

# 98 字符串摘要：模拟

```cpp
#include <iostream>
#include <string>
#include <map>
#include <vector>
#include <algorithm>

using namespace std;

void solve_method() {
    string str;
    getline(cin, str);
    string new_str = "";
    for (char c : str) {
        if (isalpha(c)) {
            new_str += c;
        }
    }

    int length = new_str.length();
    if (length == 0) {
        return;
    }

    int total = 1;
    char temp = tolower(new_str[length - 1]);

    if (length == 1) {
        cout << temp << "0" << endl;
        return;
    }

    vector<string> letter_list;
    map<char, int> char_count_map;

    for (int i = length - 2; i >= 0; --i) {
        char c = tolower(new_str[i]);
        if (temp == c) {
            total++;
        } else {
            if (total == 1) {
                total += char_count_map[temp] - 1;
                char_count_map[temp] = total + 1;
            } else {
                char_count_map[temp] = total + char_count_map[temp];
            }
            letter_list.push_back(string(1, temp) + to_string(total));
            temp = c;
            total = 1;
        }

        if (i == 0) {
            if (total == 1) {
                total += char_count_map[temp] - 1;
            }
            letter_list.push_back(string(1, temp) + to_string(total));
        }
    }

    sort(letter_list.begin(), letter_list.end(), [](const string &a, const string &b) {
        int num_a = stoi(a.substr(1));
        int num_b = stoi(b.substr(1));
        if (num_a != num_b) {
            return num_a > num_b;
        }
        return a[0] > b[0];
    });

    string res;
    for (const string &s : letter_list) {
        res += s;
    }
    cout << res << endl;
}

int main() {
    solve_method();
    return 0;
}
```

# 97 经典屏保：模拟

```cpp
#include <iostream>
using namespace std;

int main() {
    int x, y, t;
    cin >> x >> y >> t;

    int x_dir = 1;
    int y_dir = 1;
    int width = 800;
    int height = 600;
    int xBoundary = width - 50;
    int yBoundary = height - 25;

    for (int i = 0; i < t; i++) {
        x += x_dir;
        y += y_dir;

        if (x == 0 || x == xBoundary) {
            x_dir *= -1;
        }
        if (y == 0 || y == yBoundary) {
            y_dir *= -1;
        }
    }

    cout << x << " " << y << endl;

    return 0;
}
```

# 96 数大雁：模拟

题意是求重叠的数量，而非单纯的计数

我们要模拟的是多个大雁同时发出的"quack"的过程，并且要找出最少需要多少只大雁。在这个过程中，我们必须保证大雁的叫声是完整且按顺序的"quack"。

1. **定义数组"s"记录每个字符的出现次数**
   - 这是因为我们需要知道在任何给定的时间点，是否有足够的"q"去匹配"u"，足够的"u"去匹配"a"，以此类推。如果不匹配，就意味着叫声是不完整或者顺序错误的。

2. **对于每个字符的处理**
   - 当字符是"q"时，增加"s"中第一个元素，这意味着有一只新的大雁开始叫"quack"。
   - 对于其他字符，我们需要找到它在"quack"中的位置，并根据这个位置更新数组"s"。如果字符是"u"，我们就需要一个"q"来匹配，所以"s"中第一个元素减1；同时，"s"中第二个元素加1，表示"u"的数量增加了。对于"k"的处理也类似，但需要注意的是，当"k"出现时，表示一只大雁的叫声结束了，所以"s"中最后一个元素要减1。

3. **实时计算"s"中元素的累加和的最大值"c"**
   - 这是因为我们要找的是最少需要多少只大雁，而这个值就是在任何给定的时间点，已经开始但还没结束的"quack"的数量。

4. **检查"s"中是否有元素的值为-1**
   - 如果有，就说明叫声是不完整或者顺序错误的，应该直接返回-1。

5. **遍历结束后检查"s"中元素的累加和是否为0**
   - 如果不为0，说明叫声不完整，返回-1；否则，返回"c"的值，表示最少需要的大雁数量。

```cpp
#include <iostream>
#include <string>
#include <vector>
#include <algorithm>

using namespace std;

int solve_method(const string& quack) {
    const string b = "quack";
    if (quack.length() % 5 != 0) {
        return -1;
    }
    vector<int> s(b.length(), 0);
    int c = 0;

    for (char c : quack) {
        size_t index = b.find(c);
        if (index == 0) {
            s[0]++;
        } else {
            s[index - 1]--;
            s[index]++;
            if (index == b.length() - 1) {
                s[b.length() - 1]--;
            }
        }

        c = max(c, accumulate(s.begin(), s.end(), 0)); // !!!
        if (find(s.begin(), s.end(), -1) != s.end()) {
            return -1;
        }
    }

    if (accumulate(s.begin(), s.end(), 0) != 0) {
        return -1;
    }

    return c;
}

int main() {
    string user_input;
    getline(cin, user_input);
    int result = solve_method(user_input);
    cout << result << endl;

    return 0;
}
```

# 95 TLV 编码：模拟

```cpp
#include <iostream>
#include <string>
using namespace std;

void solveMethod(const string& tag, const string& source) {
    size_t p = 0;
    while (p < source.length()) {
        string curTag = source.substr(p, 2);
        string lenHEX = source.substr(p + 6, 2) + source.substr(p + 3, 2);
        int lenDEC = stoi(lenHEX, nullptr, 16); // !!!
        if (tag == curTag) {
            string value = source.substr(p + 9, lenDEC * 3);
            cout << value << endl;
        }
        p += 9 + lenDEC * 3;
    }
}

int main() {
    string tag, source;
    cin >> tag;
    getline(cin, source);
    solveMethod(tag, source);
    return 0;
}

```

# 94 数据分类：模拟

```cpp
#include <iostream>
#include <vector>
#include <map>

using namespace std;

int int_byte_sum(int x) {
    int sum = 0;
    for (int i = 0; i < 4; ++i) {
        sum += (x >> (i * 8)) & 0xff;
    }
    return sum;
}

void solve_method(const vector<int>& ints) {
    int c = ints[0];
    int b = ints[1];
    map<int, int> map;

    for (size_t i = 2; i < ints.size(); ++i) {
        int r = int_byte_sum(ints[i]) % b;
        if (r < c) {
            map[r]++;
        }
    }

    int max = 0;
    for (const auto& entry : map) {
        if (entry.second > max) {
            max = entry.second;
        }
    }
    cout << max << endl;
}

int main() {
    vector<int> ints;
    int x;
    while (cin >> x) {
        ints.push_back(x);
        if (cin.get() == '\n') {
            break;
        }
    }
    solve_method(ints);
    return 0;
}
```

# 93 剩余可用字符集：模拟, OOP

```cpp
#include <iostream>
#include <sstream>
#include <map>
#include <vector>
#include <algorithm>
using namespace std;

class Item {
public:
    char c;
    int num;
    int count;

    Item(char c, int num, int count) : c(c), num(num), count(count) {}
};

void solve_method(const string &line) {
    size_t at_pos = line.find('@');
    map<char, Item> char_map;
    string all = line.substr(0, at_pos);
    stringstream all_ss(all);
    string pair;
    int num = 0;
    while (getline(all_ss, pair, ',')) {
        char c = pair[0];
        int count = stoi(pair.substr(2));
        char_map[c] = Item(c, num++, count);
    }
    if (at_pos != string::npos) {
        string others = line.substr(at_pos + 1);
        stringstream others_ss(others);
        while (getline(others_ss, pair, ',')) {
            char c = pair[0];
            int count = stoi(pair.substr(2));
            char_map[c].count -= count;
        }
    }
    vector<Item> items;
    for (const auto &entry : char_map) {
        if (entry.second.count > 0) {
            items.push_back(entry.second);
        }
    }
    sort(items.begin(), items.end(), [](const Item &a, const Item &b) {
        return a.num < b.num;
    });
    for (size_t i = 0; i < items.size(); ++i) {
        cout << items[i].c << ':' << items[i].count;
        if (i + 1 != items.size()) {
            cout << ',';
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

# 92 阿里巴巴找黄金宝箱5：滑动窗口，前缀和

```cpp
#include <iostream>
#include <unordered_map>
#include <vector>

using namespace std;

int main() {
    // 输入
    string input;
    getline(cin, input);
    int k;
    cin >> k;

    // 将输入的字符串转换为整数数组
    vector<int> nums;
    int num = 0;
    bool negative = false;
    for (char ch : input) {
        if (ch == '-') {
            negative = true;
        } else if (isdigit(ch)) {
            num = num * 10 + (ch - '0');
        } else if (ch == ',' || ch == ' ') {
            nums.push_back(negative ? -num : num);
            num = 0;
            negative = false;
        }
    }
    if (num > 0 || negative) {
        nums.push_back(negative ? -num : num);
    }

    // 定义变量
    unordered_map<int, int> frequency_map;
    vector<int> prefix_sum(nums.size() + 1, 0);
    int start = 0;
    int max_sum = 0;

    // 遍历数组
    for (int i = 0; i < nums.size(); i++) {
        prefix_sum[i + 1] = prefix_sum[i] + nums[i];
        frequency_map[nums[i]]++;

        while (frequency_map.size() > k) { // 用 j - i 的长度判断更适合，题目没说 unique k 而是 k length
            int start_num = nums[start];
            frequency_map[start_num]--;
            if (frequency_map[start_num] == 0) {
                frequency_map.erase(start_num);
            }
            start++;
        }

        if (frequency_map.size() == k) {
            int current_sum = prefix_sum[i + 1] - prefix_sum[start];
            max_sum = max(max_sum, current_sum);
        }
    }

    // 输出结果
    cout << max_sum << endl;

    return 0;
}
```

# 91 阿里巴巴找黄金宝箱4：单调栈
循环的难度比一般的mono stack要大，最大值的结果才为-1

```cpp
#include <iostream>
#include <vector>
#include <deque>
using namespace std;

vector<int> solve_method(const vector<int>& nums) {
    deque<int> stack;
    vector<int> res(nums.size(), 0);

    for (size_t i = 0; i < nums.size(); ++i) {
        while (!stack.empty() && nums[stack.front()] < nums[i]) {
            int index = stack.front(); // front is `top`
            stack.pop_front();
            res[index] = nums[i];
        }
        stack.push_front(i);
    }

    while (!stack.empty()) {
        bool flag = false;
        int index = stack.front();
        stack.pop_front();
        for (size_t i = 0; i < nums.size(); ++i) {
            if (nums[i] > nums[index]) {
                flag = true;
                res[index] = nums[i];
                break;
            }
        }
        if (!flag) {
            res[index] = -1;
        }
    }

    return res;
}

int main() {
    vector<int> nums;
    int num;
    char comma;

    while (cin >> num) {
        nums.push_back(num);
        if (cin.peek() == ',') {
            cin >> comma;
        } else {
            break;
        }
    }

    vector<int> result = solve_method(nums);

    for (size_t i = 0; i < result.size(); ++i) {
        if (i > 0) {
            cout << ",";
        }
        cout << result[i];
    }
    cout << endl;

    return 0;
}
```

# 90 阿里巴巴找黄金宝箱3：map, 2-sum

```cpp
#include <iostream>
#include <vector>
#include <unordered_map>
#include <sstream>

using namespace std;

int main() {
    
    // 读取输入的整数数组
    string input;
    getline(cin, input);
    istringstream ss(input);
    string numStr;
    vector<int> nums;
    while (getline(ss, numStr, ',')) {
        nums.push_back(stoi(numStr));
    }
    
    // 读取整数 n
    int n;
    cin >> n;
    
    // 初始化变量
    unordered_map<int, int> lastIndexMap;
    int res = -1;
    
    // 遍历整数数组
    for (int i = 0; i < nums.size(); i++) { // 1-pass
        int currentNum = nums[i];
        
        if (lastIndexMap.find(currentNum) != lastIndexMap.end() && i - lastIndexMap[currentNum] <= n) {
            res = lastIndexMap[currentNum];
            break;
        }
        
        lastIndexMap[currentNum] = i;
    }
    
    // 输出结果
    cout << res << endl;
    
    return 0;
}
```

# 89 阿里巴巴找黄金宝箱2：map, sort

```cpp
#include <iostream>
#include <sstream>
#include <map>
#include <vector>
#include <algorithm>

using namespace std;

int count(string a) {
    stringstream ss(a);
    string item;
    map<string, int> nums;
    vector<int> count_info;
    while (getline(ss, item, ',')) {
        nums[item]++;
    }
    for (auto& v : nums) {
        count_info.push_back(v.second);
    }
    sort(count_info.begin(), count_info.end());
    int size = count_info.size();
    int total = 0;
    int target = nums.size() / 2;
    for (int i = 0; i < size; i++) {
        total += count_info[size - i - 1];
        if (total >= target) {
            return i + 1;
        }
    }
    return 0;
}

int main() {
    string a;
    getline(cin, a);
    cout << count(a) << endl;
    return 0;
}
```

# 88 座位调整：数学

```cpp
#include <iostream>
#include <vector>
#include <cmath>
#include <string>
#include <sstream>

using namespace std;

int main() {
    string input;
    getline(cin, input);

    vector<string> strings;
    stringstream ss(input);
    string item;
    while (getline(ss, item, ',')) {
        strings.push_back(item);
    }

    int length = strings.size();
    vector<int> nums;

    int count = 0; // 0的个数
    bool first = true; // 是否第一段座位
    bool has_one = false; // 全段是否有1

    for (int i = 0; i < length; ++i) {
        string str = strings[i];
        if (str == "1") {
            if (first && count > 1) { // 特殊：开头非0且至少有2个，001可以坐1人,00001可以坐2人
                nums.push_back(count - 1);
            } else if (count > 2) { // 两端有1，1001坐0个，10001坐一个
                nums.push_back(count - 2);
            }
            count = 0;
            first = false;
            has_one = true;
        } else {
            ++count;
        }
        if (i == length - 1 && count > 1) { // 特殊：结尾是0，100坐1个，10000坐2个，000坐2个
            nums.push_back(has_one ? count - 1 : count);
        }
    }

    int result = 0;
    for (int num : nums) {
        result += ceil(num / 2.0); // 这里用了向上取整
    }

    cout << result << endl;

    return 0;
}
```

# 87 食堂供餐：二分搜索

```cpp
#include <iostream>
#include <vector>

using namespace std;

int binary_search_min_speed(int total, int M, int N, const vector<int>& P) {
    int min_speed = 0;
    int max_speed = total - M;
    int result = max_speed;

    while (min_speed <= max_speed) {
        int mid_speed = (min_speed + max_speed) / 2;
        if (can_deliver(mid_speed, M, N, P)) {
            result = mid_speed;
            max_speed = mid_speed - 1;
        } else {
            min_speed = mid_speed + 1;
        }
    }

    return result;
}

bool can_deliver(int speed, int foods, int N, const vector<int>& P) {
    for (int i = 0; i < N; ++i) {
        foods -= P[i];
        if (foods < 0) {
            return false;
        }
        foods += speed;
    }
    return true;
}

int main() {
    int N, M;
    cin >> N >> M;

    vector<int> P(N);
    for (int i = 0; i < N; ++i) {
        cin >> P[i];
    }

    int total = accumulate(P.begin(), P.end(), 0);
    int result = binary_search_min_speed(total, M, N, P);

    cout << result << endl;

    return 0;
}
```

# 86 响应报文时间：位运算

```cpp
#include <iostream>
#include <algorithm>

using namespace std;

int calculate_resp_time(int T, int M) {
    if (M >= 128) { // 最大值为 255
    // exp 最大响应时间的高 5~7 位；mant 为最大响应时间的低 4 位
        int mant = (M >> 3) & 0xF;
        int exp = M & 0x7;
        M = (mant | 0x10) << (exp + 3);
    }
    return T + M;
}

int main() {
    int C, T, M;
    cin >> C;
    int min_resp_time = INT_MAX;

    for (int i = 0; i < C; i++) {
        cin >> T >> M;
        int resp_time = calculate_resp_time(T, M);
        min_resp_time = min(min_resp_time, resp_time);
    }

    cout << min_resp_time << endl;

    return 0;
}
```

# 85 最佳植树距离：二分搜索

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

int min_space(vector<int>& holes, int target) {
    sort(holes.begin(), holes.end());
    int left = 0;
    int right = holes.back() - holes.front();
    int answer = -1;

    while (left <= right) {
        int mid = left + (right - left) / 2;
        int count = 1;
        int previous = holes[0];

        for (size_t i = 1; i < holes.size(); i++) {
            if (holes[i] - previous >= mid) {
                count++;
                previous = holes[i];

                if (count >= target) {
                    answer = mid;
                    left = mid + 1;
                    break;
                }
            }
        }

        if (count < target) {
            right = mid - 1;
        }
    }

    return answer;
}

int main() {
    int n;
    cin >> n;

    vector<int> holes(n);
    for (int i = 0; i < n; i++) {
        cin >> holes[i];
    }

    int target;
    cin >> target;

    int result = min_space(holes, target);
    cout << result << endl;

    return 0;
}
```

# 84 AI 识别面板：sort
题意绕，遇到可先跳过

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

int main() {
    int n;
    cin >> n;

    vector<vector<int>> lights;
    for (int i = 0; i < n; ++i) {
        int id, x1, y1, x2, y2;
        cin >> id >> x1 >> y1 >> x2 >> y2;
        lights.push_back({id, (x1 + x2) / 2, (y1 + y2) / 2, (x2 - x1) / 2});
    }

    sort(lights.begin(), lights.end(), [](const vector<int>& a, const vector<int>& b) {
        return a[2] < b[2];
    });

    vector<int> result;
    int row_start_index = 0;
    for (int i = 1; i < n; ++i) {
        if (lights[i][2] - lights[row_start_index][2] > lights[row_start_index][3]) { // 不满足相同组，把上一组sort一下
            sort(lights.begin() + row_start_index, lights.begin() + i, [](const vector<int>& a, const vector<int>& b) {
                return a[1] < b[1];
            });
            for (int j = row_start_index; j < i; ++j) { // 保存这一组的结果
                result.push_back(lights[j][0]);
            }
            row_start_index = i; // 另起一组
        }
    }

    sort(lights.begin() + row_start_index, lights.end(), [](const vector<int>& a, const vector<int>& b) {
        return a[1] < b[1];
    }); /// don't forget !!!

    for (int i = row_start_index; i < n; ++i) {
        result.push_back(lights[i][0]);
    }

    for (int i = 0; i < result.size(); ++i) {
        cout << result[i] << (i == result.size() - 1 ? '\n' : ' ');
    }

    return 0;
}
```

# 83 矩阵稀疏扫描：模拟

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main() {
    int row, col;
    cin >> row >> col;

    vector<vector<int>> row_mat(row, vector<int>(col));
    vector<vector<int>> col_mat(col, vector<int>(row));

    int row_total = 0;
    int required_zeros = col / 2;
    vector<int> row_zeros(row, 0);

    for (int i = 0; i < row; ++i) {
        for (int j = 0; j < col; ++j) {
            cin >> row_mat[i][j];
            col_mat[j][i] = row_mat[i][j];
            if (row_mat[i][j] == 0) {
                ++row_zeros[i];
            }
        }
        if (row_zeros[i] >= required_zeros) {
            ++row_total;
        }
    }

    cout << row_total << endl;

    int col_total = 0;
    required_zeros = row / 2;
    vector<int> col_zeros(col, 0);

    for (int i = 0; i < col; ++i) {
        for (int j = 0; j < row; ++j) {
            if (col_mat[i][j] == 0) {
                ++col_zeros[i];
            }
        }
        if (col_zeros[i] >= required_zeros) {
            ++col_total;
        }
    }

    cout << col_total << endl;

    return 0;
}
```

# 82 比赛评分：sort, OOP

```cpp
#include<iostream>
#include<vector>
#include<string>
#include<algorithm>
using namespace std;

class Person {
public:
    int id, total;
    vector<int> scores;

    Person(int id, int total, vector<int> scores) : id(id), total(total), scores(scores) {}

    int check_count(vector<int> lst, int count) {
        int cou = 0;
        for (int i : lst) {
            if (i == count) {
                cou++;
            }
        }
        return cou;
    }

    bool operator<(const Person& other) const { // overload!!!
        if (total != other.total) {
            return total > other.total;
        } else {
            const vector<int>& sc_ply_list = other.scores;
            for (int i = 10; i > 0; i--) {
                int ipl = check_count(sc_ply_list, i);
                int ith = check_count(scores, i);
                if (ipl != ith) {
                    return ith > ipl;
                }
            }
        }
        return false;
    }
};

int main() {
    try {
        int jiaolian, xuanshou;
        cin >> jiaolian >> xuanshou;
        if (jiaolian > 10 || jiaolian < 3 || xuanshou > 100 || xuanshou < 3) {
            cout << -1 << endl;
            return 0;
        }

        vector<vector<int>> myList(jiaolian, vector<int>(xuanshou));
        for (int i = 0; i < jiaolian; i++) {
            for (int j = 0; j < xuanshou; j++) {
                cin >> myList[i][j];
            }
        }

        vector<Person> persons;
        for (int i = 0; i < xuanshou; i++) {
            int total = 0;
            vector<int> score_list(jiaolian);
            for (int j = 0; j < jiaolian; j++) {
                int score = myList[j][i];
                if (score < 0 || score > 10) {
                    cout << -1 << endl;
                    return 0;
                }
                score_list[j] = score;
                total += score;
            }
            persons.push_back(Person(i, total, score_list));
        }

        sort(persons.begin(), persons.end());
        for (int i = 0; i < 3; i++) {
            if (i == 2) {
                cout << persons[i].id + 1 << endl;
            } else {
                cout << persons[i].id + 1 << ", ";
            }
        }
    } catch (exception& e) {
        cout << e.what() << endl;
    }
    return 0;
}
```

# 80 双十一：双指针, 3-sum

```cpp
void solve_method(vector<int> &A, int quo)
{
    sort(A.begin(), A.end());
    int N = A.size();
    if (N < 3 || (accumulate(A.begin(), A.begin() + 3, 0) > quo))
    {
        cout << "-1" << endl;
        return;
    }
    int res = INT_MAX;
    for (int i = 0; i < N; ++i)
    {
        if (A[i] >= quo)
        {
            break;
        }
        int l = i + 1, r = N - 1;
        while (l < r)
        {
            int sum = A[i] + A[l] + A[r];
            if (quo > sum && (quo - sum < res - sum))
            {
                res = sum;
            }
            if (sum > quo)
            {
                --r;
            }
            else if (sum < quo)
            {
                ++l;
            }
            else
            {
                cout << quo << endl;
                return;
            }
        }
    }
    cout << res << endl;
}

int main()
{
    string line, s;
    getline(cin, line);
    int quo;
    cin >> quo;
    stringstream ss(line);
    vector<int> A;
    while (getline(ss, s, ','))
    {
        A.push_back(stoi(s));
    }
    solve_method(A, quo);
    return 0;
}
```

# 79 数列还原：模拟, string

```cpp
#include <iostream>
#include <string>
using namespace std;

void solve_method(int n) {
    string content = "1";

    if (n == 0) {
        cout << content << endl;
        return;
    }

    for (int i = 1; i <= n; ++i) {
        string next_content = "";
        char last = content[0]; // !!!
        int count = 1;
        for (size_t j = 1; j < content.size(); ++j) {
            if (content[j] == last) {
                ++count;
            } else {
                next_content += to_string(count) + last;
                count = 1;
                last = content[j];
            }
        }
        next_content += to_string(count) + last; // !!!
        content = next_content;
    }

    cout << content << endl;
}

int main() {
    int n;
    cin >> n;
    solve_method(n);
    return 0;
}
```

# 78 iPv4 地址转换成整数：位运算

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <sstream>

using namespace std;

void solve_method(const string& ip) {
    vector<string> strings;
    stringstream ss(ip);
    string temp;
    while (getline(ss, temp, '#')) {
        strings.push_back(temp);
    }
    
    int length = strings.size();
    long long count = 0;
    bool is_valid = true;
    
    if (length == 4) {
        for (int i = 0; i < length; ++i) {
            int n = stoi(strings[i]);
            if (i == 0 && (n < 1 || n > 128)) {
                is_valid = false;
                break;
            } else if (n < 0 || n > 255) {
                is_valid = false;
                break;
            }
            count += (long long)n << (8 * (3 - i)); // !!!
        }
    } else {
        is_valid = false;
    }
    
    if (is_valid) {
        cout << count << endl;
    } else {
        cout << "invalid IP" << endl;
    }
}

int main() {
    string ip;
    getline(cin, ip);
    solve_method(ip);
    return 0;
}
```

# 75 补种未成活胡杨树：滑动窗口

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

int main() {
    int num_of_dice, num_of_sides;
    cin >> num_of_dice >> num_of_sides;
    
    vector<int> rolls(num_of_dice, 0);
    int k;
    cin.ignore(); // 用于在输入整数后忽略换行符，防止影响后续的字符串输入
    
    for (int i = 0; i < num_of_sides; ++i) {
        int dice_side;
        cin >> dice_side;
        rolls[dice_side - 1] = 1; // withered tree idx
    }
    
    cin >> k;
    
    int left = 0, right = 0, count = 0, result = 0;

    while (right < num_of_dice) {
        while (right < num_of_dice && count <= k) {
            if (rolls[right] == 1) {
                count--;
            }
            right++;
            
            if (count <= k) {
                result = max(result, right - left);
            }
        }
        
        while (left <= right && count > k) {
            if (rolls[left] == 1) {
                count--;
            }
            left++;
        }
    }
    
    cout << result << endl;
    
    return 0;
}
```

# 72 射击比赛：map, sort

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <map>
#include <sstream>
#include <algorithm>

using namespace std;

vector<pair<string, int>> solve_method(int n, vector<string>& ids, vector<int>& nums) {
    map<string, vector<int>> dct;

    for (int i = 0; i < n; i++) {
        dct[ids[i]].push_back(nums[i]);
    }

    for (auto it = dct.begin(); it != dct.end(); ) {
        if (it->second.size() < 3) {
            it = dct.erase(it);
        } else {
            sort(it->second.rbegin(), it->second.rend());
            it->second.resize(3);
            int sum = accumulate(it->second.begin(), it->second.end(), 0);
            it->second.clear();
            it->second.push_back(sum);
            ++it;
        }
    }

    vector<pair<string, int>> ret(dct.begin(), dct.end());
    sort(ret.begin(), ret.end(), [](const pair<string, int>& a, const pair<string, int>& b) {
        return a.second == b.second ? a.first > b.first : a.second > b.second;
    });

    return ret;
}

int main() {
    int n;
    cin >> n;
    cin.ignore(); // cin: +ignore; getline: no need

    string ids_input, nums_input;
    getline(cin, ids_input);
    getline(cin, nums_input);

    stringstream ids_stream(ids_input), nums_stream(nums_input);
    string id_token, num_token;

    vector<string> ids;
    vector<int> nums;

    while (getline(ids_stream, id_token, ',')) {
        ids.push_back(id_token);
    }

    while (getline(nums_stream, num_token, ',')) {
        nums.push_back(stoi(num_token));
    }

    vector<pair<string, int>> ret = solve_method(n, ids, nums);
    for (size_t i = 0; i < ret.size(); i++) {
        cout << ret[i].first;
        if (i != ret.size() - 1) {
            cout << ",";
        }
    }
    cout << endl;

    return 0;
}
```

# 66 字符串加密：fib

```cpp
#include <iostream>
#include <vector>
#include <string>
using namespace std;

void solve_method(const vector<string>& strings) {
    vector<int> a = {1, 2, 4};
    vector<int> offsets(50, 0);
    for (int i = 0; i < offsets.size(); ++i) {
        if (i < 3) {
            offsets[i] = a[i];
        } else {
            offsets[i] = offsets[i - 1] + offsets[i - 2] + offsets[i - 3];
        }
    }

    for (const auto& str_ : strings) {
        string chars = str_;
        for (size_t i = 0; i < chars.size(); ++i) {
            char c = chars[i];
            chars[i] = char((c - 'a' + offsets[i]) % 26 + 'a');  // !!!
        }
        cout << chars << endl;
    }
}

int main() {
    int n;
    cin >> n;
    vector<string> strings(n);
    for (int i = 0; i < n; ++i) {
        cin >> strings[i];
    }
    solve_method(strings);
    return 0;
}
```

# 62 流水线：sort

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

void solve_method(int m, int n, vector<int>& jobs) {
    sort(jobs.begin(), jobs.end());
    if (n <= m) {
        cout << *max_element(jobs.begin(), jobs.end()) << endl;
        return;
    }

    vector<int> res(jobs.begin(), jobs.begin() + m);

    for (int i = m; i < n; i++) {
        auto min_iter = min_element(res.begin(), res.end()); // !!!
        *min_iter += jobs[i];
    }

    cout << *max_element(res.begin(), res.end()) << endl;
}

int main() {
    int m, n;
    cin >> m >> n;
    vector<int> jobs(n);
    for (int i = 0; i < n; i++) {
        cin >> jobs[i];
    }
    solve_method(m, n, jobs);
    return 0;
}
```

# 59 斗地主2: map
比1简单，注意两个顺子间无重复、选最长的，已经输出 `9 10 J Q K A` 就不用重复 `10 J Q K A`

# 58 斗地主1：map, 模拟

```cpp
#include <iostream>
#include <unordered_map>
#include <string>
#include <vector>
using namespace std;

vector<string> graph = {"3", "4", "5", "6", "7", "8", "9", "10", "J", "Q", "K", "A"};
unordered_map<string, int> cards;

void diff(unordered_map<string, int>& cards, string str) {
    int pos = 0;
    while ((pos = str.find("-")) != string::npos) {
        string card = str.substr(0, pos);
        if (cards.find(card) != cards.end()) {
            cards[card]--;
        }
        str.erase(0, pos + 1);
    }
    if (cards.find(str) != cards.end()) { // !!!
        cards[str]--;
    }
}

string find(unordered_map<string, int>& cards) {
    string res = "NO-CHAIN";
    int l = 0, r = 0;
    for (int i = 0; i < graph.size(); i++) {
        string card = graph[i];
        if (cards[card] > 0) {
            l = i;
            while (i + 1 < graph.size() && cards[graph[i + 1]] > 0) {
                i++;
            }
            r = i + 1;
            if (r - l > 5) {
                res = "";
                for (int j = l; j < r; j++) {
                    res += graph[j];
                    if (j < r - 1) {
                        res += "-";
                    }
                }
            }
        }
    }
    return res;
}

void solveMethod(string my, string over) {
    for (auto& card : graph) cards[card] = 4;
    diff(cards, my);
    diff(cards, over);
    string res = find(cards);
    cout << res << endl;
}

int main() {
    string my, over;
    getline(cin, my);
    getline(cin, over);
    solveMethod(my, over);
    return 0;
}
```

# 55 热点网络统计：heap, map

```cpp
#include <iostream>
#include <unordered_map>
#include <string>
#include <vector>
#include <algorithm>

using namespace std;

unordered_map<string, int> top_map;

void solve_method(const string& line) {
    if (line.size() > 2) {
        top_map[line]++;
    } else {
        int n = stoi(line);
        vector<pair<string, int>> sorted_list(top_map.begin(), top_map.end());
        sort(sorted_list.begin(), sorted_list.end(), [](const pair<string, int>& a, const pair<string, int>& b) {
            return a.second > b.second;
        });
        for (int i = 0; i < n; ++i) {
            cout << sorted_list[i].first;
            if (i != n - 1) {
                cout << ",";
            }
        }
        cout << endl;
    }
}

int main() {
    string line;
    while (getline(cin, line)) {
        solve_method(line);
    }
    return 0;
}
```

# 50 找出同班小朋友：set，字符串

```cpp
#include <iostream>
#include <set>
#include <sstream>
#include <vector>

using namespace std;

void solve_method(const string &line) {
    istringstream iss(line);
    vector<string> stus;
    string s;
    while (iss >> s) {
        stus.push_back(s);
    }

    try {
        set<int> c1, c2;
        bool is1 = true;
        for (size_t i = 0; i < stus.size(); ++i) {
            size_t slash = stus[i].find('/');
            int id_ = stoi(stus[i].substr(0, slash));
            char same = stus[i][slash + 1];

            if (i == 0) {
                c1.insert(id_);
                continue;
            }
            if (same == 'N') {
                is1 = !is1;
            }
            (is1 ? c1 : c2).insert(id_);
        }

        for (int i : c1) {
            cout << i << ' ';
        }
        cout << '\n';
        if (!c2.empty()) {
            for (int i : c2) {
                cout << i << ' ';
            }
            cout << '\n';
        }
    } catch (...) {
        cout << "ERROR\n";
    }
}

int main() {
    string line;
    getline(cin, line);
    solve_method(line);
    return 0;
}
```

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

# 18 小朋友排队：排序

偏简单，注意 lambda `[&h](const...)`


# 17 喊 7 的次数重排、喊七：模拟
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

# 16 找最小数：栈
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

# 15 组成最大数、卡片组成的最大数字：排序
```cpp
static bool cmp(int a, int b){
        string A = to_string(a) + to_string(b);
        string B = to_string(b) + to_string(a);
        return A < B; // !
    }
```

# 5 阿里巴巴找黄金宝箱：prefix sum
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

# 4 选修课：排序，模拟
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

# 3 五子棋迷
细节太多，中心扩散、找最长连续子串
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

# 2 模拟编码
真碰到这道题的话，放到最后做，细节太多
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