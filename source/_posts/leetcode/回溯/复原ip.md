---
title: 复原ip
date: 2020-09-25 14:19:41
categories:
- leetcode
---
```js
/**
 * @param {string} s
 * @return {string[]}
给定一个只包含数字的字符串，复原它并返回所有可能的 IP 地址格式。
有效的 IP 地址正好由四个整数（每个整数位于 0 到 255 之间组成），整数之间用 '.' 分隔。
示例:
输入: "25525511135"
输出: ["255.255.11.135", "255.255.111.35"]
 */
var restoreIpAddresses = function (s) {
  if (s.length > 12 || s.length < 4) {
    return []
  }
  let res = []
  let dfs = (subRes, start) => {
    // 有四个满足条件的子串，并且遍历到底了作为一种结果
    if (subRes.length === 4 && start === s.length) {
      res.push(subRes.join('.'))
      return
    }
    // 有四个满足条件的子串，并且未遍历，那么不满足条件退出
    if (subRes.length === 4 && start < s.length) {
      return
    }
    for (let len = 1; len <= 3; len++) {
      let str = s.substring(start, start + len)
      // 长度大于1并以0开头，不满足
      if (len > 1 && s[start] == 0) return
      // 大于255，不满足
      if (len === 3 && str > 255) return
      // 超出边界，不满足
      if (s[start + len - 1] == undefined) return
      // 将该子串作为结果保留
      subRes.push(str)
      // 遍历剩下的子串
      dfs(subRes, start + len)
      // 如果没有找到满足条件的四个子串，证明上一步保留的字符串无法构成ip字符，回撤，继续下个for循环
      subRes.pop()
    }
  }
  dfs([], 0)
  return res
}
```
