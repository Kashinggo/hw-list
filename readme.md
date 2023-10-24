
20 位运算，数组遍历，贪心

```cpp

```

18 排序

偏简单，注意 lambda `[&h](const...)`


17 模拟
```cpp
#include <iostream>
#include <vector>
#include <sstream>

void solve_method(const std::string& line) {
    std::istringstream iss(line);
    int sum_ = 0;
    int n;
    std::vector<int> nums;

    while (iss >> n) {
        sum_ += n;
        nums.push_back(n);
    }

    std::vector<int> res(nums.size(), 0);

    int j = 0;
    for (int i = 1; i < 300; ++i) {
        if (j == nums.size()) {
            j = 0;
        }
        if (i % 7 == 0 || std::to_string(i).find('7') != std::string::npos) {
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
        std::cout << res[i];
        if (i < res.size() - 1) {
            std::cout << " ";
        }
    }
    std::cout << std::endl;
}

int main() {
    std::string line;
    std::getline(std::cin, line);
    solve_method(line);
    return 0;
}
```

有两题跳过了，在宿舍做的，双指针

16 栈
```cpp
#include <iostream>
#include <vector>
#include <string>

std::vector<char> solve_method(const std::string& num1, int num2) {
    std::vector<char> res_list;
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
    std::string n1;
    int n2;
    std::cin >> n1;
    std::cin >> n2;
    std::vector<char> result = solve_method(n1, n2);
    // 删除前导零
    size_t non_zero_index = 0;
    while (non_zero_index < result.size() && result[non_zero_index] == '0') {
        non_zero_index++;
    }
    if (non_zero_index == result.size()) {
        std::cout << "0" << std::endl;
    } else {
        for (size_t i = non_zero_index; i < result.size(); i++) {
            std::cout << result[i];
        }
        std::cout << std::endl;
    }
    return 0;
}
```

15 排序
```cpp

static bool cmp(int a, int b){
        string A = to_string(a) + to_string(b);
        string B = to_string(b) + to_string(a);
        return A < B; // !
    }
```

3 细节太多，中心扩散、找最长连续子串
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


2 模拟编码；真碰到这道题的话，放到最后做，细节太多
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

5 prefix sum
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

4 排序，模拟
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