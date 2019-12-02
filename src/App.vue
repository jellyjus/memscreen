<template>
    <div id="app">
        <div class="meme" :style="[memeStyle]"></div>
    </div>
</template>

<script>
    const photoRegexp = /^photo_\d+$/;
    export default {
        name: 'app',
        data() {
            return {
                gradients: [
                    'linear-gradient(to right, #ffecd2 0%, #fcb69f 100%)',
                    'linear-gradient(to top, #fad0c4 0%, #ffd1ff 100%)',
                    'linear-gradient(to top, #fbc2eb 0%, #a6c1ee 100%)',
                    'linear-gradient(to top, #fdcbf1 0%, #fdcbf1 1%, #e6dee9 100%)',
                    'linear-gradient(120deg, #a1c4fd 0%, #c2e9fb 100%)',
                    'linear-gradient(to top, #cfd9df 0%, #e2ebf0 100%)',
                    'linear-gradient(120deg, #fdfbfb 0%, #ebedee 100%)',
                    'linear-gradient(to top, #a8edea 0%, #fed6e3 100%)',
                    'linear-gradient(-20deg, #e9defa 0%, #fbfcdb 100%)'
                ],
                posts: [],
                index: 0,
            }
        },
        created() {
            VK.Api.call('wall.get', {domain: 'brightonbeach', count: 100, v: "5.73"}, res => {
                this.posts = res.response.items.filter(el =>
                    el.marked_as_ads === 0 &&
                    el.attachments &&
                    el.attachments.length === 1 &&
                    el.attachments[0].type === 'photo'
                ).map(el => this.getBiggestPhoto(el.attachments[0].photo));
                setInterval(() => {
                    this.index++;
                    if (this.index === this.posts.length)
                        this.index = 0;
                }, 1000 * 30)
            });
        },
        computed: {
            memeStyle() {
                return {
                    color: 'red',
                    'background-image': `url('${this.posts[this.index]}'), ${this.getRandomGradient()}`
                }
            }
        },
        methods: {
            getBiggestPhoto(obj) {
                const photos = Object.keys(obj).filter(el => photoRegexp.test(el));
                return obj[photos[photos.length-1]]
            },
            getRandomGradient() {
                return this.gradients[this.randomInt(0, this.gradients.length-1)]
            },
            randomInt(min, max) {
                const rand = min + Math.random() * (max + 1 - min);
                return Math.floor(rand);
            }
        }
    }
</script>

<style>
    html, body {
        margin: 0;
        padding: 0;
    }

    .meme {
        width: 100vw;
        height: 100vh;
        background-size: cover;
        background: no-repeat center;
    }
</style>
