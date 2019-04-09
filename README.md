# swoft-design

swoft framework design draft

- [swoft 2.0](swoft-v2/README.md)

## 开发规范

- 代码风格遵循 PSR-2
- 遵循 PSR-4 自动加载规范 
- 有完善的单元测试
- 功能描述


## Commit Message 格式

目前规范使用较多的是 Angular 团队的规范, 继而衍生了 Conventional Commits specification. 很多工具也是基于此规范, 它的 message 格式如下:

```text
<type>(<scope>): <subject>
<BLANK LINE>
<body>
<BLANK LINE>
<footer>
```

我们通过 git commit 命令带出的 vim 界面填写的最终结果应该类似如上这个结构, 大致分为三个部分(使用空行分割):

标题行: 必填, 描述主要修改类型和内容
主题内容: 描述为什么修改, 做了什么样的修改, 以及开发的思路等等
页脚注释: 放 Breaking Changes 或 Closed Issues

分别由如下部分构成:

```text
type: commit 的类型
 feat: 新特性
 fix: 修改问题
 refactor: 代码重构
 docs: 文档修改
 style: 代码格式修改, 注意不是 css 修改
 test: 测试用例修改
 chore: 其他修改, 比如构建流程, 依赖管理.
scope: commit 影响的范围, 比如: route, component, utils, build...
subject: commit 的概述, 建议符合 50/72 formatting
body: commit 具体修改内容, 可以分为多行, 建议符合 50/72 formatting
footer: 一些备注, 通常是 BREAKING CHANGE 或修复的 bug 的链接.
```
