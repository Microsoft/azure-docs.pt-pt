---
title: Matriz de suporte para recuperação após desastre de VMs de VMware ou servidores físicos para um site de VMware secundário com o Azure Site Recovery | Documentos da Microsoft
description: Resume o suporte para recuperação após desastre de VMs de VMware e servidores físicos para um site secundário com o Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 12/31/2018
ms.author: raynew
ms.openlocfilehash: 2f70a180afcc8eda5d43356c3f4f96b25292c6d5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58088932"
---
# <a name="support-matrix-for-disaster-recovery-of-vmware-vms-and-physical-servers-to-a-secondary-site"></a>Matriz de suporte para recuperação após desastre de VMs de VMware e servidores físicos para um site secundário

Este artigo resume o que é suportado quando utiliza a [do Azure Site Recovery](site-recovery-overview.md) de serviço servidores físicos para um site de VMware secundário para recuperação após desastre de VMs de VMware ou Windows/Linux.

- Se pretender replicar VMs de VMware ou servidores físicos para o Azure, reveja [esta matriz de suporte](vmware-physical-azure-support-matrix.md).
- Se pretender replicar VMs de Hyper-V para um site secundário, consulte [esta matriz de suporte](hyper-v-azure-support-matrix.md).

> [!NOTE]
> Replicação de VMs de VMware no local e servidores físicos é fornecida pelo InMage Scout. InMage Scout está incluído na subscrição de serviço do Azure Site Recovery.


## <a name="host-servers"></a>Servidores de anfitrião

**Sistema operativo** | **Detalhes**
--- | ---
vCenter Server | vCenter 5.5, 6.0 e 6.5<br/><br/> Se executar 6.0 ou 6.5, tenha em atenção que apenas 5.5 funcionalidades são suportadas.


## <a name="replicated-vm-support"></a>Replicados suporte VM

A tabela seguinte resume o suporte do sistema operativo para máquinas replicadas com o Site Recovery. Qualquer carga de trabalho pode ser executado no sistema operativo suportado.

**Sistema operativo** | **Detalhes**
--- | ---
Windows Server | Windows Server 2016 de 64 bits, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 com em, pelo menos, SP1.
Linux | Red Hat Enterprise Linux 6.7, 6.8, 6.9, 7.1, 7.2 <br/><br/> Centos 6.5, 6.6, 6.7, 6.8, 6.9, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5, 6.8 a executar o kernel compatível do Red Hat ou Indestrutíveis Enterprise Kernel versão 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3, 11 SP4 


## <a name="linux-machine-storage"></a>Armazenamento de máquina do Linux

Apenas as máquinas do Linux com o armazenamento seguinte podem ser replicadas:

- Sistema (EXT3, ETX4, ReiserFS, XFS) de ficheiros.
- Mapeador de pontos de software MultiPath i-dispositivo.
- Gestor de volumes (LVM2).
- Servidores físicos com o armazenamento de controlador do HP CCISS não são suportados.
- O sistema de ficheiros ReiserFS é suportado apenas no SUSE Linux Enterprise Server 11 SP3.

## <a name="network-configuration---hostguest-vm"></a>Configuração de rede - Host/convidado de VM

**Configuração** | **Suportado**  
--- | --- 
Anfitrião - agrupamento NIC | Sim 
Host - VLAN | Sim 
Host - IPv4 | Sim 
Host - IPv6 | Não 
Os convidados de VM - agrupamento NIC | Não
Convidado VM - IPv4 | Sim
VM - IPv6 convidado | Não
Endereço de IP estático de VM Windows/Linux – de convidado | Sim
VM do convidado - Multi-NIC | Sim


## <a name="storage"></a>Armazenamento

### <a name="host-storage"></a>Armazenamento de anfitrião

**Armazenamento (anfitrião)** | **Suportado** 
--- | --- 
NFS | Sim 
SMB 3.0 | N/A 
SAN (ISCSI) | Sim 
Múltiplos caminhos (o MPIO) | Sim 

### <a name="guest-or-physical-server-storage"></a>Armazenamento de servidor físico ou de convidado

**Configuração** | **Suportado** 
--- | --- 
VMDK | Sim 
VHD/VHDX | N/A 
Ger 2 VM | N/A 
Partilhado o disco de cluster | Sim 
Disco encriptado | Não 
UEFI| Sim 
NFS | Não 
SMB 3.0 | Não 
RDM | Sim 
Disco > 1 TB | Sim 
Volume com o disco repartido > 1 TB<br/><br/> LVM | Sim 
Espaços de armazenamento | Não 
Disco de acesso frequente Adicionar/remover | Sim 
Excluir o disco | Sim 
Múltiplos caminhos (o MPIO) | N/A 

## <a name="vaults"></a>Cofres

**Ação** | **Suportado** 
--- | --- 
Cofres de movimentação entre grupos de recursos (dentro ou entre subscrições) | Não 
Mover o armazenamento, rede, as VMs do Azure entre grupos de recursos (dentro ou entre subscrições) | Não 

## <a name="mobility-service-and-updates"></a>O serviço de mobilidade e atualizações

O serviço de mobilidade coordena a replicação entre servidores de VMware no local ou servidores físicos e o site secundário. Quando configurar a replicação, certifique-se de que tem a versão mais recente do serviço de mobilidade e de outros componentes.

| **Atualização** | **Detalhes** |
| --- | --- |
|Atualizações do Scout | Scout atualizações são cumulativas. <br/><br/> [Conheça e baixe](vmware-physical-secondary-disaster-recovery.md#updates) as atualizações mais recentes do Scout |
|Atualizações de componentes | Scout atualizações incluem atualizações para todos os componentes, incluindo o servidor do RX, servidor de configuração, processo e servidores de destino mestre, servidores de vContinuum e servidores de origem que pretende proteger.<br/><br/> [Saiba mais](vmware-physical-secondary-disaster-recovery.md#download-and-install-component-updates).|


## <a name="next-steps"></a>Passos Seguintes

Transferir o [Guia do usuário do InMage Scout](https://aka.ms/asr-scout-user-guide)

- [Replicar VMs de Hyper-V em clouds do VMM para um site secundário](tutorial-vmm-to-vmm.md)
- [Replicar VMs VMware e servidores físicos para um site secundário](tutorial-vmware-to-vmware.md)
