---
title: Exemplos de configuração do roteador - NAT - Azure ExpressRoute | Documentos da Microsoft
description: Esta página fornece exemplos de configuração do roteador para os routers Cisco e Juniper.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 12/06/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: ccee0f0c01119ebbfb5ba9c5980ee006a555a399
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56872085"
---
# <a name="router-configuration-samples-to-set-up-and-manage-nat"></a>Exemplos de configuração de router para configurar e gerir o NAT

Esta página fornece exemplos de configuração de NAT para Cisco ASA e os routers da série de Juniper SRX ao trabalhar com o ExpressRoute. Estes destinam-se para ser amostras para obter orientações apenas e não pode ser usados como está. Pode trabalhar com o fornecedor para propor as configurações apropriadas para a sua rede.

> [!IMPORTANT]
> Exemplos nesta página se destinam a ser puramente para obter orientações. Deve trabalhar com equipes de técnicos e de vendas do seu fornecedor e seu funcionamento em rede propor configurações adequadas às suas necessidades. A Microsoft irá suporta problemas relacionados com as configurações listadas nesta página. Tem de contactar o seu fornecedor de dispositivo para problemas de suporte.
> 
> 

* Exemplos de configuração de router abaixo aplicam-se para peerings público do Azure e a Microsoft. Não é necessário configurar NAT para peering privado do Azure. Revisão [peerings do ExpressRoute](expressroute-circuit-peerings.md) e [requisitos do NAT do ExpressRoute](expressroute-nat.md) para obter mais detalhes.

* TEM de utilizar conjuntos separados de IP do NAT para a conectividade à internet e ExpressRoute. Usando o mesmo conjunto de IP do NAT na internet e do ExpressRoute resultará no encaminhamento assimétrico e a perda de conectividade.


## <a name="cisco-asa-firewalls"></a>Firewalls Cisco ASA
### <a name="pat-configuration-for-traffic-from-customer-network-to-microsoft"></a>Configuração de PAT para tráfego de rede cliente para a Microsoft
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

### <a name="pat-configuration-for-traffic-from-microsoft-to-customer-network"></a>Configuração de PAT para o tráfego da Microsoft, a rede de cliente

**Interfaces e direção:**

    Source Interface (where the traffic enters the ASA): inside
    Destination Interface (where the traffic exits the ASA): outside

**Configuração:**

Conjunto NAT:

    object network outbound-PAT
        host <NAT-IP>

Servidor de destino:

    object network Customer-Network
        network-object <IP> <Subnet-Mask>

Objeto de grupo para endereços IP de cliente

    object-group network MSFT-Network-1
        network-object <MSFT-IP> <Subnet-Mask>

    object-group network MSFT-PAT-Networks
        network-object object MSFT-Network-1

Comandos NAT:

    nat (inside,outside) source dynamic MSFT-PAT-Networks pat-pool outbound-PAT destination static Customer-Network Customer-Network


## <a name="juniper-srx-series-routers"></a>Routers de série do Juniper SRX
### <a name="1-create-redundant-ethernet-interfaces-for-the-cluster"></a>1. Criar interfaces de Ethernet redundantes para o cluster
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
* Zona de confiança para a rede interna e zona de Untrust para rede externa com acesso à Routers de limite
* Atribuir as interfaces apropriadas para as zonas
* Permitir que os serviços nas interfaces

    segurança {zonas {zona de segurança confiança {anfitrião de entrada-tráfego {-serviços do sistema {ping;                   } protocolos {bgp;                   interfaces de}} {reth0.100;               }} Untrust de zona de segurança {anfitrião de entrada-tráfego {-serviços do sistema {ping;                   } protocolos {bgp;                   interfaces de}} {reth1.100;               }           }       }   }


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


### <a name="4-configure-nat-policies"></a>4. Configurar políticas NAT
* Crie dois conjuntos NAT. Um será utilizado para tráfego NAT de saída para a Microsoft e outro da Microsoft para o cliente.
* Criar regras de NAT o respectivo tráfego
  
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

### <a name="5-configure-bgp-to-advertise-selective-prefixes-in-each-direction"></a>5. Configurar o BGP para anunciar prefixos seletivos em cada direção
Veja as amostras no [exemplos de configuração do encaminhamento](expressroute-config-samples-routing.md) página.

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

## <a name="next-steps"></a>Passos Seguintes
Veja [FAQ do ExpressRoute](expressroute-faqs.md) para obter mais detalhes.

