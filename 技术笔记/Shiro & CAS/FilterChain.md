# FilterChain #
tags: shiro spring
## Filter Chain定义说明 ##
- 一个URL可以配置多个Filter，使用逗号隔开
- 当设置多个过滤器时，全部通过才视为通过
- 部分参数可以指定参数，如perms，roles

## 内容的FilterChain ##

| Filter Name | Class |
| :---------- | :---- |
| anon | org.apache.shiro.web.filter.authc.AnonymousFilter |
| authc | org.apache.shiro.web.filter.authc.FormAuthenticationFilter |
| authcBasic | org.apache.shiro.web.filter.authc..BasicHttpAuthenticationFilter|
| perms | org.apache.shiro.web.filter.authc.PermissionsAuthorizationFiler|
| port | org.apache.shiro.web.filter.authc.PortFilter |
| rest | org.apache.shiro.web.filter.authc.HttpMethodPermisssionFilter |
| roles | org.apache.shiro.web.filter.authc.RolesAuthorizationFilter |
| ssl | org.apache.shiro.web.filter.authz.SslFilter |
| user | org.apache.shiro.web.filter.authc.UserFilter |
