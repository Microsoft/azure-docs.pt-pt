---
title: Apoio à recuperação de hiper-V para Azure com recuperação do local de Azure
description: Resume os componentes e requisitos suportados para a recuperação de desastres hiper-VM para Azure com recuperação do local de Azure
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 7/14/2020
ms.author: raynew
ms.openlocfilehash: 8d748f93337a770e0d565bab79fdfb3625bda70d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101735527"
---
# <a name="support-matrix-for-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Matriz de apoio à recuperação de desastres de Hiper-V VMs para Azure

Este artigo resume os componentes e configurações suportados para a recuperação de desastres dos Hiper-V VMs para Azure no local, utilizando [a recuperação do local de Azure.](site-recovery-overview.md)

>[!NOTE]
> A Recuperação do Site não move nem armazena os dados dos clientes para fora da região alvo, na qual foi configurada a recuperação de desastres para as máquinas de origem. Os clientes podem selecionar um Cofre de Serviços de Recuperação de uma região diferente, se assim o desejarem. O Cofre de Serviços de Recuperação contém metadados, mas nenhum dado real do cliente.

## <a name="supported-scenarios"></a>Cenários suportados

**Cenário** | **Detalhes**
--- | ---
Hiper-V com gestor de máquinas virtuais <br> <br>| Pode realizar a recuperação de desastres para O Azure para VMs em execução em anfitriões Hiper-V que são geridos no tecido De Gestor de Máquinas Virtual system Center.<br/><br/> Pode implementar este cenário no portal Azure ou utilizando o PowerShell.<br/><br/> Quando os anfitriões Hyper-V são geridos pelo Virtual Machine Manager, também pode realizar a recuperação de desastres para um site secundário no local. Para saber mais sobre este cenário, leia [este tutorial.](hyper-v-vmm-disaster-recovery.md)
Hiper-V sem Gestor de Máquinas Virtuais | Você pode realizar a recuperação de desastres para Azure para VMs em execução em anfitriões Hiper-V que não são geridos por Virtual Machine Manager.<br/><br/> Pode implementar este cenário no portal Azure ou utilizando o PowerShell.

## <a name="on-premises-servers"></a>Servidores no local

**Servidor** | **Requisitos** | **Detalhes**
--- | --- | ---
Hiper-V (funcionando sem gestor de máquinas virtuais) |  Windows Server 2019, Windows Server 2016, Windows Server 2012 R2 com as mais recentes atualizações <br/><br/> **Nota:** A instalação do núcleo do servidor destes sistemas operativos também é suportada. | Se já configurar o Windows Server 2012 R2 com/ou SCVMM 2012 R2 com a Recuperação do Site Azure e planeia atualizar o SISTEMA, siga a [documentação](upgrade-2012R2-to-2016.md) de orientação.
Hiper-V (em execução com Gestor de Máquinas Virtuais) | Gestor de Máquinas Virtuais 2019, Gestor de Máquinas Virtuais 2016, Gestor de Máquinas Virtuais 2012 R2 <br/><br/> **Nota:** A instalação do núcleo do servidor destes sistemas operativos também é suportada.  | Se o Gestor de Máquinas Virtuais for utilizado, os anfitriões do Windows Server 2019 deverão ser geridos no Virtual Machine Manager 2019. Da mesma forma, os anfitriões do Windows Server 2016 devem ser geridos no Virtual Machine Manager 2016.

> [!NOTE]
> Certifique-se de que o quadro .NET 4.6.2 ou superior está presente no servidor no local.

## <a name="replicated-vms"></a>VMs replicados


A tabela seguinte resume o suporte vM. A Recuperação do Site suporta quaisquer cargas de trabalho em execução num sistema operativo suportado.

 **Componente** | **Detalhes**
--- | ---
Configuração VM | Os VMs que se replicam ao Azure devem satisfazer os [requisitos do Azure](#azure-vm-requirements).
Sistema operativo convidado | Qualquer convidado do OS [apoiado para a Azure](../cloud-services/cloud-services-guestos-update-matrix.md#family-5-releases)..<br/><br/> O Windows Server 2016 Nano Server não é suportado.


## <a name="vmdisk-management"></a>Gestão de VM/Disco

**Ação** | **Detalhes**
--- | ---
Redimensione o disco em VM Hiper-V replicado | Não suportado. Desativar a replicação, fazer a alteração e, em seguida, reativar a replicação para o VM.
Adicione o disco no Hiper-V VM replicado | Não suportado. Desativar a replicação, fazer a alteração e, em seguida, reativar a replicação para o VM.

## <a name="hyper-v-network-configuration"></a>Configuração da rede Hyper-V

**Componente** | **Hiper-V com gestor de máquinas virtuais** | **Hiper-V sem Gestor de Máquinas Virtuais**
--- | --- | ---
Rede de anfitriões: NIC Teaming | Sim | Sim
Rede de anfitriões: VLAN | Sim | Sim
Rede de anfitriões: IPv4 | Sim | Sim
Rede de anfitriões: IPv6 | Não | Não
Rede VM convidada: NIC Teaming | Não | Não
Rede VM convidada: IPv4 | Sim | Sim
Rede VM convidada: IPv6 | Não | Sim
Rede VM do hóspede: IP estático (Windows) | Sim | Sim
Rede VM convidada: IP estático (Linux) | Não | Não
Rede VM convidada: Multi-NIC | Sim | Sim
Procuração https | Não | Não
Acesso de ligação privada ao serviço de Recuperação de Sítios | Sim. [Saiba mais](hybrid-how-to-enable-replication-private-endpoints.md). | Sim. [Saiba mais](hybrid-how-to-enable-replication-private-endpoints.md).




## <a name="azure-vm-network-configuration-after-failover"></a>Configuração da rede Azure VM (após falha)

**Componente** | **Hiper-V com gestor de máquinas virtuais** | **Hiper-V sem Gestor de Máquinas Virtuais**
--- | --- | ---
Azure ExpressRoute | Sim | Sim
ILB | Sim | Sim
ELB | Sim | Sim
Gestor de Tráfego do Azure | Sim | Sim
Multi-NIC | Sim | Sim
IP reservado | Sim | Sim
IPv4 | Sim | Sim
Reter endereço IP de origem | Sim | Sim
Pontos finais de serviço da Rede Virtual Azure<br/> (sem firewalls de armazenamento Azure) | Sim | Sim
Redes Aceleradas | Não | Não


## <a name="hyper-v-host-storage"></a>Armazenamento de hospedeiro hiper-V

**Armazenamento** | **Hiper-V com gestor de máquinas virtuais** | **Hiper-V sem Gestor de Máquinas Virtuais**
--- | --- | --- 
NFS | ND | ND
SMB 3.0 | Sim | Sim
SAN (ISCSI) | Sim | Sim
Multi-caminhos (MPIO). Testado com:<br></br> Microsoft DSM, EMC PowerPath 5.7 SP4, EMC PowerPath DSM para CLARiiON | Sim | Sim

## <a name="hyper-v-vm-guest-storage"></a>Armazenamento de hóspedes Hyper-V VM

**Armazenamento** | **Hiper-V com gestor de máquinas virtuais** | **Hiper-V sem Gestor de Máquinas Virtuais**
--- | --- | ---
VMDK | ND | ND
VHD/VHDX | Sim | Sim
VM de 2ª Geração | Sim | Sim
EFI/UEFI<br></br>O VM migrado em Azure será automaticamente convertido para um VM de arranque BIOS. O VM deve estar a executar o Windows Server 2012 e mais tarde apenas. O disco de so deve ter até cinco divisórias ou menos e o tamanho do disco de SO deve ser inferior a 300 GB.| Sim | Sim
Disco de cluster compartilhado | Não | Não
Disco encriptado | Não | Não
NFS | ND | ND
SMB 3.0 | Não | Não
RDM | ND | ND
Disco >1 TB | Sim, até 4.095 GB | Sim, até 4.095 GB
Disco: Sector lógico e físico 4K | Não suportado: Gen 1/Gen 2 | Não suportado: Gen 1/Gen 2
Disco: 4K lógico e 512 bytes sector físico | Sim |  Sim
Gestão lógica do volume (LVM). A LVM é suportada apenas em discos de dados. O Azure fornece apenas um único disco de SO. | Sim | Sim
Volume com disco listrado >1 TB | Sim | Sim
Espaços de Armazenamento | Não | Não
Disco de adicionar/remover quente | Não | Não
Excluir o disco | Sim | Sim
Multi-caminhos (MPIO) | Sim | Sim

## <a name="azure-storage"></a>Storage do Azure

**Componente** | **Hiper-V com gestor de máquinas virtuais** | **Hiper-V sem Gestor de Máquinas Virtuais**
--- | --- | ---
Armazenamento localmente redundante | Sim | Sim
Armazenamento georredundante | Sim | Sim
Armazenamento georredundante com acesso de leitura | Sim | Sim
Armazenamento com redundância entre zonas | Não | Não
Armazenamento fresco | Não | Não
Armazenamento quente| Não | Não
Blobs de blocos | Não | Não
Encriptação em repouso (SSE)| Sim | Sim
Encriptação em repouso (CMK) <br></br> (Apenas para falhas a gerir discos)| Sim (via módulo PowerShell Az 3.3.0 em diante) | Sim (via módulo PowerShell Az 3.3.0 em diante)
Dupla encriptação em repouso <br></br> (Apenas para falhas a gerir discos) <br></br> Saiba mais sobre regiões apoiadas para [Windows](../virtual-machines/disk-encryption.md) e [Linux](../virtual-machines/disk-encryption.md) | Sim (via módulo PowerShell Az 3.3.0 em diante) | Sim (via módulo PowerShell Az 3.3.0 em diante)
Armazenamento Premium | Sim | Sim
Armazenamento Standard | Sim | Sim
Serviço de importação/exportação | Não | Não
Contas de Armazenamento Azure com firewall ativadas | Sim. Para armazenamento de alvo e cache. | Sim. Para armazenamento de alvo e cache.
Modificar a conta de armazenamento | N.º A conta de armazenamento Azure alvo não pode ser modificada após permitir a replicação. Para modificar, desativar e, em seguida, reativar a recuperação de desastres. | Não
Opção de transferência segura | Sim | Sim


## <a name="azure-compute-features"></a>Características do cálculo Azure

**Funcionalidade** | **Hiper-V com gestor de máquinas virtuais** | **Hiper-V sem Gestor de Máquinas Virtuais**
--- | --- | ---
Conjuntos de disponibilidade | Sim | Sim
HUB | Sim | Sim  
Managed disks | Sim, por falhar.<br/><br/> O fracasso dos discos geridos não é suportado. | Sim, por falhar.<br/><br/> O fracasso dos discos geridos não é suportado.

## <a name="azure-vm-requirements"></a>Requisitos da VM do Azure

Os VMs no local que replicar para Azure devem satisfazer os requisitos Azure VM resumidos nesta tabela.

**Componente** | **Requisitos** | **Detalhes**
--- | --- | ---
Sistema operativo convidado | A Recuperação do Site suporta todos os sistemas operativos suportados [pelo Azure.](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc794868(v=ws.10))  | Pré-requisitos a verificação falha se não for suportado.
Arquitetura do sistema operativo convidado | 32 bits (Windows Server 2008)/64-bit | Pré-requisitos a verificação falha se não for suportado.
Tamanho do disco do sistema operativo | Até 2.048 GB para a geração 1 VMs.<br/><br/> Até 300 GB para a geração 2 VMs.  | Pré-requisitos a verificação falha se não for suportado.
Contagem de discos do sistema operativo | 1 | Pré-requisitos a verificação falha se não for suportado.
Contagem de discos de dados | 16 ou menos  | Pré-requisitos a verificação falha se não for suportado.
Tamanho do VHD do disco de dados | Até 4.095 GB | Pré-requisitos a verificação falha se não for suportado.
Placas de rede | São suportados vários adaptadores |
VHD partilhado | Não suportado | Pré-requisitos a verificação falha se não for suportado.
Disco FC | Não suportado | Pré-requisitos a verificação falha se não for suportado.
Formato de disco rígido | VHD <br/><br/> VHDX | A Recuperação do Site converte automaticamente o VHDX em VHD quando falha em Azure. Quando falhas nas instalações, as máquinas virtuais continuam a utilizar o formato VHDX.
BitLocker | Não suportado | O BitLocker tem de ser desativado antes de ativar a replicação de um VM.
o nome da VM | Entre 1 e 63 carateres. Limitado a letras, números e hífenes. O nome da VM tem de começar e terminar com uma letra ou um número. | Atualizar o valor nas propriedades VM na Recuperação do Site.
Tipo de VM | Geração 1<br/><br/> Geração 2-- Janelas | Geração 2 VMs com um tipo de disco de sistema operativo de base (que inclui um ou dois volumes de dados formatados como VHDX) e menos de 300 GB de espaço em disco são suportados.<br></br>Os VMs da Geração Linux 2 não são suportados. [Saiba mais](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).|

## <a name="recovery-services-vault-actions"></a>Ações de cofre de serviços de recuperação

**Ação** |  **Hiper-V com VMM** | **Hiper-V sem VMM**
--- | --- | ---
Mover cofre através de grupos de recursos<br/><br/> Dentro e em todas as subscrições | Não | Não
Mover armazenamento, rede, VMs Azure em grupos de recursos<br/><br/> Dentro e em todas as subscrições | Não | Não

> [!NOTE]
> Ao replicar Hyper-VMs de instalações para Azure, pode replicar-se a apenas um inquilino AD de um ambiente específico - Hyper-V ou Hyper-V com VMM, conforme aplicável.


## <a name="provider-and-agent"></a>Fornecedor e agente

Para se certificar de que a sua implementação é compatível com as definições deste artigo, certifique-se de que está a executar as versões mais recentes do fornecedor e do agente.

**Nome** | **Descrição** | **Detalhes**
--- | --- | --- 
Fornecedor de recuperação do site Azure | Coordena comunicações entre servidores no local e Azure <br/><br/> Hiper-V com Gestor de Máquinas Virtuais: Instalado em servidores de Gestor de Máquinas Virtuais<br/><br/> Hiper-V sem Gerente de Máquina Virtual: Instalado em anfitriões Hyper-V| Versão mais recente: 5.1.2700.1 (disponível a partir do portal Azure)<br/><br/> [Funcionalidades e correções mais recentes](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery)
Agente dos Serviços de Recuperação do Microsoft Azure | Coordena a replicação entre Hiper-VMs e Azure<br/><br/> Instalados em servidores Hiper-V no local (com ou sem Gestor de Máquinas Virtuais) | Último agente disponível a partir do portal






## <a name="next-steps"></a>Passos seguintes
Saiba como [preparar o Azure](tutorial-prepare-azure.md) para a recuperação de desastres dos Hiper-V VMs no local.