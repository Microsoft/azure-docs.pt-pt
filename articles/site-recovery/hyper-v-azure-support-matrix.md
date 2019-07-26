---
title: Matriz de suporte para recuperação de desastre de VMs do Hyper-V locais para o Azure | Microsoft Docs
description: Resume os componentes com suporte e os requisitos para a recuperação de desastre de VM do Hyper-V no Azure com Azure Site Recovery
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: 78a531c769612af0597e732f0dc539286dd7cdac
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489881"
---
# <a name="support-matrix-for-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Matriz de suporte para recuperação de desastre de VMs do Hyper-V locais para o Azure


Este artigo resume os componentes com suporte e as configurações de recuperação de desastre de VMs do Hyper-V locais para o Azure usando [Azure site Recovery](site-recovery-overview.md).


## <a name="supported-scenarios"></a>Cenários suportados

**Cenário** | **Detalhes**
--- | ---
Hyper-V com Virtual Machine Manager | Você pode executar a recuperação de desastre no Azure para VMs em execução em hosts Hyper-V que são gerenciados na malha de System Center Virtual Machine Manager.<br/><br/> Você pode implantar esse cenário no portal do Azure ou usando o PowerShell.<br/><br/> Quando os hosts do Hyper-V são gerenciados pelo Virtual Machine Manager, você também pode executar a recuperação de desastre em um site local secundário. Para saber mais sobre esse cenário, leia [este tutorial](hyper-v-vmm-disaster-recovery.md).
Hyper-V sem Virtual Machine Manager | Você pode executar a recuperação de desastre no Azure para VMs em execução em hosts Hyper-V que não são gerenciados pelo Virtual Machine Manager.<br/><br/> Você pode implantar esse cenário no portal do Azure ou usando o PowerShell.


## <a name="on-premises-servers"></a>Servidores no local

**servidor** | **Requirement** | **Detalhes**
--- | --- | ---
Hyper-V (em execução sem Virtual Machine Manager) |  Windows Server 2019, Windows Server 2016 (incluindo a instalação do Server Core), Windows Server 2012 R2 com as atualizações mais recentes | Se você já tiver configurado o Windows Server 2012 R2 com/ou o SCVMM 2012 R2 com Azure Site Recovery e planejar atualizar o sistema operacional, siga a [documentação](upgrade-2012R2-to-2016.md) de diretrizes. 
Hyper-V (executando com Virtual Machine Manager) | Virtual Machine Manager 2019, Virtual Machine Manager 2016, Virtual Machine Manager 2012 R2 | Se Virtual Machine Manager for usado, os hosts do Windows Server 2019 deverão ser gerenciados em Virtual Machine Manager 2019. Da mesma forma, os hosts do Windows Server 2016 devem ser gerenciados no Virtual Machine Manager 2016.<br/><br/>


## <a name="replicated-vms"></a>VMs replicadas


A tabela a seguir resume o suporte de VM. O Site Recovery dá suporte a qualquer carga de trabalho em execução em um sistema operacional com suporte.

 **Componente** | **Detalhes**
--- | ---
Configuração da VM | As VMs que replicam para o Azure devem atender aos [requisitos do Azure](#azure-vm-requirements).
Sistema operacional convidado | Qualquer SO convidado [com suporte para o Azure](https://docs.microsoft.com/azure/cloud-services/cloud-services-guestos-update-matrix#family-5-releases).<br/><br/> Não há suporte para o Windows Server 2016 nano Server.


## <a name="vmdisk-management"></a>Gerenciamento de VM/disco

**ação** | **Detalhes**
--- | ---
Redimensionar disco na VM Hyper-V replicada | Não suportado. Desabilite a replicação, faça a alteração e, em seguida, reabilite a replicação para a VM.
Adicionar disco na VM Hyper-V replicada | Não suportado. Desabilite a replicação, faça a alteração e, em seguida, reabilite a replicação para a VM.

## <a name="hyper-v-network-configuration"></a>Configuração de rede do Hyper-V

**Componente** | **Hyper-V com Virtual Machine Manager** | **Hyper-V sem Virtual Machine Manager**
--- | --- | ---
Rede do host: Agrupamento NIC | Sim | Sim
Rede do host: VLAN | Sim | Sim
Rede do host: IPv4 | Sim | Sim
Rede do host: IPv6 | Não | Não
Rede VM convidada: Agrupamento NIC | Não | Não
Rede VM convidada: IPv4 | Sim | Sim
Rede VM convidada: IPv6 | Não | Sim
Rede VM convidada: IP estático (Windows) | Sim | Sim
Rede VM convidada: IP estático (Linux) | Não | Não
Rede VM convidada: Várias NICs | Sim | Sim



## <a name="azure-vm-network-configuration-after-failover"></a>Configuração de rede de VM do Azure (após failover)

**Componente** | **Hyper-V com Virtual Machine Manager** | **Hyper-V sem Virtual Machine Manager**
--- | --- | ---
Azure ExpressRoute | Sim | Sim
ILB | Sim | Sim
ELB | Sim | Sim
Gestor de Tráfego do Azure | Sim | Sim
Várias NICs | Sim | Sim
IP Reservado | Sim | Sim
IPv4 | Sim | Sim
Reter endereço IP de origem | Sim | Sim
Pontos de extremidade de serviço de rede virtual do Azure<br/> (sem firewalls de armazenamento do Azure) | Sim | Sim
Redes Aceleradas | Não | Não


## <a name="hyper-v-host-storage"></a>Armazenamento de host do Hyper-V

**Armazenamento** | **Hyper-V com Virtual Machine Manager** | **Hyper-V sem Virtual Machine Manager**
--- | --- | --- 
NFS | ND | ND
SMB 3.0 | Sim | Sim
SAN (ISCSI) | Sim | Sim
Vários caminhos (MPIO). Testado com:<br></br> Microsoft DSM, EMC PowerPath 5,7 SP4<br/><br/> EMC PowerPath DSM para CLARiiON | Sim | Sim

## <a name="hyper-v-vm-guest-storage"></a>Armazenamento convidado de VM Hyper-V

**Armazenamento** | **Hyper-V com Virtual Machine Manager** | **Hyper-V sem Virtual Machine Manager**
--- | --- | ---
VMDK | ND | ND
VHD/VHDX | Sim | Sim
VM de geração 2 | Sim | Sim
EFI/UEFI| Sim | Sim
Disco de cluster compartilhado | Não | Não
Disco criptografado | Não | Não
NFS | ND | ND
SMB 3.0 | Não | Não
RDM | ND | ND
Disco > 1 TB | Sim, até 4.095 GB | Sim, até 4.095 GB
Disco setor lógico e físico de 4K | Sem suporte: Gen 1/Gen 2 | Sem suporte: Gen 1/Gen 2
Disco setor lógico de 4K e 512 bytes físicos | Sim |  Sim
LVM (gerenciamento de volume lógico). O LVM tem suporte apenas em discos de dados. O Azure fornece apenas um único disco do sistema operacional. | Sim | Sim
Volume com disco distribuído > 1 TB | Sim | Sim
Espaços de armazenamento | Não | Não
Adição/remoção de disco quente | Não | Não
Excluir o disco | Sim | Sim
Vários caminhos (MPIO) | Sim | Sim

## <a name="azure-storage"></a>Storage do Azure

**Componente** | **Hyper-V com Virtual Machine Manager** | **Hyper-V sem Virtual Machine Manager**
--- | --- | ---
Armazenamento localmente redundante | Sim | Sim
Armazenamento georredundante | Sim | Sim
Armazenamento com redundância geográfica com acesso de leitura | Sim | Sim
Armazenamento frio | Não | Não
Armazenamento dinâmico| Não | Não
Blobs de bloco | Não | Não
Criptografia em repouso (SSE)| Sim | Sim
Armazenamento Premium | Sim | Sim
Serviço de importação/exportação | Não | Não
Firewalls de armazenamento do Azure para redes virtuais configuradas na conta de armazenamento de armazenamento/cache de destino (usada para armazenar dados de replicação) | Não | Não


## <a name="azure-compute-features"></a>Recursos de computação do Azure

**Funcionalidade** | **Hyper-V com Virtual Machine Manager** | **Hyper-V sem Virtual Machine Manager**
--- | --- | ---
Conjuntos de disponibilidade | Sim | Sim
82801ER | Sim | Sim  
Managed disks | Sim, para failover.<br/><br/> Não há suporte para o failback de discos gerenciados. | Sim, para failover.<br/><br/> Não há suporte para o failback de discos gerenciados.

## <a name="azure-vm-requirements"></a>Requisitos de VM do Azure

As VMs locais que você replica para o Azure devem atender aos requisitos de VM do Azure resumidos nesta tabela.

**Componente** | **Requirement** | **Detalhes**
--- | --- | ---
Sistema operacional convidado | O Site Recovery dá suporte a todos os sistemas operacionais [com suporte no Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).  | A verificação de pré-requisitos falhará se não houver suporte.
Arquitetura do sistema operacional convidado | 64 bits | A verificação de pré-requisitos falhará se não houver suporte.
Tamanho do disco do sistema operacional | Até 2.048 GB para VMs de geração 1.<br/><br/> Até 300 GB para VMs de geração 2.  | A verificação de pré-requisitos falhará se não houver suporte.
Contagem de disco do sistema operacional | 1 | A verificação de pré-requisitos falhará se não houver suporte.
Contagem de disco de dados | 16 ou menos  | A verificação de pré-requisitos falhará se não houver suporte.
Tamanho do VHD do disco de dados | Até 4.095 GB | A verificação de pré-requisitos falhará se não houver suporte.
Placas de rede | São suportados vários adaptadores |
VHD compartilhado | Não suportado | A verificação de pré-requisitos falhará se não houver suporte.
Disco FC | Não suportado | A verificação de pré-requisitos falhará se não houver suporte.
Formato de disco rígido | VHD <br/><br/> VHDX | Site Recovery converte automaticamente VHDX em VHD quando você faz failover para o Azure. Quando você realiza o failback para o local, as máquinas virtuais continuam a usar o formato VHDX.
BitLocker | Não suportado | O BitLocker deve ser desabilitado antes de habilitar a replicação para uma VM.
o nome da VM | Entre 1 e 63 carateres. Limitado a letras, números e hífenes. O nome da VM tem de começar e terminar com uma letra ou um número. | Atualize o valor nas propriedades da VM no Site Recovery.
Tipo de VM | Geração 1<br/><br/> Geração 2--Windows | VMs de geração 2 com um tipo de disco de sistema operacional básico (que inclui um ou dois volumes de dados formatados como VHDX) e menos de 300 GB de espaço em disco têm suporte.<br></br>Não há suporte para VMs da geração 2 do Linux. [Saiba mais](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).|

## <a name="recovery-services-vault-actions"></a>Ações do cofre de serviços de recuperação

**ação** |  **Hyper-V com Virtual Machine Manager** | **Hyper-V sem Virtual Machine Manager**
--- | --- | ---
Mover o cofre entre grupos de recursos<br/><br/> Dentro e entre assinaturas | Não | Não
Mover armazenamento, rede, VMs do Azure entre grupos de recursos<br/><br/> Dentro e entre assinaturas | Não | Não

> [!NOTE]
> Ao replicar Hyper-VMs (gerenciados com/sem o SCVMM) do local para o Azure, você pode replicar para apenas um locatário do AD de um ambiente específico – site do Hyper-V ou SCVMM, conforme aplicável.


## <a name="provider-and-agent"></a>Fornecedor e agente

Para garantir que sua implantação seja compatível com as configurações neste artigo, verifique se você está executando as versões mais recentes do provedor e do agente.

**Name** | **Descrição** | **Detalhes**
--- | --- | --- 
Provedor de Azure Site Recovery | Coordena as comunicações entre servidores locais e o Azure <br/><br/> Hyper-V com Virtual Machine Manager: Instalado em servidores Virtual Machine Manager<br/><br/> Hyper-V sem Virtual Machine Manager: Instalado em hosts Hyper-V| Versão mais recente: 5.1.2700.1 (disponível no portal do Azure)<br/><br/> [Recursos e correções mais recentes](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery)
Agente de Serviços de Recuperação do Microsoft Azure | Coordena a replicação entre VMs do Hyper-V e o Azure<br/><br/> Instalado em servidores Hyper-V locais (com ou sem Virtual Machine Manager) | Agente mais recente disponível no portal






## <a name="next-steps"></a>Passos Seguintes
Saiba como [preparar o Azure](tutorial-prepare-azure.md) para a recuperação de desastres de VMs do Hyper-V locais.
