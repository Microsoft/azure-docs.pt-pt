---
title: Explorar Métricas em Insights de Aplicação Azure  Microsoft Docs
description: Como interpretar gráficos sobre exploradormétrico e como personalizar lâminas de explorador métrico.
ms.topic: conceptual
ms.date: 01/22/2019
ms.openlocfilehash: f85a8fe79e7f4f820d7c0e5b942730305e892095
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79275896"
---
# <a name="exploring-metrics-in-application-insights"></a>Explorar métricas em insights de aplicação
As métricas em Insights de [Aplicação][start] são valores medidos e contagens de eventos que são enviados em telemetria a partir da sua aplicação. Ajudam-no a detetar problemas de desempenho e a observar as tendências na forma como a sua aplicação está a ser utilizada. Há uma grande variedade de métricas padrão, e você também pode criar suas próprias métricas e eventos personalizados.

> [!NOTE]
> Este artigo descreve a experiência clássica do explorador de métricas que está atualmente depreciada e acabará por ser reformada. Recomendamos que se verifique a nova experiência descrita [neste artigo.](../platform/metrics-charts.md)

As métricas e as contagens de eventos são exibidas em gráficos de valores agregados, tais como somas, médias ou contagens.

Aqui está um conjunto de gráficos:

![](./media/metrics-explorer/01-overview.png)

Encontra-se gráficos de métricas em todo o lado no portal Application Insights. Na maioria dos casos, podem ser personalizados, e pode adicionar mais gráficos à lâmina. A partir da lâmina overview, clique em gráficos mais detalhados (que têm títulos como "Servidores"), ou clique no **Metrics Explorer** para abrir uma nova lâmina onde pode criar gráficos personalizados.

## <a name="time-range"></a>Intervalo de tempo
Pode alterar a gama de tempo coberta pelos gráficos ou grades em qualquer lâmina.

![Abra a lâmina geral da sua aplicação no portal Azure](./media/metrics-explorer/03-range.png)

Se está à espera de alguns dados que ainda não apareceram, clique em Refresh. Os gráficos refrescam-se em intervalos, mas os intervalos são mais longos para maiores intervalos de tempo. Pode levar algum tempo para que os dados passem pelo pipeline de análise num gráfico.

Para ampliar uma parte de um gráfico, arraste-o:

![Arraste através de parte de um gráfico.](./media/metrics-explorer/12-drag.png)

Clique no botão Desfazer Zoom para restaurá-lo.

## <a name="granularity-and-point-values"></a>Valores granularidade e ponto
Passe o rato sobre a tabela para mostrar os valores das métricas nesse ponto.

![Pairar sobre o rato sobre um gráfico](./media/metrics-explorer/02-focus.png)

O valor da métrica num determinado ponto é agregado ao longo do intervalo de amostragem anterior.

O intervalo de amostragem ou "granularidade" é mostrado na parte superior da lâmina.

![O cabeçalho de uma lâmina.](./media/metrics-explorer/11-grain.png)

Pode ajustar a granularidade na lâmina de intervalo de tempo:

![O cabeçalho de uma lâmina.](./media/metrics-explorer/grain.png)

As granularidades disponíveis dependem do intervalo de tempo que selecionar. As granularidades explícitas são alternativas à granularidade "automática" para a faixa hortime.


## <a name="editing-charts-and-grids"></a>Gráficos e grelhas de edição
Para adicionar um novo gráfico à lâmina:

![No Metrics Explorer, escolha Adicionar Gráfico](./media/metrics-explorer/04-add.png)

**Selecione Editar** num gráfico existente ou novo para editar o que mostra:

![Selecione uma ou mais métricas](./media/metrics-explorer/08-select.png)

Pode apresentar mais de uma métrica num gráfico, embora existam restrições sobre as combinações que podem ser exibidas em conjunto. Assim que escolhes uma métrica, algumas das outras estão desativadas.

Se codificar [métricas personalizadas][track] na sua aplicação (chamadas para TrackMetric e TrackEvent) serão listadas aqui.

## <a name="segment-your-data"></a>Segmente os seus dados
Pode dividir uma métrica por propriedade - por exemplo, para comparar as visualizações de páginas em clientes com diferentes sistemas operativos.

Selecione um gráfico ou grelha, ligue o agrupamento e escolha uma propriedade para agrupar:

![Selecione Grouping On e, em seguida, definir selecione uma propriedade em Grupo Por](./media/metrics-explorer/15-segment.png)

> [!NOTE]
> Quando utiliza agrupamentos, os tipos de gráficos de Área e Barra fornecem um ecrã empilhado. Isto é adequado onde o método de agregação é Soma. Mas onde o tipo de agregação é médio, escolha os tipos de visualização line ou grade.
>
>

Se codificar [métricas personalizadas][track] na sua aplicação e incluir valores de propriedade, poderá selecionar a propriedade na lista.

O gráfico é demasiado pequeno para dados segmentados? Ajuste a sua altura:

![Ajuste o deslize](./media/metrics-explorer/18-height.png)

## <a name="aggregation-types"></a>Tipos de agregação
A lenda ao lado por padrão geralmente mostra o valor agregado durante o período do gráfico. Se pairar sobre a tabela, mostra o valor naquele ponto.

Cada ponto de dados no gráfico é um agregado dos valores de dados recebidos no intervalo de amostragem anterior ou na "granularidade". A granularidade é mostrada na parte superior da lâmina, e varia com o calendário geral do gráfico.

As métricas podem ser agregadas de diferentes formas:

* **Contagem** é uma contagem dos eventos recebidos no intervalo de amostragem. É usado para eventos como pedidos. Variações na altura do gráfico indicam variações na taxa a que os eventos ocorrem. Mas note que o valor numérico muda quando muda o intervalo de amostragem.
* **Soma** soma os valores de todos os pontos de dados recebidos durante o intervalo de amostragem, ou o período do gráfico.
* **A média** divide a Soma pelo número de pontos de dados recebidos ao longo do intervalo.
* **Contagens únicas** são usadas para contagens de utilizadores e contas. Durante o intervalo de amostragem, ou durante o período do gráfico, o número mostra a contagem de diferentes utilizadores observados nesse período.
* **%** - versões percentuais de cada agregação são usadas apenas com gráficos segmentados. O total soma sempre até 100%, e o gráfico mostra a contribuição relativa de diferentes componentes de um total.

    ![Agregação percentual](./media/metrics-explorer/percentage-aggregation.png)

### <a name="change-the-aggregation-type"></a>Alterar o tipo de agregação

![Editar o gráfico e, em seguida, selecionar Agregação](./media/metrics-explorer/05-aggregation.png)

O método padrão para cada métrica é mostrado quando cria um novo gráfico ou quando todas as métricas são desselecionadas:

![Desmarque todas as métricas para ver os incumprimentos](./media/metrics-explorer/06-total.png)

## <a name="pin-y-axis"></a>Pin Y eixo 
Por padrão, um gráfico mostra valores do eixo Y a partir de zero até valores máximos na gama de dados, para dar uma representação visual do quantum dos valores. Mas em alguns casos mais do que o quântico pode ser interessante inspecionar visualmente pequenas mudanças de valores. Para personalizações como esta, utilize a função de edição da gama Y para fixar o valor mínimo ou máximo do eixo Y no local desejado.
Clique na caixa de verificação "Definições Avançadas" para elevar as definições do eixo Y

![Clique em Definições Avançadas, selecione gama Personalizada e especifique os valores min max](./media/metrics-explorer/y-axis-range.png)

## <a name="filter-your-data"></a>Filtre os seus dados
Para ver apenas as métricas para um conjunto selecionado de valores de propriedade:

![Clique em Filtro, expanda uma propriedade e verifique alguns valores](./media/metrics-explorer/19-filter.png)

Se não selecionar quaisquer valores para uma determinada propriedade, é o mesmo que selecionar todos: não há filtro nessa propriedade.

Note as contagens de eventos ao lado de cada valor patrimonial. Quando se leciona valores de uma propriedade, as contagens ao lado de outros valores de propriedade são ajustadas.

Os filtros aplicam-se a todos os gráficos de uma lâmina. Se quiser filtros diferentes aplicados a diferentes gráficos, crie e guarde diferentes lâminas métricas. Se quiser, pode fixar gráficos de diferentes lâminas para o tablier, para que possa vê-los ao lado um do outro.

### <a name="remove-bot-and-web-test-traffic"></a>Remover o tráfego de teste de bot e web
Utilize o filtro **Tráfego real ou sintético** e verifique **Real**.

Também pode filtrar por **fonte de tráfego sintético.**

### <a name="to-add-properties-to-the-filter-list"></a>Para adicionar propriedades à lista de filtros
Gostaria de filtrar a telemetria numa categoria à sua escolha? Por exemplo, talvez divida os seus utilizadores em diferentes categorias, e gostaria de segmentar os seus dados por estas categorias.

[Crie a sua própria propriedade.](../../azure-monitor/app/api-custom-events-metrics.md#properties) Coloque-o num [Iniciante de Telemetria](../../azure-monitor/app/api-custom-events-metrics.md#defaults) para que apareça em toda a telemetria - incluindo a telemetria padrão enviada por diferentes módulos SDK.

## <a name="edit-the-chart-type"></a>Editar o tipo de gráfico
Note que pode alternar entre grelhas e gráficos:

![Selecione uma grelha ou gráfico e, em seguida, escolha um tipo de gráfico](./media/metrics-explorer/16-chart-grid.png)

## <a name="save-your-metrics-blade"></a>Guarde a sua lâmina métrica
Quando criares alguns gráficos, guarde-os como favoritos. Pode escolher se partilha com outros membros da equipa, se utilizar uma conta organizacional.

![Escolha favorito](./media/metrics-explorer/21-favorite-save.png)

Para ver a lâmina novamente, **vá à lâmina de visão geral** e abra os favoritos:

![Na lâmina de visão geral, escolha Favoritos](./media/metrics-explorer/22-favorite-get.png)

Se escolher o intervalo de tempo relativo quando guardou, a lâmina será atualizada com as métricas mais recentes. Se escolher o intervalo de tempo absoluto, mostrará sempre os mesmos dados.

## <a name="reset-the-blade"></a>Redefinir a lâmina
Se editar uma lâmina, mas depois gostaria de voltar ao conjunto original guardado, basta clicar em Reset.

![Nos botões no topo do Metric Explorer](./media/metrics-explorer/17-reset.png)

## <a name="live-metrics-stream"></a>Fluxo de métricas ao vivo

Para uma visão muito mais imediata da sua telemetria, abra [o Live Stream.](live-stream.md) A maioria das métricas leva alguns minutos para aparecer, por causa do processo de agregação. Em contraste, as métricas ao vivo são otimizadas para baixa latência. 

## <a name="set-alerts"></a>Definir alertas
Para ser notificado por e-mail de valores incomuns de qualquer métrica, adicione um alerta. Pode optar por enviar o e-mail aos administradores da conta ou a endereços de e-mail específicos.

![No Metrics Explorer, escolha regras de alerta, adicione alerta](./media/metrics-explorer/appinsights-413setMetricAlert.png)

[Saiba mais sobre alertas.][alerts]


## <a name="continuous-export"></a>Exportação Contínua
Se pretender que os dados sejam continuamente exportados para que possa processá-lo externamente, considere utilizar a [exportação contínua.](../../azure-monitor/app/export-telemetry.md)

### <a name="power-bi"></a>Power BI
Se quiser visões ainda mais ricas dos seus dados, pode [exportar para o Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx).

## <a name="analytics"></a>Análise
[Analytics](../../azure-monitor/app/analytics.md) é uma forma mais versátil de analisar a sua telemetria usando uma linguagem de consulta poderosa. Use-o se quiser combinar ou calcular resultados de métricas, ou realizar uma exploração aprofundada do desempenho recente da sua aplicação. 

A partir de um gráfico métrico, pode clicar no ícone Analytics para chegar diretamente à consulta equivalente ao Analytics.

## <a name="troubleshooting"></a>Resolução de Problemas
*Não vejo nenhum dado na minha ficha.*

* Os filtros aplicam-se a todos os gráficos da lâmina. Certifique-se de que, enquanto está focado num gráfico, não definiu um filtro que exclui todos os dados de outro.

    Se quiser definir filtros diferentes em diferentes gráficos, crie-os em lâminas diferentes, guarde-os como favoritos separados. Se quiser, pode fixá-los no tablier para que possa vê-los ao lado um do outro.
* Se você agrupar um gráfico por uma propriedade que não está definida na métrica, então não haverá nada na tabela. Tente limpar 'group by', ou escolha uma propriedade de agrupamento diferente.
* Os dados de desempenho (CPU, taxa IO, e assim por diante) estão disponíveis para serviços web java, aplicações de desktop windows, [aplicações e serviços Web IIS se instalar o monitor](../../azure-monitor/app/monitor-performance-live-website-now.md)de estado e os [Serviços Azure Cloud.](../../azure-monitor/app/app-insights-overview.md) Não está disponível para websites do Azure.

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Passos Seguintes
* [Monitorização do uso com Insights de Aplicação](../../azure-monitor/app/usage-overview.md)
* [Usando pesquisa de diagnóstico](../../azure-monitor/app/diagnostic-search.md)

<!--Link references-->

[alerts]: ../../azure-monitor/app/alerts.md
[start]: ../../azure-monitor/app/app-insights-overview.md
[track]: ../../azure-monitor/app/api-custom-events-metrics.md
