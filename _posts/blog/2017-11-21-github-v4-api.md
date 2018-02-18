---
layout:     post
title:    "使用python调用github v4 api"
category: blog
description: 使用python调用github v4 api
author:     "bluebird"
date:     2017-11-21
    - 编程技术
thumb: https://avatars2.githubusercontent.com/u/31133259?s=400&v=4
---

### github api v4
github api v4使用了 GraphQL 查询,GraphQL是由Facebook于2012年在内部开发的一种数据查询语言

v4仍然在开发中,功能还在更改.[官方英文教程](https://developer.github.com/v4/)
现有资料也比较少 所以作者踩了一堆坑

#### 获得github api token
打开 [setting](https://github.com/settings/profile)>>[developers setting](https://github.com/settings/developers)>>[Personal access tokens](https://github.com/settings/tokens)
选择生成一个token 输入密码 复制token 
github为了安全 离开界面后不会再显示

#### github自带测试功能
github为我们提供了一个api测试工具 [点我](https://developer.github.com/v4/explorer/)
<!-- more -->
#### 使用python完成api请求准备
环境`python3`和`requests`
```python
# api地址
url = 'https://api.github.com/graphql'
# 你的token
key = '*******'
# 用token组成认证使用的
userheads = {
            "Authorization": "Bearer {}".format(token),
        } 
```
#### GraphQL 语法
在GraphQL中，无论您是查询和更改 都必须使用json
github的curl使用官方示例
```bash
curl -H "Authorization: bearer token" -X POST -d " \
 { \
   \"query\": \"query { viewer { login }}\" \
 } \
" https://api.github.com/graphql
```
`query { *** }`就是我们的查询语句 要使用查询需要一直输入到标量 如果用代码 不太恰当的来形容的就是一个多级字典 
```python
test = {
 'a':{
 'b':{
 'd'
 },
 'c':{
 'e'
 }
 },
}
```
如果要查询到e 在python就要使用`test['a']['c']['e']`
在GraphQL就是
```
query{
  a{
   c{
     e
   }
  }
}
```

#### requests查询
示例
```python
userheads = {
            "Authorization": "Bearer {}".format(token),
        } 
url = 'https://api.github.com/graphql'
# 你的查询
query = {"query": "query { viewer { login }}"}
req = requests.post(url, headers=userheads, json=query)
# 返回的也是json
result = json.loads(req.content)
print(result["data"]["viewer"]["login"])
```

#### 条件查询
查询自己名字是不需要限制的 但是查询储藏库必须要带上条件 不然一查名字就是全部储藏库
添加条件只需要在查询字段前添加一对键 就算是字符值也不能使用单引号
```
test(name:1)
test(name:"233")
```

#### 查看可查询信息 
github的官方信息 [点我](https://developer.github.com/v4/reference/)

#### 示例代码
我因为懒所以写了n久都没写一点的项目 [点我](https://github.com/blue-bird1/githubapi)

