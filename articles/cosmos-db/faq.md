---
title: Perguntas frequentes sobre diferentes APIs em Azure Cosmos DB
description: Obtenha respostas a perguntas frequentes sobre o Azure Cosmos DB, um serviço de base de dados multimodelo distribuído globalmente. Aprenda sobre capacidade, níveis de desempenho e escala.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 976ff4d3be194bf33d2c01f06c66782caf8dad50
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82608052"
---
# <a name="frequently-asked-questions-about-different-apis-in-azure-cosmos-db"></a>Perguntas mais frequentes sobre as diferentes APIs no Azure Cosmos DB

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Quais são os casos de uso típico para o Azure Cosmos DB?

O Azure Cosmos DB é uma boa escolha para novas aplicações web, mobile, gaming e IoT onde a escala automática, desempenho previsível, ordem rápida de tempos de resposta milisegundos, e a capacidade de consultar dados livres de esquemas é importante. A Azure Cosmos DB presta-se ao rápido desenvolvimento e ao apoio à continuação da iteração dos modelos de dados de aplicação. As aplicações que gerem conteúdos e dados gerados pelo utilizador são [casos de utilização comuns para o Azure Cosmos DB](use-cases.md).

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Como é que a Azure Cosmos DB oferece um desempenho previsível?

Uma unidade de [pedido](request-units.md) (RU) é a medida de entrada em Azure Cosmos DB. Uma entrada de 1RU corresponde à entrada do GET de um documento de 1 KB. Todas as operações em Azure Cosmos DB, incluindo leituras, escritos, consultas SQL e execuções de procedimentos armazenados, têm um valor determinístico de RU que é baseado na entrada necessária para completar a operação. Em vez de pensar em CPU, IO e memória e como cada um deles afeta a sua entrada na sua aplicação, pode pensar em termos de uma única medida de RU.

Pode configurar cada recipiente Azure Cosmos com entrada aprovisionada em termos de RUs de entrada por segundo. Para aplicações de qualquer escala, pode comparar pedidos individuais para medir os seus valores ru, e fornecer um recipiente para lidar com o total de unidades de pedido em todos os pedidos. Também pode aumentar ou reduzir a entrada do seu contentor à medida que as necessidades da sua aplicação evoluem. Para mais informações sobre as unidades de pedido e para obter ajuda na determinação das necessidades do seu contentor, experimente a [calculadora](https://www.documentdb.com/capacityplanner)de entrada .

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>Como é que o Azure Cosmos DB suporta vários modelos de dados, tais como chave/valor, colunaar, documento e gráfico?

Os modelos de dados chave/valor (tabela), colunaar, documento e gráfico são suportados de forma nativa devido ao design ARS (átomos, registos e sequências) em que o Azure Cosmos DB é construído. Átomos, registos e sequências podem ser facilmente mapeados e projetados para vários modelos de dados. As APIs para um subconjunto de modelos estão disponíveis neste momento (SQL, MongoDB, Table e Gremlin) e outros modelos específicos para modelos de dados adicionais estarão disponíveis no futuro.

A Azure Cosmos DB tem um motor de indexação agnóstico schema capaz de indexar automaticamente todos os dados que ingere sem necessitar de qualquer esquema ou índices secundários do desenvolvedor. O motor baseia-se num conjunto de layouts de índicelógico (invertidos, colunaares, árvores) que separam o layout de armazenamento dos subsistemas de processamento de índices e consultas. Cosmos DB também tem a capacidade de suportar um conjunto de protocolos de arame e APIs de uma forma extensível e traduzi-los eficientemente para o modelo de dados core (1) e os layouts lógicos do índice (2) tornando-o único capaz de suportar mais do que um modelo de dados de forma nativa.

### <a name="can-i-use-multiple-apis-to-access-my-data"></a>Posso usar várias APIs para aceder aos meus dados?

O Azure Cosmos DB é o serviço de base de dados multimodelo distribuído globalmente pela Microsoft. Quando o multi-modelo significa que o Azure Cosmos DB suporta várias APIs e vários modelos de dados, diferentes APIs usam diferentes formatos de dados para armazenamento e protocolo de arame. Por exemplo, a SQL utiliza jSON, MongoDB usa BSON, Table usa EDM, Cassandra usa CQL, Gremlin usa formato JSON. Como resultado, recomendamos a utilização da mesma API para todo o acesso aos dados numa determinada conta.

Cada API opera de forma independente, com exceção da API Gremlin e SQL, que são interoperáveis.

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>O Azure Cosmos DB HIPAA está em conformidade?

Sim, o Azure Cosmos DB é compatível com a HIPAA. A HIPAA estabelece requisitos para a utilização, divulgação e salvaguarda das informações médicas pessoalmente identificáveis. Para obter mais informações, consulte o [Centro de Fidedignidade da Microsoft](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA).

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>Quais são os limites de armazenamento do Azure Cosmos DB?

Não há limite para a quantidade total de dados que um contentor pode armazenar em Azure Cosmos DB.

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>Quais são os limites de entrada do Azure Cosmos DB?

Não há limite para a quantidade total de entrada que um contentor pode suportar em Azure Cosmos DB. A ideia-chave é distribuir a sua carga de trabalho aproximadamente uniformemente entre um número suficientemente grande de teclas de partição.

### <a name="are-direct-and-gateway-connectivity-modes-encrypted"></a>Os modos de conectividade Direct e Gateway estão encriptados?

Sim, ambos os modos estão sempre totalmente encriptados.

### <a name="how-much-does-azure-cosmos-db-cost"></a>Quanto custa o Azure Cosmos DB?

Para mais detalhes, consulte a página de detalhes de [preços do Azure Cosmos DB.](https://azure.microsoft.com/pricing/details/cosmos-db/) As taxas de utilização da Azure Cosmos DB são determinadas pelo número de contentores aprovisionados, pelo número de horas que os contentores estavam online e pela entrada prevista para cada contentor.

### <a name="is-a-free-account-available"></a>Uma conta gratuita está disponível?

Sim, pode inscrever-se para uma conta limitada sem custos, sem compromisso. Para se inscrever, visite [Try Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) ou leia mais no [Try Azure Cosmos DB FAQ](#try-cosmos-db).

Se é novo no Azure, pode inscrever-se numa [conta azure gratuita](https://azure.microsoft.com/free/), que lhe dá 30 dias e um crédito para experimentar todos os serviços do Azure. Se tiver uma subscrição do Visual Studio, também pode ser utilizado [créditos Azure gratuitos](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) em qualquer serviço Azure.

Também pode utilizar o [Emulador Db Azure Cosmos](local-emulator.md) para desenvolver e testar a sua aplicação localmente gratuitamente, sem criar uma subscrição Azure. Quando estiver satisfeito com o funcionamento da sua aplicação no Emulador do Azure Cosmos DB, pode mudar e começar a utilizar uma conta do Azure Cosmos DB na cloud.

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>Como posso obter ajuda adicional com o Azure Cosmos DB?

Para fazer uma pergunta técnica, pode colocar num destes dois fóruns de perguntas e respostas:

* [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Transbordo de pilhas.](https://stackoverflow.com/questions/tagged/azure-cosmosdb) Stack Overflow é o melhor para questões de programação. Certifique-se de que a sua pergunta está [no tópico](https://stackoverflow.com/help/on-topic) e forneça o máximo de [detalhes possível, tornando a questão clara e responsável.](https://stackoverflow.com/help/how-to-ask)

Para solicitar novas funcionalidades, crie um novo pedido na [voz do Utilizador.](https://feedback.azure.com/forums/263030-azure-cosmos-db)

Para corrigir um problema na sua conta, crie um [pedido de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) no portal do Azure.

## <a name="try-azure-cosmos-db-subscriptions"></a><a id="try-cosmos-db"></a>Experimente as subscrições do Azure Cosmos DB

Pode agora desfrutar de uma experiência De Iacosmos DB limitada no tempo sem uma subscrição, gratuitamente e compromissos. Para se inscrever para uma subscrição try Azure Cosmos DB, vá ao [Try Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) e use qualquer conta pessoal da Microsoft (MSA). Esta subscrição é separada do [Azure Free Trial,](https://azure.microsoft.com/free/)e pode ser usada juntamente com um Azure Free Trial ou uma subscrição paga azure.

As subscrições do Azure Cosmos DB aparecem no portal Azure, a seguir outras subscrições associadas ao seu ID de utilizador.

As seguintes condições aplicam-se às subscrições do Try Azure Cosmos DB:

* O acesso à conta pode ser concedido a contas pessoais da Microsoft (MSA). Evite utilizar contas ou contas de Diretório Ativo (AAD) pertencentes a inquilinos empresariais da AAD, podem ter limitações em vigor que possam bloquear a concessão de acesso.
* Um [recipiente de entrada aprovisionado](./set-throughput.md#set-throughput-on-a-container) por subscrição para contas SQL, Gremlin API e Tabela.
* Até três [cobranças de entrada previstas](./set-throughput.md#set-throughput-on-a-container) por subscrição para contas MongoDB.
* Uma [base de dados disponibilizada](./set-throughput.md#set-throughput-on-a-database) por subscrição. As bases de dados aprovisionadas em entrada podem conter qualquer número de contentores no seu interior.
* Capacidade de armazenamento de 10 GB.
* A replicação global está disponível nas [seguintes regiões de Azure](https://azure.microsoft.com/regions/): Centro dos EUA, Norte da Europa e Sudeste Asiático
* Potência máxima de 5 K RU/s quando aprovisionada ao nível do contentor.
* Entrada máxima de 20 K RU/s quando aprovisionada ao nível da base de dados.
* As inscrições expiram após 30 dias, podendo ser estendidas para um máximo de 31 dias no total.
* Os bilhetes de apoio Azure não podem ser criados para contas Try Azure Cosmos DB; no entanto, é prestado apoio aos assinantes com planos de apoio existentes.

## <a name="set-up-azure-cosmos-db"></a>Configurar O Azure Cosmos DB

### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>Como me inscrevo no Azure Cosmos DB?

O Azure Cosmos DB está disponível no portal Azure. Primeiro, inscreva-se para uma assinatura Azure. Depois de se ter inscrito, pode adicionar uma conta Azure Cosmos DB à sua subscrição Azure.

### <a name="what-is-a-master-key"></a>O que é uma chave mestra?

Uma chave mestra é um token de segurança para aceder a todos os recursos de uma conta. Os indivíduos com a chave leram e escrevem acesso a todos os recursos na conta de base de dados. Tenha cuidado quando distribuir as teclas principais. A chave principal principal e a chave principal master secundária estão disponíveis na lâmina **keys** do [portal Azure][azure-portal]. Para mais informações sobre chaves, consulte [Ver, copiar e voltar a gerar chaves de acesso](manage-with-cli.md#list-account-keys).

### <a name="what-are-the-regions-that-preferredlocations-can-be-set-to"></a>Quais são as regiões a que as PreferredLocations podem ser definidas?

O valor PreferredLocations pode ser definido para qualquer uma das regiões azure em que cosmos DB está disponível. Para obter uma lista das regiões disponíveis, consulte [as regiões de Azure.](https://azure.microsoft.com/regions/)

### <a name="is-there-anything-i-should-be-aware-of-when-distributing-data-across-the-world-via-the-azure-datacenters"></a>Há alguma coisa que eu deva saber ao distribuir dados por todo o mundo através dos centros de dados do Azure?

O Azure Cosmos DB está presente em todas as regiões do Azure, conforme especificado na página das [regiões de Azure.](https://azure.microsoft.com/regions/) Como é o serviço principal, cada novo datacenter tem uma presença de Azure Cosmos DB.

Quando definir uma região, lembre-se que o Azure Cosmos DB respeita as nuvens soberanas e governamentais. Ou seja, se criarmos uma conta numa [região soberana,](https://azure.microsoft.com/global-infrastructure/)não podemos replicar-nos daquela [região soberana.](https://azure.microsoft.com/global-infrastructure/) Da mesma forma, não se pode permitir a replicação em outros locais soberanos a partir de uma conta externa.

### <a name="is-it-possible-to-switch-from-container-level-throughput-provisioning-to-database-level-throughput-provisioning-or-vice-versa"></a>É possível passar do fornecimento de entrada de nível de contentor para o fornecimento de entrada de nível de base de dados? Ou vice-versa

O fornecimento de entrada de nível de contentores e de base de dados são ofertas separadas e alternar entre qualquer uma delas requer dados migratórios de origem para destino. O que significa que você precisa criar uma nova base de dados ou um novo recipiente e depois migrar dados usando [biblioteca de executor a granel](bulk-executor-overview.md) ou [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).

### <a name="does-azure-cosmosdb-support-time-series-analysis"></a>A Azure CosmosDB suporta a análise da série temporal?

Yes Azure CosmosDB suporta a análise da série de tempo, aqui está uma amostra para o padrão da [série de tempo.](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/samples/Patterns) Esta amostra mostra como usar o feed de mudança para construir vistas agregadas ao longo de dados da série de tempo. Pode alargar esta abordagem utilizando o streaming de faíscas ou outro processador de dados de fluxo.

## <a name="what-are-the-azure-cosmos-db-service-quotas-and-throughput-limits"></a>Quais são as quotas de serviço Azure Cosmos DB e limites de produção

Consulte as quotas de [serviço](concepts-limits.md) Azure Cosmos DB e ao longo [dos limites por contentor e](set-throughput.md#comparison-of-models) artigos de base de dados para obter mais informações.

## <a name="frequently-asked-questions-about-sql-api"></a><a id="sql-api-faq"></a>Perguntas frequentes sobre a SQL API

### <a name="how-do-i-start-developing-against-the-sql-api"></a>Como posso começar a desenvolver-me contra a API SQL?

Primeiro deve inscrever-se para uma assinatura Azure. Assim que se inscrever para uma subscrição do Azure, pode adicionar um recipiente SQL API à sua subscrição Azure. Para obter instruções sobre a adição de uma conta Azure Cosmos DB, consulte Criar uma conta de [base de dados Azure Cosmos](create-sql-api-dotnet.md#create-account).

Existem [SDKs](sql-api-sdk-dotnet.md) disponíveis para .NET, Python, Node.js, JavaScript e Java. Os desenvolvedores também podem usar as [APIs RESTful HTTP](/rest/api/cosmos-db/) para interagir com os recursos da Azure Cosmos DB de várias plataformas e idiomas.

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>Posso aceder a algumas amostras prontas para começar?

As amostras para os SQL API [.NET,](sql-api-dotnet-samples.md) [Java,](https://github.com/Azure/azure-documentdb-java) [Node.js](sql-api-nodejs-samples.md)e [Python](sql-api-python-samples.md) SDKs estão disponíveis no GitHub.

### <a name="does-the-sql-api-database-support-schema-free-data"></a>A base de dados SQL API suporta dados isentos de esquemas?

Sim, a API SQL permite aplicações para armazenar documentos JSON arbitrários sem definições de esquemas ou sugestões. Os dados estão imediatamente disponíveis para consulta através da interface de consulta Azure Cosmos DB SQL.

### <a name="does-the-sql-api-support-acid-transactions"></a>O SQL API suporta transações acidas?

Sim, a API SQL suporta transações de documentos cruzados expressas como procedimentos e gatilhos armazenados pelo JavaScript. As transações são traçadas para uma única divisória dentro de cada recipiente e executadas com semântica ACID como "tudo ou nada", isoladas de outros pedidos de código e de utilizador de execução simultânea. Se forem lançadas exceções através da execução do código de aplicação JavaScript do lado do servidor, toda a transação é reposta. 

### <a name="what-is-a-container"></a>O que é um contentor?

Um recipiente é um grupo de documentos e a sua lógica de aplicação JavaScript associada. Um recipiente é uma entidade faturada, onde o [custo](performance-levels.md) é determinado pela entrada e armazenamento usado. Os contentores podem abranger uma ou mais divisórias ou servidores e podem escalar para lidar com volumes praticamente ilimitados de armazenamento ou de entrada.

* Para a SQL API, um recipiente mapeia para um recipiente.
* Para a API da Cosmos DB para contas MongoDB, um contentor mapeia para uma Coleção.
* Para as contas da Cassandra e da Table API, um contentor mapeia para uma mesa.
* Para as contas da API Gremlin, um contentor mapeia um gráfico.

Os contentores são também as entidades de faturação da Azure Cosmos DB. Cada recipiente é faturado de hora em hora, com base na entrada prevista e no espaço de armazenamento usado. Para mais informações, consulte [o Preço db da Azure Cosmos.](https://azure.microsoft.com/pricing/details/cosmos-db/)

### <a name="how-do-i-create-a-database"></a>Como crio uma base de dados?

Pode criar bases de dados utilizando o [portal Azure](https://portal.azure.com), como descrito em [Adicionar um recipiente,](create-sql-api-java.md#add-a-container)um dos [SDKs DB Do Cosmos Azure,](sql-api-sdk-dotnet.md)ou as [APIs REST](/rest/api/cosmos-db/).

### <a name="how-do-i-set-up-users-and-permissions"></a>Como posso configurar utilizadores e permissões?

Pode criar utilizadores e permissões utilizando um dos [SDKs Cosmos DB API](sql-api-sdk-dotnet.md) ou os [APIs rest](/rest/api/cosmos-db/).

### <a name="does-the-sql-api-support-sql"></a>O SQL API suporta SQL?

A linguagem de consulta SQL suportada por contas SQL API é um subconjunto melhorado da funcionalidade de consulta que é suportada pelo SQL Server. A linguagem de consulta Azure Cosmos DB SQL fornece operadores hierárquicos e relacionais ricos e extensibility através de funções definidas pelo javaScript (UDFs). A gramática JSON permite modelar documentos JSON como árvores com nós rotulados, que são usados tanto pelas técnicas de indexação automática Azure Cosmos DB como pelo dialeto de consulta SQL de Azure Cosmos DB. Para obter informações sobre a utilização da gramática SQL, consulte o artigo [SQL Query.][query]

### <a name="does-the-sql-api-support-sql-aggregation-functions"></a>O SQL API suporta funções de agregação SQL?

A API SQL suporta a agregação de baixa latência `MAX`em `AVG`qualquer `SUM` escala através de funções `COUNT`agregadas, `MIN`, , e através da gramática SQL. Para mais informações, consulte [as funções agregadas.](sql-query-aggregates.md)

### <a name="how-does-the-sql-api-provide-concurrency"></a>Como é que a API SQL fornece conmoeda?

O SQL API suporta um controlo de moeda otimista (OCC) através de tags de entidades HTTP, ou ETags. Todos os recursos da SQL API têm um ETag, e o ETag é definido no servidor sempre que um documento é atualizado. O cabeçalho ETag e o valor atual estão incluídos em todas as mensagens de resposta. Os ETags podem ser utilizados com o cabeçalho If-Match para permitir que o servidor decida se um recurso deve ser atualizado. O valor If-Match é o valor ETag a ser verificado. Se o valor ETag corresponder ao valor ETag do servidor, o recurso é atualizado. Se o ETag já não estiver atual, o servidor rejeita a operação com um código de resposta "HTTP 412 Precondition failure". Em seguida, o cliente rebusca o recurso para adquirir o valor Atual ETag para o recurso. Além disso, os ETags podem ser usados com o cabeçalho If-None-Match para determinar se é necessária uma rebusca de um recurso.

Para utilizar uma conmoeda otimista em .NET, utilize a classe [AccessCondition.](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) Para obter uma amostra .NET, consulte [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) na amostra documentmanagement no GitHub.

### <a name="how-do-i-perform-transactions-in-the-sql-api"></a>Como faço transações na API SQL?

A SQL API suporta transações integradas em linguagem através de procedimentos e gatilhos armazenados pelo JavaScript. Todas as operações de base de dados dentro dos scripts são executadas sob isolamento instantâneo. Se for um recipiente de partição única, a execução é orientada para o contentor. Se o recipiente for dividido, a execução é remeter-se em documentos com o mesmo valor de divisória dentro do recipiente. Um instantâneo das versões do documento (ETags) é tirado no início da transação e consolidado apenas se o script for concluído com êxito. Se o JavaScript emitir um erro, a transação é revertida. Para mais informações, consulte [a programação JavaScript do lado do Servidor para O Azure Cosmos DB](stored-procedures-triggers-udfs.md).

### <a name="how-can-i-bulk-insert-documents-into-cosmos-db"></a>Como posso inserir documentos em massa no Cosmos DB?

Pode inserir documentos em massa no Azure Cosmos DB de uma das seguintes formas:

* A ferramenta executor a granel, como descrito na [biblioteca do executor](bulk-executor-dot-net.md) a granel .NET e usando biblioteca java [executor a granel](bulk-executor-java.md)
* A ferramenta de migração de dados, como descrito na ferramenta de migração de base de [dados para o Azure Cosmos DB](import-data.md).
* Procedimentos armazenados, conforme descrito na [programação JavaScript do lado do servidor para O Azure Cosmos DB](stored-procedures-triggers-udfs.md).

### <a name="does-the-sql-api-support-resource-link-caching"></a>O SQL API suporta o caching de ligação de recursos?

Sim, porque o Azure Cosmos DB é um serviço RESTful, as ligações de recursos são imutáveis e podem ser emcache. Os clientes da SQL API podem especificar um cabeçalho "If-None-Match" para leituras contra qualquer documento ou contentor semelhante a recursos e, em seguida, atualizar as suas cópias locais após a alteração da versão do servidor.

### <a name="is-a-local-instance-of-sql-api-available"></a>Uma instância local de SQL API está disponível?

Sim. O [Emulador DB azure Cosmos](local-emulator.md) proporciona uma emulação de alta fidelidade do serviço Cosmos DB. Suporta funcionalidades idênticas à Azure Cosmos DB, incluindo suporte para a criação e consulta de documentos JSON, recolhas de fornecimento e escalagem, e execução de procedimentos e gatilhos armazenados. Você pode desenvolver e testar aplicações usando o Emulator Db Azure Cosmos, e implantá-los para Azure à escala global, fazendo uma única alteração de configuração para o ponto final de ligação para Azure Cosmos DB.

### <a name="why-are-long-floating-point-values-in-a-document-rounded-when-viewed-from-data-explorer-in-the-portal"></a>Por que são longos valores de ponto flutuante num documento arredondado quando visto do explorador de dados no portal.

Isto é limitação do JavaScript. O JavaScript utiliza números de formato de ponto flutuante de dupla precisão, conforme especificado no IEEE 754 e pode segurar com segurança os números entre -(2<sup>53</sup> - 1) e 2<sup>53</sup>-1 (isto é, 9007199254740991) apenas.

### <a name="where-are-permissions-allowed-in-the-object-hierarchy"></a>Onde são permitidas permissões na hierarquia do objeto?

A criação de permissões utilizando ResourceTokens é permitida ao nível do contentor e seus descendentes (tais como documentos, anexos). Isto implica que tentar criar uma permissão na base de dados ou um nível de conta não é atualmente permitido.

[azure-portal]: https://portal.azure.com
[query]: sql-api-sql-query.md

## <a name="next-steps"></a>Passos seguintes

Para aprender sobre perguntas frequentes em outras APIs, consulte:

* Perguntas frequentes sobre a [API da Azure Cosmos DB para o MongoDB](mongodb-api-faq.md)
* Perguntas frequentes sobre [a API Gremlin em Azure Cosmos DB](gremlin-api-faq.md)
* Perguntas frequentes sobre [Cassandra API em Azure Cosmos DB](cassandra-faq.md)
* Perguntas frequentes sobre [a Tabela API em Azure Cosmos DB](table-api-faq.md)
