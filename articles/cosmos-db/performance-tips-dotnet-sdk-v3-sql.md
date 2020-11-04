---
title: Dicas de desempenho do Azure Cosmos DB para .NET SDK v3
description: Aprenda opções de configuração do cliente para ajudar a melhorar o desempenho do Azure Cosmos DB .NET v3 SDK.
author: j82w
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/13/2020
ms.author: jawilley
ms.custom: devx-track-dotnet, contperfq2
ms.openlocfilehash: 8c042032fb12e63ae32eb5a51b06e07386e5fbfc
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93308840"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net"></a>Sugestões de desempenho para o Azure Cosmos DB e .NET
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [SDK .NET v3](performance-tips-dotnet-sdk-v3-sql.md)
> * [SDK .NET v2](performance-tips.md)
> * [SDK v4 de Java](performance-tips-java-sdk-v4-sql.md)
> * [SDK v2 Java assíncrono](performance-tips-async-java.md)
> * [SDK v2 Java síncrono](performance-tips-java.md)

Azure Cosmos DB é uma base de dados distribuída rápida e flexível que escala perfeitamente com níveis de latência e produção garantidos. Não é preciso fazer grandes alterações de arquitetura ou escrever códigos complexos para escalar a sua base de dados com a Azure Cosmos DB. Escalar para cima e para baixo é tão fácil como fazer uma única chamada API. Para saber mais, consulte o rendimento do contentor de [provisão](how-to-provision-container-throughput.md) ou o rendimento da base de dados de [provisões](how-to-provision-database-throughput.md). 

Como o Azure Cosmos DB é acedido através de chamadas de rede, pode fazer otimizações do lado do cliente para atingir o desempenho máximo quando utilizar o [SQL .NET SDK](sql-api-sdk-dotnet-standard.md).

Se estiver a tentar melhorar o desempenho da sua base de dados, considere as opções apresentadas nas seguintes secções.

## <a name="hosting-recommendations"></a>Recomendações de hospedagem

**Para cargas de trabalho intensivas em consultas, utilize o Windows 64-bit em vez do processamento de anfitriões Linux ou Windows 32 bits**

Recomendamos o processamento do anfitrião Windows 64 bits para um melhor desempenho. O SQL SDK inclui uma ServiceInterop.dll nativa para analisar e otimizar consultas localmente. ServiceInterop.dll é suportado apenas na plataforma Windows x64. 

Para o Linux e outras plataformas não suportadas onde ServiceInterop.dll não está disponível, é feita uma chamada adicional de rede para o gateway para obter a consulta otimizada. 

Os quatro tipos de aplicação listados aqui usam o processamento de hospedeiro de 32 bits por padrão. Para alterar o processamento do anfitrião para processamento de 64 bits para o seu tipo de aplicação, faça o seguinte:

- **Para aplicações executáveis** : Na janela Propriedades do **Projeto,** no painel **Build,** desaponte o alvo da [plataforma](/visualstudio/ide/how-to-configure-projects-to-target-platforms?preserve-view=true&view=vs-2019) para **x64**.

- **Para projetos de teste baseados em VSTest** : No menu visual studio **test,** selecione **Test**  >  **Definições de teste** de teste e, em seguida, defina **a Arquitetura do Processador Padrão** para **X64**.

- **Para aplicações web ASP.NET implementadas localmente :** Selecione **Tools**  >  **Options**  >  **Projects and Solutions**  >  **Web Projects** , e, em seguida, **selecione Utilize a versão de 64 bits do IIS Express para sites e projetos.**

- **Para ASP.NET aplicações web implementadas no Azure** : No portal Azure, nas **definições de Aplicação,** selecione a plataforma **de 64 bits.**

> [!NOTE] 
> Por predefinição, novos projetos do Visual Studio estão definidos para **Qualquer CPU**. Recomendamos que desemalte o seu projeto para **x64** para que não mude para **x86**. Um projeto definido para **Qualquer CPU** pode facilmente mudar para **x86** se for adicionada uma dependência apenas x86.<br/>
> O ficheiro ServiceInterop.dll tem de estar na pasta da qual o DLL SDK está a ser executado. Isto só deve ser uma preocupação se copiar manualmente DLLs ou tiver sistemas de construção ou implantação personalizados.
    
**Ligue a recolha de lixo do lado do servidor**

Reduzir a frequência da recolha de lixo pode ajudar em alguns casos. Em .NET, desateia [o gcServer](/dotnet/core/run-time-config/garbage-collector#flavors-of-garbage-collection) para `true` .

**Dimensione a carga de trabalho do seu cliente**

Se estiver a testar em níveis de produção elevados, ou a taxas superiores a 50.000 Unidades de Pedido por segundo (RU/s), a aplicação do cliente pode tornar-se um estrangulamento de carga de trabalho. Isto porque a máquina pode limitar a utilização da CPU ou da rede. Se chegar a este ponto, pode continuar a empurrar ainda mais a conta DB da Azure Cosmos, escalando as aplicações do seu cliente em vários servidores.

> [!NOTE] 
> O uso elevado do CPU pode causar um aumento da latência e solicitar exceções no tempo limite.

## <a name="networking"></a>Rede
<a id="direct-connection"></a>

**Política de ligação: Utilize o modo de ligação direta**

.NET V3 SDK o modo de ligação padrão é direto. Configura o modo de ligação quando cria o `CosmosClient` caso em `CosmosClientOptions` .  Para saber mais sobre diferentes opções de conectividade, consulte o artigo [modos de conectividade.](sql-sdk-connection-modes.md)

```csharp
string connectionString = "<your-account-connection-string>";
CosmosClient client = new CosmosClient(connectionString,
new CosmosClientOptions
{
    ConnectionMode = ConnectionMode.Gateway // ConnectionMode.Direct is the default
});
```

**Exaustão de portas efémeras**

Se vir um elevado volume de ligação ou um elevado uso da porta nas suas instâncias, verifique primeiro se as instâncias do seu cliente são singletons. Por outras palavras, as instâncias do cliente devem ser únicas para o tempo de vida da aplicação.

Quando está em execução no protocolo TCP, o cliente otimiza para a latência usando as ligações de longa duração. Isto contrasta com o protocolo HTTPS, que termina as ligações após dois minutos de inatividade.

Em cenários em que tenha acesso escasso, e se notar uma contagem de ligação mais alta quando comparado com o acesso ao modo Gateway, pode:

* Configure a propriedade [CosmosClientOptions.PortReuseMode](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.portreusemode) para (eficaz com as `PrivatePortPool` versões-quadro 4.6.1 e posterior e .NET Core 2.0 e posterior). Esta propriedade permite que o SDK use uma pequena piscina de portas efémeras para vários pontos finais de destino Azure Cosmos DB.
* Configure a propriedade [CosmosClientOptions.IdleConnectionTimeout](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.idletcpconnectiontimeout) como maior ou igual a 10 minutos. Os valores recomendados são de 20 minutos a 24 horas.

<a id="same-region"></a>

**Para o desempenho, colocate clientes na mesma região de Azure**

Quando possível, coloque quaisquer aplicações que liguem para Azure Cosmos DB na mesma região que a base de dados DB Azure Cosmos. Eis uma comparação aproximada: as chamadas para Azure Cosmos DB dentro da mesma região terminam entre 1 milissegundo (ms) a 2 ms, mas a latência entre a costa oeste e leste dos EUA é de mais de 50 ms. Esta latência pode variar de pedido a pedido, dependendo do percurso feito pelo pedido à medida que passa do cliente para o limite do datacenter Azure. 

Você pode obter a latência mais baixa possível, garantindo que o pedido de chamada está localizado dentro da mesma região de Azure que o ponto final Azure Cosmos DB. Para obter uma lista das regiões disponíveis, consulte as [regiões de Azure.](https://azure.microsoft.com/regions/#services)

:::image type="content" source="./media/performance-tips/same-region.png" alt-text="Clientes collocados na mesma região." border="false":::

   <a id="increase-threads"></a>

**Aumentar o número de fios/tarefas**

Como as chamadas para Azure Cosmos DB são feitas através da rede, poderá ser necessário variar o grau de concordância dos seus pedidos para que a aplicação do cliente passe o mínimo de tempo à espera entre os pedidos. Por exemplo, se estiver a utilizar a Biblioteca Paralela .NET [Task,](/dotnet/standard/parallel-programming/task-parallel-library-tpl)crie na ordem de centenas de tarefas que leiam ou escrevam para Azure Cosmos DB.

**Permitir networking acelerado**
 
Para reduzir a latência e o nervosismo do CPU, recomendamos que permita uma ligação acelerada em rede nas máquinas virtuais do seu cliente. Para obter mais informações, consulte [Criar uma máquina virtual do Windows com rede acelerada](../virtual-network/create-vm-accelerated-networking-powershell.md) ou criar uma máquina virtual [Linux com rede acelerada](../virtual-network/create-vm-accelerated-networking-cli.md).

## <a name="sdk-usage"></a>Utilização do SDK

**Instale o SDK mais recente**

Os Azure Cosmos DB SDKs estão constantemente a ser melhorados para proporcionar o melhor desempenho. Para determinar as mais recentes melhorias da SDK e rever as [melhorias, consulte a Azure Cosmos DB SDK](sql-api-sdk-dotnet-standard.md).

**Use APIs de fluxo**

[.NET SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3) contém APIs de fluxo que podem receber e devolver dados sem serializar. 

Aplicações de nível médio que não consomem respostas diretamente do SDK, mas retransmiti-las para outros níveis de aplicação podem beneficiar das APIs de fluxo. Por exemplo, o manuseamento de correntes, consulte as amostras [de gestão](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement) de artigos.

**Use um cliente singleton Azure Cosmos DB para a vida da sua aplicação**

Cada `CosmosClient` instância é segura e executa uma gestão eficiente da ligação e cache de endereço quando funciona no modo Direct. Para permitir uma gestão eficiente da ligação e um melhor desempenho do cliente SDK, recomendamos que utilize um único exemplo por `AppDomain` dia durante o tempo de vida da aplicação.

Quando está a trabalhar em Funções Azure, as instâncias também devem seguir as [diretrizes](../azure-functions/manage-connections.md#static-clients) existentes e manter uma única instância.

<a id="max-connection"></a>

**Desativar a resposta ao conteúdo nas operações de escrita**

Para cargas de trabalho pesadas criar cargas útil, desa um `EnableContentResponseOnWrite` ressalsse a `false` . O serviço deixará de devolver o recurso criado ou atualizado ao SDK. Normalmente, porque a aplicação tem o objeto que está a ser criado, não precisa do serviço para devolvê-lo. Os valores do cabeçalho ainda estão acessíveis, como uma taxa de pedido. Desativar a resposta ao conteúdo pode ajudar a melhorar o desempenho, porque o SDK já não precisa de alocar memória ou serializar o corpo da resposta. Também reduz o uso da largura de banda da rede para ajudar ainda mais o desempenho.  

```csharp
ItemRequestOptions requestOptions = new ItemRequestOptions() { EnableContentResponseOnWrite = false };
ItemResponse<Book> itemResponse = await this.container.CreateItemAsync<Book>(book, new PartitionKey(book.pk), requestOptions);
// Resource will be null
itemResponse.Resource
```

**Permitir que a Granel otimize para a produção em vez da latência**

Ativar *a granel* para cenários em que a carga de trabalho requer uma grande quantidade de produção, e a latência não é tão importante. Para obter mais informações sobre como ativar a funcionalidade Bulk, e para saber para que cenários deve ser utilizado, consulte [Introdução ao suporte a granel](https://devblogs.microsoft.com/cosmosdb/introducing-bulk-support-in-the-net-sdk).

**Aumente System.Net MaxConnections por anfitrião quando utilizar o modo Gateway**

Os pedidos de DB da Azure Cosmos são feitos sobre HTTPS/REST quando utiliza o modo Gateway. Estão sujeitos ao limite de ligação predefinido por nome de hospedeiro ou endereço IP. Você pode precisar de definir `MaxConnections` para um valor mais alto (de 100 a 1.000) para que a biblioteca do cliente possa usar múltiplas ligações simultâneas ao Azure Cosmos DB. Em .NET SDK 1.8.0 e posteriormente, o valor predefinido para [ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) é de 50. Para alterar o valor, pode definir [`Documents.Client.ConnectionPolicy.MaxConnectionLimit`](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit) para um valor mais elevado.

**Afinar consultas paralelas para coleções divididas**

SQL .NET SDK suporta consultas paralelas, que permitem consultar um recipiente dividido em paralelo. Para obter mais informações, consulte [amostras de código relacionadas](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs) com o trabalho com os SDKs. Consultas paralelas são projetadas para proporcionar uma melhor consulta de latência e produção do que a sua contraparte em série. 

Consultas paralelas fornecem dois parâmetros que pode sintonizar para se adaptar às suas necessidades: 

- **MaxConcurrency** : Controla o número máximo de divisórias que podem ser consultadas em paralelo.

   A consulta paralela funciona consultando várias divisórias em paralelo. Mas os dados de uma partição individual são recolhidos em série no que diz respeito à consulta. A definição `MaxConcurrency` em [SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3) para o número de divisórias tem a melhor hipótese de alcançar a consulta mais performante, desde que todas as outras condições do sistema permaneçam as mesmas. Se não souber o número de divisórias, pode definir o grau de paralelismo para um número elevado. O sistema escolherá o mínimo (número de divisórias, entrada fornecida pelo utilizador) como o grau de paralelismo.

    Consultas paralelas são as que mais beneficiam se os dados forem distribuídos uniformemente em todas as divisórias no que diz respeito à consulta. Se a recolha dividida for dividida de modo a que todos ou a maioria dos dados devolvidos por uma consulta se concentrem em algumas divisórias (uma partição é o pior caso), essas divisórias irão engarrafar o desempenho da consulta.
   
- **MaxBufferedItemCount** : Controla o número de resultados pré-recáveis.

   A consulta paralela é projetada para pré-obter resultados enquanto o lote atual de resultados está sendo processado pelo cliente. Esta pré-busca ajuda a melhorar a latência geral de uma consulta. O `MaxBufferedItemCount` parâmetro limita o número de resultados pré-recedidos. `MaxBufferedItemCount`Desagreda ao número esperado de resultados devolvidos (ou um número mais elevado) para permitir que a consulta receba o máximo benefício da pré-obtenção.

   A pré-busca funciona da mesma forma, independentemente do grau de paralelismo, e há um único tampão para os dados de todas as divisórias.  

**Implementar backoff em Intervalos RetryAfter**

Durante os testes de desempenho, deverá aumentar a carga até que uma pequena taxa de pedidos seja acelerada. Se os pedidos forem acelerados, a aplicação do cliente deve recuar para o intervalo de repetição especificado pelo servidor. Respeitar o recuo ajuda a garantir que passará o mínimo de tempo à espera entre as retrações. 

Para obter mais informações, consulte [RetryAfter](/dotnet/api/microsoft.azure.cosmos.cosmosexception.retryafter?preserve-view=true&view=azure-dotnet#Microsoft_Azure_Cosmos_CosmosException_RetryAfter).
    
Há um mecanismo para registar informações adicionais de diagnóstico e problemas de latência, como mostra a amostra seguinte. Pode registar a cadeia de diagnósticos para pedidos com uma latência mais lida. A cadeia de diagnósticos capturada irá ajudá-lo a entender quantas vezes recebeu um erro *de 429* para um determinado pedido.

```csharp
ItemResponse<Book> readItemResponse = await this.cosmosContainer.ReadItemAsync<Book>("ItemId", new PartitionKey("PartitionKeyValue"));
readItemResponse.Diagnostics.ToString(); 
```

**Aumentar o número de fios/tarefas**

Ver [Aumentar o número de threads/tarefas](#increase-threads) na secção de Networking deste artigo.

## <a name="indexing-policy"></a>Política de indexação
 
**Excluir os caminhos não utilizados da indexação para assegurar escritas mais rápidas**

A política de indexação DB do Azure Cosmos também permite especificar quais os caminhos documentais a incluir ou excluir da indexação utilizando caminhos de indexação (IndexingPolicy.IncludedPaths e IndexingPolicy.ExcludeedPaths). 

Indexar apenas os caminhos de que necessita pode melhorar o desempenho da escrita, reduzir os encargos de RU nas operações de escrita e reduzir o armazenamento de índices para cenários em que os padrões de consulta são conhecidos previamente. Isto porque os custos de indexação estão correlacionados diretamente com o número de caminhos únicos indexados. Por exemplo, o seguinte código mostra como excluir uma secção inteira dos documentos (uma subtree) de indexação utilizando o wildcard "*":

```csharp
var containerProperties = new ContainerProperties(id: "excludedPathCollection", partitionKeyPath: "/pk" );
containerProperties.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
containerProperties.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
Container container = await this.cosmosDatabase.CreateContainerAsync(containerProperties);
```

Para obter mais informações, consulte [as políticas de indexação de DB do Azure Cosmos](index-policy.md).

## <a name="throughput"></a>Débito
<a id="measure-rus"></a>

**Meça e sintonize para menor utilização ru/s**

A Azure Cosmos DB oferece um rico conjunto de operações de base de dados. Estas operações incluem consultas relacionais e hierárquicas com ficheiros universal de formato de disco (UDF), procedimentos armazenados e gatilhos, todos operando nos documentos dentro de uma coleção de base de dados. 

Os custos associados a cada uma destas operações variam consoante o CPU, IO e memória que são necessários para completar a operação. Em vez de pensar e gerir recursos de hardware, pode pensar numa Unidade de Pedido como uma única medida para os recursos que são necessários para realizar várias operações de base de dados e servir um pedido de aplicação.

A produção é proscedida com base no número de Unidades de [Pedido definidas](request-units.md) para cada contentor. Pedido O consumo unitário é avaliado como uma taxa de unidades por segundo. Os pedidos que excedam a taxa de unidade de pedido prevista para o seu contentor são limitados até que a taxa baixe abaixo do nível previsto para o contentor. Se a sua aplicação necessitar de um nível de produção mais elevado, pode aumentar a sua produção fornecendo Unidades de Pedido adicionais.

A complexidade de uma consulta afeta quantas Unidades de Pedido são consumidas para uma operação. O número de predicados, a natureza dos predicados, o número de ficheiros UDF e o tamanho do conjunto de dados de origem influenciam o custo das operações de consulta.

Para medir a sobrecarga de qualquer operação (criar, atualizar ou apagar), inspecione o cabeçalho [x-ms-request-charge](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) (ou a propriedade equivalente `RequestCharge` dentro ou no `ResourceResponse\<T>` `FeedResponse\<T>` .NET SDK) para medir o número de Unidades de Pedido consumidas pelas operações:

```csharp
// Measure the performance (Request Units) of writes
ItemResponse<Book> response = await container.CreateItemAsync<Book>(myBook, new PartitionKey(myBook.PkValue));
Console.WriteLine("Insert of item consumed {0} request units", response.RequestCharge);
// Measure the performance (Request Units) of queries
FeedIterator<Book> queryable = container.GetItemQueryIterator<ToDoActivity>(queryString);
while (queryable.HasMoreResults)
    {
        FeedResponse<Book> queryResponse = await queryable.ExecuteNextAsync<Book>();
        Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge);
    }
```             

A taxa de pedido que é devolvida neste cabeçalho é uma fração da sua produção proviscionada (ou seja, 2.000 RU/s). Por exemplo, se a consulta anterior devolver 1.000 documentos 1-KB, o custo da operação é de 1.000. Então, dentro de um segundo, o servidor honra apenas dois pedidos antes de limitar os pedidos posteriores. Para obter mais informações, consulte [Unidades de Pedido](request-units.md) e a [calculadora da Unidade de Pedido.](https://www.documentdb.com/capacityplanner)
<a id="429"></a>

**Taxa de maneneta limitando/taxa de pedido demasiado grande**

Quando um cliente tenta exceder a produção reservada para uma conta, não há degradação de desempenho no servidor e não há uso da capacidade de produção para além do nível reservado. O servidor termina preventivamente o pedido com RequestRateTooLarge (código de estado HTTP 429). Devolve um cabeçalho [x-ms-retry-after-ms](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) que indica a quantidade de tempo, em milissegundos, que o utilizador deve esperar antes de tentar novamente o pedido.

```xml
    HTTP Status 429,
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100
```

Os SDKs capturam implicitamente esta resposta, respeitam o cabeçalho especificado pelo servidor e recaem o pedido. A menos que a sua conta esteja a ser acedida simultaneamente por vários clientes, a próxima repetição será bem sucedida.

Se tiver mais de um cliente a operar de forma cumulativa acima da taxa de pedido, a contagem de retíria por defeito que está atualmente definida para 9 internamente pelo cliente pode não ser suficiente. Neste caso, o cliente lança um CosmosException com o código de estado 429 para a aplicação. 

Pode alterar a contagem de repetição por defeito definindo `RetryOptions` a no `CosmosClientOptions` caso. Por predefinição, o CosmosException com código de estado 429 é devolvido após um tempo de espera acumulado de 30 segundos se o pedido continuar a funcionar acima da taxa de pedido. Este erro é devolvido mesmo quando a contagem de repetição atual é inferior à contagem máxima de repetição, quer o valor atual seja o padrão de 9 ou um valor definido pelo utilizador.

O comportamento de relembolso automatizado ajuda a melhorar a resiliência e a usabilidade para a maioria das aplicações. Mas pode não ser o melhor comportamento quando se está a fazer referências de desempenho, especialmente quando se está a medir a latência. A latência observada pelo cliente aumentará se a experiência atingir o acelerador do servidor e fizer com que o cliente SDK volte a tentar silenciosamente. Para evitar picos de latência durante as experiências de desempenho, meça a carga que é devolvida por cada operação e certifique-se de que os pedidos estão a funcionar abaixo da taxa de pedido reservada. 

Para mais informações, consulte [Unidades de Pedido.](request-units.md)

**Para uma maior produção, design para documentos menores**

A taxa de pedido (isto é, o custo de processamento de pedido) de uma operação especificada correlaciona-se diretamente com a dimensão do documento. As operações em grandes documentos custam mais do que operações em pequenos documentos.

## <a name="next-steps"></a>Passos seguintes
Para uma aplicação de amostra que é usada para avaliar Azure Cosmos DB para cenários de alto desempenho em algumas máquinas de clientes, consulte testes de [desempenho e escala com Azure Cosmos DB](performance-testing.md).

Para saber mais sobre a conceção da sua aplicação para escala e alto desempenho, consulte [Partition e dimensionamento em Azure Cosmos DB](partitioning-overview.md).