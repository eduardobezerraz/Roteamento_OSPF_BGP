# Roteamento_OSPF_BGP
Readme com os comandos para configurar topologia no gns3.

## Configurações OSPF por Roteador
### R1 (Área 1)
```cisco
interface FastEthernet0
 ip address 10.24.9.1 255.255.255.252
 ip ospf 1 area 1
 speed auto

router ospf 1
 router-id 1.1.1.1
 auto-cost reference-bandwidth 1000
 passive-interface default
 no passive-interface FastEthernet0
 network 10.24.9.0 0.0.0.3 area 1
```
### R2 (ABR: Área 1 ↔ Área 2)
```cisco
interface FastEthernet0
 ip address 10.24.9.2 255.255.255.252
 ip ospf 1 area 1
 speed auto

interface Ethernet0
 ip address 10.24.9.5 255.255.255.252
 ip ospf 1 area 2
 half-duplex

router ospf 1
 router-id 2.2.2.2
 auto-cost reference-bandwidth 1000
 area 2 virtual-link 3.3.3.3
 network 10.24.9.0 0.0.0.3 area 1
 network 10.24.9.4 0.0.0.3 area 2
```

### R3 (ABR: Área 2 ↔ Área 0)
```cisco
interface FastEthernet0
 ip address 10.24.9.9 255.255.255.252
 ip ospf network point-to-point
 ip ospf 1 area 0
 speed auto

interface Ethernet0
 ip address 10.24.9.6 255.255.255.252
 ip ospf 1 area 2
 half-duplex

router ospf 1
 router-id 3.3.3.3
 auto-cost reference-bandwidth 1000
 area 2 virtual-link 2.2.2.2
 network 10.24.9.4 0.0.0.3 area 0
 network 10.24.9.8 0.0.0.3 area 2
```

### R4 (Backbone - Área 0)
```cisco
interface FastEthernet0
 ip address 10.24.9.10 255.255.255.252
 ip ospf network point-to-point
 ip ospf 1 area 0
 speed 100

interface Ethernet0
 ip address 10.24.9.13 255.255.255.252
 ip ospf 1 area 0
 half-duplex

router ospf 1
 router-id 4.4.4.4
 auto-cost reference-bandwidth 1000
 network 10.24.9.8 0.0.0.3 area 0
 network 10.24.9.12 0.0.0.3 area 0
```

### R5 (Backbone - Área 0)
```cisco
interface FastEthernet0
 ip address 10.24.41.1 255.255.255.252
 ip ospf network point-to-point
 ip ospf 1 area 0
 speed auto

interface Ethernet0
 ip address 10.24.9.14 255.255.255.252
 ip ospf 1 area 0
 half-duplex

router ospf 1
 router-id 5.5.5.5
 auto-cost reference-bandwidth 1000
 network 10.24.9.12 0.0.0.3 area 0
 network 10.24.41.0 0.0.0.3 area 0
```

### R6 (ABR: Área 0 ↔ Área 3)
```cisco
interface FastEthernet0
 ip address 10.24.41.2 255.255.255.252
 ip ospf network point-to-point
 ip ospf 1 area 0
 speed auto

interface Ethernet0
 ip address 10.24.41.5 255.255.255.252
 ip ospf network point-to-point
 ip ospf 1 area 3
 half-duplex

router ospf 1
 router-id 6.6.6.6
 auto-cost reference-bandwidth 1000
 area 3 virtual-link 7.7.7.7
 passive-interface default
 no passive-interface Ethernet0
 no passive-interface FastEthernet0
 network 10.24.41.0 0.0.0.3 area 0
 network 10.24.41.4 0.0.0.3 area 3
```

### R7 (ABR: Área 3 ↔ Área 4)
```cisco
interface Ethernet0
 ip address 10.24.41.6 255.255.255.252
 ip ospf network point-to-point
 ip ospf 1 area 3
 half-duplex

interface FastEthernet0
 ip address 10.24.41.9 255.255.255.252
 ip ospf network point-to-point
 ip ospf 1 area 4
 speed auto

router ospf 1
 router-id 7.7.7.7
 auto-cost reference-bandwidth 1000
 area 3 virtual-link 6.6.6.6
 passive-interface default
 no passive-interface Ethernet0
 no passive-interface FastEthernet0
 network 10.24.41.4 0.0.0.3 area 3
 network 10.24.41.8 0.0.0.3 area 4
```

### R8 (Área 4)
```cisco
interface FastEthernet0
 ip address 10.24.41.10 255.255.255.252
 ip ospf network point-to-point
 ip ospf 1 area 4
 speed auto

router ospf 1
 router-id 8.8.8.8
 auto-cost reference-bandwidth 1000
 passive-interface default
 no passive-interface FastEthernet0
 network 10.24.41.8 0.0.0.3 area 4
```

## Configurações BPG por Roteador
### R1
```cisco
enable
configure terminal
router bgp 941
 bgp log-neighbor-changes
 neighbor 10.24.9.18 remote-as 65001
 network 10.24.9.0 mask 255.255.255.0
exit
router ospf 1
 redistribute bgp 941 subnets
exit
router bgp 941
 redistribute ospf 1
exit
end
write memory
```

### R8
```cisco
enable
configure terminal
router bgp 941
 bgp log-neighbor-changes
 neighbor 10.24.41.14 remote-as 65002
 network 10.24.41.0 mask 255.255.255.0
exit
router ospf 1
 redistribute bgp 941 subnets
exit
router bgp 941
 redistribute ospf 1
exit
end
write memory
```
