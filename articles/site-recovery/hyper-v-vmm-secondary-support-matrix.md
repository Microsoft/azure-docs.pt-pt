---
title: Nuvens de matriz de suporte para recuperação após desastre de VMs de Hyper-V no VMM para um site secundário com o Azure Site Recovery | Documentos da Microsoft
description: Resume o suporte para replicação de VMS de Hyper-V em clouds do VMM para um site secundário com o Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: raynew
ms.openlocfilehash: 2b801df6036566840dd96bde0801ab018d0614b5
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58103099"
---
# <a name="support-matrix-for-disaster-recovery-of-hyper-v-vms-to-a-secondary-site"></a>Matriz de suporte para recuperação após desastre de VMs de Hyper-V para um site secundário

Este artigo resume o que é suportado quando utiliza a [do Azure Site Recovery](site-recovery-overview.md) serviço para replicar VMs de Hyper-V geridas em clouds do System Center Virtual Machine Manager (VMM) para um site secundário. Se pretender replicar VMs Hyper-V para o Azure, reveja [esta matriz de suporte](hyper-v-azure-support-matrix.md).

> [!NOTE]
> Pode replicar apenas para um site secundário quando os anfitriões de Hyper-V geridos em clouds do VMM.

  

## <a name="host-servers"></a>Servidores de anfitrião

**Sistema operativo** | **Detalhes**
--- | ---
Windows Server 2012 R2 | Servidores tem de executar as atualizações mais recentes.
Windows Server 2016 |  Nuvens do VMM 2016 com uma combinação de Windows Server 2016 e 2012 R2 anfitriões não são atualmente suportados.<br/><br/> Implementações de atualização do System Center 2012 R2 VMM 2012 R2 para o System Center 2016 não são atualmente suportadas.


## <a name="replicated-vm-support"></a>Replicados suporte VM

A tabela seguinte resume o suporte do sistema operativo para máquinas replicadas com o Site Recovery. Qualquer carga de trabalho pode ser executado no sistema operativo suportado.

**Versão do Windows** | **Hyper-V (com VMM)**
--- | ---
Windows Server 2016 | Qualquer sistema operativo de convidado [suportados pelo Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/Supported-Windows-guest-operating-systems-for-Hyper-V-on-Windows) no Windows Server 2016 
Windows Server 2012 R2 | Qualquer sistema operativo de convidado [suportados pelo Hyper-V](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn792027%28v%3dws.11%29) no Windows Server 2012 R2

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
NFS | N/A
SMB 3.0 |  Sim
SAN (ISCSI) | Sim
Múltiplos caminhos (o MPIO) | Sim

### <a name="guest-or-physical-server-storage"></a>Armazenamento de servidor físico ou de convidado

**Configuração** | **Suportado**
--- | --- | 
VMDK |  N/A
VHD/VHDX | Sim (até 16 discos)
Ger 2 VM | Sim
Partilhado o disco de cluster | Não
Disco encriptado | Não
UEFI| N/A
NFS | Não
SMB 3.0 | Não
RDM | N/A
Disco > 1 TB | Sim
Volume com o disco repartido > 1 TB<br/><br/> LVM | Sim
Espaços de armazenamento | Sim
Disco de acesso frequente Adicionar/remover | Não
Excluir o disco | Sim
Múltiplos caminhos (o MPIO) | Sim

## <a name="vaults"></a>Cofres

**Ação** | **Suportado**
--- | --- 
Cofres de movimentação entre grupos de recursos (dentro ou entre subscrições) |  Não
Mover o armazenamento, rede, as VMs do Azure entre grupos de recursos (dentro ou entre subscrições) | Não

## <a name="azure-site-recovery-provider"></a>Fornecedor do Azure Site Recovery

O fornecedor coordena as comunicações entre servidores do VMM. 

**Latest** | **Atualizações**
--- | --- 
5.1.19 ([disponível no portal](https://aka.ms/downloaddra) | [Funcionalidades e correções mais recentes](https://support.microsoft.com/kb/3155002)



## <a name="next-steps"></a>Passos Seguintes

[Replicar VMs de Hyper-V em clouds do VMM para um site secundário](tutorial-vmm-to-vmm.md)

