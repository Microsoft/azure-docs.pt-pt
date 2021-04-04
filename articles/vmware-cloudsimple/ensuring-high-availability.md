---
title: Garantir alta disponibilidade da aplicação ao correr em VMware em Azure
description: Descreve funcionalidades de alta disponibilidade cloudSimple para abordar cenários comuns de falha de aplicações para aplicações em execução numa CloudSimple Private Cloud
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8bb1f8bb2aaeab88e5a9ea19534c8983af8c1626
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97895755"
---
# <a name="ensure-application-high-availability-when-running-in-vmware-on-azure"></a>Garantir alta disponibilidade da aplicação ao correr em VMware em Azure

A solução CloudSimple proporciona uma elevada disponibilidade para as suas aplicações em execução em VMware no ambiente Azure. A tabela que se segue lista os cenários de falha e as funcionalidades de alta disponibilidade associadas.

| Cenário de falha | Aplicação protegida? | Recurso HA da plataforma | Funcionalidade VMware HA | Recurso Azure HA |
------------ | ------------- | ------------ | ------------ | ------------- |
| Falha do disco | SIM | Substituição rápida do nó falhado | [Sobre a política de armazenamento padrão vSAN](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-C228168F-6807-4C2A-9D74-E584CAF49A2A.html) |
| Falha do ventilador | SIM | Adeptos redundantes, substituição rápida do nó falhado |  |  |
| Falha do NIC | SIM | NIC redundante, substituição rápida do nó falhado
| Falha de energia do hospedeiro | SIM | Fornecimento de energia redundante |  |  |
| Falha do anfitrião ESXi | SIM | rápida substituição do nó falhado | [VMware vSphere Alta Disponibilidade](https://www.vmware.com/products/vsphere/high-availability.html) |  |  |
| Falha em VM | SIM | [Balanceadores de carga](load-balancers.md)  | [VMware vSphere Alta Disponibilidade](https://www.vmware.com/products/vsphere/high-availability.html) | Balançador de carga Azure para VMware apátrida |
| Falha da porta do interruptor de folha | SIM | NIC redundante |  |  |
| Falha do interruptor de folha | SIM | Interruptores de folhas redundantes |  |  |
| Falha na cremalheira | SIM | Grupos de colocação |  |  |
| Conectividade da rede para as instalações da DC | SIM  | Serviços de rede redundantes |  | Circuitos ER redundantes |
| Conectividade da rede para Azure | SIM | |  | Circuitos ER redundantes |
| Falha do Datacenter | SIM |  |  | Zonas de disponibilidade |
| Falha Regional | SIM  |  |  | Regiões do Azure |

A Azure VMware Solution by CloudSimple fornece as seguintes funcionalidades de alta disponibilidade.

## <a name="fast-replacement-of-failed-node"></a>Substituição rápida do nó falhado

O software do plano de controlo CloudSimple monitoriza continuamente a saúde dos clusters VMware e deteta quando um nó ESXi falha. Em seguida, adiciona automaticamente um novo anfitrião ESXi ao cluster VMware afetado a partir do seu conjunto de nós prontamente disponíveis e retira o nó falhado do cluster. Esta funcionalidade garante que a capacidade de reposição no cluster VMware é restaurada rapidamente para que a resiliência do cluster fornecida por vSAN e VMware HA seja restaurada.

## <a name="placement-groups"></a>Grupos de colocação

Um utilizador que crie uma Nuvem Privada pode selecionar uma região de Azure e um grupo de colocação dentro da região selecionada. Um grupo de colocação é um conjunto de nós espalhados por vários racks, mas dentro do mesmo segmento de rede da coluna vertebral. Os nós dentro do mesmo grupo de colocação podem alcançar-se uns aos outros com um máximo de dois lúpulos extra. Um grupo de colocação está sempre dentro de uma única zona de disponibilidade de Azure e abrange vários racks. O plano de controlo CloudSimple distribui nós de uma Nuvem Privada através de várias prateleiras com base no melhor esforço. Os nós em diferentes grupos de colocação são garantidos para serem colocados em diferentes racks.

## <a name="availability-zones"></a>Zonas de disponibilidade

As zonas de disponibilidade são uma oferta de alta disponibilidade que protege as suas aplicações e dados contra falhas no datacenter. As zonas de disponibilidade são localizações físicas especiais dentro de uma região de Azure. Cada zona é composta por um ou mais datacenters equipados com energia, refrigeração e rede independentes. Cada região tem uma zona de disponibilidade. Para mais informações, consulte [quais são as Zonas de Disponibilidade em Azure?](../availability-zones/az-overview.md)

## <a name="redundant-azure-expressroute-circuits"></a>Circuitos redundantes Azure ExpressRoute

A conectividade do data center com o Azure vNet utilizando o ExpressRoute tem circuitos redundantes para fornecer ligações de conectividade de rede altamente disponíveis.

## <a name="redundant-networking-services"></a>Serviços de rede redundantes

Todos os serviços de networking CloudSimple para a Nuvem Privada (incluindo VLAN, firewall, endereços IP públicos, Internet e VPN) são projetados para estar altamente disponíveis e capazes de suportar o serviço SLA.

## <a name="azure-layer-7-load-balancer-for-stateless-vmware-vms"></a>Balançador de carga Azure Layer 7 para VMware apátrida

Os utilizadores podem colocar um Balançador de Carga Azure Layer 7 em frente aos VMs de nível apátrida que estão a funcionar no ambiente VMware para obter uma elevada disponibilidade para o nível web.

## <a name="azure-regions"></a>Regiões do Azure

Uma região de Azure é um conjunto de centros de dados implantados dentro de um perímetro definido pela latência e conectados através de uma rede regional dedicada de baixa latência. Para mais detalhes, consulte [as Regiões Azure.](https://azure.microsoft.com/global-infrastructure/regions)
