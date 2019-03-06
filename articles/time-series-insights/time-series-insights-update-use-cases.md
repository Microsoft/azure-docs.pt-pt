---
title: Casos de utilização de pré-visualização de informações de série de tempo do Azure | Documentos da Microsoft
description: Compreenda os casos de utilização de pré-visualização do Azure Time Series Insights.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: f4e14435c2ccab98619bec57fef0e121e2df4403
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57431619"
---
# <a name="azure-time-series-insights-preview-use-cases"></a>Casos de utilização de pré-visualização do Time Series Insights do Azure

Este artigo fornece uma visão geral dos vários casos de utilização comuns para pré-visualização do Azure Time Series Insights. As recomendações neste artigo servem como um ponto de partida ao desenvolver aplicativos e soluções com o Time Series Insights.

Depois de ler este artigo, pode responder a perguntas a seguir:

* Quais são os casos de utilização comuns para Time Series Insights?
* Quais são os benefícios do uso do Time Series Insights para exploração de dados e deteção de anomalias visual?
* Quais são as vantagens de utilizar o Time Series Insights para análises operacionais e eficiência do processo?
* Quais são as vantagens de utilizar o Time Series Insights para análise avançada?

Este documento fornece uma visão geral do que a pré-visualização do Azure Time Series Insights foi concebido para casos de utilização.

## <a name="introduction"></a>Introdução

O Time Series Insights é uma oferta de plataforma-como-serviço de ponto-a-ponto. É utilizado para ingerir, processar, armazenar e consultar contextualizados altamente otimizado de série de tempo, dados de IoT à escala. O Time Series Insights é ideal para exploração de dados ad hoc e análise operacional. O Time Series Insights é um serviço de exclusivamente extensível e personalizado, que cumpre amplo precisa de implementações de IoT industriais da oferta.

## <a name="data-exploration-and-visual-anomaly-detection"></a>Exploração de dados e deteção visual de anomalias

Explore e analise instantaneamente milhares de milhões de eventos para detetar anomalias e descobrir tendências ocultas nos dados. O Time Series Insights oferece desempenho quase em tempo real para as cargas de trabalho de análise de IoT e DevOps.

![Explorador de dados][1]

A maioria dos clientes concorda que a hora para obter conhecimentos aprofundados é entre os ativos mais fortes do Time Series Insights. O Time Series Insights não requer nenhuma preparação de dados inicial. Funciona rapidamente para ligá-lo a milhões de eventos no seu IoT Hub do Azure ou Event Hubs do Azure em minutos. Assim que estiver ligado, pode visualizar e analisar milhares de milhões de eventos para detetar anomalias e descobrir tendências ocultas nos seus dados. 

O Time Series Insights é intuitivo e simples de usar. Pode interagir com os seus dados sem ter de escrever uma única linha de código. Também não há nenhuma linguagem a aprender. O Time Series Insights fornece granulares com base em texto consultar para utilizadores avançados familiarizados com o SQL. Ele também fornece a exploração selecionar e clicar para Novatos.

Os clientes tirar partido da velocidade para diagnosticar problemas relacionados com o recurso rapidamente. Eles podem executar DevOps para obter a causa de um bug numa solução de IoT. Também pode identificar áreas a investigar quanto a iniciativas de ciência de dados.  

Existem três formas de interagir com dados armazenados no Time Series Insights:

- A forma de primeira e mais fácil de começar é com o Explorador do Time Series Insights pré-visualização. Pode usá-lo a visualizar rapidamente todos os seus dados de IoT num único local. Ele fornece ferramentas como o mapa térmico para ajudá-lo a detetar anomalias nos seus dados. Ele também fornece uma visão de ponto de vista. Utilize-o para comparar até quatro vistas a partir de um ou mais ambientes do Time Series Insights num único dashboard. O dashboard dá-lhe uma vista dos seus dados de séries temporais em todas as suas localizações. Saiba mais sobre o [Explorador de pré-visualização do Time Series Insights](./time-series-insights-update-explorer.md). Para planejar seu ambiente do Time Series Insights, leia [planejamento do Time Series Insights](./time-series-insights-update-plan.md).

- A segunda maneira de começar é utilizar o SDK de JavaScript para incorporar rapidamente poderosos de gráficos em seu aplicativo web. Com apenas algumas linhas de código, pode criar consultas eficientes. Utilize-os para preencher os gráficos de linhas, gráficos circulares, gráficos de barras, mapas térmicos, grades de dados e muito mais. Todos esses elementos existem out-of-the-box através do SDK. O SDK também abstrai APIs de consulta do Time Series Insights. Pode usá-los para criar predicados do tipo SQL para consultar os dados que pretende mostrar num dashboard. Para soluções de camada de apresentação de híbrida, o Time Series Insights oferece URLs parametrizados. Eles fornecem os pontos de ligação totalmente integrada com o Explorador do Time Series Insights pré-visualização para tópicos avançados sobre dados. Para saber mais sobre o JavaScript SDK, veja a [biblioteca de cliente do Time Series Insights JS](https://docs.microsoft.com/azure/time-series-insights/tutorial-explore-js-client-lib) e o [cliente do Time Series Insights](https://github.com/Microsoft/tsiclient) documentação. Para saber mais sobre URLs parametrizados, leia [URLs parametrizados](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-parameterized-urls).

- A terceira maneira de começar é usar as poderosas APIs para consultar dados armazenados no Time Series Insights. O Time Series Insights tem operadores temporais, tais como de, a primeira e última. Ele tem agregações e divisão de transformações, como média, Mín, Máx, por ordem e DateHistogram. Também tem filtragem operadores como tem, no e, em alternativa, maior que e REGEX. Todos esses operadores ativar aplicações a jusante encontrar rapidamente as tendências e padrões interessantes nos seus dados. Utilize-os para preencher os próprios visualizações para detetar anomalias.

## <a name="operational-analysis-and-driving-process-efficiency"></a>Promover a eficiência dos processos e análise operacional

Utilize o Time Series Insights para monitorizar o estado de funcionamento, a utilização e o desempenho dos equipamentos em escala. O Time Series Insights proporciona uma forma fácil de medir a eficiência operacional. O Time Series Insights ajuda a gerir cargas de trabalho de IoT diversas e imprevisíveis, sem sacrificar a ingestão ou o desempenho de consultas.

![Descrição geral][2]

Processamento contínuo de dados provenientes de processos operacionais e de transmissão em fluxo com êxito podem transformar qualquer empresa se for combinada com a tecnologia certa ou solução. Muitas vezes, essas soluções são uma combinação de vários sistemas. Eles permitem a exploração e análise de dados que é alterada constantemente, especialmente no realm IoT e compartilha um padrão comum.

Esses padrões, muitas vezes, começam com plataformas preparadas para IoT que ingerir milhões de eventos a partir de dispositivos e sensores que abrangem várias localidades. Estes sistemas de processam e analisar dados de transmissão em fluxo para derivar informações em tempo real e ações. Dados normalmente são arquivados para entender e armazenamento de esporádico para quase em tempo real e análises em lote. 

Dados recolhidos passem por uma série de processamento para limpar e a contextualizar os-lo para cenários de consulta e análise de downstream. O Azure oferece serviços avançados que podem ser aplicados a cenários de IoT, como manutenção de recursos e produção. Esses serviços incluem o Time Series Insights, o IoT Hub, os Hubs de eventos, Azure Stream Analytics, as funções do Azure, Azure Logic Apps, Azure Databricks, Azure Machine Learning e Power BI.

Arquitetura da solução pode ser obtida da seguinte forma:

- Ingestão de dados através do IoT Hub ou Hubs de eventos de segurança de melhor em classe, débito e latência. 
- Efetue o processamento de dados e computações. Funil dados ingeridos por meio de serviços como o Stream Analytics, Logic Apps e as funções do Azure. O serviço que utiliza depende das necessidades de processamento de dados específicas. 
- Sinais calculadas do pipeline de processamento são enviadas para o Time Series Insights para armazenar e análise. 

O Time Series Insights oferece quase exploração de dados em tempo real e com base no recurso de informações sobre os dados históricos. Consoante as suas necessidades de negócio, as tarefas de MapReduce e Hive podem executar em dados armazenados no Time Series Insights, ao ligar o Time Series Insights para o Azure HDInsight. Dados armazenados no Time Series Insights estão disponíveis para o Power BI e outras aplicações de cliente por meio da consulta superfície pública APIs do Time Series Insights. Estes dados podem ser utilizados para cenários de informações operacionais e de completos para empresas.

## <a name="advanced-analytics"></a>Análise avançada

Integração com serviços de análises avançadas, como o Machine Learning e o Azure Databricks. Time Series Insights ingresses dados não processados de milhões de dispositivos. Ele adiciona dados contextuais que podem ser consumidos por um conjunto de serviços de análise do Azure de forma totalmente integrada.

![Análise][3]

Análise avançada e de machine learning consumirem e processam grandes volumes de dados. Estes dados são utilizados para tomar decisões condicionadas por dados e realizar análises preditivas. Em casos de utilização do IoT, os algoritmos de análise avançada Aprenda com os dados recolhidos a partir de milhões de dispositivos. Estes dispositivos transmitem dados várias vezes por segundo. Os dados recolhidos a partir de dispositivos IoT são não processados. Não tem informações contextuais, como a localização do dispositivo e a unidade da leitura de sensor. Como resultado, os dados brutos são difícil de consumir diretamente para análises avançadas.

O Time Series Insights preenche a lacuna entre os dados de IoT e análise avançada de duas formas simples e econômicas: 

- Em primeiro lugar, o Time Series Insights recolhe dados de telemetria não processados de milhões de dispositivos através do IoT Hub. Ele otimiza os dados com informações contextuais e transforma os dados num formato parquet. Este formato pode integrar facilmente com outras análises avançadas serviços, como o Machine Learning, Azure Databricks e aplicações de terceiros. 

    O Time Series Insights pode servir como a fonte verdadeira para todos os dados em toda a organização. Ele cria um repositório central para análise de downstream cargas de trabalho para consumir. Uma vez que o Time Series Insights é um quase em tempo real de armazenamento do serviço, análises avançadas, modelos de aprender continuamente com dados de telemetria de IoT recebidos. Como resultado, os modelos podem fazer previsões mais precisas.

- Em segundo lugar, o Time Series Insights podem ser alimentado a saída de modelos de previsão e de aprendizagem automática para visualizar e armazenar os resultados. Este procedimento ajuda as organizações a otimizar e ajustar seus modelos. O Time Series Insights torna simples para visualizar a transmissão em fluxo de dados de telemetria no mesmo plano como saídas do modelo treinado. Dessa forma, ele ajuda as equipas de ciência de dados detetar anomalias e identificar padrões.  

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre o [Explorador de pré-visualização do Time Series Insights](./time-series-insights-update-explorer.md).
- Para planejar seu ambiente, leia [planejamento de pré-visualização do Time Series Insights](./time-series-insights-update-plan.md).
- Leitura a [cliente do Time Series Insights](https://github.com/Microsoft/tsiclient) documentação.

<!-- Images -->
[1]: media/v2-update-use-cases/data-explorer.svg
[2]: media/v2-update-use-cases/overview.svg
[3]: media/v2-update-use-cases/advanced-analytics.svg
