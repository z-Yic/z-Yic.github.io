---
title: 'UnmarshalJSON: the string of date to time.Time'
date: 2023-07-07 23:47:18
tags:
- gin
- JSON
- UnmarshalJSON
---
经过一段时间vue的学习，今天开始实现一个简单的前后端分离项目；
## 项目介绍
### 需求分析
简单的用户管理系统，只实现用户的增删改查功能，大致效果如下图所示：
![效果图](效果图.png)
### 系统设计
- 前端：`vue`+`vue-router`+`axios`+`layui-vue`;
- 后端：`gin`+`gorm`+`mysql`;
### 详细设计
进行中
## 问题
(因为经验不足，遇到的小问题挺多的，这里只是做个标记，另外重点记录一下日期反序化成time.Time的问题)
1. 表单提交的数据都是字符串类型，通过axios序列化成JSON格式后发送到后端，无法与结构体中的非字符串类型字段绑定；
解决方法：在axios发送前通过js进行类型转换；
2. 结构体中time.Time类型的字段序列化后的格式为`2006-01-02T15:04:05Z07:00`，在前端显示前需要格式化为`2006-01-02`的形式；
解决方法：显示前先通过js进行字符串裁剪；
3. 问题2的逆过程，表单中日期数据，通过axios以JSON序列化后发送到gin，使用Bind()直接绑定失败，结构体中time.Time类型的字段反序化失败；
**问题分析**
为什么字符串类型的日期数据反序化成time.Time类型的字段失败呢？字符串反序化成time.Time是没问题的，因为time.Time序列化后就是字符串类型。问题的关键还是日期的格式：
- 前端通过日期输入框得到的日期格式为`2006-01-02`，同时意味着以JSON格式发送的日期字段也是这个格式；
- 后端接受到该JSON数据后，我直接借助了gin框架中的`(*gin.Context).Bind()`方法尝试将得到的JSON数据与结构体实例绑定；但该方法底层使用的是默认的JSON解析器，对于JSON中的日期字段会先以字符串的形式进行反序列化，然后通过`time.Parse(layout, value string) (Time, error)`将字符串解析成`time.Time`类型，解析失败正是发生在这一步，因为`value`和`layout`的格式不一致，默认的JSON解析器中`layout`被设置为`2006-01-02T15:04:05Z07:00`，而传入的`value`的格式为`2006-01-02`，解析失败所以绑定失败！
**解决方法**
经过以上分析，解决该问题的关键是将`time.Parse(layout, value string) (Time, error)`中的`layout`参数设置成`2006-01-02`，为此不再使用默认的JSON解析器，而是自己重写该解析方法，在该结构体上实现`UnmarshalJSON(data []byte) error`即可；
```go
func (user *User) UnmarshalJSON(data []byte) error {
	type tempUser struct {
		Name string `json:"name"`
		Age  int    `json:"age"`
		Date string `json:"date"`
	}

	var tem tempUser
	err := json.Unmarshal(data, &tem)
	if err != nil {
		return err
	}

	user.Name = tem.Name
	user.Age = tem.Age
	user.Date, err = time.Parse("2006-01-02", tem.Date)
	if err != nil {
		return err
	}

	return nil
}
```
**代码说明**
- 先创建了一个可临时接收反序化结果的结构体`tempStruct`，其中`date`字段的数据类型为字符串，并使用其实例`tem`完成了反序化结果的接收；
- 通过`user.Date, err = time.Parse("2006-01-02", tem.Date)`，将正确解析的`time.Time`类型的数据赋值给实际接收的结构体；
- 因为完全<mark>重写了默认的解析规则，除了`date`字段外，其它字段也必须重新解析</mark>，否则其它字段无法解析JSON数据，会以零值输出；
**补充说明**
以上情况适用于<mark>客户端以JSON格式发送请求数据</mark>时，如果客户端直接通过表单提交数据(不转换成JSON格式)，使用`(*gin.Context)Bind()`也会遇到解析日期字符串失败的问题，但解决方法不再是重写`UnmarshalJSON()`，而是在定义结构体时，在该日期字段上，添加`time-fotmat:"2006-01-02"`标签，这样`Bind()`在解析表单数据中的日期字段时会按照`2006-01-02`的格式解析成`time.Time`类型的数据；
```go
type User struct {
	Name string 	`form:"name"`
	Age  int 		`form:"age"`
	Date time.Time 	`form:"date" time-format:"2006-01-02"`
}
```