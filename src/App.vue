<template>
  <div id="app">
    <div class="flex">
      <div class="head-left">
        <h1>空氣品質指標 (AQI)</h1>
        <select v-model="selectCity">
          <option value="請選擇地區" disabled >請選擇地區</option>
          <option v-for='(city) in citys' :key='city' :value='city'>{{city}}</option>
        </select>
      </div>
      <div class="head-right">
        <div class="indicator">
          <ul v-for='(indicator,id) in indicators' :key="id">
            <li :style='`background:${indicator.color}`' >{{indicator.score}}</li>
            <li v-html='indicator.comment'></li>
          </ul>
        </div>
      </div>
    </div>
    <!-- 縣市 -->
    <div class="grid">
      <h2>{{selectCity}}</h2>
      <div class="dot"></div>
      <div class="publishTime">{{selectCity !== '請選擇地區'? userSelect[0].PublishTime:''}} 更新</div>
    </div>
    
    <!-- 內容 -->
    <div class="content" v-if="selectCity !== '請選擇地區'">
      <div class="content-left">
        <div class="content-left-header">
          <h2 class="city">{{userSelect[0].SiteName}}</h2>
          <h2 class="aqi" :style='`background:${aqiColor(userSelect[0].AQI)}`'>{{userSelect[0].AQI}}</h2>
        </div>
        
        <div class="content-left-bottom">
          <div class="air" v-for='(item,i) in air' :key='i'>
            <div class="box flex">        
              <h5 class="air-content">{{item.name}}<span>{{item.en}}{{item.unit}}</span></h5><h5>{{userSelect[0][item.en]}}</h5>
            </div>
          </div>
        </div>
        
      </div>
      <div class="content-right" >
        <div class="content-left-header otherCity" v-for='(item,i) in userSelect' :key='i'>
          <h2 class="city">{{item.SiteName}}</h2>
          <h2 class="aqi" :style='`background:${aqiColor(item.AQI)}`'>{{item.AQI}}</h2>
        </div>
      </div>

    </div>
  </div>
</template>

<script>

export default {
  data(){
    return{
      citys:['基隆市','臺北市','新北市','桃園市','新竹市','新竹縣','苗栗縣','臺中市','彰化縣','雲林縣','嘉義縣','臺南市','高雄市','屏東縣','臺東縣','花蓮縣','宜蘭縣'],
      indicators:[
        {score:'0~50',comment:'良好',color:'#95F084'},
        {score:'51～100',comment:'普通',color:'#FFE695'},
        {score:'101～150',comment:'<span>對敏感族群</span><span>不健康</span>',color:'#FFAF6A'},
        {score:'151～200',comment:'<span>對所有族群</span><span>不健康</span>',color:'#FF5757'},
        {score:'201～300',comment:'非常不健康',color:'#9777FF'},
        {score:'301～400',comment:'危害',color:'#AD1774'}
      ],
      selectCity:'請選擇地區',
      allData:null,
      air:[
        {name:'臭氧',en:'O3',unit:'(ppb)'},
        {name:'懸浮微粒',en:'PM10',unit:'(μg/m³)'},
        {name:'細懸浮微粒',en:'PM2.5',unit:'(μg/m³)'},
        {name:'一氧化碳',en:'CO',unit:'(ppm)'},
        {name:'二氧化硫',en:'SO2',unit:'(ppb)'},
        {name:'二氧化氮',en:'NO2',unit:'(ppb)'},
      ]
    }
  },
  methods:{
    getData(){
      fetch('https://script.google.com/macros/s/AKfycbwmQ2UhNUY7Id1hUoc5BosD7MVcqM_w-lqe1kLZxsFtDgQSA-4/exec?url=http://opendata.epa.gov.tw/ws/Data/AQI/?$format=json')
        .then (response => {
        return response.json()
      }).then( data => {
        this.allData = data
      })
    },
    aqiColor(num){
      console.log(num)
      if (num <= 50){
        return '#95F084'
      }else if(num <= 100 && num > 50){
        return '#FFE695'
      }else if(num <= 150 && num > 100){
        return '#FFAF6A'
      }else if(num <= 200 && num > 150){
        return '#FF5757'
      }else if(num <= 300 && num > 200){
        return '#9777FF'
      }else if(num <= 400 && num > 300){
        return '#AD1774'
      }
    },
  },
  computed:{
    userSelect () {
      if(this.allData){
        return this.allData.filter(x => x.County === this.selectCity)
      }
      return ''
    }
  },
  
  created(){
    this.getData()
  }
    
}
</script>

<style>
html,body{
  margin:0;
  padding: 10px 0;
  font-family: 'Noto Sans TC', sans-serif,微軟正黑體;
}
*{
  box-sizing: border-box
}
#app{
  padding:53px 85px;
  width: 1280px;
  background: #F0F0F0;
  margin: 0 auto
}
h1{
  margin-top: 0;
  line-height: 32px;
}
ul{
  margin: 0;
  padding: 0;
  display: inline-block;
  vertical-align: middle;
}
li{
  list-style: none;
  border: 3px solid #000;
  border-right: 0;
  width: 121px;
  height: 55px;
  display: flex;
  align-content: space-between;
  justify-content: center;
  align-items:center;
  flex-direction: column;
}
li span{
  display: inline-block;
  width: 121px;
  text-align: center;
}
li:first-child{
  list-style: none;
  border-bottom: 0px
}
ul:last-child li{
  border-right: 3px solid #000;
}
.flex{
  display: flex;
  justify-content: space-between
}
select,option{
  width: 350px;
  height: 56px;
  border: 3px solid #000;
  font-size: 16px;
  font-weight: bold;
  font-family: 'Noto Sans TC', sans-serif,微軟正黑體;
  padding-left: 20px
}
option{
  padding: 20px
}
select:focus{
  outline: none
}
option:hover{
  background: #000;
  color:#fff
}
.grid{
  display: grid;
  grid-template-columns: auto 1fr auto
}
h2{
  font-size: 36px;
}
.dot{
  border-top: 5px dotted #000;
  margin: 56px 30px;
  height: 5px;
}
.publishTime{
  line-height: 117px;
}
.content-left, .content-right{
  display: inline-block; 
  font-size: 36px;
  width: 350px;
  vertical-align: top
}
.content-left{
  border: 3px solid #000;
}
.content-left-header{
  display: inline-block;
  line-height: 97px;
  text-align: center;
}
.city{
  width: 184px;
  height: 97px;
  display: inline-block;
  border-right: 3px solid #000;
  margin: 0
}
.aqi{
  width: 160px;
  height: 97px;
  display: inline-block;
  margin: 0
}
h5{
  font-size: 24px;
  margin: 16px 0;
}
h5 span{
  font-size: 16px;
  font-weight: normal;
}
.air{
  border-bottom: 1px solid #000
}
.content-left-bottom{
  padding: 14px 30px;
  border-top: 3px solid #000;
}
.air:last-child{
  border: none
}
.content-right{
  width: 760px;
}
.otherCity{
  margin: 0 0 30px 30px;
  border: 3px solid #000;

}
</style>
