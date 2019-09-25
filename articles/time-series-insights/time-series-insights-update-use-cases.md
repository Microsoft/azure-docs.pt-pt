---
title: Azure Time Series Insights Visualizar casos de uso | Microsoft Docs
description: Entenda Azure Time Series Insights casos de uso de visualização.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/24/2019
ms.custom: seodec18
ms.openlocfilehash: f4eb1b69a1464cacaf666e49f4aff7422d63611d
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71264523"
---
# <a name="azure-time-series-insights-preview-use-cases"></a>Azure Time Series Insights Visualizar casos de uso

Este artigo resume vários casos de uso comuns para a visualização de Azure Time Series Insights. As recomendações neste artigo servem como um ponto de partida para desenvolver seus aplicativos e soluções com Time Series Insights.

Especificamente, este artigo responde às seguintes perguntas:

* Quais são os casos de uso comuns do Time Series Insights?
* Quais são os benefícios de usar Time Series Insights para [exploração de dados e detecção de anomalias visuais](#data-exploration-and-visual-anomaly-detection)?
* Quais são os benefícios de usar Time Series Insights para [análise operacional e eficiência do processo](#operational-analysis-and-driving-process-efficiency)?
* Quais são os benefícios de usar o Time Series Insights para [análise avançada](#advanced-analytics)?

Uma visão geral desses cenários de uso é descrita nas seções a seguir.

## <a name="introduction"></a>Introdução

Azure Time Series Insights é uma oferta de plataforma como serviço de ponta a ponta. Ele é usado para coletar, processar, armazenar, analisar e consultar dados altamente contextuais e com escala de IoT otimizados para a série temporal. Time Series Insights é ideal para exploração de dados ad hoc e análise operacional. Time Series Insights é uma oferta de serviço personalizada e extensível de forma exclusiva que atende às amplas necessidades de implantações de IoT industriais.

## <a name="data-exploration-and-visual-anomaly-detection"></a>Exploração de dados e deteção visual de anomalias

Explore e analise instantaneamente milhares de milhões de eventos para detetar anomalias e descobrir tendências ocultas nos dados. O Time Series Insights oferece desempenho quase em tempo real para as cargas de trabalho de análise de IoT e DevOps.

[![Data Explorer](media/v2-update-use-cases/data-explorer.svg)](media/v2-update-use-cases/data-explorer.svg#lightbox)

A maioria dos clientes concorda que a quantidade mínima de tempo necessária para obter informações é um dos recursos Principals da Time Series Insights:

* Time Series Insights não requer preparação de dados antecipada. 
* Ele funciona rapidamente para se conectar a bilhões de eventos em seu hub IoT do Azure ou hubs de eventos do Azure em minutos. 
* Uma vez conectado, você pode visualizar e analisar bilhões de eventos para identificar anomalias e descobrir tendências ocultas em seus dados.

Time Series Insights é intuitivo e simples de usar. Você pode interagir com seus dados sem escrever uma única linha de código. Também não há nenhuma nova linguagem que você precisa aprender, embora Time Series Insights forneça uma linguagem de consulta baseada em texto granular para usuários avançados que estão familiarizados com o SQL. Ele também fornece exploração de seleção e clique para iniciantes.

Os clientes podem aproveitar a velocidade para diagnosticar problemas relacionados ao ativo rapidamente. Eles podem executar a análise de DevOps para chegar à causa raiz de um bug em uma solução de IoT. Eles também podem identificar áreas a serem sinalizadas para uma investigação mais aprofundada como parte de suas iniciativas de ciência de dados.  

Há três maneiras principais de interagir com os dados armazenados no Time Series Insights:

- A primeira e mais simples maneira de começar é com o Time Series Insights Gerenciador de visualização. Você pode usá-lo para visualizar rapidamente todos os seus dados de IoT em um único lugar. Ele fornece ferramentas como o calor para ajudá-lo a identificar anomalias em seus dados. Ele também fornece uma exibição de perspectiva. Use-o para comparar até quatro modos de exibição de um ou mais ambientes de Time Series Insights em um único painel. O painel fornece uma exibição dos dados de série temporal em todos os seus locais. Saiba mais sobre o [Gerenciador de visualização Time Series insights](./time-series-insights-update-explorer.md). Para planejar seu ambiente de Time Series Insights, leia [Time Series insights Planning](./time-series-insights-update-plan.md).

- A segunda maneira de começar é usar o SDK do JavaScript para inserir rapidamente gráficos e grafos poderosos em seu aplicativo Web. Com apenas algumas linhas de código, você pode criar consultas poderosas. Use-os para preencher gráficos de linhas, gráficos de pizza, gráficos de barras, calor, grades de dados e muito mais. Todos esses elementos existem de fora da caixa usando o SDK. O SDK também abstrai Time Series Insights APIs de consulta. Você pode usá-los para criar predicados semelhantes ao SQL para consultar os dados que você deseja mostrar em um painel. Para soluções de camada de apresentação híbrida, o Time Series Insights oferece URLs parametrizadas. Eles fornecem pontos de conexão contínuos com o Time Series Insights Explorer Preview para aprofundar-se nos dados.

    * Leia a [biblioteca de cliente Time Series insights js](tutorial-explore-js-client-lib.md) e a documentação do [Time Series insights Client](https://github.com/Microsoft/tsiclient) para saber mais sobre o SDK do JavaScript.

    * Saiba mais sobre como compartilhar URLs e a nova interface do usuário examinando [Visualizar dados no Gerenciador de Azure Time Series insights Preview](time-series-insights-update-explorer.md).

- A terceira maneira de começar é usar as APIs poderosas para consultar os dados armazenados no Time Series Insights. Time Series insights tem operadores temporais, como `from` `first`, `to`, e `last`. `average`Ele tem agregações e transformações, como `max`, `min` `split by` `order by`,,, e `DateHistogram`. `has`Ele também tem operadores de filtragem, como `in` `and`, `or` `greater than`,,, e `REGEX`. Todos esses operadores permitem que aplicativos downstream encontrem rapidamente padrões e tendências interessantes em seus dados. Use-os para preencher visualizações domésticas para identificar anomalias.

## <a name="operational-analysis-and-driving-process-efficiency"></a>Promover a eficiência dos processos e análise operacional

Use Time Series Insights para monitorar a integridade, o uso e o desempenho dos equipamentos em escala. Time Series Insights fornece uma maneira fácil de medir a eficiência operacional. O Time Series Insights ajuda a gerir cargas de trabalho de IoT diversas e imprevisíveis, sem sacrificar a ingestão ou o desempenho de consultas.

[![Descrição geral](media/v2-update-use-cases/overview.svg)](media/v2-update-use-cases/overview.svg#lightbox)

O processamento contínuo e de streaming de dados provenientes de processos operacionais pode transformar com êxito qualquer negócio se ele estiver combinado com a tecnologia ou a solução certa. Geralmente, essas soluções são uma combinação de vários sistemas. Eles permitem a exploração e a análise de dados que são alterados constantemente, especialmente no realm de IoT, e compartilham um padrão comum.

Esses padrões geralmente começam com plataformas habilitadas para IoT que ingerim bilhões de eventos de dispositivos e sensores que abrangem várias localidades. Esses sistemas processam e analisam dados de streaming para derivar informações e ações em tempo real. Normalmente, os dados são arquivados no armazenamento quente e frio para análise de lote quase em tempo real.

Os dados coletados passam por uma série de processamento para limpá-los e Contextualize-los para cenários de análise e consulta downstream. O Azure oferece serviços avançados que podem ser aplicados a cenários de IoT, como manutenção de ativos e fabricação. Esses serviços incluem Time Series Insights, Hub IoT, hubs de eventos, Azure Stream Analytics, Azure Functions, aplicativos lógicos do Azure, Azure Databricks, Azure Machine Learning e Power BI.

A arquitetura da solução pode ser obtida da seguinte maneira:

- Ingerir dados por meio do Hub IoT ou hubs de eventos para obter a melhor segurança, taxa de transferência e latência.
- Execute processamento de dados e cálculos. O funil ingeriu dados por meio de serviços como Stream Analytics, aplicativos lógicos e Azure Functions. O serviço que você usa depende das necessidades específicas de processamento de dados.
- Os sinais computados do pipeline de processamento são enviados por push para Time Series Insights para armazenamento e análise.

A Time Series Insights oferece exploração de dados quase em tempo real e informações baseadas em ativos sobre dados históricos. Dependendo de suas necessidades de negócios, os trabalhos do MapReduce e do hive podem ser executados em dados armazenados no Time Series Insights conectando Time Series Insights ao Azure HDInsight. Os dados armazenados no Time Series Insights estão disponíveis para Power BI e outros aplicativos de clientes por meio das APIs de consulta de superfície pública Time Series Insights. Esses dados podem ser usados para cenários de inteligência comercial e operacional aprofundados.

## <a name="advanced-analytics"></a>Análise avançada

Integre-se com os serviços de análise avançada, como Machine Learning e Azure Databricks. Time Series Insights dados brutos insere de milhões de dispositivos. Ele adiciona dados contextuais que podem ser consumidos diretamente por um pacote de serviços de análise do Azure.

[![Analytics](media/v2-update-use-cases/advanced-analytics.svg)](media/v2-update-use-cases/advanced-analytics.svg#lightbox)

A análise avançada e o Machine Learning consomem e processam grandes volumes de dados. Esses dados são usados para tomar decisões controladas por dados e executar análise preditiva. Em casos de uso de IoT, os algoritmos de análise avançada aprendem com os dados coletados de milhões de dispositivos. Esses dispositivos transmitem dados várias vezes a cada segundo. Os dados coletados de dispositivos IoT são brutos. Ele não tem informações contextuais, como o local do dispositivo e a unidade do sensor de leitura. Como resultado, é difícil consumir dados brutos diretamente para análise avançada.

Time Series Insights preenche a lacuna entre os dados de IoT e a análise avançada de duas maneiras simples e econômicas:

- Primeiro, Time Series Insights coleta dados de telemetria brutos de milhões de dispositivos usando o Hub IoT. Ele enriquece os dados com informações contextuais e transforma os dados em um formato parquet. Esse formato pode ser facilmente integrado a outros serviços de análise avançados, como Machine Learning, Azure Databricks e aplicativos de terceiros.

    Time Series Insights pode servir como a fonte de verdade para todos os dados em toda a organização. Ele cria um repositório central para as cargas de trabalho de análise downstream consumirem. Como Time Series Insights é um serviço de armazenamento quase em tempo real, os modelos de análise avançada podem aprender continuamente com os dados de telemetria de IoT recebidos. Como resultado, os modelos podem fazer previsões mais precisas.

- Em segundo lugar, Time Series Insights pode ser alimentado a saída de modelos de aprendizado de máquina e de previsão para visualizar e armazenar seus resultados. Este procedimento ajuda as organizações a otimizar e ajustar seus modelos. Time Series Insights simplifica a visualização de dados de telemetria de streaming no mesmo plano que as saídas de modelo treinadas. Dessa forma, ele ajuda as equipes de ciência de dados a identificar anomalias e detectar padrões.  

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [Gerenciador de visualização Time Series insights](./time-series-insights-update-explorer.md).
- Leia [Time Series insights planejar a visualização](./time-series-insights-update-plan.md) para planejar seu ambiente.
- Leia a documentação do [cliente do time Series insights](https://github.com/Microsoft/tsiclient) .
