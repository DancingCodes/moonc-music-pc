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
                    <div class="searchBox">
                        <el-input v-model="searchParams.name" placeholder="若是月亮还没来" clearable
                            @keydown.enter="searchMusicByName">
                            <template #prepend>
                                <i-ep-search @click="searchMusicByName" />
                            </template>
                        </el-input>
                    </div>
                    <div class="functionsBox">
                        Moonc
                    </div>
                </div>
                <div class="mainBodyer">
                    <div v-if="musicList.length" class="musicListHeader Music">
                        <div class="index">#</div>
                        <div class="title">标题</div>
                        <div class="album">专辑</div>
                        <div class="duration">时长</div>
                    </div>
                    <div v-if="musicList.length" class="musicListBodyer" v-infinite-scroll="loadMusicList"
                        :infinite-scroll-immediate="false" infinite-scroll-distance="1">
                        <div class="musicItem Music" :class="{ currentItem: currentPlayMusic?.id === item.id }"
                            v-for="(item, index) in musicList" :key="item.id" @click="changeMusic(item)">
                            <div class="index">{{ index + 1 }}</div>
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
                        <div class="loadingBox">
                            {{ musicList.length === musicTotal ? '没有更多了' : '再来一些' }}
                        </div>
                    </div>
                    <!-- 空状态 -->
                    <el-empty class="empty" v-else description="空空如也" />
                </div>
            </div>
        </div>
        <div class="footer" v-if="currentPlayMusic">
            <div class="musicSide Side" @click="showMusicDetails">
                <img :src="currentPlayMusic.album.picUrl" class="musicImage">
                <div class="musicInfo">
                    <div class="musicName">{{ currentPlayMusic.name }}</div>
                    <div class="musicAuthor">{{ currentPlayMusic.author.map(item => item.name).join('/') }}</div>
                </div>
            </div>
            <div class="musicControl">
                <div class="controlBtnBox">
                    <div class="preMusic MusicBtn" @click="playPreMusic">
                        <svg viewBox="0 0 1024 1024" width="1em" height="1em">
                            <path fill="currentColor"
                                d="M885.76 911.36h35.84c20.48 0 35.84-15.36 35.84-35.84V128c0-20.48-15.36-35.84-35.84-35.84h-35.84c-20.48 0-35.84 15.36-35.84 35.84v752.64c5.12 15.36 20.48 30.72 35.84 30.72zM76.8 880.64c20.48 30.72 61.44 40.96 92.16 20.48l552.96-332.8c10.24-5.12 15.36-15.36 20.48-20.48 20.48-30.72 10.24-71.68-20.48-92.16L168.96 122.88c-10.24-10.24-20.48-10.24-30.72-10.24-35.84 0-66.56 30.72-66.56 66.56v665.6c-5.12 10.24 0 25.6 5.12 35.84z">
                            </path>
                        </svg>
                    </div>

                    <div class="playMusic MusicBtn" @click="() => playState ? pauseMusic() : playMusic()">
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

                    <div class="nextMusic MusicBtn" @click="playNextMusic">
                        <svg viewBox="0 0 1024 1024" width="1em" height="1em">
                            <path fill="currentColor"
                                d="M885.76 911.36h35.84c20.48 0 35.84-15.36 35.84-35.84V128c0-20.48-15.36-35.84-35.84-35.84h-35.84c-20.48 0-35.84 15.36-35.84 35.84v752.64c5.12 15.36 20.48 30.72 35.84 30.72zM76.8 880.64c20.48 30.72 61.44 40.96 92.16 20.48l552.96-332.8c10.24-5.12 15.36-15.36 20.48-20.48 20.48-30.72 10.24-71.68-20.48-92.16L168.96 122.88c-10.24-10.24-20.48-10.24-30.72-10.24-35.84 0-66.56 30.72-66.56 66.56v665.6c-5.12 10.24 0 25.6 5.12 35.84z">
                            </path>
                        </svg>
                    </div>

                </div>
                <div class="audioBox">
                    <audio style="width: 0;height: 0;" ref="musicAudio" preload="auto" :src="currentPlayMusic.url"
                        controls></audio>
                    <div class="audioSchedule">
                        <div ref="loadBar" class="loadBar"></div>
                        <div ref="progressBar" class="progressBar"></div>
                        <div class="scheduleEntity" @click="updataMusicPlayTime"></div>
                    </div>
                </div>
            </div>
            <div class="Side"></div>
        </div>

        <div class="popup" v-show="musicDetailsShow" @click="musicDetailsShow = false">
            <div class="lyricList" ref="lyricList">
                <div class="lyricItem" v-for="item in currentMusicLyricList">
                    {{ item.text }}
                </div>
            </div>
        </div>
    </div>
</template>

<script setup lang="ts">
import { ISearchMusicParams, searchMusic } from '@/api/music';
import { IMusic } from '@/types/music';

// 搜索条件
const searchParams = reactive<ISearchMusicParams>({
    name: '',
    pageNo: 1,
    pageSize: 20
})
// 音乐列表
const musicList = ref<IMusic[]>([])
// 音乐总条数
const musicTotal = ref<number>(0)
// 获取音乐
function getMusicList() {
    return searchMusic({
        ...searchParams,
        name: searchParams.name?.trim(),
    })
}

// 搜索音乐
function searchMusicByName() {
    searchParams.pageNo = 1
    musicList.value = []
    musicTotal.value = 0
    getMusicList().then(res => {
        musicList.value = res.data.list
        musicTotal.value = res.data.total
    })
}
searchMusicByName()

// 加载更多音乐
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

// 播放器
const musicAudio = ref<HTMLAudioElement>()
watch(musicAudio, newVal => {
    if (newVal) {
        musicAudio.value!.addEventListener('timeupdate', updateProgressBar)
        musicAudio.value!.addEventListener('progress', updateLoadProgress);
    }
})



// 进度条
const progressBar = ref<HTMLDivElement>()
// 缓冲进度条
const loadBar = ref<HTMLDivElement>()
// 歌词列表
const lyricList = ref<HTMLDivElement>()
// Audio播放时设置进度条以及设置歌词位置
const updateProgressBar = () => {
    // 设置进度条
    const percentage = (musicAudio.value!.currentTime / musicAudio.value!.duration) * 100;
    progressBar.value!.style.left = `-${100 - percentage}%`


    // 设置歌词位置
    if (musicDetailsShow.value) {
        // 获取歌词列表中大于当前时间并小于下次时间的歌词
        const currentLyricIndex = currentMusicLyricList.value.findIndex((item, index) => {
            const currentTime = musicAudio.value!.currentTime
            const nextItem = currentMusicLyricList.value[index + 1];
            return item.time <= currentTime && (!nextItem || nextItem.time > currentTime);
        })
        if (currentLyricIndex === -1) {
            return
        }
        // 当前歌词Dom
        const currentLyric = lyricList.value!.children[currentLyricIndex] as HTMLDivElement
        // 设置当前歌词样式
        Array.from(lyricList.value!.children).forEach(item => {
            item.classList.remove('cuurentLyric')
        })
        currentLyric.classList.add('cuurentLyric')
        // 让当前歌词滚动至中间
        currentLyric.scrollIntoView({
            behavior: 'smooth',
            block: 'center'
        });
    }



    if (percentage === 100) {
        playNextMusic()
    }
};
// Audio的缓冲进度条
function updateLoadProgress() {
    if (musicAudio.value!.buffered.length) {
        const bufferedEnd = musicAudio.value!.buffered.end(musicAudio.value!.buffered.length - 1);
        const duration = musicAudio.value!.duration;
        const bufferedPercent = (bufferedEnd / duration) * 100;
        loadBar.value!.style.width = `${bufferedPercent}%`
    }
}

// 播放状态
const playState = ref<boolean>(false)
// 当前播放音乐
const currentPlayMusic = ref<IMusic>()
watch(currentPlayMusic, () => {
    setCurrentMusicLyricList()
})
// 当前音乐歌词列表
const currentMusicLyricList = ref<{ time: number, text: string }[]>([])
// 音乐当前播放的时长
const musicCurrentTime = ref<number>(0)

// 选中音乐
function changeMusic(music: IMusic) {
    currentPlayMusic.value = music
    musicCurrentTime.value = 0
    playMusic()
}

// 播放音乐
function playMusic() {
    if (!playState.value) {
        playState.value = true
    }
    nextTick(() => {
        musicAudio.value!.currentTime = musicCurrentTime.value;
        musicAudio.value!.play()
    })
}

// 暂停音乐
function pauseMusic() {
    if (playState.value) {
        playState.value = false
    }
    musicAudio.value!.pause()
    musicCurrentTime.value = musicAudio.value!.currentTime
}

// 上一曲
function playPreMusic() {
    const curMusicIndex = musicList.value.findIndex(item => item.id === currentPlayMusic.value?.id)
    if (curMusicIndex === -1) {
        return
    }
    const preMusicIndex = curMusicIndex === 0 ? 0 : curMusicIndex - 1
    changeMusic(musicList.value[preMusicIndex])
}

// 下一曲
function playNextMusic() {
    const curMusicIndex = musicList.value.findIndex(item => item.id === currentPlayMusic.value?.id)
    if (curMusicIndex === -1) {
        return
    }
    const nextMusicIndex = curMusicIndex === musicList.value.length - 1 ? musicList.value.length - 1 : curMusicIndex + 1
    changeMusic(musicList.value[nextMusicIndex])
}

// 更新音乐播放进度
function updataMusicPlayTime(e: MouseEvent) {
    const target = e.target as HTMLDivElement;
    const rect = target.getBoundingClientRect();
    const x = e.pageX - rect.left;

    musicAudio.value!.currentTime = (currentPlayMusic.value!.duration / 1000) * (x / rect.width);
}

// 展示音乐详情
const musicDetailsShow = ref(false)
function showMusicDetails() {
    musicDetailsShow.value = true
}
// 设置当前音乐歌词
function setCurrentMusicLyricList() {
    currentMusicLyricList.value = currentPlayMusic.value!.lyric.trim().split('\n').map(item => {
        const [time, text] = item.split(']');
        const [minutes, seconds] = time.substring(1).split(':').map(parseFloat);
        return { time: minutes * 60 + seconds, text: text.trim() };
    });
}


</script>
<style scoped lang="scss">
.home {
    height: 100vh;
    background-color: #000;
    display: flex;
    flex-direction: column;
    position: relative;

    .bodyer {
        flex: 1;
        display: flex;
        overflow: hidden;

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
            overflow: hidden;

            .mainHeader {
                display: flex;
                align-items: center;
                justify-content: space-between;
                height: 50px;
                margin-bottom: 30px;

                :deep(.searchBox) {
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
                    font-size: 30px;
                }
            }

            .mainBodyer {
                flex: 1;
                display: flex;
                flex-direction: column;
                overflow: hidden;

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
                    overflow-y: scroll;

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

                    .currentItem {
                        background-color: rgba(#aca9a7, 0.3);
                    }

                    .loadingBox {
                        font-size: 24px;
                        text-align: right;
                        background: linear-gradient(to right, #aca9a7, #000);
                        background-size: 200% 100%;
                        background-position-x: 0;
                        -webkit-background-clip: text;
                        background-clip: text;
                        color: transparent;
                        animation: backgroundMoveX 0.6s linear infinite alternate;
                    }

                    @keyframes backgroundMoveX {
                        from {
                            background-position-x: 0;
                        }

                        to {
                            background-position-x: 100%;
                        }
                    }
                }

                .musicListBodyer::-webkit-scrollbar {
                    display: none;
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
        padding: 26px 55px;
        background-color: #2d2d38;
        border-top: 4px solid #42424c;
        box-sizing: border-box;
        display: flex;
        align-items: center;
        animation: showByOpacity 0.7s ease 0s forwards;

        .Side {
            flex: 1;
        }

        .musicSide {
            display: flex;
            align-items: center;
            cursor: pointer;

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

                .playMusic {
                    font-size: 40px;
                }
            }

            .audioBox {
                display: flex;
                cursor: pointer;

                .audioSchedule {
                    width: 460px;
                    height: 10px;
                    background-color: #000;
                    border-radius: 10px;
                    position: relative;
                    overflow: hidden;

                    .loadBar {
                        position: absolute;
                        left: 0;
                        top: 0;
                        width: 0%;
                        height: 100%;
                        background-color: #42424c;
                        border-radius: 10px;
                        transition: width 1s;
                    }

                    .progressBar {
                        position: absolute;
                        top: 0;
                        left: -100%;
                        width: 100%;
                        height: 100%;
                        background-color: #c74150;
                        border-radius: 10px;
                    }

                    .scheduleEntity {
                        position: absolute;
                        left: 0;
                        top: 0;
                        width: 100%;
                        height: 100%;
                    }
                }
            }
        }
    }

    @keyframes showByOpacity {
        from {
            opacity: 0;
        }

        to {
            opacity: 1;
        }
    }

    .popup {
        position: absolute;
        left: 0;
        top: 0;
        width: 100%;
        height: 100%;
        background: linear-gradient(to bottom, #715d3d, #13131a);
        animation: showByOpacity 0.7s ease 0s forwards;


        .lyricList {
            height: 100vh;
            overflow: hidden;

            .lyricItem {
                transition: all 0.7s;
                font-size: 30px;
                text-align: center;
                padding: 8px 0;
            }

            .cuurentLyric {
                color: #fff;
                font-weight: bold;
            }
        }
    }

}
</style>