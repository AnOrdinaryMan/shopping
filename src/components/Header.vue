<template>
    <div>
        <el-menu
        :default-active="activeIndex"
        mode="horizontal"
        background-color="#545c64"
        text-color="#fff"
        active-text-color="#ffd04b"
        router>
            <el-menu-item index="/"><i class="el-icon-shopping-bag-1"></i>网上购物系统</el-menu-item>
            <el-menu-item :index="menuItem.index" class="item"><i class="el-icon-s-check"></i>{{menuItem.name}}</el-menu-item>
            <el-menu-item index="/collect" class="item"><i class="el-icon-star-on"></i>收藏夹</el-menu-item>
            <el-menu-item index="/order" class="item"><i class="el-icon-s-order"></i>我的订单</el-menu-item>
            <el-menu-item index="/shopCart" class="item"><i class="el-icon-shopping-cart-2"></i>购物车</el-menu-item>
        </el-menu>
    </div>
</template>

<script>
export default {
    data() {
        return {
            activeIndex: '/',

            menuItem: {
                index: '',
                name: ''
            }
        }
    },
    methods: {

    },
    created() {
        if(!localStorage.getItem('user')) {
            this.menuItem.index = '/login';
            this.menuItem.name = '登陆 / 注册';
        } else {
            this.menuItem.index = '/user';
            this.menuItem.name = '个人中心';
        }
    },
};
</script>

<style scoped>
.item {
    float: right !important;
}
</style>

