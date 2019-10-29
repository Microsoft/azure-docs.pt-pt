---
title: Explorar dados usando o Azure Time Series Insights Explorer | Microsoft Docs
description: Este artigo descreve como usar o Azure Time Series Insights Explorer no navegador da Web para ver rapidamente uma exibição global do seu Big Data e validar seu ambiente IoT.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/03/2019
ms.custom: seodec18
ms.openlocfilehash: 25f2c07678217b69699b881d53ee9d5f92f84be1
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990110"
---
# <a name="azure-time-series-insights-explorer"></a>Azure Time Series Insights Explorer

Este artigo descreve os recursos e as opções de disponibilidade geral para o [aplicativo web Azure Time Series insights Explorer](https://insights.timeseries.azure.com/). O Time Series Insights Explorer demonstra os poderosos recursos de visualização de dados fornecidos pelo serviço e podem ser acessados em seu próprio ambiente.

O Azure Time Series Insights é um serviço de análise, armazenamento e visualização totalmente gerido que simplifica a exploração e a análise de milhões de eventos de IoT em simultâneo. Ele fornece uma exibição global dos seus dados, o que permite que você valide rapidamente sua solução de IoT e evite um tempo de inatividade dispendioso para dispositivos de missão crítica. Você pode descobrir tendências ocultas, detectar anomalias e realizar análises de causa raiz quase em tempo real. O Time Series Insights Explorer está atualmente em visualização pública.

> [!TIP]
> Para obter um tour guiado por meio do ambiente de demonstração, leia o guia de [início rápido do Azure Time Series insights](time-series-quickstart.md).

## <a name="video"></a>Vídeo

### <a name="learn-about-querying-data-by-using-the-time-series-insights-explorer-br"></a>Saiba mais sobre como consultar dados usando o Time Series Insights Explorer. </br>

> [!VIDEO https://www.youtube.com/embed/SHFPZvrR71s]

>[!NOTE]
>Consulte o vídeo anterior <a href="https://www.youtube.com/watch?v=6ehNf6AJkFo">"introdução ao time Series insights usando um acelerador de solução do Azure IOT".</a>

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder usar o Time Series Insights Explorer, você deve:

- Crie um ambiente de Time Series Insights. Para obter mais informações, consulte como começar a [usar Time Series insights](./time-series-insights-get-started.md).
- [Forneça acesso](time-series-insights-data-access.md) à sua conta no ambiente.
- Adicione um [Hub IOT](time-series-insights-how-to-add-an-event-source-iothub.md) ou origem do evento do [Hub de eventos](time-series-insights-how-to-add-an-event-source-eventhub.md) a ele.

## <a name="explore-and-query-data"></a>Explorar e consultar dados

Em minutos de conexão da origem do evento ao seu ambiente de Time Series Insights, você pode explorar e consultar seus dados de série temporal.

1. Para iniciar, abra o [Time Series insights Explorer](https://insights.timeseries.azure.com/) em seu navegador da Web. No lado esquerdo da janela, selecione um ambiente. Todos os ambientes aos quais você tem acesso estão listados em ordem alfabética.

1. Depois de selecionar um ambiente, use as configurações **de** e **para** na parte superior, ou clique e arraste sobre o período desejado. Selecione a lupa no canto superior direito ou clique com o botão direito do mouse no período de tempo selecionado e selecione **Pesquisar**.

1. Você também pode atualizar a disponibilidade automaticamente a cada minuto selecionando o botão **automático** . O botão **auto on** só se aplica ao gráfico de disponibilidade, não ao conteúdo da visualização principal.

1. O ícone de nuvem do Azure leva você ao seu ambiente no portal do Azure.

   [ambiente de Time Series Insights![](media/time-series-insights-explorer/explorer1.png)](media/time-series-insights-explorer/explorer1.png#lightbox)

1. Em seguida, você verá um gráfico que mostra uma contagem de todos os eventos durante o período de tempo selecionado. Aqui você tem vários controles:

    - **Painel de termos do editor**: o termo espaço é onde você consulta seu ambiente. Ele é encontrado no lado esquerdo da tela:
      - **Medida**: essa lista suspensa mostra todas as colunas numéricas (**duplas**).
      - **Dividir por**: essa lista suspensa mostra colunas categóricas (**cadeias de caracteres**).
      - Você pode habilitar a interpolação de etapa, mostrar mínimo e máximo e ajustar o eixo y do painel de controle ao lado de **medida**. Você também pode ajustar se os dados mostrados são uma contagem, média ou soma dos dados.
      - Você pode adicionar até cinco termos para exibir no mesmo eixo x. Use o botão de **cópia** para adicionar um termo adicional ou selecione **Adicionar** para adicionar um novo termo.

        [painel do editor de termos de![](media/time-series-insights-explorer/explorer2.png)](media/time-series-insights-explorer/explorer2.png#lightbox)

      - **Predicado**: Use o predicado para filtrar rapidamente seus eventos usando o conjunto de operandos listados na tabela a seguir. Se você realizar uma pesquisa selecionando ou clicando em, o predicado é atualizado automaticamente com base nessa pesquisa. Os tipos de operando com suporte incluem:

         |Operação  |Tipos com suporte  |Notas  |
         |---------|---------|---------|
         |`<`, `>`, `<=`, `>=`     |  Double, DateTime, TimeSpan       |         |
         |`=`, `!=`, `<>`     | Cadeia de caracteres, bool, Double, DateTime, TimeSpan, NULL        |         |
         |IN     | Cadeia de caracteres, bool, Double, DateTime, TimeSpan, NULL        |  Todos os operandos devem ser do mesmo tipo ou ser uma constante nula.        |
         |ATINGIU     | String        |  Somente literais de cadeia de caracteres constantes são permitidos no lado direito. Cadeia de caracteres vazia e NULL não são permitidos.       |

      - **Exemplos de consultas**

         [![consultas de exemplo](media/time-series-insights-explorer/explorer9.png)](media/time-series-insights-explorer/explorer9.png#lightbox)

1. Você pode usar a ferramenta controle deslizante de **tamanho do intervalo** para ampliar e reduzir intervalos no mesmo período de tempo. O controle deslizante fornece um controle mais preciso da movimentação entre grandes fatias de tempo que mostram tendências suaves para fatias tão pequenas quanto o milissegundo, o que permite que você veja cortes granulares de alta resolução de seus dados. O ponto de partida padrão do controle deslizante é definido como a exibição ideal dos dados de sua seleção para balancear a resolução, a velocidade da consulta e a granularidade.

1. A ferramenta de **pincel de tempo** facilita a navegação de um TimeSpan para outro.

1. Use o comando **salvar** para salvar sua consulta atual e compartilhá-la com outros usuários do ambiente. Ao usar **abrir**, você pode ver todas as suas consultas salvas e todas as consultas compartilhadas de outros usuários em ambientes aos quais você tem acesso.

   [Consultas de![](media/time-series-insights-explorer/explorer3.png)](media/time-series-insights-explorer/explorer3.png#lightbox)

## <a name="visualize-data"></a>Visualizar os dados

1. Use a ferramenta de **exibição de perspectiva** para uma exibição simultânea de até quatro consultas exclusivas. O botão **exibição de perspectiva** está no canto superior direito do gráfico.

   [exibição de perspectiva![](media/time-series-insights-explorer/explorer4.png)](media/time-series-insights-explorer/explorer4.png#lightbox)

1. Exiba um gráfico para explorar visualmente seus dados e use as ferramentas de **gráfico** :

    - **Selecione** ou **clique em** um período específico ou em uma única série de dados.
    - Em uma seleção de TimeSpan, você pode aplicar zoom ou explorar eventos.
    - Em uma série de dados, você pode dividir a série por outra coluna, adicionar a série como um novo termo, mostrar apenas a série selecionada, excluir a série selecionada, executar ping dessa série ou explorar eventos da série selecionada.
    - Na área de filtro à esquerda do gráfico, você pode ver todas as séries de dados exibidas e reordená-las por valor ou nome. Você também pode exibir todas as séries de dados ou qualquer série fixa ou desafixada. Você pode selecionar uma única série de dados e dividir a série por outra coluna, adicionar a série como um novo termo, mostrar apenas a série selecionada, excluir a série selecionada, fixar a série ou explorar eventos da série selecionada.
    - Ao exibir vários termos simultaneamente, você pode empilhar, desempilhar, ver dados adicionais sobre uma série de dados e usar o mesmo eixo y em todos os termos. Use os botões no canto superior direito do gráfico.

    [ferramenta de gráfico de![](media/time-series-insights-explorer/explorer5.png)](media/time-series-insights-explorer/explorer5.png#lightbox)

1. Use o **calor** para identificar rapidamente séries de dados exclusivas ou anormais em uma determinada consulta. Somente um termo de pesquisa pode ser visualizado como um calor.

    [![calor](media/time-series-insights-explorer/explorer6.png)](media/time-series-insights-explorer/explorer6.png#lightbox)

1. Quando você explora eventos selecionando ou clicando com o botão direito do mouse, o painel **eventos** é disponibilizado. Aqui, você pode ver todos os seus eventos brutos e exportar seus eventos como arquivos JSON ou CSV. Time Series Insights armazena todos os dados brutos.

    [Eventos de![](media/time-series-insights-explorer/explorer7.png)](media/time-series-insights-explorer/explorer7.png#lightbox)

1. Selecione a guia **estatísticas** depois de explorar eventos para expor padrões e estatísticas de coluna.

    - **Padrões**: esse recurso mostra proativamente os padrões mais significativos estatisticamente em uma região de dados selecionada. Você não precisa examinar milhares de eventos para entender quais padrões exigem mais tempo e energia. Com Time Series Insights, você pode ir diretamente para esses padrões estatisticamente significativos para continuar realizando uma análise. Esse recurso também é útil para investigações post-mortem em dados históricos.
    - **Estatísticas de coluna**: estatísticas de coluna fornecem gráficos e tabelas que dividem dados de cada coluna da série de dados selecionada durante o período selecionado.

      [Estatísticas de![](media/time-series-insights-explorer/explorer8.png)](media/time-series-insights-explorer/explorer8.png#lightbox)

Agora você viu os vários recursos e opções disponíveis no aplicativo Web Time Series Insights Explorer.

## <a name="next-steps"></a>Passos seguintes

- Saiba como [diagnosticar e resolver problemas](time-series-insights-diagnose-and-solve-problems.md) em seu ambiente de time Series insights.

- Faça o Tour de [início rápido Azure Time Series insights](time-series-quickstart.md) guiado.
