<template>
	<div class="search-wrapper">
		<mt-header fixed>
			<div class="search-nav" slot="left">
				<el-input
					v-model="inputVal"
			  		placeholder="电影/电视剧/影人"
				  	icon="search"
				  	@keyup.13.native="searchClick()"
				  	:autofocus=true
			  	>
				</el-input>
			</div> 
			<div slot="right"  @click="backClick()">
				<span>取消</span>
			</div>
		</mt-header>
		<div class="hotsearch-div" v-if="searchList===''">
			<div class="hotsearch-title">Top排名</div>
			<mt-cell v-for="(film,index) in hotList" :key="index" :to="{path:'/detail/'+film.id}">
				<span class="index-title">
					<img src="../assets/image/金牌.png" v-if="index==0">
					<img src="../assets/image/银牌.png" v-else-if="index==1">
					<img src="../assets/image/铜牌.png" v-else-if="index==2">
					<span v-else>{{index+1}}</span>
				</span>
				<span class="film-title">{{film.title}}</span>
			</mt-cell>
		</div>
		<div class="result-div" v-else>
			<mt-cell  v-for="film in searchList.subjects" key="1" class="film-list"  :to="{path:'/detail/'+film.id}">
				<div style="width:30%;"><img :src="film.images.large" width="100%" height="auto"></div>
				<div  style="width:60%;" class="info-list">
					<p class="title-p">{{film.title}}</h4>
					<p class="introduce-p">
						<span  v-if="film.rating.average!=0">
							<Star :rating="film.rating.average"></Star>
							<span class="grade-span">{{film.rating.average}}</span>
						</span>
						<span v-else>暂无评分</span>
					</p>
					<p class="introduce-p">导演：
						<span v-for="(director,index) in film.directors">
							{{director.name + (index==film.directors.length-1?'':' / ')}}
						</span>
					</p>
					<p class="introduce-p">主演：
						<span v-for="(cast,index) in film.casts">
							{{cast.name + (index==film.casts.length-1?'':' / ')}}
						</span>
					</p>
				</div>
			</mt-cell>
		</div>
	</div>
</template>
<script>
import jsonp from '@/directive/jsonp.js';
import Swiper from '../../static/swiper/swiper-3.4.2.min.js';
import Star from '@/components/Star.vue'
import Vue from 'vue';
export default {
	name: 'search',
	components: { 
		Star,
    },
  	data () {
    	return {
    		inputVal:'',
    		searchList:'',
    		mark:false,
    		hotList:'',
    	}
  	},
  	methods:{

  		//搜索点击
  		searchClick(){
  			this.mark=true;
  			let val=this.inputVal.trim();
  			if(val.length==0)
  				return;
  			this.getData(val);
  		},

  		//搜索数据
  		getData(val){
  			let _this = this;
			_this.isLoading = true;
        	let url='https://api.douban.com/v2/movie/search?q='+ val;
        	//loading效果
            let loading = Vue.prototype.$loading({text:"玩命加载中..."});
			jsonp(url, {city:'广州' }, function (data) {
                this.searchList=data;
                console.log(this.searchList);
             	//先结束loading效果
                loading.close();
            }.bind(this));
        	_this.isLoading = false;
		},

		//热搜数据
		getHotData(){
  			let _this = this;
        	let url='https://api.douban.com/v2/movie/top250';
        	//loading效果
            //let loading = Vue.prototype.$loading({text:"玩命加载中..."});
			jsonp(url, {city:'广州',count:10 }, function (data) {
                this.hotList=data.subjects;
                console.log("-----hotList:",this.hotList);
             	//先结束loading效果
            }.bind(this));
        	//_this.isLoading = false;
		},

		//取消键：返回
		backClick(){
			this.$router.go(-1);
		}

  	},
  	mounted:function(){
  		this.getHotData();
	},
	watch: {
	    //监测$route对象，如果发生改变，就触发getIntheaters方法
	    //"$route":'getData',
    },
}
</script>
<style>
.search-wrapper .mint-header{
	background-color: #f9c425;
    height: 1rem;
    padding: 0 0.24rem;
}
.search-wrapper .mint-header span{
	font-size: 0.3rem;
	display: inline-block;
	line-height: 1rem;
	width: 100%;
	height: 100%;
}
.search-wrapper .mint-header-button.is-right {
	height: 100%;
	width: 18%;
	text-align: center;
}
.search-wrapper .mint-header-button.is-right>div{
	height: 100%;
	width: 100%;
}
.search-wrapper .mint-navbar .mint-tab-item.is-selected {
	border-bottom: 3px solid #f9c425;
	color: #2c3e50;
	margin-bottom:0px;
}
.search-wrapper .mint-header .el-input,.search-wrapper{
    min-width: 220px;
    width: 100%;
    font-size: 0.35rem;
}
.search-wrapper .mint-header .is-left .search-nav{
    width: 100%;
    display: inline-block;
}
.search-wrapper .mint-header .is-left {
	 width: 80%;
}
.search-wrapper .mint-header .is-left,.search-wrapper .mint-header .is-right{
    -webkit-box-flex: none;
    -ms-flex: none;
    flex: none;
   
}

/* mint-cell */
.search-wrapper .mint-button-text{
	font-size: 0.28rem;
}
.search-wrapper .mint-cell-value button{
    width: 1.2rem;
    height: 0.7rem;
	font-weight: bolder;
	background-color: white;
}
.search-wrapper .buyTicket-btn{
	color: #ec294f;
	border: 1px solid #f76884;
}
.search-wrapper .booking-btn,.interest-btn{
	color: #f8ab5b;
	border: 1px solid #f9c425;
}
.search-wrapper .mint-cell-value>div{
	height: 100%;
}
.search-wrapper .mint-cell-value{
	width: 100%;
	padding:10px 0px;
}

.search-wrapper .film-list .title-p{
	font-size: 0.4rem;
	font-weight: bold;
	color: #2f2b2b;
	font-family: "STSong";
	margin:  0.25rem 0.1rem;
}
.search-wrapper .film-list .info-list{
	text-align: left;
	padding-left: 0.35rem;
}
.search-wrapper .film-list .introduce-p{
	font-size: 0.28rem;
}
.search-wrapper .film-list .introduce-p .grade-span{
	position: relative;
	top:4px;
}
.search-wrapper .film-list .see-p{
	font-size: 0.3rem;
	color: black;
	margin: 0.18rem 0.1rem;
}
.search-wrapper .film-list p{
	margin: 0.2rem  0.1rem;
}



.search-wrapper .el-rate__icon,.search-wrapper .el-rate__text{
	font-size: 0.3rem;
}

.search-wrapper .hotsearch-div .hotsearch-title{
	text-align: left;
	background-color: #EEEEEE;
	height: 0.9rem;
	line-height: 0.9rem;
	padding-left: 0.75rem;
}

.search-wrapper .hotsearch-div .mint-cell-wrapper{
	font-size: 0.38rem; 
	padding:0px;

}
.search-wrapper .hotsearch-div .mint-cell-wrapper span{
	padding-left: 0.3rem;
}
.search-wrapper .hotsearch-div .mint-cell-wrapper img{
	width: 0.48rem;
}
.search-wrapper .hotsearch-div .mint-cell-wrapper span>span{
	padding-left: 0.12rem;
	padding-right: 0.12rem;
}
.search-wrapper .hotsearch-div .mint-cell-value{
	padding: 0.25rem 0px;
}
.search-wrapper .hotsearch-div .film-title{
	color:#1c1e21;
}
.search-wrapper .hotsearch-div .index-title{
	width: 10%;
}
</style>