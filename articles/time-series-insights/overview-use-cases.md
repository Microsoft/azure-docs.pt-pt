---
title: Casos de uso da Gen2 - Azure Time Series Insights Gen2 Microsoft Docs
description: Saiba mais sobre os casos de utilização da Azure Time Series Insights Gen2.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/16/2020
ms.custom: seodec18
ms.openlocfilehash: 7d97958c5fd1274495da88c064b63e59e354f691
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97606968"
---
# <a name="azure-time-series-insights-gen2-use-cases"></a>Azure Time Series Insights Gen2 usam casos

Este artigo resume vários casos de uso comum para Azure Time Series Insights Gen2. As recomendações deste artigo servem como ponto de partida para desenvolver as suas aplicações e soluções com a Azure Time Series Insights Gen2.

Especificamente, este artigo responde às seguintes perguntas:

* Quais são os casos de uso comum para Azure Time Series Insights Gen2?
* Quais são os benefícios de usar a Azure Time Series Insights Gen2 para [a exploração de dados e deteção de anomalias visuais?](#data-exploration-and-visual-anomaly-detection)
* Quais são os benefícios de utilizar a Azure Time Series Insights Gen2 para [análise operacional e eficiência do processo?](#operational-analysis-and-driving-process-efficiency)
* Quais são os benefícios de usar a Azure Time Series Insights Gen2 para [análises avançadas?](#advanced-analytics)

Uma visão geral destes cenários de utilização é descrita nas seguintes secções.

## <a name="introduction"></a>Introdução

Azure Time Series Insights Gen2 é uma oferta de ponta a ponta, plataforma-como-serviço. É usado para recolher, processar, armazenar, analisar e consultar dados de ioT altamente contextualizados, otimizados em séries de tempo. É ideal para a exploração de dados ad-hoc e análise operacional. Azure Time Series Insights Gen2 é uma oferta de serviços exclusivamente extensível e personalizada que atende às amplas necessidades de implantações industriais de IoT.

## <a name="data-exploration-and-visual-anomaly-detection"></a>Deteção de dados e deteção de anomalias visuais

Explore e analise instantaneamente milhares de milhões de eventos para detetar anomalias e descobrir tendências ocultas nos seus dados. Azure Time Series Insights Gen2 oferece um desempenho quase em tempo real para as suas cargas de trabalho de análise de IoT e DevOps.

[![Explorador de dados](media/v2-update-use-cases/data-explorer.png)](media/v2-update-use-cases/data-explorer.png#lightbox)

A maioria dos clientes concorda que o mínimo de tempo necessário para obter informações é uma das características de destaque da Azure Time Series Insights Gen2:

* Azure Time Series Insights Gen2 não requer preparação antecipada de dados.
* Funciona rapidamente para conectá-lo a biliões de eventos em suas instâncias Azure IoT Hub ou Azure Event Hubs em minutos.
* Uma vez conectado, você pode visualizar e analisar biliões de eventos para detetar anomalias e descobrir tendências ocultas nos seus dados.

Azure Time Series Insights Gen2 é intuitivo e simples de usar. Pode interagir com os seus dados sem escrever uma única linha de código. Também não há nenhuma nova linguagem que você é obrigado a aprender, embora Azure Time Series Insights Gen2 fornece uma linguagem de consulta baseada em texto granular para utilizadores avançados que estão familiarizados com o SQL. Também fornece exploração selecionada e clique para principiantes.

Os clientes podem aproveitar a velocidade para diagnosticar rapidamente problemas relacionados com o ativo. Podem realizar análises de DevOps para chegar à causa raiz de um bug numa solução IoT. Podem também identificar áreas a sinalizar para uma investigação mais aprofundada como parte das suas iniciativas em ciência dos dados.

Existem três formas primárias de interagir com os dados armazenados na Azure Time Series Insights Gen2:

* A primeira e mais fácil maneira de começar é com o Azure Time Series Insights Gen2 Explorer. Pode usá-lo para visualizar rapidamente todos os seus dados IoT num só local. Fornece ferramentas como o mapa de calor para ajudá-lo a detetar anomalias nos seus dados. Também fornece uma visão de perspetiva. Utilize-o para comparar até quatro visualizações de um ou mais ambientes Azure Time Series Insights Gen2 num único painel de instrumentos. O dashboard dá-lhe uma visão dos seus dados da série de tempo em todas as suas localizações. Saiba mais sobre o [Azure Time Series Insights Gen2 Explorer](./concepts-ux-panels.md). Para planear o seu ambiente, leia [o planeamento da Azure Time Series Insights Gen2](./how-to-plan-your-environment.md).

* A segunda maneira de começar é usar o SDK JavaScript para incorporar rapidamente gráficos e gráficos poderosos na sua aplicação web. Com apenas algumas linhas de código, pode-se autor de consultas poderosas. Use-os para preencher gráficos de linha, gráficos de tortas, gráficos de barras, mapas de calor, grelhas de dados, e muito mais. Todos estes elementos existem fora da caixa usando o SDK. O SDK também abstrata Azure Time Series Insights Gen2 consulta APIs. Pode usá-los para autor de predicados semelhantes ao SQL para consultar os dados que pretende mostrar num dashboard. Para soluções híbridas de camada de apresentação, a Azure Time Series Insights Gen2 oferece URLs parametrizados. Fornecem pontos de conexão sem emenda com o Azure Time Series Insights Gen2 Explorer para mergulhos profundos em dados.

  * Leia sobre a biblioteca do [cliente JS](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) e a documentação do [Cliente amostra](https://github.com/Microsoft/tsiclient) para saber mais sobre o JavaScript SDK.

  * Saiba mais sobre a partilha de URLs e a nova UI, analisando [dados do Visualize no Azure Time Series Insights Gen2 Explorer](./concepts-ux-panels.md).

* A terceira maneira de começar é usar as poderosas APIs para consultar os dados armazenados em Azure Time Series Insights Gen2. Azure Time Series Insights Gen2 tem operadores temporais `from` `to` como, `first` e `last` . Tem agregações e transformações `average` `sum` como, `min` `max` `time-weighted average` `time-weighted sum` etc. Também permite operadores de filtragem, aritmética e boolean, funções de escala, etc. Todos estes operadores permitem que as aplicações a jusante encontrem rapidamente tendências e padrões interessantes nos seus dados. Use-as para povoar visualizações caseiras para detetar anomalias.

## <a name="operational-analysis-and-driving-process-efficiency"></a>Análise operacional e eficiência do processo de condução

Utilize a Azure Time Series Insights Gen2 para monitorizar a saúde, utilização e desempenho do equipamento à escala e medir a eficiência operacional. Azure Time Series Insights Gen2 ajuda a gerir cargas de trabalho IoT diversificadas e imprevisíveis sem sacrificar a ingestão ou o desempenho de consulta.

[![O Screenshot mostra I o T devices /application data, stream processing, operacional efficiency, intelligence/insights, and advanced analytics in Azure Time Series Insights Gen2.](media/v2-update-use-cases/overview.png)](media/v2-update-use-cases/overview.png#lightbox)

O streaming e o processamento contínuo de dados provenientes de processos operacionais podem transformar com sucesso qualquer negócio se for combinado com a tecnologia ou solução certa. Muitas vezes estas soluções são uma combinação de múltiplos sistemas. Permitem a exploração e análise de dados que mudam constantemente, especialmente no reino de IoT, e partilham um padrão comum.

Estes padrões começam frequentemente com plataformas ativadas por IoT que ingerem milhares de milhões de eventos de dispositivos e sensores que abrangem vários locais. Estes sistemas processam e analisam dados de streaming para obter insights e ações em tempo real. Os dados são normalmente arquivados para armazenar quente e frio para análises de lotes em tempo real e em tempo real.

Os dados recolhidos passam por uma série de processamentos para a limpeza e contextualização para cenários de consulta a jusante e analíticos. A Azure oferece serviços ricos que podem ser aplicados a cenários IoT, como manutenção de ativos e fabricação. Estes serviços incluem Azure Time Series Insights Gen2, IoT Hub, Event Hubs, Azure Stream Analytics, Azure Functions, Azure Logic Apps, Azure Databricks, Azure Machine Learning e Power BI.

A arquitetura da solução pode ser alcançada da seguinte forma:

* Ingerir dados via IoT Hub ou Event Hubs para segurança, produção e latência de melhor classe.
* Realizar processamento de dados e computações. Funil ingeriu dados através de serviços como Stream Analytics, Logic Apps e Azure Functions. O serviço que utiliza depende das necessidades específicas de processamento de dados.
* Os sinais computatados do gasoduto de processamento são empurrados para a Azure Time Series Insights Gen2 para armazenamento e análise.

Azure Time Series Insights Gen2 oferece quase exploração de dados em tempo real e insights baseados em ativos sobre dados históricos. Dependendo das necessidades do seu negócio, os trabalhos de MapReduce e Hive podem funcionar em dados armazenados em Azure Time Series Insights Gen2, ligando a Azure Time Series Insights Gen2 ao Azure HDInsight. Os dados armazenados em Azure Time Series Insights Gen2 estão disponíveis para Power BI e outras aplicações de clientes através da Azure Time Series Insights Gen2 consulta pública de superfície APIs. Estes dados podem ser usados para cenários profundos de inteligência empresarial e operacional.

## <a name="advanced-analytics"></a>Análise avançada

Integre-se com serviços de análise avançadas como Machine Learning e Azure Databricks. Azure Time Series Insights Gen2 ingressa dados brutos de milhões de dispositivos. Adiciona dados contextuais que podem ser consumidos sem problemas por um conjunto de serviços de análise Azure.

[![Análise](media/v2-update-use-cases/advanced-analytics.png)](media/v2-update-use-cases/advanced-analytics.png#lightbox)

Análises avançadas e aprendizagem automática consomem e processam grandes volumes de dados. Estes dados são utilizados para tomar decisões baseadas em dados e realizar análises preditivas. Em casos de utilização de IoT, algoritmos de análise avançados aprendem com os dados recolhidos de milhões de dispositivos. Estes dispositivos transmitem dados várias vezes a cada segundo. Os dados recolhidos de dispositivos IoT são crus. Carece de informação contextual, como a localização do dispositivo e a unidade da leitura do sensor. Como resultado, os dados brutos são difíceis de consumir diretamente para análises avançadas.

Azure Time Series Insights Gen2 faz a ponte entre os dados IoT e a análise avançada de duas formas simples e económicas:

* Em primeiro lugar, a Azure Time Series Insights Gen2 recolhe dados de telemetria em bruto de milhões de dispositivos utilizando o IoT Hub. Enriquece dados com informação contextual e transforma dados num formato de parquet. Este formato pode facilmente integrar-se com outros serviços de análise avançada, tais como Machine Learning, Azure Databricks e aplicações de terceiros.

    Azure Time Series Insights Gen2 pode servir como a fonte da verdade para todos os dados em toda uma organização. Cria um repositório central para que as cargas de trabalho a jusante a jusante consumam. Como o Azure Time Series Insights Gen2 é um serviço de armazenamento quase em tempo real, os modelos de análise avançados podem aprender continuamente a partir de dados de telemetria IoT. Como resultado, os modelos podem fazer previsões mais precisas.

* Em segundo lugar, a saída de modelos de machine learning e previsão pode ser alimentada no Azure Time Series Insights Gen2 para visualizar e armazenar os seus resultados. Este procedimento ajuda as organizações a otimizar e ajustar os seus modelos. Azure Time Series Insights Gen2 torna simples visualizar dados de telemetria de streaming no mesmo plano que as saídas do modelo treinado. Desta forma, ajuda as equipas de ciência de dados a detetar anomalias e a identificar padrões.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre o [Azure Time Series Insights Gen2 Explorer](./concepts-ux-panels.md).
* Leia [as melhores práticas da Azure Time Series Insights Gen2](./how-to-plan-your-environment.md) para planear o seu ambiente.
* Leia a documentação do [Cliente da Amostra.](https://github.com/Microsoft/tsiclient)
