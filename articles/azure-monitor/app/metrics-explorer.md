---
title: Explorando métricas no Aplicativo Azure insights | Microsoft Docs
description: Como interpretar gráficos no Gerenciador de métricas e como personalizar as folhas do Metrics Explorer.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 01/22/2019
ms.openlocfilehash: b0831ff500ba4cbe71dae6251fd960f6c96c0fe5
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820782"
---
# <a name="exploring-metrics-in-application-insights"></a>Explorando métricas no Application Insights
As métricas em [Application insights][start] são valores medidos e contagens de eventos que são enviados na telemetria do seu aplicativo. Eles ajudam você a detectar problemas de desempenho e observar tendências de como seu aplicativo está sendo usado. Há uma ampla variedade de métricas padrão, e você também pode criar suas próprias métricas e eventos personalizados.

> [!NOTE]
> Este artigo descreve a experiência clássica do Metrics Explorer que está preterida no momento e, eventualmente, será desativada. É recomendável verificar a nova experiência descrita neste [artigo](../platform/metrics-charts.md).

As métricas e as contagens de eventos são exibidas em gráficos de valores agregados, como somas, médias ou contagens.

Aqui está um conjunto de gráficos de exemplo:

![](./media/metrics-explorer/01-overview.png)

Você encontra gráficos de métricas em qualquer lugar no portal de Application Insights. Na maioria dos casos, eles podem ser personalizados e você pode adicionar mais gráficos à folha. Na folha visão geral, clique até os gráficos mais detalhados (que têm títulos como "servidores") ou clique em **Metrics Explorer** para abrir uma nova folha em que você pode criar gráficos personalizados.

## <a name="time-range"></a>Intervalo de tempo
Você pode alterar o intervalo de tempo coberto pelos gráficos ou grades em qualquer folha.

![Abra a folha de visão geral do seu aplicativo no portal do Azure](./media/metrics-explorer/03-range.png)

Se você estiver esperando alguns dados que ainda não apareciam, clique em atualizar. Os gráficos se atualizam em intervalos, mas os intervalos são mais longos para intervalos de tempo maiores. Pode levar algum tempo para que os dados sejam fornecidos pelo pipeline de análise em um gráfico.

Para ampliar parte de um gráfico, arraste sobre ele:

![Arraste em parte de um gráfico.](./media/metrics-explorer/12-drag.png)

Clique no botão desfazer zoom para restaurá-lo.

## <a name="granularity-and-point-values"></a>Valores de granularidade e de ponto
Passe o mouse sobre o gráfico para exibir os valores das métricas nesse ponto.

![Passe o mouse sobre um gráfico](./media/metrics-explorer/02-focus.png)

O valor da métrica em um ponto específico é agregado ao longo do intervalo de amostragem anterior.

O intervalo de amostragem ou "granularidade" é mostrado na parte superior da folha.

![O cabeçalho de uma folha.](./media/metrics-explorer/11-grain.png)

Você pode ajustar a granularidade na folha intervalo de tempo:

![O cabeçalho de uma folha.](./media/metrics-explorer/grain.png)

As granularidades disponíveis dependem do intervalo de tempo selecionado. As granularidades explícitas são alternativas à granularidade "automática" para o intervalo de tempo.


## <a name="editing-charts-and-grids"></a>Editando gráficos e grades
Para adicionar um novo gráfico à folha:

![Em Metrics Explorer, escolha Adicionar gráfico](./media/metrics-explorer/04-add.png)

Selecione **Editar** em um gráfico novo ou existente para editar o que ele mostra:

![Selecione uma ou mais métricas](./media/metrics-explorer/08-select.png)

Você pode exibir mais de uma métrica em um gráfico, embora haja restrições sobre as combinações que podem ser exibidas juntas. Assim que você escolher uma métrica, algumas das outras serão desabilitadas.

Se você tiver codificado [métricas personalizadas][track] em seu aplicativo (chamadas para TrackMetric e TrackEvent), elas serão listadas aqui.

## <a name="segment-your-data"></a>Segmentar seus dados
Você pode dividir uma métrica por propriedade, por exemplo, para comparar exibições de página em clientes com diferentes sistemas operacionais.

Selecione um gráfico ou uma grade, alterne o agrupamento e escolha uma propriedade para agrupar por:

![Selecione agrupamento em e defina selecionar uma propriedade em Agrupar por](./media/metrics-explorer/15-segment.png)

> [!NOTE]
> Quando você usa o agrupamento, os tipos de gráfico de área e de barras fornecem uma exibição empilhada. Isso é adequado onde o método de agregação é Sum. Mas, em que o tipo de agregação é Average, escolha os tipos de exibição de linha ou de grade.
>
>

Se você tiver codificado [métricas personalizadas][track] em seu aplicativo e eles incluírem valores de propriedade, você poderá selecionar a propriedade na lista.

O gráfico é muito pequeno para dados segmentados? Ajuste sua altura:

![Ajustar o controle deslizante](./media/metrics-explorer/18-height.png)

## <a name="aggregation-types"></a>Tipos de agregação
A legenda no lado, por padrão, geralmente mostra o valor agregado ao longo do período do gráfico. Se você passar o mouse sobre o gráfico, ele mostrará o valor nesse ponto.

Cada ponto de dados no gráfico é uma agregação dos valores de dados recebidos no intervalo de amostragem anterior ou "granularidade". A granularidade é mostrada na parte superior da folha e varia de acordo com a escala de tempo geral do gráfico.

As métricas podem ser agregadas de maneiras diferentes:

* **Count** é uma contagem dos eventos recebidos no intervalo de amostragem. Ele é usado para eventos como solicitações. As variações na altura do gráfico indicam variações na taxa em que os eventos ocorrem. Mas Observe que o valor numérico é alterado quando você altera o intervalo de amostragem.
* **Sum** adiciona os valores de todos os pontos de dados recebidos sobre o intervalo de amostragem ou o período do gráfico.
* A **média** divide a soma pelo número de pontos de dados recebidos no intervalo.
* Contagens **exclusivas** são usadas para contagens de usuários e contas. Em relação ao intervalo de amostragem ou ao longo do período do gráfico, a figura mostra a contagem de diferentes usuários vistos nesse momento.
* as versões de **%** percentual de cada agregação são usadas somente com gráficos segmentados. O total sempre aumenta até 100% e o gráfico mostra a contribuição relativa de diferentes componentes de um total.

    ![Agregação de porcentagem](./media/metrics-explorer/percentage-aggregation.png)

### <a name="change-the-aggregation-type"></a>Alterar o tipo de agregação

![Edite o gráfico e, em seguida, selecione agregação](./media/metrics-explorer/05-aggregation.png)

O método padrão para cada métrica é mostrado quando você cria um novo gráfico ou quando todas as métricas são desmarcadas:

![Desmarque todas as métricas para ver os padrões](./media/metrics-explorer/06-total.png)

## <a name="pin-y-axis"></a>Fixar eixo Y 
Por padrão, um gráfico mostra valores de eixo Y começando de zero até os valores máximos no intervalo de dados, para fornecer uma representação visual do Quantum dos valores. Mas em alguns casos mais do que a Quantum pode ser interessante inspecionar visualmente pequenas alterações nos valores. Para personalizações como essa, use o recurso de edição de intervalo do eixo Y para fixar o valor mínimo ou máximo do eixo Y no local desejado.
Clique na caixa de seleção "configurações avançadas" para exibir as configurações de intervalo do eixo Y

![Clique em configurações avançadas, selecione intervalo personalizado e especifique os valores máximos mínimos](./media/metrics-explorer/y-axis-range.png)

## <a name="filter-your-data"></a>Filtrar seus dados
Para ver apenas as métricas para um conjunto selecionado de valores de propriedade:

![Clique em filtrar, expanda uma propriedade e verifique alguns valores](./media/metrics-explorer/19-filter.png)

Se você não selecionar nenhum valor para uma propriedade específica, será o mesmo que selecionar todos eles: não há nenhum filtro nessa propriedade.

Observe as contagens de eventos junto com cada valor de propriedade. Quando você seleciona valores de uma propriedade, as contagens junto com outros valores de propriedade são ajustadas.

Os filtros se aplicam a todos os gráficos em uma folha. Se você quiser filtros diferentes aplicados a diferentes gráficos, crie e salve folhas de métricas diferentes. Se desejar, você pode fixar os gráficos de diferentes folhas no painel, para que você possa vê-los entre si.

### <a name="remove-bot-and-web-test-traffic"></a>Remover tráfego de teste na Web e bot
Use o filtro **tráfego real ou sintético** e marque **real**.

Você também pode filtrar por **origem do tráfego sintético**.

### <a name="to-add-properties-to-the-filter-list"></a>Para adicionar propriedades à lista de filtros
Deseja filtrar a telemetria em uma categoria de sua escolha? Por exemplo, talvez você divida os usuários em diferentes categorias e queira segmentar seus dados por essas categorias.

[Crie sua própria propriedade](../../azure-monitor/app/api-custom-events-metrics.md#properties). Defina-o em um [inicializador de telemetria](../../azure-monitor/app/api-custom-events-metrics.md#defaults) para que ele apareça em toda a telemetria, incluindo a telemetria padrão enviada por diferentes módulos do SDK.

## <a name="edit-the-chart-type"></a>Editar o tipo de gráfico
Observe que você pode alternar entre grades e grafos:

![Selecione uma grade ou um grafo e, em seguida, escolha um tipo de gráfico](./media/metrics-explorer/16-chart-grid.png)

## <a name="save-your-metrics-blade"></a>Salvar sua folha de métricas
Depois de criar alguns gráficos, salve-os como um favorito. Você pode escolher se deseja compartilhá-lo com outros membros da equipe, se usar uma conta institucional.

![Escolher favorito](./media/metrics-explorer/21-favorite-save.png)

Para ver a folha novamente, **vá para a folha visão geral** e abra favoritos:

![Na folha visão geral, escolha favoritos](./media/metrics-explorer/22-favorite-get.png)

Se você escolheu o intervalo de tempo relativo quando salvou, a folha será atualizada com as métricas mais recentes. Se você escolher intervalo de tempo absoluto, ele mostrará os mesmos dados a cada vez.

## <a name="reset-the-blade"></a>Redefinir a folha
Se você editar uma folha, mas, em seguida, quiser voltar para o conjunto salvo original, basta clicar em Redefinir.

![Nos botões na parte superior do Gerenciador de métricas](./media/metrics-explorer/17-reset.png)

## <a name="live-metrics-stream"></a>Fluxo de métricas ao vivo

Para uma exibição muito mais imediata de sua telemetria, abra [Live Stream](live-stream.md). A maioria das métricas leva alguns minutos para aparecer, devido ao processo de agregação. Por outro lado, as métricas ao vivo são otimizadas para baixa latência. 

## <a name="set-alerts"></a>Definir alertas
Para ser notificado por email de valores incomuns de qualquer métrica, adicione um alerta. Você pode optar por enviar o email para os administradores da conta ou para endereços de email específicos.

![Em Metrics Explorer, escolha regras de alerta, adicionar alerta](./media/metrics-explorer/appinsights-413setMetricAlert.png)

[Saiba mais sobre alertas][alerts].


## <a name="continuous-export"></a>Exportação Contínua
Se desejar que os dados sejam exportados continuamente para que você possa processá-los externamente, considere usar a [exportação contínua](../../azure-monitor/app/export-telemetry.md).

### <a name="power-bi"></a>Power BI
Se você quiser exibições ainda mais avançadas de seus dados, poderá [exportar para Power bi](https://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx).

## <a name="analytics"></a>Análise
A [análise](../../azure-monitor/app/analytics.md) é uma maneira mais versátil de analisar sua telemetria usando uma linguagem de consulta eficiente. Use-o se você quiser combinar ou calcular os resultados de métricas ou executar uma exploração detalhada do desempenho recente do seu aplicativo. 

Em um gráfico de métrica, você pode clicar no ícone de análise para ir diretamente para a consulta de análise equivalente.

## <a name="troubleshooting"></a>Resolução de problemas
*Não vejo nenhum dado em meu gráfico.*

* Os filtros se aplicam a todos os gráficos na folha. Certifique-se de que, enquanto estiver se concentrando em um gráfico, você não definiu um filtro que exclua todos os dados em outro.

    Se você quiser definir filtros diferentes em gráficos diferentes, crie-os em diferentes folhas, salve-os como favoritos separados. Se desejar, você pode fixá-los no painel para que você possa vê-los ao lado.
* Se você agrupar um gráfico por uma propriedade que não esteja definida na métrica, não haverá nada no gráfico. Tente limpar ' Agrupar por ' ou escolha uma propriedade de agrupamento diferente.
* Os dados de desempenho (CPU, taxa de e/s e assim por diante) estão disponíveis para serviços Web Java, aplicativos de área de trabalho do Windows, [aplicativos Web do IIS e serviços se você instalar o status monitor](../../azure-monitor/app/monitor-performance-live-website-now.md)e os [serviços de nuvem do Azure](../../azure-monitor/app/app-insights-overview.md). Ele não está disponível para os sites do Azure.

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Passos seguintes
* [Monitorando o uso com Application Insights](../../azure-monitor/app/usage-overview.md)
* [Usando a pesquisa de diagnóstico](../../azure-monitor/app/diagnostic-search.md)

<!--Link references-->

[alerts]: ../../azure-monitor/app/alerts.md
[start]: ../../azure-monitor/app/app-insights-overview.md
[track]: ../../azure-monitor/app/api-custom-events-metrics.md
