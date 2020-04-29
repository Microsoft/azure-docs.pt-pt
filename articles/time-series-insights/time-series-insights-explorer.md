---
title: Explore dados utilizando o explorador - Azure Time Series Insights [ Microsoft Docs
description: Aprenda a usar o explorador de Insights da Série De Tempo Azure para ver os seus dados IoT.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/14/2020
ms.custom: seodec18
ms.openlocfilehash: b564373780da5bba71bc46ddbac3dc69a0c94a4a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81382786"
---
# <a name="azure-time-series-insights-explorer"></a>Explorador de Insights da Série De Tempo Azure

Este artigo descreve as funcionalidades e opções na disponibilidade geral para a [aplicação web web do Explorador](https://insights.timeseries.azure.com/)de Insights da Série De Tempo Azure. O explorador Time Series Insights demonstra as poderosas capacidades de visualização de dados fornecidas pelo serviço e pode ser acedida dentro do seu próprio ambiente.

O Azure Time Series Insights é um serviço de análise, armazenamento e visualização totalmente gerido que simplifica a exploração e a análise de milhões de eventos de IoT em simultâneo. Dá-lhe uma visão global dos seus dados, o que permite validar rapidamente a sua solução IoT e evitar um tempo de inatividade dispendioso para dispositivos críticos da missão. Você pode descobrir tendências ocultas, detetar anomalias e realizar análises de causa-raiz em tempo real. O explorador time series Insights está atualmente em pré-visualização pública.

> [!TIP]
> Para uma visita guiada pelo ambiente de demonstração, leia o Rapidstart da [Série De Tempo Azure](time-series-quickstart.md)Insights.

## <a name="video"></a>Vídeo

### <a name="learn-about-querying-data-by-using-the-time-series-insights-explorer-br"></a>Aprenda sobre consulta de dados utilizando o explorador time series Insights. </br>

> [!VIDEO https://www.youtube.com/embed/SHFPZvrR71s]

>[!NOTE]
>Reprodução do vídeo anterior <a href="https://www.youtube.com/watch?v=6ehNf6AJkFo">"Getting started with Time Series Insights, utilizando um Acelerador de Soluções Azure IoT."</a>

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder utilizar o explorador time series Insights, deve:

- Crie um ambiente time series insights. Para mais informações, leia [Como começar com time series Insights](./time-series-insights-get-started.md).
- [Fornecer acesso](time-series-insights-data-access.md) à sua conta no ambiente.
- Adicione-lhe um [hub IoT](time-series-insights-how-to-add-an-event-source-iothub.md) [ou](time-series-insights-how-to-add-an-event-source-eventhub.md) fonte de evento seleção.

## <a name="explore-and-query-data"></a>Explore e questione dados

Poucos minutos depois de ligar a fonte do seu evento ao seu ambiente Time Series Insights, pode explorar e consultar os dados da série de tempo.

1. Para começar, abra o explorador time [series insights](https://insights.timeseries.azure.com/) no seu navegador web. No lado esquerdo da janela, selecione um ambiente. Todos os ambientes a que tem acesso estão listados por ordem alfabética.

1. Depois de selecionar um ambiente, utilize as configurações **De** e **Para** na parte superior, ou selecione e arraste sobre o tempo que deseja. Selecione a lupa no canto superior direito ou clique à direita na hora tântema selecionada e selecione **Search**.

1. Também pode atualizar a disponibilidade automaticamente a cada minuto, selecionando o botão **Auto On.** O botão **Auto On** aplica-se apenas ao gráfico de disponibilidade, não ao conteúdo da visualização principal.

1. O ícone da nuvem Azure leva-o ao seu ambiente no portal Azure.

   [![Seleção do ambiente Time Series Insights](media/time-series-insights-explorer/tsi-ga-explorer-environments.png)](media/time-series-insights-explorer/tsi-ga-explorer-environments.png#lightbox)

1. Em seguida, é apresentado um gráfico que mostra uma contagem de todos os eventos durante o tempo de tempo selecionado. Aqui tem uma série de controlos:

    - **Termos Painel editor**: O espaço termo é onde você consulta o seu ambiente. É encontrado no lado esquerdo do ecrã:
      - **MEDIDA**: Esta lista de abandono mostra todas as colunas numéricas **(Duplos).**
      - **SPLIT BY**: Esta lista de abandono mostra colunas categóricas **(Cordas**).
      - Pode ativar a interpolação do passo, mostrar o mínimo e o máximo, e ajustar o eixo y do painel de controlo ao lado da **MEDIDA**. Também pode ajustar se os dados mostrados são uma contagem, média ou soma dos dados.
      - Pode adicionar até cinco termos para ver no mesmo eixo X. Selecione **Adicionar** para adicionar um termo novo ou use o botão **Clone neste termo** para adicionar uma cópia de um termo existente.

        [![Seleção de termos, filtragem e painel de consulta](media/time-series-insights-explorer/tsi-ga-explorer-add-or-clone.png)](media/time-series-insights-explorer/tsi-ga-explorer-add-or-clone.png#lightbox)

      - **Predicado**: Utilize o predicado para filtrar rapidamente os seus eventos utilizando o conjunto de operands listados na tabela seguinte. Se efetuar uma pesquisa selecionando ou clicando, o predicado atualiza automaticamente com base nessa pesquisa. Os tipos de opere suportados incluem:

         |Operação  |Tipos suportados  |Notas  |
         |---------|---------|---------|
         |**<**, **>**, **<=**, **>=**    |  **Duplo,** **Data,** **TimeSpan**       |         |
         |**=**, **!=**,**<>**     | **String**, **Bool,** **Double,** **DateTime,** **TimeSpan,** **NULL**        |         |
         |**EM**     | **String**, **Bool,** **Double,** **DateTime,** **TimeSpan,** **NULL**        |  Todos os operands devem ser do mesmo tipo ou ser **nulos** constantes.        |
         |**TEM**     | **String**        |  Apenas os literais de corda constantes são permitidos no lado direito. Cordas vazias e **NULO** não são permitidos.       |

      - **Consultas de exemplo**

         [![Consultas ga exemplo](media/time-series-insights-explorer/tsi-ga-example-queries.png)](media/time-series-insights-explorer/tsi-ga-example-queries.png#lightbox)

1. Pode utilizar a ferramenta de slider **Interval Size** para ampliar para dentro e para fora de intervalos durante o mesmo período de tempo. O slider proporciona um controlo mais preciso do movimento entre grandes fatias de tempo que mostram tendências suaves até fatias tão pequenas como o milissegundo, que lhe permitem exibir e analisar cortes granulares e de alta resolução dos seus dados. O ponto de partida padrão do slider é definido como a visão mais ideal dos dados da sua seleção para resolução de equilíbrio, velocidade de consulta e granularidade.

1. A ferramenta time **brush** facilita a navegação de uma hora para outra.

1. Selecione o ícone **Guardar** para guardar a sua consulta atual e partilhá-la com outros utilizadores do ambiente. Ao selecionar o ícone **Open,** pode rever todas as suas consultas guardadas e quaisquer consultas partilhadas de outros utilizadores em ambientes a que tem acesso.

   [![Consultas](media/time-series-insights-explorer/tsi-ga-explorer-saved-queries.png)](media/time-series-insights-explorer/tsi-ga-explorer-saved-queries.png#lightbox)

## <a name="visualize-data"></a>Visualizar os dados

1. Utilize a ferramenta **Perspetive View** para uma visão simultânea de até quatro consultas únicas. O botão **Perspetive View** está no canto superior direito da tabela.

   [![Selecione consultas para adicionar ao painel de perspetiva](media/time-series-insights-explorer/tsi-ga-explorer-perspective-panes.png)](media/time-series-insights-explorer/tsi-ga-explorer-perspective-panes.png#lightbox)

1. Veja um gráfico para explorar visualmente os seus dados e use as ferramentas **Chart:**

    - **Selecione** ou **clique numa** timepan específica ou numa única série de dados.
    - Dentro de uma seleção de timepan, você pode ampliar ou explorar eventos.
    - Dentro de uma série de dados, pode dividir a série por outra coluna, adicionar a série como um novo termo, mostrar apenas as séries selecionadas, excluir as séries selecionadas, ping essa série ou explorar eventos a partir da série selecionada.
    - Na área do filtro à esquerda do gráfico, pode rever todas as séries de dados exibidas e reencomendar por valor ou nome. Também pode ver todas as séries de dados ou qualquer série de fixação ou unpinne. Pode selecionar uma única série de dados e dividir a série por outra coluna, adicionar a série como um novo termo, mostrar apenas as séries selecionadas, excluir as séries selecionadas, fixar essa série ou explorar eventos da série selecionada.
    - Quando vê vários termos simultaneamente, pode empilhar, desempilhar, rever dados adicionais sobre uma série de dados e usar o mesmo eixo y em todos os termos. Utilize os botões no canto superior direito da tabela.

    [![Definições de opção de canto superior direito da ferramenta de gráfico](media/time-series-insights-explorer/tsi-ga-example-chart-options.png)](media/time-series-insights-explorer/tsi-ga-example-chart-options.png#lightbox)

1. Use o **mapa de calor** para detetar rapidamente séries de dados únicas ou anómalas numa determinada consulta. Apenas um termo de pesquisa pode ser visualizado como um mapa de calor.

    [![Gráfico de mapa de mapas de calor do explorador GA](media/time-series-insights-explorer/tsi-ga-example-heatmap-charting.png)](media/time-series-insights-explorer/tsi-ga-example-heatmap-charting.png#lightbox)

1. Quando explora os eventos selecionando ou clicando à direita, o painel **EVENTOs** é disponibilizado. Aqui, você pode rever todos os seus eventos brutos e exportar seus eventos como ficheiros JSON ou CSV. Time Series Insights armazena todos os dados brutos.

    [![Eventos](media/time-series-insights-explorer/tsi-ga-explorer-events-panel.png)](media/time-series-insights-explorer/tsi-ga-explorer-events-panel.png#lightbox)

1. Selecione o separador **STATS** depois de explorar eventos para expor padrões e estatísticas de colunas.

    - **Padrões**: Esta característica surge proativamente os padrões estatisticamente mais significativos numa região de dados selecionada. Não é preciso olhar para milhares de eventos para perceber quais os padrões que mais tempo e energia requerem. Com time series insights, você pode saltar diretamente para estes padrões estatisticamente significativos para continuar a realizar uma análise. Esta funcionalidade também é útil para investigações pós-morte sobre dados históricos.
    - **Estatísticas da coluna**: As estatísticas das colunas fornecem gráficos e tabelas que decomporem dados de cada coluna da série de dados selecionada seletiva sobre a tabela de tempos selecionada.

      [![Gráficos e opções de colunas STATS](media/time-series-insights-explorer/tsi-ga-explorer-stat-column.png)](media/time-series-insights-explorer/tsi-ga-explorer-stat-column.png#lightbox)

Agora já aprendeu sobre as funcionalidades principais, configurações de configuração e opções de exibição que estão disponíveis na aplicação web do Time Series Insights.

## <a name="next-steps"></a>Passos seguintes

- Aprenda a [diagnosticar e resolver problemas](time-series-insights-diagnose-and-solve-problems.md) no seu ambiente Time Series Insights.

- Faça o tour guiado [azure Time Series Insights quickstart.](time-series-quickstart.md)
