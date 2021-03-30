# 换电站项目技术方案

#### 简介

![image](https://user-images.githubusercontent.com/12853609/112920224-1d8fe680-913b-11eb-80ab-f904db76cb5a.png)

项目的架构包含4个系统：

* 换电站

* app端，包括android和ios

* 管理中台

* API数据服务

#### 功能介绍

APP，初代版本基于React Native开发，后续版本基于原生开发，共包含以下几个模块

- 换电站模块
- 换电模块
- 个人模块

管理中台，基于React技术栈开发，共包含以下几个模块

- 换电站模块
- 电池资产模块
- 用户模块
- 车辆模块
- 订单模块
- 财务模块
- 套餐模块
- 数据分析模块
- 系统管理模块

API数据服务，基于KOA2 的微服务开发，双向通信采用mqtt实现

- API服务，参考管理中台内容，提供各模块数据功能
- Stargate，提供认证、鉴权、用户管理等功能
- 异步任务，方便实现异步调用，如订单、支付等
- 日志系统，方便定位问题、查找异常
- API网关，提供安全、流控、过滤、缓存、计费、监控等功能
- mongo集群，提供存储功能

#### 技术细节

- 用户端向服务端请求数据：基于jwt使用restful风格的api实现基本信息交互，如  https://api.catl.com/core/v0/batteries?_limit=10

- app推送：接入成熟的推送平台，实现对用户移动设备进行的主动消息推送

- 换电站向服务端请求数据：同`用户端向服务端请求数据`，主要包含以下内容

  - 获取用户信息  https://api.catl.com/core/v0/users/userId（https://api.stargate.com/auth/v1/users/userId）
  - 获取车辆信息  https://api.catl.com/core/v0/vehicles/vehicleId
  - 创建订单 https://api.catl.com/core/v0/order
  - 完成订单 https://api.catl.com/core/v0/order/orderId

- 换电站与服务端双向通信：为实现运营方主动对换电站通信，并保证换电站及时同步站内信息，引入mqtt实现双向通信

  - 换电站作为mqtt clilent，使用用户名密码登录，推送json格式的消息

    ```
    // TOPIC类型
    TOPIC = {
    	INFO: "info", // 信息发送，用于换电站同步站内信息，仅服务端订阅
    	CONTROL: "control" // 服务端推送，用于服务端主动推送信息，所有client都需要订阅
    }
    
    // 推送数据类型
    MSG_TYPE = {
    	STATION_INFO: 0,
    	SERVER_ASK_CHARGE: 1, // 服务端请求换电站开闸换电
    	SERVER_ASK_INFO: 2, // 服务端请求换电站信息
    	SERVER_ASK_RESTART: 3, // 服务端请求换电站重置
    	SERVER_ASK_DISCONNECT: 4, // 服务端请求换电站离线
    }
    
    // MSG_TYPE.STATION_INFO
    {
      device: "1231", // 换电站编号
      type: MSG_TYPE.STATION_INFO, // 推送数据类型
      data : {
      	// 电池包数据
      	battery: {
      		bluetooth: true,
            charge: true,
            maxVol
      	}
      },
      time : "2016-2-1" // 推送时间
    }
    
    // MSG_TYPE.SERVER_ASK_CHARGE
    {
      device: "1231", // 需要响应的换电站编号
      type: MSG_TYPE.SERVER_ASK_CHARGE, // 推送数据类型
      data : {
      	user: “1234”， // 用户编号
      	vehicle: "1234" // 车辆编号
      },
      time : "2016-2-1" // 推送时间
    }
    
    // MSG_TYPE.SERVER_ASK_INFO SERVER_ASK_RESTART SERVER_ASK_DISCONNECT
    {
      device: "1231", // 需要响应的换电站编号
      type: MSG_TYPE.SERVER_ASK_INFO, // 推送数据类型
      time : "2016-2-1" // 推送时间
    }
    ```

#### 附件
[换电站提供的数据.xlsx](https://github.com/cc3630/github-test/files/6225551/default.xlsx)
[换电项目功能规划表_2021-3-12.xlsx](https://github.com/cc3630/github-test/files/6225552/_2021-3-12.xlsx)
