---
title: C# tutorial otimizar indexante com a API push
titleSuffix: Azure Cognitive Search
description: Aprenda a indexar eficientemente os dados usando a API push API da Azure Cognitive Search. Este tutorial e o código de amostra estão em C#.
manager: luisca
author: dereklegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 1/29/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: f97a99bf2d055805ee665ab51aff8cff12dc5a69
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99094197"
---
# <a name="tutorial-optimize-indexing-with-the-push-api"></a>Tutorial: Otimizar a indexação com a API push

A Azure Cognitive Search suporta [duas abordagens básicas](search-what-is-data-import.md) para importar dados para um índice de pesquisa: *empurrar* os seus dados para o índice programáticamente, ou apontar um [indexante de Pesquisa Cognitiva Azure](search-indexer-overview.md) numa fonte de dados suportada para *obter* os dados.

Este tutorial descreve como indexar eficientemente os dados usando o [modelo push](search-what-is-data-import.md#pushing-data-to-an-index) através de pedidos de loteamento e usando uma estratégia de recuo exponencial. Você pode [baixar e executar a aplicação de amostra](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing). Este artigo explica os aspectos-chave da aplicação e os fatores a ter em conta na indexação dos dados.

Este tutorial utiliza C# e o [.NET SDK](/dotnet/api/overview/azure/search) para executar as seguintes tarefas:

> [!div class="checklist"]
> * Criar um índice
> * Teste vários tamanhos de lote para determinar o tamanho mais eficiente
> * Lotes de índice assíncronosamente
> * Use vários fios para aumentar as velocidades de indexação
> * Use uma estratégia de recuo exponencial para revassar documentos falhados

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

São necessários os seguintes serviços e ferramentas para este tutorial.

+ [Estúdio Visual,](https://visualstudio.microsoft.com/downloads/)qualquer edição. O código de amostra e as instruções foram testados na edição comunitária gratuita.

+ [Crie um serviço de Pesquisa Cognitiva Azure](search-create-service-portal.md) ou [encontre um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) sob a sua subscrição atual.

<a name="get-service-info"></a>

## <a name="download-files"></a>Transferir ficheiros

O código-fonte para este tutorial encontra-se na pasta [optimzize-data indexing/v11](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing/v11) no repositório [Azure-Samples/azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) GitHub.

## <a name="key-considerations"></a>Considerações principais

Ao empurrar dados para um índice, há várias considerações fundamentais que impactam a velocidade de indexação. Pode saber mais sobre estes fatores no [índice de grandes conjuntos de dados artigo](search-howto-large-index.md).

Seis factores-chave a ter em conta são:

+ **Nível de serviço e número de divisórias/réplicas** - Adicionar divisórias e aumentar o seu nível aumentará as velocidades de indexação.
+ **Esquema de Índice** - Adicionar campos e adicionar propriedades adicionais aos campos (como *pescável,* *facetável* ou *filtrado*) reduzem as velocidades de indexação.
+ **Tamanho do lote** - O tamanho ideal do lote varia em função do seu esquema de índice e do conjunto de dados.
+ **Número de fios/trabalhadores** - um único fio não tirará o máximo partido das velocidades de indexação
+ **Estratégia de retíria** - Uma estratégia de retrocesso exponencial deve ser usada para otimizar a indexação.
+ **Velocidades de transferência de dados de rede** - As velocidades de transferência de dados podem ser um fator limitativo. Dados de índice dentro do seu ambiente Azure para aumentar as velocidades de transferência de dados.


## <a name="1---create-azure-cognitive-search-service"></a>1 - Criar serviço de Pesquisa Cognitiva Azure

Para completar este tutorial, você precisará de um serviço de Pesquisa Cognitiva Azure, que pode [criar no portal.](search-create-service-portal.md) Recomendamos a utilização do mesmo nível que pretende utilizar na produção para que possa testar e otimizar com precisão as velocidades de indexação.

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Obtenha uma api-chave de administrador e URL para pesquisa cognitiva Azure

As chamadas da API requerem o URL de serviço e uma chave de acesso. Um serviço de pesquisa é criado com ambos, por isso, se adicionar Azure Cognitive Search à sua subscrição, siga estes passos para obter as informações necessárias:

1. [Inscreva-se no portal Azure,](https://portal.azure.com/)e na página **geral do** seu serviço de pesquisa, obtenha o URL. Um ponto final de exemplo poderá ser parecido com `https://mydemo.search.windows.net`.

1. Em **Definições**  >  **Teclas,** obtenha uma chave de administração para todos os direitos sobre o serviço. Existem duas chaves de administração intercambiáveis, previstas para a continuidade do negócio, caso precise de rolar uma. Pode utilizar a tecla primária ou secundária nos pedidos de adição, modificação e eliminação de objetos.

   ![Obtenha uma chave de acesso http e acesso](media/search-get-started-rest/get-url-key.png "Obtenha uma chave de acesso http e acesso")

## <a name="2---set-up-your-environment"></a>2 - Configurar o seu ambiente

1. Inicie o Visual Studio e abra **o OptimizeDataIndexing.sln**.
1. No Solution Explorer, abra **appsettings.js** para fornecer informações de ligação.

```json
{
  "SearchServiceUri": "https://{service-name}.search.windows.net",
  "SearchServiceAdminApiKey": "",
  "SearchIndexName": "optimize-indexing"
}
```

## <a name="3---explore-the-code"></a>3 - Explorar o código

Uma vez atualizado *appsettings.jsligado,* o programa de amostras no **OptimizeDataIndexing.sln** deve estar pronto para construir e executar.

Este código é derivado do [C# Quickstart](search-get-started-dotnet.md). Pode encontrar informações mais detalhadas sobre os fundamentos de trabalhar com o .NET SDK nesse artigo.

Esta simples aplicação de consola C#/.NET executa as seguintes tarefas:

+ Cria um novo índice baseado na estrutura de dados da classe C# Hotel (que também faz referência à classe Address).
+ Testa vários tamanhos de lote para determinar o tamanho mais eficiente
+ Índices dados assíncronos
    + Usando vários fios para aumentar as velocidades de indexação
    + Usando uma estratégia de recuo exponencial para re-julgar itens falhados

 Antes de executar o programa, desem um minuto para estudar o código e as definições de índice para esta amostra. O código relevante está em vários ficheiros:

  + **Hotel.cs** e **Endereço.cs** contém o esquema que define o índice
  + **DataGenerator.cs** contém uma classe simples para facilitar a criação de grandes quantidades de dados do hotel
  + **ExponencialBackoff.cs** contém código para otimizar o processo de indexação como descrito abaixo
  + **Programa.cs** contém funções que criam e eliminam o índice de Pesquisa Cognitiva Azure, indexa lotes de dados e testa diferentes tamanhos de lote

### <a name="creating-the-index"></a>Criar o índice

Este programa de amostragem utiliza o .NET SDK para definir e criar um índice de Pesquisa Cognitiva Azure. Aproveita a `FieldBuilder` classe para gerar uma estrutura de índice a partir de uma classe de modelo de dados C#.

O modelo de dados é definido pela classe Hotel, que também contém referências à classe Address. O FieldBuilder perfura através de múltiplas definições de classe para gerar uma estrutura de dados complexa para o índice. As tags de metadados são usadas para definir os atributos de cada campo, tais como se é pesmável ou ordenado.

Os seguintes excertos do ficheiro **.cs Hotel** mostram como um único campo, e uma referência a outra classe de modelo de dados, podem ser especificados.

```csharp
. . .
[SearchableField(IsSortable = true)]
public string HotelName { get; set; }
. . .
public Address Address { get; set; }
. . .
```

No ficheiro **.cs Programa,** o índice é definido com um nome e uma coleção de campo gerada pelo `FieldBuilder.Build(typeof(Hotel))` método, e depois criado da seguinte forma:

```csharp
private static async Task CreateIndexAsync(string indexName, SearchIndexClient indexClient)
{
    // Create a new search index structure that matches the properties of the Hotel class.
    // The Address class is referenced from the Hotel class. The FieldBuilder
    // will enumerate these to create a complex data structure for the index.
    FieldBuilder builder = new FieldBuilder();
    var definition = new SearchIndex(indexName, builder.Build(typeof(Hotel)));

    await indexClient.CreateIndexAsync(definition);
}
```

### <a name="generating-data"></a>Geração de dados

Uma classe simples é implementada no ficheiro **DataGenerator.cs** para gerar dados para testes. O único objetivo desta classe é facilitar a geração de um grande número de documentos com um ID único para indexação.

Para obter uma lista de 100.000 hotéis com IDs únicos, você executaria as seguintes linhas de código:

```csharp
long numDocuments = 100000;
DataGenerator dg = new DataGenerator();
List<Hotel> hotels = dg.GetHotels(numDocuments, "large");
```

Existem dois tamanhos de hotéis disponíveis para testes nesta amostra: **pequeno** e **grande.**

O esquema do seu índice pode ter um impacto significativo nas velocidades de indexação. Devido a este impacto, faz sentido converter esta classe para gerar dados correspondentes ao seu esquema de índice pretendido depois de passar por este tutorial.

## <a name="4---test-batch-sizes"></a>4 - Tamanhos do lote de teste

A Azure Cognitive Search suporta as seguintes APIs para carregar documentos únicos ou múltiplos num índice:

+ [Adicionar, Atualizar ou Eliminar Documentos (API REST)](/rest/api/searchservice/AddUpdate-or-Delete-Documents)
+ [Classe IndexDocumentsA](/dotnet/api/azure.search.documents.models.indexdocumentsaction) ou [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch)

A indexação de documentos em lotes melhorará significativamente o desempenho da indexação. Estes lotes podem ser até 1000 documentos, ou até cerca de 16 MB por lote.

Determinar o tamanho ideal do lote para os seus dados é um componente chave para otimizar as velocidades de indexação. Os dois fatores primários que influenciam o tamanho ideal do lote são:

+ O esquema do seu índice
+ O tamanho dos seus dados

Como o tamanho ideal do lote depende do seu índice e dos seus dados, a melhor abordagem é testar diferentes tamanhos de lote para determinar quais os resultados nas velocidades de indexação mais rápidas para o seu cenário.

A seguinte função demonstra uma abordagem simples para testar tamanhos de lote.

```csharp
public static async Task TestBatchSizesAsync(SearchClient searchClient, int min = 100, int max = 1000, int step = 100, int numTries = 3)
{
    DataGenerator dg = new DataGenerator();

    Console.WriteLine("Batch Size \t Size in MB \t MB / Doc \t Time (ms) \t MB / Second");
    for (int numDocs = min; numDocs <= max; numDocs += step)
    {
        List<TimeSpan> durations = new List<TimeSpan>();
        double sizeInMb = 0.0;
        for (int x = 0; x < numTries; x++)
        {
            List<Hotel> hotels = dg.GetHotels(numDocs, "large");

            DateTime startTime = DateTime.Now;
            await UploadDocumentsAsync(searchClient, hotels).ConfigureAwait(false);
            DateTime endTime = DateTime.Now;
            durations.Add(endTime - startTime);

            sizeInMb = EstimateObjectSize(hotels);
        }

        var avgDuration = durations.Average(timeSpan => timeSpan.TotalMilliseconds);
        var avgDurationInSeconds = avgDuration / 1000;
        var mbPerSecond = sizeInMb / avgDurationInSeconds;

        Console.WriteLine("{0} \t\t {1} \t\t {2} \t\t {3} \t {4}", numDocs, Math.Round(sizeInMb, 3), Math.Round(sizeInMb / numDocs, 3), Math.Round(avgDuration, 3), Math.Round(mbPerSecond, 3));

        // Pausing 2 seconds to let the search service catch its breath
        Thread.Sleep(2000);
    }

    Console.WriteLine();
}
```

Como nem todos os documentos têm o mesmo tamanho (embora estejam nesta amostra), estimamos o tamanho dos dados que estamos a enviar para o serviço de pesquisa. Fazemos isto usando a função abaixo que primeiro converte o objeto em json e, em seguida, determina o seu tamanho em bytes. Esta técnica permite-nos determinar quais os tamanhos dos lotes mais eficientes em termos de velocidades de indexação MB/s.

```csharp
// Returns size of object in MB
public static double EstimateObjectSize(object data)
{
    // converting object to byte[] to determine the size of the data
    BinaryFormatter bf = new BinaryFormatter();
    MemoryStream ms = new MemoryStream();
    byte[] Array;

    // converting data to json for more accurate sizing
    var json = JsonSerializer.Serialize(data);
    bf.Serialize(ms, json);
    Array = ms.ToArray();

    // converting from bytes to megabytes
    double sizeInMb = (double)Array.Length / 1000000;

    return sizeInMb;
}
```

A função requer um `SearchClient` bem como o número de tentativas que gostaria de testar para cada tamanho do lote. Como pode haver alguma variabilidade nos tempos de indexação para cada lote, tentamos cada lote três vezes por padrão para tornar os resultados mais significativos estatisticamente.

```csharp
await TestBatchSizesAsync(searchClient, numTries: 3);
```

Quando executar a função, deve ver uma saída como abaixo na sua consola:

   ![Saída da função de tamanho do lote de teste](media/tutorial-optimize-data-indexing/test-batch-sizes.png "Saída da função de tamanho do lote de teste")

Identifique qual o tamanho do lote mais eficiente e, em seguida, use o tamanho do lote no próximo passo do tutorial. Você pode ver um planalto em MB/s em diferentes tamanhos de lote.

## <a name="5---index-data"></a>5 - Dados do índice

Agora que identificamos o tamanho do lote que pretendemos usar, o próximo passo é começar a indexar os dados. Para indexar os dados de forma eficiente, esta amostra:

+ Utiliza vários fios/trabalhadores.
+ Implementa uma estratégia de retrocesso exponencial.

### <a name="use-multiple-threadsworkers"></a>Use vários fios/trabalhadores

Para tirar o máximo partido das velocidades de indexação da Azure Cognitive Search, provavelmente terá de usar vários fios para enviar pedidos de indexação de lotes simultaneamente ao serviço.  

Várias das principais considerações acima mencionadas afetam o número ideal de fios. Pode modificar esta amostra e testar com diferentes contagens de fios para determinar a contagem ideal de roscas para o seu cenário. No entanto, desde que tenha vários fios a funcionar simultaneamente, deverá poder tirar partido da maioria dos ganhos de eficiência.

À medida que aumenta os pedidos que atingem o serviço de pesquisa, poderá encontrar [códigos de estado HTTP](/rest/api/searchservice/http-status-codes) que indiquem que o pedido não foi totalmente bem sucedido. Durante a indexação, dois códigos de estado HTTP comuns são:

+ **503 Serviço Indisponível** - Este erro significa que o sistema está sob carga pesada e o seu pedido não pode ser processado neste momento.
+ **207 Multi-Status** - Este erro significa que alguns documentos foram bem sucedidos, mas pelo menos um falhou.

### <a name="implement-an-exponential-backoff-retry-strategy"></a>Implementar uma estratégia de retrocesso exponencial

Se uma falha acontecer, os pedidos devem ser novamente julgados utilizando uma [estratégia de retrocesso exponencial](/dotnet/architecture/microservices/implement-resilient-applications/implement-retries-exponential-backoff).

Azure Cognitive Search.NET SDK retrifica automaticamente 503s e outros pedidos falhados, mas terá de implementar a sua própria lógica para voltar a tentar 207s. Ferramentas de código aberto como [Polly](https://github.com/App-vNext/Polly) também podem ser usadas para implementar uma estratégia de relíndi.

Nesta amostra, implementamos a nossa própria estratégia de retrocesso exponencial. Para implementar esta estratégia, começamos por definir algumas variáveis, incluindo a `maxRetryAttempts` e a inicial para um pedido `delay` falhado:

```csharp
// Create batch of documents for indexing
var batch = IndexDocumentsBatch.Upload(hotels);

// Create an object to hold the result
IndexDocumentsResult result = null;

// Define parameters for exponential backoff
int attempts = 0;
TimeSpan delay = delay = TimeSpan.FromSeconds(2);
int maxRetryAttempts = 5;
```

Os resultados da operação de indexação são armazenados na variável `IndexDocumentResult result` . Esta variável é importante porque permite verificar se algum documento no lote falhou como mostrado abaixo. Se houver uma falha parcial, um novo lote é criado com base no ID dos documentos falhados.

`RequestFailedException` as exceções também devem ser apanhadas, uma vez que indicam que o pedido falhou completamente e devem também ser novamente julgados.

```csharp
// Implement exponential backoff
do
{
    try
    {
        attempts++;
        result = await searchClient.IndexDocumentsAsync(batch).ConfigureAwait(false);

        var failedDocuments = result.Results.Where(r => r.Succeeded != true).ToList();

        // handle partial failure
        if (failedDocuments.Count > 0)
        {
            if (attempts == maxRetryAttempts)
            {
                Console.WriteLine("[MAX RETRIES HIT] - Giving up on the batch starting at {0}", id);
                break;
            }
            else
            {
                Console.WriteLine("[Batch starting at doc {0} had partial failure]", id);
                Console.WriteLine("[Retrying {0} failed documents] \n", failedDocuments.Count);

                // creating a batch of failed documents to retry
                var failedDocumentKeys = failedDocuments.Select(doc => doc.Key).ToList();
                hotels = hotels.Where(h => failedDocumentKeys.Contains(h.HotelId)).ToList();
                batch = IndexDocumentsBatch.Upload(hotels);

                Task.Delay(delay).Wait();
                delay = delay * 2;
                continue;
            }
        }

        return result;
    }
    catch (RequestFailedException ex)
    {
        Console.WriteLine("[Batch starting at doc {0} failed]", id);
        Console.WriteLine("[Retrying entire batch] \n");

        if (attempts == maxRetryAttempts)
        {
            Console.WriteLine("[MAX RETRIES HIT] - Giving up on the batch starting at {0}", id);
            break;
        }

        Task.Delay(delay).Wait();
        delay = delay * 2;
    }
} while (true);
```

A partir daqui, embrulhamos o código exponencial de backoff numa função para que possa ser facilmente chamado.

Outra função é então criada para gerir os fios ativos. Para simplificar, esta função não está incluída aqui, mas pode ser encontrada no [ExponentialBackoff.cs](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/optimize-data-indexing/v11/OptimizeDataIndexing/ExponentialBackoff.cs). A função pode ser chamada com o seguinte comando onde `hotels` estão os dados que queremos carregar, `1000` é o tamanho do lote, e é o número de fios `8` simultâneos:

```csharp
await ExponentialBackoff.IndexData(indexClient, hotels, 1000, 8);
```

Quando executar a função, deve ver uma saída como abaixo:

![Produção da função de dados indexados](media/tutorial-optimize-data-indexing/index-data-start.png "Produção da função de dados indexados")

Quando um lote de documentos falha, é impresso um erro que indica a falha e que o lote está a ser novamente julgado:

```
[Batch starting at doc 6000 had partial failure]
[Retrying 560 failed documents]
```

Depois de terminada a função, pode verificar se todos os documentos foram adicionados ao índice.

## <a name="6---explore-index"></a>6 - Explorar índice

Pode explorar o índice de pesquisa povoado após o programa ter executado programaticamente ou utilizando o [**explorador de Pesquisa**](search-explorer.md) no portal.

### <a name="programatically"></a>Programaticamente

Existem duas opções principais para verificar o número de documentos num índice: a [API dos Documentos de Contagem](/rest/api/searchservice/count-documents) e a [API de Estatísticas do Índice](/rest/api/searchservice/get-index-statistics)de Obter . Ambos os caminhos podem requerer algum tempo adicional para atualizar, por isso não se assuste se o número de documentos devolvidos for inferior ao inicialmente esperado.

#### <a name="count-documents"></a>Documentos de Contagem

A operação Documentos de Contagem recupera uma contagem do número de documentos num índice de pesquisa:

```csharp
long indexDocCount = await searchClient.GetDocumentCountAsync();
```

#### <a name="get-index-statistics"></a>Obter Estatísticas de Índice

A operação Get Index Statistics devolve uma contagem de documentos para o índice atual, mais o uso de armazenamento. As estatísticas de índices demorarão mais tempo do que a contagem de documentos para atualizar.

```csharp
var indexStats = await indexClient.GetIndexStatisticsAsync(indexName);
```

### <a name="azure-portal"></a>Portal do Azure

No portal Azure, abra a página **de visão geral** do serviço de pesquisa e encontre o índice **de indexação otimizado** na lista **de Índices.**

  ![Lista de índices de Pesquisa Cognitiva Azure](media/tutorial-optimize-data-indexing/portal-output.png "Lista de índices de Pesquisa Cognitiva Azure")

O *número de documentos* e *o tamanho do armazenamento* são baseados na [API de Estatísticas de Índice](/rest/api/searchservice/get-index-statistics) e podem demorar vários minutos a atualizar.

## <a name="reset-and-rerun"></a>Repor e executar novamente

Nas fases experimentais iniciais de desenvolvimento, a abordagem mais prática para a iteração de design é eliminar os objetos da Azure Cognitive Search e permitir que o seu código os reconstrua. Os nomes dos recursos são exclusivos. Quando elimina um objeto, pode recriá-lo com o mesmo nome.

O código de amostra deste tutorial verifica os índices existentes e elimina-os para que possa voltar a verificar o seu código.

Também pode utilizar o portal para eliminar índices.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando se está a trabalhar na sua própria subscrição, no final de um projeto, é uma boa ideia remover os recursos de que já não precisa. Os recursos que deixar em execução podem custar-lhe dinheiro. Pode eliminar recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Pode encontrar e gerir recursos no portal, utilizando a ligação **de todos os recursos** ou **grupos** de recursos no painel de navegação à esquerda.

## <a name="next-steps"></a>Passos seguintes

Agora que está familiarizado com o conceito de ingerir dados de forma eficiente, vamos olhar mais de perto a arquitetura da consulta de Lucene e como funciona a pesquisa completa de texto na Azure Cognitive Search.

> [!div class="nextstepaction"]
> [Como funciona a pesquisa em texto completo no Azure Cognitive Search](search-lucene-query-architecture.md)