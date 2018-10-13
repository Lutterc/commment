<template>
    <div class="pinglun">
        <div class="title">
            <span>网友评论<i>(999+)</i></span>
        </div>
        <ul class="messageList">
            <li v-for="(item,index) in msgList" :key="index">
                <div class="top">
                    <div class="headerImg">
                        <div class="img">
                            <img src="./img/test.jpg" alt="">
                        </div>
                        <span>{{item.userName}}</span>
                    </div>
                </div>
                <div class="center">
                    {{item.msg}}
                </div>
                <div class="messageList-bottom clearfix">
                    <span class="dateTime">{{item.dateTime}}</span>
                    <div class="dianz">
                        <div><img src="./img/noDian.png" alt="" v-show="item.isLike" @click="changeNum(index,0)">
                            <img src="./img/isDian.png" alt="" v-show="!item.isLike" @click="changeNum(index,1)">
                            <i>{{item.num}}</i>
                            <b @click="showComment(index,item.userName,'0')">回复</b>
                        </div>
                    </div>
                </div>
                <div class="comment" v-if="item.isShowTxt&&(isActive===index)">
                    <div class="el-cascader-menus el-popper" x-placement="bottom-start">
                        <div x-arrow="true" class="popper__arrow" style="left: 70px;"></div>
                        <div class="content">
                            <ul class="msgList">
                                <li v-for="(items,indexs) in item.comment" :key="indexs">
                                    <div>
                                        <span class="name">{{items.otherName?items.otherName:''}}
                                                  <i v-if="items.otherName">回复</i> {{items.userName}}:</span>
                                        <span class="msg">{{items.msgcom}}</span>
                                    </div>
                                    <div>
                                        <span class="dateTime">{{items.dateTime}}</span>
                                        <span class="msgBack" @click="showText(items.otherName?items.otherName:items.userName,index,'1')"> 回复</span>
                                    </div>
                                </li>
                                <li v-if="item.isTextarea">
                                    <textarea cols="30" rows="3" v-model="item.txt"></textarea>
                                </li>
                                <li class="clearfix" v-if="item.isTextarea">
                                    <button @click="submitAdd(index)">发送</button>
                                </li>
                            </ul>
                        </div>
                    </div>
                </div>
            </li>
        </ul>
    </div>
</template>


<script>
    export default {
        data() {
            return {
                myName: "杨波最帅", //评论者的名字
                qitaName: "", //保存其他人的名字
                flag: "", //判断是做回复还是做评论
                isActive: '',
                /*  */
                msgList: [{
                        msg: "但是感觉很高大上的样子",
                        dateTime: "2018-10-12 12:12:12",
                        userName: "兰陵王",
                        isLike: false,
                        num: "999", //点赞数
                        isShowTxt: false, //显示评论的内容
                        isTextarea: true,
                        txt: "122", //文本框内容
                        comment: [{
                                msgcom: "这是回复的什么东西",
                                dateTime: "2018-10-12 12:12:12", //第一个肯定还是没有其他对象的
                                userName: "我不是兰陵王",
                                txt: ""
                            },
                            {
                                msgcom: "这是回复的什么东西",
                                dateTime: "2018-10-12 12:12:12",
                                userName: "典韦",
                                otherName: "兰陵王",
                            }
                        ]
                    },
                    {
                        msg: "我不知道你们在说什么，但是感觉很高大上的样子",
                        dateTime: "2018-10-12 12:12:12",
                        userName: "典韦",
                        isLike: true, //是否点赞  false 没点  true已经点了
                        num: "222", //点赞数
                        txt: "", //文本框内容
                        isShowTxt: false,
                        isTextarea: false,
                        comment: [{
                            msg: "这是回复的什么东西",
                            dateTime: "2018-10-12 12:12:12",
                            userName: "我不是兰陵王",
                        }]
                    }
                ] //这是我弄的一个假的数据
                //是否显示文本域
            };
        },
        methods: {
            /* 显示点赞的数目 */
            changeNum(index, num) {
                // console.log(index)
                // console.log(num)
                this.msgList[index].isLike = !this.msgList[index].isLike;
                // console.log(index);
                // console.log(this.msgList[index].isLike);
                if (this.msgList[index].isLike) {
                    this.msgList[index].num = Number(this.msgList[index].num) - 1;
                } else {
                    this.msgList[index].num = Number(this.msgList[index].num) + 1;
                }
            },
            /* 增加评论 */
            submitAdd(index, userName) {
                // console.log(index);
                let time = this.getTime(new Date());
                // console.log(time);
                // console.log(this.msgList[index].txt);
                // console.log(this.msgList[index].txt.indexOf('：'));
                let msg = this.msgList[index].txt.split('：')[1];
                if (!msg) {
                    alert('请输入评论内容')
                    return
                }
                let addInfo = {}
                if (this.flag === '0') {
                    addInfo = {
                        msg: msg,
                        dateTime: time,
                        userName: this.myName,
                        // otherName: this.myName
                    }
                } else {
                    addInfo = {
                        msg: msg,
                        dateTime: time,
                        userName: this.qitaName,
                        otherName: this.myName
                    }
                }
                this.msgList[index].comment.push(addInfo)
                this.msgList[index].isTextarea = false
                /* 还有个评论者就是登陆人的账户 我随便编辑个啊 */
            },
            // /* 显示文本域*/
            showText(userName, index, flag) {
                this.flag = flag
                // console.log(is);
                this.qitaName = userName
                this.msgList[index].isTextarea = true
                this.msgList[index].txt = "回复 " + userName + "  ：";
                // this.msgList[index].isShowTxt = false;
                // this.msgList[index].isTextarea = true;
            },
            /* 显示评论内容 */
            showComment(index, userName, flag) {
                this.isActive=index
                this.flag = flag
                this.msgList[index].isTextarea = true
                this.qitaName = userName
                this.msgList[index].isShowTxt = true;
                this.msgList[index].txt = "回复 " + userName + " ：";
            },
            writeBack() {},
            /* 格式化时间 */
            getTime(t) {
                // var t = new Date();
                var y = t.getFullYear();
                var m = t.getMonth() + 1;
                var d = t.getDate();
                var h = t.getHours();
                var mi = t.getMinutes();
                var sec = t.getSeconds()
                m = m < 10 ? "0" + m : m;
                d = d < 10 ? "0" + d : d;
                h = h < 10 ? "0" + h : h;
                mi = mi < 10 ? "0" + mi : mi;
                sec = sec < 10 ? '0' + sec : sec;
                return y + "-" + m + "-" + d + ":" + h + ":" + mi + ":" + sec;
            }
        }
    };
</script>


<style lang="less" scoped>
    .pinglun {
        padding: 40px;
        .title {
            padding-bottom: 20px;
            border-bottom: 1px solid #e1e1e1;
            span {
                color: #333;
                font-size: 16px;
                i {
                    font-style: normal;
                }
            }
        }
        .messageList {
            padding-top: 20px;
            li {
                position: relative;
            }
            .comment {
                width: 100%;
                padding-left: 78px;
                .el-cascader-menus {
                    border: 1px solid #c1c0be;
                    width: 100%;
                    position: relative;
                    background: #c1c0be;
                    padding: 24px 46px;
                    box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0);
                    .popper__arrow {
                        left: 20px;
                    }
                    .popper__arrow::after {
                        border-bottom-color: #c1c0be;
                    }
                    .msgList {
                        li {
                            margin-bottom: 30px;
                            position: relative;
                            textarea {
                                width: 100%;
                                outline: none;
                                padding: 20px;
                                border: 1px solid #b0b0ae;
                                background: rgba(176, 176, 174, 0.6);
                                resize: none;
                            }
                            .callBack {
                                position: absolute;
                                top: 20px;
                                left: 20px;
                            }
                            button {
                                cursor: pointer;
                                float: right;
                                background: #c83e3c;
                                height: 40px;
                                padding: 0 30px;
                                color: #fff;
                                border: none;
                            }
                            div {
                                .name {
                                    font-size: 16px;
                                    font-weight: 700;
                                    color: #010101;
                                    i {
                                        font-style: normal;
                                        font-weight: 400;
                                    }
                                }
                                .msg {
                                    width: 900px;
                                    line-height: 1.5; // margin-top: -2px;
                                    font-size: 16px; // overflow: hidden;
                                    display: inline-block; // word-break: normal;
                                    white-space: pre-wrap;
                                    vertical-align: top;
                                }
                                margin-bottom: 10px;
                                .dateTime {
                                    color: #7c7f88;
                                    margin-right: 40px;
                                }
                                .msgBack {
                                    cursor: pointer;
                                    color: #d74c39;
                                }
                            }
                        }
                    }
                }
            }
            .top {
                .headerImg {
                    .img {
                        vertical-align: middle;
                        display: inline-block;
                        position: relative; // background: #000;
                        width: 64px;
                        height: 64px;
                        margin-right: 10px; // display: inline-block;
                        // border-radius: 50%;
                        // border: 1px solid #000000;
                        text-align: center;
                        line-height: 64px;
                        img {
                            width: 64px;
                            height: 64px;
                            border-radius: 50%;
                        }
                    }
                    span {
                        font-weight: 700;
                        color: #000;
                    }
                }
            }
            .center {
                margin: 10px 0px;
                margin-left: 78px;
            }
            .messageList-bottom {
                margin-left: 78px;
                .dateTime {
                    float: left;
                }
                .dianz {
                    float: right;
                    div {
                        // display: inline-block;
                        display: flex;
                        justify-content: center;
                        align-items: center;
                        img {
                            cursor: pointer;
                            width: 24px;
                            height: auto;
                            vertical-align: middle;
                            margin-right: 4px;
                        }
                        i {
                            // display: inline-block;
                            font-style: normal;
                            margin-right: 10px;
                        }
                        b {
                            cursor: pointer;
                            color: #d74c39;
                        }
                    }
                }
            }
        }
    }
</style>


