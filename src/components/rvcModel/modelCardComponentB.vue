<script lang="ts" setup>
import { RvcModelVo, FavoriteAndCollectionForm } from '@/api/rvcModel/modelType'
import { favoriteModel, collectModel } from '@/api/rvcModel/modelApi'
import { ref } from 'vue';
import { message } from '@/utils/message';
let props = defineProps<{
    model: RvcModelVo
}>()
let localModel = ref<RvcModelVo>(props.model)
let likeDisabled = ref(true)
let collectDisabled = ref(true)
let clickMore = ref(false)
let moreVisibility = ref(false)
const handleClickMore = function () {
    clickMore.value = true
    moreVisibility.value = !moreVisibility.value
    setTimeout(function () {
        clickMore.value = false
    }, 200)
}
const handleBlur = function () {
    setTimeout(function () {
        moreVisibility.value = false
    }, 200)
}
const collect = function () {
    if (!collectDisabled.value) return
    collectDisabled.value = false
    setTimeout(function () {
        collectDisabled.value = true
    }
        , 2000)
    let form = <FavoriteAndCollectionForm>{
        modelId: (localModel.value.id as unknown as string),
        status: localModel.value.isCollection ? '0' : '1'
    }
    collectModel(form).then((res:any) => {
        if (res.code == 200) {
            localModel.value.isCollection = localModel.value.isCollection == '0'?'1':'0'
            localModel.value.collectionNum = ((localModel.value.collectionNum as unknown as number) + 1) as unknown as string
        }
        message.error('收藏失败，请稍后再试')
    })
}
const like = function () {
    if (!likeDisabled.value) return
    likeDisabled.value = false
    setTimeout(function () {
        likeDisabled.value = true
    }
        , 2000)
    let form = <FavoriteAndCollectionForm>{
        modelId: (localModel.value.id as unknown as string),
        status: localModel.value.isLike ? '0' : '1'
    }
    favoriteModel(form).then((res:any) => {
        if (res.code == 200) {
            localModel.value.isLike = localModel.value.isLike == '0'?'1':'0'
            localModel.value.likesNum = ((localModel.value.likesNum as unknown as number) + 1) as unknown as string
        }
        message.error('点赞失败，请稍后再试')

    })
}
</script>
<template>
    <div class="model-card">
        <img  :src="localModel.picture!" class="model-card__cover"
            @click="$router.push('/model?id=' + localModel.id)">
        <div tabindex="-1" class="more" @click="handleClickMore" @blur="handleBlur"
            :class="clickMore ? 'dither-animation' : ''" style="z-index: 10;">
            <div
                style="height: 20px;width:20px;background-image: url('/icon/more.svg');background-repeat: no-repeat;background-position: center center;background-size: contain;">
            </div>
        </div>
        <div class="more-window" v-show="moreVisibility">
            <div class="more-window__item" @click="message.warning('敬请期待')">
                举报
            </div>
        </div>
        <div class="model-card__info">
            <div class="user-info" @click="$router.push('/user?id=' + localModel.uid)">

                <div class="user-info__avatar"
                    :style="{ backgroundImage: 'url(\'' + localModel.avatar + '\')' }">

                </div>
                <div class="user-info__usename">
                    {{ localModel.nickname }}
                </div>

            </div>
            <div class="model-card__info__creatAt" @click="$router.push('/model?id=' + localModel.id)">
                {{ localModel.createTime }}
            </div>
            <div class="model-card__info__title" @click="$router.push('/model?id=' + localModel.id)">
                {{ localModel.name }}
            </div>

            <div class="other-info">
                <div class="other-info__stats__item">
                    <div style="height: 16px;width: 16px;" :style="{ backgroundImage: 'url(\'/icon/eye.svg\')' }">
                    </div>
                    <span>{{ localModel.viewNum }}</span>
                </div>
                <div class="other-info__stats__item" @click="collect()">
                    <div style="height: 16px;width: 16px;"
                        :style="{ backgroundImage: localModel.isCollection == '1' ? 'url(\'/icon/star-fill.svg\')' : 'url(\'/icon/star.svg\')' }">
                    </div>
                    <span>{{ localModel.collectionNum }}</span>
                </div>
                <div class="other-info__stats__item" @click="like()">
                    <div style="height: 16px;width: 16px;"
                        :style="{ backgroundImage: localModel.isLike == '1' ? 'url(\'/icon/heart-fill.svg\')' : 'url(\'/icon/heart.svg\')' }">
                    </div>
                    <span>{{ localModel.collectionNum }}</span>
                </div>
                <!-- <div class="other-info__stats__item">
                    <div style="height: 16px;width: 16px;" :style="{ backgroundImage: 'url(\'/icon/chat.svg\')' }">
                    </div>
                    <span>{{ localModel.commentNum }}</span>
                </div> -->
            </div>
        </div>
    </div>
</template>
<style>
.model-card {
    position: relative;
    width: 380px;
    min-height: 200px;
    cursor: pointer;
    border-radius: 10px;
    overflow: hidden;
    border: rgba(255, 255, 255, 0.1) 1px solid;
}
.model-card__cover{
    width: 100%;
    background-size: cover;
    background-position: center center;
    background-repeat: no-repeat;
}

.model-card:hover>img {
    transition: all 0.5s;
    scale: 1.02;
}

.more-window {
    position: absolute;
    right: 40px;
    top: 40px;
    width: 120px;
    border-radius: 10px;
    border: rgba(55, 58, 64) 1px solid;
    background-color: rgba(37, 38, 43);
    padding: 5px;
    z-index: 10;
    user-select: none;
}

.more-window__item {
    padding-left: 15px;
    width: calc(100% - 15px);
    height: 40px;
    line-height: 40px;
    font-size: 14px;
    text-align: left;
    border-radius: 5px;

    color: rgba(255, 255, 255, 0.7);
}

.more-window__item:hover {
    background-color: rgba(56, 58, 64);
    cursor: pointer;
}



.model-card__info {
    position: absolute;
    bottom: 0;
    width: 100%;
    /* height: 150px; */
    left: 50%;
    transform: translate(-50%);
    z-index: 1;
    border-radius: 5px;
    background-image: linear-gradient(transparent, rgba(0, 0, 0, 0.3));
}

.model-card__info__title {
    position: relative;
    width: calc(100% - 10px);
    height: 30px;
    line-height: 30px;
    color: white;
    margin-left: 10px;
    text-overflow: ellipsis;
    font-size: 20px;
    text-align: left;
    text-overflow: ellipsis;
    z-index: 1;
}

.model-card__info__creatAt {
    position: relative;
    width: calc(100% - 10px);
    height: 20px;
    line-height: 20px;
    font-size: 12px;
    margin-left: 10px;
    color: rgba(255, 255, 255, 1);
    z-index: 1;
    text-align: left;

}

.user-info {
    position: relative;
    height: 40px;
    width: 100%;
    transition: all 0.3s;
    display: flex;
    /* background-color: rgba(0, 0, 0, 0.8); */
    margin-left: 10px;
}

.user-info__avatar {
    height: 30px;
    min-width: 30px;
    position: relative;
    top: 50%;
    transform: translate(0, -50%);
    border-radius: 15px;
    background-image: url("/public/teamPic/dhx.jpg");
    background-size: cover;
    background-repeat: no-repeat;
    background-position: center;
}



.user-info__usename {
    height: 30px;
    line-height: 30px;
    color: rgba(255, 255, 255, 0.7);
    font-size: 14px;
    text-align: left;
    padding-left: 5px;
}

.user-info__usename:hover {
    color: white;
}

.other-info {
    position: relative;
    height: 40px;
    width: calc(100% - 10px);
    display: flex;
    margin-left: 10px;
    justify-content: start;
    border-radius: 5px;
    /* background-color: rgba(0, 0, 0, 0.8); */

}

.other-info__stats__item {
    position: relative;
    top: 50%;
    transform: translate(0, -50%);
    height: 16px;
    display: flex;
    margin-right: 15px;
}

.other-info__stats__item span {
    display: inline-block;
    height: 16px;
    line-height: 16px;
    font-size: 14px;
    margin-left: 4px;
    color: rgba(255, 255, 255, 0.7);
}

.more {
    position: absolute;
    width: 30px;
    height: 30px;
    top: 10px;
    right: 10px;
}

.dither-animation {
    top: 11px;
}</style>@/api/rvcModel/modelType@/api/rvcModel/modelApi