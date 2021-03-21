---
title: Azure VMware Solution by CloudSimple - Migrar VMs de carga de trabalho para nuvem privada
description: Descreve como migrar máquinas virtuais de no local vCenter para CloudSimple Private Cloud vCenter
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 851305775d14ed371440434b2fbe9334ec0a21b9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97899325"
---
# <a name="migrate-workload-vms-from-on-premises-vcenter-to-private-cloud-vcenter-environment"></a>Migrar VMs de carga de trabalho de vCenter para ambiente private Cloud vCenter

Para migrar VMs de um datacenter no local para a cloudSimple Private Cloud, várias opções estão disponíveis.  A Nuvem Privada fornece acesso nativo ao VMware vCenter, e ferramentas suportadas por VMware podem ser usadas para migração de carga de trabalho. Este artigo descreve algumas das opções de migração do vCenter.

## <a name="prerequisites"></a>Pré-requisitos

A migração de VMs e dados do seu datacenter no local requer conectividade de rede desde o datacenter até ao seu ambiente private Cloud.  Utilize qualquer um dos seguintes métodos para estabelecer a conectividade da rede:

* [Ligação VPN site-to-site](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) entre o seu ambiente no local e a sua Nuvem Privada.
* Ligação ExpressRoute Global Reach entre o circuito ExpressRoute e um circuito CloudSimple ExpressRoute.

O caminho de rede desde o ambiente vCenter no local até à sua Nuvem Privada deve estar disponível para a migração de VMs usando vMotion.  A rede vMotion no seu vCenter no local deve ter capacidades de encaminhamento.  Verifique se a sua firewall permite todo o tráfego vMotion entre o seu vCenter e o private Cloud vCenter. (Na Nuvem Privada, o encaminhamento na rede vMotion é configurado por padrão.)

## <a name="migrate-isos-and-templates"></a>Migrar ISOs e modelos

Para criar novas máquinas virtuais na sua Nuvem Privada, utilize modelos ISOs e VM.  Para fazer o upload dos ISOs e dos modelos para o seu VCenter Private Cloud e disponibilizá-los, utilize o seguinte método.

1. Faça o upload do ISO para o Private Cloud vCenter a partir do uI do vCenter.
2. [Publique uma biblioteca de conteúdos](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A0F1C13-7336-45CE-B211-610D39A6E1F4.html) no seu Private Cloud vCenter:

    1. Publique a sua biblioteca de conteúdos no local.
    2. Crie uma nova biblioteca de conteúdos no Private Cloud vCenter.
    3. Subscreva a biblioteca de conteúdos publicada no local.
    4. Sincronizar a biblioteca de conteúdos para acesso a conteúdos subscritos.

## <a name="migrate-vms-using-powercli"></a>Migrar VMs usando PowerCLI

Para migrar VMs do vCenter no local para o VCenter Private Cloud, utilize o VMware PowerCLI ou o Utilitário de Migração da Carga de Trabalho cross vCenter disponível na VMware Labs.  O seguinte guião de amostra mostra os comandos de migração PowerCLI.

```
$sourceVC = Connect-VIServer -Server <source-vCenter name> -User <source-vCenter user name> -Password <source-vCenter user password>
$targetVC = Connect-VIServer -Server <target-vCenter name> -User <target-vCenter user name> -Password <target-vCenter user password>
$vmhost = <name of ESXi host on destination>
$vm = Get-VM -Server $sourceVC <name of VM>
Move-VM -VM $vm -VMotionPriority High -Destination (Get-VMhost -Server $targetVC -Name $vmhost) -Datastore (Get-Datastore -Server $targetVC -Name <name of tgt vc datastore>)
```

> [!NOTE]
> Para utilizar os nomes dos anfitriões vCenter e ESXi de destino, configurar o encaminhamento de DNS das instalações para a sua Nuvem Privada.

## <a name="migrate-vms-using-nsx-layer-2-vpn"></a>Migrar VMs usando NSX Layer 2 VPN

Esta opção permite a migração ao vivo de cargas de trabalho do ambiente VMware no local para a Nuvem Privada em Azure.  Com esta rede de Camada 2 estendida, a sub-rede a partir das instalações estará disponível na Nuvem Privada.  Após a migração, não é necessária uma nova atribuição de endereço IP para os VMs.

[Migrar cargas de trabalho usando redes estendidas da Camada 2](migration-layer-2-vpn.md) descreve como usar uma VPN Camada 2 para esticar uma rede De Camada 2 do seu ambiente no local para a sua Nuvem Privada.

## <a name="migrate-vms-using-backup-and-disaster-recovery-tools"></a>Migrar VMs usando ferramentas de backup e recuperação de desastres

A migração de VMs para Private Cloud pode ser feita usando ferramentas de backup/restauro e ferramentas de recuperação de desastres.  Utilize a Nuvem Privada como alvo para restaurar a partir de cópias de segurança criadas usando uma ferramenta de terceiros.  A Nuvem Privada também pode ser usada como um alvo para a recuperação de desastres usando VMware SRM ou uma ferramenta de terceiros.

Para obter mais informações utilizando estas ferramentas, consulte os seguintes tópicos:

* [Fazer o back up máquinas virtuais de carga de trabalho na CloudSimple Private Cloud usando Veeam B&R](backup-workloads-veeam.md)
* [Configurar cloudSimple Private Cloud como um local de recuperação de desastres para cargas de trabalho VMware no local](disaster-recovery-zerto.md)
