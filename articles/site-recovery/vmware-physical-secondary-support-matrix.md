---
title: Suporte para recuperação de VMware/desastre físico para um local secundário com recuperação do site Azure
description: Resume o suporte para recuperação de desastres de VMware VMs e servidores físicos para um site secundário com Recuperação do Site Azure.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: cabd3f7693c6b6b86bf0324bdafdfe1377d1ece8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256799"
---
# <a name="support-matrix-for-disaster-recovery-of-vmware-vms-and-physical-servers-to-a-secondary-site"></a>Matriz de suporte para recuperação de desastres de VMware VMs e servidores físicos para um site secundário

Este artigo resume o que é suportado quando utiliza o serviço de recuperação de [sites Azure](site-recovery-overview.md) para recuperação de desastres de VMware VMs ou servidores físicos Windows/Linux para um site de VMware secundário.

- Se quiser replicar VMs VMware ou servidores físicos para o Azure, reveja [esta matriz](vmware-physical-azure-support-matrix.md)de suporte .
- Se quiser replicar os VMs Hiper-V para um site secundário, reveja [esta matriz](hyper-v-azure-support-matrix.md)de suporte .

> [!NOTE]
> A replicação de VMware VMs no local e servidores físicos é fornecida pela InMage Scout. O InMage Scout está incluído na subscrição do serviço de recuperação do site Azure.

## <a name="end-of-support-announcement"></a>Anúncio do fim do suporte
O cenário de recuperação do site para replicação entre VMware no local ou datacenters físicos está a atingir o fim do suporte.

- A partir de agosto de 2018, o cenário não pode ser configurado no cofre dos Serviços de Recuperação, e o software InMage Scout não pode ser descarregado do cofre. As missões existentes serão apoiadas.
- - A partir de 31 de dezembro de 2020, o cenário não será apoiado.
Os parceiros existentes podem embarcar novos clientes para o cenário até que o suporte termine.
- Durante 2018 e 2019, serão lançadas duas atualizações:

    - Atualização 7: Corrige problemas de configuração e conformidade da rede e fornece suporte TLS 1.2.
    - Atualização 8: Adiciona suporte aos sistemas operativos Linux RHEL/CentOS 7.3/7.4/7.5 e para SUSE 12
    - Após a Atualização 8, não serão lançadas mais atualizações. Haverá um suporte de fixação de fixação limitado para os sistemas operativos adicionados no Update 8, e correções de bugs com base no melhor esforço.

## <a name="host-servers"></a>Servidores de anfitriões

**Sistema Operativo** | **Detalhes**
--- | ---
vCenter Server | vCenter 5.5, 6.0 e 6.5<br/><br/> Se executar 6.0 ou 6.5, note que apenas 5.5 funcionalidades são suportadas.


## <a name="replicated-vm-support"></a>Suporte vm replicado

A tabela que se segue resume o suporte do sistema operativo para máquinas replicadas com a Recuperação do Local. Qualquer carga de trabalho pode estar a funcionar no sistema operativo suportado.

**Sistema Operativo** | **Detalhes**
--- | ---
Windows Server | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 com pelo menos SP1.
Linux | Red Hat Enterprise Linux 6.7, 6.8, 6.9, 7.1, 7.2 <br/><br/> Centos 6.5, 6.6, 6.7, 6.8, 6.9, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5, 6.8 executando o kernel compatível com o Chapéu Vermelho, ou Unbreakable Enterprise Kernel Release 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3, 11 SP4 


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
NFS | Sim 
SMB 3.0 | N/D 
SAN (ISCSI) | Sim 
Multi-caminho (MPIO) | Sim 

### <a name="guest-or-physical-server-storage"></a>Armazenamento de servidores de hóspedes ou físicos

**Configuração** | **Apoiado** 
--- | --- 
VMDK | Sim 
VHD/VHDX | N/D 
Gen 2 VM | N/D 
Disco de cluster partilhado | Sim 
Disco encriptado | Não 
UEFI| Sim 
NFS | Não 
SMB 3.0 | Não 
RDM | Sim 
Disco > 1 TB | Sim 
Volume com disco listrado > 1 TB<br/><br/> LVM | Sim 
Espaços de Armazenamento | Não 
Disco de adição/remoção quente | Sim 
Excluir o disco | Sim 
Multi-caminho (MPIO) | N/D 

## <a name="vaults"></a>Cofres

**Ação** | **Apoiado** 
--- | --- 
Mover cofres através de grupos de recursos (dentro ou através de subscrições) | Não 
Movimentar armazenamento, rede, VMs Azure através de grupos de recursos (dentro ou em todas as subscrições) | Não 

## <a name="mobility-service-and-updates"></a>Serviço de mobilidade e atualizações

O serviço mobility coordena a replicação entre servidores VMware no local ou servidores físicos, e o site secundário. Ao configurar a replicação, deve certificar-se de que tem a versão mais recente do serviço mobility e de outros componentes.

| **Atualização** | **Detalhes** |
| --- | --- |
|Atualizações de escuteiros | As atualizações dos escuteiros são cumulativas. <br/><br/> [Saiba e descarregue](vmware-physical-secondary-disaster-recovery.md#updates) as últimas atualizações de Scout |
|Atualizações de componentes | As atualizações de scout incluem atualizações para todos os componentes, incluindo o servidor RX, servidor de configuração, servidor de processos e servidores-alvo principais, servidores vContinuum e servidores de origem que pretende proteger.<br/><br/> [Saiba mais](vmware-physical-secondary-disaster-recovery.md#download-and-install-component-updates).|


## <a name="next-steps"></a>Passos seguintes

Descarregue o guia de [utilizador do InMage Scout](https://aka.ms/asr-scout-user-guide)

- [Replicar VMs hiper-V em nuvens vmm para um local secundário](tutorial-vmm-to-vmm.md)
- [Replicar VMs do VMware e servidores físicos para um site secundário](tutorial-vmware-to-vmware.md)
