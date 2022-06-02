---
title: "推送监控数据（OpenFalcon协议）"
weight: 19
---

调用 n9e-server 的 `/openfalcon/push` 接口，POST 方法，该接口实现了 Open-Falcon 的数据接收协议，监控数据做成 JSON 放到 HTTP Request Body 中，举例：

```json
[
	{
		"endpoint": "c3-ceph01.bj",
		"metric": "cpu_usage_idle",
		"timestamp": 1637732157,
		"tags": "cpu=cpu-total,region=ali",
		"value": 30.5
	},
	{
		"endpoint": "c3-ceph01.bj",
		"metric": "cpu_usage_util",
		"timestamp": 1637732157,
		"tags": "cpu=cpu-total,region=ali",
		"value": 69.5
	}
]
```

显然，JSON 最外层是个数组，如果只上报一条监控数据，也可以不要外面的中括号，直接把对象结构上报：

```json
{
	"endpoint": "c3-ceph01.bj",
	"metric": "cpu_usage_idle",
	"timestamp": 1637732157,
	"tags": "cpu=cpu-total,region=ali",
	"value": 30.5
}
```

服务端会看第一个字符是否是`[`，来判断上报的是数组，还是单个对象，自动做相应的 Decode。如果觉得上报的内容太过占用带宽，也可以做 gzip 压缩，此时上报的数据，要带有`Content-Encoding: gzip`的 Header。

{{% notice info %}}
夜莺中有个监控对象（通常表示设备）的概念，n9e-server 会把 endpoint 字段当做监控对象看待，会自动获取 endpoint 的 value，注册到监控对象的列表里
{{% /notice %}}