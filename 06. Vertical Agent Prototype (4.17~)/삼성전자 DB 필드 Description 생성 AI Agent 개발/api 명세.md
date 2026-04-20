# 개요

"description 생성 요청"은 특정 시스템 DB, Table의 컬럼 목록에 대한 description을 생성 요청 하는 것이다.

# life cycle

## state transition diagram

## State 목록

|Creating|요청 정보 생성 중| |
|Created|요청 정보 생성됨| |
|Producing|kafka message producing| |
|End|kafka 전송 완료| |
|Fail|실패| |

  

# Interface

## request body

|irp_code|irp code||
|bdc_code|bdc code||
|agent_type|agent type||
|db_name|db name||
|table_name|table name||

[](https://confluence.score/display/DataAITF/%5BDXFD%5D+description+generate+API)