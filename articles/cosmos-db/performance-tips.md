---
title: Dicas de desempenho do Azure Cosmos DB para .NET
description: Aprenda opções de configuração do cliente para melhorar o desempenho da base de dados azure Cosmos
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: sngun
ms.openlocfilehash: dc9d10a6539c7fc3a7c5c8b3db290cc951c24883
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78357580"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net"></a>Dicas de desempenho para Azure Cosmos DB e .NET

> [!div class="op_single_selector"]
> * [Async Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

Azure Cosmos DB é uma base de dados distribuída rápida e flexível que escala perfeitamente com latência garantida e produção. Não é preciso fazer grandes alterações de arquitetura ou escrever código complexo para escalar a sua base de dados com o Azure Cosmos DB. Escalar para cima e para baixo é tão fácil como fazer uma única chamada aPi. Para saber mais, veja como fornecer a entrada de [recipientes](how-to-provision-container-throughput.md) ou [como fornecer a entrada](how-to-provision-database-throughput.md)de base de dados . No entanto, como o Azure Cosmos DB é acedido através de chamadas de rede, existem otimizações do lado do cliente que você pode fazer para alcançar o desempenho máximo ao usar o [SQL .NET SDK](sql-api-sdk-dotnet-standard.md).

Então, se estás a perguntar"Como posso melhorar o meu desempenho na base de dados?" considere as seguintes opções:

## <a name="hosting-recommendations"></a>Recomendações de hospedagem

1.  **Para cargas de trabalho intensivas de consultas, utilize o Windows 64-bit em vez do processamento do anfitrião Do Miné-4 ou do Windows 32**

    Recomenda-se o processamento do anfitrião do Windows 64 bits para um melhor desempenho. O SQL SDK inclui um ServiceInterop.dll nativo para analisar e otimizar consultas localmente, e só é suportado na plataforma Windows x64. Para o Linux e outras plataformas não suportadas onde o ServiceInterop.dll não está disponível, fará uma chamada adicional de rede para a porta de entrada para obter a consulta otimizada. Os seguintes tipos de aplicações têm o processo de hospedeiro de 32 bits como padrão, por isso, para alterar isso para 64 bits, siga estes passos com base no tipo da sua aplicação:

    - Para aplicações executáveis, isto pode ser feito definindo o alvo da [Plataforma](https://docs.microsoft.com/visualstudio/ide/how-to-configure-projects-to-target-platforms?view=vs-2019) para **x64** na janela Propriedades do **Projeto,** no separador **Build.**

    - Para projetos de teste baseados em VSTest, isto pode ser feito selecionando **definições** de **teste**->teste->**Arquitetura padrão do processador como X64**, a partir da opção de menu de teste de **estúdio visual.**

    - Para aplicações web ASP.NET implementadas localmente, isto pode ser feito verificando a versão de **64 bits do IIS Express para web sites e projetos,** no âmbito **de Tools**->**Options**->Projects **and Solutions**->**Web Projects.**

    - Para ASP.NET aplicações Web implementadas no Azure, isso pode ser feito escolhendo a **Plataforma como 64 bits** nas Definições de **Aplicação** no portal Azure.

    > [!NOTE] 
    > O estúdio visual falha novos projetos para qualquer CPU. Recomenda-se definir o projeto para x64 para evitar que mude para x86. Qualquer projeto CPU pode facilmente mudar para x86 se for adicionada qualquer dependência que seja apenas x86.<br/>
    > O ServiceInterop.dll precisa de estar na mesma pasta do dll SDK. Isto só deve ser feito para que os utilizadores lidem manualmente com dlls ou tenham sistemas personalizados de construção/implementação.
    
2. **Ligue a Coleção de Lixo do lado do servidor (GC)**

    A redução da frequência da recolha de lixo pode ajudar em alguns casos. Em .NET, coloque o [gcServer](https://msdn.microsoft.com/library/ms229357.aspx) como verdadeiro.

3. **Esforce a sua carga de trabalho do cliente**

    Se estiver a testar em níveis elevados de pontuação (>50.000 RU/s), a aplicação do cliente pode tornar-se o estrangulamento devido à máquina que se limita à utilização de CPU ou Rede. Se chegar a este ponto, pode continuar a empurrar ainda mais a conta Azure Cosmos DB, dimensionando as suas aplicações de clientes em vários servidores.

    > [!NOTE] 
    > O elevado uso de CPU pode causar um aumento da latência e solicitar exceções no tempo de paragem.

## <a name="networking"></a>Redes
<a id="direct-connection"></a>

1. **Política de ligação: Utilize o modo de ligação direta**

    A forma como um cliente se liga ao Azure Cosmos DB tem implicações importantes no desempenho, sobretudo em termos da latência observada do lado do cliente. Existem duas definições de configuração de chaves disponíveis para configurar a Política de Ligação ao Cliente – o *modo* de ligação e o *protocolo*de ligação .  Os dois modos disponíveis são:

   * Modo Gateway
      
     O modo Gateway é suportado em todas as plataformas SDK e é o padrão configurado para [Microsoft.Azure.DocumentDB SDK](sql-api-sdk-dotnet.md). Se a sua aplicação for executado dentro de uma rede corporativa com restrições rigorosas de firewall, o modo gateway é a melhor escolha uma vez que utiliza a porta HTTPS padrão e um único ponto final. A troca de desempenho, no entanto, é que o modo gateway envolve um salto de rede adicional cada vez que os dados são lidos ou escritos para o Azure Cosmos DB. Por isso, o Modo Direct oferece um melhor desempenho devido a menos lúpulo de rede. O modo de ligação gateway também é recomendado quando executa aplicações em ambientes com um número limitado de ligações à tomada. 

     Ao utilizar o SDK em Funções Azure, particularmente no plano de [consumo,](../azure-functions/functions-scale.md#consumption-plan)esteja atento aos limites atuais [nas ligações](../azure-functions/manage-connections.md). Nesse caso, o modo gateway pode ser recomendado se também estiver a trabalhar com outros clientes baseados em HTTP dentro da sua aplicação Funções Azure.

   * Modo direto

     O modo direct suporta a conectividade através do protocolo TCP e é o modo de conectividade padrão se estiver a utilizar [o Microsoft.Azure.Cosmos/.Net V3 SDK](sql-api-sdk-dotnet-standard.md).

     Ao utilizar o modo gateway, cosmos DB utiliza a porta 443 e as portas 10250, 10255 e 10256 quando utiliza a API do Azure Cosmos DB para MongoDB. A porta de 10250 mapeia para uma instância padrão de MongoDB sem geo-replicação e mapa de portas 10255/10256 para a instância MongoDB com funcionalidade de geo-replicação. Ao utilizar o TCP em Modo Direct, para além das portas Gateway, é necessário garantir que a gama de portas entre 10000 e 20000 está aberta porque o Azure Cosmos DB utiliza portas TCP dinâmicas. Se estas portas não estiverem abertas e tentar utilizar o TCP, recebe um erro indisponível de serviço 503. A tabela que se segue mostra os modos de conectividade disponíveis para diferentes APIs e o utilizador das portas de serviço para cada API:

     |Modo de ligação  |Protocolo apoiado  |SDKs suportados  |Porto API/Serviço  |
     |---------|---------|---------|---------|
     |Gateway  |   HTTPS    |  Todos os SDKS    |   SQL(443), Mongo (10250, 10255, 10256), Quadro (443), Cassandra (10350), Gráfico (443)    |
     |Direct    |     TCP    |  SDK .NET    | Portos dentro de 10.000-20.000 |

     A Azure Cosmos DB oferece um modelo de programação RESTful simples e aberto em HTTPS. Além disso, oferece um protocolo TCP eficiente, que também é RESTful no seu modelo de comunicação e está disponível através do SDK cliente .NET. O protocolo TCP utiliza o SSL para autenticação inicial e encriptação do tráfego. Para um melhor desempenho, utilize o protocolo TCP sempre que possível.

     Para o SDK V3, o modo de conectividade está configurado ao mesmo tempo que cria a instância CosmosClient, como parte das Opções CosmosClientOptions, lembre-se que o modo Direct é o padrão.

     ```csharp
     var serviceEndpoint = new Uri("https://contoso.documents.net");
     var authKey = "your authKey from the Azure portal";
     CosmosClient client = new CosmosClient(serviceEndpoint, authKey,
     new CosmosClientOptions
     {
        ConnectionMode = ConnectionMode.Gateway // ConnectionMode.Direct is the default
     });
     ```

     Para o Microsoft.Azure.DocumentDB SDK, o modo de conectividade é configurado durante a construção da instância DocumentClient com o parâmetro ConnectionPolicy. Se o Modo Direct for utilizado, o Protocolo também pode ser definido dentro do parâmetro ConnectionPolicy.

     ```csharp
     var serviceEndpoint = new Uri("https://contoso.documents.net");
     var authKey = "your authKey from the Azure portal";
     DocumentClient client = new DocumentClient(serviceEndpoint, authKey,
     new ConnectionPolicy
     {
        ConnectionMode = ConnectionMode.Direct, //ConnectionMode.Gateway is the default
        ConnectionProtocol = Protocol.Tcp
     });
     ```

     Uma vez que o TCP só é suportado no Modo Direct, se o modo gateway for utilizado, o protocolo HTTPS é sempre utilizado para comunicar com o Gateway e o valor do Protocolo na Política de Ligação é ignorado.

     ![Ilustração da política de conexão Azure Cosmos DB](./media/performance-tips/connection-policy.png)

2. **Ligue para a OpenAsync para evitar a latência do arranque no primeiro pedido**

    Por padrão, o primeiro pedido tem uma latência mais elevada porque tem de ir buscar a tabela de encaminhamento de endereços. Ao utilizar o [SDK V2](sql-api-sdk-dotnet.md), para evitar esta latência de arranque no primeiro pedido, deve ligar para openAsync() uma vez durante a inicialização da seguinte forma.

        await client.OpenAsync();

    > [!NOTE] 
    > O método OpenAsync gerará pedidos para obter a tabela de encaminhamento de endereços para todos os recipientes da conta. Para contas que têm muitos contentores mas a sua aplicação acede a um subconjunto deles, geraria uma quantidade desnecessária de tráfego que torna a inicialização lenta. Assim, usar o método OpenAsync pode não ser útil neste cenário, uma vez que abranda o arranque da aplicação.

   <a id="same-region"></a>
3. **Clientes collocalizados na mesma região de Azure para desempenho**

    Sempre que possível, coloque todas as aplicações que chamem Azure Cosmos DB na mesma região que a base de dados Azure Cosmos. Para uma comparação aproximada, as chamadas para Azure Cosmos DB dentro da mesma região completam dentro de 1-2 ms, mas a latência entre a costa oeste e leste dos EUA é de >50 ms. Esta latência pode variar de pedido a pedido dependendo da rota percorreu pelo pedido à medida que passa do cliente para o limite do datacenter Azure. A latência mais baixa possível é alcançada garantindo que a aplicação de chamada está localizada na mesma região de Azure que o ponto final do Azure Cosmos DB. Para obter uma lista das regiões disponíveis, consulte [as regiões de Azure.](https://azure.microsoft.com/regions/#services)

    ![Ilustração da política de conexão Azure Cosmos DB](./media/performance-tips/same-region.png)
   <a id="increase-threads"></a>

4. **Aumentar o número de fios/tarefas**

    Uma vez que as chamadas para o Azure Cosmos DB são feitas ao longo da rede, poderá ter de variar o grau de paralelismo dos seus pedidos para que a aplicação do cliente passe muito pouco tempo à espera entre pedidos. Por exemplo, se estiver a usar . Biblioteca Paralela de [Tarefas](https://msdn.microsoft.com//library/dd460717.aspx)net, criar na ordem dos 100s de Tarefas leitura ou escrita para Azure Cosmos DB.

5. **Ativar o networking acelerado**

   Para reduzir a latência e o nervosismo do CPU, recomendamos que as máquinas virtuais do cliente estejam ativadas em rede. Consulte a [Create a Windows virtual machine com Networking Acelerado](../virtual-network/create-vm-accelerated-networking-powershell.md) ou Crie uma máquina virtual Linux com artigos de [Networking Acelerados](../virtual-network/create-vm-accelerated-networking-cli.md) para permitir a ligação em rede acelerada.


## <a name="sdk-usage"></a>Utilização do SDK
1. **Instale o Mais recente SDK**

    Os SDKs DB Azure Cosmos estão constantemente a ser melhorados para proporcionar o melhor desempenho. Consulte as páginas [Azure Cosmos DB SDK](sql-api-sdk-dotnet-standard.md) para determinar as mais recentes melhorias de SDK e revisão.

2. **Utilizar aAPIs stream**

    O [.NET SDK V3](sql-api-sdk-dotnet-standard.md) contém APIs de fluxo que podem receber e devolver dados sem serializar. 

    As aplicações de nível médio que não consomem as respostas do SDK diretamente, mas retransmitem-nas para outros níveis de aplicação podem beneficiar das APIs stream. Consulte as amostras de gestão do [item,](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement) por exemplo, no manuseamento de fluxos.

3. **Use um cliente singleton Azure Cosmos DB para a vida útil da sua aplicação**

    Cada instância documentClient e CosmosClient é segura em fios e executa uma gestão eficiente da ligação e endereça-se ao operar em modo direto. Para permitir uma gestão eficiente da ligação e um melhor desempenho por parte do cliente SDK, recomenda-se a utilização de uma única instância por AppDomain durante o tempo de vida da aplicação.

   <a id="max-connection"></a>

4. **Aumente System.Net MaxConnections por hospedeiro ao utilizar o modo Gateway**

    Os pedidos da Azure Cosmos DB são feitos sobre HTTPS/REST ao utilizar o modo Gateway, e estão sujeitos ao limite de ligação predefinido por nome de anfitrião ou endereço IP. Poderá ser necessário definir as MaxConnections para um valor mais elevado (100-1000) para que a biblioteca do cliente possa utilizar múltiplas ligações simultâneas ao Azure Cosmos DB. No .NET SDK 1.8.0 ou acima, o valor predefinido para [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx) é de 50 e para alterar o valor, pode definir os [Documentos.Cliente.ConnectionPolicy.MaxConnectionLimit](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit.aspx) para um valor mais elevado.

5. **Afinação de consultas paralelas para coleções divididas**

     SQL .NET SDK versão 1.9.0 e acima suportam consultas paralelas, que lhe permitem consultar uma coleção dividida em paralelo. Para obter mais informações, consulte amostras de [código relacionadas](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs) com o trabalho com os SDKs. Consultas paralelas são concebidas para melhorar a latência de consultas e a entrada sobre a sua congénere em série. Consultas paralelas fornecem dois parâmetros que os utilizadores podem sintonizar para adaptar as suas exigências personalizadas, (a) MaxDegreeOfParallelismo: para controlar o número máximo de divisórias então podem ser consultados em paralelo, e (b) MaxBufferedItemCount: para controlar o número de resultados pré-rebuscados.

    a ***Grau de afinação do paralelismo\:*** trabalhos de consulta paralela consultando múltiplas divisórias paralelamente. No entanto, os dados de uma partição individual são recolhidos em série no que diz respeito à consulta. A definição do `MaxDegreeOfParallelism` em [SDK V2](sql-api-sdk-dotnet.md) ou `MaxConcurrency` em [SDK V3](sql-api-sdk-dotnet-standard.md) para o número de divisórias tem a máxima possibilidade de conseguir a consulta mais performante, desde que todas as outras condições do sistema permaneçam as mesmas. Se não souber o número de divisórias, pode definir o grau de paralelismo para um número elevado, e o sistema escolhe o mínimo (número de divisórias, entrada fornecida pelo utilizador) como o grau de paralelismo.

    É importante notar que as consultas paralelas produzem os melhores benefícios se os dados forem distribuídos uniformemente por todas as divisórias no que diz respeito à consulta. Se a coleção dividida for dividida de modo a que a toda ou a maioria dos dados devolvidos por uma consulta se concentrem em algumas divisórias (uma partição no pior dos casos), então o desempenho da consulta seria engarrafado por essas divisórias.

    (b) ***Afinação MaxBufferedItemCount\:*** Consulta paralela foi concebida para antecipar resultados enquanto o atual lote de resultados está a ser processado pelo cliente. A pré-busca ajuda na melhoria geral da latência de uma consulta. MaxBufferedItemCount é o parâmetro para limitar o número de resultados pré-rebuscados. Configurar o MaxBufferedItemCount para o número esperado de resultados devolvidos (ou um número superior) permite que a consulta receba o máximo benefício da pré-busca.

    A pré-busca funciona da mesma forma, independentemente do grau de paralelismo, e há um único tampão para os dados de todas as divisórias.  

6. **Implementar recuo sintetizador em intervalos de retryAfter**

    Durante os testes de desempenho, deve aumentar a carga até que uma pequena taxa de pedidos seja acelerada. Se for acelerada, a aplicação do cliente deve recuar no acelerador para o intervalo de repetição especificado pelo servidor. Respeitar o backoff garante que se passa o mínimo de tempo à espera entre as tentativas. O suporte à política de retry está incluído na versão 1.8.0 e acima do SQL [.NET](sql-api-sdk-dotnet.md) e [Java](sql-api-sdk-java.md), versão 1.9.0 e acima do [Nó.js](sql-api-sdk-node.md) e [Python,](sql-api-sdk-python.md)e todas as versões suportadas dos SDKs [.NET Core.](sql-api-sdk-dotnet-core.md) Para mais informações, [RetryAfter](https://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx).
    
    Com a versão 1.19 e mais tarde do .NET SDK, existe um mecanismo para registar informações adicionais de diagnóstico e problemas de latência de resolução de problemas, como mostra a amostra seguinte. Pode registar a cadeia de diagnóstico para pedidos que tenham uma latência de leitura superior. A cadeia de diagnóstico capturada irá ajudá-lo a entender o número de vezes que observou 429s para um determinado pedido.
    ```csharp
    ResourceResponse<Document> readDocument = await this.readClient.ReadDocumentAsync(oldDocuments[i].SelfLink);
    readDocument.RequestDiagnosticsString 
    ```

7. **Documento cache URIs para latência de leitura inferior**

    Cache documento URIs sempre que possível para o melhor desempenho de leitura. Tem de definir a lógica para cache o ID do recurso quando cria o recurso. As aparências baseadas em ID de recursos são mais rápidas do que as aparências baseadas no nome, por isso a cache destes valores melhora o desempenho. 

   <a id="tune-page-size"></a>
8. **Sintonize o tamanho da página para consultas/feeds de leitura para um melhor desempenho**

   Ao realizar uma leitura a granel de documentos utilizando a funcionalidade de feed de leitura (por exemplo, ReadDocumentFeedAsync) ou ao emitir uma consulta SQL, os resultados são devolvidos de forma segmentada se o conjunto de resultados for demasiado grande. Por padrão, os resultados são devolvidos em pedaços de 100 itens ou 1 MB, qualquer que seja o limite atingido primeiro.

   Para reduzir o número de viagens de ida e volta da rede necessárias para recuperar todos os resultados aplicáveis, pode aumentar o tamanho da página utilizando o cabeçalho de pedido de contagem de [itens x-ms-max](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) até 1000. Nos casos em que precisa de apresentar apenas alguns resultados, por exemplo, se a interface de utilizador ou aplicação API devolver apenas 10 resultados por vez, também pode diminuir o tamanho da página para 10 para reduzir a entrada consumida para leituras e consultas.

   > [!NOTE] 
   > A propriedade maxItemCount não deve ser usada apenas para fins de paginação. É o principal uso para melhorar o desempenho das consultas reduzindo o número máximo de itens devolvidos numa única página.  

   Também pode definir o tamanho da página utilizando os SDKs DB Do MB Do Mato Azul. A propriedade [MaxItemCount](/dotnet/api/microsoft.azure.documents.client.feedoptions.maxitemcount?view=azure-dotnet) em FeedOptions permite-lhe definir o número máximo de itens a devolver na operação de enumeração. Quando `maxItemCount` está definido para -1, o SDK encontra automaticamente o valor mais ideal dependendo do tamanho do documento. Por exemplo:
    
   ```csharp
    IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });
   ```
    
   Quando uma consulta é executada, os dados resultantes são enviados dentro de um pacote de TCP. Se especificar um valor demasiado baixo para `maxItemCount`, o número de viagens necessárias para enviar os dados dentro do pacote TCP é elevado, o que impacta o desempenho. Portanto, se você não tem certeza de qual o valor a definir para `maxItemCount` propriedade, é melhor defini-lo para -1 e deixar o SDK escolher o valor padrão. 

9. **Aumentar o número de fios/tarefas**

    Ver Aumentar o [número de fios/tarefas](#increase-threads) na secção de Networking.

## <a name="indexing-policy"></a>Política de Indexação
 
1. **Excluir os caminhos não utilizados da indexação para assegurar escritas mais rápidas**

    A política de indexação da Cosmos DB também permite especificar quais as trajetórias documentais a incluir ou excluir da indexação, alavancando caminhos de indexação (IndexingPolicy.IncludedPaths e IndexingPolicy.ExcluidosCaminhos). A utilização de trajetórias de indexação pode oferecer um melhor desempenho de escrita e armazenamento de índices mais baixos para cenários em que os padrões de consulta são conhecidos previamente, uma vez que os custos de indexação estão diretamente correlacionados com o número de caminhos únicos indexados.  Por exemplo, o código que se segue mostra como excluir uma secção inteira dos documentos (uma subárvore) de indexação utilizando o wildcard "*".

    ```csharp
    var collection = new DocumentCollection { Id = "excludedPathCollection" };
    collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
    collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);
    ```

    Para mais informações, consulte as políticas de [indexação do Azure Cosmos DB.](index-policy.md)

## <a name="throughput"></a>Débito
<a id="measure-rus"></a>

1. **Medir e sintonizar para unidades de pedido mais baixos/segunda utilização**

    A Azure Cosmos DB oferece um conjunto rico de operações de base de dados, incluindo consultas relacionais e hierárquicas com UDFs, procedimentos armazenados e gatilhos – todos operando nos documentos dentro de uma coleção de bases de dados. O custo associado a cada uma destas operações varia com base na CPU, E/S e memória necessárias para concluir a operação. Em vez de pensar e gerir recursos de hardware, pode pensar numa unidade de pedido (RU) como uma medida única para os recursos necessários para realizar várias operações de base de dados e servir um pedido de aplicação.

    A entrada é disponibilizada com base no número de unidades de [pedido definidas](request-units.md) para cada recipiente. O consumo unitário de pedido é avaliado como uma taxa por segundo. Os pedidos que excedam a taxa unitária de pedido previsto para o seu contentor são limitados até que a taxa desça abaixo do nível previsto para o contentor. Se a sua aplicação necessitar de um nível mais elevado de entrada, pode aumentar a sua entrada, disponibilizando unidades de pedido adicionais. 

    A complexidade de uma consulta tem impacto na quantidade de Unidades de Pedido que são consumidas para uma operação. O número de predicados, a natureza dos predicados, o número de UDFs e a dimensão dos dados de origem todos influenciam o custo das operações de consulta.

    Para medir o sobretempo de qualquer operação (criar, atualizar ou excluir), inspecione o cabeçalho [x-ms-request-charge](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) (ou a propriedade equivalente requestCharge em RecursosResponse\<T> ou FeedResponse\<T> no .NET SDK) para medir o número de unidades de pedido consumidas por estas operações.

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

    A taxa de pedido devolvida neste cabeçalho é uma fração da sua entrada prevista (isto é, 2000 RUs /segundo). Por exemplo, se a consulta anterior devolver 1000 documentos 1KB, o custo da operação é de 1000. Como tal, dentro de um segundo, o servidor honra apenas dois desses pedidos antes de limitar os pedidos subsequentes. Para mais informações, consulte as [unidades de pedido](request-units.md) e a [calculadora](https://www.documentdb.com/capacityplanner)da unidade de pedido .
<a id="429"></a>
2. **Taxa de manipulamento/taxa de pedido demasiado grande**

    Quando um cliente tenta exceder a entrada reservada para uma conta, não há degradação de desempenho no servidor e não há uso da capacidade de entrada para além do nível reservado. O servidor terminará preventivamente o pedido com o RequestRateTooLarge (código de estado HTTP 429) e devolverá o cabeçalho [x-ms-retry-after-ms](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) indicando a quantidade de tempo, em milissegundos, que o utilizador deve esperar antes de voltar a tentar o pedido.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Os SDKs captam implicitamente esta resposta, respeitam o cabeçalho de retry-after especificado pelo servidor e retentam novamente o pedido. A menos que a sua conta esteja a ser acedida simultaneamente por vários clientes, a próxima reprovação terá sucesso.

    Se tiver mais de um cliente a operar de forma consistente acima da taxa de pedido, a contagem de retry predefinido atualmente fixada para 9 internamente pelo cliente pode não ser suficiente; neste caso, o cliente lança uma DocumentClientException com o código de estado 429 para a aplicação. A contagem de retry predefinido pode ser alterada definindo as Opções de Retrynana na instância ConnectionPolicy. Por predefinição, o DocumentoClientException com o código de estado 429 é devolvido após um tempo acumulado de espera de 30 segundos se o pedido continuar a funcionar acima da taxa de pedido. Isto ocorre mesmo quando a contagem de retry atual é inferior à contagem máxima de retry, seja o padrão de 9 ou um valor definido pelo utilizador.

    Embora o comportamento automatizado de retry ajude a melhorar a resiliência e a usabilidade para a maioria das aplicações, pode estar em desacordo ao fazer referências de desempenho, especialmente quando se mede a latência.  A latência observada pelo cliente aumentará se a experiência atingir o acelerador do servidor e fizer com que o SDK do cliente volte a tentar silenciosamente. Para evitar picos de latência durante as experiências de desempenho, meça a carga devolvida por cada operação e certifique-se de que os pedidos estão a funcionar abaixo da taxa de pedido reservada. Para mais informações, consulte [Unidades de Pedido](request-units.md).
3. **Design para documentos menores para maior estoque**

    A taxa de pedido (isto é, custo de processamento de pedidos) de uma determinada operação está diretamente correlacionada com a dimensão do documento. As operações em grandes documentos custam mais do que as operações para pequenos documentos.

## <a name="next-steps"></a>Passos seguintes
Para uma aplicação de amostra usada para avaliar o Azure Cosmos DB para cenários de alto desempenho em algumas máquinas de clientes, consulte performance e testes de [escala com Azure Cosmos DB](performance-testing.md).

Além disso, para saber mais sobre a conceção da sua aplicação para escala e alto desempenho, consulte [A Partilha e Escalaem em Azure Cosmos DB](partition-data.md).
