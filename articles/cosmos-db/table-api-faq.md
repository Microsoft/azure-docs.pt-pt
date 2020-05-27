---
title: Perguntas frequentes sobre a Tabela API em Azure Cosmos DB
description: Obtenha respostas a perguntas frequentes sobre a API da Tabela em Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: sngun
ms.openlocfilehash: 4be2f61cb0a45f30f0201d1ecca0efc2d8cbd9ae
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836231"
---
# <a name="frequently-asked-questions-about-the-table-api-in-azure-cosmos-db"></a>Perguntas frequentes sobre a Tabela API em Azure Cosmos DB

O Azure Cosmos DB Table API está disponível no [portal Azure](https://portal.azure.com) Primeiro deve inscrever-se para uma subscrição Azure. Depois de se ter inscrito, pode adicionar uma conta API de Table API da Mesa Azure Cosmos à sua subscrição Azure e, em seguida, adicionar tabelas à sua conta. Você pode encontrar as línguas suportadas e quick-starts associados na [Introdução à Mesa DB Do Cosmos Azure](table-introduction.md).

## <a name="table-api-in-azure-cosmos-db-vs-azure-table-storage"></a><a id="table-api-vs-table-storage"></a>Mesa API em Armazenamento de mesa Azure Cosmos DB Vs Azure

### <a name="where-is-table-api-not-identical-with-azure-table-storage-behavior"></a>Em que medida a API de Tabela não é idêntica ao comportamento de armazenamento de Tabelas do Azure?

Existem algumas diferenças de comportamento que os utilizadores provenientes do armazenamento da Mesa Azure que querem criar tabelas com a API de Mesa DB Do Cosmos Azure devem estar cientes de:

* A API da Tabela Db da Azure Cosmos utiliza um modelo de capacidade reservado para garantir um desempenho garantido, mas isso significa que se paga a capacidade assim que a mesa é criada, mesmo que a capacidade não esteja a ser utilizada. Com o armazenamento da Mesa Azure só se paga pela capacidade que é usada. Isto ajuda a explicar por que a Table API pode oferecer uma leitura de 10 ms e 15 ms escrever SLA no percentil 99, enquanto o armazenamento da Mesa Azure oferece um SLA de 10 segundos. Mas, como consequência, com as mesas da Tabela API, mesmo mesas vazias sem quaisquer pedidos, custam dinheiro para garantir que a capacidade está disponível para lidar com quaisquer pedidos no SLA oferecido pela Azure Cosmos DB.

* Os resultados de consulta devolvidos pela Tabela API não estão classificados na chave/ordem da chave da divisória como estão no armazenamento da Mesa Azure.

* As teclas da fila só podem ser até 255 bytes.

* Os lotes só podem ter até 2 MBs.

* O CORS não é atualmente apoiado.

* Os nomes de mesa no armazenamento da Mesa Azure não são sensíveis a casos, mas estão na Mesa API da Mesa Db Da Azure Cosmos.

* Alguns dos formatos internos da Azure Cosmos DB para codificar informações, como campos binários, não são atualmente tão eficientes como se poderia gostar. Portanto, isto pode causar limitações inesperadas no tamanho dos dados. Por exemplo, atualmente não se poderia usar a Meg completa de uma entidade de mesa para armazenar dados binários porque a codificação aumenta o tamanho dos dados.

* O nome de propriedade da entidade 'ID' atualmente não suportado.

* TableQuery TakeCount não se limita a 1000.

* Em termos da API REST existem uma série de opções finais/consulta que não são suportadas pela Azure Cosmos DB Table API:

  | Métodos de repouso | Opção Fim do Descanso/Consulta | Doc URLs | Explicação |
  | ------------| ------------- | ---------- | ----------- |
  | GET, PUT | `/?restype=service@comp=properties`| [Definir propriedades](https://docs.microsoft.com/rest/api/storageservices/set-table-service-properties) do serviço de mesa e [obter propriedades de serviço](https://docs.microsoft.com/rest/api/storageservices/get-table-service-properties) de mesa | Este ponto final é utilizado para definir regras CORS, configuração de análise de armazenamento e definições de registo. Atualmente, o CORS não é suportado e a análise e a exploração madeireira são manuseadas de forma diferente em Azure Cosmos DB do que em Mesas de Armazenamento Azure |
  | Opções | `/<table-resource-name>` | [Pedido de mesa cors pré-voo](https://docs.microsoft.com/rest/api/storageservices/preflight-table-request) | Isto faz parte do CORS que a Azure Cosmos DB não suporta atualmente. |
  | GET | `/?restype=service@comp=stats` | [Obtenha estatísticas de serviço de mesa](https://docs.microsoft.com/rest/api/storageservices/get-table-service-stats) | Fornece informações a rapidez com que os dados se estão a replicar entre primários e secundários. Isto não é necessário em Cosmos DB, já que a replicação faz parte das escritas. |
  | GET, PUT | `/mytable?comp=acl` | [Obtenha mesa ACL](https://docs.microsoft.com/rest/api/storageservices/get-table-acl) e [set table ACL](https://docs.microsoft.com/rest/api/storageservices/set-table-acl) | Isto obtém e define as políticas de acesso armazenadas usadas para gerir assinaturas de acesso partilhado (SAS). Embora o SAS seja apoiado, são definidos e geridos de forma diferente. |

* A API da Tabela Db Da Azure Cosmos suporta apenas o formato JSON, não a ATOM.

* Embora a Azure Cosmos DB apoie as Assinaturas de Acesso Partilhada (SAS) existem certas políticas que não apoia, especificamente as relacionadas com operações de gestão como o direito de criar novas tabelas.

* Para o .NET SDK em particular, existem algumas classes e métodos que o Azure Cosmos DB não suporta atualmente.

  | Classe | Método não suportado |
  |-------|-------- |
  | CloudTableClient | \*Propriedades de Serviço* |
  |                  | \*Estatísticas de Serviço* |
  | CloudTable | SetPermissions* |
  |            | ObterPers* |
  | Contexto tableService | * (esta aula é depreciada) |
  | TableServiceEntity | " " |
  | Extensões de Serviço de Mesa | " " |
  | Serviço de Mesa | " " |

## <a name="other-frequently-asked-questions"></a>Outras perguntas frequentes

### <a name="do-i-need-a-new-sdk-to-use-the-table-api"></a>Preciso de um novo SDK para usar a API da tabela?

Não, os SDKs de armazenamento existentes ainda devem funcionar. No entanto, recomenda-se que se obtenha sempre os Mais recentes SDKs para o melhor suporte e, em muitos casos, um desempenho superior. Consulte a lista de idiomas disponíveis na [Introdução à Tabela API do Azure Cosmos DB](table-introduction.md).

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api"></a>Qual é a cadeia de ligação que preciso de usar para ligar à API da tabela?

A corda de ligação é:

```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmosDB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountName>.table.cosmosdb.azure.com
```

Pode obter a cadeia de ligação a partir da página De linha de ligação no portal Azure.

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-net-sdk-for-the-table-api"></a>Como sobrepor as definições de config para as opções de pedido no SDK .NET para a Tabela API?

Algumas definições são tratadas no método CreateCloudTableClient e outras através da app.config na secção appSettings na aplicação cliente. Para obter informações sobre as configurações da config, consulte as [capacidades do Azure Cosmos DB](tutorial-develop-table-dotnet.md).

### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-azure-table-storage-sdks"></a>Existem alterações para os clientes que estão a utilizar os SDKs de armazenamento de mesa azure existentes?

Nenhum. Não existem alterações para clientes existentes ou novos que estejam a utilizar os SDKs de armazenamento de mesa azure existentes.

### <a name="how-do-i-view-table-data-thats-stored-in-azure-cosmos-db-for-use-with-the-table-api"></a>Como vejo os dados da tabela armazenados no Azure Cosmos DB para serem utilizados com a API de Mesa?

Pode utilizar o portal Azure para navegar nos dados. Também pode utilizar o código API da tabela ou as ferramentas mencionadas na resposta seguinte.

### <a name="which-tools-work-with-the-table-api"></a>Que ferramentas funcionam com a API da tabela?

Pode utilizar o Explorador de [Armazenamento Azure.](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)

As ferramentas com flexibilidade para tomar uma cadeia de ligação no formato previamente especificado podem suportar a nova API do quadro. Uma lista de ferramentas de mesa é fornecida na página Ferramentas do Cliente de [Armazenamento Azure.](../storage/common/storage-explorers.md)

### <a name="is-the-concurrency-on-operations-controlled"></a>A condivisa nas operações está controlada?

Sim, a conmoeda otimista é fornecida através da utilização do mecanismo ETag.

### <a name="is-the-odata-query-model-supported-for-entities"></a>O modelo de consulta OData é suportado para entidades?

Sim, a API da tabela suporta consulta oData e consulta LINQ.

### <a name="can-i-connect-to-azure-table-storage-and-azure-cosmos-db-table-api-side-by-side-in-the-same-application"></a>Posso ligar-me ao Azure Table Storage e ao Azure Cosmos DB Table API lado a lado na mesma aplicação?

Sim, pode ligar-se criando duas instâncias separadas do CloudTableClient, cada uma apontando para o seu próprio URI através da cadeia de ligação.

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-offering"></a>Como migrarei uma aplicação de armazenamento de mesa azure existente para esta oferta?

A [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) e a Ferramenta de Migração de [Dados Da BD azure Cosmos SÃO apoiadas.](import-data.md)

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>Como é feita a expansão do tamanho do armazenamento para este serviço se, por exemplo, começo com *n* GB de dados e os meus dados vão crescer para 1 TB ao longo do tempo?

O Azure Cosmos DB foi concebido para fornecer armazenamento ilimitado através da utilização de escalas horizontais. O serviço pode monitorizar e aumentar eficazmente o seu armazenamento.

### <a name="how-do-i-monitor-the-table-api-offering"></a>Como monitorizo a oferta da API da tabela?

Pode utilizar o painel de **métricas** da Tabela API para monitorizar os pedidos e o uso do armazenamento.

### <a name="how-do-i-calculate-the-throughput-i-require"></a>Como calculo a entrada que preciso?

Pode utilizar o estimador de capacidade para calcular o TableThroughput necessário para as operações. Para mais informações, consulte [Unidades](https://www.documentdb.com/capacityplanner)de Pedido de Estimativa e Armazenamento de Dados . Em geral, pode mostrar a sua entidade como JSON e fornecer os números para as suas operações.

### <a name="can-i-use-the-table-api-sdk-locally-with-the-emulator"></a>Posso usar a Tabela API SDK localmente com o emulador?

Neste momento, não.

### <a name="can-my-existing-application-work-with-the-table-api"></a>A minha candidatura existente pode funcionar com a API de Mesa?

Sim, a mesma API é apoiada.

### <a name="do-i-need-to-migrate-my-existing-azure-table-storage-applications-to-the-sdk-if-i-dont-want-to-use-the-table-api-features"></a>Preciso de migrar os meus atuais aplicativos de armazenamento de mesa seletiva para o SDK se não quiser usar as funcionalidades da API da tabela?

Não, pode criar e utilizar os ativos de armazenamento da Mesa Azure existentes sem interrupção de qualquer tipo. No entanto, se não utilizar a API de tabela, não pode beneficiar do índice automático, da opção de consistência adicional ou da distribuição global.

### <a name="how-do-i-add-replication-of-the-data-in-the-table-api-across-more-than-one-region-of-azure"></a>Como posso adicionar a replicação dos dados da API da tabela em mais de uma região de Azure?

Pode utilizar as [definições globais](tutorial-global-distribution-sql-api.md#portal) de replicação do portal Azure Cosmos DB para adicionar regiões adequadas à sua aplicação. Para desenvolver uma aplicação distribuída globalmente, deve também adicionar a sua aplicação com as informações PreferredLocation definidas na região local para fornecer latência de baixa leitura.

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-table-api"></a>Como posso alterar a região de escrita primária para a conta na API tabela?

Você pode usar o painel do portal de replicação global Azure Cosmos DB para adicionar uma região e, em seguida, falhar na região necessária. Para obter instruções, consulte [Desenvolver com contas multi-regiões azure Cosmos DB](high-availability.md).

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>Como configurar as minhas regiões de leitura preferidas para baixa latência quando distribuo os meus dados?

Para ajudar a ler a partir da localização local, utilize a tecla PreferredLocation no ficheiro app.config. Para aplicações existentes, a API da tabela lança um erro se o Modo de Localização estiver definido. Remova esse código, porque a API da tabela recolhe esta informação do ficheiro app.config. 

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api"></a>Como devo pensar nos níveis de consistência na API do quadro?

A Azure Cosmos DB proporciona trocas bem fundamentadas entre consistência, disponibilidade e latência. O Azure Cosmos DB oferece cinco níveis de consistência aos desenvolvedores de API de mesa, para que possa escolher o modelo de consistência certo ao nível da tabela e fazer pedidos individuais enquanto consulta os dados. Quando um cliente se conecta, pode especificar um nível de consistência. Pode alterar o nível através do argumento de consistênciaLevel do CreateCloudTableClient.

A Tabela API fornece leituras de baixa latência com "Leia as suas próprias escritas", com a consistência de limite staleness como padrão. Para mais informações, consulte [os níveis de consistência.](consistency-levels.md)

Por padrão, o armazenamento da Mesa Azure proporciona uma forte consistência dentro de uma região e eventual consistência nos locais secundários.

### <a name="does-azure-cosmos-db-table-api-offer-more-consistency-levels-than-azure-table-storage"></a>A API da Tabela Db da Azure Cosmos oferece mais níveis de consistência do que o armazenamento da Mesa Azure?

Sim, para obter informações sobre como beneficiar da natureza distribuída do Azure Cosmos DB, consulte os níveis de [consistência.](consistency-levels.md) Como são dadas garantias para os níveis de consistência, pode usá-las com confiança.

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>Quando a distribuição global é ativada, quanto tempo demora a replicar os dados?

A Azure Cosmos DB compromete os dados de forma duradoura na região local e empurra os dados para outras regiões imediatamente numa questão de milissegundos. Esta replicação depende apenas do tempo de ida e volta (RTT) do datacenter. Para saber mais sobre a capacidade de distribuição global do Azure Cosmos DB, consulte [O Azure Cosmos DB: Um serviço](distribute-data-globally.md)de base de dados distribuído globalmente no Azure.

### <a name="can-the-read-request-consistency-level-be-changed"></a>O nível de consistência do pedido de leitura pode ser alterado?

Com o Azure Cosmos DB, pode definir o nível de consistência ao nível do contentor (sobre a mesa). Ao utilizar o SDK .NET, pode alterar o nível fornecendo o valor da tecla TableConsistênciaLevel no ficheiro app.config. Os valores possíveis são: Forte, Delimitado, Sessão, Prefixo Consistente e Eventual. Para mais informações, consulte os níveis de consistência de [dados Tunable em Azure Cosmos DB](consistency-levels.md). A ideia-chave é que não se pode definir o nível de consistência do pedido mais do que a definição para a tabela. Por exemplo, não é possível definir o nível de consistência para a tabela em Eventual e o nível de consistência do pedido em Strong.

### <a name="how-does-the-table-api-handle-failover-if-a-region-goes-down"></a>Como é que o cabo da Tabela API falha se uma região cai?

A API de Mesa alavanca a plataforma distribuída globalmente do Azure Cosmos DB. Para garantir que a sua aplicação pode tolerar o tempo de inatividade do datacenter, permita pelo menos mais uma região para a conta no portal Azure Cosmos DB [Desenvolvimento com contas multi-regiões Azure Cosmos DB.](high-availability.md) Pode definir a prioridade da região utilizando o portal [Desenvolvimento com contas multi-regiões Azure Cosmos DB.](high-availability.md)

Pode adicionar o número de regiões que quiser para a conta e o controlo onde pode falhar, fornecendo uma prioridade de failover. Para utilizar a base de dados, também é necessário fornecer uma aplicação. Quando o fizeres, os teus clientes não vão sentir tempo de inatividade. O [mais recente cliente .NET SDK](table-sdk-dotnet.md) é auto-homing, mas os outros SDKs não são. Ou seja, pode detetar a região que está em baixo e falha automaticamente na nova região.

### <a name="is-the-table-api-enabled-for-backups"></a>A Tabela API está ativada para backups?

Sim, a API da tabela aproveita a plataforma do Azure Cosmos DB para backups. As cópias de segurança são feitas automaticamente. Para mais informações, consulte [o backup online e restaure com o Azure Cosmos DB](online-backup-and-restore.md).

### <a name="does-the-table-api-index-all-attributes-of-an-entity-by-default"></a>O índice API tabela todos os atributos de uma entidade por defeito?

Sim, todos os atributos de uma entidade são indexados por padrão. Para mais informações, consulte [Azure Cosmos DB: Indexing policies](index-policy.md).

### <a name="does-this-mean-i-dont-have-to-create-more-than-one-index-to-satisfy-the-queries"></a>Isto significa que não tenho de criar mais do que um índice para satisfazer as consultas?

Sim, a Tabela API da Azure Cosmos DB fornece indexação automática de todos os atributos sem qualquer definição de esquema. Esta automação liberta os desenvolvedores para se concentrarem na aplicação e não na criação e gestão de índices. Para mais informações, consulte [Azure Cosmos DB: Indexing policies](index-policy.md).

### <a name="can-i-change-the-indexing-policy"></a>Posso mudar a política de indexação?

Sim, pode alterar a política de indexação fornecendo a definição de índice. É necessário codificar corretamente e escapar às definições.

Para os Non-.NET SDKs, a política de indexação só pode ser definida no portal do **Data Explorer,** navegar para a tabela específica que pretende alterar e depois ir para a **Tabela & Definições**- >Política de Indexação, fazer a mudança desejada e, em seguida, **Guardar**.

A partir do .NET SDK pode ser submetido no ficheiro app.config:

```JSON
{
  "indexingMode": "consistent",
  "automatic": true,
  "includedPaths": [
    {
      "path": "/somepath",
      "indexes": [
        {
          "kind": "Range",
          "dataType": "Number",
          "precision": -1
        },
        {
          "kind": "Range",
          "dataType": "String",
          "precision": -1
        }
      ]
    }
  ],
  "excludedPaths":
[
 {
      "path": "/anotherpath"
 }
]
}
```

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-sorting-aggregates-hierarchy-and-other-functionality-will-you-be-adding-these-capabilities-to-the-table-api"></a>O Azure Cosmos DB como plataforma parece ter muitas capacidades, tais como triagem, agregados, hierarquia e outras funcionalidades. Vai adicionar estas capacidades à API da tabela?

A API de Mesa fornece a mesma funcionalidade de consulta que o armazenamento da Mesa Azure. Também suporta ordenação, agregações, consultas geoespaciais, hierarquias e um vasto conjunto de funções incorporadas. Para mais informações, consulte [consultas SQL](how-to-sql-query.md).

### <a name="when-should-i-change-tablethroughput-for-the-table-api"></a>Quando devo mudar o TableThroughput para a Tabela API?

Deve alterar a tabelaDemodo quando qualquer uma das seguintes condições se aplica:

* Está a realizar um extrato, transformação e carga (ETL) de dados, ou pretende carregar muitos dados em pouco tempo.
* Precisa de mais entrada do recipiente ou de um conjunto de recipientes na parte de trás. Por exemplo, vê-se que a entrada usada é mais do que a entrada prevista, e está a ser estrangulada. Para mais informações, consulte [a entrada definida para os contentores Azure Cosmos](set-throughput.md).

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-table"></a>Posso escalar ou escalar a entrada da minha tabela API?

Sim, você pode usar o painel de escala do portal Azure Cosmos DB para escalar a entrada. Para mais informações, consulte a [entrada de conjunto](set-throughput.md).

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>Um tableThroughput padrão definido para novas tabelas?

Sim, se não substituir o TableThroughput via app.config e não utilizar um recipiente pré-criado em Azure Cosmos DB, o serviço cria uma tabela com a entrada de 400.

### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-azure-table-storage-service"></a>Existe alguma alteração de preços para os clientes existentes do serviço de armazenamento De Mesa Azure?

Nenhum. Não há alteração no preço dos clientes de armazenamento da Mesa Azure existentes.

### <a name="how-is-the-price-calculated-for-the-table-api"></a>Como é calculado o preço para a Tabela API?

O preço depende do tableThroughput atribuído.

### <a name="how-do-i-handle-any-rate-limiting-on-the-tables-in-table-api-offering"></a>Como lido com qualquer limitação de tarifas nas tabelas da oferta da Tabela API?

Se a taxa de pedido for superior à capacidade do produto previsto para o contentor subjacente ou a um conjunto de contentores, obtém um erro e o SDK retenta a chamada aplicando a política de retry.

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-table-api-offering-of-azure-cosmos-db"></a>Por que preciso escolher um entrada para além de PartitionKey e RowKey para aproveitar a oferta de API de Mesa de Azure Cosmos DB?

O Azure Cosmos DB define uma entrada padrão para o seu recipiente se não fornecer um no ficheiro app.config ou através do portal.

A Azure Cosmos DB oferece garantias de desempenho e latência, com limites superiores em funcionamento. Esta garantia é possível quando o motor pode impor a governação das operações do arrendatário. Definição de TableThroughput garante que obtém a entrada garantida e latência, porque a plataforma reserva esta capacidade e garante o sucesso operacional.

Ao utilizar a especificação de entrada, pode alterá-la elástico para beneficiar da sazonalidade da sua aplicação, satisfazer as necessidades de entrada e economizar custos.

### <a name="azure-table-storage-has-been-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-azure-cosmos-db-table-api-offering-seems-to-be-charging-me-even-though-i-havent-performed-a-single-transaction-or-stored-anything-can-you-explain"></a>O armazenamento da Mesa Azure tem sido barato para mim, porque pago apenas para armazenar os dados, e raramente me pergunto. A oferta da API da Mesa DD da Azure Cosmos parece estar a cobrar-me, apesar de não ter realizado uma única transação ou ter armazenado nada. Pode explicar?

O Azure Cosmos DB foi concebido para ser um sistema globalmente distribuído, baseado em SLA, com garantias de disponibilidade, latência e produção. Quando reserva a entrada em Azure Cosmos DB, é garantido, ao contrário da entrada de outros sistemas. A Azure Cosmos DB fornece capacidades adicionais que os clientes solicitaram, como índices secundários e distribuição global.

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-azure-table-storage-with-the-table-api-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>Nunca recebo uma notificação completa de quota (indicando que uma partição está cheia) quando insto dados no armazenamento da Mesa Azure. Com a API da mesa, recebo esta mensagem. Esta oferta está a limitar-me e a obrigar-me a mudar a minha candidatura existente?

O Azure Cosmos DB é um sistema baseado em SLA que fornece uma escala ilimitada, com garantias de latência, entrada, disponibilidade e consistência. Para garantir o desempenho garantido do prémio, certifique-se de que o tamanho e o índice dos seus dados são controláveis e escaláveis. O limite de 10-GB para o número de entidades ou itens por chave de partição é garantir que proporcionamos um grande desempenho de procura e consulta. Para garantir que a sua aplicação se estoire bem, mesmo para o Armazenamento Azure, recomendamos que *não* crie uma divisória quente armazenando toda a informação numa única divisória e consultando-a.

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-table-api"></a>Então, partitionKey e RowKey ainda são necessários com a API da tabela?

Sim. Uma vez que a área de superfície da API da tabela é semelhante à do Armazenamento da Tabela Azure SDK, a chave de partição fornece uma forma eficiente de distribuir os dados. A chave da linha é única dentro dessa divisória. A chave da linha precisa estar presente e não pode ser nula como no SDK padrão. O comprimento de RowKey é de 255 bytes e o comprimento da PartitionKey é de 1 KB.

### <a name="what-are-the-error-messages-for-the-table-api"></a>Quais são as mensagens de erro para a API da tabela?

O armazenamento em Mesa Azure e a API da Tabela Azure Cosmos DB utilizam os mesmos SDKs para que a maioria dos erros sejam os mesmos.

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api"></a>Por que sou estrangulado quando tento criar muitas mesas uma após a outra na API da tabela?

Azure Cosmos DB é um sistema baseado em SLA que fornece garantias de latência, entrada, disponibilidade e consistência. Por ser um sistema provisionado, reserva recursos para garantir estes requisitos. A rapidez da criação de tabelas é detetada e acelerada. Recomendamos que olhe para a taxa de criação de tabelas e baixe-a para menos de 5 por minuto. Lembre-se que a API de mesa é um sistema provisionado. Assim que o providenciares, começarás a pagar por isso.

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>Como posso dar feedback sobre o SDK ou bugs?

Pode partilhar o seu feedback de qualquer uma das seguintes formas:

* [Voz do utilizador](https://feedback.azure.com/forums/263030-azure-cosmos-db)
* [Microsoft Q&Uma página de perguntas](https://docs.microsoft.com/answers/topics/azure-cosmos-db.html)
* [Transbordo de pilhas.](https://stackoverflow.com/questions/tagged/azure-cosmosdb) Stack Overflow é o melhor para questões de programação. Certifique-se de que a sua pergunta está [no tópico](https://stackoverflow.com/help/on-topic) e forneça o máximo de [detalhes possível, tornando a questão clara e responsável.](https://stackoverflow.com/help/how-to-ask)

## <a name="next-steps"></a>Passos seguintes

* [Construa uma aplicação Table API com .NET SDK e Azure Cosmos DB](create-table-dotnet.md)
* [Construa uma app Java para gerir dados da API da Tabela Azure Cosmos DB](create-table-java.md)