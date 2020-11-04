---
title: Azure Synapse Link para Azure Cosmos DB, benefícios, e quando usá-lo
description: Saiba mais sobre a Azure Synapse Link para Azure Cosmos DB. O Synapse Link permite-lhe executar perto de análises em tempo real (HTAP) usando a Azure Synapse Analytics sobre dados operacionais em Azure Cosmos DB.
author: Rodrigossz
ms.author: rosouz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: 1e78a7ff806cbd6a7f30c68786e62f8508c850ac
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340690"
---
# <a name="what-is-azure-synapse-link-for-azure-cosmos-db-preview"></a>O que é Azure Synapse Link para Azure Cosmos DB (Preview)?
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Azure Synapse Link para Azure Cosmos DB está atualmente em pré-visualização. Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Synapse Link for Azure Cosmos DB é uma capacidade de processamento transacional e analítico híbrido nativo da nuvem (HTAP) que lhe permite executar perto de análises em tempo real sobre dados operacionais em Azure Cosmos DB. A azure Synapse Link cria uma integração apertada e perfeita entre Azure Cosmos DB e Azure Synapse Analytics.

Utilizando [a loja analítica Azure Cosmos DB](analytical-store-introduction.md), uma loja de colunas totalmente isolada, a Azure Synapse Link não permite análises extract-Transform-Load (ETL) em [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md) contra os seus dados operacionais em escala. Analistas de negócios, engenheiros de dados e cientistas de dados podem agora usar Synapse Spark ou Synapse SQL intercambiavelmente para executar perto de software de negócios em tempo real, analíticos e oleodutos de aprendizagem automática. Pode consegui-lo sem afetar o desempenho das suas cargas de trabalho no Azure Cosmos DB. 

A imagem a seguir mostra a integração da Azure Synapse Link com a Azure Cosmos DB e a Azure Synapse Analytics: 

:::image type="content" source="./media/synapse-link/synapse-analytics-cosmos-db-architecture.png" alt-text="Diagrama de arquitetura para integração Azure Synapse Analytics com Azure Cosmos DB" border="false":::

## <a name="benefits"></a><a id="synapse-link-benefits"></a> Benefícios

Para analisar grandes conjuntos de dados operacionais, minimizando o impacto no desempenho das cargas de trabalho transacionais críticas da missão, tradicionalmente, os dados operacionais em Azure Cosmos DB são extraídos e processados pelos gasodutos Extract-Transform-Load (ETL). Os oleodutos ETL requerem muitas camadas de movimento de dados, resultando em muita complexidade operacional e impacto no desempenho das suas cargas de trabalho transacionais. Aumenta também a latência para analisar os dados operacionais a partir do momento de origem.

Quando comparada com as soluções tradicionais baseadas em ETL, a Azure Synapse Link para Azure Cosmos DB oferece várias vantagens tais como:  

### <a name="reduced-complexity-with-no-etl-jobs-to-manage"></a>Complexidade reduzida sem empregos ETL para gerir

O Azure Synapse Link permite-lhe aceder diretamente à loja analítica Azure Cosmos DB utilizando a Azure Synapse Analytics sem movimento de dados complexo. Quaisquer atualizações feitas aos dados operacionais são visíveis na loja analítica em quase tempo real sem ETL ou alterando os trabalhos de alimentação. Você pode executar análises em larga escala contra loja analítica, da Synapse Analytics, sem transformação adicional de dados.

### <a name="near-real-time-insights-into-your-operational-data"></a>Quase introspeções em tempo real nos seus dados operacionais

Agora pode obter informações ricas sobre os seus dados operacionais em quase tempo real, utilizando o Azure Synapse Link. Os sistemas baseados em ETL tendem a ter uma maior latência para analisar os seus dados operacionais, devido a muitas camadas necessárias para extrair, transformar e carregar os dados operacionais. Com a integração nativa da loja analítica Azure Cosmos DB com a Azure Synapse Analytics, pode analisar dados operacionais em quase tempo real, permitindo novos cenários de negócio. 

### <a name="no-impact-on-operational-workloads"></a>Sem impacto nas cargas de trabalho operacionais

Com a Azure Synapse Link, pode executar consultas analíticas contra uma loja analítica Azure Cosmos DB (uma loja de colunas separada) enquanto as operações transacionais são processadas utilizando a produção provisitada para a carga de trabalho transacional (uma loja transacional baseada em linha).  A carga de trabalho analítica é servida independentemente do tráfego de carga de trabalho transacional sem consumir qualquer do produto previsto para os seus dados operacionais.

### <a name="optimized-for-large-scale-analytics-workloads"></a>Otimizado para cargas de trabalho de análise em larga escala

A loja analítica Azure Cosmos DB está otimizada para fornecer escalabilidade, elasticidade e desempenho para cargas de trabalho analíticas sem qualquer dependência dos tempos de cálculo. A tecnologia de armazenamento é auto-gerida para otimizar as suas cargas de trabalho analíticas. Com suporte incorporado no Azure Synapse Analytics, o acesso a esta camada de armazenamento proporciona simplicidade e alto desempenho.

### <a name="cost-effective"></a>Rentável

Com o Azure Synapse Link, você pode obter uma solução otimizada e totalmente gerida para análise operacional. Elimina as camadas extra de armazenamento e cálculo necessárias nos oleodutos tradicionais da ETL para a análise de dados operacionais. 

A loja analítica Azure Cosmos DB segue um modelo de preços baseado no consumo, que se baseia no armazenamento de dados e operações de leitura/escrita analítica e consultas executadas. Não requer que provisa qualquer produção, como faz hoje para as cargas de trabalho transacionais. Aceder aos seus dados com motores computacional altamente elásticos da Azure Synapse Analytics torna o custo global de armazenamento e cálculo de execução muito eficiente.


### <a name="analytics-for-locally-available-globally-distributed-multi-region-writes"></a>Analytics para escritas locais, distribuídas globalmente, multi-regiões

Pode executar consultas analíticas eficazmente contra a cópia regional mais próxima dos dados em Azure Cosmos DB. A Azure Cosmos DB fornece a capacidade de ponta para executar as cargas de trabalho analíticas distribuídas globalmente, juntamente com cargas de trabalho transacionais de forma ativa.

## <a name="enable-htap-scenarios-for-your-operational-data"></a>Ativar cenários de HTAP para os seus dados operacionais

A Synapse Link reúne a loja analítica Azure Cosmos DB com suporte analítico analítico Azure Synapse. Esta integração permite-lhe construir soluções HTAP nativas em nuvem (processamento transacional/analítico híbrido) que gerem insights baseados em atualizações em tempo real para os seus dados operacionais em grandes conjuntos de dados. Desbloqueia novos cenários de negócio para elevar os alertas com base nas tendências ao vivo, construir perto de dashboards em tempo real e experiências de negócio baseadas no comportamento do utilizador.

### <a name="azure-cosmos-db-analytical-store"></a>Loja analítica Azure Cosmos DB

A loja analítica Azure Cosmos DB é uma representação orientada para colunas dos seus dados operacionais em Azure Cosmos DB. Esta loja analítica é adequada para consultas rápidas e eficazes em grandes conjuntos de dados operacionais, sem copiar dados e impactar o desempenho das suas cargas de trabalho transacionais.

A loja analítica capta automaticamente inserções de alta frequência, atualizações, elimina nas suas cargas de trabalho transacionais em tempo quase real, como uma capacidade totalmente gerida ("auto-sincronização") da Azure Cosmos DB. Não é necessário qualquer feed de alteração ou ETL. 

Se tiver uma conta DB Azure Cosmos distribuída globalmente, depois de ativar uma loja analítica para um recipiente, estará disponível em todas as regiões para essa conta. Para obter mais informações sobre a loja analítica, consulte o artigo [da loja Azure Cosmos DB Analytical.](analytical-store-introduction.md)

### <a name="integration-with-azure-synapse-analytics"></a><a id="synapse-link-integration"></a>Integração com a Azure Synapse Analytics

Com a Synapse Link, pode agora ligar-se diretamente aos seus contentores DB Azure Cosmos da Azure Synapse Analytics e aceder à loja analítica sem conectores separados. A Azure Synapse Analytics suporta atualmente a Synapse Link com [o Sinapse Apache Spark](../synapse-analytics/spark/apache-spark-concepts.md) e o [sinapse SQL sem servidor](../synapse-analytics/sql/on-demand-workspace-overview.md).

Pode consultar os dados da loja analítica Azure Cosmos DB simultaneamente, com interop através de diferentes tempos de execução analíticos suportados pela Azure Synapse Analytics. Não são necessárias transformações adicionais de dados para analisar os dados operacionais. Pode consultar e analisar os dados da loja analítica utilizando:

* Sinaapse Apache Spark com apoio total para Scala, Python, SparkSQL e C#. A Faísca de Sinapse é central para a engenharia de dados e cenários de ciência de dados

* SQL sem servidor com linguagem T-SQL e suporte para ferramentas bi familiares (por exemplo, Power BI Premium, etc.)

> [!NOTE]
> A partir da Azure Synapse Analytics, você pode aceder a lojas analíticas e transacionais no seu contentor Azure Cosmos DB. No entanto, se pretender executar análises ou análises em larga escala nos seus dados operacionais, recomendamos que utilize uma loja analítica para evitar o impacto no desempenho nas cargas de trabalho transacionais.

> [!NOTE]
> Você pode executar análises com baixa latência em uma região de Azure ligando o seu contentor Azure Cosmos DB ao tempo de execução da Synapse naquela região.

Esta integração permite os seguintes cenários HTAP para diferentes utilizadores:

* Um engenheiro bi que quer modelar e publicar um relatório power bi para aceder aos dados operacionais ao vivo em Azure Cosmos DB diretamente através do Synapse SQL.

* Um analista de dados que quer obter insights a partir dos dados operacionais de um contentor Azure Cosmos DB consultando-os com Synapse SQL, leia os dados em escala e combine essas descobertas com outras fontes de dados.

* Um cientista de dados que quer usar a Synapse Spark para encontrar uma característica para melhorar o seu modelo e treinar esse modelo sem fazer engenharia de dados complexa. Também podem escrever os resultados da inferência pós-modelo em Azure Cosmos DB para pontuação em tempo real nos dados através do Spark Synapse.

* Um engenheiro de dados que quer tornar os dados acessíveis aos consumidores, criando tabelas SQL ou Spark sobre os contentores DB da Azure Cosmos sem processos ETL manuais.

Para obter mais informações sobre o suporte de runtime Azure Synapse Analytics para Azure Cosmos DB, consulte [Azure Synapse Analytics para suporte de DB cosmos](../synapse-analytics/synapse-link/concept-synapse-link-cosmos-db-support.md).

## <a name="when-to-use-azure-synapse-link-for-azure-cosmos-db"></a>Quando usar a Ligação Azure Synapse para Azure Cosmos DB?

A Synapse Link é recomendada nos seguintes casos:

* Se é cliente da Azure Cosmos DB e quer executar análises, BI e machine learning sobre os seus dados operacionais. Nestes casos, o Synapse Link proporciona uma experiência de análise mais integrada sem afetar a produção aprovisionada da sua loja transacional. Por exemplo:

  * Se estiver a executar análises ou BI nos seus dados operacionais do Azure Cosmos DB, utilize conectores separados hoje em dia, ou

  * Se estiver a executar processos ETL para extrair dados operacionais num sistema de análise separado.
 
Nestes casos, o Synapse Link proporciona uma experiência de análise mais integrada sem afetar a produção aprovisionada da sua loja transacional.

O Synapse Link não é recomendado se estiver à procura de requisitos tradicionais de armazém de dados, tais como alta concordância, gestão da carga de trabalho e persistência de agregados em várias fontes de dados. Para mais informações, consulte [cenários comuns que podem ser alimentados com Azure Synapse Link para Azure Cosmos DB](synapse-link-use-cases.md).

## <a name="limitations"></a>Limitações

* Atualmente, o Azure Synapse Link para o Azure Cosmos DB é suportado para a API SQL e a API do Azure Cosmos DB para MongoDB. Não é suportado para a API do Gremlin nem a API de Tabela. O suporte para a API cassandra está em pré-visualização privada, para mais informações contacte a equipa do [Azure Synapse Link](mailto:cosmosdbsynapselink@microsoft.com).  

* Atualmente, a loja analítica só pode ser ativada para novos contentores. Para utilizar a loja analítica para recipientes existentes, migrar dados dos seus recipientes existentes para novos recipientes utilizando [ferramentas de migração Azure Cosmos DB](cosmosdb-migrationchoices.md). Pode ativar o Synapse Link em novas e existentes contas DB do Azure Cosmos.

* Para os recipientes com loja analítica ligada, a cópia de segurança automática e a restauração dos seus dados na loja analítica não são suportadas neste momento. Quando o Synapse Link estiver ativado numa conta de base de [dados,](./online-backup-and-restore.md) a Azure Cosmos DB continuará a receber automaticamente cópias de segurança dos seus dados na loja transacional (apenas) de contentores num intervalo de backup programado, como sempre. É importante notar que quando um recipiente com loja analítica ligada é restaurado para uma nova conta, o recipiente será restaurado apenas com uma loja transacional e sem loja analítica ativada. 

* O acesso à loja de análiseS DB da Azure Cosmos com o Sinaapse SQL abastado não está disponível atualmente.

## <a name="pricing"></a>Preços

O modelo de faturação da Azure Synapse Link inclui os custos incorridos com a utilização da loja analítica Azure Cosmos DB e do tempo de execução da Synapse. Para saber mais, consulte os preços da [loja analítica Azure Cosmos DB](analytical-store-introduction.md#analytical-store-pricing) e os [artigos de preços Azure Synapse Analytics.](https://azure.microsoft.com/pricing/details/synapse-analytics/)

## <a name="next-steps"></a>Passos seguintes

Para saber mais, consulte os seguintes documentos:

* [Descrição geral do arquivo analítico do Azure Cosmos DB](analytical-store-introduction.md)

* [Introdução ao Azure Synapse Link para o Azure Cosmos DB](configure-synapse-link.md)
 
* [O que é suportado no tempo de execução do Azure Synapse Analytics](../synapse-analytics/synapse-link/concept-synapse-link-cosmos-db-support.md)

* [Perguntas mais frequentes sobre o Azure Synapse Link para o Azure Cosmos DB](synapse-link-frequently-asked-questions.md)

* [Casos de utilização do Azure Synapse Link para o Azure Cosmos DB](synapse-link-use-cases.md)