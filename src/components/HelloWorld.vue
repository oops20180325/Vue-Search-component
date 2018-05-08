<template>
  <div class="hello">
    <!-- <mySearch :options='options' @search='upload'></mySearch> -->
    <dmsSearch/>
  </div>
</template>

<script>
import mySearch from "./Mysearch.vue"
import dmsSearch from './dmsSearch'
export default {
  name: 'HelloWorld',
  data () {
    return {
      // search
      // options:[{value:'编号',type:0},{value:'关键词',type:1},{value:'型号',type:2},{value:'生产日期',type:3},{value:'销售日期',type:3}]
      // dms search
      


    }
  },
  components:{
    mySearch,
    dmsSearch
  },
  methods:{
    // upload(e){ 
    //   console.log( '父元素收到',e) 
    // }

  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
