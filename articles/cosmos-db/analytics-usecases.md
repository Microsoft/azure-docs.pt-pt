---
title: Caixas de utilização para análise incorporada com Azure Cosmos DB.
description: Aprenda a usar análises incorporadas com o Azure Cosmos DB em diferentes casos de utilização.
author: markjbrown
ms.author: mjbrown
ms.topic: conceptual
ms.service: cosmos-db
ms.date: 04/20/2020
ms.reviewer: sngun
ms.openlocfilehash: 9b1c3435222ada52c01f21c2c242dc886f566a81
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82192855"
---
# <a name="use-cases-for-built-in-analytics-with-azure-cosmos-db"></a>Casos de utilização para análise incorporada com O Azure Cosmos DB

Os seguintes casos de utilização podem ser alcançados utilizando a análise incorporada com a Apache Spark em Azure Cosmos DB:

## <a name="htap-scenarios"></a>Cenários HTAP

A análise incorporada em Azure Cosmos DB pode ser usada para:

* Detete fraude durante o processamento de transações.
* Forneça recomendações aos compradores à medida que navegam numa loja de ECommerce.
* Alerte os operadores para a iminente falha de um equipamento de fabrico crítico.
* Crie uma visão rápida e atos, incorporando análises em tempo real diretamente em dados operacionais.

A Azure Cosmos DB suporta cenários híbridos de processamento transacional/analítico (HTAP) utilizando a Faísca Apache incorporada nativamente. A Azure Cosmos DB remove a separação operacional e analítica que acompanha os sistemas tradicionais.

## <a name="globally-distributed-data-lake-without-requiring-any-etl"></a>Lago de dados distribuído globalmente sem exigir qualquer ETL

Com apache spark nativo,O Azure Cosmos DB fornece uma forma rápida, simples e escalável de construir um lago de dados distribuído globalmente que fornece uma imagem de um único sistema. Os dados multimodelo distribuídos globalmente eliminam a necessidade de investir em oleodutos e escalas ETL caros a pedido, revolucionando a forma como as equipas de dados analisam os seus conjuntos de dados.

## <a name="time-series-analytics-over-historic-data"></a>Análise de séries temporais sobre dados históricos

Em alguns casos, poderá ter de responder a perguntas baseadas em dados como num determinado momento sobre os eventos concluídos no passado. Por exemplo, para obter a contagem dos estados de atividade de CRM numa determinada data. Se publicou o relatório há uma semana, a contagem dos estatutos seria de acordo com os estatutos de cada atividade naquele momento. A apresentação do mesmo relatório de hoje dar-lhe-á a contagem das atividades cujos estatutos são como são hoje, o que pode ter mudado desde a semana passada, à medida que passam pelo seu ciclo de vida de aberto a fechar. Então, tens de reportar a fotografia em cada fase do ciclo de vida do caso.

Com o Azure Cosmos DB, os utilizadores têm a possibilidade de implementar o conceito de viagem no tempo, podendo consultar e executar análises sobre os dados retrospetivamente e perguntar como os dados olharam para um ponto de tempo específico da história. Isto significa que os utilizadores podem facilmente ver as visões atuais e históricas dos dados e executar análises sobre os mesmos.

## <a name="globally-distributed-machine-learning-and-ai"></a>Aprendizagem automática distribuída globalmente e IA

À medida que as empresas se confrontam com volumes de dados em rápido crescimento e uma variedade crescente de tipos e formatos de dados, a capacidade de obter insights mais profundos e precisos torna-se quase impossível à escala de petabyte em todo o mundo. Com apache spark nativo,o Azure Cosmos DB oferece uma plataforma de análise distribuída globalmente que oferece uma extensa biblioteca de algoritmos de aprendizagem automática. Você pode usar cadernos jupyter interativos para construir e treinar modelos, e capacidades de gestão de clusters. Estas capacidades permitem-lhe fornecer clusters spark altamente afinados e auto-elásticos a pedido.

## <a name="deep-learning-on-multi-model-globally-distributed-data"></a>Deep Learning sobre dados distribuídos globalmente por vários modelos

A aprendizagem profunda é a forma ideal de fornecer grandes soluções de análise preditiva de dados, à medida que o volume de dados e a complexidade continuam a crescer. Com a aprendizagem profunda, as empresas podem aproveitar o poder de dados não estruturados e semi-estruturados para fornecer casos de uso que alavancam técnicas como AI, processamento de linguagem natural, e muito mais.

## <a name="reporting-integrating-with-power-apps-power-bi"></a>Reportagem (integração com Aplicações de Energia, Power BI)

O Power BI fornece visualizações interativas com capacidades de inteligência empresarial de self-service, permitindo aos utilizadores finais criarrelatórios e dashboards por si só. Ao utilizar o conector Spark incorporado, pode ligar o Power BI Desktop aos clusters Apache Spark em Azure Cosmos DB. Este conector permite-lhe utilizar consulta direta para descarregar o processamento para a Apache Spark em Azure Cosmos DB, o que é ótimo quando se tem uma quantidade massiva de dados que não quer carregar no Power BI ou quando pretende realizar uma análise em tempo real.

## <a name="iot-analytics-at-global-scale"></a>Análise ioT à escala global

Os dados gerados pelo aumento dos sensores de rede trazem uma visibilidade sem precedentes em sistemas e processos anteriormente opacos. A chave é encontrar insights atuais nesta torrente de informação, independentemente de onde os dispositivos IoT são distribuídos por todo o mundo. A Azure Cosmos DB permite que as empresas iOT analisem os sensores de alta velocidade e os dados das séries temporais em tempo real em qualquer parte do mundo. Permite-lhe aproveitar o verdadeiro valor de um mundo interligado para proporcionar melhores experiências de clientes, eficiênciaoperacional e novas oportunidades de receita.

## <a name="stream-processing-and-event-analytics"></a>Processamento de fluxo e análise de eventos 

Desde ficheiros de registo a dados de sensores, as empresas têm cada vez mais a necessidade de lidar com "fluxos" de dados. Estes dados chegam num fluxo constante, muitas vezes de múltiplas fontes simultaneamente. Embora seja possível armazenar estes fluxos de dados no disco e analisá-los retrospetivamente, pode por vezes ser sensato ou importante processar e agir sobre os dados à medida que chega. Por exemplo, os fluxos de dados relacionados com transações financeiras podem ser processados em tempo real para identificar e recusar transações potencialmente fraudulentas.

## <a name="interactive-analytics"></a>Análise interativa

Além de executar consultas pré-definidas para criar dashboards estáticos para vendas, produtividade ou preços de stock, você pode querer explorar os dados interativamente. A análise interativa permite-lhe fazer perguntas, visualizar os resultados, alterar a pergunta inicial com base na resposta ou aprofundar os resultados. A Apache Spark in Azure Cosmos DB suporta consultas interativas respondendo e adaptando-se rapidamente.

## <a name="data-exploration-using-jupyter-notebooks"></a>Exploração de dados usando cadernos Jupyter

Quando tiver um novo conjunto de dados, antes de mergulhar em modelos e testes de execução, tem de inspecionar os seus dados. Por outras palavras, é necessário efetuar análises de dados exploratórias. A exploração de dados pode informar várias decisões. Por exemplo, pode encontrar detalhes como os métodos adequados a serem utilizados nos seus dados, se os dados satisfizerem determinados pressupostos de modelação, se os dados devem ser limpos, reestruturados, etc. Ao utilizar os cadernos jupyter e Apache Spark, do Azure Cosmos DB, pode fazer uma análise rápida e eficaz de dados exploratórios sobre dados transacionais e analíticos.

## <a name="next-steps"></a>Passos seguintes

Para começar com estes casos de utilização em ir para os seguintes artigos:

* [Cadernos jupyter embutidos em Azure Cosmos DB](cosmosdb-jupyter-notebooks.md)
* [Como ativar cadernos para contas Azure Cosmos](enable-notebooks.md)
* [Criar um caderno para analisar e visualizar dados](create-notebook-visualize-data.md)