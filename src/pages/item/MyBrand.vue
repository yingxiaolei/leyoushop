<template>
  <div>
    <v-data-table
      :headers="headers"
      :items="brands"
      :pagination.sync="pagination"
      :total-items="totalBrands"
      :loading="loading"
      class="elevation-1"
    >
    <template slot="items" slot-scope="props">
         <td class="text-xs-right">{{props.items.id}} </td>
        <td class="text-xs-right">{{props.items.name}} </td>
         <td class="text-xs-right">{{props.items.image}} </td>
          <td class="text-xs-right">{{props.items.letter}} </td>
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
                {text: "品牌id",value: "id",align: 'center',sortable:true},
                {text: "品牌名称",value: "name",align: 'center',sortable:false},
                {text: "品牌LOGO",value: "image",align: 'center',sortable:false},
                {text: "品牌首字母",value: "letter",align: 'center',sortable:true},
            ],
            brands: [],
            pagination: {},
            totalBrands: 0,
            loading: false,
        }
    },
    created(){
        this.brands = [
            {id:1,name:"小米",image:"",letter:"x"},
            {id:2,name:"大米",image:"222",letter:"3"}
        ];
     this.totalBrands = 15;
    }
}
</script>
<style scoped>

</style>