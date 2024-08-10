<template>
    <div class="home">
        <div class="bodyer">
            <div class="sidebar">
                <div class="sideHeader">Moonc Music</div>
                <div class="sideList">
                    <div class="sideItem">音乐列表</div>
                </div>
            </div>
            <div class="main">
                <div class="mainHeader">
                    <div class="serachBox">
                        <el-input v-model="serachMusicName" placeholder="若是月亮还没来" clearable>
                            <template #prepend>
                                <i-ep-search />
                            </template>
                        </el-input>
                    </div>
                    <div class="functionsBox">
                        Moonc
                    </div>
                </div>
                <div class="mainBodyer">
                    



                </div>
            </div>
        </div>
        <div class="footer"></div>
    </div>
</template>

<script setup lang="ts">
const serachMusicName = ref<string>('')
</script>
<style scoped lang="scss">
.home {
    height: 100vh;
    display: flex;
    flex-direction: column;

    .bodyer {
        flex: 1;
        display: flex;

        .sidebar {
            background: linear-gradient(to bottom, #756243, #1a1a21);
            display: flex;
            flex-direction: column;
            box-sizing: border-box;
            padding: 20px 55px;

            .sideHeader {
                color: #fff;
                font-size: 30px;
                display: flex;
                align-items: center;
                cursor: pointer;
                height: 50px;
                margin-bottom: 30px;
            }

            .sideList {
                flex: 1;

                .sideItem {
                    display: flex;
                    align-items: center;
                    font-size: 26px;
                    color: #dedbd7;
                    cursor: pointer;
                }

                .sideItem:not(:first-child) {
                    margin-top: 16px;
                }
            }
        }

        .main {
            flex: 1;
            background: linear-gradient(to bottom, #715d3d, #13131a);
            display: flex;
            flex-direction: column;
            padding: 20px 60px 0;
            box-sizing: border-box;

            .mainHeader {
                display: flex;
                align-items: center;
                justify-content: space-between;
                height: 50px;
                margin-bottom: 30px;

                :deep(.serachBox) {
                    display: flex;
                    align-items: center;
                    height: 100%;

                    .el-input {
                        border-radius: 10px;
                        border: 2px solid #76664e;
                        width: 360px;
                        height: 100%;

                        .el-input-group__prepend {
                            background-color: transparent;
                            box-shadow: unset;
                            color: #a69b8a;
                            font-size: 20px;
                            padding: 0 10px 0 18px;
                            cursor: pointer;
                        }

                        .el-input-group__prepend:hover {
                            color: #fff;
                        }

                        .el-input__wrapper {
                            background-color: transparent;
                            box-shadow: unset;
                            padding: 0 20px 0 0;

                            .el-input__inner {
                                height: 100%;
                                color: #fff;
                                font-size: 20px;
                            }

                            .el-input__inner::placeholder {
                                color: #a69b8a;
                            }

                            .el-input__suffix {
                                .el-input__suffix-inner {
                                    .el-icon {
                                        font-size: 22px;
                                        color: #a69b8a;
                                    }

                                    .el-icon:hover {
                                        color: #fff;
                                    }
                                }
                            }
                        }


                    }
                }

                .functionsBox {
                    color: #fff;
                }
            }

            .mainBodyer {
                flex: 1;
            }
        }
    }

    .footer {
        height: 120px;
        background-color: #2d2d38;
        border-top: 4px solid #42424c;
        box-sizing: border-box;
    }

}
</style>