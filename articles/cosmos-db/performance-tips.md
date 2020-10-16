---
title: Dicas de desempenho do Azure Cosmos DB para .NET SDK v2
description: Aprenda opções de configuração do cliente para melhorar o desempenho do Azure Cosmos DB .NET v2 SDK.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/26/2020
ms.author: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: efedfb9701d12548b80eccda9cd2aa29bc644ac2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91802145"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net-sdk-v2"></a>Sugestões de desempenho para o Azure Cosmos DB e SDK de .NET v2

> [!div class="op_single_selector"]
> * [SDK .NET v3](performance-tips-dotnet-sdk-v3-sql.md)
> * [SDK .NET v2](performance-tips.md)
> * [SDK v4 de Java](performance-tips-java-sdk-v4-sql.md)
> * [SDK v2 Java assíncrono](performance-tips-async-java.md)
> * [SDK v2 Java síncrono](performance-tips-java.md)

Azure Cosmos DB é uma base de dados distribuída rápida e flexível que escala perfeitamente com latência e produção garantidas. Não é preciso fazer grandes alterações de arquitetura ou escrever códigos complexos para escalar a sua base de dados com a Azure Cosmos DB. Escalar para cima e para baixo é tão fácil como fazer uma única chamada API. Para saber mais, consulte [como providenciar a produção](how-to-provision-container-throughput.md) de contentores ou como providenciar a [produção de bases de dados](how-to-provision-database-throughput.md). Mas como o Azure Cosmos DB é acedido através de chamadas de rede, existem otimizações do lado do cliente que pode fazer para atingir o desempenho máximo quando utiliza o [SQL .NET SDK](sql-api-sdk-dotnet-standard.md).

Por isso, se está a tentar melhorar o desempenho da sua base de dados, considere estas opções:

## <a name="upgrade-to-the-net-v3-sdk"></a>Upgrade para o .NET V3 SDK

O [.NET v3 SDK](https://github.com/Azure/azure-cosmos-dotnet-v3) é lançado. Se utilizar o .NET v3 SDK, consulte o [guia de desempenho .NET v3](performance-tips-dotnet-sdk-v3-sql.md) para obter as seguintes informações:

- Predefinições no modo TCP direto
- Suporte a API de fluxo
- Suporte serializador personalizado para permitir System.Text.JSutilização ON
- Suporte integrado ao lote e a granel

## <a name="hosting-recommendations"></a>Recomendações de hospedagem

**Para cargas de trabalho intensivas em consultas, utilize o Windows 64-bit em vez do processamento de anfitriões Linux ou Windows 32 bits**

Recomendamos o processamento do anfitrião Windows 64 bits para um melhor desempenho. O SQL SDK inclui uma ServiceInterop.dll nativa para analisar e otimizar consultas localmente. ServiceInterop.dll é suportado apenas na plataforma Windows x64. Para o Linux e outras plataformas não suportadas onde ServiceInterop.dll não está disponível, é feita uma chamada adicional de rede para o gateway para obter a consulta otimizada. Os seguintes tipos de aplicações utilizam o processamento de hospedeiro de 32 bits por padrão. Para alterar o processamento do anfitrião para processamento de 64 bits, siga estes passos, com base no tipo da sua aplicação:

- Para aplicações executáveis, pode alterar o processamento do anfitrião definindo o alvo da [plataforma](https://docs.microsoft.com/visualstudio/ide/how-to-configure-projects-to-target-platforms?view=vs-2019&preserve-view=true) para **x64** na janela Propriedades do **Projeto,** no separador **Construir.**

- Para projetos de teste baseados em VSTest, pode alterar o processamento do hospedeiro selecionando a Arquitetura do Processador Padrão de Definições de **Teste**de Teste  >  **Test Settings**  >  **como X64** no menu Visual Studio **Test.**

- Para aplicações web de ASP.NET implementadas localmente, pode alterar o processamento de anfitriões selecionando **Utilize a versão de 64 bits do IIS Express para sites e projetos** no âmbito de **Projetos**Web  >  **de Opções de**  >  **Ferramentas e Soluções.**  >  **Web Projects**

- Para ASP.NET aplicações web implementadas no Azure, pode alterar o processamento do anfitrião selecionando a plataforma **de 64 bits** nas **definições de Aplicação** no portal Azure.

> [!NOTE] 
> Por predefinição, novos projetos do Visual Studio estão definidos para **Qualquer CPU**. Recomendamos que desemalte o seu projeto para **x64** para que não mude para **x86**. Um projeto definido para **Qualquer CPU** pode facilmente mudar para **x86** se for adicionada uma dependência apenas x86.<br/>
> ServiceInterop.dll precisa estar na pasta da qual o SDK DLL está a ser executado. Isto só deve ser uma preocupação se copiar manualmente DLLs ou tiver sistemas de construção/implementação personalizados.
    
**Ligue a recolha de lixo do lado do servidor (GC)**

Reduzir a frequência da recolha de lixo pode ajudar em alguns casos. Em .NET, desateia [o gcServer](https://msdn.microsoft.com/library/ms229357.aspx) para `true` .

**Dimensione a carga de trabalho do seu cliente**

Se estiver a testar em níveis de produção elevados (mais de 50.000 RU/s), a aplicação do cliente pode tornar-se o estrangulamento devido à tampa da máquina no CPU ou na utilização da rede. Se chegar a este ponto, pode continuar a empurrar ainda mais a conta DB da Azure Cosmos, escalando as aplicações do seu cliente em vários servidores.

> [!NOTE] 
> O uso elevado do CPU pode causar um aumento da latência e solicitar exceções no tempo limite.

## <a name="networking"></a><a id="networking"></a> Networking

**Política de ligação: Utilize o modo de ligação direta**

A forma como um cliente se conecta à Azure Cosmos DB tem implicações importantes no desempenho, especialmente para a latência observada do lado do cliente. Existem duas definições de configuração chave disponíveis para configurar a política de ligação do cliente: o *modo* de ligação e o *protocolo de*ligação .  Os dois modos disponíveis são:

  * Modo gateway (Padrão)
      
    O modo Gateway é suportado em todas as plataformas SDK e é o padrão configurado para o [Microsoft.Azure.DocumentDB SDK](sql-api-sdk-dotnet.md). Se a sua aplicação for executado dentro de uma rede corporativa com restrições rígidas de firewall, o modo gateway é a melhor escolha porque utiliza a porta HTTPS padrão e um único ponto final DNS. A troca de desempenho, no entanto, é que o modo gateway envolve um salto de rede adicional cada vez que os dados são lidos ou escritos para Azure Cosmos DB. Assim, o modo direto oferece um melhor desempenho porque há menos lúpulo de rede. Recomendamos também o modo de ligação de gateway quando executar aplicações em ambientes com um número limitado de ligações à tomada.

    Quando utilizar o SDK em Funções Azure, nomeadamente no [plano de Consumo,](../azure-functions/functions-scale.md#consumption-plan)esteja atento aos [atuais limites de ligações](../azure-functions/manage-connections.md). Nesse caso, o modo gateway pode ser melhor se também estiver a trabalhar com outros clientes baseados em HTTP dentro da sua aplicação Azure Functions.

  * Modo direto

    O modo direto suporta a conectividade através do protocolo TCP.
     
Quando utilizar o TCP no modo direto, para além das portas gateway, é necessário garantir que a autonomia entre 10000 e 2000 está aberta porque a Azure Cosmos DB utiliza portas TCP dinâmicas. Quando utilizar o modo direto nos [pontos finais privados,](./how-to-configure-private-endpoints.md)deve estar aberta toda a gama de portas TCP de 0 a 65535. Se estas portas não estiverem abertas e tentar utilizar o protocolo TCP, receberá um erro 503 Serviço Indisponível. A tabela a seguir mostra os modos de conectividade disponíveis para várias APIs e as portas de serviço utilizadas para cada API:

|Modo de ligação  |Protocolo apoiado  |SDKs apoiados  |Porta API/Serviço  |
|---------|---------|---------|---------|
|Gateway  |   HTTPS    |  Todos os SDKs    |   SQL (443), MongoDB (10250, 10255, 10256), Tabela (443), Cassandra (10350), Gráfico (443) <br> A porta 10250 mapeia para um API API API AZure Cosmos padrão para a instância MongoDB sem geo-replicação. Enquanto as portas 10255 e 10256 mapeam para o caso que tem geo-replicação.   |
|Direct    |     TCP    |  SDK .NET    | Quando utilizar pontos finais públicos/de serviço: portas na gama 10000-20000<br>Quando utilizar pontos finais privados: portas na gama 0 a 65535 |

A Azure Cosmos DB oferece um modelo de programação RESTful simples e aberto sobre HTTPS. Além disso, oferece um protocolo TCP eficiente, que também é RESTful no seu modelo de comunicação e está disponível através do cliente .NET SDK. O protocolo TCP utiliza O Sº TLS para autenticação inicial e encriptação do tráfego. Para obter um melhor desempenho, utilize o protocolo TCP sempre que possível.

Para o Microsoft.Azure.DocumentDB SDK, configura o modo de ligação durante a construção do `DocumentClient` caso utilizando o `ConnectionPolicy` parâmetro. Se utilizar o modo direto, também pode definir o `Protocol` através do `ConnectionPolicy` parâmetro.

```csharp
Uri serviceEndpoint = new Uri("https://contoso.documents.net");
string authKey = "your authKey from the Azure portal";
DocumentClient client = new DocumentClient(serviceEndpoint, authKey,
new ConnectionPolicy
{
   ConnectionMode = ConnectionMode.Direct, // ConnectionMode.Gateway is the default
   ConnectionProtocol = Protocol.Tcp
});
```

Como o TCP é suportado apenas em modo direto, se utilizar o modo gateway, o protocolo HTTPS é sempre utilizado para comunicar com o gateway e o `Protocol` valor dentro `ConnectionPolicy` é ignorado.

:::image type="content" source="./media/performance-tips/connection-policy.png" alt-text="A política de conexão DB Azure Cosmos" border="false":::

**Exaustão de portas efémeras**

Se vir um elevado volume de ligação ou um elevado uso da porta nas suas instâncias, verifique primeiro se as instâncias do seu cliente são singletons. Por outras palavras, as instâncias do cliente devem ser únicas para o tempo de vida da aplicação.

Ao executar o protocolo TCP, o cliente otimiza para a latência utilizando as ligações de longa duração em oposição ao protocolo HTTPS, que termina as ligações após 2 minutos de inatividade.

Em cenários em que tenha acesso escasso e se notar uma contagem de ligação mais alta quando comparado com o acesso ao modo gateway, pode:

* Configure a propriedade [ConnectionPolicy.PortReuseMode](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.portreusemode) para `PrivatePortPool` (eficaz com a versão-quadro>= versão 4.6.1 e .NET core >= 2.0): Esta propriedade permite ao SDK utilizar uma pequena piscina de portas efémeras para diferentes pontos finais de destino Azure Cosmos DB.
* Configure a propriedade [ConnectionPolicy.IdleConnectionTimeout](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.idletcpconnectiontimeout) deve ser maior ou igual a 10 minutos. Os valores recomendados são entre 20 minutos e 24 horas.

**Ligue para o OpenAsync para evitar a latência do arranque no primeiro pedido**

Por padrão, o primeiro pedido tem maior latência porque precisa de ir buscar a tabela de encaminhamento de endereços. Quando utilizar [o SDK V2,](sql-api-sdk-dotnet.md)ligue `OpenAsync()` uma vez durante a inicialização para evitar esta latência de arranque no primeiro pedido. A chamada parece: `await client.OpenAsync();`

> [!NOTE]
> `OpenAsync` gerará pedidos para obter a tabela de encaminhamento de endereços para todos os contentores da conta. Para contas que têm muitos contentores mas cuja aplicação acede a um subconjunto deles, `OpenAsync` geraria uma quantidade desnecessária de tráfego, o que tornaria a inicialização lenta. Assim, usar `OpenAsync` pode não ser útil neste cenário porque atrasa o arranque da aplicação.

**Para o desempenho, colocate clientes na mesma região de Azure**

Quando possível, coloque quaisquer aplicações que liguem para Azure Cosmos DB na mesma região que a base de dados DB Azure Cosmos. Aqui está uma comparação aproximada: chamadas para Azure Cosmos DB dentro da mesma região completam dentro de 1 000 a 2 ms, mas a latência entre a costa oeste e leste dos EUA é de mais de 50 ms. Esta latência pode variar de pedido a pedido, dependendo do percurso feito pelo pedido à medida que passa do cliente para o limite do datacenter Azure. Você pode obter a latência mais baixa possível, garantindo que o pedido de chamada está localizado dentro da mesma região de Azure que o ponto final Azure Cosmos DB. Para obter uma lista das regiões disponíveis, consulte as [regiões de Azure.](https://azure.microsoft.com/regions/#services)

:::image type="content" source="./media/performance-tips/same-region.png" alt-text="A política de conexão DB Azure Cosmos" border="false":::

**Aumentar o número de fios/tarefas**
<a id="increase-threads"></a>

Como as chamadas para Azure Cosmos DB são feitas pela rede, poderá ser necessário variar o grau de paralelismo dos seus pedidos para que a aplicação do cliente passe o mínimo de tempo à espera entre os pedidos. Por exemplo, se estiver a utilizar a Biblioteca Paralela .NET [Task,](https://msdn.microsoft.com//library/dd460717.aspx)crie na ordem de centenas de tarefas que leiam ou escrevam para Azure Cosmos DB.

**Permitir networking acelerado**
 
Para reduzir a latência e o nervosismo do CPU, recomendamos que permita uma ligação acelerada em rede em máquinas virtuais do cliente. Ver [Criar uma máquina virtual do Windows com rede acelerada](../virtual-network/create-vm-accelerated-networking-powershell.md) ou criar uma máquina virtual [Linux com rede acelerada](../virtual-network/create-vm-accelerated-networking-cli.md).

## <a name="sdk-usage"></a>Utilização do SDK

**Instale o SDK mais recente**

Os Azure Cosmos DB SDKs estão constantemente a ser melhorados para proporcionar o melhor desempenho. Consulte as páginas [DB SDK do Azure Cosmos](sql-api-sdk-dotnet-standard.md) para determinar as mais recentes melhorias da SDK e rever as melhorias.

**Use um cliente singleton Azure Cosmos DB para a vida da sua aplicação**

Cada `DocumentClient` instância é segura e executa uma gestão eficiente da ligação e cache de endereços ao operar em modo direto. Para permitir uma gestão eficiente da ligação e um melhor desempenho do cliente SDK, recomendamos que utilize um único exemplo por `AppDomain` dia durante o tempo de vida da aplicação.

**Aumente System.Net MaxConnections por anfitrião ao utilizar o modo gateway**

Os pedidos de DB da Azure Cosmos são feitos sobre HTTPS/REST quando utiliza o modo gateway. Estão sujeitos ao limite de ligação predefinido por nome de hospedeiro ou endereço IP. Você pode precisar de definir `MaxConnections` para um valor mais alto (100 a 1.000) para que a biblioteca do cliente possa usar múltiplas ligações simultâneas ao Azure Cosmos DB. Em .NET SDK 1.8.0 e posteriormente, o valor predefinido para [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx) é de 50. Para alterar o valor, pode definir [Documentos.Cliente.ConnectionPolicy.MaxConnectionLimit](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit.aspx) para um valor mais elevado.

**Afinar consultas paralelas para coleções divididas**

SQL .NET SDK 1.9.0 e posterior suporte consultas paralelas, que permitem consultar uma coleção dividida em paralelo. Para obter mais informações, consulte [amostras de código relacionadas](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs) com o trabalho com os SDKs. Consultas paralelas são projetadas para proporcionar uma melhor consulta de latência e produção do que a sua contraparte em série. Consultas paralelas fornecem dois parâmetros que pode sintonizar para se adaptar às suas necessidades: 
- `MaxDegreeOfParallelism` controla o número máximo de divisórias que podem ser consultadas em paralelo. 
- `MaxBufferedItemCount` controla o número de resultados pré-recáveis.

***Grau de sintonização do paralelismo***

A consulta paralela funciona consultando várias divisórias em paralelo. Mas os dados de uma partição individual são recolhidos em série no que diz respeito à consulta. A fixação `MaxDegreeOfParallelism` em [SDK V2](sql-api-sdk-dotnet.md) para o número de divisórias tem a melhor hipótese de alcançar a consulta mais performante, desde que todas as outras condições do sistema permaneçam as mesmas. Se não souber o número de divisórias, pode definir o grau de paralelismo para um número elevado. O sistema escolherá o mínimo (número de divisórias, entrada fornecida pelo utilizador) como o grau de paralelismo.

Consultas paralelas são as que mais beneficiam se os dados forem distribuídos uniformemente em todas as divisórias no que diz respeito à consulta. Se a recolha dividida for dividida de modo a que todos ou a maioria dos dados devolvidos por uma consulta se concentrem em algumas divisórias (uma partição é o pior caso), essas divisórias irão engarrafar o desempenho da consulta.

***Afinação MaxBufferedItemCount***
    
A consulta paralela é projetada para pré-obter resultados enquanto o lote atual de resultados está sendo processado pelo cliente. Esta pré-busca ajuda a melhorar a latência geral de uma consulta. O `MaxBufferedItemCount` parâmetro limita o número de resultados pré-recedidos. `MaxBufferedItemCount`Desagreda ao número esperado de resultados devolvidos (ou um número mais elevado) para permitir que a consulta receba o máximo benefício da pré-obtenção.

A pré-busca funciona da mesma forma, independentemente do grau de paralelismo, e há um único tampão para os dados de todas as divisórias.  

**Implementar backoff em Intervalos RetryAfter**

Durante os testes de desempenho, deverá aumentar a carga até que uma pequena taxa de pedidos seja acelerada. Se os pedidos forem acelerados, a aplicação do cliente deve recuar no acelerador para o intervalo de retagem especificado pelo servidor. Respeitar o recuo garante que passa o mínimo de tempo à espera entre as retrações. 

O apoio à política de retenção está incluído nestes SDKs:
- Versão 1.8.0 e posterior do [.NET SDK para SQL](sql-api-sdk-dotnet.md) e o [SDK Java para SQL](sql-api-sdk-java.md)
- Versão 1.9.0 e posterior do [Node.js SDK para SQL](sql-api-sdk-node.md) e o [Python SDK para SQL](sql-api-sdk-python.md)
- Todas as versões suportadas dos [.NET Core](sql-api-sdk-dotnet-core.md) SDKs 

Para obter mais informações, consulte [RetryAfter](https://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx).
    
Na versão 1.19 e posterior do .NET SDK, existe um mecanismo para registar informações adicionais de diagnóstico e problemas de latência de resolução de problemas, como mostra a amostra seguinte. Pode registar a cadeia de diagnóstico para pedidos com uma latência mais lida. A cadeia de diagnóstico capturada irá ajudá-lo a entender quantas vezes recebeu 429 erros para um determinado pedido.

```csharp
ResourceResponse<Document> readDocument = await this.readClient.ReadDocumentAsync(oldDocuments[i].SelfLink);
readDocument.RequestDiagnosticsString 
```

**UrIs de documento de cache para menor leitura de latência**

Cache document URIs sempre que possível para o melhor desempenho de leitura. É necessário definir lógica para cache o ID do recurso quando cria um recurso. As análises baseadas em IDs de recursos são mais rápidas do que as procuras baseadas em nomes, por isso caching estes valores melhora o desempenho.

**Sintonize o tamanho da página para consultas/feeds de leitura para um melhor desempenho**

Quando se faz uma leitura a granel de documentos utilizando a funcionalidade de feed de leitura (por exemplo, `ReadDocumentFeedAsync` ) ou quando emite uma consulta SQL, os resultados são devolvidos de forma segmentada se o conjunto de resultados for demasiado grande. Por predefinição, os resultados são devolvidos em pedaços de 100 itens ou 1 MB, qualquer que seja o limite atingido primeiro.

Para reduzir o número de viagens redondas de rede necessárias para obter todos os resultados aplicáveis, pode aumentar o tamanho da página utilizando [a contagem de x-ms-max-item](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) para solicitar até 1.000 cabeçalhos. Quando precisa de apresentar apenas alguns resultados, por exemplo, se a interface do utilizador ou aplicação API retorna apenas 10 resultados de cada vez, também pode diminuir o tamanho da página para 10 para reduzir a produção consumida para leituras e consultas.

> [!NOTE] 
> A `maxItemCount` propriedade não deve ser usada apenas para paginação. O seu principal uso é melhorar o desempenho das consultas reduzindo o número máximo de itens devolvidos numa única página.  

Também pode definir o tamanho da página usando os SDKs DB DB do Azure Cosmos disponíveis. A propriedade [MaxItemCount](/dotnet/api/microsoft.azure.documents.client.feedoptions.maxitemcount?view=azure-dotnet&preserve-view=true) `FeedOptions` permite definir o número máximo de itens a serem devolvidos na operação de enumeração. Quando `maxItemCount` está definido para -1, o SDK encontra automaticamente o valor ideal, dependendo do tamanho do documento. Por exemplo:
    
```csharp
IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });
```
    
Quando uma consulta é executada, os dados resultantes são enviados dentro de um pacote TCP. Se especificar um valor demasiado baixo para `maxItemCount` , o número de viagens necessárias para enviar os dados dentro do pacote TCP é elevado, o que afeta o desempenho. Portanto, se não tem certeza de que valor definir para a `maxItemCount` propriedade, o melhor é defini-lo para -1 e deixar o SDK escolher o valor padrão.

**Aumentar o número de fios/tarefas**

Ver [Aumentar o número de linhas/tarefas](#increase-threads) na secção de networking deste artigo.

## <a name="indexing-policy"></a>Política de indexação
 
**Excluir os caminhos não utilizados da indexação para assegurar escritas mais rápidas**

A política de indexação DB do Azure Cosmos também permite especificar quais os caminhos documentais a incluir ou excluir da indexação utilizando caminhos de indexação (IndexingPolicy.IncludedPaths e IndexingPolicy.ExcludeedPaths). Os caminhos de indexação podem melhorar o desempenho da escrita e reduzir o armazenamento de índices para cenários em que os padrões de consulta são conhecidos previamente. Isto porque os custos de indexação estão correlacionados diretamente com o número de caminhos únicos indexados. Por exemplo, este código mostra como excluir uma secção inteira dos documentos (uma subtree) de indexação utilizando o wildcard "*":

```csharp
var collection = new DocumentCollection { Id = "excludedPathCollection" };
collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), collection);
```

Para obter mais informações, consulte [as políticas de indexação de DB do Azure Cosmos](index-policy.md).

## <a name="throughput"></a><a id="measure-rus"></a> Produção

**Meça e sintonize para unidades de pedido mais baixas/segunda utilização**

A Azure Cosmos DB oferece um rico conjunto de operações de base de dados. Estas operações incluem consultas relacionais e hierárquicas com UDFs, procedimentos armazenados e gatilhos, todos operando nos documentos dentro de uma recolha de base de dados. O custo associado a cada uma destas operações varia consoante o CPU, IO e memória necessários para completar a operação. Em vez de pensar e gerir recursos de hardware, pode pensar numa Unidade de Pedido (RU) como uma única medida para os recursos necessários para realizar várias operações de base de dados e servir um pedido de aplicação.

A produção é proscedida com base no número de Unidades de [Pedido definidas](request-units.md) para cada contentor. Pedido O consumo unitário é avaliado como uma taxa por segundo. Os pedidos que excedam a taxa de unidade de pedido prevista para o seu contentor são limitados até que a taxa baixe abaixo do nível previsto para o contentor. Se a sua aplicação necessitar de um nível de produção mais elevado, pode aumentar a sua produção fornecendo Unidades de Pedido adicionais.

A complexidade de uma consulta afeta quantas Unidades de Pedido são consumidas para uma operação. O número de predicados, a natureza dos predicados, o número de UDFs e o tamanho do conjunto de dados de origem influenciam o custo das operações de consulta.

Para medir a sobrecarga de qualquer operação (criar, atualizar ou apagar), inspecione o cabeçalho [x-ms-request-charge](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) (ou a propriedade equivalente `RequestCharge` dentro ou no `ResourceResponse\<T>` `FeedResponse\<T>` .NET SDK) para medir o número de Unidades de Pedido consumidas pelas operações:

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

A taxa de pedido devolvida neste cabeçalho é uma fração da sua produção proviscionada (ou seja, 2.000 RUs/segundo). Por exemplo, se a consulta anterior devolver 1.000 documentos 1-KB, o custo da operação é de 1.000. Então, dentro de um segundo, o servidor honra apenas dois desses pedidos antes de taxa limitando pedidos posteriores. Para obter mais informações, consulte [Unidades de Pedido](request-units.md) e a [calculadora da Unidade de Pedido.](https://www.documentdb.com/capacityplanner)
<a id="429"></a>

**Taxa de maneneta limitando/taxa de pedido demasiado grande**

Quando um cliente tenta exceder a produção reservada para uma conta, não há degradação de desempenho no servidor e não há uso da capacidade de produção para além do nível reservado. O servidor terminará preventivamente o pedido com o RequestRateTooLarge (código de estado HTTP 429). Retornará um cabeçalho [x-ms-retry-after-ms](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) que indica o tempo, em milissegundos, que o utilizador deve esperar antes de tentar novamente o pedido.

```http
HTTP Status 429,
Status Line: RequestRateTooLarge
x-ms-retry-after-ms :100
```

Os SDKs capturam implicitamente esta resposta, respeitam o cabeçalho especificado pelo servidor e recaem o pedido. A menos que a sua conta esteja a ser acedida simultaneamente por vários clientes, a próxima repetição será bem sucedida.

Se tiver mais de um cliente a operar de forma cumulativa acima da taxa de pedido, a contagem de retíria por defeito atualmente definida para 9 internamente pelo cliente pode não ser suficiente. Neste caso, o cliente lança um DocumentClientException com o código de estado 429 para a aplicação. 

Pode alterar a contagem de repetição por defeito definindo `RetryOptions` a no `ConnectionPolicy` caso. Por predefinição, o DocumentClientException com o código de estado 429 é devolvido após um tempo de espera acumulado de 30 segundos se o pedido continuar a funcionar acima da taxa de pedido. Este erro retorna mesmo quando a contagem de repetição atual é inferior à contagem máxima de repetição, quer o valor atual seja o padrão de 9 ou um valor definido pelo utilizador.

O comportamento de relembolso automatizado ajuda a melhorar a resiliência e a usabilidade para a maioria das aplicações. Mas pode não ser o melhor comportamento quando se está a fazer referências de desempenho, especialmente quando se está a medir a latência. A latência observada pelo cliente aumentará se a experiência atingir o acelerador do servidor e fizer com que o cliente SDK volte a tentar silenciosamente. Para evitar picos de latência durante as experiências de desempenho, meça a carga devolvida por cada operação e certifique-se de que os pedidos estão a funcionar abaixo da taxa de pedido reservada. Para mais informações, consulte [Unidades de Pedido.](request-units.md)

**Para uma maior produção, design para documentos menores**

A taxa de pedido (isto é, o custo de processamento de pedido) de uma determinada operação está diretamente relacionada com a dimensão do documento. As operações em grandes documentos custam mais do que operações em pequenos documentos.

## <a name="next-steps"></a>Passos seguintes

Para uma aplicação de amostra que é usada para avaliar Azure Cosmos DB para cenários de alto desempenho em algumas máquinas de clientes, consulte testes de [desempenho e escala com Azure Cosmos DB](performance-testing.md).

Para saber mais sobre a conceção da sua aplicação para escala e alto desempenho, consulte [Partition e dimensionamento em Azure Cosmos DB](partition-data.md).
