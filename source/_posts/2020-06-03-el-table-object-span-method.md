---
title: Element table 组件实现行合并(span-method)
date: 2020-06-03 00:37:29
tags:
  - Element
  - UI组件库
  - span-method
categories:
  - [问题解决, Element-ui]
excerpt: el-table 组件实现行合并(span-method)详解...
---

**官网文档说明：**

{% blockquote https://element.eleme.cn/#/zh-CN/component/table %}
span-method 合并行或列的计算方法 Function({ row, column, rowIndex, columnIndex })

方法的参数是一个对象，里面包含当前行 row、当前列 column、当前行号 rowIndex、当前列号 columnIndex 四个属性。该函数可以返回一个包含两个元素的数组，第一个元素代表 rowspan，第二个元素代表 colspan。 也可以返回一个键名为 rowspan 和 colspan 的对象。
{% endblockquote %}

## 实现案例 1: 理解参数

**实现效果**

{% asset_img 20200603215741.png "实现案例1效果图" %}

**html 模板**

```html
<el-table
  :data="tableData"
  :span-method="objectSpanMethod"
  border
  style="width: 300px;"
>
  <el-table-column prop="id" label="ID"></el-table-column>
  <el-table-column prop="name" label="姓名"></el-table-column>
  <el-table-column prop="sex" label="性别"></el-table-column>
</el-table>
```

**表格数据**

```js
data () {
  return {
    tableData: [
      {
        id: "1",
        name: "李国有",
        sex: "男",
      },
      {
        id: "2",
        name: "李国友",
        sex: "男",
      },
      {
        id: "3",
        name: "李友国",
        sex: "未知",
      },
      {
        id: "4",
        name: "油果",
        sex: "女",
      },
      {
        id: "5",
        name: "有国",
        sex: "女",
      },
    ];
  }
}
```

**实现方法及注解(看注释)**

```js
methods: {
  // 合并的计算方法
  objectSpanMethod({ row, column, rowIndex, columnIndex }) {
    // 当列索引是2(第三列), 需要设置要合并的列/行
    if (columnIndex === 2) {
      if (rowIndex === 0 || rowIndex === 3) {
        // 当行索引是0(第一行), 或者当行索引是3(第四行), 均向下合并1行(共两行), 合并0列(共一列)
        return {
          rowspan: 2,
          colspan: 1,
        };
      } else if (rowIndex === 2) {
        // 当行索引是2(第三行), 不作合并
        return {
          // 1表示不合并
          rowspan: 1,
          colspan: 1,
        };
      } else {
        // 其他行
        return {
          // 0表示已被合并, 直接不显示
          rowspan: 0,
          colspan: 0,
        };
      }
    }
  },
},
```

## 实现案例 2: 动态设置合并行列

**效果图**

{% asset_img 20200603230321.png "实现案例1效果图" %}

**html 模板**

````html
<el-table
  :data="tableData"
  :span-method="objectSpanMethod"
  border
  style="width: 600px;"
>
  <el-table-column prop="id" label="订单号"></el-table-column>
  <el-table-column prop="name" label="商品名称"></el-table-column>
  <el-table-column prop="price" label="价格"></el-table-column>
  <el-table-column prop="num" label="数量"></el-table-column>
</el-table>
```
````

**订单原始数据 -> 表格数据**

```js
// vue data
data () {
  return {
    // 表格数据
    tableData: []
  }
},
created() {
  this.init()
}
```

```js
methods: {
  init() {
    // 订单数据
    const orderData = [
      {
        id: 1,
        childrens: [
          {
            name: "华为1",
            price: 3000,
            num: 2,
          },
          {
            name: "苹果1",
            price: 4000,
            num: 1,
          },
          {
            name: "RedMi3",
            price: 4000,
            num: 1,
          },
        ],
      },
      {
        id: 2,
        childrens: [
          {
            name: "魅族1",
            price: 2000,
            num: 2,
          },
        ],
      },
      {
        id: 3,
        childrens: [
          {
            name: "OPPO",
            price: 3000,
            num: 1,
          },
          {
            name: "VIVO",
            price: 2000,
            num: 1,
          },
        ],
      },
    ]

    // 数据重组
    orderData.forEach((order) => {
      order.childrens.forEach((prod, i) => {
        this.tableData.push({
          id: order.id,
          name: prod.name,
          price: prod.price,
          num: prod.num,
          length: i === 0 ? order.childrens.length : 0,
        });
      });
    });

    // 重组后的数据 this.tableData
    // [
    //   {"id":1,"name":"华为1","price":3000,"num":2,"length":3},
    //   {"id":1,"name":"苹果1","price":4000,"num":1,"length":0},
    //   {"id":1,"name":"RedMi3","price":4000,"num":1,"length":0},
    //   {"id":2,"name":"魅族1","price":2000,"num":2,"length":1},
    //   {"id":3,"name":"OPPO","price":3000,"num":1,"length":2},
    //   {"id":3,"name":"VIVO","price":2000,"num":1,"length":0}
    // ]
  },

  // 合并的计算方法
  objectSpanMethod2({ row, column, rowIndex, columnIndex }) {
    // 合并第一列: 订单号
    if (columnIndex === 0) {
      return {
        rowspan: row.length, // , 通过 length 来判断需要合并的行数
        colspan: 1, // 列不合并
      };
    }
  },
}
```

列合并也是同样的操作, 实际情况中行合并的需求比较常见

通过对 row, column, rowIndex, columnIndex, rowspan, colspan 的灵活设置, 可以实现更多复杂的操作

**参考**

[element-ui table :span-method（行合并）- CSDN](https://blog.csdn.net/qq_29468573/article/details/80742646)
