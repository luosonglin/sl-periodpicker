

## sl-periodpicker 分时段选择器
> **组件名：sl-periodpicker**
> 
> **注意事项**
> 
> - `schedule`对象传入的时间段属性使用的是24小时制格式化日期yyyy-MM-dd HH:mm:ss，如： 2021-11-30 08:00:00


### 安装方式

本组件符合[easycom](https://uniapp.dcloud.io/collocation/pages?id=easycom)规范，`HBuilderX 2.5.5`起，只需将本组件导入项目，在页面`template`中即可直接使用，无需在页面中`import`和注册`components`。

### 基本用法

在 ``template`` 中使用组件

```html
<sl-periodpicker :schedule="item" :startTime="startTime" :endTime="endTime" :key="menuKey" />
```


## API

### Periodpicker Props

<table>
<thead>
<tr>
<th>属性名</th>
<th>类型</th>
<th>默认值</th>
<th>值域</th>
<th>说明</th>
</tr>
</thead>
<tbody>
<tr>
<td>startTime</td>
<td>String</td>
<td>-</td>
<td>date/daterange/datetime/
datetimerange
range</td>
<td>时间选择范围-开始时间</td>
</tr>
<tr>
<td>endTime</td>
<td>String</td>
<td>-</td>
<td>date/daterange/datetime/
datetimerange
range</td>
<td>时间选择范围-结束时间</td>
</tr>
<tr>
<td>schedule</td>
<td>Object</td>
<td>-</td>
<td>-</td>
<td>已有“业务”的时间段。期待格式

[{"name":"600室","room_id":"0","schedule":[{"start":"2021-11-30 2021-11-30 08:50:00","end":"2021-11-30 2021-11-30 10:30:00","user":"产品技术部-某某某","length":13.33,"x":6.67,"remark":1}]}]
</td>
</tr>
</tbody>
</table>

### Periodpicker Events

<table>
<thead>
<tr>
<th>事件名称</th>
<th>说明</th>
<th>返回值</th>
</tr>
</thead>
<tbody>

<tr>
<td>click</td>
<td>确定某时间段触发的事件</td>
<td>-</td>
</tr>

</tbody>
</table>

![](https://img-cdn-aliyun.dcloud.net.cn/stream/plugin_screens/6aabb900-d18c-11eb-9e15-d7bea4ff4d12_0.png){:height="100px" width="400px"}
![](https://img-cdn-aliyun.dcloud.net.cn/stream/plugin_screens/6aabb900-d18c-11eb-9e15-d7bea4ff4d12_1.png){:height="100px" width="400px"}
