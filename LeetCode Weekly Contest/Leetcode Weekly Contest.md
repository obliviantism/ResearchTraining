## Week 417
[3304. 找出第 K 个字符 I](https://leetcode.cn/problems/find-the-k-th-character-in-string-game-i/)
Alice 和 Bob 正在玩一个游戏。最初，Alice 有一个字符串 `word = "a"`。
给定一个**正整数** `k`。
现在 Bob 会要求 Alice 执行以下操作 **无限次** :
- 将 `word` 中的每个字符 **更改** 为英文字母表中的 **下一个** 字符来生成一个新字符串，并将其 **追加** 到原始的 `word`。
例如，对 `"c"` 进行操作生成 `"cd"`，对 `"zb"` 进行操作生成 `"zbac"`。
在执行足够多的操作后， `word` 中 **至少** 存在 `k` 个字符，此时返回 `word` 中第 `k` 个字符的值。
**注意**，在操作中字符 `'z'` 可以变成 `'a'`。

Limits
 * `1 <= k <= 500`

Data Structure: string

Code：
```cpp
class Solution {
public:
    char kthCharacter(int k) {
        string str = "a";
        while(str.length() < k){
            string substr = "";
            for(int j = 0; j < str.length(); j ++){
                if(str[j] < 'z')
                    substr += str[j] + 1;
                else 
                    substr += 'a';
            }
            str += substr;
        }
        return str[k - 1];
    }
};
```


Notes
模拟题，按照题意，设置字符串 str 和临时变量 substr，外循环重复 k 次，内循环重复当前字符串 str 的长度次，把 substr 的每个字符加 1 并判断是否需要掉头转为 a，内循环结束后加到 str 中，结束后返回 str 中第 k 个字符。

[3307. 找出第 K 个字符 II](https://leetcode.cn/problems/find-the-k-th-character-in-string-game-ii/)
The same condition as 3304

Limits (more strict)
- `1 <= k <= 1014`
- `1 <= operations.length <= 100`
- `operations[i]` 可以是 0 或 1。
- 输入保证在执行所有操作后，`word` 至少有 `k` 个字符。

Data structure: vector (list)

Algorithm: Deep First Search

Note

每一次操作，字符串长度倍增，所以不需要把所有操作做完，只需要做到当长度大于 k 时的操作即可。所以第一步先求出做到字符串长度大于等于 k 时最少需要多少次操作。因为 k 也过大，所以不能简单用空间换时间，需要递归。
定义递归函数 $dfs(operations, n, k, i),$ 最后得到偏移量为 $'a'+dfs ()$, 其中 n 为当前字符串长度，k 为要求的字符串索引，i 为当前操作索引。递归出口为字符串长度为 0 或者操作索引<0 时，偏移量为 0。k 分两种情况: 当 k 在字符串前半部分时，倍增与否和 k 的值无关，偏移量等于上一个操作得到的结果 $dfs(operations, n/2, k, i - 1)$；当 k 在字符串后半部分时，倍增的值是上一个操作 $dfs(operation, n/2, k - n/2, i - 1)$ (k-n/2表示当前第k个字符是由上一个字符的第k−n​/2个字符转化而来)又加了 $operation[i]$ 的偏移量

Code
```cpp
typedef long long LL;

class Solution {
public:

    char kthCharacter(long long k, vector<int>& operations) {
        LL n = 2;//字符串长度
        int cnt = 0;
        while(n < k)
        {
            n <<= 1;
            cnt ++;
        }
        //dfs寻找偏移量的函数，n为字符串长度，k为第k个字符，cnt - 1为操作次数
        int offset = dfs(operations, n, k, cnt) % 26;
        return 'a' + offset;
        
    }
        int dfs(vector<int>& operations, LL n, LL k, int i)
    {
        if(i < 0 || n == 0) return 0;
        if(k <= n / 2)
        {
            return dfs(operations, n / 2, k, i - 1);
        }
        else return operations[i] + dfs(operations, n/2, k - n/2, i - 1);
    }

};
```



[3305. 元音辅音字符串计数 I](https://leetcode.cn/problems/count-of-substrings-containing-every-vowel-and-k-consonants-i/)
给你一个字符串 `word` 和一个 **非负** 整数 `k`。
返回 `word` 的子字符串中，每个元音字母（`'a'`、`'e'`、`'i'`、`'o'`、`'u'`）**至少** 出现一次，并且 **恰好** 包含 `k` 个辅音字母的子字符串的总数。

Limits
- `5 <= word.length <= 250`
- `word` 仅由小写英文字母组成。
- `0 <= k <= word.length - 5`

Data Structure: String, unordered set (HashTable)

Code
```cpp
class Solution {
public:
    bool isVowel(char ch)
    {
        if(ch == 'a' || ch == 'e' || ch == 'i' || ch == 'o' || ch =='u')
            return true;
        return false;
    }
    int countOfSubstrings(string word, int k) 
    {
        int cnt = 0, n = word.size();
        if(n < 5 + k) return cnt;
        int i = 0;
        for(; i < n; i++)
        {
            int count_consonant = 0;
            unordered_set<char> vowelSet;
            for(int end = i; end < n; end ++)
            {
                char ch = word[end];
                if(isVowel(ch))
                {
                    vowelSet.insert(ch);
                }
                else
                {
                    count_consonant ++;
                }

                if(count_consonant == k && vowelSet.size() == 5) cnt ++;
                if(count_consonant > k) break;
            }
        }
        return cnt;
    }
};
```

Notes
IsVowel 判断是否为元音，如果是，把该字母加入 vowel hashtable 中，并在读取每个子字符串时判断是否满足条件：辅音个数 == k && 元音种类等于 5，如果在元音种类小于 5 时辅音个数已经大于 k，退出循环。



[3306. 元音辅音字符串计数 II](https://leetcode.cn/problems/count-of-substrings-containing-every-vowel-and-k-consonants-ii/)
The Same Condition as 3305

Limits (more strict)
- `5 <= word.length <= 2 * 105`
- `word` 仅由小写英文字母组成。
- `0 <= k <= word.length - 5`


Data Structure: string, unordered map (HashTable)

Algorithm: Sliding Window

Notes
* 滑动窗口解决，$O(n^2) \longrightarrow O(n)$, 
* 每个滑动窗口得到辅音字母大于等于 k 的合法子字符串个数，内部遍历 $2n$ 次，外部调用 2 次
* 神来之笔：大于等于 k 个辅音字母的字符串个数 - 大于等于 k + 1个辅音字母的字符串个数 = 等于 k 个辅音字母的字符串个数
* slide_window 具体分析：l, r 指针为左右端点，r 指针先遍历到末尾，得出整个字符串中元音分别的个数，遍历的过程中进行条件判断，如果出现满足条件的子字符串，移动左指针直到不合法（这里条件判断不能等于 k，否则答案会出错，我的理解是，该题滑动窗口解决的是一个二分问题，即符合条件的子串个数，当出现不等于 k 的情况时，可能会出现大于 k 和小于 k 两种情况，滑动窗口的右侧是固定的，所以不能找到所有合法的情况，会相应地漏掉一些子串），此时移动的个数即为合法的字符串个数，累加到答案中即可

Code:
```cpp
typedef long long LL;
class Solution {
public:

    LL slide_window(string word, int k)//返回大于等于k个辅音字母的合法子字符串个数
    {
        LL res = 0;
        int n = word.size();
        int l = 0;
        unordered_map<char, int> vowelHash;
        vowelHash['a'] = 0, vowelHash['e'] = 0, vowelHash['i'] = 0, vowelHash['o'] = 0, vowelHash['u'] = 0;
        
        for(int r = 0; r < n; r ++){
            if(word[r] == 'a' || word[r] == 'e' ||word[r] == 'i' ||word[r] == 'o' ||word[r] == 'u') vowelHash[word[r]] ++;
            while((r - l + 1 - vowelHash['a'] - vowelHash['e'] - vowelHash['i'] - vowelHash['o'] - vowelHash['u'] >= k) && (vowelHash['a'] > 0) && (vowelHash['e'] > 0)  && (vowelHash['i'] > 0)  && (vowelHash['o'] > 0)  && (vowelHash['u'] > 0) )
            {
               if(word[l] == 'a' || word[l] == 'e' ||word[l] == 'i' ||word[l] == 'o' ||word[l] == 'u') vowelHash[word[l]] --; 
               l ++;
            }
            res += l;
        }
        return res;
    }

    long long countOfSubstrings(string word, int k) {
        return slide_window(word, k) - slide_window(word, k + 1);
    }
};
```


