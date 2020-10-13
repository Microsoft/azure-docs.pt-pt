---
title: Explore dados utilizando o Explorer - Azure Time Series Insights / Microsoft Docs
description: Saiba como utilizar o Azure Time Series Insights Explorer para ver os seus dados IoT.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 49a763af8d936ec9fd15c59c908ba9015f803930
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91568641"
---
# <a name="azure-time-series-insights-gen1-explorer"></a>Azure Time Series Insights Gen1 Explorer

> [!CAUTION]
> Este é um artigo da Gen1.

Este artigo descreve as funcionalidades e opções para a [aplicação web](https://insights.timeseries.azure.com/)Azure Time Series Insights Gen1 Explorer . O Azure Time Series Insights Explorer demonstra as poderosas capacidades de visualização de dados fornecidas pelo serviço e pode ser acedido dentro do seu próprio ambiente.

O Azure Time Series Insights é um serviço de análise, armazenamento e visualização totalmente gerido que simplifica a exploração e a análise de milhões de eventos de IoT em simultâneo. Dá-lhe uma visão global dos seus dados, que lhe permite validar rapidamente a sua solução IoT e evitar tempos de inatividade dispendiosos para dispositivos críticos da missão. Você pode descobrir tendências ocultas, detetar anomalias e realizar análises de causa-raiz em tempo real.

> [!TIP]
> Para uma visita guiada pelo ambiente de demonstração, leia o [quickstart da Azure Time Series Insights](time-series-quickstart.md).

## <a name="video"></a>Vídeo

### <a name="learn-about-querying-data-by-using-the-azure-time-series-insights-explorer-br"></a>Saiba a consulta de dados utilizando o Azure Time Series Insights Explorer. </br>

> [!VIDEO https://www.youtube.com/embed/SHFPZvrR71s]

>[!NOTE]
>Reproduz o vídeo anterior <a href="https://www.youtube.com/watch?v=6ehNf6AJkFo">"Começar com a Azure Time Series Insights utilizando um Acelerador de Solução IoT Azure."</a>

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder utilizar o Azure Time Series Insights Explorer, tem de:

- Crie um ambiente Azure Time Series Insights. Para mais informações, leia [Como começar com a Azure Time Series Insights](./time-series-insights-get-started.md).
- [Fornecer acesso](time-series-insights-data-access.md) à sua conta no ambiente.
- Adicione-lhe um [hub IoT](time-series-insights-how-to-add-an-event-source-iothub.md) [ou](time-series-insights-how-to-add-an-event-source-eventhub.md) uma fonte de eventos hub.

## <a name="explore-and-query-data"></a>Explorar e consultar dados

A poucos minutos de ligar a fonte do evento ao ambiente Azure Time Series Insights, pode explorar e consultar os dados da série de tempo.

1. Para começar, abra o [Azure Time Series Insights Explorer](https://insights.timeseries.azure.com/) no seu navegador web. No lado esquerdo da janela, selecione um ambiente. Todos os ambientes a que tem acesso estão listados por ordem alfabética.

1. Depois de selecionar um ambiente, utilize as configurações **de De** e **para Na** parte Superior ou selecione e arraste o intervalo de tempo que deseja. Selecione a lupa no canto superior direito ou clique no tempo de tempo selecionado e selecione **Search**.

1. Também pode atualizar a disponibilidade automaticamente a cada minuto selecionando o botão **Auto On.** O botão **Auto On** aplica-se apenas ao gráfico de disponibilidade, não ao conteúdo da visualização principal.

1. O ícone da nuvem Azure leva-o ao seu ambiente no portal Azure.

   [![Azure Time Series Insights seleção ambiental](media/time-series-insights-explorer/tsi-ga-explorer-environments.png)](media/time-series-insights-explorer/tsi-ga-explorer-environments.png#lightbox)

1. Em seguida, é apresentado um gráfico que mostra uma contagem de todos os eventos durante o período de tempo selecionado. Aqui tem uma série de controlos:

    - **Termos Painel editor**: O termo espaço é onde você consulta o seu ambiente. Encontra-se no lado esquerdo do ecrã:
      - **MEDIDA**: Esta lista de down-down mostra todas as colunas**numéricas (Duplos**).
      - **SPLIT BY**: Esta lista de drop-down mostra colunas categóricas **(Strings**).
      - Pode ativar a interpolação de passos, mostrar o mínimo e máximo, e ajustar o eixo y do painel de controlo ao lado **do MEASURE**. Também pode ajustar se os dados apresentados são uma contagem, média ou soma dos dados.
      - Pode adicionar até cinco termos para ver no mesmo eixo x. **Selecione Adicionar** para adicionar um novo termo ou usar o botão **Clone deste termo** para adicionar uma cópia de um termo existente.

        [![Seleção de termos, filtragem e painel de consultas](media/time-series-insights-explorer/tsi-ga-explorer-add-or-clone.png)](media/time-series-insights-explorer/tsi-ga-explorer-add-or-clone.png#lightbox)

      - **Predicado**: Utilize o predicado para filtrar rapidamente os seus eventos utilizando o conjunto de operands listados na tabela seguinte. Se realizar uma pesquisa selecionando ou clicando, o predicado atualiza automaticamente com base nessa pesquisa. Os tipos de ópera suportados incluem:

         |Operação  |Tipos suportados  |Notas  |
         |---------|---------|---------|
         |**<**, **>**, **<=**, **>=**    |  **Duplo,** **DateTime,** **TimeSpan**       |         |
         |**=**, **!=**, **<>**     | **String**, **Bool**, **Double**, **DateTime,** **TimeSpan,** **NU**        |         |
         |**EM**     | **String**, **Bool**, **Double**, **DateTime,** **TimeSpan,** **NU**        |  Todos os óperas devem ser do mesmo tipo ou ser **constantes NU.**        |
         |**TEM**     | **String**        |  Apenas as cordas constantes literais são permitidas no lado direito. Corda vazia e **NU NÃO** são permitidos.       |

      - **Consultas de exemplo**

         [![Exemplo Consultas Gen1](media/time-series-insights-explorer/tsi-ga-example-queries.png)](media/time-series-insights-explorer/tsi-ga-example-queries.png#lightbox)

1. Pode utilizar a ferramenta de slider **'Tamanho de Intervalo'** para ampliar dentro e fora dos intervalos durante o mesmo período de tempo. O slider proporciona um controlo mais preciso do movimento entre grandes fatias de tempo que mostram tendências suaves até fatias tão pequenas como o milissegundo, que lhe permitem exibir e analisar cortes granulares e de alta resolução dos seus dados. O ponto de partida padrão do slider é definido como a visão mais ideal dos dados da sua seleção para equilibrar a resolução, a velocidade de consulta e a granularidade.

1. A ferramenta **de escova de tempo** facilita a navegação de um intervalo para outro.

1. Selecione o ícone **Guardar** para guardar a sua consulta atual e partilhá-la com outros utilizadores do ambiente. Ao selecionar o ícone **Open,** pode rever todas as suas consultas guardadas e quaisquer consultas partilhadas de outros utilizadores em ambientes a que tenha acesso.

   [![Consultas](media/time-series-insights-explorer/tsi-ga-explorer-saved-queries.png)](media/time-series-insights-explorer/tsi-ga-explorer-saved-queries.png#lightbox)

## <a name="visualize-data"></a>Visualizar os dados

1. Utilize a ferramenta **Perspetive View** para uma visão simultânea de até quatro consultas únicas. O botão **Perspetiva Visualização** encontra-se no canto superior direito da tabela.

   [![Selecione consultas para adicionar ao painel de perspetiva](media/time-series-insights-explorer/tsi-ga-explorer-perspective-panes.png)](media/time-series-insights-explorer/tsi-ga-explorer-perspective-panes.png#lightbox)

1. Veja um gráfico para explorar visualmente os seus dados e use as ferramentas **Chart:**

    - **Selecione** ou **clique numa** timepan específica ou numa única série de dados.
    - Dentro de uma seleção de tempos, você pode ampliar ou explorar eventos.
    - Dentro de uma série de dados, pode dividir a série por outra coluna, adicionar a série como um novo termo, mostrar apenas a série selecionada, excluir as séries selecionadas, ping que série, ou explorar eventos da série selecionada.
    - Na área do filtro à esquerda do gráfico, pode rever todas as séries de dados apresentadas e reencomendar por valor ou nome. Também pode ver todas as séries de dados ou qualquer série fixa ou não pined. Pode selecionar uma única série de dados e dividir a série por outra coluna, adicionar a série como um novo termo, mostrar apenas a série selecionada, excluir a série selecionada, pin essa série ou explorar eventos da série selecionada.
    - Quando vê vários termos simultaneamente, pode empilhar, desacafiar, rever dados adicionais sobre uma série de dados e utilizar o mesmo eixo y em todos os termos. Utilize os botões no canto superior direito da tabela.

    [![Definições de opção de opção de canto superior direito da ferramenta](media/time-series-insights-explorer/tsi-ga-example-chart-options.png)](media/time-series-insights-explorer/tsi-ga-example-chart-options.png#lightbox)

1. Utilize o **mapa térmico** para detetar rapidamente séries de dados únicas ou anómalas numa determinada consulta. Apenas um termo de pesquisa pode ser visualizado como um mapa de calor.

    [Time Series Insights explorer heatmap charting](media/time-series-insights-explorer/tsi-ga-example-heatmap-charting.png)] (media/time-series-insights-explorer/tsi-ga-example-heatmap-charting.png#lightbox)

1. Quando explora eventos selecionando ou clicando à direita, o painel **EVENTOS** é disponibilizado. Aqui, pode rever todos os seus eventos crus e exportar os seus eventos como ficheiros JSON ou CSV. A Azure Time Series Insights armazena todos os dados brutos.

    [![Eventos](media/time-series-insights-explorer/tsi-ga-explorer-events-panel.png)](media/time-series-insights-explorer/tsi-ga-explorer-events-panel.png#lightbox)

1. Selecione o separador **STATS** depois de explorar eventos para expor padrões e estatísticas de colunas.

    - **Padrões**: Esta característica apresenta proactivamente os padrões estatisticamente mais significativos numa região de dados selecionada. Não é preciso olhar para milhares de eventos para perceber que padrões requerem mais tempo e energia. Com o Azure Time Series Insights, pode saltar diretamente para estes padrões estatisticamente significativos para continuar a realizar uma análise. Esta característica também é útil para investigações post-mortem em dados históricos.
    - **Estatísticas da coluna**: As estatísticas das colunas fornecem gráficos e tabelas que desabar dados de cada coluna da série de dados selecionada sobre o intervalo de tempo selecionado.

      [![Gráfico de colunas STATS e opções](media/time-series-insights-explorer/tsi-ga-explorer-stat-column.png)](media/time-series-insights-explorer/tsi-ga-explorer-stat-column.png#lightbox)

Agora que aprendeu sobre as principais funcionalidades, configurações e opções de exibição que estão disponíveis na aplicação web do explorador Azure Time Series Insights.

## <a name="next-steps"></a>Passos seguintes

- Aprenda a [diagnosticar e a resolver problemas](time-series-insights-diagnose-and-solve-problems.md) no seu ambiente Azure Time Series Insights.

- Faça o tour guiado [Azure Time Series Insights quickstart.](time-series-quickstart.md)
