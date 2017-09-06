遍历HashMap详解

------


## 总结

常见的遍历HashMap的方式一共有以下几种：
1. `for iterator entrySet`
2. `foreach entrySet`
3. `for entrySet=entrySet()`
4. `foreach keySet`

- 如果需要遍历Key-value，使用`for iterator entrySet`方式，且支持entry的删除；
- 如果仅遍历Key，使用`for each keySet`。

## 具体实现
### for iterator entrySet

```java
Iterator<Map.Entry<String, String>> iterator = map.entrySet().iterator();
while (iterator.hasNext()) {
	Map.Entry<String, String> entry = iterator.next();
	entry.getKey();
	entry.getValue();
}
```

### foreach entrySet
```java
Map<String, String> map = new HashMap<String, String>();
for (Entry<String, String> entry : map.entrySet()) {
	entry.getKey();
	entry.getValue();
}
```

### for entrySet=entrySet()
```java
Set<Entry<String, String>> entrySet = map.entrySet();
for (Entry<String, String> entry : entrySet) {
	entry.getKey();
	entry.getValue();
}
```

### foreach keySet
```java
Map<String, String> map = new HashMap<String, String>();
for (String key : map.keySet()) {
	map.get(key);
}
```