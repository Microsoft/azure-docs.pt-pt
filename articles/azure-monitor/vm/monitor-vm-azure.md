---
title: Monitor Azure máquinas virtuais com Monitor Azure
description: Descreve como recolher e analisar dados de monitorização de máquinas virtuais em Azure usando o Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/05/2020
ms.openlocfilehash: 3b383c6c01bd6acc54e2e38c5188a12ba38243e1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100614931"
---
# <a name="monitoring-azure-virtual-machines-with-azure-monitor"></a>Monitorização de máquinas virtuais Azure com Monitor Azure
Este artigo descreve como usar o Azure Monitor para recolher e analisar dados de monitorização de máquinas virtuais Azure para manter a sua saúde. As máquinas virtuais podem ser monitorizadas para disponibilidade e desempenho com o Azure Monitor como qualquer [outro recurso Azure](../insights/monitor-azure-resource.md), mas são únicas a partir de outros recursos, uma vez que também é necessário monitorizar o funcionamento e o sistema de hóspedes e as cargas de trabalho que o operam. 

> [!NOTE]
> Este artigo fornece uma visão geral completa dos conceitos e opções para monitorizar máquinas virtuais no Azure Monitor. Para começar a monitorizar rapidamente as suas máquinas virtuais sem se concentrar nos conceitos subjacentes, consulte [Quickstart: Monitorize uma máquina virtual Azure com o Azure Monitor](../learn/quick-monitor-azure-vm.md).


## <a name="differences-from-other-azure-resources"></a>Diferenças de outros recursos da Azure
[Monitorizar os recursos do Azure com o Azure Monitor](../insights/monitor-azure-resource.md) descreve os dados de monitorização gerados pelos recursos da Azure e como pode utilizar as funcionalidades do Azure Monitor para analisar e alertar estes dados. Pode recolher e agir com os mesmos dados de monitorização das máquinas virtuais Azure com as seguintes diferenças:

-  [As métricas da plataforma](../platform/data-platform-metrics.md) são recolhidas automaticamente para máquinas virtuais, mas apenas para o [hospedeiro de máquinas virtuais.](#monitoring-data) Precisa de um agente para recolher dados de desempenho do sistema operativo dos hóspedes. 
- As máquinas virtuais não geram [registos de recursos](../platform/platform-logs-overview.md) que fornecem informações sobre operações que foram realizadas dentro de um recurso Azure. Utiliza um agente para recolher dados de registo do sistema operativo do hóspede.
- Pode criar [configurações de diagnóstico](../platform/diagnostic-settings.md) para uma máquina virtual para enviar métricas da plataforma para outros destinos, como armazenamento e centros de eventos, mas não é possível configurar estas definições de diagnóstico no portal Azure. 

## <a name="monitoring-data"></a>Monitorizar dados
As máquinas virtuais em Azure geram [troncos](../platform/data-platform-logs.md) e [métricas](../platform/data-platform-metrics.md) como mostrado no diagrama seguinte.

![Descrição Geral](media/monitor-vm-azure/logs-metrics.png)


### <a name="virtual-machine-host"></a>Anfitrião de máquinas virtuais
As máquinas virtuais em Azure geram os seguintes dados para a máquina virtual que acolhem os mesmos que outros recursos Azure descritos nos [dados de monitorização.](../insights/monitor-azure-resource.md#monitoring-data)

- [Métricas da plataforma](../platform/data-platform-metrics.md) - Valores numéricos que são automaticamente recolhidos a intervalos regulares e descrevem algum aspeto de um recurso num determinado momento. As métricas da plataforma são recolhidas para o hospedeiro de máquinas virtuais, mas você precisa da extensão de diagnóstico para recolher métricas para o sistema operativo convidado.
- [Registo de atividades](../platform/platform-logs-overview.md) - Fornece informações sobre as operações em cada recurso Azure na subscrição a partir do exterior (o plano de gestão). Para uma máquina virtual, isto inclui informações como quando foi iniciado e qualquer alteração de configuração.


### <a name="guest-operating-system"></a>Sistema operativo convidado
Para recolher dados do sistema operativo convidado de uma máquina virtual, é necessário um agente, que funciona localmente em cada máquina virtual e envia dados para o Azure Monitor. Vários agentes estão disponíveis para o Azure Monitor, com cada um a recolher dados diferentes e a escrever dados para diferentes locais. Obtenha uma comparação detalhada dos diferentes agentes na [visão geral dos agentes do Monitor Azure](../agents/agents-overview.md). 

- [Log Analytics agent](../agents/agents-overview.md#log-analytics-agent) - Disponível para máquinas virtuais em Azure, outros ambientes em nuvem e no local. Recolhe dados para registos do Monitor Azure. Suporta o Azure Monitor para VMs e soluções de monitorização. Este é o mesmo agente usado para Gerente de Operações do Centro de Sistema.
- [Agente de dependência](../agents/agents-overview.md#dependency-agent) - Recolhe dados sobre os processos em execução na máquina virtual e as suas dependências. Conta com o agente Log Analytics para transmitir dados para o Azure e suporta o Azure Monitor para soluções VMs, Service Map e Wire Data 2.0.
- [Extensão de Diagnóstico Azure](../agents/agents-overview.md#azure-diagnostics-extension) - Disponível apenas para máquinas virtuais Azure Monitor. Pode recolher dados em vários locais, mas usado principalmente para recolher dados de desempenho dos hóspedes em Azure Monitor Metrics para máquinas virtuais do Windows.
- [Telegraf agent](../platform/collect-custom-metrics-linux-telegraf.md) - Colete dados de desempenho de VMs Linux em Métricas de Monitor Azure.


## <a name="configuration-requirements"></a>Requisitos de configuração
Para ativar todas as funcionalidades do Azure Monitor para monitorizar uma máquina virtual, é necessário recolher dados de monitorização do hospedeiro de máquinas virtuais e do sistema operativo de hóspedes para as [Métricas do Monitor Azure](../platform/data-platform-logs.md) e [para os Registos do Monitor Azure.](../platform/data-platform-logs.md) A tabela a seguir lista a configuração que deve ser executada para permitir esta recolha. Pode optar por não realizar todos estes passos, dependendo dos seus requisitos específicos.

| Passo de configuração | Ações concluídas | Funcionalidades ativadas |
|:---|:---|:---|
| Sem configuração | - Métricas de plataforma de anfitrião recolhidas em Métricas.<br>- Registo de atividade recolhido. | - Explorador de métricas para hospedeiro.<br>- Alertas de métricas para hospedeiro.<br>- Alertas de registo de atividade. |
| [Ativar monitor Azure para VMs](#enable-azure-monitor-for-vms) | - Agente de registo de análise instalado.<br>- Agente de dependência instalado.<br>- Dados de desempenho dos hóspedes recolhidos nos Registos.<br>- Detalhes de processo e dependência recolhidos nos Registos. | - Gráficos de desempenho e livros de trabalho para dados de desempenho dos hóspedes.<br>- Registar consultas para os dados de desempenho dos hóspedes.<br>- Registar alertas para os dados de desempenho dos hóspedes.<br>- Mapa de dependência. |
| [Instale a extensão de diagnóstico e o agente telegraf](#enable-diagnostics-extension-and-telegraf-agent) | - Dados de desempenho dos hóspedes recolhidos nas Métricas. | - Explorador de métricas para hóspede.<br>- Alertas de métricas para o hóspede.  |
| [Configurar a área de trabalho do Log Analytics](#configure-log-analytics-workspace) | - Eventos recolhidos pelo hóspede. | - Registar consultas para eventos de hóspedes.<br>- Registar alertas para eventos de hóspedes. |
| [Criar definição de diagnóstico para máquina virtual](#collect-platform-metrics-and-activity-log) | - Métricas de plataforma recolhidas em Registos.<br>- Registo de atividade recolhido em Registos. | - Registar consultas para métricas de anfitrião.<br>- Registar alertas para as métricas do anfitrião.<br>- Registar consultas para registo de atividade.

Cada um destes passos de configuração é descrito nas seguintes secções.

### <a name="enable-azure-monitor-for-vms"></a>Ativar monitor Azure para VMs
[O Azure Monitor for VMs](../vm/vminsights-overview.md) é uma [visão](../monitor-reference.md) no Azure Monitor que é a principal ferramenta para monitorizar máquinas virtuais no Azure Monitor. Fornece o seguinte valor adicional sobre as funcionalidades padrão do Azure Monitor.

- A bordo simplificada do agente log analytics e agente de dependência para permitir a monitorização de um sistema operativo de hóspedes de máquina virtual e cargas de trabalho. 
- Gráficos de desempenho de tendência pré-definidos e livros de trabalho que permitem analisar métricas de desempenho do núcleo do sistema operativo de hóspedes da máquina virtual.
- Mapa de dependência que exibe processos em funcionamento em cada máquina virtual e os componentes interligados com outras máquinas e fontes externas.

![Monitor Azure para vista de desempenho de VMs](media/monitor-vm-azure/vminsights-01.png)

![Monitor Azure para visualização de mapas VMs](media/monitor-vm-azure/vminsights-02.png)


Ativar o Azure Monitor para VMs a partir da opção **Insights** no menu de máquinas virtuais do portal Azure. Consulte [o Enable Azure Monitor para ver os VMs](vminsights-enable-overview.md) para obter detalhes e outros métodos de configuração.

![Ativar monitor Azure para VMs](media/monitor-vm-azure/enable-vminsights.png)

### <a name="configure-log-analytics-workspace"></a>Configurar a área de trabalho do Log Analytics
O agente Log Analytics utilizado pelo Azure Monitor para VMs envia dados para um [espaço de trabalho log analytics](../platform/data-platform-logs.md). Pode ativar a recolha de dados de desempenho adicionais, eventos e outros dados de monitorização do agente configurando o espaço de trabalho Log Analytics. Só precisa de ser configurado uma vez, uma vez que qualquer agente que se conecte ao espaço de trabalho descarrega automaticamente a configuração e começa imediatamente a recolher os dados definidos. 

Pode aceder à configuração do espaço de trabalho diretamente a partir do Azure Monitor para VMs selecionando a **configuração** do Espaço de Trabalho a partir do **Get Started**. Clique no nome do espaço de trabalho para abrir o seu menu.

![Configuração da área de trabalho](media/monitor-vm-azure/workspace-configuration.png)

Selecione **Definições Avançadas** do menu do espaço de trabalho e, em seguida, **dados** para configurar fontes de dados. Para os agentes windows, selecione **Registos de Eventos do Windows** e adicione registos de eventos comuns, como *Sistema* e *Aplicação.* Para os agentes Linux, selecione **Syslog** e adicione instalações comuns como *kern* e *daemon*. Consulte [as fontes de dados do Agente no Azure Monitor](../agents/agent-data-sources.md) para obter uma lista das fontes de dados disponíveis e detalhes sobre a sua configuração. 

![Configure eventos](media/monitor-vm-azure/configure-events.png)


> [!NOTE]
> Pode configurar contadores de desempenho a serem recolhidos a partir da configuração do espaço de trabalho, mas isso pode ser redundante com os contadores de desempenho recolhidos pelo Azure Monitor para VMs. O Azure Monitor para VMs recolhe o conjunto de contadores mais comum numa frequência de uma vez por minuto. Apenas configurar contadores de desempenho a serem recolhidos pelo espaço de trabalho se pretender recolher contadores ainda não recolhidos pelo Azure Monitor para VMs ou se tiver consultas existentes utilizando dados de desempenho.


### <a name="enable-diagnostics-extension-and-telegraf-agent"></a>Permitir extensão de diagnóstico e agente Telegraf
O Azure Monitor para VMs baseia-se no agente Log Analytics que envia dados para um espaço de trabalho do Log Analytics. Isto suporta múltiplas funcionalidades do Azure Monitor, tais como [consultas de registo,](../log-query/log-query-overview.md) [alertas](../alerts/alerts-log.md)de registo e [livros de trabalho.](../visualize/workbooks-overview.md) A [extensão de diagnóstico](../agents/diagnostics-extension-overview.md) recolhe dados de desempenho do sistema operativo convidado de máquinas virtuais do Windows para o Azure Storage e envia opcionalmente dados de desempenho para as [Métricas do Monitor Azure](../platform/data-platform-metrics.md). Para máquinas virtuais Linux, o [agente Telegraf](../platform/collect-custom-metrics-linux-telegraf.md) é obrigado a enviar dados para a Azure Metrics.  Isto permite outras funcionalidades do Azure Monitor, tais como [métricas exploradores](../platform/metrics-getting-started.md) e [alertas de métricas.](../alerts/alerts-metric.md) Também pode configurar a extensão de diagnóstico para enviar eventos e dados de desempenho fora do Azure Monitor usando Azure Event Hubs.

Instale a extensão de diagnóstico para uma única máquina virtual Do Windows no portal Azure a partir da opção **de definição de Diagnóstico** no menu VM. Selecione a opção para ativar o **Monitor Azure** no **separador Sinks.** Para ativar a extensão a partir de um modelo ou linha de comando para várias máquinas virtuais, consulte [a instalação e a configuração](../agents/diagnostics-extension-overview.md#installation-and-configuration). Ao contrário do agente Log Analytics, os dados a recolher são definidos na configuração para a extensão em cada máquina virtual.

![Definição de diagnóstico](media/monitor-vm-azure/diagnostic-setting.png)

Consulte [a Instalação e configurar a Telegraf](../platform/collect-custom-metrics-linux-telegraf.md#install-and-configure-telegraf) para obter mais detalhes sobre a configuração dos agentes Telegraf em máquinas virtuais Linux. A opção **de definição de diagnóstico** está disponível para o Linux, mas só lhe permitirá enviar dados para o armazenamento do Azure.

### <a name="collect-platform-metrics-and-activity-log"></a>Recolher métricas da plataforma e log de atividade
Pode ver as métricas da plataforma e o registo de atividade recolhido para cada anfitrião de máquinas virtuais no portal Azure. Recolha estes dados no mesmo espaço de trabalho do Log Analytics que o Azure Monitor para VMs para analisá-los com os outros dados de monitorização recolhidos para a máquina virtual. Esta coleção está configurada com uma [definição de diagnóstico](../platform/diagnostic-settings.md). Recolher o registo de Atividade com uma [definição de diagnóstico para a subscrição](../platform/diagnostic-settings.md#create-in-azure-portal).

Colete as métricas da plataforma com uma definição de diagnóstico para a máquina virtual. Ao contrário de outros recursos Azure, não é possível criar uma definição de diagnóstico para uma máquina virtual no portal Azure, mas deve utilizar [outro método](../platform/diagnostic-settings.md#create-using-powershell). Os exemplos a seguir mostram como recolher métricas para uma máquina virtual usando tanto o PowerShell como o CLI.

```powershell
Set-AzDiagnosticSetting -Name vm-diagnostics -ResourceId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm" -Enabled $true -MetricCategory AllMetrics -workspaceId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
```

```azurecli
az monitor diagnostic-settings create \
--name VM-Diagnostics 
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace
```

## <a name="monitoring-in-the-azure-portal"></a>Monitorização no portal Azure 
Uma vez configurada a recolha de dados de monitorização para uma máquina virtual, tem várias opções para aceder aos mesmos no portal Azure:

- Utilize o menu **Azure Monitor** para aceder aos dados de todos os recursos monitorizados. 
- Utilize o Monitor Azure para VMs para monitorizar conjuntos de máquinas virtuais à escala.
- Analise os dados de uma única máquina virtual a partir do seu menu no portal Azure. A tabela abaixo lista diferentes opções para monitorizar o menu de máquinas virtuais.

![Monitorização no portal Azure](media/monitor-vm-azure/monitor-menu.png)

| Opção menu | Descrição |
|:---|:---|
| Descrição geral | Exibe [métricas](../platform/data-platform-metrics.md) da plataforma para o hospedeiro de máquina virtual. Clique num gráfico para trabalhar com estes dados no [explorador de métricas.](../platform/metrics-getting-started.md) |
| Registo de atividades | [Entradas de registo](../essentials/activity-log.md#view-the-activity-log) de atividade filtradas para a máquina virtual atual. |
| Informações | Abre [o Azure Monitor para VMs](../vm/vminsights-overview.md) com o mapa para a máquina virtual atual selecionada. |
| Alertas | Vistas [alertas](../platform/alerts-overview.md) para a máquina virtual atual.  |
| Métricas | Explorador [de métricas](../platform/metrics-getting-started.md) abertas com o âmbito definido para a máquina virtual atual. |
| Definições de diagnóstico | Ativar e configurar [a extensão de diagnóstico](../agents/diagnostics-extension-overview.md) para a máquina virtual atual. |
| Recomendações do assistente | Recomendações para a atual máquina virtual do [Azure Advisor](../../advisor/index.yml). |
| Registos | Abra [o Log Analytics](../log-query/log-analytics-overview.md) com o [âmbito](../log-query/scope.md) definido para a máquina virtual atual. |
| Monitor de ligação | Monitor de [ligação do Observador de Rede](../../network-watcher/connection-monitor-overview.md) aberto para monitorizar as ligações entre a máquina virtual atual e outras máquinas virtuais. |


## <a name="analyzing-metric-data"></a>Análise de dados métricos
Pode analisar métricas para máquinas virtuais usando métricas exploradores abrindo **métricas** a partir do menu da máquina virtual. Consulte [o Azure Metrics Explorer](../platform/metrics-getting-started.md) para obter detalhes sobre a utilização desta ferramenta. 

Existem três espaços de nome usados por máquinas virtuais para métricas:

| Espaço de Nomes | Description | Requisito |
|:---|:---|:---|
| Anfitrião de Máquinas Virtuais | Métricas de anfitrião recolhidas automaticamente para todas as máquinas virtuais Azure. Lista detalhada de métricas em [Microsoft.Compute/virtualMachines](../platform/metrics-supported.md#microsoftcomputevirtualmachines). | Recolhido automaticamente sem necessidade de configuração. |
| Convidado (clássico) | Conjunto limitado de dados de desempenho do sistema operativo dos hóspedes e da aplicação. Disponível em métricas explorador, mas não em outras funcionalidades do Azure Monitor, como alertas métricos.  | [Extensão de diagnóstico](../agents/diagnostics-extension-overview.md) instalada. Os dados são lidos a partir do armazenamento da Azure.  |
| Hóspede de máquina virtual | Sistema operativo de hóspedes e dados de desempenho da aplicação disponíveis para todas as funcionalidades do Azure Monitor utilizando métricas. | Para o Windows, [a extensão de diagnóstico instalada](../agents/diagnostics-extension-overview.md) com a pia do Monitor Azure ativada. Para o Linux, [o agente Telegraf instalado.](../platform/collect-custom-metrics-linux-telegraf.md) |

![Explorador de métricas no portal Azure](media/monitor-vm-azure/metrics.png)

## <a name="analyzing-log-data"></a>Análise de dados de registo
As máquinas virtuais Azure recolherão os seguintes dados para os Registos do Monitor Azure. 

O Azure Monitor para VMs permite a recolha de um conjunto pré-determinado de contadores de desempenho que são escritos na tabela *InsightsMetrics.* Esta é a mesma tabela utilizada pelo [Azure Monitor para contentores.](../insights/container-insights-overview.md) 

| Origem de dados | Requisitos | Tabelas |
|:---|:---|:---|
| Azure Monitor para VMs | Ative em cada máquina virtual. | InsightsMetrics<br>VMBoundPort<br>VMComputer<br>VMConnection<br>VMProcess<br>Consulte [como consultar os registos do Azure Monitor para VMs](../vm/vminsights-log-search.md) para obter mais detalhes. |
| Registo de atividades | Definição de diagnóstico para a subscrição. | AzureActivity |
| Métricas de anfitrião | Definição de diagnóstico para a máquina virtual. | AzureMetrics |
| Fontes de dados do sistema operativo de hóspedes | Ativar o agente Log Analytics e configurar fontes de dados. | Consulte a documentação de cada fonte de dados. |


> [!NOTE]
> Os dados de desempenho recolhidos pelo agente Log Analytics escrevem para a tabela *Perf,* enquanto o Azure Monitor para VMs irá recolhê-lo na tabela *InsightsMetrics.* Estes são os mesmos dados, mas as tabelas têm uma estrutura diferente. Se tiver consultas existentes baseadas em *Perf,* terá de ser reescrito para utilizar *insightsMetrics*.


## <a name="alerts"></a>Alertas
[Os alertas](../platform/alerts-overview.md) no Azure Monitor notificam-no proativamente quando forem encontradas condições importantes nos seus dados de monitorização e potencialmente lançar uma ação como iniciar uma App Lógica ou chamar um webhook. As regras de alerta definem a lógica utilizada para determinar quando deve ser criado um alerta. O Azure Monitor recolhe os dados utilizados pelas regras de alerta, mas é necessário criar regras para definir as condições de alerta na sua subscrição do Azure.

As seguintes secções descrevem os tipos de regras e recomendações de alerta sobre quando deve utilizar cada uma delas. Esta recomendação baseia-se na funcionalidade e custo do tipo de regra de alerta. Para obter informações sobre os preços dos alertas, consulte [os preços do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).


### <a name="activity-log-alert-rules"></a>Regras de alerta de registo de atividade
[As regras de alerta de registo de atividade disparam](../alerts/alerts-activity-log.md) quando uma entrada corresponde a critérios específicos no registo de atividade. Não têm custos, por isso devem ser a sua primeira escolha se a lógica que você precisa estiver no registo de atividades. 

O recurso-alvo para alertas de registo de atividade pode ser uma máquina virtual específica, todas as máquinas virtuais de um grupo de recursos ou todas as máquinas virtuais numa subscrição.

Por exemplo, crie um alerta se uma máquina virtual crítica for interrompida selecionando a *Máquina Virtual De desligar* o sistema para o nome de sinal.

![Alerta de registo de atividade](media/monitor-vm-azure/activity-log-alert.png)


### <a name="metric-alert-rules"></a>Regras de alerta de métricas
[As regras de alerta métrico disparam](../alerts/alerts-metric.md) quando um valor métrico excede um limiar. Pode definir um valor de limiar específico ou permitir que o Azure Monitor determine dinamicamente um limiar baseado em dados históricos.  Utilize alertas métricos sempre que possível com dados métricos, uma vez que custam menos e são mais responsivos do que as regras de alerta de registo. Também têm um significado imponente que se resolverão quando a métrica cair abaixo do limiar.

O recurso-alvo para alertas de registo de atividade pode ser uma máquina virtual específica ou todas as máquinas virtuais de um grupo de recursos.

Por exemplo, para criar um alerta quando o processador de uma máquina virtual exceder um determinado valor, crie uma regra de alerta métrica utilizando *a Percentagem CPU* como tipo de sinal. Desa ajuste um valor de limiar específico ou permita ao Monitor Azure definir um limiar dinâmico. 

![Alerta métrico](media/monitor-vm-azure/metric-alert.png)

### <a name="log-alerts"></a>Alertas de registo
[As regras de alerta de](../alerts/alerts-log.md) registo disparam quando os resultados de uma consulta de registo programada correspondem a determinados critérios. Os alertas de consulta de registo são os mais caros e menos responsivos das regras de alerta, mas têm acesso aos dados mais diversos e podem executar uma lógica complexa que não pode ser realizada pelas outras regras de alerta. 

O recurso-alvo para uma consulta de log é um espaço de trabalho Log Analytics. Filtrar para computadores específicos na consulta.

Por exemplo, para criar um alerta que verifique se alguma máquina virtual de um determinado grupo de recursos está offline, use a seguinte consulta que devolve um registo para cada computador que falhou um batimento cardíaco nos últimos dez minutos. Utilize um limiar de 1 que dispara se pelo menos um computador tiver um batimento cardíaco perdido.

```kusto
Heartbeat
| where TimeGenerated > ago(10m)
| where ResourceGroup == "my-resource-group"
| summarize max(TimeGenerated) by Computer
```

![Alerta de registo para batimentos cardíacos perdidos](media/monitor-vm-azure/log-alert-01.png)

Para criar um alerta se ocorrer um número excessivo de logons falhados em quaisquer máquinas virtuais do Windows na subscrição, utilize a seguinte consulta que devolve um registo para cada evento de início de sessão falhado na última hora. Utilize um limiar definido para o número de logons falhados que permitirá. 

```kusto
Event
| where TimeGenerated > ago(1hr)
| where EventID == 4625
```

![Alerta de registo para logons falhados](media/monitor-vm-azure/log-alert-02.png)


## <a name="system-center-operations-manager"></a>System Center Operations Manager
O Gestor de Operações do Centro de Sistemas fornece monitorização granular de cargas de trabalho em máquinas virtuais. Consulte o [Guia de Monitorização da Nuvem](/azure/cloud-adoption-framework/manage/monitor/) para comparar as plataformas de monitorização e diferentes estratégias de implementação.

Se tiver um ambiente de Gestor de Operações existente que pretende continuar a utilizar, pode integrá-lo com o Azure Monitor para fornecer funcionalidades adicionais. O agente Log Analytics utilizado pelo Azure Monitor é o mesmo utilizado para o Gestor de Operações para que tenha monitorizado as máquinas virtuais enviar dados para ambos. Ainda é necessário adicionar o agente ao Azure Monitor para VMs e configurar o espaço de trabalho para recolher dados adicionais, conforme especificado acima, mas as máquinas virtuais podem continuar a executar os seus pacotes de gestão existentes num ambiente de Gestor de Operações sem modificação.

As funcionalidades do Azure Monitor que aumentam as funcionalidades existentes do Gestor de Operações incluem:

- Utilize o Log Analytics para analisar interativamente os seus dados de registo e desempenho.
- Utilize alertas de registo para definir condições de alerta em várias máquinas virtuais e utilizando tendências de longo prazo que não são possíveis usando alertas no Gestor de Operações.   

Consulte [o Connect Operations Manager ao Azure Monitor](../agents/om-agents.md) para obter mais informações sobre a ligação do seu grupo de gestão de Gestor de Operações existente ao seu espaço de trabalho Log Analytics.


## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar dados em registos do Azure Monitor utilizando consultas de registo.](../log-query/get-started-queries.md)
* [Saiba mais sobre alertas utilizando métricas e registos no Azure Monitor.](../platform/alerts-overview.md)