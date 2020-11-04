---
title: A análise em tempo real usa casos com Azure Synapse Link para Azure Cosmos DB
description: Saiba como a Azure Synapse Link for Azure Cosmos DB é usada na análise da cadeia de fornecimento, previsão, reporte, personalização em tempo real e manutenção preditiva IOT.
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: ramkris
ms.openlocfilehash: 7621a19b510d302454465f9fcbacec27a14dddf9
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340180"
---
# <a name="azure-synapse-link-for-azure-cosmos-db-near-real-time-analytics-use-cases"></a>Azure Synapse Link para o Azure Cosmos DB: casos de utilização da análise quase em tempo real
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

[Azure Synapse Link](synapse-link.md) for Azure Cosmos DB é uma capacidade de processamento transacional e analítico híbrido nativo em nuvem (HTAP) que lhe permite executar perto de análises em tempo real sobre dados operacionais. A Synapse Link cria uma integração apertada e perfeita entre a Azure Cosmos DB e a Azure Synapse Analytics.

Você pode estar curioso para entender que casos de uso da indústria podem aproveitar esta capacidade de HTAP nativo em nuvem para análises quase em tempo real sobre dados operacionais. Aqui estão três casos de uso comum para Azure Synapse Link para Azure Cosmos DB:

* Análise da cadeia de fornecimento, previsão de relatórios &
* Personalização em tempo real
* Manutenção preditiva, deteção de anomalias em cenários IOT

> [!NOTE]
> A azure Synapse Link for Azure Cosmos DB visa o cenário em que as equipas empresariais procuram correr perto de análises em tempo real. Estas análises são executadas sem ETL sobre dados operacionais gerados através de aplicações transacionais construídas em Azure Cosmos DB. Isto não substitui a necessidade de um armazém de dados separado quando existem requisitos tradicionais de armazém de dados, tais como gestão da carga de trabalho, alta concordância, agregados de persistência em várias fontes de dados.

## <a name="supply-chain-analytics-forecasting--reporting"></a>Análise da cadeia de fornecimento, previsão de relatórios &

Estudos de investigação mostram que a incorporação de grandes análises de dados nas operações da cadeia de fornecimento leva a melhorias nos prazos de entrega do fim do ciclo e na eficiência da cadeia de abastecimento.

Os fabricantes estão a embarcar em tecnologias nativas da nuvem para sair dos constrangimentos dos sistemas legados de Planeamento de Recursos Empresariais (ERP) e gestão de cadeias de fornecimento (SCM). Com as cadeias de abastecimento a gerarem cada minuto volumes crescentes de dados operacionais (pedido, expedição, dados de transações), os fabricantes precisam de uma base de dados operacional. Esta base de dados operacional deve escalar para lidar com os volumes de dados, bem como uma plataforma analítica para chegar a um nível de inteligência contextual em tempo real para se manter à frente da curva.

A seguinte arquitetura mostra o poder de alavancar Azure Cosmos DB como a base de dados operacional nativa da nuvem e Synapse Link na análise da cadeia de fornecimento:

:::image type="content" source="./media/synapse-link-use-cases/supply-chain-analytics.png" alt-text="Azure Synapse Link para Azure Cosmos DB na análise da cadeia de fornecimento " border="false":::

Com base em arquitetura anterior, você pode alcançar os seguintes casos de uso com Synapse Link para Azure Cosmos DB:

* **Prepare & gasoduto preditivo do comboio:** Gerar insights sobre os dados operacionais em toda a cadeia de fornecimento usando a aprendizagem automática traduz. Desta forma, pode baixar os custos de inventário, de operações e reduzir os prazos de encomenda para os clientes.

  O Synapse Link permite-lhe analisar os dados operacionais em mudança no Azure Cosmos DB sem quaisquer processos ETL manuais. Poupa-lhe de custos adicionais, latência e complexidade operacional. A Synapse Link permite aos engenheiros de dados e cientistas de dados construir oleodutos preditivos robustos:

  * Consultar dados operacionais da loja analítica Azure Cosmos DB, aproveitando a integração nativa com piscinas Apache Spark em Azure Synapse Analytics. Pode consultar os dados num caderno interativo ou em trabalhos remotos programados sem engenharia de dados complexa.

  * Construa modelos de Machine Learning (ML) com algoritmos Spark ML e integração Azure ML em Azure Synapse Analytics.

  * De recreva os resultados após a inferência do modelo na Azure Cosmos DB para pontuação operacional quase em tempo real.

* **Relatórios operacionais:** As equipas da cadeia de abastecimento precisam de relatórios flexíveis e personalizados em tempo real, dados operacionais precisos. Estes relatórios são necessários para obter uma visão instantânea da eficácia, rentabilidade e produtividade da cadeia de abastecimento. Permite que analistas de dados e outras partes interessadas chave reavaliem constantemente o negócio e identifiquem áreas para ajustar para reduzir os custos operacionais. 

  Synapse Link for Azure Cosmos DB permite a inteligência empresarial rica (BI)/cenários de reporte:

  * Consultar dados operacionais da loja analítica Azure Cosmos DB utilizando a integração nativa com o servidor Synapse SQL sem servidor e a total expressividade da linguagem T-SQL.

  * Modele e publique painéis bi refrescantes auto sobre Azure Cosmos DB através do suporte sem servidor Sinaapse SQL para ferramentas bi familiares. Por exemplo, Serviços de Análise Azure, Power BI Premium, etc.

Seguem-se algumas orientações para a integração de dados para o lote & dados de streaming para a Azure Cosmos DB:

* **Integração de dados de lote & orquestração:** Com as cadeias de fornecimento a ficarem mais complexas, as plataformas de dados da cadeia de fornecimento precisam de se integrar com a variedade de fontes de dados e formatos. A Azure Synapse vem integrado com o mesmo motor de integração de dados e experiências que a Azure Data Factory. Esta integração permite que os engenheiros de dados criem ricos oleodutos de dados sem um motor de orquestração separado:

  * Mover dados de mais de 85 fontes de dados suportadas para [Azure Cosmos DB com Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).

  * Escreva oleodutos ETL isentos de código para Azure Cosmos DB, incluindo [mapeamentos relacionais-hierárquicos e hierárquicos-hierárquicos com fluxos de dados de mapeamento](../data-factory/how-to-sqldb-to-cosmosdb.md).

* **Processamento de & de integração de dados de streaming:** Com o crescimento da IoT Industrial (sensores que rastreiam ativos de 'andar para loja', frotas logísticas conectadas, etc.), há uma explosão de dados em tempo real a ser gerados de forma em streaming que precisa de ser integrada com os dados tradicionais de movimento lento para gerar insights. O Azure Stream Analytics é um serviço recomendado para o streaming de ETL e processamento em Azure com uma [vasta gama de cenários](../stream-analytics/streaming-technologies.md). A Azure Stream Analytics suporta [a Azure Cosmos DB como um lavatório de dados nativo](../stream-analytics/stream-analytics-documentdb-output.md).

## <a name="real-time-personalization"></a>Personalização em tempo real

Os retalhistas de hoje devem construir soluções de e-commerce seguras e escaláveis que atendam às exigências tanto dos clientes como das empresas. Estas soluções de e-commerce precisam de envolver os clientes através de produtos e ofertas personalizadas, processar transações de forma rápida e segura, e focar-se na realização e no atendimento ao cliente. A Azure Cosmos DB juntamente com a mais recente Synapse Link para Azure Cosmos DB permite que os retalhistas gerem recomendações personalizadas para os clientes em tempo real. Utilizam configurações de baixa latência e consistência incapazes para insights imediatos, como mostra a seguinte arquitetura:

:::image type="content" source="./media/synapse-link-use-cases/real-time-personalization.png" alt-text="Azure Synapse Link para Azure Cosmos DB em personalização em tempo real" border="false":::

Link Synapse para Azure Cosmos DB caso de utilização:

* **Prepare & gasoduto preditivo do comboio:** Pode gerar insights sobre os dados operacionais através das suas unidades de negócio ou segmentos de clientes utilizando modelos synapse Spark e machine learning. Isto traduz-se numa entrega personalizada para segmentos de clientes alvo, experiências preditivas de utilizador final e marketing direcionado para se adequar aos seus requisitos de utilizador final.

## <a name="iot-predictive-maintenance"></a>Manutenção preditiva IOT

As inovações industriais da IOT reduziram drasticamente os tempos de inatividade da maquinaria e aumentaram a eficiência global em todos os domínios da indústria. Uma dessas inovações é a análise preditiva da manutenção para máquinas na borda da nuvem.

Segue-se uma arquitetura que aproveita as capacidades nativas de HTAP em nuvem da Azure Synapse Link para Azure Cosmos DB na manutenção preditiva IoT:

:::image type="content" source="./media/synapse-link-use-cases/iot-predictive-maintenance.png" alt-text="Ligação Azure Synapse para Azure Cosmos DB na manutenção preditiva IOT" border="false" :::

Link Synapse para Azure Cosmos DB usar casos:

* **Prepare & gasoduto preditivo do comboio:** Os dados operacionais históricos dos sensores de dispositivos IoT poderiam ser usados para treinar modelos preditivos, como detetores de anomalias. Estes detetores de anomalias são depois implementados na periferia para uma monitorização em tempo real. Este ciclo virtuoso permite uma reconversão contínua dos modelos preditivos.

* **Relatórios operacionais:** Com o crescimento das iniciativas digitais gémeas, as empresas estão a recolher grandes quantidades de dados operacionais de um grande número de sensores para construir uma cópia digital de cada máquina. Este dados powers BI precisa entender tendências sobre dados históricos, além de aplicações em tempo real sobre dados quentes recentes.

## <a name="sample-scenario-htap-for-azure-cosmos-db"></a>Cenário da amostra: HTAP para Azure Cosmos DB

Durante quase uma década, a Azure Cosmos DB tem sido usada por milhares de clientes para aplicações críticas de missão que requerem escala elástica, distribuição global chave na mão, replicação de escrita multi-região para baixa latência e alta disponibilidade de ambos & escreve nas suas cargas de trabalho transacionais.
 
A lista a seguir mostra uma visão geral dos vários padrões de carga de trabalho que são suportados com dados operacionais utilizando a Azure Cosmos DB:

* Aplicativos em tempo real & serviços
* Processamento de fluxos de eventos
* Painéis de BI
* Análise de macrodados
* Aprendizagem automática

O Azure Synapse Link permite que a Azure Cosmos DB não só faça cargas de trabalho transacionais de energia, mas também realize cargas de trabalho analíticos em tempo real sobre dados operacionais históricos. Acontece sem requisitos etl e isolamento de desempenho garantido das cargas de trabalho transacionais.

A imagem a seguir mostra padrões de carga de trabalho usando Azure Cosmos DB: :::image type="content" source="./media/synapse-link-use-cases/synapse-link-workload-patterns.png" alt-text="Azure Synapse Link para padrões de carga de trabalho DB Azure Cosmos" border="false":::

Tomemos o exemplo de uma empresa de comércio eletrónico CompanyXYZ com operações globais em 20 países/regiões para ilustrar os benefícios de escolher a Azure Cosmos DB como a única base de dados em tempo real que alimenta os requisitos transacionais e analíticos de uma plataforma de gestão de inventário.

* O core business da CompanyXYZ depende do sistema de gestão de inventários – daí que a disponibilidade & fiabilidade sejam requisitos fundamentais do pilar. Benefícios da utilização do Azure Cosmos DB:

  * Em virtude da profunda integração com a infraestrutura Azure, e de várias regiões transparentes, a replicação global, a Azure Cosmos DB proporciona uma elevada disponibilidade de [99,999%](high-availability.md) contra as interrupções regionais.

* Os parceiros da cadeia de fornecimento da CompanyXYZ podem estar em locais geográficos separados, mas podem ter de ver uma única visão do inventário de produtos em todo o mundo para apoiar as suas operações locais. Isto inclui a necessidade de ser capaz de ler atualizações feitas por outros parceiros da cadeia de fornecimento em tempo real. Além de poder fazer atualizações sem se preocupar com conflitos com outros parceiros em alta produção. Benefícios da utilização do Azure Cosmos DB:

  * Com o seu exclusivo protocolo de replicação multi-região e loja transacional sem fecho, otimizada por escrito, a Azure Cosmos DB garante menos de 10 latências para leituras indexadas e escritas no percentil 99 a nível global.

  * Ingestão de dados de alto rendimento de ambos os & feeds de dados de streaming com [indexação](index-policy.md) em tempo real na loja transacional.

  * A loja de transações DB da Azure Cosmos oferece mais três opções do que os dois extremos de níveis fortes e eventuais de consistência para alcançar a [disponibilidade vs tradeoffs de desempenho](./consistency-levels.md) mais próximos das necessidades do negócio.

* Os parceiros da cadeia de fornecimento da CompanyXYZ têm padrões de tráfego altamente flutuantes que variam de centenas a milhões de pedidos/s e, portanto, a plataforma de gestão de inventário precisa lidar com a explosão inesperada no tráfego.  Benefícios da utilização do Azure Cosmos DB:

  * A loja transacional da Azure Cosmos DB suporta a escalabilidade elástica do armazenamento e da produção utilizando divisórias horizontais. Os contentores e bases de dados configurados no modo Autopilot podem escalar automaticamente e instantaneamente a produção a forerada com base nas necessidades da aplicação sem afetar a disponibilidade, latência, produção ou desempenho da carga de trabalho a nível global.

* A CompanyXYZ precisa de criar uma plataforma de análise segura para alojar dados históricos de inventário em todo o sistema para permitir análises e insights através de parceiros de cadeia de fornecimento, unidades de negócio e funções. A plataforma de análise precisa de permitir a colaboração em todo o sistema, casos tradicionais de utilização de BI/reporte, casos de utilização de análises avançadas e soluções inteligentes preditivas sobre os dados de inventário operacional. Benefícios da utilização da Synapse Link para Azure Cosmos DB:

  * Ao utilizar [a loja analítica Azure Cosmos DB](analytical-store-introduction.md), uma loja de colunas totalmente isolada, a Synapse Link não permite análises extract-Transform-Load (ETL) em [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md) contra dados operacionais distribuídos globalmente à escala.  Analistas de negócios, engenheiros de dados e cientistas de dados podem agora usar o Synapse Spark ou Synapse SQL de forma interoperável para executar perto de inteligência empresarial em tempo real, analíticos e oleodutos de aprendizagem automática sem afetar o desempenho das suas cargas de trabalho transacionais na Azure Cosmos DB. Veja os [benefícios do Synapse Link em Azure Cosmos DB](synapse-link.md) para mais detalhes.

## <a name="next-steps"></a>Passos seguintes

Para saber mais, consulte os seguintes documentos:

* [Ligação Azure Synapse para Azure Cosmos DB](synapse-link.md) 

* [Arquivo Analítico do Azure Cosmos DB](analytical-store-introduction.md)

* [Trabalhando com Azure Synapse Link para Azure Cosmos DB](configure-synapse-link.md)

* [Perguntas mais frequentes sobre o Azure Synapse Link para o Azure Cosmos DB](synapse-link-frequently-asked-questions.md)

* [Faísca Apache em Azure Synapse Analytics](../synapse-analytics/spark/apache-spark-concepts.md)

* [Suporte de tempo sem servidor SQL no Azure Synapse Analytics](../synapse-analytics/sql/on-demand-workspace-overview.md)