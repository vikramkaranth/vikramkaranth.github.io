
### How to prevent “Write Failed: broken pipe” on SSH connection?

#### What options `ServerAliveInterval` and `ClientAliveInterval` in sshd_config exactly do?

[ServerAliveInterval](http://man.openbsd.org/ssh_config#ServerAliveInterval): number of seconds that the client will wait before sending a null packet to the server (to keep the connection alive).

[ClientAliveInterval](http://man.openbsd.org/sshd_config#ClientAliveInterval): number of seconds that the server will wait before sending a null packet to the client (to keep the connection alive).

Setting a value of 0 (the default) will disable these features so your connection could drop if it is idle for too long.

ServerAliveInterval seems to be the most common strategy to keep a connection alive. To prevent the broken pipe problem, here is the ssh config I use in my `/etc/ssh/ssh_config` file:


    Host myhostshortcut
        HostName myhost.com
        User vikramk
        ServerAliveInterval 60
        ServerAliveCountMax 10


The above setting will work in the following way,

The client will wait idle for 60 seconds (ServerAliveInterval time) and, send a "no-op null packet" to the server and expect a response. If no response comes, then it will keep trying the above process till 10 (ServerAliveCountMax) times (600 seconds). If the server still doesn't respond, then the client disconnects the ssh connection.
ClientAliveCountMax on the server side might also help. This is the limit of how long a client are allowed to stay unresponsive before being disconnected. The default value is 3, as in three ClientAliveInterval.
