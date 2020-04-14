---
title: 'Visão geral: O que é Azure Time Series Insights? - Azure Time Series Insights [ Azure Time Series Insights ] Microsoft Docs'
description: Introdução ao Azure Time Series Insights, um serviço novo para análises de dados de séries de tempo e soluções de IoT.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.topic: overview
ms.date: 04/13/2020
ms.custom: seodec18
ms.openlocfilehash: 59149b2ca598104d8aca9b4e5e60194a8f6398bf
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81269963"
---
# <a name="what-is-azure-time-series-insights"></a>O que é o Azure Time Series Insights?

A Azure Time Series Insights é construída para armazenar, visualizar e consultar grandes quantidades de dados da série time, como os gerados por dispositivos IoT. Se quiser armazenar, gerir, consultar ou visualizar dados de séries de tempo na cloud, o Time Series Insights é provavelmente a solução adequada para si.

[![Fluxograma do Time Series Insights](media/overview/time-series-insights-flowchart.png)](media/overview/time-series-insights-flowchart.png#lightbox)

O Time Series Insights tem quatro tarefas principais:

- Está totalmente integrado com portais de nuvem como o Azure IoT Hub e o Azure Event Hubs. Liga-se facilmente a estas origens de eventos e analisa o JSON das mensagens e estruturas que contêm dados em linhas e colunas limpas. Associa os metadados a telemetria e indexa os dados num arquivo em colunas.
- Time Series Insights gere o armazenamento dos seus dados. Para garantir que os dados são sempre facilmente acessíveis, armazena os seus dados na memória e SSDs durante um máximo de 400 dias. Pode consultar interativamente biliões de eventos em segundos- a pedido.
- Time Series Insights fornece visualização fora da caixa através do explorador time series Insights.
- Time Series Insights fornece um serviço de consulta, tanto no explorador time series Insights como usando APIs que são fáceis de integrar para incorporar os dados da sua série de tempo em aplicações personalizadas.

Se construir uma aplicação para consumo interno ou para clientes externos, pode utilizar os Time Series Insights como backback. Pode usá-lo para indexar, armazenar e agregar dados da série de tempo. Para construir uma visualização personalizada e experiência do utilizador no topo, use o [Client SDK](https://github.com/microsoft/tsiclient/blob/master/docs/API.md). Time Series Insights também está equipado com várias [APIs](how-to-shape-query-json.md) de consulta para permitir estes cenários personalizados.

Os dados de séries de tempo representam a forma como um recurso ou processo muda ao longo do tempo. Os dados da série temporal são indexados por selos temporais, e o tempo é o eixo mais significativo ao longo do qual esses dados são organizados. Os dados da série de tempo normalmente chegam por ordem sequencial, por isso é tratado como uma inserção em vez de uma atualização para a sua base de dados.

Pode ser um desafio para armazenar, indexar, consultar, analisar e visualizar dados da série de tempo em grandes volumes.
A Azure Time Series Insights captura e armazena todos os novos eventos como uma linha, e a mudança é medida de forma eficiente ao longo do tempo. Como resultado, pode olhar para trás para tirar informações do passado para ajudar a prever mudanças futuras.

## <a name="video"></a>Vídeo

### <a name="learn-more-about-azure-time-series-insights-the-cloud-based-iot-analytics-platformbr"></a>Saiba mais sobre o Azure Time Series Insights, a plataforma de análise IoT baseada na nuvem.</br>

[![VÍDEO](https://img.youtube.com/vi/GaARrFfjoss/0.jpg)](https://www.youtube.com/watch?v=GaARrFfjoss)

## <a name="primary-scenarios"></a>Cenários principais

- Armazenar dados da série de tempo de forma escalável.

   Na sua essência, o Time Series Insights tem uma base de dados concebida a pensar nos dados de séries de tempo. Por ser escalável e totalmente gerido, a Time Series Insights trata do trabalho de armazenar e gerir eventos.

- Explore os dados em tempo real.

   Time Series Insights fornece um explorador que visualiza todos os dados que fluem para um ambiente. Pouco depois de se ligar a uma fonte de evento, pode ver, explorar e consultar dados de eventos dentro da Time Series Insights. Os dados ajudam-no a validar se um dispositivo emite dados como esperado e a monitorizar um ativo IoT para a saúde, produtividade e eficácia global.

- Efetuar análises de causa-raiz e detetar anomalias.

   Time Series Insights tem ferramentas como padrões e vistas de perspetiva para conduzir e salvar análises de causas de raiz multistep. Time Series Insights também trabalha com serviços de alerta como O Azure Stream Analytics para que possa ver alertas e anomalias detetadas em tempo quase real no explorador de Insights da Série Tempo.

- Obtenha uma visão global dos dados da série time que flui de locais diferentes para comparação de vários ativos ou site.

   Pode ligar várias origens de eventos a um ambiente do Time Series Insights. Desta forma, pode ver dados que fluem de vários locais diferentes em quase tempo real. Os utilizadores podem aproveitar esta visibilidade para partilhar dados com líderes empresariais. Podem colaborar melhor com especialistas em domínios que possam aplicar os seus conhecimentos para ajudar a resolver problemas, aplicar as melhores práticas e partilhar aprendizagens.

- Construa uma aplicação de cliente para além da Time Series Insights.

   Time Series Insights expõe APIs de consulta REST que pode usar para construir aplicações que usam dados da série de tempo.

## <a name="capabilities"></a>Capacidades

- **Inicie-se rapidamente**: A Azure Time Series Insights não requer a preparação antecipada de dados, para que possa rapidamente ligar-se a milhões de eventos no seu hub ioT ou centro de eventos. Depois de se ligar, pode visualizar e interagir com dados do sensor para validar rapidamente as suas soluções IoT. Pode interagir com os seus dados sem escrever código, e não precisa de aprender uma nova linguagem. Time Series Insights fornece uma superfície de consulta de texto livre e granular para utilizadores avançados e exploração de pontos e cliques.

- **Quase insights em tempo real**: Time Series Insights pode ingerir milhões de eventos de sensores por dia, com latência de um minuto. Time Series Insights ajuda-o a obter informações sobre os seus dados de sensores. Use-o para detetar tendências e anomalias, realizar análises de causa-raiz e evitar tempos de paragem dispendiosos. A relação entre dados em tempo real e históricos ajuda-o a encontrar tendências ocultas nos dados.

- **Construa soluções personalizadas**: Inbed Azure Time Series Insights dados nas suas aplicações existentes. Também pode criar novas soluções personalizadas com as APIs REST REST da Série De Tempo. Crie e partilhe vistas personalizadas que pode partilhar para que as outras pessoas possam explorar as suas informações.

- **Escalabilidade**: Time Series Insights foi concebido para suportar ioT em escala. Pode receber entre 1 milhão e 100 milhões de eventos por dia, com um intervalo de retenção predefinido de 31 dias. Pode visualizar e analisar fluxos de dados ao vivo em tempo real, ao lado de dados históricos.

## <a name="get-started"></a>Introdução

Para começar, siga estes passos.

1. Fornecer um ambiente time series insights no portal Azure.
1. Ligue-se a uma fonte de evento como um hub IoT ou um centro de eventos.
1. Faça upload de dados de referência. Isto não é um serviço adicional.
1. Reveja e exiba os seus dados em minutos com o explorador time series Insights.

## <a name="explorer"></a>Explorador

Veja, analise e descubra tendências nos seus dados usando o explorador de Insights da Série De Tempo Azure.

![Explorador do Time Series Insights](media/overview/time-series-insights-explorer-panel.png)

Aprenda a usar o explorador de Insights da [Série De Tempo Azure](time-series-insights-explorer.md) e tire informações dos seus dados.

## <a name="next-steps"></a>Passos seguintes

- Explore o Ambiente de [demonstração geral](./time-series-quickstart.md)da Azure Time Series Insights.

- Saiba mais sobre como planear o seu ambiente time [series Insights.](time-series-insights-environment-planning.md)
