---
title: Análise em tempo real usa casos com Ligação Synapse Azure para Azure Cosmos DB
description: Saiba como o Azure Synapse Link for Azure Cosmos DB é usado na análise da cadeia de fornecimento, previsão, reporte, personalização em tempo real e manutenção preditiva iOT.
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: ramkris
ms.openlocfilehash: f0efaf593725fd676ae8e85ff9c190c8b850fef1
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744667"
---
# <a name="azure-synapse-link-for-azure-cosmos-db-near-real-time-analytics-use-cases"></a>Ligação Azure Synapse para Azure Cosmos DB: Casos de utilização de análises em tempo real

[Azure Synapse Link](synapse-link.md) for Azure Cosmos DB é uma capacidade de processamento transalítico e analítico híbrido nativo em nuvem (HTAP) que lhe permite executar análises em tempo real sobre dados operacionais. A Synapse Link cria uma integração apertada e perfeita entre o Azure Cosmos DB e o Azure Synapse Analytics.

Você pode estar curioso para entender que casos de uso da indústria podem alavancar esta capacidade htap nativa da nuvem para análises quase em tempo real sobre dados operacionais. Aqui estão três casos de uso comum para Azure Synapse Link para Azure Cosmos DB:

* Análise da cadeia de abastecimento, previsão & relatório
* Personalização em tempo real
* Manutenção preditiva, deteção de anomalias em cenários iot

> [!NOTE]
> A Ligação Azure Synapse para o Azure Cosmos DB tem como alvo o cenário em que as equipas empresariais procuram correr perto da análise em tempo real. Estas análises são executadas sem ETL sobre dados operacionais gerados através de aplicações transacionais construídas em Azure Cosmos DB. Isto não substitui a necessidade de um armazém de dados separado quando existem requisitos tradicionais de armazém de dados, tais como gestão de carga de trabalho, alta conmoedação, agregados de persistência em várias fontes de dados.

## <a name="supply-chain-analytics-forecasting--reporting"></a>Análise da cadeia de abastecimento, previsão & relatório

Estudos de investigação mostram que incorporar grandes análises de dados nas operações da cadeia de fornecimento leva a melhorias nos tempos de entrega de encomendas a ciclo e na eficiência da cadeia de fornecimento.

Os fabricantes estão a embarcar em tecnologias nativas da nuvem para fugir dos constrangimentos dos sistemas de Planeamento de Recursos Empresariais (ERP) e Sistemas de Gestão de Cadeias de Abastecimento (SCM). Com as cadeias de abastecimento a gerarem cada vez mais volumes de dados operacionais a cada minuto (encomenda, envio, dados de transações), os fabricantes precisam de uma base de dados operacional. Esta base de dados operacional deve escalar para lidar com os volumes de dados, bem como uma plataforma analítica para chegar a um nível de inteligência contextual em tempo real para se manter à frente da curva.

A arquitetura que se segue mostra o poder de alavancar o Azure Cosmos DB como base de dados operacional nativa da nuvem e ligação synapse na análise da cadeia de fornecimento:

![Ligação Azure Synapse para Azure Cosmos DB na análise da cadeia de abastecimento ](./media/synapse-link-use-cases/supply-chain-analytics.png)

Com base em arquitetura anterior, você pode obter os seguintes casos de uso com Synapse Link para Azure Cosmos DB:

* **Prepare & gasoduto preditivo do comboio:** Gere insights sobre os dados operacionais em toda a cadeia de fornecimento usando traduções de aprendizagem automática. Desta forma, pode reduzir o inventário, os custos de operações e reduzir os prazos de encomenda para entrega para os clientes.

  A Synapse Link permite-lhe analisar os dados operacionais em mudança no Azure Cosmos DB sem quaisquer processos ETL manuais. Poupa-te de custos adicionais, latência e complexidade operacional. A Synapse Link permite que engenheiros de dados e cientistas de dados construam oleodutos preditivos robustos:

  * Consulta dados operacionais da loja analítica Azure Cosmos DB aproveitando a integração nativa com piscinas Apache Spark em Azure Synapse Analytics. Pode consultar os dados num caderno interativo ou em trabalhos remotos programados sem engenharia de dados complexa.

  * Construir modelos de Machine Learning (ML) com algoritmos Spark ML e integração Azure ML no Azure Synapse Analytics.

  * Reescreva os resultados após a inferência do modelo no Azure Cosmos DB para pontuação operacional em quase tempo real.

* **Relatório operacional:** As equipas da cadeia de fornecimento precisam de relatórios flexíveis e personalizados em tempo real, dados operacionais precisos. Estes relatórios são necessários para obter uma visão instantânea da eficácia da cadeia de abastecimento, da rentabilidade e da produtividade. Permite que analistas de dados e outras partes interessadas chave reavaliem constantemente o negócio e identifiquem áreas para ajustar para reduzir custos operacionais. 

  Synapse Link for Azure Cosmos DB permite ricos cenários de inteligência empresarial (BI)/reporte:

  * Consulta dados operacionais da loja analítica Azure Cosmos DB utilizando a integração nativa com synapse SQL Serverless e expressividade total da linguagem T-SQL.

  * Modele e publique dashboards BI refrescantes automaticamente sobre O Azure Cosmos DB através do suporte Synapse SQL Serverless para ferramentas BI familiares. Por exemplo, Serviços de Análise Azure, Power BI Premium, etc.

Segue-se algumas orientações para a integração de dados para o lote & streaming de dados para O Azure Cosmos DB:

* **Integração de dados de lote & orquestração:** Com as cadeias de abastecimento a ficarem mais complexas, as plataformas de dados da cadeia de fornecimento precisam de se integrar com a variedade de fontes e formatos de dados. O Azure Synapse surge integrado com o mesmo motor de integração de dados e experiências que a Azure Data Factory. Esta integração permite que os engenheiros de dados criem oleodutos de dados ricos sem um motor de orquestração separado:

  * Mova dados de 85+ fontes de dados suportadas para [O Azure Cosmos DB com a Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).

  * Escreva oleodutos ETL isentos de código sem código para o Azure Cosmos DB, incluindo [mapeamentos relacionais-hierárquicos e hierárquicos-hierárquicos com fluxos](../data-factory/how-to-sqldb-to-cosmosdb.md)de dados de mapeamento.

* **Processamento de & de integração de dados em streaming:** Com o crescimento da IoT Industrial (sensores que rastreiam os ativos do "chão a loja", frotas logísticas conectadas, etc.), há uma explosão de dados em tempo real sendo gerados de forma streaming que precisa de ser integrado com dados tradicionais em movimento lento para gerar insights. O Azure Stream Analytics é um serviço recomendado para o streaming e processamento de ETL e processamento em Azure com uma [vasta gama de cenários.](../stream-analytics/streaming-technologies.md) O Azure Stream Analytics suporta o [Azure Cosmos DB como um sumidouro de dados nativo.](../stream-analytics/stream-analytics-documentdb-output.md)

## <a name="real-time-personalization"></a>Personalização em tempo real

Os retalhistas de hoje devem construir soluções de comércio eletrónico seguras e escaláveis que respondam às exigências dos clientes e das empresas. Estas soluções de e-commerce precisam de envolver os clientes através de produtos e ofertas personalizados, processar transações de forma rápida e segura, e focar-se no cumprimento e atendimento ao cliente. A Azure Cosmos DB, juntamente com o mais recente Synapse Link for Azure Cosmos DB, permite que os retalhistas gerem recomendações personalizadas para os clientes em tempo real. Utilizam definições de baixa latência e consistência insupreensíveis para insights imediatos, como mostra a seguinte arquitetura:

![Ligação Azure Synapse para Azure Cosmos DB em personalização em tempo real](./media/synapse-link-use-cases/real-time-personalization.png)

Synapse Link para o caso de utilização do Azure Cosmos DB:

* **Prepare & gasoduto preditivo do comboio:** Pode gerar insights sobre os dados operacionais através das suas unidades de negócio ou segmentos de clientes utilizando modelos Synapse Spark e machine learning. Isto traduz-se em entrega personalizada para segmentos de clientes direcionados, experiências preditivas de utilizador final e marketing direcionado para se adequar aos seus requisitos de utilizador final.

## <a name="iot-predictive-maintenance"></a>Manutenção preditiva do IOT

As inovações industriais de IOT reduziram drasticamente os tempos de redução das máquinas e aumentaram a eficiência global em todos os domínios da indústria. Uma dessas inovações é a análise preditiva da manutenção das máquinas na borda da nuvem.

Segue-se uma arquitetura que aproveita as capacidades htap nativas da nuvem da Ligação Azure Synapse para O Azure Cosmos DB em manutenção preditiva IoT:

![Ligação Azure Synapse para Azure Cosmos DB em manutenção preditiva iOT](./media/synapse-link-use-cases/iot-predictive-maintenance.png)

Synapse Link para Azure Cosmos DB casos de utilização:

* **Prepare & gasoduto preditivo do comboio:** Os dados operacionais históricos dos sensores do dispositivo IoT podem ser usados para treinar modelos preditivos, como detetores de anomalias. Estes detetores de anomalias são então implantados de volta à borda para monitorização em tempo real. Um ciclo tão virtuoso permite a reconversão contínua dos modelos preditivos.

* **Relatório operacional:** Com o crescimento de iniciativas digitais gémeas, as empresas estão a recolher grandes quantidades de dados operacionais a partir de um grande número de sensores para construir uma cópia digital de cada máquina. Estes poderes de dados BI precisam entender as tendências sobre dados históricos, além de aplicações em tempo real sobre dados quentes recentes.

## <a name="sample-scenario-htap-for-azure-cosmos-db"></a>Cenário da amostra: HTAP para Azure Cosmos DB

Durante quase uma década, a Azure Cosmos DB tem sido usada por milhares de clientes para aplicações críticas de missão que requerem escala elástica, distribuição global chave na mão, replicação multi-master para baixa latência e elevada disponibilidade de ambas as leituras & escreve nas suas cargas de trabalho transacionais.
 
A lista que se segue mostra uma visão geral dos vários padrões de carga de trabalho que são suportados com dados operacionais usando o Azure Cosmos DB:

* Aplicativos em tempo real & serviços
* Processamento de fluxos de eventos
* Dashboards BI
* Análise de macrodados
* Aprendizagem automática

A Azure Synapse Link permite ao Azure Cosmos DB não só alimentar cargas de trabalho transacionais, mas também realizar cargas de trabalho analíticas em tempo real sobre dados operacionais históricos. Acontece sem requisitos ETL e isolamento de desempenho garantido das cargas de trabalho transacionais.

A imagem que se segue mostra padrões de carga de trabalho usando Azure Cosmos DB: ![ Azure Synapse Link para padrões de carga de trabalho do Azure Cosmos DB](./media/synapse-link-use-cases/synapse-link-workload-patterns.png)

Tomemos o exemplo de uma empresa de comércio eletrónico CompanyXYZ com operações globais em 20 países/regiões para ilustrar os benefícios de escolher o Azure Cosmos DB como a única base de dados em tempo real que alimenta os requisitos transacionais e analíticos de uma plataforma de gestão de inventário.

* O principal negócio da CompanyXYZ depende do sistema de gestão de inventário – portanto, a disponibilidade & fiabilidade são requisitos fundamentais do pilar. Benefícios da utilização do Azure Cosmos DB:

  * Em virtude de uma profunda integração com a infraestrutura Azure e a replicação global transparente multi-master, a Azure Cosmos DB proporciona uma elevada disponibilidade de [99,999%](high-availability.md) para o setor contra interrupções regionais.

* Os parceiros da cadeia de fornecimento da CompanyXYZ podem estar em locais geográficos separados, mas podem ter de ver uma única vista do inventário do produto em todo o mundo para apoiar as suas operações locais. Isto inclui a necessidade de poder ler atualizações feitas por outros parceiros da cadeia de abastecimento em tempo real. Além de poder fazer atualizações sem se preocupar com conflitos com outros parceiros em alta sueste. Benefícios da utilização do Azure Cosmos DB:

  * Com o seu protocolo único de replicação multi-master e loja transacional sem trincos, a Azure Cosmos DB garante menos de 10 ms de atraso para leituras indexadas e escreve no percentil 99 a nível global.

  * A ingestão de alta produção de ambos os lotes & feeds de dados de streaming com [indexação](index-policy.md) em tempo real na loja transacional.

  * A loja transacional Azure Cosmos DB oferece mais três opções do que os dois extremos de níveis de consistência fortes e eventualpara alcançar a [disponibilidade vs tradeoffs](consistency-levels-tradeoffs.md) de desempenho mais próximos das necessidades do negócio.

* Os parceiros da cadeia de fornecimento da CompanyXYZ têm padrões de tráfego altamente flutuantes que variam entre centenas e milhões de pedidos/s e, portanto, a plataforma de gestão de inventário precisa de lidar com uma explosão inesperada no tráfego.  Benefícios da utilização do Azure Cosmos DB:

  * A loja transacional da Azure Cosmos DB suporta a escalabilidade elástica do armazenamento e da produção utilizando divisóriahorizontal. Os contentores e bases de dados configurados no modo Autopilot podem escalar automaticamente e instantaneamente a produção aprovisionada com base nas necessidades da aplicação sem afetar a disponibilidade, latência, produção ou desempenho da carga de trabalho a nível global.

* A CompanyXYZ precisa de estabelecer uma plataforma de análise segura para alojar dados históricos de inventário em todo o sistema para permitir análises e insights entre parceiros, unidades de negócio e funções da cadeia de fornecimento. A plataforma de análise precisa de permitir a colaboração em todo o sistema, casos tradicionais de utilização bi/reporte, casos avançados de utilização de análises e soluções inteligentes preditivas sobre os dados do inventário operacional. Benefícios da utilização de Synapse Link para Azure Cosmos DB:

  * Ao utilizar a [loja analítica Azure Cosmos DB,](analytical-store-introduction.md)uma loja de colunas totalmente isolada, a Synapse Link não permite análises de extracção-transformação (ETL) em [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md) contra dados operacionais distribuídos globalmente em escala.  Analistas de negócios, engenheiros de dados e cientistas de dados podem agora usar synapse Spark ou Synapse SQL de uma forma interoperável para executar perto de inteligência de negócios em tempo real, análise e machine learning pipelines sem afetar o desempenho das suas cargas de trabalho transacionais em Azure Cosmos DB. Consulte os [benefícios da Ligação Synapse em Azure Cosmos DB](synapse-link.md) para mais detalhes.

## <a name="next-steps"></a>Passos seguintes

Para saber mais, consulte os seguintes docs:

* [Ligação Azure Synapse para Azure Cosmos DB](synapse-link.md) 

* [Loja Analítica Azure Cosmos DB](analytical-store-introduction.md)

* [Trabalhar com Azure Synapse Link para Azure Cosmos DB](configure-synapse-link.md)

* [Perguntas frequentes sobre Azure Synapse Link for Azure Cosmos DB](synapse-link-frequently-asked-questions.md)

* [Faísca Apache em Azure Synapse Analytics](../synapse-analytics/spark/apache-spark-concepts.md)

* [SQL sem servidor/a pedido na Azure Synapse Analytics](../synapse-analytics/sql/on-demand-workspace-overview.md)
