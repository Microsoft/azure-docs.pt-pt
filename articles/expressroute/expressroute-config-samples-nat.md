---
title: 'Azure ExpressRoute: Amostras de configuração do router - NAT'
description: Esta página fornece amostras de configuração de router para routers Cisco e Juniper.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 12/06/2018
ms.author: cherylmc
ms.openlocfilehash: ef2fd40db422c459ca966e802344ef45f7ec01de
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "74072102"
---
# <a name="router-configuration-samples-to-set-up-and-manage-nat"></a>Amostras de configuração do router para configurar e gerir NAT

Esta página fornece amostras de configuração NAT para routers da série Cisco ASA e Juniper SRX ao trabalhar com o ExpressRoute. Destinam-se a ser amostras apenas para orientação e não devem ser utilizadas como está. Pode trabalhar com o seu fornecedor para criar configurações adequadas para a sua rede.

> [!IMPORTANT]
> As amostras nesta página destinam-se exclusivamente a orientação. Tem de trabalhar com a equipa técnica/de vendas do seu fornecedor e com a sua equipa de networking para criar configurações adequadas para atender às suas necessidades. A Microsoft não suportará problemas relacionados com configurações listadas nesta página. Deve contactar o fornecedor do seu dispositivo para obter problemas de suporte.
> 
> 

* As amostras de configuração do router abaixo aplicam-se aos pares do Azure Public e da Microsoft. Não deve configurar o NAT para o peering privado azure. Reveja [os pares expressRoute](expressroute-circuit-peerings.md) e [os requisitos do ExpressRoute NAT](expressroute-nat.md) para mais detalhes.

* Deve utilizar piscinas IP NAT separadas para conectividade com a internet e expressRoute. A utilização do mesmo pool IP NAT através da internet e expressRoute resultará em encaminhamento assimétrico e perda de conectividade.


## <a name="cisco-asa-firewalls"></a>Firewalls Cisco ASA
### <a name="pat-configuration-for-traffic-from-customer-network-to-microsoft"></a>Configuração PAT para tráfego da rede de clientes para a Microsoft
    object network MSFT-PAT
      range <SNAT-START-IP> <SNAT-END-IP>


    object-group network MSFT-Range
      network-object <IP> <Subnet_Mask>

    object-group network on-prem-range-1
      network-object <IP> <Subnet-Mask>

    object-group network on-prem-range-2
      network-object <IP> <Subnet-Mask>

    object-group network on-prem
      network-object object on-prem-range-1
      network-object object on-prem-range-2

    nat (outside,inside) source dynamic on-prem pat-pool MSFT-PAT destination static MSFT-Range MSFT-Range

### <a name="pat-configuration-for-traffic-from-microsoft-to-customer-network"></a>Configuração PAT para tráfego da Microsoft para a rede de clientes

**Interfaces e Direção:**

    Source Interface (where the traffic enters the ASA): inside
    Destination Interface (where the traffic exits the ASA): outside

**Configuração:**

Piscina NAT:

    object network outbound-PAT
        host <NAT-IP>

Servidor de destino:

    object network Customer-Network
        network-object <IP> <Subnet-Mask>

Grupo de objetos para endereços IP do cliente

    object-group network MSFT-Network-1
        network-object <MSFT-IP> <Subnet-Mask>

    object-group network MSFT-PAT-Networks
        network-object object MSFT-Network-1

Comandos NAT:

    nat (inside,outside) source dynamic MSFT-PAT-Networks pat-pool outbound-PAT destination static Customer-Network Customer-Network


## <a name="juniper-srx-series-routers"></a>Routers da série Juniper SRX
### <a name="1-create-redundant-ethernet-interfaces-for-the-cluster"></a>1. Criar interfaces Ethernet redundantes para o cluster
    interfaces {
        reth0 {
            description "To Internal Network";
            vlan-tagging;
            redundant-ether-options {
                redundancy-group 1;
            }
            unit 100 {
                vlan-id 100;
                family inet {
                    address <IP-Address/Subnet-mask>;
                }
            }
        }
        reth1 {
            description "To Microsoft via Edge Router";
            vlan-tagging;
            redundant-ether-options {
                redundancy-group 2;
            }
            unit 100 {
                description "To Microsoft via Edge Router";
                vlan-id 100;
                family inet {
                    address <IP-Address/Subnet-mask>;
                }
            }
        }
    }


### <a name="2-create-two-security-zones"></a>2. Criar duas zonas de segurança
* Zona de confiança para rede interna e Zona Deconfiança para rede externa virada para routers Edge
* Atribuir interfaces apropriadas às zonas
* Permitir serviços nas interfaces

    segurança { zonas { Security-zone Trust { host-inbound-traffic { system-services { ping;                   } protocolos { bgp;                   } Interfaces { reth0.100;               } zona de segurança Untrust { host-inbound-traffic { system-services { ping;                   } protocolos { bgp;                   } Interfaces { reth1.100;               }           }       }   }


### <a name="3-create-security-policies-between-zones"></a>3. Criar políticas de segurança entre zonas
    security {
        policies {
            from-zone Trust to-zone Untrust {
                policy allow-any {
                    match {
                        source-address any;
                        destination-address any;
                        application any;
                    }
                    then {
                        permit;
                    }
                }
            }
            from-zone Untrust to-zone Trust {
                policy allow-any {
                    match {
                        source-address any;
                        destination-address any;
                        application any;
                    }
                    then {
                        permit;
                    }
                }
            }
        }
    }


### <a name="4-configure-nat-policies"></a>4. Configurar as políticas nat
* Crie duas piscinas NAT. Um será usado para o tráfego NAT de saída para a Microsoft e outro da Microsoft para o cliente.
* Criar regras para o NAT o respetivo tráfego
  
       security {
           nat {
               source {
                   pool SNAT-To-ExpressRoute {
                       routing-instance {
                           External-ExpressRoute;
                       }
                       address {
                           <NAT-IP-address/Subnet-mask>;
                       }
                   }
                   pool SNAT-From-ExpressRoute {
                       routing-instance {
                           Internal;
                       }
                       address {
                           <NAT-IP-address/Subnet-mask>;
                       }
                   }
                   rule-set Outbound_NAT {
                       from routing-instance Internal;
                       to routing-instance External-ExpressRoute;
                       rule SNAT-Out {
                           match {
                               source-address 0.0.0.0/0;
                           }
                           then {
                               source-nat {
                                   pool {
                                       SNAT-To-ExpressRoute;
                                   }
                               }
                           }
                       }
                   }
                   rule-set Inbound-NAT {
                       from routing-instance External-ExpressRoute;
                       to routing-instance Internal;
                       rule SNAT-In {
                           match {
                               source-address 0.0.0.0/0;
                           }
                           then {
                               source-nat {
                                   pool {
                                       SNAT-From-ExpressRoute;
                                   }
                               }
                           }
                       }
                   }
               }
           }
       }

### <a name="5-configure-bgp-to-advertise-selective-prefixes-in-each-direction"></a>5. Configure BGP para anunciar prefixos seletivos em cada sentido
Consulte as amostras na página de amostras de [configuração de Encaminhamento.](expressroute-config-samples-routing.md)

### <a name="6-create-policies"></a>6. Criar políticas
    routing-options {
                  autonomous-system <Customer-ASN>;
    }
    policy-options {
        prefix-list Microsoft-Prefixes {
            <IP-Address/Subnet-Mask;
            <IP-Address/Subnet-Mask;
        }
        prefix-list private-ranges {
            10.0.0.0/8;
            172.16.0.0/12;
            192.168.0.0/16;
            100.64.0.0/10;
        }
        policy-statement Advertise-NAT-Pools {
            from {
                protocol static;
                route-filter <NAT-Pool-Address/Subnet-mask> prefix-length-range /32-/32;
            }
            then accept;
        }
        policy-statement Accept-from-Microsoft {
            term 1 {
                from {
                    instance External-ExpressRoute;
                    prefix-list-filter Microsoft-Prefixes orlonger;
                }
                then accept;
            }
            term deny {
                then reject;
            }
        }
        policy-statement Accept-from-Internal {
            term no-private {
                from {
                    instance Internal;
                    prefix-list-filter private-ranges orlonger;
                }
                then reject;
            }
            term bgp {
                from {
                    instance Internal;
                    protocol bgp;
                }
                then accept;
            }
            term deny {
                then reject;
            }
        }
    }
    routing-instances {
        Internal {
            instance-type virtual-router;
            interface reth0.100;
            routing-options {
                static {
                    route <NAT-Pool-IP-Address/Subnet-mask> discard;
                }
                instance-import Accept-from-Microsoft;
            }
            protocols {
                bgp {
                    group customer {
                        export <Advertise-NAT-Pools>;
                        peer-as <Customer-ASN-1>;
                        neighbor <BGP-Neighbor-IP-Address>;
                    }
                }
            }
        }
        External-ExpressRoute {
            instance-type virtual-router;
            interface reth1.100;
            routing-options {
                static {
                    route <NAT-Pool-IP-Address/Subnet-mask> discard;
                }
                instance-import Accept-from-Internal;
            }
            protocols {
                bgp {
                    group edge-router {
                        export <Advertise-NAT-Pools>;
                        peer-as <Customer-Public-ASN>;
                        neighbor <BGP-Neighbor-IP-Address>;
                    }
                }
            }
        }
    }

## <a name="next-steps"></a>Passos seguintes
Veja [FAQ do ExpressRoute](expressroute-faqs.md) para obter mais detalhes.

