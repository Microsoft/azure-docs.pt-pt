---
title: Monitor Azure máquinas virtuais com Monitor Azure
description: Descreve como recolher e analisar dados de monitorização de máquinas virtuais em Azure utilizando o Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/17/2020
ms.openlocfilehash: 2cb53d0c88d8c29da2bd8bf52d6536555d56c76e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80283944"
---
# <a name="monitoring-azure-virtual-machines-with-azure-monitor"></a>Monitorização de máquinas virtuais Azure com Monitor Azure
Este artigo descreve como usar o Monitor Azure para recolher e analisar dados de monitorização de máquinas virtuais Azure para manter a sua saúde. As máquinas virtuais podem ser monitorizadas para disponibilidade e desempenho com o Azure Monitor como qualquer [outro recurso Azure](monitor-azure-resource.md), mas são únicas a partir de outros recursos, uma vez que também precisa de monitorizar o funcionamento e o sistema dos hóspedes e as cargas de trabalho que nele funcionam. 

> [!NOTE]
> Este artigo fornece uma visão geral completa dos conceitos e opções para monitorizar máquinas virtuais no Monitor Azure. Para começar a monitorizar rapidamente as suas máquinas virtuais sem se concentrar nos conceitos subjacentes, consulte [Quickstart: Monitor ize uma máquina virtual Azure com o Monitor Azure](../learn/quick-monitor-azure-vm.md).


## <a name="differences-from-other-azure-resources"></a>Diferenças em relação a outros recursos Azure
[A monitorização dos recursos do Azure com](monitor-azure-resource.md) o Azure Monitor descreve os dados de monitorização gerados pelos recursos do Azure e como pode utilizar as funcionalidades do Azure Monitor para analisar e alertar sobre estes dados. Pode recolher e atuar nos mesmos dados de monitorização das máquinas virtuais Azure com as seguintes diferenças:

- [As métricas da plataforma](../platform/data-platform-metrics.md) são recolhidas automaticamente para máquinas virtuais, mas apenas para o hospedeiro virtual da [máquina.](#monitoring-data) Precisa de um agente para recolher dados de desempenho do sistema operativo convidado. 
- As máquinas virtuais não geram [registos](../platform/platform-logs-overview.md) de recursos que fornecem informações sobre operações que foram realizadas dentro de um recurso Azure. Usa um agente para recolher dados de registo do sistema operativo convidado.
- Pode criar [configurações](../platform/diagnostic-settings.md) de diagnóstico para uma máquina virtual enviar métricas da plataforma para outros destinos, como centros de armazenamento e eventos, mas não é possível configurar estas definições de diagnóstico no portal Azure. 

## <a name="monitoring-data"></a>Monitorizar dados
As máquinas virtuais em Azure em Azure geram [troncos](../platform/data-platform-logs.md) e [métricas mostradas](../platform/data-platform-metrics.md) no seguinte diagrama.

![Descrição geral](media/monitor-vm-azure/logs-metrics.png)


### <a name="virtual-machine-host"></a>Anfitrião de máquina virtual
As máquinas virtuais em Azure geram os seguintes dados para a máquina virtual que acolhem os mesmos que outros recursos Azure, como descrito nos [dados de Monitorização.](monitor-azure-resource.md#monitoring-data)

- [Métricas](../platform/data-platform-metrics.md) da plataforma - valores numéricos que são automaticamente recolhidos em intervalos regulares e descrevem algum aspeto de um recurso em um determinado momento. As métricas da plataforma são recolhidas para o hospedeiro virtual da máquina, mas é necessário que a extensão de diagnóstico recolha métricas para o sistema operativo do hóspede.
- [Registo de atividade](../platform/platform-logs-overview.md) - Fornece informações sobre as operações em cada recurso Azure na subscrição a partir do exterior (o plano de gestão). Para uma máquina virtual, isto inclui informações como quando foi iniciada e qualquer mudança de configuração.


### <a name="guest-operating-system"></a>Sistema operativo convidado
Para recolher dados do sistema operativo de uma máquina virtual, é necessário um agente, que funciona localmente em cada máquina virtual e envia dados para o Monitor Azure. Vários agentes estão disponíveis para o Monitor Azure com cada um a recolher dados diferentes e a escrever dados para diferentes locais. Obtenha uma comparação detalhada dos diferentes agentes na [visão geral dos agentes do Monitor Azure.](../platform/agents-overview.md) 

- [Agente de Log Analytics](../platform/agents-overview.md#log-analytics-agent) - Disponível para máquinas virtuais em Azure, outros ambientes em nuvem e no local. Recolhe dados para registos do Monitor Azure. Suporta o Monitor Azure para VMs e soluções de monitorização. Este é o mesmo agente usado para o System Center Operations Manager.
- [Agente de dependência](../platform/agents-overview.md#dependency-agent) - Recolhe dados sobre os processos em execução na máquina virtual e suas dependências. Conta com o agente Log Analytics para transmitir dados para o Azure e suporta o Monitor Azure para vMs, mapa de serviço e soluções Wire Data 2.0.
- [Extensão de Diagnóstico Azure](../platform/agents-overview.md#azure-diagnostics-extension) - Disponível apenas para máquinas virtuais Do Monitor Azure. Pode recolher dados para vários locais, mas usado principalmente para recolher dados de desempenho dos hóspedes em Métricas do Monitor Do Azure para máquinas virtuais windows.
- [Agente telegrafo](../platform/collect-custom-metrics-linux-telegraf.md) - Recolher dados de desempenho de VMs Linux em Métricas de Monitor Azure.


## <a name="configuration-requirements"></a>Requisitos de configuração
Para permitir todas as funcionalidades do Monitor Azure para monitorizar uma máquina virtual, é necessário recolher dados de monitorização do sistema operativo virtual do anfitrião da máquina e do sistema operativo convidado, tanto para as [Métricas do Monitor Do Azure](../platform/data-platform-logs.md) como para [os registos do Monitor Azure.](../platform/data-platform-logs.md) A tabela seguinte enumera a configuração que deve ser executada para permitir esta recolha. Pode optar por não realizar todos estes passos dependendo dos seus requisitos específicos.

| Passo de configuração | Ações concluídas | Funcionalidades ativadas |
|:---|:---|:---|
| Sem configuração | - Métricas da plataforma hospedeira recolhidas para métricas.<br>- Registo de atividade recolhido. | - Explorador de métricas para hospedeiro.<br>- Alertas de métricas para hospedeiro.<br>- Alertas de registo de atividade. |
| [Ativar o Monitor Azure para VMs](#enable-azure-monitor-for-vms) | - Agente de Log Analytics instalado.<br>- Agente de dependência instalado.<br>- Dados de desempenho dos hóspedes recolhidos em Registos.<br>- Processo e detalhes de dependência recolhidos para Registos. | - Gráficos de desempenho e livros para dados de desempenho dos hóspedes.<br>- Registar consultas para os dados de desempenho dos hóspedes.<br>- Registo de alertas para os dados de desempenho dos hóspedes.<br>- Mapa de dependência. |
| [Instale a extensão de diagnóstico e o agente telegrafo](#enable-diagnostics-extension-and-telegraf-agent) | - Dados de desempenho dos hóspedes recolhidos para métricas. | - Explorador de métricas para hóspede.<br>- Alertas de métricas para hóspede.  |
| [Configure log Analytics espaço de trabalho](#configure-log-analytics-workspace) | - Eventos recolhidos de hóspedes. | - Registar consultas para eventos de hóspedes.<br>- Registar alertas para eventos de hóspedes. |
| [Criar definição de diagnóstico para máquina virtual](#collect-platform-metrics-and-activity-log) | - Métricas da plataforma recolhidas nos Registos.<br>- Registo de atividade recolhido nos Registos. | - Consultas loq para métricas hospedeiras.<br>- Registar alertas para métricas do hospedeiro.<br>- Registar consultas para registo de atividade.

Cada um destes passos de configuração é descrito nas seguintes secções.

### <a name="enable-azure-monitor-for-vms"></a>Ativar o Monitor Azure para VMs
[O Monitor Azure para VMs](vminsights-overview.md) é uma [visão](insights-overview.md) do Monitor Azure que é a principal ferramenta para monitorizar máquinas virtuais no Monitor Azure. Fornece o seguinte valor adicional sobre as funcionalidades padrão do Monitor Azure.

- Simplificado no embarque do agente log analytics e agente dependency para permitir a monitorização de um sistema operativo de hóspedes de máquina virtual e cargas de trabalho. 
- Gráficos de desempenho de tendência pré-definidos e livros de trabalho que lhe permitem analisar métricas de desempenho fundamentais do sistema operativo da máquina virtual.
- Mapa de dependência que exibe processos em execução em cada máquina virtual e os componentes interligados com outras máquinas e fontes externas.

![Azure Monitor para VMs](media/monitor-vm-azure/vminsights-01.png)

![Azure Monitor para VMs](media/monitor-vm-azure/vminsights-02.png)


Ative o Monitor Azure para VMs a partir da opção **Insights** no menu de máquinavirtual do portal Azure. Consulte o Monitor Enable Azure para obter informações sobre os [VMs](vminsights-enable-overview.md) para obter detalhes e outros métodos de configuração.

![Ativar o Monitor Azure para VMs](media/monitor-vm-azure/enable-vminsights.png)

### <a name="configure-log-analytics-workspace"></a>Configure log Analytics espaço de trabalho
O agente Log Analytics utilizado pelo Monitor Azure para VMs envia dados para um espaço de [trabalho de Log Analytics](../platform/data-platform-logs.md#how-is-data-in-azure-monitor-logs-structured). Pode ativar a recolha de dados adicionais de desempenho, eventos e outros dados de monitorização do agente configurando o espaço de trabalho log Analytics. Só precisa de ser configurado uma vez, uma vez que qualquer agente que se ligue ao espaço de trabalho irá automaticamente descarregar a configuração e começar imediatamente a recolher os dados definidos. 

Pode aceder à configuração do espaço de trabalho diretamente do Monitor Azure para VMs, selecionando a **configuração workspace** a partir do **Get Started**. Clique no nome do espaço de trabalho para abrir o seu menu.

![Configuração do espaço de trabalho](media/monitor-vm-azure/workspace-configuration.png)

Selecione **Definições Avançadas** a partir do menu espaço de trabalho e, em seguida, **Data** para configurar fontes de dados. Para os agentes windows, selecione Registos de **Eventos do Windows** e adicione registos comuns de eventos como *Sistema* e *Aplicação*. Para os agentes Linux, selecione **Syslog** e adicione instalações comuns como *kern* e *daemon*. Consulte as fontes de dados do [Agente no Monitor Azure](../platform/agent-data-sources.md) para obter uma lista das fontes de dados disponíveis e detalhes sobre a sua configuração. 

![Configurar eventos](media/monitor-vm-azure/configure-events.png)


> [!NOTE]
> Pode configurar contadores de desempenho a recolher a partir da configuração do espaço de trabalho, mas isto pode ser redundante com os contadores de desempenho recolhidos pelo Monitor Azure para VMs. O Monitor Azure para VMs recolhe o conjunto mais comum de contadores a uma frequência de uma vez por minuto. Apenas configure contadores de desempenho a recolher pelo espaço de trabalho se pretender recolher contadores ainda não recolhidos pelo Azure Monitor para VMs ou se tiver consultas existentes utilizando dados de desempenho.


### <a name="enable-diagnostics-extension-and-telegraf-agent"></a>Ativar a extensão de diagnósticoe agente Telegraf
O Monitor Azure para VMs baseia-se no agente Log Analytics que recolhe dados num espaço de trabalho de Log Analytics. Isto suporta [múltiplas funcionalidades do Monitor Azure,](../platform/data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) tais como consultas de [registo,](../log-query/log-query-overview.md) [alertas](../platform/alerts-log.md)de registo e livros de [trabalho.](../platform/workbooks-overview.md) A [extensão](../platform/diagnostics-extension-overview.md) de diagnóstico recolhe dados de desempenho do sistema operativo convidado de máquinas virtuais Windows para o Armazenamento Azure e envia opcionalmente dados de desempenho para [as Métricas do Monitor Do Azure](../platform/data-platform-metrics.md). Para as máquinas virtuais Linux, o [agente Telegraf](../platform/collect-custom-metrics-linux-telegraf.md) é obrigado a enviar dados para a Azure Metrics.  Isto permite outras funcionalidades do Monitor Azure, tais como o explorador de [métricas](../platform/metrics-getting-started.md) e alertas de [métricas.](../platform/alerts-metric.md) Também pode configurar a extensão de diagnóstico para enviar eventos e dados de desempenho fora do Azure Monitor utilizando hubs de eventos Azure.

Instale a extensão de diagnóstico para uma única máquina virtual do Windows no portal Azure a partir da opção de definição de **Diagnósticos** no menu VM. Selecione a opção para ativar o **Monitor Azure** no **separador Sinks.** Para ativar a extensão de um modelo ou linha de comando para várias máquinas virtuais, consulte a [instalação e a configuração](../platform/diagnostics-extension-overview.md#installation-and-configuration). Ao contrário do agente Log Analytics, os dados a recolher são definidos na configuração para a extensão em cada máquina virtual.

![Definição de diagnóstico](media/monitor-vm-azure/diagnostic-setting.png)

Consulte [a Instalação e configure](../platform/collect-custom-metrics-linux-telegraf.md#install-and-configure-telegraf) a Telegraf para obter detalhes sobre a configuração dos agentes Telegraf em máquinas virtuais Linux. A opção de **menu de definição de diagnóstico** está disponível para o Linux, mas só lhe permitirá enviar dados para o armazenamento do Azure.

### <a name="collect-platform-metrics-and-activity-log"></a>Recolher métricas da plataforma e registo de atividade
Pode ver as métricas da plataforma e o registo de atividade recolhido para cada máquina virtual hospedada no portal Azure. Colete estes dados no mesmo espaço de trabalho do Log Analytics que o Monitor Azure para vMs para analisá-los com os outros dados de monitorização recolhidos para a máquina virtual. Esta coleção está configurada com uma [definição de diagnóstico.](../platform/diagnostic-settings.md) Recolher o registo de Atividade com uma [definição de diagnóstico para a subscrição](../platform/diagnostic-settings.md#create-diagnostic-settings-in-azure-portal).

Colete métricas da plataforma com uma definição de diagnóstico para a máquina virtual. Ao contrário de outros recursos Azure, não é possível criar uma definição de diagnóstico para uma máquina virtual no portal Azure, mas deve utilizar [outro método](../platform/diagnostic-settings.md#create-diagnostic-settings-using-powershell). Os exemplos seguintes mostram como recolher métricas para uma máquina virtual usando powerShell e CLI.

```powershell
Set-AzDiagnosticSetting -Name vm-diagnostics -ResourceId "/subscriptions/monitor diagnostic-settings create \
xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm" -Enabled $true -MetricCategory AllMetrics -workspaceId "/subscriptions/monitor diagnostic-settings create \
xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
```

```CLI
az monitor diagnostic-settings create \
--name VM-Diagnostics 
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace

```

## <a name="monitoring-in-the-azure-portal"></a>Monitorização no portal Azure 
Uma vez configurado a recolha de dados de monitorização para uma máquina virtual, tem várias opções para acessá-lo no portal Azure:

- Utilize o menu **Azure Monitor** para aceder a dados de todos os recursos monitorizados. 
- Utilize o Monitor Azure para VMs para monitorizar conjuntos de máquinas virtuais em escala.
- Analise os dados de uma única máquina virtual a partir do seu menu no portal Azure. A tabela abaixo lista diferentes opções para monitorizar o menu de máquinas virtuais.

![Monitorização no portal Azure](media/monitor-vm-azure/monitor-menu.png)

| Opção menu | Descrição |
|:---|:---|
| Descrição geral | Exibe [métricas](../platform/data-platform-metrics.md) de plataforma para o hospedeiro virtual da máquina. Clique num gráfico para trabalhar com estes dados no explorador de [métricas](../platform/metrics-getting-started.md). |
| Registo de atividades | [Entradas](../platform/activity-log-view.md) de registo de atividade filtradas para a máquina virtual atual. |
| Informações | Abre o [Monitor Azure para VMs](../insights/vminsights-overview.md) com o mapa para a máquina virtual atual selecionada. |
| Alertas | Vê [alertas](../platform/alerts-overview.md) para a atual máquina virtual.  |
| Métricas | Explorador de [métricas abertas](../platform/metrics-getting-started.md) com o âmbito definido para a máquina virtual atual. |
| Definições de diagnóstico | Ativar e configurar a [extensão](../platform/diagnostics-extension-overview.md) de diagnóstico para a máquina virtual atual. |
| Recomendações do assistente | Recomendações para a atual máquina virtual do [Azure Advisor.](/azure/advisor/) |
| Registos | Abra o [Log Analytics](../log-query/log-query-overview.md#what-is-log-analytics) com o [âmbito](../log-query/scope.md) definido para a máquina virtual atual. |
| Monitor de ligação | Open [Network Watcher Connection Monitor](../../network-watcher/connection-monitor-preview.md) para monitorizar as ligações entre a máquina virtual atual e outras máquinas virtuais. |


## <a name="analyzing-metric-data"></a>Analisar dados métricos
Pode analisar métricas para máquinas virtuais utilizando o explorador de métricas, abrindo **métricas** a partir do menu da máquina virtual. Consulte [Começar com o Azure Metrics Explorer](../platform/metrics-getting-started.md) para obter detalhes sobre a utilização desta ferramenta. 

Existem dois espaços de nome utilizados por máquinas virtuais para métricas:

| Espaço de nomes | Descrição |
|:---|:---|
| Anfitrião de Máquinas Virtuais | As métricas do hospedeiro são recolhidas automaticamente para todas as máquinas virtuais Azure. Lista detalhada de métricas em [Microsoft.Compute/virtualMachines](../platform/metrics-supported.md#microsoftcomputevirtualmachines). |
| Hóspede da Máquina Virtual | As métricas do sistema operativo dos hóspedes recolhidas a partir de máquinas virtuais com extensão de diagnóstico instalada supor e configuradas para enviar para a pia do Monitor Azure. |

![Métricas](media/monitor-vm-azure/metrics.png)

## <a name="analyzing-log-data"></a>Analisar dados de registo
As máquinas virtuais Azure recolherão os seguintes dados para os Registos do Monitor Azure. 

O Monitor Azure para VMs permite a recolha de um conjunto pré-determinado de contadores de desempenho que estão escritos na tabela *InsightsMetrics.* Esta é a mesma tabela utilizada pelo [Monitor Azure para contentores.](container-insights-overview.md) 

| Origem de dados | Requisitos | Tabelas |
|:---|:---|:---|
| Azure Monitor para VMs | Ativar em cada máquina virtual. | InsightsMetrics<br>VmboundPort<br>VMComputer<br>VMConnection<br>VMProcess<br>Consulte [como consultar os registos do Monitor Azure para obter informações sobre os VMs.](vminsights-log-search.md) |
| Registo de atividades | Definição de diagnóstico para a subscrição. | AzureActivity |
| Métricas de hospedeiro | Definição de diagnóstico para a máquina virtual. | AzureMetrics |
| Fontes de dados do sistema operativo convidado | Ative o agente Log Analytics e configure as fontes de dados. | Consulte a documentação para cada fonte de dados. |


> [!NOTE]
> Os dados de desempenho recolhidos pelo agente Log Analytics escrevem na tabela *Perf* enquanto o Monitor Azure para VMs irá recolhê-lo para a tabela *InsightsMetrics.* Estes são os mesmos dados, mas as tabelas têm uma estrutura diferente. Se tiver consultas existentes com base no *Perf,* terá de ser reescrita para utilizar *insightsMetrics*.


## <a name="alerts"></a>Alertas
[Os alertas](../platform/alerts-overview.md) no Monitor Azure notificam-no proativamente quando forem encontradas condições importantes nos seus dados de monitorização e potencialmente lançar uma ação como iniciar uma Aplicação Lógica ou chamar um webhook. As regras de alerta definem a lógica utilizada para determinar quando deve ser criado um alerta. O Azure Monitor recolhe os dados utilizados pelas regras de alerta, mas é necessário criar regras para definir condições de alerta na sua subscrição do Azure.

As seguintes secções descrevem os tipos de regras de alerta e recomendações sobre quando deve utilizar cada um. Esta recomendação baseia-se na funcionalidade e no custo do tipo de regra de alerta. Para mais detalhes, consulte os preços do [Monitor Azure](https://azure.microsoft.com/pricing/details/monitor/).


### <a name="activity-log-alert-rules"></a>Regras de alerta de registo de atividade
[Regras](../platform/alerts-activity-log.md) de alerta de registo de atividade saem quando uma entrada que corresponda a critérios específicos é criada no registo de atividade. Eles não têm custos, então eles devem ser a sua primeira escolha se a lógica que você precisa está no registo de atividade. 

O recurso-alvo para alertas de registo de atividade pode ser uma máquina virtual específica, todas as máquinas virtuais de um grupo de recursos, ou todas as máquinas virtuais numa subscrição.

Por exemplo, crie um alerta se uma máquina virtual crítica for interrompida selecionando a *Máquina Virtual Power Off* para o nome do sinal.

![Alerta de registo de atividade](media/monitor-vm-azure/activity-log-alert.png)


### <a name="metric-alert-rules"></a>Regras de alerta de métricas
[Regras de alerta métrica saem](../platform/alerts-metric.md) quando um valor métrico excede um limiar. Pode definir um valor limiar específico ou permitir que o Monitor Azure determine dinamicamente um limiar baseado em dados históricos.  Utilize alertas métricos sempre que possível com dados métricos, uma vez que custam menos e são mais responsivos do que as regras de alerta de log. Também são um significado audais que se resolverão quando a métrica cair abaixo do limiar.

O recurso-alvo para alertas de registo de atividade pode ser uma máquina virtual específica ou todas as máquinas virtuais de um grupo de recursos.

Por exemplo, para criar um alerta quando o processador de uma máquina virtual excede um valor particular, criar uma regra de alerta métrica utilizando a *Percentagem CPU* como tipo de sinal. Estabeleça um valor limiar específico ou permita que o Monitor Azure estabeleça um limiar dinâmico. 

![Alerta métrico](media/monitor-vm-azure/metric-alert.png)

### <a name="log-alerts"></a>Alertas de registo
[As regras](../platform/alerts-log.md) de alerta de log disparam quando os resultados de uma consulta de registo programada correspondem a determinados critérios. Os alertas de consulta de log são os mais caros e menos responsivos das regras de alerta, mas têm acesso aos dados mais diversos e podem executar uma lógica complexa que não pode ser executada pelas outras regras de alerta. 

O recurso-alvo para uma consulta de log é um espaço de trabalho log Analytics. Filtrar computadores específicos na consulta.

Por exemplo, para criar um alerta que verifica se alguma máquina virtual de um determinado grupo de recursos está offline, use a seguinte consulta que devolve um registo para cada computador que falhou um batimento cardíaco nos últimos dez minutos. Utilize um limiar de 1 que dispara se pelo menos um computador tiver um batimento cardíaco perdido.

```kusto
Heartbeat
| where TimeGenerated < ago(10m)
| where ResourceGroup == "my-resource-group"
| summarize max(TimeGenerated) by Computer
```

![Alerta de log](media/monitor-vm-azure/log-alert-01.png)

Para criar um alerta se ocorrerem um número excessivo de logons falhados em quaisquer máquinas virtuais do Windows na subscrição, utilize a seguinte consulta que devolve um registo para cada evento de logon falhado na última hora. Utilize um limiar definido para o número de logons falhados que permitirá. 

```kusto
Event
| where TimeGenerated < ago(1hr)
| where EventID == 4625
```

![Alerta de log](media/monitor-vm-azure/log-alert-02.png)


## <a name="system-center-operations-manager"></a>System Center Operations Manager
O System Center Operations Manager (SCOM) fornece monitorização granular das cargas de trabalho em máquinas virtuais. Consulte o Guia de [Monitorização](https://docs.microsoft.com/azure/cloud-adoption-framework/manage/monitor/) de Nuvem para uma comparação de plataformas de monitorização e diferentes estratégias de implementação.

Se tiver um ambiente SCOM existente que pretende continuar a utilizar, pode integrá-lo com o Monitor Azure para fornecer funcionalidades adicionais. O agente Log Analytics utilizado pelo Monitor Azure é o mesmo utilizado para o SCOM para que tenha monitorizado as máquinas virtuais enviar dados para ambos. Ainda é necessário adicionar o agente ao Monitor Azure para VMs e configurar o espaço de trabalho para recolher dados adicionais, conforme especificado acima, mas as máquinas virtuais podem continuar a executar os seus pacotes de gestão existentes num ambiente SCOM sem modificação.

As funcionalidades do Monitor Azure que aumentam as funcionalidades existentes do SCOM incluem:

- Utilize o Log Analytics para analisar interativamente os seus dados de registo e desempenho.
- Utilize alertas de registo para definir condições de alerta em várias máquinas virtuais e utilizando tendências de longo prazo que não são possíveis usando alertas no SCOM.   

Consulte o [Connect Operations Manager ao Azure Monitor](../platform/om-agents.md) para obter mais informações sobre a ligação do seu grupo de gestão SCOM existente ao seu espaço de trabalho Log Analytics.


## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar dados em registos do Monitor Azure utilizando consultas de registo.](../log-query/get-started-queries.md)
* [Aprenda sobre alertas utilizando métricas e registos no Monitor Azure.](../platform/alerts-overview.md)

