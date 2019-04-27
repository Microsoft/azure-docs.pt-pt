---
title: Explorar métricas no Azure Application Insights | Documentos da Microsoft
description: Como interpretar os gráficos no Explorador de métricas e como personalizar os painéis do Explorador de métricas.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 1f471176-38f3-40b3-bc6d-3f47d0cbaaa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: mbullwin
ms.openlocfilehash: 5c659ca2f40d47450227d16963499a6b27c9e313
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60700892"
---
# <a name="exploring-metrics-in-application-insights"></a>Explorar métricas no Application Insights
Métricas no [Application Insights] [ start] são valores de medida e contagens de eventos que são enviadas em telemetria da sua aplicação. Ajudam a detetar problemas de desempenho e ver as tendências na forma como a aplicação está a ser utilizada. Há uma grande variedade de métricas padrão e também pode criar seus próprios eventos e métricas personalizadas.

> [!NOTE]
> Este artigo descreve a experiência do Explorador de métricas clássicas que atualmente é preterida e, eventualmente, será descontinuada. Recomendamos que verifique a nova experiência que está descrito [este artigo](../platform/metrics-charts.md).

Contagens de métricas e eventos são apresentadas nos gráficos de valores agregados, como somas, médias ou contagens.

Este é um conjunto de exemplo de gráficos:

![](./media/metrics-explorer/01-overview.png)

Encontre gráficos de métricas em qualquer lugar no portal do Application Insights. Na maioria dos casos, elas podem ser personalizadas e pode adicionar mais gráficos para o painel. No painel Descrição geral, clique em por meio de gráficos mais detalhados (que têm cargos como "Servidores") ou clique em **Explorador de métricas** para abrir um novo painel onde pode criar gráficos personalizados.

## <a name="time-range"></a>Intervalo de tempo
Pode alterar o intervalo de tempo abrangido pelo gráficos ou grelhas no qualquer painel.

![Abra o painel de descrição geral da sua aplicação no portal do Azure](./media/metrics-explorer/03-range.png)

Se estiver à espera de alguns dados que ainda não apareceram ainda, clique em Atualizar. Os gráficos atualizam-se em intervalos, mas os intervalos são maiores para intervalos de tempo maior. Pode demorar algum tempo para os dados vêm através do pipeline de análise num gráfico.

Para aplicar zoom no parte de um gráfico, arraste sobre ele:

![Arraste em toda parte de um gráfico.](./media/metrics-explorer/12-drag.png)

Clique no botão desfazer Zoom restaurá-lo.

## <a name="granularity-and-point-values"></a>Valores de granularidade e ponto
Passe o mouse sobre o gráfico para apresentar os valores das métricas nesse momento.

![Paire o rato sobre um gráfico](./media/metrics-explorer/02-focus.png)

O valor da métrica num momento específico é agregado ao longo do intervalo de amostragem anterior.

O intervalo de amostragem ou "granularidade" é apresentada na parte superior do painel.

![O cabeçalho de um painel.](./media/metrics-explorer/11-grain.png)

Pode ajustar a granularidade no painel de intervalo de tempo:

![O cabeçalho de um painel.](./media/metrics-explorer/grain.png)

Nas duas granularidades disponíveis dependem do intervalo de tempo selecionado. As granularidades explícitas são alternativas à granularidade "automática" para o intervalo de tempo.


## <a name="editing-charts-and-grids"></a>Edição de gráficos e grades
Para adicionar um novo gráfico para o painel:

![No Explorador de métricas, escolha adicionar gráfico](./media/metrics-explorer/04-add.png)

Selecione **editar** num gráfico novo ou existente para editar o que é mostrado:

![Selecione uma ou mais métricas](./media/metrics-explorer/08-select.png)

Pode exibir mais de uma métrica num gráfico, apesar de existirem restrições sobre as combinações que podem ser apresentadas em conjunto. Assim que escolher uma métrica, alguns dos outros estão desativadas.

Se codificou [métricas personalizadas] [ track] na sua aplicação (chamadas para TrackMetric e TrackEvent) estes serão apresentados aqui.

## <a name="segment-your-data"></a>Segmentar os seus dados
Pode dividir uma métrica pela propriedade - por exemplo, para comparar as vistas de página em clientes com sistemas operativos diferentes.

Selecione um gráfico ou grelha, mudar de agrupamento e escolha uma propriedade para agrupar por:

![Selecionar agrupamento no conjunto Selecione uma propriedade em Group By](./media/metrics-explorer/15-segment.png)

> [!NOTE]
> Quando utilizar o agrupamento, os tipos de área e gráfico de barras fornecem uma exibição empilhada. Isso é adequado em que o método de agregação é a soma. Mas onde o tipo de agregação é média, escolha os tipos de exibição de linha ou de grade.
>
>

Se codificou [métricas personalizadas] [ track] na sua aplicação e incluem valores de propriedade, poderá selecionar a propriedade na lista.

O gráfico é demasiado pequeno para dados segmentados? Ajuste a altura:

![Ajusta o controlo de deslize](./media/metrics-explorer/18-height.png)

## <a name="aggregation-types"></a>Tipos de agregação
A legenda no lado por padrão, normalmente, mostra o valor agregado ao longo do período do gráfico. Se passar o mouse sobre o gráfico, mostra o valor nesse momento.

Cada ponto de dados no gráfico é uma agregação dos valores de dados recebidos no intervalo de amostragem anterior ou "granularidade". A granularidade é mostrada na parte superior do painel e varia de acordo com a escala global temporal do gráfico.

As métricas podem ser agregadas de formas diferentes:

* **Contagem de** é uma contagem dos eventos recebidos no intervalo de amostragem. Ele é usado para eventos como pedidos. Variações na altura do gráfico indica variações na taxa a que os eventos ocorrem. Mas tenha em atenção que o valor numérico é alterado quando altera o intervalo de amostragem.
* **Soma** soma os valores de todos os pontos de dados recebidos ao longo do intervalo de amostragem, ou o período do gráfico.
* **Média** divide a soma pelo número de pontos de dados recebidos ao longo do intervalo.
* **Exclusivo** contagens são utilizadas para contagens de utilizadores e contas. Ao longo do intervalo de amostragem, ou ao longo do período do gráfico, a figura mostra a contagem de utilizadores diferentes visto nesse período de tempo.
* **%** -versões de percentagem de cada agregação são utilizadas apenas com gráficos segmentados. O total adiciona sempre o até 100% e o gráfico mostra a contribuição relativa dos diferentes componentes de um total.

    ![Agregação de percentagem](./media/metrics-explorer/percentage-aggregation.png)

### <a name="change-the-aggregation-type"></a>Alterar o tipo de agregação

![Editar o gráfico e, em seguida, selecione a agregação](./media/metrics-explorer/05-aggregation.png)

O método predefinido para cada uma é mostrado quando cria um novo gráfico ou quando todas as métricas são desmarcadas:

![Desmarcar todas as métricas para ver as predefinições](./media/metrics-explorer/06-total.png)

## <a name="pin-y-axis"></a>Eixo y de PIN 
Por predefinição, um gráfico mostra os valores de eixo Y a partir de zero até valores máximos no intervalo de dados, para lhe dar uma representação visual de quantum dos valores. Mas em alguns casos mais do que o quantum pode ser interessante inspecionar visualmente pequenas alterações em valores. Para personalizações, como esse uso o eixo y variar de funcionalidade de edição para afixar o valor de mínimo ou máximo do eixo y no local desejado.
Clique na caixa de verificação de "Definições avançadas" para abrir o definições de intervalo do eixo y

![Clique em configurações avançadas, selecione o intervalo personalizado e especifique os valores de máximo de minutos](./media/metrics-explorer/y-axis-range.png)

## <a name="filter-your-data"></a>Filtrar os seus dados
Para ver apenas as métricas de um conjunto selecionado de valores de propriedade:

![Clique em filtro, expanda uma propriedade e verificar alguns valores](./media/metrics-explorer/19-filter.png)

Se não selecionar quaisquer valores de uma propriedade específica, é o mesmo como selecionar todas elas: não existe nenhum filtro nessa propriedade.

Tenha em atenção as contagens de eventos em conjunto com cada valor de propriedade. Quando seleciona os valores de uma propriedade, as contagens juntamente com outros valores de propriedade são ajustadas.

Filtros de aplicam a todos os gráficos num painel. Se quiser diferentes filtros aplicados a gráficos de diferentes, crie e guarde os painéis das métricas diferentes. Se quiser, pode afixar gráficos de diferentes painéis para o dashboard, para que pode vê-los em conjunto com outro.

### <a name="remove-bot-and-web-test-traffic"></a>Remover o tráfego de teste da web e de bot
Utilize o filtro **tráfego Real ou sintético** e verifique **Real**.

Também pode filtrar por **origem do tráfego sintético**.

### <a name="to-add-properties-to-the-filter-list"></a>Para adicionar propriedades para a lista de filtro
Deseja filtrar a telemetria numa categoria à sua escolha? Por exemplo, talvez divide os seus utilizadores em categorias diferentes e pretender segmentar os seus dados por estas categorias.

[Criar sua própria propriedade](../../azure-monitor/app/api-custom-events-metrics.md#properties). Defini-lo [inicializador de telemetria](../../azure-monitor/app/api-custom-events-metrics.md#defaults) -lo a aparecer em toda a telemetria - incluindo a padrão telemetria enviada pelo diferentes módulos SDK.

## <a name="edit-the-chart-type"></a>Editar o tipo de gráfico
Tenha em atenção que pode alternar entre grades e gráficos:

![Selecione uma grade ou gráfico, em seguida, escolha um tipo de gráfico](./media/metrics-explorer/16-chart-grid.png)

## <a name="save-your-metrics-blade"></a>Guardar o seu painel de métricas
Depois de criar alguns gráficos, salvá-los como um favorito. Pode escolher se pretende partilhá-lo com outros membros da Equipe, se utilizar uma conta institucional.

![Escolha o favorito](./media/metrics-explorer/21-favorite-save.png)

Para ver mais uma vez, o painel **aceda ao painel de descrição geral** e abra favoritos:

![No painel de descrição geral, escolha Favoritos](./media/metrics-explorer/22-favorite-get.png)

Se tiver escolhido o intervalo de tempo relativo, quando salva, o painel será atualizado com as métricas mais recentes. Se tiver escolhido o intervalo de tempo absoluto, ela mostrará os mesmos dados cada vez.

## <a name="reset-the-blade"></a>O painel de reposição
Se editar um painel, mas, em seguida, gostaria de voltar para o original conjunto guardado, basta clicar em reposição.

![Nos botões na parte superior do Explorador de métricas](./media/metrics-explorer/17-reset.png)

## <a name="live-metrics-stream"></a>Live Metrics Stream

Para obter uma visão muito mais imediata da sua telemetria, abra [Live Stream](live-stream.md). A maioria das métricas demorar alguns minutos a aparecer, devido ao processo de agregação. Por outro lado, as métricas ao vivo são otimizadas de baixa latência. 

## <a name="set-alerts"></a>Definir alertas
Para ser notificado por correio eletrónico dos valores invulgares de qualquer métrica, adicione um alerta. Pode escolher para enviar o e-mail para os administradores de conta, ou para endereços de e-mail específico.

![No Explorador de métricas, escolher regras de alerta, adicionar alerta](./media/metrics-explorer/appinsights-413setMetricAlert.png)

[Saiba mais sobre alertas][alerts].


## <a name="continuous-export"></a>Exportação Contínua
Se pretender que os dados exportados continuamente para que pode processá-lo externamente, considere utilizar [exportação contínua](../../azure-monitor/app/export-telemetry.md).

### <a name="power-bi"></a>Power BI
Se pretender que as exibições ainda mais avançadas dos seus dados, pode [exportar para o Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx).

## <a name="analytics"></a>Análise
[Análise](../../azure-monitor/app/analytics.md) é uma maneira mais versátil para analisar a sua telemetria, através de uma linguagem de consulta poderosa. Utilize-o se pretender combinar, os resultados de métricas de computação ou executar uma análise profunda de desempenho recente da sua aplicação. 

A partir de um gráfico de métricas, pode clicar no ícone de análise para ir diretamente para a consulta do Analytics equivalente.

## <a name="troubleshooting"></a>Resolução de problemas
*Não vejo todos os dados no meu gráfico.*

* Filtros de aplicam a todos os gráficos no painel. Certifique-se de que, embora se está a focar um gráfico, não defina um filtro que exclua todos os dados em outro.

    Se pretender definir filtros diferentes em gráficos de diferentes, criá-las nos diferentes painéis, salvá-los separados como favoritos. Se quiser, pode afixá-los ao dashboard para que pode vê-los em conjunto com outro.
* Se um gráfico é agrupar por uma propriedade que não está definida na métrica, em seguida, haverá nada no gráfico. Experimente limpar "Agrupar por" ou escolha uma propriedade de agrupamento diferente.
* Dados de desempenho (CPU, a taxa de e/s e assim por diante) está disponível para os serviços de web de Java, aplicativos de desktop do Windows, [IIS aplicações web e serviços se instalar o monitor de estado](../../azure-monitor/app/monitor-performance-live-website-now.md), e [serviços Cloud do Azure](../../azure-monitor/app/app-insights-overview.md). Não está disponível para Web sites do Azure.

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Passos Seguintes
* [Monitorizar a utilização com o Application Insights](../../azure-monitor/app/usage-overview.md)
* [Utilizar a pesquisa de diagnóstico](../../azure-monitor/app/diagnostic-search.md)

<!--Link references-->

[alerts]: ../../azure-monitor/app/alerts.md
[start]: ../../azure-monitor/app/app-insights-overview.md
[track]: ../../azure-monitor/app/api-custom-events-metrics.md
