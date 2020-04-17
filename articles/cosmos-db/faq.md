---
title: Perguntas frequentes sobre diferentes APIs em Azure Cosmos DB
description: Obtenha respostas a perguntas frequentes sobre o Azure Cosmos DB, um serviço de base de dados multimodelo distribuído globalmente. Aprenda sobre capacidade, níveis de desempenho e escala.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: e904f650c9fc11aab2dcb002ac68a9f19287940e
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81449957"
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

## <a name="sql-api"></a>SQL API

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

## <a name="azure-cosmos-dbs-api-for-mongodb"></a>API do Azure Cosmos DB para MongoDB

### <a name="what-is-the-azure-cosmos-dbs-api-for-mongodb"></a>O que é a API do Azure Cosmos DB para o MongoDB?

A API do Azure Cosmos DB para MongoDB é uma camada de compatibilidade de protocolo sinuoso que permite às aplicações comunicar de forma fácil e transparente com o motor de base de dados nativo Azure Cosmos utilizando SDKs e condutores existentes e apoiados pela comunidade para o MongoDB. Os desenvolvedores podem agora usar as cadeias de ferramentas e competências mongoDB existentes para construir aplicações que tirem partido do Azure Cosmos DB. Os desenvolvedores beneficiam das capacidades únicas do Azure Cosmos DB, que incluem distribuição global com replicação multi-master, indexação automática, manutenção de backup, acordos de nível de serviço apoiados financeiramente (SLAs) etc.

### <a name="how-do-i-connect-to-my-database"></a>Como me ligo à minha base de dados?

A forma mais rápida de se ligar a uma base de dados da Cosmos com a API da Azure Cosmos DB para o MongoDB é dirigir-se ao [portal Azure.](https://portal.azure.com) Vá à sua conta e, em seguida, no menu de navegação à esquerda, clique em **Quick Start**. O Quickstart é a melhor forma de obter códigos para ligar à sua base de dados.

A Azure Cosmos DB impõe requisitos e padrões de segurança rigorosos. As contas Azure Cosmos DB requerem autenticação e comunicação segura via TLS, por isso certifique-se de usar TLSv1.2.

Para mais informações, consulte [Connect to your Cosmos database with Azure Cosmos DB's API for MongoDB](connect-mongodb-account.md).

### <a name="are-there-additional-error-codes-that-i-need-to-deal-with-while-using-azure-cosmos-dbs-api-for-mongodb"></a>Há códigos de erro adicionais com os quais preciso de lidar enquanto uso a API da Azure Cosmos DB para o MongoDB?

Juntamente com os códigos de erro comuns do MongoDB, a API do Azure Cosmos DB para a MongoDB tem os seus próprios códigos de erro específicos:

| Erro               | Código  | Descrição  | Solução  |
|---------------------|-------|--------------|-----------|
| Pedidos De TooMany     | 16500 | O número total de unidades de pedido consumidas é superior à taxa de unidade de pedido prevista para o contentor e foi estrangulada. | Considere escalpelizar a entrada atribuída a um recipiente ou a um conjunto de recipientes do portal Azure ou a tentar novamente. |
| LimiteMemóriaExcedido | 16501 | Como serviço multi-inquilinos, a operação passou por cima do loteamento de memória do cliente. | Reduzir o âmbito da operação através de critérios de consulta mais restritivos ou suporte de contacto do [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). <br><br><em> &nbsp; &nbsp;Exemplo: &nbsp; &nbsp;db.getCollection ('users')agregado([['users').([['users').agregado([['users'([['users'([['users<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$match: {name: "Andy"}}}, <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$sort: {idade: -1}}<br>&nbsp;&nbsp;&nbsp;&nbsp;])</em>) |

### <a name="is-the-simba-driver-for-mongodb-supported-for-use-with-azure-cosmos-dbs-api-for-mongodb"></a>O condutor da Simba para a MongoDB é suportado para uso com a API da Azure Cosmos DB para o MongoDB?

Sim, você pode usar o motorista Mongo ODBC de Simba com API da Azure Cosmos DB para MongoDB

## <a name="table-api"></a><a id="table"></a>API de Tabela

### <a name="how-can-i-use-the-table-api-offering"></a>Como posso usar a oferta da API da tabela?

A API da Tabela Azure Cosmos DB está disponível no [portal Azure.][azure-portal] Primeiro deve inscrever-se para uma assinatura Azure. Depois de se ter inscrito, pode adicionar uma conta API de Table API da Mesa Azure Cosmos à sua subscrição Azure e, em seguida, adicionar tabelas à sua conta.

Você pode encontrar as línguas suportadas e quick-starts associados na [Introdução à Mesa DB Do Cosmos Azure](table-introduction.md).

### <a name="do-i-need-a-new-sdk-to-use-the-table-api"></a>Preciso de um novo SDK para usar a API da tabela?

Não, os SDKs de armazenamento existentes ainda devem funcionar. No entanto, recomenda-se que se obtenha sempre os Mais recentes SDKs para o melhor suporte e, em muitos casos, um desempenho superior. Consulte a lista de idiomas disponíveis na [Introdução à Tabela API do Azure Cosmos DB](table-introduction.md).

### <a name="where-is-table-api-not-identical-with-azure-table-storage-behavior"></a>Em que medida a API de Tabela não é idêntica ao comportamento de armazenamento de Tabelas do Azure?

Existem algumas diferenças de comportamento que os utilizadores provenientes do armazenamento da Mesa Azure que querem criar tabelas com a API de Mesa DB Do Cosmos Azure devem estar cientes de:

* A API da Tabela Db da Azure Cosmos utiliza um modelo de capacidade reservado para garantir um desempenho garantido, mas isso significa que se paga a capacidade assim que a mesa é criada, mesmo que a capacidade não esteja a ser utilizada. Com o armazenamento da Mesa Azure só se paga pela capacidade que é usada. Isto ajuda a explicar por que a Table API pode oferecer uma leitura de 10 ms e 15 ms escrever SLA no percentil 99, enquanto o armazenamento da Mesa Azure oferece um SLA de 10 segundos. Mas, como consequência, com as mesas da Tabela API, mesmo mesas vazias sem quaisquer pedidos, custam dinheiro para garantir que a capacidade está disponível para lidar com quaisquer pedidos no SLA oferecido pela Azure Cosmos DB.
* Os resultados de consulta devolvidos pela Tabela API não estão classificados na chave/ordem da chave da divisória como estão no armazenamento da Mesa Azure.
* As teclas de fila só podem ser até 255 bytes
* Lotes só podem ter até 2 MBs
* Cors não é atualmente apoiado
* Os nomes de mesa no armazenamento da mesa Azure não são sensíveis a casos, mas estão na Mesa DDD Da Azure Cosmos
* Alguns dos formatos internos da Azure Cosmos DB para codificar informações, como campos binários, não são atualmente tão eficientes como se poderia gostar. Portanto, isto pode causar limitações inesperadas no tamanho dos dados. Por exemplo, atualmente não se poderia usar a Meg completa de uma entidade de mesa para armazenar dados binários porque a codificação aumenta o tamanho dos dados.
* Nome de propriedade da entidade 'ID' atualmente não suportado
* TableQuery TakeCount não se limita a 1000

Em termos da API REST existem uma série de opções finais/consulta que não são suportadas pela Azure Cosmos DB Table API:

| Métodos de repouso | Opção Fim do Descanso/Consulta | Doc URLs | Explicação |
| ------------| ------------- | ---------- | ----------- |
| GET, PUT | /?restype=service@comp=propriedades| [Definir propriedades](https://docs.microsoft.com/rest/api/storageservices/set-table-service-properties) do serviço de mesa e [obter propriedades de serviço](https://docs.microsoft.com/rest/api/storageservices/get-table-service-properties) de mesa | Este ponto final é utilizado para definir regras CORS, configuração de análise de armazenamento e definições de registo. Atualmente, o CORS não é suportado e a análise e a exploração madeireira são manuseadas de forma diferente em Azure Cosmos DB do que em Mesas de Armazenamento Azure |
| Opções | /\<> de nome de recursos de mesa | [Pedido de mesa cors pré-voo](https://docs.microsoft.com/rest/api/storageservices/preflight-table-request) | Isto faz parte do CORS que a Azure Cosmos DB não suporta atualmente. |
| GET | /?restype=service@comp=estatísticas | [Obtenha estatísticas de serviço de mesa](https://docs.microsoft.com/rest/api/storageservices/get-table-service-stats) | Fornece informações a rapidez com que os dados se estão a replicar entre primários e secundários. Isto não é necessário em Cosmos DB, já que a replicação faz parte das escritas. |
| GET, PUT | /mytable?comp=acl | [Obtenha mesa ACL](https://docs.microsoft.com/rest/api/storageservices/get-table-acl) e [set table ACL](https://docs.microsoft.com/rest/api/storageservices/set-table-acl) | Isto obtém e define as políticas de acesso armazenadas usadas para gerir assinaturas de acesso partilhado (SAS). Embora o SAS seja apoiado, são definidos e geridos de forma diferente. |

Além disso, a API da Tabela DB da Azure Cosmos suporta apenas o formato JSON, e não a ATOM.

Embora a Azure Cosmos DB apoie as Assinaturas de Acesso Partilhada (SAS) existem certas políticas que não apoia, especificamente as relacionadas com operações de gestão como o direito de criar novas tabelas.

Para o .NET SDK em particular, existem algumas classes e métodos que o Azure Cosmos DB não suporta atualmente.

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

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>Como posso dar feedback sobre o SDK ou bugs?

Pode partilhar o seu feedback de qualquer uma das seguintes formas:

* [Voz do utilizador](https://feedback.azure.com/forums/263030-azure-cosmos-db)
* [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Transbordo de pilhas.](https://stackoverflow.com/questions/tagged/azure-cosmosdb) Stack Overflow é o melhor para questões de programação. Certifique-se de que a sua pergunta está [no tópico](https://stackoverflow.com/help/on-topic) e forneça o máximo de [detalhes possível, tornando a questão clara e responsável.](https://stackoverflow.com/help/how-to-ask)

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api"></a>Qual é a cadeia de ligação que preciso de usar para ligar à API da tabela?

A corda de ligação é:

```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmos DB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountName>.table.cosmosdb.azure.com
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

## <a name="gremlin-api"></a>API do Gremlin

### <a name="for-cnet-development-should-i-use-the-microsoftazuregraphs-package-or-gremlinnet"></a>Para o desenvolvimento de C#/.NET, devo usar o pacote Microsoft.Azure.Graphs ou Gremlin.NET?

A Azure Cosmos DB Gremlin API aproveita os condutores de código aberto como os principais conectores do serviço. Assim, a opção recomendada é utilizar [os condutores que são suportados por Apache Tinkerpop](https://tinkerpop.apache.org/).

### <a name="how-are-rus-charged-when-running-queries-on-a-graph-database"></a>Como é que o RU/s é carregado ao executar consultas numa base de dados de gráficos?

Todos os objetos gráficos, vértices e bordas, são mostrados como documentos JSON no backend. Uma vez que uma consulta de Gremlin pode modificar um ou muitos objetos gráficos de cada vez, o custo associado a ele está diretamente relacionado com os objetos, bordas que são processadas pela consulta. Este é o mesmo processo que o Azure Cosmos DB usa para todas as outras APIs. Para obter mais informações, veja [Unidades de Pedido no Azure Cosmos DB](request-units.md).

A carga RU baseia-se no conjunto de dados de trabalho da travessia, e não no conjunto de resultados. Por exemplo, se uma consulta pretende obter um único vértice como resultado, mas precisa atravessar mais do que um outro objeto no caminho, então o custo será baseado em todos os objetos gráficos que será necessário para calcular o vértice de um resultado.

### <a name="whats-the-maximum-scale-that-a-graph-database-can-have-in-azure-cosmos-db-gremlin-api"></a>Qual é a escala máxima que uma base de dados de gráficos pode ter na API Do Azure Cosmos DB Gremlin?

A Azure Cosmos DB utiliza [a partilha horizontal](partition-data.md) para abordar automaticamente o aumento dos requisitos de armazenamento e de entrada. A capacidade máxima de entrada e armazenamento de uma carga de trabalho é determinada pelo número de divisórias associadas a um determinado recipiente. No entanto, um recipiente Gremlin API tem um conjunto específico de diretrizes para garantir uma experiência de desempenho adequada em escala. Para obter mais informações sobre divisórias e boas práticas, consulte a partilha no artigo [da Azure Cosmos DB.](partition-data.md)

### <a name="how-can-i-protect-against-injection-attacks-using-gremlin-drivers"></a>Como posso proteger-me contra ataques de injeção usando condutores gremlin?

A maioria dos condutores nativos apache Tinkerpop Gremlin permitem a opção de fornecer um dicionário de parâmetros para a execução de consulta. Este é um exemplo de como fazê-lo em [Gremlin.Net](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet) e em [Gremlin-Javascript.](https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started/blob/master/app.js)

### <a name="why-am-i-getting-the-gremlin-query-compilation-error-unable-to-find-any-method-error"></a>Porque é que estou a ter o erro de compilação de consultas de Gremlin: incapaz de encontrar qualquer método?

A Azure Cosmos DB Gremlin API implementa um subconjunto da funcionalidade definida na área de superfície de Gremlin. Para passos apoiados e mais informações, consulte o artigo de [apoio da Gremlin.](gremlin-support.md)

A melhor suposições é reescrever os passos gremlin necessários com a funcionalidade suportada, uma vez que todos os passos essenciais de Gremlin são suportados pela Azure Cosmos DB.

### <a name="why-am-i-getting-the-websocketexception-the-server-returned-status-code-200-when-status-code-101-was-expected-error"></a>Porque é que estou a receber o erro "WebSocketException: O servidor devolveu o código de estado '200' quando era esperado o código de estado '101'?

Este erro é provavelmente lançado quando o ponto final errado está a ser utilizado. O ponto final que gera este erro tem o seguinte padrão:

`https:// YOUR_DATABASE_ACCOUNT.documents.azure.com:443/`

Este é o ponto final dos documentos para a sua base de dados de gráficos.  O ponto final correto a utilizar é o Ponto Final gremlin, que tem o seguinte formato:

`https://YOUR_DATABASE_ACCOUNT.gremlin.cosmosdb.azure.com:443/`

### <a name="why-am-i-getting-the-requestrateistoolarge-error"></a>Porque é que estou a ter o erro do "RequestRateIsTooLarge"?

Este erro significa que as Unidades de Pedido atribuídas por segundo não são suficientes para servir a consulta. Este erro é geralmente visto quando executa uma consulta que obtém todos os vértices:

```
// Query example:
g.V()
```

Esta consulta tentará recuperar todos os vértices do gráfico. Assim, o custo desta consulta será igual ao número de vértices em termos de RUs. A definição RU/s deve ser ajustada para abordar esta consulta.

### <a name="why-do-my-gremlin-driver-connections-get-dropped-eventually"></a>Porque é que as ligações ao condutor do Gremlin acabam por ser abandonadas?

Uma ligação Gremlin é feita através de uma ligação WebSocket. Embora as ligações WebSocket não tenham um tempo específico para viver, a API Azure Cosmos DB Gremlin terminará as ligações inativas após 30 minutos de inatividade.

### <a name="why-cant-i-use-fluent-api-calls-in-the-native-gremlin-drivers"></a>Por que não posso usar chamadas fluentes de API nos motoristas nativos de Gremlin?

As chamadas fluentes da API ainda não são apoiadas pela API Do Cosmos Azure. As chamadas fluentes da API requerem uma funcionalidade de formatação interna conhecida como suporte de bytecode que atualmente não é suportada pela API DD DD DD D. Azure Cosmos. Devido à mesma razão, o mais recente controlador Gremlin-JavaScript também não é suportado.

### <a name="how-can-i-evaluate-the-efficiency-of-my-gremlin-queries"></a>Como posso avaliar a eficiência das minhas consultas gremlin?

O passo de pré-visualização **do Profile de execução** pode ser utilizado para fornecer uma análise do plano de execução de consulta. Este passo deve ser adicionado ao fim de qualquer consulta de Gremlin, como ilustrado pelo seguinte exemplo:

**Exemplo de consulta**

```
g.V('mary').out('knows').executionProfile()
```

**Saída de exemplo**

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

A saída do perfil acima mostra quanto tempo é gasto a obter os objetos vértice, os objetos de borda e o tamanho do conjunto de dados de trabalho. Isto está relacionado com as medições de custopadrão para consultas de DB da Azure Cosmos.

## <a name="cassandra-api"></a><a id="cassandra"></a>Cassandra API

### <a name="what-is-the-protocol-version-supported-by-azure-cosmos-db-cassandra-api-is-there-a-plan-to-support-other-protocols"></a>Qual é a versão protocolar suportada pela Azure Cosmos DB Cassandra API? Existe um plano para apoiar outros protocolos?

Apache Cassandra API para Azure Cosmos DB suporta hoje a versão CQL 4. Se tiver feedback sobre o apoio a outros protocolos, avise-nos através do feedback de [voz do utilizador](https://feedback.azure.com/forums/263030-azure-cosmos-db) ou envie um e-mail para [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com).

### <a name="why-is-choosing-a-throughput-for-a-table-a-requirement"></a>Por que escolher um prato para uma tabela é um requisito?

O Azure Cosmos DB define a entrada padrão para o seu recipiente com base no local onde cria a tabela a partir de - portal ou CQL.
A Azure Cosmos DB oferece garantias de desempenho e latência, com limites superiores em funcionamento. Esta garantia é possível quando o motor pode impor a governação das operações do arrendatário. A definição de entrada garante que obtém a entrada garantida e a latência, porque a plataforma reserva esta capacidade e garante o sucesso da operação.
Pode alterar elásticamente a sua entrada para beneficiar da sazonalidade da sua aplicação e economizar custos.

O conceito de entrada é explicado no artigo [Das Unidades de Pedido em Azure Cosmos DB.](request-units.md) O resultado para uma mesa é distribuído pelas divisórias físicas subjacentes igualmente.

### <a name="what-is-the-default-rus-of-table-when-created-through-cql-what-if-i-need-to-change-it"></a>Qual é o padrão RU/s da tabela quando criado através do CQL? E se eu precisar mudá-lo?

A Azure Cosmos DB utiliza unidades de pedido por segundo (RU/s) como moeda para fornecer a sua entrada. As tabelas criadas através do CQL têm 400 RU. Pode mudar o RU do portal.

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

A capacidade de armazenamento é gerida automaticamente e aumenta à medida que empurra mais dados. Assim, pode importar com confiança os dados necessários sem gerir e fornecer nós, e muito mais.

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
Se quiser modificar a retenção e a [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) frequência, envie um e-mail para ou levante um caso de suporte. A informação sobre a capacidade de backup é fornecida na cópia de segurança online Automática e restaurar com o artigo [da Azure Cosmos DB.](online-backup-and-restore.md)

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>Como é que a conta da Cassandra API falha se uma região cai?

O Azure Cosmos DB Cassandra API empresta-se da plataforma distribuída globalmente da Azure Cosmos DB. Para garantir que a sua aplicação pode tolerar o tempo de inatividade do datacenter, permita pelo menos mais uma região para a conta no portal Azure Cosmos DB [Desenvolvimento com contas multi-regiões Azure Cosmos DB.](high-availability.md) Pode definir a prioridade da região utilizando o portal [Desenvolvimento com contas multi-regiões Azure Cosmos DB.](high-availability.md)

Pode adicionar o número de regiões que quiser para a conta e o controlo onde pode falhar, fornecendo uma prioridade de failover. Para utilizar a base de dados, também é necessário fornecer uma aplicação. Quando o fizeres, os teus clientes não vão sentir tempo de inatividade.

### <a name="does-the-apache-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>O índice Apache Cassandra API atribui todos os atributos de uma entidade por defeito?

A Cassandra API está a planear apoiar a indexação secundária para ajudar a criar um índice seletivo em determinados atributos. 


### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>Posso usar a nova Cassandra API SDK localmente com o emulador?

Sim, isto é apoiado.

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-change-feed-and-other-functionality-will-these-capabilities-be-added-to-the-cassandra-api"></a>O Azure Cosmos DB como plataforma parece ter muitas capacidades, como o feed de mudanças e outras funcionalidades. Estas capacidades serão adicionadas à API de Cassandra?

A Apache Cassandra API fornece a mesma funcionalidade CQL que a Apache Cassandra. Planeamos estudar a viabilidade de apoiar várias capacidades no futuro.

### <a name="feature-x-of-regular-cassandra-api-isnt-working-as-today-where-can-the-feedback-be-provided"></a>O recurso x da API cassandra regular não está funcionando como hoje, onde o feedback pode ser fornecido?

Fornecer feedback através do feedback da [voz do utilizador](https://feedback.azure.com/forums/263030-azure-cosmos-db).

[azure-portal]: https://portal.azure.com
[query]: sql-api-sql-query.md
