---
title: Apoio à recuperação de desastres de VMs Hiper-V para Azure com recuperação do site Azure
description: Resume os componentes e requisitos suportados para a recuperação de desastres de Hiper-V VM para Azure com recuperação do site Azure
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 1/27/2020
ms.author: raynew
ms.openlocfilehash: fd10468e823201bfa67aaf7c570071bd075ec4ac
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80420833"
---
# <a name="support-matrix-for-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Matriz de suporte para recuperação de desastres de Hiper-V V no local para Azure


Este artigo resume os componentes e configurações suportados para a recuperação de desastres de Hiper-V V MMs no local para Azure utilizando a Recuperação do [Site Azure.](site-recovery-overview.md)



## <a name="supported-scenarios"></a>Cenários suportados

**Cenário** | **Detalhes**
--- | ---
Hiper-V com Gestor de Máquinavirtual <br> <br>| Você pode realizar recuperação de desastres para Azure para VMs em execução em anfitriões Hyper-V que são geridos no tecido System Center Virtual Machine Manager.<br/><br/> Pode implementar este cenário no portal Azure ou utilizando o PowerShell.<br/><br/> Quando os anfitriões Hyper-V são geridos pelo Virtual Machine Manager, você também pode realizar a recuperação de desastres para um site secundário no local. Para saber mais sobre este cenário, leia [este tutorial.](hyper-v-vmm-disaster-recovery.md)
Hiper-V sem Gestor de Máquinavirtual | Você pode realizar recuperação de desastres para Azure para VMs em execução em anfitriões Hyper-V que não são geridos por Virtual Machine Manager.<br/><br/> Pode implementar este cenário no portal Azure ou utilizando o PowerShell.

## <a name="on-premises-servers"></a>Servidores no local

**Servidor** | **Requisitos** | **Detalhes**
--- | --- | ---
Hyper-V (a correr sem Gestor de Máquinavirtual) |  Windows Server 2019, Windows Server 2016 (incluindo instalação do núcleo do servidor), Windows Server 2012 R2 com as mais recentes atualizações | Se já configurou o Windows Server 2012 R2 com/ou SCVMM 2012 R2 com recuperação do site Azure e planeie atualizar o OS, siga a [documentação](upgrade-2012R2-to-2016.md) de orientação. 
Hyper-V (a correr com gestor de máquinas virtuais) | Virtual Machine Manager 2019, Virtual Machine Manager 2016, Virtual Machine Manager 2012 R2 | Se o Gestor de Máquinas Virtuais for utilizado, os anfitriões do Windows Server 2019 devem ser geridos no Virtual Machine Manager 2019. Da mesma forma, os anfitriões do Windows Server 2016 devem ser geridos no Virtual Machine Manager 2016.<br/><br/> Nota: A falha na localização alternativa não é suportada para os anfitriões do Windows Server 2019.


## <a name="replicated-vms"></a>VMs replicados


A tabela que se segue resume o suporte vm. A Recuperação do Site suporta quaisquer cargas de trabalho em funcionamento num sistema operativo suportado.

 **Componente** | **Detalhes**
--- | ---
Configuração VM | Os VMs que se replicam ao Azure devem satisfazer os [requisitos do Azure.](#azure-vm-requirements)
Sistema operativo convidado | Qualquer hóspede [apoiado pelo Azure](https://docs.microsoft.com/azure/cloud-services/cloud-services-guestos-update-matrix#family-5-releases)..<br/><br/> O Windows Server 2016 Nano Server não é suportado.


## <a name="vmdisk-management"></a>Gestão VM/Disco

**Ação** | **Detalhes**
--- | ---
Redimensionar o disco em VM hiper-V replicado | Não suportado. Desative a replicação, faça a alteração e, em seguida, reative a replicação para o VM.
Adicione o disco em Hyper-V VM replicado | Não suportado. Desative a replicação, faça a alteração e, em seguida, reative a replicação para o VM.

## <a name="hyper-v-network-configuration"></a>Configuração da rede hyper-V

**Componente** | **Hiper-V com Gestor de Máquinavirtual** | **Hiper-V sem Gestor de Máquinavirtual**
--- | --- | ---
Rede de anfitriões: NIC Teaming | Sim | Sim
Rede de anfitriões: VLAN | Sim | Sim
Rede de anfitriões: IPv4 | Sim | Sim
Rede de anfitriões: IPv6 | Não | Não
Rede VM convidada: NIC Teaming | Não | Não
Rede VM convidada: IPv4 | Sim | Sim
Rede VM convidada: IPv6 | Não | Sim
Rede VM convidada: STATIC IP (Windows) | Sim | Sim
Rede VM convidada: STATIC IP (Linux) | Não | Não
Rede VM convidada: Multi-NIC | Sim | Sim
Procuração https | Não | Não



## <a name="azure-vm-network-configuration-after-failover"></a>Configuração da rede Azure VM (após falha)

**Componente** | **Hiper-V com Gestor de Máquinavirtual** | **Hiper-V sem Gestor de Máquinavirtual**
--- | --- | ---
Azure ExpressRoute | Sim | Sim
ILB | Sim | Sim
ELB | Sim | Sim
Traffic Manager do Azure | Sim | Sim
Multi-NIC | Sim | Sim
IP reservado | Sim | Sim
IPv4 | Sim | Sim
Reter endereço IP de origem | Sim | Sim
Pontos finais do serviço da Rede Virtual Azure<br/> (sem firewalls de armazenamento Azure) | Sim | Sim
Redes Aceleradas | Não | Não


## <a name="hyper-v-host-storage"></a>Armazenamento de hospedeiro hiper-V

**Armazenamento** | **Hiper-V com Gestor de Máquinavirtual** | **Hiper-V sem Gestor de Máquinavirtual**
--- | --- | --- 
NFS | ND | ND
SMB 3.0 | Sim | Sim
SAN (ISCSI) | Sim | Sim
Multi-caminho (MPIO). Testado com:<br></br> Microsoft DSM, EMC PowerPath 5.7 SP4, EMC PowerPath DSM para CLARiiON | Sim | Sim

## <a name="hyper-v-vm-guest-storage"></a>Armazenamento de hóspedes Hyper-V VM

**Armazenamento** | **Hiper-V com Gestor de Máquinavirtual** | **Hiper-V sem Gestor de Máquinavirtual**
--- | --- | ---
VMDK | ND | ND
VHD/VHDX | Sim | Sim
VM de 2ª Geração | Sim | Sim
EFI/UEFI<br></br>O VM migrado em Azure será automaticamente convertido para um VM de arranque BIOS. O VM deve estar a executar o Windows Server 2012 e mais tarde apenas. O disco OS deve ter até cinco divisórias ou menos e o tamanho do disco OS deve ser inferior a 300 GB.| Sim | Sim
Disco de cluster partilhado | Não | Não
Disco encriptado | Não | Não
NFS | ND | ND
SMB 3.0 | Não | Não
RDM | ND | ND
Disco >1 TB | Sim, até 4.095 GB | Sim, até 4.095 GB
Disco: sector lógico e físico 4K | Não suportado: Gen 1/Gen 2 | Não suportado: Gen 1/Gen 2
Disco: 4K lógico e 512 bytes sector físico | Sim |  Sim
Gestão lógica do volume (LVM). O LVM é suportado apenas em discos de dados. O Azure fornece apenas um único disco de SO. | Sim | Sim
Volume com disco listrado >1 TB | Sim | Sim
Espaços de Armazenamento | Não | Não
Disco de adição/remoção quente | Não | Não
Excluir o disco | Sim | Sim
Multi-caminho (MPIO) | Sim | Sim

## <a name="azure-storage"></a>Storage do Azure

**Componente** | **Hiper-V com Gestor de Máquinavirtual** | **Hiper-V sem Gestor de Máquinavirtual**
--- | --- | ---
Armazenamento localmente redundante | Sim | Sim
Armazenamento georredundante | Sim | Sim
Armazenamento georredundante com acesso de leitura | Sim | Sim
Armazenamento fresco | Não | Não
Armazenamento quente| Não | Não
Blobs de blocos | Não | Não
Encriptação em repouso (SSE)| Sim | Sim
Encriptação em repouso (CMK) <br></br> (Apenas por falhas nos discos geridos)| Sim (via PowerShell Az 3.3.0 módulo em diante) | Sim (via PowerShell Az 3.3.0 módulo em diante)
Armazenamento Premium | Sim | Sim
Serviço de Importação/Exportação | Não | Não
Contas de Armazenamento Azure com firewall ativada | Sim. Para armazenamento e cache alvo. | Sim. Para armazenamento e cache alvo.
Modificar conta de armazenamento | Não. A conta de armazenamento-alvo Azure não pode ser modificada após a replicação. Para modificar, desativar e, em seguida, reativar a recuperação de desastres. | Não


## <a name="azure-compute-features"></a>Características do computação Azure

**Funcionalidade** | **Hiper-V com Gestor de Máquinavirtual** | **Hiper-V sem Gestor de Máquinavirtual**
--- | --- | ---
Conjuntos de disponibilidade | Sim | Sim
HUB | Sim | Sim  
Managed disks | Sim, para o fracasso.<br/><br/> O recuo dos discos geridos não é suportado. | Sim, para o fracasso.<br/><br/> O recuo dos discos geridos não é suportado.

## <a name="azure-vm-requirements"></a>Requisitos da VM do Azure

As VMs no local que replica para o Azure devem satisfazer os requisitos de VM Azure resumidos nesta tabela.

**Componente** | **Requisitos** | **Detalhes**
--- | --- | ---
Sistema operativo convidado | A Recuperação do Site suporta todos os sistemas operativos que são [suportados pelo Azure.](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)  | A verificação dos pré-requisitos falha se não for suportada.
Arquitetura do sistema operativo convidado | 32-bit (Windows Server 2008)/64-bit | A verificação dos pré-requisitos falha se não for suportada.
Tamanho do disco do sistema operativo | Até 2.048 GB para os VMs da geração 1.<br/><br/> Até 300 GB para as VMs da geração 2.  | A verificação dos pré-requisitos falha se não for suportada.
Contagem de discos do sistema operativo | 1 | A verificação dos pré-requisitos falha se não for suportada.
Contagem de discos de dados | 16 ou menos  | A verificação dos pré-requisitos falha se não for suportada.
Tamanho do VHD do disco de dados | Até 4.095 GB | A verificação dos pré-requisitos falha se não for suportada.
Placas de rede | São suportados vários adaptadores |
VHD partilhado | Não suportado | A verificação dos pré-requisitos falha se não for suportada.
Disco FC | Não suportado | A verificação dos pré-requisitos falha se não for suportada.
Formato de disco rígido | VHD <br/><br/> VHDX | A Recuperação do Site converte automaticamente vHDX em VHD quando falha no Azure. Quando não volta ao local, as máquinas virtuais continuam a utilizar o formato VHDX.
BitLocker | Não suportado | O BitLocker deve ser desativado antes de ativar a replicação de um VM.
o nome da VM | Entre 1 e 63 carateres. Limitado a letras, números e hífenes. O nome da VM tem de começar e terminar com uma letra ou um número. | Atualize o valor nas propriedades vM em Recuperação do Site.
Tipo de VM | Geração 1<br/><br/> Geração 2-Windows | São suportados vMs de geração 2 com um tipo de base de disco oso (que inclui um ou dois volumes de dados formados como VHDX) e menos de 300 GB de espaço em disco.<br></br>Os VMs da Linux Generation 2 não são apoiados. [Saiba mais](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).|

## <a name="recovery-services-vault-actions"></a>Ações de cofre de serviços de recuperação

**Ação** |  **Hiper-V com VMM** | **Hiper-V sem VMM**
--- | --- | ---
Mova o cofre através de grupos de recursos<br/><br/> Dentro e em todas as subscrições | Não | Não
Movimentar armazenamento, rede, VMs Azure em grupos de recursos<br/><br/> Dentro e em todas as subscrições | Não | Não

> [!NOTE]
> Ao replicar Hiper-VMs de instalações para Azure, pode replicar-se a apenas um inquilino ad de um ambiente específico - hiper-V site ou Hyper-V com VMM conforme aplicável.


## <a name="provider-and-agent"></a>Fornecedor e agente

Para se certificar de que a sua implementação é compatível com as definições deste artigo, certifique-se de que está a executar as versões mais recentes do fornecedor e do agente.

**Nome** | **Descrição** | **Detalhes**
--- | --- | --- 
Fornecedor de recuperação de sítios azure | Coordena as comunicações entre servidores no local e Azure <br/><br/> Hyper-V com Gestor de Máquinavirtual: Instalado em servidores de Gestor de Máquinas Virtuais<br/><br/> Hiper-V sem Gestor de Máquinavirtual: Instalado em anfitriões Hiper-V| Versão mais recente: 5.1.2700.1 (disponível no portal Azure)<br/><br/> [Últimas funcionalidades e correções](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery)
Agente dos Serviços de Recuperação do Microsoft Azure | Coordena a replicação entre Os VMs Hiper-V e Azure<br/><br/> Instalado em servidores Hiper-V no local (com ou sem Gestor de Máquinavirtual) | Mais recente agente disponível a partir do portal






## <a name="next-steps"></a>Passos seguintes
Aprenda a [preparar azure](tutorial-prepare-azure.md) para a recuperação de desastres de VMs hiper-V no local.
