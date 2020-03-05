---
title: 'Tutorial: C# e IA sobre bolhas Azure'
titleSuffix: Azure Cognitive Search
description: Percorra um exemplo de extração de texto e C# processamento de linguagem natural sobre conteúdos no armazenamento Blob utilizando e o Azure Cognitive Search .NET SDK.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/27/2020
ms.openlocfilehash: 0b9e7732e5274fd71c773a19d17e09ecdaa2ceb0
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270008"
---
# <a name="tutorial-use-c-and-ai-to-generate-searchable-content-from-azure-blobs"></a>Tutorial: C# Use e IA para gerar conteúdo pesquisável a partir de blobs Azure

Se tiver texto ou imagens não estruturadas no armazenamento do Azure Blob, um pipeline de enriquecimento de [IA](cognitive-search-concept-intro.md) pode extrair informações e criar novos conteúdos que são úteis para cenários de pesquisa de texto completo ou de mineração de conhecimento. Neste C# tutorial, aplique reconhecimento ótico de caracteres (OCR) em imagens e execute o processamento de linguagem natural para criar novos campos que você pode aproveitar em consultas, facetas e filtros.

Este tutorial C# utiliza e o [.NET SDK](https://aka.ms/search-sdk) para executar as seguintes tarefas:

> [!div class="checklist"]
> * Comece com ficheiros de aplicações e imagens no armazenamento do Azure Blob.
> * Defina um pipeline para adicionar OCR, extração de texto, deteção de linguagem, reconhecimento de entidade e frase-chave.
> * Defina um índice para armazenar a saída (conteúdo bruto, mais pares de valor de nome gerados pelo gasoduto).
> * Execute o gasoduto para iniciar transformações e análises, e para criar e carregar o índice.
> * Explore os resultados usando a pesquisa completa de texto e uma sintaxe de consulta rica.

Se não tiver uma subscrição Azure, abra uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

+ [Armazenamento do Azure](https://azure.microsoft.com/services/storage/)
+ [Visual Studio](https://visualstudio.microsoft.com/downloads/)
+ [Criar](search-create-service-portal.md) ou [encontrar um serviço](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) de pesquisa existente 

> [!Note]
> Pode utilizar o serviço gratuito para este tutorial. Um serviço de pesquisa gratuito limita-o a três índices, três indexadores e três fontes de dados. Este tutorial cria um de cada. Antes de começar, certifique-se de que tem espaço ao seu serviço para aceitar os novos recursos.

## <a name="download-files"></a>Transferir ficheiros

1. Abra esta [pasta OneDrive](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) e no canto superior esquerdo, clique em **Baixar** para copiar os ficheiros para o seu computador. 

1. Clique no ficheiro zip e **selecione Extrato Tudo**. Existem 14 ficheiros de vários tipos. Use-os todos para este tutorial.

## <a name="1---create-services"></a>1 - Criar serviços

Este tutorial utiliza a Pesquisa Cognitiva Azure para indexação e consultas, Serviços Cognitivos no backend para enriquecimento de IA, e armazenamento Azure Blob para fornecer os dados. Este tutorial permanece sob a atribuição gratuita de 20 transações por indexante por dia em Serviços Cognitivos, pelo que os únicos serviços que precisa para criar são a pesquisa e armazenamento.

Se possível, crie tanto na mesma região como grupo de recursos para proximidade e gestão. Na prática, a sua conta de Armazenamento Azure pode estar em qualquer região.

### <a name="start-with-azure-storage"></a>Comece com o Armazenamento Azure

1. [Inscreva-se no portal Azure](https://portal.azure.com/) e clique **em + Criar Recurso**.

1. Procure a conta de *armazenamento* e selecione a oferta da Conta de Armazenamento da Microsoft.

   ![Criar conta de Armazenamento](media/cognitive-search-tutorial-blob/storage-account.png "Criar conta de Armazenamento")

1. No separador Basics, são necessários os seguintes itens. Aceite os incumprimentos para todo o resto.

   + **Grupo de recursos**. Selecione um existente ou crie um novo, mas use o mesmo grupo para todos os serviços para que possa geri-los coletivamente.

   + **Nome da conta de armazenamento.** Se pensa que pode ter vários recursos do mesmo tipo, use o nome para desambiguar por tipo e região, por exemplo *blobstoragewestus*. 

   + **Localização**. Se possível, escolha o mesmo local utilizado para a Pesquisa Cognitiva Azure e Serviços Cognitivos. Um único local anula as cargas de largura de banda.

   + **Tipo de conta.** Escolha o predefinido, *StorageV2 (finalidade geral v2)* .

1. Clique em **Rever + Criar** para criar o serviço.

1. Uma vez criado, clique em **Ir ao recurso** para abrir a página 'Overview'.

1. Clique no serviço **Blobs.**

1. Clique **em + Recipiente** para criar um recipiente e nomeá-lo *básico-demo-data-pr*.

1. Selecione *basic-demo-data-pr* e, em seguida, clique em **Carregar** para abrir a pasta onde guardou os ficheiros de descarregamento. Selecione todos os 14 ficheiros e clique em **OK** para carregar.

   ![Upload de ficheiros de amostras](media/cognitive-search-quickstart-blob/sample-data.png "Upload de ficheiros de amostras")

1. Antes de sair do Azure Storage, obtenha uma cadeia de ligação para que possa formular uma ligação em Azure Cognitive Search. 

   1. Volte para a página de visão geral da sua conta de armazenamento (usamos *blobstragewestus* como exemplo). 
   
   1. No painel de navegação esquerdo, selecione **teclas de acesso** e copie uma das cordas de ligação. 

   A cadeia de ligação é um URL semelhante ao seguinte exemplo:

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. Guarde a corda de ligação ao Bloco de Notas. Vai precisar mais tarde quando configurar a ligação de fonte de dados.

### <a name="cognitive-services"></a>Serviços Cognitivos

O enriquecimento de IA é apoiado por Serviços Cognitivos, incluindo Text Analytics e Computer Vision para linguagem natural e processamento de imagem. Se o seu objetivo fosse completar um protótipo ou projeto real, nesta altura forneceria serviços cognitivos (na mesma região que a Pesquisa Cognitiva Azure) para que o pudesse anexar a operações de indexação.

Para este exercício, no entanto, você pode saltar o fornecimento de recursos porque a Pesquisa Cognitiva Azure pode ligar-se aos Serviços Cognitivos nos bastidores e dar-lhe 20 transações gratuitas por corrida de indexador. Uma vez que este tutorial utiliza 7 transações, a atribuição gratuita é suficiente. Para projetos maiores, planeie o fornecimento de Serviços Cognitivos no nível S0 pay-as-you-go. Para mais informações, consulte [Anexar Serviços Cognitivos](cognitive-search-attach-cognitive-services.md).

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

O terceiro componente é a Pesquisa Cognitiva Azure, que pode [criar no portal.](search-create-service-portal.md) Pode utilizar o free tier para completar esta passagem. 

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Obtenha uma chave de api-key e URL para pesquisa cognitiva azure

Para interagir com o seu serviço de Pesquisa Cognitiva Azure vai precisar do URL de serviço e de uma chave de acesso. Um serviço de pesquisa é criado com ambos, por isso, se você adicionar Pesquisa Cognitiva Azure à sua subscrição, siga estes passos para obter as informações necessárias:

1. [Inscreva-se no portal Azure](https://portal.azure.com/), e na página de **visão geral** do seu serviço de pesquisa, obtenha o URL. Um ponto final de exemplo poderá ser parecido com `https://mydemo.search.windows.net`.

1. Em **Definições** > **Teclas,** obtenha uma chave de administração para todos os direitos sobre o serviço. Existem duas chaves de administração intercambiáveis, previstas para a continuidade do negócio no caso de precisar de rolar uma. Pode utilizar a chave primária ou secundária nos pedidos de adição, modificação e aparas de objetos.

   Pegue a chave de consulta também. É uma boa prática emitir pedidos de consulta com acesso só para leitura.

   ![Obtenha o nome de serviço e as chaves de administração e consulta](media/search-get-started-nodejs/service-name-and-keys.png)

Ter uma chave válida estabelece fidedignidade, numa base por pedido, entre a aplicação a enviar o pedido e o serviço que o processa.

## <a name="2---set-up-your-environment"></a>2 - Instale o seu ambiente

Comece por abrir o Visual Studio e criar um novo projeto de App console que possa funcionar em .NET Core.

### <a name="install-nuget-packages"></a>Instalar pacotes NuGet

O [Azure Cognitive Search .NET SDK](https://aka.ms/search-sdk) é composto por algumas bibliotecas de clientes que lhe permitem gerir os seus índices, fontes de dados, indexadores e competências, bem como carregar e gerir documentos e executar consultas, tudo sem ter de lidar com os detalhes de HTTP e JSON. Estas bibliotecas de clientes estão todas distribuídas como pacotes NuGet.

Para este projeto, instale a versão 9 ou posterior do pacote NuGet `Microsoft.Azure.Search`.

1. Abra a consola de gestor de pacotes. Selecione **ferramentas** > **NuGet Package Manager** > Consola de Gestor de **Pacotes**. 

1. Navegue para [Microsoft.Azure.Search NuGet página de pacotes](https://www.nuget.org/packages/Microsoft.Azure.Search).

1. Selecione a versão mais recente (9 ou mais tarde).

1. Copie o comando do Gestor de Pacotes.

1. Volte à consola do Gestor de Pacotes e execute o comando que copiou no passo anterior.

Em seguida, instale o mais recente pacote NuGet `Microsoft.Extensions.Configuration.Json`.

1. Selecione **ferramentas** > **NuGet Package Manager** > **gerir pacotes NuGet para solução...** . 

1. Clique em **Navegar** e procurar o pacote nuGet `Microsoft.Extensions.Configuration.Json`. 

1. Selecione o pacote, selecione o seu projeto, confirme que a versão é a versão mais recente e estável e, em seguida, clique em **Instalar**.

### <a name="add-service-connection-information"></a>Adicionar informações de ligação de serviço

1. Clique no seu projeto no Solution Explorer e selecione **Adicionar** > **Novo Item...** . 

1. Nomeie o ficheiro `appsettings.json` e selecione **Adicionar**. 

1. Inclua este ficheiro no seu diretório de saída.
    1. Clique à direita no `appsettings.json` e selecione **Propriedades**. 
    1. Alterar o valor de **Copy para 'Diretório de Saída'** para **Copiar se for mais recente**.

1. Copie o JSON abaixo no seu novo ficheiro JSON.

    ```json
    {
      "SearchServiceName": "Put your search service name here",
      "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
      "SearchServiceQueryApiKey": "Put your query API key here",
      "AzureBlobConnectionString": "Put your Azure Blob connection string here",
    }
    ```

Adicione o seu serviço de pesquisa e informações de conta de armazenamento blob. Lembre-se que pode obter esta informação a partir das etapas de fornecimento de serviços indicadas na secção anterior.

### <a name="add-namespaces"></a>Adicionar espaços de nome

Em `Program.cs`, adicione os seguintes espaços de nome.

```csharp
using System;
using System.Collections.Generic;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Extensions.Configuration;

namespace EnrichwithAI
```

### <a name="create-a-client"></a>Criar um cliente

Crie uma instância da classe `SearchServiceClient` sob main.

```csharp
public static void Main(string[] args)
{
    // Create service client
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();
    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);
```

`CreateSearchServiceClient` cria um novo `SearchServiceClient` utilizando valores que são armazenados no ficheiro config da aplicação (appsettings.json).

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

## <a name="3---create-the-pipeline"></a>3 - Criar o gasoduto

Na Pesquisa Cognitiva Azure, o processamento de IA ocorre durante a indexação (ou ingestão de dados). Esta parte do walkthrough cria quatro objetos: fonte de dados, definição de índice, skillset, indexante. 

### <a name="step-1-create-a-data-source"></a>Passo 1: criar uma origem de dados

`SearchServiceClient` tem uma propriedade `DataSources`. Esta propriedade fornece todos os métodos necessários para criar, listar, atualizar ou eliminar fontes de dados de Pesquisa Cognitiva Azure.

Crie um novo exemplo `DataSource` chamando `serviceClient.DataSources.CreateOrUpdate(dataSource)`. `DataSource.AzureBlobStorage` requer que especifique o nome da fonte de dados, a cadeia de ligação e o nome do recipiente blob.

```csharp
private static DataSource CreateOrUpdateDataSource(SearchServiceClient serviceClient, IConfigurationRoot configuration)
{
    DataSource dataSource = DataSource.AzureBlobStorage(
        name: "demodata",
        storageConnectionString: configuration["AzureBlobConnectionString"],
        containerName: "basic-demo-data-pr",
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

Para um pedido bem sucedido, o método devolverá a fonte de dados que foi criada. Se houver algum problema com o pedido, como um parâmetro inválido, o método lançará uma exceção.

Adicione agora uma linha na Main para chamar a função `CreateOrUpdateDataSource` que acabou de adicionar.

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


<!-- 
```csharp
DataSource dataSource = DataSource.AzureBlobStorage(
    name: "demodata",
    storageConnectionString: configuration["AzureBlobConnectionString"],
    containerName: "basic-demo-data-pr",
    deletionDetectionPolicy: new SoftDeleteColumnDeletionDetectionPolicy(
        softDeleteColumnName: "IsDeleted",
        softDeleteMarkerValue: "true"),
    description: "Demo files to demonstrate cognitive search capabilities.");
```

Now that you have initialized the `DataSource` object, create the data source. `SearchServiceClient` has a `DataSources` property. This property provides all the methods you need to create, list, update, or delete Azure Cognitive Search data sources.

For a successful request, the method will return the data source that was created. If there is a problem with the request, such as an invalid parameter, the method will throw an exception.

```csharp
try
{
    serviceClient.DataSources.CreateOrUpdate(dataSource);
}
catch (Exception e)
{
    // Handle the exception
}
``` -->

Crie e execute a solução. Uma vez que este é o seu primeiro pedido, consulte o portal Azure para confirmar que a fonte de dados foi criada na Pesquisa Cognitiva Azure. Na página do dashboard do serviço de pesquisa, verifique se o mosaico Origens de Dados tem um novo item. Poderá ter de aguardar alguns minutos para a página do portal atualizar.

  ![Fontes de dados azulejos no portal](./media/cognitive-search-tutorial-blob/data-source-tile.png "Fontes de dados azulejos no portal")

### <a name="step-2-create-a-skillset"></a>Passo 2: Criar uma habilidade

Nesta secção, define um conjunto de passos de enriquecimento que pretende aplicar aos seus dados. Cada passo de enriquecimento é chamado de *habilidade* e o conjunto de passos de enriquecimento um conjunto de *habilidades.* Este tutorial usa [habilidades cognitivas incorporadas](cognitive-search-predefined-skills.md) para o skillset:

+ [Reconhecimento ótico de caracteres](cognitive-search-skill-ocr.md) para reconhecer texto impresso e manuscrito em ficheiros de imagem.

+ [Fusão](cognitive-search-skill-textmerger.md) de texto para consolidar texto de uma coleção de campos num único campo.

+ [Deteção de Idioma](cognitive-search-skill-language-detection.md) para identificar o idioma do conteúdo.

+ [Text Split](cognitive-search-skill-textsplit.md) para quebrar grandes conteúdos em pedaços menores antes de chamar a habilidade de extração de frases-chave e a habilidade de reconhecimento da entidade. A extração de frases-chave e o reconhecimento da entidade aceitam inputs de 50.000 caracteres ou menos. Alguns dos ficheiros de exemplo precisam de ser divididos para caberem dentro deste limite.

+ [Reconhecimento de entidades](cognitive-search-skill-entity-recognition.md) para extrair os nomes das organizações de conteúdos no recipiente blob.

+ [Extração de Expressões-Chave](cognitive-search-skill-keyphrases.md) para solicitar as principais expressões-chaves.

Durante o processamento inicial, a Pesquisa Cognitiva Azure quebra cada documento para ler conteúdo a partir de diferentes formatos de ficheiros. O texto encontrado proveniente do ficheiro de origem é colocado num campo ```content``` gerado (um para cada documento). Como tal, detete a entrada como ```"/document/content"``` para utilizar este texto. 

As saídas podem ser mapeadas para um índice, utilizadas como entradas para uma competência a jusante ou ambas, como é o caso do código de idioma. No índice, o código de idioma é útil para a filtragem. Como entrada, o código de idioma é utilizado pelas competências de análise de texto para informar sobre as regras linguísticas em torno da separação de palavras.

Para obter mais informações acerca das noções básicas do conjunto de competências, veja [Como definir um conjunto de competências](cognitive-search-defining-skillset.md).

### <a name="ocr-skill"></a>Habilidade de OCR

A habilidade **OCR** extrai texto de imagens. Esta habilidade pressupõe que existe um campo normalized_images. Para gerar este campo, mais tarde no tutorial vamos definir a configuração ```"imageAction"``` na definição indexante para ```"generateNormalizedImages"```.

```csharp
private static OcrSkill CreateOcrSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry(
        name: "image",
        source: "/document/normalized_images/*"));

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry(
        name: "text",
        targetName: "text"));

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

### <a name="merge-skill"></a>Habilidade de fusão

Nesta secção, criará uma habilidade **de Fusão** que combina o campo de conteúdo de documentos com o texto produzido pela habilidade OCR.

```csharp
private static MergeSkill CreateMergeSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry(
        name: "text",
        source: "/document/content"));
    inputMappings.Add(new InputFieldMappingEntry(
        name: "itemsToInsert",
        source: "/document/normalized_images/*/text"));
    inputMappings.Add(new InputFieldMappingEntry(
        name: "offsets",
        source: "/document/normalized_images/*/contentOffset"));

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry(
        name: "mergedText",
        targetName: "merged_text"));

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

A habilidade de **Deteção de Idiomas** deteta a linguagem do texto de entrada e reporta um único código linguístico para cada documento submetido a pedido. Usaremos a saída da habilidade de **deteção** de idiomas como parte da entrada para a habilidade **text split.**

```csharp
private static LanguageDetectionSkill CreateLanguageDetectionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry(
        name: "text",
        source: "/document/merged_text"));

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry(
        name: "languageCode",
        targetName: "languageCode"));

    LanguageDetectionSkill languageDetectionSkill = new LanguageDetectionSkill(
        description: "Detect the language used in the document",
        context: "/document",
        inputs: inputMappings,
        outputs: outputMappings);

    return languageDetectionSkill;
}
```

### <a name="text-split-skill"></a>Habilidade dividida de texto

A habilidade abaixo **Split** dividirá texto por páginas e limitará o comprimento da página a 4.000 caracteres medido si `String.Length`. O algoritmo tentará dividir o texto em pedaços que são, no máximo, `maximumPageLength` tamanho. Neste caso, o algoritmo fará o seu melhor para quebrar a frase num limite de sentença, pelo que o tamanho do pedaço pode ser ligeiramente inferior a `maximumPageLength`.

```csharp
private static SplitSkill CreateSplitSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();

    inputMappings.Add(new InputFieldMappingEntry(
        name: "text",
        source: "/document/merged_text"));
    inputMappings.Add(new InputFieldMappingEntry(
        name: "languageCode",
        source: "/document/languageCode"));

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry(
        name: "textItems",
        targetName: "pages"));

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

### <a name="entity-recognition-skill"></a>Competência de reconhecimento de entidades

Esta `EntityRecognitionSkill` instância está definida para reconhecer o tipo de categoria `organization`. A habilidade de Reconhecimento de **Entidades** também pode reconhecer os tipos de categorias `person` e `location`.

Note que o campo "contexto" está definido para ```"/document/pages/*"``` com um asterisco, o que significa que o passo de enriquecimento é chamado para cada página sob ```"/document/pages"```.

```csharp
private static EntityRecognitionSkill CreateEntityRecognitionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry(
        name: "text",
        source: "/document/pages/*"));

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry(
        name: "organizations",
        targetName: "organizations"));

    List<EntityCategory> entityCategory = new List<EntityCategory>();
    entityCategory.Add(EntityCategory.Organization);

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

### <a name="key-phrase-extraction-skill"></a>Habilidade de extração de frase chave

Tal como o `EntityRecognitionSkill` instância que acabou de ser criada, a habilidade de extração de **frases-chave** é chamada para cada página do documento.

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

### <a name="build-and-create-the-skillset"></a>Construir e criar a habilidade

Construa o `Skillset` usando as habilidades que criou.

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

Adicione as seguintes linhas ao Main.

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
    List<Skill> skills = new List<Skill>();
    skills.Add(ocrSkill);
    skills.Add(mergeSkill);
    skills.Add(languageDetectionSkill);
    skills.Add(splitSkill);
    skills.Add(entityRecognitionSkill);
    skills.Add(keyPhraseExtractionSkill);

    Skillset skillset = CreateOrUpdateDemoSkillSet(serviceClient, skills);
```

### <a name="step-3-create-an-index"></a>Passo 3: Criar um índice

Nesta secção, pode definir o esquema de índice ao especificar os campos a incluir no índice pesquisável e os atributos de pesquisa para cada campo. Os campos têm um tipo e podem ter atributos que determinam a forma como o campo é utilizado (pesquisável, ordenável e assim por diante). Os nomes dos campos num índice não têm necessariamente de corresponder aos nomes dos campos na origem. Num passo posterior, vai adicionar mapeamentos de campos num indexador para ligar campos de origem-destino. Para este passo, defina o índice com convenções de nomenclatura de campo relevantes para a aplicação de pesquisa.

Neste exercício, utiliza os seguintes campos e tipos de campo:

| nomes de campo: | `id`       | conteúdo   | languageCode | keyPhrases         | organizações     |
|--------------|----------|-------|----------|--------------------|-------------------|
| tipos de campo: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |


#### <a name="create-demoindex-class"></a>Criar classe DemoIndex

Os campos para este índice são definidos usando uma classe modelo. Cada propriedade da classe do modelo tem atributos que determinam os comportamentos relacionados com a pesquisa do campo de índice correspondente. 

Vamos adicionar a classe modelo C# a um novo ficheiro. Clique no seu projeto e selecione **Adicionar** > **Novo Item...** selecione "Class" e nomeie o ficheiro `DemoIndex.cs`, em seguida, selecione **Adicionar**.

Certifique-se de que pretende utilizar tipos dos espaços de nome `Microsoft.Azure.Search` e `Microsoft.Azure.Search.Models`.

Adicione a definição de classe de modelo abaixo para `DemoIndex.cs` e inclua-a no mesmo espaço de nome onde irá criar o índice.

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

<!-- Add the below model class definition to `DemoIndex.cs` and include it in the same namespace where you'll create the index.

```csharp
// The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Cognitive Search .NET SDK.
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
``` -->

Agora que definiu uma classe modelo, de volta ao `Program.cs` pode criar uma definição de índice facilmente. O nome deste índice será `demoindex`. Se já existir um índice com esse nome, será eliminado.

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

Adicione as seguintes linhas ao Main.

```csharp
    // Create the index
    Console.WriteLine("Creating the index...");
    Index demoIndex = CreateDemoIndex(serviceClient);
```

<!-- ```csharp
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
    // Handle exception
}
```
 -->

Para saber mais sobre a definição de um índice, consulte [Create Index (Azure Cognitive Search REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index).

### <a name="step-4-create-and-run-an-indexer"></a>Passo 4: Criar e executar um indexante

Até ao momento, criou uma origem de dados, um conjunto de competências e um índice. Estes três componentes tornam-se parte de um [indexador](search-indexer-overview.md) que une cada uma das peças numa única operação de várias fases. Para as associar num indexador, tem de definir os mapeamentos de campo.

+ Os mapeamentos de campo são processados antes do skillset, mapeando os campos de origem da fonte de dados para os campos-alvo em um índice. Se os nomes e tipos de campo forem os mesmos em ambas as extremidades, não é necessário mapeamento.

+ Os outputFieldMappings são processados após o skillset, referenciando sourceFieldNames que não existem até que a rachadura de documentos ou o enriquecimento os crie. O targetFieldName é um campo num índice.

Além de ligar as inputs às saídas, também pode utilizar mapeamentos de campo para aplainar estruturas de dados. Para mais informações, consulte [Como mapear campos enriquecidos para um índice pesquisável](cognitive-search-output-field-mapping.md).

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

    List<FieldMapping> fieldMappings = new List<FieldMapping>();
    fieldMappings.Add(new FieldMapping(
        sourceFieldName: "metadata_storage_path",
        targetFieldName: "id",
        mappingFunction: new FieldMappingFunction(
            name: "base64Encode")));
    fieldMappings.Add(new FieldMapping(
        sourceFieldName: "content",
        targetFieldName: "content"));

    List<FieldMapping> outputMappings = new List<FieldMapping>();
    outputMappings.Add(new FieldMapping(
        sourceFieldName: "/document/pages/*/organizations/*",
        targetFieldName: "organizations"));
    outputMappings.Add(new FieldMapping(
        sourceFieldName: "/document/pages/*/keyPhrases/*",
        targetFieldName: "keyPhrases"));
    outputMappings.Add(new FieldMapping(
        sourceFieldName: "/document/languageCode",
        targetFieldName: "languageCode"));

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
Adicione as seguintes linhas ao Main.

```csharp
    // Create the indexer, map fields, and execute transformations
    Console.WriteLine("Creating the indexer...");
    Indexer demoIndexer = CreateDemoIndexer(serviceClient, dataSource, skillset, demoIndex);
```

Espere que criar o indexante levará algum tempo para completar. Apesar de o conjunto de dados ser pequeno, as competências analíticas realizam um processo de computação intensa. Algumas competências, como a análise de imagem, são de execução longa.

> [!TIP]
> A criação de um indexador invoca o pipeline. Se houver problemas em atingir os dados, com o mapeamento de entradas e saídas ou com a ordem das operações, estes vão surgir nesta fase.

### <a name="explore-creating-the-indexer"></a>Explore a criação do indexador

O código define ```"maxFailedItems"``` a -1, o que instrui o motor de indexação a ignorar erros durante a importação de dados. Esta definição é útil pois existem poucos documentos na origem de dados de demonstração. Para uma origem de dados maior, deve definir o valor com um número maior que 0.

Note também que o ```"dataToExtract"``` está definido para ```"contentAndMetadata"```. Esta instrução informa o indexador para extrair automaticamente o conteúdo a partir de formatos de ficheiros diferentes, bem como de metadados relativos a cada ficheiro.

Quando o conteúdo é extraído, pode definir `imageAction` para extrair texto das imagens existentes na origem de dados. O ```"imageAction"``` definido para ```"generateNormalizedImages"``` configuração, combinado com a Habilidade De OCR e habilidade de fusão de texto, diz ao indexador para extrair texto das imagens (por exemplo, a palavra "parar" de um sinal de paragem de tráfego), e incorpora-o como parte do campo de conteúdo. Este comportamento aplica-se tanto às imagens incorporadas nos documentos (tal como uma imagem num PDF) como às imagens existentes na origem de dados, por exemplo, um ficheiro JPG.

<a name="check-indexer-status"></a>

## <a name="4---monitor-indexing"></a>4 - Monitorização da indexação

Assim que o indexador é definido, este é executado automaticamente quando submete o pedido. Consoante as competências cognitivas que definiu, a indexação pode demorar mais do que o esperado. Para saber se o indexante ainda está em funcionamento, utilize o método `GetStatus`.

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

Adicione as seguintes linhas ao Main.

```csharp
    // Check indexer overall status
    Console.WriteLine("Check the indexer overall status...");
    CheckIndexerOverallStatus(serviceClient, demoIndexer);
```
 
## <a name="5---search"></a>5 - Pesquisa

Após a classificação estar terminada, pode executar consultas que devolvam o conteúdo de campos individuais. Por padrão, a Pesquisa Cognitiva Azure devolve os 50 melhores resultados. Os dados de exemplo são pequenos, pelo que a predefinição funciona bem. No entanto, ao trabalhar com conjuntos de dados maiores, poderá ter de incluir parâmetros na cadeia de consulta para devolver mais resultados. Para obter instruções, consulte [como página resultados em Pesquisa Cognitiva Azure](search-pagination-page-layout.md).

Como passo de verificação, consulte o índice de todos os campos.

Adicione as seguintes linhas ao Main.

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

`CreateSearchIndexClient` cria um novo `SearchIndexClient` utilizando valores que são armazenados no ficheiro config da aplicação (appsettings.json). Note que a chave API do serviço de pesquisa é utilizada e não a chave de administração.

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
   string searchServiceName = configuration["SearchServiceName"];
   string queryApiKey = configuration["SearchServiceQueryApiKey"];

   SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "demoindex", new SearchCredentials(queryApiKey));
   return indexClient;
}
```

Adicione o seguinte código ao Main. O primeiro try-catch devolve a definição de índice, com o nome, tipo e atributos de cada campo. A segunda é uma consulta parametrizada, onde `Select` especifica quais os campos a incluir nos resultados, por exemplo, `organizations`. Uma cadeia de pesquisa de `"*"` devolve todos os conteúdos de um único campo.

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

Repita para campos adicionais: conteúdo, idiomaCódigo, tecladoS e organizações neste exercício. Pode devolver vários campos através da propriedade [Select](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters.select?view=azure-dotnet) utilizando uma lista de limitada de vírvia.

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Repor e executar novamente

Nas fases experimentais iniciais de desenvolvimento, a abordagem mais prática para a iteração do design é apagar os objetos da Pesquisa Cognitiva Azure e permitir que o seu código os reconstrua. Os nomes dos recursos são exclusivos. Quando elimina um objeto, pode recriá-lo com o mesmo nome.

O código de amostra para este tutorial verifica os objetos existentes e elimina-os para que possa reexecutar o seu código.

Também pode usar o portal para eliminar índices, indexadores, fontes de dados e habilidades.

## <a name="takeaways"></a>Conclusões

Este tutorial demonstrou os passos básicos para a construção de um gasoduto de indexação enriquecido através da criação de componentes: uma fonte de dados, skillset, indexado e indexante.

Foram introduzidas [competências incorporadas,](cognitive-search-predefined-skills.md) juntamente com a definição de skillset e a mecânica das habilidades de acorrentar através de inputs e saídas. Também descobriu que `outputFieldMappings` na definição de indexante é necessário para encaminhar valores enriquecidos do oleoduto para um índice pesquisável num serviço de Pesquisa Cognitiva Azure.

Por fim, aprendeu como testar os resultados e repor o sistema para iterações futuras. Aprendeu que a emissão de consultas acerca do índice devolve o resultado criado pelo pipeline de indexação melhorado. Também aprendeu como verificar o estado do indexador e quais os objetos a eliminar antes de executar novamente um pipeline.

## <a name="clean-up-resources"></a>Limpar recursos

Quando se trabalha na sua própria subscrição, no final de um projeto, é uma boa ideia remover os recursos de que já não precisa. Os recursos deixados a funcionar podem custar-lhe dinheiro. Pode eliminar os recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Pode encontrar e gerir recursos no portal, utilizando a ligação De Todos os recursos ou grupos de Recursos no painel de navegação à esquerda.

## <a name="next-steps"></a>Passos seguintes

Agora que está familiarizado com todos os objetos num oleoduto de enriquecimento de IA, vamos ver mais de perto as definições de habilidades e habilidades individuais.

> [!div class="nextstepaction"]
> [Como criar uma habilidade](cognitive-search-defining-skillset.md)
