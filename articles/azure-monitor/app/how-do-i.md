---
title: Como é que eu... in Azure Application Insights [ Insights de Aplicação Azure ] Microsoft Docs
description: FAQ em Insights de Aplicação.
ms.topic: conceptual
ms.date: 04/04/2017
ms.openlocfilehash: 5b65087c361911f0714723c315e0b7f7e9bb74e6
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77663862"
---
# <a name="how-do-i--in-application-insights"></a>Como... no Application Insights?
## <a name="get-an-email-when-"></a>Recebe um e-mail quando...
### <a name="email-if-my-site-goes-down"></a>E-mail se o meu site cair
Detete um [teste web de disponibilidade.](../../azure-monitor/app/monitor-web-app-availability.md)

### <a name="email-if-my-site-is-overloaded"></a>E-mail se o meu site estiver sobrecarregado
Destome um [alerta](../../azure-monitor/app/alerts.md) sobre o tempo de **resposta do Servidor**. Um limiar entre 1 e 2 segundos deve funcionar.

![](./media/how-do-i/030-server.png)

A sua aplicação também pode mostrar sinais de tensão devolvendo códigos de falha. Deseme um alerta sobre **pedidos falhados**.

Se quiser definir um alerta sobre **as exceções**do Servidor, poderá ter de fazer [alguma configuração adicional](../../azure-monitor/app/asp-net-exceptions.md) para ver os dados.

### <a name="email-on-exceptions"></a>E-mail sobre exceções
1. [Configurar a monitorização da exceção](../../azure-monitor/app/asp-net-exceptions.md)
2. [Desema um alerta](../../azure-monitor/app/alerts.md) sobre a métrica da contagem de exceções

### <a name="email-on-an-event-in-my-app"></a>E-mail em um evento na minha app
Suponho que gostaria de receber um e-mail quando um evento específico ocorre. A Aplicação Insights não fornece esta instalação diretamente, mas pode [enviar um alerta quando uma métrica atravessa um limiar](../../azure-monitor/app/alerts.md).

Os alertas podem ser definidos em [métricas personalizadas,](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric)embora não em eventos personalizados. Escreva um código para aumentar uma métrica quando o evento ocorrer:

    telemetry.TrackMetric("Alarm", 10);

ou:

    var measurements = new Dictionary<string,double>();
    measurements ["Alarm"] = 10;
    telemetry.TrackEvent("status", null, measurements);

Como os alertas têm dois estados, tem de enviar um baixo valor quando considera que o alerta terminou:

    telemetry.TrackMetric("Alarm", 0.5);

Crie um gráfico em [explorador métrico](../../azure-monitor/app/metrics-explorer.md) para ver o seu alarme:

![](./media/how-do-i/010-alarm.png)

Agora, preste um alerta para o fogo quando a métrica for superior a um valor médio por um curto período:

![](./media/how-do-i/020-threshold.png)

Detete o período médio no mínimo.

Receberá e-mails quando a métrica for para cima e abaixo do limiar.

Alguns pontos a considerar:

* Um alerta tem dois estados ("alerta" e "saudável"). O estado só é avaliado quando uma métrica é recebida.
* Um e-mail só é enviado quando o estado muda. É por isso que tens de enviar métricas de alto e baixo valor.
* Para avaliar o alerta, a média é tomada dos valores recebidos durante o período anterior. Isto ocorre sempre que uma métrica é recebida, para que os e-mails possam ser enviados com mais frequência do que o período que definiu.
* Uma vez que os e-mails são enviados em "alerta" e "saudável", é melhor considerar repensar o seu evento de um tiro como uma condição de dois estados. Por exemplo, em vez de um evento de "trabalho concluído", tem uma condição de "trabalho em andamento", onde recebe e-mails no início e no fim de um trabalho.

### <a name="set-up-alerts-automatically"></a>Configurar alertas automaticamente
[Use o PowerShell para criar novos alertas](../../azure-monitor/app/alerts.md#automation)

## <a name="use-powershell-to-manage-application-insights"></a>Use powerShell para gerir insights de aplicação
* [Criar novos recursos](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#creating-a-resource-automatically)
* [Criar novos alertas](../../azure-monitor/app/alerts.md#automation)

## <a name="separate-telemetry-from-different-versions"></a>Telemetria separada de diferentes versões

* Múltiplas funções numa aplicação: Utilize um único recurso de Insights de Aplicação e filtre em [cloud_Rolename](../../azure-monitor/app/app-map.md).
* Separar as versões de desenvolvimento, teste e lançamento: Utilize diferentes recursos de Insights de Aplicação. Pegue as chaves de instrumentação da web.config. [Saiba mais](../../azure-monitor/app/separate-resources.md)
* Reportando versões de construção: Adicione uma propriedade usando um inicializador de telemetria. [Saiba mais](../../azure-monitor/app/separate-resources.md)

## <a name="monitor-backend-servers-and-desktop-apps"></a>Monitor de backend servidores e aplicativos de ambiente de trabalho
[Utilize o módulo SDK do Servidor do Windows](../../azure-monitor/app/windows-desktop.md).

## <a name="visualize-data"></a>Visualizar os dados
#### <a name="dashboard-with-metrics-from-multiple-apps"></a>Dashboard com métricas de várias aplicações
* No [Metric Explorer,](../../azure-monitor/app/metrics-explorer.md)personalize o seu gráfico e guarde-o como favorito. Prende-o ao tablier Azure.

#### <a name="dashboard-with-data-from-other-sources-and-application-insights"></a>Dashboard com dados de outras fontes e Insights de Aplicação
* [Exportar telemetria para Power BI](../../azure-monitor/app/export-power-bi.md ).

Ou

* Utilize o SharePoint como painel de instrumentos, exibindo dados em partes web do SharePoint. [Utilize a exportação contínua e o Stream Analytics para exportar para a SQL](../../azure-monitor/app/code-sample-export-sql-stream-analytics.md).  Utilize o PowerView para examinar a base de dados e crie uma peça web do SharePoint para o PowerView.

<a name="search-specific-users"></a>

### <a name="filter-out-anonymous-or-authenticated-users"></a>Filtrar utilizadores anónimos ou autenticados
Se os seus utilizadores iniciarem o seu insto, pode definir o [ID autenticado](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users)do utilizador . (Não acontece automaticamente.)

Então, pode:

* Pesquisar iDs de utilizador específicos

![](./media/how-do-i/110-search.png)

* Filtrar métricas para utilizadores anónimos ou autenticados

![](./media/how-do-i/115-metrics.png)

## <a name="modify-property-names-or-values"></a>Modificar nomes ou valores de propriedade
Criar um [filtro](../../azure-monitor/app/api-filtering-sampling.md#filtering). Isto permite modificar ou filtrar a telemetria antes de ser enviada da sua aplicação para A aplicação Insights.

## <a name="list-specific-users-and-their-usage"></a>Listar utilizadores específicos e a sua utilização
Se apenas quiser [pesquisar por utilizadores específicos,](#search-specific-users)pode definir o ID do [utilizador autenticado](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users).

Se quiser uma lista de utilizadores com dados como as páginas que analisam ou com que frequência entram, tem duas opções:

* [Detete o ID do utilizador autenticado,](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users) [exporte para uma base de dados](../../azure-monitor/app/code-sample-export-sql-stream-analytics.md) e utilize ferramentas adequadas para analisar os dados dos seus utilizadores.
* Se tiver apenas um pequeno número de utilizadores, envie eventos ou métricas personalizados, utilizando os dados de interesse como valor métrico ou nome de evento, e definindo o ID do utilizador como uma propriedade. Para analisar as vistas da página, substitua a chamada padrão do JavaScriptPageView. Para analisar a telemetria do lado do servidor, utilize um inicializador de telemetria para adicionar o ID do utilizador a toda a telemetria do servidor. Em seguida, pode filtrar e segmentar métricas e pesquisas no ID do utilizador.

## <a name="reduce-traffic-from-my-app-to-application-insights"></a>Reduzir o tráfego da minha app para Apps Insights
* Em [ApplicationInsights.config,](../../azure-monitor/app/configuration-with-applicationinsights-config.md)desative quaisquer módulos de que não necessite, tal como o contador de desempenho.
* Utilizar [amostragem e filtragem](../../azure-monitor/app/api-filtering-sampling.md) no SDK.
* Nas suas páginas web, limite o número de chamadas do Ajax reportadas para cada visualização de página. No corte do guião após `instrumentationKey:...`, insira: `,maxAjaxCallsPerView:3` (ou um número adequado).
* Se estiver a utilizar o [TrackMetric,](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric)calcule o agregado de lotes de valores métricos antes de enviar o resultado. Há uma sobrecarga de TrackMetric() que fornece para isso.

Saiba mais sobre [preços e quotas.](../../azure-monitor/app/pricing.md)

## <a name="disable-telemetry"></a>Desativar a telemetria
Para **parar e iniciar dinamicamente** a recolha e transmissão de telemetria a partir do servidor:

### <a name="aspnet-classic-applications"></a>ASP.NET Aplicações Clássicas

```csharp
    using  Microsoft.ApplicationInsights.Extensibility;

    TelemetryConfiguration.Active.DisableTelemetry = true;
```

### <a name="other-applications"></a>Outras aplicações
Não é aconselhável utilizar `TelemetryConfiguration.Active` singleton nas aplicações de consola ou ASP.NET Core.
se criou `TelemetryConfiguration` instância - `DisableTelemetry` para `true`.

Para ASP.NET aplicações Core pode aceder `TelemetryConfiguration` exemplo utilizando ASP.NET injeção de [dependência do Núcleo](/aspnet/core/fundamentals/dependency-injection/). Por favor, encontre mais detalhes no Artigo de [Aplicações Do ASP.NET Core.](../../azure-monitor/app/asp-net-core.md)

## <a name="disable-selected-standard-collectors"></a>Desativar os colecionadores padrão selecionados
Pode desativar os colecionadores padrão (por exemplo, contadores de desempenho, pedidos HTTP ou dependências)

* **ASP.NET aplicações** - Eliminar ou comentar as linhas relevantes em [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)
* **ASP.NET aplicações Core** - Siga as opções de configuração de módulos de telemetria no [ApplicationInsights ASP.NET Core](../../azure-monitor/app/asp-net-core.md#configuring-or-removing-default-telemetrymodules)

## <a name="view-system-performance-counters"></a>Ver contadores de desempenho do sistema
Entre as métricas que pode mostrar no explorador de métricas estão um conjunto de contadores de desempenho do sistema. Há uma lâmina pré-definida intitulada **Servidores** que exibe vários deles.

![Abra o seu recurso Insights de Aplicação e clique em Servidores](./media/how-do-i/121-servers.png)

### <a name="if-you-see-no-performance-counter-data"></a>Se não vir nenhum contador de dados de desempenho
* **Servidor IIS** na sua própria máquina ou num VM. [Instalar monitor de estado](../../azure-monitor/app/monitor-performance-live-website-now.md).
* **Web site azure** - ainda não apoiamos contadores de desempenho. Existem várias métricas que pode obter como uma parte padrão do painel de controlo do site Azure.
* **Servidor Unix** - [Instalar recolhido](../../azure-monitor/app/java-collectd.md)

### <a name="to-display-more-performance-counters"></a>Para exibir mais contadores de desempenho
* Em primeiro lugar, [adicione um novo gráfico](../../azure-monitor/app/metrics-explorer.md) e veja se o contador está no conjunto básico que oferecemos.
* Caso contrário, [adicione o contador ao conjunto recolhido pelo módulo do contador de desempenho](../../azure-monitor/app/performance-counters.md).
