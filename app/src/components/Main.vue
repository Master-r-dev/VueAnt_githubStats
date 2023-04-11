<template>
  <Table :dates="dat.dates" :cC="dat.cC" :key="updT" :year="dat.year" :weeks="53" @pickDate="$event => handleDate($event)" /> 
  <Activity :years="['2012','2013','2014']" :oDate="dat.oDate" :key="updA"
   :output="outputByMonth" :user="user" @changeYear="$event => handleYear($event)" /> 
</template>

<script setup lang="ts">

import Activity from './Activity.vue'
import DB from '../assets/github-dataset.json'
import Table from './Table.vue'
import { ref } from 'vue';
const dates:string[] =DB.map(d => d.created_at);
const user:string=DB[0].repository_owner
const info:any = DB.map(d =>{ return {
    r_url: d.repository_url ,
    r_des: d.repository_description,
    type:d.type,
    created: d.created_at,
    public:d.public,
    lang:d.repository_language, 
  }}
) 

const isIncluded = function (a:any, b:any) {       
  	if (  a.r_url==b.r_url && a.created.substr(0,10)==b.created.substr(0,10) && a.type==b.type )
        	return true;   
  return false;
}; 
let output:any = JSON.parse(JSON.stringify(info)); 
output.forEach((d:any)=>d.count=1) 
let lengt= info.length;
for (let i = 0; i < lengt; i++) { 
  for (let j = 0; j < lengt; j++) {
  	if (j === i)    	continue;      
    if (isIncluded(output[i], output[j])) {
      output[i].count++ ;
      output.splice(j, 1)
      lengt--;
      j--;
    }
  }        
}  
function compare( a:any, b:any ) {
  if ( a.created < b.created ){
    return -1;
  }
  if ( a.created > b.created ){
    return 1;
  }
  return 0;
}
const outputByMonth:any={'2012':[[],[],[],[],[],[],[],[],[],[],[],[]],
                        '2013':[[],[],[],[],[],[],[],[],[],[],[],[]],
                        '2014':[[],[],[],[],[],[],[],[],[],[],[],[]],
                        '2015':[[],[],[],[],[],[],[],[],[],[],[],[]],
                        '2016':[[],[],[],[],[],[],[],[],[],[],[],[]],
                        '2017':[[],[],[],[],[],[],[],[],[],[],[],[]],
                        '2018':[[],[],[],[],[],[],[],[],[],[],[],[]],
                        '2019':[[],[],[],[],[],[],[],[],[],[],[],[]],
                        '2020':[[],[],[],[],[],[],[],[],[],[],[],[]],
                        '2021':[[],[],[],[],[],[],[],[],[],[],[],[]],
                        '2022':[[],[],[],[],[],[],[],[],[],[],[],[]],
                        '2023':[[],[],[],[],[],[],[],[],[],[],[],[]],};
for (let y of Object.keys(outputByMonth)){
  for (let z=1;z<13;z++){ 
      for (let x in output){
        if (~~z<10){
          if (output[x].created.substr(5,2)==`0${z}` && output[x].created.substr(0,4)==y) outputByMonth[y][z-1].push(output[x])
        }
        else{
          if (output[x].created.substr(5,2)==z && output[x].created.substr(0,4)==y) outputByMonth[y][z-1].push(output[x])
        }        
    }
    outputByMonth[y][z-1].sort(compare ) 
  } 
} 
const updT = ref(0);
const updA = ref(0);
type contributionCount = {
    [key:string]:number;
}
const cC:contributionCount ={}
for (const num of dates) {
  let day:string=num.slice(0,10);    
  cC[day] = cC[day] ? cC[day] + 1 : 1;
}   
const dat={year:'2012',dates:dates,cC:cC,oDate:'null'};
const handleYear= function (this:any,$event:number){ 
  dat.year= ['2012', '2013', '2014', '2015', '2016', '2017', '2018', '2019', '2020', '2021', '2022', '2023'][$event ]
  updT.value+=1
}
const handleDate= function (this:any,$event:any){ 
  dat.oDate = $event.d
  updA.value+=1
}
</script>
