---
title: C# tutorial otimizar indexação com a API push
titleSuffix: Azure Cognitive Search
description: Aprenda a indexar eficientemente os dados usando a API push da Pesquisa Cognitiva Azure. Este tutorial e código de amostra estão em C#.
manager: liamca
author: dereklegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 05/05/2020
ms.openlocfilehash: 85ac56eb20eabf308d6686a047d8c5ede914fed9
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82966443"
---
# <a name="tutorial-optimize-indexing-with-the-push-api"></a>Tutorial: Otimizar a indexação com a API push

A Azure Cognitive Search suporta [duas abordagens básicas](search-what-is-data-import.md) para importar dados num índice de pesquisa: *empurrar* os seus dados para o índice programáticamente, ou apontar um [indexador](search-indexer-overview.md) de pesquisa cognitiva Azure numa fonte de dados suportada para *puxar* os dados.

Este tutorial descreve como indexar eficientemente os dados usando o [modelo push,](search-what-is-data-import.md#pushing-data-to-an-index) através de pedidos de lotação e usando uma estratégia exponencial de retry backoff. Pode [descarregar e executar a aplicação.](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing) Este artigo explica os aspectos-chave da aplicação e fatores a ter em conta na indexação dos dados.

Este tutorial utiliza C# e o [.NET SDK](https://aka.ms/search-sdk) para executar as seguintes tarefas:

> [!div class="checklist"]
> * Criar um índice
> * Testar vários tamanhos do lote para determinar o tamanho mais eficiente
> * Dados do índice assincronicamente
> * Use vários fios para aumentar as velocidades de indexação
> * Use uma estratégia exponencial de retry backoff para retentar itens falhados

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

São necessários os seguintes serviços e ferramentas para este tutorial.

+ [Estúdio Visual,](https://visualstudio.microsoft.com/downloads/)qualquer edição. O código de amostra e as instruções foram testados na edição comunitária gratuita.

+ [Crie um serviço de Pesquisa Cognitiva Azure](search-create-service-portal.md) ou [encontre um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) sob a sua subscrição atual.

<a name="get-service-info"></a>

## <a name="download-files"></a>Transferir ficheiros

O código fonte deste tutorial encontra-se na pasta de [indexação de dados otimizado](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing) no repositório GitHub de amostras de [azure-amostras de pesquisa-pontotnet.](https://github.com/Azure-Samples/azure-search-dotnet-samples)

## <a name="key-considerations"></a>Considerações principais

Ao empurrar os dados para um índice, há várias considerações-chave que impactam as velocidades de indexação. Pode saber mais sobre estes fatores no artigo de grandes conjuntos de [dados do índice.](search-howto-large-index.md)

Seis factores-chave a ter em conta são:

+ **Nível de serviço e número de divisórias/réplicas** - Adicionar divisórias e aumentar o seu nível aumentará as velocidades de indexação.
+ **Index Schema** - Adicionar campos e adicionar propriedades adicionais a campos (como *pesquisável,* *faceta,* ou *filtrante*) ambos reduzem as velocidades de indexação.
+ **Tamanho** do lote - O tamanho ideal do lote varia em função do seu esquema de índice e conjunto de dados.
+ **Número de fios/trabalhadores** - um único fio não tirará o máximo partido das velocidades de indexação
+ **Estratégia de retry** - Uma estratégia exponencial de retry backoff deve ser usada para otimizar a indexação.
+ **Velocidades** de transferência de dados de rede - Velocidades de transferência de dados podem ser um fator limitativo. Indexe dados de dentro do seu ambiente Azure para aumentar as velocidades de transferência de dados.


## <a name="1---create-azure-cognitive-search-service"></a>1 - Criar serviço de pesquisa cognitiva Azure

Para completar este tutorial, você precisará de um serviço de Pesquisa Cognitiva Azure, que você pode [criar no portal](search-create-service-portal.md). Recomendamos que utilize o mesmo nível que pretende utilizar na produção para que possa testar e otimizar com precisão as velocidades de indexação.

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Obtenha uma chave de api-key e URL para pesquisa cognitiva azure

As chamadas API requerem o URL de serviço e uma chave de acesso. Um serviço de pesquisa é criado com ambos, por isso, se você adicionar Pesquisa Cognitiva Azure à sua subscrição, siga estes passos para obter as informações necessárias:

1. [Inscreva-se no portal Azure](https://portal.azure.com/), e na página de **visão geral** do seu serviço de pesquisa, obtenha o URL. Um ponto final de exemplo poderá ser parecido com `https://mydemo.search.windows.net`.

1. Em **Definições** > **Keys,** obtenha uma chave de administração para todos os direitos sobre o serviço. Existem duas chaves de administração intercambiáveis, previstas para a continuidade do negócio no caso de precisar de rolar uma. Pode utilizar a chave primária ou secundária nos pedidos de adição, modificação e aparas de objetos.

   ![Obtenha um ponto final http e chave de acesso](media/search-get-started-postman/get-url-key.png "Obtenha um ponto final http e chave de acesso")

## <a name="2---set-up-your-environment"></a>2 - Instale o seu ambiente

1. Inicie o Estúdio Visual e abra **optimizeDataIndexing.sln**.
1. No Solution Explorer, abra **as definições.json** para fornecer informações de ligação.
1. Pois, `searchServiceName`se o URLhttps://my-demo-service.search.windows.netcompleto for " ", o nome de serviço a fornecer é "my-demo-service".

```json
{
  "SearchServiceName": "<YOUR-SEARCH-SERVICE-NAME>",
  "SearchServiceAdminApiKey": "<YOUR-ADMIN-API-KEY>",
  "SearchIndexName": "optimize-indexing"
}
```

## <a name="3---explore-the-code"></a>3 - Explore o código

Uma vez atualizado as *definições de appsettings.json*, o programa de amostras em **OptimizeDataIndexing.sln** deve estar pronto para construir e executar.

Este código é derivado do [C# Quickstart](search-get-started-dotnet.md). Pode encontrar informações mais detalhadas sobre o básico de trabalhar com o .NET SDK nesse artigo.

Esta simples aplicação de consola C#/.NET executa as seguintes tarefas:

+ Cria um novo índice com base na estrutura de dados da classe Hotel C# (que também faz referência à classe Address).
+ Testa vários tamanhos de lote para determinar o tamanho mais eficiente
+ Dados de índices assincronicamente
    + Usando vários fios para aumentar as velocidades de indexação
    + Usando uma estratégia exponencial de retry backoff para retentar itens falhados

 Antes de executar o programa, dedique um minuto para estudar o código e as definições de índice para esta amostra. O código relevante está em vários ficheiros:

  + **Hotel.cs** e **Address.cs** contém o esquema que define o índice
  + **DataGenerator.cs** contém uma classe simples para facilitar a criação de grandes quantidades de dados do hotel
  + **ExponentialBackoff.cs** contém código para otimizar o processo de indexação como descrito abaixo
  + **Program.cs** contém funções que criam e eliminam o índice de pesquisa cognitiva Azure, indexam lotes de dados e testam diferentes tamanhos de lote

### <a name="creating-the-index"></a>Criação do índice

Este programa de amostras utiliza o SDK .NET para definir e criar um índice de Pesquisa Cognitiva Azure. Aproveita a classe [FieldBuilder](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.fieldbuilder) para gerar uma estrutura de índice a partir de uma classe de modelo de dados C#.

O modelo de dados é definido pela classe Hotel, que também contém referências à classe Endereço. O FieldBuilder perfura através de várias definições de classe para gerar uma estrutura de dados complexa para o índice. As etiquetas de metadados são usadas para definir os atributos de cada campo, tais como se é pesquisável ou classificativa.

Os seguintes excertos do ficheiro **Hotel.cs** mostram como um único campo, e uma referência a outra classe de modelo de dados, podem ser especificados.

```csharp
. . .
[IsSearchable, IsSortable]
public string HotelName { get; set; }
. . .
public Address Address { get; set; }
. . .
```

No ficheiro **Program.cs,** o índice é definido com um nome `FieldBuilder.BuildForType<Hotel>()` e uma coleção de campo gerada pelo método, e depois criado da seguinte forma:

```csharp
private static async Task CreateIndex(string indexName, SearchServiceClient searchService)
{
    // Create a new search index structure that matches the properties of the Hotel class.
    // The Address class is referenced from the Hotel class. The FieldBuilder
    // will enumerate these to create a complex data structure for the index.
    var definition = new Index()
    {
        Name = indexName,
        Fields = FieldBuilder.BuildForType<Hotel>()
    };
    await searchService.Indexes.CreateAsync(definition);
}
```

### <a name="generating-data"></a>Gerar dados

Uma classe simples é implementada no ficheiro **DataGenerator.cs** para gerar dados para testes. O único propósito desta classe é facilitar a geração de um grande número de documentos com um ID único para indexação.

Para obter uma lista de 100.000 hotéis com iDs únicos, você executaria as seguintes duas linhas de código:

```csharp
DataGenerator dg = new DataGenerator();
List<Hotel> hotels = dg.GetHotels(100000, "large");
```

Existem dois tamanhos de hotéis disponíveis para testes nesta amostra: **pequeno** e **grande.**

O esquema do seu índice pode ter um impacto significativo nas velocidades de indexação. Devido a este impacto, faz sentido converter esta classe para gerar dados correspondentes ao seu esquema de índice pretendido depois de passar por este tutorial.

## <a name="4---test-batch-sizes"></a>4 - Tamanho sinuoso do lote

A Pesquisa Cognitiva Azure suporta as seguintes APIs para carregar documentos únicos ou múltiplos num índice:

+ [Adicionar, Atualizar ou Eliminar Documentos (API REST)](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents)
+ [Classe indexAction](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) ou [classe indexBatch](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet)

A indexação de documentos em lotes melhorará significativamente o desempenho da indexação. Estes lotes podem ter até 1000 documentos, ou até cerca de 16 MB por lote.

Determinar o tamanho ideal do lote para os seus dados é um componente chave para otimizar velocidades de indexação. Os dois fatores primários que influenciam o tamanho ideal do lote são:

+ O esquema do seu índice
+ O tamanho dos seus dados

Como o tamanho ideal do lote depende do seu índice e dos seus dados, a melhor abordagem é testar diferentes tamanhos de lote para determinar o que resulta nas velocidades de indexação mais rápidas para o seu cenário.

A função seguinte demonstra uma abordagem simples para testar o tamanho do lote.

```csharp
public static async Task TestBatchSizes(ISearchIndexClient indexClient, int min = 100, int max = 1000, int step = 100, int numTries = 3)
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
            await UploadDocuments(indexClient, hotels);
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
}
```

Como nem todos os documentos têm o mesmo tamanho (embora estejam nesta amostra), estimamos o tamanho dos dados que estamos a enviar para o serviço de pesquisa. Fazemos isto usando a função abaixo que primeiro converte o objeto em json e, em seguida, determina o seu tamanho em bytes. Esta técnica permite-nos determinar quais os tamanhos do lote mais eficientes em termos de velocidades de indexação MB/s.

```csharp
public static double EstimateObjectSize(object data)
{
    // converting data to json for more accurate sizing
    var json = JsonConvert.SerializeObject(data);

    // converting object to byte[] to determine the size of the data
    BinaryFormatter bf = new BinaryFormatter();
    MemoryStream ms = new MemoryStream();
    byte[] Array;

    bf.Serialize(ms, json);
    Array = ms.ToArray();

    // converting from bytes to megabytes
    double sizeInMb = (double)Array.Length / 1000000;

    return sizeInMb;
}
```

A função `ISearchIndexClient` requer um, bem como o número de tentativas que gostaria de testar para cada tamanho do lote. Como pode haver alguma variabilidade nos tempos de indexação para cada lote, tentamos cada lote três vezes por padrão para tornar os resultados estatisticamente mais significativos.

```csharp
await TestBatchSizes(indexClient, numTries: 3);
```

Quando executar a função, deve ver uma saída como a de baixo na sua consola:

   ![Saída da função de tamanho do lote de ensaio](media/tutorial-optimize-data-indexing/test-batch-sizes.png "Saída da função de tamanho do lote de ensaio")

Identifique qual o tamanho do lote mais eficiente e, em seguida, use o tamanho do lote no próximo passo do tutorial. Pode ver um planalto em MB/s em diferentes tamanhos de lote.

## <a name="5---index-data"></a>5 - Dados do índice

Agora que identificamos o tamanho do lote que pretendemos usar, o próximo passo é começar a indexar os dados. Para indexar os dados de forma eficiente, esta amostra:

* Usa vários fios/trabalhadores.
* Implementa uma estratégia exponencial de retry backoff.

### <a name="use-multiple-threadsworkers"></a>Utilize vários fios/trabalhadores

Para tirar o máximo partido das velocidades de indexação da Azure Cognitive Search, provavelmente terá de usar vários fios para enviar pedidos de indexação de lotes simultaneamente para o serviço.  

Várias das principais considerações acima mencionadas têm impacto no número ideal de fios. Pode modificar esta amostra e testar com diferentes contagens de fio para determinar a contagem ideal de fio para o seu cenário. No entanto, desde que tenha vários fios a funcionar em simultâneo, deverá poder tirar partido da maioria dos ganhos de eficiência.

À medida que aumenta os pedidos que atingem o serviço de pesquisa, poderá encontrar [códigos](https://docs.microsoft.com/rest/api/searchservice/http-status-codes) de estado HTTP indicando que o pedido não teve sucesso. Durante a indexação, dois códigos comuns de estado http são:

+ **503 Serviço Indisponível** - Este erro significa que o sistema está sob carga pesada e o seu pedido não pode ser processado neste momento.
+ **207 Multi-Status** - Este erro significa que alguns documentos foram bem sucedidos, mas pelo menos um falhou.

### <a name="implement-an-exponential-backoff-retry-strategy"></a>Implementar uma estratégia exponencial de retry backoff

Se ocorrer uma falha, os pedidos devem ser novamente julgados utilizando uma estratégia exponencial de [retry backoff](https://docs.microsoft.com/dotnet/architecture/microservices/implement-resilient-applications/implement-retries-exponential-backoff).

O SDK .NET da Azure Cognitive Search retenta automaticamente 503s e outros pedidos falhados, mas terá de implementar a sua própria lógica para voltar a tentar 207s. Ferramentas de código aberto como [a Polly](https://github.com/App-vNext/Polly) também podem ser usadas para implementar uma estratégia de retry. 

Nesta amostra, implementamos a nossa própria estratégia exponencial de retry backoff. Para implementar esta estratégia, começamos por definir `maxRetryAttempts` algumas `delay` variáveis, incluindo a e a inicial para um pedido falhado:

```csharp
// Create batch of documents for indexing
IndexBatch<Hotel> batch = IndexBatch.Upload(hotels);

// Define parameters for exponential backoff
int attempts = 0;
TimeSpan delay = delay = TimeSpan.FromSeconds(2);
int maxRetryAttempts = 5;
```

É importante apanhar [o IndexBatchException,](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexbatchexception?view=azure-dotnet) uma vez que estas exceções indicam que a operação de indexação só foi parcialmente bem sucedida (207s). Os itens falhados devem ser `FindFailedActionsToRetry` novamente experimentados utilizando o método que facilita a criação de um novo lote contendo apenas os itens falhados.

Exceções que `IndexBatchException` não devem também ser apanhadas e indicam que o pedido falhou completamente. Estas exceções são menos comuns, particularmente com o .NET SDK, uma vez que se retenta automaticamente 503s.

```csharp
// Implement exponential backoff
do
{
    try
    {
        attempts++;
        var response = await indexClient.Documents.IndexAsync(batch);
        break;
    }
    catch (IndexBatchException ex)
    {
        Console.WriteLine("[Attempt: {0} of {1} Failed] - Error: {2}", attempts, maxRetryAttempts, ex.Message);

        if (attempts == maxRetryAttempts)
            break;

        // Find the failed items and create a new batch to retry
        batch = ex.FindFailedActionsToRetry(batch, x => x.HotelId);
        Console.WriteLine("Retrying failed documents using exponential backoff...\n");

        Task.Delay(delay).Wait();
        delay = delay * 2;
    }
    catch (Exception ex)
    {
        Console.WriteLine("[Attempt: {0} of {1} Failed] - Error: {2} \n", attempts, maxRetryAttempts, ex.Message);

        if (attempts == maxRetryAttempts)
            break;

        Task.Delay(delay).Wait();
        delay = delay * 2;
    }
} while (true);
```

A partir daqui, embrulhamos o código de backoff exponencial numa função para que possa ser facilmente chamado.

Outra função é então criada para gerir os fios ativos. Para a simplicidade, essa função não está incluída aqui, mas pode ser encontrada em [ExponentialBackoff.cs](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/optimize-data-indexing/OptimizeDataIndexing/ExponentialBackoff.cs). A função pode ser chamada `hotels` com o seguinte comando `1000` onde estão `8` os dados que queremos carregar, é o tamanho do lote, e é o número de fios simultâneos:

```csharp
ExponentialBackoff.IndexData(indexClient, hotels, 1000, 8).Wait();
```

Quando executa a função, deve ver uma saída como a seguir:

![Saída da função de dados de índice](media/tutorial-optimize-data-indexing/index-data-start.png "Saída da função de dados de índice")

Quando um lote de documentos falha, é impresso um erro que indica a falha e que o lote está a ser novamente experimentado:

![Erro da função de dados do índice](media/tutorial-optimize-data-indexing/index-data-error.png "Saída da função de tamanho do lote de ensaio")

Após a função terminar, pode verificar se todos os documentos foram adicionados ao índice.

## <a name="6---explore-index"></a>6 - Explore o índice

Pode explorar o índice de pesquisa preenchido depois de o programa ter sido executado programaticamente ou usando o explorador de [**pesquisa**](search-explorer.md) no portal.

### <a name="programatically"></a>Programaticamente

Existem duas opções principais para verificar o número de documentos num índice: a API dos [Documentos de Contagem](https://docs.microsoft.com/rest/api/searchservice/count-documents) e a API estatísticas de [índices](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics). Ambos os caminhos podem exigir algum tempo adicional para atualizar, por isso não se assuste se o número de documentos devolvidos for inferior ao esperado inicialmente.

#### <a name="count-documents"></a>Documentos de Contagem

A operação Documentos de Contagem recupera uma contagem do número de documentos num índice de pesquisa:

```csharp
long indexDocCount = indexClient.Documents.Count();
```

#### <a name="get-index-statistics"></a>Obter Estatísticas de Índices

A operação Get Index Statistics devolve uma contagem de documentos para o índice atual, mais o uso de armazenamento. As estatísticas do índice demorarão mais tempo do que a contagem de documentos para atualizar.

```csharp
IndexGetStatisticsResult indexStats = serviceClient.Indexes.GetStatistics(configuration["SearchIndexName"]);
```

### <a name="azure-portal"></a>Portal do Azure

No portal Azure, abra a página de visão **geral** do serviço de pesquisa e encontre o índice **de indexação otimizado** na lista de **Índices.**

  ![Lista de índices de pesquisa cognitiva azure](media/tutorial-optimize-data-indexing/portal-output.png "Lista de índices de pesquisa cognitiva azure")

O *tamanho da contagem de documentos* e do *armazenamento* baseia-se na [API](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics) de Estatísticas do Índice e pode demorar vários minutos a atualizar.

## <a name="reset-and-rerun"></a>Repor e executar novamente

Nas fases experimentais iniciais de desenvolvimento, a abordagem mais prática para a iteração do design é apagar os objetos da Pesquisa Cognitiva Azure e permitir que o seu código os reconstrua. Os nomes dos recursos são exclusivos. Quando elimina um objeto, pode recriá-lo com o mesmo nome.

O código de amostra para este tutorial verifica os índices existentes e elimina-os para que possa reexecutar o seu código.

Também pode utilizar o portal para eliminar índices.

## <a name="clean-up-resources"></a>Limpar recursos

Quando se trabalha na sua própria subscrição, no final de um projeto, é uma boa ideia remover os recursos de que já não precisa. Os recursos que deixar em execução podem custar-lhe dinheiro. Pode eliminar recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Pode encontrar e gerir recursos no portal, utilizando a ligação **De Todos os recursos** ou **grupos de Recursos** no painel de navegação à esquerda.

## <a name="next-steps"></a>Passos seguintes

Agora que está familiarizado com o conceito de ingerir dados de forma eficiente, vamos olhar mais de perto para a arquitetura de consulta Lucene e como funciona a pesquisa completa de texto em Azure Cognitive Search.

> [!div class="nextstepaction"]
> [Como funciona a pesquisa em texto completo no Azure Cognitive Search](search-lucene-query-architecture.md)
