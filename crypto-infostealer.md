# Malware Analysis: Taking a peak inside an Infostealer Campaign Targeting Crytpo Users

### Context

When analysing various fund loss cases, I'll ocaisionally stumble on malware targeting users SRPs. This is a simple breakdown of how you can analyze malware targeting crypto users and extract the proper IOCs from it. 



### Summary![alt text](<image 1.png>)

The user downloaded an Infostealer that functioned as a fake MetaMask extension. The fake MetaMask extension send’s the password and private keys that a user creates or imports to `lep.thxs.online` and `server.thxs.in`. In addition to this the infostealer came with functionality including, Accessing the devices clipboard, Monitoring network indicators, TLS Cert manipulation, and Accessibility Service Access

### Technical Analysis

When detonating the payload in a [virus total sandbox](https://www.virustotal.com/gui/file/82b5190dbff8383a5917ebd4f1f69b35a649bd7f0ae40ac2c17c5e1a4e899c0c/behavior) we learned of all the invasive procedures in addition to the UI that the user was provided to harvest their SRP. 

User Interface Analysis 

Below is a screenshot showing the original UI translated to english. This tricked a user into sending their SRP and password to the threat actors 

![image-7.png]

Android Method Call Analysis 

### **1. Clipboard Access**

The malicious APK accessed the devices clipboard manager

```
1android.content.ContextWrappergetSystemService#binder(#3686) com.example.metamask
2Arguments:
3["clipboard"]
4Returned value:
5android.content.ClipboardManager@57c919f

```

---

### **2. Supicious URL Loaded**

This loads the phishing page and potentially acts as the C2 server for where all the critical information about the user is sent including the SRPs. 

```
1android.webkit.WebViewloadUrl#network(#3686) com.example.metamask
2Arguments:
3["https://lep.thxs.online/"]

```

---

### **3. Monitoring Proxy Changes**

Indicates some form of network manipulation. 

```
1android.content.ContextWrapperregisterReceiver#binder(#3686) com.example.metamask
2Arguments:
3["HJ@f486778","[android.intent.action.PROXY_CHANGE]"]

```

---

### **4. Monitoring Network Connectivity**

Monitors when the network is changed

```
1android.content.ContextWrapperregisterReceiver#binder(#3686) com.example.metamask
2Arguments:
3["GJ@2588324","[android.net.conn.CONNECTIVITY_CHANGE]"]
4Returned value:
5{"action":"android.net.conn.CONNECTIVITY_CHANGE","extras":"{"networkInfo":"[type: WIFI[], state: CONNECTED/CONNECTED, reason: (unspecified), extra: "VirtWifi", failover: false, available: true, roaming: false]", "networkType":"1", "inetCondition":"0", "extraInfo":""VirtWifi""}"}

```

---

### **5. Monitoring Wi-Fi Signal Strength**

```
1android.content.ContextWrapperregisterReceiver#binder(#3686) com.example.metamask
2Arguments:
3["","[android.net.wifi.RSSI_CHANGED]"]
4Returned value:
5{"action":"android.net.wifi.RSSI_CHANGED","extras":"{"newRssi":"-50"}"}

```

---

### **6. Accessing Telephony Services**

Telephony services contain critical information about the user and the user’s device (Phone provider, number etc) 

```
1android.content.ContextWrappergetSystemService#binder(#3686) com.example.metamask
2Arguments:
3["phone"]
4Returned value:
5android.telephony.TelephonyManager@db7e68e

```

---

### **7. Accessibility Service Access**

Could be used for screen monitoring or overlaying incorrect information to the user 

```
1android.content.ContextWrappergetSystemService#binder(#3686) com.example.metamask
2Arguments:
3["accessibility"]
4Returned value:
5android.view.accessibility.AccessibilityManager@64fdd9a

```

---

### **8. Reflection Usage (SSL/TLS Manipulation)**

Certificate checking - Indicates the attacker checking for if their certificate was classified as malicious. 

```
1java.lang.ClassgetMethod#reflect(#3686) com.example.metamask
2Arguments:
3["checkServerTrusted","class [Ljava.security.cert.X509Certificate;,class java.lang.String,class java.lang.String","true"]

```

---

### **9. Monitoring USB Events**

```
1android.content.ContextWrapperregisterReceiver#binder(#3686) com.example.metamask
2Arguments:
3["BF@febc26","[android.hardware.usb.action.USB_DEVICE_ATTACHED, android.hardware.usb.action.USB_DEVICE_DETACHED]"]

```

---

### **10. Monitoring Locale Changes**

```
1android.content.ContextWrapperregisterReceiver#binder(#3686) com.example.metamask
2Arguments:
3["Gy@a71462f","[android.intent.action.LOCALE_CHANGED]"]

```

---

### **11. Monitoring Timezone Changes**

```
1android.content.ContextWrapperregisterReceiver#binder(#3686) com.example.metamask
2Arguments:
3["sF@8561b9","[android.intent.action.TIMEZONE_CHANGED]"]

```

---

### **12. Attempting to Modify Audio Settings**

```
1android.app.ContextImplcheckPermission#binder(#3686) com.example.metamask
2Arguments:
3["android.permission.MODIFY_AUDIO_SETTINGS","3686","10073"]
4Returned value:
5-1

```

---

### **13. Attempting to Access Bluetooth**

```
1android.app.ContextImplcheckPermission#binder(#3686) com.example.metamask
2Arguments:
3["android.permission.BLUETOOTH","3686","10073"]
4Returned value:
5-1

```

### Conclusions

The user downloaded an info stealer that posed as the Official MetaMask mobile app. The user lost funds after entering their SRP into the fake app. The Info stealer also gained system level privileges such as clipboard access which could be used to steal more data from the user. 

### IOCS

```json
https://lep.thxs.online/
https://lep.thxs.online//menu
https://lep.thxs.online//menu%https://lep.thxs.online/accept_bridge
https://lep.thxs.online/accept_bridge
https://lep.thxs.online/bridge
```

### References

1. [Virus total report](https://www.virustotal.com/gui/file/82b5190dbff8383a5917ebd4f1f69b35a649bd7f0ae40ac2c17c5e1a4e899c0c/behavior)