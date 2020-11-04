---
title: Perguntas frequentes sobre a Tabela API em Azure Cosmos DB
description: Obtenha respostas para perguntas frequentes sobre a Tabela API em Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: conceptual
ms.date: 08/12/2020
ms.author: sngun
ms.openlocfilehash: c5404c026385f9ebf06c4f0289a4db0bf944d172
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341243"
---
# <a name="frequently-asked-questions-about-the-table-api-in-azure-cosmos-db"></a>Perguntas frequentes sobre a Tabela API em Azure Cosmos DB
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

A Azure Cosmos DB Table API está disponível no [portal Azure](https://portal.azure.com) Primeiro tem de se inscrever para uma subscrição do Azure. Depois de se ter inscrito, pode adicionar uma conta API de Mesa DB da Azure Cosmos à sua subscrição Azure e, em seguida, adicionar tabelas à sua conta. Pode encontrar as línguas suportadas e os rápidos-arranques associados na [Introdução à AZure Cosmos DB Table API](table-introduction.md).

## <a name="table-api-in-azure-cosmos-db-vs-azure-table-storage"></a><a id="table-api-vs-table-storage"></a>API de mesa em armazenamento de mesa Azure Cosmos DB Vs Azure Table

### <a name="where-is-table-api-not-identical-with-azure-table-storage-behavior"></a>Em que medida a API de Tabela não é idêntica ao comportamento de armazenamento de Tabelas do Azure?

Existem algumas diferenças de comportamento que os utilizadores vindos do armazenamento da Mesa Azure que querem criar tabelas com a API da Tabela DB AZure Cosmos devem estar cientes:

* A Azure Cosmos DB Table API utiliza um modelo de capacidade reservada para garantir o desempenho garantido, mas isso significa que se paga a capacidade assim que a tabela é criada, mesmo que a capacidade não esteja a ser utilizada. Com o armazenamento da Mesa Azure, só se paga a capacidade que é usada. Isto ajuda a explicar por que a Tabela API pode oferecer uma leitura de 10 ms e 15 ms escrever SLA no percentil 99, enquanto o armazenamento da Azure Table oferece um SLA de 10 segundos. Mas, como consequência, com as tabelas API, mesmo mesas vazias sem quaisquer pedidos, custam dinheiro para garantir que a capacidade está disponível para lidar com quaisquer pedidos no SLA oferecido pela Azure Cosmos DB.

* Os resultados de consulta devolvidos pela Tabela API não estão classificados na ordem chave de partição/linha, uma vez que estão no armazenamento da Tabela Azure.

* As teclas de fila só podem chegar aos 255 bytes.

* Os lotes só podem ter até 2 MBs.

* O CORS não é apoiado.

* Os nomes de mesa no armazenamento da mesa Azure não são sensíveis a casos, mas estão na Azure Cosmos DB Table API.

* Alguns dos formatos internos da Azure Cosmos DB para codificar informações, como campos binários, não são atualmente tão eficientes como se poderia gostar. Portanto, isto pode causar limitações inesperadas no tamanho dos dados. Por exemplo, atualmente não se poderia usar a Meg completa de uma entidade de mesa para armazenar dados binários porque a codificação aumenta o tamanho dos dados.

* Nome de propriedade da entidade 'ID' atualmente não suportado.

* TableQuery TakeCount não se limita a 1000.

* Em termos da API REST existem uma série de opções de pontos finais/consultas que não são suportadas pela Azure Cosmos DB Table API:

  | Método de repouso | Opção de ponto final/consulta de repouso | Doc URLs | Explicação |
  | ------------| ------------- | ---------- | ----------- |
  | GET, PUT | `/?restype=service@comp=properties`| [Definir propriedades de serviço de mesa](/rest/api/storageservices/set-table-service-properties) e obter propriedades de serviço de [mesa](/rest/api/storageservices/get-table-service-properties) | Este ponto final é utilizado para definir regras CORS, configuração de análise de armazenamento e definições de registo. O CORS não é atualmente suportado e a análise e o login são tratados de forma diferente em Azure Cosmos DB do que em Azure Storage Tables |
  | Opções | `/<table-resource-name>` | [Pedido de mesa CORS pré-voo](/rest/api/storageservices/preflight-table-request) | Isto faz parte do CORS que a Azure Cosmos DB não suporta atualmente. |
  | GET | `/?restype=service@comp=stats` | [Obter estatísticas de serviço de mesa](/rest/api/storageservices/get-table-service-stats) | Fornece informações sobre a rapidez com que os dados se replicam entre os primários e os secundários. Isto não é necessário na Cosmos DB, uma vez que a replicação faz parte dos escritos. |
  | GET, PUT | `/mytable?comp=acl` | [Obtenha tabela ACL](/rest/api/storageservices/get-table-acl) e [mesa de jogo ACL](/rest/api/storageservices/set-table-acl) | Isto obtém e define as políticas de acesso armazenadas utilizadas para gerir assinaturas de acesso partilhado (SAS). Embora o SAS seja suportado, eles são definidos e geridos de forma diferente. |

* A Azure Cosmos DB Table API suporta apenas o formato JSON, não o ATOM.

* Embora a Azure Cosmos DB suporte assinaturas de acesso partilhado (SAS) existem certas políticas que não suporta, especificamente as relacionadas com operações de gestão, como o direito de criar novas tabelas.

* Para o .NET SDK em particular, existem algumas classes e métodos que a Azure Cosmos DB não suporta atualmente.

  | Classe | Método não suportado |
  |-------|-------- |
  | CloudTableClient | \*Prestações de Serviço* |
  |                  | \*Estatísticas de Serviço* |
  | CloudTable | SetPermissions* |
  |            | Obter AsPermissions* |
  | TableServiceContexto | * (esta classe é depreciada) |
  | Entidade de Serviços de Mesa | " " |
  | TabelaServiceExtensions | " " |
  | TableServiceQuery | " " |

## <a name="other-frequently-asked-questions"></a>Outras perguntas frequentes

### <a name="do-i-need-a-new-sdk-to-use-the-table-api"></a>Preciso de um novo SDK para usar a Tabela API?

Não, os SDKs de armazenamento existentes ainda devem funcionar. No entanto, recomenda-se que se obtenha sempre os mais recentes SDKs para o melhor suporte e em muitos casos um desempenho superior. Consulte a lista de idiomas disponíveis na [Tabela DB API de Introdução ao Cosmos API](table-introduction.md).

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api"></a>Qual é a cadeia de ligação que preciso de usar para ligar à Tabela API?

A cadeia de ligação é:

```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmosDB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountName>.table.cosmosdb.azure.com
```

Pode obter o fio de ligação da página 'Cadeia de Ligação' no portal Azure.

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-net-sdk-for-the-table-api"></a>Como posso sobrepor as definições de config para as opções de pedido no .NET SDK para a Tabela API?

Algumas definições são manuseadas no método CreateCloudTableClient e outras através do app.config na secção de definições de apps na aplicação do cliente. Para obter informações sobre as definições de config, consulte as [capacidades de DB do Azure Cosmos](tutorial-develop-table-dotnet.md).

### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-azure-table-storage-sdks"></a>Existem alterações para os clientes que estão a utilizar os SDKs de armazenamento de mesa Azure existentes?

Nenhum. Não existem alterações para clientes existentes ou novos que estejam a utilizar os SDKs de armazenamento de mesa Azure existentes.

### <a name="how-do-i-view-table-data-thats-stored-in-azure-cosmos-db-for-use-with-the-table-api"></a>Como vejo os dados da tabela armazenados no Azure Cosmos DB para uso com a Tabela API?

Pode utilizar o portal Azure para navegar nos dados. Também pode utilizar o código API da tabela ou as ferramentas mencionadas na resposta seguinte.

### <a name="which-tools-work-with-the-table-api"></a>Que ferramentas funcionam com a Tabela API?

Pode utilizar o [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md).

As ferramentas com a flexibilidade para obter uma cadeia de ligação no formato especificado anteriormente podem suportar a nova Tabela API. Uma lista de ferramentas de mesa é fornecida na página [Azure Storage Client Tools.](../storage/common/storage-explorers.md)

### <a name="is-the-concurrency-on-operations-controlled"></a>A concordância nas operações é controlada?

Sim, a concordância otimista é fornecida através da utilização do mecanismo ETag.

### <a name="is-the-odata-query-model-supported-for-entities"></a>O modelo de consulta OData é suportado para entidades?

Sim, a Tabela API suporta consulta OData e consulta LINQ.

### <a name="can-i-connect-to-azure-table-storage-and-azure-cosmos-db-table-api-side-by-side-in-the-same-application"></a>Posso ligar-me ao Azure Table Storage e à Azure Cosmos DB Table API lado a lado na mesma aplicação?

Sim, pode ligar-se criando duas instâncias separadas do CloudTableClient, cada uma apontando para o seu próprio URI através da cadeia de ligação.

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-offering"></a>Como posso migrar uma aplicação de armazenamento de mesa Azure existente para esta oferta?

[A AzCopy](../storage/common/storage-use-azcopy-v10.md) e a [Ferramenta de Migração de Dados DB da Azure Cosmos](import-data.md) são ambas suportadas.

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>Como é que a expansão do tamanho de armazenamento é feita para este serviço se, por exemplo, eu começar com *n* GB de dados e os meus dados vão crescer para 1 TB ao longo do tempo?

A Azure Cosmos DB foi concebida para fornecer armazenamento ilimitado através da utilização de escalas horizontais. O serviço pode monitorizar e aumentar eficazmente o seu armazenamento.

### <a name="how-do-i-monitor-the-table-api-offering"></a>Como monitorizo a oferta da Table API?

Pode utilizar o painel de **métricas** da Tabela API para monitorizar os pedidos e o uso do armazenamento.

### <a name="how-do-i-calculate-the-throughput-i-require"></a>Como calculo a produção que preciso?

Pode utilizar o estimador de capacidade para calcular o TableThroughput que é necessário para as operações. Para obter mais informações, consulte [Unidades de Pedido de Estimativa e Armazenamento de Dados.](https://www.documentdb.com/capacityplanner) Em geral, pode mostrar a sua entidade como JSON e fornecer os números para as suas operações.

### <a name="can-i-use-the-table-api-sdk-locally-with-the-emulator"></a>Posso usar a Tabela API SDK localmente com o emulador?

Neste momento, não.

### <a name="can-my-existing-application-work-with-the-table-api"></a>A minha aplicação existente pode funcionar com a Tabela API?

Sim, a mesma API é apoiada.

### <a name="do-i-need-to-migrate-my-existing-azure-table-storage-applications-to-the-sdk-if-i-dont-want-to-use-the-table-api-features"></a>Preciso de migrar as minhas aplicações de armazenamento de mesa Azure existentes para o SDK se não quiser utilizar as funcionalidades API da tabela?

Não, pode criar e utilizar os ativos de armazenamento existentes da Tabela Azure sem interrupção de qualquer tipo. No entanto, se não utilizar a Tabela API, não poderá beneficiar do índice automático, da opção de consistência adicional ou da distribuição global.

### <a name="how-do-i-add-replication-of-the-data-in-the-table-api-across-more-than-one-region-of-azure"></a>Como adiciono a replicação dos dados na Tabela API em mais de uma região do Azure?

Pode utilizar as [definições globais](tutorial-global-distribution-sql-api.md#portal) de replicação do portal Azure Cosmos para adicionar regiões adequadas à sua aplicação. Para desenvolver uma aplicação distribuída globalmente, deve também adicionar a sua aplicação com as informações deLocalização Preferida definidas na região local para fornecer latência de baixa leitura.

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-table-api"></a>Como posso alterar a região de escrita primária para a conta na Tabela API?

Você pode usar o painel global de replicação do portal de replicação Azure Cosmos DB para adicionar uma região e, em seguida, falhar para a região necessária. Para obter instruções, consulte [Desenvolvimento com contas DB Azure Cosmos multi-regiões](high-availability.md).

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>Como posso configurar as minhas regiões de leitura preferidas para baixa latência quando distribuo os meus dados?

Para ajudar a ler a partir da localização local, utilize a chave PreferredLocation no ficheiro app.config. Para aplicações existentes, a API de tabela lança um erro se o LocationMode estiver definido. Remova este código, porque a API de tabela recolhe esta informação do ficheiro app.config. 

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api"></a>Como devo pensar sobre os níveis de consistência na Tabela API?

A Azure Cosmos DB proporciona compensações bem fundamentadas entre consistência, disponibilidade e latência. O Azure Cosmos DB oferece cinco níveis de consistência aos desenvolvedores de API de tabela, para que possa escolher o modelo de consistência certo ao nível da tabela e fazer pedidos individuais enquanto consulta os dados. Quando um cliente se conecta, pode especificar um nível de consistência. Pode alterar o nível através do argumento de consistênciaLevel do CreateCloudTableClient.

A Tabela API fornece leituras de baixa latência com "Leia as suas próprias escritas", com consistência desajustada como o padrão. Para mais informações, consulte [os níveis de consistência.](consistency-levels.md)

Por padrão, o armazenamento da tabela Azure proporciona uma forte consistência dentro de uma região e eventual consistência nos locais secundários.

### <a name="does-azure-cosmos-db-table-api-offer-more-consistency-levels-than-azure-table-storage"></a>A Azure Cosmos DB Table API oferece mais níveis de consistência do que o armazenamento da Mesa Azure?

Sim, para obter informações sobre como beneficiar da natureza distribuída da Azure Cosmos DB, consulte [os níveis de consistência.](consistency-levels.md) Como são dadas garantias para os níveis de consistência, pode usá-las com confiança.

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>Quando a distribuição global está ativada, quanto tempo demora a replicar os dados?

A Azure Cosmos DB compromete os dados de forma duradoura na região local e empurra os dados para outras regiões imediatamente numa questão de milissegundos. Esta replicação depende apenas do tempo de ida e volta (RTT) do datacenter. Para saber mais sobre a capacidade de distribuição global da Azure Cosmos DB, consulte [Azure Cosmos DB: Um serviço de base de dados distribuído globalmente no Azure](distribute-data-globally.md).

### <a name="can-the-read-request-consistency-level-be-changed"></a>O nível de consistência do pedido de leitura pode ser alterado?

Com a Azure Cosmos DB, pode definir o nível de consistência ao nível do recipiente (sobre a tabela). Ao utilizar o .NET SDK, pode alterar o nível fornecendo o valor da tecla TableConsistencyLevel no ficheiro app.config. Os valores possíveis são: Forte, Limitado Staleness, Sessão, Prefix consistente e Eventual. Para obter mais informações, consulte [os níveis de consistência dos dados atunados na Azure Cosmos DB](consistency-levels.md). A ideia chave é que não pode definir o nível de consistência do pedido em mais do que a definição para a tabela. Por exemplo, não é possível definir o nível de consistência da tabela no Eventual e o nível de consistência do pedido no Strong.

### <a name="how-does-the-table-api-handle-failover-if-a-region-goes-down"></a>Como é que a Tabela API lida com o fracasso se uma região cai?

A Tabela API aproveita a plataforma globalmente distribuída da Azure Cosmos DB. Para garantir que a sua aplicação pode tolerar o tempo de inatividade do datacenter, permita pelo menos mais uma região para a conta no portal DB Azure Cosmos [Desenvolvendo-se com contas DB Azure Cosmos multi-regiões.](high-availability.md) Pode definir a prioridade da região utilizando o portal [Desenvolvimento com contas DB Azure Cosmos multi-regiões.](high-availability.md)

Pode adicionar quantas regiões quiser para a conta e controlo onde pode falhar, fornecendo uma prioridade de failover. Para utilizar a base de dados, também é necessário fornecer uma aplicação. Quando o fizeres, os teus clientes não vão ter tempo de descanso. O [mais recente cliente .NET SDK](table-sdk-dotnet.md) é auto-homing, mas os outros SDKs não são. Ou seja, pode detetar a região que está em baixo e automaticamente falhar para a nova região.

### <a name="is-the-table-api-enabled-for-backups"></a>A API de tabela está ativada para cópias de segurança?

Sim, a API de tabela aproveita a plataforma da Azure Cosmos DB para cópias de segurança. As cópias de segurança são feitas automaticamente. Para mais informações, consulte [a cópia de segurança online e restaure com a Azure Cosmos DB](online-backup-and-restore.md).

### <a name="does-the-table-api-index-all-attributes-of-an-entity-by-default"></a>O índice API da tabela é atribuído por padrão a todos os atributos de uma entidade?

Sim, todos os atributos de uma entidade são indexados por padrão. Para obter mais informações, consulte [Azure Cosmos DB: Políticas de indexação](index-policy.md).

### <a name="does-this-mean-i-dont-have-to-create-more-than-one-index-to-satisfy-the-queries"></a>Isso significa que não tenho de criar mais do que um índice para satisfazer as consultas?

Sim, a Azure Cosmos DB Table API fornece indexação automática de todos os atributos sem qualquer definição de esquema. Esta automatização liberta os desenvolvedores para se concentrarem na aplicação e não na criação e gestão de índices. Para obter mais informações, consulte [Azure Cosmos DB: Políticas de indexação](index-policy.md).

### <a name="can-i-change-the-indexing-policy"></a>Posso mudar a política de indexação?

Sim, pode alterar a política de indexação fornecendo a definição de índice. É necessário codificar e escapar corretamente às definições.

Para os non-.NET SDKs, a política de indexação só pode ser definida no portal do **Data Explorer,** navegar para a tabela específica que pretende alterar e, em seguida, ir para a Política de Indexação **de Definições &** ->, fazer a alteração desejada e, em seguida, **Guardar**.

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

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-sorting-aggregates-hierarchy-and-other-functionality-will-you-be-adding-these-capabilities-to-the-table-api"></a>Azure Cosmos DB como uma plataforma parece ter muitas capacidades, tais como triagem, agregados, hierarquia e outras funcionalidades. Irá adicionar estas capacidades à Tabela API?

A Tabela API fornece a mesma funcionalidade de consulta que o armazenamento da Tabela Azure. Também suporta ordenação, agregações, consultas geoespaciais, hierarquias e um vasto conjunto de funções incorporadas. Para mais informações, consulte [as consultas SQL.](./sql-query-getting-started.md)

### <a name="when-should-i-change-tablethroughput-for-the-table-api"></a>Quando devo mudar o TableThroughput para a Tabela API?

Deve alterar a taxa de trabalho de tableThrough quando uma das seguintes condições se aplicar:

* Está a realizar um extrato, transformação e carga (ETL) de dados, ou quer enviar muitos dados em pouco tempo.
* Precisa de mais saída do recipiente ou de um conjunto de contentores na parte de trás. Por exemplo, vês que a produção usada é mais do que a produção provisida, e estás a ser estrangulada. Para obter mais informações, consulte [set produção para recipientes Azure Cosmos](set-throughput.md).

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-table"></a>Posso escalar ou escalar a produção da minha tabela API de mesa?

Sim, pode usar o painel de escala do portal Azure Cosmos para escalar a produção. Para obter mais informações, consulte [set produção](set-throughput.md).

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>É definido um tableThroughput predefinido para as tabelas recém-acumutadas?

Sim, se não anular a produção de TableThrough via app.config e não utilizar um recipiente pré-criado em Azure Cosmos DB, o serviço cria uma tabela com produção de 400.

### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-azure-table-storage-service"></a>Existe alguma alteração de preços para os clientes existentes do serviço de armazenamento Azure Table?

Nenhum. Não há alteração de preço para os clientes de armazenamento da Mesa Azure existentes.

### <a name="how-is-the-price-calculated-for-the-table-api"></a>Como é calculado o preço para a Tabela API?

O preço depende da repartição de tableThrough.

### <a name="how-do-i-handle-any-rate-limiting-on-the-tables-in-table-api-offering"></a>Como lido com qualquer taxa que limite as tabelas na oferta da Table API?

Se a taxa de pedido for superior à capacidade da produção prevista para o contentor subjacente ou um conjunto de contentores, obtém-se um erro e o SDK recaem a chamada aplicando a política de retíria.

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-table-api-offering-of-azure-cosmos-db"></a>Por que preciso escolher uma produção para além da PartitionKey e rowKey para tirar partido da oferta da API de Tabela API da Azure Cosmos DB?

O Azure Cosmos DB define uma produção padrão para o seu recipiente se não fornecer um no ficheiro app.config ou através do portal.

A Azure Cosmos DB oferece garantias de desempenho e latência, com limites superiores em funcionamento. Esta garantia é possível quando o motor pode impor a governação sobre as operações do arrendatário. A definição de TableThroughput garante que obtém a produção garantida e a latência, uma vez que a plataforma reserva esta capacidade e garante o sucesso operacional.

Ao utilizar a especificação de produção, pode alterá-la elasticamente para beneficiar da sazonalidade da sua aplicação, atender às necessidades de produção e economizar custos.

### <a name="azure-table-storage-has-been-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-azure-cosmos-db-table-api-offering-seems-to-be-charging-me-even-though-i-havent-performed-a-single-transaction-or-stored-anything-can-you-explain"></a>O armazenamento da Mesa Azure tem sido barato para mim, porque eu pago apenas para armazenar os dados, e raramente questiono. A oferta da Azure Cosmos DB Table API parece estar a cobrar-me, apesar de não ter feito uma única transação ou armazenado nada. Pode explicar?

A Azure Cosmos DB é projetado para ser um sistema baseado em SLA distribuído globalmente com garantias de disponibilidade, latência e produção. Quando reserva produção em Azure Cosmos DB, é garantido, ao contrário da produção de outros sistemas. A Azure Cosmos DB fornece capacidades adicionais que os clientes solicitaram, como índices secundários e distribuição global.

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-azure-table-storage-with-the-table-api-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>Nunca recebo uma cotação completa" (indicando que uma partição está cheia) quando ingeri dados no armazenamento da Tabela Azure. Com a API da mesa, recebo esta mensagem. Esta oferta está a limitar-me e a forçar-me a mudar a minha aplicação existente?

Azure Cosmos DB é um sistema baseado em SLA que fornece escala ilimitada, com garantias de latência, produção, disponibilidade e consistência. Para garantir um desempenho premium garantido, certifique-se de que o tamanho e índice dos seus dados são geríveis e escaláveis. O limite de 20 GB para o número de entidades ou itens por chave de partição é para garantir que fornecemos um grande desempenho de procura e consulta. Para garantir que a sua aplicação se escava bem, mesmo para o Azure Storage, recomendamos que *não* crie uma divisória quente armazenando todas as informações numa única divisória e consultando-a.

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-table-api"></a>Então, partitionKey e RowKey ainda são necessários com a Tabela API?

Sim. Uma vez que a área de superfície da Tabela API é semelhante à do SDK de armazenamento da tabela Azure, a chave de partição fornece uma forma eficiente de distribuir os dados. A chave da linha é única dentro dessa divisória. A chave da linha tem de estar presente e não pode ser nula como no SDK padrão. O comprimento do RowKey é de 255 bytes e o comprimento da PartitionKey é de 1 KB.

### <a name="what-are-the-error-messages-for-the-table-api"></a>Quais são as mensagens de erro para a Tabela API?

O armazenamento da Azure Table e a Azure Cosmos DB Table API utilizam os mesmos SDKs para que a maioria dos erros sejam os mesmos.

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api"></a>Por que sou estrangulado quando tento criar muitas mesas umas atrás das outras na Mesa API?

Azure Cosmos DB é um sistema baseado em SLA que fornece garantias de latência, produção, disponibilidade e consistência. Por ser um sistema a provisionado, reserva recursos para garantir estes requisitos. A rapidez de criação de tabelas é detetada e estrangulada. Recomendamos que olhe para a taxa de criação de tabelas e baixe para menos de 5 por minuto. Lembre-se que a Tabela API é um sistema provisionado. Assim que o foreares, começarás a pagar por isso.

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>Como posso fornecer feedback sobre o SDK ou bugs?

Pode partilhar o seu feedback de qualquer das seguintes formas:

* [Voz do utilizador](https://feedback.azure.com/forums/263030-azure-cosmos-db)
* [Microsoft Q&Uma página de perguntas](/answers/topics/azure-cosmos-db.html)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Stack Overflow é o melhor para questões de programação. [Certifique-se de](https://stackoverflow.com/help/on-topic) que a sua pergunta está sobre o tema e forneça o máximo de detalhes [possível, tornando a pergunta clara e responsável.](https://stackoverflow.com/help/how-to-ask)

## <a name="next-steps"></a>Passos seguintes

* [Construa uma app API de tabela com .NET SDK e Azure Cosmos DB](create-table-dotnet.md)
* [Construa uma app Java para gerir dados da Azure Cosmos DB Table API](create-table-java.md)