## 滚动加载demo
```vuejs
ScrollLoading.vue（子组件）
<template>
    <div class="dz-loading-list">
        <slot :data="data"></slot>

        <div class="no-list-infos" v-if="data.length === 0 && !isImg">
            <!-- <img src="/static/image/noinfo.png" alt=""> -->
            暂无数据
        </div>
        <div class="no-evaluation-info"  v-if="data.length === 0 && isImg">
            <!-- <img src="/static/image/evaluation_default@2x.png" class="avatar"> -->
            <p class="text">对不起，未搜索到相关信息 </p>
        </div>

        <load-more v-show="showLoading" :show-loading="showLoading" tip="正在加载更多" background-color="#fbf9fe"></load-more>
        <div style="width:1px;height:1px;" id="scrollDiv"></div>
    </div>
</template>

<script>
    import {XButton, LoadMore} from 'vux'

    export default {
        components: {
            XButton, LoadMore
        },
        name: 'ScrollLoading',
        data() {
            return {
                isGettingData: false,
                currentPage: 1,
                isRead: 0,
                hasMoreData: true,
                showLoading: false,
                data: [],
                urlParam: {},
                getUrl: '',
                pager: {
                    totalCount: '',
                    pageSize: '',
                    currentPage: ''
                }
            }
        },
        watch: {
            otherObj: {
                handler() {
                    this.data = []
                    this.urlParam = {}
                    this.currentPage = 1;
                    for (let value in this.otherObj) {
                        this.urlParam[value] = this.otherObj[value]
                    }
                    this.hasMoreData = true
                    this.updateData(this.urlParam, 'change')
                },
                deep: true
            },
            loadUrl () {
                this.data = []
                this.urlParam = {}
                this.currentPage = 1;
                this.hasMoreData = true
                this.updateData(this.urlParam, 'change')
            }
        },
        props: {
            loadUrl: {
                required: true
            },
            dataListName: {
                required: true
            },
            otherObj: {
                required: false,
                type: Object
            },
            isGet: {
                type: Boolean,
                default: false
            },
            //是否清楚数组
            isClean: {
                type: Boolean
            },
            // 是否替换背景图
            isImg: {
                type: Boolean,
                default: false
            }
        },
        methods: {
            updateData(param, arg) {
                this.getUrl = this.loadUrl;
                let data = {};
                this.isGettingData = true;
                if (this.isGet) {
                    data = {};
                    if (this.getUrl.indexOf('page') == -1) {
                        this.getUrl = this.loadUrl + '?page=' + this.currentPage
                    }
                } else {
                    data = {page: this.currentPage};
                }

                for(let key in param){
                    data[key] = param[key]
                }
                // 页面验证
                if (this.pager.totalCount) {
                    if (this.currentPage > Math.ceil(parseInt(this.pager.totalCount)/parseInt(this.pager.pageSize)) ) {
                        this.showLoading = false
                        return
                    }
                }

                this.api.post(this.getUrl, {
                    ...data,
                    page: this.currentPage
                }, {},result => {
                    if (arg == 'change') {
                        this.data = []
                    }
                    this.pager = result.pager

                    this.$emit('getPaginationData', result.content)
                    this.isGettingData = false
                    if (this.dataListName.length != 0) {
                        if (result[this.dataListName].length == 0) {
                            this.showLoading = false;
                            this.hasMoreData = false;
                            return;
                        }

                        result[this.dataListName].forEach((item) => {
                            this.data.push(item);
                        });
                    } else {
                        if (result.length == 0) {
                            this.showLoading = false;
                            this.hasMoreData = false;
                            return;
                        }
                        result.forEach((item) => {
                            this.data.push(item);
                        });
                    }

                    this.$emit('updateData', this.data)
                    this.currentPage++
                })
            },
            scrollAction() {
                if (document.getElementById('scrollDiv')) {
                    var loadingMoreTop = document.getElementById('scrollDiv').offsetTop;
                    var windowScrollBotton = window.pageYOffset + window.screen.height;
                    if (windowScrollBotton >= (loadingMoreTop - 30) && this.isGettingData == false && this.hasMoreData) {
                        this.showLoading = true;
                        this.getData();
                    }
                }
            },
            getData() {
                if (this.otherObj) {
                    this.urlParam = {}
                    if (this.otherObj) {
                        for (let value in this.otherObj) {
                            this.urlParam[value] = this.otherObj[value];
                        }
                    }
                    this.updateData(this.urlParam)
                } else {
                    this.updateData()
                }
            }
        },
        created() {
            this.getData();
            document.addEventListener('scroll', this.scrollAction);
        },
        destoryed() {
            document.removeEventListener('scroll', this.scrollAction)
        }

    }
</script>

<style lang="less">
    .dz-loading-list {
        min-height: 100%;
        .no-evaluation-info{
            text-align: center;
            margin-top: 4rem;
            .avatar{
                width: 15rem;
                height: 14rem;
            }
            .text{
                font-size: 1.5rem;
                color: #A4BCCE;
                line-height: 1;
                margin-top: 2rem;
            }
        }
        .no-list-infos{
            margin-top: 30%;
            font-size: .3rem;
            text-align: center;
            color: #888;
        }
    }
</style>
List.vue 父组件
<template>
    <div class="teacher-index">
        <dz-scroll-loading dataListName="list" :other-obj="selectInfo" load-url="/teacher/get-list" v-on:data-change="handleDataChange">
            <template slot-scope="props">
                 <ul class="info-list">
                    <!-- <li class="info-item-box" v-for="item in testData" :key="item.id" @click="handleClick(item)"> -->
                    <li class="info-item-box" v-for="item in props.data" :key="item.id" @click="handleClick(item)">
                        <div class="info-item">
                            <p class="name">{{item.name}}</p>
                            <p class="tip" :class="overTime(item)?'red':''">
                                <span class="label">最晚提交时间：</span>{{item.deadline | timestampToYMDHm}}
                            </p>
                            <p class="tip status">
                                <span class="label">是否已提交：{{Number(item.status) === 1 ?'是':'否'}}</span>
                            </p>
                        </div>
                        <div class="grey-label" v-if="overTime(item,'noStatus')">采集结束</div>
                        <div class="blue-label" v-if="!overTime(item,'noStatus')">正在采集</div>
                    </li>
                </ul>
            </template>
        </dz-scroll-loading>
    </div>
</template>

<script>
    /**
     * 信息采集首页
     * @author liujingyan-2018/11/8
     */
    import DzScrollLoading from '@/components/ScrollLoading'

    export default {
        components:{
            DzScrollLoading
        },
        name: "List",
        data () {
            return {
                selectInfo: {},
                testData: [{
                    "id": "1",
                    "cId": "8",
                    "teacherId": "1",
                    "teacherName": "测试教师1",
                    "department": "校务处",
                    "createTime": "1",
                    "submitTime": null,
                    deadline: 1541469411,
                    "status": "0",
                    "content": "2",
                    "type": "1",
                    "filePath": null,
                    "schoolId": "1",
                    name: '测试111112222222222222222222222'
                },{
                    "id": "2",
                    "cId": "8",
                    "teacherId": "1",
                    "teacherName": "测试教师1",
                    "department": "校务处",
                    deadline: 1567717177,
                    "createTime": "1",
                    "submitTime": null,
                    "status": "0",
                    "content": "2",
                    "type": "1",
                    "filePath": null,
                    "schoolId": "1",
                    name: '测试11111'
                }]
            }
        },
        created () {

        },
        methods: {
            handleDataChange(data) {
                console.log(data)
            },
            overTime (item, noStatus){
               if (noStatus) {
                    if ( parseInt(item.deadline)*1000 < new Date().getTime()) {
                        return true
                    } else {
                        return false
                    }
               } else {
                    if ( parseInt(item.deadline)*1000 < new Date().getTime() && !parseInt(item.status)) {
                        return true
                   } else {
                        return false
                   }
               }
            },
            handleClick (item) {
                // 提交
                if (parseInt(item.status)) {
                    let routeName = Number(item.type) === 1 ? 'DetailText' : 'DetailFile'
                    this.$router.push({
                        name: routeName,
                        query: {
                            id: item.id
                        }
                    })
                } else {
                    // 未提交且过期
                    if (this.overTime(item,true)) {
                        this.$vux.toast.show({
                            text: '您未提交采集信息'
                        })
                    } else {
                        let routeName = Number(item.type) === 1 ? 'SubmitText' : 'SubmitFile'
                        this.$router.push({
                            name: routeName,
                            query: {
                                id: item.id,
                                cId: item.cId
                            }
                        })
                    }
                }
            }
        }
    }
</script>

<style lang="less">
    @import url(~@/assets/css/global.less);
    .teacher-index{
        .info-list{
            box-sizing: border-box;
        }
        .info-item-box{
            position: relative;
            padding:0 .3rem;
            overflow: hidden;
        }
        .info-item{
            box-sizing: border-box;
            padding: .2rem 0;
            border-bottom: 1px solid @color-d3dede;
            font-size: .24rem;
            color: @color-888888;
            .name{
                font-size: .3rem;
                line-height: 1.5;
                color: @color-555555;
                margin-bottom: .18rem;
                font-weight: bold;
                max-width: calc(100% - 1.5rem);
                word-break: break-all;
            }
            .status{
                margin-top: .18rem;
            }
        }
        .grey-label,.blue-label{
            position: absolute;
            width: 2.5rem;
            height: .4rem;
            font-size: .24rem;
            line-height: .4rem;
            color: #fff;
            text-align: center;
            top: .3rem;
            right: -.75rem;
            font-weight: normal;
            transform:rotate(45deg);
        }
        .grey-label{
            background-color: @color-888888;
        }
        .blue-label{
            background-color: @blue-color;
        }
        .red{
            color: @color-e32727;
        }
    }
</style>
```