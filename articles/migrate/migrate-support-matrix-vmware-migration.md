---
title: Suporte à migração de VMware em Azure Migrate
description: Saiba mais sobre o suporte à migração VMware VM em Azure Migrate.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: b263d3e62ae97914fc8e06580486bddd0cb9b3b7
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/07/2021
ms.locfileid: "97968454"
---
# <a name="support-matrix-for-vmware-migration"></a>Matriz de suporte da migração do VMware

Este artigo resume as configurações e limitações de suporte para vMware VMs migratórios com [Azure Migrate: Migração do servidor](migrate-services-overview.md#azure-migrate-server-migration-tool) . Se procura informações sobre a avaliação de VMware VMs para migração para Azure, reveja a matriz de suporte de [avaliação](migrate-support-matrix-vmware.md).


## <a name="migration-options"></a>Opções de migração

Pode migrar VMware VMs de várias maneiras:

- **Utilização de migração sem agentes**: Migrar VMs sem necessidade de instalar nada neles. Você implanta o [aparelho Azure Migrate](migrate-appliance.md) para migração sem agentes.
- **Utilização da migração baseada em agentes:** Instale um agente no VM para replicação. Para a migração baseada em agentes, você implanta um [aparelho de replicação](migrate-replication-appliance.md).

[Reveja este artigo](server-migrate-overview.md) para descobrir que método pretende utilizar.

## <a name="migration-limitations"></a>Limitações da migração

- Pode selecionar até 10 VMs de uma só vez para replicação. Se quiser migrar mais máquinas, então replique em grupos de 10.
- Para a migração sem agente VMware, pode executar até 300 replicações simultaneamente.

## <a name="agentless-migration"></a>Migração sem agentes 

Esta secção resume os requisitos para a migração sem agentes.

### <a name="vmware-requirements-agentless"></a>Requisitos VMware (sem agente)

A tabela resume os requisitos do hipervisor VMware.

**VMware** | **Detalhes**
--- | ---
**VMware vCenter Server** | Versão 5.5, 6.0, 6.5, 6.7, 7.0.
**VMware vSphere ESXI anfitrião** | Versão 5.5, 6.0, 6.5, 6.7, 7.0.
**permissões vCenter Server** | A migração sem agentes utiliza o [Aparelho Migratório](migrate-appliance.md). O aparelho necessita destas permissões no servidor vCenter:<br/><br/> - **Datastore.Browse** (Datastore -> Loja de dados Browse): Permitir a navegação de ficheiros de registo VM para resolver problemas na criação e eliminação de instantâneos.<br/><br/> - **Datastore.FileManagement** (Datastore -> Operações de ficheiros de nível baixo): Permitir operações de leitura/escrita/exclusão/renome no navegador datastore, para resolver problemas na criação e eliminação de instantâneos.<br/><br/> - **VirtualMachine.Config. ChangeTracking** (Rastreio de alteração de > de alteração do disco da máquina virtual): Permitir ativar ou desativar o rastreio de alterações dos discos VM, para puxar blocos de dados alterados entre instantâneos.<br/><br/> - **VirtualMachine.Config. DiskLease** (máquina virtual -> aluguer de disco): Permitir operações de locação de discos para um VM, ler o disco utilizando o VMware vSphere Virtual Disk Development Kit (VDDK).<br/><br/> - **VirtualMachine.Provisioning.DiskRandomRead** (máquina virtual -> Provisioning -> Permitir o acesso ao disco apenas de leitura): Permitir a abertura de um disco num VM, para ler o disco utilizando o VDDK.<br/><br/> - **VirtualMachine.Provisioning.DiskRandomAccess** (máquina virtual -> Provisioning -> Permitir o acesso ao disco): Permitir a abertura de um disco num VM, para ler o disco utilizando o VDDK.<br/><br/> - **VirtualMachine.Provisioning.GetVmFiles** (Máquina virtual -> Provisioning -> Permitir o download de máquinas virtuais): Permite ler operações em ficheiros associados a um VM, descarregar os registos e resolução de problemas em caso de falha.<br/><br/> - **VirtualMachine.State. \* *_ (Gestão virtual de > Snapshot): Permitir a criação e gestão de instantâneos VM para replicação. <br/> <br/> - _* VirtualMachine.Interact.PowerOff** (Máquina virtual -> Interação -> Desligar): Permitir que o VM seja desligado durante a migração para Azure.



### <a name="vm-requirements-agentless"></a>Requisitos de VM (sem agente)

A tabela resume os requisitos de migração sem agente para VMware VMs.

**Suporte** | **Detalhes**
--- | ---
**Sistemas operativos suportados** | Pode migrar sistemas operativos [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) e [Linux](../virtual-machines/linux/endorsed-distros.md) que são suportados pelo Azure.
**Windows VMs em Azure** | Talvez precise de [fazer algumas alterações](prepare-for-migration.md#verify-required-changes-before-migrating) nos VM antes da migração. 
**Linux VMs em Azure** | Alguns VMs podem necessitar de alterações para que possam ser executados em Azure.<br/><br/> Para o Linux, a Azure Migrate faz as alterações automaticamente para estes sistemas operativos:<br/> - Red Hat Enterprise Linux 7.8, 7.7, 7.6, 7.5, 7.4, 7.0, 6.x<br/> - Cent OS 7.7, 7.6, 7.5, 7.4, 6.x</br> - SUSE Linux Enterprise Server 12 SP1+<br/> - SUSE Linux Enterprise Server 15 SP1 <br/>- Ubuntu 19.04, 19.10, 14.04LTS, 16.04LTS, 18.04LTS<br/> - Debian 7, 8 <br/> Oracle Linux 7.7, 7.7-CI<br/> Para outros sistemas operativos, es faça as [alterações necessárias](prepare-for-migration.md#verify-required-changes-before-migrating) manualmente.
**Bota Linux** | Se o arranque estiver numa divisória dedicada, deve residir no disco OS e não ser espalhado por vários discos.<br/> Se /boot é parte da raiz (/) partição, então a partição '/' deve estar no disco DE, e não abranger outros discos.
**Bota UEFI** | Suportado. Os VMs baseados na UEFI serão migrados para a geração Azure 2 VMs. 
**Tamanho do disco** | 2 Disco de SO TB;  32 TB para discos de dados.
**Limites de disco** |  Até 60 discos por VM.
**Discos/volumes encriptados** | VMs com discos/volumes encriptados não são suportados para migração.
**Aglomerado de disco compartilhado** | Não suportado.
**Discos independentes** | Não suportado.
**Discos RDM/passthrough** | Se os VMs tiverem discos RDM ou passthrough, estes discos não serão replicados para Azure.
**NFS** | Os volumes NFS montados à medida que os volumes nos VMs não serão replicados.
**metas iSCSI** | VMs com alvos iSCSI não são suportados para migração sem agentes.
**IO multipata** | Não suportado.
**Armazenamento vMotion** | Não suportado. A replicação não funcionará se um VM utilizar o armazenamento vMotion.
**NICs em equipa** | Não suportado.
**IPv6** | Não suportado.
**Disco-alvo** | Os VMs só podem ser migrados para discos geridos (HDD padrão, SSD padrão, SSD premium) em Azure.
**Replicação simultânea** | 300 VMs por vCenter Server. Se tiver mais, migra-os em lotes de 300.
**Instalação automática do agente Azure VM (Windows Agent)** | Suportado para Windows Server 2008 R2 em diante.

### <a name="appliance-requirements-agentless"></a>Requisitos do aparelho (sem agente)

A migração sem agentes utiliza o [aparelho Azure Migrate](migrate-appliance.md). Pode implantar o aparelho como VMware VM usando um modelo OVA, importado para o vCenter Server, ou utilizando um [script PowerShell](deploy-appliance-script.md).

- Saiba mais sobre [os requisitos do aparelho](migrate-appliance.md#appliance---vmware) para o VMware.
- Saiba mais sobre URLs que o aparelho precisa de aceder em nuvens [públicas](migrate-appliance.md#public-cloud-urls) e [governamentais.](migrate-appliance.md#government-cloud-urls)
- No Governo de Azure, deve utilizar o aparelho [utilizando o guião](deploy-appliance-script-government.md).

### <a name="port-requirements-agentless"></a>Requisitos portuários (sem agente)

**Dispositivo** | **Ligação**
--- | ---
Aparelho | Ligações de saída no porto 443 para carregar dados replicados para Azure, e para comunicar com os serviços da Azure Migrate orquestrando replicação e migração.
vCenter Server | Ligações de entrada na porta 443 para permitir que o aparelho orquestende a replicação - criar instantâneos, copiar dados, lançar instantâneos
vSphere/ESXI anfitrião | Entrada na porta TCP 902 para que o aparelho reproduza dados de instantâneos.

## <a name="agent-based-migration"></a>Migração baseada em agente 


Esta secção resume os requisitos para a migração baseada em agentes.


### <a name="vmware-requirements-agent-based"></a>Requisitos VMware (baseado em agente)

Esta tabela resume suporte de avaliação e limitações para servidores de virtualização VMware.

**Requisitos de VMware** | **Detalhes**
--- | ---
**VMware vCenter Server** | Versão 5.5, 6.0, 6.5 ou 6.7.
**VMware vSphere ESXI anfitrião** | Versão 5.5, 6.0, 6.5 ou 6.7.
**permissões vCenter Server** | Uma conta só de leitura para o servidor vCenter.

### <a name="vm-requirements-agent-based"></a>Requisitos de VM (baseados em agente)

A tabela resume o suporte VMware VM para VMware VMs que pretende migrar usando migração baseada em agentes.

**Suporte** | **Detalhes**
--- | ---
**Carga de trabalho da máquina** | A Azure Migrate suporta a migração de qualquer carga de trabalho (por exemplo, Ative Directory, servidor SQL, etc.) que funciona numa máquina suportada.
**Sistemas operativos** | Para obter as informações mais recentes, reveja o suporte do [sistema operativo](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) para recuperação do local. A Azure Migrate fornece suporte ao sistema operativo VM idêntico.
**Sistema de ficheiros Linux/armazenamento de hóspedes** | Para obter as informações mais recentes, reveja o suporte do [sistema de ficheiros Linux](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) para recuperação do site. Azure Migrate tem suporte idêntico do sistema de ficheiros Linux.
**Rede/Armazenamento** | Para obter as informações mais recentes, reveja os pré-requisitos de [rede](../site-recovery/vmware-physical-azure-support-matrix.md#network) e [armazenamento](../site-recovery/vmware-physical-azure-support-matrix.md#storage) para a Recuperação do Local. A Azure Migrate fornece requisitos idênticos de rede/armazenamento.
**Requisitos do Azure** | Para obter as informações mais recentes, reveja a [rede Azure,](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover) [armazenamento](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)e requisitos [de cálculo](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) para recuperação de locais. A Azure Migrate tem requisitos idênticos para a migração de VMware.
**Serviço de Mobilidade** | O agente de serviço de mobilidade deve ser instalado em cada VM que pretende migrar.
**Bota UEFI** | Suportado. Os VMs baseados na UEFI serão migrados para a geração Azure 2 VMs. 
**UEFI - Arranque seguro**         | Não apoiado para a migração.
**Disco-alvo** | Os VMs só podem ser migrados para discos geridos (HDD padrão, SSD padrão, SSD premium) em Azure.
**Tamanho do disco** | 2 Disco de SO TB; 32 TB para discos de dados.
**Limites de disco** |  Até 63 discos por VM.
**Discos/volumes encriptados** | VMs com discos/volumes encriptados não são suportados para migração.
**Aglomerado de disco compartilhado** | Não suportado.
**Discos independentes** | Suportado.
**Discos passthrough** | Suportado.
**NFS** | Os volumes NFS montados à medida que os volumes nos VMs não serão replicados.
**metas iSCSI** | Suportado.
**IO multipata** | Não suportado.
**Armazenamento vMotion** | Suportado
**NICs em equipa** | Não suportado.
**IPv6** | Não suportado.




### <a name="appliance-requirements-agent-based"></a>Requisitos do aparelho (baseado em agente)

Quando configura o aparelho de replicação utilizando o modelo OVA fornecido no hub Azure Migrate, o aparelho executa o Windows Server 2016 e cumpre os requisitos de suporte. Se configurar o aparelho de replicação manualmente num servidor físico, certifique-se de que está em conformidade com os requisitos.

- Saiba mais sobre [os requisitos do aparelho](migrate-replication-appliance.md#appliance-requirements) de replicação para VMware.
- O MySQL deve ser instalado no aparelho. Saiba mais sobre [as opções de instalação.](migrate-replication-appliance.md#mysql-installation)
- Saiba mais sobre URLs que o aparelho de replicação precisa de aceder em nuvens [públicas](migrate-replication-appliance.md#url-access) e [governamentais.](migrate-replication-appliance.md#azure-government-url-access)
- Reveja as [portas](migrate-replication-appliance.md#port-access) a que o aparelho de replicação precisa de aceder.

### <a name="port-requirements-agent-based"></a>Requisitos portuários (baseados em agente)

**Dispositivo** | **Ligação**
--- | ---
VMs | O serviço de Mobilidade em execução em VMs comunica com o aparelho de replicação no local (servidor de configuração) na entrada HTTPS 443, para gestão de replicação.<br/><br/> Os VMs enviam dados de replicação para o servidor de processo (em execução na máquina do servidor de configuração) na entrada HTTPS 9443. Esta porta pode ser modificada.
Aparelho de replicação | O aparelho de replicação orquestra a replicação com o Azure sobre a porta HTTPS 443.
Servidor de processo | O servidor de processo recebe dados de replicação, otimiza e encripta-os e envia-os para o armazenamento Azure sobre a porta 443 de saída.<br/> Por predefinição, o servidor de processo funciona no aparelho de replicação.

## <a name="azure-vm-requirements"></a>Requisitos da VM do Azure

Todos os VMs no local replicados ao Azure (com migração sem agente ou com base em agentes) devem satisfazer os requisitos Azure VM resumidos nesta tabela. 

**Componente** | **Requisitos** 
--- | --- | ---
Sistema operativo convidado | Verifica sistemas operativos VMware VMware para migração.<br/> Pode migrar qualquer carga de trabalho em execução num sistema operativo suportado. 
Arquitetura do sistema operativo convidado | 64-bit. 
Tamanho do disco do sistema operativo | Até 2.048 GB. 
Contagem de discos do sistema operativo | 1 
Contagem de discos de dados | 64 ou menos. 
Tamanho do disco de dados | Até 8.095 GB
Placas de rede | Vários adaptadores são suportados.
VHD partilhado | Não suportado. 
Disco FC | Não suportado. 
BitLocker | Não suportado.<br/><br/> O BitLocker tem de ser desativado antes de migrar a máquina.
o nome da VM | De 1 a 63 caracteres.<br/><br/> Limitado a letras, números e hífenes.<br/><br/> O nome da máquina deve começar e terminar com uma letra ou número. 
Conecte-se após a migração-Windows | Para ligar aos VMs Azure que executam o Windows após a migração:<br/><br/> - Antes da migração, permita pDR nas VM no local.<br/><br/> Confirme que são adicionadas regras de TCP e UDP ao perfil **Público** e que o protocolo RDP é permitido em **Firewall do Windows** > **Aplicações Permitidas** para todos os perfis.<br/><br/> Para acesso local-a-site VPN, ative o RDP e permita o RDP em  ->  **aplicações e funcionalidades permitidas** para o Windows Firewall e para redes **de domínio e privado.**<br/><br/> Além disso, verifique se a política SAN do sistema operativo está definida para **OnlineAll**. [Saiba mais](prepare-for-migration.md).
Conecte-se após migração-Linux | Para ligar aos VMs Azure após a migração utilizando SSH:<br/><br/> Antes da migração, na máquina no local, verifique se o serviço Secure Shell está definido para iniciar e que as regras de firewall permitem uma ligação SSH.<br/><br/> Após o failover, no Azure VM, permita a entrada de ligações à porta SSH para as regras do grupo de segurança da rede sobre o falhado sobre vM, e para a sub-rede Azure à qual está ligada.<br/><br/> Além disso, adicione um endereço IP público para o VM.  


## <a name="next-steps"></a>Passos seguintes

[Selecione](server-migrate-overview.md) uma opção de migração VMware.
