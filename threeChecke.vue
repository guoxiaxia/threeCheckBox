<!--
 * @Description: 
 * @Version: 2.0
 * @Author: guoxx
 * @Date: 2019-09-06 17:58:36
 * @lastEditors: guoxx
 * @LastEditTime: 2019-09-07 09:07:33
 -->
<template>
    <div class="container">
        <div class="table_list">
            <div class="flexB">
                <div class="btns">
                    <el-button type="primary" @click="deletes">批量移除</el-button>
                </div>
            </div>

            <table class="all_title" border="0" cellpadding="0" cellspacing="0">
                <thead>
                    <tr>
                        <th width="30px">
                            <el-checkbox
                                :indeterminate="isIndeterminateAll"
                                v-model="selectCurrentAll"
                                @change="isSelectCurrentAll"
                            ></el-checkbox>
                        </th>
                        <th
                            v-for="(item,index) in currentShops"
                            :key="index+'-'"
                            :width="item.width"
                        >{{item.title}}</th>
                    </tr>
                </thead>
            </table>
            <table
                class="list_title table"
                border="0"
                cellspacing="0"
                cellpadding="0"
                v-for="(shop,index) in shopList"
                :key="index+'/'"
            >
                <thead>
                    <tr>
                        <th width="30px">
                            <el-checkbox
                                :indeterminate="isIndeterminate[index]"
                                v-model="selectCurrentShop[index]"
                                @change="isSelectCurrentShop(shop, index)"
                            ></el-checkbox>
                        </th>
                        <th colspan="9" class="tableFlex">
                            <div class="name">
                                <p class="shopTitle">商品：</p>
                                {{shop.title}}
                            </div>
                        </th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="(item,j) in shop.article_goods" :key="'index'+j">
                        <td width="30px">
                            <el-checkbox
                                @change="isSelectShop(item, shop, index, j)"
                                v-model="selectShop[index][j]"
                            ></el-checkbox>
                        </td>
                        <td width="100px">
                            <div class="pro_de">
                                <img :src="item.img_url" alt />
                            </div>
                        </td>
                        <td>{{item.spec_text}}</td>
                        <td width="10%" class="btnDel">
                            <el-button type="text" @click="currentDel(item,index)">移除</el-button>
                        </td>
                    </tr>
                </tbody>
            </table>
        </div>
    </div>
</template>
<script>
export default {
    name: "platActivity",
    data() {
        return {
            currentShops: [
                { title: "商品缩略图", width: "100px" },
                { title: "规格" },
                { title: "操作" }
            ],
            //复选框绑定值
            //一级复选框
            selectCurrentAll: false,
            //二级复选框
            selectCurrentShop: [],
            //三级复选框
            selectShop: [],
            //记录选中商品的id集合，map对象
            //[{1:[2,3},{4:[5]}]
            submitShopIds: null,
            //不确定状态
            //一级不确定
            isIndeterminateAll: false,
            //二级不确定
            isIndeterminate: [],
            shopList: [
                {
                    articleId: 10,
                    title: "商品1",
                    article_goods: [
                        {
                            id: 1,
                            spec_text: "红色",
                            img_url:
                                "//img.alicdn.com/imgextra/i4/790274250/TB2oc7xkPuhSKJjSspjXXci8VXa_!!790274250-0-daren.jpg_180x180xzq90.jpg_.webp"
                        },
                        {
                            id: 2,
                            spec_text: "蓝色",
                            img_url:
                                "//img.alicdn.com/imgextra/i1/693739777/TB2TW6MtwxlpuFjy0FoXXa.lXXa_!!693739777-0-daren.jpg_180x180xzq90.jpg_.webp"
                        }
                    ]
                },
                {
                    articleId: 9,
                    title: "商品2",
                    article_goods: [
                        {
                            id: 4,
                            spec_text: "粉色",
                            img_url:
                                "//img.alicdn.com/tfscom/i1/2103587316/TB2bi0Ub_SPY1JjSZPcXXXIwpXa_!!2103587316.jpg_180x180xzq90.jpg_.webp"
                        },
                    ]
                }
            ]
        };
    },
    created() {
        //初始化map对象
        this.submitShopIds = new Map();
        this.initBtn();
    },
    methods: {
        //初始化复选框
        initBtn() {
            this.selectCurrentShop = [];
            this.isIndeterminate = [];
            this.selectShop = [];
            this.selectCurrentAll = false;
            this.isIndeterminateAll = false;
            this.shopList.forEach((item, index) => {
                this.selectCurrentShop.push(false);
                this.isIndeterminate.push(false);
                this.selectShop[index] = [];
                item.article_goods.forEach((ele, i) => {
                    this.selectShop[index].push(false);
                });
            });
        },

        /**
         * @desciption: 获取所选商品和规格属性的id集合
         * @param {arr} 商品列表集合
         * @param {currentData} 为当前已选商品--对象，专用于处理三级复选框
         * @return:
         * @author: guoxx
         */
        filterIds(arr, currentData) {
            if (!arr || !arr.length) return;
            arr.forEach((ele, i) => {
                // 处理三级复选框
                if (currentData && Object.prototype.toString.call(currentData) === "[object Object]") {
                    // article_goods 存放商品规格
                    let value = ele.article_goods.forEach(e => {
                        console.log(e, currentData)
                        //得到当前商品
                        if (e.id === currentData.id) {
                            //得到该商品已有的规格ID集合
                            let has = this.submitShopIds.get(ele.articleId);
                            //不重复添加
                            if (has && has.includes(e.id)) return;
                            //添加商品规格id
                            let value = has ? [...has, e.id] : [e.id];
                            this.submitShopIds.set(ele.articleId, value);
                        }
                    });
                    return;
                }

                // 处理一级、二级复选框
                let value = [];
                if (ele.article_goods && ele.article_goods.length) {
                    value = ele.article_goods.map(e => e.id);
                }
                this.submitShopIds.set(ele.articleId, value);
            });
        },
        //勾选或取消一级复选框
        isSelectCurrentAll() {
            this.isIndeterminateAll = false;
            this.isIndeterminate.forEach(
                (ele, i) => (this.isIndeterminate[i] = false)
            );
            //勾选时
            if (this.selectCurrentAll) {
                this.selectCurrentShop.forEach(
                    (ele, index) => (this.selectCurrentShop[index] = true)
                );
                this.selectShop.forEach(ele =>
                    ele.forEach((e, i) => (ele[i] = true))
                );
                this.filterIds(this.shopList);
            } else {
                this.selectCurrentShop.forEach(
                    (ele, index) => (this.selectCurrentShop[index] = false)
                );
                this.selectShop.forEach(ele =>
                    ele.forEach((e, i) => (ele[i] = false))
                );
                this.submitShopIds.clear();
            }
        },

        /**
         * @desciption: 勾选或取消二级复选框
         * @param {shop} 删除的商品数据
         * @param {index} 删除的商品下标
         * @return:
         * @author: guoxx
         */
        isSelectCurrentShop(shop, index) {
            //全选二级---商品
            if (this.selectCurrentShop[index]) {
                this.selectShop[index].forEach(
                    (e, i) => (this.selectShop[index][i] = true)
                );
                this.filterIds([shop]);
            } else {
                this.selectShop[index].forEach(
                    (e, i) => (this.selectShop[index][i] = false)
                );
                this.submitShopIds.delete(shop.articleId);
            }
            this.checkIsInde(shop, index);
        },

        /**
         * @desciption: 勾选或取消三级复选框
         * @param {data} 删除的商品规格数据
         * @param {shop} 规格所在的商品数据
         * @param {index} 商品下标
         * @param {i} 规格下标
         * @return:
         * @author: guoxx
         */
        isSelectShop(data, shop, index, i) {
            this.$forceUpdate();
            //全选三级---商品某一规格
            if (this.selectShop[index][i]) {
                this.filterIds(this.shopList, data);
            } else {
                if (shop.article_goods && shop.article_goods.length) {
                    //删除取消的规格
                    let has = this.submitShopIds.get(shop.articleId);
                    let value = has.filter(e => e !== data.id);
                    if (!!value.length) {
                        this.submitShopIds.set(shop.articleId, value);
                    } else {
                        this.submitShopIds.delete(shop.articleId);
                    }
                }
            }
            console.log(this.submitShopIds)
            this.checkIsInde(shop, index);
        },

        /**
         * @desciption: 判断一级、二级复选框的状态
         * @param {shop}  商品数据
         * @param {index} 商品下标
         * @return:
         * @author: guoxx
         */
        checkIsInde(shop, index) {
            //二级复选框
            //复选框样式为不确定状态：商品规格总数和已选规格数量不一致且存在已选规格
            if (this.submitShopIds.has(shop.articleId) && this.submitShopIds.get(shop.articleId).length > 0 && shop.article_goods.length !== this.submitShopIds.get(shop.articleId).length) {
                this.isIndeterminate[index] = true;
            } else {
                this.isIndeterminate[index] = false;
            }
            //二级复选框样式为已选状态：商品规格总数和已选规格数量一致
            if (this.submitShopIds.has(shop.articleId) && shop.article_goods.length === this.submitShopIds.get(shop.articleId).length) {
                this.selectCurrentShop[index] = true;
            } else {
                this.selectCurrentShop[index] = false;
            }

            //一级复选框不确定状态：同时存在已选和未选商品
            if (this.submitShopIds.size > 0 && this.selectCurrentShop.includes(false)) {
                this.isIndeterminateAll = true;
            } else {
                this.isIndeterminateAll = false;
            }
            //一级复选框为已选状态：二级复选框中均为已选状态
            if (this.selectCurrentShop.every(e => e === true)) {
                this.selectCurrentAll = true;
            } else {
                this.selectCurrentAll = false;
            }
        },
        //批量删除
        deletes() {
            if (!this.submitShopIds.size) {
                this.$message.warning("请选择移除的商品！");
                return;
            }

            //this.shopList删除一个数组元素后，其下标不断变化产生问题解决：
            let articleIds = this.shopList.map(ele => ele.articleId);
            articleIds.forEach(ele => {
                for (let [key, value] of this.submitShopIds) {
                    if (ele === key) {
                        let index = this.shopList.findIndex(
                            e => e.articleId === ele
                        );
                        //删除规则的条数
                        if (!value.length) return;
                        value.forEach(li => {
                            let litter = this.shopList[
                                index
                            ].article_goods.findIndex(e => e.id === li);
                            this.handleDelIndex(
                                index,
                                this.shopList[index].article_goods[litter]
                            );
                        });
                    }
                }
            });
        },
        //删除列表中已选的商品, data为当前删除的数据
        handleDelIndex(pIndex, data) {
            if (!this.shopList[pIndex].articleId) return;
            if (this.shopList[pIndex].article_goods.length === 1) {
                this.submitShopIds.delete(this.shopList[pIndex].articleId);
                this.shopList = this.delArrayIndex(this.shopList, pIndex);
            } else {
                let index = this.shopList[pIndex].article_goods.findIndex(
                    ele => ele.id === data.id
                );
                if (index == -1) return;
                let has = this.submitShopIds.get(
                    this.shopList[pIndex].articleId
                );
                if (has && has.length) {
                    has = this.delArrayIndex(has, index);
                    this.submitShopIds.set(
                        this.shopList[pIndex].articleId,
                        has
                    );
                }

                let goods = this.shopList[pIndex];
                goods.article_goods = this.delArrayIndex(
                    this.shopList[pIndex].article_goods,
                    index
                );
                this.$set(this.shopList, pIndex, goods);
            }

            this.initBtn();
        },
        //删除当前商品规格
        currentDel(data, pIndex) {
            let flag = true;
            this.$confirm("确认删除该商品?", "提示", {
                confirmButtonText: "确定",
                cancelButtonText: "取消",
                type: "warning"
            })
                .then(() => {
                    flag = false;
                    this.handleDelIndex(pIndex, data);
                })
                .catch(() => {
                    if (!flag) return;
                    this.$message({
                        type: "info",
                        message: "已取消删除"
                    });
                });
        },
        //删除数组中指定的下标元素，返回新值
        delArrayIndex(arr, index) {
            let newArr = [];
            for (let i = 0; i < arr.length; i++) {
                if (i !== index) {
                    newArr.push(arr[i]);
                }
            }
            return newArr;
        }
    }
};
</script>
<style scoped lang="less">
.table_list {
    margin-top: 20px;
    width: auto;
    height: auto;
    .btns {
        display: flex;
        align-items: center;
        /*margin-bottom: 10px;*/
    }
    /deep/ .el-input {
        .el-input__inner {
            width: 148px;
        }
    }
}

.all_title {
    width: 100%;
    height: 50px;
    border-collapse: collapse;
    thead {
        width: 100%;
        height: 100%;
        background: #f1f1f3;
        tr {
            border: 1px #e1e1e1 solid;
            th {
                height: 50px;
                background: #f1f1f3;
                font-size: 14px;
                color: #333333;
                padding-left: 20px;
                text-align: center;
                &:first-child {
                    padding: 0;
                }
                &:nth-of-type(2) {
                    text-align: left;
                }
            }
        }
    }
}

.list_title {
    width: 100%;
    height: auto;
    margin-top: 20px;
    border-collapse: collapse;
    thead {
        width: 100%;
        height: 40px;
        tr {
            width: 100%;
            height: 40px;
            border: 1px #e1e1e1 solid;
            text-align: center;
            th {
                height: 40px;
                background: #f1f1f3;
                color: #666666;
                font-weight: normal;
                padding-left: 16px;
                .shopTitle {
                    background: none;
                    padding: 0;
                    color: #666;
                }
            }
            &:nth-of-type(2) {
                text-align: left;
            }
        }
    }
    tbody {
        tr {
            width: 100%;
            border: 1px #e1e1e1 solid;
            background: #fff;
            td {
                height: 110px;
                padding-left: 20px;
                text-align: center;
                padding-left: 32px;
                .pro_de {
                    img {
                        width: 66px;
                        height: 66px;
                    }
                }
                &:first-of-type {
                    text-align: center;
                }
                &:nth-of-type(2) {
                    text-align: left;
                }
            }
        }
    }
    &:last-of-type {
        margin-bottom: 20px;
    }
}
.tableFlex {
    overflow: hidden;
    line-height: 40px;
    p,
    div {
        float: left;
    }
    .name {
        margin-right: 20px;
        margin-left: 20px;
    }
}

.flexB {
    display: flex;
    /*align-items: center;*/
    justify-content: space-between;
}
</style>
