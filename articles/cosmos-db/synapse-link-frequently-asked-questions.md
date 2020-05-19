---
title: Perguntas frequentes sobre Azure Synapse Link for Azure Cosmos DB
description: Obtenha respostas a perguntas frequentes sobre synapse Link for Azure Cosmos DB em áreas como faturação, loja analítica, segurança, tempo para viver em loja analítica.
author: srchi
ms.author: srchi
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: 36be05f72597ae9864eff812862589cafb1f5b0d
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598683"
---
# <a name="frequently-asked-questions-about-azure-synapse-link-for-azure-cosmos-db"></a>Perguntas frequentes sobre Azure Synapse Link for Azure Cosmos DB

Azure Synapse Link for Azure Cosmos DB cria uma integração apertada entre azure Cosmos DB e Azure Synapse Analytics. Permite que os clientes executem análises em tempo real sobre os seus dados operacionais com total isolamento de desempenho das suas cargas de trabalho transacionais e sem um pipeline ETL. Este artigo responde a perguntas comumente feitas sobre synapse Link for Azure Cosmos DB.

## <a name="general-faq"></a>FaQ Geral

### <a name="is-synapse-link-supported-for-all-azure-cosmos-db-apis"></a>A Ligação Synapse é suportada por todas as APIs DB do Azure Cosmos?
No lançamento da pré-visualização pública, o Synapse Link é suportado apenas para a API Azure Cosmos DB SQL (Core). O apoio à API da Cosmos DB para o Mongo DB & Cassandra API está atualmente sob pré-visualização fechada.  Para solicitar acesso à pré-visualização fechada, contacte a [equipa Azure Cosmos DB.](mailto:cosmosdbsynapselink@microsoft.com)

### <a name="is-synapse-link-supported-for-multi-region-azure-cosmos-accounts"></a>A Synapse Link é suportada para contas multi-regiões da Azure Cosmos?
Sim, para as contas multi-regiões da Azure Cosmos, os dados armazenados na loja analítica também estão distribuídos globalmente. Independentemente da região de escrita única (single master) ou de várias regiões de escrita (também conhecidas como multi-mestre), as consultas analíticas realizadas pela Azure Synapse Analytics podem ser servidas a partir da região local mais próxima.

Ao planear configurar uma conta azure cosmos multi-região com suporte analítico da loja, recomenda-se que todas as regiões necessárias acrescentem no momento da criação de contas.

### <a name="can-i-choose-to-enable-synapse-link-for-only-certain-region-and-not-all-regions-in-a-multi-region-account-set-up"></a>Posso optar por permitir a Ligação Synapse apenas para determinadas regiões e não para todas as regiões de uma conta multi-região?
No comunicado de pré-visualização, quando o Synapse Link está habilitado para uma conta multi-região, a loja analítica é criada em todas as regiões. Os dados subjacentes são otimizados para a consistência de produção e transação na loja transacional.

### <a name="can-i-disable-the-synapse-link-feature-for-my-azure-cosmos-account"></a>Posso desativar a funcionalidade Synapse Link para a minha conta Azure Cosmos?
Atualmente, após a capacidade de Ligação Synapse estar ativada ao nível da conta, não pode desativá-la.  Se quiser desligar a capacidade, tem de apagar e recriar uma nova conta Azure Cosmos.

Entenda que **não** terá quaisquer implicações de faturação se a capacidade de Ligação Synapse estiver ativada ao nível da conta, mas não existem recipientes de loja analítica habilitados.

## <a name="azure-cosmos-db-analytical-store"></a>Loja analítica Azure Cosmos DB

### <a name="can-i-enable-analytical-store-on-existing-containers"></a>Posso permitir uma loja analítica em contentores existentes?
Atualmente, a loja analítica só pode ser ativada para novos contentores (tanto em contas novas como existentes).

### <a name="can-i-disable-analytical-store-on-my-azure-cosmos-containers-after-enabling-it-during-container-creation"></a>Posso desativar a loja analítica nos meus contentores Azure Cosmos depois de a permitir durante a criação de contentores?
Atualmente, a loja analítica não pode ser desativada num recipiente Azure Cosmos depois de ativada durante a criação de contentores.

### <a name="is-analytical-store-supported-for-azure-cosmos-containers-with-autoscale-provisioned-throughput"></a>A loja analítica é suportada para contentores Azure Cosmos com entrada autócteda de escala automática?
Sim, a loja analítica pode ser ativada em recipientes com entrada em escala automática.

### <a name="is-there-any-effect-on-azure-cosmos-db-transactional-store-provisioned-rus"></a>Existe algum efeito na loja transacional Azure Cosmos DB aprovisionada em RUs?
A Azure Cosmos DB garante o isolamento de desempenho entre as cargas de trabalho transacionais e analíticas. Permitir a loja analítica num contentor não terá impacto no RU/s provisionado na loja transacional Azure Cosmos DB. As transações (ler & escrever) e os custos de armazenamento da loja analítica serão cobrados separadamente. Consulte os [preços da loja analítica Azure Cosmos DB](analytical-store-introduction.md#analytical-store-pricing) para mais detalhes.

### <a name="are-delete-and-update-operations-on-the-transactional-store-reflected-in-the-analytical-store"></a>As operações de eliminação e atualização na loja de transações estão refletidas na loja analítica? 
Sim, as eliminações e atualizações dos dados da loja de transações serão refletidas na loja analítica. Pode configurar o Tempo de Viver (TTL) no recipiente para incluir dados históricos para que a loja analítica retenha todas as versões de itens que satisfaçam os critérios analíticos da TTL. Consulte a [visão geral da TTL analítica](analytical-store-introduction.md#analytical-ttl) para mais detalhes.

### <a name="can-i-connect-to-analytical-store-from-analytics-engines-other-than-azure-synapse-analytics"></a>Posso ligar-me à loja analítica a partir de motores de análise que não o Azure Synapse Analytics?
Só é possível aceder e executar consultas contra a loja analítica utilizando os vários tempos de execução fornecidos pela Azure Synapse Analytics. A loja analítica pode ser consultada e analisada utilizando:

* Faísca Synapse com suporte total para Scala, Python, SparkSQL e C#. Synapse Spark é central na engenharia de dados e cenários científicos
* SQL sem servidor com linguagem T-SQL e suporte para ferramentas BI familiares (por exemplo, Power BI Premium, etc.)

### <a name="can-i-connect-to-analytical-store-from-synapse-sql-provisioned"></a>Posso ligar-me à loja analítica da Synapse SQL aprovisionada?
Neste momento, a loja analítica não pode ser acedida a partir do Synapse SQL provisionado.

### <a name="can-i-write-back-the-query-aggregation-results-from-synapse-back-to-the-analytical-store"></a>Posso responder aos resultados da agregação da Synapse até à loja analítica?
A loja analítica é uma loja só de leitura num contentor Azure Cosmos. Assim, não pode reescrever diretamente os resultados da agregação à loja analítica, mas pode escrevê-los na loja transacional Azure Cosmos DB de outro contentor, que mais tarde pode ser alavancado como uma camada de serviço.

### <a name="is-the-autosync-replication-from-transactional-store-to-the-analytical-store-asynchronous-or-synchronous-and-what-are-the-latencies"></a>A replicação autosincronizada da loja transacional para a loja analítica é assíncrona ou sincronizada e quais são as latenciências? 
A replicação é assíncrona, e atualmente a latência esperada é de cerca de 2 min.

### <a name="are-there-any-scenarios-where-the-items-from-the-transactional-store-are-not-automatically-propagated-to-the-analytical-store"></a>Existem cenários em que os itens da loja de transações não sejam automaticamente propagados à loja analítica?
Se itens específicos no seu recipiente violarem o [esquema bem definido para análise,](analytical-store-introduction.md#analytical-schema)não serão incluídos na loja analítica. Se tiver cenários bloqueados por esquemas bem definidos para análise, envie um e-mail à [equipa do Azure Cosmos DB](mailto:cosmosdbsynapselink@microsoft.com) para obter ajuda.

### <a name="can-i-partition-the-data-in-analytical-store-differently-from-transactional-store"></a>Posso dividir os dados na loja analítica de forma diferente da loja transacional?
Os dados na loja analítica são divididos com base na divisão horizontal de fragmentos na loja transacional. Atualmente, não é possível escolher uma estratégia de parto diferente para a loja analítica.

### <a name="can-i-customize-or-override-the-way-transactional-data-is-transformed-into-columnar-format-in-the-analytical-store"></a>Posso personalizar ou anular a forma como os dados transacionais são transformados em formato colunaar na loja analítica?
Atualmente não é possível transformar os itens de dados quando são automaticamente propagados da loja transacional para loja analítica. Se tiver cenários bloqueados por esta limitação, envie um e-mail para a equipa DoB Do [MB.](mailto:cosmosdbsynapselink@microsoft.com)

## <a name="analytical-time-to-live-ttl"></a>Tempo Analítico para viver (TTL)

### <a name="is-ttl-for-analytical-data-supported-at-both-container-and-item-level"></a>A TTL é suportada tanto no nível do contentor como do item?
Neste momento, o TTl para dados analíticos só pode ser configurado ao nível do recipiente e não existe suporte para definir TTL analítico ao nível do ponto.

### <a name="after-setting-the-container-level--analytical-ttl-on-an-azure-cosmos-db-container-can-i-change-to-a-different-value-later"></a>Depois de colocar o tTL analítico do nível do recipiente num recipiente Azure Cosmos DB, posso mudar para um valor diferente mais tarde?
Sim, tTL analítico pode ser atualizado para qualquer valor válido. Consulte o artigo [da TTL analítica](analytical-store-introduction.md#analytical-ttl) para obter mais detalhes sobre tTL analítico.

### <a name="can-i-update-or-delete-an-item-from-the-analytical-store-after-it-has-been-ttld-out-from-the-transactional-store"></a>Posso atualizar ou apagar um item da loja analítica depois de ter sido tTL'd fora da loja transacional?
Todas as atualizações e exclusões transacionais são copiadas para a loja analítica, mas se o artigo tiver sido purgado da loja transacional, então não pode ser atualizado na loja analítica. Para saber mais, consulte o artigo [da TTL analítica.](analytical-store-introduction.md#analytical-ttl)

## <a name="billing"></a>Faturação

### <a name="what-is-the-billing-model-of-synapse-link-for-azure-cosmos-db"></a>Qual é o modelo de faturação da Ligação Synapse para o Azure Cosmos DB?
A [loja analítica Azure Cosmos DB](analytical-store-introduction.md) está disponível em pré-visualização pública sem quaisquer encargos para a loja analítica até 30 de agosto de 2020. Synapse Spark e Synapse SQL são cobrados através do consumo de [serviço Synapse](https://azure.microsoft.com/pricing/details/synapse-analytics/).

## <a name="security"></a>Segurança

### <a name="what-are-the-ways-to-authenticate-with-the-analytical-store"></a>Quais são as formas de autenticar com a loja analítica?
A autenticação com a loja analítica é a mesma que uma loja de transações. Para uma determinada base de dados, pode autenticar com o mestre ou achave de leitura. Você pode aproveitar o serviço ligado no Estúdio Synapse para evitar a colagem das chaves Azure Cosmos DB nos cadernos Spark. O acesso a este Serviço Linked está disponível para todos os que tenham acesso ao espaço de trabalho.

## <a name="synapse-run-times"></a>Tempos de corrida sinapse

### <a name="what-are-the-currently-supported-synapse-run-times-to-access-azure-cosmos-db-analytical-store"></a>Quais são os tempos de execução da Synapse atualmente suportados para aceder à loja analítica Azure Cosmos DB?

|Tempo de execução sinapse |Suporte atual |
|---------|---------|
|Piscinas de faíscas synapse | Ler, Escrever (através de loja transacional), Tabela, Vista Temporária |
|Synapse SQL Serverless    | Ler, Ver (Pré-visualização fechada)  |
|Synapse SQL Provisioned   |  Não disponível |

### <a name="do-my-synapse-spark-tables-sync-with-my-synapse-sql-serverless-tables-the-same-way-they-do-with-azure-data-lake"></a>As minhas mesas Synapse Spark sincronizam-se com as minhas mesas Synapse SQL Serverless da mesma forma que fazem com o Lago de Dados Azure?
Atualmente, esta funcionalidade não está disponível.

### <a name="can-i-do-spark-structured-streaming-from-analytical-store"></a>Posso fazer o spark estruturado streaming de uma loja analítica?
Atualmente, o suporte de streaming estruturado spark para O Azure Cosmos DB é implementado usando a funcionalidade de feed de mudança da loja transacional e ainda não é suportado a partir de loja analítica.

## <a name="synapse-studio"></a>Synapse Studio

### <a name="in-the-synapse-studio-how-do-i-recognize-if-im-connected-to-an-azure-cosmos-db-container-with-the-analytics-store-enabled"></a>No Estúdio Synapse, como reconheço se estou ligado a um contentor Azure Cosmos DB com a loja de análise ativada?
Um recipiente Azure Cosmos DB ativado com loja analítica tem o seguinte ícone:

![Recipiente Azure Cosmos DB ativado com loja analítica ícone](./media/synapse-link-frequently-asked-questions/analytical-store-icon.png)

Um recipiente de loja transacional será representado com o seguinte ícone:

![Recipiente Azure Cosmos DB ativado com loja transacional ícone](./media/synapse-link-frequently-asked-questions/transactional-store-icon.png)
 
### <a name="how-do-you-pass-azure-cosmos-db-credentials-from-synapse-studio"></a>Como se aprova as credenciais da Azure Cosmos DB do Estúdio Synapse?
Atualmente, as credenciais De DB da Azure Cosmos são passadas ao mesmo tempo que criam o serviço ligado pelo utilizador que tem acesso às bases de dados Do BD Do Azure Cosmos. O acesso a essa loja está disponível para outros utilizadores que tenham acesso ao espaço de trabalho.

## <a name="next-steps"></a>Passos seguintes

* Conheça os [benefícios da Ligação Synapse](synapse-link.md#synapse-link-benefits)

* Conheça a [integração entre a Ligação Synapse e a Azure Cosmos DB.](synapse-link.md#synapse-link-integration)
