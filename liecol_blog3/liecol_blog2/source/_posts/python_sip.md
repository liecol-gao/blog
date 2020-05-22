---
title: Requests对摘要认证的处理
date: 2017-12-21 18:25:50
tags: [python,http]
categories:
- python
---

许多 web 服务都需要身份认证，并且也有多种不同的认证类型。
下面笔者着重说一下摘要认证以及python包Requests对摘要认证的处理。
首先，Requests对摘要认证其实是开箱即用的。
如下：
```python
from requests.auth import HTTPDigestAuth
url = 'http://digest.cereson.cn/auth/index.php'
requests.get(url, auth=HTTPDigestAuth('user', 'passwd'))
```
接下来说一下什么是摘要认证：
首先摘要认证被设计用来弥补基本认证的缺点。
摘要认证基于请求-响应（challenge-response）模式，
而且使用了哈希加密算法（常用为MD5），从而某些程度上解决了基本认证安全性的问题。
服务器返回的初始401响应的www认证头（WWW-Authenticate header）中多出了一个称为nonce的随机数的字段。
服务端保证每个401响应中的nonce值唯一。如：
`Authorization: Digest username="admin", realm="HiPER"`

接下来的客户端响应中将包含由用户名、密码、nonce和其他信息组成的数据的哈希值（如使用MD5加密）。
所有被加密的数据服务端也具有，因此服务端执行同样加密过程。如果二者一致则认证成功。
因为如MD5等哈希加密算法是不可逆的，因此用户名密码明文无法被窃听破解。
因为服务器对同一个nonce的请求只接受一次客户端请求，从而能避免重放攻击。
但是，digest的安全性也有缺点：
  * 只有密码密码被加密，而客户端最终请求的被保护资源是明文传送的，可被窃听
  * 客户端无法确认服务端的正确身份，缺少对服务端的认证方式
  * 近年来，随着计算机性能的提高等因素，传统高强度加密算法的破解已成可能。而MD5更是已有破解方法。
<!--more-->

##### 通俗点说：#####
客户端首次向服务器发送HTTP请求，服务器返回401（未授权）响应进行挑战。
401消息的头里带有WWW-Authenticate消息头，其中包含挑战摘要的随机参数nonce。
客户端收到401后，将用户名密码和挑战信息用MD5加密形成认证鉴权头，重新发送给服务器，服务器对认证鉴权头进行验证，
如果认证成功则返回200 OK并返回操作的Cookie,在后续的访问中客户端只需要使用该分配的Cookie进行服务操作即可。

服务器的401未授权挑战WWW-Authenticate消息头语法：
```python
  * challenge        = "Digest" digest-challenge
  * digest-challenge = 1#( realm | [ domain ] | nonce | [opaque] |[stale] | [algorithm] | [qop-options] | [auth-param] )
  * domain            = "domain" "=" <"> URI ( 1*SP URI ) <">
  * URI               = absoluteURI | abs_path
  * nonce             = "nonce" "=" nonce-value
  * nonce-value       = quoted-string
  * opaque            = "opaque" "=" quoted-string
  * stale             = "stale" "=" ( "true" | "false" )
  * algorithm         = "algorithm" "=" ( "MD5" | "MD5-sess" | token )
  * qop-options       = "qop" "=" <"> 1#qop-value <">
  * qop-value         = "auth" | "auth-int" | token
```

##### 备注：#####
- realm：让客户知道使用哪个用户名和密码的字符串。不同的领域可能密码不一样。至少告诉用户是什么主机做认证，他可能会提示用哪个用户名登录，类似一个Email。
- domain：一个URI列表，指示要保护的域。可能是一个列表。提示用户这些URI采用一样的认证。如果为空或忽略则为整个服务器。
- nonce：随机字符串，每次401都不一样。跟算法有关。算法类似Base64加密：time-stamp H(time-stamp ":" ETag ":" private-key) 。time-stamp为服务器时钟，ETag为请求的Etag头。private-key为服务器知道的一个值。
- opaque：服务器产生的由客户下去请求时原样返回。最好是Base64串或十六进制字符串。
- auth-param：为扩展用的，现阶段忽略。

摘要的计算方法：
客户端的挑战响应消息头语法：
```python
credentials   =“Digest" digest-response
digest-response=1#( username | realm | nonce | digest-uri
               | response | [ algorithm ] | [cnonce] |
               [opaque] | [message-qop] |
                   [nonce-count]  | [auth-param] )
username      ="username" "=" username-value
username-value =quoted-string
digest-uri    =“uri" "=" digest-uri-value
digest-uri-value=request-uri   ; As specified by HTTP/1.1
message-qop   =“qop" "=" qop-value
cnonce        =“cnonce" "=" cnonce-value
cnonce-value   =nonce-value
nonce-count   =“nc" "=" nc-value
nc-value       =8LHEX
response      =“response" "=" request-digest
request-digest=<"> 32LHEX <">
LHEX           ="0" | "1" | "2" | "3" |
                   "4" | "5" | "6" | "7" |
                   "8" | "9" | "a" | "b" |
                   "c" | "d" | "e" | "f"
```



| 参数名称   |       说明           |    必填      |     数据类型     |     备注
| --------   | :--------            | :--------:   |:--------:        |    :--: |
| username   |      用户名          |       Y      |    xsd:string    | 客户端ID或者VcuId
| realm      |      用户域          |       Y      |    xsd:string    | 告知用户使用哪个域的用户名密码登录。这里如“bj.hyc.cn”
| nonce      |      摘要质询参数    |       Y      |    xsd:string    | 返回请求一响应中的参数nonce
| uri        |      访问路径        |       Y      |    xsd:string    | 请求的URI
| qop        |      保护质量        |       Y      |    xsd:string    | auth：鉴权，不对消息体做完整性验证。
|            |                      |              |                  | auth-int：鉴权并需要对消息体做摘要，保证消息完整性。
|            |                      |              |                  | 注册过程使用auth。
| nc         |      nonce计数参数   |       Y      |    xsd:string    | 客户端请求的十六进制计数，以00000001开始，每次请求加1，目的是防止重放攻击。
| cnonce     |      客户端nonce值   |       Y      |    xsd:string    | 客户端用来鉴定服务器的摘要质询参数，本版本暂不实现客户端对服务器的认证
| response   |      响应值          |       Y      |    xsd:string    | 对请求一中401响应的参数采用MD5算法做摘要计算的结果
| opaque     |      会话标识        |       Y      |    xsd:string    | 返回服务器原值

response参数计算算法：
```python
response=<"> < KD ( H(A1), unq(nonce-value)
":" nc-value
":" unq(cnonce-value)
":" unq(qop-value)
":" H(A2)
) <">
A1=unq(username-value) ":" unq(realm-value) ":" passwd
如果qop等于auth，A2=Method ":" digest-uri-value
如果qop等于auth-int，A2=Method ":" digest-uri-value ":" H(entity-body)
其中：
1)  H(data)=MD5(data)。
2)  KD(secret, data)=H(concat(secret, ":", data))。
3)  unq(X)代表去掉X前后的引号。
4)  Method=GET或者POST。
5)  entity-body代表HTTP请求的消息体。
6)  passwd=key。
```
服务器在收到客户端的挑战响应消息后，根据Authorization消息头中的username参数，
取出对应的key和key的有效期，然后使用和客户端相同的计算方法，
对Authorization消息头中的参数进行摘要计算，将计算结果与response值进行比较，
相同则鉴权成功，返回200 OK响应，不同则鉴权失败，重新返回401 Unauthorized响应，格式与请求一的401响应相同。

rspauth参数计算算法：
```python
rspauth=<"> < KD(H(A1),unq(nextnonce-value)
":"nc-value
":"unq(cnonce-value)
":"unq(qop-value)
":"H(A2)
)<">
A1=unq(username-value)":"unq(realm-value)":"passwd
如果qop等于auth，A2=":" digest-uri-value
如果qop等于auth-int，A2=":" digest-uri-value ":" H(entity-body)
其中：
1) H(data)=MD5(data)。
2) KD(secret, data)=H(concat(secret, ":", data))。
3) unq(X)代表去掉X前后的引号。
4) entity-body代表HTTP请求的消息体。
5) passwd=key。  
```
##### 示例：#####
首次接入进行挑战过程，假设操作为登陆接(实际可能不存在该接口，此处只是举例说明)，用户名为abc@123.com 密码为111111.

请求：
```python
GET /login HTTP/1.1
Host: 123.com
```

应答401挑战：
```python
HTTP/1.1 401 Unauthorized 
WWW-Authenticate:Digest realm="123.com",nonce="30373034323033393432343638323539",qop=auth
```

认证请求：
```python
GET /login HTTP/1.1
Host: 123.com
Authorization:Digest realm="123.com",nonce="30373034323033393432343638323539",username="abc@123.com",qop=auth,uri="/banklogin",response="7a086bb798e81d0c815fc3fc32238bc6",cnonce="b1ce32121cb97d8dde121f3e8421288e",nc=00000001, algorithm="MD5"
Date: Sun,4 Jul 2010 12:39:43 GMT
```

成功响应：
```python
HTTP/1.0 200 OK 
Server: webserver
ContentType:application/json;charset=utf8
Set-Cookie: JSESSIONID=07205746E416581AADD6BBAFB053804C
```

只需用在后续的请求中携带Cookie即可。

笔者在这也翻了一下Requests对于摘要认证的处理,方便大家参考：
```python
class HTTPDigestAuth(AuthBase):
    """Attaches HTTP Digest Authentication to the given Request object."""

    def __init__(self, username, password):
        self.username = username
        self.password = password
        # Keep state in per-thread local storage
        self._thread_local = threading.local()

    def init_per_thread_state(self):
        # Ensure state is initialized just once per-thread
        if not hasattr(self._thread_local, 'init'):
            self._thread_local.init = True
            self._thread_local.last_nonce = ''
            self._thread_local.nonce_count = 0
            self._thread_local.chal = {}
            self._thread_local.pos = None
            self._thread_local.num_401_calls = None

    def build_digest_header(self, method, url):
        """
        :rtype: str
        """

        realm = self._thread_local.chal['realm']
        nonce = self._thread_local.chal['nonce']
        qop = self._thread_local.chal.get('qop')
        algorithm = self._thread_local.chal.get('algorithm')
        opaque = self._thread_local.chal.get('opaque')
        hash_utf8 = None

        if algorithm is None:
            _algorithm = 'MD5'
        else:
            _algorithm = algorithm.upper()
        # lambdas assume digest modules are imported at the top level
        if _algorithm == 'MD5' or _algorithm == 'MD5-SESS':
            def md5_utf8(x):
                if isinstance(x, str):
                    x = x.encode('utf-8')
                return hashlib.md5(x).hexdigest()
            hash_utf8 = md5_utf8
        elif _algorithm == 'SHA':
            def sha_utf8(x):
                if isinstance(x, str):
                    x = x.encode('utf-8')
                return hashlib.sha1(x).hexdigest()
            hash_utf8 = sha_utf8

        KD = lambda s, d: hash_utf8("%s:%s" % (s, d))

        if hash_utf8 is None:
            return None

        # XXX not implemented yet
        entdig = None
        p_parsed = urlparse(url)
        #: path is request-uri defined in RFC 2616 which should not be empty
        path = p_parsed.path or "/"
        if p_parsed.query:
            path += '?' + p_parsed.query

        A1 = '%s:%s:%s' % (self.username, realm, self.password)
        A2 = '%s:%s' % (method, path)

        HA1 = hash_utf8(A1)
        HA2 = hash_utf8(A2)

        if nonce == self._thread_local.last_nonce:
            self._thread_local.nonce_count += 1
        else:
            self._thread_local.nonce_count = 1
        ncvalue = '%08x' % self._thread_local.nonce_count
        s = str(self._thread_local.nonce_count).encode('utf-8')
        s += nonce.encode('utf-8')
        s += time.ctime().encode('utf-8')
        s += os.urandom(8)

        cnonce = (hashlib.sha1(s).hexdigest()[:16])
        if _algorithm == 'MD5-SESS':
            HA1 = hash_utf8('%s:%s:%s' % (HA1, nonce, cnonce))

        if not qop:
            respdig = KD(HA1, "%s:%s" % (nonce, HA2))
        elif qop == 'auth' or 'auth' in qop.split(','):
            noncebit = "%s:%s:%s:%s:%s" % (
                nonce, ncvalue, cnonce, 'auth', HA2
                )
            respdig = KD(HA1, noncebit)
        else:
            # XXX handle auth-int.
            return None

        self._thread_local.last_nonce = nonce

        # XXX should the partial digests be encoded too?
        base = 'username="%s", realm="%s", nonce="%s", uri="%s", ' \
               'response="%s"' % (self.username, realm, nonce, path, respdig)
        if opaque:
            base += ', opaque="%s"' % opaque
        if algorithm:
            base += ', algorithm="%s"' % algorithm
        if entdig:
            base += ', digest="%s"' % entdig
        if qop:
            base += ', qop="auth", nc=%s, cnonce="%s"' % (ncvalue, cnonce)

        return 'Digest %s' % (base)

    def handle_redirect(self, r, **kwargs):
        """Reset num_401_calls counter on redirects."""
        if r.is_redirect:
            self._thread_local.num_401_calls = 1

    def handle_401(self, r, **kwargs):
        """
        Takes the given response and tries digest-auth, if needed.

        :rtype: requests.Response
        """

        if self._thread_local.pos is not None:
            # Rewind the file position indicator of the body to where
            # it was to resend the request.
            r.request.body.seek(self._thread_local.pos)
        s_auth = r.headers.get('www-authenticate', '')

        if 'digest' in s_auth.lower() and self._thread_local.num_401_calls < 2:

            self._thread_local.num_401_calls += 1
            pat = re.compile(r'digest ', flags=re.IGNORECASE)
            self._thread_local.chal = parse_dict_header(pat.sub('', s_auth, count=1))

            # Consume content and release the original connection
            # to allow our new request to reuse the same one.
            r.content
            r.close()
            prep = r.request.copy()
            extract_cookies_to_jar(prep._cookies, r.request, r.raw)
            prep.prepare_cookies(prep._cookies)

            prep.headers['Authorization'] = self.build_digest_header(
                prep.method, prep.url)
            _r = r.connection.send(prep, **kwargs)
            _r.history.append(r)
            _r.request = prep

            return _r

        self._thread_local.num_401_calls = 1
        return r
```