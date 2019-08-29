---
title: Azure Cosmos DB dicas de desempenho para .NET
description: Aprenda as opções de configuração do cliente para melhorar o desempenho do banco de dados Cosmos do Azure
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: sngun
ms.openlocfilehash: bdf81eb447596c8f580809eed99004186a81eacf
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70065924"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net"></a>Dicas de desempenho para Azure Cosmos DB e .NET

> [!div class="op_single_selector"]
> * [Async Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

Azure Cosmos DB é um banco de dados distribuído rápido e flexível que é dimensionado diretamente com latência garantida e taxa de transferência. Você não precisa fazer alterações de arquitetura importantes ou escrever código complexo para dimensionar seu banco de dados com Azure Cosmos DB. Escalar verticalmente e reduzir é tão fácil quanto fazer uma única chamada à API. Para saber mais, consulte [como provisionar a produtividade do contêiner](how-to-provision-container-throughput.md) ou [como provisionar a taxa de transferência do banco de dados](how-to-provision-database-throughput.md). No entanto, como Azure Cosmos DB é acessado por meio de chamadas de rede, há otimizações do lado do cliente que você pode fazer para obter o máximo de desempenho ao usar o [SDK do .net do SQL](sql-api-sdk-dotnet-standard.md).

Então, se você estiver perguntando "como posso melhorar meu desempenho de banco de dados?" Considere as seguintes opções:

## <a name="networking"></a>Redes
<a id="direct-connection"></a>

1. **Política de conexão: Usar o modo de conexão direta**

    Como um cliente se conecta a Azure Cosmos DB tem implicações importantes no desempenho, especialmente em termos de latência de cliente observada. Há duas definições de configuração principais disponíveis para configurar a política de conexão do cliente – o *modo* de conexão e o *protocolo*de conexão.  Os dois modos disponíveis são:

   * Modo de gateway (padrão)
      
     O modo de gateway tem suporte em todas as plataformas SDK e é o padrão configurado. Se seu aplicativo for executado em uma rede corporativa com restrições de firewall estritas, o modo de gateway será a melhor opção, pois ele usa a porta HTTPS padrão e um único ponto de extremidade. No entanto, a compensação de desempenho é que o modo de gateway envolve um salto de rede adicional toda vez que os dados são lidos ou gravados Azure Cosmos DB. Por isso, o modo direto oferece melhor desempenho devido a menos saltos de rede. O modo de conexão do gateway também é recomendado quando você executa aplicativos em ambientes com número limitado de conexões de soquete, por exemplo, ao usar Azure Functions ou se você estiver em um plano de consumo. 

   * Modo direto

     O modo direto dá suporte à conectividade por meio de protocolos TCP e HTTPS. Se você estiver usando a versão mais recente do SDK do .NET, o modo de conectividade direta terá suporte no .NET Standard 2,0 e no .NET Framework. Ao usar o modo direto, há duas opções de protocolo disponíveis:

     * TCP
     * HTTPS

     Ao usar o modo Gateway, Cosmos DB usa a porta 443 e as portas 10250, 10255 e 10256 ao usar a API do Azure Cosmos DB para MongoDB. A porta 10250 é mapeada para uma instância padrão do MongoDB sem a replicação geográfica e as portas 10255/10256 são mapeadas para a instância do MongoDB com a funcionalidade de replicação geográfica. Ao usar TCP no modo direto, além das portas de gateway, você precisa garantir que o intervalo de portas entre 10000 e 20000 esteja aberto porque o Azure Cosmos DB usa portas TCP dinâmicas. Se essas portas não estiverem abertas e você tentar usar TCP, você receberá um erro 503 Serviço indisponível. A tabela a seguir mostra os modos de conectividade disponíveis para diferentes APIs e o usuário de portas de serviço para cada API:

     |Modo de conexão  |Protocolo com suporte  |SDKs com suporte  |Porta de API/serviço  |
     |---------|---------|---------|---------|
     |Gateway  |   HTTPS    |  Todos os SDKS    |   SQL (443), Mongo (10250, 10255, 10256), tabela (443), Cassandra (10350), grafo (443)    |
     |Direto    |    HTTPS     |  SDK do .NET e do Java    |   Portas dentro do intervalo de 10000 a 20.000    |
     |Direto    |     TCP    |  SDK .NET    | Portas dentro do intervalo de 10000 a 20.000 |

     O Azure Cosmos DB oferece um modelo de programação RESTful simples e aberto por HTTPS. Além disso, ele oferece um protocolo TCP eficiente, que também é RESTful em seu modelo de comunicação e está disponível por meio do SDK do cliente .NET. O TCP direto e o HTTPS usam SSL para autenticação inicial e criptografia de tráfego. Para obter o melhor desempenho, use o protocolo TCP quando possível.

     O modo de conectividade é configurado durante a construção da instância DocumentClient com o parâmetro ConnectionPolicy. Se o modo direto for usado, o protocolo também poderá ser definido dentro do parâmetro ConnectionPolicy.

     ```csharp
     var serviceEndpoint = new Uri("https://contoso.documents.net");
     var authKey = "your authKey from the Azure portal";
     DocumentClient client = new DocumentClient(serviceEndpoint, authKey,
     new ConnectionPolicy
     {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp
     });
     ```

     Como o TCP só tem suporte no modo direto, se o modo de gateway for usado, o protocolo HTTPS sempre será usado para se comunicar com o gateway e o valor de protocolo no ConnectionPolicy será ignorado.

     ![Ilustração da política de conexão de Azure Cosmos DB](./media/performance-tips/connection-policy.png)

2. **Chamar OpenAsync para evitar a latência de inicialização na primeira solicitação**

    Por padrão, a primeira solicitação tem uma latência maior porque ela precisa buscar a tabela de roteamento de endereços. Para evitar essa latência de inicialização na primeira solicitação, você deve chamar OpenAsync () uma vez durante a inicialização da seguinte maneira.

        await client.OpenAsync();
   <a id="same-region"></a>
3. **Colocar clientes na mesma região do Azure para desempenho**

    Quando possível, coloque todos os aplicativos que chamam Azure Cosmos DB na mesma região que o banco de dados Cosmos do Azure. Para uma comparação aproximada, as chamadas para Azure Cosmos DB na mesma região são concluídas dentro de 1-2 ms, mas a latência entre a costa oeste e a leste dos EUA é > 50 ms. Essa latência pode variar de acordo com a solicitação até a solicitação, dependendo da rota tomada pela solicitação à medida que ele passa do cliente para o limite de datacenter do Azure. A menor latência possível é obtida garantindo que o aplicativo de chamada esteja localizado na mesma região do Azure que o ponto de extremidade de Azure Cosmos DB provisionado. Para obter uma lista de regiões disponíveis, consulte [regiões do Azure](https://azure.microsoft.com/regions/#services).

    ![Ilustração da política de conexão de Azure Cosmos DB](./media/performance-tips/same-region.png)
   <a id="increase-threads"></a>
4. **Aumentar o número de threads/tarefas**

    Como chamadas para Azure Cosmos DB são feitas pela rede, talvez seja necessário variar o grau de paralelismo de suas solicitações para que o aplicativo cliente aguarde muito pouco tempo esperando entre as solicitações. Por exemplo, se você estiver usando o. [Biblioteca paralela de tarefas](https://msdn.microsoft.com//library/dd460717.aspx)da rede, crie na ordem de centenas de tarefas de leitura ou gravação em Azure Cosmos DB.

5. **Habilitar rede acelerada**

   Para reduzir a latência e a tremulação da CPU, recomendamos que as máquinas virtuais do cliente sejam habilitadas para rede acelerada. Consulte a artigos [criar uma máquina virtual do Windows com rede acelerada](../virtual-network/create-vm-accelerated-networking-powershell.md) ou [criar uma máquina virtual Linux com rede acelerada](../virtual-network/create-vm-accelerated-networking-cli.md) para habilitar a rede acelerada.


## <a name="sdk-usage"></a>Uso do SDK
1. **Instalar o SDK mais recente**

    Os SDKs de Azure Cosmos DB estão constantemente sendo aprimorados para fornecer o melhor desempenho. Consulte as páginas [Azure Cosmos DB SDK](sql-api-sdk-dotnet-standard.md) para determinar o SDK mais recente e revisar as melhorias.
2. **Usar um cliente Azure Cosmos DB singleton durante o tempo de vida do seu aplicativo**

    Cada instância de DocumentClient é thread-safe e executa o gerenciamento de conexão e o cache de endereços eficientes ao operar no modo direto. Para permitir um gerenciamento de conexão eficiente e melhor desempenho por DocumentClient, é recomendável usar uma única instância de DocumentClient por AppDomain durante o tempo de vida do aplicativo.

   <a id="max-connection"></a>
3. **Aumentar o System.Net MaxConnections por host ao usar o modo de gateway**

    Azure Cosmos DB solicitações são feitas por HTTPS/REST ao usar o modo de gateway e estão sujeitas ao limite de conexões padrão por nome de host ou endereço IP. Talvez seja necessário definir MaxConnections para um valor mais alto (100-1000) para que a biblioteca de cliente possa utilizar várias conexões simultâneas para Azure Cosmos DB. No SDK do .NET 1.8.0 e superior, o valor padrão para [ServicePointManager. DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx) é 50 e para alterar o valor, você pode definir os [Documents. Client. ConnectionPolicy. MaxConnectionLimit](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit.aspx) como um valor mais alto.   
4. **Ajustando consultas paralelas para coleções particionadas**

     O SDK do SQL .NET versão 1.9.0 e superior oferece suporte a consultas paralelas, que permitem consultar uma coleção particionada em paralelo. Para obter mais informações, consulte [exemplos de código](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs) relacionados ao trabalho com os SDKs. Consultas paralelas são projetadas para melhorar a latência de consulta e a taxa de transferência em relação à sua contraparte serial. As consultas paralelas fornecem dois parâmetros que os usuários podem ajustar para atender aos requisitos personalizados, (a) MaxDegreeOfParallelism: para controlar o número máximo de partições, então, pode ser consultado em paralelo e (b) MaxBufferedItemCount: para controlar o número de resultados previamente buscados.

    (a) o ***ajuste\:***  da consulta paralela MaxDegreeOfParallelism funciona consultando várias partições em paralelo. No entanto, os dados de uma coleta particionada individual são buscados em série em relação à consulta. Portanto, definir o MaxDegreeOfParallelism como o número de partições tem a chance máxima de obter a consulta de melhor desempenho, desde que todas as outras condições do sistema permaneçam as mesmas. Se você não souber o número de partições, poderá definir MaxDegreeOfParallelism como um número alto, e o sistema escolherá o mínimo (número de partições, entrada fornecida pelo usuário) como o MaxDegreeOfParallelism.

    É importante observar que as consultas paralelas produzem os melhores benefícios se os dados forem distribuídos uniformemente entre todas as partições em relação à consulta. Se a coleção particionada for particionada de forma que toda ou a maioria dos dados retornados por uma consulta esteja concentrada em algumas partições (uma partição, no pior caso), o desempenho da consulta seria afunilado por essas partições.

    (b) ***ajuste MaxBufferedItemCount\:***  a consulta paralela foi projetada para buscar antecipadamente resultados enquanto o lote atual de resultados está sendo processado pelo cliente. A busca prévia ajuda na melhoria da latência geral de uma consulta. MaxBufferedItemCount é o parâmetro para limitar o número de resultados previamente buscados. A definição de MaxBufferedItemCount como o número esperado de resultados retornados (ou um número mais alto) permite que a consulta receba o máximo benefício da busca prévia.

    A busca prévia funciona da mesma maneira, independentemente do MaxDegreeOfParallelism, e há um único buffer para os dados de todas as partições.  
5. **Ativar GC do lado do servidor**

    Reduzir a frequência da coleta de lixo pode ajudar em alguns casos. Em .NET, defina [gcServer](https://msdn.microsoft.com/library/ms229357.aspx) como true.
6. **Implementar retirada em intervalos de RetryAfter**

    Durante o teste de desempenho, você deve aumentar a carga até que uma pequena taxa de solicitações seja limitada. Se limitado, o aplicativo cliente deve retirar o limite para o intervalo de repetição especificado pelo servidor. Respeitar a retirada garante que você gaste uma quantidade mínima de tempo esperando entre repetições. O suporte à política de repetição está incluído na versão 1.8.0 e superior do SQL [.net](sql-api-sdk-dotnet.md) e [Java](sql-api-sdk-java.md), versão 1.9.0 e superior do [node. js](sql-api-sdk-node.md) e do [Python](sql-api-sdk-python.md)e todas as versões com suporte dos SDKs do [.NET Core](sql-api-sdk-dotnet-core.md) . Para obter mais informações, [RetryAfter](https://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx).
    
    Com a versão 1,19 e posterior do SDK do .NET, há um mecanismo para registrar informações de diagnóstico adicionais e solucionar problemas de latência, conforme mostrado no exemplo a seguir. Você pode registrar a cadeia de caracteres de diagnóstico para solicitações que têm uma latência de leitura maior. A cadeia de caracteres de diagnóstico capturada ajudará você a entender o número de vezes que você observou o 429s para uma determinada solicitação.
    ```csharp
    ResourceResponse<Document> readDocument = await this.readClient.ReadDocumentAsync(oldDocuments[i].SelfLink);
    readDocument.RequestDiagnosticsString 
    ```
    
7. **Escalar horizontalmente seu cliente-carga de trabalho**

    Se você estiver testando em níveis de taxa de transferência alta (> 50000 RU/s), o aplicativo cliente poderá se tornar o afunilamento devido à máquina que está capping a utilização da CPU ou da rede. Se você chegar a esse ponto, poderá continuar a enviar a conta Azure Cosmos DB mais detalhadamente expandindo os aplicativos cliente em vários servidores.
8. **Armazenar URIs de documento em cache para latência de leitura mais baixa**

    Armazenar em cache URIs de documento sempre que possível para o melhor desempenho de leitura. Você precisa definir a lógica para armazenar em cache o ResourceId ao criar o recurso. As pesquisas baseadas em ResourceId são mais rápidas do que as pesquisas baseadas em nome, portanto, armazenar esses valores em cache melhora o desempenho. 

   <a id="tune-page-size"></a>
1. **Ajustar o tamanho da página para consultas/ler feeds para melhorar o desempenho**

   Ao executar uma leitura em massa de documentos usando a funcionalidade de feed de leitura (por exemplo, ReadDocumentFeedAsync) ou ao emitir uma consulta SQL, os resultados são retornados de uma maneira segmentada se o conjunto de resultados for muito grande. Por padrão, os resultados são retornados em partes de 100 itens ou 1 MB, o limite que for atingido primeiro.

   Para reduzir o número de viagens de ida e volta da rede necessárias para recuperar todos os resultados aplicáveis, você pode aumentar o tamanho da página usando o cabeçalho de solicitação [x-MS-Max-item-Count](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) para até 1000. Nos casos em que você precisa exibir apenas alguns resultados, por exemplo, se a interface do usuário ou a API do aplicativo retornar apenas 10 resultados por vez, você também poderá diminuir o tamanho da página para 10 para reduzir a taxa de transferência consumida para leituras e consultas.

   > [!NOTE] 
   > A propriedade maxItemCount não deve ser usada apenas para fins de paginação. É o principal uso para melhorar o desempenho das consultas, reduzindo o número máximo de itens retornados em uma única página.  

   Você também pode definir o tamanho da página usando os SDKs de Azure Cosmos DB disponíveis. A propriedade [MaxItemCount](/dotnet/api/microsoft.azure.documents.client.feedoptions.maxitemcount?view=azure-dotnet) no feedoptions permite que você defina o número máximo de itens a serem retornados na operação enmuration. Quando `maxItemCount` é definido como-1, o SDK localiza automaticamente o valor ideal dependendo do tamanho do documento. Por exemplo:
    
   ```csharp
    IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });
   ```
    
   Quando uma consulta é executada, os dados resultantes são enviados em um pacote TCP. Se você especificar um valor muito baixo `maxItemCount`para, o número de corridas necessárias para enviar os dados no pacote TCP será alto, o que afetará o desempenho. Portanto, se você não tiver certeza de qual valor definir `maxItemCount` para propriedade, é melhor defini-lo como-1 e permitir que o SDK escolha o valor padrão. 

10. **Aumentar o número de threads/tarefas**

    Consulte [aumentar o número de threads/tarefas](#increase-threads) na seção rede.

11. **Usar o processamento de host de 64 bits**

    O SDK do SQL funciona em um processo de host de 32 bits quando você estiver usando o SQL .NET SDK versão 1.11.4 e superior. No entanto, se você estiver usando consultas entre partições, o processamento de host de 64 bits será recomendado para melhorar o desempenho. Os seguintes tipos de aplicativos têm um processo de host de 32 bits como o padrão; portanto, para alterá-lo para 64 bits, siga estas etapas com base no tipo de seu aplicativo:

    - Para aplicativos executáveis, isso pode ser feito desmarcando a opção **preferir 32 bits** na janela **Propriedades do projeto** , na guia **Compilar** .

    - Para projetos de teste baseados em VSTest, isso pode ser feito->selecionando testar**configurações**->**de teste arquitetura de processador padrão como x64**, na opção de menu **teste do Visual Studio** .

    - Para aplicativos Web ASP.net implantados localmente, isso pode ser feito verificando o **uso da versão de 64 bits do IIS Express para sites e projetos**, em **ferramentas**->**Opções**->**projetos e soluções** **Projetos da Web.** ->

    - Para aplicativos Web ASP.NET implantados no Azure, isso pode ser feito escolhendo a **plataforma como 64 bits** nas **configurações do aplicativo** na portal do Azure.

## <a name="indexing-policy"></a>Política de Indexação
 
1. **Excluir os caminhos não utilizados da indexação para assegurar escritas mais rápidas**

    A política de indexação do Cosmos DB também permite que você especifique quais caminhos de documento incluir ou excluir da indexação, aproveitando os caminhos de indexação (IndexingPolicy. IncludedPaths e IndexingPolicy. ExcludedPaths). O uso de caminhos de indexação pode oferecer melhor desempenho de gravação e menor armazenamento de índice para cenários nos quais os padrões de consulta são conhecidos com antecedência, pois os custos de indexação são correlacionados diretamente com o número de caminhos exclusivos indexados.  Por exemplo, o código a seguir mostra como excluir uma seção inteira dos documentos (uma subárvore) da indexação usando o curinga "*".

    ```csharp
    var collection = new DocumentCollection { Id = "excludedPathCollection" };
    collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
    collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);
    ```

    Para obter mais informações, consulte [Azure Cosmos DB políticas](index-policy.md)de indexação.

## <a name="throughput"></a>Débito
<a id="measure-rus"></a>

1. **Medir e ajustar para unidades de solicitação menores/segundo uso**

    O Azure Cosmos DB oferece um conjunto avançado de operações de banco de dados, incluindo consultas relacionais e hierárquicas com UDFs, procedimentos armazenados e gatilhos – todos operando nos documentos dentro de uma coleção de banco de dados. O custo associado a cada uma dessas operações varia de acordo com a CPU, a e/s e a memória necessária para concluir a operação. Em vez de pensar e gerenciar recursos de hardware, você pode pensar em uma RU (unidade de solicitação) como uma medida única para os recursos necessários para executar várias operações de banco de dados e atender a uma solicitação de aplicativo.

    A taxa de transferência é provisionada com base no número de [unidades de solicitação](request-units.md) definidas para cada contêiner. O consumo de unidade de solicitação é avaliado como uma taxa por segundo. Os aplicativos que excedem a taxa de unidade de solicitação provisionada para seu contêiner são limitados até que a taxa fique abaixo do nível provisionado para o contêiner. Se seu aplicativo exigir um nível mais alto de taxa de transferência, você poderá aumentar sua taxa de transferência Provisionando unidades de solicitação adicionais. 

    A complexidade de uma consulta afeta quantas unidades de solicitação são consumidas para uma operação. O número de predicados, a natureza dos predicados, o número de UDFs e o tamanho do conjunto de dados de origem influenciam o custo das operações de consulta.

    Para medir a sobrecarga de qualquer operação (criar, atualizar ou excluir), inspecione o cabeçalho [x-MS-Request-encharge](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) (ou a propriedade RequestCharge equivalente em ResourceResponse\<T > ou FeedResponse\<t > no SDK do .net) para Meça o número de unidades de solicitação consumidas por essas operações.

    ```csharp
    // Measure the performance (request units) of writes
    ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);
    Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);
    // Measure the performance (request units) of queries
    IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();
    while (queryable.HasMoreResults)
         {
              FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>();
              Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge);
         }
    ```             

    O encargo de solicitação retornado neste cabeçalho é uma fração de sua taxa de transferência provisionada (ou seja, 2000 RUs/segundo). Por exemplo, se a consulta anterior retornar 1000 1 KB-Documents, o custo da operação será 1000. Como tal, dentro de um segundo, o servidor honra apenas duas solicitações desse tipo antes de limitar a taxa de solicitações subsequentes. Para obter mais informações, consulte [unidades de solicitação](request-units.md) e a calculadora de unidade de [solicitação](https://www.documentdb.com/capacityplanner).
<a id="429"></a>
2. **Processar limitação de taxa/taxa de solicitação muito grande**

    Quando um cliente tenta exceder a taxa de transferência reservada para uma conta, não há degradação de desempenho no servidor e nenhum uso da capacidade de taxa de transferência além do nível reservado. O servidor encerrará de forma preventiva a solicitação com RequestRateTooLarge (código de status HTTP 429) e retornará o cabeçalho [x-MS-Retry-After-MS](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) , indicando o tempo, em milissegundos, que o usuário deve aguardar antes de tentar novamente a solicitação.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Todos os SDKs capturam implicitamente essa resposta, respeitam o cabeçalho Retry-After especificado pelo servidor e tentam novamente a solicitação. A menos que sua conta esteja sendo acessada simultaneamente por vários clientes, a próxima tentativa terá sucesso.

    Se você tiver mais de um cliente operando de forma cumulativa consistentemente acima da taxa de solicitação, a contagem de repetição padrão definida atualmente como 9 internamente pelo cliente pode não ser suficiente; Nesse caso, o cliente gera um DocumentClientException com o código de status 429 para o aplicativo. A contagem de repetição padrão pode ser alterada configurando as Repetiçãooptions na instância ConnectionPolicy. Por padrão, o DocumentClientException com o código de status 429 é retornado após um tempo de espera cumulativo de 30 segundos se a solicitação continuar a operar acima da taxa de solicitação. Isso ocorre mesmo quando a contagem de repetições atual é menor que a contagem máxima de tentativas, seja o padrão de 9 ou um valor definido pelo usuário.

    Embora o comportamento de repetição automatizado ajude a melhorar a resiliência e a usabilidade para a maioria dos aplicativos, isso pode ocorrer ao fazer benchmarks de desempenho, especialmente ao medir a latência.  A latência observada pelo cliente será propico se o experimento atingir a limitação do servidor e fizer com que o SDK do cliente tente novamente de forma silenciosa. Para evitar picos de latência durante os experimentos de desempenho, meça o encargo retornado por cada operação e verifique se as solicitações estão operando abaixo da taxa de solicitação reservada. Para obter mais informações, consulte [unidades de solicitação](request-units.md).
3. **Design de documentos menores para maior taxa de transferência**

    O encargo da solicitação (ou seja, o custo de processamento da solicitação) de uma determinada operação está correlacionado diretamente ao tamanho do documento. As operações em documentos grandes custam mais do que as operações para documentos pequenos.

## <a name="next-steps"></a>Passos Seguintes
Para um aplicativo de exemplo usado para avaliar Azure Cosmos DB para cenários de alto desempenho em alguns computadores cliente, consulte [teste de desempenho e escala com Azure Cosmos DB](performance-testing.md).

Além disso, para saber mais sobre como projetar seu aplicativo para escala e alto desempenho, confira [particionamento e dimensionamento em Azure Cosmos DB](partition-data.md).
