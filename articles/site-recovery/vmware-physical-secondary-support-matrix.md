---
title: Suporte para recuperação de VMware/desastre físico para um local secundário com recuperação do site Azure
description: Resume o suporte para a recuperação de desastres de VMware VMs e servidores físicos para um site secundário com recuperação do site Azure.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: c6f0f48df32db0beb9c0a57982d9bc87b26538d8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86135143"
---
# <a name="support-matrix-for-disaster-recovery-of-vmware-vms-and-physical-servers-to-a-secondary-site"></a>Matriz de suporte para recuperação de desastres de VMware VMs e servidores físicos para um site secundário

Este artigo resume o que é suportado quando utiliza o serviço de Recuperação do [Site Azure](site-recovery-overview.md) para a recuperação de desastres de VMware VMs ou servidores físicos Windows/Linux para um site secundário de VMware.

- Se pretender replicar VMware VMs ou servidores físicos para a Azure, [reveja esta matriz de suporte](vmware-physical-azure-support-matrix.md).
- Se pretender replicar Os Hiper-VMs para um site secundário, [reveja esta matriz de suporte](hyper-v-azure-support-matrix.md).

> [!NOTE]
> A replicação de VMware VMware e servidores físicos no local é fornecida pela InMage Scout. O InMage Scout está incluído na subscrição do serviço de recuperação do site Azure.

## <a name="end-of-support-announcement"></a>Anúncio do fim do suporte
O cenário de recuperação do site para replicação entre VMware ou centros de dados físicos está a atingir o fim do suporte.

- A partir de agosto de 2018, o cenário não pode ser configurado no cofre dos Serviços de Recuperação, e o software InMage Scout não pode ser descarregado do cofre. As implementações existentes serão apoiadas.
- - A partir de 31 de dezembro de 2020, o cenário não será apoiado.
Os parceiros existentes podem embarcar novos clientes para o cenário até que o suporte termine.
- Durante 2018 e 2019, serão lançadas duas atualizações:

    - Atualização 7: Corrige problemas de configuração e conformidade da rede e fornece suporte TLS 1.2.
    - Atualização 8: Adiciona suporte aos sistemas operativos Linux RHEL/CentOS 7.3/7.4/7.5 e ao SUSE 12
    - Depois da Atualização 8, não serão lançadas mais atualizações. Haverá um suporte limitado de hotfix para os sistemas operativos adicionados no Update 8, e correções de bugs com base no melhor esforço.

## <a name="host-servers"></a>Servidores anfitriões

**Sistema operativo** | **Detalhes**
--- | ---
vCenter Server | vCenter 5.5, 6.0 e 6.5<br/><br/> Se correr 6.0 ou 6.5, note que apenas 5.5 funcionalidades são suportadas.


## <a name="replicated-vm-support"></a>Suporte VM replicado

A tabela seguinte resume o suporte do sistema operativo para máquinas replicadas com a Recuperação do Local. Qualquer carga de trabalho pode estar a funcionar no sistema operativo suportado.

**Sistema operativo** | **Detalhes**
--- | ---
Windows Server | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 com pelo menos SP1.
Linux | Red Hat Enterprise Linux 6.7, 6.8, 6.9, 7.1, 7.2 <br/><br/> Centos 6.5, 6.6, 6.7, 6.8, 6.9, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5, 6.8 com o núcleo compatível com o Chapéu Vermelho, ou Unbreakable Enterprise Kernel Release 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3, 11 SP4 


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
Anfitrião - EQUIPA NIC | Sim 
Anfitrião - VLAN | Sim 
Anfitrião - IPv4 | Sim 
Anfitrião - IPv6 | Não 
VM convidado - EQUIPA NIC | Não
VM convidado - IPv4 | Sim
VM convidado - IPv6 | Não
VM convidado - Windows/Linux - Endereço IP estático | Sim
VM convidado - Multi-NIC | Sim


## <a name="storage"></a>Armazenamento

### <a name="host-storage"></a>Armazenamento de hospedeiros

**Armazenamento (hospedeiro)** | **Suportado** 
--- | --- 
NFS | Sim 
SMB 3.0 | N/D 
SAN (ISCSI) | Sim 
Multi-caminhos (MPIO) | Sim 

### <a name="guest-or-physical-server-storage"></a>Armazenamento de servidores físicos ou de hóspedes

**Configuração** | **Suportado** 
--- | --- 
VMDK | Sim 
VHD/VHDX | N/D 
Gen 2 VM | N/D 
Disco de cluster compartilhado | Sim 
Disco encriptado | Não 
UEFI| Sim 
NFS | Não 
SMB 3.0 | Não 
RDM | Sim 
Disco > 1 TB | Sim 
Volume com disco listrado > 1 TB<br/><br/> LVM | Sim 
Espaços de Armazenamento | Não 
Disco de adicionar/remover quente | Sim 
Excluir o disco | Sim 
Multi-caminhos (MPIO) | N/D 

## <a name="vaults"></a>Cofres

**Ação** | **Suportado** 
--- | --- 
Mover cofres através de grupos de recursos (dentro ou em todas as subscrições) | Não 
Mover armazenamento, rede, VMs Azure em grupos de recursos (dentro ou em todas as subscrições) | Não 

## <a name="mobility-service-and-updates"></a>Serviço de mobilidade e atualizações

O serviço Mobility coordena a replicação entre servidores VMware no local ou servidores físicos e o site secundário. Quando configurar a replicação, deve certificar-se de que tem a versão mais recente do serviço mobility, e de outros componentes.

| **Atualizar** | **Detalhes** |
| --- | --- |
|Atualizações de escuteiros | As atualizações dos escuteiros são cumulativas. <br/><br/> [Saiba e baixe](vmware-physical-secondary-disaster-recovery.md#updates) as últimas atualizações do Scout |
|Atualizações de componentes | As atualizações do Scout incluem atualizações para todos os componentes, incluindo o servidor RX, servidor de configuração, servidores de processo e alvo principal, servidores vContinuum e servidores de origem que pretende proteger.<br/><br/> [Saiba mais](vmware-physical-secondary-disaster-recovery.md#download-and-install-component-updates).|


## <a name="next-steps"></a>Passos seguintes

Descarregue o [guia de utilizador inMage Scout](https://aka.ms/asr-scout-user-guide)

- [Replicar VMs hiper-V em nuvens VMM para um local secundário](./hyper-v-vmm-disaster-recovery.md)
- [Replicar VMs do VMware e servidores físicos para um site secundário](./vmware-physical-secondary-disaster-recovery.md)
