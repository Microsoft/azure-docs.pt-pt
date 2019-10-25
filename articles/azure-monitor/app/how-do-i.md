---
title: Como fazer... no Aplicativo Azure insights | Microsoft Docs
description: Perguntas frequentes em Application Insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/04/2017
ms.openlocfilehash: 28881403e4938376cc1912227bdff51aa5f069cf
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72817364"
---
# <a name="how-do-i--in-application-insights"></a>Como... no Application Insights?
## <a name="get-an-email-when-"></a>Receber um email quando...
### <a name="email-if-my-site-goes-down"></a>Enviar email se meu site ficar inativo
Defina um [teste da Web de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md).

### <a name="email-if-my-site-is-overloaded"></a>Enviar email se meu site estiver sobrecarregado
Definir um [alerta](../../azure-monitor/app/alerts.md) no **tempo de resposta do servidor**. Um limite entre 1 e 2 segundos deve funcionar.

![](./media/how-do-i/030-server.png)

Seu aplicativo também pode mostrar sinais de tensão retornando códigos de falha. Definir um alerta sobre **solicitações com falha**.

Se você quiser definir um alerta sobre **exceções de servidor**, talvez seja necessário fazer [algumas configurações adicionais](../../azure-monitor/app/asp-net-exceptions.md) para ver os dados.

### <a name="email-on-exceptions"></a>Email sobre exceções
1. [Configurar o monitoramento de exceção](../../azure-monitor/app/asp-net-exceptions.md)
2. [Definir um alerta](../../azure-monitor/app/alerts.md) na métrica de contagem de exceção

### <a name="email-on-an-event-in-my-app"></a>Email em um evento em meu aplicativo
Vamos supor que você gostaria de receber um email quando ocorrer um evento específico. Application Insights não fornece esse recurso diretamente, mas pode [enviar um alerta quando uma métrica ultrapassar um limite](../../azure-monitor/app/alerts.md).

Os alertas podem ser definidos em [métricas personalizadas](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric), embora não sejam eventos personalizados. Escreva algum código para aumentar uma métrica quando o evento ocorrer:

    telemetry.TrackMetric("Alarm", 10);

or

    var measurements = new Dictionary<string,double>();
    measurements ["Alarm"] = 10;
    telemetry.TrackEvent("status", null, measurements);

Como os alertas têm dois Estados, você precisa enviar um valor baixo ao considerar que o alerta foi encerrado:

    telemetry.TrackMetric("Alarm", 0.5);

Crie um gráfico no [Metrics Explorer](../../azure-monitor/app/metrics-explorer.md) para ver seu alarme:

![](./media/how-do-i/010-alarm.png)

Agora, defina um alerta para disparar quando a métrica estiver acima de um valor médio por um curto período:

![](./media/how-do-i/020-threshold.png)

Defina o período de média para o mínimo.

Você receberá emails quando a métrica ficar acima e abaixo do limite.

Alguns pontos a considerar:

* Um alerta tem dois Estados ("alerta" e "íntegro"). O estado é avaliado somente quando uma métrica é recebida.
* Um email é enviado somente quando o estado é alterado. É por isso que você precisa enviar métricas de valor alto e baixo.
* Para avaliar o alerta, a média é obtida dos valores recebidos no período anterior. Isso ocorre toda vez que uma métrica é recebida, para que os emails possam ser enviados com mais frequência do que o período definido.
* Como os emails são enviados em "alerta" e "íntegro", talvez você queira considerar a possibilidade de repensar o evento de uma imagem como uma condição de dois Estados. Por exemplo, em vez de um evento "trabalho concluído", tenha uma condição de "trabalho em andamento", em que você recebe emails no início e no final de um trabalho.

### <a name="set-up-alerts-automatically"></a>Configurar alertas automaticamente
[Usar o PowerShell para criar novos alertas](../../azure-monitor/app/alerts.md#automation)

## <a name="use-powershell-to-manage-application-insights"></a>Usar o PowerShell para gerenciar o Application Insights
* [Criar novos recursos](../../azure-monitor/app/powershell-script-create-resource.md)
* [Criar novos alertas](../../azure-monitor/app/alerts.md#automation)

## <a name="separate-telemetry-from-different-versions"></a>Separar telemetria de versões diferentes

* Várias funções em um aplicativo: Use um único recurso de Application Insights e filtre em [cloud_Rolename](../../azure-monitor/app/app-map.md).
* Separando versões de desenvolvimento, teste e lançamento: Use diferentes recursos de Application Insights. Pegue as chaves de instrumentação em Web. config. [Saiba mais](../../azure-monitor/app/separate-resources.md)
* Relatando versões de compilação: Adicione uma propriedade usando um inicializador de telemetria. [Saiba mais](../../azure-monitor/app/separate-resources.md)

## <a name="monitor-backend-servers-and-desktop-apps"></a>Monitorar servidores de back-end e aplicativos da área de trabalho
[Use o módulo SDK do Windows Server](../../azure-monitor/app/windows-desktop.md).

## <a name="visualize-data"></a>Visualizar os dados
#### <a name="dashboard-with-metrics-from-multiple-apps"></a>Painel com métricas de vários aplicativos
* No [Gerenciador de métricas](../../azure-monitor/app/metrics-explorer.md), personalize seu gráfico e salve-o como um favorito. Fixe-o no painel do Azure.

#### <a name="dashboard-with-data-from-other-sources-and-application-insights"></a>Painel com dados de outras fontes e Application Insights
* [Exportar telemetria para Power bi](../../azure-monitor/app/export-power-bi.md ).

Ou

* Use o SharePoint como seu painel, exibindo dados em Web Parts do SharePoint. [Use a exportação e a Stream Analytics contínua para exportar para o SQL](../../azure-monitor/app/code-sample-export-sql-stream-analytics.md).  Use PowerView para examinar o banco de dados e criar uma Web Part do SharePoint para PowerView.

<a name="search-specific-users"></a>

### <a name="filter-out-anonymous-or-authenticated-users"></a>Filtrar usuários anônimos ou autenticados
Se os usuários entrarem, você poderá definir a [ID de usuário autenticado](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users). (Isso não acontece automaticamente.)

Em seguida, você pode:

* Pesquisar IDs de usuário específicas

![](./media/how-do-i/110-search.png)

* Filtrar métricas para usuários anônimos ou autenticados

![](./media/how-do-i/115-metrics.png)

## <a name="modify-property-names-or-values"></a>Modificar valores ou nomes de propriedade
Crie um [filtro](../../azure-monitor/app/api-filtering-sampling.md#filtering). Isso permite que você modifique ou filtre a telemetria antes que ela seja enviada do seu aplicativo para Application Insights.

## <a name="list-specific-users-and-their-usage"></a>Listar usuários específicos e seu uso
Se você quiser apenas [Pesquisar usuários específicos](#search-specific-users), poderá definir a [ID de usuário autenticado](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users).

Se você quiser uma lista de usuários com dados como as páginas que eles examinam ou com que frequência eles fazem logon, você tem duas opções:

* [Defina a ID de usuário autenticado](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users), [exporte para um banco de dados](../../azure-monitor/app/code-sample-export-sql-stream-analytics.md) e use ferramentas adequadas para analisar os dados do usuário ali.
* Se você tiver apenas um pequeno número de usuários, envie eventos personalizados ou métricas, usando os dados de interesse como o valor da métrica ou o nome do evento e definindo a ID de usuário como uma propriedade. Para analisar exibições de página, substitua a chamada trackPageView do JavaScript padrão. Para analisar a telemetria do lado do servidor, use um inicializador de telemetria para adicionar a ID de usuário a toda a telemetria do servidor. Em seguida, você pode filtrar e segmentar métricas e pesquisas na ID de usuário.

## <a name="reduce-traffic-from-my-app-to-application-insights"></a>Reduzir o tráfego do meu aplicativo para Application Insights
* Em [ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md), desabilite todos os módulos dos quais você não precisa, como o coletor do contador de desempenho.
* Use a [amostragem e a filtragem](../../azure-monitor/app/api-filtering-sampling.md) no SDK.
* Em suas páginas da Web, limite o número de chamadas AJAX relatadas para cada exibição de página. No trecho de script após `instrumentationKey:...`, insira: `,maxAjaxCallsPerView:3` (ou um número adequado).
* Se você estiver usando [TrackMetric](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric), Compute a agregação de lotes de valores de métrica antes de enviar o resultado. Há uma sobrecarga de TrackMetric () que o fornece para isso.

Saiba mais sobre [preços e cotas](../../azure-monitor/app/pricing.md).

## <a name="disable-telemetry"></a>Desabilitar telemetria
Para **parar e iniciar dinamicamente** a coleta e a transmissão da telemetria do servidor:

### <a name="aspnet-classic-applications"></a>Aplicativos clássicos ASP.NET

```csharp
    using  Microsoft.ApplicationInsights.Extensibility;

    TelemetryConfiguration.Active.DisableTelemetry = true;
```

### <a name="other-applications"></a>Outros aplicativos
Não é recomendável usar `TelemetryConfiguration.Active` singleton no console ou em aplicativos ASP.NET Core.
Se você criou `TelemetryConfiguration` instância por conta própria, defina `DisableTelemetry` como `true`.

Para ASP.NET Core aplicativos, você pode acessar `TelemetryConfiguration` instância usando [ASP.NET Core injeção de dependência](/aspnet/core/fundamentals/dependency-injection/). Encontre mais detalhes no artigo [ApplicationInsights for ASP.NET Core Applications](../../azure-monitor/app/asp-net-core.md) .

## <a name="disable-selected-standard-collectors"></a>Desabilitar coletores padrão selecionados
Você pode desabilitar os coletores padrão (por exemplo, contadores de desempenho, solicitações HTTP ou dependências)

* **Aplicativos ASP.net** -exclua ou comente as linhas relevantes em [ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)
* **ASP.NET Core aplicativos** – siga as opções de configuração dos módulos de telemetria no [ApplicationInsights ASP.NET Core](../../azure-monitor/app/asp-net-core.md#configuring-or-removing-default-telemetrymodules)

## <a name="view-system-performance-counters"></a>Exibir contadores de desempenho do sistema
Entre as métricas que você pode mostrar no Metrics Explorer estão um conjunto de contadores de desempenho do sistema. Há uma folha predefinida intitulada **servidores** que exibe vários deles.

![Abra o recurso Application Insights e clique em servidores](./media/how-do-i/121-servers.png)

### <a name="if-you-see-no-performance-counter-data"></a>Se você não vir dados do contador de desempenho
* **Servidor IIS** em seu próprio computador ou em uma VM. [Instale o status monitor](../../azure-monitor/app/monitor-performance-live-website-now.md).
* **Site do Azure** -ainda não há suporte para contadores de desempenho. Há várias métricas que você pode obter como uma parte padrão do painel de controle do site do Azure.
* **Servidor Unix** - [instalar a coleta](../../azure-monitor/app/java-collectd.md)

### <a name="to-display-more-performance-counters"></a>Para exibir mais contadores de desempenho
* Primeiro, [adicione um novo gráfico](../../azure-monitor/app/metrics-explorer.md) e veja se o contador está no conjunto básico que oferecemos.
* Caso contrário, [adicione o contador ao conjunto coletado pelo módulo contador de desempenho](../../azure-monitor/app/performance-counters.md).
