<template>
  <div>
    <v-layout class="px-2 pb-2">
      <v-flex xs2>
          <v-btn color="info">新增品牌</v-btn>
      </v-flex>
      <v-spacer/>
      <v-flex xs4>
          <v-text-field label="搜索"  hide-details append-icon="search" v-model="key">
          </v-text-field>
      </v-flex>
    </v-layout>
    <v-data-table
      :headers="headers"
      :items="brands"
      :pagination.sync="pagination"
      :total-items="totalBrands"
      :loading="loading"
      class="elevation-1"
    >
    <template slot="items" slot-scope="props">
          <td class="text-xs-center">{{props.item.id}} </td>
          <td class="text-xs-center">{{props.item.name}} </td>
          <td class="text-xs-center">{{props.item.image}} </td>
          <td class="text-xs-center">{{props.item.letter}} </td>
          <td class="text-xs-center">
            <v-btn flat icon color="info">
               <v-icon>edit</v-icon>
            </v-btn>
            <v-btn flat icon color="error">
               <v-icon>delete</v-icon>
            </v-btn>
          </td>
    </template>
    </v-data-table>
  </div>
</template>
<script>
export default {
    name: "MyBrand",
    data(){
        return{
            headers:[
                {text: "品牌id" ,value: "id",align: 'center',sortable:true},
                {text: "品牌名称" ,value: "name",align: 'center',sortable:false},
                {text: "品牌LOGO" ,value: "image",align: 'center',sortable:false},
                {text: "品牌首字母" ,value: "letter",align: 'center',sortable:true},
                 {text: "操作" ,align: 'center',sortable:false}
            ],
            brands: [],
            pagination: {},
            totalBrands: 0,
            loading: false,
            key:""
        }
    },
    created(){
     this.totalBrands = 15;
    },
    watch:{
        key(){
          this.loadBrands();
        },
        pagination:{
          deep: true,
          handler(){
            this.loadBrands();
          }
        }
    },
    methods:{
      loadBrands(){
         this.$http.get('/item/brand/page',{params:{
          page: this.pagination.page, //当前页
          rows: this.pagination.rowsPerPage, //每页大小
          sortBy: this.pagination.sortBy,//排序字段
          desc: this.pagination.desccoding,//是否排序
          key: this.key//搜索条件
        }}).then(resp => {
          this.brands = resp.data.items
           var b= Object.keys(resp.data.items);
           console.log(b);
          if (resp.data == null) {
            console.log("没有值");
          }
          let aa = resp.data.items.length
          console.log(aa)
          if (aa == 0) {
            this.$message("没用数据");
          }
          
        })
      }
    }
}
</script>