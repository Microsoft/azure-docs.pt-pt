---
title: Matriz de suporte para recuperação de desastre de VMs VMware ou servidores físicos em um site VMware secundário com Azure Site Recovery | Microsoft Docs
description: Resume o suporte para recuperação de desastre de VMs VMware e servidores físicos em um site secundário com Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 08/22/2019
ms.author: raynew
ms.openlocfilehash: c330afb2c5d315b3d386d1477669f1aab2f6e6f9
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972083"
---
# <a name="support-matrix-for-disaster-recovery-of-vmware-vms-and-physical-servers-to-a-secondary-site"></a>Matriz de suporte para recuperação de desastre de VMs VMware e servidores físicos em um site secundário

Este artigo resume o que tem suporte quando você usa o serviço de [Azure site Recovery](site-recovery-overview.md) para recuperação de desastre de VMs VMware ou servidores físicos Windows/Linux para um site secundário do VMware.

- Se você quiser replicar VMs VMware ou servidores físicos para o Azure, examine [esta matriz de suporte](vmware-physical-azure-support-matrix.md).
- Se você quiser replicar VMs do Hyper-V para um site secundário, examine [esta matriz de suporte](hyper-v-azure-support-matrix.md).

> [!NOTE]
> A replicação de VMs VMware locais e servidores físicos é fornecida pelo InMage Scout. O InMage Scout está incluído na assinatura do serviço Azure Site Recovery.


## <a name="host-servers"></a>Servidores host

**Sistema operativo** | **Detalhes**
--- | ---
vCenter Server | vCenter 5,5, 6,0 e 6,5<br/><br/> Se você executar 6,0 ou 6,5, observe que somente os recursos do 5,5 têm suporte.


## <a name="replicated-vm-support"></a>Suporte à VM replicada

A tabela a seguir resume o suporte do sistema operacional para computadores replicados com o Site Recovery. Qualquer carga de trabalho pode ser executada no sistema operacional com suporte.

**Sistema operativo** | **Detalhes**
--- | ---
Windows Server | 64-bit Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 com pelo menos SP1.
Linux | Red Hat Enterprise Linux 6,7, 6,8, 6,9, 7,1, 7,2 <br/><br/> Centos 6.5, 6.6, 6.7, 6.8, 6.9, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6,4, 6,5, 6,8 executando o kernel compatível com Red Hat ou o UEK3 (inquebrable Enterprise kernel versão 3) <br/><br/> SUSE Linux Enterprise Server 11 SP3, 11 SP4 


## <a name="linux-machine-storage"></a>Armazenamento do computador Linux

Somente computadores Linux com o seguinte armazenamento podem ser replicados:

- Sistema de arquivos (EXT3, ETX4, ReiserFS, XFS).
- Software de vários caminhos – mapeador de dispositivo.
- Gerenciador de volumes (LVM2).
- Não há suporte para servidores físicos com o armazenamento do controlador HP CCISS.
- O sistema de arquivos ReiserFS tem suporte apenas no SUSE Linux Enterprise Server 11 SP3.

## <a name="network-configuration---hostguest-vm"></a>Configuração de rede-VM de host/convidado

**Configuração** | **Suportado**  
--- | --- 
Agrupamento do host NIC | Sim 
Host-VLAN | Sim 
Host-IPv4 | Sim 
Host - IPv6 | Não 
VM convidada-agrupamento NIC | Não
VM convidada-IPv4 | Sim
VM convidada-IPv6 | Não
VM convidada-Windows/Linux-endereço IP estático | Sim
VM convidada-multi-NIC | Sim


## <a name="storage"></a>Armazenamento

### <a name="host-storage"></a>Armazenamento de host

**Armazenamento (host)** | **Suportado** 
--- | --- 
NFS | Sim 
SMB 3.0 | N/A 
SAN (ISCSI) | Sim 
Vários caminhos (MPIO) | Sim 

### <a name="guest-or-physical-server-storage"></a>Armazenamento de servidor físico ou convidado

**Configuração** | **Suportado** 
--- | --- 
VMDK | Sim 
VHD/VHDX | N/A 
VM de Gen 2 | N/A 
Disco de cluster compartilhado | Sim 
Disco criptografado | Não 
UEFI| Sim 
NFS | Não 
SMB 3.0 | Não 
RDM | Sim 
Disco > 1 TB | Sim 
Volume com disco distribuído > 1 TB<br/><br/> LVM | Sim 
Espaços de armazenamento | Não 
Adição/remoção de disco quente | Sim 
Excluir o disco | Sim 
Vários caminhos (MPIO) | N/A 

## <a name="vaults"></a>Cofres

**ação** | **Suportado** 
--- | --- 
Mover cofres entre grupos de recursos (dentro de ou entre assinaturas) | Não 
Mover armazenamento, rede, VMs do Azure entre grupos de recursos (dentro de ou entre assinaturas) | Não 

## <a name="mobility-service-and-updates"></a>Serviço de mobilidade e atualizações

O serviço de mobilidade coordena a replicação entre servidores VMware locais ou servidores físicos e o site secundário. Ao configurar a replicação, você deve verificar se tem a versão mais recente do serviço de mobilidade e de outros componentes.

| **Atualização** | **Detalhes** |
| --- | --- |
|Atualizações do Scout | As atualizações do Scout são cumulativas. <br/><br/> [Saiba mais e baixe](vmware-physical-secondary-disaster-recovery.md#updates) as atualizações mais recentes do Scout |
|Atualizações de componentes | As atualizações do Scout incluem atualizações para todos os componentes, incluindo o servidor RX, servidor de configuração, servidores de destino mestre e de processo, servidores vContinuum e servidores de origem que você deseja proteger.<br/><br/> [Saiba mais](vmware-physical-secondary-disaster-recovery.md#download-and-install-component-updates).|


## <a name="next-steps"></a>Passos Seguintes

Baixe o [Guia do usuário do InMage Scout](https://aka.ms/asr-scout-user-guide)

- [Replicar VMs do Hyper-V em nuvens do VMM para um site secundário](tutorial-vmm-to-vmm.md)
- [Replicar VMs VMware e servidores físicos para um site secundário](tutorial-vmware-to-vmware.md)
