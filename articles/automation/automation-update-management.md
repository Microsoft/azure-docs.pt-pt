---
title: Solução de gestão de atualizações no Azure
description: Este artigo destina-se para o ajudar a compreender como utilizar a solução de gestão de atualizações do Azure para gerir atualizações para os computadores Windows e Linux.
services: automation
ms.service: automation
ms.subservice: update-management
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5267467b2874c1cc36c8a9f5f27cc1c348eba7ac
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369773"
---
# <a name="update-management-solution-in-azure"></a>Solução de gestão de atualizações no Azure

Pode utilizar a solução de gestão de atualizações na automatização do Azure para gerir atualizações do sistema operativo para os seus computadores Windows e Linux no Azure, em ambientes no local ou em outros fornecedores de cloud. Pode rapidamente avaliar o estado das atualizações disponíveis em todos os computadores agente e gerir o processo de instalação de atualizações necessárias para os servidores.

Pode ativar a gestão de atualizações para máquinas virtuais diretamente a partir da sua conta de automatização do Azure. Para saber como ativar a gestão de atualizações para máquinas virtuais a partir da sua conta de automatização, veja [gerir atualizações para várias máquinas virtuais](manage-update-multi.md). Também pode ativar a gestão de atualizações para uma máquina virtual a partir da página de máquina virtual no portal do Azure. Este cenário está disponível para [Linux](../virtual-machines/linux/tutorial-monitoring.md#enable-update-management) e [Windows](../virtual-machines/windows/tutorial-monitoring.md#enable-update-management) máquinas virtuais.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="solution-overview"></a>Descrição geral da solução

Computadores que são geridos pela gestão de atualizações utilizam as seguintes configurações para efetuar a avaliação e implementações de atualizações:

* Microsoft Monitoring Agent (MMA) para Windows ou Linux
* Configuração de Estado Pretendido do PowerShell (DSC) para Linux
* Função de Trabalho de Runbook Híbrida da Automatização
* Microsoft Update ou Windows Server Update Services (WSUS) para computadores Windows

O diagrama seguinte mostra uma vista conceptual do comportamento e fluxo de dados com como a solução avalia e aplica as atualizações de segurança a todos os Windows Server computadores e Linux ligados numa área de trabalho:

![Atualizar o fluxo do processo de gestão](./media/automation-update-management/update-mgmt-updateworkflow.png)

Gestão de atualizações pode ser utilizado de forma nativa carregar máquinas em várias subscrições no mesmo inquilino.

Assim que um CVE versão, demora 2 a 3 horas para o patch seja apresentado para computadores Linux para avaliação.  Para máquinas Windows, demora de 12 a 15 horas para o patch a aparecer para avaliação, após ele foi lançado.

Depois de um computador realiza uma análise de conformidade de atualização, o agente reencaminha as informações em massa para os registos do Azure Monitor. Num computador Windows, a análise de conformidade é executada a cada 12 horas por predefinição.

Além do agendamento da análise, a análise da compatibilidade de atualização é iniciada dentro de 15 minutos do MMA ser reiniciado, antes da instalação da atualização e após a instalação de atualização.

Para um computador Linux, a análise de conformidade é realizada em três horas por predefinição. Se o agente MMA ser reiniciado, é iniciada uma análise de conformidade em 15 minutos.

A solução de relatórios como atualizados, o computador baseia-se a origem com a qual estão configurados para sincronizar com. Se o computador do Windows estiver configurado para reportar para o WSUS, dependendo de quando um WSUS última sincronização com o Microsoft Update, os resultados podem divergir o que mostra o Microsoft Updates. Este comportamento é o mesmo para computadores Linux que estão configurados para reportar a um repositório local, em vez de para um repositório público.

> [!NOTE]
> Para comunicar corretamente para o serviço, o gerenciamento de atualizações requer determinadas portas e URLs para ser ativada. Para saber mais sobre estes requisitos, consulte [planear funções de trabalho híbridas de rede](automation-hybrid-runbook-worker.md#network-planning).

Pode criar uma implementação agendada para implementar e instalar atualizações de software em computadores que precisam das atualizações. As atualizações classificadas como *opcional* não estão incluídos no âmbito da implementação para computadores Windows. Apenas as atualizações necessárias estão incluídas no âmbito da implementação. 

A implementação agendada define que computadores de destino recebem as atualizações aplicáveis, ao especificar explicitamente os computadores ou ao selecionar uma [grupo de computadores](../azure-monitor/platform/computer-groups.md) que se baseia em pesquisas de registos de um conjunto específico de computadores. Também especificar uma agenda para aprovar e definir um período de tempo durante o qual as atualizações podem ser instaladas.

As atualizações são instaladas por runbooks na Automatização do Azure. Não é possível ver estes runbooks e os runbooks não requerem nenhuma configuração. Quando é criada uma implementação de atualização, a implementação da atualização cria uma agenda que inicia um runbook de atualização principal num momento especificado nos computadores incluídos. O runbook principal inicia um runbook subordinado em cada agente para instalar as atualizações necessárias.

A data e hora especificada na implementação de atualização, os computadores de destino executar a implantação em paralelo. Antes da instalação, executar uma análise para verificar se as atualizações são ainda necessárias. Para computadores de cliente do WSUS, se as atualizações não aprovadas no WSUS, a implementação de atualização falha.

Ter uma máquina registados para gestão de atualizações em mais do que uma áreas de trabalho do Log Analytics (multi-homing) não é suportada.

## <a name="clients"></a>Clientes

### <a name="supported-client-types"></a>Tipos de cliente suportados

A tabela seguinte mostra uma lista dos sistemas operativos suportados:

|Sistema operativo  |Notas  |
|---------|---------|
|Windows Server 2008, Windows Server 2008 R2 RTM    | Suporta apenas avaliações de atualização.         |
|Windows Server 2008 R2 SP1 e posterior (incluindo o Windows Server 2012 e 2016)    |.NET framework 4.5.1 ou posterior é necessária. ([Baixe o .NET Framework](/dotnet/framework/install/guide-for-developers))<br/> É necessário o Windows PowerShell 4.0 ou posterior. ([Transferir WMF 4.0](https://www.microsoft.com/download/details.aspx?id=40855))<br/> Windows PowerShell 5.1 é recomendada para maior confiabilidade.  ([Transferir WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616))        |
|CentOS 6 (x86/x64) e 7 (x64)      | Os agentes do Linux têm de ter acesso a um repositório de atualização. Com base na classificação de aplicação de patches requer "yum" para devolver dados de segurança que não têm a CentOS prontos a utilizar. Para obter mais informações sobre a classificação com base na aplicação de patches no CentOS, consulte [atualizar classificações no Linux](#linux-2)          |
|Red Hat Enterprise 6 (x86/x64) e 7 (x64)     | Os agentes do Linux têm de ter acesso a um repositório de atualização.        |
|SUSE Linux Enterprise Server 11 (x86/x64) e 12 (x64)     | Os agentes do Linux têm de ter acesso a um repositório de atualização.        |
|Ubuntu 14.04 LTS e 16.04 LTS e 18.04 (x86/x64)      |Os agentes do Linux têm de ter acesso a um repositório de atualização.         |

### <a name="unsupported-client-types"></a>Tipos de cliente não suportada

A tabela seguinte lista os sistemas operativos que não são suportados:

|Sistema operativo  |Notas  |
|---------|---------|
|Cliente Windows     | Não são suportados sistemas operativos de cliente (por exemplo, o Windows 7 e Windows 10).        |
|Servidor de Nano do Windows Server 2016     | Não suportado.       |

### <a name="client-requirements"></a>Requisitos do cliente

#### <a name="windows"></a>Windows

Agentes do Windows tem de ser configurados para comunicar com um servidor WSUS ou têm de ter acesso ao Microsoft Update. Pode utilizar a gestão de atualizações com o System Center Configuration Manager. Para saber mais sobre cenários de integração, consulte [integrar o System Center Configuration Manager com a gestão de atualizações](oms-solution-updatemgmt-sccmintegration.md#configuration). O [agente do Windows](../azure-monitor/platform/agent-windows.md) é necessária. O agente é instalado automaticamente se estiver a inclusão de uma máquina virtual do Azure.

#### <a name="linux"></a>Linux

Para o Linux, a máquina tem de ter acesso a um repositório de atualização. O repositório de atualização pode ser privado ou público. TLS 1.1 ou 1.2 de TLS é necessário para interagir com a gestão de atualizações. Um agente de Log Analytics para Linux está configurado para reportar a mais do que uma áreas de trabalho do Log Analytics não é suportado com esta solução.

Para obter informações sobre como instalar o agente do Log Analytics para Linux e para transferir a versão mais recente, consulte [agente do Log Analytics para Linux](https://github.com/microsoft/oms-agent-for-linux). Para obter informações sobre como instalar o Log Analytics agente para Windows, consulte [Microsoft Monitoring Agent para Windows](../log-analytics/log-analytics-windows-agent.md).

## <a name="permissions"></a>Permissões

Para criar e gerir implementações de atualização, terá de permissões específicas. Para saber mais sobre estas permissões, consulte [acesso baseado em funções - gestão de atualizações](automation-role-based-access-control.md#update-management).

## <a name="solution-components"></a>Componentes da solução

A solução consiste nos seguintes recursos. Os recursos são adicionados à sua conta de automatização. Eles são qualquer um dos agentes ligados diretamente ou num grupo de gestão ligados do Operations Manager.

### <a name="hybrid-worker-groups"></a>Grupos de Função de Trabalho Híbrida

Depois de ativar esta solução, qualquer computador Windows que estejam ligado diretamente à sua área de trabalho do Log Analytics é configurado automaticamente como uma função de trabalho de Runbook híbrida para suportar os runbooks que estão incluídos nesta solução.

Cada computador Windows gerido pela solução é listado na **grupos de trabalho híbrido** painel como uma **grupo de trabalho do sistema híbrido** da conta de automatização. As soluções utilizam a Convenção de nomenclatura *Hostname FQDN_GUID*. Não pode segmentar estes grupos com runbooks na sua conta. Falha se tentar. Estes grupos destinam-se para oferecer suporte a apenas uma solução de gestão.

Pode adicionar os computadores Windows a um grupo de trabalho de Runbook híbrida na conta de automatização para suportar runbooks de automatização, se utilizar a mesma conta para a solução e a associação ao grupo de trabalho de Runbook híbrida. Esta funcionalidade foi adicionada na versão 7.2.12024.0 da função de trabalho de Runbook híbrida.

### <a name="management-packs"></a>Pacotes de gestão

Se o grupo de gestão do System Center Operations Manager estiver ligado a uma área de trabalho do Log Analytics, os seguintes pacotes de gestão são instalados no Operations Manager. Estes pacotes de gestão também são instalados nos computadores ligados diretamente do Windows depois de adicionar a solução. Não precisa de configurar ou gerir estes pacotes de gestão.

* Pacote de Informações de Avaliação de Atualização do Microsoft System Center Advisor (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Pacote de Gestão de Implementação de Atualização

> [!NOTE]
> Se tiver um grupo de gestão do Operations Manager 1807 com agentes configurados ao nível do grupo de gestão para ser associada a uma área de trabalho, a solução atual para que eles apareçam é substituir **IsAutoRegistrationEnabled** para **True** no **Microsoft.IntelligencePacks.AzureAutomation.HybridAgent.Init** regra.

Para obter mais informações sobre como são atualizados os pacotes de gestão de solução, consulte [registos de ligar o Operations Manager para o Azure Monitor](../azure-monitor/platform/om-agents.md).

> [!NOTE]
> Para sistemas com o agente do Operations Manager, para poder ser completamente geridos pela gestão de atualizações, o agente tem de ser atualizado para o Microsoft Monitoring Agent. Para saber como atualizar o agente, veja [como atualizar um agente do Operations Manager](https://docs.microsoft.com/system-center/scom/deploy-upgrade-agents). Para ambientes com o Operations Manager, é necessário que está a executar System Center Operations Manager 2012 R2 UR 14 ou posterior.

## <a name="onboard"></a>Ativar a gestão de atualizações

Para iniciar a aplicação de patches de sistemas, terá de ativar a solução de gestão de atualizações. Existem várias formas de carregar máquinas para gestão de atualizações. A seguir é o recomendada e suportado formas de ativar a solução:

* [De uma máquina virtual](automation-onboard-solutions-from-vm.md)
* [A partir de várias máquinas de navegação](automation-onboard-solutions-from-browse.md)
* [Da sua conta de automatização](automation-onboard-solutions-from-automation-account.md)
* [Com um runbook da automatização do Azure](automation-onboard-solutions.md)
  
### <a name="confirm-that-non-azure-machines-are-onboarded"></a>Confirme que as máquinas não Azure são carregadas

Para confirmar que as máquinas ligadas diretamente estão a comunicar com os registos do Azure Monitor, após alguns minutos, pode executar um as pesquisas de registos seguinte.

#### <a name="linux"></a>Linux

```
Heartbeat
| where OSType == "Linux" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

#### <a name="windows"></a>Windows

```
Heartbeat
| where OSType == "Windows" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

Num computador Windows, pode rever as seguintes informações para verificar a conectividade de agente com os registos do Azure Monitor:

1. No painel de controlo, abra **Microsoft Monitoring Agent**. Sobre o **do Azure Log Analytics** guia, o agente apresenta a seguinte mensagem: **O Microsoft Monitoring Agent foi ligado com êxito para o Log Analytics**.
2. Abra o registo de eventos do Windows. Aceda a **aplicativo e o Gestor de registos de serviços** e procure o evento ID 3000 e 5002 de ID de evento da origem **conector do serviço**. Estes eventos indicam que o computador tiver registrado com a área de trabalho do Log Analytics e está a receber a configuração.

Se o agente não consegue comunicar com registos do Azure Monitor e o agente estiver configurado para comunicar com a internet através de um servidor de firewall ou proxy, confirme a firewall ou servidor proxy está configurado corretamente. Para saber como verificar o servidor de firewall ou proxy está configurado corretamente, veja [configuração de rede para o agente do Windows](../azure-monitor/platform/agent-windows.md) ou [configuração de rede para o agente Linux](../log-analytics/log-analytics-agent-linux.md).

> [!NOTE]
> Se os sistemas Linux estiverem configurados para comunicar com um proxy ou Gateway do Log Analytics e está integração essa solução, a atualização a *proxy. Conf* permissões para conceder ao grupo omiuser permissão de leitura no ficheiro ao utilizar o comandos seguintes:
>
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

Agentes do Linux adicionados recentemente mostram um Estado de **Updated** depois de uma avaliação foi realizada. Este processo pode demorar até seis horas.

Para confirmar que um grupo de gestão do Operations Manager está a comunicar com os registos do Azure Monitor, consulte [integração de validar o Operations Manager com os registos do Azure Monitor](../azure-monitor/platform/om-agents.md#validate-operations-manager-integration-with-azure-monitor).

## <a name="data-collection"></a>Recolha de dados

### <a name="supported-agents"></a>Agentes suportados

A tabela seguinte descreve as origens ligadas que são suportadas por esta solução:

| Origem ligada | Suportadas | Descrição |
| --- | --- | --- |
| Agentes do Windows |Sim |A solução recolhe informações sobre atualizações do sistema de agentes do Windows e, em seguida, inicia a instalação das atualizações necessárias. |
| Agentes do Linux |Sim |A solução recolhe informações sobre atualizações do sistema de agentes do Linux e, em seguida, inicia a instalação das atualizações obrigatórias em distribuições suportadas. |
| Grupo de gestão do Operations Manager |Sim |A solução recolhe informações sobre atualizações do sistema de agentes num grupo de gestão ligado.<br/>Uma conexão direta do agente do Operations Manager com os registos do Azure Monitor não é necessária. Os dados são reencaminhados do grupo de gestão para a área de trabalho do Log Analytics. |

### <a name="collection-frequency"></a>Frequência da recolha

É feita uma análise duas vezes por dia em cada computador Windows gerido. A cada 15 minutos, a API do Windows é chamada para consultar a hora da última atualização determinar se o estado foi alterado. Se o estado mudou, é iniciada uma análise de conformidade.

Uma análise é executada em três horas para cada computador Linux gerido.

Pode demorar entre 30 minutos e 6 horas para o dashboard apresentar os dados atualizados dos computadores gerenciados.

A média de utilização de dados de registos do Azure Monitor para uma máquina com o gerenciamento de atualizações é aproximadamente 25MB por mês. Este valor é apenas uma aproximação e está sujeitas a alterações com base no seu ambiente. Recomenda-se que monitorizar o seu ambiente para ver a utilização de exata que precisa.

## <a name="viewing-update-assessments"></a>Ver avaliações de atualização

Na sua conta de automatização, selecione **gestão de atualizações** para ver o estado das suas máquinas.

Esta vista fornece informações sobre as suas máquinas, em falta atualizações, implementações de atualizações e implementações de atualizações agendadas. Na **coluna de conformidade**, pode ver a última vez que a máquina foi avaliada. Na **a preparação de ATUALIZAÇÃO do agente** coluna, pode ver se o estado de funcionamento do agente de atualização. Se houver um problema, selecione a ligação para ir para a resolução de problemas de documentação que pode ajudar a que saber o que passos para corrigir o problema.

Para executar uma pesquisa de registos que devolve informações sobre o computador, atualização ou implementação, selecione o item na lista. O **pesquisa de registos** painel abre-se com uma consulta para o item selecionado:

![Vista de padrão de gestão de atualizações](media/automation-update-management/update-management-view.png)

## <a name="install-updates"></a>Instalar atualizações

Depois das atualizações são avaliadas para todos os computadores Linux e Windows na sua área de trabalho, pode instalar atualizações necessárias ao criar uma *implementação de atualização*. Uma implementação de atualização é uma instalação agendada de atualizações necessárias para um ou mais computadores. Especifique a data e hora para a implementação e um computador ou grupo de computadores a incluir no âmbito de uma implementação. Para saber mais sobre grupos de computadores, consulte [grupos de computadores nos registos do Azure Monitor](../azure-monitor/platform/computer-groups.md).

 Ao incluir grupos de computadores na sua implementação de atualização, a associação de grupo é avaliada apenas uma vez, no momento da criação da agenda. As alterações subsequentes a um grupo não são refletidas. Para contornar esse uso [grupos dinâmicos](#using-dynamic-groups), estes grupos são resolvidos no momento da implementação e são definidos por uma consulta.

> [!NOTE]
> Máquinas de virtuais do Windows que são implementadas no Azure Marketplace por predefinição, são definidas para receber atualizações automáticas do serviço de atualização do Windows. Este comportamento não é alterado quando adicionar esta solução ou adicionar as máquinas virtuais do Windows à sua área de trabalho. Se não gerir ativamente atualizações ao utilizar esta solução, aplica-se o comportamento predefinido (para aplicar atualizações automaticamente).

Para evitar as atualizações sejam aplicadas fora da janela de manutenção no Ubuntu, reconfigure o pacote Unattended-Upgrade para desativar as atualizações automáticas. Para obter informações sobre como configurar o pacote, consulte [tópico de atualizações automáticas no Guia do Ubuntu Server](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

Máquinas virtuais que foram criadas a partir de imagens de Red Hat Enterprise Linux (RHEL) a pedido que estão disponíveis no Azure Marketplace estão registadas para o acesso a [Red Hat atualização de infra-estrutura (RHUI)](../virtual-machines/virtual-machines-linux-update-infrastructure-redhat.md) que é implementado no Azure. Qualquer outra distribuição de Linux tem de ser atualizada de repositório de ficheiros online a distribuição através dos seguintes métodos com suporte a distribuição.

Para criar uma nova implementação de atualização, selecione **agendar a implementação da atualização**. O **nova implementação de atualização** painel abre-se. Introduza valores para as propriedades descritas na tabela seguinte e, em seguida, clique em **criar**:

| Propriedade | Descrição |
| --- | --- |
| Name |O nome exclusivo para identificar a implementação de atualizações. |
|Sistema Operativo| Linux ou Windows|
| Grupos de atualização (pré-visualização)|Defina uma consulta com base numa combinação de subscrição, grupos de recursos, localizações e as etiquetas para criar um grupo dinâmico de VMs do Azure para incluir na sua implementação. Para obter mais informações, consulte [grupos dinâmicos](automation-update-management.md#using-dynamic-groups)|
| Computadores a atualizar |Selecione uma pesquisa guardada, grupo importada, ou escolher máquina da lista pendente e selecione máquinas individuais. Se escolher **Máquinas**, a preparação da máquina é mostrada na coluna **ATUALIZAÇÃO DE PREPARAÇÃO DO AGENTE**.</br> Para saber mais sobre os diferentes métodos de criação de grupos de computadores nos registos do Azure Monitor, consulte o artigo [grupos de computadores nos registos do Azure Monitor](../azure-monitor/platform/computer-groups.md) |
|Classificações de atualizações|Selecione todas as classificações de atualização que precisa|
|Incluir/excluir atualizações|Esta ação abre o **incluir/excluir** página. As atualizações a serem incluídas ou excluídas estão em separadores diferentes. Para obter mais informações sobre como a inclusão é processada, consulte [comportamento de inclusão](automation-update-management.md#inclusion-behavior) |
|Definições da agenda|Selecione a hora para iniciar e selecionar qualquer uma vez ou periodicamente para a periodicidade|
| Pré- scripts de + pós-scripts|Selecione os scripts sejam executados antes e após a implementação|
| Janela de manutenção |Número de minutos definido para atualizações. O valor não pode ser inferior a 30 minutos e não mais de 6 horas |
| Controlo de reinício| Determina como devem ser tratadas reinicializações. As opções disponíveis são:</br>Reiniciar se for preciso (Predefinição)</br>Reiniciar sempre</br>Nunca reiniciar</br>Reiniciar apenas - não irá instalar atualizações|

Também é possível criar implementações de atualizações por meio de programação. Para saber como criar uma implementação de atualização com a API REST, veja [as configurações de atualização de Software - criar](/rest/api/automation/softwareupdateconfigurations/create). Também existe um runbook de exemplo que pode ser utilizado para criar uma implementação de atualização semanal. Para saber mais sobre este runbook, consulte [criar uma implementação de atualização semanal para uma ou mais VMs num grupo de recursos](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

### <a name="multi-tenant"></a>Implementações de atualização entre inquilinos

Se tiver máquinas noutro inquilino do Azure a comunicar com a gestão de atualizações que terá de aplicar o patch, terá de utilizar a solução abaixo para que eles agendada. Pode utilizar o [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule?view=azurermps-6.13.0) cmdlet com o comutador `-ForUpdate` para criar uma agenda e utilizar os [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration?view=azurermps-6.13.0
) cmdlet e passar o as máquinas no outro inquilino para o `-NonAzureComputer` parâmetro. O exemplo seguinte mostra um exemplo sobre como fazer isso:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="view-missing-updates"></a>Ver as atualizações em falta

Selecione **atualizações em falta** para ver a lista de atualizações que estão em falta das suas máquinas. Cada atualização está listada e pode ser selecionada. Informações sobre o número de máquinas que requerem a atualização, o sistema operativo e uma ligação para obter mais informações são mostradas. O **pesquisa de registos** painel mostra mais detalhes sobre as atualizações.

## <a name="view-update-deployments"></a>Implementações de atualização da vista

Selecione o **implementações de atualizações** separador para ver a lista de implementações de atualizações existentes. Selecione qualquer uma das implementações de atualização da tabela para abrir o **atualizar implementação executar** painel para essa implementação da atualização.

![Descrição geral dos resultados de implementação de atualização](./media/automation-update-management/update-deployment-run.png)

Para ver uma implementação de atualização a partir da API REST, consulte [execuções de configuração de atualização de Software](/rest/api/automation/softwareupdateconfigurationruns).

## <a name="update-classifications"></a>Classificações de atualizações

As tabelas seguintes listam as classificações de atualização na gestão de atualizações, com uma definição para cada classificação.

### <a name="windows"></a>Windows

|Classificação  |Descrição  |
|---------|---------|
|Atualizações críticas     | Uma atualização para um problema específico que corrige um erro crítico não relacionado com segurança.        |
|Atualizações de segurança     | Uma atualização para um problema específico do produto, relacionadas com segurança.        |
|Update rollups     | Um conjunto cumulativo de correções que são agrupadas para facilitar a implementação.        |
|Pacotes de funcionalidades     | Novas funcionalidades do produto que são distribuídas fora de uma versão do produto.        |
|Service packs     | Um conjunto cumulativo de correções que são aplicadas a uma aplicação.        |
|Atualizações de definições     | Uma atualização para vírus ou outros ficheiros de definição.        |
|Ferramentas     | Um utilitário ou funcionalidade que ajuda a concluir tarefas de um ou mais.        |
|Atualizações     | Uma atualização para uma aplicação ou ficheiro que está instalado atualmente.        |

### <a name="linux"></a>Linux

|Classificação  |Descrição  |
|---------|---------|
|Atualizações críticas e de segurança     | Atualizações para um problema específico ou um problema específico do produto, relacionadas com segurança.         |
|Outras atualizações     | Todas as outras atualizações que não são de natureza crítica ou que não são atualizações de segurança.        |

Para o Linux, a gestão de atualizações pode distinguir entre críticas e atualizações de segurança na cloud ao apresentar os dados da avaliação devido a enriquecimento de dados na cloud. Para a aplicação de patches, gerenciamento de atualizações baseia-se nos dados de classificação disponíveis na máquina. Ao contrário de outras distribuições, CentOS não tem essas informações disponíveis de imediato. Se tiver máquinas de CentOS configuradas de forma a retornar dados de segurança para o seguinte comando, gestão de atualizações será capaz de correção com base nas classificações.

```bash
sudo yum -q --security check-update
```

Atualmente, não existe nenhum método de método suportado para ativar a disponibilidade de dados de classificação nativos no CentOS. Neste momento, apenas de melhor esforço suporte é fornecido aos clientes que poderão ter ativada isso por conta própria.

## <a name="firstparty-predownload"></a>Definições avançadas

Gestão de atualizações que se baseia no Windows Update para transferir e instalar atualizações do Windows. Como resultado, respeitamos muitos das definições utilizadas pelo Windows Update. Se utilizar as definições para ativar as atualizações de não-Windows, gestão de atualizações irão gerir as atualizações também. Se pretender ativar a transferir atualizações antes de ocorre uma implementação de atualização, implementações de atualizações podem ir mais rápido e ser menos provável de exceder a janela de manutenção.

### <a name="pre-download-updates"></a>Atualizações de transferências de pré

Para configurar automaticamente a transferir atualizações na diretiva de grupo, pode definir o [definição configurar atualizações automáticas](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates#BKMK_comp5) ao **3**. Transfere as atualizações necessárias em segundo plano, mas não instalá-los. Isso mantém o gerenciamento de atualizações no controlo de agendas, mas permitir atualizações transferir fora da janela de manutenção de gestão de atualizações. Isso pode impedir **janela de manutenção excedida** erros na gestão de atualizações.

Também pode definir isso com o PowerShell, execute o PowerShell seguinte num sistema que pretende transferir automaticamente as atualizações.

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

### <a name="disable-automatic-installation"></a>Desativar a instalação automática

As VMs do Azure têm a instalação automática de atualizações ativada por predefinição. Isso pode fazer com que a instalação antes de agendá-las para ser instalado por gestão de atualizações de atualizações. Pode desativar esse comportamento, definindo a `NoAutoUpdate` chave de registo para `1`. O fragmento de PowerShell seguinte mostra-lhe uma forma de fazer isso.

```powershell
$AutoUpdatePath = "HKLM:SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU"
Set-ItemProperty -Path $AutoUpdatePath -Name NoAutoUpdate -Value 1
```

### <a name="enable-updates-for-other-microsoft-products"></a>Ativar as atualizações para outros produtos da Microsoft

Por predefinição, o Windows Update fornece apenas atualizações para Windows. Se habilitar **dar-me de atualizações para outros produtos Microsoft se eu atualizar o Windows**, é apresentada com as atualizações para outros produtos, incluindo patches de segurança para o SQL Server ou outro software de terceiros primeiro. Esta opção não pode ser configurada pela diretiva de grupo. Execute o PowerShell seguinte nos sistemas que deseja ativar outros primeiro patches de terceiros em e gestão de atualizações irão honrar esta definição.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="third-party"></a> Patches de terceiros no Windows

Gestão de atualizações baseia-se no WSUS ou a atualização do Windows de correção suportada sistemas Windows. Ferramentas como o [System Center Updates Publisher](/sccm/sum/tools/updates-publisher
) (Updates Publisher) permite-lhe publicar atualizações personalizadas no WSUS. Neste cenário permite a gestão de atualizações para máquinas de patch que usar o WSUS como seu repositório de atualização com o software de terceiros. Para saber como configurar o Updates Publisher, veja [instalar o Updates Publisher](/sccm/sum/tools/install-updates-publisher).

## <a name="ports"></a>Planeamento de rede

Os seguintes endereços são obrigatórios especificamente para a gestão de atualizações. Comunicação para estes endereços ocorre através da porta 443.

|Público do Azure  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|*.azure-automation.net|*.azure-automation.us|

Para obter mais informações sobre as portas que requer a função de trabalho de Runbook híbrida, veja [portas de função da função de trabalho híbrida](automation-hybrid-runbook-worker.md#hybrid-worker-role).

É recomendado para utilizar os endereços listados quando definir exceções. Para endereços IP, pode baixar o [intervalos de IP do Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653). Este ficheiro é atualizado semanalmente e reflete os intervalos implementados atualmente e as alterações futuras para os intervalos de IP.

## <a name="search-logs"></a>Pesquisar registos

Além dos detalhes que são fornecidos no portal do Azure, pode fazer pesquisas contra os registos. Nas páginas de solução, selecione **do Log Analytics**. O **pesquisa de registos** painel abre-se.

Também pode saber como personalizar as consultas ou utilizá-los a partir de diferentes clientes e mais, visite a página:  [Documentação da API de pesquisa do log Analytics](
https://dev.loganalytics.io/).

### <a name="sample-queries"></a>Amostras de consultas

As secções seguintes fornecem consultas de registo de exemplo para registos de atualizações que são recolhidos por esta solução:

#### <a name="single-azure-vm-assessment-queries-windows"></a>Consultas de avaliação de VM do Azure únicas (Windows)

Substitua o valor VMUUID com o GUID de VM da máquina virtual que estiver a consultar. Pode encontrar o VMUUID que deve ser utilizada ao executar a consulta seguinte nos registos do Azure Monitor: `Update | where Computer == "<machine name>" | summarize by Computer, VMUUID`

##### <a name="missing-updates-summary"></a>Resumo de atualizações em falta

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"b08d5afa-1471-4b52-bd95-a44fea6e4ca8"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security"
```

##### <a name="missing-updates-list"></a>Lista de atualizações em falta

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

#### <a name="single-azure-vm-assessment-queries-linux"></a>Consultas de avaliação do únicas VM do Azure (Linux)

Para algumas distribuições de Linux, há uma [ordenação de bits](https://en.wikipedia.org/wiki/Endianness) erro de correspondência com o valor VMUUID proveniente do Azure Resource Manager e o que é armazenado nos registos do Azure Monitor. A consulta seguinte verifica a existência de uma correspondência em qualquer ordenação de bits. Substitua os valores VMUUID com o formato de big-endian e little-endian do GUID corretamente retornar os resultados. Pode encontrar o VMUUID que deve ser utilizada ao executar a consulta seguinte nos registos do Azure Monitor: `Update | where Computer == "<machine name>"
| summarize by Computer, VMUUID`

##### <a name="missing-updates-summary"></a>Resumo de atualizações em falta

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

##### <a name="missing-updates-list"></a>Lista de atualizações em falta

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

#### <a name="multi-vm-assessment-queries"></a>Consultas de avaliação de várias VMS

##### <a name="computers-summary"></a>Resumos de computadores

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

##### <a name="missing-updates-summary"></a>Resumo de atualizações em falta

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
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security"
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

##### <a name="missing-updates-list"></a>Lista de atualizações em falta

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

## <a name="using-dynamic-groups"></a>Utilizando os grupos dinâmicos (pré-visualização)

Gestão de atualizações fornece a capacidade para visar um grupo dinâmico de VMs do Azure para implementações de atualizações. Estes grupos são definidos por uma consulta, quando uma implementação de atualização é iniciada, os membros desse grupo são avaliados. Grupos dinâmicos não funcionam com as VMs clássicas. Ao definir a sua consulta, os seguintes itens podem ser usados juntos para preencher o grupo dinâmico

* Subscrição
* Grupos de recursos
* Localizações
* Etiquetas

![Selecionar grupos](./media/automation-update-management/select-groups.png)

Para pré-visualizar os resultados de um grupo dinâmico, clique nas **pré-visualização** botão. Esta pré-visualização mostra a associação de grupo nessa altura, neste exemplo, que está a procurar máquinas com a marca **função** é igual a **BackendServer**. Se mais máquinas tiverem esta etiqueta adicionada, estes serão adicionados a todas as implementações futuras em relação a esse grupo.

![grupos de pré-visualização](./media/automation-update-management/preview-groups.png)

## <a name="integrate-with-system-center-configuration-manager"></a>Integrar no System Center Configuration Manager

Os clientes que investiram no System Center Configuration Manager para gerir PCs, servidores e dispositivos móveis também contam com a potência e maturidade do Configuration Manager para os ajudar a gerir atualizações de software. O Configuration Manager faz parte do respetivo ciclo de gestão (soma) de atualização de software.

Para saber como integrar a solução de gestão com o System Center Configuration Manager, veja [integrar o System Center Configuration Manager com a gestão de atualizações](oms-solution-updatemgmt-sccmintegration.md).

## <a name="inclusion-behavior"></a>Comportamento de inclusão

Inclusão de atualização permite-lhe especificar as atualizações específicas a aplicar. Patches ou pacotes que estão incluídas são instaladas. Quando Patches ou pacotes estão incluídas e uma classificação é selecionada também, são instalados os itens incluídos e os itens que atendam a classificação.

É importante saber que exclusões substituem as inclusões. Por exemplo, se definir uma regra de exclusão de `*`, em seguida, sem patches ou pacotes são instalados, como eles são todos excluídos. Excluídos patches show ainda como estando em falta da máquina. Para máquinas Linux se um pacote está incluído, mas tem um pacote de dependentes que foi excluído, o pacote não está instalado.

## <a name="patch-linux-machines"></a>Máquinas do Linux de patch

As secções seguintes explicam os potenciais problemas com a aplicação de patches do Linux.

### <a name="unexpected-os-level-upgrades"></a>Atualizações de nível de sistema operacional inesperadas

Em algumas variantes de Linux, como Red Hat Enterprise Linux, atualizações de nível de sistema operacional poderão ocorrer através de pacotes. Isso pode levar a execuções de gestão de atualizações em que o número de versão do SO é alterado. Como gestão de atualizações utiliza os mesmos métodos para atualizar os pacotes que um administrador usaria localmente no computador Linux, este comportamento é intencional.

Para evitar a atualizar a versão de sistema operacional por meio de execuções de gestão de atualizações, utilize o **exclusão** funcionalidade.

No Red Hat Enterprise Linux, o nome do pacote para excluir é redhat-release-server.x86_64.

![Pacotes a excluir para Linux](./media/automation-update-management/linuxpatches.png)

### <a name="critical--security-patches-arent-applied"></a>Críticas / de patches de segurança não foram aplicadas

Ao implementar atualizações de uma máquina Linux, pode selecionar as classificações de atualização. Este procedimento filtra as atualizações que são aplicadas à máquina que cumpram os critérios especificados. Este filtro é aplicado localmente na máquina, quando a atualização é implementada.

Como gestão de atualizações executa enriquecimento de atualização na cloud, algumas atualizações podem ser sinalizadas na gestão de atualizações como tendo impacto de segurança, mesmo que a máquina local não tem essa informação. Como resultado, se aplicar atualizações críticas a uma máquina Linux, poderá haver atualizações que não estejam marcadas como tendo o impacto de segurança que a máquina e as atualizações não são aplicadas.

No entanto, a gestão de atualizações ainda podem comunicar que a máquina como estando em não conformidade porque tem informações adicionais sobre a atualização relevante.

Implantação de atualizações por classificação da atualização não funciona no CentOS prontos a utilizar. Para implementar corretamente as atualizações para CentOS, selecione todas as classificações para garantir que as atualizações são aplicadas. Para o SUSE, selecionando *apenas* "Outras atualizações" como a classificação pode resultar em alguma segurança atualiza também a ser instalada se as atualizações de segurança relacionados com o zypper (Gestor de pacotes) ou as respetivas dependências são necessárias em primeiro lugar. Este comportamento é uma limitação do zypper. Em alguns casos, poderá ter de voltar a executar a implementação da atualização. Para verificar, verifique o registo de atualização.

## <a name="remove-a-vm-for-update-management"></a>Remover uma VM para gestão de atualizações

Para remover uma VM de gestão de atualizações:

* Na área de trabalho do Log Analytics, remover a VM da pesquisa guardada para a configuração de âmbito `MicrosoftDefaultScopeConfig-Updates`. Pesquisas guardadas podem ser encontradas na **gerais** na sua área de trabalho.
* Remover os [Microsoft Monitoring agent](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) ou o [agente do Log Analytics para Linux](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>Passos Seguintes

Avance para o tutorial para saber como gerir atualizações para as suas máquinas virtuais do Windows.

> [!div class="nextstepaction"]
> [Gerir atualizações e correções para as VMs do Windows Azure](automation-tutorial-update-management.md)

* Utilizar as pesquisas de registos no [registos do Azure Monitor](../log-analytics/log-analytics-log-searches.md) para ver os dados de atualizações detalhados.
* [Criar alertas](automation-tutorial-update-management.md#configure-alerts) para obter o estado de implementação de atualização.

* Para saber como interagir com a gestão de atualizações por meio da API REST, veja [as configurações de atualização de Software](/rest/api/automation/softwareupdateconfigurations)
* Para saber como resolver problemas de gestão de atualizações, veja [resolução de problemas de gestão de atualizações](troubleshoot/update-management.md)
