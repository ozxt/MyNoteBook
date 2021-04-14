#### AES MODE_ECB

```shell
>>> from Crypto.Cipher import AES
>>> key='1234567887654321'    # key长度须为16字节
>>> aes=AES.new(key,AES.MODE_ECB)
>>> aes
<Crypto.Cipher.AES.AESCipher object at 0x7f65fec1ca00>
>>> plaintext='hellow word!'
>>> plaintext=plaintext.encode('utf-8')
>>> plaintext += b'\x00' * (16-len(plaintext)%16)  # 须为16字节倍数
>>> plaintext
'hellow word!\x00\x00\x00\x00'
>>> ciphertext=aes.encrypt(plaintext)
>>> ciphertext
b'\x95\xdb=?^>\x97\xa1M\x18\xbf\xf6\xfes\xbfx'
>>> import base64
>>> b64str=base64.b64encode(ciphertext)
>>> b64str
b'lds9P14+l6FNGL/2/nO/eA=='
>>> b64str.decode('ascii')
'lds9P14+l6FNGL/2/nO/eA=='
>>> str(b64str)
"b'lds9P14+l6FNGL/2/nO/eA=='"
>>> repr(b64str)
"b'lds9P14+l6FNGL/2/nO/eA=='"
>>> aes.decrypt(ciphertext)
b'hellow word!\x00\x00\x00\x00'
>>> plaintext=aes.decrypt(ciphertext)
>>> plaintext.decode('utf-8')
'hellow word!\x00\x00\x00\x00'
```

