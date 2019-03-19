---
title: Perguntas mais frequentes sobre as diferentes APIs no Azure Cosmos DB
description: Obtenha respostas para perguntas mais frequentes sobre o Azure Cosmos DB, um serviço de base de dados com múltiplos modelos distribuída globalmente. Saiba mais sobre a capacidade, níveis de desempenho e dimensionamento.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 7e1c6d771e2452e99b47c256c99e25e281b9a3aa
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57849232"
---
# <a name="frequently-asked-questions-about-different-apis-in-azure-cosmos-db"></a>Perguntas mais frequentes sobre as diferentes APIs no Azure Cosmos DB

### <a name="what-happened-to-the-documentdb-api"></a>O que aconteceu com a API do DocumentDB?

O Azure Cosmos DB DocumentDB API ou o SQL (DocumentDB) API é agora conhecido como API de SQL do Azure Cosmos DB. Não precisa alterar nada para continuar a executar as suas aplicações criadas com a API do DocumentDB. A funcionalidade permanece igual.

Se tiver uma conta de API do DocumentDB antes, agora tem uma conta da API de SQL, sem qualquer alteração à sua faturação.

### <a name="what-happened-to-azure-documentdb-as-a-service"></a>O que aconteceu para o Azure DocumentDB como serviço?

O serviço DocumentDB do Azure faz agora parte do serviço do Azure Cosmos DB e se manifesta na forma da API de SQL. Os aplicativos criados com o DocumentDB do Azure serão executada sem alterações de API de SQL do Azure Cosmos DB. O cosmos DB também implementa [Cassandra](cassandra-introduction.md), [MongoDB](mongodb-introduction.md), [Gremlin](graph-introduction.md) e [Azure Table Storage](table-introduction.md) conectar protocolos diretamente no serviço. Isto permite-lhe do ponto de controladores do cliente (e ferramentas) para as APIs de NoSQL usadas diretamente à sua base de dados do Cosmos.

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Quais são os casos de utilização típica para o Azure Cosmos DB?

Azure Cosmos DB é uma boa opção para o novo web, móvel, jogos e aplicações de IoT onde o dimensionamento automático, desempenho previsível, rápida ordem dos tempos de resposta de milissegundos e a capacidade de consultar sobre dados sem esquema é importante. O Azure Cosmos DB presta-se para um desenvolvimento rápido e suportar a iteração contínua de modelos de dados de aplicação. As aplicações que gerem conteúdos gerados pelo utilizador e os dados são [casos de utilização comuns para o Azure Cosmos DB](use-cases.md).

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Como é que o Azure Cosmos DB oferece um desempenho previsível?

R [unidades de pedido](request-units.md) (RU) é a medida de débito no Azure Cosmos DB. Um débito de 1 RU corresponde ao débito do GET de um documento de 1 KB. Cada operação no Azure Cosmos DB, incluindo leituras, escritas, consultas SQL e execuções de procedimentos armazenados, têm um valor de RU determinístico com base no débito necessário para concluir a operação. Em vez de pensar no CPU, memória e e/s e como elas cada afetam o débito da sua aplicação, pode pensar em termos de uma única medida de RU.

Pode configurar cada contentor do Azure Cosmos DB com um débito aprovisionado em termos de RUs de débito por segundo. Para aplicativos de qualquer escala, pode referenciar pedidos individuais para medir os respetivos valores de RU e aprovisionar um contentor para processar o total de unidades de pedido em todos os pedidos. Também pode aumentar verticalmente ou horizontalmente o débito do seu contentor conforme as necessidades da sua aplicação evoluem. Para obter mais informações sobre unidades de pedido e para obter ajuda a determinar suas necessidades de contentor, experimente o [Calculadora de débito](https://www.documentdb.com/capacityplanner).

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>Como é que o Azure Cosmos DB suporta vários modelos de dados, como a chave/valor, em colunas, documentos e gráficos?

Chaves/valores (tabela), em colunas, documentos e dados de gráficos modelos são todos suportados nativamente devido a ARS (atoms, registos e sequências) de design que o Azure Cosmos DB é criado. Atoms, registos e sequências de podem ser facilmente mapeadas e projetadas para vários modelos de dados. As APIs para um subconjunto de modelos estão diretamente disponível agora (SQL, MongoDB, Table e Gremlin) e outras pessoas específicas para modelos de dados adicionais vão estar disponíveis no futuro.

O Azure Cosmos DB tem um esquema agnóstico indexação motor capaz de indexar automaticamente todos os dados que ingere sem necessidade de qualquer esquema ou índices secundários do desenvolvedor. O mecanismo conta com um conjunto de layouts de lógica de índice (invertida, em colunas, árvore) que desacoplar o esquema de armazenamento do índice e subsistemas de processamento de consultas. O cosmos DB também tem a capacidade de suporte a um conjunto de APIs e protocolos de transmissão de forma extensível e transformá-los com eficiência para o modelo de dados do core (1) e os layouts de lógica de índice (2) tornando exclusivamente capaz de suportar mais do que um modelo de dados de forma nativa.

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>HIPAA do Azure Cosmos DB está em conformidade?

Sim, o Azure Cosmos DB está em conformidade com HIPAA. A HIPAA estabelece requisitos para a utilização, divulgação e salvaguarda das informações médicas pessoalmente identificáveis. Para obter mais informações, consulte o [Centro de Fidedignidade da Microsoft](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA).

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>Quais são os limites de armazenamento do Azure Cosmos DB?

Não existe nenhum limite para a quantidade total de dados que um contentor pode armazenar no Azure Cosmos DB.

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>Quais são os limites de débito do Azure Cosmos DB?

Não existe nenhum limite para a quantidade total de débito que pode oferecer suporte a um contentor no Azure Cosmos DB. A idéia-chave é distribuir a carga de trabalho aproximadamente uniformemente entre um número grande o suficiente de chaves de partição.

### <a name="are-direct-and-gateway-connectivity-modes-encrypted"></a>Direct e o Gateway de modos de conectividade são encriptados?

Sim ambos os modos são sempre totalmente encriptados.

### <a name="how-much-does-azure-cosmos-db-cost"></a>Quanto custa o Azure Cosmos DB?

Para obter detalhes, consulte a [os detalhes dos preços do Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) página. Os custos de utilização do Azure Cosmos DB são determinados pelo número de contentores aprovisionados, o número de horas, os contentores estiveram online e o débito aprovisionado para cada contentor.

### <a name="is-a-free-account-available"></a>É uma conta gratuita disponível?

Sim, pode inscrever-se para uma conta de tempo limitado sem custos, sem compromisso. Para se inscrever, visite [Experimente o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) ou leia mais no [experimentar o Azure Cosmos DB FAQ](#try-cosmos-db).

Se estiver familiarizado com o Azure, pode inscrever-se para obter um [conta gratuita do Azure](https://azure.microsoft.com/free/), que dá-lhe 30 dias e um crédito para experimentar todos os serviços do Azure. Se tiver uma subscrição do Visual Studio, também é elegível para [gratuita créditos do Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) para utilizar em qualquer serviço do Azure.

Também pode utilizar o [emulador do Azure Cosmos DB](local-emulator.md) para desenvolver e testar seu aplicativo localmente para obter gratuitamente, sem criar uma subscrição do Azure. Quando estiver satisfeito com o funcionamento da sua aplicação no Emulador do Azure Cosmos DB, pode mudar e começar a utilizar uma conta do Azure Cosmos DB na cloud.

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>Como posso obter ajuda adicional com o Azure Cosmos DB?

Para fazer uma pergunta técnica, pode publicar para um destes dois pergunta e responder a fóruns:

* [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Estouro de pilha é melhor para questões de programação. Certifique-se de que é a sua pergunta [no tópico](https://stackoverflow.com/help/on-topic) e [fornecer tantos detalhes quanto possível, fazer a pergunta clara e answerable](https://stackoverflow.com/help/how-to-ask).

Para pedir novas funcionalidades, crie um novo pedido no [uservoice](https://feedback.azure.com/forums/263030-azure-cosmos-db).

Para corrigir um problema na sua conta, crie um [pedido de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) no portal do Azure.

Outras perguntas que podem ser submetidas para a equipe da [ askcosmosdb@microsoft.com ](mailto:askcosmosdb@microsoft.com); no entanto, isso não é um alias de suporte técnico.

## <a id="try-cosmos-db"></a>Experimente as subscrições do Azure Cosmos DB

Agora pode Desfrute da experiência do Azure Cosmos DB por tempo limitado sem uma subscrição, sem encargos e compromissos. Para se inscrever para uma subscrição do Azure do Cosmos DB, aceda a [Experimente o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/). Esta subscrição está separada do [avaliação gratuita do Azure](https://azure.microsoft.com/free/)e pode ser utilizado juntamente com uma versão de avaliação gratuita do Azure ou um Azure subscrição paga.

Try Azure Cosmos DB subscrições são apresentadas no portal do Azure a seguir outras subscrições associadas à sua ID de utilizador.

As seguintes condições aplicam-se a subscrições do Azure do Cosmos DB:

* Um contêiner por subscrição para contas SQL, a API do Gremlin e a tabela.
* Até três coleções por subscrição para contas do MongoDB.
* Capacidade de 10 GB de armazenamento.
* A replicação global está disponível na seguinte [regiões do Azure](https://azure.microsoft.com/regions/): Centro dos E.U.A., Europa do Norte e Sudeste asiático
* Débito máximo de 5 mil RU/s.
* Subscrições expirem após 24 horas e podem ser estendidas até um máximo de total de 48 horas.
* Pedidos de suporte do Azure não não possível criar contas de experimentar o Azure Cosmos DB No entanto, o suporte é fornecido para os subscritores com planos de suporte existentes.

## <a name="set-up-azure-cosmos-db"></a>Configurar o Azure Cosmos DB

### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>Como me inscrevo para o Azure Cosmos DB?

O Azure Cosmos DB está disponível no portal do Azure. Em primeiro lugar, inscreva-se uma subscrição do Azure. Depois que tiver se inscrito, pode adicionar uma conta do Azure Cosmos DB para a sua subscrição do Azure.

### <a name="what-is-a-master-key"></a>O que é uma chave mestra?

Uma chave mestra é um token de segurança para aceder a todos os recursos de uma conta. Indivíduos com a chave tem de leitura e escrita a todos os recursos na conta de base de dados. Tenha cuidado ao distribuir chaves mestras. A chave mestra principal e a chave mestra secundária estão disponíveis no **chaves** painel da [portal do Azure][azure-portal]. Para mais informações sobre chaves, consulte [Ver, copiar e voltar a gerar chaves de acesso](manage-with-cli.md#list-account-keys).

### <a name="what-are-the-regions-that-preferredlocations-can-be-set-to"></a>Quais são as regiões que PreferredLocations pode ser definido como?

O valor de PreferredLocations pode ser definido como qualquer uma das regiões do Azure em que o Cosmos DB está disponível. Para obter uma lista de regiões disponíveis, consulte [regiões do Azure](https://azure.microsoft.com/regions/).

### <a name="is-there-anything-i-should-be-aware-of-when-distributing-data-across-the-world-via-the-azure-datacenters"></a>Existe algo que deve ter conhecimento ao distribuir dados em todo o mundo por meio dos datacenters do Azure?

O Azure Cosmos DB está presente em todas as regiões do Azure, conforme especificado no [regiões do Azure](https://azure.microsoft.com/regions/) página. Como é o serviço principal, todos os datacenters novos tem uma presença do Azure Cosmos DB.

Quando define uma região, lembre-se de que o Azure Cosmos DB respeita clouds soberanas e Governo. Ou seja, se criar uma conta num [garante a soberania: região](https://azure.microsoft.com/global-infrastructure/), não é possível replicar tudo isso [região garante a soberania:](https://azure.microsoft.com/global-infrastructure/). Da mesma forma, não é possível ativar a replicação em outros locais soberanas de uma conta externa.

### <a name="is-it-possible-to-switch-from-container-level-throughput-provisioning-to-database-level-throughput-provisioning-or-vice-versa"></a>É possível alternar de débito de nível de contêiner de aprovisionamento para o aprovisionamento de débito de nível de base de dados? Ou vice versa

Contentor e o aprovisionamento de débito de nível de base de dados são ofertas distintas e alterar entre qualquer um destes requerer a migrar dados de origem para destino. Que significa que precisa criar uma nova base de dados ou uma nova coleção e, em seguida, migrar dados, utilizando [biblioteca de executor em massa](bulk-executor-overview.md) ou [do Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).

### <a name="does-azure-cosmosdb-support-time-series-analysis"></a>O Azure cosmos DB suporta análise de série de tempo?

Sim Azure cosmos DB suporta análise de série de tempo, aqui está um exemplo para o [padrão de série de tempo](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/samples/Patterns). Este exemplo mostra como utilizar o feed criar vistas agregadas sobre dados de séries de tempo de alterações. Pode estender essa abordagem com transmissão em fluxo do spark ou outro processador de fluxo de dados.

## <a name="sql-api"></a>SQL API

### <a name="how-do-i-start-developing-against-the-sql-api"></a>Como iniciar a programação com a API de SQL?

Primeiro, deve inscrever para uma subscrição do Azure. Depois de se inscrever para uma subscrição do Azure, pode adicionar um contentor de API de SQL para a sua subscrição do Azure. Para obter instruções sobre como adicionar uma conta do Azure Cosmos DB, veja [criar uma conta de base de dados do Azure Cosmos DB](create-sql-api-dotnet.md#create-account).

Existem [SDKs](sql-api-sdk-dotnet.md) disponíveis para .NET, Python, Node.js, JavaScript e Java. Os programadores também podem utilizar o [RESTful HTTP APIs](/rest/api/cosmos-db/) para interagir com recursos do Azure Cosmos DB a partir de várias plataformas e linguagens.

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>Posso aceder a alguns exemplos prontas a utilizar para obter um ponto de partida?

Exemplos para a API de SQL [.NET](sql-api-dotnet-samples.md), [Java](https://github.com/Azure/azure-documentdb-java), [node. js](sql-api-nodejs-samples.md), e [Python](sql-api-python-samples.md) os SDKs estão disponíveis no GitHub.

### <a name="does-the-sql-api-database-support-schema-free-data"></a>A base de dados do SQL API suporta dados sem esquema?

Sim, a API de SQL permite que os aplicativos armazenem documentos JSON arbitrários sem definições de esquema ou sugestões. Os dados estão imediatamente disponíveis para consulta através da interface de consulta de SQL do Azure Cosmos DB.

### <a name="does-the-sql-api-support-acid-transactions"></a>A API de SQL suporta transações ACID?

Sim, a API de SQL suporta transações entre documentos expressadas como procedimentos armazenados do JavaScript e acionadores. Transações estão confinadas a uma única partição dentro de cada contentor e executadas com semântica ACID como "tudo ou nada," isolado de outros pedidos de utilizador e de código em execução simultânea. Se forem geradas exceções através da execução do lado do servidor de código de aplicação do JavaScript, toda a transação é revertida. 

### <a name="what-is-a-container"></a>O que é um contentor?

Um contêiner é um grupo de documentos e a respetiva lógica da aplicação associada JavaScript. Um contentor é uma entidade faturável, onde o [custo](performance-levels.md) é determinada pela taxa de transferência e utilizado de armazenamento. Os contentores podem abranger uma ou mais partições ou servidores e podem ser dimensionado para processar volumes praticamente ilimitados de armazenamento ou débito.

* Para a API de SQL e a API do Cosmos DB para contas de MongoDB, um contentor é mapeado para uma coleção.
* Para contas de Cassandra e API de tabela, um contentor mapeia para uma tabela.
* Para contas de API do Gremlin, um contentor é mapeado para um gráfico.

Os contentores também são as entidades de faturação para o Azure Cosmos DB. Cada contentor é cobrado à hora, com base no débito aprovisionado e espaço de armazenamento utilizado. Para obter mais informações, consulte [preços do Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

### <a name="how-do-i-create-a-database"></a>Como crio uma base de dados?

Pode criar bases de dados com o [portal do Azure](https://portal.azure.com), conforme descrito na [adicionar uma coleção](create-sql-api-dotnet.md#create-collection), um do [SDKs do Azure Cosmos DB](sql-api-sdk-dotnet.md), ou o [REST APIs](/rest/api/cosmos-db/).

### <a name="how-do-i-set-up-users-and-permissions"></a>Como posso configurar utilizadores e permissões?

Pode criar utilizadores e permissões ao utilizar uma da [SDKs de API do Cosmos DB](sql-api-sdk-dotnet.md) ou o [REST APIs](/rest/api/cosmos-db/).

### <a name="does-the-sql-api-support-sql"></a>A API de SQL suporta SQL?

A linguagem de consulta SQL suportada por contas da API de SQL é um subconjunto avançado da funcionalidade de consulta que é suportado pelo SQL Server. A linguagem de consulta de SQL do Azure Cosmos DB fornece operadores avançados de hierárquicos e relacionais e extensibilidade através de funções com base em JavaScript, definidas pelo utilizador (UDFs). A gramática JSON permite-lhe modelar documentos JSON como árvores connosco com nome, o que são utilizados pelas técnicas de indexação automática do Azure Cosmos DB e o dialeto de consulta SQL do Azure Cosmos DB. Para obter informações sobre como utilizar a gramática SQL, consulte a [consulta SQL] [ query] artigo.

### <a name="does-the-sql-api-support-sql-aggregation-functions"></a>A API de SQL suporta as funções de agregação de SQL?

A API do SQL oferece suporte a agregação de baixa latência em qualquer escala através de funções de agregação `COUNT`, `MIN`, `MAX`, `AVG`, e `SUM` via a gramática SQL. Para obter mais informações, consulte [funções de agregação](how-to-sql-query.md#Aggregates).

### <a name="how-does-the-sql-api-provide-concurrency"></a>Como a API de SQL fornece simultaneidade?

A API de SQL suporta o controlo de simultaneidade otimista (OCC) através de etiquetas de entidade HTTP ou ETags. Cada recurso de API de SQL tem uma ETag e ETag está definido no servidor sempre que um documento é atualizado. O cabeçalho ETag e o valor atual estão incluídos em todas as mensagens de resposta. ETags pode ser utilizadas com o cabeçalho If-Match para permitir que o servidor decidir se um recurso deve ser atualizado. O valor If-Match é o valor de ETag para ser verificado com base. Se o valor de ETag coincidir com o valor de ETag de servidor, o recurso for atualizado. Se a ETag já não está disponível, o servidor rejeita a operação com um "HTTP 412 Falha de pré-condição" código de resposta. O cliente refetches, em seguida, o recurso para adquirir o valor de ETag atual para o recurso. Além disso, as ETags pode ser utilizadas com o cabeçalho If-None-Match para determinar se um refetch de um recurso é necessário.

Para utilizar a simultaneidade otimista no .NET, utilize o [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) classe. Para obter um exemplo de .NET, veja [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) no exemplo DocumentManagement no GitHub.

### <a name="how-do-i-perform-transactions-in-the-sql-api"></a>Como posso efetuar transações na API de SQL?

A API do SQL oferece suporte a transações de linguagem integrada através de acionadores e procedimentos armazenados do JavaScript. Todas as operações de base de dados no interior de scripts são executadas em isolamento do instantâneo. Se é uma coleção de partição única, a execução tem como escopo o conjunto. Se a coleção é particionada, a execução é confinada a documentos com o mesmo valor de chave de partição dentro da coleção. Um instantâneo das versões do documento (ETags) é tirado no início da transação e consolidado apenas se o script for concluído com êxito. Se o JavaScript emitir um erro, a transação é revertida. Para obter mais informações, consulte [programação de JavaScript do lado do servidor para o Azure Cosmos DB](stored-procedures-triggers-udfs.md).

### <a name="how-can-i-bulk-insert-documents-into-cosmos-db"></a>Como pode eu inserção em massa documentos para o Cosmos DB?

Pode inserção em massa documentos para o Azure Cosmos DB em uma das seguintes formas:

* A ferramenta de executor em massa, conforme descrito em [biblioteca de .NET de executor do Using em massa](bulk-executor-dot-net.md) e [biblioteca de Java de executor do Using em massa](bulk-executor-java.md)
* A ferramenta de migração de dados, conforme descrito em [ferramenta de migração de bases de dados para o Azure Cosmos DB](import-data.md).
* Procedimentos armazenados, conforme descrito em [programação de JavaScript do lado do servidor para o Azure Cosmos DB](stored-procedures-triggers-udfs.md).

### <a name="does-the-sql-api-support-resource-link-caching"></a>É que o cache do link de recurso do suporte de API de SQL?

Sim, como o Azure Cosmos DB é um serviço RESTful, ligações de recursos são imutáveis e podem ser colocadas em cache. Os clientes de API de SQL podem especificar um cabeçalho de "If-None-Match" para leituras em relação a qualquer documento de tipo de recurso ou uma coleção e, em seguida, atualizar suas cópias locais depois da versão de servidor foi alterada.

### <a name="is-a-local-instance-of-sql-api-available"></a>Uma instância local da SQL API está disponível?

Sim. O [emulador do Azure Cosmos DB](local-emulator.md) fornece uma emulação de alta fidelidade do serviço do Cosmos DB. Ele oferece suporte a funcionalidade que é idêntica ao Azure Cosmos DB, incluindo suporte para criar e consultar documentos JSON, aprovisionamento e dimensionar coleções e a execução de procedimentos armazenados e acionadores. Pode desenvolver e testar aplicações com o emulador do Azure Cosmos DB e implementá-las para o Azure à escala global, fazendo uma única alteração de configuração para o ponto final de ligação para o Azure Cosmos DB.

### <a name="why-are-long-floating-point-values-in-a-document-rounded-when-viewed-from-data-explorer-in-the-portal"></a>Por que são valores longa vírgula flutuante num documento arredondado quando visualizado a partir do Explorador de dados no portal.

Esta é a limitação do JavaScript. JavaScript usa números de formato de vírgula flutuante de dupla precisão, conforme especificado na IEEE 754 e com segurança que pode conter números entre-(253 - 1) e 253-1 (ou seja, 9007199254740991) apenas.

### <a name="where-are-permissions-allowed-in-the-object-hierarchy"></a>Em que as permissões são permitidas na hierarquia do objeto?

Criação de permissões com ResourceTokens é permitido no nível do contentor e respetivos descendentes (como documentos, anexos). Isso implica que tentar criar uma permissão no banco de dados ou o nível de uma conta atualmente não é permitido.

## <a name="azure-cosmos-dbs-api-for-mongodb"></a>API do Azure Cosmos DB para MongoDB

### <a name="what-is-the-azure-cosmos-dbs-api-for-mongodb"></a>O que é a API do Azure Cosmos DB para MongoDB?

API do Azure Cosmos DB para o MongoDB é uma camada de compatibilidade de protocolo de transmissão que permite que aplicativos facilmente e transparente comunicar com o motor de base de dados do Azure Cosmos DB nativo, utilizando existente, com suporte da Comunidade SDKs e drivers para o MongoDB. Os programadores podem agora utilizar grupos de ferramentas do MongoDB existentes e as habilidades necessários para criar aplicações que tiram partido do Azure Cosmos DB. Os desenvolvedores se beneficiam as capacidades exclusivas do Azure Cosmos DB, que incluem a distribuição global com a replicação de vários mestre, manutenção de cópia de segurança, indexa automaticamente, apoiados financeiramente contratos de nível de serviço (SLAs) etc.

### <a name="how-do-i-connect-to-my-database"></a>Como posso ligar a minha base de dados?

A forma mais rápida para ligar a uma base de dados do Cosmos com a API do Azure Cosmos DB do MongoDB é head ao longo para o [portal do Azure](https://portal.azure.com). Aceda à sua conta e, em seguida, no menu de navegação esquerdo, clique em **início rápido**. Início rápido é a melhor forma de obter trechos de código para ligar à base de dados.

O Azure Cosmos DB impõe requisitos de segurança rigorosas e padrões. As contas do Azure Cosmos DB requerem autenticação e comunicação segura através de SSL, por isso, certifique-se de que utilize TLSv1.2.

Para obter mais informações, consulte [ligar à sua base de dados do Cosmos com a API do Azure Cosmos DB para o MongoDB](connect-mongodb-account.md).

Existem os códigos de erro adicionais que eu preciso lidar com ao utilizar a API do Azure Cosmos DB para o MongoDB?

Juntamente com os códigos de erro comuns do MongoDB, a API do Azure Cosmos DB para o MongoDB tem seus próprios códigos de erro específicas:

| Erro               | Código  | Descrição  | Solução  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | O número total de unidades de pedido consumida é mais do que a taxa de unidade de pedido de aprovisionamento para a coleção e otimizado. | Considere aumentar o débito atribuído a um contentor ou um conjunto de contentores do Azure portal ou a repetir novamente. |
| ExceededMemoryLimit | 16501 | Como um serviço de multi-inquilino, tornou-se a operação ao longo de alocação de memória do cliente. | Reduza o âmbito da operação por meio de critérios de consulta mais restritivas ou contacte o suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). <br><br>Example: <em>&nbsp;&nbsp;&nbsp;&nbsp;db.getCollection('users').aggregate([<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$match: {nome: "Andy"}}, <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$sort: {age: -1}}<br>&nbsp;&nbsp;&nbsp;&nbsp;])</em>) |

### <a name="is-the-simba-driver-for-mongodb-supported-for-use-with-azure-cosmos-dbs-api-for-mongodb"></a>O driver Simba destina-se a MongoDB suportado para utilização com a API do Azure Cosmos DB para o MongoDB?

Sim, pode utilizar controladores de ODBC do Mongo do Simba com a API do Azure Cosmos DB para o MongoDB

## <a id="table"></a>API de Tabela

### <a name="how-can-i-use-the-table-api-offering"></a>Como posso utilizar a oferta de API de tabela?

A API de tabela do Azure Cosmos DB está disponível na [portal do Azure][azure-portal]. Primeiro, deve inscrever para uma subscrição do Azure. Depois que tiver se inscrito, pode adicionar uma conta de API de tabela do Azure Cosmos DB para a sua subscrição do Azure e, em seguida, adicionar tabelas à sua conta.

Pode encontrar os idiomas suportados e associados inícios Rápidos no [introdução à API de tabela do Azure Cosmos DB](table-introduction.md).

### <a name="do-i-need-a-new-sdk-to-use-the-table-api"></a>É necessário um novo SDK para utilizar a API de tabela?

Não, existentes armazenamento que SDKs deverá continuar a funcionar. No entanto, é recomendável que sempre obtém os SDKs mais recentes para o melhor suporte e em muitos casos desempenho superior. Ver a lista de idiomas de disponíveis no [introdução à API de tabela do Azure Cosmos DB](table-introduction.md).

### <a name="where-is-table-api-not-identical-with-azure-table-storage-behavior"></a>Em que a API de tabela não é idêntico com o comportamento de armazenamento de tabelas do Azure?

Existem algumas diferenças de comportamento que os utilizadores provenientes do armazenamento de tabelas do Azure que pretendem criar tabelas com a API de tabela do Azure Cosmos DB devem ter conhecimento:

* API de tabela do Azure Cosmos DB utiliza um modelo de capacidade de reserva para garantir o desempenho garantido, mas isso significa que paga a capacidade de assim que a tabela é criada, mesmo que a capacidade não está a ser utilizada. Com tabelas do Azure o armazenamento um paga apenas a capacidade que é utilizada. Isto ajuda a explicar por que a API de tabela pode oferecer a leitura dos 10 ms e 15 ms escrita SLA no percentil 99, enquanto o armazenamento de tabelas do Azure oferece um SLA de 10 segundos. Mas, como conseqüência, com tabelas de API de tabela, até mesmo esvaziar tabelas sem todos os pedidos, custo dinheiro para garantir que a capacidade está disponível para processar pedidos aos mesmos no SLA oferecidos pelo Azure Cosmos DB.
* Resultados devolvidos pela API de tabela não são ordenados por ordem de chave de linha/chave de partição como estão no armazenamento de tabelas do Azure.
* Chaves de linha só é possível até 255 bytes
* Lotes só podem ter até 2 MB
* CORS não é atualmente suportada
* Os nomes das tabelas no armazenamento de tabelas do Azure não são maiúsculas e minúsculas, mas estão na API de tabela do Azure Cosmos DB
* Alguns dos formatos internos do Azure Cosmos DB para informações de codificação, como campos binários, atualmente não são tão eficientes como um poderia desejar. Portanto, isso pode causar limitações inesperadas no tamanho dos dados. Por exemplo, atualmente é não foi possível usar a um total Meg de uma entidade de tabela para armazenar dados binários, uma vez que a codificação aumenta o tamanho dos dados.
* Nome de propriedade da entidade "Id" não suportado atualmente
* TableQuery TakeCount não se limita a 1000

Em termos da API REST, existem várias opções de consulta/pontos finais que não são suportados pela API de tabela do Azure Cosmos DB:

| Método (s) de REST | Opção de consulta/ponto final de REST | URLs de documento | Explicação |
| ------------| ------------- | ---------- | ----------- |
| OBTER, COLOCAR | /?restype=service@comp=properties| [Definir as propriedades do serviço de tabela](https://docs.microsoft.com/rest/api/storageservices/set-table-service-properties) e [obter as propriedades do serviço de tabela](https://docs.microsoft.com/rest/api/storageservices/get-table-service-properties) | Este ponto final é utilizado para definir regras CORS, configuração de análise de armazenamento e definições de registo. CORS não é atualmente suportado e o registo e análise são processadas forma diferente no Azure Cosmos DB que as tabelas de armazenamento do Azure |
| OPÇÕES | / < nome de tabela-recursos > | [Pedido de tabela CORS de simulação](https://docs.microsoft.com/rest/api/storageservices/preflight-table-request) | Esta é a parte do CORS que atualmente não suporta o Azure Cosmos DB. |
| GET | /?restype=service@comp=stats | [Obter estatísticas do serviço tabela](https://docs.microsoft.com/rest/api/storageservices/get-table-service-stats) | Fornece informações como rapidamente dados está a replicar entre primárias e secundárias. Isso não é necessário no Cosmos DB como a replicação é a parte das escritas. |
| OBTER, COLOCAR | /MyTable? comp = acl | [Obter a tabela de ACL](https://docs.microsoft.com/rest/api/storageservices/get-table-acl) e [Definir tabela de ACL](https://docs.microsoft.com/rest/api/storageservices/set-table-acl) | Isso obtém e define as políticas de acesso armazenadas utilizadas para gerir assinaturas de acesso partilhado (SAS). Embora seja suportada SAS, eles são estabelecidos e gerenciados de forma diferente. |

Além disso API de tabela do Azure Cosmos DB suporta apenas o formato JSON, não ATOM.

Enquanto o Azure Cosmos DB suporta partilhado assinaturas de acesso (SAS) existem determinadas políticas, que ele não dá suporte, especificamente aqueles relacionados com operações de gestão, como o direito de criar novas tabelas.

Para o SDK de .NET em particular, existem algumas classes e métodos que atualmente não suporta o Azure Cosmos DB.

| Classe | Método não suportado |
|-------|-------- |
| CloudTableClient | \*ServiceProperties * |
|                  | \*ServiceStats * |
| CloudTable | SetPermissions * |
|            | GetPermissions * |
| TableServiceContext | * (essa classe é preterida) |
| TableServiceEntity | " " |
| TableServiceExtensions | " " |
| TableServiceQuery | " " |

Se qualquer uma dessas diferenças são um problema para o seu projeto, contacte [ askcosmosdb@microsoft.com ](mailto:askcosmosdb@microsoft.com) e informe-nos.

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>Como posso enviar comentários sobre o SDK ou bugs?

Pode partilhar os seus comentários em qualquer uma das seguintes formas:

* [Voz do utilizador](https://feedback.azure.com/forums/263030-azure-cosmos-db)
* [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Estouro de pilha é melhor para questões de programação. Certifique-se de que é a sua pergunta [no tópico](https://stackoverflow.com/help/on-topic) e [fornecer tantos detalhes quanto possível, fazer a pergunta clara e answerable](https://stackoverflow.com/help/how-to-ask).

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api"></a>O que é a cadeia de ligação que preciso para utilizar para ligar para a API de tabela?

A cadeia de ligação é:

```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmos DB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountName>.table.cosmosdb.azure.com
```

Pode obter a cadeia de ligação a partir da página de cadeia de ligação no portal do Azure.

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-net-sdk-for-the-table-api"></a>Como posso substituir as definições de configuração para as opções de pedido no SDK do .NET para a API de tabela?

Algumas definições são processadas no método CreateCloudTableClient e outros via o App. config na seção appSettings no aplicativo cliente. Para obter informações sobre definições de configuração, consulte [capacidades do Azure Cosmos DB](tutorial-develop-table-dotnet.md).

### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-azure-table-storage-sdks"></a>Existem alterações para os clientes que estão a utilizar o armazenamento de tabelas do Azure SDKs existente?

Nenhum. Não há nenhuma alteração para clientes de novas ou existentes que estão a utilizar o armazenamento de tabelas do Azure existente SDKs.

### <a name="how-do-i-view-table-data-thats-stored-in-azure-cosmos-db-for-use-with-the-table-api"></a>Como faço para visualizar dados de tabela que são armazenados no Azure Cosmos DB para utilização com a API de tabela?

Pode utilizar o portal do Azure para navegar pelos dados. Também pode utilizar o código de API de tabela ou as ferramentas que mencionei na resposta seguinte.

### <a name="which-tools-work-with-the-table-api"></a>Quais são as ferramentas funcionam com a API de tabela?

Pode utilizar o [Explorador de armazenamento do Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

Ferramentas com a flexibilidade de tirar uma cadeia de ligação no formato especificado anteriormente podem suportar a nova API de tabela. É fornecida uma lista de ferramentas de tabela no [ferramentas de cliente de armazenamento do Azure](../storage/common/storage-explorers.md) página.

### <a name="is-the-concurrency-on-operations-controlled"></a>É a simultaneidade em operações controladas?

Sim, a simultaneidade otimista é fornecida através da utilização do mecanismo ETag.

### <a name="is-the-odata-query-model-supported-for-entities"></a>O modelo de consulta de OData é suportado para entidades?

Sim, a API de tabela oferece suporte a consulta de OData e consulta LINQ.

### <a name="can-i-connect-to-azure-table-storage-and-azure-cosmos-db-table-api-side-by-side-in-the-same-application"></a>Posso ligar ao armazenamento de tabelas do Azure e a API de tabela do Azure Cosmos DB lado a lado no mesmo aplicativo?

Sim, pode ligar através da criação de duas instâncias separadas da CloudTableClient, cada um apontando para o seu próprio URI através da cadeia de ligação.

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-offering"></a>Como posso migrar um aplicativo de armazenamento de tabelas do Azure existente a esta oferta?

[AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) e o [ferramenta de migração de dados do Azure Cosmos DB](import-data.md) são ambas suportada.

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>Como é a expansão do tamanho do armazenamento feito por este serviço se, por exemplo, começar com *n* GB de dados e meus dados, irá aumentar a 1 TB ao longo do tempo?

O Azure Cosmos DB foi concebido para fornecer armazenamento ilimitado através da utilização de dimensionamento horizontal. O serviço pode monitorizar e aumentar o seu armazenamento com eficiência.

### <a name="how-do-i-monitor-the-table-api-offering"></a>Como posso monitorizar a oferta de API de tabela?

Pode usar a API de tabela **métricas** painel para monitorizar pedidos e a utilização de armazenamento.

### <a name="how-do-i-calculate-the-throughput-i-require"></a>Como posso calcular o débito de que preciso?

Pode usar o avaliador de capacidade para calcular o TableThroughput necessários para as operações. Para obter mais informações, consulte [unidades de pedido de estimativa e o armazenamento de dados](https://www.documentdb.com/capacityplanner). Em geral, pode mostrar a entidade como JSON e fornecer os números das suas operações.

### <a name="can-i-use-the-table-api-sdk-locally-with-the-emulator"></a>Posso utilizar o SDK de API de tabela localmente com o emulador?

Neste momento, não.

### <a name="can-my-existing-application-work-with-the-table-api"></a>Meu aplicativo existente pode trabalhar com a API de tabela?

Sim, é suportada a mesma API.

### <a name="do-i-need-to-migrate-my-existing-azure-table-storage-applications-to-the-sdk-if-i-dont-want-to-use-the-table-api-features"></a>É necessário migrar as minhas aplicações de armazenamento de tabelas do Azure existentes para o SDK desejo usar os recursos da API de tabela?

Não, pode criar e utilizar recursos de armazenamento de tabelas do Azure existentes sem interrupção de qualquer tipo. No entanto, se não usar a API de tabela, não pode se beneficiar de índices automática, a opção de consistência adicionais ou distribuição global.

### <a name="how-do-i-add-replication-of-the-data-in-the-table-api-across-more-than-one-region-of-azure"></a>Como adicionar replicação dos dados na API de tabela em mais do que uma região do Azure?

Pode utilizar o portal Azure Cosmos DB [as definições de replicação global](tutorial-global-distribution-sql-api.md#portal) adicionar regiões que são adequadas para a sua aplicação. Para desenvolver uma aplicação distribuída globalmente, deverá ainda adicionar o seu aplicativo com as informações de PreferredLocation definidas para a região local para o fornecimento de baixa latência de leitura.

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-table-api"></a>Como posso alterar a região de escrita principal para a conta na API de tabela?

Pode utilizar o painel do portal do Azure Cosmos DB replicação global para adicionar uma região e, em seguida, efetuar a ativação pós-falha para a região necessária. Para obter instruções, consulte [desenvolver com contas do Azure Cosmos DB de várias regiões](high-availability.md).

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>Como posso configurar meu regiões de leitura preferenciais de baixa latência quando posso distribuir meus dados?

Para ajudar a ler a partir da localização local, utilize a chave de PreferredLocation no ficheiro App. config. Para aplicativos existentes, a API de tabela emite um erro se LocationMode está definido. Remova esse código, uma vez que a API de tabela são recolhidos por esta informação do ficheiro App. config. 

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api"></a>Como deve pensar sobre os níveis de consistência na API de tabela?

O Azure Cosmos DB fornece bem manter compromissos entre consistência, disponibilidade e latência. O Azure Cosmos DB oferece cinco níveis de consistência aos desenvolvedores de API de tabela, para que possa escolher o modelo de consistência direita no nível da tabela e fazer pedidos individuais ao consultar os dados. Quando se liga um cliente, pode especificar um nível de consistência. Pode alterar o nível via o argumento de consistencyLevel de CreateCloudTableClient.

A API de tabela fornece leituras de latência baixa com "ler suas próprias escritas" com consistência de limitado vinculada como predefinição. Para obter mais informações, consulte [níveis de consistência](consistency-levels.md).

Por predefinição, o armazenamento de tabelas do Azure fornece consistência forte dentro de uma região e consistência Eventual nas localizações secundárias.

### <a name="does-azure-cosmos-db-table-api-offer-more-consistency-levels-than-azure-table-storage"></a>API de tabela do Azure Cosmos DB oferece mais níveis de consistência que o armazenamento de tabelas do Azure?

Sim, para obter informações sobre como se beneficiar da natureza distribuída do Azure Cosmos DB, consulte [níveis de consistência](consistency-levels.md). Porque garantias são fornecidas para os níveis de consistência, pode usá-los com confiança.

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>Quando a distribuição global está ativada, o tempo que demora a replicar os dados?

O Azure Cosmos DB consolida os dados de maneira duradoura na região local e envia os dados para outras regiões imediatamente em questão de milissegundos. Esta replicação é depende apenas o tempo de ida e volta (RTT) do Data Center. Para saber mais sobre a capacidade de distribuição global do Azure Cosmos DB, veja [do Azure Cosmos DB: Um serviço de base de dados globalmente distribuída no Azure](distribute-data-globally.md).

### <a name="can-the-read-request-consistency-level-be-changed"></a>O nível de consistência de solicitação de leitura pode ser alterado?

Com o Azure Cosmos DB, pode definir o nível de consistência ao nível do contentor (na tabela). Com o SDK de .NET, pode alterar o nível ao fornecer o valor de chave de TableConsistencyLevel no ficheiro App. config. Os valores possíveis são: Forte, estagnação limitada, sessão, prefixo consistente e Eventual. Para obter mais informações, consulte [níveis de consistência Sincronizáveis dados no Azure Cosmos DB](consistency-levels.md). A idéia-chave é que não é possível definir a consistência de pedido nível em mais do que a definição da tabela. Por exemplo, não é possível definir o nível de consistência para a tabela na Eventual e o nível de consistência pedido em forte.

### <a name="how-does-the-table-api-handle-failover-if-a-region-goes-down"></a>Como a API de tabela com ativação pós-falha se uma região ficar inativo?

A API de tabela tira partido da plataforma de distribuição global do Azure Cosmos DB. Para garantir que seu aplicativo pode tolerar o período de indisponibilidade do Centro de dados, ative, pelo menos, uma região de mais para a conta no portal do Azure Cosmos DB [desenvolver com contas do Azure Cosmos DB de várias regiões](high-availability.md). Pode definir a prioridade da região com o portal [desenvolver com contas do Azure Cosmos DB de várias regiões](high-availability.md).

Pode adicionar tantas regiões à medida que pretende para a conta e controla onde ele pode efetuar a ativação pós-falha, fornecendo uma prioridade de ativação pós-falha. Para utilizar a base de dados, terá de fornecer um aplicativo aqui também. Ao fazê-lo, os seus clientes não ocorrerá um período de indisponibilidade. O [SDK mais recente do cliente .NET](table-sdk-dotnet.md) é automática homing, mas o outro não são SDKs. Ou seja, pode detetar a região que está inativo e automaticamente a ativação pós-falha para a nova região.

### <a name="is-the-table-api-enabled-for-backups"></a>A API de tabela está ativada para cópias de segurança?

Sim, a API de tabela tira partido da plataforma do Azure Cosmos DB para cópias de segurança. As cópias de segurança são efetuadas automaticamente. Para obter mais informações, consulte [Online backup e restauração com o Azure Cosmos DB](online-backup-and-restore.md).

### <a name="does-the-table-api-index-all-attributes-of-an-entity-by-default"></a>A API de tabela de índice todos os atributos de uma entidade por predefinição?

Sim, todos os atributos de uma entidade são indexados por predefinição. Para obter mais informações, consulte [do Azure Cosmos DB: Políticas de indexação](index-policy.md).

### <a name="does-this-mean-i-dont-have-to-create-more-than-one-index-to-satisfy-the-queries"></a>É que isso significa que eu não preciso criar mais do que um índice para satisfazer as consultas?

Sim, a API de tabela do Azure Cosmos DB fornece uma indexação automática de todos os atributos sem qualquer definição de esquema. Esta automatização isenta os desenvolvedores concentrar-se a aplicação em vez de criação de índices e gestão. Para obter mais informações, consulte [do Azure Cosmos DB: Políticas de indexação](index-policy.md).

### <a name="can-i-change-the-indexing-policy"></a>Pode alterar a política de indexação?

Sim, pode alterar a política de indexação ao fornecer a definição do índice. Terá de codificar e as definições de escape adequadamente.

Para os não - .NET SDKs, a política de indexação só pode ser definida no portal em **Data Explorer**, navegue para a tabela específica que pretende alterar e, em seguida, vá para o **dimensionamento e definições**-> política de indexação, Faça a alteração pretendida e, em seguida **guardar**.

Do SDK do .NET que pode ser submetido no ficheiro App. config:

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

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-sorting-aggregates-hierarchy-and-other-functionality-will-you-be-adding-these-capabilities-to-the-table-api"></a>O Azure Cosmos DB como uma plataforma parece ter vários recursos, tais como a ordenação, agregações, hierarquia e outras funcionalidades. Adicionará estas capacidades para a API de tabela?

A API de tabela fornece a mesma funcionalidade de consulta como armazenamento de tabelas do Azure. Também suporta ordenação, agregações, consultas geoespaciais, hierarquias e um vasto conjunto de funções incorporadas. Para obter mais informações, consulte [consultas SQL](how-to-sql-query.md).

### <a name="when-should-i-change-tablethroughput-for-the-table-api"></a>Quando posso alterar TableThroughput para a API de tabela?

Deve alterar TableThroughput quando qualquer uma das seguintes condições se aplicar:

* Estiver a efetuar uma extração, transformação e carregamento (ETL) de dados ou, se pretender carregar uma grande quantidade de dados num curto período de tempo.
* Precisa de mais débito do contentor ou a partir de um conjunto de contentores em back-end. Por exemplo, verá que a taxa de transferência utilizada é mais do que o débito aprovisionado e está obtendo limitada. Para obter mais informações, consulte [definir débito de contentores do Azure Cosmos DB](set-throughput.md).

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-table"></a>Pode aumentar ou reduzir verticalmente o débito da minha tabela de API de tabela?

Sim, pode utilizar o painel de escala do portal do Azure Cosmos DB para dimensionar o débito. Para obter mais informações, consulte [definir débito](set-throughput.md).

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>É um padrão que tablethroughput definida para tabelas recentemente aprovisionadas?

Sim, se não substituir TableThroughput via App. config e não utilizar um contentor previamente criado no Azure Cosmos DB, o serviço cria uma tabela com um débito de 400.

### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-azure-table-storage-service"></a>Existe qualquer alteração de preços para os clientes existentes do serviço de armazenamento de tabelas do Azure?

Nenhum. Não há nenhuma alteração no preço para os clientes existentes do armazenamento de tabelas do Azure.

### <a name="how-is-the-price-calculated-for-the-table-api"></a>Como é calculado o preço para a API de tabela?

O preço depende do TableThroughput alocado.

### <a name="how-do-i-handle-any-rate-limiting-on-the-tables-in-table-api-offering"></a>Como com qualquer limitação de velocidade em tabelas na oferta de API de tabela?

Se a taxa de pedidos é mais do que a capacidade do débito aprovisionado para o contentor subjacente ou um conjunto de contentores, obterá um erro e o SDK repetirá a chamada ao aplicar a política de repetição.

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-table-api-offering-of-azure-cosmos-db"></a>Por que motivo é necessário escolher uma taxa de transferência para além dos PartitionKey e RowKey para tirar partido da oferta de API de tabela do Azure Cosmos DB?

O Azure Cosmos DB define uma taxa de transferência predefinido para o seu contentor, se não fornecer uma no ficheiro App. config ou através do portal.

O Azure Cosmos DB fornece garantias de desempenho e latência, com limites operação. Essa garantia é possível quando o mecanismo pode impor a governação nas operações do inquilino. Definição TableThroughput garante que obtenha o débito garantido e a latência, uma vez que a plataforma esta capacidade de reserva-se e garantias de êxito operacional.

Ao utilizar a especificação de débito, pode de forma elástica alterá-lo para se beneficiar de sazonalidade do seu aplicativo, satisfazer as necessidades de débito e reduzir os custos.

### <a name="azure-table-storage-has-been-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-azure-cosmos-db-table-api-offering-seems-to-be-charging-me-even-though-i-havent-performed-a-single-transaction-or-stored-anything-can-you-explain"></a>Armazenamento de tabelas do Azure tem sido baixo custo para mim, porque eu presto apenas armazenar os dados e eu raramente consulta. A oferta de API de tabela do Azure Cosmos DB parece ser cobrar-me, embora eu ainda não realizada uma única transação ou armazenados nada. Pode explicar?

O Azure Cosmos DB foi concebido para ser um sistema distribuído globalmente com base no SLA com garantias de disponibilidade, latência e débito. Quando a reservar débito no Azure Cosmos DB, é garantido, ao contrário da taxa de transferência de outros sistemas. O Azure Cosmos DB fornece capacidades adicionais que os clientes solicitaram, como índices secundários e distribuição global.

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-azure-table-storage-with-the-table-api-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>Notificação de nunca entendi, uma quota total"(que indica que uma partição está cheio) quando eu ingerir dados no armazenamento de tabelas do Azure. Com a API de tabela, eu receber esta mensagem. Isso oferece limitar-me e me forçar a alterar a minha aplicação existente?

O Azure Cosmos DB é um sistema com base no SLA que oferece um dimensionamento ilimitado, sem garantias de latência, débito, disponibilidade e consistência. Para garantir um desempenho premium garantida, certifique-se de que o tamanho dos dados e índice são gerenciável e dimensionável. O limite de 10 GB no número de entidades ou itens por chave de partição é garantir que podemos fornecer um excelente desempenho de pesquisa e consulta. Para garantir que seu aplicativo se dimensione bem, mesmo para o armazenamento do Azure, recomendamos que *não* criar uma partição de acesso frequente ao armazenar todas as informações de uma partição e consultá-lo.

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-table-api"></a>Então, PartitionKey e RowKey ainda são necessárias com a API de tabela?

Sim. Como a área de superfície da API de tabela é semelhante à que o SDK de armazenamento de tabelas do Azure, a chave de partição fornece uma maneira eficiente para distribuir os dados. A chave de linha é única dentro dessa partição. A chave de linha tem de estar presente e não pode ser nula como no SDK do padrão. O comprimento do RowKey é 255 bytes e o comprimento de PartitionKey é de 1 KB.

### <a name="what-are-the-error-messages-for-the-table-api"></a>Quais são as mensagens de erro para a API de tabela?

API de tabela do Azure Cosmos DB e armazenamento de tabelas do Azure utilizam os SDKs do mesmo, por isso, a maioria dos erros será o mesmo.

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api"></a>Por que, é limitado ao tentar criar um após o outro muitas tabelas na API de tabela?

O Azure Cosmos DB é um sistema com base no SLA que fornece a latência, débito, disponibilidade e garantias de consistência. Como é um sistema aprovisionado, se a reserva recursos para garantir a esses requisitos. A taxa de rápida da criação de tabelas é detectada e limitada. Recomendamos que examinar a taxa de criação de tabelas e reduzi-lo para menos de 5 por minuto. Lembre-se de que a API de tabela é um sistema aprovisionado. No momento em que aprovisioná-lo, começará a pagá-la.

## <a name="gremlin-api"></a>API do Gremlin

### <a name="for-cnet-development-should-i-use-the-microsoftazuregraphs-package-or-gremlinnet"></a>No c# / desenvolvimento do .NET, devo utilizar o pacote de Microsoft.Azure.Graphs ou Gremlin.NET?

API de Gremlin do Azure Cosmos DB tira partido os controladores de código-fonte aberto como os conectores principais para o serviço. Para que a opção recomendada é usar [drivers que são suportados pela Apache Tinkerpop](https://tinkerpop.apache.org/).

### <a name="how-are-rus-charged-when-running-queries-on-a-graph-database"></a>Como os RU/s são cobrados quando a execução de consultas numa base de dados?

Todos os objetos de gráfico, vértices e margens, são apresentadas como documentos JSON no back-end. Uma vez que uma consulta do Gremlin pode modificar um ou muitos objetos de gráfico ao mesmo tempo, o custo associado ele está diretamente relacionada com os objetos, margens que são processados pela consulta. Este é o mesmo processo que utiliza o Azure Cosmos DB para todas as outras APIs. Para obter mais informações, consulte [unidades de pedido no Azure Cosmos DB](request-units.md).

O custo de RU baseia-se no conjunto de dados de trabalho do transversal e não o resultado definido. Por exemplo, se uma consulta tem como objetivo obter um único vértice como resultado, mas precisa atravessar mais do que um outro objeto no caminho, em seguida, o custo terá por base em todos os objetos de gráfico que irá demorar para computar o vértice de um resultado.

### <a name="whats-the-maximum-scale-that-a-graph-database-can-have-in-azure-cosmos-db-gremlin-api"></a>O que é o dimensionamento máximo que uma base de dados pode ter na API do Gremlin do Azure Cosmos DB?

O Azure Cosmos DB faz uso de [criação de partições horizontais](partition-data.md) automaticamente endereço aumento nos requisitos de armazenamento e débito. A capacidade máxima de armazenamento e débito de uma carga de trabalho é determinada pelo número de partições que estão associadas uma dada coleção. No entanto, uma coleção da Gremlin API tem um conjunto específico de diretrizes para garantir uma experiência de desempenho adequado à escala. Para obter mais informações sobre a criação de partições e as melhores práticas, consulte [criação de partições no Azure Cosmos DB](partition-data.md) artigo.

### <a name="how-can-i-protect-against-injection-attacks-using-gremlin-drivers"></a>Como posso proteger contra ataques de injeção usando drivers de Gremlin?

Controladores de Tinkerpop Gremlin mais nativos que a opção fornecer um dicionário de parâmetros para a execução da consulta. Este é um exemplo de como fazê-lo na [Gremlin.Net](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet) e, na [Gremlin-Javascript](https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started/blob/master/app.js).

### <a name="why-am-i-getting-the-gremlin-query-compilation-error-unable-to-find-any-method-error"></a>Por que eu ganho o "erro de compilação de consulta do Gremlin: Não é possível localizar qualquer método"erro?

API de Gremlin do Azure Cosmos DB implementa um subconjunto da funcionalidade definida na área de superfície do Gremlin. Para obter passos suportados e obter mais informações, consulte [suporte para Gremlin](gremlin-support.md) artigo.

A melhor solução alternativa é reescrever as etapas necessárias do Gremlin com a funcionalidade suportada, uma vez que todos os passos de Gremlin essenciais são suportados pelo Azure Cosmos DB.

### <a name="why-am-i-getting-the-websocketexception-the-server-returned-status-code-200-when-status-code-101-was-expected-error"></a>Por que eu ganho o "WebSocketException: O servidor devolveu o código de estado "200", quando o código de estado '101' era esperado"erro?

Este erro provavelmente é lançado quando o ponto de extremidade errado que está a ser utilizado. O ponto final que gera esse erro tem o seguinte padrão:

`https:// YOUR_DATABASE_ACCOUNT.documents.azure.com:443/`

Este é o ponto de final de documentos para a base de dados do gráfico.  O ponto final correto a utilizar é o ponto de final do Gremlin, que tem o seguinte formato:

`https://YOUR_DATABASE_ACCOUNT.gremlin.cosmosdb.azure.com:443/`

### <a name="why-am-i-getting-the-requestrateistoolarge-error"></a>Por que estou a receber o erro "RequestRateIsTooLarge"?

Este erro significa que o alocado unidades de pedido por segundo não são suficientes para servir a consulta. Este erro normalmente é visto quando executa uma consulta que obtém todos os vértices:

```
// Query example:
g.V()
```

Esta consulta irá tentar obter todos os vértices do gráfico. Então, o custo dessa consulta será igual a, pelo menos, o número de vértices em termos de RUs. A definição de RU/s deve ser ajustada para abordar esta consulta.

### <a name="why-do-my-gremlin-driver-connections-get-dropped-eventually"></a>Por que minhas ligações de driver do Gremlin são removidas, eventualmente?

Uma ligação de Gremlin é feita por meio de uma conexão WebSocket. Embora o WebSocket ligações não tem uma hora específica ao vivo, API Gremlin do Azure Cosmos DB irá terminar ligações inativas após 30 minutos de inatividade.

### <a name="why-cant-i-use-fluent-api-calls-in-the-native-gremlin-drivers"></a>Por que não usar chamadas de API fluentes em drivers de Gremlin nativos?

Chamadas de API Fluent ainda não são suportadas pela API de Gremlin do Azure Cosmos DB. Chamadas de API Fluent requerem uma funcionalidade de formatação interna conhecida como suporte de bytecode que atualmente não é suportado pela API do Azure Cosmos DB Gremlin. Devido a essa mesma razão, o controlador mais recente do Gremlin-JavaScript também não é atualmente suportado.

### <a name="how-can-i-evaluate-the-efficiency-of-my-gremlin-queries"></a>Como posso avaliar a eficiência das minhas consultas do Gremlin?

O **executionProfile()** passo de pré-visualização pode ser utilizado para fornecer uma análise do plano de execução de consulta. Este passo tem de ser adicionado ao final de qualquer consulta Gremlin, conforme ilustrado no exemplo seguinte:

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

O resultado do perfil acima mostra quanto tempo é gasto a obter os objetos de vértice, os objetos de borda e o tamanho do conjunto de dados de trabalho. Isso está relacionado com as medidas de custo padrão para consultas do Azure Cosmos DB.

## <a id="cassandra"></a> API para Cassandra

### <a name="what-is-the-protocol-version-supported-by-azure-cosmso-db-cassandra-api-is-there-a-plan-to-support-other-protocols"></a>O que é a versão do protocolo suportada pela API de Cassandra do Azure Cosmso DB? Existe um plano para suportar outros protocolos?

API Apache Cassandra para o Azure Cosmos DB suporta atualmente a versão CQL 4. Se tiver comentários sobre o suporte de outros protocolos, fale conosco [comentários da voz do utilizador](https://feedback.azure.com/forums/263030-azure-cosmos-db) ou envie um e-mail para [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com).

### <a name="why-is-choosing-a-throughput-for-a-table-a-requirement"></a>Por isso que escolhe um débito de uma tabela de um requisito?

O Azure Cosmos DB define o débito predefinido para o contentor com base em onde criar a tabela de - portal ou CQL.
O Azure Cosmos DB fornece garantias de desempenho e latência, com limites operação. Essa garantia é possível quando o mecanismo pode impor a governação nas operações do inquilino. Débito de definição garante que obtenha o débito garantido e a latência, uma vez que a plataforma esta capacidade de reserva-se e garantias de êxito da operação.
Elástica pode alterar o débito para se beneficiar de sazonalidade do seu aplicativo e reduzir os custos.

O conceito de débito é explicado no [unidades de pedido no Azure Cosmos DB](request-units.md) artigo. O débito de uma tabela é distribuído entre as partições físicas subjacentes igualmente.

### <a name="what-is-the-default-rus-of-table-when-created-through-cql-what-if-i-need-to-change-it"></a>O que é a predefinição de RU/s da tabela quando criado por meio de CQL? E se eu precisar de alterá-la?

O Azure Cosmos DB utiliza unidades de pedido por segundo (RU/s) como uma moeda para fornecer o débito. As tabelas criadas por meio de CQL ter 400 RU. Pode alterar o RU do portal.

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

### <a name="what-happens-when-throughput-is-used-up"></a>O que acontece quando o débito é utilizado a cópia de segurança?

O Azure Cosmos DB fornece garantias de desempenho e latência, com limites operação. Essa garantia é possível quando o mecanismo pode impor a governação nas operações do inquilino. Isso é possível com base na definição a taxa de transferência, o que garante que obtenha o débito garantido e a latência, uma vez que esta capacidade de reserva-se de plataforma e garantias de êxito da operação.
Quando vai sobre esta capacidade, obtém mensagem de erro sobrecarregado indicando que sua capacidade foi utilizada a cópia de segurança.
0x1001 sobrecarregado: não é possível processar o pedido porque "Taxa de pedidos é grande". Neste juncture, é essencial para saber quais operações e volume faz com que este problema. Pode obter uma idéia sobre a capacidade consumida acontecendo sobre a capacidade aprovisionada com métricas no portal. Em seguida, certifique-se de capacidade é quase consumida igualmente todas as partições subjacente. Se vir a maior parte da taxa de transferência é consumido por partição, tem distorção da carga de trabalho.

As métricas estão disponíveis que mostram como débito é utilizado durante horas, dias e por sete dias, entre partições ou em forma agregada. Para obter mais informações, consulte [monitorização e depuração com métricas na Azure Cosmos DB](use-metrics.md).

Os registos de diagnóstico são explicados no [registo de diagnósticos do Azure Cosmos DB](logging.md) artigo.

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>É que o mapa de chave primário para o conceito de chave de partição do Azure Cosmos DB?

Sim, a chave de partição é utilizada para colocar a entidade na localização correta. No Azure Cosmos DB, é utilizado para localizar a partição lógica direito que está armazenada numa partição física. O conceito de criação de partições é explicado bem a [particionar e dimensionar no Azure Cosmos DB](partition-data.md) artigo. O essencial take distância aqui é que uma partição lógica não deve exceder o limite de 10 GB hoje mesmo.

### <a name="what-happens-when-i-get-a-quota-full-notification-indicating-that-a-partition-is-full"></a>O que acontece quando recebo uma quota total"notificação indicando que uma partição é completa?

O Azure Cosmos DB é um sistema com base no SLA que oferece um dimensionamento ilimitado, sem garantias de latência, débito, disponibilidade e consistência. Este armazenamento ilimitado baseia-se a escala horizontal fora de dados com a criação de partições como o conceito-chave. O conceito de criação de partições é explicado bem a [particionar e dimensionar no Azure Cosmos DB](partition-data.md) artigo.

O limite de 10 GB no número de entidades ou itens por partição lógica, que deve seguir. Para garantir que seu aplicativo se dimensione bem, recomendamos que *não* criar uma partição de acesso frequente ao armazenar todas as informações de uma partição e consultá-lo. Este erro pode são fornecidas apenas se os seus dados são desviados: ou seja, tem muitos dados para a chave de partição (mais de 10&nbsp;GB). Pode encontrar a distribuição de dados com o portal de armazenamento. Forma para corrigir este erro é recriar a tabela e escolha um principal granular (chave de partição), que permite uma melhor distribuição dos dados.

### <a name="is-it-possible-to-use-cassandra-api-as-key-value-store-with-millions-or-billions-of-individual-partition-keys"></a>É possível utilizar a Cassandra API como arquivo de chave-valor com milhões ou bilhões de chaves de partição individual?

O Azure Cosmos DB pode armazenar dados ilimitados ao aumentar horizontalmente o armazenamento. Este facto é independente da taxa de transferência. Sim sempre apenas pode utilizar a API de Cassandra para armazenar e obter chaves/valores ao especificar a chave primária/partição correta. Essas chaves individuais obtém sua própria partição lógica e permanecem acima da partição física sem problemas.

### <a name="is-it-possible-to-create-more-than-one-table-with-apache-cassandra-api-of-azure-cosmos-db"></a>É possível criar mais de uma tabela com o Apache Cassandra API do Azure Cosmos DB?

Sim, é possível criar mais de uma tabela com a Apache Cassandra API. Cada uma dessas tabelas é tratada como a unidade de débito e armazenamento.

### <a name="is-it-possible-to-create-more-than-one-table-in-succession"></a>É possível criar mais de uma tabela em sucessão?

O Azure Cosmos DB é regido por recursos de sistema para atividades de plano de dados e controle. Contentores, como coleções, as tabelas são entidades de tempo de execução que são aprovisionadas para devido a capacidade de débito. A criação destes contentores numa sucessão rápida não é esperada de atividade e limitada. Se tiver de testes que remova/criar tabelas imediatamente, tente espaço-los.

### <a name="what-is-maximum-number-of-tables-that-can-be-created"></a>O que é o número máximo de tabelas que podem ser criados?

Não tem qualquer limite físico no número de tabelas, envie um e-mail para [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com) se tiver grande número de tabelas (em que o tamanho de constante total exceder 10 TB de dados) que precisam de ser criado a partir habitual 10s ou 100s.

### <a name="what-is-the-maximum--of-keyspace-that-we-can-create"></a>O que é o n. º máximo de keyspace que podemos criar?

Não tem qualquer limite físico no número de keyspaces, pois eles são contentores de metadados, enviar um e-mail para [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com) se tiver grande número de keyspaces por algum motivo.

### <a name="is-it-possible-to-bring-in-lot-of-data-after-starting-from-normal-table"></a>É possível colocar em grande quantidade de dados após o início da tabela normal?

A capacidade de armazenamento é gerida automaticamente e aumenta à medida que emitir no mais dados. Portanto, é possível importar com confiança quantidade de dados que necessita sem gerenciamento e provisionamento de nós e muito mais.

### <a name="is-it-possible-to-supply-yaml-file-settings-to-configure-apache-casssandra-api-of-azure-cosmos-db-behavior"></a>É possível fornecer definições de ficheiro yaml para configurar o comportamento de Apache Casssandra API do Azure Cosmos DB?

O Apache Cassandra API do Azure Cosmos DB é um serviço de plataforma. Ele fornece compatibilidade de nível de protocolo para a execução de operações. Distância oculta a complexidade da gestão, monitorização e configuração. Como um desenvolvedor/utilizador, não precisa se preocupar sobre a disponibilidade, marcas para exclusão, cache de chave, cache de linha, filtro de bloom e inúmeras outras definições. Focos de Apache Cassandra API do Azure Cosmos DB no fornecimento de ler e gravar o desempenho de que necessita sem a sobrecarga de configuração e gestão.

### <a name="will-apache-cassandra-api-for-azure-cosmos-db-support-node-additioncluster-statusnode-status-commands"></a>API Apache Cassandra para o Azure Cosmos DB irá suportar a comandos de estado do nó adição/cluster/nó Estado?

Apache Cassandra API é um serviço de plataforma que facilita o planeamento de capacidade, a responder as demandas de elasticidade de débito e armazenamento muito simples. Com o Azure Cosmos DB aprovisionar o débito, precisa. Em seguida, pode dimensioná-lo e diminuir a qualquer número de vezes por dia sem se preocupar em Adicionar/eliminar nós ou gerenciá-los. Isso implica que não precisa de utilizar o nó, a ferramenta de gerenciamento de cluster também.

### <a name="what-happens-with-respect-to-various-config-settings-for-keyspace-creation-like-simplenetwork"></a>O que acontece em relação a várias definições de configuração para criação de keyspace como simples/rede?

O Azure Cosmos DB oferece distribuição global prontos a utilizar por motivos de baixa latência e de disponibilidade. Não precisa de réplicas de configuração ou outras coisas. Todas as escritas são sempre maneira duradoura quórum consolidada em qualquer região em que escreve e fornece garantias de desempenho.

### <a name="what-happens-with-respect-to-various-settings-for-table-metadata-like-bloom-filter-caching-read-repair-change-gcgrace-compression-memtableflushperiod-and-more"></a>O que acontece em relação a várias definições para os metadados de tabela, como o filtro de bloom, colocação em cache, leia a alteração de reparação, gc_grace, memtable_flush_period de compactação e muito mais?

O Azure Cosmos DB fornece desempenho para leituras e gravações e débito sem necessidade de alterar qualquer uma das definições de configuração e manipular acidentalmente.

### <a name="is-time-to-live-ttl-supported-for-cassandra-tables"></a>Time-to-live (TTL) é suportada para tabelas de Cassandra?

Sim, o TTL é suportada.

### <a name="is-it-possible-to-monitor-node-status-replica-status-gc-and-os-parameters-earlier-with-various-tools-what-needs-to-be-monitored-now"></a>É possível monitorizar o estado do nó, o estado da réplica, o gc e parâmetros de sistema operacional mais cedo com várias ferramentas? O que precisa ser monitorado agora?

O Azure Cosmos DB é um serviço de plataforma que o ajuda a aumentar a produtividade e não se preocupar sobre como gerir e monitorizar a infraestrutura. Terá de tomar conta de débito que está disponível em métricas do portal para encontrar se estiver obtendo limitada e aumentar ou diminuir esse débito.
Monitor [SLAs](monitor-accounts.md).
Uso [métricas](use-metrics.md) utilização [registos de diagnóstico](logging.md).

### <a name="which-client-sdks-can-work-with-apache-cassandra-api-of-azure-cosmos-db"></a>Os SDKs de cliente podem trabalhar com o Apache Cassandra API do Azure Cosmos DB?

Controladores do cliente do SDK do Apache Cassandra que utilizam CQLv3 foram utilizados para programas de cliente. Se tiver outros controladores que pode utilizar a ou se estiver a ter problemas, envie um e-mail para [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com).

### <a name="is-composite-partition-key-supported"></a>Chave de partição composta é suportada?

Sim, pode utilizar a sintaxe normal para criar a chave de partição compostos.

### <a name="can-i-use-stable-loader-for-data-loading"></a>Posso utilizar o carregador estável para carregamento de dados?

Não, durante a pré-visualização estável carregador não é suportada.

### <a name="can-an-on-premises-cassandra-cluster-be-paired-with-azure-cosmos-dbs-apache-cassandra-api"></a>Um cluster do cassandra no local pode ser emparelhado com o Azure Cosmos DB Apache Cassandra API?

No presente para o Azure Cosmos DB tem uma experiência otimizada para o ambiente de cloud sem sobrecarga de operações. Se necessitar de emparelhamento, envie um e-mail para [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com) com uma descrição do seu cenário.

### <a name="does-cassandra-api-provide-full-backups"></a>O Cassandra API fornece cópias de segurança completas?

O Azure Cosmos DB fornece dois gratuitas completas cópias de segurança criadas em intervalos de quatro horas hoje em todas as APIs. Isto garante que não precisa de configurar uma agenda de cópia de segurança e outras coisas.
Se pretender modificar a retenção e a frequência, envie um e-mail para [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com) ou emitir um pedido de suporte. São fornecidas informações sobre a capacidade de cópia de segurança no [cópia de segurança online automática e restauro com o Azure Cosmos DB](online-backup-and-restore.md) artigo.

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>Como a conta do Cassandra API com ativação pós-falha se uma região ficar inativo?

A API de Cassandra do Azure Cosmos DB empresta a partir da plataforma de distribuição global do Azure Cosmos DB. Para garantir que seu aplicativo pode tolerar o período de indisponibilidade do Centro de dados, ative, pelo menos, uma região de mais para a conta no portal do Azure Cosmos DB [desenvolver com contas do Azure Cosmos DB de várias regiões](high-availability.md). Pode definir a prioridade da região com o portal [desenvolver com contas do Azure Cosmos DB de várias regiões](high-availability.md).

Pode adicionar tantas regiões à medida que pretende para a conta e controla onde ele pode efetuar a ativação pós-falha, fornecendo uma prioridade de ativação pós-falha. Para utilizar a base de dados, terá de fornecer um aplicativo aqui também. Ao fazê-lo, os seus clientes não ocorrerá um período de indisponibilidade.

### <a name="does-the-apache-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>Apache Cassandra API indexar todos os atributos de uma entidade por predefinição?

Sim, todos os atributos de uma entidade são indexados por predefinição pelo Azure Cosmos DB. Para obter mais informações, consulte [do Azure Cosmos DB: Políticas de indexação](index-policy.md). Obtém os benefícios de desempenho garantido indexação consistente e durável quórum consolidada escreve sempre.

### <a name="does-this-mean-i-dont-have-to-create-more-than-one-index-to-satisfy-the-queries"></a>É que isso significa que eu não preciso criar mais do que um índice para satisfazer as consultas?

Sim, o Azure Cosmos DB fornece uma indexação automática de todos os atributos sem qualquer definição de esquema. Esta automatização isenta os desenvolvedores concentrar-se a aplicação em vez de criação de índices e gestão. Para obter mais informações, consulte [do Azure Cosmos DB: Políticas de indexação](index-policy.md).

### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>Posso utilizar o novo SDK de API de Cassandra localmente com o emulador?

Planeamos suportar esta capacidade no futuro.

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-change-feed-and-other-functionality-will-these-capabilities-be-added-to-the-cassandra-api"></a>O Azure Cosmos DB como uma plataforma parece ter vários recursos, como o feed de alterações e outras funcionalidades. Serão adicionados esses recursos para a API de Cassandra?

Apache Cassandra API fornece a mesma funcionalidade CQL Apache Cassandra. Planeamos examinar a viabilidade de oferecer suporte a vários recursos no futuro.

### <a name="feature-x-of-regular-cassandra-api-isnt-working-as-today-where-can-the-feedback-be-provided"></a>O recurso x da API de Cassandra regular não está a funcionar como hoje em dia, onde podem os comentários ser fornecidos?

Fornecer feedback através de [comentários da voz do utilizador](https://feedback.azure.com/forums/263030-azure-cosmos-db).

[azure-portal]: https://portal.azure.com
[query]: sql-api-sql-query.md
