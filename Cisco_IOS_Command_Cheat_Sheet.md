## EtherChannel

// Etherchannel config first, then DTP(Dynamic Trunk Protocol)
```
S1(config)# int range f0/21-22
S1(config-if-range)# channel-group 1 mode desirable       // desirable & auto -> PAgP, active & passive ->LACP
S1(config-if-range)# int port-channel 1
S1(config-if)# switchport mode trunk
S1(config-if)# switchport trunk allowed 1,2,20
```
```
S1# show etherchannel summary
S1# show etherchannel port-channel
S1# show int etherchannel
```
## Spanning Tree Protocol (STP)

### PVST+ :
```
S1# spanning-tree vlan 1 root primary
S2# spanning-tree vlan 1 root secondary
S3# spanning-tree vlan 1 priority 24576  // the vlaue of priority must be a multiply of 4096 (0~61440)
S1(config)# int f0/1
S1(config-if)# spanning-tree portfast
S1(config-if)# spanning-tree bpduguard enable
```
```
S1(config)# spanning-tree portfast default // enable portfast on all non trunk interfaces
S1(config)# spanning-tree portfast bpduguard default //enable bpduguard on all interfaces which have enabled portfast
```
### RSTP :
```
S1(config)# sapnning-tree mode rapid-pvst
S1(config)# int f0/1
S1(config-if)# spanning-tree link-type point-to-point
```
```
S3# show spanning-tree
S3# show spanning-tree vlan 10
S3# show spanning-tree active
S3# show spanning-tree summary
S3# show run | include span
```
## Password Recovery

	Reboot the router(in the physical Tab);
	Click 'Ctrl+C' or 'Ctrl+Break' to interrupt the boot process;
		rommon1 > confreg 0x2142
		rommon1 > boot (or reset)
	Wait until the reboot process completed;
	'Ctrl+C' or type 'no' ;
		router > en
		router > copy startup-config running-config
	Issue the "no shutdown" command on every interface that you use.

## VPN config
Make sure that the securityk9 module is enabled:
```
R1# show version
R1(config)# license boot module c2900 technology-package securityk9
R1(config)# end
R1# copy running-config startup-config
R1# reload
```

```
R1(config)# access-list 110 permit ip 192.168.1.0 0.0.0.255 192.168.3.0 0.0.0.255
--------------------------------------------------------------------------------------------------------
R1(config)# crypto isakmp policy 10
R1(config-isakmp)# encryption aes
R1(config-isakmp)# authentication pre-share
R1(config-isakmp)# group 2
R1(config-isakmp)# exit
R1(config)# crypto isakmp key cisco address 10.2.2.2
--------------------------------------------------------------------------------------------------------
R1(config)# crypto ipsec transform-set VPN-SET esp-3des esp-sha-hmac
R1(config)# crypto map VPN-MAP 10 ipsec-isakmp
R1(config-crypto-map)# description VPN connection to R3
R1(config-crypto-map)# set peer 10.2.2.2
R1(config-crypto-map)# set transform-set VPN-SET
R1(config-crypto-map)# match address 110
--------------------------------------------------------------------------------------------------------
R1(config)# interface S0/0/0
R1(config-if)# crypto map VPN-MAP
--------------------------------------------------------------------------------------------------------
```
```
R3(config)# access-list 110 permit ip 192.168.3.0 0.0.0.255 192.168.1.0 0.0.0.255
--------------------------------------------------------------------------------------------------------
R3(config)# crypto isakmp policy 10
R3(config-isakmp)# encryption aes
R3(config-isakmp)# authentication pre-share
R3(config-isakmp)# group 2
R3(config-isakmp)# exit
R3(config)# crypto isakmp key cisco address 10.1.1.2
--------------------------------------------------------------------------------------------------------
R3(config)# crypto ipsec transform-set VPN-SET esp-3des esp-sha-hmac
R3(config)# crypto map VPN-MAP 10 ipsec-isakmp
R3(config-crypto-map)# description VPN connection to R1
R3(config-crypto-map)# set peer 10.1.1.2
R3(config-crypto-map)# set transform-set VPN-SET
R3(config-crypto-map)# match address 110
R3(config-crypto-map)# exit
--------------------------------------------------------------------------------------------------------
R3(config)# interface S0/0/1
R3(config-if)# crypto map VPN-MAP
```
```
R1# show crypto ipsec sa
```
