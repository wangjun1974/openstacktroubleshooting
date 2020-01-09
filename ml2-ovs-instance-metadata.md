### ml2 ovs
#### 实例如何获取metadata

实例可以通过config drive或者metadata agent获取实例metadata

对于通过metadata agent获取实例metadata支持两种方式：<br>
1. 通过l3 agent访问metadata agent<br>
2. 通过dhcp agent访问metadata agent<br>

这两种方式都要求实例网络可达metadata服务地址

通过metadata agent访问metadata服务地址为 169.254.169.254:80
![](pics/ml2-ovs-instance-metadata-pic1.png)

虚拟机通过qrouter或者qdhcp网络名字空间里的neutron-ns-metadata-proxy将虚拟机对metadata的请求通过unix domain socket转发给neutron-metadata-agent，再转发给nova-api-metadata
![](pics/ml2-ovs-instance-metadata-pic2.png)
![](pics/ml2-ovs-instance-metadata-pic3.png)

qrouter-xxxxx名字空间里的neutron-ns-metadata-proxy
![](pics/ml2-ovs-instance-metadata-pic4.png)

neutron-ns-metadata-proxy的配置文件
![](pics/ml2-ovs-instance-metadata-pic5.png)

/var/lib/neutron/metadata_proxy是用于neutron-ns-metadata-proxy和metadata agent间IPC通信的unix socket
![](pics/ml2-ovs-instance-metadata-pic6.png)

neutron-ns-metadata-proxy监听在9697端口
![](pics/ml2-ovs-instance-metadata-pic7.png)

访问169.254.169.254 80时，通过iptables规则转发请求到neutron-ns-metadata-proxy所监听的端口
![](pics/ml2-ovs-instance-metadata-pic8.png)

neutron-ns-metadata-proxy通过unix domain socket /var/lib/neutron/metadata_proxy发送到neutron-metadata-agent
，neutron-metadata-agent再转发给nova-api-metadata获取相关实例metadata

通过dhcp agent进行neutron-ns-metadata-proxy的方式，与qrouter类似。在控制节点上配置文件/var/lib/config-data/neutron/etc/neutron/dhcp_agent.ini里，配置项enable_isolated_metadata需设置为true
![](pics/ml2-ovs-instance-metadata-pic9.png)

在启动实例时，实例内会增加169.254.169.254的路由
![](pics/ml2-ovs-instance-metadata-pic10.png)

路由指向qdhcp-xxxx namespace里的ip地址，同一个设备上还启用着169.254.169.254
![](pics/ml2-ovs-instance-metadata-pic11.png)

