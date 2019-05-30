---
title: 'Descrição geral: O que é o Azure Time Series Insights? | Microsoft Docs'
description: Introdução ao Azure Time Series Insights, um serviço novo para análises de dados de séries de tempo e soluções de IoT.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: overview
ms.date: 04/26/2019
ms.custom: seodec18
ms.openlocfilehash: 6ef965b9c22524df5893d5826548a0b07f077062
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237623"
---
# <a name="what-is-azure-time-series-insights"></a>O que é o Azure Time Series Insights?

O Azure Time Series Insights foi concebido para armazenar, visualizar e consultar grandes quantidades de dados de séries de tempo, por exemplo, que são geradas pelos dispositivos de IoT. Se quiser armazenar, gerir, consultar ou visualizar dados de séries de tempo na cloud, o Time Series Insights é provavelmente a solução adequada para si. 

![Fluxograma do Time Series Insights](media/overview/time-series-insights-flowchart.png)

O Time Series Insights tem quatro tarefas principais:

- Ele é totalmente integrado com gateways de cloud, como o IoT Hub do Azure e os Hubs de eventos do Azure. Liga-se facilmente a estas origens de eventos e analisa o JSON das mensagens e estruturas que contêm dados em linhas e colunas limpas. Associa os metadados a telemetria e indexa os dados num arquivo em colunas.
- O Time Series Insights gere o armazenamento dos seus dados. Para se certificar-se de que os dados sempre estão facilmente acessíveis, armazena os dados na memória e SSD para até 400 dias. Pode consultar interativamente milhares de milhões de eventos em segundos – a pedido.
- O Time Series Insights fornece a visualização de out-of-the-box através do Explorador do Time Series Insights. 
- O Time Series Insights fornece um serviço de consulta tanto no Explorador do Time Series Insights através de APIs que são fáceis de integrar para incorporar os seus dados de séries de tempo em aplicativos personalizados.

Se criar uma aplicação de consumo interno ou de clientes externos utilizar, pode utilizar o Time Series Insights como um back-end. Pode usá-lo para indexar, armazenamento e dados de séries de tempo agregado. Para criar uma experiência de utilizador e de visualização personalizada na parte superior, utilize o [SDK de cliente](tutorial-explore-js-client-lib.md). O Time Series Insights é também equipado com vários [APIs de consulta](how-to-shape-query-json.md) ativar estas personalizado cenários.

Os dados de séries de tempo representam a forma como um recurso ou processo muda ao longo do tempo. Carimbos de data / indexa os dados de séries de tempo e o tempo é o mais significativo eixo ao longo do que esses dados são organizados. Dados de séries de tempo, normalmente, são recebidos por ordem sequencial, para que ela normalmente é tratada como uma inserção em vez de uma atualização para a base de dados.

Pode ser um desafio para armazenar, indexar, consultar, analisar e visualizar dados de séries de tempo em grandes volumes.
O Azure Time Series Insights captura e armazena todos os novos eventos como uma linha e alteração de forma eficiente é medida ao longo do tempo. Como resultado, pode examinar com versões anteriores para tirar partido das informações do passado para o ajudar a prever futuras alterações.

## <a name="video"></a>Vídeo

### <a name="learn-more-about-azure-time-series-insights-the-cloud-based-iot-analytics-platformbr"></a>Saiba mais sobre o Azure Time Series Insights, a plataforma de análise de IoT com base na cloud.</br>

[![VÍDEO](https://img.youtube.com/vi/GaARrFfjoss/0.jpg)](https://www.youtube.com/watch?v=GaARrFfjoss)

## <a name="primary-scenarios"></a>Cenários principais

- Store dados de séries de tempo de maneira escalonável. 

   Na sua essência, o Time Series Insights tem uma base de dados concebida a pensar nos dados de séries de tempo. Como é dimensionável e totalmente gerido, o Time Series Insights trata da função de armazenar e gerir eventos.

- Explore os dados em tempo real. 

   O Time Series Insights fornece um Explorador que visualiza dados de todos os fluxos de que num ambiente. Pouco tempo depois de ligar a uma origem de eventos, pode ver, explorar e consultar dados de evento dentro do Time Series Insights. Os dados ajudam-o para validar se um dispositivo emite dados conforme esperado e monitorizar um recurso de IoT para o estado de funcionamento, produtividade e eficácia global. 

- Executar análises de causa raiz e detetar anomalias.

   O Time Series Insights tem ferramentas, como padrões e vistas de ponto de vista para realizar e guardar a análise da causa de raiz de vários passos. O Time Series Insights também funciona com alertas de serviços, como o Azure Stream Analytics, para que pode ver os alertas e anomalias detetadas em tempo real no Explorador do Time Series Insights. 

- Obtenha uma vista global dos dados de séries de tempo que transmite a partir de localizações diferentes para comparação de recurso multi ou site.

   Pode ligar várias origens de eventos a um ambiente do Time Series Insights. Desta forma pode ver os dados transmitidos em fluxo a partir de vários locais diferentes em conjunto numa quase em tempo real. Os utilizadores podem aproveitar essa visibilidade para partilhar dados com os líderes empresariais. Eles podem colaborar melhor com especialistas de domínio que podem aplicar seus conhecimentos para o ajudar a resolver problemas, aplicar as práticas recomendadas e lições aprendidas de partilhar.

- Crie um aplicativo de cliente com base no Time Series Insights. 

   O Time Series Insights expõe as APIs REST Query que pode utilizar para criar aplicações que utilizam dados de séries de tempo.

## <a name="capabilities"></a>Capacidades

- **Comece a trabalhar rapidamente**: O Azure Time Series Insights não requer preparação de dados inicial, para que pode ligar-se rapidamente para milhões de eventos no seu IoT hub ou hub de eventos. Depois de ligar, pode visualizar e interagir com dados de sensor para validar rapidamente as suas soluções de IoT. Pode interagir com os seus dados sem escrever código, e não precisa aprender uma nova linguagem. O Time Series Insights proporciona uma superfície de consultas granular e de texto livre para utilizadores avançados e apontar e clicar exploração.

- **Informações em tempo real de perto**: O Time Series Insights pode ingerir milhões de eventos de sensor por dia, com latência de um minuto. O Time Series Insights ajuda-o a obter informações sobre os seus dados de sensor. Usá-lo para detetar tendências e anomalias, realizar análises de causa raiz e evitar períodos de indisponibilidade dispendiosos. A correlação cruzada entre dados históricos e em tempo real ajuda a localizar tendências ocultas nos dados.

- **Criar soluções personalizadas**: Incorpore dados do Azure Time Series Insights nas suas aplicações existentes. Também pode criar novas soluções personalizadas com as APIs de REST do Time Series Insights. Crie e partilhe vistas personalizadas que pode partilhar para que as outras pessoas possam explorar as suas informações.

- **Escalabilidade**: O Time Series Insights foi concebido para suportar IoT em escala. Pode receber entre 1 milhão e 100 milhões de eventos por dia, com um intervalo de retenção predefinido de 31 dias. Pode ver e analisar fluxos de dados em direto em tempo quase real, juntamente com os dados históricos.

## <a name="get-started"></a>Introdução

Para começar, siga estes passos.

1. Aprovisione um ambiente do Time Series Insights no portal do Azure.
1. Ligar a uma origem de evento, como um hub IoT ou de um hub de eventos. 
1. Carregar dados de referência. Isso não é um serviço adicional.
1. Veja os dados em minutos com o explorador do Time Series Insights.

## <a name="time-series-insights-explorer"></a>Explorador do Time Series Insights

Este diagrama mostra um exemplo de séries de tempo dados insights visualizado através do Explorador do Time Series Insights.

![Explorador do Time Series Insights](media/time-series-insights-explorer/explorer4.png)

## <a name="next-steps"></a>Passos Seguintes

- Explorar a disponibilidade geral do Azure Time Series Insights [ambiente de demonstração gratuito](./time-series-quickstart.md).
- Saiba mais sobre como [planear o Time Series Insights](time-series-insights-environment-planning.md) ambiente.
