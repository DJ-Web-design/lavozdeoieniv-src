<template>
	<div>
		<VideosHeader/>
			<template v-if="videos.length > 0">
				<span>Galeria &gt; <nuxt-link to="/Galeria/videos/">Videos</nuxt-link><template v-if="$route.hash"> &gt; {{setTitle}}</template></span>
				<aside id="enlaces">
					<ul>
						<li v-for="(iten, index) in videos" :key="'videoLi '+index">
							<nuxt-link
								:to="'/Galeria/videos#'+iten.id.videoId"
								:key="'iten.snippet.title '+index">
								{{ iten.snippet.title }}
							</nuxt-link>
						</li>
					</ul>
				</aside>
				<template v-if="!$route.hash">
					<div id="video-list">
						<transition-group name="fade">
							<template v-for="(iten, value) in videos">
							<VideosLink
								v-if="value+1 <= vistos"
								:title="iten.snippet.title"
								:linkTitle="setLink(iten.snippet.title)"
								:description="iten.snippet.description"
								:thumb="iten.snippet.thumbnails.medium.url"
								:link="'https://www.youtube.com/watch?v='+iten.id.videoId"
								:key="value"
							/>
							</template>
						</transition-group>
						<div id="seeMoreContainer" v-if="showSeeMoreButton">
							<span @click="vistos = vistos + 10">Ver Mas</span>
						</div>
					</div>
				</template>
				<template v-else>
					<VideosView :data="bindedData"/>
				</template>
			</template>
			<template v-else>
				<VideosMissing/>
			</template>

	</div>
</template>
<script>
import VideosHeader from "@/components/GaleryVideo/VideosHeader"
import VideosLink from "@/components/GaleryVideo/VideosLink"
import VideosMissing from "@/components/GaleryVideo/VideosMissing"
import VideosView from "@/components/GaleryVideo/VideosView"

import {videoDatabase} from "@/plugins/firebase"

import videos from "@/assets/json/videos.json"

export default {
	components: {
		VideosView,
		VideosHeader,
		VideosLink,
		VideosMissing
	},
	created(){
		if (this.$route.hash) {
			this.bindedData = this.videos.filter(e=>{
				if (e.id === this.$route.hash.slice(1)) return e;
			})[0]
		}
	},
	mounted(){
		initializeFB();
		this.videosData = []//videos.items;
		if (this.videos.length <= 10) {
			this.showSeeMoreButton = false;
		}
		/*let videos = this.videos;
		await videoDatabase.once("value", e => {
			videos = [];
			e.forEach(snap=>{
				videos.push(snap.val());
			})
		});*/
	},
	updated(){
		if (this.$route.hash) {
			this.bindedData = this.videos.filter(e=>{
				if (e.id === this.$route.hash.slice(1)) return e;
			})[0]
		}
	},
	data(){
		return { 
			videosData:[],
			vistos:10,
			showSeeMoreButton: true,
			bindedData:{}
		}
	},
	watch:{
		params(e){
			if (e) {
				this._computedWatchers.$metaInfo.value = {};
			}
		},
		vistos(e){
			if (e >= this.videos.length) {
				this.showSeeMoreButton = false
			}
		}
	},
	methods: {
		setLink(link){
			let string = link
			let replace = string.replace(/\s/g, "-")
			let newString = replace.toLowerCase()
			return newString
		}
	},
	computed:{
		setTitle(){
			var title;
			if (this.$route.hash) {
				title = this.videos.filter(e=>{
					if (e.id === this.$route.hash.slice(1)) return e;
				})[0].title;
			} else {
				title = "";
			}
			return title;
		},
		videos() {
			return this.videosData.filter(e => {
				return e.id.kind === "youtube#video"
			})
		}
	},
	head () {
		let data;
		let description = "Entra y mira nuestros videos de ayuda, predicas y programas, y no dejes que tus problemas te aflijan, lenvanta la cara y recuerda que Dios esta contigo donde quieras que estes, ¡Animo!.";
		if (this.$route.params.youtube) {
			data = {}
		} else  {
			data = {
				title:"Videos | La Voz de OIENIV",
				meta:[
					{name:"keywords", hid:"keywords", content:"radio cristiana online, radio evangelica online, emisora cristiana evangelica online de venezuela, Radio cristiana de venezuela, emisora cristiana evangelica en linea" },
					{name:"description", hid:"description", content:description},
					// Facebook Open Graph
					{ property:"og:title", content:"Videos | La Voz de OIENIV" },
					{ property:"og:url", content:"https://www.lavozdeoieniv.tk/Galeria/videos" },
					{ property:"og:description", content:description},
					//twitter cards
					{name:"twitter:description", content:description}
				]
			}
		}
		return data
	}
}
</script>
<style>
.fade-enter-active, .fade-leave-active {
	transition: opacity .5s
}
.fade-enter, .fade-leave-to, .fade-leave-active {
	opacity: 0
}
/*Enlaces*/
#video-list{
	display: grid;
	grid-template-columns:1fr;
	grid-auto-flow: row;
	width:80%;
	float:right;
}
#enlaces {
	width:20%;
	float: left;
	margin: 50px auto;
	display: none
}
#enlaces ul {
	overflow: hidden;
	margin: auto 10px;
	box-shadow: 1px 1px 3px gray;
}
#enlaces ul li{
	list-style: none;
	background:red;
	overflow: hidden;
	background: #f1f1f1;
}
#enlaces ul li a{
	text-decoration: none;
	color: black; 
	display: block;
	width: 100%;
	height: 100%;
	padding: 10px;
}

* {
	font-family: "Roboto", arial;
	margin: 0;
	padding: 0;
}
header,
section,
footer,
aside,
nav,
article,
figure,
figcaption,
hgroup {
	display: block;
}

.pull-left {
	float: left !important;
}

.pull-right {
	float: right !important;
}

.img-circle {
	border-radius: 50%;
}

#vista {
	background: url(/Black-earphone-wallpaper-music.jpg);
	background-size: cover;
	margin-top: 10px;
	position: relative;
	width: 100%;
	height: 400px;
}

#vista div {
	position: absolute;
	top: 0;
	width: 100%;
	height: 100%;
}
#vista div div {
	position: absolute;
	top: 0;
	width: 100%;
	height: 100%;
	background: rgba(80, 80, 80, 0.5);
	z-index: 2;
	text-align: center;
}

#vista div div h1 {
	width: 100%;
	position: relative;
	font-size: 30px;
	margin-top: 2.5%;
	font-weight: 500;
	font-family: Arapey;
	color: #fff;
}
#vista div div img {
	margin-top: 30px;
}
#social-gal span {
	font-size: 25px;
	display: inline-block;
	margin: 15px 15px 0 15px;
	cursor: pointer;
}
#social-gal p {
	color: white;
	display: block;
	margin-top: 20px;
}

.fb {
	background: rgb(59, 89, 152);
	padding: 2px;
	border-radius: 2px;
}

#gal {
	box-shadow: black 3px 3px 8px;
	border-radius: 5px;
	position: relative;
	float: left;
	width: 90%;
	margin: 20px 2.5%;
	padding: 5%;
	border: 2px solid rgba(75, 127, 232, 0.3);
	border-radius: 3px;
	background: #e7e5e5;
}

#gal img {
	border-radius: 5px;
	width: 100%;
	height: 100%;
}

#gal div {
	border-radius: 5px;
	background: rgba(255, 255, 255, 0.2);
	position: absolute;
	top: 0;
	width: 100%;
	height: 100%;
}

#gal div > #t {
	display: block;
	width: 100%;
	height: 100%;
	text-align: center;
	font-size: 30px;
	color: #f7f7f7;
	text-shadow: black 3px 3px 3px;
	z-index: 2;
}

#gal div span > #txt {
	position: absolute;
	width: 100%;
	top: 45%;
	left: 0;
}

#galerias {
	width: 92%;
}

#galerias,
#lateral {
	float: left;
}

#lateral {
	display: none;
}

#pie {
	float: left;
	width: 100%;
}
#icon-face:hover,
#icon-twit:hover,
#icon-inst:hover,
#icon-yt:hover {
	cursor: pointer;
}
@media screen and (min-width: 767px) {
	#vista div div div h1 {
		font-size:50px;
	}
}

@media screen and (min-width: 950px) {
	#enlaces {
		display:block;
	}
	#vista {
		margin-top: 10px;
		position: relative;
		height: 600px;
		background-size: cover;
		background-attachment: fixed;
		width: 100%;
	}
	#vista > div {
		height: 100%;
		width: 100%;
	}
	#vista div > #sb {
		position: relative;
		margin: 0 auto;
		width: 100%;
		height: 100%;
		z-index: 2;
		text-align: center;
	}
	#social-gal span {
		margin: 50px 40px 0 40px;
	}
	#vista div > div > div > h1 {
		margin-top: 5%;
		font-size: 60px;
	}
	#vista div > div > div > p {
		margin-top: 50px;
		font-size: 22px;
	}
	#gal {
		width: 40.4%;
		padding: 2%;
	}
	#gal:hover {
		background: #c7c7c7;
		transition: 0.2s ease;
	}
	#galerias {
		width: 70%;
	}
	#galerias,
	#lateral {
		float: left;
	}
	#lateral {
		display: block;
		margin: 25px 0 0 0;
		width: 30%;
		height: 900px;
		background: red;
	}
}
</style>
