---
title: Matriz de suporte do Azure para migrar para a migração e de avaliação de VMware
description: Resume as definições de suporte e limitações para avaliação e migração de VMs de VMware para o Azure com o serviço Azure Migrate.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: raynew
ms.openlocfilehash: 567a6582e193208a7ff4aa37bafefe1dec4f4e8f
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811586"
---
# <a name="support-matrix-for-vmware-assessment-and-migration"></a>Matriz de suporte para a migração e de avaliação de VMware

Pode utilizar o [serviço Azure Migrate](migrate-overview.md) para avaliar e migrar as máquinas para a cloud do Microsoft Azure. Este artigo resume as definições de suporte e limitações para avaliar e migrar VMs de VMware no local.


## <a name="vmware-scenarios"></a>Cenários de VMware

A tabela resume os cenários suportados para VMs de VMware.

**Implementação** | **Detalhes** 
--- | --- 
**Avaliar VMs VMware no local** | [Configurar](tutorial-prepare-vmware.md) sua avaliação primeiro.<br/><br/> [Executar](scale-vmware-assessment.md) uma avaliação de grande escala.
**Migrar VMs de VMware** | Pode migrar com a migração sem agente, com algumas limitações ou utilizar uma migração com base em agente. [Saiba mais](server-migrate-overview.md)


    


## <a name="azure-migrate-projects"></a>Projetos de migração do Azure

**Suporte** | **Detalhes**
--- | ---
Permissões do Azure | Precisa de permissões de proprietário ou contribuinte na subscrição para criar um projeto do Azure Migrate.
Limitações de VMware  | Avalie até 35 000 VMs de VMware num único projeto.

Um projeto pode incluir as VMs de VMware e VMs de Hyper-V, até aos limites de avaliação.

## <a name="assessment-vmware-server-requirements"></a>Requisitos do servidor VMware de avaliação

A tabela seguinte resume o suporte de avaliação e limitações para servidores de Virtualização do VMware.

**Suporte** | **Detalhes**
--- | ---
**Servidor vCenter** | VMs de VMware que pretende avaliar tem de ser geridas por um ou mais servidores vCenter com o 5.5, 6.0, 6.5 ou 6.7.

## <a name="assessment-vcenter-server-permissions"></a>Permissões do servidor vCenter de avaliação

Para avaliação, precisa de uma conta só de leitura para o vCenter Server.

## <a name="assessment-appliance-requirements"></a>Requisitos de aplicação de avaliação

**Suporte** | **Detalhes**
--- | ---
**ESXi** | A aplicação da VM tem de ser implementada num anfitrião ESXi com a versão 5.5 ou posterior.
**Projeto de migração do Azure** | Uma aplicação pode ser associada um único projeto.
**vCenter Server** | Uma aplicação pode detetar até 10 000 VMware VMs num vCenter Server.<br/> Uma aplicação pode ligar a um vCenter Server.


## <a name="assessment-url-access-requirements"></a>Requisitos de acesso de URL de avaliação

A aplicação do Azure Migrate necessita de conectividade de internet para a internet.

- Ao implementar a aplicação, o Azure Migrate faz uma verificação de conectividade para os URLs resumidos na tabela abaixo.
- Se estiver a utilizar um firewall.proxy baseado em URL, permita o acesso a estes URLs, certificar-se de que o proxy resolve quaisquer registos CNAME receberam ao pesquisar os URLs.

**URL** | **Detalhes**  
--- | --- |
*.portal.azure.com  | Navegue para o Azure Migrate no portal do Azure.
\*.windows.net | Inicie sessão na subscrição do Azure.
*.microsoftonline.com | Crie aplicações do Active Directory para a aplicação comunicar com o serviço Azure Migrate.
management.azure.com | Crie aplicações do Active Directory para a aplicação comunicar com o serviço Azure Migrate.
dc.services.visualstudio.com | Carregar registos de aplicação para monitorizar interno.
*.vault.azure.net | Gerir segredos num cofre de chaves do Azure.
*.servicebus.windows.net | Comunicação entre a aplicação e o serviço Azure Migrate.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | Ligar a URLs do serviço Azure Migrate.
*.blob.core.windows.net | Carregar dados para contas de armazenamento.


## <a name="assessment-port-requirements"></a>Requisitos de portas de avaliação

**dispositivo** | **ligação**
--- | --- 
Aplicação | Ligações de entrada na porta TCP 3389 para permitir ligações de ambiente de trabalho remotas para a aplicação.<br/> Ligações na porta 44368 para aceder remotamente a aplicação de gestão da aplicação através do URL de entrada: https://<appliance-ip-or-name>:44368 <br/>Ligações de saída na porta 443 para enviar metadados de deteção e de desempenho para o Azure Migrate.
servidor vCenter | Ligações na porta TCP 443 para permitir que a aplicação recolher os metadados de configuração e desempenho para obter avaliações de entrada. <br/> A aplicação da liga-se ao vCenter na porta 443 por predefinição. Se o vCenter server escuta numa porta diferente, pode modificar a porta ao configurar a deteção.


## <a name="agentless-migration-vmware-server-requirements"></a>Requisitos do servidor VMware migração sem agente

A tabela seguinte resume o suporte de avaliação e limitações para servidores de Virtualização do VMware.

**Suporte** | **Detalhes**
--- | ---
**Servidor vCenter** | VMs de VMware, que migrar com uma migração sem agente tem de ser geridas por um ou mais servidores vCenter com o 5.5, 6.0, 6.5 ou 6.7.

## <a name="agentless-migration-vcenter-server-permissions"></a>Permissões do servidor vCenter de migração de sem agente

**Permissões** | **Detalhes**
--- | --- 
Datastore.Browse | Permita a navegação na VM ficheiros de registo para resolver problemas de criação do instantâneo e eliminação.
Datastore.LowLevelFileOperations | Permitir operações de leitura/escrita/delete/mudança de nome no browser do arquivo de dados, para resolver problemas de criação do instantâneo e eliminação.
VirtualMachine.Configuration.DiskChangeTracking | Permitir ativar ou desativar o registo de alterações de discos VM, para extrair os blocos alterados de dados entre os instantâneos
VirtualMachine.Configuration.DiskLease | Permitir operações de concessão de disco para uma VM, para ler o disco usando o Kit de desenvolvimento de disco Virtual (VDDK) do VMware vSphere.
VirtualMachine.Provisioning.AllowReadOnlyDiskAccess | Permita abertura de um disco numa VM, para ler o disco com o VDDK.
VirtualMachine.Provisioning.AllowVirtualMachineDownload  | Permite operações de leitura em ficheiros associados a uma VM, para transferir os registos e resolver problemas se ocorrer uma falha. 
VirtualMachine.SnapshotManagement.* | Permitir a criação e gestão de instantâneos VM para replicação. 
Machine.Interaction.Power virtual desativado | Permitir que a VM seja desligado durante a migração para o Azure.


## <a name="agentless-migration-vmware-vm-requirements"></a>Requisitos de VM de VMware de migração sem agente

**Suporte** | **Detalhes**
--- | ---
**Sistemas operativos suportados** | [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) e [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) sistemas operativos que são suportados pelo Azure pode ser migrados utilizando a migração sem agente.
**Alterações necessárias para o Azure** | Algumas VMs podem exigir alterações para que eles podem ser executados no Azure. O Azure Migrate torna estas alterações automaticamente para os seguintes sistemas operativos:<br/> - Red Hat Enterprise Linux 6.5+, 7.0+<br/> - CentOS 6.5+, 7.0+</br> - SUSE Linux Enterprise Server 12 SP1+<br/> - Ubuntu 14.04LTS, 16.04LTS, 18.04LTS<br/> - Debian 7,8<br/><br/> Para outros sistemas operacionais, precisa fazer ajustes manualmente antes da migração. Os artigos relevantes contém instruções sobre como fazer isso.
**Arranque do Linux** | Se for /boot numa partição dedicada, devem residir no disco do SO e não serão distribuído por vários discos.<br/> Se /boot faz parte da partição de raiz (/), em seguida, a partição '/' deve ser no disco do SO e não abrangem outros discos.
**Arranque UEFI** | As VMs com arranque UEFI não são suportadas para migração.
**Discos/volumes encriptados** | As VMs com discos/volumes encriptados não são suportadas para migração.
**Discos RDM/pass-through** | Se as VMs têm discos RDM ou pass-through, estes discos não ser replicados para o Azure.
**NFS** | Volumes NFS montados como volumes nas VMs não ser replicadas.
**Disco de destino** | As VMs só podem ser migradas para os managed disks (HHD standard, premium SSD) no Azure.


## <a name="agentless-migration-appliance-requirements"></a>Requisitos de migração sem agente-da aplicação


**Suporte** | **Detalhes**
--- | ---
**ESXi** | A aplicação da VM tem de ser implementada num anfitrião ESXi com a versão 5.5 ou posterior.
**Projeto de migração do Azure** | Uma aplicação pode ser associada um único projeto.
**vCenter Server** | Uma aplicação pode detetar até 10 000 VMware VMs num vCenter Server.<br/> Uma aplicação pode ligar a um vCenter Server.
**VDDK** | Se estiver a executar uma migração sem agente com a migração do servidor de migrar do Azure, tem de estar instalado o VMware vSphere Kit de desenvolvimento de disco Virtual (VDDK) na aplicação da VM.

## <a name="agentless-migration-url-access-requirements"></a>Requisitos de acesso de URL de migração sem agente

A aplicação do Azure Migrate necessita de conectividade de internet para a internet.

- Ao implementar a aplicação, o Azure Migrate faz uma verificação de conectividade para os URLs resumidos na tabela abaixo.
- Se estiver a utilizar um firewall.proxy baseado em URL, permita o acesso a estes URLs, certificar-se de que o proxy resolve quaisquer registos CNAME receberam ao pesquisar os URLs.

**URL** | **Detalhes**  
--- | --- 
*.portal.azure.com | Navegue para o Azure Migrate no portal do Azure.
\*.windows.net | Inicie sessão na subscrição do Azure.
*.microsoftonline.com | Crie aplicações do Active Directory para a aplicação comunicar com o serviço Azure Migrate.
management.azure.com | Crie aplicações do Active Directory para a aplicação comunicar com o serviço Azure Migrate.
dc.services.visualstudio.com | Carregar registos de aplicação para monitorizar interno.
*.vault.azure.net | Gerir segredos num cofre de chaves do Azure.
*.servicebus.windows.net | Comunicação entre a aplicação e o serviço Azure Migrate.
*.discoverysrv.windowsazure.com<br/> *.migration.windowsazure.com<br/> *.hypervrecoverymanager.windowsazure.com | Ligar a URLs do serviço Azure Migrate.
*.blob.core.windows.net | Carregar dados para contas de armazenamento.


## <a name="agentless-migration-port-requirements"></a>Requisitos de porta de migração sem agente

**dispositivo** | **ligação**
--- | --- 
Aplicação | Porta de saída de TCP 3389 para carregar os dados replicados para o Azure e para comunicar com o Azure Migrate para a replicação e a migração.
servidor vCenter | Ligações de entrada na porta TCP 443 para permitir que a aplicação orquestrar a replicação - criar instantâneos, dados de cópia, instantâneos de versão
anfitrião vSphere/ESXI | Entrada na porta TCP 902 para a aplicação da replicar dados a partir de instantâneos. 


## <a name="agent-based-migration-vmware-server-requirements"></a>Requisitos do servidor VMware de migração baseada em agente

A tabela seguinte resume o suporte de avaliação e limitações para servidores de Virtualização do VMware.

**Suporte** | **Detalhes**
--- | ---
**servidor do vCenter/ESXI** | Migrar VMs do VMware tem de ser geridas por um ou mais servidores de vCenter em execução 5.5, 6.0, 6.5 ou 6.7 ou em execução num anfitrião ESXI com a versão de vSphere 5.5, 6.0, 6.5 ou 6.7.

### <a name="agent-based-migration-vcenter-server-permissions"></a>Permissões do servidor vCenter de migração baseada em agente

**Permissões** | **Detalhes**
--- | --- 
Datastore.AllocateSpace | Permitir alocação de espaço num arquivo de dados para uma VM, instantâneo, clone ou um disco virtual. 
Datastore.Browse | Permita a navegação na VM ficheiros de registo para resolver problemas de criação do instantâneo e eliminação.
Datastore.LowLevelFileOperations | Permitir leitura, escrever, eliminar e mudar o nome de operações no browser do arquivo de dados para resolver problemas de criação/eliminação de instantâneos.
Datastore.UpdateVirtualMachineFiles | Permitir a atualização caminhos de ficheiros VM num arquivo de dados após o arquivo de dados ter sido resignatured.
Network.AssignNetwork | Permita a atribuição de uma rede a um recurso de VM.
AssignVirtualMachineToResourcePool | Permita a atribuição de uma VM para um agrupamento de recursos.
Resource.MigratePoweredOffVirtualMachine | Permita a migração de um alimentado VMS para um anfitrião ou agrupamento de recursos diferentes.
Resource.MigratePoweredOnVirtualMachine | Permita a migração usando o vMotion, de uma VM ligado a um agrupamento de recursos diferente ou o anfitrião.
Tasks.CreateTask | Permitir que uma extensão criar uma tarefa definida pelo utilizador.
Tasks.UpdateTask | Permitir que uma extensão atualizar uma tarefa definida pelo utilizador.
VirtualMachine.Configuration. | Permita configuração de dispositivos e opções de VM.
Virtual Machine.Interaction.AnswerQuestion | Permita a resolução de problemas relacionados com transições de estado VM ou erros de tempo de execução.
Virtual Machine.Interaction.DeviceConnection | Permitem alterar o estado de ligação de dispositivos virtuais disconnectable de uma VM.
Virtual Machine.Interaction.ConfigureCDMedia | Permitir a configuração de um dispositivo de DVD ou CD-ROM virtual.
Virtual Machine.Interaction.ConfigureFloppyMedia | Permitir a configuração de um dispositivo de disquete virtual.
Virtual Machine.Interaction.PowerOff | Permite que a VM seja desligado durante a migração para o Azure.
Virtual Machine.Interaction.PowerOn | Permitem ligar uma VM com tecnologia-off e retomar uma VM suspensa.
Virtual Machine.Interaction.VMwareToolsInstall | Permitir montar e desmontar o instalador do CD de ferramentas do VMware, como um CD-ROM para o sistema operativo convidado.
VirtualMachine.Inventory.CreateNew | Permitir a criação de uma VM e a alocação de recursos necessários.
VirtualMachine.Inventory.Register | Permitir a adição de uma VM existente para um servidor vCenter ou o inventário de anfitrião.
VirtualMachine.Inventory.Unregister | Permita anular o registo de um VMe a partir de um vCenter Server ou o inventário de anfitrião.
VirtualMachine.Provisioning.AllowVirtualMachineFilesUpload | Permitir operações de escrita em ficheiros associados a uma VM, incluindo o vmx, discos, registos e nvram.
VirtualMachine.Provisioning.AllowVirtualMachineDownload | Permitir operações de leitura em ficheiros associados a uma VM, para transferir os registos de resolução de problemas.
VirtualMachine.SnapshotManagement.RemoveSnapshot | Permitir a remoção de um instantâneo do histórico de instantâneo.

## <a name="agent-based-migration-replication-appliance-requirements"></a>Requisitos da aplicação de replicação de migração baseada em agente

Os requisitos para o [aplicação de replicação](migrate-replication-appliance.md) utilizado para a migração baseada em agente de VMs de VMware e físicas servidores com o Azure Migrate migração do servidor estão resumidos na tabela.

> [!NOTE]
> Quando configurou a aplicação de replicação com o modelo de OVA fornecido no hub do Azure Migrate, a aplicação executa o Windows Server 2016 e está em conformidade com os requisitos de suporte. Se configurar a aplicação de replicação manualmente num servidor físico, em seguida, certifique-se de que ele está em conformidade com os requisitos.



**Componente** | **Requisito** 
--- | ---
 | **Definições de VMware** (aplicação de VM de VMware)
**PowerCLI** | [Versão 6.0 do PowerCLI](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) deve ser instalado se a aplicação de replicação está em execução numa VM do VMware.
**Tipo NIC** | VMXNET3 (se a aplicação for uma VM do VMware)
 | **Definições de hardware** 
Núcleos de CPU | 8 
RAM | 16 GB
Número de discos | Três: O disco SO, um disco de cache do servidor de processo e unidade de retenção.
Espaço livre em disco (cache) | 600 GB
Espaço livre em disco (disco de retenção) | 600 GB
**Definições de software** | 
Sistema operativo | Windows Server 2016 ou Windows Server 2012 R2
Região do sistema operativo | Inglês (en-us)
TLS | TLS 1.2 deve estar ativada.
.NET Framework | .NET framework 4.6 ou posterior deve ser instalado no computador (com criptografia segura ativada.
MySQL | Deve ser instalado o MySQL na aplicação da.<br/> Deve ser instalado o MySQL. Pode instalar manualmente ou recuperação de sites pode instalá-lo durante a implementação da aplicação. 
Outras aplicações | Não deve executar outras aplicações na aplicação da replicação.
Funções do Windows Server | Não ative estas funções: <br> - Active Directory Domain Services <br>- Serviços de Informação da Internet <br> - Hyper-V 
Políticas de grupo | Não ative estas políticas de grupo: <br> -Impedi o acesso ao prompt de comando. <br> -Impedi o acesso a ferramentas de edição do registo. <br> -Lógica para anexos de ficheiros de fidedignidade. <br> – Ative a execução do Script. <br> [Saiba mais](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | -Sem Web site predefinido do já existente <br> -Sem preexistente/aplicação do Web site escute na porta 443 <br>-Permitir [autenticação anónima](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Ativar [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) definição 
**Definições de rede** | 
Tipo de endereço IP | Estático 
Portas | 443 (Canal de controlo e orquestração)<br>9443 (Transporte de dados) 
Tipo NIC | VMXNET3 

### <a name="replication-appliance-url-access"></a>Acesso de URL da aplicação de replicação

A aplicação de replicação tem acesso a estes URLs.

**URL** | **Detalhes**
--- | ---
\*.backup.windowsazure.com | Utilizado para transferência de dados replicados e coordenação
\*.store.core.windows.net | Utilizado para transferência de dados replicados e coordenação
\*.blob.core.windows.net | Utilizado para aceder à conta de armazenamento que armazena dados replicados
\*.hypervrecoverymanager.windowsazure.com | Utilizado para operações de gestão de replicação e coordenação
https:\//management.azure.com | Utilizado para operações de gestão de replicação e coordenação 
*.services.visualstudio.com | Utilizada para fins de telemetria (é opcional)
time.nist.gov | Utilizados para verificar a sincronização da hora entre o sistema e a hora global.
time.windows.com | Utilizados para verificar a sincronização da hora entre o sistema e a hora global.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https:\//login.live.com <br/> https:\//graph.windows.net <br/> https:\//login.windows.net <br/> https:\//www.live.com <br/> https:\//www.microsoft.com  | Configuração OVF precisa de aceder a estes URLs. São utilizados para gestão de identidades e de controlo de acesso ao Azure Active Directory
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | Para concluir o download do MySQL


#### <a name="mysql-installation-options"></a>Opções de instalação do MySQL

Pode ser instalado o MySQL na aplicação da replicação através de um dos seguintes métodos.

**Instalar** | **Detalhes**
--- | ---
Transferir e instalar manualmente | Transferir a aplicação MySQL & colocá-lo na pasta C:\Temp\ASRSetup e, em seguida, instalar manualmente.<br/> Quando configurou a aplicação MySQL mostrará como já instalada. 
Não transferir online | Coloque o aplicativo de instalação do MySQL na pasta C:\Temp\ASRSetup. Quando instalar a aplicação e clique em transferir e instalar o MySQL, a configuração irá utilizar o instalador que adicionou. 
Migrar de download do Azure | Quando instalar a aplicação e são-lhe pedido para o MySQL, selecione **transferir e instalar**.



## <a name="agent-based-migration-vmware-vm-requirements"></a>Requisitos de VM de VMware de migração baseada em agente

**Suporte** | **Detalhes**
--- | ---
**Carga de trabalho de máquina** | O Azure Migrate suporta a migração de qualquer carga de trabalho (Digamos Active Directory, do SQL server, etc.) em execução numa máquina.
**Sistemas operativos** | Para obter as informações mais recentes, reveja os [suporte do sistema operativo](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) para o Site Recovery. O Azure Migrate fornece suporte de sistema operativo de VMS idêntico.
**Armazenamento de convidado/sistema de ficheiros do Linux** | Para obter as informações mais recentes, reveja os [suporte de sistema de ficheiros de Linux](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) para o Site Recovery. O Azure Migrate tem suporte de sistema de ficheiros de Linux idêntico.
**Rede/armazenamento** | Para obter as informações mais recentes, reveja os [rede](../site-recovery/vmware-physical-azure-support-matrix.md#network) e [armazenamento](../site-recovery/vmware-physical-azure-support-matrix.md#storage) pré-requisitos para o Site Recovery. O Azure Migrate fornece os requisitos de rede idênticos/armazenamento.
**Requisitos do Azure** | Para obter as informações mais recentes, reveja os [rede do Azure](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover), [armazenamento](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage), e [computação](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) requisitos para a recuperação de Site. O Azure Migrate tem requisitos idênticos para migração do VMware.
**Serviço de mobilidade** | O agente do serviço de mobilidade tem de ser instalado em cada VM que pretende migrar.
**Disco de destino** | As VMs só podem ser migradas para os managed disks (HHD standard, premium SSD) no Azure.

   

## <a name="agent-based-migration-url-access-requirements"></a>Requisitos de acesso de URL de migração baseada em agente

O serviço de mobilidade em execução em VMs de VMware necessita de conectividade de internet para a internet.

- Ao implementar o serviço de mobilidade, ele faz uma verificação de conectividade para os URLs resumidos na tabela abaixo.
- Se estiver a utilizar um firewall.proxy baseado em URL, permita o acesso a estes URLs, certificar-se de que o proxy resolve quaisquer registos CNAME receberam ao pesquisar os URLs.

**URL** | **Detalhes**  
--- | --- 
*.portal.azure.com | Navegue para o Azure Migrate no portal do Azure.
\*.windows.net | Inicie sessão na subscrição do Azure.
*.microsoftonline.com | Crie aplicações do Active Directory para a aplicação comunicar com o serviço Azure Migrate. 
management.azure.com | Crie aplicações do Active Directory para a aplicação comunicar com o serviço Azure Migrate.
dc.services.visualstudio.com | Carregar registos de aplicação para monitorizar interno.
*.vault.azure.net | Gerir segredos num cofre de chaves do Azure.
*.servicebus.windows.net | Comunicação entre a aplicação e o serviço Azure Migrate.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | Ligar a URLs do serviço Azure Migrate.
*.blob.core.windows.net | Carregar dados para contas de armazenamento.

## <a name="agent-based-migration-port-requirements"></a>Requisitos de porta de migração baseada em agente

**dispositivo** | **ligação**
--- | --- 
VMs | O serviço de mobilidade em execução em VMs comunica com o servidor de configuração no local na porta HTTPS 443 entrada, para a gestão da replicação.<br/><br/> VMs enviam dados de replicação para o servidor de processos (em execução na máquina do servidor de configuração) na porta HTTPS 9443 de entrada. Esta porta pode ser modificada.
Aplicação de replicação | A aplicação de replicação orquestra a replicação com o Azure através da porta HTTPS 443 de saída.
Servidor de processos | O servidor de processos recebe dados de replicação, otimiza e criptografa- e envia-os para o armazenamento do Azure através da porta 443 saída.<br/> Por predefinição, o servidor de processos é executada na aplicação da replicação.

## <a name="azure-vm-requirements"></a>Requisitos de VM do Azure

Todos os locais VMs replicadas para o Azure tem de cumprir os requisitos de VM do Azure resumidos nesta tabela. Quando a recuperação de Site é executada uma verificação de pré-requisitos para a replicação, a verificação irá falhar se alguns dos requisitos não forem cumpridas.

**Componente** | **Requisitos** | **Detalhes**
--- | --- | ---
Sistema operativo convidado | Certifique-se de sistemas operativos suportados para [VMs do VMware através da replicação sem agente](#agentless-migration-vmware-vm-requirements)e para [VMs VMware com o agente de replicação](#agent-based-migration-vmware-vm-requirements).<br/> Pode migrar qualquer carga de trabalho em execução num sistema operativo suportado. | Falha na verificação se não suportado.
Arquitetura de sistema operativo convidado | 64 bits. | Falha na verificação se não suportado.
Tamanho de disco do sistema operativo | Até 2048 GB. | Falha na verificação se não suportado.
Número de discos do sistema operativo | 1 | Falha na verificação se não suportado.
Número de discos de dados | 64 ou menos. | Falha na verificação se não suportado.
Tamanho do disco de dados | Até 4095 GB | Falha na verificação se não suportado.
Adaptadores de rede | São suportados vários adaptadores. | 
VHD partilhado | Não suportado. | Falha na verificação se não suportado.
Disco FC | Não suportado. | Falha na verificação se não suportado.
BitLocker | Não suportado. | O BitLocker tem de ser desativado antes de ativar a replicação para uma máquina. 
o nome da VM | Entre 1 e 63 carateres.<br/> Limitado a letras, números e hífenes.<br/><br/> O nome do computador deve começar e terminar com uma letra ou número. |  Atualize o valor nas propriedades da máquina no Site Recovery.
Ligar após a migração-Windows | Para ligar a VMs do Azure a executar o Windows após a migração:<br/> -Antes da migração ative o RDP na VM no local. Confirme que são adicionadas regras de TCP e UDP ao perfil **Público** e que o protocolo RDP é permitido em **Firewall do Windows** > **Aplicações Permitidas** para todos os perfis.<br/> Para ter acesso a VPN de site a site, ative o protocolo RDP e permitir que o RDP na **Firewall do Windows** -> **aplicações e funcionalidades permitidas** para **domínio e privadas** redes. Além disso, verifique que a política de SAN do sistema operacional está definida como **OnlineAll**. [Saiba mais](https://support.microsoft.com/kb/3031135). | 
Ligar após a migração-Linux | Para ligar a VMs do Azure após a migração através de SSH:<br/> Antes da migração, na máquina no local, verifique que o serviço de Secure Shell está definido para iniciar e, se as regras de firewall permitem uma ligação de SSH.<br/> Após a ativação pós-falha, na VM do Azure, permita ligações de entrada na porta SSH para as regras de grupo de segurança de rede na ativação pós-falha VM e para a sub-rede do Azure à qual está ligado. Além disso, adicione um endereço IP público para a VM. |  


## <a name="next-steps"></a>Passos Seguintes

[Preparar para VMware](tutorial-prepare-vmware.md) avaliação e migração.








