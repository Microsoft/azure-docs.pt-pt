---
title: Matriz de suporte para recuperação após desastre de VMs de Hyper-V no local para o Azure | Documentos da Microsoft
description: Resume os requisitos para a recuperação de desastres da VM de Hyper-V para o Azure com o Azure Site Recovery e componentes suportados
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: raynew
ms.openlocfilehash: 84b1cfd54f5ff9e6a29c975622847e3ae6304bf8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58107156"
---
# <a name="support-matrix-for-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Matriz de suporte para recuperação após desastre de VMs de Hyper-V no local para o Azure


Este artigo resume os componentes suportados e configurações para recuperação após desastre de VMs de Hyper-V no local para o Azure, utilizando [do Azure Site Recovery](site-recovery-overview.md).


## <a name="supported-scenarios"></a>Cenários suportados

**Cenário** | **Detalhes**
--- | ---
Hyper-V com o Virtual Machine Manager | Pode executar a recuperação após desastre para o Azure para VMs em execução em anfitriões de Hyper-V que são geridos nos recursos de infraestrutura do System Center Virtual Machine Manager.<br/><br/> Pode implementar este cenário no portal do Azure ou através do PowerShell.<br/><br/> Quando os anfitriões de Hyper-V forem geridos pelo Virtual Machine Manager, também pode executar recuperação após desastre para um site secundário no local. Para saber mais sobre este cenário, leia [deste tutorial](hyper-v-vmm-disaster-recovery.md).
Hyper-V sem o Virtual Machine Manager | Pode executar a recuperação após desastre para o Azure para VMs em execução em anfitriões de Hyper-V que não são geridos pelo Virtual Machine Manager.<br/><br/> Pode implementar este cenário no portal do Azure ou através do PowerShell.


## <a name="on-premises-servers"></a>Servidores no local

**Servidor** | **Requisitos** | **Detalhes**
--- | --- | ---
Hyper-V (sem o Gestor de Máquina Virtual a ser executada) | Windows Server 2016 (incluindo instalação server core), Windows Server 2012 R2 com as atualizações mais recentes | Se já tiver configurado o Windows Server 2012 R2 com / ou o SCVMM 2012 R2 com o Azure Site Recovery e o plano para atualizar o sistema operacional, siga as orientações [documentação.](upgrade-2012R2-to-2016.md) 
Hyper-V (em execução com o Virtual Machine Manager) | Virtual Machine Manager 2016, Virtual Machine Manager 2012 R2 | Se for utilizado do Virtual Machine Manager, os anfitriões do Windows Server 2016 devem ser geridos no Virtual Machine Manager 2016.<br/><br/>


## <a name="replicated-vms"></a>VMs replicadas


A tabela seguinte resume o suporte VM. Recuperação de sites suporta quaisquer cargas de trabalho em execução num sistema operativo suportado.

 **Componente** | **Detalhes**
--- | ---
Configuração da VM | As VMs que são replicados para o Azure tem de cumprir [requisitos do Azure](#azure-vm-requirements).
Sistema operativo convidado | Qualquer SO convidado [suportada para o Azure](https://docs.microsoft.com/azure/cloud-services/cloud-services-guestos-update-matrix#family-5-releases)....<br/><br/> Windows Server 2016 Nano Server não é suportado.


## <a name="vmdisk-management"></a>Gerenciamento de VM/disco

**Ação** | **Detalhes**
--- | ---
Redimensionar o disco na VM de Hyper-V replicada | Não suportado. Desative a replicação, efetuar a alteração e, em seguida, reativar a replicação para a VM.
Adicionar o disco na VM de Hyper-V replicada | Não suportado. Desative a replicação, efetuar a alteração e, em seguida, reativar a replicação para a VM.

## <a name="hyper-v-network-configuration"></a>Configuração de rede Hyper-V

**Componente** | **Hyper-V com o Virtual Machine Manager** | **Hyper-V sem o Virtual Machine Manager**
--- | --- | ---
Rede de anfitrião: Agrupamento NIC | Sim | Sim
Rede de anfitrião: VLAN | Sim | Sim
Rede de anfitrião: IPv4 | Sim | Sim
Rede de anfitrião: IPv6 | Não | Não
Rede de VM do convidado: Agrupamento NIC | Não | Não
Rede de VM do convidado: IPv4 | Sim | Sim
Rede de VM do convidado: IPv6 | Não | Sim
Rede de VM do convidado: IP estático (Windows) | Sim | Sim
Rede de VM do convidado: IP estático (Linux) | Não | Não
Rede de VM do convidado: Multi-NIC | Sim | Sim



## <a name="azure-vm-network-configuration-after-failover"></a>Configuração de rede VM do Azure (após a ativação pós-falha)

**Componente** | **Hyper-V com o Virtual Machine Manager** | **Hyper-V sem o Virtual Machine Manager**
--- | --- | ---
Azure ExpressRoute | Sim | Sim
ILB | Sim | Sim
ELB | Sim | Sim
Traffic Manager do Azure | Sim | Sim
Multi-NIC | Sim | Sim
IP Reservado | Sim | Sim
IPv4 | Sim | Sim
Manter o endereço IP de origem | Sim | Sim
Pontos finais de serviço de rede Virtual do Azure<br/> (sem firewalls de armazenamento do Azure) | Sim | Sim
Redes Aceleradas | Não | Não


## <a name="hyper-v-host-storage"></a>Armazenamento de anfitrião Hyper-V

**Armazenamento** | **Hyper-V com o Virtual Machine Manager** | **Hyper-V sem o Virtual Machine Manager**
--- | --- | --- 
NFS | ND | ND
SMB 3.0 | Sim | Sim
SAN (ISCSI) | Sim | Sim
Múltiplos caminhos (o MPIO). Testado com:<br></br> Microsoft DSM, EMC PowerPath 5.7 SP4<br/><br/> EMC PowerPath DSM para CLARiiON | Sim | Sim

## <a name="hyper-v-vm-guest-storage"></a>Armazenamento de convidado de VM de Hyper-V

**Armazenamento** | **Hyper-V com o Virtual Machine Manager** | **Hyper-V sem o Virtual Machine Manager**
--- | --- | ---
VMDK | ND | ND
VHD/VHDX | Sim | Sim
Geração 2 VM | Sim | Sim
EFI/UEFI| Sim | Sim
Partilhado o disco de cluster | Não | Não
Disco encriptado | Não | Não
NFS | ND | ND
SMB 3.0 | Não | Não
RDM | ND | ND
Disco > 1 TB | Sim, até 4095 GB | Sim, até 4095 GB
Disco: Setor de lógico e físico de 4K | Não suportado: Gen 1/Gen 2 | Não suportado: Gen 1/Gen 2
Disco: 4K lógico e o setor físico de 512 bytes | Sim |  Sim
Gestão de volumes lógicos (LVM). LVM é suportada em discos de dados apenas. O Azure fornece apenas um único disco de SO. | Sim | Sim
Volume com o disco repartido > 1 TB | Sim | Sim
Espaços de armazenamento | Sim | Sim
Disco de acesso frequente Adicionar/remover | Não | Não
Excluir o disco | Sim | Sim
Múltiplos caminhos (o MPIO) | Sim | Sim

## <a name="azure-storage"></a>Storage do Azure

**Componente** | **Hyper-V com o Virtual Machine Manager** | **Hyper-V sem o Virtual Machine Manager**
--- | --- | ---
Armazenamento localmente redundante | Sim | Sim
Armazenamento georredundante | Sim | Sim
Armazenamento georredundante com acesso de leitura | Sim | Sim
Armazenamento de acesso esporádico | Não | Não
Armazenamento frequente| Não | Não
Blobs de bloco | Não | Não
Encriptação em repouso (SSE)| Sim | Sim
Armazenamento Premium | Sim | Sim
Serviço de importação/exportação | Não | Não
Firewalls de armazenamento do Azure para redes virtuais configuradas na conta de armazenamento de cache/armazenamento de destino (usada para armazenar dados de replicação) | Não | Não


## <a name="azure-compute-features"></a>Recursos de computação do Azure

**Funcionalidade** | **Hyper-V com o Virtual Machine Manager** | **Hyper-V sem o Virtual Machine Manager**
--- | --- | ---
Conjuntos de disponibilidade | Sim | Sim
HUB | Sim | Sim  
Managed disks | Sim, para a ativação pós-falha.<br/><br/> Não é suportada a reativação pós-falha de discos geridos. | Sim, para a ativação pós-falha.<br/><br/> Não é suportada a reativação pós-falha de discos geridos.

## <a name="azure-vm-requirements"></a>Requisitos de VM do Azure

As VMs no local que replica para o Azure tem de cumprir os requisitos de VM do Azure resumidos nesta tabela.

**Componente** | **Requisitos** | **Detalhes**
--- | --- | ---
Sistema operativo convidado | Recuperação de sites suporta todos os sistemas operativos que são [suportado pelo Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).  | A verificação de pré-requisitos falha se não suportado.
Arquitetura de sistema operativo convidado | 64 bits | A verificação de pré-requisitos falha se não suportado.
Tamanho de disco do sistema operativo | Até 2.048 GB para VMs de geração 1.<br/><br/> Até 300 GB para VMs de geração 2.  | A verificação de pré-requisitos falha se não suportado.
Número de discos do sistema operativo | 1 | A verificação de pré-requisitos falha se não suportado.
Número de discos de dados | 16 ou menos  | A verificação de pré-requisitos falha se não suportado.
Tamanho VHD de disco de dados | Até 4095 GB | A verificação de pré-requisitos falha se não suportado.
Placas de rede | São suportados vários adaptadores |
VHD partilhado | Não suportado | A verificação de pré-requisitos falha se não suportado.
Disco FC | Não suportado | A verificação de pré-requisitos falha se não suportado.
Formato de disco rígido | VHD <br/><br/> VHDX | Recuperação de sites converte automaticamente VHDX em VHD quando efetuar a ativação pós-falha para o Azure. Quando a reativação pós-falha para no local, as máquinas virtuais é continuar a utilizar o formato VHDX.
BitLocker | Não suportado | O BitLocker tem de ser desativado antes de ativar a replicação para uma VM.
o nome da VM | Entre 1 e 63 carateres. Limitado a letras, números e hífenes. O nome da VM tem de começar e terminar com uma letra ou um número. | Atualize o valor nas propriedades da VM no Site Recovery.
Tipo de VM | Geração 1<br/><br/> Geração 2 – Windows | VMs de geração 2, com um tipo de disco de SO do basic (que inclui um ou dois volumes de dados formatados como VHDX) e menos de 300 GB de espaço em disco são suportadas.<br></br>VMs de Linux geração 2 não são suportadas. [Saiba mais](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).|

## <a name="recovery-services-vault-actions"></a>Ações de Cofre de serviços de recuperação

**Ação** |  **Hyper-V com o Virtual Machine Manager** | **Hyper-V sem o Virtual Machine Manager**
--- | --- | ---
Mover o Cofre entre grupos de recursos<br/><br/> Dentro e entre subscrições | Não | Não
Mover o armazenamento, rede, as VMs do Azure entre grupos de recursos<br/><br/> Dentro e entre subscrições | Não | Não

> [!NOTE]
> Ao replicar VMs de Hyper-(geridas com/sem SCVMM) no local para o Azure, pode replicar apenas um inquilino do AD de um ambiente específico - site Hyper-V ou o SCVMM, conforme aplicável.


## <a name="provider-and-agent"></a>Fornecedor e agente

Para certificar-se de que a implementação é compatível com as definições neste artigo, certifique-se de que está a executar o fornecedor mais recente e versões do agente.

**Nome** | **Descrição** | **Detalhes**
--- | --- | --- 
Fornecedor do Azure Site Recovery | Coordena as comunicações entre servidores no local e o Azure <br/><br/> Hyper-V com o Virtual Machine Manager: Instalado em servidores do Virtual Machine Manager<br/><br/> Hyper-V sem o Virtual Machine Manager: Instalado em anfitriões Hyper-V| Versão mais recente: 5.1.2700.1 (disponível no portal do Azure)<br/><br/> [Funcionalidades e correções mais recentes](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery)
Agente dos serviços de recuperação do Microsoft Azure | Coordena a replicação entre VMs de Hyper-V e o Azure<br/><br/> Instalado em servidores de Hyper-V no local (com ou sem o Gestor de Máquina Virtual) | Agente mais recente disponível no portal






## <a name="next-steps"></a>Passos Seguintes
Saiba como [preparar o Azure](tutorial-prepare-azure.md) para recuperação após desastre de VMs de Hyper-V no local.
