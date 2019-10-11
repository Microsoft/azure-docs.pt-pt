---
title: Perguntas mais frequentes sobre as diferentes APIs no Azure Cosmos DB
description: Obtenha respostas para perguntas frequentes sobre Azure Cosmos DB, um serviço de banco de dados multimodelo distribuído globalmente. Saiba mais sobre a capacidade, os níveis de desempenho e o dimensionamento.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 15e4fc3ce412807908c75fe25acecac0fe86d261
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72262796"
---
# <a name="frequently-asked-questions-about-different-apis-in-azure-cosmos-db"></a>Perguntas mais frequentes sobre as diferentes APIs no Azure Cosmos DB

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Quais são os casos de uso típicos para Azure Cosmos DB?

Azure Cosmos DB é uma boa opção para novos aplicativos Web, móveis, de jogos e IoT em que a escala automática, o desempenho previsível, os tempos de resposta rápidos da ordem de milissegundos e a capacidade de consultar dados sem esquemas é importante. Azure Cosmos DB presta-se ao desenvolvimento rápido e ao suporte à iteração contínua de modelos de dados de aplicativos. Os aplicativos que gerenciam conteúdo e dados gerados pelo usuário são [casos de uso comuns para Azure Cosmos DB](use-cases.md).

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Como Azure Cosmos DB oferece desempenho previsível?

Uma ru ( [unidade de solicitação](request-units.md) ) é a medida de taxa de transferência em Azure Cosmos DB. Uma taxa de transferência de 1RU corresponde à taxa de transferência da obtenção de um documento de 1 KB. Cada operação no Azure Cosmos DB, incluindo leituras, gravações, consultas SQL e execuções de procedimento armazenado, tem um valor de RU determinístico baseado na taxa de transferência necessária para concluir a operação. Em vez de pensar sobre CPU, e/s e memória e como cada um deles afeta a taxa de transferência do seu aplicativo, você pode pensar em uma medida única de RU.

Você pode configurar cada contêiner Cosmos do Azure com taxa de transferência provisionada em termos de RUs de taxa de transferência por segundo. Para aplicativos de qualquer escala, você pode fazer o benchmark de solicitações individuais para medir seus valores de RU e provisionar um contêiner para lidar com o total de unidades de solicitação em todas as solicitações. Você também pode escalar ou reduzir verticalmente a taxa de transferência do contêiner conforme as necessidades do seu aplicativo evoluem. Para obter mais informações sobre unidades de solicitação e ajuda para determinar suas necessidades de contêiner, experimente a [calculadora de produtividade](https://www.documentdb.com/capacityplanner).

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>Como Azure Cosmos DB dá suporte a vários modelos de dados, como chave/valor, colunar, documento e grafo?

Os modelos de dados de chave/valor (tabela), coluna, documento e gráfico têm suporte nativo por causa do design ARS (átomos, registros e sequências) no qual Azure Cosmos DB é criado. Os átomos, os registros e as sequências podem ser facilmente mapeados e projetados para vários modelos de dados. As APIs para um subconjunto de modelos estão disponíveis no momento (SQL, MongoDB, Table e Gremlin) e outras específicas para modelos de dados adicionais estarão disponíveis no futuro.

Azure Cosmos DB tem um mecanismo de indexação independente de esquema capaz de indexar automaticamente todos os dados ingeridos sem a necessidade de nenhum esquema ou de índices secundários do desenvolvedor. O mecanismo depende de um conjunto de layouts de índice lógico (invertido, colunar, árvore) que dissocia o layout de armazenamento dos subsistemas de índice e de processamento de consulta. O Cosmos DB também tem a capacidade de dar suporte a um conjunto de protocolos de transmissão e APIs de maneira extensível e traduzi-los com eficiência para o modelo de dados principal (1) e os layouts de índice lógico (2) tornando-o exclusivamente capaz de dar suporte a mais de um modelo de dados nativamente.

### <a name="can-i-use-multiple-apis-to-access-my-data"></a>Posso usar várias APIs para acessar meus dados?

Azure Cosmos DB é o serviço de banco de dados multimodelo distribuído globalmente da Microsoft. Onde vários modelos significam Azure Cosmos DB dá suporte a várias APIs e a vários modelos de dados, diferentes APIs usam formatos de dados diferentes para o protocolo de armazenamento e de conexão. Por exemplo, o SQL usa JSON, o MongoDB usa BSON, a tabela usa o EDM, o Cassandra usa CQL, Gremlin usa GraphSON. Como resultado, é recomendável usar a mesma API para todo o acesso aos dados em uma determinada conta.

Cada API Opera de forma independente, exceto o Gremlin e a API do SQL, que são interoperáveis.

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>O é Azure Cosmos DB compatível com HIPAA?

Sim, Azure Cosmos DB é compatível com HIPAA. A HIPAA estabelece requisitos para a utilização, divulgação e salvaguarda das informações médicas pessoalmente identificáveis. Para obter mais informações, consulte o [Centro de Fidedignidade da Microsoft](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA).

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>Quais são os limites de armazenamento de Azure Cosmos DB?

Não há limite para a quantidade total de dados que um contêiner pode armazenar em Azure Cosmos DB.

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>Quais são os limites de taxa de transferência de Azure Cosmos DB?

Não há limite para a quantidade total de produtividade que um contêiner pode dar suporte em Azure Cosmos DB. A ideia principal é distribuir sua carga de trabalho aproximadamente de forma uniforme entre um número suficientemente grande de chaves de partição.

### <a name="are-direct-and-gateway-connectivity-modes-encrypted"></a>Os modos de conectividade direta e de gateway são criptografados?

Sim os dois modos são sempre totalmente criptografados.

### <a name="how-much-does-azure-cosmos-db-cost"></a>Quanto custa Azure Cosmos DB?

Para obter detalhes, consulte a página de [detalhes de preços de Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) . Azure Cosmos DB encargos de uso são determinados pelo número de contêineres provisionados, pelo número de horas em que os contêineres estavam online e pela taxa de transferência provisionada para cada contêiner.

### <a name="is-a-free-account-available"></a>Há uma conta gratuita disponível?

Sim, você pode se inscrever para uma conta de tempo limitado sem custos, sem compromisso. Para se inscrever, visite [tentar Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) ou Leia mais nas [perguntas frequentes sobre Experimente Azure Cosmos DB](#try-cosmos-db).

Se você for novo no Azure, poderá se inscrever para uma [conta gratuita do Azure](https://azure.microsoft.com/free/), que oferece 30 dias e um crédito para experimentar todos os serviços do Azure. Se você tiver uma assinatura do Visual Studio, você também estará qualificado para [créditos gratuitos do Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) para usar em qualquer serviço do Azure.

Você também pode usar o [emulador de Azure Cosmos DB](local-emulator.md) para desenvolver e testar seu aplicativo localmente gratuitamente, sem criar uma assinatura do Azure. Quando estiver satisfeito com o funcionamento da sua aplicação no Emulador do Azure Cosmos DB, pode mudar e começar a utilizar uma conta do Azure Cosmos DB na cloud.

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>Como posso obter ajuda adicional com Azure Cosmos DB?

Para fazer uma pergunta técnica, você pode postar em um desses dois fóruns de perguntas e respostas:

* [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Stack Overflow é melhor para perguntas de programação. Verifique se sua pergunta está [no tópico](https://stackoverflow.com/help/on-topic) e [forneça o máximo de detalhes possível, tornando a pergunta clara e pode ser respondida](https://stackoverflow.com/help/how-to-ask).

Para solicitar novos recursos, crie uma nova solicitação no [User Voice](https://feedback.azure.com/forums/263030-azure-cosmos-db).

Para corrigir um problema na sua conta, crie um [pedido de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) no portal do Azure.

Outras perguntas podem ser enviadas para a equipe em [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com); no entanto, esse não é um alias de suporte técnico.

## <a id="try-cosmos-db"></a>Experimentar Azure Cosmos DB assinaturas

Agora você pode desfrutar de uma experiência de Azure Cosmos DB de tempo limitado sem uma assinatura, sem encargos e compromissos. Para se inscrever em uma assinatura do try Azure Cosmos DB, vá para [experimentar Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/). Essa assinatura é separada da [avaliação gratuita do Azure](https://azure.microsoft.com/free/)e pode ser usada junto com uma avaliação gratuita do Azure ou uma assinatura paga do Azure.

Tente Azure Cosmos DB assinaturas sejam exibidas na portal do Azure próximas outras assinaturas associadas à sua ID de usuário.

As seguintes condições se aplicam para experimentar Azure Cosmos DB assinaturas:

* Um [contêiner de taxa de transferência provisionado](./set-throughput.md#set-throughput-on-a-container) por assinatura para SQL, API Gremlin e contas de tabela.
* Até três [coleções de taxa de transferência provisionada](./set-throughput.md#set-throughput-on-a-container) por assinatura para contas do MongoDB.
* Uma [taxa de transferência de banco de dados provisionada](./set-throughput.md#set-throughput-on-a-database) por assinatura. Os bancos de dados provisionados pela taxa de transferência podem conter qualquer número de contêineres dentro do.
* capacidade de armazenamento de 10 GB.
* A replicação global está disponível nas seguintes [regiões do Azure](https://azure.microsoft.com/regions/): EUA Central, Europa setentrional e sudeste asiático
* Taxa de transferência máxima de 5 K RU/s quando provisionado no nível de contêiner.
* Taxa de transferência máxima de 20 K RU/s quando provisionado no nível do banco de dados.
* As assinaturas expiram após 30 dias e podem ser estendidas para um máximo de 31 dias.
* Tíquetes de suporte do Azure não podem ser criados para contas try Azure Cosmos DB; no entanto, o suporte é fornecido para assinantes com planos de suporte existentes.

## <a name="set-up-azure-cosmos-db"></a>Configurar Azure Cosmos DB

### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>Como fazer inscrever-se no Azure Cosmos DB?

Azure Cosmos DB está disponível na portal do Azure. Primeiro, Inscreva-se para uma assinatura do Azure. Depois de se inscrever, você pode adicionar uma conta de Azure Cosmos DB à sua assinatura do Azure.

### <a name="what-is-a-master-key"></a>O que é uma chave mestra?

Uma chave mestra é um token de segurança para aceder a todos os recursos de uma conta. Indivíduos com a chave têm acesso de leitura e gravação a todos os recursos na conta do banco de dados. Tenha cuidado ao distribuir chaves mestras. A chave mestra primária e a chave mestra secundária estão disponíveis na folha **chaves** da [portal do Azure][azure-portal]. Para mais informações sobre chaves, consulte [Ver, copiar e voltar a gerar chaves de acesso](manage-with-cli.md#list-account-keys).

### <a name="what-are-the-regions-that-preferredlocations-can-be-set-to"></a>Quais são as regiões às quais PreferredLocations pode ser definido?

O valor de PreferredLocations pode ser definido como qualquer uma das regiões do Azure nas quais Cosmos DB está disponível. Para obter uma lista de regiões disponíveis, consulte [regiões do Azure](https://azure.microsoft.com/regions/).

### <a name="is-there-anything-i-should-be-aware-of-when-distributing-data-across-the-world-via-the-azure-datacenters"></a>Há alguma coisa que eu deva saber ao distribuir dados pelo mundo por meio dos data centers do Azure?

Azure Cosmos DB está presente em todas as regiões do Azure, conforme especificado na página de [regiões do Azure](https://azure.microsoft.com/regions/) . Por ser o serviço principal, cada novo datacenter tem uma presença Azure Cosmos DB.

Quando você define uma região, lembre-se de que Azure Cosmos DB respeita as nuvens soberanas e governamental. Ou seja, se você criar uma conta em uma [região soberanas](https://azure.microsoft.com/global-infrastructure/), não poderá replicar fora dessa [região soberanas](https://azure.microsoft.com/global-infrastructure/). Da mesma forma, você não pode habilitar a replicação em outros locais soberanas de uma conta externa.

### <a name="is-it-possible-to-switch-from-container-level-throughput-provisioning-to-database-level-throughput-provisioning-or-vice-versa"></a>É possível mudar do provisionamento de taxa de transferência no nível de contêiner para o provisionamento de taxa de transferência no nível do banco de dados? Ou vice-versa

O provisionamento de taxa de transferência de nível de banco de dados e contêiner são ofertas separadas e a alternância entre uma delas exige a migração de dados da origem para o destino. Isso significa que você precisa criar um novo banco de dados ou um novo contêiner e, em seguida, migrá-los usando a [biblioteca de executores em massa](bulk-executor-overview.md) ou [Azure data Factory](../data-factory/connector-azure-cosmos-db.md).

### <a name="does-azure-cosmosdb-support-time-series-analysis"></a>O Azure CosmosDB dá suporte à análise de série temporal?

Sim o Azure CosmosDB dá suporte à análise de série temporal, aqui está um exemplo de [padrão de série temporal](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/samples/Patterns). Este exemplo mostra como usar o feed de alterações para criar exibições agregadas em dados de série temporal. Você pode estender essa abordagem usando o streaming do Spark ou outro processador de dados de fluxo.

## <a name="what-are-the-azure-cosmos-db-service-quotas-and-throughput-limits"></a>Quais são as cotas de serviço Azure Cosmos DB e os limites de taxa de transferência

Consulte os artigos Azure Cosmos DB [cotas de serviço](concepts-limits.md) e [limites por contêiner e banco de dados](set-throughput.md#comparison-of-models) para obter mais informações.

## <a name="sql-api"></a>SQL API

### <a name="how-do-i-start-developing-against-the-sql-api"></a>Como fazer começar a desenvolver na API do SQL?

Primeiro, você deve se inscrever para uma assinatura do Azure. Depois de se inscrever para uma assinatura do Azure, você pode adicionar um contêiner da API do SQL à sua assinatura do Azure. Para obter instruções sobre como adicionar uma conta de Azure Cosmos DB, consulte [criar uma conta de banco de dados Cosmos do Azure](create-sql-api-dotnet.md#create-account).

Existem [SDKs](sql-api-sdk-dotnet.md) disponíveis para .NET, Python, Node.js, JavaScript e Java. Os desenvolvedores também podem usar as [APIs http RESTful](/rest/api/cosmos-db/) para interagir com Azure Cosmos DB recursos de várias plataformas e linguagens.

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>Posso acessar alguns exemplos prontos para começar a usar?

Exemplos para os SDKs [.net](sql-api-dotnet-samples.md), [Java](https://github.com/Azure/azure-documentdb-java), [node. js](sql-api-nodejs-samples.md)e [Python](sql-api-python-samples.md) da API do SQL estão disponíveis no github.

### <a name="does-the-sql-api-database-support-schema-free-data"></a>O banco de dados da API do SQL dá suporte a informações sem esquema?

Sim, a API do SQL permite que os aplicativos armazenem documentos JSON arbitrários sem definições ou dicas de esquema. Os dados ficam imediatamente disponíveis para consulta por meio da interface de consulta do Azure Cosmos DB SQL.

### <a name="does-the-sql-api-support-acid-transactions"></a>A API do SQL dá suporte a transações ACID?

Sim, a API do SQL dá suporte a transações entre documentos expressas como procedimentos e gatilhos armazenados em JavaScript. As transações são delimitadas a uma única partição dentro de cada contêiner e executadas com semântica ACID como "tudo ou nada", isoladas de outras solicitações de código e usuário executadas simultaneamente. Se as exceções forem geradas por meio da execução do código do aplicativo JavaScript no lado do servidor, toda a transação será revertida. 

### <a name="what-is-a-container"></a>O que é um contêiner?

Um contêiner é um grupo de documentos e sua lógica de aplicativo JavaScript associada. Um contêiner é uma entidade Faturável, em que o [custo](performance-levels.md) é determinado pela taxa de transferência e pelo armazenamento usado. Os contêineres podem abranger uma ou mais partições ou servidores e podem ser dimensionados para lidar com volumes praticamente ilimitados de armazenamento ou taxa de transferência.

* Para a API do SQL, um contêiner é mapeado para um contêiner.
* Para as contas da API de Cosmos DB para MongoDB, um contêiner é mapeado para uma coleção.
* Para contas Cassandra e API de Tabela, um contêiner é mapeado para uma tabela.
* Para contas de API Gremlin, um contêiner é mapeado para um grafo.

Os contêineres também são as entidades de cobrança para Azure Cosmos DB. Cada contêiner é cobrado por hora, com base na taxa de transferência provisionada e no espaço de armazenamento usado. Para obter mais informações, consulte [preços de Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

### <a name="how-do-i-create-a-database"></a>Como crio uma base de dados?

Você pode criar bancos de dados usando o [portal do Azure](https://portal.azure.com), conforme descrito em [Adicionar um contêiner](create-sql-api-java.md#add-a-container), um dos SDKs de [Azure Cosmos DB](sql-api-sdk-dotnet.md)ou as [APIs REST](/rest/api/cosmos-db/).

### <a name="how-do-i-set-up-users-and-permissions"></a>Como posso configurar utilizadores e permissões?

Você pode criar usuários e permissões usando um dos SDKs de [API Cosmos DB](sql-api-sdk-dotnet.md) ou as [APIs REST](/rest/api/cosmos-db/).

### <a name="does-the-sql-api-support-sql"></a>A API do SQL dá suporte ao SQL?

A linguagem de consulta SQL com suporte das contas da API do SQL é um subconjunto aprimorado da funcionalidade de consulta com suporte pelo SQL Server. A linguagem de consulta do Azure Cosmos DB SQL fornece operadores hierárquicos e relacionais avançados e extensibilidade por meio de UDFs (funções definidas pelo usuário) baseadas em JavaScript. A gramática JSON permite modelar documentos JSON como árvores com nós rotulados, que são usados pelo Azure Cosmos DB técnicas de indexação automática e o dialeto de consulta SQL de Azure Cosmos DB. Para obter informações sobre como usar a gramática SQL, consulte o artigo [consulta SQL][query] .

### <a name="does-the-sql-api-support-sql-aggregation-functions"></a>A API do SQL dá suporte às funções de agregação do SQL?

A API do SQL dá suporte à agregação de baixa latência em qualquer escala por meio de funções de agregação `COUNT`, `MIN`, `MAX`, `AVG` e `SUM` por meio da gramática do SQL. Para obter mais informações, consulte [funções de agregação](sql-query-aggregates.md).

### <a name="how-does-the-sql-api-provide-concurrency"></a>Como a API do SQL fornece simultaneidade?

A API do SQL dá suporte a OCC (controle de simultaneidade otimista) por meio de marcas de entidade HTTP ou ETags. Cada recurso da API do SQL tem uma ETag e a ETag é definida no servidor sempre que um documento é atualizado. O cabeçalho ETag e o valor atual são incluídos em todas as mensagens de resposta. As ETags podem ser usadas com o cabeçalho If-match para permitir que o servidor decida se um recurso deve ser atualizado. O valor de If-match é o valor de ETag a ser verificado. Se o valor de ETag corresponder ao valor de ETag do servidor, o recurso será atualizado. Se a ETag não for mais atual, o servidor rejeitará a operação com um código de resposta "erro de pré-condição HTTP 412". O cliente então busca novamente o recurso para adquirir o valor de ETag atual para o recurso. Além disso, as ETags podem ser usadas com o cabeçalho If-None-Match para determinar se uma rebusca de um recurso é necessária.

Para usar a simultaneidade otimista no .NET, use a classe [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) . Para obter um exemplo do .NET, consulte [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) no exemplo de DocumentManagement no github.

### <a name="how-do-i-perform-transactions-in-the-sql-api"></a>Como fazer executar transações na API do SQL?

A API do SQL dá suporte a transações integradas à linguagem por meio de procedimentos e gatilhos armazenados em JavaScript. Todas as operações de banco de dados dentro de scripts são executadas sob isolamento de instantâneo. Se for um contêiner de partição única, a execução será delimitada para o contêiner. Se o contêiner for particionado, a execução será delimitada para documentos com o mesmo valor de chave de partição no contêiner. Um instantâneo das versões do documento (ETags) é tirado no início da transação e consolidado apenas se o script for concluído com êxito. Se o JavaScript emitir um erro, a transação é revertida. Para obter mais informações, consulte [programação de JavaScript do lado do servidor para Azure Cosmos DB](stored-procedures-triggers-udfs.md).

### <a name="how-can-i-bulk-insert-documents-into-cosmos-db"></a>Como posso inserir documentos em massa no Cosmos DB?

Você pode inserir documentos em massa no Azure Cosmos DB de uma das seguintes maneiras:

* A ferramenta executor em massa, conforme descrito em [usando a biblioteca .net do executor em](bulk-executor-dot-net.md) massa e [usando a biblioteca Java do executor em massa](bulk-executor-java.md)
* A ferramenta de migração de dados, conforme descrito na [ferramenta de migração de banco de dado para Azure Cosmos DB](import-data.md).
* Procedimentos armazenados, conforme descrito na [programação JavaScript do lado do servidor para Azure Cosmos DB](stored-procedures-triggers-udfs.md).

### <a name="does-the-sql-api-support-resource-link-caching"></a>A API do SQL dá suporte ao cache de link de recursos?

Sim, como Azure Cosmos DB é um serviço RESTful, os links de recursos são imutáveis e podem ser armazenados em cache. Os clientes da API do SQL podem especificar um cabeçalho "If-None-Match" para leituras em qualquer documento ou contêiner do tipo recurso e, em seguida, atualizar suas cópias locais depois que a versão do servidor for alterada.

### <a name="is-a-local-instance-of-sql-api-available"></a>Uma instância local da API do SQL está disponível?

Sim. O [emulador de Azure Cosmos DB](local-emulator.md) fornece uma emulação de alta fidelidade do serviço Cosmos DB. Ele dá suporte à funcionalidade que é idêntica à Azure Cosmos DB, incluindo suporte para criar e consultar documentos JSON, provisionamento e dimensionamento de coleções e execução de procedimentos armazenados e gatilhos. Você pode desenvolver e testar aplicativos usando o emulador de Azure Cosmos DB e implantá-los no Azure em uma escala global fazendo uma única alteração de configuração para o ponto de extremidade de conexão para Azure Cosmos DB.

### <a name="why-are-long-floating-point-values-in-a-document-rounded-when-viewed-from-data-explorer-in-the-portal"></a>Por que os valores longos de ponto flutuante em um documento são arredondados quando exibidos no data Explorer no Portal.

Isso é a limitação do JavaScript. O JavaScript usa números de formato de ponto flutuante de precisão dupla conforme especificado no IEEE 754 e pode conter com segurança números entre-(2<sup>53</sup> -1) e 2<sup>53</sup>-1 (ou seja, 9007199254740991) somente.

### <a name="where-are-permissions-allowed-in-the-object-hierarchy"></a>Onde as permissões são permitidas na hierarquia de objetos?

A criação de permissões usando ResourceTokens é permitida no nível de contêiner e seus descendentes (como documentos, anexos). Isso significa que a tentativa de criar uma permissão no banco de dados ou em um nível de conta não é permitida no momento.

## <a name="azure-cosmos-dbs-api-for-mongodb"></a>API do Azure Cosmos DB para MongoDB

### <a name="what-is-the-azure-cosmos-dbs-api-for-mongodb"></a>Qual é a API do Azure Cosmos DB para MongoDB?

A API do Azure Cosmos DB para MongoDB é uma camada de compatibilidade de protocolo Wire que permite que os aplicativos se comuniquem de forma fácil e transparente com o mecanismo de banco de dados nativo do Azure Cosmos usando SDKs e drivers existentes com suporte da Comunidade para o MongoDB. Os desenvolvedores agora podem usar cadeias e habilidades existentes do MongoDB para criar aplicativos que tiram proveito do Azure Cosmos DB. Os desenvolvedores se beneficiam dos recursos exclusivos do Azure Cosmos DB, que incluem a distribuição global com replicação de vários mestres, indexação automática, manutenção de backup, SLAs (contratos de nível de serviço) com suporte financeiro etc.

### <a name="how-do-i-connect-to-my-database"></a>Como fazer conectar-se ao meu banco de dados?

A maneira mais rápida de se conectar a um banco de dados cosmos com a API de Azure Cosmos DB para MongoDB é ir para o [portal do Azure](https://portal.azure.com). Vá para sua conta e, no menu de navegação à esquerda, clique em **início rápido**. O início rápido é a melhor maneira de obter trechos de código para se conectar ao banco de dados.

Azure Cosmos DB impõe requisitos e padrões de segurança estritos. Azure Cosmos DB contas exigem autenticação e comunicação segura via SSL, portanto, certifique-se de usar o TLSv 1.2.

Para obter mais informações, consulte [conectar-se ao banco de dados do cosmos com a API do Azure Cosmos DB para MongoDB](connect-mongodb-account.md).

### <a name="are-there-additional-error-codes-that-i-need-to-deal-with-while-using-azure-cosmos-dbs-api-for-mongodb"></a>Há códigos de erro adicionais com os quais preciso lidar ao usar a API do Azure Cosmos DB para MongoDB?

Junto com os códigos de erro comuns do MongoDB, a API do Azure Cosmos DB para MongoDB tem seus próprios códigos de erro específicos:

| Erro               | Código  | Descrição  | Solução  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | O número total de unidades de solicitação consumidas é maior que a taxa de unidade de solicitação provisionada para o contêiner e foi limitada. | Considere dimensionar a taxa de transferência atribuída a um contêiner ou a um conjunto de contêineres do portal do Azure ou tentar novamente. |
| ExceededMemoryLimit | 16501 | Como um serviço multilocatário, a operação passou pela alocação de memória do cliente. | Reduza o escopo da operação por meio de critérios de consulta mais restritivos ou entre em contato com o suporte da [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). <br><br>Exemplo: <em> @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4dB. GetCollection (' Users '). Aggregate ([<br>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 {$match: {Name: "Andy"}}, <br>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 {$sort: {Age:-1}}<br>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3]) </em>) |

### <a name="is-the-simba-driver-for-mongodb-supported-for-use-with-azure-cosmos-dbs-api-for-mongodb"></a>O driver Simba para MongoDB tem suporte para uso com a API do Azure Cosmos DB para MongoDB?

Sim, você pode usar o driver ODBC do Mongo da Simba com a API do Azure Cosmos DB para MongoDB

## <a id="table"></a>API de Tabela

### <a name="how-can-i-use-the-table-api-offering"></a>Como posso usar a oferta de API de Tabela?

O API de Tabela de Azure Cosmos DB está disponível no [portal do Azure][azure-portal]. Primeiro, você deve se inscrever para uma assinatura do Azure. Depois de se inscrever, você pode adicionar uma conta de API de Tabela Azure Cosmos DB à sua assinatura do Azure e, em seguida, adicionar tabelas à sua conta.

Você pode encontrar os idiomas com suporte e as inicializações rápidas associadas na [introdução ao Azure Cosmos DB API de tabela](table-introduction.md).

### <a name="do-i-need-a-new-sdk-to-use-the-table-api"></a>Preciso de um novo SDK para usar o API de Tabela?

Não, os SDKs de armazenamento existentes ainda devem funcionar. No entanto, é recomendável que um sempre obtenha os SDKs mais recentes para obter o melhor suporte e, em muitos casos, um desempenho superior. Consulte a lista de idiomas disponíveis na [introdução ao Azure Cosmos DB API de tabela](table-introduction.md).

### <a name="where-is-table-api-not-identical-with-azure-table-storage-behavior"></a>Onde API de Tabela não é idêntico ao comportamento do armazenamento de tabelas do Azure?

Há algumas diferenças de comportamento que os usuários provenientes do armazenamento de tabelas do Azure que desejam criar tabelas com o Azure Cosmos DB API de Tabela devem estar cientes:

* Azure Cosmos DB API de Tabela usa um modelo de capacidade reservado para garantir o desempenho garantido, mas isso significa que um paga pela capacidade assim que a tabela é criada, mesmo que a capacidade não esteja sendo usada. Com o armazenamento de tabelas do Azure, um só paga pela capacidade usada. Isso ajuda a explicar por que API de Tabela pode oferecer uma leitura de 10 ms e um SLA de gravação de 15 ms no 99 º percentil, enquanto o armazenamento de tabelas do Azure oferece um SLA de 10 segundos. Mas, como consequência, com API de Tabela tabelas, até mesmo tabelas vazias sem nenhuma solicitação, custam dinheiro para garantir que a capacidade esteja disponível para lidar com todas as solicitações para elas no SLA oferecido pelo Azure Cosmos DB.
* Os resultados da consulta retornados pelo API de Tabela não são classificados em ordem de chave de partição/chave de linha, pois estão no armazenamento de tabelas do Azure.
* As chaves de linha podem ter até 255 bytes
* Os lotes só podem ter até 2 MBs
* Atualmente, não há suporte para CORS
* Os nomes de tabela no armazenamento de tabelas do Azure não diferenciam maiúsculas de minúsculas, mas estão em Azure Cosmos DB API de Tabela
* Alguns dos formatos internos de Azure Cosmos DB para informações de codificação, como campos binários, atualmente não são tão eficientes quanto o que se pode desejar. Portanto, isso pode causar limitações inesperadas no tamanho dos dados. Por exemplo, no momento, não foi possível usar o MB completo de uma entidade de tabela para armazenar dados binários porque a codificação aumenta o tamanho dos dados.
* O nome da propriedade de entidade ' ID ' não tem suporte no momento
* TableQuery TakeCount não está limitado a 1000

Em termos da API REST, há um número de pontos de extremidade/opções de consulta que não têm suporte pelo Azure Cosmos DB API de Tabela:

| Método (s) REST | Opção ponto de extremidade/consulta REST | URLs do documento | Explicação |
| ------------| ------------- | ---------- | ----------- |
| OBTER, COLOCAR | /? ResType = service@comp = Propriedades| [Definir propriedades do serviço tabela](https://docs.microsoft.com/rest/api/storageservices/set-table-service-properties) e [obter propriedades do serviço tabela](https://docs.microsoft.com/rest/api/storageservices/get-table-service-properties) | Esse ponto de extremidade é usado para definir regras de CORS, configuração de análise de armazenamento e configurações de log. Atualmente, o CORS não tem suporte e a análise e o registro em log são tratados de forma diferente em Azure Cosmos DB do que nas tabelas do armazenamento |
| Opções | / @ no__t-1Table-nome-do-recurso > | [Solicitação de tabela CORS de simulação](https://docs.microsoft.com/rest/api/storageservices/preflight-table-request) | Isso faz parte do CORS ao qual Azure Cosmos DB não oferece suporte no momento. |
| GET | /? ResType = service@comp = estatísticas | [Obter estatísticas do serviço tabela](https://docs.microsoft.com/rest/api/storageservices/get-table-service-stats) | Fornece informações sobre como os dados são replicados com rapidez entre primários e secundários. Isso não é necessário na Cosmos DB, pois a replicação faz parte das gravações. |
| OBTER, COLOCAR | /MyTable? comp = ACL | [Obter](https://docs.microsoft.com/rest/api/storageservices/get-table-acl) ACL de tabela e [definir ACL de tabela](https://docs.microsoft.com/rest/api/storageservices/set-table-acl) | Isso obtém e define as políticas de acesso armazenadas usadas para gerenciar assinaturas de acesso compartilhado (SAS). Embora haja suporte para SAS, elas são definidas e gerenciadas de forma diferente. |

Além disso Azure Cosmos DB API de Tabela dá suporte apenas ao formato JSON, não ao ATOM.

Embora Azure Cosmos DB suporte a SAS (assinaturas de acesso compartilhado), há determinadas políticas que não são compatíveis, especificamente aquelas relacionadas a operações de gerenciamento, como o direito de criar novas tabelas.

Para o SDK do .NET em particular, há algumas classes e métodos para os quais Azure Cosmos DB não oferece suporte no momento.

| Classe | Método sem suporte |
|-------|-------- |
| CloudTableClient | \*ServiceProperties * |
|                  | \*ServiceStats * |
| CloudTable | SetPermissions |
|            | GetPermissions |
| TableServiceContext | * (essa classe é preterida) |
| TableServiceEntity | " " |
| TableServiceExtensions | " " |
| TableServiceQuery | " " |

Se qualquer uma dessas diferenças for um problema para seu projeto, entre em contato com [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) e avise-nos.

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>Como fazer fornecer comentários sobre o SDK ou bugs?

Você pode compartilhar seus comentários de qualquer uma das seguintes maneiras:

* [Voz do usuário](https://feedback.azure.com/forums/263030-azure-cosmos-db)
* [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Stack Overflow é melhor para perguntas de programação. Verifique se sua pergunta está [no tópico](https://stackoverflow.com/help/on-topic) e [forneça o máximo de detalhes possível, tornando a pergunta clara e pode ser respondida](https://stackoverflow.com/help/how-to-ask).

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api"></a>Qual é a cadeia de conexão que preciso usar para se conectar ao API de Tabela?

A cadeia de conexão é:

```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmos DB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountName>.table.cosmosdb.azure.com
```

Você pode obter a cadeia de conexão da página cadeia de conexão na portal do Azure.

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-net-sdk-for-the-table-api"></a>Como fazer substituir as configurações de configuração das opções de solicitação no SDK do .NET para o API de Tabela?

Algumas configurações são tratadas no método CreateCloudTableClient e outras por meio do App. config na seção appSettings no aplicativo cliente. Para obter informações sobre configurações de configuração, consulte [recursos de Azure Cosmos DB](tutorial-develop-table-dotnet.md).

### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-azure-table-storage-sdks"></a>Há alguma alteração para os clientes que estão usando os SDKs de armazenamento de tabelas do Azure existentes?

Nenhum. Não há alterações para clientes novos ou existentes que estejam usando os SDKs de armazenamento de tabela do Azure existentes.

### <a name="how-do-i-view-table-data-thats-stored-in-azure-cosmos-db-for-use-with-the-table-api"></a>Como fazer exibir dados de tabela armazenados em Azure Cosmos DB para uso com o API de Tabela?

Você pode usar o portal do Azure para procurar os dados. Você também pode usar o código de API de Tabela ou as ferramentas mencionadas na próxima resposta.

### <a name="which-tools-work-with-the-table-api"></a>Quais ferramentas funcionam com o API de Tabela?

Você pode usar o [Gerenciador de armazenamento do Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

As ferramentas com a flexibilidade para obter uma cadeia de conexão no formato especificado anteriormente podem dar suporte à nova API de Tabela. Uma lista de ferramentas de tabela é fornecida na página [ferramentas de cliente de armazenamento do Azure](../storage/common/storage-explorers.md) .

### <a name="is-the-concurrency-on-operations-controlled"></a>A simultaneidade é controlada por operações?

Sim, a simultaneidade otimista é fornecida por meio do uso do mecanismo ETag.

### <a name="is-the-odata-query-model-supported-for-entities"></a>Há suporte para o modelo de consulta OData para entidades?

Sim, o API de Tabela dá suporte à consulta OData e consulta LINQ.

### <a name="can-i-connect-to-azure-table-storage-and-azure-cosmos-db-table-api-side-by-side-in-the-same-application"></a>Posso me conectar ao armazenamento de tabelas do Azure e Azure Cosmos DB API de Tabela lado a lado no mesmo aplicativo?

Sim, você pode se conectar criando duas instâncias separadas do CloudTableClient, cada uma apontando para seu próprio URI por meio da cadeia de conexão.

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-offering"></a>Como fazer migrar um aplicativo de armazenamento de tabelas do Azure existente para esta oferta?

O [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) e a [ferramenta de migração de dados Azure Cosmos DB](import-data.md) têm suporte.

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>Como a expansão do tamanho do armazenamento é feita para esse serviço se, por exemplo, eu começar com *n* GB de dados e meus dados aumentarem para 1 TB ao longo do tempo?

O Azure Cosmos DB foi projetado para fornecer armazenamento ilimitado por meio do uso de dimensionamento horizontal. O serviço pode monitorar e aumentar efetivamente o armazenamento.

### <a name="how-do-i-monitor-the-table-api-offering"></a>Como fazer monitorar a oferta de API de Tabela?

Você pode usar o painel API de Tabela **métricas** para monitorar as solicitações e o uso do armazenamento.

### <a name="how-do-i-calculate-the-throughput-i-require"></a>Como fazer calcular a taxa de transferência que eu preciso?

Você pode usar o avaliador de capacidade para calcular o TableThroughput necessário para as operações. Para obter mais informações, consulte [estimar unidades de solicitação e armazenamento de dados](https://www.documentdb.com/capacityplanner). Em geral, você pode mostrar sua entidade como JSON e fornecer os números para suas operações.

### <a name="can-i-use-the-table-api-sdk-locally-with-the-emulator"></a>Posso usar o SDK do API de Tabela localmente com o emulador?

Neste momento, não.

### <a name="can-my-existing-application-work-with-the-table-api"></a>Meu aplicativo existente pode funcionar com o API de Tabela?

Sim, há suporte para a mesma API.

### <a name="do-i-need-to-migrate-my-existing-azure-table-storage-applications-to-the-sdk-if-i-dont-want-to-use-the-table-api-features"></a>Preciso migrar meus aplicativos de armazenamento de tabelas do Azure existentes para o SDK se não quiser usar os recursos de API de Tabela?

Não, você pode criar e usar ativos de armazenamento de tabelas do Azure existentes sem interrupção de nenhum tipo. No entanto, se você não usar o API de Tabela, não poderá se beneficiar do índice automático, da opção de consistência adicional ou da distribuição global.

### <a name="how-do-i-add-replication-of-the-data-in-the-table-api-across-more-than-one-region-of-azure"></a>Como fazer adicionar a replicação dos dados no API de Tabela em mais de uma região do Azure?

Você pode usar as [configurações de replicação global](tutorial-global-distribution-sql-api.md#portal) do portal de Azure Cosmos DB para adicionar regiões adequadas para seu aplicativo. Para desenvolver um aplicativo distribuído globalmente, você também deve adicionar seu aplicativo com o conjunto de informações PreferredLocation à região local para fornecer baixa latência de leitura.

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-table-api"></a>Como fazer alterar a região de gravação primária da conta na API de Tabela?

Você pode usar o painel Azure Cosmos DB portal de replicação global para adicionar uma região e, em seguida, fazer failover para a região necessária. Para obter instruções, consulte [desenvolvendo com contas de Azure Cosmos DB de várias regiões](high-availability.md).

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>Como fazer configurar minhas regiões de leitura preferenciais para baixa latência quando eu distribuir meus dados?

Para ajudar a ler a partir do local local, use a chave PreferredLocation no arquivo app. config. Para aplicativos existentes, o API de Tabela gera um erro se Locationmode estiver definido. Remova esse código, pois o API de Tabela pega essas informações do arquivo app. config. 

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api"></a>Como devo pensar nos níveis de consistência na API de Tabela?

O Azure Cosmos DB fornece compensações bem ponderadas entre consistência, disponibilidade e latência. O Azure Cosmos DB oferece cinco níveis de consistência para API de Tabela desenvolvedores, para que você possa escolher o modelo de consistência certo no nível da tabela e fazer solicitações individuais ao consultar os dados. Quando um cliente se conecta, ele pode especificar um nível de consistência. Você pode alterar o nível por meio do argumento consistencyLevel de CreateCloudTableClient.

O API de Tabela fornece leituras de baixa latência com "ler suas próprias gravações", com consistência de desatualização limitada como padrão. Para obter mais informações, consulte [níveis de consistência](consistency-levels.md).

Por padrão, o armazenamento de tabelas do Azure fornece uma consistência forte em uma região e consistência eventual nos locais secundários.

### <a name="does-azure-cosmos-db-table-api-offer-more-consistency-levels-than-azure-table-storage"></a>O Azure Cosmos DB API de Tabela oferece mais níveis de consistência do que o armazenamento de tabelas do Azure?

Sim, para obter informações sobre como se beneficiar da natureza distribuída do Azure Cosmos DB, consulte [níveis de consistência](consistency-levels.md). Como as garantias são fornecidas para os níveis de consistência, você pode usá-las com confiança.

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>Quando a distribuição global está habilitada, quanto tempo leva para replicar os dados?

Azure Cosmos DB confirma os dados permanentemente na região local e envia os dados para outras regiões imediatamente em questão de milissegundos. Essa replicação é dependente apenas do RTT (tempo de ida e volta) do datacenter. Para saber mais sobre a capacidade de distribuição global do Azure Cosmos DB, confira [Azure Cosmos DB: um serviço de banco de dados distribuído globalmente no Azure](distribute-data-globally.md).

### <a name="can-the-read-request-consistency-level-be-changed"></a>O nível de consistência da solicitação de leitura pode ser alterado?

Com Azure Cosmos DB, você pode definir o nível de consistência no nível de contêiner (na tabela). Usando o SDK do .NET, você pode alterar o nível fornecendo o valor para a chave TableConsistencyLevel no arquivo app. config. Os valores possíveis são: Strong, desatualização limitada, sessão, prefixo consistente e eventual. Para obter mais informações, consulte [níveis de consistência de dados ajustáveis em Azure Cosmos DB](consistency-levels.md). A ideia principal é que você não pode definir o nível de consistência da solicitação em mais do que a configuração da tabela. Por exemplo, você não pode definir o nível de consistência para a tabela em eventual e o nível de consistência da solicitação em alta.

### <a name="how-does-the-table-api-handle-failover-if-a-region-goes-down"></a>Como o API de Tabela lida com failover se uma região fica inoperante?

O API de Tabela aproveita a plataforma distribuída globalmente do Azure Cosmos DB. Para garantir que seu aplicativo possa tolerar o tempo de inatividade do datacenter, habilite pelo menos mais uma região para a conta no portal de Azure Cosmos DB [desenvolvendo com contas de Azure Cosmos DB de várias regiões](high-availability.md). Você pode definir a prioridade da região usando o portal [desenvolvendo com contas de Azure Cosmos DB de várias regiões](high-availability.md).

Você pode adicionar quantas regiões desejar para a conta e o controle para o qual ela pode fazer failover fornecendo uma prioridade de failover. Para usar o banco de dados, você precisa fornecer um aplicativo também. Quando você fizer isso, seus clientes não terão tempo de inatividade. O [SDK do cliente .net mais recente](table-sdk-dotnet.md) é a hospedagem automática, mas os outros SDKs não são. Ou seja, ele pode detectar a região que está inoperante e fazer failover automaticamente para a nova região.

### <a name="is-the-table-api-enabled-for-backups"></a>O API de Tabela está habilitado para backups?

Sim, o API de Tabela aproveita a plataforma de Azure Cosmos DB para backups. Os backups são feitos automaticamente. Para obter mais informações, consulte [backup e restauração online com o Azure Cosmos DB](online-backup-and-restore.md).

### <a name="does-the-table-api-index-all-attributes-of-an-entity-by-default"></a>O API de Tabela indexa todos os atributos de uma entidade por padrão?

Sim, todos os atributos de uma entidade são indexados por padrão. Para obter mais informações, consulte [Azure Cosmos DB: políticas de indexação](index-policy.md).

### <a name="does-this-mean-i-dont-have-to-create-more-than-one-index-to-satisfy-the-queries"></a>Isso significa que não preciso criar mais de um índice para atender às consultas?

Sim, Azure Cosmos DB API de Tabela fornece a indexação automática de todos os atributos sem nenhuma definição de esquema. Essa automação libera os desenvolvedores para se concentrarem no aplicativo, e não na criação e no gerenciamento de índices. Para obter mais informações, consulte [Azure Cosmos DB: políticas de indexação](index-policy.md).

### <a name="can-i-change-the-indexing-policy"></a>Posso alterar a política de indexação?

Sim, você pode alterar a política de indexação fornecendo a definição do índice. Você precisa codificar e escapar corretamente as configurações.

Para os SDKs do non-.NET, a política de indexação só pode ser definida no portal em **Data Explorer**, navegue até a tabela específica que você deseja alterar e, em seguida, vá para a **escala & configurações**-> política de indexação, faça a alteração desejada e, em seguida, **salve** .

No SDK do .NET, ele pode ser enviado no arquivo app. config:

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

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-sorting-aggregates-hierarchy-and-other-functionality-will-you-be-adding-these-capabilities-to-the-table-api"></a>Azure Cosmos DB como uma plataforma parece ter muitos recursos, como classificação, agregações, hierarquia e outras funcionalidades. Você estará adicionando esses recursos ao API de Tabela?

O API de Tabela fornece a mesma funcionalidade de consulta que o armazenamento de tabelas do Azure. Também suporta ordenação, agregações, consultas geoespaciais, hierarquias e um vasto conjunto de funções incorporadas. Para obter mais informações, consulte [consultas SQL](how-to-sql-query.md).

### <a name="when-should-i-change-tablethroughput-for-the-table-api"></a>Quando devo alterar TableThroughput para o API de Tabela?

Você deve alterar TableThroughput quando uma das seguintes condições se aplicar:

* Você está executando um ETL (extração, transformação e carregamento) de dados ou deseja carregar muitos dados em um curto período de tempo.
* Você precisa de mais taxa de transferência do contêiner ou de um conjunto de contêineres no back-end. Por exemplo, você verá que a taxa de transferência usada é mais do que a taxa de transferência provisionada e que você está ficando limitado. Para obter mais informações, consulte [definir taxa de transferência para contêineres de Cosmos do Azure](set-throughput.md).

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-table"></a>Posso escalar ou reduzir verticalmente a taxa de transferência da minha tabela de API de Tabela?

Sim, você pode usar o painel escala do Azure Cosmos DB portal para dimensionar a taxa de transferência. Para obter mais informações, consulte [definir taxa de transferência](set-throughput.md).

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>É um conjunto de TableThroughput padrão para tabelas provisionadas recentemente?

Sim, se você não substituir o TableThroughput por meio de App. config e não usar um contêiner criado previamente em Azure Cosmos DB, o serviço criará uma tabela com a taxa de transferência de 400.

### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-azure-table-storage-service"></a>Há alguma alteração de preço para os clientes existentes do serviço de armazenamento de tabelas do Azure?

Nenhum. Não há nenhuma alteração no preço para clientes existentes do armazenamento de tabelas do Azure.

### <a name="how-is-the-price-calculated-for-the-table-api"></a>Como o preço é calculado para o API de Tabela?

O preço depende do TableThroughput alocado.

### <a name="how-do-i-handle-any-rate-limiting-on-the-tables-in-table-api-offering"></a>Como fazer lidar com qualquer limitação de taxa nas tabelas na oferta de API de Tabela?

Se a taxa de solicitação for maior que a capacidade da taxa de transferência provisionada para o contêiner subjacente ou um conjunto de contêineres, você receberá um erro e o SDK tentará novamente a chamada aplicando a política de repetição.

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-table-api-offering-of-azure-cosmos-db"></a>Por que preciso escolher uma taxa de transferência além de PartitionKey e RowKey para aproveitar a oferta API de Tabela de Azure Cosmos DB?

Azure Cosmos DB define uma taxa de transferência padrão para seu contêiner se você não fornecer um no arquivo app. config ou por meio do Portal.

Azure Cosmos DB fornece garantias de desempenho e latência, com limites superiores na operação. Essa garantia é possível quando o mecanismo pode impor a governança nas operações do locatário. A definição de TableThroughput garante que você obtenha a taxa de transferência e a latência garantidas, porque a plataforma reserva essa capacidade e garante o êxito operacional.

Usando a especificação de taxa de transferência, você pode alterá-la de forma elástica para se beneficiar da sazonalidade de seu aplicativo, atender às necessidades de produtividade e economizar custos.

### <a name="azure-table-storage-has-been-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-azure-cosmos-db-table-api-offering-seems-to-be-charging-me-even-though-i-havent-performed-a-single-transaction-or-stored-anything-can-you-explain"></a>O armazenamento de tabelas do Azure foi barato para mim, porque paguei apenas para armazenar os dados e eu raramente a consulta. A oferta de API de Tabela Azure Cosmos DB parece estar se cobrando mesmo que eu não tenha realizado uma única transação nem armazenado nada. Você pode explicar?

O Azure Cosmos DB foi projetado para ser um sistema baseado em SLA, distribuído globalmente, com garantias de disponibilidade, latência e taxa de transferência. Quando você reserva a taxa de transferência na Azure Cosmos DB, ela é garantida, ao contrário da taxa de transferência de outros sistemas. Azure Cosmos DB fornece recursos adicionais que os clientes solicitaram, como índices secundários e distribuição global.

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-azure-table-storage-with-the-table-api-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>Nunca recebo uma notificação de uma cota cheia (indicando que uma partição está cheia) quando ingerir dados no armazenamento de tabelas do Azure. Com o API de Tabela, eu obtenho essa mensagem. Essa oferta está me limitando e me forçando a alterar meu aplicativo existente?

O Azure Cosmos DB é um sistema baseado em SLA que fornece escala ilimitada, com garantias de latência, taxa de transferência, disponibilidade e consistência. Para garantir o desempenho premium garantido, verifique se o tamanho e o índice dos dados são gerenciáveis e escalonáveis. O limite de 10 GB no número de entidades ou itens por chave de partição é garantir que possamos fornecer um ótimo desempenho de pesquisa e consulta. Para garantir que seu aplicativo dimensione bem, mesmo para o armazenamento do Azure, recomendamos que você *não* crie uma partição ativa armazenando todas as informações em uma partição e consultando-as.

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-table-api"></a>Portanto, PartitionKey e RowKey ainda são necessários com o API de Tabela?

Sim. Como a área de superfície da API de Tabela é semelhante à do SDK de armazenamento de tabelas do Azure, a chave de partição fornece uma maneira eficiente de distribuir os dados. A chave de linha é exclusiva dentro dessa partição. A chave de linha precisa estar presente e não pode ser nula como no SDK padrão. O comprimento de RowKey é de 255 bytes e o comprimento de PartitionKey é 1 KB.

### <a name="what-are-the-error-messages-for-the-table-api"></a>Quais são as mensagens de erro do API de Tabela?

O armazenamento de tabelas do Azure e Azure Cosmos DB API de Tabela usar os mesmos SDKs, de modo que a maioria dos erros será a mesma.

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api"></a>Por que estou limitado quando tento criar muitas tabelas uma após a outra na API de Tabela?

O Azure Cosmos DB é um sistema baseado em SLA que fornece garantia de latência, taxa de transferência, disponibilidade e consistência. Como ele é um sistema provisionado, ele reserva recursos para garantir esses requisitos. A taxa rápida de criação de tabelas é detectada e limitada. Recomendamos que você examine a taxa de criação de tabelas e reduza-a para menos de 5 por minuto. Lembre-se de que o API de Tabela é um sistema provisionado. No momento em que provisioná-lo, você começará a pagar por ele.

## <a name="gremlin-api"></a>API do Gremlin

### <a name="for-cnet-development-should-i-use-the-microsoftazuregraphs-package-or-gremlinnet"></a>Para C#o desenvolvimento de/.net, devo usar o pacote Microsoft. Azure. graphs ou Gremlin.net?

Azure Cosmos DB API Gremlin aproveita os drivers de software livre como os conectores principais para o serviço. Portanto, a opção recomendada é usar [drivers compatíveis com o Apache Tinkerpop](https://tinkerpop.apache.org/).

### <a name="how-are-rus-charged-when-running-queries-on-a-graph-database"></a>Como as RU/s são cobradas durante a execução de consultas em um banco de dados de grafo?

Todos os objetos, vértices e bordas do grafo são mostrados como documentos JSON no back-end. Como uma consulta Gremlin pode modificar um ou vários objetos de grafo de cada vez, o custo associado a ele está diretamente relacionado aos objetos, bordas que são processadas pela consulta. Esse é o mesmo processo que o Azure Cosmos DB usa para todas as outras APIs. Para obter mais informações, consulte [unidades de solicitação no Azure Cosmos DB](request-units.md).

O encargo de RU é baseado no conjunto de dados de trabalho da passagem e não no conjunto de resultados. Por exemplo, se uma consulta pretende obter um único vértice como resultado, mas precisa atravessar mais de um outro objeto no caminho, o custo será baseado em todos os objetos de grafo que serão necessários para calcular o vértice de um resultado.

### <a name="whats-the-maximum-scale-that-a-graph-database-can-have-in-azure-cosmos-db-gremlin-api"></a>Qual é a escala máxima que um banco de dados de grafo pode ter em Azure Cosmos DB API Gremlin?

Azure Cosmos DB usa o [particionamento horizontal](partition-data.md) para abordar automaticamente o aumento nos requisitos de armazenamento e taxa de transferência. A taxa de transferência máxima e a capacidade de armazenamento de uma carga de trabalho são determinadas pelo número de partições associadas a um determinado contêiner. No entanto, um contêiner de API do Gremlin tem um conjunto específico de diretrizes para garantir uma experiência de desempenho adequada em escala. Para obter mais informações sobre particionamento e práticas recomendadas, consulte o artigo sobre [particionamento no Azure Cosmos DB](partition-data.md) .

### <a name="how-can-i-protect-against-injection-attacks-using-gremlin-drivers"></a>Como posso proteger contra ataques de injeção usando drivers Gremlin?

A maioria dos drivers Tinkerpop Gremlin do Apache nativos permitem a opção de fornecer um dicionário de parâmetros para a execução da consulta. Este é um exemplo de como fazer isso no [Gremlin.net](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet) e no [Gremlin-JavaScript](https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started/blob/master/app.js).

### <a name="why-am-i-getting-the-gremlin-query-compilation-error-unable-to-find-any-method-error"></a>Por que estou recebendo o erro "erro de compilação de consulta Gremlin: não foi possível encontrar nenhum método"?

Azure Cosmos DB API Gremlin implementa um subconjunto da funcionalidade definida na área de superfície Gremlin. Para obter as etapas com suporte e obter mais informações, consulte o artigo de [suporte do Gremlin](gremlin-support.md) .

A melhor solução alternativa é reescrever as etapas necessárias do Gremlin com a funcionalidade com suporte, já que todas as etapas essenciais do Gremlin são suportadas pelo Azure Cosmos DB.

### <a name="why-am-i-getting-the-websocketexception-the-server-returned-status-code-200-when-status-code-101-was-expected-error"></a>Por que estou recebendo o "websocketexception: o servidor retornou o código de status ' 200 ' quando o código de status ' 101 ' era esperado"?

Esse erro é provavelmente gerado quando o ponto de extremidade incorreto está sendo usado. O ponto de extremidade que gera esse erro tem o seguinte padrão:

`https:// YOUR_DATABASE_ACCOUNT.documents.azure.com:443/`

Este é o ponto de extremidade de documentos para seu banco de dados de grafo.  O ponto de extremidade correto a ser usado é o ponto de extremidade Gremlin, que tem o seguinte formato:

`https://YOUR_DATABASE_ACCOUNT.gremlin.cosmosdb.azure.com:443/`

### <a name="why-am-i-getting-the-requestrateistoolarge-error"></a>Por que estou recebendo o erro "RequestRateIsTooLarge"?

Esse erro significa que as unidades de solicitação alocadas por segundo não são suficientes para atender à consulta. Esse erro geralmente é visto quando você executa uma consulta que obtém todos os vértices:

```
// Query example:
g.V()
```

Essa consulta tentará recuperar todos os vértices do grafo. Portanto, o custo dessa consulta será igual ao mínimo do número de vértices em termos de RUs. A configuração de RU/s deve ser ajustada para resolver essa consulta.

### <a name="why-do-my-gremlin-driver-connections-get-dropped-eventually"></a>Por que minhas conexões de driver Gremlin são removidas eventualmente?

Uma conexão Gremlin é feita por meio de uma conexão WebSocket. Embora as conexões WebSocket não tenham uma vida útil específica, Azure Cosmos DB API Gremlin encerrará conexões ociosas após 30 minutos de inatividade.

### <a name="why-cant-i-use-fluent-api-calls-in-the-native-gremlin-drivers"></a>Por que não consigo usar chamadas de API fluentes nos drivers Gremlin nativos?

As chamadas à API fluente ainda não têm suporte pela API do Azure Cosmos DB Gremlin. Chamadas à API fluentes exigem um recurso de formatação interno conhecido como suporte a código de bytes que atualmente não tem suporte pela API Gremlin do Azure Cosmos DB. Devido ao mesmo motivo, o driver Gremlin-JavaScript mais recente também não tem suporte no momento.

### <a name="how-can-i-evaluate-the-efficiency-of-my-gremlin-queries"></a>Como posso avaliar a eficiência das minhas consultas do Gremlin?

A etapa de visualização **executionProfile ()** pode ser usada para fornecer uma análise do plano de execução de consulta. Esta etapa precisa ser adicionada ao final de qualquer consulta Gremlin, conforme ilustrado pelo exemplo a seguir:

**Exemplo de consulta**

```
g.V('mary').out('knows').executionProfile()
```

**Exemplo de saída**

```json
[
  {
    "gremlin": "g.V('mary').out('knows').executionProfile()",
    "totalTime": 8,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 3,
        "annotations": {
          "percentTime": 37.5
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 62.5
        },
        "counts": {
          "resultCount": 0
        },
        "storeOps": [
          {
            "count": 0,
            "size": 0,
            "time": 0.6
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      }
    ]
  }
]
```

A saída do perfil acima mostra quanto tempo é gasto para obter os objetos de vértice, os objetos de borda e o tamanho do conjunto de dados de trabalho. Isso está relacionado às medidas de custo padrão para consultas de Azure Cosmos DB.

## <a id="cassandra"></a>API do Cassandra

### <a name="what-is-the-protocol-version-supported-by-azure-cosmso-db-cassandra-api-is-there-a-plan-to-support-other-protocols"></a>Qual é a versão de protocolo com suporte pelo Azure Cosmso API do Cassandra DB? Há um plano para dar suporte a outros protocolos?

API do Apache Cassandra para Azure Cosmos DB dá suporte à versão 4 do CQL hoje. Se você tiver comentários sobre como dar suporte a outros protocolos, avise-nos por meio [dos comentários do User Voice](https://feedback.azure.com/forums/263030-azure-cosmos-db) ou envie um email para [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com).

### <a name="why-is-choosing-a-throughput-for-a-table-a-requirement"></a>Por que a escolha de uma taxa de transferência para uma tabela é um requisito?

Azure Cosmos DB define a taxa de transferência padrão para seu contêiner com base em onde você cria a tabela do-portal ou CQL.
Azure Cosmos DB fornece garantias de desempenho e latência, com limites superiores na operação. Essa garantia é possível quando o mecanismo pode impor a governança nas operações do locatário. A configuração da taxa de transferência garante que você obtenha a taxa de transferência e a latência garantidas, porque a plataforma reserva essa capacidade e garante o êxito da operação.
Você pode alterar a taxa de transferência de forma elástica para se beneficiar da sazonalidade de seu aplicativo e economizar custos.

O conceito de taxa de transferência é explicado nas [unidades de solicitação no artigo Azure Cosmos DB](request-units.md) . A taxa de transferência de uma tabela é distribuída entre as partições físicas subjacentes igualmente.

### <a name="what-is-the-default-rus-of-table-when-created-through-cql-what-if-i-need-to-change-it"></a>Qual é o RU/s padrão da tabela quando criada por meio de CQL? What If preciso alterá-lo?

O Azure Cosmos DB usa unidades de solicitação por segundo (RU/s) como uma moeda para fornecer a taxa de transferência. As tabelas criadas por meio de CQL têm 400 RU. Você pode alterar a RU do Portal.

CQL

```
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

### <a name="what-happens-when-throughput-is-used-up"></a>O que acontece quando a taxa de transferência é usada?

Azure Cosmos DB fornece garantias de desempenho e latência, com limites superiores na operação. Essa garantia é possível quando o mecanismo pode impor a governança nas operações do locatário. Isso é possível com base na definição da taxa de transferência, o que garante que você obtenha a taxa de transferência e a latência garantidas, porque a plataforma reserva essa capacidade e garante o êxito da operação.
Ao passar por essa capacidade, você receberá uma mensagem de erro sobrecarregada indicando que a capacidade foi usada.
0x1001 sobrecarregado: a solicitação não pode ser processada porque "a taxa de solicitação é grande". Neste momento, é essencial ver quais operações e seu volume causam esse problema. Você pode ter uma ideia da capacidade consumida de passar pela capacidade provisionada com métricas no Portal. Em seguida, você precisa garantir que a capacidade seja consumida quase igualmente em todas as partições subjacentes. Se você vir a maior parte da taxa de transferência é consumida por uma partição, você tem uma distorção de carga de trabalho.

Há métricas disponíveis que mostram como a taxa de transferência é usada em horas, dias e por sete dias, em partições ou em agregação. Para obter mais informações, consulte [monitorando e Depurando com métricas em Azure Cosmos DB](use-metrics.md).

Os logs de diagnóstico são explicados no artigo [Azure Cosmos DB log de diagnóstico](logging.md) .

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>A chave primária é mapeada para o conceito de chave de partição de Azure Cosmos DB?

Sim, a chave de partição é usada para posicionar a entidade no local certo. No Azure Cosmos DB, ele é usado para localizar a partição lógica correta que é armazenada em uma partição física. O conceito de particionamento é bem explicado na [partição e escala no artigo Azure Cosmos DB](partition-data.md) . O essencial é que uma partição lógica não deve ultrapassar o limite de 10 GB hoje.

### <a name="what-happens-when-i-get-a-quota-full-notification-indicating-that-a-partition-is-full"></a>O que acontece quando obtenho uma cota completa ", indicando que uma partição está cheia?

O Azure Cosmos DB é um sistema baseado em SLA que fornece escala ilimitada, com garantias de latência, taxa de transferência, disponibilidade e consistência. Esse armazenamento ilimitado baseia-se na escala horizontal dos dados usando o particionamento como o conceito-chave. O conceito de particionamento é bem explicado na [partição e escala no artigo Azure Cosmos DB](partition-data.md) .

O limite de 10 GB no número de entidades ou itens por partição lógica que você deve aderir. Para garantir que seu aplicativo seja dimensionado bem, recomendamos que você *não* crie uma partição ativa armazenando todas as informações em uma partição e consultando-as. Esse erro só poderá ser dado se os dados estiverem distorcidos: ou seja, se você tiver muitos dados para uma chave de partição (mais de 10 @ no__t-0GB). Você pode encontrar a distribuição de dados usando o portal de armazenamento. A maneira de corrigir esse erro é recriar a tabela e escolher uma primária granular (chave de partição), o que permite uma melhor distribuição dos dados.

### <a name="is-it-possible-to-use-cassandra-api-as-key-value-store-with-millions-or-billions-of-individual-partition-keys"></a>É possível usar API do Cassandra como armazenamento de valor de chave com milhões ou bilhões de chaves de partição individuais?

Azure Cosmos DB pode armazenar dados ilimitados expandindo o armazenamento. Isso é independente da taxa de transferência. Sim, você sempre pode usar apenas API do Cassandra para armazenar e recuperar chaves/valores especificando a chave primária/de partição correta. Essas chaves individuais obtêm sua própria partição lógica e ficam acima da partição física sem problemas.

### <a name="is-it-possible-to-create-more-than-one-table-with-apache-cassandra-api-of-azure-cosmos-db"></a>É possível criar mais de uma tabela com API do Apache Cassandra de Azure Cosmos DB?

Sim, é possível criar mais de uma tabela com API do Apache Cassandra. Cada uma dessas tabelas é tratada como uma unidade para taxa de transferência e armazenamento.

### <a name="is-it-possible-to-create-more-than-one-table-in-succession"></a>É possível criar mais de uma tabela sucessivamente?

Azure Cosmos DB é um sistema controlado por recursos para atividades de plano de controle e de dados. Contêineres como coleções, tabelas são entidades de tempo de execução que são provisionadas para determinada capacidade de taxa de transferência. A criação desses contêineres em sucessão rápida não é uma atividade esperada e limitada. Se você tiver testes que descartam/criam tabelas imediatamente, tente espaçar.

### <a name="what-is-maximum-number-of-tables-that-can-be-created"></a>O que é o número máximo de tabelas que podem ser criadas?

Não há nenhum limite físico no número de tabelas, envie um email em [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) se você tiver um grande número de tabelas (em que o tamanho constante total vai mais de 10 TB de dados) que precisam ser criados a partir de 10s ou 100s usuais.

### <a name="what-is-the-maximum--of-keyspace-that-we-can-create"></a>Qual é o número máximo de keyspace que podemos criar?

Não há nenhum limite físico para o número de keyspaces, pois eles são contêineres de metadados, envie um email em [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) se você tiver um grande número de keyspaces por algum motivo.

### <a name="is-it-possible-to-bring-in-lot-of-data-after-starting-from-normal-table"></a>É possível colocar muitos dados depois de iniciar da tabela normal?

A capacidade de armazenamento é gerenciada automaticamente e aumenta à medida que você envia mais dados por push. Portanto, você pode importar com segurança tantos dados quantos forem necessários sem gerenciar e provisionar nós e muito mais.

### <a name="is-it-possible-to-supply-yaml-file-settings-to-configure-apache-casssandra-api-of-azure-cosmos-db-behavior"></a>É possível fornecer configurações de arquivo YAML para configurar a API do Apache Casssandra do comportamento Azure Cosmos DB?

API do Apache Cassandra de Azure Cosmos DB é um serviço de plataforma. Ele fornece compatibilidade no nível de protocolo para executar operações. Ele oculta a complexidade do gerenciamento, do monitoramento e da configuração. Como desenvolvedor/usuário, você não precisa se preocupar com a disponibilidade, marcas para exclusão, cache de chave, cache de linha, filtro de flor e diversas outras configurações. Os API do Apache Cassandra de Azure Cosmos DB se concentram em fornecer desempenho de leitura e gravação que você precisa sem a sobrecarga de configuração e gerenciamento.

### <a name="will-apache-cassandra-api-for-azure-cosmos-db-support-node-additioncluster-statusnode-status-commands"></a>API do Apache Cassandra Azure Cosmos DB comandos de status de nó/status de cluster de adição/nó de suporte?

O API do Apache Cassandra é um serviço de plataforma que torna o planejamento de capacidade, respondendo às demandas de elasticidade para taxa de transferência & armazenamento de uma Breeze. Com Azure Cosmos DB você provisiona a taxa de transferência, precisa. Em seguida, você pode dimensioná-lo para cima e para baixo várias vezes no dia sem se preocupar em adicionar/excluir nós ou gerenciá-los. Isso implica que você não precisa usar o nó, a ferramenta de gerenciamento de cluster também.

### <a name="what-happens-with-respect-to-various-config-settings-for-keyspace-creation-like-simplenetwork"></a>O que acontece com relação a várias definições de configuração para a criação de keyspace como simples/rede?

O Azure Cosmos DB fornece distribuição global pronta para uso por motivos de baixa latência e disponibilidade. Você não precisa configurar réplicas ou outras coisas. Todas as gravações são sempre o quorum permanentemente confirmado em qualquer região em que você escreve enquanto fornece garantias de desempenho.

### <a name="what-happens-with-respect-to-various-settings-for-table-metadata-like-bloom-filter-caching-read-repair-change-gc_grace-compression-memtable_flush_period-and-more"></a>O que acontece com relação a várias configurações para metadados de tabela como filtro de flor, cache, alteração de reparo de leitura, gc_grace, compactação memtable_flush_period e muito mais?

Azure Cosmos DB fornece desempenho para leituras/gravações e taxa de transferência sem necessidade de tocar em qualquer um dos parâmetros de configuração e manipulá-los acidentalmente.

### <a name="is-time-to-live-ttl-supported-for-cassandra-tables"></a>O tempo de vida (TTL) tem suporte para tabelas Cassandra?

Sim, há suporte para TTL.

### <a name="is-it-possible-to-monitor-node-status-replica-status-gc-and-os-parameters-earlier-with-various-tools-what-needs-to-be-monitored-now"></a>É possível monitorar o status do nó, o status da réplica, o GC e OS parâmetros de so anteriormente com várias ferramentas? O que precisa ser monitorado agora?

O Azure Cosmos DB é um serviço de plataforma que ajuda você a aumentar a produtividade e não se preocupar com o gerenciamento e monitoramento da infraestrutura. Você só precisa cuidar da taxa de transferência disponível nas métricas do portal para descobrir se você está ficando limitado e aumentar ou diminuir essa taxa de transferência.
Monitorar [SLAs](monitor-accounts.md).
Usar [métricas](use-metrics.md) use [logs de diagnóstico](logging.md).

### <a name="which-client-sdks-can-work-with-apache-cassandra-api-of-azure-cosmos-db"></a>Quais SDKs de cliente podem trabalhar com API do Apache Cassandra de Azure Cosmos DB?

Os drivers de cliente do Apache Cassandra SDK que usam CQLv3 foram usados para programas cliente. Se você tiver outros drivers que você usa ou se estiver enfrentando problemas, envie um email para [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com).

### <a name="is-composite-partition-key-supported"></a>Há suporte para chave de partição composta?

Sim, você pode usar a sintaxe regular para criar a chave de partição composta.

### <a name="can-i-use-sstableloader-for-data-loading"></a>Posso usar sstableloader para carregamento de dados?

Não, não há suporte para sstableloader.

### <a name="can-an-on-premises-apache-cassandra-cluster-be-paired-with-azure-cosmos-dbs-cassandra-api"></a>Um cluster apache Cassandra local pode ser emparelhado com API do Cassandra do Azure Cosmos DB?

No momento, a Azure Cosmos DB tem uma experiência otimizada para o ambiente de nuvem sem sobrecarga de operações. Se você precisar de emparelhamento, envie um email para [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) com uma descrição do seu cenário. Estamos trabalhando na oferta para ajudar a emparelhar o cluster Cassandra de nuvem local/diferente para o API do Cassandra do Cosomos DB.

### <a name="does-cassandra-api-provide-full-backups"></a>API do Cassandra fornecer backups completos?

Azure Cosmos DB fornece dois backups completos gratuitos feitos no intervalo de quatro horas hoje em todas as APIs. Isso garante que você não precise configurar um agendamento de backup e outras coisas.
Se você quiser modificar a retenção e a frequência, envie um email para [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) ou gere um caso de suporte. Informações sobre o recurso de backup são fornecidas no artigo [backup e restauração online automáticos com Azure Cosmos DB](online-backup-and-restore.md) .

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>Como a conta de API do Cassandra lida com o failover se uma região fica inativa?

O Azure Cosmos DB API do Cassandra emprestado da plataforma de Azure Cosmos DB distribuída globalmente. Para garantir que seu aplicativo possa tolerar o tempo de inatividade do datacenter, habilite pelo menos mais uma região para a conta no portal de Azure Cosmos DB [desenvolvendo com contas de Azure Cosmos DB de várias regiões](high-availability.md). Você pode definir a prioridade da região usando o portal [desenvolvendo com contas de Azure Cosmos DB de várias regiões](high-availability.md).

Você pode adicionar quantas regiões desejar para a conta e o controle para o qual ela pode fazer failover fornecendo uma prioridade de failover. Para usar o banco de dados, você precisa fornecer um aplicativo também. Quando você fizer isso, seus clientes não terão tempo de inatividade.

### <a name="does-the-apache-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>O API do Apache Cassandra indexa todos os atributos de uma entidade por padrão?

API do Cassandra está planejando dar suporte à indexação secundária para ajudar a criar um índice seletivo em determinados atributos. 


### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>Posso usar o novo SDK do API do Cassandra localmente com o emulador?

Sim, há suporte para isso.

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-change-feed-and-other-functionality-will-these-capabilities-be-added-to-the-cassandra-api"></a>Azure Cosmos DB como uma plataforma parece ter muitos recursos, como o feed de alterações e outras funcionalidades. Esses recursos serão adicionados ao API do Cassandra?

O API do Apache Cassandra fornece a mesma funcionalidade de CQL que o Apache Cassandra. Planejamos examinar a viabilidade de dar suporte a vários recursos no futuro.

### <a name="feature-x-of-regular-cassandra-api-isnt-working-as-today-where-can-the-feedback-be-provided"></a>O recurso x de API do Cassandra regular não está funcionando como hoje, onde os comentários podem ser fornecidos?

Forneça comentários por meio de [comentários de voz do usuário](https://feedback.azure.com/forums/263030-azure-cosmos-db).

[azure-portal]: https://portal.azure.com
[query]: sql-api-sql-query.md
