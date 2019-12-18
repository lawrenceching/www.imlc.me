# dh key too small

Today I encoutered the `dh key too small` issue when running curl and wget commands.

And most of the reasons is that server is passing a weak DH key to client. This is the most pages say in the Internet. However today I found the issue only happens on Kali Linux. This should be the hint that there is some wrong in client side instead of server side.

I checked `/etc/ssl/openssl.cnf`. No surprise there is an extra configuration for SSL security level:

```
[system_default_sect]
MinProtocol = TLSv1.2
CipherString = DEFAULT@SECLEVEL=2
```

The default security level is 1 for OpenSSL. So setting it up to 2, some of the websites may not meet level 2 security requirement and caused the "dh key to small" issue.

Recorded in this page, https://www.openssl.org/docs/man1.1.0/man3/SSL_CTX_get_security_level.html:

> DSA and DH keys shorter than 2048 bits and ECC keys shorter than 224 bits are prohibited.

## Workaround

1. You can turn down the security level in `/etc/ssl/openssl.cnf`
2. You can add `--cipher 'DEFAULT:!DH'` as command line parameter

