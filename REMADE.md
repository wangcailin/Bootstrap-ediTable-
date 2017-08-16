# Bootstrap ediTable使用教程
Bootstrap ediTable

## 整理资料来源
> 本资料部分来自 [Bootstrap Table](http://bootstrap-table.wenzhixin.net.cn/zh-cn/documentation/)和[X-editable](http://vitalets.github.io/x-editable/docs.html)
> 本人在日常使用过程中遇到的一些问题

## 实例：增删改查
使用X-editable官方给出的建议是：`Markup elements that should be editable. Usually it is <A> element with additional data-* attributes`尽量使用data属性来绑定事件和数据;
> 我在实际使用过程中发现使用data属性和使用js绑定事件属性代码少了三分之一,而且易读性和易扩展性都非常好，官方文档虽然建议data属性绑定但是好多例子是用的js事件绑定,现在把一些官方基本没有出现的绑定属性分享一下

```
    <table id="table"
           data-id-field="id"
           data-toggle="table"
           data-pagination="true"
           data-search="true"
           data-height="543"
           data-side-pagination="server"
           data-editable-save="onEditableSave"
           data-editable-success="success"
           data-editable-validate="validate"
           data-editable-url="/aapi/phone_book/updateOrderInfo"
           data-url="/aapi/phone_book/getList">
        <thead>
        <tr>
            <th data-field="id" data-sortable="true" data-width="10%">id</th>
            <th data-field="name" data-editable="true" data-width="30%" data-editable-title="请输入name">name</th>
            <th data-field="title" data-editable="true" data-width="40%" data-editable-title="请输入title">title</th>
            <th data-field="id" data-formatter="operateFormatter" data-width="10%">操作</th>
        </tr>
        </thead>
    </table>
````
> 以上数据响应格式{success: true, msg: "更新成功"}

### 修改

```shell
1. # 点击修改确认按钮数据提交接口属性:data-editable-url
data-editable-url="/aapi/phone_book/updateOrderInfo"

2. # 数据提交保存事件属性:data-editable-save
data-editable-save="onEditableSave"

3. # 响应数据事件:data-editable-success
data-editable-success="success"
```


### Bootstrap table绑定事件
html
```
<div class="alert alert-danger" id="eventInfo"></div>
    <table id="goods"
        data-pagination="true"
        data-search="true"
        data-show-refresh="true"
           data-show-toggle="true"
           data-show-columns="true">
        <thead>
            <tr>
                <th data-field="state" data-checkbox="true"></th>
                <th data-sortable="true" data-field="goodsName">商品名称</th>
                <th data-sortable="true" data-field="price">价格</th>
                <th data-field="date">日期</th>
            </tr>
        </thead>
    </table>
```
js
```
$(function(){
            /*初始化表格*/
            $("#goods").bootstrapTable({
                data: data
            });
            /*加载事件*/
            $("#goods")
            .on('click-row.bs.table', function (e, row, ele,field) {
                $("#eventInfo").text('点击行事件 当前商品名：'+ row.goodsName
                                    + '，价格：' + row.price
                                    + '，效期：' + row.date
                                    + '当前点击单元格field值为：' + field);
            })
            .on('dbl-click-row.bs.table', function (e, row, ele,field) {
                $("#eventInfo").text('双击行事件');
            })
            .on('check.bs.table', function (e, row,ele) {
                $("#eventInfo").text('checkbox选中事件');
            })
            .on('uncheck.bs.table', function (e, row,ele) {
                $("#eventInfo").text('checkbox取消选中事件');
            })
            .on('sort.bs.table', function (e, field, order) {
                var o;
                switch(order){
                    case "desc":
                    o = "降序";
                    break;
                    case "asc":
                    o = "升序";
                    break;
                }
                $("#eventInfo").text('排序事件 当前' + name + '列，以' + o + "排列");
            })
            .on('check-all.bs.table', function (e,dataArr) {
                $("#eventInfo").text('全选事件');
            })
            .on('uncheck-all.bs.table', function (e,dataArr) {
                $("#eventInfo").text('取消全选事件');
            })
            .on('load-success.bs.table', function (e, data) {
                $("#eventInfo").text('加载成功事件');
            })
            .on('load-error.bs.table', function (e, status) {
                $("#eventInfo").text('加载错误事件');
            })
            .on('column-switch.bs.table', function (e, field, checked) {
                var colName;
                switch(field){
                    case "goodsName":
                    colName = "商品名称";
                    break;
                    case "price":
                    colName = "价格";
                    break;
                    case "date":
                    colName = "日期";
                    break;
                }
                if(checked){
                    $("#eventInfo").text('筛选列事件 ' + colName + '列显示');
                }else{
                    $("#eventInfo").text('筛选列事件 ' + colName + '列隐藏');
                }
            })
            .on('page-change.bs.table', function (e, number, size) {
                $("#eventInfo").text('切换页事件 当前页数：第' + number + "页，每页显示数量" + size + "条");
            })
            .on('search.bs.table', function (e, text) {
                $("#eventInfo").text('搜索事件');
            });
        });
```
demo数据以json加载数据自己写... ... ...

分析事件及回调函数

bootstrap-table事件基本以on绑定，bootstrap-table的事件都有.bs.table后缀 bs即bootstrap 显然.bs.table是申明boostrap-table。

click-row：行点击事件，callback获取4个参数 ： e 事件对象 ， row 当前行数据对象 ， ele 当前单元格对象 ， field 当前单元格的field值。(row是数据对象，ele是DOM对象)

dbl-click-row：行双击事件，callback获取4个参数 ： e 事件对象 ， row 当前行数据对象 ， ele 当前单元格对象 ， field 当前单元格的field值。(row是数据对象，ele是DOM对象)

check：单个checkbox选中事件，callback获取3个参数 ： e 事件对象 ， row 当前行数据对象 ， ele 当前单元格对象。(row是数据对象，ele是DOM对象)

uncheck：单个checkbox取消选中事件，callback获取3个参数 ： e 事件对象 ， row 当前行数据对象 ， ele 当前单元格对象。(row是数据对象，ele是DOM对象)

check-all：全选checkbox事件，callback获取2个参数 ：e 事件对象 ，dataArr 选中行数据对象集合(dataArr的对象是数据对象)

uncheck-all：全选checkbox取消事件，callback获取2个参数 ：e 事件对象 ，dataArr 选中行数据对象集合(dataArr的对象是数据对象)

sort：列排序事件，callback获取3个参数 ： e 事件对象 ， field 当前列的field值 ， order 当前列是升序还是降序(enum值，desc ， asc)。

load-success：数据加载成功事件，callback获取2个参数：e 事件对象 ， data 成功加载的数据对象集合。(dataArr的对象是数据对象)

load-error：数据加载失败事件，callback获取2个参数：e 事件对象 ， status 失败状态码。

column-switch：显示隐藏列选择事件，callback获取3个参数：e 事件对象 ，field 当前列的field值 ，checked 是否勾选(bool值)。

page-change：翻页事件，callback获取3个参数 ： e 事件对象 ，number 当前页码(不是下标，是页码) ，size 当前页数据条数。

search：搜索事件，callback获取2个参数 ： e 事件对象 ，text 搜索框输入内容。

这个demo包含几乎所有bootstrap-table的事件。
