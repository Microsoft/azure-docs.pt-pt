---
title: Garantir a elevada disponibilidade da aplicação ao correr em VMware no Azure
description: Descreve funcionalidades de alta disponibilidade cloudSimple para abordar cenários comuns de falha de aplicações para aplicações em execução numa CloudSimple Private Cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a3eed033ba6a1a6f9237116a53ec7751ae906fe4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025355"
---
# <a name="ensure-application-high-availability-when-running-in-vmware-on-azure"></a>Garantir a elevada disponibilidade da aplicação ao correr em VMware no Azure

A solução CloudSimple proporciona uma elevada disponibilidade para as suas aplicações em vMware no ambiente Azure. A tabela seguinte lista cenários de falha e as características de alta disponibilidade associadas.

| Cenário de falha | Aplicação protegida? | Funcionalidade de Plataforma HA | Funcionalidade VMware HA | Característica azure HA |
------------ | ------------- | ------------ | ------------ | ------------- |
| Falha no disco | SIM | Substituição rápida do nó falhado | [Sobre a política de armazenamento padrão vSAN](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-C228168F-6807-4C2A-9D74-E584CAF49A2A.html) |
| Falha do ventilador | SIM | Fãs redundantes, substituição rápida do nó falhado |  |  |
| Falha nic | SIM | NIC redundante, substituição rápida do nó falhado
| Falha de energia do hospedeiro | SIM | Fornecimento de energia redundante |  |  |
| Falha do hospedeiro ESXi | SIM | substituição rápida do nó falhado | [VMware vSphere Alta Disponibilidade](https://www.vmware.com/products/vsphere/high-availability.html) |  |  |
| Falha vm | SIM | [Balanceadores de carga](load-balancers.md)  | [VMware vSphere Alta Disponibilidade](https://www.vmware.com/products/vsphere/high-availability.html) | Equilíbrio de carga Azure para VMware apátrida |
| Falha da porta do interruptor de folha | SIM | NIC redundante |  |  |
| Falha no interruptor de folha | SIM | Interruptores de folharedundantes |  |  |
| Falha no rack | SIM | Grupos de colocação |  |  |
| Conectividade da rede para o local DC | SIM  | Serviços de rede redundantes |  | Circuitos er redundantes |
| Conectividade da Rede para Azure | SIM | |  | Circuitos er redundantes |
| Falha no datacenter | SIM |  |  | Zonas de disponibilidade |
| Falha Regional | SIM  |  |  | Regiões do Azure |

A Solução Azure VMware by CloudSimple fornece as seguintes funcionalidades de alta disponibilidade.

## <a name="fast-replacement-of-failed-node"></a>Substituição rápida do nó falhado

O software de plano de controlo CloudSimple monitoriza continuamente a saúde dos clusters VMware e deteta quando um nó ESXi falha. Em seguida, adiciona automaticamente um novo hospedeiro ESXi ao cluster VMware afetado a partir do seu conjunto de nós prontamente disponíveis e tira o nó falhado do cluster. Esta funcionalidade garante que a capacidade de reposição no cluster VMware é restaurada rapidamente de modo a restaurar a resiliência do cluster fornecida pela vSAN e VMware HA.

## <a name="placement-groups"></a>Grupos de colocação

Um utilizador que cria uma Nuvem Privada pode selecionar uma região Azure e um grupo de colocação dentro da região selecionada. Um grupo de colocação é um conjunto de nódosos espalhados por várias prateleiras, mas dentro do mesmo segmento de rede de colunavertebral. Os nós dentro do mesmo grupo de colocação podem alcançar-se uns aos outros com um máximo de dois lúpulos extra. Um grupo de colocação está sempre dentro de uma única zona de disponibilidade Azure e abrange várias prateleiras. O plano de controlo CloudSimple distribui os nódeos de uma Nuvem Privada em várias prateleiras com base no melhor esforço. Os nódosos em diferentes grupos de colocação são garantidos para serem colocados em diferentes prateleiras.

## <a name="availability-zones"></a>Zonas de disponibilidade

As zonas de disponibilidade são uma oferta de alta disponibilidade que protege as suas aplicações e dados de falhas no datacenter. As zonas de disponibilidade são locais físicos especiais dentro de uma região de Azure. Cada zona é composta por um ou mais datacenters equipados com energia, refrigeração e rede independentes. Cada região tem uma zona de disponibilidade. Para mais informações, consulte [As Zonas de Disponibilidade em Azure?](../availability-zones/az-overview.md)

## <a name="redundant-azure-expressroute-circuits"></a>Circuitos Redundantes azure ExpressRoute

A conectividade do centro de dados com o Azure vNet utilizando o ExpressRoute tem circuitos redundantes para fornecer uma ligação de conectividade de rede altamente disponível.

## <a name="redundant-networking-services"></a>Serviços de rede redundantes

Todos os serviços de rede CloudSimple para a Cloud Privada (incluindo VLAN, firewall, endereços IP públicos, Internet e VPN) são projetados para estar altamente disponível e capaz de suportar o serviço SLA.

## <a name="azure-layer-7-load-balancer-for-stateless-vmware-vms"></a>Equilíbrio de carga da camada 7 azure para VMware apátrida

Os utilizadores podem colocar um Balancer de Carga De Camada 7 Azure na frente dos VMs apátridas de nível web que executam no ambiente VMware para alcançar uma elevada disponibilidade para o nível web.

## <a name="azure-regions"></a>Regiões do Azure

Uma região de Azure é um conjunto de centros de dados implantados dentro de um perímetro definido pela latência e conectados através de uma rede regional dedicada de baixa latência. Para mais detalhes, consulte [as Regiões de Azure.](https://azure.microsoft.com/global-infrastructure/regions)
