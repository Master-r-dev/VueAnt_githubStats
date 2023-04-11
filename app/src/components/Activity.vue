<template>
    <div id="activity" class="activity">
        <h3>Contribution Activity</h3>
        <a-tabs :activeKey="activeKey" :tab-position="'right'" :style="{ height: scr && !toggleODay ?'400px': '250px', overflow: scr || toggleODay ? 'scroll':'hidden' }"
            @tabClick="changeYear">
            <template v-for="year,i in years" :key="i">
                <a-tab-pane  :tab="`${year}`"> 
                    <template v-if="!toggleODay">
                        <a-timeline>
                            <template v-for="month, i in output[year]" :key="month">
                                <a-timeline-item v-if="month.length > 0">
                                    <h3>{{ monthss(year, i) }}</h3>
                                    <a-timeline>
                                        <template v-for="item in month" :key="item">
                                            <a-timeline-item v-if="!item.public">
                                                <template #dot>
                                                    <IconPrivate />
                                                </template>
                                                <h4>{{ `${item.count} contribution${item.count>1 ?"s":''} in private repositor${item.count>1 ?"ies":'y'}` }}</h4>
                                                <span> {{ item.created.substr(0, 10) }} </span>
                                            </a-timeline-item>
                                            <a-timeline-item v-else-if="item.type == 'PushEvent'">
                                                <template #dot>
                                                    <IconCreate />
                                                </template>
                                                <h4>{{ `Created ${item.count} commit${item.count>1 ?"s":''} in 1 repository` }}</h4>
                                                <a-tooltip placement="top">
                                                    <template #title>
                                                        <span>{{item.r_url.slice(19) }}</span>
                                                        <br />
                                                        <span>
                                                            {{ item.r_des.length > 0 ? item.r_des.substring(0, 42) + '...' :
                                                                '' }}
                                                        </span>
                                                        <br />
                                                        <div style="display:flex;justify-content:space-between;">
                                                            <span>{{ item.lang }}</span> 
                                                            <span>{{ `${item.public ? 'public' : 'private'}`}}</span>
                                                            <span> {{ item.created.substr(0, 10) }} </span>
                                                        </div>
                                                    </template>
                                                    <a :href="item.r_url"> {{ item.r_url.slice(19) }}</a>
                                                </a-tooltip>
                                            </a-timeline-item>
                                            <a-timeline-item v-else-if="item.type == 'PullRequestEvent'">
                                                <template #dot>
                                                    <IconPull />
                                                </template>
                                                <h4>{{ `Opened ${item.count} pull request${item.count>1 ?"s":''} in 1 repository` }}</h4>
                                                <a-tooltip placement="top">
                                                    <template #title>
                                                        <span>{{ item.r_url.slice(19) }}</span>
                                                        <br />
                                                        <span>
                                                            {{ item.r_des.length > 0 ? item.r_des.substring(0, 42) + '...' :  '' }}
                                                        </span>
                                                        <br />
                                                        <div style="display:flex;justify-content:space-between;">
                                                            <span>{{ item.lang }}</span>
                                                            <span>{{ `${item.public ? 'public' : 'private'}`}}</span> 
                                                            <span> {{ item.created.substr(0, 10) }} </span>
                                                        </div>
                                                    </template>
                                                    <a :href="item.r_url"> {{ item.r_url.slice(19) }}</a>
                                                </a-tooltip>
                                            </a-timeline-item>
                                            <a-timeline-item v-else-if="item.type == 'WatchEvent'">
                                                <template #dot>
                                                    <IconReview />
                                                </template>
                                                <h4>{{ `Reviewed ${item.count} request${item.count>1 ?"s":''} in 1 repository` }}</h4>
                                                <a-tooltip placement="top">
                                                    <template #title>
                                                        <span>{{ item.r_url.slice(19) }}</span>
                                                        <br />
                                                        <span>
                                                            {{ item.r_des.length > 0 ? item.r_des.substring(0, 42) + '...' :
                                                                '' }}
                                                        </span>
                                                        <br />
                                                        <div style="display:flex;justify-content:space-between;">
                                                            <span>{{ item.lang }}</span> 
                                                            <span>{{ `${item.public ? 'public' : 'private'}`}}</span>
                                                            <span> {{ item.created.substr(0, 10)   }} </span>
                                                        </div>
                                                    </template>
                                                    <a :href="item.r_url"> {{ item.r_url.slice(19) }}</a>
                                                </a-tooltip>
                                            </a-timeline-item>
                                            <a-timeline-item v-else>
                                                <template #dot>
                                                    <IconGit />
                                                </template>
                                                <h4>{{ `Other ${item.count} contribution${item.count>1 ?"s":''}` }}</h4>
                                                <a-tooltip placement="top">
                                                    <template #title>
                                                        <span>{{ item.r_url.slice(19) }}</span>
                                                        <br />
                                                        <span>
                                                            {{ item.r_des.length > 0 ? item.r_des.substring(0, 42) + '...' :
                                                                '' }}
                                                        </span>
                                                        <br />
                                                        <div style="display:flex;justify-content:space-between;">
                                                            <span>{{ item.lang }}</span>
                                                            <span>{{ `${item.public ? 'public' : 'private'}`}}</span>
                                                            <span> {{ item.created.substr(0, 10)  }} </span>
                                                        </div>
                                                    </template>
                                                    <a :href="item.r_url"> {{ item.r_url.slice(19) }}</a>
                                                </a-tooltip>
                                            </a-timeline-item>
                                        </template>
                                    </a-timeline>
                                </a-timeline-item>
                                <a-timeline-item v-else>
                                    <h3>{{ monthss(year, i) }}</h3>
                                    <span>{{ user }} had no activity during this period. </span>
                                </a-timeline-item>
                            </template>
                        </a-timeline>                       
                    </template>
                    <template v-else> 
                        <a-timeline v-if="output[oDate.substr(0,4)][Number(oDate.substr(5,2))-1].filter((d:any)=>d.created.substr(0,10)==oDate).length>0">                             
                            <template v-for="item in output[oDate.substr(0,4)][Number(oDate.substr(5,2))-1].filter((d:any)=>d.created.substr(0,10)==oDate)  " :key="item">
                                <a-timeline-item >
                                    <h3>{{ monthss(year, Number(oDate.substr(5,2))-1) }},{{oDate.substr(8,2) }}</h3>
                                    <a-timeline> 
                                            <a-timeline-item v-if="!item.public">
                                                <template #dot>
                                                    <IconPrivate />
                                                </template>
                                                <h4>{{ `${item.count} contribution${item.count>1 ?"s":''} in private repositor${item.count>1 ?"ies":'y'}` }}</h4>
                                                <span> {{ item.created.substr(0, 10) }} </span>
                                            </a-timeline-item>
                                            <a-timeline-item v-else-if="item.type == 'PushEvent'">
                                                <template #dot>
                                                    <IconCreate />
                                                </template>
                                                <h4>{{ `Created ${item.count} commit${item.count>1 ?"s":''} in 1 repository` }}</h4>
                                                <a-tooltip placement="top">
                                                    <template #title>
                                                        <span>{{item.r_url.slice(19) }}</span>
                                                        <br />
                                                        <span>
                                                            {{ item.r_des.length > 0 ? item.r_des.substring(0, 42) + '...' :
                                                                '' }}
                                                        </span>
                                                        <br />
                                                        <div style="display:flex;justify-content:space-between;">
                                                            <span>{{ item.lang }}</span> 
                                                            <span>{{ `${item.public ? 'public' : 'private'}`}}</span>
                                                            <span> {{ item.created.substr(0, 10) }} </span>
                                                        </div>
                                                    </template>
                                                    <a :href="item.r_url"> {{ item.r_url.slice(19) }}</a>
                                                </a-tooltip>
                                            </a-timeline-item>
                                            <a-timeline-item v-else-if="item.type == 'PullRequestEvent'">
                                                <template #dot>
                                                    <IconPull />
                                                </template>
                                                <h4>{{ `Opened ${item.count} pull request${item.count>1 ?"s":''} in 1 repository` }}</h4>
                                                <a-tooltip placement="top">
                                                    <template #title>
                                                        <span>{{ item.r_url.slice(19) }}</span>
                                                        <br />
                                                        <span>
                                                            {{ item.r_des.length > 0 ? item.r_des.substring(0, 42) + '...' :  '' }}
                                                        </span>
                                                        <br />
                                                        <div style="display:flex;justify-content:space-between;">
                                                            <span>{{ item.lang }}</span>
                                                            <span>{{ `${item.public ? 'public' : 'private'}`}}</span> 
                                                            <span> {{ item.created.substr(0, 10) }} </span>
                                                        </div>
                                                    </template>
                                                    <a :href="item.r_url"> {{ item.r_url.slice(19) }}</a>
                                                </a-tooltip>
                                            </a-timeline-item>
                                            <a-timeline-item v-else-if="item.type == 'WatchEvent'">
                                                <template #dot>
                                                    <IconReview />
                                                </template>
                                                <h4>{{ `Reviewed ${item.count} request${item.count>1 ?"s":''} in 1 repository` }}</h4>
                                                <a-tooltip placement="top">
                                                    <template #title>
                                                        <span>{{ item.r_url.slice(19) }}</span>
                                                        <br />
                                                        <span>
                                                            {{ item.r_des.length > 0 ? item.r_des.substring(0, 42) + '...' :
                                                                '' }}
                                                        </span>
                                                        <br />
                                                        <div style="display:flex;justify-content:space-between;">
                                                            <span>{{ item.lang }}</span> 
                                                            <span>{{ `${item.public ? 'public' : 'private'}`}}</span>
                                                            <span> {{ item.created.substr(0, 10)   }} </span>
                                                        </div>
                                                    </template>
                                                    <a :href="item.r_url"> {{ item.r_url.slice(19) }}</a>
                                                </a-tooltip>
                                            </a-timeline-item>
                                            <a-timeline-item v-else>
                                                <template #dot>
                                                    <IconGit />
                                                </template>
                                                <h4>{{ `Other ${item.count} contribution${item.count>1 ?"s":''}` }}</h4>
                                                <a-tooltip placement="top">
                                                    <template #title>
                                                        <span>{{ item.r_url.slice(19) }}</span>
                                                        <br />
                                                        <span>
                                                            {{ item.r_des.length > 0 ? item.r_des.substring(0, 42) + '...' :
                                                                '' }}
                                                        </span>
                                                        <br />
                                                        <div style="display:flex;justify-content:space-between;">
                                                            <span>{{ item.lang }}</span>
                                                            <span>{{ `${item.public ? 'public' : 'private'}`}}</span>
                                                            <span> {{ item.created.substr(0, 10)  }} </span>
                                                        </div>
                                                    </template>
                                                    <a :href="item.r_url"> {{ item.r_url.slice(19) }}</a>
                                                </a-tooltip>
                                            </a-timeline-item> 
                                    </a-timeline>
                                </a-timeline-item>                                                   
                            </template>
                        </a-timeline> 
                        <a-timeline v-else>
                                <a-timeline-item >
                                    <h3>{{ monthss(year, Number(oDate.substr(5,2))-1) }},{{oDate.substr(8,2) }}</h3>
                                    <span>{{ user }} had no activity during this period. </span>
                                </a-timeline-item>
                        </a-timeline>     
                    </template>                 
                </a-tab-pane>                
            </template>
        </a-tabs>
        <a-button  type="primary" style="margin-top: 16px" @click="handleClick">{{scr && !toggleODay ?`Hide` :`Show`}} all activity</a-button>
    </div>
</template>

<script lang="ts">
import IconPull from './icons/IconPull.vue'
import IconPrivate from './icons/IconPrivate.vue'
import IconCreate from './icons/IconCreate.vue'
import IconReview from './icons/IconReview.vue'
import IconGit from './icons/IconGit.vue'
import { ref } from 'vue';
import type { TabsProps } from 'ant-design-vue';
 
const activeKey = ref(0);
const changeYear: TabsProps['onTabClick'] = function (this: any, val) { 
    this.activeKey = val;
    this.toggleODay=false; 
    this.$emit('changeYear', val)
};
const handleClick = function (this: any) {
    this.toggleODay=false; 
    this.scr=!this.scr
}

const monthss = function (year: string, i: number) {
    return [`January ${year}`, `February ${year}`, `March ${year}`, `April ${year}`, `May ${year}`, `June ${year}`, `July ${year}`, `August ${year}`, `September ${year}`, `October ${year}`, `November ${year}`, `December ${year}`][i]
} 
export default {
    name: "Activity",
    props: {
        user: {
            required: true,
            type: String
        },
        oDate: {
            type: String,
            required: true
        }, 
        years: {
            required: true,
            type: Array<string>,
            default: ['2012', '2013', '2014', '2015', '2016', '2017', '2018', '2019', '2020', '2021', '2022', '2023']
        },
        output: {
            required: true,
            type: Object
        },
        idSel: {
            type: Number,
            default: -1
        },
    },
    data() {
        return {
            activeKey, 
            scr:false,
            toggleODay: this.oDate=='null' ? false :true,  
        }
    },
    methods: {
        changeYear, handleClick, monthss
    },
    components: {
        IconPull, IconPrivate, IconCreate, IconReview, IconGit
    }
}
</script>
<style>
.activity {
    margin-top: 10px;
}

h5 {
    color: white !important;
} 
.ant-timeline {
    margin: 10px !important;
}

 

</style>
