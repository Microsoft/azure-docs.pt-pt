---
title: Gerenciamento de Atualizações solução no Azure
description: Este artigo destina-se a ajudá-lo a entender como usar a solução de Gerenciamento de Atualizações do Azure para gerenciar atualizações para seus computadores Windows e Linux.
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 05/22/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6f23a1f8e60567e1c2ed89b27f0eb2bab4ca5912
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70061808"
---
# <a name="update-management-solution-in-azure"></a>Gerenciamento de Atualizações solução no Azure

Você pode usar a solução Gerenciamento de Atualizações na automação do Azure para gerenciar atualizações do sistema operacional para seus computadores Windows e Linux no Azure, em ambientes locais ou em outros provedores de nuvem. Pode rapidamente avaliar o estado das atualizações disponíveis em todos os computadores agente e gerir o processo de instalação de atualizações necessárias para os servidores.

Você pode habilitar Gerenciamento de Atualizações para máquinas virtuais diretamente da sua conta de automação do Azure. Para saber como habilitar Gerenciamento de Atualizações para máquinas virtuais de sua conta de automação, consulte [gerenciar atualizações para várias máquinas virtuais](manage-update-multi.md). Você também pode habilitar Gerenciamento de Atualizações para uma máquina virtual na página de máquina virtual no portal do Azure. Esse cenário está disponível para máquinas virtuais [Linux](../virtual-machines/linux/tutorial-monitoring.md#enable-update-management) e [Windows](../virtual-machines/windows/tutorial-monitoring.md#enable-update-management) .

> [!NOTE]
> A solução Gerenciamento de Atualizações requer a vinculação de um espaço de trabalho Log Analytics à sua conta de automação. Para obter uma lista definitiva de regiões com suporte, consulte Mapeamentos de [espaço de trabalho do Azure](./how-to/region-mappings.md). Os mapeamentos de região não afetam a capacidade de gerenciar máquinas virtuais em uma região separada do que a sua conta de automação.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="solution-overview"></a>Descrição geral da solução

Os computadores gerenciados pelo Gerenciamento de Atualizações usam as seguintes configurações para executar implantações de avaliação e atualização:

* Microsoft Monitoring Agent (MMA) para Windows ou Linux
* Configuração de Estado Pretendido do PowerShell (DSC) para Linux
* Função de Trabalho de Runbook Híbrida da Automatização
* Microsoft Update ou Windows Server Update Services (WSUS) para computadores Windows

O diagrama a seguir mostra uma exibição conceitual do comportamento e do fluxo de dados com o modo como a solução avalia e aplica atualizações de segurança a todos os computadores Windows Server e Linux conectados em um espaço de trabalho:

![Fluxo do processo de Gerenciamento de Atualizações](./media/automation-update-management/update-mgmt-updateworkflow.png)

Gerenciamento de Atualizações pode ser usado para integrar de forma nativa computadores em várias assinaturas no mesmo locatário.

Depois que um pacote é liberado, leva 2-3 horas para que o patch seja exibido para os computadores Linux para avaliação. Para computadores Windows, leva 12-15 horas para que o patch seja mostrado para avaliação após sua liberação.

Depois que um computador conclui uma verificação de conformidade de atualização, o agente encaminha as informações em massa para Azure Monitor logs. Em um computador Windows, a verificação de conformidade é executada a cada 12 horas por padrão.

Além do agendamento da verificação, a verificação de conformidade da atualização é iniciada dentro de 15 minutos do MMA que está sendo reiniciado, antes da instalação da atualização e após a instalação da atualização.

Para um computador Linux, a verificação de conformidade é executada a cada hora por padrão. Se o agente MMA for reiniciado, uma verificação de conformidade será iniciada dentro de 15 minutos.

A solução relata o quão atualizado o computador é baseado em qual fonte você está configurado para sincronizar. Se o computador com Windows estiver configurado para relatar para o WSUS, dependendo de quando o WSUS foi sincronizado pela última vez com o Microsoft Update, os resultados poderão ser diferentes do que as atualizações da Microsoft mostram. Esse comportamento é o mesmo para computadores Linux configurados para relatar a um repositório local, em vez de a um repositório público.

> [!NOTE]
> Para relatar corretamente ao serviço, Gerenciamento de Atualizações exige que determinadas URLs e portas sejam habilitadas. Para saber mais sobre esses requisitos, confira [planejamento de rede para Hybrid Workers](automation-hybrid-runbook-worker.md#network-planning).

Pode criar uma implementação agendada para implementar e instalar atualizações de software em computadores que precisam das atualizações. As atualizações classificadas como *opcionais* não são incluídas no escopo de implantação para computadores Windows. Somente as atualizações necessárias são incluídas no escopo de implantação.

A implantação agendada define quais computadores de destino recebem as atualizações aplicáveis, especificando explicitamente computadores ou selecionando um [grupo](../azure-monitor/platform/computer-groups.md) de computadores baseado em pesquisas de log de um conjunto específico de computadores ou uma [consulta do Azure](#azure-machines) Isso seleciona dinamicamente as VMs do Azure com base em critérios especificados. Esses grupos são diferentes da [configuração de escopo](../azure-monitor/insights/solution-targeting.md), que é usada apenas para determinar quais computadores obtêm os pacotes de gerenciamento que habilitam a solução.

Você também especifica uma agenda para aprovar e definir um período de tempo durante o qual as atualizações podem ser instaladas. Esse período de tempo é chamado de janela de manutenção. Dez minutos da janela de manutenção será reservado para reinicializações se uma reinicialização for necessária e você tiver selecionado a opção de reinicialização apropriada. Se a aplicação de patch demorar mais do que o esperado e houver menos de dez minutos na janela de manutenção, uma reinicialização não ocorrerá.

As atualizações são instaladas por runbooks na Automatização do Azure. Você não pode exibir esses runbooks e os runbooks não exigem nenhuma configuração. Quando uma implantação de atualização é criada, a implantação de atualização cria uma agenda que inicia um runbook de atualização mestre no momento especificado para os computadores incluídos. O runbook mestre inicia um runbook filho em cada agente para instalar as atualizações necessárias.

Na data e hora especificadas na implantação da atualização, os computadores de destino executam a implantação em paralelo. Antes da instalação, uma verificação é executada para verificar se as atualizações ainda são necessárias. Para computadores cliente do WSUS, se as atualizações não forem aprovadas no WSUS, a implantação da atualização falhará.

Não há suporte para um computador registrado para Gerenciamento de Atualizações em mais de um espaço de trabalho Log Analytics (hospedagem múltipla).

## <a name="clients"></a>Clientes

### <a name="supported-client-types"></a>Tipos de cliente suportados

A tabela a seguir mostra uma lista de sistemas operacionais com suporte:

|Sistema operativo  |Notas  |
|---------|---------|
|Windows Server 2008, Windows Server 2008 R2 RTM    | Dá suporte apenas a avaliações de atualização.         |
|Windows Server 2019 (datacenter/Data Center Core/Standard)<br><br>Windows Server 2016 (datacenter/Data Center Core/Standard)<br><br>Windows Server 2012 R2 (datacenter/padrão)<br><br>Windows Server 2008 R2 (RTM e SP1 Standard)|O .NET Framework 4.5.1 ou posterior é necessário. ([Baixar .NET Framework](/dotnet/framework/install/guide-for-developers))<br/> O Windows PowerShell 4,0 ou posterior é necessário. ([Baixe o WMF 4,0](https://www.microsoft.com/download/details.aspx?id=40855))<br/> O Windows PowerShell 5,1 é recomendado para maior confiabilidade.  ([Baixe o WMF 5,1](https://www.microsoft.com/download/details.aspx?id=54616))        |
|CentOS 6 (x86/x64) e 7 (x64)      | Os agentes do Linux têm de ter acesso a um repositório de atualização. A aplicação de patches baseada em classificação requer ' yum ' para retornar dados de segurança que o CentOS não está pronto para uso. Para obter mais informações sobre aplicação de patch com base em classificação no CentOS, consulte [Atualizar classificações no Linux](#linux-2)          |
|Red Hat Enterprise 6 (x86/x64) e 7 (x64)     | Os agentes do Linux têm de ter acesso a um repositório de atualização.        |
|SUSE Linux Enterprise Server 11 (x86/x64) e 12 (x64)     | Os agentes do Linux têm de ter acesso a um repositório de atualização.        |
|Ubuntu 14, 4 LTS, 16, 4 LTS e 18, 4 (x86/x64)      |Os agentes do Linux têm de ter acesso a um repositório de atualização.         |

> [!NOTE]
> Os conjuntos de dimensionamento de máquinas virtuais do Azure podem ser gerenciados com Gerenciamento de Atualizações. Gerenciamento de Atualizações funciona nas próprias instâncias e não na imagem base. Você precisará agendar as atualizações de forma incremental, como não atualizar todas as instâncias de VM ao mesmo tempo.
> Nós VMSS podem ser adicionados seguindo as etapas em [onbaord uma máquina não Azure](automation-tutorial-installed-software.md#onboard-a-non-azure-machine).

### <a name="unsupported-client-types"></a>Tipos de cliente não suportada

A tabela seguinte lista os sistemas operativos que não são suportados:

|Sistema operativo  |Notas  |
|---------|---------|
|Cliente Windows     | Não são suportados sistemas operativos de cliente (por exemplo, o Windows 7 e Windows 10).        |
|Servidor de Nano do Windows Server 2016     | Não suportado.       |
|Nós do serviço kubernetes do Azure | Não suportado. Use o processo de aplicação de patch detalhado em [aplicar segurança e atualizações de kernel a nós do Linux no serviço kubernetes do Azure (AKs)](../aks/node-updates-kured.md)|

### <a name="client-requirements"></a>Requisitos do cliente

#### <a name="windows"></a>Windows

Os agentes do Windows devem ser configurados para se comunicar com um servidor WSUS ou devem ter acesso ao Microsoft Update. Você pode usar Gerenciamento de Atualizações com System Center Configuration Manager. Para saber mais sobre cenários de integração, consulte [integrar System Center Configuration Manager com gerenciamento de atualizações](oms-solution-updatemgmt-sccmintegration.md#configuration). O [agente do Windows](../azure-monitor/platform/agent-windows.md) é necessário. O agente será instalado automaticamente se você estiver integrando uma máquina virtual do Azure.

> [!NOTE]
> É possível que um usuário modifique Política de Grupo para que as reinicializações do computador só possam ser executadas pelo usuário, não pelo sistema. Os computadores gerenciados podem ficar presos, se Gerenciamento de Atualizações não tiver direitos para reinicializar o computador sem interação manual do usuário.
>
> Para obter mais informações, consulte [definir configurações de política de grupo para atualizações automáticas](https://docs.microsoft.com/en-us/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates).

#### <a name="linux"></a>Linux

Para o Linux, o computador deve ter acesso a um repositório de atualização. O repositório de atualizações pode ser privado ou público. O TLS 1,1 ou o TLS 1,2 é necessário para interagir com Gerenciamento de Atualizações. Não há suporte para um agente Log Analytics para Linux configurado para relatar mais de um espaço de trabalho de Log Analytics com esta solução.

Para obter informações sobre como instalar o agente de Log Analytics para Linux e baixar a versão mais recente, consulte [log Analytics Agent para Linux](https://github.com/microsoft/oms-agent-for-linux). Para obter informações sobre como instalar o agente do Log Analytics para Windows, consulte [Microsoft Monitoring Agent para Windows](../log-analytics/log-analytics-windows-agent.md).

## <a name="permissions"></a>Permissões

Para criar e gerenciar implantações de atualização, você precisa de permissões específicas. Para saber mais sobre essas permissões, consulte [acesso baseado em função-gerenciamento de atualizações](automation-role-based-access-control.md#update-management).

## <a name="solution-components"></a>Componentes da solução

A solução consiste nos seguintes recursos. Os recursos são adicionados à sua conta de automação. Eles são agentes conectados diretamente ou em um grupo de gerenciamento conectado a Operations Manager.

### <a name="hybrid-worker-groups"></a>Grupos de Função de Trabalho Híbrida

Depois de habilitar essa solução, qualquer computador com Windows conectado diretamente ao seu espaço de trabalho Log Analytics será automaticamente configurado como um Hybrid Runbook Worker para dar suporte aos runbooks incluídos nesta solução.

Cada computador Windows gerenciado pela solução é listado no painel **grupos do Hybrid Worker** como um **grupo de trabalho híbrido do sistema** para a conta de automação. As soluções usam o nome de *host*da Convenção de nomenclatura FQDN_GUID. Você não pode direcionar esses grupos com runbooks em sua conta. Eles falharão se você tentar. Esses grupos têm o objetivo de dar suporte apenas à solução de gerenciamento.

Você pode adicionar os computadores Windows a um grupo de Hybrid Runbook Worker em sua conta de automação para dar suporte a runbooks de automação se usar a mesma conta para a solução e a associação de grupo de Hybrid Runbook Worker. Essa funcionalidade foi adicionada na versão 7.2.12024.0 do Hybrid Runbook Worker.

### <a name="management-packs"></a>Pacotes de gestão

Se o grupo de gerenciamento do System Center Operations Manager estiver conectado a um espaço de trabalho do Log Analytics, os pacotes de gerenciamento a seguir serão instalados no Operations Manager. Esses pacotes de gerenciamento também são instalados em computadores com Windows conectados diretamente depois que você adiciona a solução. Você não precisa configurar ou gerenciar esses pacotes de gerenciamento.

* Pacote de Informações de Avaliação de Atualização do Microsoft System Center Advisor (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Pacote de Gestão de Implementação de Atualização

> [!NOTE]
> Se você tiver um grupo de gerenciamento Operations Manager 1807 com agentes configurados no nível do grupo de gerenciamento para ser associado a um espaço de trabalho, a solução alternativa atual para que eles apareçam é substituir **IsAutoRegistrationEnabled** como **true** no Regra **Microsoft. IntelligencePacks. AzureAutomation. HybridAgent. init** .

Para obter mais informações sobre como os pacotes de gerenciamento de solução são atualizados, consulte [conectar Operations Manager a logs de Azure monitor](../azure-monitor/platform/om-agents.md).

> [!NOTE]
> Para sistemas com o agente do Operations Manager, para poder ser totalmente gerenciados pelo Gerenciamento de Atualizações, o agente precisa ser atualizado para o Microsoft Monitoring Agent. Para saber como atualizar o agente, consulte [como atualizar um agente de Operations Manager](https://docs.microsoft.com/system-center/scom/deploy-upgrade-agents). Para ambientes que usam Operations Manager, é necessário que você esteja executando o System Center Operations Manager 2012 R2 UR 14 ou posterior.

## <a name="onboard"></a>Habilitar Gerenciamento de Atualizações

Para iniciar a aplicação de patches em sistemas, você precisa habilitar a solução Gerenciamento de Atualizações. Há várias maneiras de carregar computadores para Gerenciamento de Atualizações. A seguir estão as maneiras recomendadas e com suporte para integrar a solução:

* [De uma máquina virtual](automation-onboard-solutions-from-vm.md)
* [De navegar por vários computadores](automation-onboard-solutions-from-browse.md)
* [Da sua conta de automação](automation-onboard-solutions-from-automation-account.md)
* [Com um runbook de automação do Azure](automation-onboard-solutions.md)

### <a name="confirm-that-non-azure-machines-are-onboarded"></a>Confirmar se os computadores não Azure estão integrados

Para confirmar se os computadores conectados diretamente estão se comunicando com os logs de Azure Monitor, após alguns minutos, você pode executar uma das seguintes pesquisas de log.

#### <a name="linux"></a>Linux

```loganalytics
Heartbeat
| where OSType == "Linux" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

#### <a name="windows"></a>Windows

```loganalytics
Heartbeat
| where OSType == "Windows" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

Em um computador com Windows, você pode examinar as seguintes informações para verificar a conectividade do agente com os logs de Azure Monitor:

1. No painel de controle, abra **Microsoft Monitoring Agent**. Na guia **log Analytics do Azure** , o agente exibe a seguinte mensagem: **O Microsoft Monitoring Agent se conectou com êxito ao log Analytics**.
2. Abra o log de eventos do Windows. Vá para **aplicativo e serviços Gerenciador de logs** e procure a ID de evento 3000 e a ID de evento 5002 do **conector de serviço**de origem. Esses eventos indicam que o computador foi registrado com o espaço de trabalho Log Analytics e está recebendo a configuração.

Se o agente não puder se comunicar com os logs de Azure Monitor e o agente estiver configurado para se comunicar com a Internet por meio de um servidor proxy ou firewall, confirme se o firewall ou o servidor proxy está configurado corretamente. Para saber como verificar se o firewall ou o servidor proxy está configurado corretamente, consulte [configuração de rede para o agente do Windows](../azure-monitor/platform/agent-windows.md) ou [configuração de rede para o agente do Linux](../log-analytics/log-analytics-agent-linux.md).

> [!NOTE]
> Se os sistemas Linux estiverem configurados para se comunicar com um proxy ou Log Analytics gateway e você estiver integrando essa solução, atualize as permissões de *proxy. conf* para conceder a permissão de leitura do grupo omiuser no arquivo usando os seguintes comandos:
>
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

Agentes Linux adicionados recentemente mostram um status de **atualizado** após a execução de uma avaliação. Este processo pode demorar até seis horas.

Para confirmar que um grupo de gerenciamento de Operations Manager está se comunicando com os logs de Azure Monitor, consulte [validar Operations Manager integração com os logs de Azure monitor](../azure-monitor/platform/om-agents.md#validate-operations-manager-integration-with-azure-monitor).

## <a name="data-collection"></a>Recolha de dados

### <a name="supported-agents"></a>Agentes suportados

A tabela seguinte descreve as origens ligadas que são suportadas por esta solução:

| Origem ligada | Suportadas | Descrição |
| --- | --- | --- |
| Agentes do Windows |Sim |A solução coleta informações sobre atualizações do sistema de agentes do Windows e, em seguida, inicia a instalação de atualizações necessárias. |
| Agentes do Linux |Sim |A solução coleta informações sobre atualizações do sistema de agentes do Linux e, em seguida, inicia a instalação de atualizações necessárias em distribuições com suporte. |
| Grupo de gestão do Operations Manager |Sim |A solução recolhe informações sobre atualizações do sistema de agentes num grupo de gestão ligado.<br/>Não é necessária uma conexão direta do agente de Operations Manager para os logs do Azure Monitor. Os dados são reencaminhados do grupo de gestão para a área de trabalho do Log Analytics. |

### <a name="collection-frequency"></a>Frequência da recolha

Uma verificação é executada duas vezes por dia para cada computador gerenciado do Windows. A cada 15 minutos, a API do Windows é chamada para consultar a hora da última atualização para determinar se o status foi alterado. Se o status for alterado, uma verificação de conformidade será iniciada.

Uma verificação é executada a cada hora para cada computador Linux gerenciado.

Pode levar entre 30 minutos e 6 horas para que o painel exiba dados atualizados de computadores gerenciados.

A Azure Monitor média registra o uso de dados de um computador usando Gerenciamento de Atualizações é de aproximadamente 25 MB por mês. Esse valor é apenas uma aproximação e está sujeito a alterações com base em seu ambiente. É recomendável que você monitore seu ambiente para ver o uso exato que você tem.

## <a name="viewing-update-assessments"></a>Exibir avaliações de atualização

Na sua conta de automação, selecione **Gerenciamento de atualizações** para exibir o status de seus computadores.

Esta exibição fornece informações sobre seus computadores, atualizações ausentes, implantações de atualização e implantações de atualização agendada. Na **coluna conformidade**, você pode ver a última vez em que o computador foi avaliado. Na coluna **prontidão do agente de atualização** , você pode ver se a integridade do agente de atualização. Se houver um problema, selecione o link para acessar a documentação de solução de problemas que pode ajudá-lo a saber quais etapas executar para corrigir o problema.

Para executar uma pesquisa de logs que retorna informações sobre a máquina, atualização ou implantação, selecione o item na lista. O painel **pesquisa de log** é aberto com uma consulta para o item selecionado:

![Gerenciamento de Atualizações exibição padrão](media/automation-update-management/update-management-view.png)

## <a name="install-updates"></a>Instalar atualizações

Depois que as atualizações forem avaliadas para todos os computadores Linux e Windows em seu espaço de trabalho, você poderá instalar as atualizações necessárias criando uma *implantação de atualização*. Para criar uma implantação de atualização, você deve ter acesso de gravação à conta de automação e acesso de gravação para as VMs do Azure que são direcionadas na implantação. Uma implantação de atualização é uma instalação agendada de atualizações necessárias para um ou mais computadores. Você especifica a data e a hora da implantação e um computador ou grupo de computadores a serem incluídos no escopo de uma implantação. Para saber mais sobre grupos de computadores, confira [grupos de computadores em logs de Azure monitor](../azure-monitor/platform/computer-groups.md).

Quando você inclui grupos de computadores em sua implantação de atualização, a associação de grupo é avaliada apenas uma vez, no momento da criação do agendamento. Alterações subsequentes em um grupo não são refletidas. Para contornar esse uso de [grupos dinâmicos](#using-dynamic-groups), esses grupos são resolvidos no momento da implantação e definidos por uma consulta para VMs do Azure ou uma pesquisa salva para VMs não Azure.

> [!NOTE]
> Por padrão, as máquinas virtuais do Windows que são implantadas no Azure Marketplace são definidas para receber atualizações automáticas do serviço Windows Update. Esse comportamento não é alterado quando você adiciona essa solução ou adiciona máquinas virtuais do Windows ao seu espaço de trabalho. Se você não gerenciar ativamente as atualizações usando essa solução, o comportamento padrão (para aplicar atualizações automaticamente) será aplicado.

Para evitar que as atualizações sejam aplicadas fora de uma janela de manutenção no Ubuntu, reconfigure o pacote de atualização autônoma para desabilitar as atualizações automáticas. Para obter informações sobre como configurar o pacote, consulte [atualizações automáticas tópico no guia do servidor Ubuntu](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

As máquinas virtuais que foram criadas a partir das imagens de Red Hat Enterprise Linux sob demanda (RHEL) que estão disponíveis no Azure Marketplace são registradas para acessar a [RHUI (infraestrutura de atualização do Red Hat)](../virtual-machines/virtual-machines-linux-update-infrastructure-redhat.md) implantada no Azure. Qualquer outra distribuição do Linux deve ser atualizada a partir do repositório de arquivos online da distribuição, seguindo os métodos com suporte da distribuição.

Para criar uma nova implantação de atualização, selecione **agendar implantação de atualização**. A página **nova implantação de atualizações** é aberta. Insira valores para as propriedades descritas na tabela a seguir e clique em **criar**:

| Propriedade | Description |
| --- | --- |
| Name |O nome exclusivo para identificar a implementação de atualizações. |
|Sistema operativo| Linux ou Windows|
| Grupos a serem atualizados |Para computadores do Azure, defina uma consulta com base em uma combinação de assinatura, grupos de recursos, locais e marcas para criar um grupo dinâmico de VMs do Azure para incluir em sua implantação. </br></br>Para computadores não Azure, selecione uma pesquisa salva existente para selecionar um grupo de computadores não Azure a serem incluídos na implantação. </br></br>Para saber mais, confira [grupos dinâmicos](automation-update-management.md#using-dynamic-groups)|
| Computadores para atualizar |Selecione uma pesquisa salva, um grupo importado ou escolha a máquina na lista suspensa e selecione computadores individuais. Se escolher **Máquinas**, a preparação da máquina é mostrada na coluna **ATUALIZAÇÃO DE PREPARAÇÃO DO AGENTE**.</br> Para saber mais sobre os diferentes métodos de criação de grupos de computadores em logs de Azure Monitor, consulte [grupos de computadores em logs de Azure monitor](../azure-monitor/platform/computer-groups.md) |
|Classificações de atualizações|Selecione todas as classificações de atualização de que você precisa|
|Incluir/excluir atualizações|Isso abre a página **incluir/excluir** . As atualizações a serem incluídas ou excluídas estão em separadores diferentes. Para obter mais informações sobre como a inclusão é tratada, consulte [comportamento de inclusão](automation-update-management.md#inclusion-behavior) |
|Definições da agenda|Selecione a hora para iniciar e selecione uma vez ou recorrente para a recorrência|
| Pré-scripts + pós-scripts|Selecione os scripts a serem executados antes e depois da implantação|
| Janela de manutenção |Número de minutos definidos para atualizações. O valor não pode ser inferior a 30 minutos e não mais do que 6 horas |
| Controle de reinicialização| Determina como as reinicializações devem ser tratadas. As opções disponíveis são:</br>Reiniciar se for preciso (Predefinição)</br>Reiniciar sempre</br>Nunca reiniciar</br>Reiniciar apenas - não irá instalar atualizações|

As implantações de atualização também podem ser criadas programaticamente. Para saber como criar uma implantação de atualização com a API REST, consulte [Software Update Configurations-Create](/rest/api/automation/softwareupdateconfigurations/create). Também há um runbook de exemplo que pode ser usado para criar uma implantação semanal de atualização. Para saber mais sobre esse runbook, confira [criar uma implantação de atualização semanal para uma ou mais VMs em um grupo de recursos](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

### <a name="maintenance-windows"></a>Janelas de manutenção

As janelas de manutenção controlam a quantidade de tempo permitida para a instalação das atualizações. Considere os detalhes a seguir ao especificar uma janela de manutenção.

* As janelas de manutenção controlam quantas atualizações foram tentadas para serem instaladas.
* Gerenciamento de Atualizações não interromperá a instalação de novas atualizações se o final de uma janela de manutenção estiver se aproximando.
* Gerenciamento de Atualizações não terminará as atualizações em andamento se a janela de manutenção for excedida.
* Se a janela de manutenção for excedida no Windows, isso geralmente ocorre devido a uma atualização service pack demorando muito para ser instalada.

### <a name="multi-tenant"></a>Implantações de atualização entre locatários

Se você tiver computadores em outro relatório de locatário do Azure para Gerenciamento de Atualizações que você precisa aplicar patch, você precisará usar a solução alternativa a seguir para obtê-los agendados. Você pode usar o cmdlet [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) com a opção `-ForUpdate` para criar um agendamento e usar o [cmdlet New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) e passar os computadores no outro locatário para o `-NonAzureComputer` parâmetro. O exemplo a seguir mostra um exemplo de como fazer isso:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="view-missing-updates"></a>Exibir atualizações ausentes

Selecione **atualizações ausentes** para exibir a lista de atualizações ausentes em seus computadores. Cada atualização é listada e pode ser selecionada. As informações sobre o número de computadores que exigem a atualização, o sistema operacional e um link para obter mais informações são mostradas. O painel **pesquisa de log** mostra mais detalhes sobre as atualizações.

## <a name="view-update-deployments"></a>Exibir implantações de atualização

Selecione a guia implantações de **atualização** para exibir a lista de implantações de atualização existentes. Selecione qualquer uma das implantações de atualização na tabela para abrir o painel **Atualizar execução de implantação** para essa implantação de atualização. Os logs de trabalho são armazenados por um máximo de 30 dias.

![Visão geral dos resultados da implantação de atualização](./media/automation-update-management/update-deployment-run.png)

Para exibir uma implantação de atualização da API REST, consulte [execuções de configuração de atualização de software](/rest/api/automation/softwareupdateconfigurationruns).

## <a name="update-classifications"></a>Classificações de atualizações

As tabelas a seguir listam as classificações de atualização no Gerenciamento de Atualizações, com uma definição para cada classificação.

### <a name="windows"></a>Windows

|Classificação  |Descrição  |
|---------|---------|
|Atualizações críticas     | Uma atualização para um problema específico que aborda um bug crítico não relacionado à segurança.        |
|Atualizações de segurança     | Uma atualização para um problema específico de um produto e relacionado à segurança.        |
|Update rollups     | Um conjunto cumulativo de hotfixes que são empacotados em conjunto para facilitar a implantação.        |
|Pacotes de funcionalidades     | Novos recursos do produto que são distribuídos fora de uma versão do produto.        |
|Service packs     | Um conjunto cumulativo de hotfixes que são aplicados a um aplicativo.        |
|Atualizações da definição     | Uma atualização para vírus ou outros arquivos de definição.        |
|Ferramentas     | Um utilitário ou recurso que ajuda a concluir uma ou mais tarefas.        |
|Atualizações     | Uma atualização para um aplicativo ou arquivo que está instalado no momento.        |

### <a name="linux-2"></a>Linux

|Classificação  |Descrição  |
|---------|---------|
|Atualizações críticas e de segurança     | Atualizações para um problema específico ou um problema específico do produto, relacionado à segurança.         |
|Outras atualizações     | Todas as outras atualizações que não são críticas por natureza ou não são atualizações de segurança.        |

Para o Linux, Gerenciamento de Atualizações pode distinguir entre atualizações críticas e de segurança na nuvem enquanto exibe dados de avaliação devido ao enriquecimento de dados na nuvem. Para aplicação de patch, Gerenciamento de Atualizações depende dos dados de classificação disponíveis no computador. Ao contrário de outras distribuições, o CentOS não tem essas informações disponíveis prontamente. Se você tiver computadores CentOS configurados de forma a retornar dados de segurança para o comando a seguir, Gerenciamento de Atualizações poderá aplicar patches com base nas classificações.

```bash
sudo yum -q --security check-update
```

Atualmente, não há método com suporte para habilitar a classificação nativa-disponibilidade de dados no CentOS. Neste momento, apenas o suporte de melhor esforço é fornecido aos clientes que podem ter habilitado isso por conta própria.

## <a name="firstparty-predownload"></a>Configurações avançadas

Gerenciamento de Atualizações se baseia em Windows Update para baixar e instalar as atualizações do Windows. Como resultado, respeitamos muitas das configurações usadas pelo Windows Update. Se você usar configurações para habilitar atualizações não Windows, Gerenciamento de Atualizações gerenciará essas atualizações também. Se você deseja habilitar o download de atualizações antes que ocorra uma implantação de atualização, as implantações de atualização podem ser mais rápidas e ter menos probabilidade de exceder a janela de manutenção.

### <a name="pre-download-updates"></a>Pré-baixar atualizações

Para configurar o download automático de atualizações no Política de Grupo, você pode definir a [configuração definir atualizações automáticas](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) como **3**. Isso baixa as atualizações necessárias em segundo plano, mas não as instala. Isso mantém Gerenciamento de Atualizações no controle de agendas, mas permite que as atualizações sejam baixadas fora da janela de manutenção do Gerenciamento de Atualizações. Isso pode impedir que a **janela de manutenção exceda** erros no gerenciamento de atualizações.

Você também pode definir isso com o PowerShell, executar o PowerShell a seguir em um sistema para o qual você deseja baixar atualizações automaticamente.

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

### <a name="disable-automatic-installation"></a>Desabilitar instalação automática

As VMs do Azure têm a instalação automática de atualizações habilitadas por padrão. Isso pode fazer com que as atualizações sejam instaladas antes de você agendá-las para serem instaladas pelo Gerenciamento de Atualizações. Você pode desabilitar esse comportamento definindo a `NoAutoUpdate` chave do registro como. `1` O trecho do PowerShell a seguir mostra uma maneira de fazer isso.

```powershell
$AutoUpdatePath = "HKLM:SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU"
Set-ItemProperty -Path $AutoUpdatePath -Name NoAutoUpdate -Value 1
```

### <a name="enable-updates-for-other-microsoft-products"></a>Habilitar atualizações para outros produtos da Microsoft

Por padrão, Windows Update apenas fornece atualizações para o Windows. Se você habilitar **fornecer atualizações para outros produtos da Microsoft ao atualizar o Windows**, você receberá atualizações para outros produtos, incluindo patches de segurança para SQL Server ou outros softwares de terceiros. Esta opção não pode ser configurada por Política de Grupo. Execute o PowerShell a seguir nos sistemas nos quais você deseja habilitar outros patches de terceiros e Gerenciamento de Atualizações honrará essa configuração.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="third-party"></a>Patches de terceiros no Windows

Gerenciamento de Atualizações se baseia no repositório de atualização configurado localmente para corrigir os sistemas Windows com suporte. Este é o WSUS ou o Windows Update. Ferramentas como [System Center Updates Publisher](/sccm/sum/tools/updates-publisher
) (Updates Publisher) permitem que você publique atualizações personalizadas no WSUS. Esse cenário permite que Gerenciamento de Atualizações patch de máquinas que usam System Center Configuration Manager como seu repositório de atualizações com software de terceiros. Para saber como configurar o Updates Publisher, consulte [instalar o Updates Publisher](/sccm/sum/tools/install-updates-publisher).

## <a name="ports"></a>Planejamento de rede

Os endereços a seguir são necessários especificamente para Gerenciamento de Atualizações. A comunicação com esses endereços ocorre na porta 443.

|Público do Azure  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|*.azure-automation.net|*.azure-automation.us|

Para computadores Windows, você também deve permitir o tráfego para qualquer ponto de extremidade exigido pelo Windows Update.  Você pode encontrar uma lista atualizada de pontos de extremidade necessários em [problemas relacionados ao http/proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy). Se você tiver um [servidor de Windows Update](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment)local, também deverá permitir o tráfego para o servidor especificado em sua [chave do WSUS](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry).

Para computadores Red Hat Linux, consulte [os IPs para os servidores de distribuição de conteúdo RHUI para os](../virtual-machines/linux/update-infrastructure-redhat.md#the-ips-for-the-rhui-content-delivery-servers) pontos de extremidade necessários. Para outras distribuições do Linux, consulte a documentação do provedor.

Para obter mais informações sobre as portas que o Hybrid Runbook Worker requer, consulte [Hybrid Worker portas de função](automation-hybrid-runbook-worker.md#hybrid-worker-role).

É recomendável usar os endereços listados ao definir exceções. Para endereços IP, você pode baixar os [intervalos de IP do Microsoft Azure datacenter](https://www.microsoft.com/download/details.aspx?id=41653). Esse arquivo é atualizado semanalmente e reflete os intervalos atualmente implantados e as alterações futuras nos intervalos de IP.

Siga as instruções em [conectar computadores sem acesso à Internet](../azure-monitor/platform/gateway.md) para configurar computadores que não têm acesso à Internet.

## <a name="search-logs"></a>Pesquisar registos

Além dos detalhes que são fornecidos no portal do Azure, você pode fazer pesquisas nos logs. Nas páginas da solução, selecione **log Analytics**. O painel **pesquisa de logs** é aberto.

Você também pode aprender como personalizar as consultas ou usá-las de diferentes clientes e muito mais visitando:  [Documentação](
https://dev.loganalytics.io/)da API de pesquisa do log Analytics.

### <a name="sample-queries"></a>Amostras de consultas

As seções a seguir fornecem exemplos de consultas de log para registros de atualização que são coletados por essa solução:

#### <a name="single-azure-vm-assessment-queries-windows"></a>Consultas únicas de avaliação de VM do Azure (Windows)

Substitua o valor VMUUID pelo GUID da VM da máquina virtual que você está consultando. Você pode encontrar o VMUUID que deve ser usado executando a seguinte consulta nos logs de Azure Monitor:`Update | where Computer == "<machine name>" | summarize by Computer, VMUUID`

##### <a name="missing-updates-summary"></a>Resumo de atualizações ausentes

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"b08d5afa-1471-4b52-bd95-a44fea6e4ca8"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

##### <a name="missing-updates-list"></a>Lista de atualizações ausentes

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"8bf1ccc6-b6d3-4a0b-a643-23f346dfdf82"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

#### <a name="single-azure-vm-assessment-queries-linux"></a>Consultas únicas de avaliação de VM do Azure (Linux)

Para alguns distribuições do Linux, há uma incompatibilidade de [endian](https://en.wikipedia.org/wiki/Endianness) com o valor de VMUUID que vem de Azure Resource Manager e o que é armazenado em logs de Azure monitor. A consulta a seguir verifica uma correspondência em qualquer endian. Substitua os valores de VMUUID pelo formato big-endian e little-endian do GUID para retornar os resultados corretamente. Você pode encontrar o VMUUID que deve ser usado executando a seguinte consulta nos logs de Azure Monitor:`Update | where Computer == "<machine name>"
| summarize by Computer, VMUUID`

##### <a name="missing-updates-summary"></a>Resumo de atualizações ausentes

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

##### <a name="missing-updates-list"></a>Lista de atualizações ausentes

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl

```

#### <a name="multi-vm-assessment-queries"></a>Consultas de avaliação de várias VMs

##### <a name="computers-summary"></a>Resumo de computadores

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Approved, Optional, Classification) by SourceComputerId, UpdateID
    | distinct SourceComputerId, Classification, UpdateState, Approved, Optional
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed" and (Optional==false or Classification has "Critical" or Classification has "Security") and Approved!=false, iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity
| union (Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by SourceComputerId, Product, ProductArch
    | distinct SourceComputerId, Classification, UpdateState
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed", iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity)
| summarize assessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity>-1), notAssessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==-1), computersNeedCriticalUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==4), computersNeedSecurityUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==2), computersNeedOtherUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==1), upToDateComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==0)
| summarize assessedComputersCount=sum(assessedComputersCount), computersNeedCriticalUpdatesCount=sum(computersNeedCriticalUpdatesCount),  computersNeedSecurityUpdatesCount=sum(computersNeedSecurityUpdatesCount), computersNeedOtherUpdatesCount=sum(computersNeedOtherUpdatesCount), upToDateComputersCount=sum(upToDateComputersCount), notAssessedComputersCount=sum(notAssessedComputersCount)
| extend allComputersCount=assessedComputersCount+notAssessedComputersCount


```

##### <a name="missing-updates-summary"></a>Resumo de atualizações ausentes

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| union (Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification )
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

##### <a name="computers-list"></a>Lista de computadores

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Product, Computer, ComputerEnvironment) by SourceComputerId, Product, ProductArch
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed"), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed"), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed"), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2)
| union(Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, Optional, Approved, Computer, ComputerEnvironment) by Computer, SourceComputerId, UpdateID
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed" and Approved!=false), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed" and Approved!=false), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed" and Optional==false and Approved!=false), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2) )
| order by ComplianceOrder asc, missingCriticalUpdatesCount desc, missingSecurityUpdatesCount desc, missingOtherUpdatesCount desc, displayName asc
| project-away ComplianceOrder
```

##### <a name="missing-updates-list"></a>Lista de atualizações ausentes

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| union(Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2)
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

## <a name="using-dynamic-groups"></a>Usando grupos dinâmicos

Gerenciamento de Atualizações fornece a capacidade de direcionar um grupo dinâmico de VMs do Azure ou não Azure para implantações de atualização. Esses grupos são avaliados no momento da implantação para que você não precise editar sua implantação para adicionar computadores.

> [!NOTE]
> Você deve ter as permissões adequadas ao criar uma implantação de atualização. Para saber mais, consulte [instalar atualizações](#install-updates).

### <a name="azure-machines"></a>Computadores do Azure

Esses grupos são definidos por uma consulta, quando uma implantação de atualização é iniciada, os membros desse grupo são avaliados. Grupos dinâmicos não funcionam com VMs clássicas. Ao definir sua consulta, os itens a seguir podem ser usados juntos para preencher o grupo dinâmico

* Subscription
* Grupos de recursos
* Localizações
* Tags

![Selecionar grupos](./media/automation-update-management/select-groups.png)

Para visualizar os resultados de um grupo dinâmico, clique no botão **Visualizar** . Essa visualização mostra a associação de grupo nesse momento, neste exemplo, Estamos pesquisando computadores com a **função** de marca é igual a **BackendServer**. Se mais computadores tiverem essa marca adicionada, eles serão adicionados a quaisquer implantações futuras no grupo.

![grupos de visualização](./media/automation-update-management/preview-groups.png)

### <a name="non-azure-machines"></a>Computadores não Azure

Para computadores não Azure, as pesquisas salvas também conhecidas como grupos de computadores são usadas para criar o grupo dinâmico. Para saber como criar uma pesquisa salva, consulte [criando um grupo de computadores](../azure-monitor/platform/computer-groups.md#creating-a-computer-group). Depois que o grupo for criado, você poderá selecioná-lo na lista de pesquisas salvas. Clique em **Visualizar** para visualizar os computadores na pesquisa salva naquele momento.

![Selecionar grupos](./media/automation-update-management/select-groups-2.png)

## <a name="integrate-with-system-center-configuration-manager"></a>Integrar no System Center Configuration Manager

Os clientes que investiram em System Center Configuration Manager para gerenciar PCs, servidores e dispositivos móveis também dependem da força e da maturidade de Configuration Manager para ajudá-los a gerenciar atualizações de software. Configuration Manager faz parte do ciclo de gerenciamento de atualização de software (SUM).

Para saber como integrar a solução de gerenciamento com o System Center Configuration Manager, consulte [integrar System Center Configuration Manager com gerenciamento de atualizações](oms-solution-updatemgmt-sccmintegration.md).

## <a name="inclusion-behavior"></a>Comportamento de inclusão

A inclusão de atualização permite que você especifique atualizações específicas a serem aplicadas. Os patches ou pacotes incluídos são instalados. Quando patches ou pacotes são incluídos e uma classificação é selecionada também, os itens e itens incluídos que atendem à classificação são instalados.

É importante saber que as exclusões substituem as inclusões. Por exemplo, se você definir uma regra de exclusão `*`de, nenhum patch ou pacote será instalado, pois todos eles serão excluídos. Os patches excluídos ainda aparecem como ausentes no computador. Para computadores Linux, se um pacote estiver incluído, mas tiver um pacote dependente que foi excluído, o pacote não será instalado.

## <a name="patch-linux-machines"></a>Corrigir computadores Linux

As seções a seguir explicam possíveis problemas com a aplicação de patch do Linux.

### <a name="unexpected-os-level-upgrades"></a>Atualizações inesperadas no nível do sistema operacional

Em algumas variantes do Linux, como Red Hat Enterprise Linux, as atualizações no nível do sistema operacional podem ocorrer por meio de pacotes. Isso pode levar a Gerenciamento de Atualizações execuções em que o número de versão do sistema operacional é alterado. Como Gerenciamento de Atualizações usa os mesmos métodos para atualizar os pacotes que um administrador usaria localmente no computador Linux, esse comportamento é intencional.

Para evitar a atualização da versão do sistema operacional por meio de Gerenciamento de Atualizações execuções, use o recurso de **exclusão** .

Em Red Hat Enterprise Linux, o nome do pacote a ser excluído é redhat-Release-Server. x86_64.

![Pacotes a serem excluídos para Linux](./media/automation-update-management/linuxpatches.png)

### <a name="critical--security-patches-arent-applied"></a>Os patches críticos/de segurança não são aplicados

Ao implantar atualizações em um computador Linux, você pode selecionar classificações de atualização. Isso filtra as atualizações que são aplicadas ao computador que atende aos critérios especificados. Esse filtro é aplicado localmente no computador quando a atualização é implantada.

Como Gerenciamento de Atualizações executa o enriquecimento de atualização na nuvem, algumas atualizações podem ser sinalizadas em Gerenciamento de Atualizações como tendo impacto sobre a segurança, mesmo que o computador local não tenha essas informações. Como resultado, se você aplicar atualizações críticas a um computador Linux, pode haver atualizações que não estão marcadas como tendo impacto na segurança nesse computador e as atualizações não são aplicadas.

No entanto, Gerenciamento de Atualizações ainda pode relatar que a máquina não está em conformidade porque tem informações adicionais sobre a atualização relevante.

A implantação de atualizações por classificação de atualização não funciona no CentOS de fora da caixa. Para implantar atualizações para CentOS corretamente, selecione todas as classificações para garantir que as atualizações sejam aplicadas. Para o SUSE, selecionar *apenas* "outras atualizações" como a classificação pode resultar em algumas atualizações de segurança também sendo instaladas se as atualizações de segurança relacionadas ao zypper (Gerenciador de pacotes) ou suas dependências forem necessárias primeiro. Esse comportamento é uma limitação do zypper. Em alguns casos, talvez seja necessário executar novamente a implantação da atualização. Para verificar, verifique o log de atualizações.

## <a name="remove-a-vm-from-update-management"></a>Remover uma VM do Gerenciamento de Atualizações

Para remover uma VM do Gerenciamento de Atualizações:

* No espaço de trabalho Log Analytics, remova a VM da pesquisa salva para a configuração `MicrosoftDefaultScopeConfig-Updates`de escopo. As pesquisas salvas podem ser encontradas em **geral** em seu espaço de trabalho.
* Remova o [Microsoft Monitoring Agent](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) ou o [agente de log Analytics para Linux](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>Passos Seguintes

Continue no tutorial para saber como gerenciar atualizações para suas máquinas virtuais do Windows.

> [!div class="nextstepaction"]
> [Gerenciar atualizações e patches para suas VMs do Windows do Azure](automation-tutorial-update-management.md)

* Use pesquisas de log em [logs de Azure monitor](../log-analytics/log-analytics-log-searches.md) para exibir dados de atualização detalhados.
* [Criar alertas](automation-tutorial-update-management.md#configure-alerts) para status de implantação de atualização.

* Para saber como interagir com Gerenciamento de Atualizações por meio da API REST, consulte [configurações de atualização de software](/rest/api/automation/softwareupdateconfigurations)
* Para saber como solucionar problemas de seu Gerenciamento de Atualizações, consulte [solução de problemas gerenciamento de atualizações](troubleshoot/update-management.md)
