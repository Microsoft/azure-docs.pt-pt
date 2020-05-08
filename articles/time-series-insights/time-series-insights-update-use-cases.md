---
title: Pré-visualização casos de utilização - Azure Time Series Insights [ Microsoft Docs
description: Saiba mais sobre os casos de pré-visualização da Série de Tempo Azure Insights.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 04/29/2020
ms.custom: seodec18
ms.openlocfilehash: 93813dfaecf40fcb5cab0856d013fb890118886c
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82580676"
---
# <a name="azure-time-series-insights-preview-use-cases"></a>Casos de pré-visualização da Série de Tempo Azure Insights

Este artigo resume vários casos de uso comum para a Pré-visualização de Insights da Série De Tempo Azure. As recomendações neste artigo servem como ponto de partida para desenvolver as suas aplicações e soluções com insights da Time Series.

Especificamente, este artigo responde às seguintes perguntas:

* Quais são os casos de uso comum para Time Series Insights?
* Quais são os benefícios de usar insights da Série tempo para a exploração de [dados e deteção de anomalias visuais?](#data-exploration-and-visual-anomaly-detection)
* Quais são os benefícios de utilizar insights da Série Time para análise operacional e eficiência de [processos?](#operational-analysis-and-driving-process-efficiency)
* Quais são os benefícios de usar insights da Série time para [análises avançadas?](#advanced-analytics)

Uma visão geral destes cenários de utilização é descrita nas seguintes secções.

## <a name="introduction"></a>Introdução

A Azure Time Series Insights é uma oferta de ponta a ponta, plataforma-como-um-serviço. É usado para recolher, processar, armazenar, analisar e consulta altamente contextualizada, dados à escala ioT otimizada em séries de tempo. Time Series Insights é ideal para a exploração de dados ad-hoc e análise operacional. Time Series Insights é uma oferta de serviço exclusivamente extensível e personalizada que satisfaz as grandes necessidades das implementações industriais de IoT.

## <a name="data-exploration-and-visual-anomaly-detection"></a>Exploração de dados e deteção de anomalias visuais

Explore e analise instantaneamente milhares de milhões de eventos para detetar anomalias e descobrir tendências ocultas nos seus dados. Time Series Insights oferece desempenho quase em tempo real para as suas cargas de trabalho de análise IoT e DevOps.

[![Explorador de dados](media/v2-update-use-cases/data-explorer.png)](media/v2-update-use-cases/data-explorer.png#lightbox)

A maioria dos clientes concorda que o tempo mínimo necessário para obter informações é uma das características de destaque da Time Series Insights:

* A Time Series Insights não requer preparação antecipada de dados.
* Funciona rapidamente para ligá-lo a milhares de milhões de eventos nas instâncias do seu Hub Azure IoT ou Azure Event Hubs em minutos.
* Uma vez conectado, pode visualizar e analisar milhares de milhões de eventos para detetar anomalias e descobrir tendências ocultas nos seus dados.

Time Series Insights é intuitivo e simples de usar. Pode interagir com os seus dados sem escrever uma única linha de código. Também não há nenhuma nova linguagem que seja obrigado a aprender, embora time Series Insights forneça uma linguagem de consulta baseada em texto granular para utilizadores avançados que estejam familiarizados com o SQL. Também fornece exploração selecionada e clique para novatos.

Os clientes podem aproveitar a velocidade para diagnosticar rapidamente problemas relacionados com os ativos. Podem realizar a análise de DevOps para chegar à causa principal de um inseto numa solução IoT. Podem também identificar áreas a sinalizar para uma investigação mais aprofundada como parte das suas iniciativas em ciência dos dados.

Existem três formas primárias de interagir com os dados armazenados na Time Series Insights:

* A primeira e mais fácil maneira de começar é com o explorador de pré-visualização time series Insights. Pode usá-lo para visualizar rapidamente todos os seus dados ioT num só local. Fornece ferramentas como o mapa de calor para ajudá-lo a detetar anomalias nos seus dados. Também proporciona uma perspetiva de perspetiva. Use-o para comparar até quatro visualizações de um ou mais ambientes time series insights num único dashboard. O painel de instrumentos dá-lhe uma visão dos seus dados da série de tempo em todas as suas localizações. Saiba mais sobre o explorador de [pré-visualização](./time-series-insights-update-explorer.md)de Insights da Série De Tempo. Para planear o seu ambiente time series Insights, leia o [planeamento da Time Series Insights.](./time-series-insights-update-plan.md)

* A segunda maneira de começar é usar o JavaScript SDK para incorporar rapidamente gráficos e gráficos poderosos na sua aplicação web. Com apenas algumas linhas de código, pode-se autoria de perguntas poderosas. Use-os para povoar gráficos de linhas, gráficos de tartes, gráficos de barras, mapas de calor, grelhas de dados, e muito mais. Todos estes elementos existem fora da caixa utilizando o SDK. O SDK também abstrata apis de consulta da Time Series Insights. Pode usá-los para autores predicados semelhantes ao SQL para consultar os dados que pretende mostrar num dashboard. Para soluções híbridas de camada de apresentação, a Time Series Insights oferece URLs parametrizados. Eles fornecem pontos de ligação sem emenda com o explorador de pré-visualização de Insights da Série de Tempo para mergulhos profundos em dados.

  * Leia sobre a biblioteca de [clientes Da Série De Tempo Insights JS](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) e a documentação do cliente da Time Series [Insights](https://github.com/Microsoft/tsiclient) para saber mais sobre o JavaScript SDK.

  * Saiba mais sobre a partilha de URLs e os novos UI através da revisão de dados visualize no explorador de [pré-visualização](time-series-insights-update-explorer.md)da Série de Tempo Azure Insights .

* A terceira maneira de começar é usar as APIs poderosas para consultar os dados armazenados em Time Series Insights. Time Series Insights tem `from`operadores temporais como, `to`e `first` `last`. Tem `average`agregações e transformações como, `min` `max`, `split by` `order by`, `DateHistogram`e . Tem também operadores de `has` `in`filtragem `or` `greater than`como, `REGEX` `and`, , , e . Todos estes operadores permitem que as aplicações a jusante encontrem rapidamente tendências e padrões interessantes nos seus dados. Use-as para povoar visualizações caseiras para detetar anomalias.

## <a name="operational-analysis-and-driving-process-efficiency"></a>Análise operacional e eficiência do processo de condução

Utilize insights da Time Series para monitorizar a saúde, utilização e desempenho do equipamento em escala. Time Series Insights fornece uma maneira fácil de medir a eficiência operacional. Time Series Insights ajuda a gerir cargas de trabalho ioT diversificadas e imprevisíveis sem sacrificar a ingestão ou o desempenho da consulta.

[![Descrição geral](media/v2-update-use-cases/overview.png)](media/v2-update-use-cases/overview.png#lightbox)

O streaming e o processamento contínuo de dados provenientes de processos operacionais podem transformar com sucesso qualquer negócio se for combinado com a tecnologia ou solução certa. Muitas vezes estas soluções são uma combinação de múltiplos sistemas. Permitem a exploração e análise de dados que mudam constantemente, especialmente no reino ioT, e partilham um padrão comum.

Estes padrões começam frequentemente com plataformas ativadas por IoT que ingebem biliões de eventos a partir de dispositivos e sensores que abrangem vários locais. Estes sistemas processam e analisam dados de streaming para obter insights e ações em tempo real. Os dados são normalmente arquivados para uma loja quente e fria para análise sinuosa e de lote.

Os dados recolhidos passam por uma série de processamento sinuoso para limpar e contextualizar para cenários de consulta a jusante e análise. O Azure oferece serviços ricos que podem ser aplicados a cenários ioT, como manutenção de ativos e fabrico. Estes serviços incluem Insights da Série Time, IoT Hub, Hubs de Eventos, Azure Stream Analytics, Funções Azure, Aplicações Lógicas Azure, Tijolos de Dados Azure, Machine Learning Azure e Power BI.

A arquitetura de solução pode ser alcançada da seguinte forma:

* Ingerir dados através do IoT Hub ou do Event Hubs para a segurança, produção e latência de melhor classe.
* Realizar o processamento e a computação de dados. O funil ingeriu dados através de serviços como Stream Analytics, Logic Apps e Azure Functions. O serviço que utiliza depende das necessidades específicas de processamento de dados.
* Os sinais computacionados do gasoduto de processamento são empurrados para insights da Série Time para armazenamento e análise.

Time Series Insights oferece quase exploração de dados em tempo real e insights baseados em ativos sobre dados históricos. Dependendo das necessidades do seu negócio, os trabalhos mapReduce e Hive podem ser executados em dados armazenados em Time Series Insights, ligando insights da Série Time ao Azure HDInsight. Os dados armazenados na Time Series Insights estão disponíveis para Power BI e outras aplicações de clientes através das APIs de consulta de superfície pública da Time Series Insights. Estes dados podem ser utilizados para cenários de inteligência operacional e de negócios profundos.

## <a name="advanced-analytics"></a>Análise avançada

Integre com serviços de análise avançados como Machine Learning e Azure Databricks. Time Series Insights inala dados brutos de milhões de dispositivos. Adiciona dados contextuais que podem ser consumidos sem problemas por um conjunto de serviços de análise Azure.

[![Análise](media/v2-update-use-cases/advanced-analytics.png)](media/v2-update-use-cases/advanced-analytics.png#lightbox)

Análise avançada e machine learning consomem e processam grandes volumes de dados. Estes dados são utilizados para tomar decisões baseadas em dados e realizar análises preditivas. Nos casos de utilização de IoT, algoritmos de análise avançados aprendem com os dados recolhidos a partir de milhões de dispositivos. Estes dispositivos transmitem dados várias vezes a cada segundo. Os dados recolhidos dos dispositivos IoT são crus. Carece de informação contextual, como a localização do dispositivo e a unidade da leitura do sensor. Como resultado, os dados brutos são difíceis de consumir diretamente para análises avançadas.

Time Series Insights colmata o fosso entre os dados ioT e a análise avançada de duas formas simples e económicas:

* Primeiro, time Series Insights recolhe dados de telemetria crua de milhões de dispositivos usando IoT Hub. Enriquece dados com informação contextual e transforma os dados num formato parquet. Este formato pode facilmente integrar-se com outros serviços de análise avançada, como machine learning, Azure Databricks e aplicações de terceiros.

    Time Series Insights pode servir como fonte de verdade para todos os dados através de uma organização. Cria um repositório central para que as cargas de trabalho a jusante consumadas. Como a Time Series Insights é um serviço de armazenamento quase em tempo real, os modelos de análise avançados podem aprender continuamente a partir de dados de telemetria IoT. Como resultado, os modelos podem fazer previsões mais precisas.

* Em segundo lugar, a produção de modelos de machine learning e previsão pode ser alimentada em Time Series Insights para visualizar e armazenar os seus resultados. Este procedimento ajuda as organizações a otimizar e ajustar os seus modelos. Time Series Insights torna simples visualizar dados de telemetria em streaming no mesmo plano que as saídas do modelo treinado. Desta forma, ajuda as equipas de ciência de dados a detetar anomalias e a identificar padrões.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre o explorador de [pré-visualização](./time-series-insights-update-explorer.md)de Insights da Série De Tempo.
* Leia [a Pré-visualização](./time-series-insights-update-plan.md) da Time Series Insights para planear o seu ambiente.
* Leia a documentação do cliente da [Time Series Insights.](https://github.com/Microsoft/tsiclient)
