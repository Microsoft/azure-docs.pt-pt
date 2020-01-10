---
title: Suporte do VMware em migrações para Azure
description: Saiba mais sobre o suporte de avaliação/migração do VMware nas migrações para Azure.
ms.topic: conceptual
ms.date: 01/02/2020
ms.openlocfilehash: b4d498b869bafe579e2539a049aae58ac6f26575
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/07/2020
ms.locfileid: "75719448"
---
# <a name="support-matrix-for-vmware-assessment-and-migration"></a>Matriz de suporte para avaliar e migrar o VMware

Você pode usar as [migrações para Azure](migrate-overview.md) para avaliar e migrar computadores para a nuvem Microsoft Azure. Este artigo resume as configurações de suporte e as limitações para avaliar e migrar VMs VMware locais.


## <a name="vmware-scenarios"></a>Cenários de VMware

A tabela resume os cenários com suporte para VMs VMware.

**Implementação** | **Detalhes**
--- | ---
**Avaliar VMs VMware locais** | [Configure](tutorial-prepare-vmware.md) sua primeira avaliação.<br/><br/> [Execute](scale-vmware-assessment.md) uma avaliação em larga escala.
**Migrar VMs VMware** | Você pode migrar usando a migração sem agente ou usar uma migração baseada em agente. [Saiba mais](server-migrate-overview.md)


## <a name="azure-migrate-projects"></a>Projetos de migrações para Azure

**Suporte** | **Detalhes**
--- | ---
**Permissões do Azure** | Você precisa de permissões de colaborador ou de proprietário na assinatura para criar um projeto de migrações para Azure.
**Limitações do VMware**  | Avalie até 35.000 VMs VMware em um único projeto. Você pode criar vários projetos em uma assinatura do Azure. Um projeto pode incluir VMs do VMware e VMs do Hyper-V, até os limites de avaliação.
**Geografia** | [Examine](migrate-support-matrix.md#supported-geographies) as geografias com suporte.

**Geografia** | **Local de armazenamento de metadados**
--- | ---
Azure Government | Gov dos E.U.A. Virginia
Ásia-Pacífico | Ásia Oriental ou sudeste asiático
Austrália | Leste da Austrália ou sudeste da Austrália
Brasil | Sul do Brasil
Canadá | Canadá central ou leste do Canadá
Europa | Europa Setentrional ou Europa Ocidental
França | França Central
Índia | Índia central ou sul da Índia
Japão |  Leste do Japão ou oeste do Japão
Coreia | Coreia central ou sul da Coreia
Reino Unido | Sul do Reino Unido ou Oeste do Reino Unido
Estados Unidos | EUA Central ou oeste dos EUA 2


 > [!NOTE]
 > No momento, o suporte para Azure governamental está disponível apenas para a [versão mais antiga](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) das migrações para Azure.


## <a name="application-discovery"></a>Deteção de aplicações

Migrações para Azure: a avaliação do servidor pode descobrir aplicativos, funções e recursos. Descobrir seu inventário de aplicativos permite que você identifique e planeje um caminho de migração adaptado para suas cargas de trabalho locais. Migrações para Azure: a avaliação do servidor fornece descoberta sem agente, usando credenciais de convidado do computador, acessando remotamente computadores usando chamadas de WMI e SSH.

**Suporte** | **Detalhes**
--- | ---
Computadores com suporte | VMs VMware no local
Sistema operacional do computador | Todas as versões do Windows e Linux
Credenciais | Atualmente, o dá suporte ao uso de uma credencial para todos os servidores Windows e uma credencial para todos os servidores Linux. Você cria uma conta de usuário convidado para VMs do Windows e uma conta de usuário regular/normal (acesso não sudo) para todas as VMs do Linux.
Limites de computador para descoberta de aplicativos | 10000 por dispositivo. 35000 por projeto

## <a name="assessment-vcenter-server-requirements"></a>Requisitos de vCenter Server de avaliação

Esta tabela resume o suporte de avaliação e as limitações para servidores de virtualização VMware.

**Suporte** | **Detalhes**
--- | ---
**Servidor vCenter** | As VMs do VMware que você deseja avaliar devem ser gerenciadas por um ou mais servidores vCenter que executam 5,5, 6,0, 6,5 ou 6,7.

## <a name="assessment-vcenter-server-permissions"></a>VCenter Server de avaliação – permissões

As migrações para Azure precisam acessar o vCenter Server para descobrir VMs para avaliação e migração sem agente.

**Tarefa** | **Permissões necessárias**
--- | ---
Somente avaliação | vCenter Server conta somente leitura.
Avaliação com a [descoberta de aplicativo ou a](how-to-discover-applications.md) visualização de [dependência sem agente](how-to-create-group-machine-dependencies-agentless.md) | vCenter Server conta com acesso somente leitura e privilégios habilitados para **máquinas virtuais** > **operações de convidado**.

## <a name="assessment-appliance-requirements"></a>Avaliação – requisitos de dispositivo

As migrações para Azure executam um dispositivo leve para descobrir VMs VMware e enviar metadados de VM e dados de desempenho para migrações para Azure. O dispositivo para VMware é implantado usando um modelo OVA importado para o vCenter Server. A tabela a seguir resume os requisitos do dispositivo.

**Suporte** | **Detalhes**
--- | ---
**Implantação de dispositivo** | Você implanta o dispositivo como uma VM VMware. Você precisa de recursos suficientes no vCenter Server para alocar uma VM com 32 GB de RAM, 8 vCPUs, cerca de 80 GB de armazenamento em disco e um comutador virtual externo.<br/><br/> O dispositivo requer acesso à Internet, seja diretamente ou por meio de um proxy.<br/> A VM do dispositivo deve ser implantada em um host ESXi executando a versão 5,5 ou posterior.
**Projeto de migrações para Azure** | Um dispositivo pode ser associado a um único projeto. <br/> Qualquer número de dispositivos pode ser associado a um único projeto.<br/> Você pode avaliar até 35.000 VMs em um projeto.
**Deteção** | Um dispositivo pode descobrir até 10.000 VMs VMware em um vCenter Server.<br/> Um dispositivo pode se conectar a um único vCenter Server.
**Grupo de avaliação** | Você pode adicionar até 35.000 computadores em um único grupo.
**Avaliação** | Você pode avaliar até 35.000 VMs em uma única avaliação.


## <a name="assessment-url-access-requirements"></a>Avaliação-requisitos de acesso à URL

O dispositivo de migrações para Azure precisa de conectividade com a Internet.

- Quando você implanta o dispositivo, as migrações para Azure executam uma verificação de conectividade para as URLs resumidas na tabela a seguir.
- Se você estiver usando um proxy baseado em URL para se conectar à Internet, permita o acesso a essas URLs, certificando-se de que o proxy resolva todos os registros CNAME recebidos ao pesquisar as URLs.

**URL** | **Detalhes**  
--- | --- |
*.portal.azure.com  | Navegue até as migrações para Azure no portal do Azure.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *. live.com | Inicie sessão na subscrição do Azure.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Crie Active Directory aplicativos para o dispositivo se comunicar com o serviço migrações para Azure.
management.azure.com | Crie Active Directory aplicativos para o dispositivo se comunicar com o serviço migrações para Azure.
dc.services.visualstudio.com | Carregar logs de aplicativo usados para monitoramento interno.
*.vault.azure.net | Gerenciar segredos no Azure Key Vault.
*.servicebus.windows.net | Comunicação entre o dispositivo e o serviço de migrações para Azure.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | Conecte-se às URLs de serviço de migrações para Azure.
*.blob.core.windows.net | Carregar dados para contas de armazenamento.
https://aka.ms/latestapplianceservices<br/><br/> https://download.microsoft.com/download | Usado para atualizações do dispositivo de migração do Azure.

## <a name="assessment-port-requirements"></a>Avaliação – requisitos de porta

**Dispositivo** | **ligação**
--- | ---
Baseado | Conexões de entrada na porta TCP 3389 para permitir conexões de área de trabalho remota para o dispositivo.<br/><br/> Conexões de entrada na porta 44368 para acessar remotamente o aplicativo de gerenciamento de dispositivo usando a URL: ```https://<appliance-ip-or-name>:44368``` <br/><br/>Conexões de saída na porta 443, 5671 e 5672 para enviar metadados de descoberta e desempenho para migrações para Azure.
vCenter Server | Conexões de entrada na porta TCP 443 para permitir que o dispositivo colete metadados de configuração e desempenho para avaliações. <br/><br/> O dispositivo se conecta ao vCenter na porta 443 por padrão. Se o servidor vCenter escutar em uma porta diferente, você poderá modificar a porta ao configurar a descoberta.

## <a name="assessment-dependency-visualization"></a>Avaliação – visualização de dependência

A visualização de dependência ajuda a Visualizar dependências entre computadores que você deseja avaliar e migrar. Normalmente, você usa o mapeamento de dependência quando deseja avaliar computadores com níveis mais altos de confiança. Para VMs VMware, há suporte para a visualização de dependência da seguinte maneira:

- **Visualização de dependência sem agente**: essa opção está atualmente em visualização. Ele não exige a instalação de agentes em computadores.
    - Ele funciona capturando os dados de conexão TCP de computadores para os quais está habilitado. Depois que a descoberta de dependência é iniciada, o dispositivo reúne dados de computadores em um intervalo de sondagem de cinco minutos.
    - Os seguintes dados são coletados:
        - Ligações TCP
        - Nomes de processos que têm conexões ativas
        - Nomes de aplicativos instalados que executam os processos acima
        - Não. de conexões detectadas em cada intervalo de sondagem
- **Visualização de dependência baseada em agente**: para usar a visualização de dependência baseada em agente, você precisa baixar e instalar os agentes a seguir em cada computador local que você deseja analisar.
    - Instale o MMA (Microsoft Monitoring Agent) em cada computador. [Saiba mais](how-to-create-group-machine-dependencies.md#install-the-mma) sobre como instalar o agente do MMA.
    - Instale o Dependency Agent em cada computador. [Saiba mais](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) sobre como instalar o Dependency Agent.
    - Além disso, caso tenha computadores sem conectividade à Internet, terá de transferir e instalar o gateway do Log Analytics.

## <a name="migration---limitations"></a>Migração-limitações
Você pode selecionar até 10 VMs de uma só vez para replicação. Se você quiser migrar mais máquinas, faça a replicação em grupos de 10. Para a migração sem agente do VMware, você pode executar até 100 replicações simultaneamente.

## <a name="agentless-migration-vmware-server-requirements"></a>Migração sem agente-requisitos do servidor VMware

Esta tabela resume o suporte de avaliação e as limitações para servidores de virtualização VMware.

**Suporte** | **Detalhes**
--- | ---
vCenter Server | Versão 5,5, 6,0, 6,5 ou 6,7.
VMware vSphere | Versão 5,5, 6,0, 6,5 ou 6,7,

## <a name="agentless-migration-vcenter-server-permissions"></a>Migração sem agente-permissões de vCenter Server

**Permissões** | **Detalhes**
--- | ---
Datastore.Browse | Permitir a navegação de arquivos de log da VM para solucionar problemas de criação e exclusão de instantâneos.
Datastore.LowLevelFileOperations | Permitir operações de leitura/gravação/exclusão/renomeação no navegador de repositório de armazenamento, para solucionar problemas de criação e exclusão de instantâneos.
VirtualMachine.Configuration.DiskChangeTracking | Permitir habilitar ou desabilitar o controle de alterações de discos de VM para efetuar pull de blocos de dados alterados entre instantâneos
VirtualMachine.Configuration.DiskLease | Permitir operações de concessão de disco para uma VM, ler o disco usando o VMware vSphere VDDK (Kit de desenvolvimento de disco virtual).
VirtualMachine.Provisioning.AllowReadOnlyDiskAccess | Permitir a abertura de um disco em uma VM para ler o disco usando o VDDK.
VirtualMachine.Provisioning.AllowVirtualMachineDownload  | Permite operações de leitura em arquivos associados a uma VM, para baixar os logs e solucionar problemas se ocorrer falha.
VirtualMachine.SnapshotManagement.* | Permitir a criação e o gerenciamento de instantâneos de VM para replicação.
Máquina virtual. interação. desligar | Permitir que a VM seja desligada durante a migração para o Azure.


## <a name="agentless-migration-vmware-vm-requirements"></a>Migração sem agente-requisitos de VM do VMware

**Suporte** | **Detalhes**
--- | ---
**Sistemas operativos suportados** | Os sistemas operacionais [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) e [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) com suporte do Azure podem ser migrados usando a migração sem agente.
**Alterações necessárias para o Azure** | Algumas VMs podem exigir alterações para que possam ser executadas no Azure. As migrações para Azure fazem essas alterações automaticamente para os seguintes sistemas operacionais:<br/> -Red Hat Enterprise Linux 6.5 +, 7.0 +<br/> -CentOS 6.5 +, 7.0 +</br> - SUSE Linux Enterprise Server 12 SP1+<br/> -Ubuntu 14.04 LTS, 16.04 LTS, 18.04 LTS<br/> -Debian 7, 8<br/><br/> Para outros sistemas operacionais, você precisa fazer ajustes manualmente antes da migração. Os artigos relevantes contêm instruções sobre como fazer isso.
**Inicialização do Linux** | Se/boot estiver em uma partição dedicada, ele deverá residir no disco do sistema operacional e não poderá ser distribuído em vários discos.<br/> Se/boot fizer parte da partição raiz (/), a partição '/' deverá estar no disco do sistema operacional e não poderá abranger outros discos.
**Inicialização UEFI** | As VMs com inicialização UEFI não têm suporte para migração.
**Tamanho do disco** | disco do sistema operacional de 2 TB; 4 TB para discos de dados.
**Limites de disco** |  Até 60 discos por VM.
**Discos/volumes criptografados** | VMs com discos/volumes criptografados não têm suporte para migração.
**Cluster de disco compartilhado** | Não suportado.
**Discos independentes** | Não suportado.
**Discos de RDM/PassThrough** | Se as VMs tiverem discos RDM ou de passagem, esses discos não serão replicados para o Azure.
**NFS** | Volumes NFS montados como volumes nas VMs não serão replicados.
**destinos iSCSI** | Não há suporte para VMs com destinos iSCSI para migração sem agente.
**E/s de vários caminhos** | Não suportado.
**VMotion de armazenamento** | Não suportado. A replicação não funcionará se uma VM usar o Storage vMotion.
**NICs agrupadas** | Não suportado.
**Protocolo** | Não suportado.
**Disco de destino** | As VMs só podem ser migradas para discos gerenciados (HDD padrão, SSD Premium) no Azure.
**Replicação simultânea** | 100 VMs por vCenter Server. Se você tiver mais, migre-os em lotes de 100.


## <a name="agentless-migration-appliance-requirements"></a>Migração sem agente-requisitos de dispositivo


**Suporte** | **Detalhes**
--- | ---
**ESXi** | A VM do dispositivo deve ser implantada em um host ESXi executando a versão 5,5 ou posterior.
**Projeto de migrações para Azure** | Um dispositivo pode ser associado a um único projeto.
**vCenter Server** | Um dispositivo pode descobrir até 10.000 VMs VMware em um vCenter Server.<br/> Um dispositivo pode se conectar a um vCenter Server.
**VDDK** | Se você estiver executando uma migração sem agente com a migração de servidor de migrações para Azure, o VMware vSphere VDDK deverá ser instalado na VM do dispositivo.

## <a name="agentless-migration-url-access-requirements"></a>Migração sem agente-requisitos de acesso à URL

O dispositivo de migrações para Azure precisa de conectividade com a Internet.

- Quando você implanta o dispositivo, as migrações para Azure executam uma verificação de conectividade para as URLs resumidas na tabela a seguir.
- Se você estiver usando um proxy baseado em URL, permita o acesso a essas URLs, certificando-se de que o proxy resolva todos os registros CNAME recebidos ao pesquisar as URLs.

**URL** | **Detalhes**  
--- | ---
*.portal.azure.com | Navegue até as migrações para Azure no portal do Azure.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *. live.com  | Inicie sessão na subscrição do Azure.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Crie Active Directory aplicativos para o dispositivo se comunicar com o serviço migrações para Azure.
management.azure.com | Crie Active Directory aplicativos para o dispositivo se comunicar com o serviço migrações para Azure.
dc.services.visualstudio.com | Carregar logs de aplicativo usados para monitoramento interno.
*.vault.azure.net | Gerenciar segredos no Azure Key Vault.
*.servicebus.windows.net | Comunicação entre o dispositivo e o serviço de migrações para Azure.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | Conecte-se às URLs de serviço de migrações para Azure.
*.blob.core.windows.net | Carregar dados para contas de armazenamento.
https://aka.ms/latestapplianceservices<br/><br/> https://download.microsoft.com/download | Usado para atualizações do dispositivo de migração do Azure.


## <a name="agentless-migration-port-requirements"></a>Migração sem agente – requisitos de porta

**Dispositivo** | **ligação**
--- | ---
Baseado | Conexões de saída na porta 443 para carregar dados replicados no Azure e para se comunicar com os serviços de migração do Azure orquestrando a replicação e a migração.
vCenter Server | Conexões de entrada na porta 443 para permitir que o dispositivo coordene a replicação-criar instantâneos, copiar dados, liberar instantâneos
host vSphere/ESXI | Entrada na porta TCP 902 para o dispositivo replicar dados de instantâneos.


## <a name="agent-based-migration-vmware-server-requirements"></a>Migração baseada em agente-requisitos do servidor VMware

Esta tabela resume o suporte de avaliação e as limitações para servidores de virtualização VMware.

**Suporte** | **Detalhes**
--- | ---
vCenter Server | Versão 5,5, 6,0, 6,5 ou 6,7.
VMware vSphere | Versão 5,5, 6,0, 6,5 ou 6,7.

### <a name="agent-based-migration-vcenter-server-permissions"></a>Migração baseada em agente-permissões de vCenter Server

Uma conta somente leitura para vCenter Server.

## <a name="agent-based-migration-replication-appliance-requirements"></a>Migração baseada em agente-requisitos de dispositivo de replicação

Os requisitos para o [dispositivo de replicação](migrate-replication-appliance.md) usado para a migração baseada em agente de VMs VMware e servidores físicos com migração de servidor de migrações para Azure são resumidos na tabela.

> [!NOTE]
> Quando você configura o dispositivo de replicação usando o modelo OVA fornecido no Hub migrações para Azure, o dispositivo executa o Windows Server 2016 e está em conformidade com os requisitos de suporte. Se você configurar o dispositivo de replicação manualmente em um servidor físico, verifique se ele está em conformidade com os requisitos.



**Componente** | **Requisito**
--- | ---
 | **Configurações do VMware** (dispositivo de VM VMware)
PowerCLI | A [versão 6,0 do PowerCLI](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) deverá ser instalada se o dispositivo de replicação estiver em execução em uma VM VMware.
Tipo de NIC | VMXNET3 (se o dispositivo for uma VM VMware)
 | **Configurações de hardware**
Núcleos de CPU | 8
RAM | 16 GB
Número de discos | Três: o disco do sistema operacional, o disco de cache do servidor de processo e a unidade de retenção.
Espaço livre em disco (cache) | 600 GB
Espaço livre em disco (disco de retenção) | 600 GB
**Configurações de software** |
Sistema operativo | Windows Server 2016 ou Windows Server 2012 R2
Região do sistema operativo | Inglês (en-us)
TLS | O TLS 1,2 deve estar habilitado.
.NET Framework | .NET Framework 4,6 ou posterior deve ser instalado no computador (com criptografia forte habilitada.
MySQL | O MySQL deve ser instalado no dispositivo.<br/> O MySQL deve ser instalado. Você pode instalar manualmente ou Site Recovery pode instalá-lo durante a implantação do dispositivo.
Outras aplicações | Não execute outros aplicativos no dispositivo de replicação.
Funções do Windows Server | Não habilite estas funções: <br> - Active Directory Domain Services <br>- Serviços de Informação da Internet <br> - Hyper-V
Políticas de grupo | Não habilite essas políticas de Grupo: <br> -Impedir o acesso ao prompt de comando. <br> -Impedir o acesso às ferramentas de edição do registro. <br> -Lógica de confiança para anexos de arquivo. <br> -Ative a execução do script. <br> [Saiba mais](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | -Nenhum site padrão já existente <br> -Nenhum site/aplicativo já existente escutando na porta 443 <br>-Habilitar [autenticação anônima](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Habilitar configuração de [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx)
**Configurações de rede** |
Tipo de endereço IP | Estático
Portas | 443 (Canal de controlo e orquestração)<br>9443 (Transporte de dados)
Tipo de NIC | VMXNET3

### <a name="replication-appliance-url-access"></a>Acesso à URL do dispositivo de replicação

O dispositivo de replicação precisa de acesso a essas URLs.

**URL** | **Detalhes**
--- | ---
\*.backup.windowsazure.com | Usado para transferência e coordenação de dados replicados
\*.store.core.windows.net | Usado para transferência e coordenação de dados replicados
\*.blob.core.windows.net | Usado para acessar a conta de armazenamento que armazena os dados replicados
\*.hypervrecoverymanager.windowsazure.com | Usado para operações de gerenciamento de replicação e coordenação
https:\//management.azure.com | Usado para operações de gerenciamento de replicação e coordenação
*.services.visualstudio.com | Usado para fins de telemetria (é opcional)
time.nist.gov | Utilizados para verificar a sincronização da hora entre o sistema e a hora global.
time.windows.com | Utilizados para verificar a sincronização da hora entre o sistema e a hora global.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https:\//login.live.com <br/> https:\//graph.windows.net <br/> https:\//login.windows.net <br/> https:\//www.live.com <br/> https:\//www.microsoft.com  | A instalação do OVF precisa de acesso a essas URLs. Eles são usados para controle de acesso e gerenciamento de identidade por Azure Active Directory
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | Para concluir o download do MySQL


#### <a name="mysql-installation-options"></a>Opções de instalação do MySQL

O MySQL pode ser instalado no dispositivo de replicação usando um destes métodos.

**Método** | **Detalhes**
--- | ---
Baixar e instalar manualmente | Baixe o aplicativo MySQL & Coloque-o na pasta C:\Temp\ASRSetup e instale manualmente.<br/> Quando você configurar o dispositivo, o MySQL será mostrado como já instalado.
Sem download online | Coloque o aplicativo instalador do MySQL na pasta C:\Temp\ASRSetup. Ao instalar o dispositivo e clicar para baixar e instalar o MySQL, a instalação usará o instalador adicionado.
Baixar e instalar em migrações para Azure | Quando você instalar o dispositivo e for solicitado a fornecer o MySQL, selecione **baixar e instalar**.



## <a name="agent-based-migration-vmware-vm-requirements"></a>Migração baseada em agente-requisitos de VM do VMware

**Suporte** | **Detalhes**
--- | ---
**Carga de trabalho da máquina** | As migrações para Azure dão suporte à migração de qualquer carga de trabalho (digamos Active Directory, SQL Server, etc.) em execução em um computador com suporte.
**Sistemas operativos** | Para obter as informações mais recentes, examine o [suporte do sistema operacional](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) para site Recovery. As migrações para Azure fornecem suporte ao sistema operacional de VM idêntico.
**Armazenamento de sistema de arquivos/convidado do Linux** | Para obter as informações mais recentes, examine o [suporte do sistema de arquivos do Linux](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) para site Recovery. As migrações para Azure têm suporte ao sistema de arquivos Linux idêntico.
**Rede/armazenamento** | Para obter as informações mais recentes, examine os pré-requisitos de [rede](../site-recovery/vmware-physical-azure-support-matrix.md#network) e [armazenamento](../site-recovery/vmware-physical-azure-support-matrix.md#storage) para site Recovery. As migrações para Azure fornecem requisitos de rede/armazenamento idênticos.
**Requisitos do Azure** | Para obter as informações mais recentes, examine os requisitos de rede, [armazenamento](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)e [computação](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) do [Azure](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover)para site Recovery. As migrações para Azure têm requisitos idênticos para a migração do VMware.
**Serviço de mobilidade** | O agente do serviço de mobilidade deve ser instalado em cada VM que você deseja migrar.
**Inicialização UEFI** | A VM migrada no Azure será convertida automaticamente em uma VM de inicialização do BIOS.<br/><br/> O disco do sistema operacional deve ter até quatro partições, e os volumes devem ser formatados com NTFS.
**Disco de destino** | As VMs só podem ser migradas para discos gerenciados (HDD padrão, SSD Premium) no Azure.
**Tamanho do disco** | disco do sistema operacional de 2 TB; 8 TB para discos de dados.
**Limites de disco** |  Até 63 discos por VM.
**Discos/volumes criptografados** | VMs com discos/volumes criptografados não têm suporte para migração.
**Cluster de disco compartilhado** | Não suportado.
**Discos independentes** | Suportado.
**Discos de passagem** | Suportado.
**NFS** | Volumes NFS montados como volumes nas VMs não serão replicados.
**destinos iSCSI** | Não há suporte para VMs com destinos iSCSI para migração sem agente.
**E/s de vários caminhos** | Não suportado.
**VMotion de armazenamento** | Suportadas
**NICs agrupadas** | Não suportado.
**Protocolo** | Não suportado.




## <a name="agent-based-migration-url-access-requirements"></a>Migração baseada em agente-requisitos de acesso à URL

O serviço de mobilidade em execução nas VMs VMware precisa de conectividade com a Internet.

Quando você implanta o serviço de mobilidade, ele faz uma verificação de conectividade para as URLs resumidas na tabela a seguir.


**URL** | **Detalhes**  
--- | ---
*.portal.azure.com | Navegue até as migrações para Azure no portal do Azure.
*.windows.net | Inicie sessão na subscrição do Azure.
*.microsoftonline.com | Crie Active Directory aplicativos para o dispositivo se comunicar com o serviço migrações para Azure.
management.azure.com | Crie Active Directory aplicativos para o dispositivo se comunicar com o serviço migrações para Azure.
dc.services.visualstudio.com | Carregar logs de aplicativo usados para monitoramento interno.
*.vault.azure.net | Gerenciar segredos no Azure Key Vault.
*.servicebus.windows.net | Comunicação entre o dispositivo e o serviço de migrações para Azure.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | Conecte-se às URLs de serviço de migrações para Azure.
*.blob.core.windows.net | Carregar dados para contas de armazenamento.

## <a name="agent-based-migration-port-requirements"></a>Migração baseada em agente-requisitos de porta

**Dispositivo** | **ligação**
--- | ---
VMs | O serviço de mobilidade em execução nas VMs se comunica com o dispositivo de replicação local (servidor de configuração) na porta HTTPS 443 de entrada, para o gerenciamento de replicação.<br/><br/> As VMs enviam dados de replicação para o servidor de processo (em execução no computador do servidor de configuração) na porta HTTPS 9443 de entrada. Essa porta pode ser modificada.
Dispositivo de replicação | O dispositivo de replicação orquestra a replicação com o Azure sobre a porta HTTPS 443 de saída.
Servidor de processo | O servidor de processo recebe dados de replicação, otimiza-os e criptografa-os e envia-os para o armazenamento do Azure pela porta 443 de saída.<br/> Por padrão, o servidor de processo é executado no dispositivo de replicação.

## <a name="azure-vm-requirements"></a>Requisitos de VM do Azure

Todas as VMs locais replicadas para o Azure devem atender aos requisitos de VM do Azure resumidos nesta tabela. Quando Site Recovery executar uma verificação de pré-requisitos para replicação, a verificação falhará se alguns dos requisitos não forem atendidos.

**Componente** | **Requisitos** | **Detalhes**
--- | --- | ---
Sistema operativo convidado | Verifique os sistemas operacionais com suporte para [VMs VMware usando a replicação sem agente](#agentless-migration-vmware-vm-requirements)e para [VMs VMware usando a replicação baseada em agente](#agent-based-migration-vmware-vm-requirements).<br/> Você pode migrar qualquer carga de trabalho em execução em um sistema operacional com suporte. | A verificação falhará se não houver suporte.
Arquitetura do sistema operacional convidado | 64 bits. | A verificação falhará se não houver suporte.
Tamanho do disco do sistema operacional | Até 2.048 GB. | A verificação falhará se não houver suporte.
Contagem de disco do sistema operacional | 1 | A verificação falhará se não houver suporte.
Contagem de disco de dados | 64 ou menos. | A verificação falhará se não houver suporte.
Tamanho do disco de dados | Até 4.095 GB | A verificação falhará se não houver suporte.
Placas de rede | Há suporte para vários adaptadores. |
VHD Partilhado | Não suportado. | A verificação falhará se não houver suporte.
Disco FC | Não suportado. | A verificação falhará se não houver suporte.
BitLocker | Não suportado. | O BitLocker deve ser desabilitado antes de habilitar a replicação para um computador.
o nome da VM | De 1 a 63 caracteres.<br/> Limitado a letras, números e hífenes.<br/><br/> O nome do computador deve começar e terminar com uma letra ou número. |  Atualize o valor nas propriedades da máquina em Site Recovery.
Conectar após a migração-Windows | Para se conectar às VMs do Azure que executam o Windows após a migração:<br/> -Antes de a migração habilitar o RDP na VM local. Confirme que são adicionadas regras de TCP e UDP ao perfil **Público** e que o protocolo RDP é permitido em **Firewall do Windows** > **Aplicações Permitidas** para todos os perfis.<br/> Para acesso VPN site a site, habilite o RDP e permita que o RDP no **Firewall do Windows** -> **aplicativos e recursos permitidos** para redes de **domínio e privadas** . Além disso, verifique se a política de SAN do sistema operacional está definida como **OnlineAll**. [Saiba mais](prepare-for-migration.md). |
Conectar após a migração-Linux | Para se conectar às VMs do Azure após a migração usando SSH:<br/> Antes da migração, no computador local, verifique se o serviço Secure Shell está definido como iniciar e se as regras de firewall permitem uma conexão SSH.<br/> Após o failover, na VM do Azure, permita conexões de entrada para a porta SSH para as regras do grupo de segurança de rede na VM com failover e para a sub-rede do Azure à qual ela está conectada. Além disso, adicione um endereço IP público para a VM. |  


## <a name="next-steps"></a>Passos seguintes

[Prepare-se para](tutorial-prepare-vmware.md) avaliação e migração do VMware.
