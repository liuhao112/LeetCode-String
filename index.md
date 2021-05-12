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

作者：bu-zhi-yin
链接：https://leetcode-cn.com/problems/minimum-size-subarray-sum/solution/209chun-chun-de-hua-dong-chuang-kou-by-b-a92x/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
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
