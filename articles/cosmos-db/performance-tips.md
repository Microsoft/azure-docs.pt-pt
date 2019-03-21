---
title: Sugestões de desempenho do Cosmos DB do Azure para .NET
description: Saiba mais opções de configuração de cliente para melhorar o desempenho de base de dados do Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/24/2018
ms.author: sngun
ms.openlocfilehash: cf90f7231362d147914e22419c9008d2628a483f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57861898"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net"></a>Sugestões de desempenho para o Azure Cosmos DB e .NET

> [!div class="op_single_selector"]
> * [Async Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

O Azure Cosmos DB é uma rápida e flexível base de dados distribuída que se dimensiona de forma totalmente integrada com o débito e latência garantida. Não é necessário fazer alterações de arquitetura principais ou escrever código complexo para dimensionar a sua base de dados com o Azure Cosmos DB. Aumentar e reduzir verticalmente é tão fácil quanto efetuar uma única chamada à API. Para obter mais informações, consulte [como aprovisionar o débito de contentores](how-to-provision-container-throughput.md) ou [como aprovisionar o débito de base de dados](how-to-provision-database-throughput.md). No entanto, porque o Azure Cosmos DB é acessado por meio de chamadas de rede há otimizações de lado do cliente, pode fazer para alcançar o desempenho de pico quando utilizar o [SDK de .NET de SQL](documentdb-sdk-dotnet.md).

Portanto, se está perguntando "como posso melhorar o desempenho da minha base de dados?" Considere as seguintes opções:

## <a name="networking"></a>Redes
<a id="direct-connection"></a>

1. **Política de ligação: Utilizar o modo de ligação direta**

    Como um cliente liga-se ao Azure Cosmos DB tem implicações importantes sobre o desempenho, especialmente em termos de latência de lado do cliente observado. Existem duas definições de configuração da chave disponíveis para configurar a política de ligação – a ligação de cliente *modo* e a ligação *protocolo*.  Os dois modos disponíveis são:

   * Modo de gateway (predefinição)
      
     Modo de gateway é suportado em todas as plataformas SDK e é o padrão configurado. Se seu aplicativo é executado dentro de uma rede corporativa com restrições de strict firewall, o modo de Gateway é a melhor opção, porque utiliza a porta HTTPS padrão e um único ponto final. A compensação de desempenho, no entanto, é que o Gateway de modo envolve um salto de rede adicionais sempre que os dados são lidos ou escritos para o Azure Cosmos DB. Por este motivo, o modo direto oferece um melhor desempenho devido ao menos saltos de rede. Modo de ligação de gateway também é recomendado quando executa aplicativos em ambientes com um número limitado de conexões de soquete, por exemplo, ao utilizar as funções do Azure ou estiver num plano de consumo. 

   * Modo direto

     Modo direto suporta a conectividade através de protocolos TCP e HTTPS. Se estiver a utilizar a versão mais recente do SDK de .NET, o modo de conectividade direta é suportado no .NET Standard 2.0 e o .NET framework. Ao utilizar o modo direto, existem duas opções de protocolo disponíveis:

     * TCP
     * HTTPS

     Ao utilizar o modo de Gateway, Cosmos DB utiliza a porta 443 e as portas 10250, 10255 e 10256 ao utilizar a API do Azure Cosmos DB para o MongoDB. O 10250 porta é mapeado para uma instância de MongoDB predefinida sem georreplicação e mapa de 10255/10256 portas para a instância de MongoDB com a funcionalidade de georreplicação. Quando o TCP no modo direto, além das portas de Gateway, tem de garantir que a porta de intervalo entre 10000 e 20000 está aberto porque o Azure Cosmos DB utiliza as portas TCP dinâmicas. Se estas portas não estão abertas e tentar utilizar TCP, receberá um erro 503 Serviço indisponível. A tabela seguinte mostra os modos de conectividade disponíveis para as APIs diferentes e o utilizador de portas de serviço para cada API:

     |Modo de ligação  |Protocolo suportado  |SDKs suportados  |Porta de serviço/API  |
     |---------|---------|---------|---------|
     |Gateway  |   HTTPS    |  Todos os SDKS    |   SQL(443), Mongo(10250, 10255, 10256), Table(443), Cassandra(10350), Graph(443)    |
     |Direto    |    HTTPS     |  SDK de .NET e Java    |   Portas dentro do intervalo de 10 000 20.000    |
     |Direto    |     TCP    |  SDK .NET    | Portas dentro do intervalo de 10 000 20.000 |

     O Azure Cosmos DB oferece um modelo de programação RESTful simples e aberto através de HTTPS. Além disso, ele oferece um protocolo TCP eficiente, que também é RESTful no seu modelo de comunicação e está disponível através do SDK de cliente .NET. TCP direto e HTTPS utilizem SSL para tráfego de criptografia e autenticação inicial. Para obter melhor desempenho, utilize o protocolo TCP, sempre que possível.

     O modo de conectividade é configurado durante a construção da instância do DocumentClient com o parâmetro ConnectionPolicy. Se for utilizado o modo direto, o protocolo também pode ser definido dentro o parâmetro ConnectionPolicy.

     ```csharp
     var serviceEndpoint = new Uri("https://contoso.documents.net");
     var authKey = new "your authKey from the Azure portal";
     DocumentClient client = new DocumentClient(serviceEndpoint, authKey,
     new ConnectionPolicy
     {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp
     });
     ```

     Porque TCP só é suportado no modo direto, se o modo de Gateway é usado, em seguida, o protocolo HTTPS é sempre utilizado para comunicar com o Gateway e o valor de protocolo no ConnectionPolicy é ignorado.

     ![Ilustração da política de ligação do Azure Cosmos DB](./media/performance-tips/connection-policy.png)

2. **Chamar OpenAsync para evitar a latência de inicialização no primeiro pedido**

    Por predefinição, a primeira solicitação tem uma latência superior porque tem de obter a tabela de encaminhamento de endereço. Para evitar esta latência de inicialização na primeira solicitação, deve chamar openasync () uma vez durante a inicialização da seguinte forma.

        await client.OpenAsync();
   <a id="same-region"></a>
3. **Colocar os clientes na mesma região do Azure para desempenho**

    Sempre que possível, coloque todas as aplicações do Azure Cosmos DB ao chamar na mesma região que a base de dados do Azure Cosmos DB. Para obter uma comparação aproximada, chamadas para o Azure Cosmos DB na mesma região são concluídas em 1-2 ms, mas a latência entre a Costa Leste dos EUA e oeste é > 50 ms. Esta latência provavelmente pode variar para cada solicitação, dependendo da rota executada pelo pedido, à medida que passa do cliente com o limite de datacenter do Azure. A menor latência possível é obtida ao garantir que o aplicativo de chamada está localizado na mesma região do Azure como o ponto de final aprovisionado do Azure Cosmos DB. Para obter uma lista de regiões disponíveis, consulte [regiões do Azure](https://azure.microsoft.com/regions/#services).

    ![Ilustração da política de ligação do Azure Cosmos DB](./media/performance-tips/same-region.png)
   <a id="increase-threads"></a>
4. **Aumentar o número de threads/tarefas**

    Uma vez que as chamadas para o Azure Cosmos DB são feitas através da rede, poderá variar o grau de paralelismo dos seus pedidos, para que a aplicação cliente passa muito pouco tempo a aguardar entre as solicitações. Por exemplo, se estiver a utilizar. Do NET [Task Parallel Library](https://msdn.microsoft.com//library/dd460717.aspx), criar na ordem 100s das tarefas ler ou escrever para o Azure Cosmos DB.

## <a name="sdk-usage"></a>Utilização do SDK
1. **Instalar o SDK mais recente**

    Os SDKs do Azure Cosmos DB estão constantemente a ser melhorados para proporcionar o melhor desempenho. Consulte a [SDK do Azure Cosmos DB](documentdb-sdk-dotnet.md) páginas para determinar o SDK mais recente e rever melhorias.
2. **Utilizar o cliente do Azure Cosmos DB singleton durante o ciclo de vida da sua aplicação**

    Cada instância do DocumentClient é thread-safe e efetua a gestão eficiente da conexão e o endereço de colocação em cache quando a funcionar no modo direto. Para permitir a gestão eficiente da conexão e um melhor desempenho por DocumentClient, é recomendado utilizar uma única instância do DocumentClient por AppDomain durante o ciclo de vida do aplicativo.

   <a id="max-connection"></a>
3. **Aumentar MaxConnections do System.Net por anfitrião, ao utilizar o modo de Gateway**

    O Azure Cosmos DB solicitações são feitas através do HTTPS/REST ao utilizar o modo de Gateway e estão sujeitos ao limite de ligação predefinido por nome de anfitrião ou endereço IP. Poderá ter de definir o MaxConnections para um valor mais alto (100-1000) para que a biblioteca de cliente pode utilizar várias ligações simultâneas ao Azure Cosmos DB. No .NET SDK 1.8.0 e superior, o valor predefinido para [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx) é 50 e para alterar o valor, pode definir o [Documents.Client.ConnectionPolicy.MaxConnectionLimit](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit.aspx)para um valor mais alto.   
4. **Ajuste de consultas paralelas para coleções particionadas**

     O SDK de .NET de SQL versão 1.9.0 e acima consultas paralelas suporte, permitindo-lhe consultar uma coleção com partições em paralelo. Para obter mais informações, consulte [exemplos de código](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs) relacionadas ao trabalho com os SDKs. Consultas paralelas foram concebidas para melhorar o débito e latência de consulta em sua contraparte serial. Consultas paralelas fornecem dois parâmetros que utilizadores podem sintonizar de acordo com os respetivos requisitos, MaxDegreeOfParallelism (a): para controlar o número máximo de partições, em seguida, pode ser consultado em paralelo e (b) MaxBufferedItemCount: para controlar o número de pré-foram obtidos resultados.

    (a) ***otimização MaxDegreeOfParallelism\:***  funciona de consulta em paralelo ao consultar várias partições em paralelo. No entanto, os dados a partir de uma recolha particionada individual são obtidos em série em relação a consulta. Então, definir o MaxDegreeOfParallelism para o número de partições tem a chance de máxima de conseguir a maioria dos consulta de elevado desempenho, desde que todas as outras condições do sistema permanecem os mesmos. Se não souber o número de partições, pode definir o MaxDegreeOfParallelism para um número alto e o sistema escolhe o mínimo (número de partições, entrada do usuário fornecida) como o MaxDegreeOfParallelism.

    É importante observar que consultas paralelas produzem os benefícios de melhor se os dados são distribuídos uniformemente por todas as partições em relação a consulta. Se a coleção particionada está particionada de forma que todos os ou a maioria dos dados retornados por uma consulta é concentrada em algumas partições (uma partição na pior das hipóteses), em seguida, o desempenho da consulta poderia ser um afunilamento nessas partições.

    (b) ***otimização MaxBufferedItemCount\:***  paralela da consulta destina-se na pré-busca resultados enquanto o lote atual dos resultados está a ser processado pelo cliente. A obtenção prévia ajuda a melhoria geral de latência de uma consulta. MaxBufferedItemCount é o parâmetro para limitar o número de resultados previamente foram obtidos. Definir MaxBufferedItemCount para o número esperado de resultados retornados (ou um número mais alto) permite que a consulta receber o máximo benefício de obtenção prévia.

    Obtenção prévia funciona da mesma forma, independentemente do MaxDegreeOfParallelism e não existe uma única memória intermédia para os dados de todas as partições.  
5. **Ativar o GC do lado do servidor**

    Reduzir a frequência da coleta de lixo pode ajudar em alguns casos. No .NET, defina [gcServer](https://msdn.microsoft.com/library/ms229357.aspx) como true.
6. **Implementar o término em intervalos de RetryAfter**

    Durante os testes de desempenho, deve aumentar a carga até que uma pequena taxa de pedidos são limitados. Se otimizado, o aplicativo cliente deve término na limitação para o intervalo entre tentativas de servidor especificado. Respeitar o término garante que passe uma quantidade mínima de espera de tempo entre as repetições. O suporte de política de repetição está incluído na versão 1.8.0 e superior do SQL [.NET](sql-api-sdk-dotnet.md) e [Java](sql-api-sdk-java.md), versão 1.9.0 e acima da [node. js](sql-api-sdk-node.md) e [Python](sql-api-sdk-python.md), e todas as versões suportadas do [.NET Core](sql-api-sdk-dotnet-core.md) SDKs. Para obter mais informações, [RetryAfter](https://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx).
    
    Com a versão 1.19 e versões posterior do SDK do .NET, existe um mecanismo para registar informações de diagnóstico adicionais e resolução de problemas de latência, como mostrado no exemplo a seguir. Pode iniciar a cadeia de diagnóstico para pedidos que têm uma latência de leitura superior. A cadeia de caracteres de diagnóstico capturada lhe ajudarão a compreender o número de vezes que observa 429s para uma determinada solicitação.
    ```csharp
    ResourceResponse<Document> readDocument = await this.readClient.ReadDocumentAsync(oldDocuments[i].SelfLink);
    readDocument.RequestDiagnosticsString 
    ```
    
7. **Aumentar horizontalmente o seu cliente e carga de trabalho**

    Se estiver a testar em níveis de débito elevado (> 50 000 RU/s), a aplicação cliente pode se tornar o afunilamento devido a máquina capping horizontalmente na utilização da CPU ou de rede. Se atingir este ponto, pode continuar a enviar por push ainda mais a conta do Azure Cosmos DB, aumente horizontalmente as suas aplicações de cliente em vários servidores.
8. **Documento de cache URIs para menor latência de leitura**

    Documento de cache URIs sempre que possível para o melhor desempenho de leitura. Tem de definir a lógica para colocar em cache o resourceid ao criar o recurso. ResourceId com base em pesquisas são mais rápidas do que as pesquisas de nome com base, para que estes valores de colocação em cache melhora o desempenho. 

   <a id="tune-page-size"></a>
1. **Otimizar o tamanho da página para feeds de consultas/leitura para um melhor desempenho**

    Quando efetuar uma massa lidos de documentos com a leitura do feed funcionalidade (por exemplo, ReadDocumentFeedAsync) ou ao emitir uma consulta SQL, os resultados são retornados de uma maneira segmentada se o conjunto de resultados é demasiado grande. Por predefinição, os resultados são devolvidos em blocos de 100 itens ou de 1 MB, consoante o limite for atingida primeiro.

    Para reduzir o número de percursos de ida necessária para obter resultados de todos os aplicáveis e rede, pode aumentar o tamanho de página usando [x-ms-max-item-count](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) cabeçalho do pedido para até 1000. Em casos em que precisa exibir apenas alguns resultados, por exemplo, se a API de interface ou a aplicação do utilizador devolve apenas 10 resulta de uma hora, também pode diminuir o tamanho da página para 10 para reduzir o débito consumido para leituras e consultas.

    Também pode definir o tamanho da página usando disponíveis SDKs do Azure Cosmos DB.  Por exemplo:

        IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });
10. **Aumentar o número de threads/tarefas**

    Ver [aumentar o número de threads/tarefas](#increase-threads) na seção de sistema de rede.

11. **Utilizar o processamento do host de 64 bits**

    O SDK de SQL funciona num processo de host de 32 bits quando estiver a utilizar o SDK de .NET de SQL versão 1.11.4 e superior. No entanto, se estiver a utilizar consultas de partição cruzada, o processamento do host de 64 bits é recomendado para um melhor desempenho. Os seguintes tipos de aplicações têm o processo de host de 32 bits como padrão, portanto, para alterar isso para 64 bits, siga estes passos com base no tipo de seu aplicativo:

    - Para aplicativos executáveis, isso pode ser feito ao desmarcar a opção a **preferir 32 bits** opção a **propriedades do projeto** janela, no **criar** separador.

    - Para VSTest com a base de projetos de teste, isso pode ser feito selecionando **testar**->**definições de teste**->**predefinido a arquitetura de processador como X64**, partir do **Visual Studio Test** opção de menu.

    - Para aplicativos ASP.NET Web implementados localmente, isso pode ser feito marcando a **utiliza a versão de 64 bits do IIS Express para web sites e projetos**, em **ferramentas**->**opções**  -> **Projetos e soluções**->**projetos da Web**.

    - Para aplicativos da Web de ASP.NET implementados no Azure, isto pode ser feito ao escolher o **plataforma de 64 bits** no **configurações de aplicativo** no portal do Azure.

## <a name="indexing-policy"></a>Política de Indexação
 
1. **Excluir caminhos não utilizados de indexação para escritas mais rápidas**

    Política de indexação do cosmos DB permite-lhe especificar os caminhos de documento para incluir ou excluir da indexação ao tirar partido da indexação caminhos (IndexingPolicy.IncludedPaths e IndexingPolicy.ExcludedPaths). O uso de caminhos de indexação pode oferecer desempenho aprimorado de escrita e de armazenamento de índice mais baixo para cenários em que os padrões de consulta são previamente conhecidos, como os custos de indexação são correlacionados diretamente para o número de caminhos exclusivos indexados.  Por exemplo, o código seguinte mostra como excluir uma seção inteira de documentos (também conhecido como uma subárvore) da utilização de indexação a "*" caráter universal.

    ```csharp
    var collection = new DocumentCollection { Id = "excludedPathCollection" };
    collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
    collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);
    ```

    Para obter mais informações, consulte [políticas de indexação do Azure Cosmos DB](index-policy.md).

## <a name="throughput"></a>Débito
<a id="measure-rus"></a>

1. **Medir e otimizar para pedido inferior unidades/segundo utilização**

    O Azure Cosmos DB oferece um conjunto avançado de operações de banco de dados, incluindo consultas relacionais e hierárquicas com UDFs, procedimentos armazenados e acionadores-tudo isto em documentos dentro de uma coleção de base de dados. O custo associado a cada uma destas operações varia consoante a CPU, IO e memória necessários para concluir a operação. Em vez de pensar e gerir recursos de hardware, pode pensar numa unidade de pedido (RU) como medida única para os recursos necessários para executar várias operações de base de dados e servir um pedido de aplicação.

    Débito aprovisionado com base no número de [unidades de pedido](request-units.md) definido para cada contentor. Consumo de unidades de pedido é avaliado como uma taxa por segundo. Aplicações que excedem a taxa de unidade de pedido aprovisionadas para o contentor estão limitadas até que a velocidade cai abaixo do nível de aprovisionamento do contentor. Se seu aplicativo exigir um nível de débito mais elevado, pode aumentar o débito por unidades de pedido de aprovisionamento. 

    A complexidade de uma consulta tem impacto sobre o número de unidades de pedido são consumidas para uma operação. O número de predicados, a natureza dos predicados, UDFs e o tamanho do conjunto de dados de origem todos os influenciar o custo das operações de consulta.

    Para medir a sobrecarga de qualquer operação (criar, atualizar ou eliminar), Inspecione o [x-ms--de encargos de pedidos](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) cabeçalho (ou a propriedade RequestCharge equivalente no ResourceResponse<T> ou FeedResponse<T> na. SDK NET) para medir o número de unidades de pedido consumidas por essas operações.

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

    O custo de pedido devolvido neste cabeçalho é uma fração do débito aprovisionado (ou seja, de 2000 RU / segundo). Por exemplo, se a consulta anterior devolve 1000 1KB-documentos, o custo da operação é 1000. Como tal, dentro de um segundo, o servidor honra apenas dois esses pedidos antes dos pedidos subsequentes de limitação de taxas. Para obter mais informações, consulte [unidades de pedido](request-units.md) e o [Calculadora de unidade de pedido](https://www.documentdb.com/capacityplanner).
<a id="429"></a>
2. **Taxa de pedidos/limitação de taxa de identificador demasiado grande**

    Quando um cliente tenta exceder o débito reservado para uma conta, não existe sem degradação do desempenho no servidor e a não utilização de capacidade de débito além do nível reservado. O servidor preventivamente irá terminar o pedido com RequestRateTooLarge (código de estado HTTP 429) e retornar o [x-ms-repetição-após-ms](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) cabeçalho que indica a quantidade de tempo, em milissegundos, que o utilizador tem de aguardar até reattempting o pedido.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Os SDKs tudo implicitamente capturar essa resposta, respeitem o servidor especificado cabeçalho retry-after e novamente o pedido. A menos que a sua conta está sendo acessada em simultâneo por vários clientes, a próxima repetição será concluída com êxito.

    Se tiver mais do que um cliente cumulativamente e a funcionar consistentemente acima a taxa de pedidos, a contagem de repetições de predefinição atualmente definida para 9 internamente pelo cliente não ser suficientes; Neste caso, o cliente gera um DocumentClientException com código de estado 429 à aplicação. A contagem de repetições padrão pode ser alterada ao definir o RetryOptions na instância ConnectionPolicy. Por predefinição, o DocumentClientException com código de estado 429 é devolvido após um tempo cumulativo de espera de 30 segundos se o pedido continuar a operar acima a taxa de pedidos. Isto ocorre mesmo quando o número atual de tentativas é menor que o número máximo de tentativas, seja ele o padrão de 9 ou de um valor definido pelo utilizador.

    Embora o comportamento de repetição automatizada ajuda a melhorar a resiliência e facilidade de utilização para a maioria dos aplicativos, podem surgir em desacordo durante a realização de testes de desempenho, especialmente ao medir a latência.  A latência observado o cliente será expandam se a experimentação atinge a limitação de servidor e faz com que o cliente SDK para repetir automaticamente. Para evitar picos de latência durante experimentos de desempenho, a cobrança devolvida por cada operação de medir e certifique-se de que pedidos estão a funcionar abaixo a taxa de pedido reservadas. Para obter mais informações, consulte [unidades de pedido](request-units.md).
3. **Design para documentos mais pequenos para um débito mais elevado**

    O custo de pedido (ou seja, o custo de processamento da solicitação) de uma determinada operação diretamente é correlacionado com o tamanho do documento. Operações em documentos grandes custam mais do que operações para pequenos documentos.

## <a name="next-steps"></a>Passos Seguintes
Para uma aplicação de exemplo utilizada para avaliar o Azure Cosmos DB para cenários de alto desempenho em algumas máquinas de cliente, consulte [desempenho e dimensionamento testes com o Azure Cosmos DB](performance-testing.md).

Além disso, para saber mais sobre a criação do seu aplicativo para dimensionamento e desempenho elevado, veja [criação de partições e dimensionamento no Azure Cosmos DB](partition-data.md).
