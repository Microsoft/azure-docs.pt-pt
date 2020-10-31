---
title: Perguntas frequentes sobre a API de Cassandra para Azure Cosmos DB
description: Obtenha respostas para perguntas frequentes sobre a API cassandra para Azure Cosmos DB.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/12/2020
ms.author: thvankra
ms.openlocfilehash: b467e191a8f009b85abb4866821dd64fc87ce657
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93092386"
---
# <a name="frequently-asked-questions-about-the-cassandra-api-in-azure-cosmos-db"></a>Perguntas frequentes sobre a API cassandra em Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Este artigo descreve as diferenças de funcionalidade entre Apache Cassandra e Cassandra API em Azure Cosmos DB. Também fornece respostas a perguntas frequentes sobre a API Cassandra em Azure Cosmos DB.

## <a name="key-differences-between-apache-cassandra-and-the-cassandra-api"></a>Principais diferenças entre Apache Cassandra e a API cassandra

- Apache Cassandra recomenda um limite de 100-MB no tamanho de uma chave de partição. A API Cassandra para Azure Cosmos DB permite até 20 GB por partição.
- Apache Cassandra permite-lhe desativar compromissos duradouros. Pode saltar a escrita para o registo de compromisso e ir diretamente para os memtables. Isto pode levar à perda de dados se o nó descer antes que os memtables sejam lavados para SSTables no disco. A Azure Cosmos DB compromete-se sempre a ajudar a prevenir a perda de dados.
- Apache Cassandra pode ver desempenho reduzido se a carga de trabalho envolve muitas substituições ou supressões. A razão são as lápides que a carga de trabalho de leitura precisa saltar para ir buscar os dados mais recentes. A API cassandra não verá um desempenho de leitura reduzido quando a carga de trabalho tem muitas substituições ou supressões.
- Durante cenários de cargas de trabalho de substituição elevadas, a compactação precisa de ser executada para fundir SSTables no disco. (Uma fusão é necessária porque as escritas de Apache Cassandra são apenas anexas. Várias atualizações são armazenadas como entradas SSTable individuais que precisam de ser intercaladas periodicamente). Esta situação também pode levar a uma redução do desempenho da leitura durante a compactação. Este impacto de desempenho não acontece na API de Cassandra porque a API não implementa a compactação.
- Definir um fator de replicação de 1 é possível com Apache Cassandra. No entanto, leva a uma baixa disponibilidade se o único nó com os dados descer. Este não é um problema com a API cassandra para Azure Cosmos DB porque há sempre um fator de replicação de 4 (quórum de 3).
- Adicionar ou remover nós em Apache Cassandra requer intervenção manual, juntamente com uma alta utilização do CPU no novo nó enquanto os nós existentes movem algumas das suas gamas simbólicas para o novo nó. Esta situação é a mesma quando se está a desmantelar um nó existente. No entanto, a API de Cassandra escala sem quaisquer problemas observados no serviço ou aplicação.
- Não há necessidade de colocar **num_tokens** em cada nó no aglomerado como em Apache Cassandra. A Azure Cosmos DB gere totalmente os nós e as gamas simbólicas.
- A API cassandra é totalmente gerida. Não precisa dos comandos **nodetool,** como reparação e desmantelamento, que são usados em Apache Cassandra.

## <a name="other-frequently-asked-questions"></a>Outras perguntas frequentes

### <a name="what-protocol-version-does-the-cassandra-api-support"></a>Que versão protocolar suporta a API cassandra?

A API Cassandra para Azure Cosmos DB suporta a versão 3.x do CQL. A sua compatibilidade CQL baseia-se no [repositório público Apache Cassandra GitHub.](https://github.com/apache/cassandra/blob/trunk/doc/cql3/CQL.textile) Se tiver feedback sobre o suporte a outros protocolos, informe-nos através do feedback de [voz do utilizador](https://feedback.azure.com/forums/263030-azure-cosmos-db) ou envie e-mail para [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) .

### <a name="why-is-choosing-throughput-for-a-table-a-requirement"></a>Por que escolher a produção para uma mesa é um requisito?

Azure Cosmos DB define a produção padrão para o seu recipiente com base no local onde cria a tabela a partir de: Portal Azure ou CQL.

A Azure Cosmos DB oferece garantias de desempenho e latência, com limites superiores nas operações. Estas garantias são possíveis quando o motor pode impor a governação sobre as operações do arrendatário. A definição de produção garante que obtém o rendimento garantido e a latência, uma vez que a plataforma reserva esta capacidade e garante o sucesso do funcionamento.
Pode [alterar elasticamente](manage-scale-cassandra.md) a produção para beneficiar da sazonalidade da sua aplicação e economizar custos.

O conceito de produção é explicado no artigo das [Unidades de Pedido no Azure Cosmos DB.](request-units.md) A produção de uma mesa é igualmente distribuída através das divisórias físicas subjacentes.

### <a name="what-is-the-throughput-of-a-table-thats-created-through-cql"></a>Qual é a produção de uma tabela que é criada através do CQL?

A Azure Cosmos DB utiliza Unidades de Pedido por segundo (RU/s) como moeda para fornecer produção. As tabelas criadas através do CQL têm 400 RU por padrão. Pode mudar o RU do portal Azure.

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

### <a name="what-happens-when-throughput-is-used-up"></a>O que acontece quando a produção é usada?

A Azure Cosmos DB oferece garantias de desempenho e latência, com limites superiores nas operações. Estas garantias são possíveis quando o motor pode impor a governação sobre as operações do arrendatário. A definição de produção garante que obtém o rendimento garantido e a latência, uma vez que a plataforma reserva esta capacidade e garante o sucesso do funcionamento.

Quando se ultrapassa esta capacidade, obtém-se a seguinte mensagem de erro que indica que a sua capacidade foi utilizada:

**0x1001 Sobrecarregado: o pedido não pode ser processado porque "Taxa de Pedido é grande"** 

É essencial ver que operações (e seu volume) causam esta questão. Pode ter uma ideia sobre a capacidade consumida que ultrapassa a capacidade a provisionada com métricas no portal Azure. Então você precisa garantir que a capacidade é consumida quase igualmente em todas as divisórias subjacentes. Se vires que uma partição está a consumir a maior parte da produção, tens uma distorção da carga de trabalho.

Estão disponíveis métricas que mostram como a produção é usada ao longo de horas, ao longo de dias, e por sete dias, através de divisórias ou em conjunto. Para obter mais informações, consulte [Monitorização e depuração com métricas em Azure Cosmos DB](use-metrics.md).

Os registos de diagnóstico são explicados no artigo de [registo de diagnóstico Azure Cosmos DB.](./monitor-cosmos-db.md)

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>O mapa principal da chave primária para o conceito chave de partição de Azure Cosmos DB?

Sim, a chave de partição é usada para colocar a entidade no local certo. Em Azure Cosmos DB, é usado para encontrar a partição lógica certa que está armazenada numa partição física. O conceito de partição é bem explicado na Partição e escala no artigo [DB do Azure Cosmos.](partitioning-overview.md) O takeaway essencial aqui é que uma divisória lógica não deve passar do limite de 20 GB.

### <a name="what-happens-when-i-get-a-notification-that-a-partition-is-full"></a>O que acontece quando recebo uma notificação de que uma divisória está cheia?

Azure Cosmos DB é um sistema baseado em acordo de nível de serviço (SLA). Proporciona uma escala ilimitada, com garantias de latência, produção, disponibilidade e consistência. Este armazenamento ilimitado baseia-se na escala horizontal dos dados, utilizando a partição como conceito-chave. O conceito de partição é bem explicado na Partição e escala no artigo [DB do Azure Cosmos.](partitioning-overview.md)

Deve aderir ao limite de 20 GB no número de entidades ou itens por partição lógica. Para garantir que a sua aplicação se escasseia bem, recomendamos que *não* crie uma divisória quente armazenando todas as informações numa única partição e consultando-a. Este erro só pode vir se os seus dados forem distorcidos: ou seja, tem muitos dados para uma chave de partição (mais de 20 GB). Pode encontrar a distribuição de dados utilizando o portal de armazenamento. A forma de corrigir este erro é recriar a tabela e escolher uma chave primária granular (chave de partição), que permita uma melhor distribuição dos dados.

### <a name="can-i-use-the-cassandra-api-as-a-key-value-store-with-millions-or-billions-of-partition-keys"></a>Posso usar a API cassandra como uma loja de valor chave com milhões ou biliões de chaves de partição?

A Azure Cosmos DB pode armazenar dados ilimitados escalando o armazenamento. Este armazenamento é independente da produção. Sim, pode sempre utilizar a API Cassandra apenas para armazenar e recuperar chaves e valores especificando a chave primária/partição certa. Estas chaves individuais obtêm a sua própria divisória lógica e sentam-se em cima de uma divisória física sem problemas.

### <a name="can-i-create-more-than-one-table-with-the-cassandra-api"></a>Posso criar mais do que uma mesa com a API cassandra?

Sim, é possível criar mais do que uma mesa com a API cassandra. Cada uma dessas mesas é tratada como unidade para produção e armazenamento.

### <a name="can-i-create-more-than-one-table-in-succession"></a>Posso criar mais do que uma mesa sucessiva?

AZure Cosmos DB é um sistema governado por recursos para atividades de dados e planos de controlo. Os contentores, como as coleções e as mesas, são entidades de tempo de execução que estão previstas para uma determinada capacidade de produção. A criação destes contentores numa rápida sucessão não é uma atividade esperada e pode ser estrangulada. Se tiver testes que caiam ou criam tabelas imediatamente, tente espaça-las.

### <a name="what-is-the-maximum-number-of-tables-that-i-can-create"></a>Qual é o número máximo de mesas que posso criar?

Não há limite físico no número de mesas. Se tiver um grande número de tabelas (onde o tamanho total estável ultrapassa 10 TB de dados) que precisam de ser criadas, e não as dezenas ou centenas habituais, envie e-mail para [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) .

### <a name="what-is-the-maximum-number-of-keyspaces-that-i-can-create"></a>Qual é o número máximo de espaços chave que posso criar?

Não há limite físico no número de espaços-chave porque são contentores de metadados. Se tiver um grande número de espaços-chave, envie um e-mail para [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) .

### <a name="can-i-bring-in-a-lot-of-data-after-starting-from-a-normal-table"></a>Posso trazer muitos dados depois de começar de uma mesa normal?

Sim. Assumindo divisórias distribuídas uniformemente, a capacidade de armazenamento é gerida automaticamente e aumenta à medida que empurra mais dados. Assim, pode importar com confiança os dados que precisar sem gerir e aacaçar nós e muito mais. Mas se está a antecipar um grande crescimento imediato dos dados, faz mais sentido prever diretamente [a produção prevista,](set-throughput.md) em vez de começar mais baixo e ausenhá-lo imediatamente.

### <a name="can-i-use-yaml-file-settings-to-configure-api-behavior"></a>Posso usar as definições de ficheiro YAML para configurar o comportamento da API?

A API cassandra para Azure Cosmos DB fornece compatibilidade ao nível do protocolo para a execução de operações. Esconde a complexidade da gestão, monitorização e configuração. Como desenvolvedor/utilizador, não precisa de se preocupar com disponibilidade, lápides, cache chave, cache de linha, filtro de flores e uma infinidade de outras configurações. A API Cassandra foca-se em fornecer o desempenho de leitura e escrita que você precisa sem a sobrecarga de configuração e gestão.

### <a name="will-the-cassandra-api-support-node-addition-cluster-status-and-node-status-commands"></a>A API cassandra suporta a adição de nós, o estado do cluster e os comandos de estado do nó?

A API cassandra simplifica o planeamento de capacidades e responde às exigências de elasticidade para a produção e armazenamento. Com a Azure Cosmos DB, forneces a produção que precisas. Depois pode escaloná-lo para cima e para baixo várias vezes ao longo do dia, sem se preocupar em adicionar, apagar ou gerir nós. Não é preciso usar ferramentas para gestão de nó e cluster.

### <a name="what-happens-with-various-configuration-settings-for-keyspace-creation-like-simplenetwork"></a>O que acontece com várias configurações para criação de keyspace como simples/rede?

A Azure Cosmos DB fornece distribuição global fora da caixa por razões de disponibilidade e baixa latência. Não precisas de montar réplicas ou outras coisas. As gravações são sempre durávelmente comprometidas em qualquer região onde você escreve, ao mesmo tempo que fornece garantias de desempenho.

### <a name="what-happens-with-various-settings-for-table-metadata"></a>O que acontece com várias definições para metadados de mesa?

A Azure Cosmos DB oferece garantias de desempenho para leituras, escritas e produção. Por isso, não precisas de te preocupar em tocar em nenhuma das definições de configuração e manipulá-las acidentalmente. Essas configurações incluem filtro de flores, caching, leitura de chance de reparação, gc_grace e memtable_flush_period de compressão.

### <a name="is-time-to-live-supported-for-cassandra-tables"></a>O tempo-a-vida é apoiado para as mesas de Cassandra?

Sim, a TTL é apoiada.

### <a name="how-can-i-monitor-infrastructure-along-with-throughput"></a>Como posso monitorizar as infraestruturas juntamente com a produção?

A Azure Cosmos DB é um serviço de plataforma que o ajuda a aumentar a produtividade e não se preocupa em gerir e monitorizar infraestruturas. Por exemplo, não precisa monitorizar o estado do nó, o estado da réplica, os parâmetros gc e OS anteriormente com várias ferramentas. Só precisas de cuidar da produção que está disponível nas métricas do portal para ver se estás a ser estrangulado e depois aumentar ou diminuir essa produção. Pode:

- Monitor [SLAs](./monitor-cosmos-db.md)
- Use [métricas](use-metrics.md)
- Use [registos de diagnóstico](./monitor-cosmos-db.md)

### <a name="which-client-sdks-can-work-with-the-cassandra-api"></a>Que cliente sDKs pode trabalhar com a API Cassandra?

Os motoristas clientes da Apache Cassandra SDK que usam o CQLv3 foram usados para programas de clientes. Se tiver outros condutores que utilize ou se estiver a enfrentar problemas, envie um correio para [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) .

### <a name="are-composite-partition-keys-supported"></a>As chaves de partição compostas são suportadas?

Sim, pode usar sintaxe regular para criar teclas de partição compostas.

### <a name="can-i-use-sstableloader-for-data-loading"></a>Posso usar o sstableloader para o carregamento de dados?

Não, o sstableloader não é suportado.

### <a name="can-i-pair-an-on-premises-apache-cassandra-cluster-with-the-cassandra-api"></a>Posso emparelhar um aglomerado Apache Cassandra no local com a API Cassandra?

Atualmente, a Azure Cosmos DB tem uma experiência otimizada para um ambiente em nuvem sem a sobrecarga de operações. Se necessitar de emparelhamento, envie um e-mail [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) para a descrição do seu cenário. Estamos a trabalhar numa oferta para ajudar a emparelhar o aglomerado de Cassandra no local ou nuvem cassandra com a Cassandra API para a Azure Cosmos DB.

### <a name="does-the-cassandra-api-provide-full-backups"></a>A API cassandra fornece reforços completos?

A Azure Cosmos DB fornece dois backups completos gratuitos feitos em intervalos de quatro horas em todas as APIs. Então não precisas de marcar um horário de reserva. 

Se quiser modificar a retenção e frequência, envie e-mail [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) ou levante um caso de suporte. A informação sobre a capacidade de backup é fornecida na cópia de segurança online automática e restaurada com o artigo [DB da Azure Cosmos.](online-backup-and-restore.md)

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>Como é que a conta da API cassandra lida com o fracasso se uma região cai?

A API Cassandra empresta da plataforma globalmente distribuída da Azure Cosmos DB. Para garantir que a sua aplicação pode tolerar o tempo de inatividade do datacenter, permita pelo menos mais uma região para a conta no portal Azure. Para mais informações, consulte [Alta disponibilidade com Azure Cosmos DB](high-availability.md).

Pode adicionar quantas regiões quiser para a conta e controlo onde pode falhar, fornecendo uma prioridade de failover. Para utilizar a base de dados, também é necessário fornecer uma aplicação. Quando o fizeres, os teus clientes não vão ter tempo de descanso.

### <a name="does-the-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>A API Cassandra indexa todos os atributos de uma entidade por defeito?

N.º A API cassandra suporta [índices secundários,](cassandra-secondary-index.md)que se comportam de forma semelhante à De Apache Cassandra. A API não indexa todos os atributos por defeito.  


### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>Posso usar a nova Cassandra API SDK localmente com o emulador?

Sim, esta ação é suportada. Pode encontrar detalhes sobre como permitir isso no [Emulador Azure Cosmos para desenvolvimento local e](local-emulator.md#cassandra-api) artigo de teste.


### <a name="how-can-i-migrate-data-from-apache-cassandra-clusters-to-azure-cosmos-db"></a>Como posso migrar dados dos aglomerados Apache Cassandra para a Azure Cosmos DB?

Pode ler sobre as opções de migração na [conta da API de Cassandra na conta Azure Cosmos DB.](cassandra-import-data.md)


### <a name="where-can-i-give-feedback-on-cassandra-api-features"></a>Onde posso dar feedback sobre as funcionalidades da API da Cassandra?

Fornecer feedback através do feedback de [voz do utilizador](https://feedback.azure.com/forums/263030-azure-cosmos-db).

[azure-portal]: https://portal.azure.com
[query]: ./sql-query-getting-started.md

## <a name="next-steps"></a>Passos seguintes

- Começa com [a dimensionamento elasticamente de uma conta Azure Cosmos DB Cassandra API](manage-scale-cassandra.md).