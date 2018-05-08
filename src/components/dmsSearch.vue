<template>
    <div class="dms_search">
        <h5>
            后台传入的应该是一个关于搜索的对象类似 [{类型：文本，叫什么名字：联系人，规则：包含}，......（多个）]
        </h5>
        <!-- 一个小单元   for循环便利出来-->
        <div v-for="(item,index) in searchGroup" :key="index">
             <!-- if 文本 -->
            <!-- <div v-if="item.type==='text'">    这样写不报错v-if不能直接定义到item -->
            <div v-if="searchGroup[index].type==='text'">
                <p>
                    <span>{{item.title}}</span>
                    <span>{{item.selected}}</span>
                    <select name="" id="" v-model="item['selected']">
                        <option value="" disabled>请选择</option>
                        <option value="include">包含</option>
                        <option value="just">正好是</option>
                        <option value="except">不包含</option>
                        <option value="not">不是</option>
                        <option value="oneOf">之一</option>
                        <option value="start">开头是</option>
                        <option value="end">结尾是</option>
                    </select>
                    <button @click="removeSearchOption(index)">移除搜索单元</button>
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
                <p>
                    <span>{{item.title}}</span>
                    <select name="" id="" v-model="item['selected']">
                        <option value="" disabled>请选择</option>
                        <option value="just">正好是</option>
                        <option value="not">不是</option>
                        <option value="oneOf">之一</option>
                    </select>
                    <button @click="removeSearchOption(index)" >移除搜索单元</button>
                </p>
                <div>
                    <div v-for="(ls,ide) in item.list" :key="ide">
                        <select name="" id="" v-model="ls.selected">
                            <option value="0">请选择</option>
                            <option value="1">请</option>
                            <option value="2">333</option>
                            <!-- v-for遍历 -->
                            <!-- <option value="" v-for=""></option> -->
                        </select>
                        <button @click="deleOneOf(index,ide)" v-show="item.list.length>1">×</button>
                    </div>

                    <!-- 如果是之一应该有添加按钮 -->
                    <button v-show="item.selected==='oneOf'" @click="addOneOf(index)">+添加</button>
                </div>
            </div>
            <!-- if 数字 -->
            <div v-if="searchGroup[index].type==='numbers'">
                 <p>
                    <span>{{item.title}}</span>
                    <span>{{item.selected}}</span>
                    <select name="" id="" v-model="item['selected']">
                        <option value="" disabled>请选择</option>
                        <option value="equal">等于</option>
                        <option value="lt">小于</option>
                        <option value="lte">小于等于</option>
                        <option value="gt">大于</option>
                        <option value="gte">大于等于</option>
                        <option value="between">介于</option>
                        <option value="oneOf">之一</option>
                    </select>
                    <button @click="removeSearchOption(index)">移除搜索单元</button>
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
                <div>
                    <span>{{item.title}}</span>
                    <select name="" id="">
                        <option value="">等于</option>
                        <option value="">小于</option>
                        <option value="">小于等于</option>
                        <option value="">大于</option>
                        <option value="">大于等于</option>
                        <option value="">介于</option>
                        <option value="">之一</option>
                    </select>
                    <button @click="removeSearchOption(index)">移除搜索单元</button>
                </div>
                <div>
                    <input type="date" name="" id="">
                    <!-- 之一的话需要添加 -->
                    <button>+添加</button>
                </div>
            </div>
            <!-- if 车系车型车款  四级联动-->
            <div v-if="searchGroup[index].type === 'carAbout'">
                <div>
                    <span>{{item.title}}</span>
                    <select name="" id="" v-model="item['selected']">
                        <option value="">请选择</option>
                        <option value="include">包含</option>
                        <option value="just">正好是</option>
                        <option value="except">不包含</option>
                        <option value="not">不是</option>
                        <option value="oneOf">之一</option>
                        <option value="start">开头是</option>
                        <option value="end">结尾是</option>
                    </select>
                    <button @click="removeSearchOption(index)">移除搜索单元</button>
                </div>
                <div>
                    <div>
                        <div v-for="(ls,ide) in item.list" :key="ide">
                            <!-- 品牌 -->
                            <select name="" id="" @change="carBrandChange(index,ide)" v-model="item.list[ide].brand">
                                <option value="">请选择</option>
                                <!-- for 遍历要选的内容 -->
                                <option v-for="brand in carBrand" :key="brand.id" :value="brand.id">{{brand.title}}</option>
                            </select>
                            <!-- 车系 -->
                            <select name="" id="" @change="carSeriesChange(index,ide)" v-model="item.list[ide].series">
                                <option value="">请选择</option>
                                <!-- for 遍历要选的内容 -->
                                <option :value="series" v-for="series in carSeries" :key="series.id">{{series.title}}</option>
                            </select>
                            <!-- 车型 -->
                            <select name="" id="" @change="carModelChange(index,ide)" v-model="item.list[ide].model">
                                <option value="">请选择</option>
                                <!-- for 遍历要选的内容 -->
                                <option v-for="model in carModel" :value="model.id" :key="model.id">{{model.title}}</option>
                            </select>
                            <!-- 车款 -->
                            <select name="" id="" v-model="item.list[ide].style" >
                                <option value="">请选择</option>
                                <!-- for 遍历要选的内容 -->
                                <option v-for="style in carStyle" :key="style.id"  :value="style.id">{{style.title}}</option>
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
                <div>
                    <span>{{item.title}}</span>
                    <button @click="removeSearchOption(index)">移除搜索单元</button>
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
           <button>+添加筛选字段</button> &nbsp; <button @click="saveSearchGroup">保存</button>
           <br>
           <button>重置</button> &nbsp; <button @click="searchMessage">查询</button>
       </div>
    </div>
</template>
<script>
    export default {
        name:'dmsSearch',
        data(){
            return {
                carBrand:[], // 汽车品牌列表
                carSeries:[], // 车系列表
                carModel:[], // 车型列表
                carStyle:[], // 车款列表
                proData:[], // 省份信息列表
                cityData:[], // 城市信息列表
                areaData:[], // 区县信息列表
                searchGroup:[ //储存搜索列表
                    { title:'工单号',type:'text',list:[{}],selected:''},
                    // {title:'送修人',type:'selectes',list:[{}],selected:''},
                    // {title:'金额',type:'numbers',list:[{}],selected:''},
                    // {title:'购买日期',type:'dates'},
                    {title:'车型车款',type:'carAbout',list:[{brand:'',series:'',model:'',style:''}]},
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
            // 按条件查询
            searchMessage(){

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
                this.searchGroup[index].list.push({});
            },
            // 移除搜索项乘员
            removeSearchOption(index){
                this.searchGroup.splice(index,1)
                console.log('移除第',index,'个')
            },
            // 获取汽车品牌信息
            getBrandData(){
                // Api
                // 先写假数据
                this.carBrand = [{id:1,title:'奥迪'},{id:2,title:'奔驰'}];
            },
            // 获取车系数据
            getSeriesData(){
                // 假数据
                this.carSeries = [{id:101,title:'Q'},{id:102,title:'XXL'},{id:103,title:'XXXL'}]
            },
            // 获取车型数据
            getModelData(){
                // 假数据
                this.carModel = [{id:101011,title:'车型1'},{id:1022,title:'车型2'}]
            },
            // 获取车款数据
            getStyleData(){
                this.carStyle = [{id:11012,title:'车款1'},{id:11013,title:'车款2'},{id:11014,title:'车款4'},{id:11015,title:'车款5'},]
            },
            // 汽车品牌改变时
            carBrandChange(index,ide){
                console.log('汽车品牌改变');
                 // 0. 清空后面 select option
                this.carSeries = [];
                this.carModel = [];
                this.carStyle = [];
                // 1.发送请求获取 车系 数据 组件加载时已经获取
                this.getSeriesData();
                //2.记录数据

                // 3.初始化 车型 车系 车款
                this.searchGroup[index].list[ide].series = '';
                this.searchGroup[index].list[ide].model = '';
                this.searchGroup[index].list[ide].style = '';
            },
            // 车系改变时
            carSeriesChange(index,ide){
                // 0. 清空后面 select option
                this.carModel = [];
                this.carStyle = [];
                // 1.发送请求获取 车型 数据
                this.getModelData();
                //2.记录数据

                // 3.初始化 车型  车款
                this.searchGroup[index].list[ide].model = '';
                this.searchGroup[index].list[ide].style = '';
            },
            // 车型改变时
            carModelChange(index,ide){
                 // 0. 清空后面 select option
                this.carStyle = [];
                // 1.发送请求获取 车型 数据
                this.getStyleData();
                //2.记录数据

                // 3.初始化 车型  车款
                this.searchGroup[index].list[ide].style = '';
            },
        },
        mounted(){
            // 获取汽车品牌信息
            this.getBrandData();
            // 获取省份
            this.getProData();
        }
    }
</script>
