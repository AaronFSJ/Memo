# Spring Boot Configuratoin 刷新方式 #
tags: spring boot, refresh
## 文件服务 ##
- {application} 在客户端映射到`spring.application.name`
- {profile} 在客户端功能标签`spring.active.profiles`, 使用逗号隔开
- {label} 服务端功能标签**版本**的一组配置文件
## 自动刷新 ##
当环境变量参数通过`git`更新后，客户端自动更新的方式如下：

> 1. 可以使用`post/refresh` 来执行，客户端调用更新功能。
> 2. 使用定时器执行，每个一段时间更新一次
> 3. 通过`Spring Cloud Bus`更新
> 4. 其他广播形式

```java
/**
 * 手动刷新git更新方案
 */
public void refresh() {
  Map<String, Object> before = extract(context.getEnvironment().getPropertySources());
  this.addConfigFilesToEnvironment();
  Set<String> keys = changes(before, extract(context.getEnvironment().getPropertySources()));
  scope.refreshAll();
  context.publishEvent(new EnvironmentChangeEvent(keys));
}

private void addConfigFilesToEnvironment() {
  ConfigurableApplicationContext capture = null;
  try {
    capture = new SpringApplicationBuilder(Empty.class).showBanner(false)
				.web(false).environment(context.getEnvironment()).run();
		MutablePropertySources target = context.getEnvironment().getPropertySources();
		for (PropertySource<?> source : capture.getEnvironment().getPropertySources()) {
			String name = source.getName();
			if (!standardSources.contains(name)) {
				if (target.contains(name)) {
					target.replace(name, source);
				}
				else {
					if (target.contains("defaultProperties")) {
						target.addBefore("defaultProperties", source);
					}
					else {
						target.addLast(source);
					}
				}
			}
		}
  } finally {
		while (capture != null) {
			capture.close();
			ApplicationContext parent = capture.getParent();
			if (parent instanceof ConfigurableApplicationContext) {
				capture = (ConfigurableApplicationContext) parent;
			} else {
				capture = null;
			}
		}
	}
}
private Map<String, Object> changes(Map<String, Object> before,
									Map<String, Object> after) {
	Map<String, Object> result = new HashMap<String, Object>();
	for (String key : before.keySet()) {
		if (!after.containsKey(key)) {
			result.put(key, null);
		}
		else if (!equal(before.get(key), after.get(key))) {
			result.put(key, after.get(key));
		}
	}
	for (String key : after.keySet()) {
		if (!before.containsKey(key)) {
			result.put(key, after.get(key));
		}
	}
	return result;
}
private boolean equal(Object one, Object two) {
	if (one == null && two == null) {
		return true;
	}
	if (one == null || two == null) {
		return false;
	}
	return one.equals(two);
}
private Map<String, Object> extract(MutablePropertySources propertySources) {
	Map<String, Object> result = new HashMap<String, Object>();
	for (PropertySource<?> parent : propertySources) {
		if (!standardSources.contains(parent.getName())) {
			extract(parent, result);
		}
	}
	return result;
}
private void extract(PropertySource<?> parent, Map<String, Object> result) {
	if (parent instanceof CompositePropertySource) {
		try {
			for (PropertySource<?> source : ((CompositePropertySource) parent)
					.getPropertySources()) {
				extract(source, result);
			}
		}
		catch (Exception e) {
			return;
		}
	}
	else if (parent instanceof EnumerablePropertySource) {
		for (String key : ((EnumerablePropertySource<?>) parent).getPropertyNames()) {
			result.put(key, parent.getProperty(key));
		}
	}
}
@Configuration
protected static class Empty {
}
```
