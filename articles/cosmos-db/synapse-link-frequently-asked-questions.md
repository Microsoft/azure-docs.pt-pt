---
title: Perguntas mais frequentes sobre o Azure Synapse Link para o Azure Cosmos DB
description: Obtenha respostas para perguntas frequentes sobre Synapse Link para Azure Cosmos DB em áreas como faturação, loja analítica, segurança, tempo para viver em loja analítica.
author: Rodrigossz
ms.author: rosouz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/09/2020
ms.openlocfilehash: 9eb1f9162f0546e08f59391af1042becad25cf3b
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91803998"
---
# <a name="frequently-asked-questions-about-azure-synapse-link-for-azure-cosmos-db"></a>Perguntas mais frequentes sobre o Azure Synapse Link para o Azure Cosmos DB

Azure Synapse Link for Azure Cosmos DB cria uma integração apertada entre Azure Cosmos DB e Azure Synapse Analytics. Permite que os clientes corram quase análises em tempo real sobre os seus dados operacionais com isolamento total do desempenho das suas cargas de trabalho transacionais e sem um gasoduto ETL. Este artigo responde a perguntas mais frequentes sobre o Synapse Link para o Azure Cosmos DB.

## <a name="general-faq"></a>FAQs Gerais

### <a name="is-azure-synapse-link-supported-for-all-azure-cosmos-db-apis"></a>A Azure Synapse Link é suportado para todas as APIs Azure Cosmos?

No comunicado de pré-visualização público, a Azure Synapse Link é suportada pela API AZURE Cosmos DB SQL (Core) e para a AZure Cosmos DB API para a MongoDB. 

### <a name="is-azure-synapse-link-supported-for-multi-region-azure-cosmos-db-accounts"></a>A Azure Synapse Link é suportada para contas DB Azure Cosmos multi-regiões?

Sim, para as contas Azure Cosmos de várias regiões, os dados armazenados na loja analítica também são distribuídos globalmente. Independentemente de ser uma única região de escrita ou várias regiões de escrita, as consultas analíticas executadas no Azure Synapse Analytics podem ser servidas a partir da região local mais próxima.

Ao planear configurar uma conta DB Azure Cosmos multi-região com suporte analítico à loja, recomenda-se que todas as regiões necessárias tenham sido adicionadas no momento da criação de conta.

### <a name="can-i-choose-to-enable-azure-synapse-link-for-only-certain-region-and-not-all-regions-in-a-multi-region-account-set-up"></a>Posso optar por permitir o Azure Synapse Link apenas para determinadas regiões e não para todas as regiões numa configuração de contas multi-região?

No lançamento de pré-visualização, quando o Azure Synapse Link está ativado para uma conta multi-região, a loja analítica é criada em todas as regiões. Os dados subjacentes são otimizados para a produção e consistência transacional na loja transacional.

### <a name="is-backup-and-restore-supported-for-azure-synapse-link-enabled-accounts"></a>A cópia de segurança e a restauração são suportadas para contas ativadas pelo Azure Synapse Link?

Na pré-visualização, para o Azure Synapse Link as contas de base de dados ativadas, a cópia de segurança e a restauração de contentores não são suportadas. Se tiver cargas de trabalho de produção que exijam cópias de segurança e restaurar a funcionalidade, recomendamos que não ative o Synapse Link nessas contas de base de dados. 

### <a name="can-i-disable-the-azure-synapse-link-feature-for-my-azure-cosmos-db-account"></a>Posso desativar a funcionalidade Azure Synapse Link para a minha conta DB Azure Cosmos?

Atualmente, após a capacidade do Synapse Link ser ativada ao nível de conta, não poderá desativá-la. Compreenda que não terá quaisquer implicações de faturação se a capacidade do Synapse Link estiver ativada ao nível da conta e não existirem contentores ativados para o arquivo analítico. 

Se precisar de desligar a capacidade, tem 2 opções. A primeira é eliminar e recriar uma nova conta DB da Azure Cosmos, migrando os dados se necessário. A segunda opção é abrir um bilhete de apoio, para obter ajuda numa migração de dados para outra conta.

## <a name="azure-cosmos-db-analytical-store"></a>Loja analítica Azure Cosmos DB

### <a name="can-i-enable-analytical-store-on-existing-containers"></a>Posso permitir a loja analítica em recipientes existentes?

Atualmente, a loja analítica só pode ser ativada para novos contentores (tanto em contas novas como existentes).

### <a name="can-i-disable-analytical-store-on-my-azure-cosmos-db-containers-after-enabling-it-during-container-creation"></a>Posso desativar a loja analítica nos meus contentores Azure Cosmos DB depois de o permitir durante a criação de contentores?

Atualmente, o arquivo analítico não pode ser desativado num contentor do Azure Cosmos DB após ser ativado durante a criação do contentor.

### <a name="is-analytical-store-supported-for-azure-cosmos-db-containers-with-autoscale-provisioned-throughput"></a>A loja analítica é suportada para contentores DB Azure Cosmos com produção de autoescala a provisionada?

Sim, a loja analítica pode ser ativada em recipientes com produção de autoescala.

### <a name="is-there-any-effect-on-azure-cosmos-db-transactional-store-provisioned-rus"></a>Existe algum efeito na loja de transações Azure Cosmos DB aprovisionada RUs?

A Azure Cosmos DB garante o isolamento do desempenho entre as cargas de trabalho transacionais e analíticas. Permitir a loja analítica num recipiente não terá impacto no RU/s aprovisionado na loja transacional Azure Cosmos DB. As transações (leia-se & escrever) e os custos de armazenamento da loja analítica serão cobrados separadamente. Consulte o [preço da loja analítica Azure Cosmos DB](analytical-store-introduction.md#analytical-store-pricing) para obter mais detalhes.

### <a name="are-delete-and-update-operations-on-the-transactional-store-reflected-in-the-analytical-store"></a>As operações de exclusão e atualização da loja transacional são refletidas na loja analítica?

Sim, as eliminações e atualizações dos dados na loja transacional serão refletidas na loja analítica. Pode configurar a Hora de Viver (TTL) no recipiente para incluir dados históricos para que a loja analítica retenha todas as versões de itens que satisfaçam os critérios de TTL analíticos. Consulte a [visão geral da TTL analítica](analytical-store-introduction.md#analytical-ttl) para mais detalhes.

### <a name="can-i-connect-to-analytical-store-from-analytics-engines-other-than-azure-synapse-analytics"></a>Posso ligar-me à loja analítica a partir de motores de análise que não o Azure Synapse Analytics?

Só pode aceder e executar consultas no arquivo analítico com os vários tempos de execução disponibilizados pelo Azure Synapse Analytics. Pode consultar e analisar o arquivo analítico com:

* Sinapse Spark com apoio total para Scala, Python, SparkSQL e C#. O Synapse Spark é fundamental para os cenários de ciência e engenharia de dados
* O SQL sem servidor com linguagem T-SQL e suporte para ferramentas BI familiares (por exemplo, Power BI Premium, etc.)

### <a name="can-i-connect-to-analytical-store-from-synapse-sql-provisioned"></a>Posso ligar-me à loja analítica da Synapse SQL a provisionada?

Neste momento, a loja analítica não pode ser acedida a partir do Sinaapse SQL a provisionado.

### <a name="can-i-write-back-the-query-aggregation-results-from-synapse-back-to-the-analytical-store"></a>Posso responder aos resultados da agregação de consultas da Synapse para a loja analítica?

A loja analítica é uma loja só de leitura num contentor Azure Cosmos DB. Assim, não pode escrever diretamente os resultados da agregação para a loja analítica, mas pode escrevê-los na loja transacional Azure Cosmos DB de outro recipiente, que pode ser posteriormente alavancado como uma camada de serviço.

### <a name="is-the-autosync-replication-from-transactional-store-to-the-analytical-store-asynchronous-or-synchronous-and-what-are-the-latencies"></a>A replicação autossícola da loja transacional para a loja analítica assíncronus ou sincronizadas e quais são as latências?

A latência de sincronização automática é geralmente dentro de 2 minutos. Em caso de base de dados de produção partilhada com um grande número de contentores, a latência auto-sincronizada de contentores individuais pode ser maior e demorar até 5 minutos. Gostaríamos de saber mais como esta latência se encaixa nos seus cenários. Para isso, por favor contacte a equipa DB da [Azure Cosmos.](mailto:cosmosdbsynapselink@microsoft.com)

### <a name="are-there-any-scenarios-where-the-items-from-the-transactional-store-are-not-automatically-propagated-to-the-analytical-store"></a>Existem cenários em que os itens da loja transacional não sejam automaticamente propagados à loja analítica?

Se itens específicos no seu recipiente violarem o [esquema bem definido para análise,](analytical-store-introduction.md#analytical-schema)não serão incluídos na loja analítica. Se tiver cenários bloqueados por esquemas bem definidos para análise, envie um e-mail à equipa DB do [Azure Cosmos](mailto:cosmosdbsynapselink@microsoft.com) para pedir ajuda.

### <a name="can-i-partition-the-data-in-analytical-store-differently-from-transactional-store"></a>Posso dividir os dados na loja analítica de forma diferente da loja transacional?

Os dados no arquivo analítico são particionados com base na criação de partições horizontais no arquivo transacional. Atualmente, não pode escolher uma estratégia de criação de partições diferente para o arquivo analítico.

### <a name="can-i-customize-or-override-the-way-transactional-data-is-transformed-into-columnar-format-in-the-analytical-store"></a>Posso personalizar ou sobrepor a forma como os dados transacionais são transformados em formato colunar na loja analítica?

Atualmente não é possível transformar os itens de dados quando são automaticamente propagados da loja transacional para a loja analítica. Se tiver cenários bloqueados por esta limitação, envie um e-mail à equipa DB da [Azure Cosmos](mailto:cosmosdbsynapselink@microsoft.com).

## <a name="analytical-time-to-live-ttl"></a>Tempo analítico para viver (TTL)

### <a name="is-ttl-for-analytical-data-supported-at-both-container-and-item-level"></a>O TTL para dados analíticos é suportado tanto ao nível do recipiente como do item?

Neste momento, o TTl para dados analíticos só pode ser configurado ao nível do recipiente e não há suporte para definir TTL analítico ao nível do item.

### <a name="after-setting-the-container-level--analytical-ttl-on-an-azure-cosmos-db-container-can-i-change-to-a-different-value-later"></a>Depois de definir o nível de TTL analítico do recipiente num recipiente DB Azure Cosmos, posso mudar para um valor diferente mais tarde?

Sim, o TTL analítico pode ser atualizado para qualquer valor válido. Consulte o artigo [da TTL analítica](analytical-store-introduction.md#analytical-ttl) para obter mais detalhes sobre a TTL analítica.

### <a name="can-i-update-or-delete-an-item-from-the-analytical-store-after-it-has-been-ttld-out-from-the-transactional-store"></a>Posso atualizar ou apagar um artigo da loja de análise depois de ter sido TTL'd da loja transacional?

Todas as atualizações e eliminações transacionais são copiadas para a loja analítica, mas se o item tiver sido purgado da loja transacional, então não pode ser atualizado na loja analítica. Para saber mais, consulte o artigo [da TTL Analítica.](analytical-store-introduction.md#analytical-ttl)

## <a name="billing"></a>Faturação

### <a name="what-is-the-billing-model-of-azure-synapse-link-for-azure-cosmos-db"></a>Qual é o modelo de faturação do Azure Synapse Link para Azure Cosmos DB?

[A loja analítica Azure Cosmos DB](analytical-store-introduction.md) está disponível em pré-visualização pública sem qualquer custo para a loja analítica até 30 de agosto de 2020. A Synapse Spark e a Synapse SQL são faturadas através do consumo de [serviços Synapse](https://azure.microsoft.com/pricing/details/synapse-analytics/).

## <a name="security"></a>Segurança

### <a name="what-are-the-ways-to-authenticate-with-the-analytical-store"></a>Quais são as formas de autenticar com a loja de analítica?

A autenticação com a loja analítica é a mesma que uma loja transacional. Para uma determinada base de dados, pode autenticar-se com a chave principal ou apenas de leitura. Você pode aproveitar o serviço ligado no Azure Synapse Studio para evitar colar as chaves DB do Azure Cosmos nos cadernos Spark. O acesso a este Serviço Linked está disponível para todos os que tenham acesso ao espaço de trabalho.

## <a name="synapse-run-times"></a>Resumos

### <a name="what-are-the-currently-supported-synapse-run-times-to-access-azure-cosmos-db-analytical-store"></a>Quais são os tempos de sinapse atualmente suportados para aceder à loja analítica Azure Cosmos DB?

|Tempo de execução de Azure Synapse |Suporte atual |
|---------|---------|
|Piscinas Azure Synapse Spark | Ler, Escrever (através de loja transacional), Tabela, Vista Temporária |
|Piscinas sem servidor Azure Synapse    | Ler, Ver |
|Azure Synapse SQL Provisioned   |  Não disponível |

### <a name="do-my-azure-synapse-spark-tables-sync-with-my-azure-synapse-sql-serverless-tables-the-same-way-they-do-with-azure-data-lake"></a>As minhas tabelas Azure Synapse Spark sincronizam-se com as minhas tabelas sem servidor Azure Synapse SQL da mesma forma que fazem com o Azure Data Lake?

Atualmente, esta funcionalidade não se encontra disponível.

### <a name="can-i-do-spark-structured-streaming-from-analytical-store"></a>Posso fazer o streaming estruturado spark da loja de analítica?

Atualmente, o suporte de streaming estruturado pela Spark para a Azure Cosmos DB é implementado utilizando a funcionalidade de feed de mudança da loja transacional e ainda não é suportado a partir da loja analítica.

## <a name="azure-synapse-studio"></a>Estúdio Azure Synapse

### <a name="in-the-azure-synapse-studio-how-do-i-recognize-if-im-connected-to-an-azure-cosmos-db-container-with-the-analytics-store-enabled"></a>No Azure Synapse Studio, como reconheço se estou ligado a um contentor Azure Cosmos DB com a loja de análise ativada?

Um recipiente DB Azure Cosmos ativado com loja analítica tem o seguinte ícone:

:::image type="content" source="./media/synapse-link-frequently-asked-questions/analytical-store-icon.png" alt-text="Recipiente DB Azure Cosmos habilitado com ícone de loja analítica":::

Um recipiente de loja transacional será representado com o seguinte ícone:

:::image type="content" source="./media/synapse-link-frequently-asked-questions/transactional-store-icon.png" alt-text="Recipiente DB Azure Cosmos habilitado com ícone de loja analítica":::
 
### <a name="how-do-you-pass-azure-cosmos-db-credentials-from-azure-synapse-studio"></a>Como é que se passam as credenciais de DB do Azure Cosmos do Azure Synapse Studio?

Atualmente, as credenciais DB da Azure Cosmos são passadas enquanto criam o serviço ligado pelo utilizador que tem acesso às bases de dados DB do Azure Cosmos. O acesso a essa loja está disponível para outros utilizadores que tenham acesso ao espaço de trabalho.

## <a name="next-steps"></a>Passos seguintes

* Conheça os [benefícios do Azure Synapse Link](synapse-link.md#synapse-link-benefits)

* Conheça a [integração entre a Azure Synapse Link e a Azure Cosmos DB](synapse-link.md#synapse-link-integration).
