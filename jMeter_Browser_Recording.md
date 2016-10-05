jMeter and Browser recording for performance testing
====================================================
This file is having the set up for browser recording using jMeter. Here is below steps

###Steps
- Create a thread group for the project
  ![alt tag](https://raw.githubusercontent.com/cooligc/Installations/master/static/img/jMeterRecordings/threadgroup.png)
- Add the description for the thread group
  ![alt tag](https://raw.githubusercontent.com/cooligc/Installations/master/static/img/jMeterRecordings/ThreadGroupDetails.PNG)
- Create a Recording controller
  ![alt tag](https://raw.githubusercontent.com/cooligc/Installations/master/static/img/jMeterRecordings/RecordingController_Creation.png)
- Add the description for Recording Controller
  ![alt tag](https://raw.githubusercontent.com/cooligc/Installations/master/static/img/jMeterRecordings/RecordingController_Description.png)
- Create a HTTPRequestDefault for load testing 
  ![alt tag](https://raw.githubusercontent.com/cooligc/Installations/master/static/img/jMeterRecordings/Recording_Http_Request.png)
- Configuration for HTTPRequestDefault
  ![alt tag](https://raw.githubusercontent.com/cooligc/Installations/master/static/img/jMeterRecordings/Recording_Http_Request_Config.png)
- Create a proxy server 
  ![alt tag](https://raw.githubusercontent.com/cooligc/Installations/master/static/img/jMeterRecordings/Proxy_Server_Creation.png)
- Add the configuration for proxy server
  ![alt tag](https://raw.githubusercontent.com/cooligc/Installations/master/static/img/jMeterRecordings/Proxy_Server_Config.png)
- Add the proxy details in mozzila browser
  ![alt tag](https://raw.githubusercontent.com/cooligc/Installations/master/static/img/jMeterRecordings/Proxy_Server_Config_Mozzila.png)
- Now try to browse the pages, and you can see all the links are been recorded by jMeter Recording Controller. Then after you can configure some listener and configure the no of thread, ramp up time etc. Now, run the jMeter for load testing and get the reports
