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
                        <el-input v-model="searchParams.name" placeholder="若是月亮还没来" clearable
                            @keydown.enter="serachMusicForName">
                            <template #prepend>
                                <i-ep-search @click="serachMusicForName" />
                            </template>
                        </el-input>
                    </div>
                    <div class="functionsBox">
                        Moonc
                    </div>
                </div>
                <div class="mainBodyer">
                    <div class="musicListHeader Music">
                        <div class="index">#</div>
                        <div class="title">标题</div>
                        <div class="album">专辑</div>
                        <div class="duration">时长</div>
                    </div>

                    <div v-if="musicList.length" class="musicListBodyer" v-infinite-scroll="loadMusicList"
                        :infinite-scroll-immediate="false" infinite-scroll-distance="1">
                        <div class="musicItem Music" v-for="item in musicList" :key="item.id">
                            <div class="index">01</div>
                            <div class="title">
                                <img :src="item.album.picUrl" class="musicImage">
                                <div class="musicInfo">
                                    <div class="musicName">{{ item.name }}</div>
                                    <div class="musicAuthor">{{ item.author.map(item => item.name).join('/') }}</div>
                                </div>
                            </div>
                            <div class="album">{{ item.album.name }}</div>
                            <div class="duration">{{ (item.duration / 1000 / 60).toFixed(2).replace('.', ':') }}</div>
                        </div>
                    </div>
                    <!-- 空状态 -->
                    <el-empty class="empty" v-else description="空空如也" />
                </div>
            </div>
        </div>

        <div class="footer">
            <div class="musicSide Side">
                <img src="https://cube.elemecdn.com/6/94/4d3ea53c084bad6931a56d5158a48jpeg.jpeg" class="musicImage">
                <div class="musicInfo">
                    <div class="musicName">轻轻的告诉你</div>
                    <div class="musicAuthor">杨珏莹</div>
                </div>
            </div>
            <div class="musicControl">
                <div class="controlBtnBox" @click="playMusic">
                    <div class="preMusic MusicBtn">
                        <svg viewBox="0 0 1024 1024" width="1em" height="1em">
                            <path fill="currentColor"
                                d="M885.76 911.36h35.84c20.48 0 35.84-15.36 35.84-35.84V128c0-20.48-15.36-35.84-35.84-35.84h-35.84c-20.48 0-35.84 15.36-35.84 35.84v752.64c5.12 15.36 20.48 30.72 35.84 30.72zM76.8 880.64c20.48 30.72 61.44 40.96 92.16 20.48l552.96-332.8c10.24-5.12 15.36-15.36 20.48-20.48 20.48-30.72 10.24-71.68-20.48-92.16L168.96 122.88c-10.24-10.24-20.48-10.24-30.72-10.24-35.84 0-66.56 30.72-66.56 66.56v665.6c-5.12 10.24 0 25.6 5.12 35.84z">
                            </path>
                        </svg>
                    </div>

                    <div class="playMusic MusicBtn">
                        <svg v-show="playState" viewBox="0 0 1024 1024" xmlns="http://www.w3.org/2000/svg" width="1em"
                            height="1em">
                            <path fill="currentColor"
                                d="M309.3 130.7h-70.9c-24.3 0-44 19.7-44 44v674.5c0 24.3 19.7 44 44 44h70.9c24.3 0 44-19.7 44-44V174.7c0-24.3-19.7-44-44-44z m476.3 0h-70.9c-24.3 0-44 19.7-44 44v674.5c0 24.3 19.7 44 44 44h70.9c24.3 0 44-19.7 44-44V174.7c0-24.3-19.7-44-44-44z">
                            </path>
                        </svg>
                        <svg v-show="!playState" viewBox="0 0 1024 1024" width="1em" height="1em">
                            <path fill="currentColor"
                                d="M897.143467 597.051733l-464.648534 311.5264c-46.976 31.488-110.592 18.944-142.08-28.023466A102.4 102.4 0 0 1 273.066667 823.5264V200.4736c0-56.5504 45.8496-102.4 102.4-102.4a102.4 102.4 0 0 1 57.028266 17.348267l464.64 311.5264c46.976 31.488 59.528533 95.104 28.032 142.08a102.4 102.4 0 0 1-28.023466 28.023466z">
                            </path>
                        </svg>
                    </div>


                    <div class="nextMusic MusicBtn">
                        <svg viewBox="0 0 1024 1024" width="1em" height="1em">
                            <path fill="currentColor"
                                d="M885.76 911.36h35.84c20.48 0 35.84-15.36 35.84-35.84V128c0-20.48-15.36-35.84-35.84-35.84h-35.84c-20.48 0-35.84 15.36-35.84 35.84v752.64c5.12 15.36 20.48 30.72 35.84 30.72zM76.8 880.64c20.48 30.72 61.44 40.96 92.16 20.48l552.96-332.8c10.24-5.12 15.36-15.36 20.48-20.48 20.48-30.72 10.24-71.68-20.48-92.16L168.96 122.88c-10.24-10.24-20.48-10.24-30.72-10.24-35.84 0-66.56 30.72-66.56 66.56v665.6c-5.12 10.24 0 25.6 5.12 35.84z">
                            </path>
                        </svg>
                    </div>


                </div>
                <div class="audioBox">
                    <audio style="width: 0;height: 0;" ref="musicAudio"
                        src="http://downsc.chinaz.net/Files/DownLoad/sound1/201906/11582.mp3" controls></audio>
                    <div class="audioSchedule">
                        <div ref="progressBar" class="progressBar"></div>
                    </div>
                </div>
            </div>
            <div class="Side"></div>
        </div>
    </div>
</template>

<script setup lang="ts">
import { ISearchMusicParams, searchMusic } from '@/api/music';
import { IMusic } from '@/types/music';


const searchParams = reactive<ISearchMusicParams>({
    name: '',
    pageNo: 1,
    pageSize: 10
})
const musicList = ref<IMusic[]>([])
const musicTotal = ref<number>(0)
function getMusicList() {
    return searchMusic({
        ...searchParams,
        name: searchParams.name?.trim(),
    })
}


function serachMusicForName() {
    searchParams.pageNo = 1
    musicList.value = []
    musicTotal.value = 0
    getMusicList().then(res => {
        musicList.value = res.data.list
        musicTotal.value = res.data.total
    })
}
serachMusicForName()

function loadMusicList() {
    if (musicList.value.length === musicTotal.value) {
        return
    }
    searchParams.pageNo && searchParams.pageNo++
    getMusicList().then(res => {
        musicList.value = [...musicList.value, ...res.data.list]
        musicTotal.value = res.data.total
    })
}

const musicAudio = ref<HTMLAudioElement>()
const progressBar = ref<HTMLDivElement>()
const playState = ref<boolean>(false)
// const currentPlayMusic = ref<IMusic>()

function playMusic() {
    musicAudio.value!.play()
    musicAudio.value!.addEventListener('timeupdate', () => {
        const percentage = (musicAudio.value!.currentTime / musicAudio.value!.duration) * 100;
        progressBar.value!.style.width = `${percentage}%`
    })
}
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
            padding: 20px 60px;
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
                            font-size: 22px;
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
                                font-size: 22px;
                            }

                            .el-input__inner::placeholder {
                                color: #a69b8a;
                            }

                            .el-input__suffix {
                                .el-input__suffix-inner {
                                    .el-icon {
                                        font-size: 24px;
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
                display: flex;
                flex-direction: column;

                .Music {
                    display: flex;
                    align-items: center;
                    color: #aca9a7;
                    font-size: 24px;
                    padding: 0 20px;

                    .index {
                        width: 40px;
                    }

                    .title {
                        flex: 1;
                    }

                    .album {
                        flex: 1;
                    }
                }

                .musicListHeader {
                    border-bottom: 1px solid rgba(#aca9a7, 0.3);
                    padding-bottom: 12px;
                }

                .musicListBodyer {
                    flex: 1;
                    padding: 6px 0;
                    box-sizing: border-box;

                    .musicItem {
                        padding: 14px 20px;
                        border-radius: 10px;
                        cursor: pointer;

                        .title {
                            display: flex;
                            align-items: center;

                            .musicImage {
                                width: 56px;
                                height: 56px;
                                border-radius: 6px;
                                margin-right: 10px;
                                object-fit: cover;
                            }

                            .musicInfo {
                                height: 56px;
                                display: flex;
                                flex-direction: column;
                                justify-content: space-between;
                                padding: 2px 0;
                                box-sizing: border-box;

                                .musicName {
                                    color: #fff;
                                    font-size: 22px;
                                }

                                .musicAuthor {
                                    font-size: 20px;
                                }
                            }

                        }
                    }

                    .musicItem:hover {
                        background-color: rgba(#aca9a7, 0.3);
                    }
                }

                :deep(.empty) {
                    flex: 1;
                    display: flex;
                    align-items: center;
                    justify-content: center;

                    .el-empty__image {
                        width: 200px;
                    }

                    .el-empty__description p {
                        font-size: 40px;
                    }
                }

            }
        }
    }

    .footer {
        background-color: #2d2d38;
        border-top: 4px solid #42424c;
        box-sizing: border-box;
        padding: 32px 55px;
        display: flex;
        align-items: center;


        .Side {
            flex: 1;
        }

        .musicSide {
            display: flex;
            align-items: center;

            .musicImage {
                width: 60px;
                height: 60px;
                border-radius: 6px;
                margin-right: 10px;
                object-fit: cover;
            }

            .musicInfo {
                height: 60px;
                display: flex;
                flex-direction: column;
                justify-content: space-between;
                padding: 2px 0;
                box-sizing: border-box;

                .musicName {
                    color: #fff;
                    font-size: 24px;
                }

                .musicAuthor {
                    color: #aca9a7;
                    font-size: 22px;
                }
            }

        }

        .musicControl {
            display: flex;
            flex-direction: column;
            row-gap: 26px;
            align-items: center;
            justify-content: center;


            .controlBtnBox {
                display: flex;
                align-items: center;
                justify-content: center;
                column-gap: 16px;

                .MusicBtn {
                    color: #bed1dc;
                    font-size: 36px;
                    display: flex;
                    align-items: center;
                    justify-content: center;
                    cursor: pointer;
                }

                .preMusic {
                    transform: rotate(-180deg);
                }
            }

            .audioBox {
                display: flex;
                cursor: pointer;

                .audioSchedule {
                    width: 460px;
                    height: 10px;
                    background-color: #42424c;
                    border-radius: 10px;
                    position: relative;

                    .progressBar {
                        position: absolute;
                        left: 0;
                        top: 0;
                        width: 0%;
                        height: 100%;
                        background-color: #c74150;
                        border-radius: 10px;
                    }
                }
            }
        }
    }

}
</style>