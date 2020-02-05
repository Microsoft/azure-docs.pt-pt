---
title: Perguntas frequentes sobre Azure Monitor | Microsoft Docs
description: Respostas para perguntas frequentes sobre Azure Monitor.
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/23/2020
ms.openlocfilehash: 9f377f93ab8fef2c1ad713da6fcd6c6f14107c3f
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76986823"
---
# <a name="azure-monitor-frequently-asked-questions"></a>Azure Monitor perguntas frequentes

Esta FAQ da Microsoft é uma lista de perguntas frequentes sobre Azure Monitor.

## <a name="general"></a>Geral

### <a name="what-is-azure-monitor"></a>O que é o Azure Monitor?
[Azure monitor](overview.md) é um serviço no Azure que fornece monitoramento de desempenho e disponibilidade para aplicativos e serviços no Azure, em outros ambientes de nuvem ou localmente. Azure Monitor coleta dados de várias fontes em uma plataforma de dados comum, onde podem ser analisados quanto a tendências e anomalias. Recursos avançados do Azure Monitor ajudam a identificar e responder rapidamente a situações críticas que podem afetar seu aplicativo.

### <a name="whats-the-difference-between-azure-monitor-log-analytics-and-application-insights"></a>Qual é a diferença entre Azure Monitor, Log Analytics e Application Insights?
Em setembro de 2018, a Microsoft combinou Azure Monitor, Log Analytics e Application Insights em um único serviço para fornecer monitoramento de ponta a ponta avançado de seus aplicativos e dos componentes dos quais eles dependem. Os recursos do Log Analytics e Application Insights não foram alterados, embora alguns recursos tenham sido remarcados para Azure Monitor a fim de refletir melhor seu novo escopo. O mecanismo de dados de log e a linguagem de consulta do Log Analytics agora são chamados de logs de Azure Monitor. Consulte [Azure monitor atualizações de terminologia](terminology.md).

### <a name="what-does-azure-monitor-cost"></a>O que Azure Monitor custo?
Recursos de Azure Monitor que são habilitados automaticamente, como a coleta de métricas e logs de atividade, são fornecidos sem custo. Há um custo associado a outros recursos, como consultas de log e alertas. Consulte a [página de preços Azure monitor](https://azure.microsoft.com/pricing/details/monitor/) para obter informações detalhadas sobre preços.

### <a name="how-do-i-enable-azure-monitor"></a>Como fazer habilitar Azure Monitor?
O Azure Monitor está habilitado no momento em que você cria uma nova assinatura do Azure, e as [métricas](platform/data-platform-metrics.md) do [log de atividades](platform/activity-logs-overview.md) e da plataforma são coletadas automaticamente. Crie [configurações de diagnóstico](platform/diagnostic-settings.md) para coletar informações mais detalhadas sobre a operação dos recursos do Azure e adicionar [soluções](insights/solutions.md) e [insights](insights/insights-overview.md) de monitoramento para fornecer análise adicional sobre os dados coletados para serviços específicos. 

### <a name="how-do-i-access-azure-monitor"></a>Como fazer Azure Monitor de acesso?
Acesse todos os recursos e dados do Azure Monitor no menu **monitorar** na portal do Azure. A seção **monitoramento** do menu para diferentes serviços do Azure fornece acesso às mesmas ferramentas com dados filtrados para um recurso específico. Azure Monitor dados também estão acessíveis para uma variedade de cenários usando a CLI, o PowerShell e uma API REST.

### <a name="is-there-an-on-premises-version-of-azure-monitor"></a>Há uma versão local do Azure Monitor?
Não. Azure Monitor é um serviço de nuvem escalonável que processa e armazena grandes quantidades de dados, embora Azure Monitor possa monitorar recursos locais e em outras nuvens.

### <a name="can-azure-monitor-monitor-on-premises-resources"></a>Azure Monitor pode monitorar recursos locais?
Sim, além de coletar dados de monitoramento de recursos do Azure, Azure Monitor pode coletar dados de máquinas virtuais e aplicativos em outras nuvens e locais. Consulte [fontes de dados de monitoramento para Azure monitor](platform/data-sources.md).

### <a name="does-azure-monitor-integrate-with-system-center-operations-manager"></a>Azure Monitor integrar com System Center Operations Manager?
Você pode conectar seu grupo de gerenciamento do System Center Operations Manager existente a Azure Monitor para coletar dados de agentes em logs do Azure Monitor. Isso permite que você use consultas de log e solução para analisar os dados coletados dos agentes. Você também pode configurar os agentes de System Center Operations Manager existentes para enviar dados diretamente para Azure Monitor. Consulte [conectar Operations Manager ao Azure monitor](platform/om-agents.md).

### <a name="what-ip-addresses-does-azure-monitor-use"></a>Quais endereços IP Azure Monitor usar?
Consulte [endereços IP usados por Application insights e log Analytics](app/ip-addresses.md) para obter uma lista dos endereços IP e portas necessárias para agentes e outros recursos externos para acessar Azure monitor. 

## <a name="monitoring-data"></a>Monitorizar dados

### <a name="where-does-azure-monitor-get-its-data"></a>Onde Azure Monitor obter seus dados?
O Azure Monitor coleta dados de uma variedade de fontes, incluindo logs e métricas da plataforma e dos recursos do Azure, aplicativos personalizados e agentes em execução em máquinas virtuais. Outros serviços, como a central de segurança do Azure e o observador de rede, coletam dados em um Log Analytics espaço de trabalho para que possam ser analisados com Azure Monitor dados. Você também pode enviar dados personalizados para Azure Monitor usando a API REST para logs ou métricas. Consulte [fontes de dados de monitoramento para Azure monitor](platform/data-sources.md).

### <a name="what-data-is-collected-by-azure-monitor"></a>Quais dados são coletados por Azure Monitor? 
Azure Monitor coleta dados de uma variedade de fontes em [logs](platform/data-platform-logs.md) ou [métricas](platform/data-platform-metrics.md). Cada tipo de dados tem suas próprias vantagens relativas, e cada um deles dá suporte a um determinado conjunto de recursos no Azure Monitor. Há um único banco de dados de métricas para cada assinatura do Azure, enquanto você pode criar vários Log Analytics espaços de trabalho para coletar logs dependendo de seus requisitos. Consulte [Azure monitor plataforma de dados](platform/data-platform.md).

### <a name="is-there-a-maximum-amount-of-data-that-i-can-collect-in-azure-monitor"></a>Há uma quantidade máxima de dados que posso coletar em Azure Monitor?
Não há nenhum limite para a quantidade de dados de métrica que você pode coletar, mas esses dados são armazenados por um máximo de 93 dias. Consulte [retenção de métricas](platform/data-platform-metrics.md#retention-of-metrics). Não há limite para a quantidade de dados de log que você pode coletar, mas pode ser afetado pelo tipo de preço que você escolher para o espaço de trabalho Log Analytics. Consulte os [detalhes de preços](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="how-do-i-access-data-collected-by-azure-monitor"></a>Como fazer acessar dados coletados pelo Azure Monitor?
As informações e soluções fornecem uma experiência personalizada para trabalhar com dados armazenados no Azure Monitor. Você pode trabalhar diretamente com dados de log usando uma consulta de log escrita na linguagem de consulta Kusto (KQL). No portal do Azure, você pode escrever e executar consultas e analisar dados interativamente usando Log Analytics. Analise as métricas no portal do Azure com o Metrics Explorer. Consulte [analisar dados de log em Azure monitor](log-query/log-query-overview.md) e [introdução ao Metrics Explorer do Azure](platform/metrics-getting-started.md).





## <a name="solutions-and-insights"></a>Soluções e ideias

### <a name="what-is-an-insight-in-azure-monitor"></a>O que é um insight no Azure Monitor?
As informações fornecem uma experiência de monitoramento Personalizada para determinados serviços do Azure. Eles usam as mesmas métricas e logs que outros recursos no Azure Monitor, mas podem coletar dados adicionais e fornecer uma experiência única no portal do Azure. Confira [informações em Azure monitor](insights/insights-overview.md).

Para exibir informações no portal do Azure, consulte a seção **insights** do menu **monitorar** ou da seção **monitoramento** do menu do serviço.

### <a name="what-is-a-solution-in-azure-monitor"></a>O que é uma solução no Azure Monitor?
Soluções de monitoramento são conjuntos de lógica empacotados para monitorar um aplicativo ou serviço específico com base em recursos de Azure Monitor. Eles coletam dados de log em Azure Monitor e fornecem consultas de log e exibições para sua análise usando uma experiência comum no portal do Azure. Consulte [Monitoring Solutions in Azure monitor](insights/solutions.md).

Para exibir soluções no portal do Azure, clique em **mais** na seção **insights** do menu **monitorar** . Clique em **Adicionar** para adicionar outras soluções ao espaço de trabalho.






## <a name="logs"></a>Registos

### <a name="whats-the-difference-between-azure-monitor-logs-and-azure-data-explorer"></a>Qual é a diferença entre os logs de Azure Monitor e o Data Explorer do Azure?
O Azure Data Explorer é um serviço de exploração de dados rápido e altamente dimensionável para dados telemétricos e de registo. Azure Monitor logs é criado sobre o Data Explorer do Azure e usa a mesma linguagem de consulta Kusto (KQL) com algumas pequenas diferenças. Veja [Azure monitor diferenças na linguagem de consulta de log](log-query/data-explorer-difference.md).

### <a name="how-do-i-retrieve-log-data"></a>Como fazer recuperar dados de log?
Todos os dados são recuperados de um espaço de trabalho Log Analytics usando uma consulta de log escrita usando a linguagem de consulta Kusto (KQL). Você pode escrever suas próprias consultas ou usar soluções e ideias que incluem consultas de log para um determinado aplicativo ou serviço. Consulte [visão geral das consultas de log no Azure monitor](log-query/log-query-overview.md).

### <a name="what-is-a-log-analytics-workspace"></a>O que é uma área de trabalho do Log Analytics?
Todos os dados de log coletados pelo Azure Monitor são armazenados em um espaço de trabalho do Log Analytics. Um espaço de trabalho é essencialmente um contêiner em que os dados de log são coletados de uma variedade de fontes. Você pode ter um único espaço de trabalho Log Analytics para todos os seus dados de monitoramento ou pode ter requisitos para vários espaços de trabalho. Consulte [projetando sua implantação de logs de Azure monitor](platform/design-logs-deployment.md).

### <a name="can-you-move-an-existing-log-analytics-workspace-to-another-azure-subscription"></a>Você pode mover um espaço de trabalho Log Analytics existente para outra assinatura do Azure?
Você pode mover um espaço de trabalho entre grupos de recursos ou assinaturas, mas não para uma região diferente. Consulte [mover um espaço de trabalho log Analytics para uma assinatura ou grupo de recursos diferente](platform/move-workspace.md).

### <a name="why-cant-i-see-query-explorer-and-save-buttons-in-log-analytics"></a>Por que não posso ver o Query Explorer e guardar botões no Log Analytics?

**Consulta Explorer**, **Save** and New **alert rule** buttons não estão disponíveis quando o âmbito de [consulta](log-query/scope.md) é definido para um recurso específico. Para criar alertas, guardar ou carregar uma consulta, o Log Analytics deve ser remeto para um espaço de trabalho. Para abrir o Log Analytics no contexto do espaço de trabalho, selecione **Registos** do menu **Do Monitor Azure.** A última área de trabalho utilizada é selecionada, mas pode selecionar qualquer outra área de trabalho. Consulte o âmbito e o intervalo de tempo de consulta de [registo no Azure Monitor Log Analytics](log-query/scope.md)

### <a name="why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-when-opening-log-analytics-from-a-vm"></a>Porque é que estou a receber o erro: "Registar o fornecedor de recursos 'Microsoft.Insights' para esta subscrição para permitir esta consulta" ao abrir o Log Analytics a partir de um VM? 
Muitos fornecedores de recursos estão automaticamente registados, mas pode ser necessário registar manualmente alguns fornecedores de recursos. O âmbito de registo é sempre a subscrição. Veja [Fornecedores e tipos de recursos](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) para obter mais informações.

### <a name="why-am-i-am-getting-no-access-error-message-when-opening-log-analytics-from-a-vm"></a>Porque é que não estou a receber nenhuma mensagem de erro de acesso ao abrir o Log Analytics a partir de um VM? 
Para ver os Registos VM, é necessário ter permissão de leitura para os espaços de trabalho que armazenam os registos VM. Nestes casos, o administrador deve conceder-lhe com as permissões no Azure.




## <a name="alerts"></a>Alertas

### <a name="what-is-an-alert-in-azure-monitor"></a>O que é um alerta no Azure Monitor?
Os alertas o notificam proativamente quando condições importantes são encontradas nos dados de monitoramento. Eles permitem que você identifique e resolva problemas antes que os usuários do seu sistema os observem. Há vários tipos de alertas:

- Métrica-o valor da métrica excede um limite.
- Consulta de log – os resultados de uma consulta de log correspondem aos critérios definidos.
- Log de atividades-evento de log de atividades corresponde aos critérios definidos.
- Teste na Web-resultados dos critérios de correspondência de teste de disponibilidade definidos.


Consulte [visão geral de alertas no Microsoft Azure](platform/alerts-overview.md).


### <a name="what-is-an-action-group"></a>O que é um grupo de ações?
Um grupo de ações é uma coleção de notificações e ações que podem ser disparadas por um alerta. Vários alertas podem usar um único grupo de ação, permitindo que você aproveite conjuntos comuns de notificações e ações. Consulte [criar e gerenciar grupos de ações no portal do Azure](platform/action-groups.md).


### <a name="what-is-an-action-rule"></a>O que é uma regra de ação?
Uma regra de ação permite modificar o comportamento de um conjunto de alertas que correspondem a um determinado critério. Isso permite que você execute esses requisitos como desabilitar ações de alerta durante uma janela de manutenção. Você também pode aplicar um grupo de ações a um conjunto de alertas em vez de aplicá-los diretamente às regras de alerta. Consulte [regras de ação](platform/alerts-action-rules.md).


## <a name="agents"></a>Agentes

### <a name="does-azure-monitor-require-an-agent"></a>Azure Monitor requer um agente?
Um agente só é necessário para coletar dados do sistema operacional e das cargas de trabalho em máquinas virtuais. As máquinas virtuais podem estar localizadas no Azure, em outro ambiente de nuvem ou localmente. Consulte [visão geral dos agentes de Azure monitor](platform/agents-overview.md).


### <a name="whats-the-difference-between-the-azure-monitor-agents"></a>Qual é a diferença entre os agentes de Azure Monitor?
A extensão de diagnóstico do Azure é para máquinas virtuais do Azure e coleta dados para Azure Monitor métricas, armazenamento do Azure e hubs de eventos do Azure. O agente de Log Analytics é para máquinas virtuais no Azure, outro ambiente de nuvem ou local e coleta dados para Azure Monitor logs. O agente de dependência requer o agente de Log Analytics e os detalhes e as dependências do processo coletados. Consulte [visão geral dos agentes de Azure monitor](platform/agents-overview.md).


### <a name="does-my-agent-traffic-use-my-expressroute-connection"></a>Meu tráfego de agente usa minha conexão de ExpressRoute?
O tráfego para Azure Monitor usa o circuito do ExpressRoute de emparelhamento da Microsoft. Consulte a [documentação do expressroute](../expressroute/expressroute-faqs.md#supported-services) para obter uma descrição dos diferentes tipos de tráfego de expressroute. 

### <a name="how-can-i-confirm-that-the-log-analytics-agent-is-able-to-communicate-with-azure-monitor"></a>Como posso confirmar se o agente de Log Analytics é capaz de se comunicar com Azure Monitor?
No painel de controle no computador do agente, selecione **configurações de & de segurança** **Microsoft Monitoring Agent** . Na guia **log Analytics do Azure (OMS)** , um ícone de marca de seleção verde confirma que o agente é capaz de se comunicar com Azure monitor. Um ícone de aviso amarelo significa que o agente está tendo problemas. Um motivo comum é que o serviço de **Microsoft Monitoring Agent** foi interrompido. Utilize o Gestor de controlo de serviço para reiniciar o serviço.

### <a name="how-do-i-stop-the-log-analytics-agent-from-communicating-with-azure-monitor"></a>Como fazer impedir que o agente de Log Analytics se comunique com Azure Monitor?
Para agentes conectados a Log Analytics diretamente, abra o painel de controle e selecione **configurações de & de segurança** **Microsoft Monitoring Agent**. Na guia **log Analytics do Azure (OMS)** , remova todos os espaços de trabalho listados. Em System Center Operations Manager, remova o computador da lista Log Analytics computadores gerenciados. Do Operations Manager atualiza a configuração do agente já não é o relatório para o Log Analytics. 

### <a name="how-much-data-is-sent-per-agent"></a>A quantidade de dados é enviado por agente?
A quantidade de dados enviados por agente depende de:

* As soluções que tiver ativado
* O número de registos e contadores de desempenho recolhidos
* O volume de dados nos registos

Consulte [gerenciar o uso e os custos com os logs de Azure monitor](platform/manage-cost-storage.md) para obter detalhes.

Para computadores que são capazes de executar o agente de WireData, utilize a seguinte consulta para ver a quantidade de dados está sendo enviada:

```Kusto
WireData
| where ProcessName == "C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe" 
| where Direction == "Outbound" 
| summarize sum(TotalBytes) by Computer 
```

### <a name="how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-azure-monitor"></a>Quanta largura de banda de rede é usada pelo MMA (agente de gerenciamento da Microsoft) ao enviar dados para Azure Monitor?
Largura de banda é uma função na quantidade de dados enviados. Dados são comprimidos à medida que ele é enviado através da rede.


### <a name="how-can-i-be-notified-when-data-collection-from-the-log-analytics-agent-stops"></a>Como posso ser notificado quando a coleta de dados do agente de Log Analytics é interrompida?

Use as etapas descritas em [criar um novo alerta de log](platform/alerts-metric.md) para ser notificado quando a coleta de dados for interrompida. Use as seguintes configurações para a regra de alerta:

- **Definir condição de alerta**: especifique seu espaço de trabalho log Analytics como o destino do recurso.
- **Critérios de alerta** 
   - **Nome do sinal**: *pesquisa de logs personalizada*
   - **Consulta de pesquisa**: `Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`
   - **Lógica de alerta**: **com base no** *número de resultados*, **condição** *maior que*, **valor de limite** *0*
   - **Avaliado com base em**: **período (em minutos)** *30*, **frequência (em minutos)** *10*
- **Definir detalhes do alerta** 
   - **Nome**: *coleta de dados interrompida*
   - **Severidade**: *aviso*

Especifique um [grupo de ação](platform/action-groups.md) novo ou existente para que, quando o alerta de log corresponder aos critérios, você seja notificado se tiver uma pulsação ausente por mais de 15 minutos.


### <a name="what-are-the-firewall-requirements-for-azure-monitor-agents"></a>Quais são os requisitos de firewall para agentes de Azure Monitor?
Consulte [requisitos de firewall de rede](platform/log-analytics-agent.md#network-firewall-requirements)para obter detalhes sobre os requisitos de firewall.


## <a name="visualizations"></a>Visualizações

### <a name="why-cant-i-cant-see-view-designer"></a>Por que não posso ver o Designer de Vistas?

O View Designer só está disponível para utilizadores atribuídos com permissões do Colaborador ou superiores no espaço de trabalho do Log Analytics.


## <a name="application-insights"></a>Estatísticas das Aplicações

### <a name="configuration-problems"></a>Problemas de configuração
*Estou tendo problemas para configurar meu:*

* [Aplicação .NET](app/asp-net-troubleshoot-no-data.md)
* [Monitorando um aplicativo já em execução](app/monitor-performance-live-website-now.md#troubleshoot)
* [Diagnóstico do Azure](platform/diagnostics-extension-to-application-insights.md)
* [Aplicação Web Java](app/java-troubleshoot.md)

*Não obtenho dados do meu servidor*

* [Definir exceções de firewall](app/ip-addresses.md)
* [Configurar um servidor ASP.NET](app/monitor-performance-live-website-now.md)
* [Configurar um servidor Java](app/java-agent.md)

### <a name="can-i-use-application-insights-with-"></a>Posso usar Application Insights com...?

* [Aplicativos Web em um servidor IIS na VM do Azure ou no conjunto de dimensionamento de máquinas virtuais do Azure](app/azure-vm-vmss-apps.md)
* [Aplicativos Web em um servidor IIS-local ou em uma VM](app/asp-net.md)
* [Aplicativos Web Java](app/java-get-started.md)
* [Aplicações Node.js](app/nodejs.md)
* [Aplicativos Web no Azure](app/azure-web-apps.md)
* [Serviços de nuvem no Azure](app/cloudservices.md)
* [Servidores de aplicativos em execução no Docker](app/docker.md)
* [Aplicativos Web de página única](app/javascript.md)
* [SharePoint](app/sharepoint.md)
* [Aplicativo de área de trabalho do Windows](app/windows-desktop.md)
* [Outras plataformas](app/platforms.md)

### <a name="is-it-free"></a>É gratuito?

Sim, para uso experimental. No plano de preços básico, seu aplicativo pode enviar uma determinada concessão de dados a cada mês gratuitamente. A concessão gratuita é grande o suficiente para abranger o desenvolvimento e publicar um aplicativo para um número pequeno de usuários. Você pode definir um limite para evitar que mais de uma quantidade especificada de dados seja processada.

Volumes maiores de telemetria são cobrados por GB. Fornecemos algumas dicas sobre como [limitar seus encargos](app/pricing.md).

O plano Enterprise incorre em um encargo para cada dia em que cada nó do servidor Web envia telemetria. É adequado se você quiser usar a exportação contínua em uma grande escala.

[Leia o plano de preços](https://azure.microsoft.com/pricing/details/application-insights/).

### <a name="how-much-does-it-cost"></a>Quanto custa?

* Abra a **página uso e custos estimados** em um recurso de Application insights. Há um gráfico de uso recente. Você pode definir um limite de volume de dados, se desejar.
* Abra a [folha de cobrança do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade/Overview) para ver suas listas em todos os recursos.

### <a name="q14"></a>O que Application Insights modificar em meu projeto?
Os detalhes dependem do tipo de projeto. Para um aplicativo Web:

* Adiciona esses arquivos ao seu projeto:
  * ApplicationInsights.config
  * ai.js
* Instala estes pacotes NuGet:
  * *API de Application insights* -a API principal
  * *API de Application insights para aplicativos Web* -usada para enviar telemetria do servidor
  * *API de Application insights para aplicativos JavaScript* – usada para enviar telemetria do cliente
* Os pacotes incluem estes assemblies:
  * Microsoft.ApplicationInsights
  * Microsoft.ApplicationInsights.Platform
* Insere itens em:
  * Web.config
  * packages.config
* (Somente novos projetos – se você [adicionar Application insights a um projeto existente][start], precisará fazer isso manualmente.) Insere trechos no código do cliente e do servidor para inicializá-los com a ID de recurso Application Insights. Por exemplo, numa aplicação MVC, o código é inserido na página principal Views/Shared/\_Layout.cshtml

### <a name="how-do-i-upgrade-from-older-sdk-versions"></a>Como fazer atualizar de versões mais antigas do SDK?
Consulte as [notas de versão](app/release-notes.md) do SDK apropriado para seu tipo de aplicativo.

### <a name="update"></a>Como posso alterar a qual recurso do Azure meu projeto envia dados?
Em Gerenciador de Soluções, clique com o botão direito do mouse em `ApplicationInsights.config` e escolha **atualizar Application insights**. Você pode enviar os dados para um recurso novo ou existente no Azure. O assistente de atualização altera a chave de instrumentação em ApplicationInsights. config, que determina onde o SDK do servidor envia seus dados. A menos que você desmarque "atualizar tudo", ele também alterará a chave onde ele aparece em suas páginas da Web.

### <a name="what-is-status-monitor"></a>O que é o Monitor de Estado?

Um aplicativo de área de trabalho que você pode usar em seu servidor Web IIS para ajudar a configurar Application Insights em aplicativos Web. Ele não coleta telemetria: você pode interrompê-lo quando não estiver configurando um aplicativo. 

[Saiba mais](app/monitor-performance-live-website-now.md#questions).

### <a name="what-telemetry-is-collected-by-application-insights"></a>Qual telemetria é coletada por Application Insights?

De aplicativos Web do servidor:

* Pedidos HTTP
* [Dependências](app/asp-net-dependencies.md). Chamadas para: bancos de dados SQL; Chamadas HTTP para serviços externos; Azure Cosmos DB, tabela, armazenamento de BLOBs e fila. 
* [Exceções](app/asp-net-exceptions.md) e rastreamentos de pilha.
* [Contadores de desempenho](app/performance-counters.md) -se você [usar status monitor](app/monitor-performance-live-website-now.md), [monitoramento do Azure para serviços de aplicativos](app/azure-web-apps.md), [monitoramento do Azure para VM ou conjunto de dimensionamento de máquinas virtuais](app/azure-vm-vmss-apps.md)ou o [gravador Application insights coletado](app/java-collectd.md).
* [Métricas e eventos personalizados](app/api-custom-events-metrics.md) que você codifica.
* [Logs de rastreamento](app/asp-net-trace-logs.md) se você configurar o coletor apropriado.

De [páginas da Web do cliente](app/javascript.md):

* [Contagens de exibição de página](app/usage-overview.md)
* [Chamadas AJAX](app/asp-net-dependencies.md) Solicitações feitas de um script em execução.
* Dados de carregamento de exibição de página
* Contagens de usuário e sessão
* [IDs de usuário autenticado](app/api-custom-events-metrics.md#authenticated-users)

De outras fontes, se você configurá-las:

* [Diagnóstico do Azure](platform/diagnostics-extension-to-application-insights.md)
* [Importar para o Analytics](platform/data-collector-api.md)
* [Log Analytics](platform/data-collector-api.md)
* [Logstash](platform/data-collector-api.md)

### <a name="can-i-filter-out-or-modify-some-telemetry"></a>Posso filtrar ou modificar alguma telemetria?

Sim, no servidor que você pode escrever:

* Processador de telemetria para filtrar ou adicionar propriedades aos itens de telemetria selecionados antes que eles sejam enviados do seu aplicativo.
* Inicializador de telemetria para adicionar propriedades a todos os itens de telemetria.

Saiba mais sobre o [ASP.net](app/api-filtering-sampling.md) ou o [Java](app/java-filter-telemetry.md).

### <a name="how-are-city-countryregion-and-other-geo-location-data-calculated"></a>Como a cidade, o país/região e outros dados de localização geográfica são calculados?

Procuramos o endereço IP (IPv4 ou IPv6) do cliente Web usando [GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/).

* Telemetria do navegador: coletamos o endereço IP do remetente.
* Telemetria do servidor: o módulo Application Insights coleta o endereço IP do cliente. Ele não será coletado se `X-Forwarded-For` estiver definido.
* Para saber mais sobre como o endereço IP e os dados de localização geográfica são coletados no Application Insights consulte este [artigo](https://docs.microsoft.com/azure/azure-monitor/app/ip-collection).


Você pode configurar o `ClientIpHeaderTelemetryInitializer` para obter o endereço IP de um cabeçalho diferente. Em alguns sistemas, por exemplo, ele é movido por um proxy, balanceador de carga ou CDN para `X-Originating-IP`. [Saiba mais](https://apmtips.com/blog/2016/07/05/client-ip-address/).

Você pode [usar Power bi](app/export-power-bi.md ) para exibir a telemetria de solicitação em um mapa.


### <a name="data"></a>Por quanto tempo os dados são retidos no portal? É seguro?
Dê uma olhada na [retenção e na privacidade dos dados][data].

### <a name="what-happens-to-application-insights-telemetry-when-a-server-or-device-loses-connection-with-azure"></a>O que acontece com a telemetria do Application Insight quando um servidor ou dispositivo perde a ligação com o Azure?

Todos os nossos SDKs, incluindo o Web SDK, inclui "transporte fiável" ou "transporte robusto". Quando o servidor ou dispositivo perde a ligação com o Azure, a telemetria é [armazenada localmente no sistema](https://docs.microsoft.com/azure/azure-monitor/app/data-retention-privacy#does-the-sdk-create-temporary-local-storage) de ficheiros (Server SDKs) ou no ARMAZENAMENTO de Sessão HTML5 (Web SDK). O SDK irá periodicamente voltar a tentar enviar esta telemetria até que o nosso serviço de ingestão a considere "velha" (48 horas para registos, 30 minutos para métricas). A telemetria velha será largada. Em alguns casos, como quando o armazenamento local está cheio, não haverá nova tentativa.


### <a name="could-personal-data-be-sent-in-the-telemetry"></a>Os dados pessoais podem ser enviados na telemetria?

Isso será possível se o seu código enviar esses dados. Isso também pode acontecer se as variáveis nos rastreamentos de pilha incluírem dados pessoais. Sua equipe de desenvolvimento deve realizar avaliações de risco para garantir que os dados pessoais sejam tratados adequadamente. [Saiba mais sobre a retenção e a privacidade dos dados](app/data-retention-privacy.md).

**Todos os** octetos do endereço Web do cliente são sempre definidos como 0 depois que os atributos de localização geográfica são pesquisados.

### <a name="my-instrumentation-key-is-visible-in-my-web-page-source"></a>Minha chave de instrumentação está visível na minha origem da página da Web. 

* Essa é uma prática comum em soluções de monitoramento.
* Ele não pode ser usado para roubar seus dados.
* Ele pode ser usado para distorcer seus dados ou disparar alertas.
* Não ouvimos que qualquer cliente tenha tido tais problemas.

Podia:

* Use duas chaves de instrumentação separadas (recursos de Application Insights separados) para dados de cliente e servidor. Ou
* Gravar um proxy que é executado em seu servidor e fazer com que o cliente Web envie dados por meio desse proxy.

### <a name="post"></a>Como fazer ver dados de POSTAgem na pesquisa de diagnóstico?
Não registramos dados de POSTAgem automaticamente, mas você pode usar uma chamada TrackTrace: Coloque os dados no parâmetro Message. Isso tem um limite de tamanho maior do que os limites em Propriedades de cadeia de caracteres, embora você não possa filtrá-los.

### <a name="should-i-use-single-or-multiple-application-insights-resources"></a>Devo usar um ou vários recursos do Application Insights?

Use um único recurso para todos os componentes ou funções em um único sistema de negócios. Use recursos separados para desenvolvimento, teste e versões de lançamento e para aplicativos independentes.

* [Veja a discussão aqui](app/separate-resources.md)
* [Exemplo – serviço de nuvem com funções Web e de trabalho](app/cloudservices.md)

### <a name="how-do-i-dynamically-change-the-instrumentation-key"></a>Como fazer alterar dinamicamente a chave de instrumentação?

* [Discussão aqui](app/separate-resources.md)
* [Exemplo – serviço de nuvem com funções Web e de trabalho](app/cloudservices.md)

### <a name="what-are-the-user-and-session-counts"></a>Quais são as contagens de usuário e sessão?

* O SDK do JavaScript define um cookie de usuário no cliente Web, para identificar usuários que retornam e um cookie de sessão para agrupar atividades.
* Se não houver nenhum script do lado do cliente, você poderá [definir cookies no servidor](https://apmtips.com/blog/2016/07/09/tracking-users-in-api-apps/).
* Se um usuário real usa seu site em navegadores diferentes ou usando a navegação no-Private/Incognito ou em computadores diferentes, eles serão contados mais de uma vez.
* Para identificar um usuário conectado entre computadores e navegadores, adicione uma chamada para [setAuthenticatedUserContext ()](app/api-custom-events-metrics.md#authenticated-users).

### <a name="q17"></a>Habilitei tudo em Application Insights?
| O que você deve ver | Como obtê-lo | Por que você deseja |
| --- | --- | --- |
| Gráficos de disponibilidade |[Testes na Web](app/monitor-web-app-availability.md) |Sabe que seu aplicativo Web está ativo |
| Desempenho do aplicativo do servidor: tempos de resposta,... |[Adicione Application insights ao seu projeto](app/asp-net.md) ou [instale o ai status monitor no servidor](app/monitor-performance-live-website-now.md) (ou escreva seu próprio código para [acompanhar as dependências](app/api-custom-events-metrics.md#trackdependency)) |Detectar problemas de desempenho |
| Telemetria de dependência |[Instalar o ia Status Monitor no servidor](app/monitor-performance-live-website-now.md) |Diagnosticar problemas com bancos de dados ou outros componentes externos |
| Obter rastreamentos de pilha de exceções |[Inserir chamadas trackexception em seu código](app/asp-net-exceptions.md) (mas algumas são relatadas automaticamente) |Detectar e diagnosticar exceções |
| Pesquisar rastreamentos de log |[Adicionar um adaptador de log](app/asp-net-trace-logs.md) |Diagnosticar exceções, problemas de desempenho |
| Noções básicas de uso do cliente: exibições de página, sessões,... |[Inicializador de JavaScript em páginas da Web](app/javascript.md) |Análise de utilização |
| Métricas personalizadas do cliente |[Acompanhamento de chamadas em páginas da Web](app/api-custom-events-metrics.md) |Aperfeiçoar a experiência de utilizador |
| Métricas personalizadas do servidor |[Acompanhamento de chamadas no servidor](app/api-custom-events-metrics.md) |Business intelligence |

### <a name="why-are-the-counts-in-search-and-metrics-charts-unequal"></a>Por que as contagens em gráficos de pesquisa e métricas são desiguais?

A [amostragem](app/sampling.md) reduz o número de itens de telemetria (solicitações, eventos personalizados e assim por diante) que são realmente enviados de seu aplicativo para o Portal. Em Pesquisar, você verá o número de itens realmente recebidos. Em gráficos de métricas que exibem uma contagem de eventos, você verá o número de eventos originais que ocorreram. 

Cada item transmitido contém uma propriedade `itemCount` que mostra quantos eventos originais o item representa. Para observar a amostragem na operação, você pode executar esta consulta na análise:

```
    requests | summarize original_events = sum(itemCount), transmitted_events = count()
```


### <a name="automation"></a>Automatização

#### <a name="configuring-application-insights"></a>Configurando Application Insights

Você pode [escrever scripts do PowerShell](app/powershell.md) usando o Azure monitor de recursos para:

* Criar e atualizar Application Insights recursos.
* Defina o plano de preços.
* Obtenha a chave de instrumentação.
* Adicione um alerta de métrica.
* Adicione um teste de disponibilidade.

Você não pode configurar um relatório do Gerenciador de métricas ou configurar a exportação contínua.

#### <a name="querying-the-telemetry"></a>Consultando a telemetria

Use a [API REST](https://dev.applicationinsights.io/) para executar consultas de [análise](app/analytics.md) .

### <a name="how-can-i-set-an-alert-on-an-event"></a>Como posso definir um alerta em um evento?

Os alertas do Azure são apenas em métricas. Crie uma métrica personalizada que cruze um limite de valor sempre que o evento ocorrer. Em seguida, defina um alerta na métrica. Você receberá uma notificação sempre que a métrica cruzar o limite em qualquer direção; Você não receberá uma notificação até o primeiro cruzamento, não importa se o valor inicial é alto ou baixo; sempre há uma latência de alguns minutos.

### <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Há encargos de transferência de dados entre um aplicativo Web do Azure e Application Insights?

* Se seu aplicativo Web do Azure estiver hospedado em um data center em que há um ponto de extremidade de coleção Application Insights, não haverá encargos. 
* Se não houver nenhum ponto de extremidade de coleta no data center do host, a telemetria do aplicativo incorrerá em [encargos de saída do Azure](https://azure.microsoft.com/pricing/details/bandwidth/).

Isso não depende de onde o recurso de Application Insights está hospedado. Depende apenas da distribuição de nossos pontos de extremidade.

### <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>Posso enviar telemetria para o portal de Application Insights?

Recomendamos que você use nossos SDKs e use a [API do SDK](app/api-custom-events-metrics.md)do. Há variantes do SDK para várias [plataformas](app/platforms.md). Esses SDKs manipulam o buffer, a compactação, a limitação, as repetições e assim por diante. No entanto, o [esquema de ingestão](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/develop/Schema/PublicSchema) e o [protocolo de ponto de extremidade](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md) são públicos.

### <a name="can-i-monitor-an-intranet-web-server"></a>Posso monitorar um servidor Web de intranet?

Sim, mas será necessário permitir o tráfego para nossos serviços por meio de exceções de firewall ou redirecionamentos de proxy.
- `https://rt.services.visualstudio.com:443` QuickPulse 
- ApplicationIdprovider `https://dc.services.visualstudio.com:443` 
- `https://dc.services.visualstudio.com:443` TelemetryChannel 


Examine nossa lista completa de serviços e endereços IP [aqui](app/ip-addresses.md).

#### <a name="firewall-exception"></a>Exceção de firewall

Permitir que seu servidor Web envie telemetria para nossos pontos de extremidade. 

#### <a name="gateway-redirect"></a>Redirecionamento de gateway

Encaminhar o tráfego do servidor para um gateway em sua intranet, substituindo pontos de extremidade em sua configuração. Se essas propriedades de "ponto de extremidade" não estiverem presentes em sua configuração, essas classes usarão os valores padrão mostrados abaixo no exemplo ApplicationInsights. config. 

O gateway deve rotear o tráfego para o endereço base do nosso ponto de extremidade. Em sua configuração, substitua os valores padrão por `http://<your.gateway.address>/<relative path>`.


##### <a name="example-applicationinsightsconfig-with-default-endpoints"></a>Exemplo de ApplicationInsights. config com pontos de extremidade padrão:
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
> ApplicationIdprovider está disponível a partir de v 2.6.0.



#### <a name="proxy-passthrough"></a>Passagem de proxy

A passagem de proxy pode ser obtida com a configuração de um nível de máquina ou proxy de nível de aplicativo.
Para obter mais informações, consulte o artigo do dotnet em [defaultProxy](https://docs.microsoft.com/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings).
 
 Exemplo de Web. config:
 ```xml
<system.net>
    <defaultProxy>
      <proxy proxyaddress="http://xx.xx.xx.xx:yyyy" bypassonlocal="true"/>
    </defaultProxy>
</system.net>
```
 

### <a name="can-i-run-availability-web-tests-on-an-intranet-server"></a>Posso executar testes da Web de disponibilidade em um servidor de intranet?

Nossos [testes na Web](app/monitor-web-app-availability.md) são executados em pontos de presença que são distribuídos em todo o mundo. Há duas soluções:

* Porta do firewall-permita solicitações ao seu servidor [a partir da lista longa e alterada de agentes de teste na Web](app/ip-addresses.md).
* Escreva seu próprio código para enviar solicitações periódicas ao seu servidor de dentro de sua intranet. Você pode executar testes do Visual Studio Web para essa finalidade. O testador pode enviar os resultados para Application Insights usando a API API trackavailability ().

### <a name="how-long-does-it-take-for-telemetry-to-be-collected"></a>Quanto tempo leva para que a telemetria seja coletada?

A maioria dos dados Application Insights tem uma latência de menos de 5 minutos. Alguns dados podem levar mais tempo; geralmente arquivos de log maiores. Para obter mais informações, consulte o [SLA de Application insights](https://azure.microsoft.com/support/legal/sla/application-insights/v1_2/).



<!--Link references-->

[data]: app/data-retention-privacy.md
[platforms]: app/platforms.md
[start]: app/app-insights-overview.md
[windows]: app/app-insights-windows-get-started.md



## <a name="azure-monitor-for-containers"></a>Azure Monitor para contentores

Esta FAQ da Microsoft é uma lista de perguntas frequentes sobre Azure Monitor para contêineres. Se você tiver outras dúvidas sobre a solução, vá para o [Fórum de discussão](https://feedback.azure.com/forums/34192--general-feedback) e poste suas perguntas. Quando uma pergunta é colocada frequentemente, adicionamo-la a este artigo para que ele pode ser encontrado rapidamente e facilmente.

### <a name="i-dont-see-image-and-name-property-values-populated-when-i-query-the-containerlog-table"></a>Não vejo os valores de Propriedade Image e Name preenchidos quando eu consulta a tabela ContainerLog.

Para a versão do agente ciprod12042019 e posteriores, por padrão, essas duas propriedades não são preenchidas para cada linha de log para minimizar o custo incorrido nos dados de log coletados. Há duas opções para consultar a tabela que inclui essas propriedades com seus valores:

#### <a name="option-1"></a>Opção 1 

Ingresse outras tabelas para incluir esses valores de propriedade nos resultados.

Modifique suas consultas para incluir as propriedades Image e ImageTag da tabela ```ContainerInventory``` unindo a propriedade ContainerId. Você pode incluir a propriedade Name (como apareceu anteriormente na tabela ```ContainerLog```) do campo ContaineName da tabela KubepodInventory unindo a propriedade ContainerId. Essa é a opção recomendada.

O exemplo a seguir é uma consulta detalhada de exemplo que explica como obter esses valores de campo com junções.

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

Reabilitar a coleção para essas propriedades para cada linha de log de contêiner.

Se a primeira opção não for conveniente devido às alterações de consulta envolvidas, pode voltar a ativar a recolha destes campos, permitindo a definição ```log_collection_settings.enrich_container_logs``` no mapa de config do agente, tal como descrito nas definições de [configuração](insights/container-insights-agent-config.md)de recolha de dados .

> [!NOTE]
> A segunda opção não é recomendada com clusters grandes que têm mais de 50 nós porque ele gera chamadas de servidor de API de cada nó no cluster para executar esse enriquecimento. Essa opção também aumenta o tamanho dos dados para cada linha de log coletada.

### <a name="can-i-view-metrics-collected-in-grafana"></a>Posso exibir as métricas coletadas no Grafana?

Azure Monitor para contêineres dá suporte à exibição de métricas armazenadas em seu espaço de trabalho Log Analytics em painéis do Grafana. Fornecemos um modelo que você pode baixar do [repositório do painel](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) do Grafana para começar e fazer referência para ajudá-lo a aprender a consultar dados adicionais de seus clusters monitorados para visualizar em painéis de Grafana personalizados. 

### <a name="can-i-monitor-my-aks-engine-cluster-with-azure-monitor-for-containers"></a>Posso monitorar meu cluster do AKS-Engine com Azure Monitor para contêineres?

Azure Monitor para contêineres dá suporte ao monitoramento de cargas de trabalho de contêiner implantadas em cluster (s) AKS (anteriormente conhecido como ACS-Engine) hospedados no Azure. Para obter mais detalhes e uma visão geral das etapas necessárias para habilitar o monitoramento para esse cenário, consulte [usando Azure monitor para contêineres para AKs-Engine](https://github.com/microsoft/OMS-docker/tree/aks-engine).

### <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>Por que não vejo dados em meu espaço de trabalho do Log Analytics?

Se não for possível ver dados no espaço de trabalho Log Analytics em um determinado horário, você pode ter atingido o limite de 500 MB padrão ou o limite diário especificado para controlar a quantidade de dados a serem coletados diariamente. Quando o limite for atingido para o dia, a coleta de dados será interrompida e retomada somente no dia seguinte. Para examinar o uso de dados e atualizar para um tipo de preço diferente com base nos padrões de uso previstos, consulte [uso e custo de dados de log](platform/manage-cost-storage.md). 

### <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>Quais são os Estados do contêiner especificados na tabela ContainerInventory?

A tabela ContainerInventory contém informações sobre contêineres interrompidos e em execução. A tabela é populada por um fluxo de trabalho dentro do agente que consulta o Docker para todos os contêineres (em execução e parado) e encaminha esses dados para o espaço de trabalho Log Analytics.
 
### <a name="how-do-i-resolve-missing-subscription-registration-error"></a>Como fazer resolver erro de *registro de assinatura ausente* ?

Se você receber o erro **registro de assinatura ausente para Microsoft. OperationsManagement**, você poderá resolvê-lo registrando o provedor de recursos **Microsoft. OperationsManagement** na assinatura em que o espaço de trabalho está definido. A documentação sobre como fazer isso pode ser encontrada [aqui](../azure-resource-manager/templates/error-register-resource-provider.md).

### <a name="is-there-support-for-rbac-enabled-aks-clusters"></a>Há suporte para clusters AKS habilitados para RBAC?

A solução de monitoramento de contêiner não dá suporte a RBAC, mas tem suporte com Azure Monitor para contêineres. A página de detalhes da solução pode não mostrar as informações corretas nas folhas que mostram dados para esses clusters.

### <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Como fazer habilitar a coleta de log para contêineres no namespace do sistema Kube por meio de Helm?

A coleção de logs de contêineres no namespace Kube-System está desabilitada por padrão. A coleta de log pode ser habilitada definindo uma variável de ambiente no omsagent. Para obter mais informações, consulte a página [Azure monitor para contêineres](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers) github. 

### <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>Como fazer atualizar o omsagent para a versão de lançamento mais recente?

Para saber como atualizar o agente, consulte [Gerenciamento de agente](insights/container-insights-manage-agent.md).

### <a name="how-do-i-enable-multi-line-logging"></a>Como fazer habilitar o log de várias linhas?

Atualmente Azure Monitor para contêineres não dá suporte a log de várias linhas, mas há soluções alternativas disponíveis. Você pode configurar todos os serviços para gravar no formato JSON e, em seguida, Docker/Moby os gravará como uma única linha.

Por exemplo, você pode encapsular o log como um objeto JSON, conforme mostrado no exemplo abaixo, para um aplicativo node. js de exemplo:

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

Esses dados serão parecidos com o exemplo a seguir no Azure Monitor para logs quando você fizer a consulta:

```
LogEntry : ({“Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

Para obter uma visão detalhada do problema, examine o [link do GitHub](https://github.com/moby/moby/issues/22920)a seguir.

### <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>Como fazer resolver erros do Azure AD quando habilito logs dinâmicos? 

Você pode ver o seguinte erro: **a URL de resposta especificada na solicitação não corresponde às URLs de resposta configuradas para o aplicativo: ' < ID do aplicativo\>'** . A solução para solucioná-lo pode ser encontrada no artigo [como exibir dados de contêiner em tempo real com Azure monitor para contêineres](insights/container-insights-livedata-setup.md#configure-ad-integrated-authentication). 

### <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>Por que não posso atualizar o cluster após a integração?

Se, depois de habilitar Azure Monitor para contêineres para um cluster AKS, você excluirá o espaço de trabalho Log Analytics ao qual o cluster estava enviando seus dados, ao tentar atualizar o cluster, ele falhará. Para contornar isso, você precisará desabilitar o monitoramento e reabilitá-lo fazendo referência a um espaço de trabalho válido diferente em sua assinatura. Quando você tenta executar a atualização do cluster novamente, ele deve processar e concluir com êxito.  

### <a name="which-ports-and-domains-do-i-need-to-openwhitelist-for-the-agent"></a>Quais portas e domínios eu preciso abrir/Adicionar à lista de permissões para o agente?

Consulte os [requisitos de firewall de rede](insights/container-insights-onboard.md#network-firewall-requirements) para as informações de configuração de proxy e firewall necessárias para o agente em contêineres com o Azure, o governo dos EUA do Azure e as nuvens do Azure China 21vianet.

## <a name="azure-monitor-for-vms-preview"></a>Azure Monitor para VMs (versão prévia)
Esta FAQ da Microsoft é uma lista de perguntas frequentes sobre Azure Monitor para VMs. Se você tiver outras dúvidas sobre a solução, vá para o [Fórum de discussão](https://feedback.azure.com/forums/34192--general-feedback) e poste suas perguntas. Quando uma pergunta é colocada frequentemente, adicionamo-la a este artigo para que ele pode ser encontrado rapidamente e facilmente.

### <a name="can-i-onboard-to-an-existing-workspace"></a>Posso carregar um espaço de trabalho existente?
Se suas máquinas virtuais já estiverem conectadas a um espaço de trabalho Log Analytics, você poderá continuar a usar esse espaço de trabalho ao realizar a integração com o Azure Monitor para VMs, desde que ele esteja em uma das regiões com suporte listadas [aqui](insights/vminsights-enable-overview.md#prerequisites).

Ao realizar a integração, configuramos contadores de desempenho para o espaço de trabalho que fará com que todas as VMs que relatam dados para o espaço de trabalho comecem a coletar essas informações para exibição e análise no Azure Monitor para VMs.  Como resultado, você verá dados de desempenho de todas as VMs conectadas ao espaço de trabalho selecionado.  Os recursos de integridade e de mapa são habilitados somente para as VMs que você especificou para carregar.

Para obter mais informações sobre quais contadores de desempenho estão habilitados, consulte nosso artigo [habilitar visão geral](insights/vminsights-enable-overview.md#performance-counters-enabled) .

### <a name="can-i-onboard-to-a-new-workspace"></a>Posso carregar um novo espaço de trabalho? 
Se suas VMs não estiverem atualmente conectadas a um espaço de trabalho Log Analytics existente, você precisará criar um novo espaço de trabalho para armazenar seus dados. A criação de um novo espaço de trabalho padrão será feita automaticamente se você configurar uma única VM do Azure para Azure Monitor para VMs por meio do portal do Azure.

Se você optar por usar o método baseado em script, essas etapas serão abordadas no artigo [habilitar Azure monitor para VMs (visualização) usando o Azure PowerShell ou o modelo do Resource Manager](insights/vminsights-enable-at-scale-powershell.md) . 

### <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>O que devo fazer se minha VM já estiver se comunicando a um espaço de trabalho existente?
Se você já estiver coletando dados de suas máquinas virtuais, talvez já o tenha configurado para relatar dados a um espaço de trabalho Log Analytics existente.  Contanto que esse espaço de trabalho esteja em uma de nossas regiões com suporte, você pode habilitar Azure Monitor para VMs para esse espaço de trabalho já existente.  Se o espaço de trabalho que você já está usando não estiver em uma de nossas regiões com suporte, você não poderá integrar ao Azure Monitor para VMs no momento.  Estamos a trabalhar ativamente para oferecer suporte a mais regiões.

>[!NOTE]
>Configuramos contadores de desempenho para o espaço de trabalho que afeta todas as VMs que se reportam ao espaço de trabalho, independentemente de você ter optado por integrá-las ao Azure Monitor para VMs. Para obter mais detalhes sobre como os contadores de desempenho são configurados para o espaço de trabalho, consulte nossa [documentação](platform/data-sources-performance-counters.md). Para obter informações sobre os contadores configurados para Azure Monitor para VMs, consulte nosso artigo [habilitar Azure monitor para VMs](insights/vminsights-enable-overview.md#performance-counters-enabled) .  

### <a name="why-did-my-vm-fail-to-onboard"></a>Por que minha VM falhou ao carregar?
Ao integrar uma VM do Azure da portal do Azure, ocorrem as seguintes etapas:

* Uma área de trabalho do Log Analytics do padrão é criada, se que a opção tiver sido selecionada.
* Os contadores de desempenho são configurados para o espaço de trabalho selecionado. Se essa etapa falhar, você observará que alguns dos gráficos de desempenho e tabelas não estão mostrando dados para a VM que você integrou. Você pode corrigir isso executando o script do PowerShell documentado [aqui](insights/vminsights-enable-at-scale-powershell.md#enable-performance-counters).
* O agente de Log Analytics é instalado em VMs do Azure usando uma extensão de VM, se determinado for necessário.  
* O agente de dependência do mapa de Azure Monitor para VMs é instalado em VMs do Azure usando uma extensão, se determinado for necessário.  
* Azure Monitor componentes que dão suporte ao recurso de integridade são configurados, se necessário, e a VM é configurada para relatar dados de integridade.

Durante o processo integrado, verificamos o status em cada um dos itens acima para retornar um status de notificação para você no Portal. Configuração de área de trabalho e a instalação do agente normalmente demora 5 a 10 minutos. A exibição de dados de monitoramento e integridade no portal leva um adicional de 5 a 10 minutos.  

Se você iniciou a integração e confira mensagens indicando que a VM precisa ser integrada, aguarde até 30 minutos para que a VM conclua o processo. 

### <a name="i-only-enabled-azure-monitor-for-vms-why-do-i-see-all-my-vms-monitored-by-the-health-feature"></a>Habilitei apenas Azure Monitor para VMs, por que vejo todas as minhas VMs monitoradas pelo recurso de integridade?
O recurso de integridade é habilitado para todas as VMs que estão conectadas ao espaço de trabalho Log Analytics, mesmo quando a ação é iniciada para uma única VM.

### <a name="can-i-modify-the-schedule-for-when-health-criteria-evaluates-a-condition"></a>Posso modificar o agendamento de quando os critérios de integridade avaliam uma condição?
Não, o período de tempo e a frequência dos critérios de integridade não podem ser modificados com esta versão. 

### <a name="can-i-disable-health-criteria-for-a-condition-i-dont-need-to-monitor"></a>Posso desabilitar critérios de integridade para uma condição que não preciso monitorar?
Os critérios de integridade não podem ser desabilitados nesta versão.

### <a name="are-the-health-alert-severities-configurable"></a>As severidades de alerta de integridade são configuráveis?  
A severidade do alerta de integridade não pode ser modificada, só pode ser habilitada ou desabilitada. Além disso, algumas severidades de alerta são atualizadas com base no estado dos critérios de integridade. 

### <a name="if-i-reconfigure-the-settings-of-a-particular-health-criteria-can-it-be-scoped-to-a-specific-instance"></a>Se eu reconfigurar as configurações de um determinado critério de integridade, ele poderá ser definido como escopo para uma instância específica?  
Se você modificar qualquer configuração de uma instância de critério de integridade, todas as instâncias de critérios de integridade do mesmo tipo na VM do Azure serão modificadas. Por exemplo, se o limiar da instância de critério de estado de funcionamento do espaço livre de disco que corresponde ao disco lógico c: for modificado, este limite aplica-se a todos os outros discos lógicos que são detetados e monitorizados para a mesma VM.

### <a name="does-the-health-feature-monitor-logical-processors-and-cores"></a>O recurso de integridade monitora processadores e núcleos lógicos?
Não, os critérios de integridade de processador individual e de nível de processador lógico não estão incluídos para um Windows, apenas a utilização total da CPU é monitorada por padrão para avaliar efetivamente a pressão da CPU com base no número total de CPUs lógicas disponíveis para a VM do Azure. 

### <a name="are-all-health-criteria-thresholds-configurable"></a>Todos os limites de critérios de integridade são configuráveis?  
Os limites de critérios de estado de funcionamento que se destinam uma VM do Windows não são fáceis de serem modificados, porque os Estados de funcionamento estão definidos como *em execução* ou *disponível*. Quando consulta o estado de funcionamento do [API do Monitor de carga de trabalho](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components), ele exibe a *comparisonOperator* valor de **LessThan** ou **GreaterThan** com um *limiar* o valor de **4** para a entidade ou o serviço se:
   - Estado de funcionamento do serviço do cliente DNS – serviço não está em execução. 
   - Estado de funcionamento do serviço do cliente DHCP – serviço não está em execução. 
   - Estado de funcionamento do serviço RPC – serviço não está em execução. 
   - Estado de funcionamento do serviço do firewall de Windows – serviço não está em execução.
   - Estado de funcionamento do serviço do registo de eventos de Windows – serviço não está em execução. 
   - Estado de funcionamento do servidor service – serviço não está em execução. 
   - Funcionamento de serviço de gestão remota do Windows – serviço não está em execução. 
   - Erro de sistema de ficheiros ou corrupção – disco lógico não está disponível.

Limiares para os seguintes critérios de estado de funcionamento do Linux não são fáceis de serem modificados, porque o respetivo estado de funcionamento já está definido como *true*. Apresenta o estado de funcionamento a *comparisonOperator* com um valor **LessThan** e *limiar* valor de **1** quando consultados a partir do Carga de trabalho monitorização o API para a entidade, dependendo de seu contexto:
   - Estado do disco lógico – o disco lógico não está online / disponíveis
   - Estado do disco – o disco não está online / disponíveis
   - Estado do adaptador de rede - placa de rede está desativado

### <a name="how-do-i-modify-alerts-that-are-included-with-the-health-feature"></a>Como fazer modificar alertas incluídos com o recurso de integridade?
Regras de alerta que estão definidas para cada critério de estado de funcionamento não são apresentadas no portal do Azure. Pode ativar ou desativar um alerta de estado de funcionamento apenas na regra a [API do Monitor de carga de trabalho](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components). Além disso, você não pode atribuir um [grupo de ação Azure monitor](platform/action-groups.md) para alertas de integridade no portal do Azure. Você só pode usar a API de configuração de notificação para configurar um grupo de ações a ser disparado sempre que um alerta de integridade for acionado. Atualmente, pode atribuir grupos de ação em relação a uma VM para que todos os *alertas de estado de funcionamento* disparado contra o acionador VM, os mesmos grupos de ação. Ao contrário dos alertas do Azure tradicionais, não há conceito de um grupo de ação separada para cada regra de alerta de estado de funcionamento. Além disso, apenas os grupos de ação que estão configurados para fornecer e-mail ou notificações por SMS são suportados quando são acionados alertas de estado de funcionamento. 

### <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>Não vejo algumas ou quaisquer dados nos gráficos de desempenho para a minha VM
Se você não vir dados de desempenho na tabela de disco ou em alguns dos gráficos de desempenho, os contadores de desempenho não poderão ser configurados no espaço de trabalho. Para resolver, execute o seguinte [script do PowerShell](insights/vminsights-enable-at-scale-powershell.md#enable-with-powershell).

### <a name="how-is-azure-monitor-for-vms-map-feature-different-from-service-map"></a>Como Azure Monitor para VMs recurso de mapa é diferente do Mapa do Serviço?
O recurso de mapa de Azure Monitor para VMs é baseado em Mapa do Serviço, mas tem as seguintes diferenças:

* O modo de exibição de mapa pode ser acessado na folha da VM e de Azure Monitor para VMs em Azure Monitor.
* As conexões no mapa agora são clicáveis e exibem uma exibição dos dados de métrica de conexão no painel lateral da conexão selecionada.
* Há uma nova API que é usada para criar os mapas para dar melhor suporte a mapas mais complexos.
* As VMs monitoradas agora estão incluídas no nó do grupo de clientes e o gráfico de rosca mostra a proporção de máquinas virtuais monitoradas vs não monitoradas no grupo.  Ele também pode ser usado para filtrar a lista de computadores quando o grupo é expandido.
* As máquinas virtuais monitoradas agora estão incluídas nos nós do grupo de portas do servidor e o gráfico de rosca mostra a proporção de computadores monitorados vs não monitorados no grupo.  Ele também pode ser usado para filtrar a lista de computadores quando o grupo é expandido.
* O estilo de mapa foi atualizado para ser mais consistente com o mapa de aplicativos do Application insights.
* Os painéis do lado foram atualizados e não têm o conjunto completo de integração que foram compatíveis com Mapa do Serviço Gerenciamento de Atualizações, Controle de Alterações, segurança e central de serviços. 
* A opção de escolher grupos e máquinas para mapear foi atualizada e agora dá suporte a assinaturas, grupos de recursos, conjuntos de dimensionamento de máquinas virtuais do Azure e serviços de nuvem.
* Você não pode criar novos grupos de computadores Mapa do Serviço no recurso de mapa de Azure Monitor para VMs.  

### <a name="why-do-my-performance-charts-show-dotted-lines"></a>Por que motivo é que meu gráficos de desempenho mostra linhas pontilhadas?
Isso pode ocorrer por alguns motivos.  Em casos em que há uma lacuna na coleta de dados, descrevemos as linhas como pontilhadas.  Se você tiver modificado a frequência de amostragem de dados para os contadores de desempenho habilitados (a configuração padrão é coletar dados a cada 60 segundos), você poderá ver linhas pontilhadas no gráfico se escolher um intervalo de tempo estreito para o gráfico e sua frequência de amostragem for menor que o tamanho do Bucket usado no gráfico (por exemplo, a frequência de amostragem é a cada 10 minutos e cada bucket no gráfico é de 5 minutos).  Escolher um intervalo de tempo maior para exibir deve fazer com que as linhas do gráfico sejam exibidas como linhas sólidas em vez de pontos nesse caso.

### <a name="are-groups-supported-with-azure-monitor-for-vms"></a>São os grupos suportados com o Azure Monitor para VMs?
Sim, depois de instalar o agente de dependência, coletamos informações das VMs para exibir grupos com base na assinatura, grupo de recursos, conjuntos de dimensionamento de máquinas virtuais e serviços de nuvem.  Se você estiver usando Mapa do Serviço e tiver criado grupos de computadores, eles também serão exibidos.  Os grupos de computadores também serão exibidos no filtro grupos se você os tiver criado para o espaço de trabalho que está sendo exibido. 

### <a name="how-do-i-see-the-details-for-what-is-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>Como fazer Ver os detalhes do que está conduzindo a linha do 95 º percentil nos gráficos de desempenho agregados?
Por padrão, a lista é classificada para mostrar as VMs que têm o valor mais alto para o percentil de 95 º para a métrica selecionada, exceto para o gráfico de memória disponível, que mostra as máquinas com o menor valor do quinto percentil.  Clicar no gráfico abrirá o modo de exibição de **lista N superior** com a métrica apropriada selecionada.

### <a name="how-does-the-map-feature-handle-duplicate-ips-across-different-vnets-and-subnets"></a>Como o recurso de mapa lida com IPs duplicados entre diferentes vnets e sub-redes?
Se você estiver duplicando intervalos de IP com VMs ou conjuntos de dimensionamento de máquinas virtuais do Azure em sub-redes e vnets, isso poderá fazer com que Azure Monitor para VMs mapa exiba informações incorretas. Esse é um problema conhecido e estamos investigando opções para melhorar essa experiência.

### <a name="does-map-feature-support-ipv6"></a>O recurso de mapa dá suporte a IPv6?
Atualmente, o recurso de mapa dá suporte apenas para IPv4 e estamos investigando suporte para IPv6. Também há suporte para IPv4 que é túnel dentro do IPv6.

### <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-the-map-is-difficult-to-view"></a>Ao carregar um mapa para um grupo de recursos ou outro grupo grande, é difícil exibir o mapa
Embora tenhamos feito melhorias para mapear para lidar com configurações grandes e complexas, percebemos que um mapa pode ter muitos nós, conexões e nó funcionando como um cluster.  Estamos empenhados em continuar melhorar o suporte para aumentar a escalabilidade.   

### <a name="why-does-the-network-chart-on-the-performance-tab-look-different-than-the-network-chart-on-the-azure-vm-overview-page"></a>Por que o gráfico de rede na guia desempenho é diferente do gráfico de rede na página Visão geral da VM do Azure?

A página de visão geral de uma VM do Azure exibe gráficos com base na medição de atividade do host na VM convidada.  Para o gráfico de rede na visão geral da VM do Azure, ele exibe apenas o tráfego de rede que será cobrado.  Isso não inclui o tráfego de rede entre redes virtuais.  Os dados e gráficos mostrados para Azure Monitor para VMs baseiam-se nos dados da VM convidada e o gráfico de rede exibe todo o tráfego TCP/IP que é de entrada e saída para essa VM, incluindo a rede entre redes virtuais.

### <a name="how-is-response-time-measured-for-data-stored-in-vmconnection-and-displayed-in-the-connection-panel-and-workbooks"></a>Como o tempo de resposta é medido para os dados armazenados em VMConnection e exibidos no painel de conexão e nas pastas de trabalho?

O tempo de resposta é uma aproximação. Como não instrumento o código do aplicativo, não sabemos realmente quando uma solicitação começa e quando chega a resposta. Em vez disso, observamos os dados que estão sendo enviados em uma conexão e, em seguida, os dados voltam para essa conexão. Nosso agente controla esses envios e recebe e tenta emparelhar: uma sequência de envios, seguida por uma sequência de receives, é interpretada como um par de solicitação/resposta. O intervalo entre essas operações é o tempo de resposta. Incluirá a latência de rede e o tempo de processamento do servidor.

Essa aproximação funciona bem para protocolos que são baseados em solicitação/resposta: uma única solicitação sai da conexão e uma única resposta chega. Esse é o caso para HTTP (S) (sem Pipeline), mas não é atendido para outros protocolos.

### <a name="are-their-limitations-if-i-am-on-the-log-analytics-free-pricing-plan"></a>Suas limitações se eu estiver no plano de preços de Log Analytics gratuito?
Se você tiver configurado Azure Monitor com um espaço de trabalho Log Analytics usando o tipo de preço *gratuito* , Azure monitor para VMs recurso de mapa dará suporte apenas a cinco computadores conectados conectados ao espaço de trabalho. Se você tiver cinco VMs conectadas a um espaço de trabalho gratuito, desconecte uma das VMs e, posteriormente, conecte uma nova VM, a nova VM não será monitorada e refletida na página do mapa.  

Sob essa condição, você será avisado com a opção **Experimente agora** quando abrir a VM e selecionar **insights (versão prévia)** no painel esquerdo, mesmo depois que ela já tiver sido instalada na VM.  No entanto, as opções não são solicitadas como normalmente ocorrerão se essa VM não estivesse integrada a Azure Monitor para VMs. 


## <a name="next-steps"></a>Passos seguintes
Se sua pergunta não for respondida aqui, você poderá consultar os fóruns a seguir para obter perguntas e respostas adicionais.

- [Log Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights)
- [Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/home?forum=ApplicationInsights)

Para obter comentários gerais sobre Azure Monitor, visite o [Fórum de comentários](https://feedback.azure.com/forums/34192--general-feedback).
