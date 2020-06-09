<template>
    <div class="zn-audio">
        <audio :id="id" preload="auto" src="../assets/audio/赵霏儿 - 出山.mp3" v-if="audioStatus != 3"></audio>
        <div class="zn-audio-wrapper">
            <div class="audio-left">
                <i v-show="audioStatus == 1" class="zn-audio-loading"></i>
                <i v-show="audioStatus == 2" class="audio-left-btn" :class="playing ? 'playing' : 'pause'" @click="trigger"></i>
                <i v-show="audioStatus == 3" class="zn-audio-refresh" @click="reload"></i>
            </div>
            <input  class="audio-middle" type="range" min="0" :max="totalDuration" @input="setProgress" v-model="playDuration" :style="rangeStyle" :disabled="disabled">
            <div class="audio-right">
               <span class="audio-length-current">{{playDuration | durationFormat}}</span> / <span class="audio-length-total">{{totalDuration | durationFormat}}</span>
            </div>
        </div>
    </div>
</template>
<script>
    export default {
        props:["url"],
        data(){
            return{
                id: "myaudio",
                range: 0,
                playDuration: 0, //毫秒ms
                totalDuration: 0, //音频总的时长 单位:毫秒ms
                playing: false,
                disabled: false,
                audioStatus: 1, //1-加载中loading 2-加载成功 3-加载失败
            }
        },
        computed:{
            rangeStyle: function () {
                let percent = Math.round((this.playDuration/this.totalDuration)*100)+'%';
                return { "backgroundSize" : percent +" 100%"};
            },
        },
        filters:{
            durationFormat: function (val) {
                let min = Math.floor(val / (60 * 1000));
                let sec = val % (60 * 1000);
                sec = Math.round(sec/1000);
                sec = sec < 10 ? '0' + sec : sec;
                return min + ":" + sec;
            }
        },

        mounted(){
            this.$nextTick(()=>{
                this.init();
            })
        },
        beforeDestroy() {
            this.pause();
        },
        methods:{
            init(){
                let audio = document.getElementById(this.id);
                audio.addEventListener("play", ()=>{
                    this.playing = true;
                });
                audio.addEventListener("pause", ()=>{
                    this.playing = false;
                });
                audio.addEventListener("canplay", ()=>{
                    this.audioStatus = 2;
                    this.totalDuration = Math.round(audio.duration * 1000);
                });
                audio.addEventListener("timeupdate", ()=>{
                    this.playDuration = Math.round(audio.currentTime * 1000);
                });
                audio.addEventListener("ended", ()=>{
                    if(this.disabled){
                        this.disabled = false;
                    }
                });
                audio.addEventListener("error",  () => {   //请求数据时遇到错误
                    this.audioStatus = 3;
                });
                this.play();
            },
            setProgress(){
                let audio = document.getElementById(this.id);
                audio.currentTime =  this.playDuration / 1000;
            },
            trigger(){
                let audio = document.getElementById(this.id);
                if(this.playing){
                    audio.pause();
                }else{
                    audio.play();
                }
            },
            play(){
                let audio = document.getElementById(this.id);
                audio.play();
            },
            reload(){
                this.audioStatus = 1;
                clearTimeout(this.st);
                this.$nextTick(()=>{
                    this.init();
                })
            },
            pause(){
                let audio = document.getElementById(this.id);
                audio && audio.pause();
            }
        }
    }
</script>
<style lang="less">
    .zn-audio{
        overflow: hidden;
        audio{
            display: none;
        }
        &::after{
            content: "";
            display: block;
            clear: both;
        }
        .zn-audio-wrapper{
            display: flex;
            align-items: center;
            height: 1.1rem;
            background-color: #f4f4f4;
            border-radius: 1rem;
            overflow: hidden;
            float: left;
            &::after{
                content: "";
                display: block;
                height: 0;
                clear: both;
            }
            .audio-left{
                font-size: .36rem;
                padding: 0 .3rem;
                .zn-audio-loading,
                .audio-left-btn,
                .zn-audio-refresh{
                    display: block;
                    width: .54rem;
                    height: .54rem;
                }
                .zn-audio-loading{
                    background: url("../assets/img/gr-51@2x.png") no-repeat center;
                    background-size: cover;
                    animation: loading 1s linear infinite;
                    transform-origin: 50% 50%;
                }
                @keyframes loading
                {
                    from {
                        transform: rotate(0);
                    }
                    to {
                        transform: rotate(360deg);
                    }
                }
                .audio-left-btn{
                    &.playing{
                        background: url("../assets/img/gr-45@2x.png") no-repeat left top;
                        background-size: 1.08rem .54rem;
                    }
                    &.pause{
                        background: url("../assets/img/gr-45@2x.png") no-repeat right top;
                        background-size: 1.08rem .54rem;
                    }
                }
                .zn-audio-refresh{
                    background: url("../assets/img/gr-49@2x.png") no-repeat center;
                    background-size: cover;
                }
            }
            .audio-middle{
            }
            .audio-right{
                padding: 0 .3rem;
                font-size: .34rem;
            }
            /*横条样式*/
            input[type=range] {
                -webkit-appearance: none;/*清除系统默认样式*/
                /*background: #fff;!*设置左边颜色为#61bd12，右边颜色为#ddd*!*/
                background: -webkit-linear-gradient(#42b8a2, #42b8a2) no-repeat, #ddd;
                background-size: 0% 100%;/*设置左右宽度比例*/
                height: .16rem;/*横条的高度*/
                border-radius: .16rem;
                outline: none;
            }
            /*拖动块的样式*/
            input[type=range]:not(:disabled)::-webkit-slider-thumb {
                -webkit-appearance: none;/*清除系统默认样式*/
                height: .3rem;/*拖动块高度*/
                width: .3rem;/*拖动块宽度*/
                background: #0dc2a1;/*拖动块背景*/
                border-radius: 50%; /*外观设置为圆形*/
                /*border: solid 1px #fff; !*设置边框*!*/
                box-shadow: 0 0 3px 1px #fff;
            }
            input[type=range]:disabled::-webkit-slider-thumb {
                -webkit-appearance: none;/*清除系统默认样式*/
            }
        }
    }
</style>