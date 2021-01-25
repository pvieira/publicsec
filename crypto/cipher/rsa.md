# RSA

### Two identical messages encrypted with same key

```text
sudo python3 /opt/RsaCtfTool/RsaCtfTool.py --publickey key1.pem --dumpkey
sudo python3 /opt/RsaCtfTool/RsaCtfTool.py --publickey key2.pem --dumpkey

sudo python3 /opt/RSA-Common-Modulus-Attack/rsa-cm.py -c1 message1 -c2 message2 -k1 key1.pem -k2 key2.pem
```

{% embed url="https://github.com/Ganapati/RsaCtfTool" %}

{% embed url="https://github.com/HexPandaa/RSA-Common-Modulus-Attack" %}



