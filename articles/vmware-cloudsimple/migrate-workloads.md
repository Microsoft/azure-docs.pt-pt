---
title: Soluções Azure VMware (AVS) - Migração de VMs de carga de trabalho para AVS Private Cloud
description: Descreve como migrar máquinas virtuais do vCenter no local para AVS Private Cloud vCenter
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: af02bd947c73b670306704a10a09ca981b34c9a9
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77020000"
---
# <a name="migrate-workload-vms-from-on-premises-vcenter-to-avs-private-cloud-vcenter-environment"></a>Migram VMs de carga de trabalho do vCenter no local para o ambiente VCenter da Nuvem Privada AVS

Para migrar VMs de um datacenter no local para a sua Nuvem Privada AVS, estão disponíveis várias opções. A Nuvem Privada AVS fornece acesso nativo ao VMware vCenter, e as ferramentas suportadas pela VMware podem ser usadas para a migração de carga de trabalho. Este artigo descreve algumas das opções de migração vCenter.

## <a name="prerequisites"></a>Pré-requisitos

A migração de VMs e dados do seu datacenter no local requer conectividade da rede desde o datacenter até ao seu ambiente AVS Private Cloud. Utilize qualquer um dos seguintes métodos para estabelecer a conectividade da rede:

* [Ligação VPN do site-para-site](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) entre o seu ambiente no local e a sua Nuvem Privada AVS.
* Ligação ExpressRoute Global Reach entre o circuito ExpressRoute no local e um circuito AVS ExpressRoute.

O caminho de rede desde o ambiente vCenter no local até à sua Nuvem Privada AVS deve estar disponível para migração de VMs usando vMotion. A rede vMotion no seu vCenter no local deve ter capacidades de encaminhamento. Verifique se a sua firewall permite todo o tráfego de vMotion entre o vCenter no local e o VCenter da Nuvem Privada AVS. (Na Nuvem Privada AVS, o encaminhamento na rede vMotion é configurado por padrão.)

## <a name="migrate-isos-and-templates"></a>Migrar ISOs e modelos

Para criar novas máquinas virtuais na sua Nuvem Privada AVS, utilize modelos ISOs e VM. Para fazer o upload dos ISOs e modelos para o seu VCenter De Nuvem Privada AVS e disponibilizá-los, utilize o seguinte método.

1. Faça o upload do ISO para o VCenter De Nuvem Privada AVS a partir do vCenter UI.
2. [Publique uma biblioteca](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A0F1C13-7336-45CE-B211-610D39A6E1F4.html) de conteúdos no seu VCenter De Nuvem Privada AVS:

    1. Publique a sua biblioteca de conteúdos no local.
    2. Crie uma nova biblioteca de conteúdos no VCenter De Nuvem Privada AVS.
    3. Subscreva a biblioteca de conteúdos publicado no local.
    4. Sincronizar a biblioteca de conteúdos para acesso a conteúdos subscritos.

## <a name="migrate-vms-using-powercli"></a>VMs migratórios usando PowerCLI

Para migrar VMs do vCenter no local para o VCenter Private Cloud VCenter, utilize vMware PowerCLI ou o Utilitário de Migração de Carga de Trabalho Cross vCenter disponível na VMware Labs. O seguinte script de amostra mostra os comandos de migração PowerCLI.

```
$sourceVC = Connect-VIServer -Server <source-vCenter name> -User <source-vCenter user name> -Password <source-vCenter user password>
$targetVC = Connect-VIServer -Server <target-vCenter name> -User <target-vCenter user name> -Password <target-vCenter user password>
$vmhost = <name of ESXi host on destination>
$vm = Get-VM -Server $sourceVC <name of VM>
Move-VM -VM $vm -VMotionPriority High -Destination (Get-VMhost -Server $targetVC -Name $vmhost) -Datastore (Get-Datastore -Server $targetVC -Name <name of tgt vc datastore>)
```

> [!NOTE]
> Para utilizar os nomes do servidor vCenter de destino e anfitriões ESXi, configure o dNS reencaminhamento de dNS a partir das instalações para o seu AVS Private Cloud.

## <a name="migrate-vms-using-nsx-layer-2-vpn"></a>VMs migratórios usando NSX Layer 2 VPN

Esta opção permite a migração ao vivo de cargas de trabalho do seu ambiente VMware no local para a Nuvem Privada AVS em Azure. Com esta rede de Camada 2 estendida, a subnet a partir das instalações estará disponível na Nuvem Privada AVS. Após a migração, não é necessária uma nova atribuição de endereços IP para os VMs.

[As cargas de trabalho de migração utilizando redes estendidas da Camada 2](migration-layer-2-vpn.md) descrevem como usar uma VPN camada 2 para esticar uma rede Layer 2 do seu ambiente no local até à sua Nuvem Privada AVS.

## <a name="migrate-vms-using-backup-and-disaster-recovery-tools"></a>VMs migratórios usando ferramentas de backup e recuperação de desastres

A migração de VMs para AVS Private Cloud pode ser feita usando ferramentas de backup/restauro e ferramentas de recuperação de desastres. Utilize a Nuvem Privada AVS como alvo para restaurar a partir de backups que são criados usando uma ferramenta de terceiros. A Nuvem Privada AVS também pode ser usada como um alvo para a recuperação de desastres usando VMware SRM ou uma ferramenta de terceiros.

Para mais informações utilizando estas ferramentas, consulte os seguintes tópicos:

* [Back up máquinas virtuais de carga de trabalho em AVS Private Cloud usando Veeam B&R](backup-workloads-veeam.md)
* [Configurar a AVS Private Cloud como local de recuperação de desastres para cargas de trabalho vMware no local](disaster-recovery-zerto.md)
