---
title: Tutorial C# usando IA em blobs Azure
titleSuffix: Azure Cognitive Search
description: Passe por um exemplo de extração de texto e processamento de linguagem natural sobre o conteúdo no armazenamento blob usando C# e a Azure Cognitive Search .NET SDK.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 08/20/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 09273cf901830e850acca05c57c0b110ffd7e28a
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2020
ms.locfileid: "89002867"
---
# <a name="tutorial-ai-generated-searchable-content-from-azure-blobs-using-the-net-sdk"></a>Tutorial: Conteúdo pesmável gerado pela IA a partir de bolhas Azure usando o .NET SDK

Se tiver texto ou imagens não estruturados no armazenamento de Azure Blob, um [oleoduto de enriquecimento de IA](cognitive-search-concept-intro.md) pode extrair informações e criar novos conteúdos que são úteis para a pesquisa completa de texto ou cenários de mineração de conhecimento. Neste tutorial C#, aplique reconhecimento de caracteres óticos (OCR) em imagens e realize o processamento de linguagem natural para criar novos campos que possa alavancar em consultas, facetas e filtros.

Este tutorial utiliza C# e o [.NET SDK](/dotnet/api/overview/azure/search) para executar as seguintes tarefas:

> [!div class="checklist"]
> * Comece com ficheiros de aplicações e imagens no armazenamento Azure Blob.
> * Defina um pipeline para adicionar OCR, extração de texto, deteção de linguagem, entidade e reconhecimento de frases-chave.
> * Defina um índice para armazenar a saída (conteúdo bruto, mais pares de valor-nome gerados pelo gasoduto).
> * Execute o pipeline para iniciar transformações e análises, e para criar e carregar o índice.
> * Explore resultados utilizando a pesquisa completa por texto e uma sintaxe de consulta rica.

Se não tiver uma subscrição do Azure, abra uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

+ [Armazenamento do Azure](https://azure.microsoft.com/services/storage/)
+ [Visual Studio](https://visualstudio.microsoft.com/downloads/)
+ [Criar](search-create-service-portal.md) ou [encontrar um serviço de pesquisa existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Você pode usar o serviço gratuito para este tutorial. Um serviço de pesquisa gratuito limita-o a três índices, três indexantes e três fontes de dados. Este tutorial cria um de cada. Antes de começar, certifique-se de ter espaço no seu serviço para aceitar os novos recursos.

## <a name="download-files"></a>Transferir ficheiros

1. Abra esta [pasta OneDrive](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) e no canto superior esquerdo, clique em **Baixar** para copiar os ficheiros para o seu computador. 

1. Clique com o botão direito no ficheiro zip e selecione **Extract All**. Existem 14 ficheiros de vários tipos. Vais usar 7 para este exercício.

Também pode baixar o código fonte para este tutorial. O código fonte está na pasta tutorial-ai-enriquecimento no repositório [de amostras de pesquisa de](https://github.com/Azure-Samples/azure-search-dotnet-samples) azul..

## <a name="1---create-services"></a>1 - Criar serviços

Este tutorial usa a Azure Cognitive Search para indexar e consultas, Serviços Cognitivos no backend para enriquecimento de IA, e armazenamento Azure Blob para fornecer os dados. Este tutorial fica sob a alocação gratuita de 20 transações por indexante por dia em Serviços Cognitivos, pelo que os únicos serviços que precisa de criar são a pesquisa e armazenamento.

Se possível, crie tanto na mesma região como no grupo de recursos para proximidade e gestão. Na prática, a sua conta de Armazenamento Azure pode estar em qualquer região.

### <a name="start-with-azure-storage"></a>Comece com o armazenamento Azure

1. [Inscreva-se no portal Azure](https://portal.azure.com/) e clique **+ Criar Recurso.**

1. Procure a *conta de armazenamento* e selecione a oferta de Conta de Armazenamento da Microsoft.

   ![Criar conta de Armazenamento](media/cognitive-search-tutorial-blob/storage-account.png "Criar conta de Armazenamento")

1. No separador Básicos, são necessários os seguintes itens. Aceite os incumprimentos para tudo o resto.

   + **Grupo de recursos**. Selecione um existente ou crie um novo, mas use o mesmo grupo para todos os serviços para que possa geri-los coletivamente.

   + **Nome da conta de armazenamento**. Se acha que pode ter múltiplos recursos do mesmo tipo, use o nome para desambiguar por tipo e região, por *exemplo, blobstoragewestus*. 

   + **Localização**. Se possível, escolha o mesmo local utilizado para a Azure Cognitive Search and Cognitive Services. Um único local anula as cargas de largura de banda.

   + **Tipo de Conta**. Escolha o predefinido, *StorageV2 (finalidade geral v2)*.

1. Clique **em 'Rever + Criar'** para criar o serviço.

1. Uma vez criado, clique em **Ir ao recurso** para abrir a página 'Vista Geral'.

1. Clique no serviço **Blobs.**

1. Clique **em + Recipiente** para criar um recipiente e nomeie-o *cog-search-demo*.

1. Selecione *a demonstração de pesquisa de cog e,* em seguida, clique em **Upload** para abrir a pasta onde guardou os ficheiros de descarregamento. Selecione todos os catorze ficheiros e clique **em OK** para carregar.

   ![Carregar ficheiros de amostras](media/cognitive-search-quickstart-blob/sample-data.png "Carregar ficheiros de amostras")

1. Antes de sair do Azure Storage, obtenha uma cadeia de ligação para que possa formular uma ligação na Pesquisa Cognitiva Azure. 

   1. Procure de volta à página geral da sua conta de armazenamento *(usamos blobstoragewestus* como exemplo). 
   
   1. No painel de navegação esquerdo, selecione **as teclas de acesso** e copie uma das cordas de ligação. 

   A cadeia de ligação é um URL semelhante ao seguinte exemplo:

      ```http
      DefaultEndpointsProtocol=https;AccountName=blobstoragewestus;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. Guarde o fio de ligação para o Bloco de Notas. Vai precisar mais tarde ao configurar a ligação à fonte de dados.

### <a name="cognitive-services"></a>Serviços Cognitivos

O enriquecimento de IA é apoiado por Serviços Cognitivos, incluindo Análise de Texto e Visão Computacional para o processamento de linguagem natural e imagem. Se o seu objetivo fosse completar um protótipo ou projeto real, você iria neste momento providenciar Serviços Cognitivos (na mesma região que a Azure Cognitive Search) para que você possa anexá-lo a operações de indexação.

Para este exercício, no entanto, você pode ignorar o fornecimento de recursos porque a Azure Cognitive Search pode ligar-se aos Serviços Cognitivos nos bastidores e dar-lhe 20 transações gratuitas por indexer executado. Uma vez que este tutorial utiliza 14 transações, a atribuição gratuita é suficiente. Para projetos maiores, planeie o fornecimento de Serviços Cognitivos no nível S0 pay-as-you-go. Para obter mais informações, consulte [Os Serviços Cognitivos anexados.](cognitive-search-attach-cognitive-services.md)

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

O terceiro componente é Azure Cognitive Search, que pode [criar no portal.](search-create-service-portal.md) Pode utilizar o nível Free para completar este walkthrough. 

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Obtenha uma api-chave de administrador e URL para pesquisa cognitiva Azure

Para interagir com o seu serviço de Pesquisa Cognitiva Azure, necessitará do URL de serviço e de uma chave de acesso. Um serviço de pesquisa é criado com ambos, por isso, se adicionar Azure Cognitive Search à sua subscrição, siga estes passos para obter as informações necessárias:

1. [Inscreva-se no portal Azure,](https://portal.azure.com/)e na página **geral do** seu serviço de pesquisa, obtenha o URL. Um ponto final de exemplo poderá ser parecido com `https://mydemo.search.windows.net`.

1. Em **Definições**  >  **Teclas,** obtenha uma chave de administração para todos os direitos sobre o serviço. Existem duas chaves de administração intercambiáveis, previstas para a continuidade do negócio, caso precise de rolar uma. Pode utilizar a tecla primária ou secundária nos pedidos de adição, modificação e eliminação de objetos.

   Pegue a chave de consulta também. É uma boa prática emitir pedidos de consulta com acesso apenas de leitura.

   ![Obtenha o nome de serviço e as chaves de administração e consulta](media/search-get-started-nodejs/service-name-and-keys.png)

Ter uma chave válida estabelece fidedignidade, numa base por pedido, entre a aplicação a enviar o pedido e o serviço que o processa.

## <a name="2---set-up-your-environment"></a>2 - Configurar o seu ambiente

Comece por abrir o Visual Studio e criar um novo projeto de App de Consola que pode funcionar em .NET Core.

### <a name="install-nuget-packages"></a>Instalar pacotes NuGet

O [Azure Cognitive Search .NET SDK](/dotnet/api/overview/azure/search) é composto por algumas bibliotecas de clientes que lhe permitem gerir os seus índices, fontes de dados, indexadores e skillsets, bem como carregar e gerir documentos e executar consultas, tudo sem ter de lidar com os detalhes de HTTP e JSON. Estas bibliotecas de clientes são distribuídas como pacotes NuGet.

Para este projeto, instale a versão 9 ou posterior do `Microsoft.Azure.Search` pacote NuGet.

1. Num browser, aceda à [página do pacote Microsoft.Azure.Search NuGet](https://www.nuget.org/packages/Microsoft.Azure.Search).

1. Selecione a versão mais recente (9 ou mais tarde).

1. Copie o comando do Gestor de Pacotes.

1. Abra a consola do gestor de pacotes. Selecione **ferramentas**  >  **NuGet Package Manager**Package Manager  >  **Consola**. 

1. Cole e faça o comando que copiou no passo anterior.

Em seguida, instale o mais recente `Microsoft.Extensions.Configuration.Json` pacote NuGet.

1. Selecione **Ferramentas**  >  **NuGet Package Manager**  >  **Gerencie pacotes nuget para solução...**. 

1. Clique **em Procurar** e procure o pacote `Microsoft.Extensions.Configuration.Json` NuGet. 

1. Selecione o pacote, selecione o seu projeto, confirme que a versão é a versão mais recente estável e, em seguida, clique em **Instalar**.

### <a name="add-service-connection-information"></a>Adicionar informações de ligação de serviço

1. Clique com o botão direito no seu projeto no Solution Explorer e **selecione Add**  >  **New Item...** . 

1. Nomeie o ficheiro `appsettings.json` e **selecione Adicionar**. 

1. Inclua este ficheiro no seu diretório de saída.
    1. Clique com o direito `appsettings.json` e selecione **Propriedades.** 
    1. Altere o valor da **Cópia para o Diretório de Saída** para Copiar se for mais **recente**.

1. Copie o JSON abaixo no seu novo ficheiro JSON.

    ```json
    {
      "SearchServiceName": "Put your search service name here",
      "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
      "SearchServiceQueryApiKey": "Put your query API key here",
      "AzureBlobConnectionString": "Put your Azure Blob connection string here",
    }
    ```
    
Adicione o seu serviço de pesquisa e informações de conta de armazenamento blob. Lembre-se que pode obter esta informação a partir das etapas de prestação de serviços indicadas na secção anterior.

Para **SearchServiceName**, insira o nome de serviço curto e não o URL completo.

### <a name="add-namespaces"></a>Adicionar espaços de nome

In `Program.cs` , adicione os seguintes espaços de nome.

```csharp
using System;
using System.Collections.Generic;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Extensions.Configuration;

namespace EnrichwithAI
```

### <a name="create-a-client"></a>Criar um cliente

Criar um exemplo da `SearchServiceClient` classe em `Main` .

```csharp
public static void Main(string[] args)
{
    // Create service client
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();
    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);
```

`CreateSearchServiceClient` cria um novo `SearchServiceClient` valor de utilização que são armazenados no ficheiro config da aplicação (appsettings.jsligado).

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
   string searchServiceName = configuration["SearchServiceName"];
   string adminApiKey = configuration["SearchServiceAdminApiKey"];

   SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
   return serviceClient;
}
```

> [!NOTE]
> A classe `SearchServiceClient` gere ligações com o seu serviço de pesquisa. Se possível, para evitar abrir demasiadas ligações, tente partilhar uma única instância de `SearchServiceClient` na sua aplicação. Os seus métodos são seguros para threads de forma a permitir esta partilha.
> 

### <a name="add-function-to-exit-the-program-during-failure"></a>Adicionar função para sair do programa durante a falha

Este tutorial destina-se a ajudá-lo a entender cada passo do pipeline de indexação. Se houver um problema crítico que impeça o programa de criar a fonte de dados, skillset, indexar ou indexar, o programa irá produzir a mensagem de erro e sair para que o problema possa ser compreendido e abordado.

Adicione `ExitProgram` `Main` para lidar com cenários que requerem a saída do programa.

```csharp
private static void ExitProgram(string message)
{
    Console.WriteLine("{0}", message);
    Console.WriteLine("Press any key to exit the program...");
    Console.ReadKey();
    Environment.Exit(0);
}
```

## <a name="3---create-the-pipeline"></a>3 - Criar o gasoduto

Na Pesquisa Cognitiva Azure, o processamento de IA ocorre durante a indexação (ou ingestão de dados). Esta parte do walkthrough cria quatro objetos: fonte de dados, definição de índice, skillset, indexante. 

### <a name="step-1-create-a-data-source"></a>Passo 1: criar uma origem de dados

`SearchServiceClient` tem uma propriedade `DataSources`. Esta propriedade fornece todos os métodos necessários para criar, listar, atualizar ou eliminar fontes de dados de Pesquisa Cognitiva Azure.

Crie um novo `DataSource` exemplo chamando `serviceClient.DataSources.CreateOrUpdate(dataSource)` . `DataSource.AzureBlobStorage` requer que especifique o nome da fonte de dados, o fio de ligação e o nome do recipiente blob.

```csharp
private static DataSource CreateOrUpdateDataSource(SearchServiceClient serviceClient, IConfigurationRoot configuration)
{
    DataSource dataSource = DataSource.AzureBlobStorage(
        name: "demodata",
        storageConnectionString: configuration["AzureBlobConnectionString"],
        containerName: "cog-search-demo",
        description: "Demo files to demonstrate cognitive search capabilities.");

    // The data source does not need to be deleted if it was already created
    // since we are using the CreateOrUpdate method
    try
    {
        serviceClient.DataSources.CreateOrUpdate(dataSource);
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to create or update the data source\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without a data source");
    }

    return dataSource;
}
```

Para um pedido bem sucedido, o método devolverá a fonte de dados que foi criada. Se houver um problema com o pedido, como um parâmetro inválido, o método lançará uma exceção.

Agora adicione uma linha `Main` para chamar a `CreateOrUpdateDataSource` função que acabou de adicionar.

```csharp
public static void Main(string[] args)
{
    // Create service client
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();
    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

    // Create or Update the data source
    Console.WriteLine("Creating or updating the data source...");
    DataSource dataSource = CreateOrUpdateDataSource(serviceClient, configuration);
```

Crie e execute a solução. Uma vez que este é o seu primeiro pedido, consulte o portal Azure para confirmar que a fonte de dados foi criada na Azure Cognitive Search. Na página do dashboard do serviço de pesquisa, verifique se o mosaico Origens de Dados tem um novo item. Poderá ter de aguardar alguns minutos para a página do portal atualizar.

  ![Azulejos de fontes de dados no portal](./media/cognitive-search-tutorial-blob/data-source-tile.png "Azulejos de fontes de dados no portal")

### <a name="step-2-create-a-skillset"></a>Passo 2: Criar um skillset

Nesta secção, define um conjunto de passos de enriquecimento que pretende aplicar aos seus dados. Cada passo de enriquecimento é chamado de *habilidade* e o conjunto de passos de enriquecimento é um *skillset*. Este tutorial usa [habilidades cognitivas incorporadas](cognitive-search-predefined-skills.md) para o skillset:

+ [Reconhecimento de Caracteres Óticos](cognitive-search-skill-ocr.md) para reconhecer texto impresso e manuscrito em ficheiros de imagem.

+ [Fusão de texto](cognitive-search-skill-textmerger.md) para consolidar texto de uma coleção de campos num único campo.

+ [Deteção de Idioma](cognitive-search-skill-language-detection.md) para identificar o idioma do conteúdo.

+ [Texto Dividido](cognitive-search-skill-textsplit.md) para dividir grandes conteúdos em pedaços menores antes de chamar a habilidade de extração da frase chave e a habilidade de reconhecimento da entidade. A extração de frases-chave e o reconhecimento de entidades aceitam entradas de 50.000 caracteres ou menos. Alguns dos ficheiros de exemplo precisam de ser divididos para caberem dentro deste limite.

+ [Reconhecimento de Entidades](cognitive-search-skill-entity-recognition.md) para extrair os nomes das organizações do conteúdo do recipiente blob.

+ [Extração de Expressões-Chave](cognitive-search-skill-keyphrases.md) para solicitar as principais expressões-chaves.

Durante o processamento inicial, a Azure Cognitive Search quebra cada documento para ler conteúdo de diferentes formatos de ficheiros. O texto encontrado proveniente do ficheiro de origem é colocado num campo ```content``` gerado (um para cada documento). Como tal, desa estada a entrada para ```"/document/content"``` utilizar este texto. 

As saídas podem ser mapeadas para um índice, utilizadas como entradas para uma competência a jusante ou ambas, como é o caso do código de idioma. No índice, o código de idioma é útil para a filtragem. Como entrada, o código de idioma é utilizado pelas competências de análise de texto para informar sobre as regras linguísticas em torno da separação de palavras.

Para obter mais informações acerca das noções básicas do conjunto de competências, veja [Como definir um conjunto de competências](cognitive-search-defining-skillset.md).

### <a name="ocr-skill"></a>Habilidade de OCR

A habilidade **OCR** extrai texto de imagens. Esta habilidade pressupõe que existe um campo normalized_images. Para gerar este campo, mais tarde no tutorial vamos definir a ```"imageAction"``` configuração na definição de indexante para ```"generateNormalizedImages"``` .

```csharp
private static OcrSkill CreateOcrSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>
    {
        new InputFieldMappingEntry(
        name: "image",
        source: "/document/normalized_images/*")
    };

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>
    {
        new OutputFieldMappingEntry(
        name: "text",
        targetName: "text")
    };

    OcrSkill ocrSkill = new OcrSkill(
        description: "Extract text (plain and structured) from image",
        context: "/document/normalized_images/*",
        inputs: inputMappings,
        outputs: outputMappings,
        defaultLanguageCode: OcrSkillLanguage.En,
        shouldDetectOrientation: true);

    return ocrSkill;
}
```

### <a name="merge-skill"></a>Fusão de habilidades

Nesta secção irá criar uma habilidade **de fusão** que funde o campo de conteúdo documental com o texto produzido pela habilidade OCR.

```csharp
private static MergeSkill CreateMergeSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>
    {
        new InputFieldMappingEntry(
        name: "text",
        source: "/document/content"),
        new InputFieldMappingEntry(
        name: "itemsToInsert",
        source: "/document/normalized_images/*/text"),
        new InputFieldMappingEntry(
        name: "offsets",
        source: "/document/normalized_images/*/contentOffset")
    };

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>
    {
        new OutputFieldMappingEntry(
        name: "mergedText",
        targetName: "merged_text")
    };

    MergeSkill mergeSkill = new MergeSkill(
        description: "Create merged_text which includes all the textual representation of each image inserted at the right location in the content field.",
        context: "/document",
        inputs: inputMappings,
        outputs: outputMappings,
        insertPreTag: " ",
        insertPostTag: " ");

    return mergeSkill;
}
```

### <a name="language-detection-skill"></a>Habilidade de deteção de linguagem

A habilidade **de Deteção de Idiomas** deteta a linguagem do texto de entrada e reporta um único código linguístico para cada documento submetido no pedido. Usaremos a saída da habilidade de **Deteção de Idiomas** como parte da entrada para a habilidade **de Divisão de Texto.**

```csharp
private static LanguageDetectionSkill CreateLanguageDetectionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>
    {
        new InputFieldMappingEntry(
        name: "text",
        source: "/document/merged_text")
    };

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>
    {
        new OutputFieldMappingEntry(
        name: "languageCode",
        targetName: "languageCode")
    };

    LanguageDetectionSkill languageDetectionSkill = new LanguageDetectionSkill(
        description: "Detect the language used in the document",
        context: "/document",
        inputs: inputMappings,
        outputs: outputMappings);

    return languageDetectionSkill;
}
```

### <a name="text-split-skill"></a>Habilidade de divisão de texto

A habilidade abaixo **split** dividirá texto por páginas e limitará o comprimento da página a 4.000 caracteres medido por `String.Length` . O algoritmo tentará dividir o texto em pedaços que são no máximo `maximumPageLength` de tamanho. Neste caso, o algoritmo fará o seu melhor para quebrar a frase num limite de sentença, pelo que o tamanho do pedaço pode ser ligeiramente inferior `maximumPageLength` a .

```csharp
private static SplitSkill CreateSplitSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>
    {
        new InputFieldMappingEntry(
        name: "text",
        source: "/document/merged_text"),
        new InputFieldMappingEntry(
        name: "languageCode",
        source: "/document/languageCode")
    };

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>
    {
        new OutputFieldMappingEntry(
        name: "textItems",
        targetName: "pages")
    };

    SplitSkill splitSkill = new SplitSkill(
        description: "Split content into pages",
        context: "/document",
        inputs: inputMappings,
        outputs: outputMappings,
        textSplitMode: TextSplitMode.Pages,
        maximumPageLength: 4000);

    return splitSkill;
}
```

### <a name="entity-recognition-skill"></a>Competência de reconhecimento de entidade

Este `EntityRecognitionSkill` caso está definido para reconhecer o tipo de `organization` categoria. A competência **de Reconhecimento de Entidades** também pode reconhecer tipos de `person` categorias e `location` .

Note que o campo "contexto" é definido ```"/document/pages/*"``` com um asterisco, o que significa que o passo de enriquecimento é chamado para cada página em ```"/document/pages"``` .

```csharp
private static EntityRecognitionSkill CreateEntityRecognitionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>
    {
        new InputFieldMappingEntry(
        name: "text",
        source: "/document/pages/*")
    };

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>
    {
        new OutputFieldMappingEntry(
        name: "organizations",
        targetName: "organizations")
    };

    List<EntityCategory> entityCategory = new List<EntityCategory>
    {
        EntityCategory.Organization
    };

    EntityRecognitionSkill entityRecognitionSkill = new EntityRecognitionSkill(
        description: "Recognize organizations",
        context: "/document/pages/*",
        inputs: inputMappings,
        outputs: outputMappings,
        categories: entityCategory,
        defaultLanguageCode: EntityRecognitionSkillLanguage.En);

    return entityRecognitionSkill;
}
```

### <a name="key-phrase-extraction-skill"></a>Habilidade de extração de frase-chave

Tal como o `EntityRecognitionSkill` caso que acabou de ser criado, a habilidade **de extração de frase-chave** é chamada para cada página do documento.

```csharp
private static KeyPhraseExtractionSkill CreateKeyPhraseExtractionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry(
        name: "text",
        source: "/document/pages/*"));
    inputMappings.Add(new InputFieldMappingEntry(
        name: "languageCode",
        source: "/document/languageCode"));

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry(
        name: "keyPhrases",
        targetName: "keyPhrases"));

    KeyPhraseExtractionSkill keyPhraseExtractionSkill = new KeyPhraseExtractionSkill(
        description: "Extract the key phrases",
        context: "/document/pages/*",
        inputs: inputMappings,
        outputs: outputMappings);

    return keyPhraseExtractionSkill;
}
```

### <a name="build-and-create-the-skillset"></a>Construir e criar o skillset

Construa o `Skillset` uso das habilidades que criou.

```csharp
private static Skillset CreateOrUpdateDemoSkillSet(SearchServiceClient serviceClient, IList<Skill> skills)
{
    Skillset skillset = new Skillset(
        name: "demoskillset",
        description: "Demo skillset",
        skills: skills);

    // Create the skillset in your search service.
    // The skillset does not need to be deleted if it was already created
    // since we are using the CreateOrUpdate method
    try
    {
        serviceClient.Skillsets.CreateOrUpdate(skillset);
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to create the skillset\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without a skillset");
    }

    return skillset;
}
```

Adicione as seguintes linhas a `Main` .

```csharp
    // Create the skills
    Console.WriteLine("Creating the skills...");
    OcrSkill ocrSkill = CreateOcrSkill();
    MergeSkill mergeSkill = CreateMergeSkill();
    EntityRecognitionSkill entityRecognitionSkill = CreateEntityRecognitionSkill();
    LanguageDetectionSkill languageDetectionSkill = CreateLanguageDetectionSkill();
    SplitSkill splitSkill = CreateSplitSkill();
    KeyPhraseExtractionSkill keyPhraseExtractionSkill = CreateKeyPhraseExtractionSkill();

    // Create the skillset
    Console.WriteLine("Creating or updating the skillset...");
    List<Skill> skills = new List<Skill>
    {
        ocrSkill,
        mergeSkill,
        languageDetectionSkill,
        splitSkill,
        entityRecognitionSkill,
        keyPhraseExtractionSkill
    };

    Skillset skillset = CreateOrUpdateDemoSkillSet(serviceClient, skills);
```

### <a name="step-3-create-an-index"></a>Passo 3: Criar um índice

Nesta secção, pode definir o esquema de índice ao especificar os campos a incluir no índice pesquisável e os atributos de pesquisa para cada campo. Os campos têm um tipo e podem ter atributos que determinam a forma como o campo é utilizado (pesquisável, ordenável e assim por diante). Os nomes dos campos num índice não têm necessariamente de corresponder aos nomes dos campos na origem. Num passo posterior, vai adicionar mapeamentos de campos num indexador para ligar campos de origem-destino. Para este passo, defina o índice com convenções de nomenclatura de campo relevantes para a aplicação de pesquisa.

Neste exercício, utiliza os seguintes campos e tipos de campo:

| Nomes de campo | Tipos de campo |
| --- | --- |
| ID | Edm.String |
| conteúdo | Edm.String |
| languageCode | Edm.String |
| keyPhrases | List<Edm.String> |
| organizações | List<Edm.String> |

#### <a name="create-demoindex-class"></a>Criar Classe DemoIndex

Os campos para este índice são definidos usando uma classe modelo. Cada propriedade da classe do modelo tem atributos que determinam os comportamentos relacionados com a pesquisa do campo de índice correspondente. 

Vamos adicionar a classe modelo a um novo ficheiro C#. Clique no seu projeto e **selecione Adicionar**  >  **Novo Item...** selecione "Class" e nomeie o `DemoIndex.cs` ficheiro, em seguida, selecione **Add**.

Certifique-se de que pretende utilizar tipos dos `Microsoft.Azure.Search` espaços e `Microsoft.Azure.Search.Models` nomes.

Adicione a definição de classe abaixo do modelo `DemoIndex.cs` e inclua-a no mesmo espaço de nome onde irá criar o índice.

```csharp
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;

namespace EnrichwithAI
{
    // The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Search .NET SDK.
    // It ensures that Pascal-case property names in the model class are mapped to camel-case
    // field names in the index.
    [SerializePropertyNamesAsCamelCase]
    public class DemoIndex
    {
        [System.ComponentModel.DataAnnotations.Key]
        [IsSearchable, IsSortable]
        public string Id { get; set; }

        [IsSearchable]
        public string Content { get; set; }

        [IsSearchable]
        public string LanguageCode { get; set; }

        [IsSearchable]
        public string[] KeyPhrases { get; set; }

        [IsSearchable]
        public string[] Organizations { get; set; }
    }
}
```

Agora que definiu uma classe modelo, `Program.cs` pode criar uma definição de índice com bastante facilidade. O nome deste índice será `demoindex` . Se um índice já existir com esse nome, será eliminado.

```csharp
private static Index CreateDemoIndex(SearchServiceClient serviceClient)
{
    var index = new Index()
    {
        Name = "demoindex",
        Fields = FieldBuilder.BuildForType<DemoIndex>()
    };

    try
    {
        bool exists = serviceClient.Indexes.Exists(index.Name);

        if (exists)
        {
            serviceClient.Indexes.Delete(index.Name);
        }

        serviceClient.Indexes.Create(index);
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to create the index\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without an index");
    }

    return index;
}
```

Durante os testes poderá descobrir que está a tentar criar o índice mais de uma vez. Por causa disso, verifique se o índice que está prestes a criar já existe antes de tentar criá-lo.

Adicione as seguintes linhas a `Main` .

```csharp
    // Create the index
    Console.WriteLine("Creating the index...");
    Microsoft.Azure.Search.Models.Index demoIndex = CreateDemoIndex(serviceClient);
```

Adicione a seguinte declaração de utilização para resolver a referência de desambiguado.

```csharp
using Index = Microsoft.Azure.Search.Models.Index;
```

Para saber mais sobre a definição de um índice, consulte [Create Index (Azure Cognitive Search REST API)](/rest/api/searchservice/create-index).

### <a name="step-4-create-and-run-an-indexer"></a>Passo 4: Criar e executar um indexador

Até ao momento, criou uma origem de dados, um conjunto de competências e um índice. Estes três componentes tornam-se parte de um [indexador](search-indexer-overview.md) que une cada uma das peças numa única operação de várias fases. Para as associar num indexador, tem de definir os mapeamentos de campo.

+ Os fieldMappings são processados antes do skillset, mapeando campos de origem da fonte de dados para campos alvo em um índice. Se os nomes e tipos de campo forem os mesmos em ambas as extremidades, não é necessário mapear.

+ As saídasFieldMappings são processadas após o skillset, referindo-se a origemFieldNames que não existem até que o documento de cracking ou enriquecimento os crie. O targetFieldName é um campo num índice.

Além de ligar entradas às saídas, também pode usar mapeamentos de campo para achatar estruturas de dados. Para mais informações, consulte [como mapear campos enriquecidos para um índice pescável.](cognitive-search-output-field-mapping.md)

```csharp
private static Indexer CreateDemoIndexer(SearchServiceClient serviceClient, DataSource dataSource, Skillset skillSet, Index index)
{
    IDictionary<string, object> config = new Dictionary<string, object>();
    config.Add(
        key: "dataToExtract",
        value: "contentAndMetadata");
    config.Add(
        key: "imageAction",
        value: "generateNormalizedImages");

    List<FieldMapping> fieldMappings = new List<FieldMapping>
    {
        new FieldMapping(
        sourceFieldName: "metadata_storage_path",
        targetFieldName: "id",
        mappingFunction: new FieldMappingFunction(
            name: "base64Encode")),
        new FieldMapping(
        sourceFieldName: "content",
        targetFieldName: "content")
    };

    List<FieldMapping> outputMappings = new List<FieldMapping>
    {
        new FieldMapping(
        sourceFieldName: "/document/pages/*/organizations/*",
        targetFieldName: "organizations"),
        new FieldMapping(
        sourceFieldName: "/document/pages/*/keyPhrases/*",
        targetFieldName: "keyPhrases"),
        new FieldMapping(
        sourceFieldName: "/document/languageCode",
        targetFieldName: "languageCode")
    };

    Indexer indexer = new Indexer(
        name: "demoindexer",
        dataSourceName: dataSource.Name,
        targetIndexName: index.Name,
        description: "Demo Indexer",
        skillsetName: skillSet.Name,
        parameters: new IndexingParameters(
            maxFailedItems: -1,
            maxFailedItemsPerBatch: -1,
            configuration: config),
        fieldMappings: fieldMappings,
        outputFieldMappings: outputMappings);

    try
    {
        bool exists = serviceClient.Indexers.Exists(indexer.Name);

        if (exists)
        {
            serviceClient.Indexers.Delete(indexer.Name);
        }

        serviceClient.Indexers.Create(indexer);
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to create the indexer\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without creating an indexer");
    }

    return indexer;
}
```
Adicione as seguintes linhas a `Main` .

```csharp
    // Create the indexer, map fields, and execute transformations
    Console.WriteLine("Creating the indexer and executing the pipeline...");
    Indexer demoIndexer = CreateDemoIndexer(serviceClient, dataSource, skillset, demoIndex);
```

Espere que a criação do indexante leve algum tempo a ser concluída. Apesar de o conjunto de dados ser pequeno, as competências analíticas realizam um processo de computação intensa. Algumas competências, como a análise de imagem, são de execução longa.

> [!TIP]
> A criação de um indexador invoca o pipeline. Se houver problemas em atingir os dados, com o mapeamento de entradas e saídas ou com a ordem das operações, estes vão surgir nesta fase.

### <a name="explore-creating-the-indexer"></a>Explore a criação do indexante

O código ```"maxFailedItems"```  define-se para -1, que instrui o motor de indexação a ignorar erros durante a importação de dados. Esta definição é útil pois existem poucos documentos na origem de dados de demonstração. Para uma origem de dados maior, deve definir o valor com um número maior que 0.

Note também que o ```"dataToExtract"``` está definido para ```"contentAndMetadata"``` . Esta instrução informa o indexador para extrair automaticamente o conteúdo a partir de formatos de ficheiros diferentes, bem como de metadados relativos a cada ficheiro.

Quando o conteúdo é extraído, pode definir `imageAction` para extrair texto das imagens existentes na origem de dados. O ```"imageAction"``` conjunto de ```"generateNormalizedImages"``` configuração, combinado com a Habilidade de Fusão de OCR e A Capacidade de Fusão de Texto, diz ao indexante para extrair texto das imagens (por exemplo, a palavra "parar" de um sinal de stop de tráfego), e incorpora-o como parte do campo de conteúdo. Este comportamento aplica-se tanto às imagens incorporadas nos documentos (tal como uma imagem num PDF) como às imagens existentes na origem de dados, por exemplo, um ficheiro JPG.

<a name="check-indexer-status"></a>

## <a name="4---monitor-indexing"></a>4 - Monitorização da indexação

Assim que o indexador é definido, este é executado automaticamente quando submete o pedido. Consoante as competências cognitivas que definiu, a indexação pode demorar mais do que o esperado. Para saber se o indexante ainda está em funcionamento, use o `GetStatus` método.

```csharp
private static void CheckIndexerOverallStatus(SearchServiceClient serviceClient, Indexer indexer)
{
    try
    {
        IndexerExecutionInfo demoIndexerExecutionInfo = serviceClient.Indexers.GetStatus(indexer.Name);

        switch (demoIndexerExecutionInfo.Status)
        {
            case IndexerStatus.Error:
                ExitProgram("Indexer has error status. Check the Azure Portal to further understand the error.");
                break;
            case IndexerStatus.Running:
                Console.WriteLine("Indexer is running");
                break;
            case IndexerStatus.Unknown:
                Console.WriteLine("Indexer status is unknown");
                break;
            default:
                Console.WriteLine("No indexer information");
                break;
        }
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to get indexer overall status\n Exception message: {0}\n", e.Message);
    }
}
```

`IndexerExecutionInfo` representa o estado atual e o histórico de execução de um indexante.

Os avisos são comuns com algumas combinações de ficheiros de origem e competências e nem sempre indicam um problema. Neste tutorial, os avisos são benignos (por exemplo, os ficheiros JPEG não têm entradas de texto).

Adicione as seguintes linhas a `Main` .

```csharp
    // Check indexer overall status
    Console.WriteLine("Check the indexer overall status...");
    CheckIndexerOverallStatus(serviceClient, demoIndexer);
```
 
## <a name="5---search"></a>5 - Pesquisar

Depois de terminar a indexação, pode executar consultas que devolvam o conteúdo de campos individuais. Por padrão, a Azure Cognitive Search devolve os 50 melhores resultados. Os dados de exemplo são pequenos, pelo que a predefinição funciona bem. No entanto, ao trabalhar com conjuntos de dados maiores, poderá ter de incluir parâmetros na cadeia de consulta para devolver mais resultados. Para obter instruções, consulte [como página os resultados na Pesquisa Cognitiva Azure](search-pagination-page-layout.md).

Como passo de verificação, consulte o índice de todos os campos.

Adicione as seguintes linhas a `Main` .

```csharp
DocumentSearchResult<DemoIndex> results;

ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

SearchParameters parameters =
    new SearchParameters
    {
        Select = new[] { "organizations" }
    };

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*", parameters);
}
catch (Exception e)
{
    // Handle exception
}
```

`CreateSearchIndexClient` cria um novo `SearchIndexClient` valor de utilização que são armazenados no ficheiro config da aplicação (appsettings.jsligado). Note que a chave API do serviço de pesquisa é utilizada e não a chave de administração.

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
   string searchServiceName = configuration["SearchServiceName"];
   string queryApiKey = configuration["SearchServiceQueryApiKey"];

   SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "demoindex", new SearchCredentials(queryApiKey));
   return indexClient;
}
```

Adicione o seguinte código a `Main` . A primeira captura retorna a definição de índice, com o nome, tipo e atributos de cada campo. A segunda é uma consulta parametrizada, onde `Select` especifica quais os campos a incluir nos resultados, por exemplo `organizations` . Uma série de pesquisas `"*"` devolve todos os conteúdos de um único campo.

```csharp
//Verify content is returned after indexing is finished
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*");
    Console.WriteLine("First query succeeded with a result count of {0}", results.Results.Count);
}
catch (Exception e)
{
    Console.WriteLine("First query failed\n Exception message: {0}\n", e.Message);
}

SearchParameters parameters =
    new SearchParameters
    {
        Select = new[] { "organizations" }
    };

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*", parameters);
    Console.WriteLine("Second query succeeded with a result count of {0}", results.Results.Count);
}
catch (Exception e)
{
    Console.WriteLine("Second query failed\n Exception message: {0}\n", e.Message);
}
```

Repita para campos adicionais: conteúdo, languageCode, keyPhrases e organizações neste exercício. Pode devolver vários campos através da propriedade [Select](/dotnet/api/microsoft.azure.search.models.searchparameters.select?view=azure-dotnet) utilizando uma lista delimitada por vírgula.

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Repor e executar novamente

Nas fases experimentais iniciais de desenvolvimento, a abordagem mais prática para a iteração de design é eliminar os objetos da Azure Cognitive Search e permitir que o seu código os reconstrua. Os nomes dos recursos são exclusivos. Quando elimina um objeto, pode recriá-lo com o mesmo nome.

O código de amostra deste tutorial verifica os objetos existentes e elimina-os para que possa voltar a verificar o seu código.

Também pode utilizar o portal para eliminar índices, indexadores, fontes de dados e skillsets.

## <a name="takeaways"></a>Conclusões

Este tutorial demonstrou os passos básicos para a construção de um gasoduto de indexação enriquecido através da criação de componentes: uma fonte de dados, skillset, índice e indexante.

[Foram introduzidas competências incorporadas,](cognitive-search-predefined-skills.md) juntamente com a definição de skillset e a mecânica das habilidades de acorrentamento em conjunto através de entradas e saídas. Também aprendeu que `outputFieldMappings` na definição do indexante é necessário encaminhar valores enriquecidos do pipeline para um índice pesmável num serviço de Pesquisa Cognitiva Azure.

Por fim, aprendeu como testar os resultados e repor o sistema para iterações futuras. Aprendeu que a emissão de consultas acerca do índice devolve o resultado criado pelo pipeline de indexação melhorado. Também aprendeu como verificar o estado do indexador e quais os objetos a eliminar antes de executar novamente um pipeline.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando se está a trabalhar na sua própria subscrição, no final de um projeto, é uma boa ideia remover os recursos de que já não precisa. Os recursos que deixar em execução podem custar-lhe dinheiro. Pode eliminar recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Pode encontrar e gerir recursos no portal, utilizando a ligação de todos os recursos ou grupos de recursos no painel de navegação à esquerda.

## <a name="next-steps"></a>Passos seguintes

Agora que está familiarizado com todos os objetos num oleoduto de enriquecimento de IA, vamos olhar mais de perto as definições de skillset e habilidades individuais.

> [!div class="nextstepaction"]
> [Como criar um skillset](cognitive-search-defining-skillset.md)