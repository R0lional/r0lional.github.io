#Android Bluedroid的开启流程总结

##名词理解
- 整个Bluedroid可以分为两大模块：BTIF,BTE
	- BTIF:提供bluedroid对外的接口
	- BTE：bluedroid的内部处理，又细分为BTA，BTU，BTM和HCI
		- BTA: bluedroid中各profile的逻辑实现和处理, 如蓝牙设备管理、状态管理及一些应用规范
		- BTU：承接BTA与HCI
		- BTM：蓝牙配对与链路管理
		- HCI：读取或写入数据到蓝牙hw

##初始化 

![bluedroid init](http://i.imgur.com/MlXviQ9.png)  
 
BlueDroid与JNI层的BTIF适配初始化，主要是sBluetoothCallBacks和sBluetoothInterface，前者为从Bluedroid到java层时的回调调用，后者为BlueDroid对外提供的接口初始化，其实际指向为bluetooth.c中的bluetoothInterface，包含了inti、enable、search等。

##开启过程  

![bluedroid enable](http://i.imgur.com/C3LsbRx.png)  

主要实现的功能：  
1、开启BTSNOOP MODULE和HCI MODULE  
2、创建bt-workqueue-thread线程，可以理解为BTU task  
3、将在初始化时注册的btu hci msg queue队列和现在注册的btu hci msg queue消息队列加入到线程bt-workqueue-thread中，btu task承接BTA和HCI  
4、初始化并启动BTM，和蓝牙协议的初始化  
5、当蓝牙协议栈初始化完成后通过init过程中注册的bt hal callback通知到java层, 并通知相关队列去处理所有需要连接的请求

##关于BTA, BTU, BTM, BTE, HCI的关系图  
此图应该是比较早的了，不过可以借鉴着看一看，捋一捋它们之间的关系  

![bte and btif](http://i.imgur.com/1pAcU9L.png)
