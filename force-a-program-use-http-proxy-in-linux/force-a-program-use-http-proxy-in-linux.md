# How to force a program use HTTP proxy in Linux

Download the latest release: https://github.com/rofl0r/proxychains-ng/releases

Unzip and cd into the directory

./configure && make

Optional: sudo make install && sudo make install-config

nano /usr/local/etc/proxychains.conf

At the end of the config file, set the SOCKS IP port address

Example:
```
proxychains4 -q curl icanhazip.com
```

## References
https://github.com/rofl0r/proxychains-ng
https://unix.stackexchange.com/a/411558
