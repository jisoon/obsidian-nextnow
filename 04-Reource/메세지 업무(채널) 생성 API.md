---
tags: 
created: 2024-12-19 17:14
modified: 2024-12-19 17:14
related: []
updated: 2025-02-28 12:30
---
## Controller
```jsp
<#case "insertChannel">  
          <#--    
메시지업무에서 템플릿 등록시 호출  
              payload = {                  saveDivCd : this.saveType === "messageAllSave" ? "all" : "one",                  ...this.template,                  치환변수추가목록: [...changeOfVariables],  
                  다채널전략구분: this.multiChannelInfo.다채널전략구분,  
                  다채널전략내용: this.multiChannelInfo.다채널전략내용,  
                  템플릿목록: this.multiChannelInfo.템플릿목록,  
금지어정보: this.objForbiddenInfo,  
              }          -->          <#assign apiData = doSvc("message", "svcTmpl", "insertChannel") />  
      <#break />
```

## Service
```jsp
  
<#--    
함수명 : insertChannel함수설명 : 메시지업무에 템플릿 등록  
    파리미터 :리턴 : returnData -->  
<#function insertChannel>  
    <#local returnData = m1.editable({  
        "status" : 200,  
        "code" : "00",  
        "msg" : "",  
        "results" : []  
    }) />  
  
    <#assign r=m1.print("########### 채널 저장 시작 ##########")>  
    <#assign r=m1.print(JSON)>  
  
    <@validate JSON>  
        <@notnull "업무식별자" />  
    </@>  
    <#local verNo = "1" />  
    <#if JSON.버전번호?? && JSON.버전번호 == "0">  
        <#local verNo = "0" />  
    </#if>    <#local r=JSON.put("verNo", verNo)/>  
  
    <#--  메시지업무가 존재하는지 조회  -->    <@SQL params=JSON into="exists">  
        SELECT -- m1bizapi.classes.message.sql.getExistTmpl  
               처리상태구분  
          FROM TSDMSU16  
         WHERE 그룹회사코드 = '${M1_GROUP_CO_CD}'  
           AND 업무식별자 = @업무식별자  
           AND 버전번호 = @verNo  
    </@>  
  
    <#local existBiz = SQLD.exists/>  
  
    <#local 처리상태구분 = "00" />  
    <#list existBiz as row>  
        <#local 처리상태구분 = row["처리상태구분"] />  
    </#list>  
    <#if !(처리상태구분 == "00" || 처리상태구분 == "-9" || 처리상태구분 == "04")>  
        <#local msg = "템플릿을 수정할수 없는 업무상태 입니다." />  
        <#local code = "99" />  
        <#local status = 406 />  
    <#else>        <#if JSON.템플릿일련번호?? && JSON.템플릿일련번호 != "">  
            <#local 템플릿일련번호 = JSON.템플릿일련번호 />  
        <#else>            <#local 템플릿일련번호 = findBizTemplateSeq(JSON.업무식별자, verNo) />  
        </#if>  
        <#local r=JSON.put("템플릿일련번호", 템플릿일련번호)/>  
        <#assign r = m1.print("templateId: "+ 템플릿일련번호 + " ,  ver: "+ verNo + " , bzwkID: "+ JSON.업무식별자) />  
  
        <#-- 템플릿 마스터 테이블에 데이터 존재하지 않으면 저장  -->        <#local r = insertTmplMaster(JSON) />  
  
        <#-- 존재하는 템플릿 개수 조회 -->        <#local cnt = getExistTmplCnt(JSON) />  
  
        <#if (!JSON.첨부파일들?? || JSON.첨부파일들 == "") && JSON.attachfile?? >  
            <#local 첨부파일들 = attach2첨부(JSON.attachfile) />  
        <#else>            <#local 첨부파일들 = JSON.첨부파일들!"" />  
        </#if>        <#local r=JSON.put("첨부파일들", 첨부파일들)/>  
  
        <#if !JSON.채널별전문규격?has_content || JSON.채널별전문규격 == "">  
            <#local r = JSON.put("채널별전문규격", "JS") />  
        </#if>  
        <#local r = JSON.put("서브메시지구분", JSON.메시지구분) />  
  
        <#if JSON.메시지구분 == "SR" || JSON.메시지구분 == "LR" || JSON.메시지구분 == "MR" || JSON.메시지구분 == "TR" || JSON.메시지구분 == "IR">  
            <#if !JSON.템플릿분류?? || JSON.템플릿분류 == "">  
                <#if JSON.메시지구분 == "SR" || JSON.메시지구분 == "LR">  
                    <#local r = JSON.put("템플릿분류", JSON.메시지구분) />  
                </#if>            <#elseif JSON.메시지구분 == "TR" && JSON.템플릿분류?? && ["TC","TD"]?seq_contains(JSON.템플릿분류)>  
                <#local r = JSON.put("LOB내용연계여부", "Y") />  
                <#local r = JSON.put("LOB내용", m1.toJsonBytes(JSON.스타일내용!"")?string) />  
            <#elseif (JSON.메시지구분 == "TR" && JSON.템플릿분류?? && !["TC","TD"]?seq_contains(JSON.템플릿분류)) || JSON.메시지구분 != "TR">  
                <#local r = JSON.put("LOB내용연계여부", "N") />  
            </#if>  
            <#if JSON.RCS전문변환여부?? && JSON.RCS전문변환여부 == "N">  
                <#-- simple version 으로 RCS JSON 변환 (2022-11-05 by GlauCos) -->                <#if !JSON.템플릿분류?? || JSON.템플릿분류 == "">  
                    <#if JSON.메시지구분 == "TR">  
                        <#if JSON.템플릿속성?? && JSON.템플릿속성 != "" && ["C","D"]?seq_index_of(JSON.템플릿속성?substring(JSON.템플릿속성?length -1)) != -1>  
                            <#local r = JSON.put("템플릿분류", "T"+ JSON.템플릿속성?substring(JSON.템플릿속성?length -1)) />  
                        <#else>                            <#local r = JSON.put("템플릿분류", "TF") />  
                        </#if>                    <#else>                    </#if>                </#if>  
                <#local aRcsJson = convertSimpleFEtoBE(JSON) />  
                <#local r = JSON.put("채널별전문내용", aRcsJson["채널별전문내용"]!"") />  
  
                <#if !JSON.템플릿속성?? || JSON.템플릿속성 == "">  
                    <#local r = JSON.put("템플릿속성", aRcsJson["템플릿속성"]!"") />  
                </#if>  
                <#if !JSON.메시지제목?? || JSON.메시지제목 == "">  
                    <#local r = JSON.put("메시지제목", aRcsJson["메시지제목"]!"") />  
                </#if>  
                <#if !JSON.메시지내용?? || JSON.메시지내용 == "">  
                    <#local r = JSON.put("메시지내용", aRcsJson["메시지내용"]!"") />  
                </#if>  
                <#local r = JSON.put("채널별템플릿ID", aRcsJson["채널별템플릿ID"]!"") />  
            <#else>                <#-- complex version 으로 RCS JSON 변환 (2022-10-01 by GlauCos) -->                <#local aRcsJson = dgbRcsComposeFEtoBE(JSON) />  
                <#local r = JSON.put("채널별전문내용", aRcsJson["채널별전문내용"]!"") />  
                <#local r = JSON.put("채널별버튼내용", aRcsJson["채널별버튼내용"]!"") />  
                <#local r = JSON.put("채널별옵션내용", aRcsJson["채널별옵션내용"]!"") />  
                <#local r = JSON.put("템플릿속성", aRcsJson["템플릿속성"]!"") />  
  
                <#if aRcsJson["메시지제목"]??>  
                    <#local r = JSON.put("메시지제목", aRcsJson["메시지제목"]!"") />  
                </#if>  
                <#if aRcsJson["메시지내용"]??>  
                    <#local r = JSON.put("메시지내용", aRcsJson["메시지내용"]!"") />  
                </#if>            </#if>        <#elseif JSON.메시지구분 == "PM">  
            <#if (!JSON.템플릿속성?? || JSON.템플릿속성 == "")>  
            <#-- 푸시상세내용이 존재하면 템플릿속성(푸시메시지작성구분)이 B:둘다 이거나 M:사서함 이다 -->                <#if JSON.푸시상세내용?? && JSON.푸시상세내용 != "">  
                    <#local r = JSON.put("푸시상세내용여부", "H") />  
  
                    <#if JSON.메시지내용 != "">  
                        <#local r = JSON.put("템플릿속성", "B") />  
                    <#else>                        <#local r = JSON.put("템플릿속성", "M") />  
                    </#if>                <#else>                    <#local r = JSON.put("템플릿속성", "N") />  
                </#if>            </#if>  
            <#if !JSON.푸시상세내용여부?? || JSON.푸시상세내용여부 == "">  
                <#local r = JSON.put("푸시상세내용여부", "H") />  
            </#if>        <#elseif JSON.메시지구분 == "KM">  
            <#if !JSON.템플릿속성?? || JSON.템플릿속성 == "">  
                <#local r = JSON.put("템플릿속성", "999999") />  
            </#if>        <#elseif JSON.메시지구분 == "FM">  
            <#local r = JSON.put("서브메시지구분", JSON.템플릿분류!"FT") />  
        </#if>  
       <#if JSON.금지어정보?has_content && JSON.금지어정보.forbiddenWordList?has_content && JSON.금지어정보.forbiddenWordList?is_sequence>  
          <#local r = JSON.put("채널금지어여부", JSON.금지어정보.hasForbiddenYn!"N") />  
          <#local r = JSON.put("채널금지어내용", JSON.금지어정보.forbiddenWordList?join(",")) />  
       <#else>          <#local r = JSON.put("채널금지어여부", "N") />  
          <#local r = JSON.put("채널금지어내용", "") />  
       </#if>  
        <#if 0 == cnt[0]["CNT"]?number>  
            <#assign r = m1.print("[INSERT 23] - templateId: "+ 템플릿일련번호 + " ,  attach: "+ 첨부파일들 + " , bzwkID: "+ JSON.업무식별자) />  
            <#assign r = m1.print(JSON) />  
            <#--  신규 템플릿 저장  -->            <#local insertChannelTemp = insertChannelImpl(JSON) />  
            <#local msg = "등록이 완료되었습니다." />  
            <#local code = "00" />  
            <#local status = 200 />  
        <#else>            <#assign r = m1.print("[UPDATE 23] - templateId: "+ 템플릿일련번호 + " ,  attach: "+ 첨부파일들 + " , bzwkID: "+ JSON.업무식별자) />  
            <#assign r = m1.print(JSON) />  
            <#--  존재하는 템플릿 업데이트  -->            <#local resultSetChannel = setChannel(JSON) />  
            <#local msg = "수정이 완료되었습니다." />  
            <#local code = "00" />  
            <#local status = 200 />  
        </#if>  
        <#--  LOB내용 연계에 따른 LOB테이블 신규생성 및 업데이트  -->        <#if JSON.LOB내용연계여부?? && JSON.LOB내용연계여부 == "Y">  
            <#local setLobLink = setLobLinkImpl(JSON) />  
        </#if>  
        <#-- 메시지구분조회 후 메시지구분유형내용 만들기 -->        <#local aMsgType = m1.editable([]) />  
        <#local getMsgDivType = getMsgDivTypeImpl(JSON) />  
        <#--<#local getMsgDivType = doSql("message", "sqlTmpl", "getMsgDivType", JSON) />-->  
  
        <#list getMsgDivType as row>  
            <#local r = aMsgType.put(row.메시지구분) />  
        </#list>        <#local sMsgTypes = aMsgType?join(",") />  
        <#local r=JSON.put("메시지구분유형내용", sMsgTypes)/>  
  
  
        <#local aReqDataS22 = {"업무식별자":JSON.업무식별자, "업무템플릿일련번호":JSON.템플릿일련번호 } />  
        <#--  업무채널템플릿목록 조회 후 T16.치환변수추가목록 에 설정 -->        <#local sBizVarList = "[]" />  
        <#local aS22Info = getBizTemplateMstInfoImpl(aReqDataS22) />  
        <#local r = m1.print("@@@@@@@@@@ insertChannel - 메시지편집유형", aS22Info.메시지편집유형!"__________", "@@@@@@@@@@@@@@@@") />  
        <#if aS22Info?has_content && !aS22Info.메시지편집유형?has_content || !["F","S"]?seq_contains(aS22Info.메시지편집유형)>  
            <#local aT23List = getTemplateListImpl({"업무식별자":JSON.업무식별자, "업무템플릿일련번호":JSON.템플릿일련번호 }) />  
            <#local aBizVarNameList = extractVariableFromMessages(aT23List, ["메시지내용", "메시지제목", "LOB내용", "채널별전문내용", "채널별버튼내용", "채널별옵션내용"]) />  
            <#local aBizVarList = makeVariableSampleValueList(aBizVarNameList, ["W"]) />  
  
            <#-- 추심피로도 있을시 '${#채권식별번호}' 변수 추가 단, 존재하면 추가 안함 -->            <#if JSON.필터링유형내용?? && JSON.필터링유형내용?seq_contains("G")>  
                <#-- #채권식별번호 key가 있는지 확인 -->                <#assign hasAccountIdKey = false />  
                <#list aBizVarList as item>  
                    <#if item?is_hash && (item?keys?seq_contains("#메시지계좌식별번호") || item?keys?seq_contains("#채권식별번호"))>  
                        <#assign hasAccountIdKey = true />  
                        <#break> <!-- 키를 찾으면 순회 중단 -->  
                    </#if>  
                </#list>  
                <#-- key가 없으면 #채권식별번호 값을 추가 -->                <#if !hasAccountIdKey>  
                    <#local updatedList = aBizVarList.put({"#채권식별번호": ""})/>  
                    <#assign aBizVarList = updatedList />  
                </#if>            </#if>  
            <#local r=m1.print("###aBizVarList : " , aBizVarList)/>  
  
            <#local sBizVarList = m1.toJsonBytes(aBizVarList![])?string />  
        </#if>  
        <#local r = JSON.put("치환변수추가목록", sBizVarList)/>  
  
        <#--  메시지구분유형내용(다채널용) 템플릿 마스터 테이블에 업데이트(S22, T16)  -->  
        <#assign r = m1.print("[UPDATE 22] - templateId: "+ 템플릿일련번호 + " ,  attach: "+ 첨부파일들 + " , sMsgTypes: "+ sMsgTypes) />  
        <#local setMsgDivTypeCont = setMsgDivTypeCont(JSON) />  
        <#--<#local setMsgDivTypeCont = doSql("message", "sqlTmpl", "setMsgDivTypeCont", JSON) />-->  
  
        <#--  템플릿 메시지내용 업데이트  -->        <#local r=m1.print("############## J1S1O1N ##############")/>  
        <#local r=m1.print(JSON)/>  
  
  
       <#-- 업무템플릿 내 채널별 금지어를 merge 해줌 -->       <#local aReqDataForbidden = m1.editable({"업무식별자":JSON.업무식별자, "업무템플릿일련번호":JSON.템플릿일련번호 }) />  
       <#local r = updateSyncTmplMasterForbiddenWord(aReqDataForbidden) />  
  
        <@commit/>  
    </#if>  
    <#local r = returnData.put("msg", msg) />  
    <#local r = returnData.put("code", code) />  
    <#local r = returnData.put("status", status) />  
    <#return returnData />  
</#function>
```


## SQL
```jsp
<#function insertChannelImpl reqData>  
    <@SQL  params=reqData  >  
       insert into TSDMSU16_S23  
          (그룹회사코드, 업무템플릿일련번호, 업무채널템플릿일련번호, 메시지구분, 서브메시지구분, 업무식별자, 템플릿명, 메시지제목,  
          메시지내용, 첨부파일들, 등록일시, 등록직원번호, 푸시앱식별자, 푸시알림함구분, 푸시동의구분,  
          푸시상세내용여부, 푸시상세내용, 푸시파일경로명, 푸시발송방식,  
          버튼사용여부, 활성여부, 템플릿속성,  
          템플릿분류, 채널별브랜드ID, 채널별템플릿ID, 채널별전문규격, 채널별전문내용, 채널별버튼내용,  
          채널별옵션내용, 발송우선순위, LOB내용연계여부, 채널금지어여부, 채널금지어내용, 채널별변수내용)  
       VALUES  
          ('${M1_GROUP_CO_CD}', @템플릿일련번호, SQ_DMSU22_TMPLID.NEXTVAL, @메시지구분, NVL(@서브메시지구분, @메시지구분), @업무식별자, @템플릿명, @메시지제목,  
          @메시지내용, @첨부파일들, TO_CHAR(SYSDATE, 'YYYYMMDDHH24MISS'), '${SESS_INFO.직원번호!""}', @푸시앱식별자, @푸시알림함구분, @푸시동의구분,  
          @푸시상세내용여부, @푸시상세내용, @푸시파일경로명, @푸시발송방식,  
          @버튼사용여부, 'Y', @템플릿속성,  
          @템플릿분류, @채널별브랜드ID, @채널별템플릿ID, @채널별전문규격, @채널별전문내용, @채널별버튼내용,  
          @채널별옵션내용, @발송우선순위, @LOB내용연계여부, NVL(@채널금지어여부,'N'), @채널금지어내용, @채널별변수내용)  
    </@>  
</#function>  
  
<#function setChannel reqData>  
    <@SQL params=reqData >  
       UPDATE TSDMSU16_S23  
       SET 메시지제목 = @메시지제목,  
          메시지내용 = @메시지내용,  
          <#if reqData.이전메시지구분?? && reqData.이전메시지구분 != reqData.메시지구분>  
             메시지구분 = @메시지구분,  
          <#else>  
             <#if ['IR','TR']?seq_contains(reqData.메시지구분)>  
                메시지구분 = @메시지구분,  
             </#if>  
          </#if>          서브메시지구분 = NVL(@서브메시지구분, @메시지구분),  
          템플릿명 = @템플릿명,  
          첨부파일들 = @첨부파일들,  
          푸시앱식별자 = @푸시앱식별자,  
          푸시알림함구분 = @푸시알림함구분,  
          푸시동의구분 = @푸시동의구분,  
          푸시상세내용여부 = @푸시상세내용여부,  
          푸시상세내용 = @푸시상세내용,  
          푸시파일경로명 = @푸시파일경로명,  
          푸시발송방식 = @푸시발송방식,  
          버튼사용여부 = @버튼사용여부,  
          템플릿속성 = @템플릿속성,  
          템플릿분류 = @템플릿분류,  
          채널별브랜드ID = @채널별브랜드ID,  
          채널별템플릿ID = @채널별템플릿ID,  
          채널별전문규격 = @채널별전문규격,  
          채널별전문내용 = @채널별전문내용,  
          채널별버튼내용 = @채널별버튼내용,  
          채널별옵션내용 = @채널별옵션내용,  
          채널금지어여부 = NVL(@채널금지어여부,'N'),  
          채널금지어내용 = @채널금지어내용,  
          채널별변수내용 = @채널별변수내용,  
          발송우선순위 = @발송우선순위,  
          LOB내용연계여부 = @LOB내용연계여부  
       WHERE 그룹회사코드 = '${M1_GROUP_CO_CD}'  
          AND 업무식별자 = @업무식별자  
          <#if reqData.이전메시지구분?? && reqData.이전메시지구분 != reqData.메시지구분>  
             AND 메시지구분 in (@메시지구분, @이전메시지구분)  
          <#else>  
             <#if ['IR','TR']?seq_contains(reqData.메시지구분)>  
                AND 메시지구분 IN ('IR','TR')  
             <#else>  
                AND 메시지구분 = @메시지구분  
             </#if>  
          </#if>          AND 업무템플릿일련번호 = @템플릿일련번호  
    </@>  
</#function>

```
