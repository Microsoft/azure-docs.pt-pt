---
title: Como faço para... no Application Insights do Azure | Documentos da Microsoft
description: Perguntas frequentes no Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 48b2b644-92e4-44c3-bc14-068f1bbedd22
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2017
ms.author: mbullwin
ms.openlocfilehash: 8cee346a45cd20e7dd677fd7f2efed5500175598
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2018
ms.locfileid: "47096399"
---
# <a name="how-do-i--in-application-insights"></a>Como... no Application Insights?
## <a name="get-an-email-when-"></a>Receber um e-mail quando...
### <a name="email-if-my-site-goes-down"></a>E-mail se o meu site ficar inativo
Definir um [teste da web de disponibilidade](app-insights-monitor-web-app-availability.md).

### <a name="email-if-my-site-is-overloaded"></a>Se o meu site está sobrecarregado de e-mail
Definir um [alerta](app-insights-alerts.md) nos **tempo de resposta do servidor**. Um limiar entre 1 e 2 segundos deve funcionar.

![](./media/app-insights-how-do-i/030-server.png)

A aplicação também poderá mostrar sinais de enfraquecer retornando códigos de falha. Definir um alerta num **pedidos falhados**.

Se quiser definir um alerta num **exceções de servidor**, poderá ter de fazer [alguma configuração adicional](app-insights-asp-net-exceptions.md) para ver os dados.

### <a name="email-on-exceptions"></a>Exceções de e-mail
1. [Configurar a monitorização de exceção](app-insights-asp-net-exceptions.md)
2. [Definir um alerta](app-insights-alerts.md) na exceção contagem de métrica

### <a name="email-on-an-event-in-my-app"></a>Num evento na minha aplicação de e-mail
Vamos supor que gostaria de receber um e-mail quando ocorre um evento específico. O Application Insights não fornece esse recurso diretamente, mas pode [enviar um alerta quando uma métrica ultrapassa um limiar](app-insights-alerts.md).

Os alertas podem ser definidos no [métricas personalizadas](app-insights-api-custom-events-metrics.md#trackmetric), embora os eventos não personalizados. Escreva alguns códigos para aumentar uma métrica quando o evento ocorre:

    telemetry.TrackMetric("Alarm", 10);

Ou:

    var measurements = new Dictionary<string,double>();
    measurements ["Alarm"] = 10;
    telemetry.TrackEvent("status", null, measurements);

Como os alertas têm dois Estados, terá de enviar um valor baixo, quando considera o alerta tem de terminar:

    telemetry.TrackMetric("Alarm", 0.5);

Criar um gráfico no [Explorador de métricas](app-insights-metrics-explorer.md) para ver o alarme:

![](./media/app-insights-how-do-i/010-alarm.png)

Agora, defina um alerta ser lançado quando a métrica ultrapassarem um valor mid durante um curto período:

![](./media/app-insights-how-do-i/020-threshold.png)

Defina o período médio para o mínimo.

Irá receber e-mails quando a métrica fica acima e abaixo do limiar.

Alguns pontos a considerar:

* Um alerta tem dois Estados ("alert" e "integridade"). O estado é avaliado apenas quando uma métrica é recebida.
* Uma mensagem de e-mail é enviada apenas quando o estado é alterado. É por que precisa enviar ambos elevada e métricas de baixo valor.
* Para avaliar o alerta, a média é removida dos valores recebidos durante o período anterior. Isto ocorre sempre que uma métrica é recebida, para que as mensagens de e-mail podem ser enviadas com mais frequência do que o período que definir.
* Uma vez que os e-mails são enviados no "alert" e "integridade", talvez queira considerar a pensar novamente o seu evento da como uma condição de dois Estados. Por exemplo, em vez de um evento de "tarefa concluída", tem uma condição de "tarefa em curso", onde pode obter mensagens de correio eletrónico no início e no final de uma tarefa.

### <a name="set-up-alerts-automatically"></a>Configure alertas automaticamente
[Utilize o PowerShell para criar novos alertas](app-insights-alerts.md#automation)

## <a name="use-powershell-to-manage-application-insights"></a>Utilize o PowerShell para gerir o Application Insights
* [Criar novos recursos](app-insights-powershell-script-create-resource.md)
* [Criar novos alertas](app-insights-alerts.md#automation)

## <a name="separate-telemetry-from-different-versions"></a>Telemetria separado de diferentes versões

* Várias funções num aplicativo: utilizar um único recurso do Application Insights e filtre os cloud_Rolename. [Saiba mais](app-insights-monitor-multi-role-apps.md)
* Separar o desenvolvimento, teste e versões de lançamento: utilizar diferentes recursos do Application Insights. Escolha a segurança das chaves de instrumentação do Web. config. [Saiba mais](app-insights-separate-resources.md)
* Relatório de versões de compilação: adicionar uma propriedade com um inicializador de telemetria. [Saiba mais](app-insights-separate-resources.md)

## <a name="monitor-backend-servers-and-desktop-apps"></a>Monitorar os servidores de back-end e os aplicativos de desktop
[Utilizar o módulo SDK do Windows Server](app-insights-windows-desktop.md).

## <a name="visualize-data"></a>Visualizar os dados
#### <a name="dashboard-with-metrics-from-multiple-apps"></a>Dashboard com métricas de várias aplicações
* Na [Explorador de métricas](app-insights-metrics-explorer.md), personalizar o gráfico e guarde-o como um favorito. Afixá-la ao dashboard do Azure.

#### <a name="dashboard-with-data-from-other-sources-and-application-insights"></a>Dashboard com dados de outras origens e o Application Insights
* [Exportar a telemetria para o Power BI](app-insights-export-power-bi.md).

Ou

* Use o SharePoint como dashboard, exibir dados em peças web do SharePoint. [Utilizar a exportação contínua e o Stream Analytics para exportar para o SQL](app-insights-code-sample-export-sql-stream-analytics.md).  Utilize PowerView para examinar a base de dados e criar uma web part do SharePoint para PowerView.

<a name="search-specific-users"></a>

### <a name="filter-out-anonymous-or-authenticated-users"></a>Filtrar utilizadores anônimos ou autenticados
Se os seus utilizadores iniciarem sessão, pode definir o [autenticado o id de utilizador](app-insights-api-custom-events-metrics.md#authenticated-users). (Isso não acontece automaticamente.)

Em seguida, pode:

* Pesquisar ids de utilizador específico

![](./media/app-insights-how-do-i/110-search.png)

* Filtrar métricas para os utilizadores de anônimos ou autenticados

![](./media/app-insights-how-do-i/115-metrics.png)

## <a name="modify-property-names-or-values"></a>Modificar os nomes das propriedades ou valores
Criar uma [filtro](app-insights-api-filtering-sampling.md#filtering). Isto permite-lhe modificar ou filtrar telemetria antes do envio da sua aplicação para o Application Insights.

## <a name="list-specific-users-and-their-usage"></a>Lista de utilizadores específicos e seu uso
Se só quer [pesquisa para utilizadores específicos](#search-specific-users), pode definir o [autenticado o id de utilizador](app-insights-api-custom-events-metrics.md#authenticated-users).

Se pretender que uma lista de utilizadores com os dados, como quais são as páginas olham para ou a frequência com que iniciar sessão, tem duas opções:

* [Id do conjunto de utilizador autenticado](app-insights-api-custom-events-metrics.md#authenticated-users), [exportar para uma base de dados](app-insights-code-sample-export-sql-stream-analytics.md) e usar ferramentas adequadas para analisar os seus dados de utilizador existe.
* Se tiver apenas um pequeno número de utilizadores, envie eventos personalizados ou métricas, usando os dados de interesse como o nome de evento ou valor métrico e definindo o id de utilizador como uma propriedade. Para analisar as vistas de página, substitua a chamada de trackPageView JavaScript padrão. Para analisar a telemetria de lado do servidor, utilize um inicializador de telemetria para adicionar o id de utilizador para toda a telemetria server. Pode, em seguida, filtrar e segmentar métricas e pesquisas sobre o id de utilizador.

## <a name="reduce-traffic-from-my-app-to-application-insights"></a>Reduzir o tráfego do meu aplicativo para o Application Insights
* Na [applicationinsights. config](app-insights-configuration-with-applicationinsights-config.md), desative quaisquer módulos não precisar, o coletor de contador de desempenho desse tipo.
* Uso [amostragem e filtragem](app-insights-api-filtering-sampling.md) no SDK.
* Nas suas páginas da web, limite o número de chamadas Ajax comunicado para cada vista de página. No fragmento de script após `instrumentationKey:...` , insira: `,maxAjaxCallsPerView:3` (ou um número adequado).
* Se estiver a utilizar [TrackMetric](app-insights-api-custom-events-metrics.md#trackmetric), a agregação de lotes de valores de métrica de computação antes de enviar o resultado. Há uma sobrecarga de trackmetric () que fornece para isso.

Saiba mais sobre [preços e quotas](app-insights-pricing.md).

## <a name="disable-telemetry"></a>Desativar a telemetria
Para **dinamicamente parar e iniciar** a recolha e a transmissão de telemetria do servidor:

```

    using  Microsoft.ApplicationInsights.Extensibility;

    TelemetryConfiguration.Active.DisableTelemetry = true;
```



Para **desativar recoletores padrão selecionadas** – por exemplo, contadores de desempenho, pedidos de HTTP ou dependências - eliminar ou comente as linhas relevantes na [applicationinsights. config](app-insights-api-custom-events-metrics.md). Pode fazer isso, por exemplo, se pretender enviar os seus próprios dados TrackRequest.

## <a name="view-system-performance-counters"></a>Contadores de desempenho do sistema de vista
Entre as métricas que podem mostrar no Explorador de métricas é um conjunto de sistema, contadores de desempenho. Há um painel predefinido intitulado **servidores** que apresenta várias delas.

![Abra o recurso do Application Insights e clique em servidores](./media/app-insights-how-do-i/121-servers.png)

### <a name="if-you-see-no-performance-counter-data"></a>Se vir que não existem dados de contador de desempenho
* **Servidor IIS** no seu computador ou numa VM. [Instale o Monitor de estado](app-insights-monitor-performance-live-website-now.md).
* **Web site do Azure** -não é suportada ainda contadores de desempenho. Existem várias métricas, que pode obter como parte padrão do painel de controlo do web site do Azure.
* **Servidor UNIX** - [instale collectd](app-insights-java-collectd.md)

### <a name="to-display-more-performance-counters"></a>Para apresentar mais contadores de desempenho
* Primeiro, [adicione um novo gráfico](app-insights-metrics-explorer.md) e ver se o contador está no conjunto básico que oferecemos.
* Caso contrário, [adicionar o contador para o conjunto recolhido pelo módulo de contador de desempenho](app-insights-performance-counters.md).
