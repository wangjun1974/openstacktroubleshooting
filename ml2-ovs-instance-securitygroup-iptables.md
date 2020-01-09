### ml2 ovs
#### 基于iptables实现的安全组规则

ml2 ovs下默认通过iptables实现安全组规则

包到达FORWARD Chain，会转发给neutron-openvswi-FORWARD Chain
![](pics/ml2-ovs-instance-securitygroup-iptables-pic1.png)

neutron-openvswi-FORWARD下每个实例会有一对规则，下一步处理会进入neutron-openvswi-sg-chain Chain
![](pics/ml2-ovs-instance-securitygroup-iptables-pic2.png)

--physdev-out tap4a27c20e-8f这条规则定义了来自虚拟机的流量发到neutron-openvswi-i4a27c20e-8(VM OUT)<br>
--physdev-in tap4a27c20e-8这条规则定义了发往虚拟机的流量发到neutron-openvswi-o4a27c20e-8(VM IN)

neutron-openvswi-sg-chain下一步跳转到VM对应的Chain neutron-openvswi-i4a27c20e-8和neutron-openvswi-o4a27c20e-8
![](pics/ml2-ovs-instance-securitygroup-iptables-pic3.png)

neutron-openvswi-i4a27c20e-8处理来自虚拟机的流量
![](pics/ml2-ovs-instance-securitygroup-iptables-pic4.png)

neutron-openvswi-o4a27c20e-8处理发给虚拟机的流量
![](pics/ml2-ovs-instance-securitygroup-iptables-pic5.png)

![](pics/ml2-ovs-instance-securitygroup-iptables-pic6.png)
