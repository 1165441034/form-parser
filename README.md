# Form-Parser

#### 介绍
Form Generator JSON格式重写前的自定义表单回显组件,适配JSON格式还为更新前的框架数据

新版本的Form Generator请使用官方自带* 的   * parser插件,使用npm 安装

#### 软件架构
主要组件Parser  Preview.html是打酱油的


#### 安装教程

1. 在VUE中以组件形式导入

2. 直接在文件中使用

   

#### 使用说明

​	组件需要传递两个必须值:

```
* formConf: {
*          type: Object,
*          required: true
*       },
* drawingList: {
*          type: Array,
*           required: true
*      }
```

组件提交的数据头可以在自定义表单设计页面中控制.

提交事件为自定义事件@submit 

表格数据回显以及行进行编辑操作时单行数据回显到自定义表单中的组件在这:

permission-c.vue   这个组件两个必传值,formData和pageData  回显的数据需要传递dataList 且是同一个自定义表单生成并保存的数据,建议后端使用唯一表单ID绑定值,查询时间
