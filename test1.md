# 换电站对接方案

##### 简介

![image](https://user-images.githubusercontent.com/12853609/113239539-81521500-92dd-11eb-8d7e-6157215c3588.png)

换电站与数据中心对接，分为同步通信与异步通信两方面内容

- 同步通信采用http方式，由数据中心提供接口，换电站实时访问
- 异步通信采用mqtt通讯，实现换电站与数据中心双向通信

##### 认证

- 同步通信认证时，数据中心为每个换电站提供accessToken，换电站首先通过accessToken获取api访问token（7天），再进行api请求
- 异步通信认证时，数据中心为每个换电站提供用户名、密码及需要订阅的topic，换电站通过用户名密码登录，并订阅相关topic后，与数据中心进行消息收发

##### 具体实现

- 同步通信涉及接口包括用户信息(https://api.catl.com/core/v0/users/userId)、车辆信息(https://api.catl.com/core/v0/vehicles/vehicleId)及订单管理(https://api.catl.com/core/v0/order/orderId)，具体接口内容及线上环境在4月8日前提供

- 异步通信默认使用json格式进行交互，传输前需先将数据序列化（转为字符串），具体内容在4月8日前提供，线上环境在4月16日前提供

  ```
  // 默认消息格式
  {
    source: "1231", // 消息源设备ID，为换电站ID或数据中心ID（默认为0）
    desc: "0", // 消息目的设备ID，为换电站ID或数据中心ID（默认为0）
    type: MSG_TYPE.SERVER_ASK_INFO, // 消息推送类型
    time : "2016-2-1" // 消息推送时间
    data: {}, // 消息详情，可为空
  }
  
  // 消息类型
  MSG_TYPE = {
  	STATION_INFO: 0,
  	SERVER_ASK_CHARGE: 1, // 服务端请求换电站开闸换电
  	SERVER_ASK_INFO: 2, // 服务端请求换电站信息
  	SERVER_ASK_RESTART: 3, // 服务端请求换电站重置
  	SERVER_ASK_DISCONNECT: 4, // 服务端请求换电站离线
  }
  
  // 以下为各消息模板
  // MSG_TYPE.STATION_INFO
  {
    source: "1231",
    desc: "0",
    type: MSG_TYPE.STATION_INFO,
    data : {
    	// 电池包数据
    	battery: {
    		bluetooth: true,
          charge: true,
          maxVol
    	}
    },
    time : "2016-2-1"
  }
  
  // MSG_TYPE.SERVER_ASK_CHARGE
  {
    source: "0",
    desc: "1231",
    type: MSG_TYPE.SERVER_ASK_CHARGE,
    data : {
    	user: “1234”， // 用户编号
    	vehicle: "1234" // 车辆编号
    },
    time : "2016-2-1"
  }
  
  // MSG_TYPE.SERVER_ASK_INFO SERVER_ASK_RESTART SERVER_ASK_DISCONNECT
  {
    source: "0",
    desc: "1231",
    type: MSG_TYPE.SERVER_ASK_INFO, // 推送数据类型
    time : "2016-2-1" // 推送时间
  }
  ```
