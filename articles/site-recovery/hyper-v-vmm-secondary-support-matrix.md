---
title: Suporte a recuperação de desastres matrix-Hiper-V para um site secundário de VMM com recuperação do local de Azure
description: Resume o suporte para a replicação do Hiper-VM nas nuvens VMM para um local secundário com recuperação do local de Azure.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/06/2019
ms.author: raynew
ms.openlocfilehash: af7baf413c9054ef3e5bf527851ac06c113cdce7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "86131173"
---
# <a name="support-matrix-for-disaster-recovery-of-hyper-v-vms-to-a-secondary-site"></a>Matriz de suporte para recuperação após desastre de VMs Hyper-V para um site secundário

Este artigo resume o que é suportado quando utiliza o serviço de Recuperação do [Site Azure](site-recovery-overview.md) para replicar VMs Hiper-V geridos em nuvens do System Center Virtual Machine Manager (VMM) para um site secundário. Se pretender replicar Os Hiper-VMs para Azure, [reveja esta matriz de suporte](hyper-v-azure-support-matrix.md).

> [!NOTE]
> Só é possível replicar para um local secundário quando os seus anfitriões Hiper-V são geridos em nuvens VMM.


## <a name="host-servers"></a>Servidores anfitriões

**Sistema operativo** | **Detalhes**
--- | ---
Windows Server 2012 R2 | Os servidores devem estar a executar as últimas atualizações.
Windows Server 2016 |  As nuvens VMM 2016 com uma mistura de anfitriões Windows Server 2016 e 2012 R2 não são suportadas atualmente.<br/><br/> As implementações que foram atualizadas do System Center 2012 R2 VMM 2012 R2 para System Center 2016 não são suportadas atualmente.


## <a name="replicated-vm-support"></a>Suporte VM replicado

A tabela seguinte resume o suporte do sistema operativo para máquinas replicadas com a Recuperação do Local. Qualquer carga de trabalho pode estar a funcionar no sistema operativo suportado.

**Versão do Windows** | **Hiper-V (com VMM)**
--- | ---
Windows Server 2016 | Qualquer sistema operativo [de hóspedes suportado por Hyper-V](/windows-server/virtualization/hyper-v/Supported-Windows-guest-operating-systems-for-Hyper-V-on-Windows) no Windows Server 2016 
Windows Server 2012 R2 | Qualquer sistema operativo [de hóspedes suportado por Hyper-V](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn792027%28v%3dws.11%29) no Windows Server 2012 R2

## <a name="linux-machine-storage"></a>Armazenamento de máquinas Linux

Apenas as máquinas Linux com o seguinte armazenamento podem ser replicadas:

- Sistema de ficheiros (EXT3, ETX4, ReiserFS, XFS).
- Dispositivo de software multi-path Mapper.
- Gestor de volume (LVM2).
- Os servidores físicos com armazenamento do controlador HP CCISS não são suportados.
- O sistema de ficheiros ReiserFS é suportado apenas no SUSE Linux Enterprise Server 11 SP3.

## <a name="network-configuration---hostguest-vm"></a>Configuração da rede - Host/Guest VM

**Configuração** | **Suportado**  
--- | --- 
Anfitrião - EQUIPA NIC | Yes 
Anfitrião - VLAN | Yes 
Anfitrião - IPv4 | Yes 
Anfitrião - IPv6 | No 
VM convidado - EQUIPA NIC | No
VM convidado - IPv4 | Yes
VM convidado - IPv6 | No
VM convidado - Windows/Linux - Endereço IP estático | Yes
VM convidado - Multi-NIC | Sim


## <a name="storage"></a>Armazenamento

### <a name="host-storage"></a>Armazenamento de hospedeiros

**Armazenamento (hospedeiro)** | **Suportado**
--- | --- 
NFS | N/D
SMB 3.0 |  Yes
SAN (ISCSI) | Yes
Multi-caminhos (MPIO) | Yes

### <a name="guest-or-physical-server-storage"></a>Armazenamento de servidores físicos ou de hóspedes

**Configuração** | **Suportado**
--- | --- | 
VMDK |  N/D
VHD/VHDX | Sim (até 16 discos)
Gen 2 VM | Yes
Disco de cluster compartilhado | No
Disco encriptado | No
UEFI| N/D
NFS | No
SMB 3.0 | No
RDM | N/D
Disco > 1 TB | Yes
Volume com disco listrado > 1 TB<br/><br/> LVM | Yes
Espaços de Armazenamento | Yes
Disco de adicionar/remover quente | No
Excluir o disco | Yes
Multi-caminhos (MPIO) | Yes

## <a name="vaults"></a>Cofres

**Ação** | **Suportado**
--- | --- 
Mover cofres através de grupos de recursos (dentro ou em todas as subscrições) |  No
Mover armazenamento, rede, VMs Azure em grupos de recursos (dentro ou em todas as subscrições) | No

## <a name="azure-site-recovery-provider"></a>Fornecedor de recuperação do site Azure

O Fornecedor coordena as comunicações entre servidores VMM. 

**Últimas** | **Updates**
--- | --- 
5.1.19 ([disponível no portal](https://aka.ms/downloaddra) | [Funcionalidades e correções mais recentes](https://support.microsoft.com/kb/3155002)



## <a name="next-steps"></a>Passos seguintes

[Replicar VMs hiper-V em nuvens VMM para um local secundário](./hyper-v-vmm-disaster-recovery.md)
