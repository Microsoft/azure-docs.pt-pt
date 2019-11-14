---
title: Suporte à matriz-recuperação de desastre do Hyper-V em um site secundário do VMM com Azure Site Recovery
description: Resume o suporte para replicação de VM do Hyper-V em nuvens do VMM para um site secundário com Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/06/2019
ms.author: raynew
ms.openlocfilehash: 76b34e11bba00105792ac5163c5163184c185e09
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74039611"
---
# <a name="support-matrix-for-disaster-recovery-of-hyper-v-vms-to-a-secondary-site"></a>Matriz de suporte para recuperação de desastre de VMs do Hyper-V em um site secundário

Este artigo resume o que tem suporte quando você usa o serviço de [Azure site Recovery](site-recovery-overview.md) para replicar VMs do Hyper-V gerenciadas em nuvens do System Center Virtual Machine Manager (VMM) para um site secundário. Se você quiser replicar VMs do Hyper-V para o Azure, examine [esta matriz de suporte](hyper-v-azure-support-matrix.md).

> [!NOTE]
> Você só pode replicar para um site secundário quando seus hosts Hyper-V são gerenciados em nuvens do VMM.

> [!WARNING]
> Observe que o suporte a ASR para usar a configuração do SCVMM em conta em breve será preterido e, portanto, recomendamos que você leia os detalhes de [substituição](site-to-site-deprecation.md) antes de continuar.


## <a name="host-servers"></a>Servidores host

**Sistema operativo** | **Detalhes**
--- | ---
Windows Server 2012 R2 | Os servidores devem estar executando as atualizações mais recentes.
Windows Server 2016 |  Atualmente, não há suporte para nuvens do VMM 2016 com uma combinação de hosts do Windows Server 2016 e 2012 R2.<br/><br/> Atualmente, não há suporte para implantações atualizadas do System Center 2012 R2 VMM 2012 R2 para o System Center 2016.


## <a name="replicated-vm-support"></a>Suporte à VM replicada

A tabela a seguir resume o suporte do sistema operacional para computadores replicados com o Site Recovery. Qualquer carga de trabalho pode ser executada no sistema operacional com suporte.

**Versão do Windows** | **Hyper-V (com VMM)**
--- | ---
Windows Server 2016 | Qualquer sistema operacional convidado [com suporte do Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/Supported-Windows-guest-operating-systems-for-Hyper-V-on-Windows) no Windows Server 2016 
Windows Server 2012 R2 | Qualquer sistema operacional convidado [com suporte do Hyper-V](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn792027%28v%3dws.11%29) no Windows Server 2012 R2

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


## <a name="storage"></a>Storage

### <a name="host-storage"></a>Armazenamento de host

**Armazenamento (host)** | **Suportado**
--- | --- 
NFS | N/D
SMB 3.0 |  Sim
SAN (ISCSI) | Sim
Vários caminhos (MPIO) | Sim

### <a name="guest-or-physical-server-storage"></a>Armazenamento de servidor físico ou convidado

**Configuração** | **Suportado**
--- | --- | 
VMDK |  N/D
VHD/VHDX | Sim (até 16 discos)
VM de Gen 2 | Sim
Disco de cluster compartilhado | Não
Disco criptografado | Não
UEFI| N/D
NFS | Não
SMB 3.0 | Não
RDM | N/D
Disco > 1 TB | Sim
Volume com disco distribuído > 1 TB<br/><br/> LVM | Sim
Espaços de armazenamento | Sim
Adição/remoção de disco quente | Não
Excluir o disco | Sim
Vários caminhos (MPIO) | Sim

## <a name="vaults"></a>Cofres

**Ação** | **Suportado**
--- | --- 
Mover cofres entre grupos de recursos (dentro de ou entre assinaturas) |  Não
Mover armazenamento, rede, VMs do Azure entre grupos de recursos (dentro de ou entre assinaturas) | Não

## <a name="azure-site-recovery-provider"></a>Provedor de Azure Site Recovery

O provedor coordena as comunicações entre os servidores do VMM. 

**Latest** | **Atualizações**
--- | --- 
5.1.19 ([disponível no portal](https://aka.ms/downloaddra) | [Recursos e correções mais recentes](https://support.microsoft.com/kb/3155002)



## <a name="next-steps"></a>Passos seguintes

[Replicar VMs do Hyper-V em nuvens do VMM para um site secundário](tutorial-vmm-to-vmm.md)

