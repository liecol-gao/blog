---
title: 关于flexigrid的初识
date: 2015-11-23 18:02:15
tags: [javascript,flexigrid]
categories:
- javascript
---

对比了一下jquery表格框架`flexigrid`和`jqgrid`，个人认为flexigrid表格框架其实要比jqgrid用着要舒坦
样式也比jqgrid的好看，二者有很大相似也有很大不同。
在这里笔者说一下flexigrid，因为国内demo比较少
希望能对大家有点帮助。
引用：
```javascript
<script src=".jquery.js"></script>
<link rel="stylesheet" href=".styleII.css" />
<link rel="stylesheet" type="text/css" href="./flexigrid.css">
<script type="text/javascript" src="./flexigrid.js"></script>
```
<!--more-->
接受的json格式：
```javascript
{"total":111, "page":4, "rows":[{"id":"1","cell":["a","b","c"]},{"id":"2","cell":["a","b","c"]}]}
```
表格构造:
```javascript
 $("#flex").flexigrid({  
                    url : 'list.php',  //ajax方式对应的url地址
                    dataType : 'json',  //数据加载的类型
                    colModel : [{  
                            display : '文章名称', //表格标题  
                            name : 'name',  //sort名称
                            width : 320,  //宽度
                            sortable : true,  //是否排序
                            align : 'center'  //对齐样式
                        }, {  
                            display : '发布时间',  
                            name : 'stand',  
                            width : 240,  
                            sortable : true,  
                            align : 'center'  
                        }, {  
                            display : '操作',  
                            name : 'money',  
                            width : 240,  
                            sortable : true,  
                            align : 'center'
                            
                        }],  
                    sortname : "id",  //初始排序名称
                    sortorder : "asc",  //初始排序规则
                    usepager : true,   //是否分页
                    title : '商品信息',  //标题
                    useRp : true,          //是否可以动态设置每页显示的结果数 
                    checkbox : true,// 是否要多选框  
                    rowId : 'id',// 多选框绑定行的id  
                    rp : 10,  //每页默认的结果数 
                    showTableToggleBtn : true,  // 是否允许显示隐藏列
                    width : 960,  // 总宽度
                    height : 400,    // 总高度
                    page: 1,    // 默认当前页 
                    total: 1, // 总页面数 
                }); 
```` 

其他的一些参数:
    striped : true, // 是否显示斑纹效果，默认是奇偶交互的形式 
    minwidth : 30, // 列的最小宽度
    minheight : 80, // 列的最小高度
    resizable : true, // 是否可伸缩
    method : 'POST', // 数据发送方式
    dataType : 'xml', // 数据加载的类型
    errormsg : '连接错误!',// 错误提示信息
    rpOptions : [5,10, 15, 20, 25, 30, 40],// 可选择设定的每页结果数
    title : false,// 是否包含标题
    pagestat : '显示第 {from} 条到 {to} 条,共 {total} 条数据',// 显示当前页和总页面的样式
    procmsg : '正在处理,请稍候 ...',// 正在处理的提示信息
    query : '',// 搜索查询的条件(没用过，但是好像蛮流弊蛮实用的样子)
    qtype : '',// 搜索查询的类别 
    nomsg : '没有数据存在!',// 无结果的提示信息 
    minColToggle : 1, // 允许显示的最小列数
    showToggleBtn : true, // 是否允许显示隐藏列
    hideOnSubmit : true,// 隐藏提交
    autoload : true,// 自动加载 
    blockOpacity : 0.5,// 透明度设置 
    onToggleCol : false,// 当在行之间转换时，可在此方法中重写默认实现，基本无用 
    onChangeSort : false,// 当改变排序时，可在此方法中重写默认实现，自行实现客户端排序
    onSuccess : false,// 成功后执行 
    onSubmit : false //提交时执行的函数
    idProperty: 'id',//绑定主键ID，列格式化时用到。 pagetext: '第',//当前页
    outof: '页，总页数',//总页数
    findtext: 'Find',//查询按钮的文本
    params: [],允许可选被传递到的参数
    preProcess: false,(没用过,可能是是否绑定事件的意思)
    dblClickResize: false, 双击自动调整列
    onDragCol: false,//拖拽列的触发的事件
    onDoubleClick: false,//当在双击行时触发的事件
    totalRender: null, //统计面板(全部数据)

表格传递参数：
    实例化flexigrid时，传参数用params属性，类型是数组，格式是[{name:"参数名",value:"参数值"}]
```javascript
$("#query").flexigrid({
params : [ {
            name : "参数名1",
            value : "参数值1"
            }, {
            name : "参数名2",
            value : "参数值2"
         } ]
});
```
刷新：
newp //自定义翻页事件
```javascript
$('#tabQuery').flexOptions({newp: 1}).flexReload();
return false;

function flexReload(){
    var str = jq("#form1").serialize();
    jq("#flex").flexOptions({url:"List?"+str}).flexReload(); 
}
```
自定义列名和内容
process 函数名
```javascript
$("#query").flexigrid({
    idProperty : "id",
    colModel : [ {
        display : '操作',
        width : 120,
        align : 'center',
        process : operate
    } ]
});
```

process 自己定义的函数名中带有两个参数例如:function operate(thDiv,id)){},
其中thDiv是当前单元格的div，id则是这行数据的主键列。 
```javascript
function operate(tdDiv,id){
    $(tdDiv).html("<a hre='index.jsp?id="+id+"'>"+$(tdDiv).html()+"</a>");
}
```
create By liecol-晓斌

2015.11.23