
<template>
  <div class="hello">
    <div class="container">
        <div style="height:10px;"></div>    
        <nav class="grey lighten-5">
          <div class="nav-wrapper">
            <a class="brand-logo center"style="color:#41B883;"><i class="material-icons large">chat</i>전체로그</a>
          </div>
        </nav>  

        <div class="row">

          <!-- 로그 -->


          <div class="col s12 m12 l9">

            <!-- 로그 추가 -->
            <div class="card grey lighten-5">

              <!-- 로그 추가 버튼 -->
              <div class="center-align">
              <router-link to="/logAdd">
                <a style="cursor:Pointer;">
                  <i class="material-icons medium"style="color:#41B883">add</i>
                </a>
              </router-link>
              </div>
       
            </div>             

            <div @scroll="logScroll()" id="logScroll" style="height:600px;overflow:auto">
              <div id="innerScroll" style="margin:10px;">

                <template v-for="(log,index) in logData">
                  <div class="card grey lighten-5" :style="'border-left: solid;border-left-color: #'+log.PRO_COLOR+';'">
                    <div style="padding:2px;font-size:11pt">
                      <div class="row" :id="'logId'+log.LOG_ID">
                        <!-- 로그 view -->
                        <div class="view">
                          <div class="col s12">
                            {{log.LOG_WRITER}} 
                            <span style="float:right;">                       
                              <span :style="'font-size:8pt;color:#'+log.PRO_COLOR+';'">{{log.PRO_NAME}}</span> 
                              {{log.LOG_DATE}}
                            </span>                                             
                          </div>

                          <div class="divider"></div> 


                          <div class="col s12" style="word-break: break-all;">
                            <div class="divider" style="margin:10px;"></div> 
                            {{log.LOG_DETAIL}}           
                          </div>  

                          <div class="col s12">
                            <span style="float:right;">
                              <a class="pointer" @click="logUpdateForm(log.LOG_ID,index)">수정</a> 
                              <a class="pointer" @click="logDeleteForm(log.LOG_ID,index)">삭제</a>                              
                            </span>                          
                          </div>
                 
                        </div>

                        <!-- 로그 edit -->
                        <div class="edit">
                        
                          <div class="col s12">
                            <input type="text" :value="log.LOG_WRITER" :id="'logModifyLogWriter'+log.LOG_ID" style="width:40%">
                            <!-- log modify member key -->
                            <input :id="'logModifyMemberKey'+log.LOG_ID" type="password" data-length="10" placeholder="MEMBERKEY"style="width:40%">                                                         
                          </div>

                          <div class="col s11">
                            <textarea :id="'logModifyDetail'+log.LOG_ID"class="materialize-textarea" data-length="120" style="font-size:12pt;">{{log.LOG_DETAIL}}</textarea>
                          </div>  

                          <div class="col s12" style="text-align:right">
                            <a class="waves-effect waves-teal btn-flat" @click="logUpdateAction(log.LOG_ID,index)">확인</a> 
                            <a class="waves-effect waves-teal btn-flat" @click="logUpdateFormOut(log.LOG_ID)">취소</a>
                          </div>                        
                        </div>
                                        
                      </div>
                    </div>           
                  </div>            
                </template>  

              </div>
            </div> 
                                                                                            
          </div>

          <!-- 모아보기 -->
          <div class="col s12 m12 l3 flow-text">

            <div class="row">

              <!--
              <div class="col s6 m6 l12">
                <div class="card grey lighten-5 center-align moaTitle">
                  <span style="font-size:13pt;">날짜별 모아보기</span>
                  <div style="padding-left:10px;padding-right:10px;">
                    <input type="date" id="logStartDate" class="datepicker"> ~
                    <input type="date" id="logEndDate"class="datepicker">
                  </div>
                  <div style="text-align:right;">
                    <a @click="dateLogSearch" class="waves-effect waves-light btn" style="background-color:#41B883;margin-bottom:5px;">모아보기</a>
                  </div>              
                </div>
              </div>
              -->

              <div class="col s12 m12 l12">
                <div class="card grey lighten-5 center-align moaTitle" style="height:700px;overflow:auto;">
                  <span style="font-size:13pt;">프로젝트별 모아보기</span>
                  <div class="collection" style="font-size:11pt;">

                    <a @click="totalLog()" class="collection-item projectSelector" style="cursor:pointer">전체로그</a>

                    <template v-for="project in projectData">
                      <a @click="projectLogSearch(project.PRO_ID)" :id="'proItem'+project.PRO_ID" class="collection-item projectSelector" style="cursor:pointer">{{project.PRO_NAME}}</a>                    
                    </template>

                  </div>                       
                </div>               
              </div>    

            </div>

          </div>          
          
        </div>

    </div>
  </div>
</template>

<script>

var context = require('../../context.js');

export default {

  name: 'logList',
  components: {
    
  },
  data () {
    return {
      logData:[ ]
      ,projectData:[ ]
      ,logCurrentCount:1
      ,limit:10
      ,logType:'total'
      ,projectId:''
    }
  },
  methods:{
    //로그 수정 폼 열기
    logUpdateForm : function(logId){
      $('#logId'+logId).find('.edit').show();
      $('#logId'+logId).find('.view').hide();
    }
    //로그 수정 폼 닫기
    ,logUpdateFormOut : function(logId){
      $('#logId'+logId).find('.view').show();
      $('#logId'+logId).find('.edit').hide();
    }

    //로그 수정
    ,logUpdateAction : function(logId,index){    
        if($('#logModifyMemberKey'+logId).val() == context.memberKey){
        $.ajax({
          url:context.hostUrl+'/projectLogModify',
          async:false,
          type:'post',
          data:{ logId : logId
                ,logWriter : $('#logModifyLogWriter'+logId).val()
                ,logDetail : $('#logModifyDetail'+logId).val()},
          dataType : "json",
          success : function(data){ },
          error : function(err){ console.log(err); }
        });    

        this.logData[index].LOG_WRITER = $('#logModifyLogWriter'+logId).val();
        this.logData[index].LOG_DETAIL = $('#logModifyDetail'+logId).val();
        $('#logId'+logId).find('.view').show();
        $('#logId'+logId).find('.edit').hide(); 
        Materialize.toast('로그가 수정되었습니다..!', 4000);  
      }else {
        Materialize.toast('멤버키 불일치.!', 4000);
      }
   
    }

    //로그 삭제 확인 창
    ,logDeleteForm : function(logId,index){
      var logDeleteMemberKey = prompt("멤버키 입력");
      if(logDeleteMemberKey == context.memberKey){
        $.ajax({
          url:context.hostUrl+'/projectLogDelete',
          async:false,
          type:'post',
          data:{logId:logId},
          dataType : "json",
          success : function(data){ },
          error : function(err){ console.log(err); }
        });   
        Materialize.toast('로그가 삭제되었습니다..!', 4000);
        this.logData.splice(index,1);          
      } else {
        Materialize.toast('멤버키 불일치.!', 4000);
      }
  
    }

    //로그스크롤 끝 페이징
    ,logScroll : function(){   

        if($('#logScroll').scrollTop()+60 > $('#innerScroll').height() - $('#logScroll').height()+45 ){

          let getLogData = 
          $.ajax({
            url:context.hostUrl+'/searchAllLog',
            async:false,
            type:'post',
            data:{'logType':this.logType
                  ,'logCurrentCount':this.logCurrentCount
                  ,'limit':this.limit,'proId':this.projectId
                  ,'logStartDate':$('#logStartDate').val()
                  ,'logEndDate':$('#logEndDate').val()
            },
            dataType : "json",
            success : function(data){ },
            error : function(err){ console.log(err); }
          }); 

          let log = getLogData.responseJSON
          for(var i in log){
            this.logData.push(log[i]);
          }
          this.logCurrentCount = this.logCurrentCount+10
          this.limit = this.limit+10;    
        }
    }    

    //프로젝트별 모아보기
    ,projectLogSearch : function(id){
      $('.projectSelector').removeClass('active');
      $('#proItem'+id).addClass('active')

      this.logCurrentCount=1;
      this.limit=10;
      this.logType='project';
      this.projectId=id;

      let getlogDataforProId = 
      $.ajax({
        url:context.hostUrl+'/searchAllLog',
        async:false,
        type:'post',
        data:{'logType':this.logType,'logCurrentCount':this.logCurrentCount,'limit':this.limit,'proId':this.projectId},
        dataType : "json",
        success : function(data){ },
        error : function(err){ console.log(err); }
      }); 


      this.logCurrentCount+=10;
      this.limit+=10;  
      this.logData=getlogDataforProId.responseJSON;

    }

    //전체 프로젝트 로그 가져오기
    ,totalLog : function(){

      this.logCurrentCount=1;
      this.limit=10;
      this.logType='total';

      //init logData
      let initLogData = 
        $.ajax({
          url:context.hostUrl+'/searchAllLog',
          async:false,
          type:'post',
          data:{'logType':this.logType,'logCurrentCount':this.logCurrentCount,'limit':this.limit},
          dataType : "json",
          success : function(data){ },
          error : function(err){ console.log(err); }
        }); 

      this.logData=initLogData.responseJSON;
      this.logCurrentCount = this.logCurrentCount+10
      this.limit = this.limit+10;
    }    

    //날짜별 모아보기
    ,dateLogSearch : function(){
      
      this.logCurrentCount=1;
      this.limit=10;
      this.logType='date';
   
      let getlogDataforProDate = 
      $.ajax({
        url:context.hostUrl+'/searchAllLog',
        async:false,
        type:'post',
        data:{'logType':this.logType,'logCurrentCount':this.logCurrentCount,'limit':this.limit,'logStartDate':$('#logStartDate').val(),'logEndDate':$('#logEndDate').val()},
        dataType : "json",
        success : function(data){ },
        error : function(err){ console.log(err); }
      });      

      this.logCurrentCount+=10;
      this.limit+=10;      
      this.logData=getlogDataforProDate.responseJSON;
    }  
      

  } //methods end

  ,mounted : function(){

    //init logData
    let initLogData = 
      $.ajax({
        url:context.hostUrl+'/searchAllLog',
        async:false,
        type:'post',
        data:{'logType':this.logType,'logCurrentCount':this.logCurrentCount,'limit':this.limit},
        dataType : "json",
        success : function(data){ },
        error : function(err){ console.log(err); }
      }); 

    this.logData=initLogData.responseJSON;
    this.logCurrentCount = this.logCurrentCount+10
    this.limit = this.limit+10;

    //init projectData
    let initProjectData =
      $.ajax({
        url:context.hostUrl+'/searchLogProject',
        async:false,
        type:'post',
        data:{ },
        dataType : "json",
        success : function(data){ },
        error : function(err){ console.log(err); }
      });     

      this.projectData=initProjectData.responseJSON;
      
 

    //datepicker
    $('.datepicker').pickadate({
        format: 'yyyy-mm-dd',
        selectMonths: true, // Creates a dropdown to control month
        selectYears: 15 // Creates a dropdown of 15 years to control year
      });       

  }
}



</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

.collection .collection-item.active{
  background-color:#41B883;
}

.moaTitle {
    border-left: solid;
    border-left-color: #41B883;  
}


.edit {
  display:none;
}

.pointer{
  cursor:pointer;
}
</style>
