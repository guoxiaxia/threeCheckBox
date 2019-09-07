# threeCheckBox
基于vue的可选择三级复选框的表格功能



使用map对象记录已选二级+三级复选框的id集合，并建立对应关系。



与checkTable区别：

1. chectTable不限制一级、二级、三级复选框的数量限制，threeCheckBox只有一个一级复选框，二级、三级同样不限制。
2. checkTable复选框只支持文字类型，threeCheckBox复选框支持各种数据类型，不限



数据结构为：

```js
{
    articleId: 10,
    title: "商品1",
    article_goods: [
        {
            id: 1,
            spec_text: "红色",
            img_url:""
        },
        {
            id: 2,
            spec_text: "蓝色",
            img_url:""
        }
    ]
},
{
    articleId: 9,
    title: "商品2",
    article_goods: [
        {
            id: 4,
            spec_text: "粉色",
            img_url:""
            },
    ]
}
```



使用时： 除封装复选框功能外，HTML结构可自行扩充



效果图：

![check](C:\Users\Administrator\Desktop\threeCheckBox\check.png)