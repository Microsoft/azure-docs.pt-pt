---
title: Como é que eu... in Azure Application Insights / Microsoft Docs
description: FAQ em Insights de Aplicação.
ms.topic: conceptual
ms.date: 04/04/2017
ms.openlocfilehash: 134089f4df8f80147182835ca8746322c1de7e50
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87319257"
---
# <a name="how-do-i--in-application-insights"></a>Como... no Application Insights?
## <a name="get-an-email-when-"></a>Receba um e-mail quando...
### <a name="email-if-my-site-goes-down"></a>E-mail se o meu site cair
Desaponhe um [teste web de disponibilidade.](./monitor-web-app-availability.md)

### <a name="email-if-my-site-is-overloaded"></a>E-mail se o meu site estiver sobrecarregado
Desateia um [alerta](../platform/alerts-log.md) no **tempo de resposta do Servidor**. Um limiar entre 1 e 2 segundos deve funcionar.

![Screenshot que mostra como definir um alerta no tempo de resposta do Servidor.](./media/how-do-i/030-server.png)

A sua aplicação também pode mostrar sinais de tensão ao devolver códigos de falha. Desabar os **pedidos falhados.**

Se pretender definir um alerta sobre **as exceções do Servidor,** poderá ter de fazer [alguma configuração adicional](./asp-net-exceptions.md) para ver os dados.

### <a name="email-on-exceptions"></a>E-mail sobre exceções
1. [Configurar monitorização de exceções](./asp-net-exceptions.md)
2. [Desateia um alerta](../platform/alerts-log.md) na métrica da contagem de exceções

### <a name="email-on-an-event-in-my-app"></a>E-mail em um evento na minha app
Suponhamos que gostaria de receber um e-mail quando ocorrer um evento específico. A Application Insights não fornece esta facilidade diretamente, mas pode [enviar um alerta quando uma métrica cruza um limiar](../platform/alerts-log.md).

Os alertas podem ser definidos em [métricas personalizadas,](./api-custom-events-metrics.md#trackmetric)embora não eventos personalizados. Escreva um código para aumentar uma métrica quando o evento ocorrer:

```csharp
telemetry.TrackMetric("Alarm", 10);
```

ou:

```csharp
var measurements = new Dictionary<string,double>();
measurements ["Alarm"] = 10;
telemetry.TrackEvent("status", null, measurements);
```

Como os alertas têm dois estados, tem de enviar um valor baixo quando se considera que o alerta terminou:

```csharp
telemetry.TrackMetric("Alarm", 0.5);
```

Crie um gráfico no [explorador métrico](../platform/metrics-charts.md) para ver o seu alarme:

![Screenshot que mostra como criar um gráfico no explorador métrico para ver o seu alarme.](./media/how-do-i/010-alarm.png)

Agora, coloque um alerta para o fogo quando a métrica for acima de um valor médio por um curto período:

![Screenshot que mostra como definir um alerta para o fogo quando a métrica vai acima do valor médio por um curto período de tempo.](./media/how-do-i/020-threshold.png)

Desa esta altura o período médio é mínimo.

Receberá e-mails tanto quando a métrica for abaixo do limiar.

Alguns pontos a considerar:

* Um alerta tem dois estados ("alerta" e "saudável"). O estado só é avaliado quando uma métrica é recebida.
* Um e-mail é enviado apenas quando o estado muda. É por isso que tens de enviar métricas de alto e baixo valor.
* Para avaliar o alerta, a média é tomada dos valores recebidos durante o período anterior. Isto ocorre sempre que uma métrica é recebida, para que os e-mails possam ser enviados com mais frequência do que o período definido.
* Uma vez que os e-mails são enviados em "alerta" e "saudável", talvez deva considerar o seu evento de um tiro como uma condição de dois estados. Por exemplo, em vez de um evento de "trabalho concluído", tem uma condição de "trabalho em progresso", onde recebe e-mails no início e no fim de um trabalho.

### <a name="set-up-alerts-automatically"></a>Configurar alertas automaticamente
[Use o PowerShell para criar novos alertas](../platform/alerts-log.md)

## <a name="use-powershell-to-manage-application-insights"></a>Use powerShell para gerir insights de aplicações
* [Criar novos recursos](./create-new-resource.md#creating-a-resource-automatically)
* [Criar novos alertas](../platform/alerts-log.md)

## <a name="separate-telemetry-from-different-versions"></a>Telemetria separada de diferentes versões

* Múltiplas funções numa aplicação: Utilize um único recurso Application Insights e filtre [cloud_Rolename](./app-map.md).
* Separando as versões de desenvolvimento, teste e lançamento: Utilize diferentes recursos de Insights de Aplicação. Pegue as chaves de instrumentação web.config. [Saiba mais](./separate-resources.md)
* Reportando versões de construção: Adicione uma propriedade usando um inicializador de telemetria. [Saiba mais](./separate-resources.md)

## <a name="monitor-backend-servers-and-desktop-apps"></a>Monitorize servidores de backend e aplicações de ambiente de trabalho
[Utilize o módulo SDK do Servidor do Windows](./windows-desktop.md).

## <a name="visualize-data"></a>Visualizar os dados
#### <a name="dashboard-with-metrics-from-multiple-apps"></a>Painel de instrumentos com métricas de várias apps
* No [explorador métrico,](../platform/metrics-charts.md)personalize a sua carta e guarde-a como favorita. Prende-o ao painel Azure.

#### <a name="dashboard-with-data-from-other-sources-and-application-insights"></a>Painel de instrumentos com dados de outras fontes e Insights de Aplicações
* [Exportar telemetria para Power BI](./export-power-bi.md).

Ou

* Utilize o SharePoint como o seu dashboard, exibindo dados em partes web do SharePoint. [Utilize exportação contínua e Stream Analytics para exportar para a SQL](./code-sample-export-sql-stream-analytics.md).  Utilize o PowerView para examinar a base de dados e crie uma peça web SharePoint para o PowerView.

<a name="search-specific-users"></a>

### <a name="filter-out-anonymous-or-authenticated-users"></a>Filtrar utilizadores anónimos ou autenticados
Se os seus utilizadores iniciarem sôms, pode definir o ID do [utilizador autenticado.](./api-custom-events-metrics.md#authenticated-users) (Não acontece automaticamente.)

Em seguida, pode:

* Pesquisa em IDs específicos do utilizador

![Screenshot que mostra as opções de pesquisa em IDs de utilizador específicos.](./media/how-do-i/110-search.png)

* Filtrar métricas para utilizadores anónimos ou autenticados

![Screenshot que mostra metrixs filtrantes para utilizadores anónimos ou autenticados.](./media/how-do-i/115-metrics.png)

## <a name="modify-property-names-or-values"></a>Modificar nomes ou valores de propriedade
Criar um [filtro](./api-filtering-sampling.md#filtering). Isto permite modificar ou filtrar a telemetria antes de ser enviada da sua aplicação para o Application Insights.

## <a name="list-specific-users-and-their-usage"></a>Listar utilizadores específicos e sua utilização
Se apenas pretender [pesquisar utilizadores específicos,](#search-specific-users)pode definir o ID do [utilizador autenticado.](./api-custom-events-metrics.md#authenticated-users)

Se quiser uma lista de utilizadores com dados como as páginas que olham ou com que frequência fazem login, tem duas opções:

* [Desemaça o ID do utilizador autenticado,](./api-custom-events-metrics.md#authenticated-users) [exporte para uma base de dados](./code-sample-export-sql-stream-analytics.md) e utilize ferramentas adequadas para analisar os dados do utilizador.
* Se tiver apenas um pequeno número de utilizadores, envie eventos ou métricas personalizados, utilizando os dados de interesse como o valor métrico ou nome do evento, e definindo o ID do utilizador como uma propriedade. Para analisar as visualizações da página, substitua a chamada padrão javaScript TrackPageView. Para analisar a telemetria do lado do servidor, utilize um inicializador de telemetria para adicionar o ID do utilizador a toda a telemetria do servidor. Em seguida, pode filtrar e segmentar métricas e pesquisas no ID do utilizador.

## <a name="reduce-traffic-from-my-app-to-application-insights"></a>Reduza o tráfego da minha app para o Application Insights
* Em [ApplicationInsights.config, ](./configuration-with-applicationinsights-config.md)desative os módulos de que não necessita, tal o contador de desempenho.
* Utilize [amostragem e filtragem](./api-filtering-sampling.md) no SDK.
* Nas suas páginas web, limite o número de chamadas do Ajax reportadas para cada visualização de página. No corte do guião `instrumentationKey:...` depois, insira: `,maxAjaxCallsPerView:3` (ou um número adequado).
* Se estiver a utilizar [trackMetric,](./api-custom-events-metrics.md#trackmetric)calcule o agregado de lotes de valores métricos antes de enviar o resultado. Há uma sobrecarga de TrackMetric que prevê isso.

Saiba mais sobre [preços e quotas.](./pricing.md)

## <a name="disable-telemetry"></a>Desativar a telemetria
Para **parar e iniciar dinamicamente** a recolha e transmissão de telemetria a partir do servidor:

### <a name="aspnet-classic-applications"></a>aplicações ASP.NET Classic

```csharp
using  Microsoft.ApplicationInsights.Extensibility;

TelemetryConfiguration.Active.DisableTelemetry = true;
```

### <a name="other-applications"></a>Outras aplicações
Não é aconselhável utilizar `TelemetryConfiguration.Active` singleton na consola ou ASP.NET aplicações Core.
se criou `TelemetryConfiguration` o exemplo por si mesmo - definido para `DisableTelemetry` `true` .

Para ASP.NET aplicações Core pode aceder `TelemetryConfiguration` a instâncias utilizando [ASP.NET injeção de dependência do Núcleo](/aspnet/core/fundamentals/dependency-injection/). Por favor, encontre mais detalhes em [AplicaçõesInsights para ASP.NET artigo de aplicações Core.](./asp-net-core.md)

## <a name="disable-selected-standard-collectors"></a>Desativar os colecionadores padrão selecionados
Pode desativar os colecionadores padrão (por exemplo, contadores de desempenho, pedidos HTTP ou dependências)

* **ASP.NET aplicações** - Eliminar ou comentar as linhas relevantes em [ApplicationInsights.config](./configuration-with-applicationinsights-config.md)
* **ASP.NET Aplicações Core** - Siga as opções de configuração de módulos de telemetria em [ApplicationInsights ASP.NET Core](./asp-net-core.md#configuring-or-removing-default-telemetrymodules)

## <a name="view-system-performance-counters"></a>Ver contadores de desempenho do sistema
Entre as métricas que pode mostrar no explorador de métricas estão um conjunto de contadores de desempenho do sistema. Há uma lâmina predefinida intitulada **Servers** que exibe vários deles.

![Abra o seu recurso 'Insights de Aplicações' e clique em Servidores](./media/how-do-i/121-servers.png)

### <a name="if-you-see-no-performance-counter-data"></a>Se não vir nenhum dado de contador de desempenho
* **Servidor IIS** na sua própria máquina ou num VM. [Instalar o Monitor de Estado](./monitor-performance-live-website-now.md).
* **Site Azure** - ainda não suportamos contadores de desempenho. Existem várias métricas que pode obter como parte padrão do painel de controlo do site Azure.
* **Servidor Unix**  -  [Instalar colecionado](./java-collectd.md)

### <a name="to-display-more-performance-counters"></a>Para exibir mais contadores de desempenho
* Primeiro, [adicione um novo gráfico](../platform/metrics-charts.md) e veja se o contador está no conjunto básico que oferecemos.
* Caso contrário, [adicione o contador ao conjunto recolhido pelo módulo contador de desempenho](./performance-counters.md).

