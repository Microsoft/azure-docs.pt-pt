---
title: Perguntas frequentes sobre a Cassandra API para Azure Cosmos DB
description: Obtenha respostas a perguntas frequentes sobre a Cassandra API para o Azure Cosmos DB.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: thvankra
ms.openlocfilehash: 2d6cae3a7a41eae05783d3bcc12ec2bfe8220c4c
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82148327"
---
# <a name="frequently-asked-questions-about-the-cassandra-api-for-azure-cosmos-db"></a>Perguntas frequentes sobre a Cassandra API para Azure Cosmos DB

## <a name="what-are-some-key-differences-between-apache-cassandra-and-the-cassandra-api"></a>Quais são algumas diferenças fundamentais entre Apache Cassandra e a API Cassandra?

- Apache Cassandra recomenda um limite de 100 MB sobre o tamanho de uma chave de partição. A Cassandra API para Azure Cosmos DB permite até 10 GB por partição.
- Apache Cassandra permite-lhe desativar compromissos duráveis. Pode saltar a escrita para o registo de compromissoe ir diretamente para os memtables. Isto pode levar à perda de dados se o nó descer antes que os memtables sejam lavados para SSTables no disco. A Azure Cosmos DB sempre se compromete a ajudar a prevenir a perda de dados.
- Apache Cassandra pode ver um desempenho diminuído se a carga de trabalho envolver muitas substituições ou supressões. A razão são lápides que a carga de trabalho de leitura precisa saltar para buscar os dados mais recentes. A Cassandra API não verá um desempenho de leitura reduzido quando a carga de trabalho tem muitas substituições ou supressões.
- Durante cenários de elevadas cargas de trabalho de substituição, a compactação tem de ser executada para fundir sSTables no disco. (Uma fusão é necessária porque as escritas de Apache Cassandra são apenas anexadas. Várias atualizações são armazenadas como entradas individuais do SSTable que precisam de ser periodicamente fundidas). Esta situação também pode levar a uma redução do desempenho da leitura durante a compactação. Este impacto de desempenho não acontece na API cassandra porque a API não implementa a compactação.
- Definir um fator de replicação de 1 é possível com Apache Cassandra. No entanto, leva a uma baixa disponibilidade se o único nó com os dados descer. Isto não é um problema com a Cassandra API para o Azure Cosmos DB porque há sempre um fator de replicação de 4 (quórum de 3).
- A adição ou remoção de nós em Apache Cassandra requer uma intervenção manual, juntamente com uma elevada utilização de CPU no novo nó enquanto os nós existentes movem algumas das suas gamas simbólicas para o novo nó. Esta situação é a mesma quando se está a desmantelar um nó existente. No entanto, a API cassandra eslarga-se sem quaisquer problemas observados no serviço ou aplicação.
- Não há necessidade de colocar **num_tokens** em cada nó no aglomerado como em Apache Cassandra. A Azure Cosmos DB gere totalmente os nós e as gamas de token.
- A API cassandra é totalmente gerida. Não precisa dos comandos de **banquinhos** de nós, como reparação e desmantelamento, que são usados em Apache Cassandra.

## <a name="other-frequently-asked-questions"></a>Outras perguntas frequentes

### <a name="what-protocol-version-does-the-cassandra-api-support"></a>Que versão protocolar a Cassandra API suporta?

A Cassandra API para Azure Cosmos DB suporta a versão CQL 3.x. A sua compatibilidade cql baseia-se no [repositório público Apache Cassandra GitHub.](https://github.com/apache/cassandra/blob/trunk/doc/cql3/CQL.textile) Se tiver feedback sobre o apoio a outros protocolos, avise-nos através do feedback de [voz do utilizador](https://feedback.azure.com/forums/263030-azure-cosmos-db) ou envie e-mail para [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com).

### <a name="why-is-choosing-throughput-for-a-table-a-requirement"></a>Por que escolher a entrada para uma tabela é um requisito?

O Azure Cosmos DB define a entrada padrão do seu recipiente com base no local onde cria a tabela a partir de: Portal Azure ou CQL.

A Azure Cosmos DB oferece garantias de desempenho e latência, com limites superiores nas operações. Estas garantias são possíveis quando o motor pode impor a governação das operações do arrendatário. A definição de entrada garante que obtém a entrada garantida e a latência, porque a plataforma reserva esta capacidade e garante o sucesso da operação.
Pode [alterar elásticamente](manage-scale-cassandra.md) a sua entrada para beneficiar da sazonalidade da sua aplicação e economizar custos.

O conceito de entrada é explicado no artigo [Das Unidades de Pedido em Azure Cosmos DB.](request-units.md) A entrada para uma mesa é distribuída igualmente através das divisórias físicas subjacentes.

### <a name="what-is-the-throughput-of-a-table-thats-created-through-cql"></a>Qual é a entrada de uma mesa que é criada através do CQL?

A Azure Cosmos DB utiliza unidades de pedido por segundo (RU/s) como moeda para fornecer a sua entrada. As tabelas criadas através do CQL têm 400 RU por padrão. Pode mudar o RU do portal Azure.

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

A Azure Cosmos DB oferece garantias de desempenho e latência, com limites superiores nas operações. Estas garantias são possíveis quando o motor pode impor a governação das operações do arrendatário. A definição de entrada garante que obtém a entrada garantida e a latência, porque a plataforma reserva esta capacidade e garante o sucesso da operação.

Quando ultrapassaesta capacidade, recebe a seguinte mensagem de erro que indica que a sua capacidade foi esustada:

**0x1001 Sobrecarregado: o pedido não pode ser processado porque "Taxa de pedido é grande"** 

É essencial ver que operações (e o seu volume) causam esta questão. Pode ter uma ideia sobre a capacidade consumida que ultrapassa a capacidade aprovisionada com métricas no portal Azure. Então é necessário garantir que a capacidade é consumida quase igualmente em todas as divisórias subjacentes. Se vires que uma divisória está a consumir a maior parte da entrada, tens uma carga de trabalho distorcida.

Estão disponíveis métricas que mostram como a produção é usada ao longo de horas, ao longo de dias, e por sete dias, através de divisórias ou em conjunto. Para mais informações, consulte [Monitorização e depuração com métricas em Azure Cosmos DB](use-metrics.md).

Os registos de diagnóstico são explicados no artigo de registo de diagnóstico do [Azure Cosmos DB.](logging.md)

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>Será que o mapa principal do conceito chave de partição do Azure Cosmos DB?

Sim, a chave de partição é usada para colocar a entidade no local certo. Em Azure Cosmos DB, é usado para encontrar a partição lógica certa que está armazenada numa partição física. O conceito de partição é bem explicado na partilha e escala no artigo [da Azure Cosmos DB.](partition-data.md) O takeaway essencial aqui é que uma partição lógica não deve ultrapassar o limite de 10 GB.

### <a name="what-happens-when-i-get-a-notification-that-a-partition-is-full"></a>O que acontece quando recebo uma notificação de que uma partição está cheia?

Azure Cosmos DB é um sistema baseado em acordo de nível de serviço (SLA). Proporciona uma escala ilimitada, com garantias de latência, entrada, disponibilidade e consistência. Este armazenamento ilimitado baseia-se na escala horizontal de dados, utilizando a partilha como conceito-chave. O conceito de partição é bem explicado na partilha e escala no artigo [da Azure Cosmos DB.](partition-data.md)

Deve aderir ao limite de 10 GB sobre o número de entidades ou itens por partição lógica. Para garantir que a sua aplicação se estoire bem, recomendamos que *não* crie uma divisória quente armazenando toda a informação numa única divisória e consultando-a. Este erro só pode vir se os seus dados forem distorcidos: isto é,&nbsp;você tem muitos dados para uma chave de partição (mais de 10 GB). Pode encontrar a distribuição de dados utilizando o portal de armazenamento. A forma de corrigir este erro é recriar a tabela e escolher uma chave primária granular (chave de partição), que permite uma melhor distribuição de dados.

### <a name="can-i-use-the-cassandra-api-as-a-key-value-store-with-millions-or-billions-of-partition-keys"></a>Posso usar a API Cassandra como uma loja de valor chave com milhões ou biliões de chaves de partição?

A Azure Cosmos DB pode armazenar dados ilimitados através da escala do armazenamento. Este armazenamento é independente da entrada. Sim, pode sempre usar a API Cassandra apenas para armazenar e recuperar chaves e valores especificando a chave primária/partição certa. Estas chaves individuais obtêm a sua própria partição lógica e sentam-se no topo de uma divisória física sem problemas.

### <a name="can-i-create-more-than-one-table-with-the-cassandra-api"></a>Posso criar mais do que uma mesa com a API Cassandra?

Sim, é possível criar mais do que uma mesa com a API cassandra. Cada uma dessas tabelas é tratada como unidade para a entrada e armazenamento.

### <a name="can-i-create-more-than-one-table-in-succession"></a>Posso criar mais do que uma mesa sucessivamente?

A Azure Cosmos DB é um sistema repartido por recursos tanto para dados como para controlar as atividades dos aviões. Os contentores, como coleções e mesas, são entidades de tempo de execução que são aprovisionadas para uma determinada capacidade de entrada. A criação destes contentores em rápida sucessão não é uma atividade esperada e pode ser estrangulada. Se tiver testes que caiam ou criem tabelas imediatamente, tente espaça-las.

### <a name="what-is-the-maximum-number-of-tables-that-i-can-create"></a>Qual é o número máximo de mesas que posso criar?

Não há limite físico no número de mesas. Se tiver um grande número de tabelas (onde o tamanho total estável ultrapassa 10 TB de dados) [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com)que precisam de ser criadas, e não as dezenas ou centenas habituais, envie e-mail para .

### <a name="what-is-the-maximum-number-of-keyspaces-that-i-can-create"></a>Qual é o número máximo de espaços-chave que posso criar?

Não há limite físico para o número de espaços-chave porque são recipientes de metadados. Se tiver um grande número de espaços-chave, envie e-mail para [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com).

### <a name="can-i-bring-in-a-lot-of-data-after-starting-from-a-normal-table"></a>Posso trazer muitos dados depois de começar de uma mesa normal?

Sim. Assumindo divisórias distribuídas uniformemente, a capacidade de armazenamento é gerida automaticamente e aumenta à medida que empurra mais dados. Para que possa importar com confiança os dados necessários sem gerir e fornecer nós e muito mais. Mas se está a antecipar um grande crescimento imediato de dados, faz mais sentido prever diretamente [a entrada prevista](set-throughput.md) em vez de começar a descer e a aumtá-la imediatamente.

### <a name="can-i-use-yaml-file-settings-to-configure-api-behavior"></a>Posso usar as definições de ficheiro YAML para configurar o comportamento da API?

A Cassandra API para a Azure Cosmos DB proporciona compatibilidade de nível protocolar para executar operações. Esconde a complexidade da gestão, monitorização e configuração. Como desenvolvedor/utilizador, não precisa de se preocupar com disponibilidade, lápides, cache chave, cache de linha, filtro de flor e uma infinidade de outras definições. A Cassandra API centra-se em fornecer o desempenho de leitura e escrita que você precisa sem a sobrecarga de configuração e gestão.

### <a name="will-the-cassandra-api-support-node-addition-cluster-status-and-node-status-commands"></a>A API de Cassandra apoiará a adição do nó, o estado do cluster e os comandos de estado do nó?

A API Cassandra simplifica o planeamento da capacidade e responde às exigências de elasticidade para a entrada e armazenamento. Com a Azure Cosmos DB, você fornecer a entrada que você precisa. Depois pode escaloná-lo para cima e para baixo várias vezes ao longo do dia, sem se preocupar em adicionar, apagar ou gerir nós. Não precisas de usar ferramentas para o nó e para a gestão de clusters.

### <a name="what-happens-with-various-configuration-settings-for-keyspace-creation-like-simplenetwork"></a>O que acontece com várias configurações de configuração para criação de espaço chave como simples/rede?

A Azure Cosmos DB fornece distribuição global da caixa por razões de disponibilidade e baixa latência. Não precisas de montar réplicas ou outras coisas. Os escritos são sempre duramente quórum cometidos em qualquer região onde escreve, ao mesmo tempo que fornece garantias de desempenho.

### <a name="what-happens-with-various-settings-for-table-metadata"></a>O que acontece com várias definições para metadados de mesa?

A Azure Cosmos DB oferece garantias de desempenho para leituras, escritos e suposições. Por isso, não precisa de se preocupar em tocar em nenhuma das definições de configuração e em manipulá-las acidentalmente. Essas definições incluem filtro de flores, cache, oportunidade de reparação de leitura, gc_grace e memtable_flush_period de compressão.

### <a name="is-time-to-live-supported-for-cassandra-tables"></a>O tempo-a-viver é apoiado para as mesas de Cassandra?

Sim, a TTL é apoiada.

### <a name="how-can-i-monitor-infrastructure-along-with-throughput"></a>Como posso monitorizar as infraestruturas juntamente com a entrada?

O Azure Cosmos DB é um serviço de plataforma que o ajuda a aumentar a produtividade e não se preocupar em gerir e monitorizar infraestruturas. Por exemplo, não é necessário monitorizar o estado do nó, o estado da réplica, o gc e os parâmetros do OS mais cedo com várias ferramentas. Basta cuidar da entrada disponível nas métricas do portal para ver se está a ser estrangulada e, em seguida, aumentar ou diminuir essa entrada. Pode:

- Monitor [SLAs](monitor-accounts.md)
- Utilizar [métricas](use-metrics.md)
- Utilize [registos de diagnóstico](logging.md)

### <a name="which-client-sdks-can-work-with-the-cassandra-api"></a>Que cliente os SDKs podem trabalhar com a API Cassandra?

Os condutores clientes da Apache Cassandra SDK que usam o CQLv3 foram usados para programas de clientes. Se tiver outros condutores que utiliza ou se estiver a [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com)enfrentar problemas, envie correio para .

### <a name="are-composite-partition-keys-supported"></a>As chaves de partição compostasão são suportadas?

Sim, você pode usar sintaxe regular para criar chaves de partição compostas.

### <a name="can-i-use-sstableloader-for-data-loading"></a>Posso usar o sstableloader para o carregamento de dados?

Não, o sstableloader não é apoiado.

### <a name="can-i-pair-an-on-premises-apache-cassandra-cluster-with-the-cassandra-api"></a>Posso emparelhar um aglomerado apache Cassandra no local com a API Cassandra?

Atualmente, a Azure Cosmos DB tem uma experiência otimizada para um ambiente em nuvem sem a sobrecarga de operações. Se necessitar de emparelhamento, envie correio para [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) com uma descrição do seu cenário. Estamos a trabalhar numa oferta para ajudar a emparelhar o aglomerado de Cassandra no local ou nuvem com a Cassandra API para o Azure Cosmos DB.

### <a name="does-the-cassandra-api-provide-full-backups"></a>A API Cassandra fornece reforços completos?

A Azure Cosmos DB fornece duas cópias de segurança gratuitas tomadas em intervalos de quatro horas em todas as APIs. Então não precisas de arranjar um horário de reserva. 

Se quiser modificar a retenção e [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) a frequência, envie e-mail ou levante um caso de suporte. A informação sobre a capacidade de backup é fornecida na cópia de segurança online Automática e restaurar com o artigo [da Azure Cosmos DB.](../synapse-analytics/sql-data-warehouse/backup-and-restore.md)

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>Como é que a conta da Cassandra API falha se uma região cai?

A Cassandra API empresta da plataforma distribuída globalmente da Azure Cosmos DB. Para garantir que a sua aplicação pode tolerar o tempo de inatividade do datacenter, permita pelo menos mais uma região para a conta no portal Azure. Para mais informações, consulte [Alta disponibilidade com o Azure Cosmos DB](high-availability.md).

Pode adicionar o número de regiões que quiser para a conta e o controlo onde pode falhar, fornecendo uma prioridade de failover. Para utilizar a base de dados, também é necessário fornecer uma aplicação. Quando o fizeres, os teus clientes não vão sentir tempo de inatividade.

### <a name="does-the-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>O índice Cassandra API atribui todos os atributos de uma entidade por defeito?

Não. A Cassandra API apoia [índices secundários,](cassandra-secondary-index.md)que se comportam de forma semelhante à De Apache Cassandra. A API não indexa todos os atributos por defeito.  


### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>Posso usar a nova Cassandra API SDK localmente com o emulador?

Sim, esta ação é suportada. Você pode encontrar detalhes sobre como permitir isso no [Emulador De Utilização do Cosmos Azure para o desenvolvimento local e artigo](local-emulator.md#cassandra-api) de teste.


### <a name="how-can-i-migrate-data-from-apache-cassandra-clusters-to-azure-cosmos-db"></a>Como posso migrar dados dos aglomerados Apache Cassandra para o Azure Cosmos DB?

Pode ler sobre as opções de migração na [conta de Migração para Cassandra API no tutorial De DD Da Azure Cosmos.](cassandra-import-data.md)


### <a name="where-can-i-give-feedback-on-cassandra-api-features"></a>Onde posso dar feedback sobre as características da Cassandra API?

Fornecer feedback através do feedback da [voz do utilizador](https://feedback.azure.com/forums/263030-azure-cosmos-db).

[azure-portal]: https://portal.azure.com
[query]: sql-api-sql-query.md

## <a name="next-steps"></a>Passos seguintes

- Começar com a [escala elástica de uma conta API Da API do Azure Cosmos DB](manage-scale-cassandra.md)Cassandra.
