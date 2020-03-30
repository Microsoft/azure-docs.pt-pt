---
title: Suporte a recuperação de desastres matriciais-Hiper-V para um site secundário de VMM com recuperação do site Azure
description: Resume o suporte para a replicação de VM Hiper-V em nuvens VMM para um local secundário com recuperação do site Azure.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/06/2019
ms.author: raynew
ms.openlocfilehash: 1126a85ed22ee17879767a93ca75dc76dd04b747
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74132959"
---
# <a name="support-matrix-for-disaster-recovery-of-hyper-v-vms-to-a-secondary-site"></a>Matriz de suporte para recuperação após desastre de VMs Hyper-V para um site secundário

Este artigo resume o que é suportado quando utiliza o serviço de recuperação de [sítios Azure](site-recovery-overview.md) para replicar VMs Hiper-V geridos em nuvens de Máquina virtual do System Center (VMM) para um local secundário. Se quiser replicar VMs Hiper-V para Azure, reveja [esta matriz](hyper-v-azure-support-matrix.md)de suporte .

> [!NOTE]
> Você só pode replicar para um local secundário quando os seus anfitriões Hyper-V são geridos em nuvens VMM.


## <a name="host-servers"></a>Servidores de anfitriões

**Sistema Operativo** | **Detalhes**
--- | ---
Windows Server 2012 R2 | Os servidores devem estar a executar as últimas atualizações.
Windows Server 2016 |  As nuvens VMM 2016 com uma mistura de anfitriões Do Windows Server 2016 e 2012 R2 não são suportadas atualmente.<br/><br/> As implementações que foram atualizadas do System Center 2012 R2 VMM 2012 R2 para system center 2016 não são suportadas atualmente.


## <a name="replicated-vm-support"></a>Suporte vm replicado

A tabela que se segue resume o suporte do sistema operativo para máquinas replicadas com a Recuperação do Local. Qualquer carga de trabalho pode estar a funcionar no sistema operativo suportado.

**Versão do Windows** | **Hiper-V (com VMM)**
--- | ---
Windows Server 2016 | Qualquer sistema operativo de hóspedes [suportado por Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/Supported-Windows-guest-operating-systems-for-Hyper-V-on-Windows) no Windows Server 2016 
Windows Server 2012 R2 | Qualquer sistema operativo de hóspedes [suportado por Hyper-V](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn792027%28v%3dws.11%29) no Windows Server 2012 R2

## <a name="linux-machine-storage"></a>Armazenamento de máquinas Linux

Só podem ser replicadas máquinas Linux com o seguinte armazenamento:

- Sistema de ficheiros (EXT3, ETX4, ReiserFS, XFS).
- Mapper, dispositivo de software multipath.
- Gestor de volume (LVM2).
- Os servidores físicos com armazenamento do controlador HP CCISS não são suportados.
- O sistema de ficheiros ReiserFS é suportado apenas no SUSE Linux Enterprise Server 11 SP3.

## <a name="network-configuration---hostguest-vm"></a>Configuração da rede - Host/Guest VM

**Configuração** | **Apoiado**  
--- | --- 
Anfitrião - Equipa NIC | Sim 
Anfitrião - VLAN | Sim 
Anfitrião - IPv4 | Sim 
Anfitrião - IPv6 | Não 
Guest VM - EQUIPA NIC | Não
Guest VM - IPv4 | Sim
Guest VM - IPv6 | Não
VM convidado - Windows/Linux - Endereço IP estático | Sim
VM convidado - Multi-NIC | Sim


## <a name="storage"></a>Storage

### <a name="host-storage"></a>Armazenamento de hospedeiro

**Armazenamento (hospedeiro)** | **Apoiado**
--- | --- 
NFS | N/D
SMB 3.0 |  Sim
SAN (ISCSI) | Sim
Multi-caminho (MPIO) | Sim

### <a name="guest-or-physical-server-storage"></a>Armazenamento de servidores de hóspedes ou físicos

**Configuração** | **Apoiado**
--- | --- | 
VMDK |  N/D
VHD/VHDX | Sim (até 16 discos)
Gen 2 VM | Sim
Disco de cluster partilhado | Não
Disco encriptado | Não
UEFI| N/D
NFS | Não
SMB 3.0 | Não
RDM | N/D
Disco > 1 TB | Sim
Volume com disco listrado > 1 TB<br/><br/> LVM | Sim
Espaços de Armazenamento | Sim
Disco de adição/remoção quente | Não
Excluir o disco | Sim
Multi-caminho (MPIO) | Sim

## <a name="vaults"></a>Cofres

**Ação** | **Apoiado**
--- | --- 
Mover cofres através de grupos de recursos (dentro ou através de subscrições) |  Não
Movimentar armazenamento, rede, VMs Azure através de grupos de recursos (dentro ou em todas as subscrições) | Não

## <a name="azure-site-recovery-provider"></a>Fornecedor de recuperação de sítios azure

O Fornecedor coordena as comunicações entre servidores VMM. 

**Últimas** | **Updates**
--- | --- 
5.1.19[(disponível no portal](https://aka.ms/downloaddra) | [Últimas funcionalidades e correções](https://support.microsoft.com/kb/3155002)



## <a name="next-steps"></a>Passos seguintes

[Replicar VMs hiper-V em nuvens vmm para um local secundário](tutorial-vmm-to-vmm.md)

