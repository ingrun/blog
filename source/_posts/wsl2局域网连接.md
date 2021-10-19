---
title: wsl2局域网连接
date: 2021-04-28
---



# wsl2局域网连接



```shell

# 添加转发端口  powershell 管理员
netsh interface portproxy add v4tov4 listenport=port connectaddress=127.0.0.1 listenaddress=* protocol=tcp

# 查看已转发的端口
netsh interface portproxy show all

# 删除转发端口
netsh interface portproxy delete v4tov4 listenport=80 listenaddress=0.0.0.0 
# 注意 写的是0.0.0.0删的时候也需要是0.0.0.0进行对应，不然会提示找不到文件


```

