
<template>
  <div class="hello">
    <div class="container">

        <div style="height:10px;"></div>    

        <nav class="grey lighten-5">
          <div class="nav-wrapper">
            <a class="brand-logo center"style="color:#41B883;"><i class="material-icons large">assignment</i>프로젝트 목록</a>
            <ul class="right ">
              <li>
                <router-link to="/projectAdd">
                  <a style="color:#41B883;">
                    <i class="material-icons left">add</i>프로젝트 등록
                  </a>
                </router-link>
              </li>
            </ul>               
          </div>          
        </nav>  



      <div class="card grey lighten-5 " style="padding:10px;">
        <div class="row">
    

          <!-- 진행중인 프로젝트 -->
          <div class="col s12">
            <h5 style="font-weight: lighter;"><blockquote>진행중인 프로젝트</blockquote></h5>
            <template v-for="project in projectListData" v-if="project.PRO_PROGRESS!=100">

              <router-link :to="'/projectDetail/'+project.PRO_ID" style="color:#4C4C4C">
                <div class="col s12 m6 l3">
                  
                  <div class="card"style="min-height:200px;">
                  
                      <div class="center-align" :style="'padding-top:25px;width:100%;min-height:60px;background-color:#'+project.PRO_COLOR+';'">
                        <!-- 프로젝트명 -->
                        <div style="font-weight: lighter;font-size:16pt;position:relative;z-index:1;top:-10px;color:#FFFFFF;">
                          <i class="material-icons" style="position:relative;top:3px;">description</i>{{project.PRO_NAME}}
                        </div> 
                      </div>   

                      <div class="center-align" style="font-size:10pt;" >                      

                        <!-- 여백 -->
                        <div style="height:15px;"></div>
                                
                        <div style="font-size:13pt;color:#41B883;">
                          <i class="material-icons" style="position:relative;top:5px;">play_arrow</i>
                          진행중
                        </div>

                        <!-- 진행률 -->
                        <div>프로젝트 진행율: {{project.PRO_PROGRESS}}%
                          <div class="progress container" style="height:10px;">
                            <div class="determinate" :style="'width:' +project.PRO_PROGRESS+'%;background-color:#'+project.PRO_COLOR+';'"></div>
                          </div>                  
                        </div>

                        <!-- 프로젝트 시작일 -->
                        <div>시작일:{{project.PRO_START_DATE}}</div>

                      </div>
                    

                  </div>
                </div>   
              </router-link>                

            </template>          
          </div>

          <!-- 마감된 프로젝트 -->
          <div class="col s12">
            <h5 style="font-weight: lighter;"><blockquote>마감된 프로젝트</blockquote></h5>
            <template v-for="project in projectListData" v-if="project.PRO_PROGRESS==100">

              <router-link :to="'/projectDetail/'+project.PRO_ID" style="color:#4C4C4C">
                <div class="col s12 m6 l3">
                  
                  <div class="card"style="min-height:200px;">
                  
                      <div class="center-align" style="padding-top:25px;width:100%;min-height:60px;background-color:#424242;">
                        <!-- 프로젝트명 -->
                        <div style="font-weight: lighter;font-size:16pt;position:relative;z-index:1;top:-10px;color:#FFFFFF;">
                          <i class="material-icons" style="position:relative;top:3px;">description</i>{{project.PRO_NAME}}
                        </div> 
                      </div>   

                      <div class="center-align" style="font-size:10pt;" >                      

                        <!-- 여백 -->
                        <div style="height:15px;"></div>
                                
                        <div style="font-size:13pt;color:#ef5350;">
                          <i class="material-icons" style="position:relative;top:5px;">pause</i>
                          프로젝트 마감
                        </div>

                        <!-- 진행률 -->
                        <div>프로젝트 진행율: {{project.PRO_PROGRESS}}%
                          <div class="progress container" style="height:10px;">
                            <div class="determinate" :style="'width:' +project.PRO_PROGRESS+'%;background-color:#'+project.PRO_COLOR+';'"></div>
                          </div>                  
                        </div>

                        <!-- 프로젝트 시작일 -->
                        <div>시작일:{{project.PRO_START_DATE}}</div>

                      </div>
                    

                  </div>
                </div>   
              </router-link>                

            </template>                
          </div>          
                      
        </div>
      </div>

    </div>
  </div>
</template>

<script>

var context = require('../../context.js');


export default {

  name: 'projectList',
  components: {
    
  }
  ,data () {
    return {
      projectListData : []
    }
  }
  ,mounted : function(){

    /* ----------------------------------------------
    * 프로젝트 리스트 초기값 가져오기
    */
    var initProjectList = 
          $.ajax({
              url:context.hostUrl+'/projectListData',
              async:false,
              type:'get',
              dataType : "json",
              success : function(data){ 

              },
              error : function(err){
                console.log(err);
              }
          });
    //프로젝트 리스트에 바인딩
    this.projectListData = initProjectList.responseJSON;

    $('.collapsible').collapsible();

  } //mounted end

}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
