<template>
    <div>
        <div><button @click="addSearch">添加查询</button></div>
        <div>
            <!-- 第一块 -->
            <select name="" id="" v-model="selected">
                <option value="" selected="selected" disabled >需求名称</option>
                <option 
                    v-for="(option,index) in options" :key="index"
                    :value="option.type"
                >{{option.value}}</option>
            </select>
            <!-- 第二块 -->
            <select name="" id="" v-show="selected===0" @change="selected2Val" >
                <option value="" selected="selected" disabled>请选择</option>

                <option value="equal">等于</option>
                <option value="lessThan">小于</option>
                <option value="greatThan">大于</option>
                <option value="notEqual">不等于</option>
            </select>
            <select name="" id="" v-show="selected===1||selected==2" @change="selected2Val">

                <option value="" selected="selected" disabled>请选择</option>

                <option value="conten">包含</option>
                <option value="notConten">不包含</option>
            </select>
            <select name="" id="" v-show="selected===3" @change="selected2Val">

                <option value="" selected="selected" disabled>请选择</option>

                <option value="before">之前</option>
               <option value="after">之后</option>
               <option value="onTheDay">当天</option>
               <option value="beteween">之间</option>
            </select>
            <!-- 第三块 -->
            <template v-if="selected===0||selected===1">
                <input type="text" >
            </template>
            <template v-else-if="selected===2">
                <select name="" id="">
                    <!-- 型号提前定义好或者传入 -->
                    <option value="">型号1</option>
                    <option value="">型号2</option>
                    <option value="">型号3</option>
                    <option value="">型号4</option>
                </select>
            </template>
            <template v-else-if="selected===3">
                <!-- <input type="date"> -->
                <!-- <span v-if="selected2==='beteween'">至</span> -->
                <!-- <input type="date" v-if="selected2==='beteween'"> -->
<Date-picker :confirm=true :type="selected2==='beteween'?'daterange':'date'" placement="bottom-end" placeholder="选择日期"
 style="width: 200px" @on-change='datechange' ref="datepacker"></Date-picker>







            </template>
        </div>
        <!-- new group -->
        <div class="search" v-for="(item,index) in newSearchGroups" :key="index">
            <hr>
            <div>
                <select name="" id="" >
                    <option value="and">并且</option>
                    <option value="or">或者</option>
                </select>
            </div>
            <div>
            <!-- 第一块 -->
            <!-- <select name="" :id="index"  @change="selected3Val"> -->
            <select name="" :id="index"  v-model="newSearchGroups[index]['selected']">
                <option value="" selected="selected" disabled>需求名称</option>
                <option v-for="(option,index) in options" :key="index" :value="option.type">{{option.value}}</option>
            </select>
            <!-- 第二块 -->
            <select name="" :id="index" v-show="newSearchGroups[index]['selected']===0" @change="selected3Val">

                <option value="" selected="selected" disabled>请选择</option>

                <option value="equal">等于</option>
                <option value="lessThan">小于</option>
                <option value="greatThan">大于</option>
                <option value="notEqual">不等于</option>
            </select>
            <select name="" :id="index" v-show="newSearchGroups[index]['selected']===1||newSearchGroups[index]['selected']==2" @change="selected3Val">



                <option value="" selected="selected" disabled>请选择</option>

                <option value="conten">包含</option>
                <option value="notConten">不包含</option>
            </select>
            <select name="" :id="index" v-show="newSearchGroups[index]['selected']===3" @change="selected3Val">


                <option value="" selected="selected" disabled>请选择</option>

                <option value="before">之前</option>
                <option value="after">之后</option>
                <option value="onTheDay">当天</option>
                <option value="beteween">之间</option>
            </select>
            <!-- 第三块 -->
            <template v-if="newSearchGroups[index]['selected']===0||newSearchGroups[index]['selected']===1">


                <input type="text">
            </template>
            <template v-else-if="newSearchGroups[index]['selected']===2">

                <select name="" id="">
                <!-- 型号提前定义好或者传入 -->
                <option value="">型号1</option>
                <option value="">型号2</option>
                <option value="">型号3</option>
                <option value="">型号4</option>
                </select>
            </template>
            <template v-else-if="newSearchGroups[index]['selected']===3">

                <input type="date">
            <span v-if="newSearchGroups[index]['selected2']==='beteween'">至</span>

            <input type="date" v-if="newSearchGroups[index]['selected2']==='beteween'">

            </template>
            </div>
        </div>
        <div><button @click="search">搜索</button></div>
    </div>
</template>
<script>
//  默认会有一组 searchgroup   添加的searchgroup放在 newSarchGroup中  剩下要做的就是把 search里的参数拼接取来  广播给父元素. 还应该加个删除searchgroup的按钮
    export default  {
        data(){
            return{
                newSearchGroups:[],//存放搜索组 
                selected:'' ,//标记第一个select 那个被选中的option
                selected2:'',
                selected3:'',
                id:1, // 暂时用来存储一个标识 
            }
        },
        props:{
            options:Array
        },
        created(){
            console.log(this.options)
        },
        watch:{
            // 监听那个被选中
            selected(after,before){
                // console.log(before,after) //
                console.log(this.selected)
            }
        },
        methods:{
            selected2Val(e){
                this.selected2 = e.target.value
                console.log(' selected2:',this.selected2)
            },
            selected3Val(e){
                console.log(e.target.value);
                console.log(e.target.id);
                console.log(this.newSearchGroups[e.target.id]);
                this.newSearchGroups[e.target.id]['selected2'] = e.target.value

            },
            // 添加新的查询模块
            addSearch(){
            this.newSearchGroups.push({id:this.id++,selected:'',selected2:'',selected3:''})



            },
            // 拼接数据然后把 拼接好的数据广播出去
            search(){
                console.log('向父元素广播一个事件');
                this.$emit('search','啦啦啦')

            },
            datechange(){
                console.log(this.$refs.datepacker)
                console.log(this.$refs.datepacker.visualValue)

            }




        }
    }
</script>
<style scoped> 
    
</style>
