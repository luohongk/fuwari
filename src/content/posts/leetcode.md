---
title: LeetCode接雨水题目与解法
published: 2026-01-19T12:02:23.000Z
description: 本文展示leetcode接雨水题目解法
tags:
  - 技术分享
  - 关于编程
category: 技术分享类
draft: false
---

[🌐 接雨水题目地址](https://leetcode.cn/problems/trapping-rain-water/description/?envType=study-plan-v2&envId=top-100-liked)

![image.png](https://raw.githubusercontent.com/luohongk/picgo-img/main/20260118200922725.png)

## 1 我的思路，先计算左半边，然后再计算右半边

![image.png](https://raw.githubusercontent.com/luohongk/picgo-img/main/20260118200517654.png)



```C++
class Solution {
public:
    int trap(vector<int>& height) {
        int N = height.size();
        if (N == 0) return 0;

        int water = 0;
        int sum = 0;

        // 使用 std::max_element 找到最大值的迭代器
        auto maxIt = max_element(height.begin(), height.end());
        int maxValue = *maxIt;
        int maxIndex = distance(height.begin(), maxIt);

        // 计算左侧区域
        int i = 0;
        while (i < maxIndex) {
            int step = 0;
            while (i + step + 1 < maxIndex && height[i] <= height[i + step + 1]) {
                step++;
            }
            sum += step * height[i];
            i += step;
        }

        // 计算右侧区域
        int j = N - 1;
        while (j > maxIndex) {
            int step = 0;
            while (j - step - 1 > maxIndex && height[j] <= height[j - step - 1]) {
                step++;
            }
            sum += step * height[j];
            j -= step;
        }

        // 计算总高度
        int sum_height = accumulate(height.begin(), height.end(), 0);

        // 计算接水量
        water = sum - (sum_height - maxValue);
        return water;
    }
};
```


## 2 使用动态规划优化时间复杂度

![image.png](https://raw.githubusercontent.com/luohongk/picgo-img/main/20260118200541018.png)


```C++
class Solution {
public:
    int trap(vector<int>& height) {
        int N = height.size();
        if (N == 0) return 0;

        vector<int> leftMax(N);
        vector<int> rightMax(N);

        // 计算左侧最大高度
        leftMax[0] = height[0];
        for (int i = 1; i < N; i++) {
            leftMax[i] = max(leftMax[i - 1], height[i]);
        }

        // 计算右侧最大高度
        rightMax[N - 1] = height[N - 1];
        for (int i = N - 2; i >= 0; i--) {
            rightMax[i] = max(rightMax[i + 1], height[i]);
        }

        // 计算接雨水的总量
        int water = 0;
        for (int i = 0; i < N; i++) {
            water += min(leftMax[i], rightMax[i]) - height[i];
        }

        return water;
    }
};
```




## 3 双指针解法

![image.png](https://raw.githubusercontent.com/luohongk/picgo-img/main/20260118200608993.png)


```C++
class Solution {
public:
    int trap(vector<int>& height) {
        int N = height.size();
        if (N == 0) return 0;

        int left = 0, right = N - 1;
        int leftMax = 0, rightMax = 0;
        int water = 0;

        while (left <= right) {
            // 更新左右边界的最大高度
            if (height[left] < height[right]) {
                if (height[left] >= leftMax) {
                    leftMax = height[left];
                } else {
                    water += leftMax - height[left];
                }
                left++;
            } else {
                if (height[right] >= rightMax) {
                    rightMax = height[right];
                } else {
                    water += rightMax - height[right];
                }
                right--;
            }
        }

        return water;
    }
};