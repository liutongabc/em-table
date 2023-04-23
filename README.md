## em-table
__示例：__

__完整示例__
::: demo 示例
```html
<template>
    <div>
        
        <em-table :columns="columns" :rowOption="rowOption" :rowHeight="rowHeight" row-key-field-name="rowKey" :cellOption="cellOption" :sortOption="sortOption" :borderX="false" :borderY="true" :width="900" :scroll-width="1100" :max-height="300" :table-data="tableData"></em-table>
        

    </div>
</template>
<script>

export default {
    data() {
        return {
            rowHeight:36,
            rowOption:{
                hoverHighlight:true,
                clickHighlight:true,
                rowClick:(rowItem,rowIndex)=>{
                    console.log(rowItem,rowIndex)
                },
                stripe:true
            },
            sortOption:{
                multipleSort:false,
                sortAlways:true,
                sortChange:(params)=>{
                    this.tableData.reverse();
                    console.log(params)
                },
            },
            cellOption:{
                headerCellClass:(column,rowIndex)=>{
                    return rowIndex==0 && column.field == "hobby" ? "backRed" : ""
                },
                bodyCellClass:(row,column,rowIndex)=>{
                    return rowIndex==1 && column.field == "hobby1" ? "backRed" : ""
                },
            },
            columns: [
                {
                    field: "name",
                    key: "a",
                    title: "Name",
                    align: "center",
                    width: 100,
                    titleAlign:"right",
                    columnAlign:"right",
                    fixed:"left",
                    sortBy:"desc",
                    // titleComponentName:"example1",
                    // bodyComponentName:"example2"
                },
                {
                    field: "date",
                    key: "b",
                    title: "Date",
                    align: "left",
                    width: 100,
                    fixed:"left",
                    formatter:(row,rowIndex,field,colIndex)=>{
                        return `<span style="color:red">${row[field]}%</span>`
                    },
                },
                {
                    field: "hobby",
                    key: "c",
                    title: "Hobby",
                    align: "right",
                    children:[
                        {
                            field: "hobby1",
                            key: "c1",
                            title: "hobby1",
                            width: 100,
                            sortBy:""
                        },
                        {
                            field: "hobby2",
                            key: "c2",
                            title: "hobby2",
                            width: 100,
                            ellipsis:{
                                showTitle:true,
                                enable:true,
                                lineClamp:2
                            }
                        }
                    ]
                },
                { 
                    field: "address",
                    key: "d",
                    title: "Address",
                    width: 100,
                },
                {
                    title:"others",
                    children:[
                        {
                            field: "other1",
                            key: "e",
                            title: "other1",
                            width: 100,
                        },
                        {
                            field: "other2",
                            key: "f",
                            title: "other2",
                            width: 100,
                        },
                        {
                            field: "other3",
                            key: "g",
                            title: "other3",
                            width: 100,
                        }
                    ]
                }
            ],
            tableData: [],
        }
    },
    created(){
        setTimeout(() => {
            this.tableData = [
                {
                    rowKey:"1",
                    name: "John",
                    date: "1900-05-20",
                    hobby: "coding and coding repeat",
                    address: "No.1 Century Avenue, Shanghai",
                    other1:"1",
                    other2:"2",
                    other3:"3",
                    hobby1:"hobby1",
                    hobby2:"hobby2"
                },
                {
                    rowKey:"2",
                    name: "Dickerson",
                    date: "1910-06-20",
                    hobby: "coding and coding repeat",
                    address: "No.1 Century Avenue, Beijing",
                    other1:"1",
                    other2:"2",
                    other3:"3",
                    hobby1:"hobby1",
                    hobby2:"hobby2"
                },
                {
                    rowKey:"3",
                    name: "Larsen",
                    date: "2000-07-20",
                    hobby: "coding and coding repeat",
                    address: "No.1 Century Avenue, Chongqing",
                    other1:"1",
                    other2:"2",
                    other3:"3",
                    hobby1:"hobby1",
                    hobby2:"hobby2"
                },
                {
                    rowKey:"4",
                    name: "Geneva",
                    date: "2010-08-20",
                    hobby: "coding and coding repeat",
                    address: "No.1 Century Avenue, Xiamen",
                    other1:"1",
                    other2:"2",
                    other3:"3",
                    hobby1:"hobby1",
                    hobby2:"hobby2"
                },
                {
                    rowKey:"5",
                    name: "Jami",
                    date: "2020-09-20",
                    hobby: "coding and coding repeat",
                    address: "No.1 Century Avenue, Shenzhen",
                    other1:"1",
                    other2:"2",
                    other3:"3",
                    hobby1:"hobby1",
                    hobby2:"hobby2"
                },
            ]
        }, 0);
    },
}

// Vue.component("example1",{
//     template:`
//         <div @click="sortClick(colData)">
//             {{colData['field']}}
//             <span class="em-table-sort" v-if="colData.sortBy!=undefined">
//                 <i class="em-table-sort-icon em-table-sort-icon-up" :class="{'active':colData.sortBy=='asc'}"></i>
//                 <i class="em-table-sort-icon em-table-sort-icon-down" :class="{'active':colData.sortBy=='desc'}"></i>
//             </span>
//         </div>
//     `,
//     props:{
//         colData: {
//             type: Object
//         },
//         colData: {
//             type: Object
//         },
//         field: {
//             type: String
//         },
//         index: {
//             type: Number
//         }
//     },
//     created(){
//         console.log(this.colData,this.field)
//     },
//     methods:{
//         sortClick(item){
//             this.$parent.sortClick(item)
//         }
//     }
// })

// Vue.component("example2",{
//     template:`
//         <div>
//             {{rowData[field]}}
//         </div>
//     `,
//     props:{
//         rowData: {
//             type: Object
//         },
//         field: {
//             type: String
//         },
//         index: {
//             type: Number
//         }
//     },
//     created(){
//     },
//     methods:{
//     }
// })

</script>
```
:::

### Attributes
| 参数       |  说明                                 | 类型      | 可选值       | 默认值   |
|-----------|------------------------------------ |---------- |------------- |-------- |
|   tableData     |	    表格数据        |       Array   |  -    |  -	  |
|   columns     |	    列配置，具体项见下表 columns 配置        |       Array   |  	-    |  -	  |
|   footerData     |	    表footer数据        |       Array   |  -    |  -	  |
|   width     |	    表格宽度        |       String/Number   |  	Number指定像素；String指定百分比    |  100%	  |
|   scrollWidth     |	    表格滚动区域的宽（开始出滚动条的宽度）        |       String/Number   |  	Number指定像素；String指定百分比   |  	-  |
|   maxHeight     |	    表格的最大高度        |       String/Number   |  	Number指定像素；String可指定vh 例:(calc(100vh-100px))   |  	100%  |
|   fixedHeader     |	    固定表头        |       Boolean   |  	—   |  	true  |
|   titleRowHeight     |	    表头单元格高度        |       Number   |  	—   |  	36  |
|   rowHeight     |	    表体单元格高度        |       Number   |  	—   |  	36  |
|   footerRowHeight     |	    表footer单元格高度        |       Number   |  	—   |  	rowHeight  |
|   borderX     |	    横向边框        |       Boolean   |  	—   |  	true  |
|   borderY     |	    纵向边框        |       Boolean   |  	—   |  	false  |
|   rowKeyFieldName     |	    指定 row key 的字段名称。用于行点击、单元格点击高亮        |       String   |  	-  |  	rowkey  |
|   rowOption     |	    行配置，具体见下表行配置        |       Object   | -   |  	-  |
|   sortOption     |	    排序配置，具体见下表排序配置        |       Object   |  —  |  —	  |
|   noDataOption     |	    暂无数据配置，具体见下表暂无数据配置        |       Object   |  —  |  —	  |
|   cellOption     |	    单元格样式配置，具体见下表单元格样式配置        |       Object   |  —  |  false	  |

### columns列配置
| 参数       |  说明                                 | 类型      | 可选值       | 默认值   |
|-----------|------------------------------------ |---------- |------------- |-------- |
|   field     |	    对应列的字段        |       String   |  -    |  -	  |
|   key     |	    每个列的唯一key值        |       String   |  -    |  -	  |
|   title     |	    列标题        |       String   |  -    |  -	  |
|   width     |	    列宽度        |       String/Number   |  -    |  -	  |
|   titleAlign     |	    表头列内容对齐方式        |       String   |  left/center/right    |  -	  |
|   columnAlign     |	    表体列内容对齐方式        |       String   |  left/center/right    |  -	  |
|   sortBy     |	    排序规则        |       String   |  ""、"desc"、"asc"    |  -	  |
|   fixed     |	    列固定        |       String   |  "left"、"right"    |  -	  |
|   titleComponentName     |	    表头自定义组件        |       String   |  -    |  -	  |
|   bodyComponentName     |	    表体自定义组件        |       String   |  -    |  -	  |
|   formatter     |	    用来格式化内容        |       Function(row,rowIndex,field,colIndex)   |  -    |  返回格式化的html	  |
|   children     |	    复杂表头时配置子项        |       Array   |  -    |  -	  |
|   ellipsis     |	    单元格省略，具体见下表单元格省略配置        |       Object   |  -    |  -	  |

### ellipsis单元格省略配置
| 参数       |  说明                                 | 类型      | 可选值       | 默认值   |
|-----------|------------------------------------ |---------- |------------- |-------- |
|   enable     |	    是否开启省略        |       Boolean   |  -    |  true	  |
|   showTitle     |	    是否鼠标悬浮展示title        |       Boolean   |  -    |  false	  |
|   lineClamp     |	    多少行开始省略        |       Number   |  -    |  1	  |

### rowOption行配置
| 参数       |  说明                                 | 类型      | 可选值       | 默认值   |
|-----------|------------------------------------ |---------- |------------- |-------- |
|   hoverHighlight     |	    悬浮高亮        |       Boolean   |  -    |  true	  |
|   clickHighlight     |	    点击高亮        |       Boolean   |  -    |  false	  |
|   stripe     |	    斑马纹        |       Boolean   |  -    |  true	  |
|   rowClick     |	    行点击事件        |       Function(row,rowIndex)   |  -    |  -	  |

### sortOption排序配置
| 参数       |  说明                                 | 类型      | 可选值       | 默认值   |
|-----------|------------------------------------ |---------- |------------- |-------- |
|   multipleSort     |	    多字段排序        |       Boolean   |  -    |  false	  |
|   sortAlways     |	    只在升降序切换        |       Boolean   |  -    |  true	  |
|   sortChange     |	    排序点击事件        |       Function(column)   |  -    |  -	  |

### noDataOption暂无数据配置
| 参数       |  说明                                 | 类型      | 可选值       | 默认值   |
|-----------|------------------------------------ |---------- |------------- |-------- |
|   enable     |	    是否开启暂无数据        |       Boolean   |  -    |  false	  |
|   height     |	    暂无数据高度        |       String/Number   |  -    |  表格传入的最大高度	  |
|   size     |	    暂无数据大小        |       Number   |  -    |  100	  |

### cellOption单元格配置
| 参数       |  说明                                 | 类型      | 可选值       | 默认值   |
|-----------|------------------------------------ |---------- |------------- |-------- |
|   headerCellClass     |	    表头单元格自定义class        |       Function(column,rowIndex)   |  -    |  要有返回值	  |
|   bodyCellClass     |	    表体单元格自定义class        |       Function(row, column, rowIndex)   |  -    |  要有返回值	  |
|   headerCellClick     |	    表头单元格点击事件        |       Function(column,rowIndex)   |  -    |  选中时的class:"em-table-cell-select"	  |
|   bodyCellClick     |	    表体单元格点击事件        |       Function(row,column,rowIndex,colIndex)   |  -    |  选中时的class:"em-table-cell-select"	  |
|   headerCellMouseEnter     |	    表头单元格鼠标进入事件        |       Function(column, rowIndex)   |  -    |  -	  |
|   headerCellMouseLeave     |	    表头单元格鼠标离开事件        |       Function(column, rowIndex)   |  -    |  -	  |

### Event
| 事件名称      | 说明       | 回调数据类型   | 回调参数   |
|------------- |----------- |---------  |---------  |
|sortClick         |排序点击事件| Object | {column} |
|setHighlightRow         |设置高亮行| - | rowKey |

### 更新日志
| 版本      | 类型       | 内容   | 	作者   |更新日期|
|-----------|-----------|--------- |--------- |-------|
|0.0.1      |feature| 初版 | 刘通 |2022-07-18|