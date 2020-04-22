---
title: Frequentemente fez perguntas sobre API DB Azure Cosmos para Cassandra.
description: Obtenha respostas para perguntas frequentes sobre API DB Azure Cosmos para Cassandra.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: thvankra
ms.openlocfilehash: 416f0c5f995a101298e84c81317c7d39fb5d43f8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727388"
---
# <a name="frequently-asked-questions-about-the-azure-cosmos-db-api-for-cassandra"></a>Perguntas frequentes sobre a API do Azure Cosmos DB para Cassandra

## <a name="what-are-some-key-differences-between-apache-cassandra-and-cassandra-api"></a>Quais são algumas diferenças fundamentais entre Apache Cassandra e Cassandra API?

- Apache Cassandra recomenda um limite de 100MB sobre o tamanho de uma chave de partição. Cassandra API permite até 10GB por partição.
- Apache Cassandra permite-lhe desativar compromissos duráveis - ou seja, saltar a escrita para o registo de compromisso simesmo e ir diretamente para o Memtable(s). Isto pode levar à perda de dados se o nó descer antes de memtables ser lavado para SStables no disco. Cosmos DB sempre comete compromissos duráveis para que nunca tenha perda de dados.
- Apache Cassandra pode ver um desempenho diminuído se a carga de trabalho envolver muitas substituições e/ou exclusões. A razão para isso são lápides que a carga de trabalho de leitura precisa de saltar para buscar os dados mais recentes. Cassandra API não verá um desempenho de leitura reduzido quando a carga de trabalho tiver muitas substituições e/ou exclusões.
- Durante cenários de carga de trabalho de alta substituição, a compactação precisa de ser executada para fundir SSTable no disco (a fusão é necessária porque as escritas de Apache Cassandra são apenas anexadas, pelo que várias atualizações são armazenadas como entradas individuais do SSTable que precisam de ser periodicamente fundidas). Isto também pode levar a uma redução do desempenho da leitura durante a compactação. Isto não ocorre na API de Cassandra, uma vez que não implementa a compactação.
- Definir um fator de replicação de 1 é possível com Apache Cassandra. No entanto, leva a uma baixa disponibilidade se o único nó com os dados descer. Isto não é um problema com a API da Azure Cosmos DB Cassandra porque há sempre um fator de replicação de 4 (quórum de 3).
- Adicionar/remover nós em Apache Cassandra requer muita intervenção manual, mas também alta CPU no novo nó enquanto os nós existentes movem algumas das suas gamas simbólicas para o novo nó. Isto é o mesmo quando se desmantela um nó existente. No entanto, a escala é feita sem problemas sob o capot da Azure Cosmos DB Cassandra API, sem quaisquer problemas observados no serviço/aplicação.
- Não há necessidade de colocar num_tokens em cada nó no aglomerado como em Apache Cassandra. Os nós e as gamas de token são totalmente geridos pela Cosmos DB.
- A API da Azure Cosmos DB Cassandra é totalmente gerida para que não seja necessário o comando de nodetool, tais como reparação, desativação, etc. que são usados em Apache Cassandra.

## <a name="other-frequently-asked-questions"></a>Outras perguntas frequentes

### <a name="what-is-the-protocol-version-supported-by-azure-cosmos-db-cassandra-api-is-there-a-plan-to-support-other-protocols"></a>Qual é a versão protocolar suportada pela Azure Cosmos DB Cassandra API? Existe um plano para apoiar outros protocolos?

Azure Cosmos DB Cassandra API suporta a versão CQL 3.x. A compatibilidade do CQL baseia-se no [repositório público apache Cassandra GitHub.](https://github.com/apache/cassandra/blob/trunk/doc/cql3/CQL.textile) Se tiver feedback sobre o apoio a outros protocolos, avise-nos através do feedback de [voz do utilizador](https://feedback.azure.com/forums/263030-azure-cosmos-db) ou envie um e-mail para [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com).

### <a name="why-is-choosing-a-throughput-for-a-table-a-requirement"></a>Por que escolher um prato para uma tabela é um requisito?

O Azure Cosmos DB define a entrada padrão para o seu recipiente com base no local onde cria a tabela a partir de - portal ou CQL.
A Azure Cosmos DB oferece garantias de desempenho e latência, com limites superiores em funcionamento. Esta garantia é possível quando o motor pode impor a governação das operações do arrendatário. A definição de entrada garante que obtém a entrada garantida e a latência, porque a plataforma reserva esta capacidade e garante o sucesso da operação.
Pode [alterar elásticamente](manage-scale-cassandra.md) a sua entrada para beneficiar da sazonalidade da sua aplicação e economizar custos.

O conceito de entrada é explicado no artigo [Das Unidades de Pedido em Azure Cosmos DB.](request-units.md) O resultado para uma mesa é distribuído pelas divisórias físicas subjacentes igualmente.

### <a name="what-is-the-default-rus-of-table-when-created-through-cql-what-if-i-need-to-change-it"></a>Qual é o padrão RU/s da tabela quando criado através do CQL? E se eu precisar mudá-lo?

A Azure Cosmos DB utiliza unidades de pedido por segundo (RU/s) como moeda para fornecer a sua entrada. As tabelas criadas através do CQL têm 400 RU. Pode mudar o RU do portal.

CQL

```shell
CREATE TABLE keyspaceName.tablename (user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=1200
```

.NET

```csharp
int provisionedThroughput = 400;
var simpleStatement = new SimpleStatement($"CREATE TABLE {keyspaceName}.{tableName} (user_id int PRIMARY KEY, lastname text)");
var outgoingPayload = new Dictionary<string, byte[]>();
outgoingPayload["cosmosdb_provisioned_throughput"] = Encoding.UTF8.GetBytes(provisionedThroughput.ToString());
simpleStatement.SetOutgoingPayload(outgoingPayload);
```

### <a name="what-happens-when-throughput-is-used-up"></a>O que acontece quando a entrada é usada?

A Azure Cosmos DB oferece garantias de desempenho e latência, com limites superiores em funcionamento. Esta garantia é possível quando o motor pode impor a governação das operações do arrendatário. Isto é possível com base na definição da produção, que garante que obtém a entrada garantida e a latência, porque a plataforma reserva esta capacidade e garante o sucesso da operação.
Quando ultrapassas esta capacidade, recebes uma mensagem de erro sobrecarregada indicando que a tua capacidade estava esoutra.
0x1001 Sobrecarregado: o pedido não pode ser processado porque "A Taxa de Pedido é grande". Neste momento, é essencial ver que operações e o seu volume causam esta questão. Você pode ter uma ideia sobre a capacidade consumida passando sobre a capacidade aprovisionada com métricas no portal. Então você precisa garantir que a capacidade é consumida quase igualmente em todas as divisórias subjacentes. Se vir que a maior parte da entrada é consumida por uma divisória, tem uma carga de trabalho distorcida.

Estão disponíveis métricas que mostram como a produção é usada ao longo de horas, dias e por sete dias, através de divisórias ou em conjunto. Para mais informações, consulte [Monitorização e depuração com métricas em Azure Cosmos DB](use-metrics.md).

Os registos de diagnóstico são explicados no artigo de registo de diagnóstico do [Azure Cosmos DB.](logging.md)

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>Será que o mapa principal do conceito chave de partição do Azure Cosmos DB?

Sim, a chave de partição é usada para colocar a entidade no local certo. Em Azure Cosmos DB, é usado para encontrar partição lógica certa que é armazenada numa divisória física. O conceito de partição é bem explicado na partilha e escala no artigo [da Azure Cosmos DB.](partition-data.md) O essencial aqui é que uma partição lógica não deve ultrapassar o limite de 10 GB hoje.

### <a name="what-happens-when-i-get-a-quota-full-notification-indicating-that-a-partition-is-full"></a>O que acontece quando recebo uma notificação cheia de quotas indicando que uma partição está cheia?

O Azure Cosmos DB é um sistema baseado em SLA que fornece uma escala ilimitada, com garantias de latência, entrada, disponibilidade e consistência. Este armazenamento ilimitado baseia-se na escala horizontal a partir de dados utilizando a partilha como conceito-chave. O conceito de partição é bem explicado na partilha e escala no artigo [da Azure Cosmos DB.](partition-data.md)

O limite de 10 GB sobre o número de entidades ou itens por partição lógica a que deve aderir. Para garantir que a sua aplicação se estoire bem, recomendamos que *não* crie uma divisória quente armazenando toda a informação numa única divisória e consultando-a. Este erro só pode vir se os seus dados estiverem distorcidos: isto é,&nbsp;tem muitos dados para uma chave de partição (mais de 10 GB). Pode encontrar a distribuição de dados através do portal de armazenamento. A forma de corrigir este erro é recriar a tabela e escolher uma chave primária granular (chave de partição), que permite uma melhor distribuição de dados.

### <a name="is-it-possible-to-use-cassandra-api-as-key-value-store-with-millions-or-billions-of-individual-partition-keys"></a>É possível usar a Cassandra API como loja de valor chave com milhões ou biliões de chaves individuais de partição?

A Azure Cosmos DB pode armazenar dados ilimitados através da escala do armazenamento. Isto é independente da entrada. Sim, pode sempre utilizar a API Cassandra para armazenar e recuperar a chave/valores, especificando a chave primária/partição certa. Estas chaves individuais obtêm a sua própria partição lógica e sentam-se no topo da partição física sem problemas.

### <a name="is-it-possible-to-create-more-than-one-table-with-apache-cassandra-api-of-azure-cosmos-db"></a>É possível criar mais do que uma mesa com API Apache Cassandra da Azure Cosmos DB?

Sim, é possível criar mais do que uma mesa com a Apache Cassandra API. Cada uma dessas tabelas é tratada como unidade para a entrada e armazenamento.

### <a name="is-it-possible-to-create-more-than-one-table-in-succession"></a>É possível criar mais do que uma mesa sucessivamente?

A Azure Cosmos DB é um sistema repartido por recursos tanto para dados como para controlar as atividades dos aviões. Contentores como coleções, mesas são entidades de tempo de execução que são aprovisionadas para a capacidade de entrada dada. A criação destes contentores em rápida sucessão não é esperada atividade e acelerada. Se tiver testes que caiam/criem tabelas imediatamente, tente espaça-las.

### <a name="what-is-maximum-number-of-tables-that-can-be-created"></a>O que é o número máximo de tabelas que podem ser criadas?

Não há limite físico no número de tabelas, envie um e-mail para [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) se tiver um grande número de tabelas (onde o tamanho total constante ultrapassa 10 TB de dados) que precisam de ser criados a partir dos habituais 10 ou 100s.

### <a name="what-is-the-maximum--of-keyspace-that-we-can-create"></a>Qual é o máximo # do espaço-chave que podemos criar?

Não há limite físico para o número de espaços-chave, uma vez [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) que são recipientes de metadados, envie um e-mail para se tiver um grande número de espaços-chave por alguma razão.

### <a name="is-it-possible-to-bring-in-lot-of-data-after-starting-from-normal-table"></a>É possível trazer muitos dados depois de começar da tabela normal?

Sim, assumindo divisórias distribuídas uniformemente, a capacidade de armazenamento é gerida automaticamente e aumenta à medida que empurra mais dados. Assim, pode importar com confiança os dados necessários sem gerir e fornecer nós, e muito mais. No entanto, se está a antecipar um grande crescimento imediato de dados, faz mais sentido prever diretamente [a entrada prevista](set-throughput.md) em vez de começar a descer e a aumtá-la imediatamente.

### <a name="is-it-possible-to-supply-yaml-file-settings-to-configure-apache-casssandra-api-of-azure-cosmos-db-behavior"></a>É possível fornecer configurações de ficheiros yaml para configurar Apache Casssandra API do comportamento do Azure Cosmos DB?

Apache Cassandra API da Azure Cosmos DB é um serviço de plataforma. Proporciona compatibilidade de nível protocolar para executar operações. Esconde a complexidade da gestão, monitorização e configuração. Como desenvolvedor/utilizador, não precisa de se preocupar com disponibilidade, lápides, cache chave, cache de linha, filtro de flor e infinidade de outras definições. A API Apache Cassandra da Azure Cosmos DB foca-se em fornecer o desempenho de leitura e escrita que você precisa sem a sobrecarga de configuração e gestão.

### <a name="will-apache-cassandra-api-for-azure-cosmos-db-support-node-additioncluster-statusnode-status-commands"></a>Será que a Apache Cassandra API para o Azure Cosmos DB suporta a adição de nó/cluster/comandos de estado de cluster/ estado do nó?

Apache Cassandra API é um serviço de plataforma que faz do planeamento da capacidade, respondendo às exigências de elasticidade para a entrada & armazenamento uma brisa. Com a Azure Cosmos DB, você fornecer a sua entrada, você precisa. Depois pode escaloná-lo para cima e para baixo várias vezes ao longo do dia sem se preocupar em adicionar/apagar nós ou geri-los. Isto implica que você não precisa usar o nó, ferramenta de gestão de cluster sintetiza também.

### <a name="what-happens-with-respect-to-various-config-settings-for-keyspace-creation-like-simplenetwork"></a>O que acontece em relação a várias configurações de config para criação de espaço chave como simples/rede?

A Azure Cosmos DB fornece distribuição global da caixa por razões de disponibilidade e baixa latência. Não precisas de arranjar réplicas ou outras coisas. Todos os escritos são sempre duramente quórum cometidos em qualquer região onde você escreve enquanto fornece garantias de desempenho.

### <a name="what-happens-with-respect-to-various-settings-for-table-metadata-like-bloom-filter-caching-read-repair-change-gc_grace-compression-memtable_flush_period-and-more"></a>O que acontece em relação a várias definições para metadados de mesa como filtro de flores, cache, alteração de reparação, gc_grace, memtable_flush_period de compressão, e muito mais?

O Azure Cosmos DB proporciona desempenho para leituras/escritas e entradas sem necessidade de tocar em nenhuma das configurações de configuração e manipuladora-as acidentalmente.

### <a name="is-time-to-live-ttl-supported-for-cassandra-tables"></a>O tempo de viver (TTL) é apoiado nas mesas de Cassandra?

Sim, a TTL é apoiada.

### <a name="is-it-possible-to-monitor-node-status-replica-status-gc-and-os-parameters-earlier-with-various-tools-what-needs-to-be-monitored-now"></a>É possível monitorizar o estado do nó, o estado da réplica, a GC e os parâmetros do OS mais cedo com várias ferramentas? O que precisa de ser monitorizado agora?

O Azure Cosmos DB é um serviço de plataforma que o ajuda a aumentar a produtividade e não se preocupar em gerir e monitorizar infraestruturas. Basta cuidar da entrada disponível nas métricas do portal para descobrir se está a ser estrangulado e aumentar ou diminuir essa entrada.
Monitor [SLAs](monitor-accounts.md).
Utilizar [métricas](use-metrics.md) Utilize [registos de diagnóstico](logging.md).

### <a name="which-client-sdks-can-work-with-apache-cassandra-api-of-azure-cosmos-db"></a>Que cliente sDKs pode trabalhar com Apache Cassandra API da Azure Cosmos DB?

Os condutores clientes da Apache Cassandra SDK que utilizam o CQLv3 foram usados para programas de clientes. Se tiver outros condutores que utiliza ou se estiver a [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com)enfrentar problemas, envie correio para .

### <a name="is-composite-partition-key-supported"></a>A chave de partição composta é suportada?

Sim, você pode usar sintaxe regular para criar chave de partição composta.

### <a name="can-i-use-sstableloader-for-data-loading"></a>Posso usar o sstableloader para o carregamento de dados?

Não, o sstableloader não é apoiado.

### <a name="can-an-on-premises-apache-cassandra-cluster-be-paired-with-azure-cosmos-dbs-cassandra-api"></a>Um aglomerado apache Cassandra pode ser emparelhado com a Cassandra API de Azure Cosmos DB?

Atualmente, a Azure Cosmos DB tem uma experiência otimizada para o ambiente em nuvem sem sobrecarga de operações. Se necessitar de emparelhamento, envie correio para [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) com uma descrição do seu cenário. Estamos a trabalhar na oferta para ajudar a emparelhar o aglomerado de Cassandra nas instalações/nuvem diferente com a Cassandra API de Cosomos DB.

### <a name="does-cassandra-api-provide-full-backups"></a>A Cassandra API fornece reforços completos?

A Azure Cosmos DB fornece duas cópias de segurança gratuitas tomadas hoje em intervalo de quatro horas em todas as APIs. Isto garante que não precisa de configurar um horário de reserva e outras coisas.
Se quiser modificar a retenção e a [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) frequência, envie um e-mail para ou levante um caso de suporte. A informação sobre a capacidade de backup é fornecida na cópia de segurança online Automática e restaurar com o artigo [da Azure Cosmos DB.](../synapse-analytics/sql-data-warehouse/backup-and-restore.md)

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>Como é que a conta da Cassandra API falha se uma região cai?

O Azure Cosmos DB Cassandra API empresta-se da plataforma distribuída globalmente da Azure Cosmos DB. Para garantir que a sua aplicação pode tolerar o tempo de inatividade do datacenter, permita pelo menos mais uma região para a conta no portal Azure Cosmos DB [Desenvolvimento com contas multi-regiões Azure Cosmos DB.](high-availability.md) Pode definir a prioridade da região utilizando o portal [Desenvolvimento com contas multi-regiões Azure Cosmos DB.](high-availability.md)

Pode adicionar o número de regiões que quiser para a conta e o controlo onde pode falhar, fornecendo uma prioridade de failover. Para utilizar a base de dados, também é necessário fornecer uma aplicação. Quando o fizeres, os teus clientes não vão sentir tempo de inatividade.

### <a name="does-the-apache-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>O índice Apache Cassandra API atribui todos os atributos de uma entidade por defeito?

Não. Cassandra API apoia [índices secundários](cassandra-secondary-index.md), que se comportam de uma forma muito semelhante à De Apache Cassandra. Não indexa todos os atributos por defeito.  


### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>Posso usar a nova Cassandra API SDK localmente com o emulador?

Sim, isto é apoiado. Você pode encontrar detalhes de como ativar isso [aqui](local-emulator.md#cassandra-api)


### <a name="how-can-i-migrate-data-from-their-apache-cassandra-clusters-to-cosmos-db"></a>Como posso migrar dados dos aglomerados apache cassandra para cosmos DB?

Pode ler sobre opções de migração [aqui.](cassandra-import-data.md)


### <a name="feature-x-of-regular-cassandra-api-isnt-working-as-today-where-can-the-feedback-be-provided"></a>O recurso x da API cassandra regular não está funcionando como hoje, onde o feedback pode ser fornecido?

Fornecer feedback através do feedback da [voz do utilizador](https://feedback.azure.com/forums/263030-azure-cosmos-db).

[azure-portal]: https://portal.azure.com
[query]: sql-api-sql-query.md

## <a name="next-steps"></a>Passos seguintes

- Começar com a [escala elástica de uma conta API Da API do Azure Cosmos DB](manage-scale-cassandra.md)Cassandra.
