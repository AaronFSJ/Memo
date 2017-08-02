# 单纯使用Shiro的流程图 #
tags: shiro

## 使用案例 ##
- 首次请求页面： `/user`
- 登入后默认跳转页面： `/index`
- 没有权限：`/403`
- `/index`和`/user`均为受保护请求

```flow
st=>start:  浏览器请求`/user`
op1=>operation: ShiroFilter
cond1=>condition: 是否已经登入认证？
op2=>operation: AuthorizaingRealm权限认证
cond2=>condition: 是否有权限？
cond3=>condition: 是否的登入？
op3=>operation: 登入页面，提交登入
op4=>operation: LoginController/doLogin()
op5=>operation: AuthorizaingRealm权限认证
cond4=>condition: 认证通过？
e1=>end: 页面`/403`
e2=>end: 响应页面`/user`
e3=>end: 响应页面`/index`

st->op1->cond1
cond1(no)->op3->op4->op5->cond5
cond5(no)->op3
cond5(yes)->e3
cond1(yes)->op2->cond2
cond2(no)->e1
cond2(yes)->cond3
cond3(no)->e3
cond3(yes)->e2
```
