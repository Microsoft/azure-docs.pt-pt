---
title: Dicas de desempenho do Azure Cosmos DB para .NET
description: Aprenda as opções de configuração do cliente para melhorar o desempenho do Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: sngun
ms.openlocfilehash: ee2743af2f8499aec04d8b6b733e1ba4c2a82083
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546067"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net"></a>Sugestões de desempenho para o Azure Cosmos DB e .NET

> [!div class="op_single_selector"]
> * [Java assíncrono](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

Azure Cosmos DB é uma base de dados distribuída rápida e flexível que escala perfeitamente com latência garantida e produção. Não é preciso fazer grandes alterações de arquitetura ou escrever código complexo para escalar a sua base de dados com o Azure Cosmos DB. Escalar para cima e para baixo é tão fácil como fazer uma única chamada aPi. Para saber mais, veja como fornecer a entrada de [recipientes](how-to-provision-container-throughput.md) ou [como fornecer a entrada](how-to-provision-database-throughput.md)de base de dados . Mas como o Azure Cosmos DB é acedido através de chamadas de rede, existem otimizações do lado do cliente que você pode fazer para alcançar o desempenho máximo quando você usa o [SQL .NET SDK](sql-api-sdk-dotnet-standard.md).

Por isso, se está a tentar melhorar o desempenho da sua base de dados, considere estas opções:

## <a name="hosting-recommendations"></a>Recomendações de hospedagem

**Para cargas de trabalho intensivas de consultas, utilize o Windows 64-bit em vez do processamento de anfitriões do Windows 32 bits**

Recomendamos o processamento do anfitrião windows 64 bits para um melhor desempenho. O SQL SDK inclui um ServiceInterop.dll nativo para analisar e otimizar consultas localmente. ServiceInterop.dll é suportado apenas na plataforma Windows x64. Para o Linux e outras plataformas não suportadas onde o ServiceInterop.dll não está disponível, é feita uma chamada adicional de rede para a porta de entrada para obter a consulta otimizada. Os seguintes tipos de aplicações utilizam o processamento de hospedeiro de 32 bits por padrão. Para alterar o processamento do hospedeiro para processamento de 64 bits, siga estes passos, com base no tipo da sua aplicação:

- Para aplicações executáveis, pode alterar o processamento do anfitrião definindo o alvo da [plataforma](https://docs.microsoft.com/visualstudio/ide/how-to-configure-projects-to-target-platforms?view=vs-2019) para **x64** na janela **Project Properties,** no separador **Build.**

- Para projetos de teste baseados em VSTest, pode alterar o processamento do anfitrião selecionando a Arquitetura padrão do processador padrão de**definições** > de **teste** > **como X64** no menu de Teste de **Estúdio** Visual.

- Para aplicações web ASP.NET implementadas localmente, pode alterar o processamento de anfitriões selecionando **a versão de 64 bits do IIS Express para web sites e projetos** no âmbito de Projetos Web de Opções de **Tools** > **Ferramentas** > **e Soluções.** > **Web Projects**

- Para ASP.NET aplicações web implantadas no Azure, pode alterar o processamento do anfitrião selecionando a plataforma **de 64 bits** nas definições de **Aplicação** no portal Azure.

> [!NOTE] 
> Por padrão, novos projetos do Estúdio Visual estão definidos para **Qualquer CPU**. Recomendamos que detetete o seu projeto para **x64** para que não mude para **x86**. Um projeto definido para **Qualquer CPU** pode facilmente mudar para **x86** se uma dependência apenas x86 for adicionada.<br/>
> ServiceInterop.dll needs to be in the folder that the SDK DLL is being executed from. Esta só deve ser uma preocupação se copiar manualmente DLLs ou tiver sistemas personalizados de construção/implementação.
    
**Ligue a recolha de lixo do lado do servidor (GC)**

A redução da frequência da recolha de lixo pode ajudar em alguns casos. Em .NET, coloque `true` [o gcServer](https://msdn.microsoft.com/library/ms229357.aspx) para .

**Esforce a carga de trabalho do seu cliente**

Se estiver a testar em níveis elevados de saída (mais de 50.000 RU/s), a aplicação do cliente pode tornar-se o estrangulamento devido à máquina que se limita ao CPU ou à utilização da rede. Se chegar a este ponto, pode continuar a empurrar ainda mais a conta Azure Cosmos DB, dimensionando as suas aplicações de clientes em vários servidores.

> [!NOTE] 
> O elevado uso de CPU pode causar um aumento da latência e solicitar exceções no tempo de paragem.

## <a name="networking"></a>Redes
<a id="direct-connection"></a>

**Política de ligação: Utilize o modo de ligação direta**

A forma como um cliente se conecta ao Azure Cosmos DB tem implicações importantes no desempenho, especialmente para a latência observada do lado do cliente. Existem duas definições de configuração chave disponíveis para configurar a política de ligação ao cliente: o *modo* de ligação e o *protocolo*de ligação .  Os dois modos disponíveis são:

   * Modo Gateway
      
     O modo Gateway é suportado em todas as plataformas SDK e é o padrão configurado para o [Microsoft.Azure.DocumentDB SDK](sql-api-sdk-dotnet.md). Se a sua aplicação for executado dentro de uma rede corporativa com restrições rigorosas de firewall, o modo gateway é a melhor escolha porque utiliza a porta HTTPS padrão e um único ponto final. A troca de desempenho, no entanto, é que o modo gateway envolve um saltos de rede adicionais cada vez que os dados são lidos ou escritos para O Azure Cosmos DB. Assim, o modo direto oferece um melhor desempenho porque há menos lúpulo de rede. Também recomendamos o modo de ligação gateway quando executa aplicações em ambientes que tenham um número limitado de ligações de tomadas.

     Quando utilizar o SDK em Funções Azure, particularmente no [plano de consumo,](../azure-functions/functions-scale.md#consumption-plan)esteja ciente dos limites atuais [nas ligações](../azure-functions/manage-connections.md). Nesse caso, o modo gateway pode ser melhor se também estiver a trabalhar com outros clientes baseados em HTTP dentro da sua aplicação Funções Azure.

   * Modo direto

     O modo direct suporta a conectividade através do protocolo TCP e é o modo de conectividade padrão se estiver a utilizar o [Microsoft.Azure.Cosmos/.NET V3 SDK](sql-api-sdk-dotnet-standard.md).

No modo gateway, o Azure Cosmos DB utiliza o porto 443 e as portas 10250, 10255 e 10256 quando estiver a usar o Azure Cosmos DB API para MongoDB. Os mapas da porta 10250 para uma instância padrão de MongoDB sem geo-replicação. Portos 10255 e 10256 mapa para a instância MongoDB que tem geo-replicação.
     
Quando utiliza tCP em modo direto, para além das portas de gateway, é necessário garantir que a gama de portas entre 10000 e 20000 está aberta porque o Azure Cosmos DB utiliza portas TCP dinâmicas. Se estas portas não estiverem abertas e tentar utilizar o TCP, recebe um erro indisponível de serviço 503. Esta tabela mostra os modos de conectividade disponíveis para várias APIs e as portas de serviço utilizadas para cada API:

|Modo de ligação  |Protocolo apoiado  |SDKs suportados  |Porto API/Serviço  |
|---------|---------|---------|---------|
|Gateway  |   HTTPS    |  Todos os SDKs    |   SQL (443), MongoDB (10250, 10255, 10256), Tabela (443), Cassandra (10350), Gráfico (443)    |
|Direct    |     TCP    |  SDK .NET    | Portos na gama 10000 a 20000 |

A Azure Cosmos DB oferece um modelo de programação RESTful simples e aberto em HTTPS. Além disso, oferece um protocolo TCP eficiente, que também é RESTful no seu modelo de comunicação e está disponível através do SDK cliente .NET. O protocolo TCP utiliza TLS para autenticação inicial e encriptação do tráfego. Para um melhor desempenho, utilize o protocolo TCP sempre que possível.

Para o SDK V3, configure o `CosmosClient` modo de `CosmosClientOptions`ligação quando criar a instância, em . Lembre-se que o modo direto é o padrão.

```csharp
var serviceEndpoint = new Uri("https://contoso.documents.net");
var authKey = "your authKey from the Azure portal";
CosmosClient client = new CosmosClient(serviceEndpoint, authKey,
new CosmosClientOptions
{
    ConnectionMode = ConnectionMode.Gateway // ConnectionMode.Direct is the default
});
```

Para o Microsoft.Azure.DocumentDB SDK, configura o modo de `DocumentClient` ligação `ConnectionPolicy` durante a construção da instância utilizando o parâmetro. Se utilizar o modo direto, `Protocol` também pode `ConnectionPolicy` definir o parâmetro utilizando o parâmetro.

```csharp
var serviceEndpoint = new Uri("https://contoso.documents.net");
var authKey = "your authKey from the Azure portal";
DocumentClient client = new DocumentClient(serviceEndpoint, authKey,
new ConnectionPolicy
{
   ConnectionMode = ConnectionMode.Direct, // ConnectionMode.Gateway is the default
   ConnectionProtocol = Protocol.Tcp
});
```

Uma vez que o TCP é suportado apenas em modo direto, se utilizar `Protocol` o `ConnectionPolicy` modo gateway, o protocolo HTTPS é sempre utilizado para comunicar com o gateway e o valor é ignorado.

![A política de conexão Azure Cosmos DB](./media/performance-tips/connection-policy.png)

**Ligue para a OpenAsync para evitar a latência do arranque no primeiro pedido**

Por padrão, o primeiro pedido tem maior latência porque precisa de buscar a tabela de encaminhamento de endereços. Quando utilizar [o SDK V2,](sql-api-sdk-dotnet.md)ligue `OpenAsync()` uma vez durante a inicialização para evitar esta latência de arranque no primeiro pedido:

    await client.OpenAsync();

> [!NOTE] 
> `OpenAsync`gerará pedidos para obter o quadro de encaminhamento de endereços para todos os recipientes da conta. Para contas que têm muitos contentores mas cuja aplicação `OpenAsync` acede a um subconjunto dos mesmos, geraria uma quantidade desnecessária de tráfego, o que tornaria a inicialização lenta. Assim, `OpenAsync` usar pode não ser útil neste cenário porque abranda o arranque da aplicação.

   <a id="same-region"></a>
**Para desempenho, colode clientes na mesma região de Azure**

Sempre que possível, coloque todas as aplicações que liguem para o Azure Cosmos DB na mesma região que a base de dados Azure Cosmos DB. Eis uma comparação aproximada: as chamadas para O BD Do M na mesma região completam-se entre 1 ms e 2 ms, mas a latência entre a costa oeste e leste dos EUA é superior a 50 ms. Esta latência pode variar de pedido a pedido, dependendo da rota percorreu o pedido à medida que passa do cliente para o limite do datacenter Azure. Você pode obter a menor latência possível, garantindo que a aplicação de chamada está localizada dentro da mesma região de Azure que o ponto final do Azure Cosmos DB. Para obter uma lista das regiões disponíveis, consulte [as regiões de Azure.](https://azure.microsoft.com/regions/#services)

![A política](./media/performance-tips/same-region.png) de conexão Azure Cosmos DB<a id="increase-threads"></a>

**Aumentar o número de fios/tarefas**

Como as chamadas para o Azure Cosmos DB são feitas ao longo da rede, poderá ter de variar o grau de paralelismo dos seus pedidos para que a aplicação do cliente passe o mínimo de tempo à espera entre pedidos. Por exemplo, se estiver a usar a [Biblioteca Paralela](https://msdn.microsoft.com//library/dd460717.aspx).NET Task , crie na ordem das centenas de tarefas que lêem ou escrevem para o Azure Cosmos DB.

**Ativar o networking acelerado**
 
 Para reduzir a latência e o nervosismo do CPU, recomendamos que ative uma ligação acelerada em redes em máquinas virtuais de clientes. Consulte [Criar uma máquina virtual do Windows com networking acelerado](../virtual-network/create-vm-accelerated-networking-powershell.md) ou criar uma máquina virtual [Linux com rede acelerada](../virtual-network/create-vm-accelerated-networking-cli.md).

## <a name="sdk-usage"></a>Utilização do SDK
**Instale o Mais recente SDK**

Os SDKs DB Azure Cosmos estão constantemente a ser melhorados para proporcionar o melhor desempenho. Consulte as páginas [Azure Cosmos DB SDK](sql-api-sdk-dotnet-standard.md) para determinar as mais recentes melhorias de SDK e revisão.

**Use APIs de fluxo**

[.NET SDK V3](sql-api-sdk-dotnet-standard.md) contém APIs de fluxo que podem receber e devolver dados sem serializar. 

Aplicações de nível médio que não consomem respostas diretamente do SDK, mas retransmitem-nas para outros níveis de aplicação podem beneficiar das APIs stream. Consulte as amostras de gestão de [itens,](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement) por exemplo, de manipulação de fluxos.

**Use um cliente singleton Azure Cosmos DB para a vida útil da sua aplicação**

Cada `DocumentClient` `CosmosClient` e um caso é seguro para fios e executa uma gestão eficiente da ligação e endereça o cache quando opera em modo direto. Para permitir uma gestão eficiente da ligação e um melhor `AppDomain` desempenho do cliente SDK, recomendamos que utilize uma única instância por cada para a vida útil da aplicação.

   <a id="max-connection"></a>

**Aumente System.Net MaxConnections por hospedeiro ao utilizar o modo gateway**

Os pedidos da Azure Cosmos DB são feitos em HTTPS/REST quando utiliza o modo gateway. Estão sujeitos ao limite de ligação predefinido por nome de anfitrião ou endereço IP. Você pode precisar `MaxConnections` definir para um valor mais alto (100 a 1.000) para que a biblioteca do cliente possa usar múltiplas conexões simultâneas com o Azure Cosmos DB. Em .NET SDK 1.8.0 e posteriormente, o valor padrão para [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx) é de 50. Para alterar o valor, pode definir [Documentos.Cliente.ConnectionPolicy.MaxConnectionLimit](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit.aspx) para um valor mais elevado.

**Sintonize consultas paralelas para coleções divididas**

SQL .NET SDK 1.9.0 e posteriormente suportam consultas paralelas, que lhe permitem consultar uma coleção dividida em paralelo. Para obter mais informações, consulte amostras de [código relacionadas](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs) com o trabalho com os SDKs. Consultas paralelas são concebidas para proporcionar uma melhor latência e entrada de consulta do que a sua congénere em série. Consultas paralelas fornecem dois parâmetros que pode sintonizar para se adaptar aos seus requisitos: 
- `MaxDegreeOfParallelism`controla o número máximo de divisórias que podem ser consultadas paralelamente. 
- `MaxBufferedItemCount`controla o número de resultados pré-rebuscados.

***Grau de afinação do paralelismo***

A consulta paralela funciona consultando múltiplas divisórias em paralelo. Mas os dados de uma divisória individual são recolhidos em série no que diz respeito à consulta. A `MaxDegreeOfParallelism` definição em [SDK V2](sql-api-sdk-dotnet.md) ou `MaxConcurrency` em [SDK V3](sql-api-sdk-dotnet-standard.md) para o número de divisórias tem a melhor chance de alcançar a consulta mais performante, desde que todas as outras condições do sistema permaneçam as mesmas. Se não sabe o número de divisórias, pode definir o grau de paralelismo para um número elevado. O sistema escolherá o mínimo (número de divisórias, entrada fornecida pelo utilizador) como o grau de paralelismo.

Note que consultas paralelas produzem mais benefícios se os dados forem distribuídos uniformemente por todas as divisórias no que diz respeito à consulta. Se a recolha dividida for dividida de modo a que todos ou a maioria dos dados devolvidos por uma consulta se concentrem em algumas divisórias (uma divisória é o pior caso), essas divisórias irão engarrafar o desempenho da consulta.

***Afinação MaxBufferedItemCount***
    
A consulta paralela foi concebida para pré-obter resultados enquanto o atual lote de resultados está a ser processado pelo cliente. Esta pré-busca ajuda a melhorar a latência geral de uma consulta. O `MaxBufferedItemCount` parâmetro limita o número de resultados pré-rebuscados. Definir `MaxBufferedItemCount` para o número esperado de resultados devolvidos (ou um número superior) para permitir que a consulta receba o máximo benefício da pré-busca.

A pré-busca funciona da mesma forma, independentemente do grau de paralelismo, e há um único tampão para os dados de todas as divisórias.  

**Implementar recuo sintetizador em intervalos de retryAfter**

Durante os testes de desempenho, deve aumentar a carga até que uma pequena taxa de pedidos seja acelerada. Se os pedidos forem estrangulados, a aplicação do cliente deve recuar no acelerador para o intervalo de repetição especificado pelo servidor. Respeitar o backoff garante que passa si um tempo mínimo à espera entre tentativas. 

O apoio à política de retry está incluído nestes SDKs:
- Versão 1.8.0 e mais tarde do [.NET SDK para SQL](sql-api-sdk-dotnet.md) e o [Java SDK para SQL](sql-api-sdk-java.md)
- Versão 1.9.0 e mais tarde do [Node.js SDK para SQL](sql-api-sdk-node.md) e o [Python SDK para SQL](sql-api-sdk-python.md)
- Todas as versões suportadas dos SDKs [.NET Core](sql-api-sdk-dotnet-core.md) 

Para mais informações, consulte [RetryAfter](https://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx).
    
Na versão 1.19 e posteriormente do .NET SDK, existe um mecanismo para registar informações adicionais de diagnóstico e problemas de latência de resolução de problemas, como mostra a amostra seguinte. Pode registar a cadeia de diagnóstico para pedidos que tenham uma latência de leitura superior. A cadeia de diagnóstico capturada irá ajudá-lo a entender quantas vezes recebeu 429 erros para um determinado pedido.

```csharp
ResourceResponse<Document> readDocument = await this.readClient.ReadDocumentAsync(oldDocuments[i].SelfLink);
readDocument.RequestDiagnosticsString 
```

**Documento cache URIs para latência de leitura inferior**

Cache documento URIs sempre que possível para o melhor desempenho de leitura. Precisa definir a lógica para cache o ID do recurso quando cria um recurso. As análises baseadas em iDs de recursos são mais rápidas do que as aparências baseadas em nomes, por isso, a cache destes valores melhora o desempenho.

   <a id="tune-page-size"></a>
**Sintonize o tamanho da página para consultas/feeds de leitura para um melhor desempenho**

Quando faz uma leitura em massa de documentos utilizando `ReadDocumentFeedAsync`a funcionalidade de feed de leitura (por exemplo, ) ou quando emite uma consulta SQL, os resultados são devolvidos de forma segmentada se o conjunto de resultados for demasiado grande. Por padrão, os resultados são devolvidos em pedaços de 100 itens ou 1 MB, qualquer que seja o limite atingido primeiro.

Para reduzir o número de viagens de ida e volta da rede necessárias para recuperar todos os resultados aplicáveis, pode aumentar o tamanho da página utilizando [x-ms-max-item-count](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) para solicitar até 1.000 cabeçalhos. Quando precisa de apresentar apenas alguns resultados, por exemplo, se a interface de utilizador ou a aplicação API devolvera apenas 10 resultados de cada vez, também pode diminuir o tamanho da página para 10 para reduzir a entrada consumida para leituras e consultas.

> [!NOTE] 
> A `maxItemCount` propriedade não deve ser usada só para paginação. O seu principal uso é melhorar o desempenho das consultas reduzindo o número máximo de itens devolvidos numa única página.  

Também pode definir o tamanho da página utilizando os SDKs DB Do MB Do Azure Cosmos disponíveis. A propriedade [MaxItemCount](/dotnet/api/microsoft.azure.documents.client.feedoptions.maxitemcount?view=azure-dotnet) permite-lhe `FeedOptions` definir o número máximo de itens a devolver na operação de enumeração. Quando `maxItemCount` está definido para -1, o SDK encontra automaticamente o valor ideal, dependendo do tamanho do documento. Por exemplo:
    
```csharp
IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });
```
    
Quando uma consulta é executada, os dados resultantes são enviados dentro de um pacote de TCP. Se especificar um valor demasiado `maxItemCount`baixo para , o número de viagens necessárias para enviar os dados dentro do pacote TCP é elevado, o que afeta o desempenho. Portanto, se não tem certeza de qual `maxItemCount` o valor a definir para a propriedade, é melhor defini-lo para -1 e deixar o SDK escolher o valor padrão.

**Aumentar o número de fios/tarefas**

Ver [Aumentar a fonte do artigo Aumento do número de linhas/tarefas](#increase-threads) na secção de Networking deste artigo.

## <a name="indexing-policy"></a>Política de indexação
 
**Excluir caminhos não utilizados da indexação para escritas mais rápidas**

A política de indexação do Azure Cosmos DB também permite especificar quais as trajetórias documentais a incluir ou excluir da indexação utilizando caminhos de indexação (IndexingPolicy.IncludedPaths e IndexingPolicy.ExcluidosCaminhos). As trajetórias de indexação podem melhorar o desempenho da escrita e reduzir o armazenamento de índices para cenários em que os padrões de consulta são conhecidos previamente. Isto porque os custos de indexação se relacionam diretamente com o número de caminhos únicos indexados. Por exemplo, este código mostra como excluir uma secção inteira dos documentos (uma subárvore) de indexação utilizando o wildcard "*":

```csharp
var collection = new DocumentCollection { Id = "excludedPathCollection" };
collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);
```

Para mais informações, consulte as políticas de [indexação do Azure Cosmos DB.](index-policy.md)

## <a name="throughput"></a>Débito
<a id="measure-rus"></a>

**Medir e sintonizar para unidades de pedido mais baixas/segunda utilização**

A Azure Cosmos DB oferece um conjunto rico de operações de base de dados. Estas operações incluem consultas relacionais e hierárquicas com UDFs, procedimentos armazenados e gatilhos, todos operando nos documentos dentro de uma coleção de base de dados. O custo associado a cada uma destas operações varia consoante o CPU, a IO e a memória necessária para completar a operação. Em vez de pensar e gerir recursos de hardware, pode pensar numa Unidade de Pedido (RU) como uma medida única para os recursos necessários para realizar várias operações de base de dados e servir um pedido de aplicação.

A entrada é disponibilizada com base no número de [Unidades de Pedido definidas](request-units.md) para cada recipiente. Pedido O consumo unitário é avaliado como uma taxa por segundo. As aplicações que excedam a taxa unidade de pedido prevista para o seu contentor são limitadas até que a taxa desça abaixo do nível previsto para o contentor. Se a sua aplicação necessitar de um nível mais elevado de entrada, pode aumentar a sua entrada, disponibilizando unidades de pedido adicionais.

A complexidade de uma consulta afeta quantas Unidades de Pedido são consumidas para uma operação. O número de predicados, a natureza dos predicados, o número de UDFs e a dimensão do conjunto de dados de origem influenciam o custo das operações de consulta.

Para medir o sobretempo de qualquer operação (criar, atualizar ou eliminar), inspecione o cabeçalho `FeedResponse\<T>` [x-ms-request-charge](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) (ou o imóvel equivalente `RequestCharge` no `ResourceResponse\<T>` SDK .NET) para medir o número de Unidades de Pedido consumidas pelas operações:

```csharp
// Measure the performance (Request Units) of writes
ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);
Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);
// Measure the performance (Request Units) of queries
IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();
while (queryable.HasMoreResults)
    {
        FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>();
        Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge);
    }
```             

A taxa de pedido devolvida neste cabeçalho é uma fração da sua entrada prevista (isto é, 2.000 RUs /segundo). Por exemplo, se a consulta anterior devolver 1.000 documentos de 1 KB, o custo da operação é de 1.000. Assim, dentro de um segundo, o servidor honra apenas dois desses pedidos antes de limitar os pedidos posteriores. Para mais informações, consulte [Unidades de Pedido](request-units.md) e a [calculadora](https://www.documentdb.com/capacityplanner)da Unidade de Pedidos .
<a id="429"></a>

**Taxa de manipulamento/taxa de pedido demasiado grande**

Quando um cliente tenta exceder a entrada reservada para uma conta, não há degradação de desempenho no servidor e não há uso da capacidade de entrada para além do nível reservado. O servidor terminará preventivamente o pedido com requestRateTooLarge (código de estado HTTP 429). Devolverá um cabeçalho [x-ms-retry-after-ms](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) que indica a quantidade de tempo, em milissegundos, que o utilizador deve esperar antes de tentar novamente o pedido.

    HTTP Status 429,
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100

Os SDKs captam implicitamente esta resposta, respeitam o cabeçalho de retry-after especificado pelo servidor e retentam novamente o pedido. A menos que a sua conta esteja a ser acedida simultaneamente por vários clientes, a próxima reprovação terá sucesso.

Se tiver mais de um cliente a operar de forma consistente acima da taxa de pedido, a contagem de retry predefinido atualmente fixada para 9 internamente pelo cliente pode não ser suficiente. Neste caso, o cliente lança uma DocumentClientException com o código de estado 429 para a aplicação. 

Pode alterar a contagem de retry `ConnectionPolicy` predefinido, definindo a `RetryOptions` posição. Por predefinição, o DocumentoClientException com o código de estado 429 é devolvido após um tempo acumulado de espera de 30 segundos se o pedido continuar a funcionar acima da taxa de pedido. Este erro retorna mesmo quando a contagem de retry atual é inferior à contagem máxima de retry, se o valor atual é o padrão de 9 ou um valor definido pelo utilizador.

O comportamento automatizado de retry ajuda a melhorar a resiliência e a usabilidade para a maioria das aplicações. Mas pode não ser o melhor comportamento quando se faz referências de desempenho, especialmente quando se está a medir a latência. A latência observada pelo cliente aumentará se a experiência atingir o acelerador do servidor e fizer com que o SDK do cliente volte a tentar silenciosamente. Para evitar picos de latência durante as experiências de desempenho, meça a carga devolvida por cada operação e certifique-se de que os pedidos estão a funcionar abaixo da taxa de pedido reservada. Para mais informações, consulte [Unidades de Pedido](request-units.md).

**Para uma maior entrada, design para documentos menores**

A taxa de pedido (isto é, o custo de processamento de pedidos) de uma determinada operação correlaciona-se diretamente com a dimensão do documento. As operações em grandes documentos custam mais do que operações em pequenos documentos.

## <a name="next-steps"></a>Passos seguintes
Para uma aplicação de amostra que é usada para avaliar o Azure Cosmos DB para cenários de alto desempenho em algumas máquinas de clientes, consulte performance e testes de [escala com Azure Cosmos DB](performance-testing.md).

Para saber mais sobre a conceção da sua aplicação para escala e alto desempenho, consulte [A Partilha e Escalaem em Azure Cosmos DB](partition-data.md).
