# Scripts

## IP

{% code title="ip.sh" %}
```bash
###########################################################################
# Title: Script to set ip (tun0)
# Date: 12/10/2019
# Exploit Author: shell5
###########################################################################

export xip2=$(ifconfig eth0 | grep 'inet ' | awk -F'[: ]+' '{ print $3 }')

```
{% endcode %}

## Local IP

{% code title="local.sh" %}
```
#!/bin/bash

###########################################################################
# Title: 
# Date: 12/10/2019
# Exploit Author: shell5
###########################################################################

remote_ip() { ifconfig tun0 | grep 'inet ' | awk -F'[: ]+' '{ print $3 }'; }
export -f remote_ip
remote_ip

```
{% endcode %}



