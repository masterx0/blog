# FastJson主要方法toJsonString和parseObject实现思想

## 1.toJsonString

```
1.new 一个序列化写对象SerializeWriter

2.通过该SerializeWriter对象创建Json序列化对象JSONSerializer

3.为该JSONSerializer对象配置日期格式dateFormat和config

4.如存在序列化过滤器SerializeFilter则为该JSONSerializer对象添加相应Filter

5.调用JSONSerializer的write方法将Object写为Json字符串
```

## 2.parseObject

```
1.原始Json字符串根据config和featureValues调用DefaultJSONParser生成parse对象

2.parse对象调用parseObject方法，参数为目标类

3.parseObject方法内部调用ObjectDeserializer的deserialze方法解析

4.返回目标类的一个实例
```



