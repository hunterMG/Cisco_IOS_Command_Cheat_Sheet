EtherChannel

// Etherchannel config first, then DTP(Dynamic Trunk Protocol)

S1(config)# int range f0/21-22
S1(config-if-range)# channel-group 1 mode desirable       // desirable & auto -> PAgP, active & passive ->LACP
S1(config-if-range)# int port-channel 1
S1(config-if)# switchport mode trunk
S1(config-if)# switchport trunk allowed 1,2,20

S1# show etherchannel summary
S1# show etherchannel port-channel
S1# show int etherchannel

Spanning Tree Protocol (STP)



