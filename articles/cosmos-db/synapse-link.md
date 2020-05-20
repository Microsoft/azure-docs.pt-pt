---
title: Ligação Azure Synapse para Azure Cosmos DB, benefícios e quando usá-lo
description: Saiba mais sobre o Azure Synapse Link para Azure Cosmos DB. O Synapse Link permite-lhe correr perto da análise em tempo real (HTAP) utilizando a Azure Synapse Analytics sobre os dados operacionais em Azure Cosmos DB.
author: srchi
ms.author: srchi
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.reviewer: sngun
ms.openlocfilehash: 709b442fde94d88adc7fe4cce16ee1a50bf06c6b
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83674348"
---
# <a name="what-is-azure-synapse-link-for-azure-cosmos-db-preview"></a>O que é Azure Synapse Link for Azure Cosmos DB (Pré-visualização)?

> [!IMPORTANT]
> Azure Synapse Link for Azure Cosmos DB está atualmente em pré-visualização. Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Para mais informações, consulte [os termos suplementares de utilização para as pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Synapse Link for Azure Cosmos DB é uma capacidade de processamento transalítico e analítico híbrido nativo-nuvem (HTAP) que lhe permite executar análises em tempo real sobre dados operacionais em Azure Cosmos DB. A Azure Synapse Link cria uma integração apertada e perfeita entre o Azure Cosmos DB e o Azure Synapse Analytics.

Utilizando [a loja analítica Azure Cosmos DB](analytical-store-introduction.md), uma loja de colunas totalmente isolada, a Ligação Synapse Azure não permite análises de extracção-transformação (ETL) em [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md) contra os seus dados operacionais em escala. Analistas de negócios, engenheiros de dados e cientistas de dados podem agora usar synapse Spark ou Synapse SQL intercambiavelmente para executar perto de inteligência de negócios em tempo real, análise e machine learning pipelines. Pode conseguir isso sem afetar o desempenho das suas cargas de trabalho transacionais no Azure Cosmos DB. 

A imagem seguinte mostra a integração da Ligação Synapse Azure com a Azure Cosmos DB e a Azure Synapse Analytics: 

![Diagrama de arquitetura para azure synapse integração com Azure Cosmos DB](./media/synapse-link/synapse-analytics-cosmos-db-architecture.png)

## <a name="benefits"></a><a id="synapse-link-benefits"></a>Benefícios

Para analisar grandes conjuntos de dados operacionais, minimizando o impacto no desempenho das cargas de trabalho transacionais críticas da missão, tradicionalmente, os dados operacionais em Azure Cosmos DB são extraídos e processados por gasodutos Extract-Transform-Load (ETL). Os oleodutos ETL requerem muitas camadas de movimento de dados, resultando em muita complexidade operacional e impacto no desempenho nas suas cargas de trabalho transacionais. Aumenta também a latência de analisar os dados operacionais a partir do momento da origem.

Quando comparado com as soluções tradicionais baseadas na ETL, o Azure Synapse Link for Azure Cosmos DB oferece várias vantagens tais como:  

### <a name="reduced-complexity-with-no-etl-jobs-to-manage"></a>Complexidade reduzida sem empregos ETL para gerir

O Azure Synapse Link permite-lhe aceder diretamente à loja analítica Azure Cosmos DB utilizando o Azure Synapse Analytics sem um movimento complexo de dados. Quaisquer atualizações feitas aos dados operacionais são visíveis na loja analítica em quase tempo real sem ETL ou alterando postos de trabalho de alimentação. Pode executar análises em larga escala contra loja analítica, a partir da Synapse Analytics, sem transformação adicional de dados.

### <a name="near-real-time-insights-into-your-operational-data"></a>Informações quase em tempo real sobre os seus dados operacionais

Agora pode obter informações ricas sobre os seus dados operacionais em tempo real, utilizando o Azure Synapse Link. Os sistemas baseados na ETL tendem a ter maior latência para analisar os seus dados operacionais, devido a muitas camadas necessárias para extrair, transformar e carregar os dados operacionais. Com a integração nativa da loja analítica Azure Cosmos DB com a Azure Synapse Analytics, pode analisar dados operacionais em tempo próximo, permitindo novos cenários de negócio. 


### <a name="no-impact-on-operational-workloads"></a>Sem impacto nas cargas de trabalho operacionais

Com o Azure Synapse Link, pode executar consultas analíticas contra uma loja analítica Azure Cosmos DB (uma loja de colunas separada) enquanto as operações transacionais são processadas utilizando a produção provisionativa para a carga de trabalho transacional (uma loja transacional baseada em linhas).  A carga de trabalho analítica é servida independentemente do tráfego de carga de trabalho transacional sem consumir qualquer dos dados de entrada previstos para os seus dados operacionais.

### <a name="optimized-for-large-scale-analytics-workloads"></a>Otimizado para cargas de trabalho de análise em larga escala

A loja analítica Azure Cosmos DB está otimizada para proporcionar escalabilidade, elasticidade e desempenho para cargas de trabalho analíticas sem qualquer dependência dos tempos de execução da computação. A tecnologia de armazenamento é autogerida para otimizar as suas cargas de trabalho de análise. Com suporte incorporado no Azure Synapse Analytics, o acesso a esta camada de armazenamento proporciona simplicidade e alto desempenho.

### <a name="cost-effective"></a>Rentável

Com o Azure Synapse Link, pode obter uma solução otimizada em termos de custos e totalmente gerida para análise operacional. Elimina as camadas extras de armazenamento e computação necessárias nos oleodutos Tradicionais ETL para analisar dados operacionais. 

A loja analítica Azure Cosmos DB segue um modelo de preços baseado no consumo, que se baseia no armazenamento de dados e operações de leitura/escrita analíticas e consultas executadas. Não requer que oprovisione qualquer entrada, como faz hoje para as cargas de trabalho transacionais. Aceder aos seus dados com motores computacionais altamente elásticos da Azure Synapse Analytics torna o custo global de armazenamento e computação muito eficiente.


### <a name="analytics-for-locally-available-globally-distributed-multi-master-data"></a>Analytics para dados locais disponíveis, distribuídos globalmente, multi-master

Pode executar consultas analíticas eficazmente contra a cópia regional mais próxima dos dados em Azure Cosmos DB. A Azure Cosmos DB fornece a capacidade de última geração para executar as cargas de trabalho analíticas distribuídas globalmente, juntamente com cargas de trabalho transacionais de forma ativa.

## <a name="enable-htap-scenarios-for-your-operational-data"></a>Ativar cenários HTAP para os seus dados operacionais

A Synapse Link reúne a loja analítica Azure Cosmos DB com suporte a análise azure Synapse. Esta integração permite-lhe construir soluções htap nativas em nuvem (processamento transalítico/analítico híbrido) que geram insights baseados em atualizações em tempo real aos seus dados operacionais em grandes conjuntos de dados. Desbloqueia novos cenários de negócio para elevar alertas com base nas tendências ao vivo, construir perto de dashboards em tempo real e experiências de negócio baseadas no comportamento do utilizador.

### <a name="azure-cosmos-db-analytical-store"></a>Loja analítica Azure Cosmos DB

A loja analítica Azure Cosmos DB é uma representação orientada para colunas dos seus dados operacionais em Azure Cosmos DB. Esta loja analítica é adequada para consultas rápidas e eficazes em grandes conjuntos de dados operacionais, sem copiar dados e afetar o desempenho das suas cargas de trabalho transacionais.

A loja analítica capta automaticamente inserções de alta frequência, atualizações, elimina nas suas cargas de trabalho transacionais em tempo real, como uma capacidade totalmente gerida ("auto-sincronização") do Azure Cosmos DB. Não é necessário alterar o feed ou eTL. 

Se tiver uma conta Azure Cosmos DB distribuída globalmente, depois de ativar a loja analítica para um contentor, estará disponível em todas as regiões para essa conta. Para obter mais informações sobre a loja analítica, consulte o artigo de visão geral da [loja analítica Azure Cosmos DB.](analytical-store-introduction.md)

### <a name="integration-with-azure-synapse-analytics"></a><a id="synapse-link-integration"></a>Integração com azure Synapse Analytics

Com o Synapse Link, pode agora ligar-se diretamente aos seus recipientes Azure Cosmos DB da Azure Synapse Analytics e aceder à loja analítica sem conectores separados. A Azure Synapse Analytics suporta atualmente a Ligação Synapse com [a Synapse Apache Spark](../synapse-analytics/spark/apache-spark-concepts.md) e a [Synapse SQL Serverless](../synapse-analytics/sql/on-demand-workspace-overview.md).

Pode consultar os dados da loja analítica Azure Cosmos DB simultaneamente, com interop através de diferentes tempos de análise suportados pela Azure Synapse Analytics. Não são necessárias transformações adicionais de dados para analisar os dados operacionais. Pode consultar e analisar os dados da loja analítica utilizando:

* Synapse Apache Spark com suporte total para Scala, Python, SparkSQL e C#. Synapse Spark é central na engenharia de dados e cenários de ciência de dados

* SQL sem servidor com linguagem T-SQL e suporte para ferramentas BI familiares (por exemplo, Power BI Premium, etc.)

> [!NOTE]
> A partir do Azure Synapse Analytics, pode aceder a lojas analíticas e transacionais no seu recipiente Azure Cosmos DB. No entanto, se pretender fazer análises em larga escala ou digitalização dos seus dados operacionais, recomendamos que utilize uma loja analítica para evitar o impacto no desempenho nas cargas de trabalho transacionais.

> [!NOTE]
> Você pode executar análises com baixa latência em uma região de Azure ligando o seu recipiente Azure Cosmos DB ao tempo de execução de Synapse naquela região.

Esta integração permite os seguintes cenários HTAP para diferentes utilizadores:

* Um engenheiro bi que quer modelar e publicar um relatório para aceder aos dados operacionais em Azure Cosmos DB diretamente através da Synapse SQL.

* Um analista de dados que quer obter informações dos dados operacionais num contentor do Azure Cosmos DB consultando-os com synapse SQL, leia os dados em escala e combine esses resultados com outras fontes de dados.

* Um cientista de dados que quer usar a Synapse Spark para encontrar uma característica para melhorar o seu modelo e treinar esse modelo sem fazer engenharia de dados complexa. Também podem escrever os resultados da inferência do post do modelo no Azure Cosmos DB para pontuação em tempo real nos dados através de Spark Synapse.

* Um engenheiro de dados que quer tornar os dados acessíveis aos consumidores, criando tabelas SQL ou Spark sobre contentores Azure Cosmos DB sem processos ETL manuais.

Para obter mais informações sobre o suporte de tempo de execução da Azure Synapse Analytics para o Azure Cosmos DB, consulte [azure Synapse Analytics para suporte a Cosmos DB](../synapse-analytics/synapse-link/concept-synapse-link-cosmos-db-support.md).

## <a name="when-to-use-azure-synapse-link-for-azure-cosmos-db"></a>Quando usar o Azure Synapse Link para Azure Cosmos DB?

Recomenda-se a Ligação Synapse nos seguintes casos:

* Se é cliente da Azure Cosmos DB e pretende executar análises, BI e machine learning sobre os seus dados operacionais. Nestes casos, a Synapse Link proporciona uma experiência de análise mais integrada sem afetar a produção provisionada da sua loja transacional. Por exemplo:

  * Se estiver a executar análises ou BI nos seus dados operacionais Do MB Do MB D DSB diretamente utilizando conectores separados hoje em dia, ou

  * Se estiver a executar processos ETL para extrair dados operacionais num sistema de análise separado.
 
Nestes casos, a Synapse Link proporciona uma experiência de análise mais integrada sem afetar a produção provisionada da sua loja transacional.

A Synapse Link não é recomendada se estiver à procura de requisitos tradicionais de armazém de dados, tais como alta conmoedação, gestão da carga de trabalho e persistência de agregados em várias fontes de dados. Para mais informações, consulte [cenários comuns que podem ser alimentados com Azure Synapse Link for Azure Cosmos DB](synapse-link-use-cases.md).


## <a name="limitations"></a>Limitações

* Durante a pré-visualização pública, o Azure Synapse Link é suportado apenas para a API Azure Cosmos DB SQL (Core). O apoio à API da Azure Cosmos DB para a MongoDB & Cassandra API estão atualmente sob uma pré-visualização fechada. Para solicitar acesso à pré-visualização fechada, envie um e-mail para a [equipa DoM Do MB.](mailto:cosmosdbsynapselink@microsoft.com)

* Atualmente, a loja analítica só pode ser ativada para novos contentores (tanto em contas novas como existentes do Azure Cosmos DB).

* O acesso à loja analítica Azure Cosmos DB com servidor SQL Synapse está atualmente em pré-visualização fechada. Para solicitar acesso, envie um e-mail para a [equipa DoB](mailto:cosmosdbsynapselink@microsoft.com)Do MB.

* O acesso à loja de análise Azure Cosmos DB com synapse SQL aprovisionado não está disponível atualmente.

## <a name="pricing"></a>Preços

O modelo de faturação da Azure Synapse Link inclui os custos incorridos com a utilização da loja analítica Azure Cosmos DB e do tempo de execução da Synapse. Para saber mais, consulte os preços da [loja analítica Azure Cosmos DB](analytical-store-introduction.md#analytical-store-pricing) e os artigos de preços da [Azure Synapse Analytics.](https://azure.microsoft.com/pricing/details/synapse-analytics/)

## <a name="next-steps"></a>Passos seguintes

Para saber mais, consulte os seguintes docs:

* [Visão geral da loja analítica Azure Cosmos DB](analytical-store-introduction.md)

* [Começa com azure synapse Link para Azure Cosmos DB](configure-synapse-link.md)
 
* [O que é suportado no tempo de execução da Azure Synapse Analytics](../synapse-analytics/synapse-link/concept-synapse-link-cosmos-db-support.md)

* [Perguntas frequentes sobre Azure Synapse Link for Azure Cosmos DB](synapse-link-frequently-asked-questions.md)

* [Ligação Azure Synapse para casos de utilização de Azure Cosmos DB](synapse-link-use-cases.md)
