# Openwrt

[Setting up a soft-router with Raspberry Pi + OpenWRT](#head1)

[The web page development of OpenWRT](#head2)

# <Span id = "head1">1. Setting up a soft-router with Raspberry Pi + OpenWRT:wink:

## Steps::bulb:
1. Install OpenWRT into Raspeberry Pi:white_check_mark:
   1. Find the certain OpenWRT version from the official website: https://firmware-selector.openwrt.org/
      1. Or use firmware that has been compiled by others, here I used the one from SuLingGG https://github.com/SuLingGG/OpenWrt-Rpi and we could find the specific version of Raspeberry：https://doc.openwrt.cc/2-OpenWrt-Rpi/
   2. Download the image file (with “FACTORY”)
   3. Install the burning software on your computer：balenaEtcher
   4. Install the SD Card Formatting Software：SD card Formatter，https://www.sdcard.org/downloads/formatter/
   5. Insert the SD card into your computer and burn the .img file into the SD card using balenaEtcher (if the card has contents, you will need to format it first using SD card Formatter)

2. Insert the SD card into the Raspberry Pi and link the Raspberry Pi to the laptop with a network cable, turn on the Raspberrty Pi and disconnect any other Internet connection of the laptop:white_check_mark:
3. In the laptop, change the ***Ethernet Properties*** (aim to avoid the confliction):white_check_mark:

   ![img1](https://user-images.githubusercontent.com/58734009/187032137-9dd291a7-f90f-4391-8275-e965c95bd0fc.png)
   
   1. p.s. The default gateway should be the default IP address of the Raspberry Pi openwrt：192.168.1.1

4. Enter 192.168.1.1 in your browser to access the OpenWRT backend:white_check_mark:
   1. If you are using the official version, you can log in directly without a password the first time.
   2. If using the SuLingGG version, the default password is "password"
5. Set the system password,system--> administration--> router password（what I set is 123456）:white_check_mark:
6. Turn on the Raspberry Pi's own wifi:white_check_mark:
   1. Network--> Wireless--> select SSID as OpenWrt--> Click Enable--> Save
   
      ![img2](https://user-images.githubusercontent.com/58734009/187032145-192b54c7-fdec-4138-ba9b-bd0bff473ff6.png)
   
   2. Click Edit，in Operating frequency, change the "Mode" as "Legacy"，change the "channel" as "auto"，Click Save
   
      ![img3](https://user-images.githubusercontent.com/58734009/187032154-8c8e32c2-7e31-4350-8d87-b22025bfab86.png)
      
7. Change LAN port configuration:white_check_mark:

   1. In "Interface", choose the "edit" button of LAN
   
      ![img4](https://user-images.githubusercontent.com/58734009/187032164-df46a3b1-05f0-4ed6-94f6-57f54f15cde7.png)
   
   2. Change Ipv4 address and gateway
   
      * Change IPv4 address to：192.168.3.10
      * Change IPv4 gateway to：192.168.3.1
      * In "Advanced setting", change custom DNS to 114.114.114.114
      
      Notice that：The gateway is the IPv4 of my main router, to avoid conflicts, the IPv4 of OpenWRT is changed to a different address under the same network segment (2-254 is fine), we suggest to check it in the computer terminal, cmd --> type ipconfig
      
         ![img6](https://user-images.githubusercontent.com/58734009/187032179-56f4a1a6-47e8-487c-b7de-7605f29e5729.png)
         
         ![img7](https://user-images.githubusercontent.com/58734009/187032184-721abc0f-78a6-4b66-b270-d2e7e7c6f9f9.png)
      
   ***In order to have more functions, the following screenshots are taken with the firmware downloaded and used from SuLingGG's GitHub***
      
      
   3. After saving and applying, it will remain in the loading state because at this stage, the openwrt access is no longer 192.168.1.1 (shown below), we need to unplug the network cable from the computer and plug it into one of the LAN ports of the router (main router)
      
      * Notice that: Using the official version, you have to unplug the cable and plug in the LAN port within 90s, otherwise it will start a rollback.
      
      ![img5](https://user-images.githubusercontent.com/58734009/187032175-f75a7f18-e3d4-4f6e-8af0-9bdf60c2fde7.png)
      
8. Re-login to Openwrt:white_check_mark:

   Enter the new address in your browser：192.168.3.10，enter the password: “password”, then you can login again
   
      ![img8](https://user-images.githubusercontent.com/58734009/187032187-67b40d5c-836c-45a3-938a-cf86303437ee.png)
      
9. Any mobile phone or other device connected to the wifi with SSID "Openwrt" can successfully connect and access the internet at this time. (The main router I used is HUAWEI-CA1P7S):white_check_mark:

   ![img9](https://user-images.githubusercontent.com/58734009/187032191-0bb6ac83-2237-4271-90fe-226a4ca44093.png)![img10](https://user-images.githubusercontent.com/58734009/187032194-2e9df5ba-ef46-4aaf-b67d-6d075b918700.png)

   From the figure above, we could find the IP address of the main router is still 192.168.3.1
   
10. Set the password for the soft-router "OpenWrt" in Network --> Wireless --> Wireless Security, what I set is 15042237015:white_check_mark:

   ![img11](https://user-images.githubusercontent.com/58734009/187032202-8df57820-8a98-4f29-9a67-5815053a943c.png)
   
11. Some reference:white_check_mark:
   1. The main video：https://www.v2ex.com/t/835585 Or https://www.youtube.com/watch?v=iyQjjgOfPnQ
   2. https://www.youtube.com/watch?v=w7rwNF2Q3lM
   3. https://zhuanlan.zhihu.com/p/509064156


# <Span id = "head2">2. The web page development of OpenWRT</span>

## 1. In order to access the file in OpenWRT remotely, we first need to remote access to the Raspberry Pi
   1. In your browser, enter 192.168.3.10 to access the OpenWRT, find the "Gateway Port" in System -> Administrative Rights and make sure it is open
   
   ![image](https://user-images.githubusercontent.com/58734009/187030054-378676b0-b0d1-4ef8-89af-13f82e0cc002.png)
   
   Otherwise, the subsequent steps will result in an error: "Unable to connect because target computer actively rejects + connection failed" etc.

   2. Download and install ***WinSCP*** for transferring and modifying files between Windows and the Raspberry Pi directly
   
      * Click on New Session and select FTP for the protocol
      
      ![image](https://user-images.githubusercontent.com/58734009/187031532-33b22c0a-da3c-4724-adfa-675c2b341aac.png)

      * Port switch to 22, hostname is the IP of the OpenWRT development board：for us it is 192.168.3.10, the username and password are what we used to login to OpenWRT website in browser：they are "root" and "password" respectively
      
      * Click on login and the result is as shown
      
      ![image](https://user-images.githubusercontent.com/58734009/187030255-7f1c10c4-9530-482a-a792-272ff2158973.png)
      
      The Windows directory is on the left and the OpenWRT directory is on the right, the files can be dragged and dropped directly

## 2. Create a new interface (try to add a "HelloWorld" tab under System navigation in the menu bar) 

   * Use WinSCP to login to the Raspberry, in /usr/lib/lua/luci/controller/admin, open the system.lua file and start edition, we need to add one more code：
   
      ```
      entry({"admin", "system", "Helloworld"}, template("admin_system/helloworld"), _("Helloworld"), 99)
      ```

   ![image](https://user-images.githubusercontent.com/58734009/187031900-c6afabde-991f-4da4-a167-64e4daf84b33.png)

   * In /usr/lib/lua/luci/view/admin_system, create a new file named:helloworld.htm, and add one more line：
   
   
      ```
      <%+header%> <h1><%: HelloWorld %></h1> <%+footer%>
      ```
   
   * Save it and re-start the Raspberry, re-login to the OPenWRT website, you can see that：
   
   ![image](https://user-images.githubusercontent.com/58734009/187032098-562add46-2a12-477f-af45-dfc0bac402c1.png)
   
   ![image](https://user-images.githubusercontent.com/58734009/187032104-38147383-e373-4622-b858-bf6f4fa9212e.png)


