---
title: Apoio à migração de VMware em Azure Migrate
description: Conheça o suporte à migração VMware VM em Azure Migrate.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 9d8dc4dadc975a0fb69ea207f6062b72231460ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269513"
---
# <a name="support-matrix-for-vmware-migration"></a>Matriz de suporte para migração vMware

Este artigo resume as definições de suporte e limitações para vMware migratório vMware com [Azure Migrate: Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) . Se procura informações sobre a avaliação de VMware VMs para migração para Azure, reveja a matriz de suporte de [avaliação](migrate-support-matrix-vmware.md).


## <a name="migration-options"></a>Opções de migração

Pode migrar VMware VMs de várias maneiras:

- Com migração sem agente: Migram VMs sem necessidade de instalar nada neles. Implanta o [aparelho Azure Migrate](migrate-appliance.md) para migração sem agente.
- Com migração baseada em agente: Instale um agente no VM para replicação. Para a migração baseada no agente, é necessário utilizar um aparelho de [replicação](migrate-replication-appliance.md).

Reveja [este artigo](server-migrate-overview.md) para descobrir qual o método que pretende utilizar.

## <a name="migration-limitations"></a>Limitações da migração

- Pode selecionar até 10 VMs de uma só vez para replicação. Se quiser migrar mais máquinas, reproduza-se em grupos de 10.
- Para a migração sem agente VMware, pode executar até 100 replicações simultaneamente.

## <a name="agentless-vmware-servers"></a>Servidores de VMware sem agente

**VMware** | **Detalhes**
--- | ---
**VMware vCenter Server** | Versão 5.5, 6.0, 6.5 ou 6.7.
**VMware vSphere ESXI hospedeiro** | Versão 5.5, 6.0, 6.5 ou 6.7.
**vCenter Server permissões** | A migração sem agente utiliza o [Aparelho migratório](migrate-appliance.md). O aparelho necessita destas permissões:<br/><br/> - **Datastore.Browse:** Permitir a navegação de ficheiros de registo VM para resolver a criação e eliminação de instantâneos.<br/><br/> - **Datastore.LowLevelFileOperations**: Permitir operações de leitura/escrita/exclusão/renome no navegador da datastore, para resolver a criação e eliminação de instantâneos.<br/><br/> - **VirtualMachine.Configuration.DiskChangeTracking**: Permitir ativar ou desativar o rastreio de alterações dos discos VM, para puxar blocos alterados de dados entre instantâneos.<br/><br/> - **VirtualMachine.Configuration.DiskLease**: Permita operações de locação de discos para um VM, para ler o disco utilizando o Kit de Desenvolvimento virtual do disco VMware vSphere (VDDK).<br/><br/> - **VirtualMachine.Provisioning.AllowDiskAccess**: (especificamente para vSphere 6.0 ou superior) Permitir a abertura de um disco num VM para acesso de leitura aleatória no disco utilizando o VDDK.<br/><br/> - **VirtualMachine.Provisioning.AllowReadOnlyDiskAccess**: Permita abrir um disco num VM, para ler o disco utilizando o VDDK.<br/><br/> - **VirtualMachine.Provisioning.AllowDiskRandomAccess**: Permita abrir um disco num VM, para ler o disco utilizando o VDDK.<br/><br/> - **VirtualMachine.Provisioning.AllowVirtualMachineDownload**: Permite a leitura de operações em ficheiros associados a um VM, para descarregar os registos e resolução de problemas se ocorrer falha.<br/><br/> - **VirtualMachine.SnapshotManagement.***: Permitir a criação e gestão de instantâneos VM para replicação.<br/><br/> - **Máquina Virtual.Interaction.Power Off**: Permitir que o VM seja desligado durante a migração para Azure.



## <a name="agentless-vmware-vms"></a>VMs sem agente-VMware

**Suporte** | **Detalhes**
--- | ---
**Sistemas operativos suportados** | Os sistemas operativos [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) e [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) que são suportados pelo Azure podem ser migrados através de migração sem agente.
**Alterações necessárias para O Azure** | Alguns VMs podem exigir alterações para que possam correr em Azure. A Azure Migrate faz estas alterações automaticamente para os seguintes sistemas operativos:<br/> - Red Hat Enterprise Linux 6.5+, 7.0+<br/> - Centos 6.5+, 7.0+</br> - SUSE Linux Enterprise Server 12 SP1+<br/> - Ubuntu 14.04LTS, 16.04LTS, 18.04LTS<br/> - Debiano 7, 8<br/><br/> Para outros sistemas operativos, é necessário efazer ajustes manualmente antes da migração. Os artigos relevantes contêm instruções sobre como fazê-lo.
**Bota Linux** | Se /boot estiver numa divisória dedicada, deve residir no disco OS e não ser espalhado por vários discos.<br/> Se a /bota fizer parte da partição raiz (/), então a partição '/' deve estar no disco OS e não abranger outros discos.
**Bota UEFI** | VMs com bota UEFI não são suportados para migração.
**Tamanho do disco** | Disco de 2 TB OS; 4 TB para discos de dados.
**Limites de disco** |  Até 60 discos por VM.
**Discos/volumes encriptados** | VMs com discos/volumes encriptados não são suportados para migração.
**Cluster de disco partilhado** | Não suportado.
**Discos independentes** | Não suportado.
**Discos RDM/passthrough** | Se os VMs tiverem RDM ou discos de passagem, estes discos não serão replicados para o Azure.
**NFS** | Os volumes nFS montados à medida que os volumes nos VMs não serão replicados.
**metas iSCSI** | VMs com alvos iSCSI não são suportados para migração sem agente.
**IO multipata** | Não suportado.
**Armazenamento vMotion** | Não suportado. A replicação não funcionará se um VM usar o armazenamento vMotion.
**NICs em equipa** | Não suportado.
**IPv6** | Não suportado.
**Disco-alvo** | Os VMs só podem ser migrados para discos geridos (HDD padrão, SSD premium) em Azure.
**Replicação simultânea** | 100 VMs por vCenter Server. Se tiver mais, emigra-os em lotes de 100.


## <a name="agentless-azure-migrate-appliance"></a>Aparelho migratório Agenteless-Azure 
A migração sem agente utiliza o aparelho Azure Migrate, implantado num VMware VM.

- Conheça os [requisitos](migrate-appliance.md#appliance---vmware) do aparelho para vMware.
- Saiba mais sobre [os URLs](migrate-appliance.md#url-access) a que o aparelho precisa de aceder.

## <a name="agentless-ports"></a>Portas sem agente

**Dispositivo** | **Conexão**
--- | ---
Aparelho | Ligações de saída na porta 443 para carregar dados replicados para Azure, e para comunicar com os serviços da Azure Migrate orquestrando a replicação e a migração.
vCenter Server | Ligações de entrada na porta 443 para permitir que o aparelho orquequequea replicação - crie instantâneos, dados de cópia, liberte instantâneos
hospedeiro vSphere/EXSI | Aderà entrada na porta 902 do TCP para que o aparelho reproduza dados a partir de instantâneos.


## <a name="agent-based-vmware-servers"></a>Servidores VMware baseados em agente
Esta tabela resume o suporte de avaliação e as limitações para servidores de virtualização VMware.

**Requisitos vMware** | **Detalhes**
--- | ---
**VMware vCenter Server** | Versão 5.5, 6.0, 6.5 ou 6.7.
**VMware vSphere ESXI hospedeiro** | Versão 5.5, 6.0, 6.5 ou 6.7.
**vCenter Server permissões** | Uma conta só de leitura para o VCenter Server.

## <a name="agent-based-vmware-vms"></a>VMs baseados em VMware baseados em agente

A tabela resume o suporte vM vm vm para VMware VMs que você quer migrar usando migração baseada em agente.

**Suporte** | **Detalhes**
--- | ---
**Carga de trabalho da máquina** | A Azure Migrate suporta a migração de qualquer carga de trabalho (por exemplo, Ative Directory, servidor SQL, etc.) em funcionamento numa máquina suportada.
**Sistemas operativos** | Para obter as informações mais recentes, reveja o suporte do [sistema operativo](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) para a Recuperação do Site. A Azure Migrate fornece suporte idêntico ao sistema operativo VM.
**Sistema de ficheiros Linux/armazenamento de hóspedes** | Para obter as informações mais recentes, reveja o suporte do sistema de [ficheiros Linux](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) para a Recuperação do Site. A Azure Migrate tem suporte idêntico ao sistema de ficheiros Linux.
**Rede/Armazenamento** | Para obter as informações mais recentes, reveja os pré-requisitos de [rede](../site-recovery/vmware-physical-azure-support-matrix.md#network) e [armazenamento](../site-recovery/vmware-physical-azure-support-matrix.md#storage) para a Recuperação do Site. A Azure Migrate fornece requisitos idênticos de rede/armazenamento.
**Requisitos do Azure** | Para obter as mais recentes informações, reveja os requisitos da [rede Azure,](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover) [armazenamento](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)e [cálculo](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) para a Recuperação do Site. A Azure Migrate tem requisitos idênticos para a migração de VMware.
**Serviço de mobilidade** | O agente de serviço de Mobilidade deve ser instalado em cada VM que pretende migrar.
**Bota UEFI** | O VM migrado em Azure será automaticamente convertido para um VM de arranque BIOS.<br/><br/> O disco osso deve ter até quatro divisórias, e os volumes devem ser formatados com NTFS.
**Disco-alvo** | Os VMs só podem ser migrados para discos geridos (HDD padrão, SSD premium) em Azure.
**Tamanho do disco** | Disco de 2 TB OS; 8 TB para discos de dados.
**Limites de disco** |  Até 63 discos por VM.
**Discos/volumes encriptados** | VMs com discos/volumes encriptados não são suportados para migração.
**Cluster de disco partilhado** | Não suportado.
**Discos independentes** | Suportado.
**Discos de passagem** | Suportado.
**NFS** | Os volumes nFS montados à medida que os volumes nos VMs não serão replicados.
**metas iSCSI** | VMs com alvos iSCSI não são suportados para migração sem agente.
**IO multipata** | Não suportado.
**Armazenamento vMotion** | Suportado
**NICs em equipa** | Não suportado.
**IPv6** | Não suportado.




## <a name="agent-based-replication-appliance"></a>Aparelho de replicação baseado em agente 

Quando configura o aparelho de replicação utilizando o modelo OVA fornecido no hub De migração Azure, o aparelho executa o Windows Server 2016 e cumpre os requisitos de suporte. Se configurar manualmente o aparelho de replicação num servidor físico, certifique-se de que está em conformidade com os requisitos.

- Conheça os [requisitos do aparelho](migrate-replication-appliance.md#appliance-requirements) de replicação para vMware.
- O MySQL deve ser instalado no aparelho. Saiba mais sobre as opções de [instalação.](migrate-replication-appliance.md#mysql-installation)
- Saiba mais sobre [URLs](migrate-replication-appliance.md#url-access) e [portas](migrate-replication-appliance.md#port-access) que o aparelho de replicação precisa de aceder.

## <a name="agent-based-ports"></a>Portas baseadas em agentes

**Dispositivo** | **Conexão**
--- | ---
VMs | O serviço de Mobilidade em funcionamento em VMs comunica com o aparelho de replicação no local (servidor de configuração) na entrada da porta HTTPS 443, para gestão de replicação.<br/><br/> Os VMs enviam dados de replicação para o servidor de processo (funcionando na máquina do servidor de configuração) na entrada da porta HTTPS 9443. Esta porta pode ser modificada.
Aparelho de replicação | O aparelho de replicação orquestra a replicação com Azure sobre a saída da porta HTTPS 443.
Servidor de processos | O servidor de processos recebe dados de replicação, otimiza e encripta-os, e envia-os para o armazenamento Azure sobre a saída da porta 443.<br/> Por defeito, o servidor de processo funciona no aparelho de replicação.

## <a name="azure-vm-requirements"></a>Requisitos da VM do Azure

Todos os VMs no local replicados ao Azure devem satisfazer os requisitos de VM Azure resumidos nesta tabela. Quando a Recuperação do Site executa um pré-requisito para verificar a replicação, a verificação falhará se alguns dos requisitos não forem cumpridos.

**Componente** | **Requisitos** | **Detalhes**
--- | --- | ---
Sistema operativo convidado | Verifica sistemas operativos VMware VM suportados para migração.<br/> Pode migrar qualquer carga de trabalho em funcionamento num sistema operativo suportado. | A verificação falha se não for suportada.
Arquitetura do sistema operativo convidado | 64-bit. | A verificação falha se não for suportada.
Tamanho do disco do sistema operativo | Até 2.048 GB. | A verificação falha se não for suportada.
Contagem de discos do sistema operativo | 1 | A verificação falha se não for suportada.
Contagem de discos de dados | 64 ou menos. | A verificação falha se não for suportada.
Tamanho do disco de dados | Até 4.095 GB | A verificação falha se não for suportada.
Placas de rede | São suportados múltiplos adaptadores. |
VHD partilhado | Não suportado. | A verificação falha se não for suportada.
Disco FC | Não suportado. | A verificação falha se não for suportada.
BitLocker | Não suportado. | O BitLocker deve ser desativado antes de ativar a replicação de uma máquina.
o nome da VM | De 1 a 63 caracteres.<br/> Limitado a letras, números e hífenes.<br/><br/> O nome da máquina deve começar e terminar com uma letra ou número. |  Atualize o valor nas propriedades da máquina na Recuperação do Site.
Conecte-se após a migração-Windows | Para ligar aos VMs Azure que executam o Windows após a migração:<br/> - Antes que a migração permita o PDR no VM no local. Confirme que são adicionadas regras de TCP e UDP ao perfil **Público** e que o protocolo RDP é permitido em **Firewall do Windows** > **Aplicações Permitidas** para todos os perfis.<br/> Para acesso VPN site-to-site, ative rdP e permita RDP em **Windows Firewall** -> **Permitida aplicações e funcionalidades** para redes **De domínio e privadas.** Além disso, verifique se a política SAN do sistema operativo está definida para **OnlineAll**. [Saiba mais](prepare-for-migration.md). |
Conecte-se após a migração-Linux | Para ligar aos VMs Azure após a migração utilizando o SSH:<br/> Antes da migração, na máquina no local, verifique se o serviço Secure Shell está definido para iniciar e que as regras de firewall permitem uma ligação SSH.<br/> Após a falha, no Azure VM, permitir ligações de entrada à porta SSH para as regras do grupo de segurança da rede sobre o VM falhado, e para a subnet Azure a que está conectado. Além disso, adicione um endereço IP público para o VM. |  


## <a name="next-steps"></a>Passos seguintes

[Selecione](server-migrate-overview.md) uma opção de migração VMware.
