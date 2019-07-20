<template>
    <div style="margin-bottom: 520px;">
        <div class="adminHeader">
            <el-menu
            :default-active="activeIndex"
            mode="horizontal"
            background-color="#545c64"
            text-color="#fff"
            active-text-color="#ffd04b"
            router>
                <el-menu-item index="/admin"><i class="el-icon-shopping-bag-1"></i>网上购物系统-上架商品</el-menu-item>
                <el-menu-item class="item" @click="onExit"><i class="el-icon-error"></i>退出</el-menu-item>
                <el-menu-item index="/adminOrder" class="item"><i class="el-icon-s-order"></i>订单</el-menu-item>
            </el-menu>
        </div>

        <div>
            <div v-for="(item, index) in commodities" :key="item.goods_ID" class="commodityItem">
                <div>
                    <div class="inlineBlock" style="margin-left: 30px;">
                        <img :src="item.goods_Picture">
                    </div>
                    <div class="inlineBlock itemName">
                        <span>{{item.goods_Name}}</span>
                    </div>
                </div>
                <div>
                    <div class="inlineBlock info">
                        <span><span class="inlineBlock" style="width: 60px;">原价：</span><span class="oldPrice">￥{{item.goods_OldPrice}}</span></span>
                        <br>
                        <span><span class="inlineBlock" style="width: 60px;">优惠价：</span><span class="newPrice">￥{{item.goods_NewPrice}}</span></span>
                        <br>
                        <span><span class="inlineBlock" style="width: 60px;">类型：</span>{{item.goods_TypeName}}</span>
                        <br>
                        <span class="inlineBlock" style="width: 70px;">商品介绍：</span>
                        <p style="text-indent: 2em; margin: 0;">{{item.goods_Info}}</p>
                    </div>
                    <div class="inlineBlock" style="vertical-align: bottom;">
                        <el-button type="danger" round @click="remove(index)">下架商品</el-button>
                    </div>
                </div>
            </div>
            <div class="addButton">
                <el-button type="primary" round @click="dialogVisible = true;">上架商品</el-button>
            </div>
            <el-dialog
            title="上架商品"
            :visible.sync="dialogVisible"
            width="30%">

                <el-form label-width="100px" label-position="left">
                    <el-form-item label="商品名称：">
                        <el-input v-model="form.name"></el-input>
                    </el-form-item>
                    <el-form-item label="商品类型：">
                        <el-input v-model="form.type"></el-input>
                    </el-form-item>
                    <el-form-item label="商品原价：">
                        <el-input v-model="form.oldPrice"></el-input>
                    </el-form-item>
                    <el-form-item label="商品优惠价：">
                        <el-input v-model="form.newPrice"></el-input>
                    </el-form-item>
                    <el-form-item label="商品介绍：">
                        <el-input type="textarea" v-model="form.introduce" autosize></el-input>
                    </el-form-item>

                    <el-form-item label="商品图片：">
                        <img :src="temp" width="150px" height="150px">
                        <input type="file" @change="uploadImg" id="file">
                    </el-form-item>
                </el-form>

                <div slot="footer" style="margin-top: -40px;">
                    <el-button type="primary" @click="add" size="small">确 定</el-button>
                    <el-button @click="dialogVisible = false" size="small">取 消</el-button>
                </div>
            </el-dialog>
        </div>
    </div>
</template>

<script>

export default {
    data() {
        return {
            activeIndex: '/admin',
            dialogVisible: false,
            temp: null,

            form: {
                name: '',
                type: '',
                oldPrice: '',
                newPrice: '',
                introduce: '',
                img: ''
            },

            commodities: [
                
            ]
        }
    },
    methods: {
        remove(index) {
            var that = this;
            var id = this.commodities[index].goods_ID;
            this.$axios.get('/outGoods', {
                params: {
                    goods_ID: id
                }
            })
            .then(function (response) {
                var temp = that.commodities[index];
                that.commodities.splice(index, 1);
                alert('下架【' + temp.goods_Name + '】成功！');
            })
            .catch(function (error) {
                console.log(error);
            });
        },
        add() {
            var that = this;
            this.$axios.post('/inGoods', {
                goods_Name: this.form.name,
                goods_TypeName: this.form.type,
                goods_Info: this.form.introduce,
                goods_OldPrice: this.form.oldPrice,
                goods_NewPrice: this.form.newPrice,
                goods_Picture: this.temp
            })
            .then(function (response) {
                alert('上架商品成功！');
                that.$axios.get('/showAllGoods')
                .then(function (response) {
                    console.log(response.data);
                    that.commodities = response.data;
                })
                .catch(function (error) {
                    console.log(error);
                });
                that.form.name = '';
                that.form.type = '';
                that.form.introduce = '';
                that.form.oldPrice = '';
                that.form.newPrice = '';
                that.form.img = '';
                that.temp = null;
                var file = document.getElementById('file');
                file.value = '';
            })
            .catch(function (error) {
                console.log(error);
            });
            this.dialogVisible = false;
        },
        uploadImg(e) {
            var that = this;
            var reader = new FileReader();
            reader.readAsDataURL(e.target.files[0]);
            reader.onload = function() {
                that.form.img = this.result;
                that.temp = this.result;
            }
        },
        onExit() {
            this.$router.push('/');
            // 刷新页面
            window.location.reload(true);
        }
    },
    created() {
        var that = this;
        this.$axios.get('/showAllGoods')
        .then(function (response) {
            console.log(response.data);
            that.commodities = response.data;
        })
        .catch(function (error) {
            console.log(error);
        });
    },
}
</script>

<style scoped>
.adminHeader {
    width: 1400px;
    height: 60px;
    position: fixed;
    top: 0;
    background-color: #545c64;
    padding: 0 60px !important;
    margin-left: -20px;
}

.item {
    float: right !important;
}

.commodityItem {
    width: 1140px;
    background-color: white;
    padding: 20px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, .12), 0 0 6px rgba(0, 0, 0, .04);
    margin: 0 auto;
    margin-bottom: 30px;
}

.commodityItem:hover{
    border: solid 2px #F56C6C;
    border-radius: 4px;
}

.commodityItem img {
    width: 100px;
    height: 100px;
    object-fit: contain;
    vertical-align: top;
}

.inlineBlock {
    display: inline-block;
}

.itemName {
    width: 200px;
    font-size: 13px;
    margin-left: 20px;
    margin-right: 200px;
    vertical-align: text-top;
}

.newPrice {
    font-size: 22px;
    color: #f40;
}

.oldPrice {
    font-size: 16px;
    color: #909399;
    text-decoration: line-through;
}

.info {
    width: 750px;
    line-height: 26px;
    font-size: 14px;
    margin-left: 30px;
    margin-right: 200px;
    margin-top: 20px;
}

.addButton {
    position: fixed;
    top: 80px;
    left: 40px;
}
</style>
