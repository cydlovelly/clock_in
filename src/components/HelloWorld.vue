<template>
  <div class="hello">
    <div class="box_title">
      <div class="box_t_p">
        <i class="iconfont iconrili"></i>
        <p>{{years_new}}年{{month_new}}月{{new_day}}号</p>
      </div>
      <div class="box_t_k">
        <i class="iconfont iconjintian box_jin"  @click="jin()"></i>
      </div>
    </div>

    <div class="box_center">
      <div class="box_c_r_i">
        <p v-for="(rlista,i) in rlist" :key="i">{{rlista}}</p>
      </div>
      <div class="box_center_l" id="calendar_list"   v-swiperight="(e)=>Touchright(e)" v-swipeleft="(e)=>touchleft()">

          <div class="box_center_ritem" >
            <div v-for="(dayitem,iay) in daylist" :key="iay">
              <p
                class="box_center_ritem_p"
                :style="{background:dayitem.style,color:dayitem.fontcolor}"  @click="rilclick(iay)"
              >{{dayitem.name}}</p>
              <div class="box_center_ritem_div" v-show="false"></div>
            </div>
          </div>
      </div>
    </div>

    <!-- 打卡展示 -->
    <div class="show">
          <p>你已经坚持打卡{{dakalength}}天</p>
          <p :style={color:dakacolor}>{{years}}年{{month}}月{{gDay}}号。{{dakatext}}</p>
    </div>
    <!-- 打卡按钮 -->
    <div class="daka_btn iconfont icondaka" :style={color:btn_color} @click="dakabtn()"></div>


    <div class="alert" v-show="flag" @click="autohide()">
      <p>{{alerttext}}</p>
      <img src="../assets/alert.png" />
      <div class="renyi">点击任意处关闭弹框</div>
    </div>

  </div>
</template>

<script>
import $ from 'jquery'
export default {
  name: "HelloWorld",
  data() {
    return {
      rlist: ["日", "一", "二", "三", "四", "五", "六"], //周期
      // month_list:[1,2,3,4,5,6,7,8,9,10,11,12],    
      month: "",     //月份
      month_new:"",//不变的月份
      yeara:"",      //是不是润年
      years:"",      //年份
      years_new:"",//不变年份
      dayzong:"",    //当前月份的总天数
      daylist:[],    //这个月多少天
      muth_w:"",//当前月份的第一天，
      new_day:"",   //当前是第几天   不变的天数
      gDay:"",   //通知的天数
      gyearms:"",//几年几月几日
      dakatext:"",    //打卡展示
      dakacolor:"",//打卡样式
      btn_color:"#ee99a0",//打卡按钮样式
      dakalength:"", //打卡天数
      //要存储的数据
      cunday:"",   //日
      cunmouth:"",  //月
      cunyear:"",    //年
      cunlist:[],    //存的数组
      //弹框
      flag:false,
      alerttext:"乖，不要重复打卡",
      //花样
      // sx:"",
      // sy:"",
      // sflag:false
    };
  },
  mounted() {
    this.init();
    // this.touch();
     var length = this.new_day -1;
      document.querySelector('.box_center_ritem div');
      this.huayang();
  },
  methods: {
    init() {

      //  localStorage.removeItem("riliarr");

        var time = new Date();
        this.month = time.getMonth() + 1;
        this.month_new = time.getMonth() + 1;
        if (time.getMonth() == 0) {
          this.month = 12;
        }
       
  
        this.new_day = time.getDate(); //获得天数
        this.years = time.getFullYear();    //获得当前年份
        this.years_new =  time.getFullYear();
        this.gDay =  this.new_day      //更改显示方式
        this.dakatext = '今天还未打卡';
        this.dakacolor = "#333";
        var week = time.getDay(); //获得周

        // console.log(this.years);
        // console.log(this.month);
        var mweek = new Date(this.years,this.month-1,1); 
        this.muth_w =  mweek.getDay();   //获得当前月份第一天周几

        // console.log(this.muth_w);
       this.dayzong = this.runy(this.years,this.yeara,this.month,this.dayzong);
      
      //this.dayzong  这个月的总天数        this.daylist  天数数组集合用来渲染页面   this.muth_w    获得当前月份第一天周几   this.new_day   当前属于第几日
      this.xuanran(this.dayzong,this.daylist,this.muth_w,this.new_day); 

      //初始化按钮样式
      this.cunyear = this.years_new;
      this.cunmouth= this.month_new;
      this.cunday=this.new_day;

       var riliarrs =  localStorage.getItem("riliarr");
        var lengtharr = JSON.parse(riliarrs);
         console.log(lengtharr);
         this.dakalength = lengtharr.length;
        for(let a = 0; a < lengtharr.length; a++){
            if(lengtharr[a].year == this.cunyear && lengtharr[a].month == this.cunmouth && lengtharr[a].day == this.cunday){
                  this.btn_color = '#aaa'
                  this.dakatext = '今天已经打过卡了';
                  this.dakacolor = "#e6669c";
                  return false;
            }
        }





    },
    //判断是否为润年
    runy(a,b,c,e){
        var myRest = /^[1-9]{1}(000){1}$/;    //判断整白年的正则
        if(myRest.test(a)){       //判断是不是整百年 
            if(a % 400 == 0){   
                b = "闰年"
            }else{
                b = "平年"
            }
        }else{
            if(a % 4 == 0){
                b = "闰年"
            }else{
                b = "平年"
            }
        }
        
        if(c== 1 || c == 3 || c == 5 || c == 7 || c == 8 || c == 10 || c == 12){
            e = 31
        }else if(c == 4 || c == 6 || c == 9 || c == 11){
           e = 30;
        }

        if(c ===  2){
            if( b == "闰年"){
                e = 29
            }else{
              e = 28
            }
        }

        return e
    },
     //渲染日历的单位月的天数
    xuanran(a,c,d,e){
      for(let as = 0;as < d;as++){
          c.push({name:"",style:'#fff',fontcolor:'#333'});
      }
      var num = a;

        if(a == num){
         for(var is = 1; is <= num; is++){

                    if(is == e){
                        c.push({name:is,style:"#e6669c",fontcolor:"#fff"});
                    }else{
                        c.push({name:is,style:"#fff",fontcolor:'#333'});
                    }
                     
                // }   
            }
        }
    },
    //滑动事件   
    //左滑
    Touchright(x){
        // console.log(x)
        this.gDay = this.new_day;
        this.daylist = [];
        this.month = this.month-1;

         var mweek = new Date(this.years,this.month-1,1); 
        this.muth_w =  mweek.getDay();   //获得当前月份第一天周几
        if(this.month < 1){
            this.years  =  this.years-1;
            if (this.month == 0) {
                  this.month = 12;
            }
            this.dayzong = this.runy(this.years,this.yeara,this.month,this.dayzong);   //获得月份的总天数
        }
       
        // console.log(this.muth_w);
        this.dayzong = this.runy(this.years,this.yeara,this.month,this.dayzong);   //获得月份的总天数

        this.xuanran(this.dayzong,this.daylist,this.muth_w,this.new_day); 
        this.weiyu();

        // console.log(this.month);

    },
    //右滑
    touchleft(){
        this.daylist = [];
        this.gDay = this.new_day;
        this.month = this.month+1;

         var mweek = new Date(this.years,this.month-1,1); 
        this.muth_w =  mweek.getDay();   //获得当前月份第一天周几
        if(this.month > 12){
            this.years  =  this.years+1;
            if (this.month == 12) {
                  this.month = 1;
            }
            this.dayzong = this.runy(this.years,this.yeara,this.month,this.dayzong);   //获得月份的总天数
        }
       
        // console.log(this.muth_w);
        this.dayzong = this.runy(this.years,this.yeara,this.month,this.dayzong);   //获得月份的总天数

        this.xuanran(this.dayzong,this.daylist,this.muth_w,this.new_day); 
        this.weiyu();
    },
    //点击
    rilclick(e){
        // console.log(e);
        
        var str = 0;
        
         this.daylist = [];
        this.xuanran(this.dayzong,this.daylist,this.muth_w,this.new_day); 
        var arr = this.daylist;
        // str = arr.length - this.dayzong;
          if(arr.length > this.dayzong){
                str = arr.length - this.dayzong;
            } 

        if(e-str < 0){
            return false
        }else{
              
              arr.splice(this.new_day-1+str,1,{name:this.new_day,style:"#fff",fontcolor:"#e6669c"});
              arr.splice(e,1,{name:e+1-str,style:"#e6669c",fontcolor:"#fff"})

              this.gDay = e+1-str;   //显示在上面样式

        }

        this.weiyu();
       

      
    },
    // 返回到今天
    jin(){
      this.daylist = [];
        this.init();
    },
    //打卡点击
    dakabtn(){
        var timecun = new Date();    //创建当前打卡时间。
        this.cunday = timecun.getDay()+1;
        this.cunmouth = timecun.getMonth()+1;
        this.cunyear = timecun.getFullYear();


        if(localStorage.getItem("riliarr") != null){
               var riliarrs =  localStorage.getItem("riliarr");
              var lengtharr = JSON.parse(riliarrs);
              console.log(lengtharr);
              for(let a = 0; a < lengtharr.length; a++){
                  if(lengtharr[a].year == this.cunyear && lengtharr[a].month == this.cunmouth && lengtharr[a].day == this.cunday){
                        this.btn_color = '#aaa'
                        this.alerttext = '乖，不要重复打卡';
                        this.flag = true;
                        return false;

                  }
              }

       
                this.alerttext = '恭喜,打卡成功';
                  this.flag = true;
                this.cunlist.push({day:this.cunday,month:this.cunmouth,year:this.cunyear})

                var arrlist = JSON.stringify(this.cunlist);

                localStorage.setItem("riliarr",arrlist,2000000000000);
                var riliarrs =  localStorage.getItem("riliarr");
        }else{
               this.alerttext = '恭喜,打开成功';
                this.flag = true;
                
              this.cunlist.push({day:this.cunday,month:this.cunmouth,year:this.cunyear})

                var arrlist = JSON.stringify(this.cunlist);

                localStorage.setItem("riliarr",arrlist,2000000000000);
                var riliarrs =  localStorage.getItem("riliarr");
        }

       
           

        // localStorage.removeItem("riliarr");

    },
    autohide(){
          this.flag = false;
          this.jin();
    },
    //判断什么时候是佛漏打或者未打
    weiyu(){
               // console.log(e-str);
                if(this.years >= this.years_new && this.month >= this.month_new && this.gDay > this.new_day){
                        this.dakacolor = '#333';
                        this.dakatext  = "打卡还未开始";
                 }


            if(this.years < this.years_new){
                  this.louda();
            }else if(this.month < this.month_new){
                    this.louda();
            }else if(this.gDay <= this.new_day){
                      this.louda();
            }
        
            
    },
    //判断是否漏打
    louda(){
            var riliarrs =  localStorage.getItem("riliarr");
                    var lengtharr = JSON.parse(riliarrs);
                    console.log(lengtharr);
                    for(let a = 0; a < lengtharr.length; a++){
                        if(lengtharr[a].year == this.years && lengtharr[a].month == this.month && lengtharr[a].day == this.gDay){
                              this.btn_color = '#aaa'
                              this.dakacolor = '#e6669c';
                              this.dakatext  = "打卡已完成"
                              return false;

                        }else{
                              this.btn_color = '#aaa'
                              this.dakacolor = '#333';
                              this.dakatext  = "漏打";
                              return false;
                        }
                    }
    },
    huayang(){
       

        $('body').on("click",function(e){
            let x = e.pageX;
            let y = e.pageY;  
            
             var div = `<div class="box iconfont iconxinaixin" style="position:absolute;width:10px;height:10px"></div>`
              $('body').append(div);
            
            $('.box').css({
              top:y+'px',
              left:x+'px',
              color:'red'
            })
          
            $('.box').animate({
                top:y-100+'px',
                opacity:0,
            },1000,function(){
              $(this).css('display','none')
            })
           
        })
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->0
<style scoped>
/* 日历头部 */
.box_title {
  width: 100vw;
  height: 50px;
  margin: 0 auto;
  display: flex;
  justify-content: space-between;
  background: #e6669c;
  
}
.box_t_p {
  display: flex;
  color: #fff;
  margin-left: 10px;
  align-items: center;
}
.box_t_p i {
  font-size: 30px;
}
.box_t_p p {
  margin-left: 10px;
}
.box_t_k {
  display: flex;
  align-items: center;
  margin-right: 10px;
}
.box_jin {
  font-size: 30px;
  color: #fff;
}

.box_c_r_i {
  width: 90%;
  margin: 10px auto;
  height: 35px;
  display: flex;
  color: #5294a7;
  justify-content: space-between;
}
.box_c_r_i p {
  width: 14%;
  height: 35px;
  line-height: 35px;
  text-align: center;
}
.box_center_l {
  width: 90%;
  margin: 0 auto;
  display: flex;
  flex-wrap: wrap;
  overflow: hidden;
}

.box_center_r {
  width: 14%;
  height: 20px;
}
.box_center_ritem {
  width: 100%;
  display: flex;
  flex-wrap: wrap;
}
.box_center_ritem > div {
  width: 14%;
  height: 35px;
  line-height: 35px;
  background: #fff;
  text-align: center;
}
.box_center_ritem_p {
  width: 100%;
  height: 100%;
}
.box_center_ritem_div {
  width: 100%;
  height: 100%;
}
.show{
  width: 86%;
  margin: 5vh 7%;
  line-height: 40px;
}
.daka_btn{
  width: 100px;
  font-size: 100px;
  position: absolute;
  left: 0;
  right: 0;
  margin: auto;
}
.alert{
  width: 100vw;
  height: 100vh;
  position:fixed;
  top: 0;
  left: 0;
  background: rgba(0,0,0,0.5);
}
.alert img{
  width: 80%;
  height: 280px;
  position: absolute;
  left: 0;
  right: 0;
  top: 0;
  bottom: 0;
  margin: auto;
  z-index: 10;
}
.alert p{
  width: 42%;
    position: absolute;
    top: 56vh;
    left: 50%;
    z-index: 11;
    margin-left: -75px;
    color: #e6669c;
    font-size: 20px;
}
.renyi{
    width: 100%;
    position: absolute;
    top: 72vh;
    text-align: center;
    font-size: 12px;
    color: #fff;
}
.box{
  width: 10px;
  height: 10px;
  position: fixed;
  z-index: 999;
}
</style>
