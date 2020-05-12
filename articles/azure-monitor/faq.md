---
title: Azure Monitor FAQ [ Azure Monitor FAQ ] Microsoft Docs
description: Respostas a perguntas frequentes sobre o Monitor Azure.
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/11/2020
ms.openlocfilehash: 471ccddd31fd6c9f332bdaa8ea76b7bda25ac191
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83117789"
---
# <a name="azure-monitor-frequently-asked-questions"></a>Monitor Azure frequentemente questionado

Este FaQ da Microsoft é uma lista de perguntas comumente feitas sobre o Monitor Azure.

## <a name="general"></a>Geral

### <a name="what-is-azure-monitor"></a>O que é o Azure Monitor?
O [Azure Monitor](overview.md) é um serviço no Azure que fornece monitorização de desempenho e disponibilidade para aplicações e serviços em Azure, outros ambientes na nuvem, ou no local. O Azure Monitor recolhe dados de várias fontes para uma plataforma de dados comum onde pode ser analisado para tendências e anomalias. As funcionalidades ricas no Monitor Azure ajudam-no a identificar e responder rapidamente a situações críticas que podem afetar a sua aplicação.

### <a name="whats-the-difference-between-azure-monitor-log-analytics-and-application-insights"></a>Qual é a diferença entre o Monitor Azure, o Log Analytics e os Insights de Aplicação?
Em setembro de 2018, a Microsoft combinou o Azure Monitor, o Log Analytics e o Application Insights num único serviço para fornecer uma monitorização final poderosa das suas aplicações e dos componentes em que confiam. As funcionalidades em Log Analytics e Application Insights não mudaram, embora algumas funcionalidades tenham sido renomeadas para o Monitor Azure de forma a refletir melhor o seu novo âmbito. O motor de dados de registo e a linguagem de consulta do Log Analytics são agora referidos como Registos do Monitor De Azure. Consulte as atualizações da [terminologia do Monitor Azure](terminology.md).

### <a name="what-does-azure-monitor-cost"></a>Quanto custa o Azure Monitor?
As funcionalidades do Monitor Azure que são automaticamente ativadas, tais como a recolha de métricas e registos de atividade, são fornecidas sem qualquer custo. Existe um custo associado a outras funcionalidades, tais como consultas de log e alerta. Consulte a página de preços do [Monitor Azure](https://azure.microsoft.com/pricing/details/monitor/) para obter informações detalhadas sobre preços.

### <a name="how-do-i-enable-azure-monitor"></a>Como posso ativar o Monitor Azure?
O Azure Monitor está ativado no momento em que cria uma nova subscrição Azure, e as métricas de registo e plataforma de [atividade](platform/activity-logs-overview.md) são [recolhidas](platform/data-platform-metrics.md) automaticamente. Crie [configurações de diagnóstico](platform/diagnostic-settings.md) para recolher informações mais detalhadas sobre o funcionamento dos seus recursos Azure e adicione soluções de [monitorização](insights/solutions.md) e [insights](insights/insights-overview.md) para fornecer análises adicionais sobre dados recolhidos para determinados serviços. 

### <a name="how-do-i-access-azure-monitor"></a>Como posso aceder ao Monitor Azure?
Aceda a todas as funcionalidades e dados do Monitor Azure do menu **Monitor** no portal Azure. A secção de **Monitorização** do menu para diferentes serviços Azure proporciona acesso às mesmas ferramentas com dados filtrados a um recurso específico. Os dados do Azure Monitor também são acessíveis para uma variedade de cenários usando CLI, PowerShell e uma API REST.

### <a name="is-there-an-on-premises-version-of-azure-monitor"></a>Existe uma versão no local do Monitor Azure?
Não. O Azure Monitor é um serviço de nuvem escalável que processa e armazena grandes quantidades de dados, embora o Azure Monitor possa monitorizar os recursos que estão no local e noutras nuvens.

### <a name="can-azure-monitor-monitor-on-premises-resources"></a>O Monitor Azure pode monitorizar os recursos no local?
Sim, além de recolher dados de monitorização dos recursos do Azure, o Azure Monitor pode recolher dados de máquinas virtuais e aplicações noutras nuvens e no local. Consulte [fontes de monitorização dos dados para](platform/data-sources.md)o Monitor Azure .

### <a name="does-azure-monitor-integrate-with-system-center-operations-manager"></a>O Monitor Azure integra-se com o System Center Operations Manager?
Pode ligar o seu grupo de gestão de gestão de operações do System Center ao Azure Monitor para recolher dados de agentes em Registos do Monitor Do Azure. Isto permite-lhe utilizar consultas de registo e solução para analisar os dados recolhidos junto dos agentes. Também pode configurar os agentes existentes do System Center Operations Manager para enviar dados diretamente para o Monitor Azure. Consulte [o Gestor de Operações de Ligação ao Monitor Azure](platform/om-agents.md).

### <a name="what-ip-addresses-does-azure-monitor-use"></a>Que endereços IP usa o Azure Monitor?
Consulte [os endereços IP utilizados pela Application Insights e log Analytics](app/ip-addresses.md) para obter uma listagem dos endereços IP e portas necessários para que os agentes e outros recursos externos acedam ao Monitor Azure. 

## <a name="monitoring-data"></a>Monitorizar dados

### <a name="where-does-azure-monitor-get-its-data"></a>Onde é que o Azure Monitor obtém os seus dados?
O Azure Monitor recolhe dados de várias fontes, incluindo registos e métricas da plataforma Estatal e recursos, aplicações personalizadas e agentes que executam máquinas virtuais. Outros serviços, como o Azure Security Center e o Network Watcher, recolhem dados num espaço de trabalho do Log Analytics para que possam ser analisados com dados do Azure Monitor. Também pode enviar dados personalizados para o Monitor Azure utilizando a API REST para registos ou métricas. Consulte [fontes de monitorização dos dados para](platform/data-sources.md)o Monitor Azure .

### <a name="what-data-is-collected-by-azure-monitor"></a>Que dados são recolhidos pelo Azure Monitor? 
O Monitor Azure recolhe dados de várias fontes em [registos](platform/data-platform-logs.md) ou [métricas.](platform/data-platform-metrics.md) Cada tipo de dados tem as suas vantagens relativas, e cada um suporta um conjunto particular de funcionalidades no Monitor Azure. Existe uma única base de dados de métricas para cada subscrição do Azure, enquanto pode criar vários espaços de trabalho do Log Analytics para recolher registos dependendo dos seus requisitos. Ver plataforma de [dados Do Monitor Azure](platform/data-platform.md).

### <a name="is-there-a-maximum-amount-of-data-that-i-can-collect-in-azure-monitor"></a>Há uma quantidade máxima de dados que posso recolher no Monitor Azure?
Não existe limite para a quantidade de dados métricos que pode recolher, mas estes dados são armazenados por um período máximo de 93 dias. Ver [Retenção de Métricas](platform/data-platform-metrics.md#retention-of-metrics). Não existe limite para a quantidade de dados de registo que pode recolher, mas pode ser afetado pelo nível de preços que escolhe para o espaço de trabalho do Log Analytics. Consulte [os detalhes dos preços.](https://azure.microsoft.com/pricing/details/monitor/)

### <a name="how-do-i-access-data-collected-by-azure-monitor"></a>Como posso aceder aos dados recolhidos pelo Azure Monitor?
Insights e soluções proporcionam uma experiência personalizada para trabalhar com dados armazenados no Monitor Azure. Pode trabalhar diretamente com dados de registo utilizando uma consulta de registo escrita na Linguagem de Consulta Kusto (KQL). No portal Azure, pode escrever e executar consultas e analisar interativamente dados usando o Log Analytics. Analise as métricas no portal Azure com o Explorador de Métricas. Consulte os dados de [registo de análise no Monitor Azure](log-query/log-query-overview.md) e começa com o [Azure Metrics Explorer](platform/metrics-getting-started.md).

## <a name="solutions-and-insights"></a>Soluções e insights

### <a name="what-is-an-insight-in-azure-monitor"></a>O que é uma visão no Monitor Azure?
Insights fornecem uma experiência de monitorização personalizada para determinados serviços Azure. Utilizam as mesmas métricas e registos que outras funcionalidades no Monitor Azure, mas podem recolher dados adicionais e proporcionar uma experiência única no portal Azure. Consulte [insights no Monitor Azure](insights/insights-overview.md).

Para ver informações no portal Azure, consulte a secção **Insights** do menu **Monitor** ou a secção **de Monitorização** do menu do serviço.

### <a name="what-is-a-solution-in-azure-monitor"></a>Qual é a solução no Monitor Azure?
As soluções de monitorização são conjuntos de lógica embalados para monitorizar uma determinada aplicação ou serviço com base nas funcionalidades do Monitor Azure. Recolhem dados de registo no Monitor Azure e fornecem consultas de registo e pontos de vista para a sua análise utilizando uma experiência comum no portal Azure. Consulte [soluções de monitorização no Monitor Azure](insights/solutions.md).

Para ver soluções no portal Azure, clique em **Mais** informações na secção **Insights** do menu **Monitor.** Clique em **Adicionar** para adicionar soluções adicionais ao espaço de trabalho.

## <a name="logs"></a>Registos

### <a name="whats-the-difference-between-azure-monitor-logs-and-azure-data-explorer"></a>Qual é a diferença entre os Registos do Monitor Azure e o Azure Data Explorer?
O Azure Data Explorer é um serviço de exploração de dados rápido e altamente dimensionável para dados telemétricos e de registo. O Azure Monitor Logs é construído em cima do Azure Data Explorer e utiliza a mesma linguagem de consulta kusto (KQL) com algumas pequenas diferenças. Consulte [as diferenças linguísticas](log-query/data-explorer-difference.md)de consulta de registo do Azure Monitor .

### <a name="how-do-i-retrieve-log-data"></a>Como posso recuperar dados de registo?
Todos os dados são recuperados a partir de um espaço de trabalho de Log Analytics usando uma consulta de log escrita usando a linguagem de consulta kusto (KQL). Pode escrever as suas próprias consultas ou utilizar soluções e insights que incluam consultas de registo para uma determinada aplicação ou serviço. Consulte [a visão geral das consultas de registo no Monitor Azure](log-query/log-query-overview.md).

### <a name="what-is-a-log-analytics-workspace"></a>O que é uma área de trabalho do Log Analytics?
Todos os dados de registo recolhidos pelo Azure Monitor são armazenados num espaço de trabalho do Log Analytics. Um espaço de trabalho é essencialmente um recipiente onde os dados de registo são recolhidos de uma variedade de fontes. Pode ter um único espaço de trabalho de Log Analytics para todos os seus dados de monitorização ou pode ter requisitos para vários espaços de trabalho. Consulte a conceção da implementação dos registos do [Monitor Azure](platform/design-logs-deployment.md).

### <a name="can-you-move-an-existing-log-analytics-workspace-to-another-azure-subscription"></a>Pode mover um espaço de trabalho existente no Log Analytics para outra subscrição do Azure?
Pode mover um espaço de trabalho entre grupos de recursos ou subscrições, mas não para uma região diferente. Consulte Mover um espaço de [trabalho de Log Analytics para diferentes subscrições ou grupo de recursos](platform/move-workspace.md).

### <a name="why-cant-i-see-query-explorer-and-save-buttons-in-log-analytics"></a>Por que não posso ver o Query Explorer e guardar botões no Log Analytics?

**Consulta Explorer**, **Save** and New **alert rule** buttons não estão disponíveis quando o âmbito de [consulta](log-query/scope.md) é definido para um recurso específico. Para criar alertas, guardar ou carregar uma consulta, o Log Analytics deve ser remeto para um espaço de trabalho. Para abrir o Log Analytics no contexto do espaço de trabalho, selecione **Registos** do menu **Do Monitor Azure.** O último espaço de trabalho usado é selecionado, mas pode selecionar qualquer outro espaço de trabalho. Consulte o âmbito e o intervalo de tempo de consulta de [registo no Azure Monitor Log Analytics](log-query/scope.md)

### <a name="why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-when-opening-log-analytics-from-a-vm"></a>Porque é que estou a receber o erro: "Registar o fornecedor de recursos 'Microsoft.Insights' para esta subscrição para permitir esta consulta" ao abrir o Log Analytics a partir de um VM? 
Muitos fornecedores de recursos estão automaticamente registados, mas pode ser necessário registar manualmente alguns fornecedores de recursos. A margem de inscrição é sempre a subscrição. Veja [Fornecedores e tipos de recursos](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) para obter mais informações.

### <a name="why-am-i-am-getting-no-access-error-message-when-opening-log-analytics-from-a-vm"></a>Porque é que não estou a receber nenhuma mensagem de erro de acesso ao abrir o Log Analytics a partir de um VM? 
Para ver os Registos VM, é necessário ter permissão de leitura para os espaços de trabalho que armazenam os registos VM. Nestes casos, o seu administrador deve conceder-lhe permissões em Azure.

## <a name="alerts"></a>Alertas

### <a name="what-is-an-alert-in-azure-monitor"></a>O que é um alerta no Monitor Azure?
Os alertas notificam-no proativamente quando forem encontradas condições importantes nos seus dados de monitorização. Permitem identificar e resolver problemas antes que os utilizadores do seu sistema os notem. Existem vários tipos de alertas:

- Métrica - O valor métrico excede um limiar.
- Consulta de registo - Resultados de uma consulta de log match critérios definidos.
- Registo de atividades - Evento de registo de atividade corresponde a critérios definidos.
- Teste web - Resultados do teste de disponibilidade correspondem a critérios definidos.


Ver [síntese de alertas no Microsoft Azure](platform/alerts-overview.md).


### <a name="what-is-an-action-group"></a>O que é um grupo de ação?
Um grupo de ação é uma coleção de notificações e ações que podem ser desencadeadas por um alerta. Vários alertas podem usar um único grupo de ação que lhe permite alavancar conjuntos comuns de notificações e ações. Consulte criar e gerir grupos de [ação no portal Azure.](platform/action-groups.md)


### <a name="what-is-an-action-rule"></a>O que é uma regra de ação?
Uma regra de ação permite modificar o comportamento de um conjunto de alertas que correspondem a determinados critérios. Isto permite-lhe executar tais requisitos como desativar as ações de alerta durante uma janela de manutenção. Também pode aplicar um grupo de ação a um conjunto de alertas em vez de os aplicar diretamente às regras de alerta. Ver Regras de [Ação](platform/alerts-action-rules.md).

## <a name="agents"></a>Agentes

### <a name="does-azure-monitor-require-an-agent"></a>O Monitor Azure requer um agente?
É necessário apenas recolher dados do sistema operativo e cargas de trabalho em máquinas virtuais. As máquinas virtuais podem estar localizadas em Azure, outro ambiente em nuvem, ou no local. Consulte [a visão geral dos agentes do Monitor Azure](platform/agents-overview.md).


### <a name="whats-the-difference-between-the-azure-monitor-agents"></a>Qual é a diferença entre os agentes do Monitor Azure?
A extensão do Azure Diagnostic é para máquinas virtuais Azure e recolhe dados para as Métricas do Monitor Azure, armazenamento Azure e Hubs de Eventos Azure. O agente Log Analytics é para máquinas virtuais em Azure, outro ambiente em nuvem, ou no local e recolhe dados para registos do Monitor Azure. O agente Dependency requer o agente Log Analytics e recolhido detalhes e dependências do processo. Consulte [a visão geral dos agentes do Monitor Azure](platform/agents-overview.md).


### <a name="does-my-agent-traffic-use-my-expressroute-connection"></a>O tráfego do meu agente usa a minha ligação ExpressRoute?
O tráfego para o Azure Monitor utiliza o circuito ExpressRoute da Microsoft. Consulte a [documentação expressRoute](../expressroute/expressroute-faqs.md#supported-services) para obter uma descrição dos diferentes tipos de tráfego ExpressRoute. 

### <a name="how-can-i-confirm-that-the-log-analytics-agent-is-able-to-communicate-with-azure-monitor"></a>Como posso confirmar que o agente Delog Analytics é capaz de comunicar com o Monitor Azure?
A partir do Painel de Controlo no computador do agente, selecione **Definições de & de Segurança**, Agente de **Monitorização** da Microsoft . Sob o separador **Azure Log Analytics (OMS),** um ícone de marca de verificação verde confirma que o agente é capaz de comunicar com o Monitor Azure. Um ícone de aviso amarelo significa que o agente está com problemas. Uma razão comum é que o serviço **do Microsoft Monitoring Agent** parou. Utilize o gestor de controlo de serviço para reiniciar o serviço.

### <a name="how-do-i-stop-the-log-analytics-agent-from-communicating-with-azure-monitor"></a>Como posso impedir o agente de Log Analytics de comunicar com o Monitor Azure?
Para os agentes ligados diretamente ao Log Analytics, abra o Painel de Controlo e selecione **Definições de & de Segurança,** **Agente de Monitorização**da Microsoft . Sob o separador **Azure Log Analytics (OMS),** remova todos os espaços de trabalho listados. No System Center Operations Manager, remova o computador da lista de computadores geridos pelo Log Analytics. O Diretor de Operações atualiza a configuração do agente para deixar de reportar ao Log Analytics. 

### <a name="how-much-data-is-sent-per-agent"></a>Quantos dados são enviados por agente?
A quantidade de dados enviados por agente depende de:

* As soluções que permitiu
* O número de registos e contadores de desempenho a ser recolhidos
* O volume de dados nos registos

Consulte [gerir a utilização e os custos com registos](platform/manage-cost-storage.md) do Monitor Azure para obter mais detalhes.

Para computadores capazes de executar o agente WireData, utilize a seguinte consulta para ver quantos dados estão a ser enviados:

```Kusto
WireData
| where ProcessName == "C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe" 
| where Direction == "Outbound" 
| summarize sum(TotalBytes) by Computer 
```

### <a name="how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-azure-monitor"></a>Quanto largura de banda de rede é utilizada pelo Microsoft Management Agent (MMA) ao enviar dados para o Azure Monitor?
A largura de banda é uma função na quantidade de dados enviados. Os dados são comprimidos à medida que são enviados pela rede.


### <a name="how-can-i-be-notified-when-data-collection-from-the-log-analytics-agent-stops"></a>Como posso ser notificado quando a recolha de dados do agente Log Analytics para?

Utilize os passos descritos para [criar um novo alerta](platform/alerts-metric.md) de registo para ser notificado quando a recolha de dados parar. Utilize as seguintes definições para a regra de alerta:

- **Defina condição de alerta**: Especifique o seu espaço de trabalho de Log Analytics como alvo de recursos.
- **Critérios de alerta** 
   - **Nome do sinal**: *Pesquisa de registo personalizado*
   - **Consulta de pesquisa:**`Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`
   - **Lógica de alerta**: **Com base no** número de *resultados*, **Condição** Maior *do que*, **Valor limiar** *0*
   - **Avaliado com base em:** **Período (em minutos)** *30,* Frequência **(em minutos)** *10*
- **Definir detalhes do alerta** 
   - **Nome**: *Recolha de dados parada*
   - **Gravidade**: *Aviso*

Especifique um Grupo de [Ação](platform/action-groups.md) existente ou novo para que, quando o alerta de registo corresponda aos critérios, seja notificado se tiver um batimento cardíaco em falta por mais de 15 minutos.


### <a name="what-are-the-firewall-requirements-for-azure-monitor-agents"></a>Quais são os requisitos de firewall para os agentes do Monitor Azure?
Consulte [os requisitos](platform/log-analytics-agent.md#network-requirements)de firewall da Rede para obter detalhes sobre os requisitos de firewall.


## <a name="visualizations"></a>Visualizações

### <a name="why-cant-i-see-view-designer"></a>Por que não posso ver o Designer de Vistas?

O View Designer só está disponível para utilizadores atribuídos com permissões do Colaborador ou superiores no espaço de trabalho do Log Analytics.

## <a name="application-insights"></a>Application Insights

### <a name="configuration-problems"></a>Problemas de configuração
*Estou a ter dificuldades em preparar o meu.*

* [Aplicação .NET](app/asp-net-troubleshoot-no-data.md)
* [Monitorização de uma aplicação já em execução](app/monitor-performance-live-website-now.md#troubleshoot)
* [Diagnósticos azure](platform/diagnostics-extension-to-application-insights.md)
* [Aplicação Web Java](app/java-troubleshoot.md)

*Não recebo dados do meu servidor:*

* [Definir exceções à firewall](app/ip-addresses.md)
* [Configurar um servidor ASP.NET](app/monitor-performance-live-website-now.md)
* [Configurar um servidor Java](app/java-agent.md)

*Quantas Informações de Aplicação devo implementar?:*

* [Como conceber a sua implementação de Insights de Aplicação: Um contra muitos recursos de Insights de Aplicação?](app/separate-resources.md)

### <a name="can-i-use-application-insights-with-"></a>Posso usar os Insights de Aplicação com...?

* [Aplicações web em um servidor IIS em conjunto de escala de máquina virtual Azure VM ou Azure](app/azure-vm-vmss-apps.md)
* [Aplicativos web em um servidor IIS - no local ou em um VM](app/asp-net.md)
* [Aplicações Web Java](app/java-get-started.md)
* [Aplicações Node.js](app/nodejs.md)
* [Aplicativos web em Azure](app/azure-web-apps.md)
* [Serviços de Nuvem em Azure](app/cloudservices.md)
* [Servidores de aplicativos em execução em Docker](app/docker.md)
* [Aplicativos web de página única](app/javascript.md)
* [SharePoint](app/sharepoint.md)
* [Aplicativo de desktop do Windows](app/windows-desktop.md)
* [Outras plataformas](app/platforms.md)

### <a name="is-it-free"></a>É de graça?

Sim, para uso experimental. No plano de preços básicos, a sua candidatura pode enviar gratuitamente um determinado subsídio de dados todos os meses. O subsídio gratuito é grande o suficiente para cobrir o desenvolvimento, e publicar uma app para um pequeno número de utilizadores. Pode definir uma tampa para evitar que mais do que uma quantidade especificada de dados sejam processados.

Volumes maiores de telemetria são cobrados pelo Gb. Fornecemos algumas dicas sobre como [limitar as suas taxas.](app/pricing.md)

O plano da Enterprise incorre numa carga por cada dia que cada nó de servidor web envia telemetria. É adequado se pretender utilizar a Exportação Contínua em larga escala.

[Leia o plano de preços.](https://azure.microsoft.com/pricing/details/application-insights/)

### <a name="how-much-does-it-cost"></a>Quanto custa?

* Abra a **página de Utilização e custos estimados** num recurso Deinsights de Aplicação. Há um gráfico de uso recente. Pode definir uma tampa de volume de dados, se quiser.
* Abra a lâmina de [faturação Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade/Overview) para ver as suas contas em todos os recursos.

### <a name="what-does-application-insights-modify-in-my-project"></a><a name="q14"></a>O que modifica os Application Insights no meu projeto?
Os detalhes dependem do tipo de projeto. Para uma aplicação web:

* Adiciona estes ficheiros ao seu projeto:
  * ApplicationInsights.config
  * ai.js
* Instala estes pacotes NuGet:
  * *Application Insights API* - a API central
  * *Application Insights API para Aplicações Web* - usado para enviar telemetria do servidor
  * *Application Insights API para Aplicações JavaScript* - usado para enviar telemetria do cliente
* Os pacotes incluem estes conjuntos:
  * Microsoft.ApplicationInsights
  * Microsoft.ApplicationInsights.Platform
* Insere itens em:
  * Web.config
  * pacotes.config
* (Apenas novos projetos - se [adicionar informações de aplicação a um projeto existente][start], tem de o fazer manualmente.) Insere os snippets no código do cliente e do servidor para inicializá-los com o ID de recurso de Recursos De Insights de Aplicação. Por exemplo, numa aplicação MVC, o código é inserido na página principal Views/Shared/ \_ Layout.cshtml

### <a name="how-do-i-upgrade-from-older-sdk-versions"></a>Como faço upgrade a partir de versões SDK mais antigas?
Consulte as notas de [lançamento](app/release-notes.md) do SDK adequadas ao seu tipo de aplicação.

### <a name="how-can-i-change-which-azure-resource-my-project-sends-data-to"></a><a name="update"></a>Como posso alterar o recurso azure para o qual o meu projeto envia dados?
No Solution Explorer, clique à direita `ApplicationInsights.config` e escolha Insights de **Aplicação de Atualização**. Pode enviar os dados para um recurso existente ou novo no Azure. O assistente de atualização altera a chave de instrumentação em ApplicationInsights.config, que determina onde o Servidor SDK envia os seus dados. A menos que desmarque "Update all", também mudará a tecla onde aparece nas suas páginas web.

### <a name="can-i-use-providersmicrosoftinsights-componentsapiversions0-in-my-azure-resource-manager-deployments"></a>Posso usar nas minhas implementações do Gestor de `providers('Microsoft.Insights', 'components').apiVersions[0]` Recursos Azure?

Não recomendamos a utilização deste método de povoação da versão API. A versão mais recente pode representar lançamentos de pré-visualização que podem conter alterações de rutura. Mesmo com lançamentos não pré-visualizados mais recentes, as versões API nem sempre são compatíveis com os modelos existentes, ou em alguns casos a versão API pode não estar disponível para todas as subscrições.

### <a name="what-is-status-monitor"></a>O que é o Monitor de Estado?

Uma aplicação de desktop que pode utilizar no seu servidor web IIS para ajudar a configurar os Insights de Aplicação em aplicações web. Não recolhe telemetria: pode detê-la quando não estiver a configurar uma aplicação. 

[Saiba mais](app/monitor-performance-live-website-now.md#questions).

### <a name="what-telemetry-is-collected-by-application-insights"></a>Que telemetria é recolhida pela Application Insights?

A partir de aplicações web do servidor:

* Pedidos HTTP
* [Dependências.](app/asp-net-dependencies.md) Chamadas para: Bases de dados SQL; HTTP chama serviços externos; Azure Cosmos DB, mesa, armazenamento de bolhas e fila. 
* [Exceções](app/asp-net-exceptions.md) e vestígios de pilhas.
* [Contadores](app/performance-counters.md) de Desempenho - Se utilizar o [Status Monitor,](app/monitor-performance-live-website-now.md) [a monitorização azure para serviços de aplicações,](app/azure-web-apps.md)a [monitorização do Azure para VM ou o conjunto](app/azure-vm-vmss-apps.md)de escala de máquinavirtual, ou o escritor [colecionado Application Insights.](app/java-collectd.md)
* [Eventos e métricas personalizados](app/api-custom-events-metrics.md) que codifica.
* [Trace os registos](app/asp-net-trace-logs.md) se configurar o colecionador apropriado.

A partir de páginas web do [cliente:](app/javascript.md)

* [Contagens de visualizações de página](app/usage-overview.md)
* [Chamadas do AJAX](app/asp-net-dependencies.md) Pedidos feitos a partir de um guião em execução.
* Dados de carga de visualização de página
* Contagens de utilizador e sessão
* [IDs de utilizador autenticados](app/api-custom-events-metrics.md#authenticated-users)

De outras fontes, se as configurar:

* [Diagnósticos azure](platform/diagnostics-extension-to-application-insights.md)
* [Importação para Analítica](platform/data-collector-api.md)
* [Log Analytics](platform/data-collector-api.md)
* [Logstash](platform/data-collector-api.md)

### <a name="can-i-filter-out-or-modify-some-telemetry"></a>Posso filtrar ou modificar alguma telemetria?

Sim, no servidor pode escrever:

* Processador de telemetria para filtrar ou adicionar propriedades a artigos de telemetria selecionados antes de serem enviados da sua aplicação.
* Ininicializador de Telemetria para adicionar propriedades a todos os itens de telemetria.

Saiba mais sobre [ASP.NET](app/api-filtering-sampling.md) ou [Java.](app/java-filter-telemetry.md)

### <a name="how-are-city-countryregion-and-other-geo-location-data-calculated"></a>Como são calculados os dados da cidade, do país/região e de outros dados de localização geos?

Procuramos o endereço IP (IPv4 ou IPv6) do cliente web usando [geoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/).

* Telemetria do navegador: Recolhemos o endereço IP do remetente.
* Telemetria do servidor: O módulo Application Insights recolhe o endereço IP do cliente. Não é recolhido se `X-Forwarded-For` estiver definido.
* Para saber mais sobre como os dados de endereço IP e geolocalização são recolhidos em Insights de Aplicação consulte este [artigo](https://docs.microsoft.com/azure/azure-monitor/app/ip-collection).


Pode configurar o `ClientIpHeaderTelemetryInitializer` endereço IP para tirar o endereço IP de um cabeçalho diferente. Em alguns sistemas, por exemplo, é movido por um proxy, um equilibrista de carga ou CDN para `X-Originating-IP` . [Saiba mais](https://apmtips.com/blog/2016/07/05/client-ip-address/).

Pode [utilizar o Power BI](app/export-power-bi.md ) para exibir a sua telemetria de pedidos num mapa.


### <a name="how-long-is-data-retained-in-the-portal-is-it-secure"></a><a name="data"></a>Quanto tempo os dados são retidos no portal? É seguro?
Dê uma olhada na Retenção de [Dados e Privacidade.][data]

### <a name="what-happens-to-application-insights-telemetry-when-a-server-or-device-loses-connection-with-azure"></a>O que acontece com a telemetria do Application Insight quando um servidor ou dispositivo perde a ligação com o Azure?

Todos os nossos SDKs, incluindo o Web SDK, inclui "transporte fiável" ou "transporte robusto". Quando o servidor ou dispositivo perde a ligação com o Azure, a telemetria é [armazenada localmente no sistema](https://docs.microsoft.com/azure/azure-monitor/app/data-retention-privacy#does-the-sdk-create-temporary-local-storage) de ficheiros (Server SDKs) ou no ARMAZENAMENTO de Sessão HTML5 (Web SDK). O SDK irá periodicamente voltar a tentar enviar esta telemetria até que o nosso serviço de ingestão a considere "velha" (48 horas para registos, 30 minutos para métricas). A telemetria velha será largada. Em alguns casos, como quando o armazenamento local está cheio, não haverá nova tentativa.


### <a name="could-personal-data-be-sent-in-the-telemetry"></a>Os dados pessoais podem ser enviados na telemetria?

Isto é possível se o seu código enviar esses dados. Também pode acontecer se variáveis em vestígios de pilhas incluirem dados pessoais. A sua equipa de desenvolvimento deve realizar avaliações de risco para garantir que os dados pessoais são corretamente tratados. [Saiba mais sobre a retenção de dados e privacidade.](app/data-retention-privacy.md)

**Todos os** octetos do endereço web do cliente são sempre definidos para 0 após os atributos de localização geografia serem procurados.

### <a name="my-instrumentation-key-is-visible-in-my-web-page-source"></a>A minha chave de instrumentação é visível na minha fonte de página web. 

* Trata-se de uma prática comum na monitorização de soluções.
* Não pode ser usado para roubar os seus dados.
* Pode ser usado para distorcer os seus dados ou desencadear alertas.
* Não ouvimos dizer que qualquer cliente teve tais problemas.

Pode:

* Utilize duas teclas de instrumentação separadas (recursos separados de Insights de Aplicação), para dados de clientes e servidores. Ou
* Escreva um representante que corre no seu servidor e peça ao cliente web que envie dados através desse representante.

### <a name="how-do-i-see-post-data-in-diagnostic-search"></a><a name="post"></a>Como vejo os dados post na pesquisa de Diagnóstico?
Não registamos os dados post a automaticamente, mas pode utilizar uma chamada TrackTrace: coloque os dados no parâmetro da mensagem. Isto tem um limite de tamanho mais longo do que os limites nas propriedades das cordas, embora não possa filtrar nele.

### <a name="should-i-use-single-or-multiple-application-insights-resources"></a>Devo usar recursos únicos ou múltiplos de Insights de Aplicação?

Utilize um único recurso para todos os componentes ou funções num único sistema de negócio. Utilize recursos separados para versões de desenvolvimento, teste e lançamento, e para aplicações independentes.

* [Veja a discussão aqui](app/separate-resources.md)
* [Exemplo - serviço na nuvem com funções de trabalhador e web](app/cloudservices.md)

### <a name="how-do-i-dynamically-change-the-instrumentation-key"></a>Como posso alterar dinamicamente a chave de instrumentação?

* [Discussão aqui](app/separate-resources.md)
* [Exemplo - serviço na nuvem com funções de trabalhador e web](app/cloudservices.md)

### <a name="what-are-the-user-and-session-counts"></a>O que conta o Utilizador e a Sessão?

* O JavaScript SDK define um cookie de utilizador no cliente web, para identificar os utilizadores retornados, e um cookie de sessão para atividades de grupo.
* Se não houver um script do lado do cliente, pode [definir cookies no servidor](https://apmtips.com/blog/2016/07/09/tracking-users-in-api-apps/).
* Se um utilizador real utilizar o seu site em diferentes navegadores, ou utilizar navegação inprivada/incógnita, ou máquinas diferentes, então serão contados mais de uma vez.
* Para identificar um utilizador sessão entre máquinas e navegadores, adicione uma chamada para [definirAuthenticatedUserContext()](app/api-custom-events-metrics.md#authenticated-users).

### <a name="have-i-enabled-everything-in-application-insights"></a><a name="q17"></a>Já ativei tudo em Insights de Aplicação?
| O que deve ver | Como obtê-lo | Por que quer |
| --- | --- | --- |
| Gráficos de disponibilidade |[Testes web](app/monitor-web-app-availability.md) |Saiba que a sua aplicação web está em cima |
| Perf app do servidor: tempos de resposta, ... |[Adicione insights de aplicação ao seu projeto](app/asp-net.md) ou [instale o Monitor de Estado](app/monitor-performance-live-website-now.md) da IA no servidor (ou escreva o seu próprio código para rastrear [dependências)](app/api-custom-events-metrics.md#trackdependency) |Detetar problemas perf |
| Telemetria de dependência |[Instale o Monitor de Estado da IA no servidor](app/monitor-performance-live-website-now.md) |Diagnosticar problemas com bases de dados ou outros componentes externos |
| Obtenha vestígios de pilhas de exceções |[Insira as chamadas TrackException no seu código](app/asp-net-exceptions.md) (mas algumas são reportadas automaticamente) |Detetar e diagnosticar exceções |
| Pesquisar vestígios de registo |[Adicione um adaptador de exploração madeireira](app/asp-net-trace-logs.md) |Diagnosticar exceções, questões perf |
| Básicos de utilização do cliente: visualizações de página, sessões, ... |[Inicializador JavaScript nas páginas web](app/javascript.md) |Análise de utilização |
| Métricas personalizadas do cliente |[Rastreio de chamadas em páginas web](app/api-custom-events-metrics.md) |Melhorar a experiência do utilizador |
| Métricas personalizadas do servidor |[Rastreio de chamadas no servidor](app/api-custom-events-metrics.md) |Business intelligence |

### <a name="why-are-the-counts-in-search-and-metrics-charts-unequal"></a>Por que as contagens nos gráficos de pesquisa e métricas são desiguais?

[A amostragem](app/sampling.md) reduz o número de artigos de telemetria (pedidos, eventos personalizados, e assim por diante) que são realmente enviados da sua app para o portal. Em Search, vê-se o número de itens realmente recebidos. Em gráficos métricos que exibem uma contagem de eventos, você vê o número de eventos originais que ocorreram. 

Cada item que é transmitido transporta uma `itemCount` propriedade que mostra quantos eventos originais que o item representa. Para observar a amostragem em funcionamento, pode executar esta consulta no Analytics:

```
    requests | summarize original_events = sum(itemCount), transmitted_events = count()
```


### <a name="automation"></a>Automatização

#### <a name="configuring-application-insights"></a>Configurar insights de aplicação

Pode [escrever scripts PowerShell](app/powershell.md) utilizando o Monitor de Recursos Azure para:

* Criar e atualizar os recursos da Application Insights.
* Desestabeleça o plano de preços.
* Pegue a chave de instrumentação.
* Adicione um alerta métrico.
* Adicione um teste de disponibilidade.

Não é possível configurar um relatório do Metric Explorer ou configurar uma exportação contínua.

#### <a name="querying-the-telemetry"></a>Consultando a telemetria

Use a [API REST](https://dev.applicationinsights.io/) para executar consultas [de Análise.](app/analytics.md)

### <a name="how-can-i-set-an-alert-on-an-event"></a>Como posso alertar um evento?

Os alertas azure são apenas sobre métricas. Crie uma métrica personalizada que cruze um limiar de valor sempre que o seu evento ocorra. Em seguida, coloque um alerta na métrica. Receberá uma notificação sempre que a métrica cruze o limiar em qualquer direção; não receberá uma notificação até à primeira travessia, independentemente de o valor inicial ser elevado ou baixo; há sempre uma latência de alguns minutos.

### <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Existem encargos de transferência de dados entre uma aplicação web Azure e Insights de Aplicação?

* Se a sua aplicação web Azure estiver hospedada num centro de dados onde existe um ponto final de recolha de Insights de aplicação, não há custo. 
* Se não houver um ponto final de recolha no seu centro de dados de hospedaria, então a telemetria da sua aplicação incorrerá em [encargos de saída do Azure](https://azure.microsoft.com/pricing/details/bandwidth/).

Isto não depende de onde o seu recurso Application Insights está hospedado. Só depende da distribuição dos nossos pontos finais.

### <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>Posso enviar telemetria para o portal Deinsights de Aplicações?

Recomendamos que utilize os nossos SDKs e utilize o [SDK API](app/api-custom-events-metrics.md). Existem variantes do SDK para [várias plataformas.](app/platforms.md) Estes SDKs lidam com tampão, compressão, estrangulamento, tentativas, e assim por diante. No entanto, o esquema de [ingestão](https://github.com/microsoft/ApplicationInsights-dotnet/tree/master/BASE/Schema/PublicSchema) e o [protocolo de ponto final](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md) são públicos.

### <a name="can-i-monitor-an-intranet-web-server"></a>Posso monitorizar um servidor web intranet?

Sim, mas terá de permitir o tráfego dos nossos serviços através de exceções de firewall ou redirecionamentos por procuração.
- QuickPulse`https://rt.services.visualstudio.com:443` 
- ApplicationIdProvider`https://dc.services.visualstudio.com:443` 
- TelemetriaChannel`https://dc.services.visualstudio.com:443` 


Reveja [aqui](app/ip-addresses.md)a nossa lista completa de serviços e endereços IP.

#### <a name="firewall-exception"></a>Exceção da firewall

Permita que o seu servidor web envie telemetria para os nossos pontos finais. 

#### <a name="gateway-redirect"></a>Redirecionamento gateway

Encaminhe o tráfego do seu servidor para um portal na sua intranet, sobrepor pontos finais na sua configuração. Se estas propriedades "Endpoint" não estiverem presentes no seu config, estas classes usarão os valores predefinidos abaixo indicados no exemplo ApplicationInsights.config. 

O seu portal deve direcionar o tráfego para o endereço base do nosso ponto final. Na sua configuração, substitua os valores predefinidos por `http://<your.gateway.address>/<relative path>` .


##### <a name="example-applicationinsightsconfig-with-default-endpoints"></a>Exemplo ApplicationInsights.config com pontos finais predefinidos:
```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>https://rt.services.visualstudio.com/QuickPulseService.svc</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>https://dc.services.visualstudio.com/v2/track</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

> [!NOTE]
> ApplicationIdProvider está disponível a partir de v2.6.0.



#### <a name="proxy-passthrough"></a>Passagem por procuração

A passagem por procuração pode ser conseguida configurando um nível de máquina ou um proxy nível de aplicação.
Para mais informações consulte o artigo da Dotnet sobre [DefaultProxy](https://docs.microsoft.com/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings).
 
 Exemplo Web.config:
 ```xml
<system.net>
    <defaultProxy>
      <proxy proxyaddress="http://xx.xx.xx.xx:yyyy" bypassonlocal="true"/>
    </defaultProxy>
</system.net>
```
 

### <a name="can-i-run-availability-web-tests-on-an-intranet-server"></a>Posso executar testes web de disponibilidade num servidor intranet?

Os [nossos testes na web](app/monitor-web-app-availability.md) funcionam em pontos de presença que são distribuídos por todo o mundo. Há duas soluções:

* Firewall door - Permita pedidos ao seu servidor a partir da [longa e mutável lista de agentes de teste web](app/ip-addresses.md).
* Escreva o seu próprio código para enviar pedidos periódicos para o seu servidor a partir de dentro da sua intranet. Pode sondar testes web do Estúdio Visual para este fim. O teste pode enviar os resultados para Insights de Aplicação utilizando a API trackAvailability()

### <a name="how-long-does-it-take-for-telemetry-to-be-collected"></a>Quanto tempo demora a recolher a telemetria?

A maioria dos dados da Application Insights tem uma latência inferior a 5 minutos. Alguns dados podem demorar mais tempo; ficheiros de registo tipicamente maiores. Para mais informações, consulte o [Application Insights SLA](https://azure.microsoft.com/support/legal/sla/application-insights/v1_2/).



<!--Link references-->

[data]: app/data-retention-privacy.md
[platforms]: app/platforms.md
[start]: app/app-insights-overview.md
[windows]: app/app-insights-windows-get-started.md


## <a name="azure-monitor-for-containers"></a>Azure Monitor para contentores

Este FaQ da Microsoft é uma lista de perguntas comumente feitas sobre o Monitor Azure para contentores. Se tiver alguma dúvida adicional sobre a solução, vá ao fórum de [discussão](https://feedback.azure.com/forums/34192--general-feedback) e publique as suas perguntas. Quando uma pergunta é frequentemente feita, adicionamo-la a este artigo para que possa ser encontrado de forma rápida e fácil.

### <a name="what-does-other-processes-represent-under-the-node-view"></a>O que representam *outros processos* sob a visão do Nó?

**Outros processos** destinam-se a ajudá-lo a entender claramente a causa principal do uso de recursos elevados no seu nó. Isto permite-lhe distinguir o uso entre processos contentorizados vs processos não contentorizados.

O que são estes **Outros Processos?** 

Estes são processos não contentorizados que funcionam no seu nó.  

Como calculamos isto?

**Outros Processos**  =  *Utilização total do CAdvisor*  -  *Utilização do processo contentorizado*

Os **outros processos** incluem:

- Processos kubernetes autogeridos ou geridos não contentorizados 

- Processos de tempo de execução de contentores  

- Kubelet  

- Processos de sistema em execução no seu nó 

- Outras cargas de trabalho não-Kubernetes em funcionamento em hardware de nó ou VM 

### <a name="i-dont-see-image-and-name-property-values-populated-when-i-query-the-containerlog-table"></a>Não vejo valores de propriedade de Imagem e Nome povoados quando questiono a tabela ContainerLog.

Para a versão do agente ciprod12042019 e mais tarde, por padrão, estas duas propriedades não são povoadas para cada linha de registo para minimizar os custos incorridos nos dados de registo recolhidos. Existem duas opções para consultar a tabela que incluem estas propriedades com os seus valores:

#### <a name="option-1"></a>Opção 1 

Junte-se a outras tabelas para incluir estes valores de propriedade nos resultados.

Modifique as suas consultas para incluir propriedades de Image e ImageTag da ```ContainerInventory``` tabela, juntando-se à propriedade ContainerID. Pode incluir a propriedade Name (como apareceu anteriormente na ```ContainerLog``` tabela) do campo ContaineName da tabela KubepodInventory, juntando-se à propriedade ContainerID. Esta é a opção recomendada.

O exemplo seguinte é uma amostra detalhada que explica como obter estes valores de campo com juntas.

```
//lets say we are querying an hour worth of logs
let startTime = ago(1h);
let endTime = now();
//below gets the latest Image & ImageTag for every containerID, during the time window
let ContainerInv = ContainerInventory | where TimeGenerated >= startTime and TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID, Image, ImageTag | project-away TimeGenerated | project ContainerID1=ContainerID, Image1=Image ,ImageTag1=ImageTag;
//below gets the latest Name for every containerID, during the time window
let KubePodInv  = KubePodInventory | where ContainerID != "" | where TimeGenerated >= startTime | where TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID2 = ContainerID, Name1=ContainerName | project ContainerID2 , Name1;
//now join the above 2 to get a 'jointed table' that has name, image & imagetag. Outer left is safer in-case there are no kubepod records are if they are latent
let ContainerData = ContainerInv | join kind=leftouter (KubePodInv) on $left.ContainerID1 == $right.ContainerID2;
//now join ContainerLog table with the 'jointed table' above and project-away redundant fields/columns and rename columns that were re-written
//Outer left is safer so you dont lose logs even if we cannot find container metadata for loglines (due to latency, time skew between data types etc...)
ContainerLog
| where TimeGenerated >= startTime and TimeGenerated < endTime 
| join kind= leftouter (
   ContainerData
) on $left.ContainerID == $right.ContainerID2 | project-away ContainerID1, ContainerID2, Name, Image, ImageTag | project-rename Name = Name1, Image=Image1, ImageTag=ImageTag1 

```

#### <a name="option-2"></a>Opção 2

Reativar a recolha destas propriedades para cada linha de registo de contentores.

Se a primeira opção não for conveniente devido a alterações de consulta envolvidas, pode voltar a ativar a recolha destes campos, permitindo a definição ```log_collection_settings.enrich_container_logs``` no mapa de config do agente, tal como descrito nas definições de [configuração](insights/container-insights-agent-config.md)de recolha de dados .

> [!NOTE]
> A segunda opção não é recomendada com grandes clusters que têm mais de 50 nós porque gera chamadas de servidora API de cada nó do cluster para realizar este enriquecimento. Esta opção também aumenta o tamanho dos dados para cada linha de registo recolhida.

### <a name="can-i-view-metrics-collected-in-grafana"></a>Posso ver métricas recolhidas em Grafana?

O Monitor Azure para contentores suporta métricas de visualização armazenadas no seu espaço de trabalho Log Analytics nos dashboards Grafana. Fornecemos um modelo que você pode baixar do [repositório](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) de dashboard da Grafana para começar e referência para ajudá-lo a aprender a consultar dados adicionais dos seus clusters monitorizados para visualizar em dashboards grafana personalizados. 

### <a name="can-i-monitor-my-aks-engine-cluster-with-azure-monitor-for-containers"></a>Posso monitorizar o meu aglomerado de motores AKS com o Monitor Azure para contentores?

O Monitor Azure para contentores suporta a monitorização das cargas de trabalho dos contentores implantados no cluster(s) do motor AKS (anteriormente conhecido como motor ACS) hospedado no Azure. Para mais detalhes e uma visão geral das etapas necessárias para permitir a monitorização deste cenário, consulte a utilização do [Monitor Azure para recipientes para o motor AKS](https://github.com/microsoft/OMS-docker/tree/aks-engine).

### <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>Por que não vejo dados no meu espaço de trabalho de Log Analytics?

Se não conseguir ver quaisquer dados no espaço de trabalho do Log Analytics num determinado momento todos os dias, pode ter atingido o limite de 500 MB padrão ou a tampa diária especificada para controlar a quantidade de dados a recolher diariamente. Quando o limite é cumprido para o dia, a recolha de dados para e retoma apenas no dia seguinte. Para rever a utilização dos seus dados e atualizar para um nível de preços diferente com base nos padrões de utilização previstos, consulte a [utilização e o custo](platform/manage-cost-storage.md)dos dados do Log . 

### <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>Quais são os estados do contentor especificados na tabela ContainerInventário?

A tabela ContainerInventário contém informações sobre os recipientes parados e de funcionamento. A tabela é povoada por um fluxo de trabalho dentro do agente que consulta o estivador para todos os recipientes (em execução e parado), e reencaminha que data o espaço de trabalho log Analytics.
 
### <a name="how-do-i-resolve-missing-subscription-registration-error"></a>Como resolvo o erro *de registo de subscrição em falta?*

Se receber o erro de registo de **subscrição em falta para microsoft.OperationsManagement,** pode resolvê-lo registando o fornecedor de recursos **Microsoft.OperationsManagement** na subscrição onde o espaço de trabalho é definido. A documentação para como fazê-lo pode ser encontrada [aqui.](../azure-resource-manager/templates/error-register-resource-provider.md)

### <a name="is-there-support-for-rbac-enabled-aks-clusters"></a>Existe apoio para clusters AKS ativados por RBAC?

A solução de monitorização de contentores não suporta o RBAC, mas é suportada com o Monitor Azure para Contentores. A página de detalhes da solução pode não mostrar a informação certa nas lâminas que mostram dados para estes clusters.

### <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Como posso permitir a recolha de registos de contentores no espaço de nome do sistema kube através do Helm?

A recolha de registos de contentores no espaço de nome do sistema kube é desativada por defeito. A recolha de registos pode ser ativada através da definição de uma variável ambiental no omsagent. Para mais informações, consulte a página [do Monitor Azure para](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers) obter recipientes gitHub. 

### <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>Como actualizao o omsagent à versão mais recente?

Para aprender a atualizar o agente, consulte a [gestão do Agente.](insights/container-insights-manage-agent.md)

### <a name="how-do-i-enable-multi-line-logging"></a>Como posso permitir a exploração madeireira de várias linhas?

Atualmente o Monitor Azure para contentores não suporta a exploração madeireira multi-linhas, mas existem saliências disponíveis. Pode configurar todos os serviços para escrever em formato JSON e, em seguida, Docker/Moby irá escrevê-los como uma única linha.

Por exemplo, pode embrulhar o seu registo como um objeto JSON, como mostra o exemplo abaixo para uma aplicação de nó de amostra.js:

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

Estes dados serão os seguintes exemplos no Monitor Azure para registos quando você consultar:

```
LogEntry : ({"Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

Para uma análise detalhada do problema, reveja a seguinte [ligação GitHub](https://github.com/moby/moby/issues/22920).

### <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>Como posso resolver os erros da AD Azure quando ativo registos ao vivo? 

Pode ver o seguinte erro: O url de **resposta especificado no pedido não corresponde aos urls de resposta configurados para a aplicação: '<id da aplicação \> '.** A solução para resolvê-lo pode ser encontrada no artigo Como ver os dados dos contentores em tempo real com o [Monitor Azure para contentores.](insights/container-insights-livedata-setup.md#configure-ad-integrated-authentication) 

### <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>Por que não posso atualizar o cluster depois de embarcar?

Se depois de ativar o Monitor Azure para obter contentores para um cluster AKS, elimina o espaço de trabalho do Log Analytics para o que o cluster estava a enviar, ao tentar atualizar o cluster para o efeito, falhará. Para contornar isto, terá de desativar a monitorização e, em seguida, reactivar-a referindo-se a um espaço de trabalho válido diferente na sua subscrição. Quando tentar realizar a atualização do cluster novamente, deve processar e completar com sucesso.  

### <a name="which-ports-and-domains-do-i-need-to-openwhitelist-for-the-agent"></a>Que portos e domínios preciso abrir/whitelist para o agente?

Consulte os [requisitos](insights/container-insights-onboard.md#network-firewall-requirements) de firewall da Rede para as informações de configuração de proxy e firewall necessárias para o agente contentorizado com nuvens Azure, Azure US, e Azure China 21Vianet.

## <a name="azure-monitor-for-vms"></a>Azure Monitor para VMs
Esta FAQ da Microsoft é uma lista de perguntas comumente feitas sobre o Monitor Azure para VMs. Se tiver alguma dúvida adicional sobre a solução, vá ao fórum de [discussão](https://feedback.azure.com/forums/34192--general-feedback) e publique as suas perguntas. Quando uma pergunta é frequentemente feita, adicionamo-la a este artigo para que possa ser encontrado de forma rápida e fácil.

### <a name="can-i-onboard-to-an-existing-workspace"></a>Posso embarcar para um espaço de trabalho existente?
Se as suas máquinas virtuais já estiverem ligadas a um espaço de trabalho do Log Analytics, poderá continuar a utilizar esse espaço de trabalho ao embarcar no Azure Monitor para VMs, desde que se faça numa das regiões apoiadas [aqui.](insights/vminsights-enable-overview.md#prerequisites)


### <a name="can-i-onboard-to-a-new-workspace"></a>Posso embarcar para um novo espaço de trabalho? 
Se os seus VMs não estiverem atualmente ligados a um espaço de trabalho existente no Log Analytics, é necessário criar um novo espaço de trabalho para armazenar os seus dados. A criação de um novo espaço de trabalho padrão é feita automaticamente se configurar um único VM Azure para VMs Azure através do portal Azure.

Se optar por utilizar o método baseado no script, estes passos estão cobertos no [Monitor Enable Azure para VMs utilizando](insights/vminsights-enable-at-scale-powershell.md) o artigo de modelo Azure PowerShell ou Resource Manager. 

### <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>O que faço se o meu VM já está a reportar a um espaço de trabalho existente?
Se já está a recolher dados das suas máquinas virtuais, pode já os ter configurado para reportar dados a um espaço de trabalho existente no Log Analytics.  Enquanto esse espaço de trabalho estiver numa das nossas regiões apoiadas, pode permitir que o Azure Monitor para VMs possa chegar a esse espaço de trabalho pré-existente.  Se o espaço de trabalho que já está a utilizar não estiver numa das nossas regiões apoiadas, não poderá embarcar no Azure Monitor para VMs neste momento.  Estamos a trabalhar ativamente para apoiar regiões adicionais.


### <a name="why-did-my-vm-fail-to-onboard"></a>Porque é que o meu VM falhou a bordo?
Ao embarcar num VM Azure do portal Azure, ocorrem os seguintes passos:

* É criado um espaço de trabalho padrão log Analytics, se essa opção foi selecionada.
* O agente Log Analytics está instalado em VMs Azure utilizando uma extensão VM, se for determinado.  
* O monitor Azure para vMs Map Dependency é instalado em VMs Azure utilizando uma extensão, se for determinado que é necessário. 

Durante o processo a bordo, verificamos o estado de cada um dos acima para devolver um estado de notificação no portal. A configuração do espaço de trabalho e da instalação do agente normalmente demora 5 a 10 minutos. Ver dados de monitorização no portal leva mais 5 a 10 minutos.  

Se iniciou o embarque e viu mensagens indicando que o VM precisa de ser a bordo, deixe até 30 minutos para que o VM complete o processo. 


### <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>Não vejo alguns dados ou quaisquer dados nas tabelas de desempenho para o meu VM
Os nossos gráficos de desempenho foram atualizados para utilizar dados armazenados na tabela *InsightsMetrics.*  Para ver os dados nestes gráficos terá de fazer upgrade para utilizar a nova solução VM Insights.  Consulte o nosso [GA FAQ](insights/vminsights-ga-release-faq.md) para obter informações adicionais.

Se não vir os dados de desempenho na tabela de discos ou em algumas das tabelas de desempenho, os seus contadores de desempenho podem não estar configurados no espaço de trabalho. Para resolver, execute o seguinte [script PowerShell](insights/vminsights-enable-at-scale-powershell.md#enable-with-powershell).


### <a name="how-is-azure-monitor-for-vms-map-feature-different-from-service-map"></a>Como é que o Azure Monitor para vMs Map é diferente do Mapa de Serviços?
A funcionalidade Azure Monitor para Mapas vMs baseia-se no Mapa de Serviços, mas tem as seguintes diferenças:

* A vista do Mapa pode ser acedida a partir da lâmina VM e do Monitor Azure para VMs sob o Monitor Azure.
* As ligações no Mapa são agora clicáveis e exibem uma visão dos dados métricos de ligação no painel lateral para a ligação selecionada.
* Há uma nova API que é usada para criar os mapas para melhor suportar mapas mais complexos.
* Os VMs monitorizados estão agora incluídos no nó do grupo de clientes, e o gráfico de donuts mostra a proporção de máquinas virtuais monitorizadas vs não monitorizadas no grupo.  Também pode ser usado para filtrar a lista de máquinas quando o grupo é expandido.
* As máquinas virtuais monitorizadas estão agora incluídas nos nós do grupo de porta do servidor, e o gráfico de donuts mostra a proporção de máquinas monitorizadas vs não monitorizadas no grupo.  Também pode ser usado para filtrar a lista de máquinas quando o grupo é expandido.
* O estilo do mapa foi atualizado para ser mais consistente com o App Map a partir de insights de aplicação.
* Os painéis laterais foram atualizados e não têm todo o conjunto de integração que foram suportados no Mapa de Serviços - Gestão de Atualizações, Rastreio de Alterações, Segurança e Balcão de Serviços. 
* A opção de escolher grupos e máquinas para mapear foi atualizada e agora suporta Subscrições, Grupos de Recursos, conjuntos de escala de máquinas virtuais Azure e serviços Cloud.
* Não é possível criar novos grupos de máquinas de mapas de serviço na funcionalidade Azure Monitor para Mapas vMs.  

### <a name="why-do-my-performance-charts-show-dotted-lines"></a>Porque é que os meus gráficos de desempenho mostram linhas pontilhadas?
Isto pode ocorrer por algumas razões.  Nos casos em que há uma lacuna na recolha de dados, retratamos as linhas como pontilhadas.  Se tiver modificado a frequência de amostragem de dados para os contadores de desempenho ativados (a definição predefinida é recolher dados a cada 60 segundos), pode ver linhas pontilhadas na tabela se escolher um intervalo de tempo estreito para o gráfico e a sua frequência de amostragem for inferior ao tamanho do balde utilizado na tabela (por exemplo, a frequência de amostragem é de 10 minutos e cada balde na tabela é de 5 minutos).  A escolha de um intervalo de tempo mais alargado para visualizar deve fazer com que as linhas de gráfico apareçam como linhas sólidas em vez de pontos neste caso.

### <a name="are-groups-supported-with-azure-monitor-for-vms"></a>Os grupos são apoiados com o Monitor Azure para VMs?
Sim, uma vez instalado o agente dependency recolhemos informações dos VMs para exibir grupos com base em subscrição, grupo de recursos, conjuntos de escala de máquinas virtuais e serviços na nuvem.  Se tem usado o Service Map e criado grupos de máquinas, estes também são apresentados.  Os grupos de computador também aparecerão no filtro dos grupos se os tiver criado para o espaço de trabalho que está a ver. 

### <a name="how-do-i-see-the-details-for-what-is-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>Como vejo os detalhes para o que está a conduzir a linha de percentil 95 nas tabelas de desempenho agregados?
Por padrão, a lista está classificada para lhe mostrar os VMs que têm o valor mais alto para o percentil 95 para a métrica selecionada, exceto para o gráfico de memória disponível, que mostra as máquinas com o valor mais baixo do percentil 5.  Clicar na tabela abrirá a vista **Top N List** com a métrica adequada selecionada.

### <a name="how-does-the-map-feature-handle-duplicate-ips-across-different-vnets-and-subnets"></a>Como é que o recurso Map manuseia iPs duplicados em diferentes redes e subredes?
Se estiver a duplicar as gamas IP, quer com vMs quer com conjuntos de escala de máquinas virtuais Azure em subredes e redes, pode fazer com que o Monitor Azure para o Mapa de VMs apresente informações incorretas. Esta é uma questão conhecida e estamos a investigar opções para melhorar esta experiência.

### <a name="does-map-feature-support-ipv6"></a>O suporte do recurso Map IPv6?
A funcionalidade do mapa atualmente apenas suporta o IPv4 e estamos a investigar o suporte para o IPv6. Também apoiamos o IPv4 que está num túnel dentro do IPv6.

### <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-the-map-is-difficult-to-view"></a>Quando carrego um mapa para um Grupo de Recursos ou outro grupo grande, o mapa é difícil de ver
Embora tenhamos feito melhorias no Map para lidar com configurações grandes e complexas, percebemos que um mapa pode ter muitos nós, ligações e nó funcionando como um cluster.  Estamos empenhados em continuar a reforçar o apoio para aumentar a escalabilidade.   

### <a name="why-does-the-network-chart-on-the-performance-tab-look-different-than-the-network-chart-on-the-azure-vm-overview-page"></a>Porque é que o gráfico de rede no separador Performance é diferente do gráfico de rede na página de visão geral do Azure VM?

A página geral de um VM Azure exibe gráficos baseados na medição da atividade do anfitrião no VM convidado.  Para o gráfico de rede na visão geral do Azure VM, apenas exibe tráfego de rede que será faturado.  Isto não inclui o tráfego intervirtual da rede.  Os dados e gráficos mostrados para o Monitor Azure para VMs baseiam-se em dados do VM convidado e o gráfico de rede apresenta todo o tráfego TCP/IP que está de entrada e saída para esse VM, incluindo a rede intervirtual.

### <a name="how-is-response-time-measured-for-data-stored-in-vmconnection-and-displayed-in-the-connection-panel-and-workbooks"></a>Como é medido o tempo de resposta para os dados armazenados na VMConnection e apresentados no painel de ligação e nos livros de trabalho?

O tempo de resposta é uma aproximação. Uma vez que não instrumentamos o código da aplicação, não sabemos realmente quando é que um pedido começa e quando a resposta chega. Em vez disso, observamos os dados enviados numa ligação e, em seguida, os dados voltam a essa ligação. O nosso agente acompanha estes envios e recebe e tenta emparelhá-los: uma sequência de envios, seguida de uma sequência de receções é interpretada como um par de pedidos/resposta. O timing entre estas operações é o tempo de resposta. Incluirá a latência da rede e o tempo de processamento do servidor.

Esta aproximação funciona bem para protocolos que são baseados em pedido/resposta: um único pedido sai na ligação, e uma única resposta chega. É o caso do HTTP(S) (sem canalização), mas não satisfeito com outros protocolos.

### <a name="are-their-limitations-if-i-am-on-the-log-analytics-free-pricing-plan"></a>As suas limitações são se eu estiver no plano de preços gratuitos do Log Analytics?
Se tiver configurado o Monitor Azure com um espaço de trabalho de Log Analytics utilizando o nível de preços *gratuitos,* a funcionalidade Azure Monitor para VMs Map apenas suporta cinco máquinas conectadas ligadas ao espaço de trabalho. Se tiver cinco VMs ligados a um espaço de trabalho gratuito, desliga um dos VMs e, em seguida, liga um novo VM, o novo VM não é monitorizado e refletido na página do Mapa.  

Sob esta condição, será solicitado com a opção **Try Now** quando abrir o VM e selecionar **Insights** do painel da mão esquerda, mesmo depois de já ter sido instalado no VM.  No entanto, não é solicitado com opções como normalmente ocorreria se este VM não estivesse a bordo do Monitor Azure para VMs. 


## <a name="next-steps"></a>Passos seguintes
Se a sua pergunta não for respondida aqui, pode consultar os seguintes fóruns para perguntas e respostas adicionais.

- [Log Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights)
- [Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/home?forum=ApplicationInsights)

Para obter feedback geral no Monitor Azure visite o fórum de [feedback](https://feedback.azure.com/forums/34192--general-feedback).
