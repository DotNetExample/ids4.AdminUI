<template>
    <transition name="msgbox-fade">
    <div class="dialog">
        <!--外层的遮罩 点击事件用来关闭弹窗，isShow控制弹窗显示 隐藏的props-->
        <div class="dialog-cover back" @click="closeMyself"></div>
        <!-- transition 这里可以加一些简单的动画效果 -->
            <!--style 通过props 控制内容的样式 -->
            <div class="dialog-content" ref='dialogContentBox' :style="{width:dialogWidth+'px'}">

                <div class="dialog_head back" ref='header'>
                    <!--弹窗头部 title-->
                    <el-row>
                        <el-col :span='22' class='dialog_head_tip'>
                            <slot name="header">提示</slot>
                        </el-col>
                        <el-col :span='2'>
                            <el-button class='el-message-box__headerbtn el-message-box_btn' type='text'
                                       @click="closeMyself">
                                <i class='el-message-box__close el-icon-close'></i>
                            </el-button>
                        </el-col>
                    </el-row>
                </div>
                <div id="dialog_main" ref="dialogMain">
                    <el-scrollbar ref="scrollbar" wrap-class="scrollbar-wrapper-y">
                        <slot name="main">弹窗内容</slot>
                    </el-scrollbar>
                </div>
                <!--<div class="dialog_main" ref="dialogMain">-->
                <!--&lt;!&ndash;弹窗的内容&ndash;&gt;-->
                <!--<slot name="main">弹窗内容</slot>-->
                <!--</div>-->
                <!--弹窗关闭按钮-->
                <div class="el-message-box__btns" ref='footer'>
                    <slot name="footer">按钮区域</slot>
                </div>
            </div>
    </div>
    </transition>
</template>
<script>
    export default {
        props: {
            dialogWidth: {
                type: Number
            }
        },
        data() {
            return {
                dialogMainHeight: ''
            }
        },
        mounted() {
            this.$nextTick(() => {
                this.dialogMainHeight = this.$refs.dialogContentBox.clientHeight
                this.resize();
            });
            this.$$resize = () => {
                this.resize();
            }
            window.addEventListener("resize", this.$$resize);
        },
        methods: {
            closeMyself() {
                this.$emit("on-close");
                //如果需要传参的话，可以在"on-close"后面再加参数，然后在父组件的函数里接收就可以了。
            },
            resize() {
                let windowHeight = window.innerHeight
                if (this.dialogMainHeight >= (windowHeight - 60)) {
                    this.$refs.dialogMain.style.height = (windowHeight - 60 - this.$refs.header.clientHeight - this.$refs.footer.clientHeight) + 'px';
                    this.$refs.dialogContentBox.style.top = '30px'
                } else {
                    this.$refs.dialogMain.style.height = "auto";
                    this.$refs.dialogContentBox.style.top = (windowHeight - this.dialogMainHeight) / 2 + 'px'
                }
                this.$refs.scrollbar.update();
            }
        },
        destroyed() {
            window.removeEventListener("resize", this.$$resize);
        }
    }
</script>
<style lang="scss" scoped>
    // 最外层 设置position定位
    .dialog {
        position: fixed;
        top: 0;
        left: 0;
        right: 0;
        bottom: 0;
        color: #2e2c2d;
        font-size: 16px;
        z-index: 222;
    }

    // 遮罩 设置背景层，z-index值要足够大确保能覆盖，高度 宽度设置满 做到全屏遮罩
    .dialog-cover {
        background: rgba(0, 0, 0, 0.3);
        position: fixed;
        z-index: 200;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
    }

    // 内容层 z-index要比遮罩大，否则会被遮盖，
    .dialog-content {
        position: absolute;
        background-color: #fff;
        z-index: 300;
        border-radius: 4px;
        display: inline-block;
        margin: auto;
        left: 0;
        right: 0;
    }

    .dialog_head {
        position: relative;
        padding: 15px;
        padding-bottom: 10px;
        .dialog_head_tip {
            font-size: 16px;
        }
        .el-message-box_btn {
            padding: 0;
            top: 2px;
        }
    }

    #dialog_main {
        padding: 10px 15px ;
    }

    .el-message-box__btns {
        padding-bottom: 10px;
    }

</style>