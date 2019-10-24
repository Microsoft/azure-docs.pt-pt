---
title: Casos de uso para análise interna com Azure Cosmos DB.
description: Saiba como usar a análise interna com Azure Cosmos DB em diferentes casos de uso.
author: markjbrown
ms.author: mjbrown
ms.topic: conceptual
ms.service: cosmos-db
ms.date: 09/26/2019
ms.reviewer: sngun
ms.openlocfilehash: b9bac14ff47107a4b39f12989685e2e510569ba2
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72757066"
---
# <a name="use-cases-for-built-in-analytics-with-azure-cosmos-db"></a>Casos de uso para análise interna com Azure Cosmos DB

Os casos de uso a seguir podem ser obtidos usando a análise interna com Apache Spark no Azure Cosmos DB:

## <a name="htap-scenarios"></a>Cenários de HTAP

A análise interna no Azure Cosmos DB pode ser usada para:

* Detectar fraude durante o processamento da transação.
* Forneça recomendações para os compradores ao navegar por uma loja de comércio eletrônico.
* Operadores de alerta para a falha iminente de uma parte crítica do equipamento de manufatura.
* Crie informações rápidas e acionáveis inserindo análises em tempo real diretamente em dados operacionais.

O Azure Cosmos DB dá suporte a cenários de HTAP (processamento transacional/analítico) híbrido usando o Apache Spark interno nativo. Azure Cosmos DB remove a separação operacional e analítica que vem com os sistemas tradicionais.

## <a name="globally-distributed-data-lake-without-requiring-any-etl"></a>Data Lake distribuído globalmente sem a necessidade de qualquer ETL

Com o Apache Spark interno nativamente, o Azure Cosmos DB fornece uma maneira rápida, simples e escalonável de criar um data Lake distribuído globalmente que fornece uma única imagem do sistema. Os dados de vários modelos distribuídos globalmente eliminam a necessidade de investir em pipelines de ETL caros e são dimensionados sob demanda, revolucionando a maneira como as equipes de dados analisam seus conjuntos de dados.

## <a name="time-series-analytics-over-historic-data"></a>Análise de série temporal sobre dados históricos

Em alguns casos, talvez seja necessário responder a perguntas com base nos dados como em um ponto específico no tempo sobre os eventos concluídos no passado. Por exemplo, para obter a contagem de status de atividade de CRM em uma determinada data. Se você executou o relatório uma semana atrás, a contagem dos status será de acordo com o status de cada atividade naquele ponto no tempo. A execução do mesmo relatório hoje fornecerá a você a contagem das atividades cujos status são como estão hoje, que podem ter mudado desde a última semana, à medida que passam pelo ciclo de vida de aberto para o fechamento. Portanto, você precisa relatar o instantâneo em cada estágio do ciclo de vida do caso.

Em cenários tradicionais de data warehouse, o conceito de instantâneo não é possível porque os data warehouses não foram projetados para incorporá-lo e os dados fornecem apenas uma visão atual do que está acontecendo. Com o Azure Cosmos DB, os usuários têm a possibilidade de implementar o conceito de viagem de tempo, poder consultar e executar análises nos dados de forma retrospectiva e solicitar como os dados são examinados em um determinado momento no histórico. Isso significa que os usuários podem exibir facilmente as exibições atuais e históricas dos dados e executar a análise nela.

## <a name="globally-distributed-machine-learning-and-ai"></a>Aprendizado de máquina e ia distribuídos globalmente

À medida que as empresas contentam aumentar rapidamente os volumes de dados e uma variedade cada vez maior de formatos e tipos de dados, a capacidade de obter informações mais detalhadas e mais precisas torna-se quase impossível em escala de petabytes em todo o mundo. Com o Apache Spark interno nativamente, o Azure Cosmos DB fornece uma plataforma de análise distribuída globalmente que oferece uma ampla biblioteca de algoritmos de aprendizado de máquina. Você pode usar blocos de anotações interativos do Jupyter para criar e treinar modelos e recursos de gerenciamento de cluster. Esses recursos permitem que você provisione clusters Spark altamente ajustados e elásticos automaticamente sob demanda.

## <a name="deep-learning-on-multi-model-globally-distributed-data"></a>Aprendizado profundo sobre dados distribuídos globalmente de vários modelos

O aprendizado profundo é a maneira ideal de fornecer Big Data soluções de análise preditiva à medida que o volume de dados e a complexidade continuam crescendo. Com o aprendizado profundo, as empresas podem aproveitar o poder de dados não estruturados e semiestruturados para fornecer casos de uso que aproveitam técnicas como ia, processamento de linguagem natural e muito mais.

## <a name="reporting-integrating-with-power-apps-power-bi"></a>Relatórios (integração com o Power apps, Power BI)

Power BI fornece visualizações interativas com recursos de business intelligence de autoatendimento, permitindo que os usuários finais criem relatórios e Dashboards por conta própria. Usando o conector interno do Spark, você pode conectar Power BI Desktop a Apache Spark clusters no Azure Cosmos DB. Esse conector permite que você use a consulta direta para descarregar o processamento para Apache Spark em Azure Cosmos DB, o que é ótimo quando você tem uma grande quantidade de dados que você não deseja carregar no Power BI ou quando deseja executar uma análise quase em tempo real.

## <a name="iot-analytics-at-global-scale"></a>Análise de IoT em escala global

Os dados gerados do aumento dos sensores de rede proporcionam uma visibilidade sem precedentes dos processos e sistemas opacos anteriormente. A chave é encontrar informações acionáveis nesse torrent de informação, independentemente de onde os dispositivos IoT são distribuídos em todo o mundo. Azure Cosmos DB permite que as empresas IOT analisem o sensor de alta velocidade e os dados de série temporal em tempo real em qualquer lugar do mundo. Ele permite que você aproveite o verdadeiro valor de um mundo interconectado para fornecer experiências de clientes aprimoradas, eficiências operacionais e novas oportunidades de receita.

## <a name="stream-processing-and-event-analytics"></a>Processamento de fluxo e análise de eventos 

De arquivos de log para dados de sensor, as empresas têm cada vez a necessidade de lidar com "fluxos" de dados. Esses dados chegam em um fluxo constante, geralmente de várias fontes simultaneamente. Embora seja possível armazenar esses fluxos de dados em disco e analisá-los de forma retrospectiva, às vezes pode ser sensato ou importante processar e agir sobre os dados conforme eles chegam. Por exemplo, fluxos de dados relacionados a transações financeiras podem ser processados em tempo real para identificar e recusar transações potencialmente fraudulentas.

## <a name="interactive-analytics"></a>Análise interativa

Além de executar consultas predefinidas para criar painéis estáticos para preços de vendas, produtividade ou ações, talvez você queira explorar os dados interativamente. A análise interativa permite fazer perguntas, exibir os resultados, alterar a pergunta inicial com base na resposta ou aprofundar-se nos resultados. Apache Spark no Azure Cosmos DB dá suporte a consultas interativas respondendo e se adaptando rapidamente.

## <a name="data-exploration-using-jupyter-notebooks"></a>Exploração de dados usando notebooks Jupyter

Quando você tiver um novo conjunto de dados, antes de se aprofundar nos modelos e testes em execução, você precisará inspecionar os Data. Em outras palavras, você precisa executar a análise de dados exploratório. A exploração de dados pode informar várias decisões. Por exemplo, você pode encontrar detalhes como os métodos que são apropriados para uso em seus dados, se os dados atendem a certas pressuposições de modelagem, se os dados devem ser limpos, reestruturados etc. Usando os blocos de anotações Jupyter e Apache Spark nativos internos do Azure Cosmos DB, você pode fazer análises de dados exploratórios rápidas e eficazes em dados transacionais e analíticos.

## <a name="next-steps"></a>Passos seguintes

Para começar a usar esses casos de uso, acesse os seguintes artigos:

* [Blocos de anotações Jupyter internos no Azure Cosmos DB](cosmosdb-jupyter-notebooks.md)
* [Como habilitar blocos de anotações para contas do Azure Cosmos](enable-notebooks.md)
* [Criar um bloco de anotações para analisar e Visualizar dados](create-notebook-visualize-data.md)