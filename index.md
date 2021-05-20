## 滑动窗口

滑动窗口本质上是一个队列，比如例题中的 abcabcbb，进入这个队列（窗口）为 abc 满足题目要求，当再进入 a，队列变成了 abca，这时候不满足要求。所以，我们要移动这个队列！

如何移动？我们只要把队列的左边的元素移出就行了，直到满足题目要求！一直维持这样的队列，找出队列出现最长的长度时候，求出解！

``` 滑动窗口
- 滑动窗口算法的框架
void slidwindow(vector<int> nums)
{
    int left = 0, right = 0;
    while(right < nums.size())
    {
        ...//扩大右边界并更新窗口状态
        right++;
        while(需要收缩)//窗口到达什么状态需要收缩
        {
            ...//缩小左边界并更新窗口状态
            left++;
        }
    }
}

int slidingWindow(vector<int> nums) {
    int n = nums.size();
    int ans = 0;
    // 记录窗口内的元素及其个数，非必要
    map<int, int> um;
    // l：窗口左边界； r：窗口右边界
    int l = 0, r = 0;
    // r 指针负责探索新的区间，直到搜索到nums的某末尾
    while (r < n) {
        um[r]++;
        // 如果区间不满足条件，l指针右移，窗口收缩
        while(区间 [l, r] is Invalid) {
            um[l]--;
            l++;
        }
        // 此处处理结果, deal with(ans, 区间[l, r])
        res = max(ans, r - l + 1); // 或者res = min(ans, r - l + 1);
        // 右指针右移，继续搜索
        r++;
    }
    return ans;
}
```

### 时间复杂度：O(n)

## 相关题目

### 1.无重复字符的最长子串
``` 无重复字符的最长子串
- 依次将字符串插入set，并判断当前set中是否含有重复值，如果有重复值，将该重复值以及其之前的值出队

class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        if(s.size() == 0) return 0;
        unordered_set<char> lookup;    //选定一个结构作为滑动窗口的队列
        int maxStr = 0;
        int left = 0;
        for(int i =0; i< s.length(); i++)
        {
            while(lookup.find(s[i]) != lookup.end())
            {
                lookup.earse(s[left]);
                left++;
            }
            maxStr = max(maxStr, i - left + 1);
            lookup.insert(s[i]);
        }
    }
};
```
### 2.最长覆盖子串
``` 最长覆盖子串
class Solution {
public:
    string minWindow(string s, string t) {
        vector<int> need(128,0);
        int count = 0;  
        for(char c : t)
        {
            need[c]++;
        }
        count = t.length();
        int l=0, r=0, start=0, size = INT_MAX;
        while(r<s.length())
        {
            char c = s[r];
            if(need[c]>0)
                count--;
            need[c]--;  //先把右边的字符加入窗口
            if(count==0)    //窗口中已经包含所需的全部字符
            {
                while(l<r && need[s[l]]<0) //缩减窗口
                {
                    need[s[l++]]++;
                }   //此时窗口符合要求
                if(r-l+1 < size)    //更新答案
                {
                    size = r-l+1;
                    start = l;
                }
                need[s[l]]++;   //左边界右移之前需要释放need[s[l]]
                l++;
                count++;
            }
            r++;
        }
        return size==INT_MAX ? "" : s.substr(start, size);
    }
};
```
### 30.串联所有单词的子串
``` 串联所有单词的子串
class Solution {
public:
    vector<int> findSubstring(string s, vector<string>& words) {
        vector<int> res;
        int n = s.size();

        if(n<=0 || words.empty())
        {
            return res;
        }

        int wordSize = words[0].size();              //根据题意，word中单词的长度固定
        int count = words.size();                    //word中字符串的数量

        unordered_map<string, int> need;
        for(auto &s:words)
        {
            ++need[s];                              //使用map纪录word中所有的字符串及个数
        }

        for (int i = 0; i < wordSize; ++i)
        {
            int left = i;
            int right = i;
            int cnt = 0;

            unordered_map<string, int> currMap;

            while(right + wordSize <= n)
            {
                string temp = s.substr(right, wordSize);
                right+=wordSize;

                if(need.find(temp) != need.end())
                {
                    ++currMap[temp];
                    ++cnt;

                    while(currMap[temp] > need[temp])
                    {
                        string ts = s.substr(left, wordSize);
                        left += wordSize;
                        --cnt;
                        --currMap[ts];
                    }

                    if(cnt == count)
                    {
                        res.push_back(left);
                    }
                }
                else
                {
                    left = right;
                    cnt = 0;
                    currMap.clear();
                }
            }
        }

        return res;
    }
};
```
### 209.长度最小的子数组
``` 长度最小的子数组
class Solution {
public:
    int minSubArrayLen(int target, vector<int>& nums) {
        int size = nums.size();
        if(size == 0)
        {
            return 0;
        }

        int maxStr = INT_MAX;
        int result = 0;

        int left = 0, right = 0, start = 0;

        while(right < size)
        {
            result += nums[right];
            right++;

            while(result >= target)
            {
                maxStr = min(maxStr, right-left);
                result -= nums[left];
                left++;
            }
        }
        return maxStr == INT_MAX? 0:maxStr;
    }
};
```

### 面试题17.18.最短超串
``` 最短超串
假设你有两个数组，一个长一个短，短的元素均不相同。找到长数组中包含短数组所有的元素的最短子数组，其出现顺序无关紧要。

返回最短子数组的左端点和右端点，如有多个满足条件的子数组，返回左端点最小的一个。若不存在，返回空数组。

示例 1:

输入:
big = [7,5,9,0,2,1,3,5,7,9,1,1,5,8,8,9,7]
small = [1,5,9]
输出: [7,10]

    vector<int> shortestSeq(vector<int>& big, vector<int>& small) {
        int n = big.size();
        vector<int> res;
        unordered_map<int, int> need;
        int minlen =n, diff = 0;
        for(auto &e:small)
        {
            ++need[e];
            diff++;
        }

        int l =0, r = 0;
        for(; r<n; r++)
        {
            if(need.find(big[r]) != need.end() && --need[big[r]] >= 0) --diff;
            while(diff == 0)
            {
                if(r-l < minlen)
                {
                    minlen = r-l;
                    res={l, r};
                }
                if(need.find(big[l]) != need.end() && ++need[big[l]] > 0) ++diff;
                ++l;
            }
        }

        return res;
    }

```

### 395.至少有k个重复字符的最长子串
``` 至少有k个重复字符的最长子串
- 采用分治+哈希的方法

int longestSubstring(string s, int k) {
    int n = s.size();
    if (k < 2) return n;
    if (n < k) return 0;
    int m[26] = {0};
    for (auto c : s) 
        ++m[c-'a'];
    int i = 0;
    while (i < n && m[s[i]-'a'] >= k) 
        ++i;
    if (i == n) 
        return n;
    int left = longestSubstring(s.substr(0, i), k);
    while (i < n && m[s[i]-'a'] < k) 
        ++i;
    int right = longestSubstring(s.substr(i), k);

    return max(left, right);
}
```

### Markdown

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/liuhao112/LeetCode-String/settings/pages). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://support.github.com/contact) and we’ll help you sort it out.
