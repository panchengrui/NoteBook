SpringBoot 整合slf4j输出日志，在`application.properties`文件中加入：

```properties
logging.file.path=H:/
logging.file.name=log.log
```



## 封装一个查看每个Sheet表执行时，查看单条记录插入时间的工具类

```java
package com.snt.medevid.util;

import com.alibaba.excel.EasyExcel;
import com.alibaba.excel.event.AnalysisEventListener;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import java.lang.reflect.Field;

/**
 * 获取Excel每个Sheet中，单条记录转换导入的时间
 * 调用方法：RunningTimeUtil.getTime(path, BasicHealthInfo.class, new BasicHealthInfoListener(userId, type), "患者基本信息");
 *          RunningTimeUtil.getTime(path, Outpatient.class, new OutpatientListener(userId), "门诊记录");
 */
public class RunningTimeUtil {
    private static final Logger log = LoggerFactory.getLogger(RunningTimeUtil.class);

    public static void getTime(String path, Class<?> clazz, AnalysisEventListener listener, String sheetName) throws Exception{
        Long start = System.currentTimeMillis();
        EasyExcel.read(path, clazz, listener).sheet(sheetName).doRead();
        Long end = System.currentTimeMillis();

        // 获取监听器中的count成员
        Field field = listener.getClass().getDeclaredField("count");
        // 解除封装
        field.setAccessible(true);
        // 获取count的值
        int count = field.getInt(listener);
        if(count != 0){
            log.info(sheetName + "——单条记录执行时间：" + (end - start)/count + "ms");
        }
    }
}
```





## 使用	NamedParameterJdbcTemplate  实现数据的批量操作

```java
// 批量插入，参数为List，其中装载的为对象
public void insertBatchDeathRecord(List<DeathRecord> deathRecords){
    String sql = "INSERT INTO template.v_death_record (id, patient_id, visit_id, medical_record_number, death_record_id, death_time, cause_of_death, death_diagnosis, medical_organization_code, medical_organization_name, effective_time, admin_id) " + "VALUES" +
        "(:id, :patientId, :visitId, :medicalRecordNumber, :deathRecordId, :deathTime, :causeOfDeath, :deathDiagnosis, :medicalOrganizationCode, :medicalOrganizationName, :effectiveTime, :adminId)";
    SqlParameterSource[] beanSources = SqlParameterSourceUtils.createBatch(deathRecords);
    try {
        jdbcTemplate.batchUpdate(sql, beanSources);
    } catch (DataAccessException e){
        log.error(e.getMessage());
    }
}

// 批量删除，参数为List,其中装载的是基本数据类型
public void deleteBatchHistoryRecord(List<Long> personIds){
    int len = personIds.size();
    SqlParameterSource[] beanSources = new SqlParameterSource[len];
    for(int i=0; i<personIds.size(); i++){
        beanSources[i] = new MapSqlParameterSource("personId", personIds.get(i)); // 自己构造map
    }
    try{
        jdbcTemplate.batchUpdate("DELETE FROM public.person where person_id = :personId", beanSources);
        // 多表连接的删除
        jdbcTemplate.batchUpdate("DELETE FROM public.location where location_id in ( " +
                    "SELECT location_id FROM public.person where person_id in (:personId))", beanSources);
    } catch (DataAccessException e){
        log.error(e.getMessage());
    }
}




// 直接使用List作为参数进行查询
public void deleteBatchHistoryRecord(List<Long> personIds){
    MapSqlParameterSource parameterSource = new MapSqlParameterSource();
    parameterSource.addValue("personId", personIds);
    List<Map<String, Object>> locationIdList = jdbcTemplate.queryForList("SELECT location_id FROM 							public.person where person_id in (:personId)", parameterSource);
}
```











```java
if(this.batchCount>=this.BATCHSIZE){
            cdmBatchRepository.insertBatchLocation(locationList);
            batchCount = 0;
            locationList.clear();
        } else {
            locationList.add(location);
            batchCount++;
        }
```





# Stream 操作

```java
// 不在当前联盟用户列表中的用户不能调用该接口
Integer userId = TokenUtil.getUserId(token);
List<UserLeague> currentLeagueUsersId = userLeagueService.getUsersByLeagueId(leagueId);
if(!currentLeagueUsersId.stream().filter(m->m.getUserId().equals(userId)).findAny().isPresent()){
            return "当前用户不在该联盟中";
        }
```

