---
title: Perguntas frequentes sobre diferentes APIs em Azure Cosmos DB
description: Obtenha respostas para perguntas frequentes sobre Azure Cosmos DB, um serviço de base de dados multi-modelo distribuído globalmente. Saiba mais sobre capacidade, níveis de desempenho e escala.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 26794f0d743bc701879a161f69e374340206e5d8
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92488474"
---
# <a name="frequently-asked-questions-about-different-apis-in-azure-cosmos-db"></a>Perguntas mais frequentes sobre as diferentes APIs no Azure Cosmos DB

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Quais são os casos típicos de uso para Azure Cosmos DB?

O Azure Cosmos DB é uma boa escolha para novas aplicações web, mobile, gaming e IoT onde a escala automática, o desempenho previsível, a ordem rápida dos tempos de resposta do milisegundo e a capacidade de consultar dados isentos de esquemas é importante. A Azure Cosmos DB presta-se ao rápido desenvolvimento e ao apoio à iteração contínua de modelos de dados de aplicação. As aplicações que gerem conteúdo e dados gerados pelo utilizador são [casos de utilização comum para a Azure Cosmos DB](use-cases.md).

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Como é que a Azure Cosmos DB oferece um desempenho previsível?

Uma [unidade de pedido](request-units.md) (RU) é a medida de produção em Azure Cosmos DB. Uma produção de 1RU corresponde à produção do GET de um documento de 1-KB. Todas as operações em Azure Cosmos DB, incluindo leituras, escritos, consultas SQL e execuções de procedimentos armazenadas, têm um valor RU determinístico que é baseado na produção necessária para completar a operação. Em vez de pensar em CPU, IO e memória e como cada um deles afeta a sua aplicação, você pode pensar em termos de uma única medida RU.

Pode configurar cada recipiente Azure Cosmos com produção a forerada em termos de RUs de produção por segundo. Para aplicações de qualquer escala, pode comparar pedidos individuais para medir os seus valores RU, e providenciar um recipiente para lidar com o total de unidades de pedido em todos os pedidos. Também pode escalar ou escalar a produção do seu recipiente à medida que as necessidades da sua aplicação evoluem. Para obter mais informações sobre unidades de solicitação e para obter ajuda para determinar as necessidades do seu contentor, experimente a [calculadora de produção](https://www.documentdb.com/capacityplanner).

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>Como é que a Azure Cosmos DB suporta vários modelos de dados, tais como chave/valor, colunaar, documento e gráfico?

Os modelos de dados de chave/valor (tabela), colunaar, documento e gráfico são todos suportados de forma nativa devido ao design ARS (átomos, registos e sequências) em que a Azure Cosmos DB é construída. Átomos, registos e sequências podem ser facilmente mapeados e projetados para vários modelos de dados. As APIs para um subconjunto de modelos estão disponíveis neste momento (SQL, MongoDB, Table e Gremlin) e outros específicos para modelos de dados adicionais estarão disponíveis no futuro.

A Azure Cosmos DB tem um motor de indexação agnóstico de esquema capaz de indexar automaticamente todos os dados que ingere sem exigir qualquer esquema ou índices secundários do desenvolvedor. O motor baseia-se num conjunto de layouts de índices lógicos (invertidos, colunaares, árvores) que separam o layout de armazenamento dos subsistemas de processamento de índices e consultas. Cosmos DB também tem a capacidade de suportar um conjunto de protocolos de fio e APIs de uma forma extensível e traduzi-los eficientemente para o modelo de dados de base (1) e os layouts de índice lógico (2) tornando-o exclusivamente capaz de suportar mais de um modelo de dados de forma nativa.

### <a name="can-i-use-multiple-apis-to-access-my-data"></a>Posso usar várias APIs para aceder aos meus dados?

O Azure Cosmos DB é o serviço de base de dados com múltiplos modelos e distribuído globalmente da Microsoft. Onde multi-modelo significa Azure Cosmos DB suporta várias APIs e vários modelos de dados, diferentes APIs usam diferentes formatos de dados para armazenamento e protocolo de fio. Por exemplo, a SQL utiliza JSON, a MongoDB usa BSON, a Table usa EDM, a Cassandra usa o CQL, a Gremlin usa o formato JSON. Como resultado, recomendamos a utilização da mesma API para todos os acessos aos dados numa determinada conta.

Cada API opera de forma independente, com exceção da API Gremlin e SQL, que são interoperáveis.

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>A Azure Cosmos DB HIPAA está em conformidade?

Sim, a Azure Cosmos DB é compatível com a HIPAA. A HIPAA estabelece requisitos para a utilização, divulgação e salvaguarda das informações médicas pessoalmente identificáveis. Para obter mais informações, consulte o [Centro de Fidedignidade da Microsoft](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA).

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>Quais são os limites de armazenamento da Azure Cosmos DB?

Não há limite para a quantidade total de dados que um contentor pode armazenar em Azure Cosmos DB.

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>Quais são os limites de produção de Azure Cosmos DB?

Não há limite para a quantidade total de produção que um contentor pode suportar em Azure Cosmos DB. A ideia chave é distribuir a sua carga de trabalho aproximadamente uniformemente entre um número suficientemente grande de chaves de partição.

### <a name="are-direct-and-gateway-connectivity-modes-encrypted"></a>Os modos de conectividade Direct e Gateway estão encriptados?

Sim, ambos os modos estão sempre totalmente encriptados.

### <a name="how-much-does-azure-cosmos-db-cost"></a>Quanto custa a Azure Cosmos DB?

Para mais detalhes, consulte a página de detalhes de [preços da Azure Cosmos DB.](https://azure.microsoft.com/pricing/details/cosmos-db/) As taxas de utilização da Azure Cosmos DB são determinadas pelo número de contentores a provisionados, pelo número de horas em que os contentores estavam online e pela produção prevista para cada contentor.

### <a name="is-a-free-account-available"></a>Há uma conta gratuita disponível?

Sim, pode inscrever-se numa conta limitada no tempo, sem qualquer compromisso. Para se inscrever, visite [Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) ou leia mais na [Try Azure Cosmos DB FAQ](#try-cosmos-db).

Se é novo no Azure, pode inscrever-se numa [conta gratuita da Azure,](https://azure.microsoft.com/free/)que lhe dá 30 dias e um crédito para experimentar todos os serviços da Azure. Se tiver uma subscrição do Visual Studio, também pode utilizar [créditos Azure gratuitos](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) em qualquer serviço Azure.

Também pode utilizar o [Emulador Azure Cosmos DB](local-emulator.md) para desenvolver e testar a sua aplicação localmente gratuitamente, sem criar uma subscrição do Azure. Quando estiver satisfeito com o funcionamento da sua aplicação no Emulador do Azure Cosmos DB, pode mudar e começar a utilizar uma conta do Azure Cosmos DB na cloud.

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>Como posso obter ajuda adicional com a Azure Cosmos DB?

Para fazer uma pergunta técnica, pode publicar num destes dois fóruns de perguntas e respostas:

* [Microsoft Q&Uma página de perguntas](/answers/topics/azure-cosmos-db.html)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Stack Overflow é o melhor para questões de programação. [Certifique-se de](https://stackoverflow.com/help/on-topic) que a sua pergunta está sobre o tema e forneça o máximo de detalhes [possível, tornando a pergunta clara e responsável.](https://stackoverflow.com/help/how-to-ask)

Para solicitar novas funcionalidades, crie um novo pedido na [voz do Utilizador](https://feedback.azure.com/forums/263030-azure-cosmos-db).

Para corrigir um problema na sua conta, crie um [pedido de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) no portal do Azure.

## <a name="try-azure-cosmos-db-subscriptions"></a><a id="try-cosmos-db"></a>Experimente assinaturas DB da Azure Cosmos

Você pode agora desfrutar de uma experiência DB Azure Cosmos limitada no tempo sem uma subscrição, gratuitamente e compromissos. Para se inscrever para uma subscrição de Try Azure Cosmos DB, vá a [Try Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) e use qualquer conta pessoal da Microsoft (MSA). Esta subscrição é separada do [Azure Free Trial](https://azure.microsoft.com/free/), e pode ser usada juntamente com um Azure Free Trial ou uma subscrição paga Azure.

Experimente as subscrições DB do Azure Cosmos aparecem no portal Azure a seguir outras subscrições associadas ao ID do utilizador.

As seguintes condições aplicam-se às subscrições da Try Azure Cosmos DB:

* O acesso à conta pode ser concedido a contas pessoais da Microsoft (MSA). Evite utilizar contas ou contas do Azure Ative Directory (Azure AD) pertencentes a inquilinos da AD Azure, que possam ter limitações que possam bloquear a concessão de acesso.
* Um [contentor de produção provisido](./set-throughput.md#set-throughput-on-a-container) por subscrição para contas SQL, Gremlin API e Table.
* Até três [cobranças de produção previstas](./set-throughput.md#set-throughput-on-a-container) por subscrição para contas MongoDB.
* Uma [base de dados de produção](./set-throughput.md#set-throughput-on-a-database) por subscrição. As bases de dados previstas para o rendimento podem conter qualquer número de contentores no seu interior.
* Capacidade de armazenamento de 10 GB.
* A replicação global está disponível nas [seguintes regiões do Azure](https://azure.microsoft.com/regions/): Eua Central, Norte da Europa e Sudeste Asiático
* Produção máxima de 5 K RU/s quando a provisionada ao nível do recipiente.
* Produção máxima de 20 K RU/s quando previsto ao nível da base de dados.
* As assinaturas expiram após 30 dias e podem ser alargadas para um máximo de 31 dias no total. Após a expiração, a informação contida é eliminada.
* Os bilhetes de apoio Azure não podem ser criados para contas de Try Azure Cosmos DB; no entanto, é prestado apoio aos assinantes com planos de apoio existentes.

## <a name="set-up-azure-cosmos-db"></a>Configurar Azure Cosmos DB

### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>Como me inscrevo na Azure Cosmos DB?

AZure Cosmos DB está disponível no portal Azure. Primeiro, inscreva-se para uma assinatura Azure. Depois de se inscrever, pode adicionar uma conta DB da Azure Cosmos à sua assinatura Azure.

### <a name="what-is-a-primary-key"></a>O que é uma chave primária?

Uma chave primária é um sinal de segurança para aceder a todos os recursos para uma conta. Os indivíduos com a chave leram e escreveram acesso a todos os recursos na conta de base de dados. Tenha cuidado quando distribuir as teclas primárias. A chave primária primária primária e a chave primária secundária estão disponíveis na lâmina de **Chaves** do [portal Azure][azure-portal]. Para mais informações sobre chaves, consulte [Ver, copiar e voltar a gerar chaves de acesso](manage-with-cli.md#list-account-keys).

### <a name="what-are-the-regions-that-preferredlocations-can-be-set-to"></a>Quais são as regiões que as PreferênciasLocações podem definir?

O valor preferredLocations pode ser definido para qualquer uma das regiões de Azure em que o Cosmos DB está disponível. Para obter uma lista das regiões disponíveis, consulte as [regiões de Azure.](https://azure.microsoft.com/regions/)

### <a name="is-there-anything-i-should-be-aware-of-when-distributing-data-across-the-world-via-the-azure-datacenters"></a>Há alguma coisa que eu deva saber ao distribuir dados por todo o mundo através dos centros de dados Azure?

Azure Cosmos DB está presente em todas as regiões do Azure, conforme especificado na página das [regiões de Azure.](https://azure.microsoft.com/regions/) Porque é o serviço principal, cada novo datacenter tem uma presença DB Azure Cosmos.

Quando definir uma região, lembre-se que Azure Cosmos DB respeita as nuvens soberanas e governamentais. Ou seja, se criarmos uma conta numa [região soberana,](https://azure.microsoft.com/global-infrastructure/)não podemos replicar-nos a partir dessa [região soberana.](https://azure.microsoft.com/global-infrastructure/) Da mesma forma, não é possível permitir a replicação em outras localizações soberanas a partir de uma conta externa.

### <a name="is-it-possible-to-switch-from-container-level-throughput-provisioning-to-database-level-throughput-provisioning-or-vice-versa"></a>É possível passar do fornecimento de produção de nível de contentor para o fornecimento de produção de nível de base de dados? Ou vice-versa

O fornecimento de produção de contentores e de nível de base de dados são ofertas separadas e a troca entre ambos requerem dados migratórios de origem para destino. O que significa que precisa de criar uma nova base de dados ou de um novo recipiente e, em seguida, migrar dados utilizando [a biblioteca de executores a granel](bulk-executor-overview.md) ou a [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).

### <a name="does-azure-cosmosdb-support-time-series-analysis"></a>A Azure CosmosDB suporta a análise das séries de tempo?

Yes Azure CosmosDB suporta a análise de séries de tempo, aqui está uma amostra para [o padrão da série de tempo.](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/samples/Patterns) Esta amostra mostra como usar o feed de mudança para construir visualizações agregadas ao longo de dados de séries de tempo. Pode estender esta abordagem utilizando o streaming de faíscas ou outro processador de dados de fluxo.

## <a name="what-are-the-azure-cosmos-db-service-quotas-and-throughput-limits"></a>Quais são as quotas de serviço DB da Azure Cosmos e os limites de produção

Consulte as quotas de [serviço](concepts-limits.md) DB da Azure Cosmos e [em todos os limites por contentor e](set-throughput.md#comparison-of-models) artigos de base de dados para obter mais informações.

## <a name="frequently-asked-questions-about-sql-api"></a><a id="sql-api-faq"></a>Perguntas frequentes sobre API SQL

### <a name="how-do-i-start-developing-against-the-sql-api"></a>Como começo a desenvolver-me contra a API SQL?

Primeiro tem de se inscrever para uma subscrição do Azure. Uma vez que se inscreva para uma subscrição Azure, pode adicionar um recipiente API SQL à sua assinatura Azure. Para obter instruções sobre a adição de uma conta DB Azure Cosmos, consulte [criar uma conta de base de dados Azure Cosmos](create-sql-api-dotnet.md#create-account).

Existem [SDKs](sql-api-sdk-dotnet.md) disponíveis para .NET, Python, Node.js, JavaScript e Java. Os desenvolvedores também podem usar as [APIs HTTP RESTful](/rest/api/cosmos-db/) para interagir com os recursos DB do Azure Cosmos de várias plataformas e idiomas.

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>Posso aceder a algumas amostras prontas para ter uma vantagem?

As amostras para os SQL API [.NET](sql-api-dotnet-samples.md), [Java,](https://github.com/Azure/azure-documentdb-java) [Node.js](sql-api-nodejs-samples.md)e [Python](sql-api-python-samples.md) SDKs estão disponíveis no GitHub.

### <a name="does-the-sql-api-database-support-schema-free-data"></a>A base de dados da API SQL suporta dados isentos de esquemas?

Sim, a API SQL permite que as aplicações armazenem documentos JSON arbitrários sem definições de esquema ou sugestões. Os dados estão imediatamente disponíveis para consulta através da interface de consulta Azure Cosmos DB SQL.

### <a name="does-the-sql-api-support-acid-transactions"></a>A API SQL suporta transações acid?

Sim, a API SQL suporta transações de documentos cruzados expressas como procedimentos e gatilhos armazenados por JavaScript. As transações são scopeadas para uma única divisória dentro de cada contentor e executadas com semântica acid como "tudo ou nada", isoladas de outro código de execução simultânea e pedidos de utilizador. Se as exceções forem lançadas através da execução do código de aplicação JavaScript, toda a transação é revertida. 

### <a name="what-is-a-container"></a>O que é um contentor?

Um recipiente é um grupo de documentos e a sua lógica de aplicação JavaScript associada. Um contentor é uma entidade faturada, onde o [custo](performance-levels.md) é determinado pela produção e armazenamento usado. Os contentores podem abranger uma ou mais divisórias ou servidores e podem escalar para lidar com volumes de armazenamento ou produção praticamente ilimitados.

* Para a API SQL, o recurso é chamado de recipiente.
* Para a API da Cosmos DB para contas MongoDB, um mapa de contentores para uma Coleção.
* Para as contas cassandra e tabela API, um mapa de contentores para uma tabela.
* Para as contas da API gremlin, um contentor mapeia para um gráfico.

Os contentores são também as entidades de faturação da Azure Cosmos DB. Cada recipiente é faturado de hora a hora, com base na produção e espaço de armazenamento utilizado. Para mais informações, consulte [a Azure Cosmos DB Pricing](https://azure.microsoft.com/pricing/details/cosmos-db/).

### <a name="how-do-i-create-a-database"></a>Como crio uma base de dados?

Pode criar bases de dados utilizando o [portal Azure,](https://portal.azure.com)conforme descrito no [Add a container](create-sql-api-java.md#add-a-container), um dos [SDKs Azure Cosmos DB](sql-api-sdk-dotnet.md), ou as [APIs REST](/rest/api/cosmos-db/).

### <a name="how-do-i-set-up-users-and-permissions"></a>Como posso configurar utilizadores e permissões?

Pode criar utilizadores e permissões utilizando um dos [SDKs da API do Cosmos ou](sql-api-sdk-dotnet.md) das [APIs REST](/rest/api/cosmos-db/).

### <a name="does-the-sql-api-support-sql"></a>A SQL API suporta o SQL?

O idioma de consulta SQL suportado pelas contas API SQL é um subconjunto melhorado da funcionalidade de consulta que é suportado pelo SQL Server. A linguagem de consulta Azure Cosmos DB SQL fornece operadores hierárquicos e relacionais ricos e extensibilidade através de funções baseadas em JavaScript, definidas pelo utilizador (UDFs). A gramática JSON permite modelar documentos JSON como árvores com nós rotulados, que são usados tanto pelas técnicas de indexação automática Azure Cosmos DB como pelo dialeto de consulta SQL de Azure Cosmos DB. Para obter informações sobre a utilização da gramática SQL, consulte o artigo [de consulta SQL.][query]

### <a name="does-the-sql-api-support-sql-aggregation-functions"></a>O SQL API suporta funções de agregação SQL?

A API SQL suporta a agregação de baixa latência em qualquer escala através de funções `COUNT` agregadas, e através da gramática `MIN` `MAX` `AVG` `SUM` SQL. Para obter mais informações, consulte [as funções agregadas.](sql-query-aggregates.md)

### <a name="how-does-the-sql-api-provide-concurrency"></a>Como é que a API SQL proporciona concordância?

A API SQL suporta controlo de concência otimista (OCC) através de tags de entidades HTTP, ou ETags. Cada recurso API SQL tem um ETag, e o ETag é definido no servidor sempre que um documento é atualizado. O cabeçalho ETag e o valor atual estão incluídos em todas as mensagens de resposta. Os ETags podem ser utilizados com o cabeçalho If-Match para permitir que o servidor decida se um recurso deve ser atualizado. O valor If-Match é o valor ETag a ser verificado. Se o valor ETag corresponder ao valor ETag do servidor, o recurso é atualizado. Se o ETag já não estiver em funcionamento, o servidor rejeita a operação com um código de resposta "FALHA DE Pré-condição HTTP 412". Em seguida, o cliente refetches o recurso para adquirir o valor ETag atual para o recurso. Além disso, os ETags podem ser utilizados com o cabeçalho Se-Nenhum-Match para determinar se é necessário um refetch de um recurso.

Para utilizar a concordância otimista em .NET, utilize a classe [AccessCondition.](/dotnet/api/microsoft.azure.documents.client.accesscondition) Para obter uma amostra .NET, consulte [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) na amostra de DocumentosManagement no GitHub.

### <a name="how-do-i-perform-transactions-in-the-sql-api"></a>Como faço transações na API SQL?

A API SQL suporta transações integradas na linguagem através de procedimentos e gatilhos armazenados pelo JavaScript. Todas as operações de base de dados dentro dos scripts são executadas sob isolamento instantâneo. Se for um recipiente de partição única, a execução é apecada para o contentor. Se o recipiente for dividido, a execução é limitada a documentos com o mesmo valor de chave de partição dentro do recipiente. Um instantâneo das versões do documento (ETags) é tirado no início da transação e consolidado apenas se o script for concluído com êxito. Se o JavaScript emitir um erro, a transação é revertida. Para obter mais informações, consulte [a programação JavaScript do lado do servidor para Azure Cosmos DB](stored-procedures-triggers-udfs.md).

### <a name="how-can-i-bulk-insert-documents-into-cosmos-db"></a>Como posso inserir documentos em massa na Cosmos DB?

Pode inserir documentos em massa na Azure Cosmos DB de uma das seguintes formas:

* A ferramenta executora a granel, conforme descrito na [utilização do executor a granel .NET library](bulk-executor-dot-net.md) e [usando a biblioteca Java do executor a granel](bulk-executor-java.md)
* A ferramenta de migração de dados, como descrito na [ferramenta de migração da Base de Dados para Azure Cosmos DB](import-data.md).
* Procedimentos armazenados, conforme descrito na [programação JavaScript do lado do servidor para Azure Cosmos DB](stored-procedures-triggers-udfs.md).

### <a name="does-the-sql-api-support-resource-link-caching"></a>A API SQL suporta o caching de recursos?

Sim, como a Azure Cosmos DB é um serviço RESTful, as ligações de recursos são imutáveis e podem ser em cache. Os clientes da API SQL podem especificar um cabeçalho "Se-Nenhum-Match" para leituras contra qualquer documento ou contentor semelhante a recursos e, em seguida, atualizar as suas cópias locais após a versão do servidor ter sido alterada.

### <a name="is-a-local-instance-of-sql-api-available"></a>Está disponível um caso local de API SQL?

Sim. O [Azure Cosmos DB Emulator](local-emulator.md) proporciona uma emulação de alta fidelidade do serviço Cosmos DB. Suporta funcionalidades idênticas à Azure Cosmos DB, incluindo suporte para criar e consultar documentos JSON, provisões e coleções de escala, e executar procedimentos e gatilhos armazenados. Pode desenvolver e testar aplicações utilizando o Emulador Azure Cosmos DB e implantá-las para Azure à escala global, fazendo uma única alteração de configuração no ponto final de ligação para Azure Cosmos DB.

### <a name="why-are-long-floating-point-values-in-a-document-rounded-when-viewed-from-data-explorer-in-the-portal"></a>Por que os valores de ponto flutuante longos num documento arredondado quando vistos a partir de data explorer no portal.

Esta é a limitação do JavaScript. O JavaScript utiliza números de formato flutuante de dupla precisão, conforme especificado no IEEE 754 e pode conter números com segurança entre -(2<sup>53</sup> - 1) e 2<sup>53</sup>-1 (i.e., 900719254740991) apenas.

### <a name="where-are-permissions-allowed-in-the-object-hierarchy"></a>Onde são permitidas permissões na hierarquia dos objetos?

A criação de permissões utilizando o ResourceTokens é permitida ao nível do contentor e dos seus descendentes (tais como documentos, anexos). Isto implica que tentar criar uma permissão na base de dados ou um nível de conta não é atualmente permitido.

[azure-portal]: https://portal.azure.com
[query]: ./sql-query-getting-started.md

## <a name="next-steps"></a>Passos seguintes

Para aprender sobre perguntas frequentes noutras APIs, consulte:

* Perguntas frequentes sobre [A API da Azure Cosmos DB para a MongoDB](mongodb-api-faq.md)
* Perguntas frequentes sobre [Gremlin API em Azure Cosmos DB](gremlin-api-faq.md)
* Perguntas frequentes sobre [Cassandra API em Azure Cosmos DB](cassandra-faq.md)
* Perguntas frequentes sobre [API de Tabela em Azure Cosmos DB](table-api-faq.md)