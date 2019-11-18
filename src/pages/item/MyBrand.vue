<template>
  <div>
    <v-layout class="px-2 pb-2">
      <v-flex xs2>
          <v-btn color="info" @click="addBrand">新增品牌</v-btn>
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
   
    <v-dialog v-model="dialog" persistent max-width="500px" scrollable>
    
       <v-card>
        <!--对话框的标题-->
        <v-toolbar dense dark color="primary">
          <v-toolbar-title>{{isEdit ? '修改' : '新增'}}品牌</v-toolbar-title>
          <v-spacer/>
          <!--关闭窗口的按钮-->
          <v-btn icon @click="closeWindow"><v-icon>close</v-icon></v-btn>
        </v-toolbar>
        <!--对话框的内容，表单-->
        <v-card-text class="px-5" style="height:400px">
               <v-form v-model="valid" ref="myBrandForm">
                <v-text-field v-model="brand.name" label="请输入品牌名称" required />
                <v-text-field v-model="brand.letter" label="请输入品牌首字母" required />
                <v-cascader
                url="/item/category/list"
                multiple
                required
                v-model="brand.categories"
                label="请选择商品分类"/>
              </v-form>
              <v-layout row>
                  <v-flex xs3>
                    <span style="font-size: 16px; color: #444">品牌LOGO：</span>
                  </v-flex>
                  <v-flex>
                    <v-upload
                      v-model="brand.image" url="/upload/image" :multiple="false" :pic-width="250" :pic-height="90"
                    />
                  </v-flex>
                </v-layout>
              <v-layout class="my-4" row>
                  <v-spacer/>
                  <v-btn @click="submit" color="primary">提交</v-btn>
                  <v-btn @click="clear">重置</v-btn>
                </v-layout>
        </v-card-text>
      </v-card>
    </v-dialog>
  
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
            key:"",
            dialog:false,
            isEdit:false,
            valid:false,
            brand:{
              name:"",
              letter:"",
              categories:""
            },
            nameRules: [
              v => !!v || "品牌名称不能为空",
              v => v.length > 1 || "品牌名称至少2位"
            ],
            letterRules: [
              v => !!v || "首字母不能为空",
              v => /^[a-zA-Z]{1}$/.test(v) || "品牌字母只能是1个字母"
            ]
        }
    },
    created(){
     this.totalBrands = 15;
     this.loadBrands();
    },
    watch:{
        key(){
          this.pagination.page = 1;
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
        this.loading = true;
         this.$http.get('/item/brand/page',{params:{
          page: this.pagination.page, //当前页
          rows: this.pagination.rowsPerPage, //每页大小
          sortBy: this.pagination.sortBy,//排序字段
          desc: this.pagination.desccoding,//是否排序
          key: this.key//搜索条件
        }}).then(resp => {
          this.brands = resp.data.items
          this.totalBrands = resp.data.total
          this.loading = false;
        })
      },
      addBrand() {
        // // 修改标记
        this.isEdit = false;
        // 控制弹窗可见：
        this.dialog = true;
        // // 把oldBrand变为null
        // this.oldBrand = null;
      },
       closeWindow(){
        // 关闭窗口
        this.dialog = false;
      },
      clear() {
        // 重置表单
        this.$refs.myBrandForm.reset();
        // 需要手动清空商品分类
        this.categories = [];
      },
       submit() {
        // 表单校验
        if (this.$refs.myBrandForm.validate()) {
          // 定义一个请求参数对象，通过解构表达式来获取brand中的属性
          const {categories, letter, ...params} = this.brand;
          // 数据库中只要保存分类的id即可，因此我们对categories的值进行处理,只保留id，并转为字符串
          params.cids = categories.map(c => c.id).join(",");
          // 将字母都处理为大写
          params.letter = letter.toUpperCase();
          // 将数据提交到后台
          // this.$http.post('/item/brand', this.$qs.stringify(params))
          this.$http({
            method: this.isEdit ? 'put' : 'post',
            url: '/item/brand',
            data: this.$qs.stringify(params)
          }).then(() => {
            // 关闭窗口
            this.$emit("close");
            this.$message.success("保存成功！");
          })
            .catch(() => {
              this.$message.error("保存失败！");
            });
        }
      }
    }
}
</script>