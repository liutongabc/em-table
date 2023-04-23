<template>
    
    <div class="em-table" :style="tableStyle">
        <div class="em-table-container" :style="tableContainerStyle" :class="tableContainerClass">
            <table class="em-table-content" :class="{'em-table-border-x':borderX,'em-table-border-y':borderY}" :style="tableContentStyle">
                <colgroup>
                    <col v-for="(item) in bodyColumnsFlat(internalColumns)" :key="item.key" :style="colgroupStyle(item)">
                </colgroup>
                <thead :class="{'em-table-fixed-header':fixedHeader}">
                    <tr v-if="maxLevel==1" :style="tableHeaderStyle">
                        <template v-for="(item,index) in internalColumns">
                            <th v-if="item.titleComponentName" :key="index" :class="tableHeaderThClass(item,0,index)" :style="tableHeaderCellStyle(item,0)" @click="headerCellClick(item,0,index)" @mouseenter="headerCellMouseEnter(item,0,index)" @mouseleave="headerCellMouseLeave(item,0,index)">
                                <component :colData="item" :field="item.field ? item.field : ''" :index="index" :is="item.titleComponentName" ></component>
                            </th>
                            <th v-else :key="index" :class="tableHeaderThClass(item,0,index)" :style="tableHeaderCellStyle(item,0)" @click="headerCellClick(item,0,index)" @mouseenter="headerCellMouseEnter(item,0,index)" @mouseleave="headerCellMouseLeave(item,0,index)">
                                {{item.title}}
                                <span class="em-table-sort" v-if="item.sortBy!=undefined" @click="sortClick(item)">
                                    <i class="em-table-sort-icon em-table-sort-icon-up" :class="{'active':item.sortBy=='asc'}"></i>
                                    <i class="em-table-sort-icon em-table-sort-icon-down" :class="{'active':item.sortBy=='desc'}"></i>
                                </span>
                            </th>
                        </template>
                    </tr>
                    <template v-else>
                        <tr v-for="level in maxLevel" :key="level" :style="tableHeaderStyle">
                            <template v-for="(item,index) in headerColumnsFlat(internalColumns)">
                                <th v-if="item.titleComponentName && level==item.level" :rowspan="item.rowspan" :colspan="item.colspan" :key="index" :class="tableHeaderThClass(item,level-1,index)" :style="tableHeaderCellStyle(item,level-1)" @click="headerCellClick(item,level-1,index)" @mouseenter="headerCellMouseEnter(item,level-1,index)" @mouseleave="headerCellMouseLeave(item,level-1,index)">
                                    <component :colData="item" :field="item.field ? item.field : ''" :index="index" :is="item.titleComponentName" ></component>
                                </th>
                                <th v-else-if="level==item.level" :rowspan="item.rowspan" :colspan="item.colspan" :key="index" :class="tableHeaderThClass(item,level-1,index)" :style="tableHeaderCellStyle(item,level-1)" @click="headerCellClick(item,level-1,index)" @mouseenter="headerCellMouseEnter(item,level-1,index)" @mouseleave="headerCellMouseLeave(item,level-1,index)">
                                    {{item.title}}
                                    <span class="em-table-sort" v-if="item.sortBy!=undefined" @click="sortClick(item)">
                                        <i class="em-table-sort-icon em-table-sort-icon-up" :class="{'active':item.sortBy=='asc'}"></i>
                                        <i class="em-table-sort-icon em-table-sort-icon-down" :class="{'active':item.sortBy=='desc'}"></i>
                                    </span>
                                </th>
                            </template>
                        </tr>
                    </template>
                </thead>
                <tbody :class="tableBodyClass">
                    <tr :style="noShowTrStyle" :id="randomId">
                        <td v-for="(item,index) in bodyColumnsFlat(internalColumns)" :key="index" :style="noShowTdStyle"></td>
                    </tr>
                    <tr v-for="(rowItem,rowIndex) in internalTableData" :key="rowIndex" :style="tableBodyStyle" :class="tableBodyTrClass(rowItem,rowIndex)" @click="rowClick(rowItem,rowIndex)">
                        <td v-for="(colItem,colIndex) in bodyColumnsFlat(internalColumns)" :colspan="colItem.colspan" :key="colIndex" :class="tableBodyTdClass(rowItem,colItem,rowIndex,colIndex)" :style="tableBodyCellStyle(colItem,rowIndex)" @click="bodyCellClick(rowItem,colItem,rowIndex,colIndex)">
                            
                            <template v-if="colItem.bodyComponentName">
                                <component :rowData="rowItem" :colData="colItem" :field="colItem.field ? colItem.field : ''" :index="rowIndex" :is="colItem.bodyComponentName" ></component>
                            </template>
                            <template v-else-if="typeof colItem.formatter==='function'">
                                <span :class="{'em-table-body-td-ellipsis':!colItem.ellipsis || colItem.ellipsis.enable!=false}" :style="ellipsisStyle(colItem)" v-html="colItem.formatter(rowItem,rowIndex,colItem.field,colIndex)"></span>
                            </template>
                            <template v-else-if="colItem.ellipsis && colItem.ellipsis.enable==false">
                                {{rowItem[colItem["field"]]}}
                            </template>
                            <template v-else>
                                <span :title="[colItem.ellipsis && colItem.ellipsis.showTitle ? rowItem[colItem['field']] : '']" :class="{'em-table-body-td-ellipsis':!colItem.ellipsis || colItem.ellipsis.enable!=false}" :style="ellipsisStyle(colItem)">
                                    {{rowItem[colItem["field"]]}}
                                </span>
                            </template>
                        </td>
                    </tr>
                </tbody>
                <tfoot :class="tableFooterClass" v-if="internalFooterData.length">
                    <tr v-for="(rowItem,rowIndex) in internalFooterData" :key="rowIndex" :style="tableFooterStyle" :class="tableFooterTrClass(rowItem,rowIndex)" @click="rowClick(rowItem,rowIndex)">
                        <td v-for="(colItem,colIndex) in bodyColumnsFlat(internalColumns)" :colspan="colItem.colspan" :key="colIndex" :class="tableFooterTdClass(rowItem,colItem,rowIndex,colIndex)" :style="tableFooterCellStyle(colItem,rowIndex)" @click="bodyCellClick(rowItem,colItem,rowIndex,colIndex)">
                            
                            <template v-if="colItem.bodyComponentName">
                                <component :rowData="rowItem" :colData="colItem" :field="colItem.field ? colItem.field : ''" :index="rowIndex" :is="colItem.bodyComponentName" ></component>
                            </template>
                            <template v-else-if="typeof colItem.formatter==='function'">
                                <span v-html="colItem.formatter(rowItem,rowIndex,colItem.field,colIndex)"></span>
                            </template>
                            <template v-else>
                                <span :title="[colItem.ellipsis && colItem.ellipsis.showTitle ? rowItem[colItem['field']] : '']" :class="{'em-table-body-td-ellipsis':!colItem.ellipsis || colItem.ellipsis.enable!=false}" :style="ellipsisStyle(colItem)">
                                    {{rowItem[colItem["field"]]}}
                                </span>
                            </template>
                        </td>
                    </tr>
                </tfoot>
            </table>
        </div>
    </div>

</template>

<script>
import Vue from 'vue'
import { cloneDeep, debounce } from "lodash";

export default {
    name:'em-table',
    props:{
        columns:{
            type:Array,
            require:true
        },
        tableData:{
            type:Array,
            require:true,
            default:function(){
                return []
            }
        },
        footerData:{
            type:Array,
            require:false,
            default:function(){
                return []
            }
        },
        scrollWidth:{
            type:String/Number
        },
        width:{
            type:String/Number,
            default:"100%"
        },
        maxHeight:{
            type:String/Number,
            default:"100%"
        },
        fixedHeader:{
            type:Boolean,
            default:true
        },
        titleRowHeight:{
            type:Number,
            default:36
        },
        rowHeight:{
            type:Number,
            default:36
        },
        footerRowHeight:{
            type:Number,
        },
        borderX:{
            type:Boolean,
            default:true
        },
        borderY:{
            type:Boolean,
            default:false
        },
        rowKeyFieldName:{
            type:String,
            default:"rowKey"
        },
        rowOption:{
            type:Object,
            default:function(){
                return {
                    hoverHighlight:true,
                    clickHighlight:false,
                    rowClick:(rowItem,rowIndex)=>{

                    },
                    stripe:true,
                }
            }
        },
        // footerOption:{
        //     type:Object,
        //     default:function(){
        //         return {
        //             hoverHighlight:true,
        //             clickHighlight:false,
        //             rowClick:(rowItem,rowIndex)=>{

        //             },
        //             stripe:true,
        //         }
        //     }
        // },
        sortOption:{
            type:Object,
            default:function(){
                return {
                    multipleSort:false,
                    sortAlways:true,
                    sortChange:(params)=>{},
                }
            }
        },
        noDataOption:{
            type:Object,
            default:function(){
                return {
                    enable:false,
                    height:this.maxHeight,
                    size:100
                }
            }
        },
        cellOption:{
            type:Object,
            default:function(){
                return {
                    headerCellClass:(column,rowIndex,colIndex)=>{
                        return ""
                    },
                    bodyCellClass:(row, column, rowIndex,colIndex)=>{
                        return ""
                    },
                    headerCellClick:(column,rowIndex,colIndex)=>{

                    },
                    bodyCellClick:(rowItem,colItem,rowIndex,colIndex)=>{

                    },
                    headerCellMouseEnter:(column,rowIndex,colIndex)=>{
                        
                    },
                    headerCellMouseLeave:(column,rowIndex,colIndex)=>{
                        
                    },
                }
            }
        },
        columnsResize:{
            type:Boolean,
            default:false
        }
    },
    data(){

        return {
            internalTableData:[],
            internalFooterData:[],
            internalColumns:[],
            maxLevel:1,
            nowHightlightKey:"",
            selectCellOption:{
            },
            selectHeaderCellOption:{
            },
            randomId:`em-table-no-show-tr-${new Date().getTime()}`
        }

    },
    computed:{
        colgroupStyle(){
            return (item)=>{
                return {
                    width: this.getValueWithUnit(item.width)
                }
            }
        },
        tableStyle(){
            return {
                width: this.getValueWithUnit(this.width)
            }
        },
        tableContainerStyle(){
            return {
                maxHeight:this.getValueWithUnit(this.maxHeight),
                height:this.internalTableData.length ? "" : this.getValueWithUnit(this.noDataOption.height),
                backgroundPosition:this.internalTableData.length ? "" : `center calc(50% + ${this.getValueWithUnit(this.initColumns.length ? this.maxLevel*this.titleRowHeight/2 : 0)})`,
                backgroundSize:this.internalTableData.length ? "" : this.getValueWithUnit(this.noDataOption.size)
            }
        },
        tableContentStyle(){
            return {
                width:this.getValueWithUnit(this.scrollWidth)
            }
        },
        tableHeaderStyle(){
            return {
                height:`${this.titleRowHeight}px`
            }
        },
        tableHeaderCellStyle(){
            return (item,level)=>{
                return {
                    'textAlign':item.titleAlign,
                    'left':item.left || '',
                    'right':item.right || '',
                    'top':`${level*this.titleRowHeight}px`
                }
            }
        },
        tableBodyStyle(){
            return {
                height:`${this.rowHeight}px`
            }
        },
        tableFooterStyle(){
            return {
                height:`${this.footerRowHeight || this.rowHeight}px`
            }
        },
        tableBodyCellStyle(){
            return (colItem,rowIndex)=>{
                return {
                    'textAlign':colItem.columnAlign,
                    'left':colItem.left || '',
                    'right':colItem.right || '',
                }
            }
        },
        tableFooterCellStyle(){
            return (colItem,rowIndex)=>{
                return {
                    'textAlign':colItem.columnAlign,
                    'left':colItem.left || '',
                    'right':colItem.right || '',
                    'bottom':this.footerRowHeight ? `${(this.internalFooterData.length-rowIndex-1)*this.footerRowHeight}px` : `${(this.internalFooterData.length-rowIndex-1)*this.rowHeight}px`
                }
            }
        },
        ellipsisStyle(){
            return (colItem)=>{
                return {
                    '-webkit-line-clamp':colItem.ellipsis && colItem.ellipsis.lineClamp ? colItem.ellipsis.lineClamp : 1
                }
            }
        },
        tableContainerClass(){
            return {
                'em-table-no-data':this.internalTableData.length==0  && this.noDataOption.enable,
                'em-table-columns-resize':this.columnsResize
            }
        },
        tableHeaderThClass(){
            return (item,rowIndex,colIndex)=>{
                return [
                    {
                        'em-table-fixed-left':item.fixed=='left',
                        'em-table-fixed-right':item.fixed=='right',
                        'em-table-cell-select':item.key==this.selectHeaderCellOption.key && rowIndex==this.selectHeaderCellOption.rowIndex,
                        'em-table-columns-resize-th':!item.colspan || item.colspan==1
                    },
                    typeof this.cellOption.headerCellClass=='function' && this.cellOption.headerCellClass(item,rowIndex,colIndex)
                ]
            }
        },
        tableBodyTdClass(){
            return (rowItem,colItem,rowIndex,colIndex)=>{
                return [
                    {
                        'em-table-fixed-left':colItem.fixed=='left',
                        'em-table-fixed-right':colItem.fixed=='right',
                        'em-table-cell-select':colItem.key==this.selectCellOption.key && rowItem[this.rowKeyFieldName]==this.selectCellOption[this.rowKeyFieldName]
                    },
                    typeof this.cellOption.bodyCellClass=='function' && this.cellOption.bodyCellClass(rowItem,colItem,rowIndex,colIndex)
                ]
            }
        },
        tableFooterTdClass(){
            return (rowItem,colItem,rowIndex,colIndex)=>{
                return [
                    {
                        'em-table-fixed-left':colItem.fixed=='left',
                        'em-table-fixed-right':colItem.fixed=='right',
                        'em-table-cell-select':colItem.key==this.selectCellOption.key && rowItem[this.rowKeyFieldName]==this.selectCellOption[this.rowKeyFieldName]
                    },
                    typeof this.cellOption.bodyCellClass=='function' && this.cellOption.bodyCellClass(rowItem,colItem,rowIndex,colIndex)
                ]
            }
        },
        tableBodyTrClass(){
            return (rowItem,rowIndex)=>{
                return {
                    'em-table-tr-highlight': this.rowOption.clickHighlight && this.nowHightlightKey===rowItem[this.rowKeyFieldName]
                }
            }
        },
        tableFooterTrClass(){
            return (rowItem,rowIndex)=>{
                return {
                    'em-table-tr-highlight': this.rowOption.clickHighlight && this.nowHightlightKey===rowItem[this.rowKeyFieldName]
                }
            }
        },
        tableBodyClass(){
            return {
                'em-table-stripe': this.rowOption.stripe,
                'em-table-row-hover': this.rowOption.hoverHighlight,
                'em-table-row-highlight':true
            }
        },
        tableFooterClass(){
            return [
                {
                    'em-table-footer':true,
                    'em-table-row-hover': this.rowOption.hoverHighlight,
                    'em-table-row-highlight':true
                },
                this.rowOption.stripe ? this.internalTableData.length%2 == 0 ? 'em-table-stripe-odd' : 'em-table-stripe' : ''
            ]
        },
        noShowTrStyle(){
            return {
                'height':'0px!important'
            }
        },
        noShowTdStyle(){
            return {
                'padding':'0px!important',
                'border':'0px!important',
                'height':'0px!important'
            }
        }
    },
    methods:{
        initTable() {
            [...this.internalTableData] = cloneDeep(this.tableData);
            [...this.internalColumns] = cloneDeep(this.columns);
            [...this.internalFooterData] = cloneDeep(this.footerData);
        },
        initColumns(){
            let cloneColumns =  cloneDeep(this.columns);
            this.internalColumns = cloneColumns;
            this.setMutiHeaders();
        },
        getValueWithUnit(value){
            return typeof(value)=='number' ? `${value}px` : value
        },
        setFixedTdStyle(){
            this.setFixedWidth(this.internalColumns);
            // window.requestAnimationFrame(()=>{
                // this.internalColumns.map(item=>{
                //     if(item.children){

                //     }
                //     if(item.fixed)
                //     Vue.set(item,item.fixed,this.getFixedWidthByKey(this.internalColumns,item.key,item.keys,item.fixed));
                // })
            // })
        },
        setMutiHeaders(){
            const setColumnsLevel = (item,level)=>{
                Vue.set(item,"level",level);
                if(item.children){
                    this.maxLevel = level = level + 1;
                    item.children.map(child=>{
                        setColumnsLevel(child,level)
                    })
                }
            }
            const setColspanAndRowspanAndKeys = (item) => {
                if (item.children) {
                    let keys = "";
                    let colspan = 0;
                    item.children.forEach((child) => {
                        setColspanAndRowspanAndKeys(child);

                        colspan += child.colspan;
                        keys += child.keys.endsWith("|")
                            ? child.keys
                            : child.keys + "|";
                    });
                    Vue.set(item,"keys",keys);
                    Vue.set(item,"colspan",colspan);
                    Vue.set(item,"rowspan",1);
                } else {
                    Vue.set(item,"keys",item.key);
                    Vue.set(item,"colspan",1);
                    Vue.set(item,"rowspan",this.maxLevel - item.level + 1);
                }
            };
            this.internalColumns.map(item=>{
                let level = 1;
                setColumnsLevel(item,level);
            })
            this.internalColumns.map(item=>{
                setColspanAndRowspanAndKeys(item)
            })
        },
        getFixedWidthByKey(columns, colKey, colKeys, fixed){
            let currentIndex,colgroups = this.bodyColumnsFlat(columns);
            if(colKeys.indexOf("|")>-1){
                currentIndex = colgroups.findIndex((x) => colKeys.split("|").indexOf(x.key)>-1);
            }else{
                currentIndex = colgroups.findIndex((x) => x.key === colKey);
            }
            
            let result = 0;

            if (fixed === "left") {
                // 只计算左列固定的
                result = colgroups.reduce((total, currentVal, index) => {
                    return index < currentIndex && currentVal.fixed == "left"
                        ? Number(window.getComputedStyle(document.querySelectorAll(`#${this.randomId} td`)[index]).width.slice(0,-2)) + total
                        : total;
                }, 0);
            } else if (fixed === "right") {
                // 只计算右列固定的
                result = colgroups.reduce((total, currentVal, index) => {
                    return index > currentIndex && currentVal.fixed == "right"
                        ? Number(window.getComputedStyle(document.querySelectorAll(`#${this.randomId} td`)[index]).width.slice(0,-2)) + total
                        : total;
                }, 0);
            }

            return `${result}px`;
        },
        setFixedWidth(colgroups){
            colgroups.map(item=>{
                if(item.fixed) {
                    Vue.set(item,item.fixed,this.getFixedWidthByKey(this.internalColumns,item.key,item.keys,item.fixed));
                    if(item.children){
                        item.children.map(child=>{
                            Vue.set(child,"fixed",item.fixed)
                        })
                        this.setFixedWidth(item.children)
                    }
                }
            })
        },
        headerColumnsFlat(array){
            let newArray = [];
            const structHeaderArray = (array)=>{
                array.map(item=>{
                    newArray.push(item);
                    if(item.children){
                        structHeaderArray(item.children)
                    }
                })
            }
            structHeaderArray(array);
            return newArray;
        },
        bodyColumnsFlat(array){
            let newArray = [];
            const structBodyArray = (array)=>{
                array.map(item=>{
                    if(item.children){
                        structBodyArray(item.children)
                    }else{
                        newArray.push(item);
                    }
                })
            }
            structBodyArray(array);
            return newArray;
        },
        sortClick(item){
            if(!this.sortOption.multipleSort){
                this.clearColumsOthersSort(this.internalColumns,item.field)
            }
            
            if(this.sortOption.sortAlways){
                item.sortBy = item.sortBy == "desc" ? "asc" : "desc";
            }else{
                item.sortBy = item.sortBy == "desc" ? "asc" : item.sortBy == "asc" ? "" : "desc";
            }
            
            typeof this.sortOption.sortChange=='function' && this.sortOption.sortChange(item)
        },
        clearColumsOthersSort(columns,field){
            columns.map(item=>{
                if(item.field!=field){
                    if(item.sortBy) item.sortBy="";
                }
                if(item.children){
                    this.clearColumsOthersSort(item.children,field)
                }
            })
        },
        setHighlightRow(rowKey){
            this.nowHightlightKey = rowKey;
        },
        rowClick(rowItem,rowIndex){
            this.nowHightlightKey = rowItem[this.rowKeyFieldName];
            typeof this.rowOption.rowClick=='function' && this.rowOption.rowClick(rowItem,rowIndex)
        },
        headerCellClick(colItem,rowIndex,colIndex){
            Vue.set(this.selectHeaderCellOption,"key",colItem.key);
            Vue.set(this.selectHeaderCellOption,"rowIndex",rowIndex);
            typeof this.cellOption.headerCellClick=='function' && this.cellOption.headerCellClick(colItem,rowIndex,colIndex);
        },
        bodyCellClick(rowItem,colItem,rowIndex,colIndex){
            Vue.set(this.selectCellOption,"key",colItem.key);
            Vue.set(this.selectCellOption,this.rowKeyFieldName,rowItem[this.rowKeyFieldName]);
            typeof this.cellOption.bodyCellClick=='function' && this.cellOption.bodyCellClick(rowItem,colItem,rowIndex,colIndex);
        },
        headerCellMouseEnter(colItem,rowIndex,colIndex){
            typeof this.cellOption.headerCellMouseEnter=='function' && this.cellOption.headerCellMouseEnter(colItem,rowIndex,colIndex);
        },
        headerCellMouseLeave(colItem,rowIndex,colIndex){
            typeof this.cellOption.headerCellMouseLeave=='function' && this.cellOption.headerCellMouseLeave(colItem,rowIndex,colIndex);
        },
    },
    created(){
        // this.initTable()
    },
    mounted(){
        // this.setFixedTdStyle();
        window.addEventListener("resize",this.setFixedTdStyle())
    },
    destroy(){
        
    },
    watch:{
        columns:{
            handler(newVal,oldVal){
            

                this.initColumns();
                
                
            },
            immediate: true,
        },
        tableData:{
            handler(newVal,oldVal){
                [...this.internalTableData] = cloneDeep(this.tableData);
                this.$nextTick(()=>{
                    this.setFixedTdStyle();
                })
            },
            immediate: true,
        },
        footerData:{
            handler(newVal,oldVal){
                [...this.internalFooterData] = cloneDeep(this.footerData);
                this.$nextTick(()=>{
                    this.setFixedTdStyle();
                })
            },
            immediate: true,
        }
    }
}
</script>