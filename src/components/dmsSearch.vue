<template>
    <div class="dms_search" ref="dms_search">
        <h5>
            后台传入的应该是一个关于搜索的对象类似 [{类型：文本，叫什么名字：联系人，规则：包含}，......（多个）]
        </h5>
        <!-- 一个小单元   for循环便利出来-->
        <div v-for="(item,index) in searchGroup" :key="index" class="search_item">
             <!-- if 文本 -->
            <!-- <div v-if="item.type==='text'">    这样写不报错v-if不能直接定义到item -->
            <div v-if="searchGroup[index].type==='text'" >
                <p class="searchTop">
                    <span>{{item.title}}</span>
                    <span>{{item.selected}}</span>
                    <select name="" id="" v-model="item['selected']" @change="conditionChange(item.selected,index)">
                        <option value="" disabled>请选择</option>
                        <option value="include">包含</option>
                        <option value="just">正好是</option>
                        <option value="except">不包含</option>
                        <option value="not">不是</option>
                        <option value="oneOf">之一</option>
                        <option value="start">开头是</option>
                        <option value="end">结尾是</option>
                    </select>
                    <button @click="removeSearchOption(index)">X</button>
                </p>
                <div>
                    <div v-for="(ls,ide) in item.list" :key="ide">
                        <input type="text" v-model="ls.content" >
                        <button @click="deleOneOf(index,ide)" v-show="item.list.length>1">×</button>
                    </div>

                    <!-- 如果是之一应该有添加按钮 -->
                    <button v-show="item.selected==='oneOf'" @click="addOneOf(index)">+添加</button>
                </div>
            </div>
            <!-- if 枚举 -->
            <div v-if="searchGroup[index].type==='selectes'">
                <p class="searchTop">
                    <span>{{item.title}}</span>
                    <select name="" id="" v-model="item['selected']" @change="conditionChange(item.selected,index)">
                        <option value="" disabled>请选择</option>
                        <option value="just">正好是</option>
                        <option value="not">不是</option>
                        <option value="oneOf">之一</option>
                    </select>
                    <button @click="removeSearchOption(index)" >X</button>
                </p>
                <div>
                    <div v-for="(ls,ide) in item.list" :key="ide">
                        <select name="" id="" v-model="ls.selected" >
                            <option value="" disabled >请选择</option>
                            <!-- v-for遍历 -->
                            <option :value="op.key" v-for="op in item.options" :key="op.key" >{{op.title}}</option>
                        </select>
                        <button @click="deleOneOf(index,ide)" v-show="item.list.length>1">×</button>
                    </div>

                    <!-- 如果是之一应该有添加按钮 -->
                    <button v-show="item.selected==='oneOf'" @click="addOneOf(index)">+添加</button>
                </div>
            </div>
            <!-- if 数字 -->
            <div v-if="searchGroup[index].type==='numbers'">
                 <p class="searchTop">
                    <span>{{item.title}}</span>
                    <span>{{item.selected}}</span>
                    <select name="" id="" v-model="item['selected']"  @change="conditionChange(item.selected,index)">
                        <option value="" disabled>请选择</option>
                        <option value="equal">等于</option>
                        <option value="lt">小于</option>
                        <option value="lte">小于等于</option>
                        <option value="gt">大于</option>
                        <option value="gte">大于等于</option>
                        <option value="between">介于</option>
                        <option value="oneOf">之一</option>
                    </select>
                    <button @click="removeSearchOption(index)">X</button>
                </p>
                <div>
                    <div v-for="(ls,ide) in item.list" :key="ide">
                        <div v-if="item.selected==='between'">
                             <input type="number" v-model="ls.contentstart"  placeholder='请输入起始数字'>
                             <input type="number" v-model="ls.contentend"  placeholder='请输入结束数字'>
                        </div>
                        <div v-else>
                            <input type="number" v-model="ls.content">
                             <button @click="deleOneOf(index,ide)" v-show="item.list.length>1">×</button>
                        </div>
                    </div>

                    <!-- 如果是之一应该有添加按钮 -->
                    <button v-show="item.selected==='oneOf'" @click="addOneOf(index)">+添加</button>
                </div>
            </div>
            <!-- if 日期 -->
            <div v-if="searchGroup[index].type==='dates'">
                <div class="searchTop">
                    <span>{{item.title}}</span>
                    <select name="" id="" v-model="item.selected" @change="conditionChange(item.selected,index)">
                        <option value="" disabled>请选择</option>
                        <option value="equal">等于</option>
                        <option value="lt">小于</option>
                        <option value="lte">小于等于</option>
                        <option value="gt">大于</option>
                        <option value="gte">大于等于</option>
                        <option value="between">介于</option>
                        <option value="oneOf">之一</option>
                    </select>
                    <button @click="removeSearchOption(index)">X</button>
                </div>
                <div>
                    <div v-for="(ls,ide) in item.list" :key="ide">
                        <div style="position:relative">
                            <!-- <div v-if="item.selected==='between'"> -->
                            <div v-show="showDateRange">
                                <el-date-picker
                                v-model="ls.dateRange"
                                size='small'
                                type="daterange"
                                range-separator="至"
                                value-format="yyyy-MM-dd"
                                start-placeholder="开始日期"
                                end-placeholder="结束日期">
                                </el-date-picker>
                                <button @click="deleOneOf(index,ide)" v-show="item.list.length>1">×</button>
                            </div>
                            <!-- <div v-if="item.selected !='between'"> -->
                            <div v-show="!showDateRange">
                                <!-- <input type="date" v-model="ls.date" name="" id=""> -->
                                <el-date-picker
                                v-model="ls.date"
                                size='small'
                                type="date"
                                value-format="yyyy-MM-dd"
                                placeholder="选择日期">
                                </el-date-picker>
                                <button @click="deleOneOf(index,ide)" v-show="item.list.length>1">×</button>
                            </div>
                        </div>

                    </div>

                    <!-- 之一的话需要添加 -->
                    <button v-show="item.selected==='oneOf'" @click="addOneOf(index)" >+添加</button>
                </div>
            </div>
            <!-- if 车系车型车款  四级联动-->
            <div v-if="searchGroup[index].type === 'carAbout'">
                <div class="searchTop">
                    <span>{{item.title}}</span>
                    <select name="" id="" v-model="item['selected']" @change="conditionChange(item.selected,index)">
                        <option value="" disabled>请选择</option>
                        <option value="include">包含</option>
                        <option value="just">正好是</option>
                        <option value="except">不包含</option>
                        <option value="not">不是</option>
                        <option value="oneOf">之一</option>
                        <option value="start">开头是</option>
                        <option value="end">结尾是</option>
                    </select>
                    <button @click="removeSearchOption(index)">X</button>
                </div>
                <div>
                    <div>
                        <div v-for="(ls,ide) in item.list" :key="ide">
                            <!-- 品牌 -->
                            <select name="" id="" @change="carBrandChange(index,ide,item.list[ide].brandID)" v-model="item.list[ide].brandID">
                                <option value="">请选择</option>
                                <!-- for 遍历要选的内容 -->
                                <option v-for="brand in carBrand" :key="brand.id" :value="brand.id">{{brand.title}}</option>
                            </select>
                            <!-- 车系 -->
                            <select name="" id="" @change="carSeriesChange(index,ide,item.list[ide].seriesID)" v-model="item.list[ide].seriesID">
                                <option value="">请选择</option>
                                <!-- for 遍历要选的内容 -->
                                <option :value="series.id" v-for="series in carSeries[ide]" :key="series.id">{{series.title}}</option>
                            </select>
                            <!-- 车型 -->
                            <select name="" id="" @change="carModelChange(index,ide,item.list[ide].modelID)" v-model="item.list[ide].modelID">
                                <option value="">请选择</option>
                                <!-- for 遍历要选的内容 -->
                                <option v-for="model in carModel[ide]" :value="model.id" :key="model.id">{{model.title}}</option>
                            </select>
                            <!-- 车款 -->
                            <select name="" id="" v-model="item.list[ide].styleID" >
                                <option value="">请选择</option>
                                <!-- for 遍历要选的内容 -->
                                <option v-for="style in carStyle[ide]" :key="style.id"  :value="style.id">{{style.title}}</option>
                            </select>
                            <button @click="deleOneOf(index,ide)" v-show="item.list.length>1">×</button>
                        </div>
                    </div>
                    <div>
                         <!-- 如果是之一则需要添加 -->
                         <button v-show="item.selected==='oneOf'" @click="addOneOf(index)">+添加</button>
                    </div>

                </div>
            </div>
            <!-- 地址 四级联动 -->
            <div v-if="searchGroup[index].type === 'address'">
                <div class="searchTop">
                    <span>{{item.title}}</span>
                    <button @click="removeSearchOption(index)">X</button>
                </div>
                <div>

                            <!-- 省 -->
                            <select name="" id="" @change="SelectProChange(index)" v-model="item.list.pro">
                                <option value="">省</option>
                                <option :value="pro.id" v-for="pro in proData" :key="pro.id">{{pro.area}}</option>
                            </select>
                            <!-- 市 -->
                            <select name="" id="" v-model='item.list.city' @change="SelectCityChange(index)">
                                <option value="">市</option>
                                <option :value="city.id" v-for="city in cityData" :key="city.id" >{{city.area}}</option>
                            </select>
                            <!-- 区县 -->
                            <select name="" id="" v-model="item.list.area" @change="SelectAreaChange(index)">
                                <option value="">区/县</option>
                                <option v-for="area in areaData" :key="area.key" :value="area.area">{{area.area}}</option>
                            </select>
                            <!-- 详细地址 -->
                            <input type="text" v-model="item.list.address" placeholder="请输入详细地址">

                </div>
            </div>
        </div>

       <!-- footer -->
       <div class="search_footer">
           <button @click="addCell">+添加筛选字段</button> &nbsp; <button @click="saveSearchGroup">保存</button>
            <br>
           <select name="" id="" v-if="addCellshow" @change="addCellChange(addCellSelect)" v-model="addCellSelect" onclick="event.stopPropagation();">
               <option value="" disabled>请选择筛选字段</option>
               <!-- 把索引传给 select 绑定的v-molde里 -->
               <option :value="index" v-for="(item,index) in addCellGroup" :key="index" :disabled="item.inSearchGroup">{{item.title}}</option>

           </select>
           <br>
           <button class="footer_btn" @click="searchResetting">重置</button> &nbsp; <button @click="searchMessage" class="footer_btn">查询</button>
       </div>
    </div>
</template>
<script>
    export default {
        name:'dmsSearch',
        data(){
            return {
                addCellSelect:'', // 储存添加此段selected 对应addCellGroup选中项的索引
                addCellGroup:[ // 储存添加字段列表
                    {title:'姓名',type:'text',list:[{}],selected:'',inSearchGroup:false},
                    {title:'出货日期',type:'dates',list:[{}],selected:"",inSearchGroup:false}, // list 数组里至少有ge空对象
                    {title:'工单号',type:'text',list:[{}],selected:'',inSearchGroup:true },
                    {title:'送修人',type:'selectes',list:[{selected:''}],selected:'',options:[{key:1,title:'张三'},{key:2,title:'李四'},{key:3,title:'王五'},{key:4,title:'赵六'}],inSearchGroup:true},
                    {title:'金额',type:'numbers',list:[{}],selected:'',inSearchGroup:true},
                    {title:'购买日期',type:'dates',list:[{}],selected:"equal",inSearchGroup:true}, // list 数组里至少有ge空对象
                    {title:'车型车款',type:'carAbout',list:[{brandID:'',seriesID:'',modelID:'',styleID:''}],inSearchGroup:true},
                    {title:'联系地址',type:'address',list:{pro:'',city:'',area:'',address:''},inSearchGroup:true},  // 省 暂时不用列表 后面可能会改回来
                ],
                addCellshow:false, // 添加筛选字段显示与否
                showDateRange:false, //修改element样式bug
                carBrand:[], // 汽车品牌列表
                carSeries:[], // 车系列表
                carModel:[], // 车型列表
                carStyle:[], // 车款列表
                proData:[], // 省份信息列表
                cityData:[], // 城市信息列表
                areaData:[], // 区县信息列表
                searchGroup:[ //储存搜索列表
                    { title:'工单号',type:'text',list:[{}],selected:''},
                    {title:'送修人',type:'selectes',list:[{selected:''}],selected:'',options:[{key:1,title:'张三'},{key:2,title:'李四'},{key:3,title:'王五'},{key:4,title:'赵六'}]},
                    {title:'金额',type:'numbers',list:[{}],selected:''},
                    {title:'购买日期',type:'dates',list:[{}],selected:"equal"}, // list 数组里至少有ge空对象
                    {title:'车型车款',type:'carAbout',list:[{brandID:'',seriesID:'',modelID:'',styleID:''}]},
                    {title:'联系地址',type:'address',list:{pro:'',city:'',area:'',address:''}},  // 省 暂时不用列表 后面可能会改回来
                ],
                // searchlist:[
                //     {
                //         title:'车系车款车型', // 搜索项目标题
                //         type:'careAbout',  // 类型 文本 数字 车型车系等
                //         rules:'oneOf',  // 类型 之一包含等
                //         list:[    // 搜索内容列表 如果是之一 就有好几个不等

                //         ]
                //     }
                // ] // 储存搜索列表

            }
        },
        methods:{
            // 添加单元
            addCell(){
                event.stopPropagation();
                this.addCellshow = true
                this.addCellSelect = '';
            },
            // 添加单元select选中
            addCellChange(addCellSelectIndex){
                console.log('addCellChange',addCellSelectIndex);
                // do something;
                this.searchGroup.push(this.addCellGroup[addCellSelectIndex]);
                this.addCellGroup[addCellSelectIndex].inSearchGroup = true;
                this.addCellshow = false;
            },
            // 按条件查询
            searchMessage(){
                console.log('查询条件',this.searchGroup)
            },
            // 保存查询项目
            saveSearchGroup(){
                console.log('保存查新项目');
                // console.log(this.searchGroup)
                let saveGroup = []; // 保存搜索项目
                this.searchGroup.forEach((item,index) =>{
                    saveGroup[index] = {};
                    saveGroup[index].title = item.title;
                    saveGroup[index].type = item.type;
                    saveGroup[index].list = [{}];
                    saveGroup[index].selected = item.selected;
                })
                // 指针问题不能用 ，如果要用得序列化和反序列化
                // Object.assign(saveGroup,this.searchGroup)
                // console.log(saveGroup)
                // saveGroup.forEach((item,index) =>{
                //     Object.keys(item).forEach(k =>{
                //         switch(k){
                //             case 'list':
                //             saveGroup[index][k] = [{}]; break;

                //         }
                //     })
                // })
                console.log('after',saveGroup)

            },
            // 获取省份列表
            getProData(){
                // 假数据
                this.proData = [
                    {id:1,area:'陕西'},{id:2,area:'河南'},{id:3,area:'江苏'}
                ]
            },
            // 获取城市列表
            getCityData(){
                // 假数据
                this.cityData = [{id:101,area:'xian'},{id:102,area:'咸阳'}]
            },
            // 获取区县列表
            getAreaData(){
                // 假数据
                this.areaData = [{id:10101,area:'未央'},{id:10102,area:'户县'}]
            },
            // 区县改变
            SelectAreaChange(index){
                // 1.记录数据
                // 2. 初始化 具体地址
                this.searchGroup[index].list.address = '';
            },
            // 城市改变
            SelectCityChange(index){
                console.log('城市改变',this.searchGroup[index].list)
                // 1.记录数据
                // 2.Api获取 区域列表
                this.getAreaData();
                // 3.初始化 县 具体地址
                this.searchGroup[index].list.area='';
                this.searchGroup[index].list.address='';
            },
            // 省份改变
            SelectProChange(index){
                console.log('省分改变',this.searchGroup[index].list)
                // 1.记录数据
                // 2.API获取市级信息 //省份加载时以获取 这里获取城市
                this.getCityData();
                // 3.初始化市 县 具体
                // this.cityData = [{id:'',area}]
                this.searchGroup[index].list.city = '';
                this.searchGroup[index].list.area = '';
                this.searchGroup[index].list.address= ''

            },
            // 移除之一
            deleOneOf(index,ide){
                // console.log(index,ide)
                console.log(this.searchGroup[index].list);
                this.searchGroup[index].list.splice(ide,1);
                console.log(this.searchGroup[index].list);
            },
            // 之一 时添加选项
            addOneOf(index){
                if(this.searchGroup[index].type==='carAbout'){
                    console.log('add careAbout');
                    this.searchGroup[index].list.push({brandID:'',seriesID:'',modelID:'',styleID:''});
                }else if(this.searchGroup[index].type==='selectes' ){
                        console.log('add selectes');
                        this.searchGroup[index].list.push({selected:''});
                }else{
                    this.searchGroup[index].list.push({});
                }

            },
            // 移除搜索项乘员
            removeSearchOption(index){
                // 修改addCellGroup
                let flag =  this.searchGroup[index].title;  // title 在这里做唯一标识
                let deleIndex = this.addCellGroup.findIndex((value,index,arr) =>{
                    return value.title ===flag;
                })
                console.log('编辑addCellGroup里的索引',deleIndex)
                this.addCellGroup[deleIndex].inSearchGroup = false;
                // 修改searchGroup
                this.searchGroup.splice(index,1)
                console.log('移除第',index,'个');
            },
            // 搜索条件改变时
            conditionChange(selet,index){
                console.log('选择了',selet,'当前单元的索引是',index,'索引用于区分哪个改变')
                // 如果不是之一的时候需要对searchGroup做处理
                if(selet !='oneOf'){
                    console.log('之一');
                    this.searchGroup[index].list.splice(1)
                    console.log(this.searchGroup[index])
                    // console.log(this.searchGroup[index].list)
                }
                // 处理如果是between
                if(selet ==='between'){
                    console.log('之间')
                    // 日期之间
                    if(this.searchGroup[index].type ==="dates"){
                        console.log('日期之间');
                        this.searchGroup[index].list = [{}];
                         this.showDateRange =true ; // 修改enelemt样式bug
                    }
                }else{
                    // 不是日期之间
                    if(this.searchGroup[index].type ==="dates"){
                        console.log('日期不是之间');
                        // this.searchGroup[index].list = [{}];  //直接置空对象
                        delete this.searchGroup[index].list[0].dateRange; // 或 清空队形属性（好处时可以保留日期里的结果
                         this.showDateRange =false ;// 修改enelemt样式bug
                    }
                }
            },
            // 获取汽车品牌信息
            getBrandData(){
                // Api
                // 先写假数据
                this.carBrand = [{id:1,title:'奥迪'},{id:2,title:'奔驰'}];
            },
            // 获取车系数据
            getSeriesData(ide){
                // 假数据
                this.carSeries[ide] = [{id:101,title:'Q'},{id:102,title:'XXL'},{id:103,title:'XXXL'}];
            },
            // 获取车型数据
            getModelData(ide){
                // 假数据
                this.carModel[ide] = [{id:101011,title:'车型1'},{id:1022,title:'车型2'}];
            },
            // 获取车款数据
            getStyleData(ide){
                this.carStyle[ide] = [{id:11012,title:'车款1'},{id:11013,title:'车款2'},{id:11014,title:'车款4'},{id:11015,title:'车款5'}];
            },
            // 汽车品牌改变时
            carBrandChange(index,ide,brandId){
                console.log('汽车品牌改变',brandId);
                 // 0. 清空后面 select option
                this.carSeries[ide] = [];
                this.carModel[ide] = [];
                this.carStyle[ide] = [];
                // 1.发送请求获取 车系 数据 组件加载时已经获取
                this.getSeriesData(ide);
                //2.记录数据

                // 3.初始化 车型 车系 车款
                this.searchGroup[index].list[ide].seriesID = '';
                this.searchGroup[index].list[ide].modelID = '';
                this.searchGroup[index].list[ide].styleID = '';
            },
            // 车系改变时
            carSeriesChange(index,ide,seriesId){
                console.log('车系改变',seriesId)
                // 0. 清空后面 select option
                this.carModel[ide] = [];
                this.carStyle[ide] = [];
                // 1.发送请求获取 车型 数据
                this.getModelData(ide);
                //2.记录数据

                // 3.初始化 车型  车款
                this.searchGroup[index].list[ide].modelID = '';
                this.searchGroup[index].list[ide].styleID = '';
            },
            // 车型改变时
            carModelChange(index,ide,modelId){
                console.log('车型改变',modelId)
                 // 0. 清空后面 select option
                this.carStyle[ide] = [];
                // 1.发送请求获取 车型 数据
                this.getStyleData(ide);
                //2.记录数据

                // 3.初始化 车型  车款
                this.searchGroup[index].list[ide].styleID = '';
            },
            // 搜索重置
            searchResetting(){
                this.searchGroup.forEach((value,index)=>{
                    switch (value.type) {
                        case 'selectes':
                            value.list = [{selected:''}];
                            break;
                        case 'carAbout':
                            value.list = [{brandID:'',seriesID:'',modelID:'',styleID:''}];
                            break;
                        case 'address':
                            value.list = {pro:'',city:'',area:'',address:''};
                            break;
                        default:
                            value.list = [{}];
                            break;
                    }
                })
            }
        },
        mounted(){
            // 获取汽车品牌信息
            this.getBrandData();
            // 获取省份
            this.getProData();
            //点击des_search关闭addCellSelect
            this.$refs.dms_search.onclick = ()=>{
                if(this.addCellshow === true){
                     this.addCellshow = false;
                }
            }
        }
    }
</script>
<style lang='less'>
.dms_search {
    // border: 1px solid #bcf127;
    padding: 20px 0;
    width: 100%;
    height: 100%;
    background-color: #575757;
    opacity: 0.9;
    color: #fff;
    overflow-y: auto;
    .search_item{
        margin: 10px 0;
    }
    .search_item:hover .searchTop button {
        display: inline-block;
        float: right;
    }
    .searchTop{
        padding-right: 20px;
        button {
            float: right;
            display: none;
            border-radius: 50%;
            width: 20px;
            height: 20px;
            padding: 0;
            text-align: center;
        }
        select {
            background-color: #575757;
            color: #fff;
            border: none;
            // height: 30px;
            width: 80px;
            option {
                color: black;
                background-color: #fff;
            }
        }
    }
    select,input{
        border: 1px solid #ccc ;
        border-radius: 4px;
        height: 30px;
        // width: 230px;
    }
    button {
        position: relative;
        overflow: hidden;
        height: 32px;
        border: none;
        border-radius: 4px;
        color: #fff;
        background: #575757;
        padding: 6px 15px;
        line-height: 15px;
        cursor: pointer;
    }
    .footer_btn {
        background: #D3E8FD;
        color: black;
        width: 100px;
    }
    &::-webkit-scrollbar {
        width: 10px;
        height: 10px;
    }
    &::-webkit-scrollbar-thumb {
        /*滚动条里面小方块*/
        border-radius: 5px;
        -webkit-box-shadow: inset 0 0 5px rgba(0, 0, 0, 0.2);
        background: #c2c2c2;
    }
    &::-webkit-scrollbar-track {
        /*滚动条里面轨道*/
        -webkit-box-shadow: inset 0 0 5px rgba(0, 0, 0, 0.2);
        border-radius: 0;
        background: rgba(0, 0, 0, 0.1);
    }
}

</style>

