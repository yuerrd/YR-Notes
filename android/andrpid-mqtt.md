##  MQTT  SDK

#### 重试机制

```
重试连接次数 ：10
最小连接延迟时长：4秒
最大连接延迟时长：64秒
连接稳定时间： 10秒
```

| 重试次数 | 延迟时长 |
| -------- | -------- |
| 1        | 4秒      |
| 2        | 8秒      |
| 3        | 16秒     |
| 4        | 32秒     |
| 5        | 64秒     |
| 6        | 64秒     |
| 7        | 64秒     |
| 8        | 64秒     |
| 9        | 64秒     |
| 10       | 64秒     |

```flow
st=>start: 开始连接
condConnected1=>condition: 已连接
condConnected3=>condition: 已连接
opReconCf1=>operation: 重置连接参数
opReconCf2=>operation: 重置连接参数
opCon=>operation: 连接
condConnecSuc=>condition: 连接成功
opReCon1=>operation: 重试连接
opReCon2=>operation: 重试连接
ioConnectSuc=>inputoutput: 已连接
ioConnect=>inputoutput: 已连接
ioConnect2=>inputoutput: 已连接
ioConnectLost=>inputoutput: 连接丢失
condConnectionStabilityTime=>condition: 连接稳定时间<10/s
e=>end: 关闭连接
st->condConnected1
condConnected1(yes)->ioConnect
condConnected1(no)->opReconCf1->opCon->condConnecSuc
condConnecSuc(yes)->ioConnectSuc
condConnecSuc(no)->opReCon2->condConnected3
ioConnect->ioConnectLost->condConnectionStabilityTime
condConnectionStabilityTime(yes)->opReconCf2->opReCon2
condConnectionStabilityTime(no)->opReCon2
opReCon2->condConnected3
condConnected3(yes)->ioConnect2
condConnected3(no)->e
```