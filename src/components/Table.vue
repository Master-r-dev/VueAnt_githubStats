<template>
  <div class="outlin">
    <table id="github-stats" :height="10 * squareSize">      
        <tr>
          <th class="cal-months" v-for="month in ['','Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec']" :key="month"
            fill="#000000" :colspan="month ? 4: 2" :width="month ? 'auto': '29px' "
            >{{ month }}</th>
        </tr>
        <tr v-for="day in days" :key="day"> 
          <td class="cal-label">
            <span aria-hidden="true"  class="cal-labelName">
              {{ ['', 'Mon', '', 'Wed', '', 'Fri', '',][day-1] }}
              </span>            
          </td> 
              <a-tooltip  class="cal-week" v-for=" week  in weeks"  :key="week" placement="top" >
                <template #title>
                  <span>{{makeTitle(year, week,day,cC[createDate(year,week,day)])}}</span>
                </template> 
                <td class="cal-day" v-if="!(week-1==0 && day-1<firstDay) && !(week*7-7+day>366) "
                    @click="e=>handleClick(e,createDate(year,week,day))"
                    :day="createDate(year,week,day)"
                    :style="{backgroundColor:  
                            cC[createDate(year,week,day)]
                            ? rangeColors[contribCount(cC[createDate(year,week,day)])] 
                            : '#e2dddd'   }"                            
                    ></td> 
              </a-tooltip>                    
          </tr> 
          
          <caption class="capp">Less 
             <span class="cal-day" :style="{ backgroundColor:rangeColors[0]}"> </span>
             <span class="cal-day" :style="{ backgroundColor:rangeColors[1]}"> </span>            
             <span class="cal-day" :style="{ backgroundColor:rangeColors[2]}"> </span>
             <span class="cal-day" :style="{ backgroundColor:rangeColors[3]}"> </span>
             <span class="cal-day" :style="{ backgroundColor:rangeColors[4]}"> </span> 
              More</caption>
      </table>
      <div> <a href="https://docs.github.com/articles/why-are-my-contributions-not-showing-up-on-my-profile" style="color:#656d76">
              Learn how we count contributions</a> </div>
    </div>
  </template>
  
  <script lang="ts"> 
  import type { PropType } from 'vue'
  const COLOR_RANGE = ['#ebedf0', '#c6e48b', '#7bc96f', '#239a3b', '#196127'] 
    type contributionCount = {
      [key:string]:number;
  }   
  const getDateC= (year:string,week:number,day:number,t:boolean) =>{  
    return t ? new Date(Number(year),0,week*7-7+day).getDate() : new Date(Number(year),0,week*7-7+day).getMonth()+1
  } 
  const createDate=(year:string,week:number,day:number)=>{
        return `${year}-${getDateC(year,week,day,false) < 10 
              ? getDateC(year,week,day,false).toString().padStart(2, '0') 
              : getDateC(year,week,day,false)}-${getDateC(year,week,day,true) < 10 
              ? getDateC(year,week,day,true).toString().padStart(2, '0') 
              : getDateC(year,week,day,true)}`
      }
  export default {
    name: "Table",
    props: {
      dates:{
        required:true
      },
      cC:{
        required:true,
        type: Object as PropType<contributionCount>
      },
      year: {
        required: true,
        type: String
      },
      weeks: {
        type: Number,
        default: 53
      },
      days:{
        type: Number,
        default: 7
      }, 
      rangeColors: {
        type: Array<string>,
        default: () => COLOR_RANGE
      },

    },
    data() {
      return {  
        values: [],
        squareSize: 11,
        firstDay :new Date(Number(this.year),0,1).getDay(), // 0-6 ['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat',] 
      }
    },
    methods: { 
      handleClick(e:any,d:string){
        console.log(e)
        e.srcElement.attributes['style'].value='background-color: #056dba;'
        this.$emit('pickDate',{d,t:true})
      },
      createDate,
      contribCount(count:number) {
        return count >= this.rangeColors.length ? this.rangeColors.length - 1 : count
      },
      makeTitle(year:string,week:number,day:number,Count:number){ 
          return `${Count > 0 ? Count : 'No'}\
                      contribution${Count > 1 ? 's' : ''} on \
                      ${new Date(createDate(year,week,day)).toDateString().split(/(?<=[A-z]) (?=[A-z])|(?<=\d) (?=\d)/).join(', ')}`                 
      },
    },  
  }
  </script>
  
<style >
#github-stats {
    width: 100%;
} 
.outlin{
  border: 1px solid black;
  padding:5px;
}
.capp{  
  text-align:right;
}
.capp .cal-day{
  display:inline-block;
}
.lleft{
  float: left !important;
} 
.cal-label{
  padding: .125em .5em .125em 0;
  font-size: 12px; 
  text-align: left;
  min-width:29px;
} 
td:nth-of-type(0){
  height:20px;
}
.cal-labelName{
  
  clip-path: None;
   position: absolute;
   bottom: -4px;
}
.cal-day{
  width:11px;
  height:11px; 
  opacity: .8;
  shape-rendering: geometricPrecision;
  background-color: rgb(235, 237, 240);
  border-radius: 3px;
  outline: 1px solid rgba(27,31,35,0.06);
  outline-offset: -1px;
}
.cal-day.active {
    stroke-width: 1;
    stroke: black;
}  

</style>