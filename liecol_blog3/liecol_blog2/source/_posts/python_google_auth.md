---
title: 通过python进行谷歌hotp身份验证
date: 2020-05-21 18:14:40
tags: [python,chrome]
categories:
- python
---

具体原理如下图（copy自网络）：

{% img "" /images/google_auth.jpg 700 280 liecol-晓斌|tmux liecol-晓斌|tmux %}
<!--more-->
代码如下图：
```python
import hmac
import hashlib
import base64
import struct
import time
import six


def get_hotp(secret_key, intervals_no, as_string=True, casefold=True, digest_method=hashlib.sha1, token_length=6):
    if isinstance(secret_key, six.string_types):
        secret_key = secret_key.encode('utf-8')
    secret_key = secret_key.replace(b' ', b'')
    try:
        key = base64.b32decode(secret_key, casefold=casefold)
    except TypeError:
        raise TypeError('Incorrect secret_key: %s' % secret_key)
    msg = struct.pack('>Q', intervals_no)
    hmac_digest = hmac.new(key, msg, digest_method).digest()
    ob = hmac_digest[19] if six.PY3 else ord(hmac_digest[19])
    o = ob & 15
    token_base = struct.unpack('>I', hmac_digest[o:o + 4])[0] & 0x7fffffff
    token = token_base % (10 ** token_length)
    if as_string:
        return '{{:0{}d}}'.format(token_length).format(token)
    else:
        return token


if __name__ == '__main__':
    while True:
        secret = "liecol_Gao"  # 去除=后转为b64后长度不能低于16
        secret = base64.b32encode(s=secret.encode('utf-8'))
        # print(secret)
        hotp = get_hotp(secret_key=secret, intervals_no=int(time.time()) // 30, )
        print(hotp)
        time.sleep(30)
```

最后结果为:
{% img "" /images/google_auth_ver.jpg 600 280 liecol-晓斌|tmux liecol-晓斌|tmux %}
