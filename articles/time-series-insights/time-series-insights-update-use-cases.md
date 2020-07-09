---
title: Pré-visualização de casos - Azure Time Series Insights / Microsoft Docs
description: Saiba mais sobre os casos de visualização de insights da Série De Tempo Azure.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82580676"
---
# <a name="azure-time-series-insights-preview-use-cases"></a>Casos de visualização de insights de séries de tempo Azure

Este artigo resume vários casos de uso comum para a pré-visualização de Insights da Série de Tempo Azure. As recomendações deste artigo servem como ponto de partida para desenvolver as suas aplicações e soluções com a Time Series Insights.

Especificamente, este artigo responde às seguintes perguntas:

* Quais são os casos de uso comum para insights de séries de tempo?
* Quais são os benefícios de utilizar o Time Series Insights para [a exploração de dados e deteção de anomalias visuais?](#data-exploration-and-visual-anomaly-detection)
* Quais são os benefícios de utilizar o Time Series Insights para [análise operacional e eficiência do processo?](#operational-analysis-and-driving-process-efficiency)
* Quais são os benefícios de usar insights de séries de tempo para [análises avançadas?](#advanced-analytics)

Uma visão geral destes cenários de utilização é descrita nas seguintes secções.

## <a name="introduction"></a>Introdução

Azure Time Series Insights é uma oferta de ponta a ponta, plataforma-as-a-service. É usado para recolher, processar, armazenar, analisar e consultar dados de ioT altamente contextualizados, otimizados em séries de tempo. Time Series Insights é ideal para a exploração de dados ad-hoc e análise operacional. A Time Series Insights é uma oferta de serviços exclusivamente extensível e personalizada que atende às amplas necessidades de implantações de IoT industrial.

## <a name="data-exploration-and-visual-anomaly-detection"></a>Deteção de dados e deteção de anomalias visuais

Explore e analise instantaneamente milhares de milhões de eventos para detetar anomalias e descobrir tendências ocultas nos seus dados. A Time Series Insights oferece um desempenho quase em tempo real para as suas cargas de trabalho de análise de IoT e DevOps.

[![Explorador de dados](media/v2-update-use-cases/data-explorer.png)](media/v2-update-use-cases/data-explorer.png#lightbox)

A maioria dos clientes concorda que o mínimo de tempo necessário para obter informações é uma das características de destaque da Time Series Insights:

* As Insights de Séries de Tempo não requerem preparação antecipada de dados.
* Funciona rapidamente para conectá-lo a biliões de eventos em suas instâncias Azure IoT Hub ou Azure Event Hubs em minutos.
* Uma vez conectado, você pode visualizar e analisar biliões de eventos para detetar anomalias e descobrir tendências ocultas nos seus dados.

Time Series Insights é intuitivo e simples de usar. Pode interagir com os seus dados sem escrever uma única linha de código. Também não há nenhuma nova linguagem que você é necessário aprender, embora o Time Series Insights forneça uma linguagem de consulta baseada em texto granular para utilizadores avançados que estejam familiarizados com o SQL. Também fornece exploração selecionada e clique para principiantes.

Os clientes podem aproveitar a velocidade para diagnosticar rapidamente problemas relacionados com o ativo. Podem realizar análises de DevOps para chegar à causa raiz de um bug numa solução IoT. Podem também identificar áreas a sinalizar para uma investigação mais aprofundada como parte das suas iniciativas em ciência dos dados.

Existem três formas primárias de interagir com os dados armazenados no Time Series Insights:

* A primeira e mais fácil maneira de começar é com o explorador de pré-visualização da Série Time Series. Pode usá-lo para visualizar rapidamente todos os seus dados IoT num só local. Fornece ferramentas como o mapa de calor para ajudá-lo a detetar anomalias nos seus dados. Também fornece uma visão de perspetiva. Utilize-o para comparar até quatro visualizações de um ou mais ambientes de Insights de Séries de Tempo num único painel de instrumentos. O dashboard dá-lhe uma visão dos seus dados da série de tempo em todas as suas localizações. Saiba mais sobre o explorador de [pré-visualização de Insights séries de tempo.](./time-series-insights-update-explorer.md) Para planear o seu ambiente de Insights de Séries Tempo, leia [o planeamento da Time Series Insights](./time-series-insights-update-plan.md).

* A segunda maneira de começar é usar o SDK JavaScript para incorporar rapidamente gráficos e gráficos poderosos na sua aplicação web. Com apenas algumas linhas de código, pode-se autor de consultas poderosas. Use-os para preencher gráficos de linha, gráficos de tortas, gráficos de barras, mapas de calor, grelhas de dados, e muito mais. Todos estes elementos existem fora da caixa usando o SDK. O SDK também abstrata a consulta de Insights de Séries de Tempo APIs. Pode usá-los para autor de predicados semelhantes ao SQL para consultar os dados que pretende mostrar num dashboard. Para soluções híbridas de camada de apresentação, a Time Series Insights oferece URLs parametrizados. Fornecem pontos de conexão sem emenda com o explorador de pré-visualização de séries de tempo para mergulhos profundos em dados.

  * Leia sobre a biblioteca de [clientes da Série De Tempo Insights JS](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) e a documentação do [cliente da Série Tempo Insights](https://github.com/Microsoft/tsiclient) para saber mais sobre o JavaScript SDK.

  * Saiba mais sobre a partilha de URLs e a nova UI, analisando [dados do Visualize no explorador de pré-visualização da Série de Tempo Azure.](time-series-insights-update-explorer.md)

* A terceira maneira de começar é usar as poderosas APIs para consultar dados armazenados em Time Series Insights. A Time Series Insights tem operadores temporais `from` `to` como, `first` e `last` . Tem agregações e transformações `average` `min` como, `max` `split by` `order by` `DateHistogram` e... Tem também operadores de filtragem `has` `in` como, `and` , , , `or` e `greater than` `REGEX` . Todos estes operadores permitem que as aplicações a jusante encontrem rapidamente tendências e padrões interessantes nos seus dados. Use-as para povoar visualizações caseiras para detetar anomalias.

## <a name="operational-analysis-and-driving-process-efficiency"></a>Análise operacional e eficiência do processo de condução

Use time series Insights para monitorizar a saúde, utilização e desempenho do equipamento em escala. A Time Series Insights fornece uma forma fácil de medir a eficiência operacional. A Time Series Insights ajuda a gerir cargas de trabalho de IoT diversificadas e imprevisíveis sem sacrificar a ingestão ou o desempenho de consulta.

[![Descrição Geral](media/v2-update-use-cases/overview.png)](media/v2-update-use-cases/overview.png#lightbox)

O streaming e o processamento contínuo de dados provenientes de processos operacionais podem transformar com sucesso qualquer negócio se for combinado com a tecnologia ou solução certa. Muitas vezes estas soluções são uma combinação de múltiplos sistemas. Permitem a exploração e análise de dados que mudam constantemente, especialmente no reino de IoT, e partilham um padrão comum.

Estes padrões começam frequentemente com plataformas ativadas por IoT que ingerem milhares de milhões de eventos de dispositivos e sensores que abrangem vários locais. Estes sistemas processam e analisam dados de streaming para obter insights e ações em tempo real. Os dados são normalmente arquivados para armazenar quente e frio para análises de lotes em tempo real e em tempo real.

Os dados recolhidos passam por uma série de processamentos para a limpeza e contextualização para cenários de consulta a jusante e analíticos. A Azure oferece serviços ricos que podem ser aplicados a cenários IoT, como manutenção de ativos e fabricação. Estes serviços incluem Time Series Insights, IoT Hub, Event Hubs, Azure Stream Analytics, Azure Functions, Azure Logic Apps, Azure Databricks, Azure Machine Learning e Power BI.

A arquitetura da solução pode ser alcançada da seguinte forma:

* Ingerir dados via IoT Hub ou Event Hubs para segurança, produção e latência de melhor classe.
* Realizar processamento de dados e computações. Funil ingeriu dados através de serviços como Stream Analytics, Logic Apps e Azure Functions. O serviço que utiliza depende das necessidades específicas de processamento de dados.
* Os sinais computatados do gasoduto de processamento são empurrados para a Time Series Insights para armazenamento e análise.

A Time Series Insights oferece quase uma exploração de dados em tempo real e insights baseados em ativos sobre dados históricos. Dependendo das necessidades do seu negócio, os trabalhos de MapReduce e Hive podem funcionar em dados armazenados em Time Series Insights, ligando Time Series Insights ao Azure HDInsight. Os dados armazenados em Time Series Insights estão disponíveis para o Power BI e outras aplicações de clientes através da consulta pública de superfície da Time Series Insights APIs. Estes dados podem ser usados para cenários profundos de inteligência empresarial e operacional.

## <a name="advanced-analytics"></a>Análise avançada

Integre-se com serviços de análise avançadas como Machine Learning e Azure Databricks. Time Series Insights ingressa dados brutos de milhões de dispositivos. Adiciona dados contextuais que podem ser consumidos sem problemas por um conjunto de serviços de análise Azure.

[![Análise](media/v2-update-use-cases/advanced-analytics.png)](media/v2-update-use-cases/advanced-analytics.png#lightbox)

Análises avançadas e aprendizagem automática consomem e processam grandes volumes de dados. Estes dados são utilizados para tomar decisões baseadas em dados e realizar análises preditivas. Em casos de utilização de IoT, algoritmos de análise avançados aprendem com os dados recolhidos de milhões de dispositivos. Estes dispositivos transmitem dados várias vezes a cada segundo. Os dados recolhidos de dispositivos IoT são crus. Carece de informação contextual, como a localização do dispositivo e a unidade da leitura do sensor. Como resultado, os dados brutos são difíceis de consumir diretamente para análises avançadas.

Time Series Insights faz a ponte entre os dados IoT e a análise avançada de duas formas simples e económicas:

* Em primeiro lugar, a Time Series Insights recolhe dados de telemetria em bruto de milhões de dispositivos utilizando o IoT Hub. Enriquece dados com informação contextual e transforma dados num formato de parquet. Este formato pode facilmente integrar-se com outros serviços de análise avançada, tais como Machine Learning, Azure Databricks e aplicações de terceiros.

    A Time Series Insights pode servir de fonte de verdade para todos os dados em toda uma organização. Cria um repositório central para que as cargas de trabalho a jusante a jusante consumam. Como o Time Series Insights é um serviço de armazenamento quase em tempo real, os modelos de análise avançados podem aprender continuamente a partir de dados de telemetria IoT. Como resultado, os modelos podem fazer previsões mais precisas.

* Em segundo lugar, a saída de modelos de machine learning e previsão pode ser alimentada no Time Series Insights para visualizar e armazenar os seus resultados. Este procedimento ajuda as organizações a otimizar e ajustar os seus modelos. A Time Series Insights torna simples visualizar dados de telemetria de streaming no mesmo plano que as saídas do modelo treinado. Desta forma, ajuda as equipas de ciência de dados a detetar anomalias e a identificar padrões.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre o explorador de [pré-visualização de Insights séries de tempo.](./time-series-insights-update-explorer.md)
* Leia [Séries de Tempo Insights Preview planejando](./time-series-insights-update-plan.md) planear o seu ambiente.
* Leia a documentação do [cliente da Time Series Insights.](https://github.com/Microsoft/tsiclient)
