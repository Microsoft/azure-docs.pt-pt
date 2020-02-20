---
title: 'Tutorial: Criar um C# skillset na utilização de .NET'
titleSuffix: Azure Cognitive Search
description: Pise no código de exemplo mostrando a extração de dados, a linguagem natural e o processamento de IA de imagem num pipeline de indexação de enriquecimento de pesquisa cognitiva Azure.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: efd4a9333b5fb02c18b2f6a6d0f8ce58bfb8f220
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472388"
---
# <a name="tutorial-create-an-ai-enrichment-pipeline-using-c-and-the-net-sdk"></a>Tutorial: Criar um oleoduto C# de enriquecimento de IA utilizando e o .NET SDK

Neste tutorial, aprende-se a mecânica do enriquecimento de dados de programação na Pesquisa Cognitiva Azure utilizando *habilidades cognitivas.* As competências são apoiadas pelo processamento de linguagem natural (NLP) e pelas capacidades de análise de imagem nos Serviços Cognitivos. Através da composição e configuração do skillset, pode extrair representações de texto e texto de uma imagem ou ficheiro de documento digitalizado. Também pode detetar linguagem, entidades, frases-chave e muito mais. O resultado final é rico em conteúdo adicional num índice de pesquisa, criado por um pipeline de indexação movido a IA.

Neste tutorial, utiliza o .NET SDK para executar as seguintes tarefas:

> [!div class="checklist"]
> * Criar um pipeline de indexação para melhorar os dados de exemplo na rota para um índice
> * Aplicar competências incorporadas: reconhecimento ótico de caracteres, fusão de texto, deteção de linguagem, divisão de texto, reconhecimento de entidades, extração de frases-chave
> * Saber como encadear competências em conjunto através do mapeamento de entradas para saídas num conjunto de competências
> * Executar pedidos e rever resultados
> * Repor o índice e os indexadores para desenvolvimento adicional

A saída é um índice pesquisável de texto completo na Pesquisa Cognitiva Azure. Pode melhorar o índice com outras funcionalidades standard, tais como [sinónimos](search-synonyms.md), [perfis de classificação](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [analisadores](search-analyzers.md) e [filtros](search-filters.md).

Este tutorial funciona no serviço Gratuito, mas o número de transações gratuitas é limitado a 20 documentos por dia. Se quiser executar este tutorial mais de uma vez no mesmo dia, elimine o indexante para repor o contador.

> [!NOTE]
> À medida que expande o âmbito aumentando a frequência do processamento, adicionando mais documentos, ou adicionando mais algoritmos de IA, terá de anexar um recurso de Serviços Cognitivos faturados. As acusações acumulam-se quando se ligam para apis em Serviços Cognitivos, e para extração de imagem como parte da fase de quebra de documentos na Pesquisa Cognitiva Azure. Não há encargos para a extração de texto de documentos.
>
> A execução de competências incorporadas é cobrada pelo preço de pagamento dos [Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/) existentes. Os preços de extração de imagem são descritos na página de preços da [Pesquisa Cognitiva Azure](https://go.microsoft.com/fwlink/?linkid=2042400).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes serviços, ferramentas e dados são utilizados neste tutorial. 

+ [Crie uma conta](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) de armazenamento Azure para armazenar os dados da amostra. Certifique-se de que a conta de armazenamento está na mesma região que a Pesquisa Cognitiva Azure.

+ [Os dados](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) da amostra consistem num pequeno conjunto de ficheiros de diferentes tipos. 

+ [Instale](https://visualstudio.microsoft.com/) o Estúdio Visual para utilizar como IDE.

+ [Crie um serviço de Pesquisa Cognitiva Azure](search-create-service-portal.md) ou [encontre um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) sob a sua subscrição atual. Você pode usar um serviço gratuito para este tutorial.

## <a name="get-a-key-and-url"></a>Obtenha uma chave e URL

Para interagir com o seu serviço de Pesquisa Cognitiva Azure vai precisar do URL de serviço e de uma chave de acesso. Um serviço de pesquisa é criado com ambos, por isso, se você adicionar Pesquisa Cognitiva Azure à sua subscrição, siga estes passos para obter as informações necessárias:

1. [Inscreva-se no portal Azure](https://portal.azure.com/), e na página de **visão geral** do seu serviço de pesquisa, obtenha o URL. Um ponto final de exemplo poderá ser parecido com `https://mydemo.search.windows.net`.

1. Em **Definições** > **Teclas,** obtenha uma chave de administração para todos os direitos sobre o serviço. Existem duas chaves de administração intercambiáveis, previstas para a continuidade do negócio no caso de precisar de rolar uma. Pode utilizar a chave primária ou secundária nos pedidos de adição, modificação e aparas de objetos.

   ![Obtenha um ponto final http e chave de acesso](media/search-get-started-postman/get-url-key.png "Obtenha um ponto final http e chave de acesso")

Ter uma chave válida estabelece fidedignidade, numa base por pedido, entre a aplicação a enviar o pedido e o serviço que o processa.

## <a name="prepare-sample-data"></a>Preparar dados da amostra

O pipeline de melhoramento solicita conteúdo das origens de dados do Azure. Os dados de origem devem ter origem num tipo de fonte de dados suportado de um [indexador](search-indexer-overview.md)de pesquisa cognitiva Azure . Para este exercício, vamos utilizar o armazenamento de blobs para demonstrar os vários tipos de conteúdo.

1. [Inscreva-se no portal Azure,](https://portal.azure.com)navegue na sua conta de armazenamento Azure, clique em **Blobs,** e depois clique em **+ Recipiente**.

1. [Crie um recipiente Blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) para conter dados da amostra. Pode definir o Nível de Acesso Público a qualquer um dos seus valores válidos. Este tutorial assume que o nome do recipiente é "basic-demo-data-pr".

1. Depois de criado o recipiente, abra-o e **selecione Upload** na barra de comando para carregar os dados da [amostra](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4).

   ![Ficheiros de origem no armazenamento de blobs do Azure](./media/cognitive-search-quickstart-blob/sample-data.png)

1. Depois de os ficheiros de exemplo serem carregados, obtenha o nome do contentor e uma cadeia de ligação para o seu armazenamento de Blobs. Pode fazê-lo navegando na sua conta de armazenamento no portal Azure, selecionando **teclas de Acesso**e, em seguida, copiar o campo **De linha de ligação.**

   A cadeia de ligação deve ter um URL semelhante ao seguinte exemplo:

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

Existem outras formas de especificar a cadeia de ligação, por exemplo, fornecer uma assinatura de acesso partilhada. Para saber mais sobre as credenciais da origem de dados, veja [Indexar o Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md#Credentials).

## <a name="set-up-your-environment"></a>Configurar o ambiente

Comece por abrir o Visual Studio e criar um novo projeto de App console que possa funcionar em .NET Core.

### <a name="install-nuget-packages"></a>Instalar pacotes NuGet

O [Azure Cognitive Search .NET SDK](https://aka.ms/search-sdk) é composto por algumas bibliotecas de clientes que lhe permitem gerir os seus índices, fontes de dados, indexadores e competências, bem como carregar e gerir documentos e executar consultas, tudo sem ter de lidar com os detalhes de HTTP e JSON. Estas bibliotecas de clientes estão todas distribuídas como pacotes NuGet.

Para este projeto, terá de instalar a versão 9 do pacote nuGet `Microsoft.Azure.Search` e o mais recente pacote NuGet `Microsoft.Extensions.Configuration.Json`.

Instale o pacote nuGet `Microsoft.Azure.Search` utilizando a consola Package Manager no Estúdio Visual. Para abrir a consola do Gestor de **Pacotes,** selecione Ferramentas > **NuGet Package Manager** > **Consola de Gestor de Pacotes**. Para obter o comando executado, navegue para a página de [pacotes Microsoft.Azure.Search NuGet](https://www.nuget.org/packages/Microsoft.Azure.Search), selecione a versão 9 e copie o comando do Gestor de Pacotes. Na consola do Gestor de Pacotes, execute este comando.

Para instalar o pacote nuGet `Microsoft.Extensions.Configuration.Json` no Estúdio Visual, selecione **Tools** > **NuGet Package Manager** > Gerir **pacotes NuGet para solução...** . Selecione Procurar e procurar o pacote nuGet `Microsoft.Extensions.Configuration.Json`. Assim que o encontrar, selecione o pacote, selecione o seu projeto, confirme que a versão é a versão mais recente e estável e, em seguida, selecione Instalar.

## <a name="add-azure-cognitive-search-service-information"></a>Adicione informações do serviço de pesquisa cognitiva Azure

Para se ligar ao seu serviço de Pesquisa Cognitiva Azure, terá de adicionar as informações do serviço de pesquisa ao seu projeto. Clique no seu projeto no Solution Explorer e selecione **Adicionar** > **Novo Item...** . Nomeie o ficheiro `appsettings.json` e selecione **Adicionar**. 

Este ficheiro terá de ser incluído no seu diretório de saída. Para isso, clique à direita na `appsettings.json` e selecione **Propriedades**. Alterar o valor de **Copy para 'Diretório de Saída'** para **Copiar se for mais recente**.

Copie o JSON abaixo no seu novo ficheiro JSON.

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "SearchServiceQueryApiKey": "Put your query API key here",
  "AzureBlobConnectionString": "Put your Azure Blob connection string here",
}
```

Adicione o seu serviço de pesquisa e informações de conta de armazenamento blob.

Pode obter os seus dados do serviço de pesquisa na sua página de conta de pesquisa no portal Azure. O nome da conta estará na página principal e as teclas podem ser encontradas selecionando **Teclas**.

Pode obter a cadeia de ligação blob navegando para a sua conta de armazenamento no portal Azure, selecionando **teclas de Acesso**e, em seguida, copiando o campo **De linha de ligação.**

## <a name="add-namespaces"></a>Adicionar espaços de nome

Este tutorial utiliza vários tipos diferentes de vários espaços de nome. Para utilizar estes tipos, adicione o seguinte à `Program.cs`.

```csharp
using System;
using System.Collections.Generic;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Extensions.Configuration;
```

## <a name="create-a-client"></a>Criar um cliente

Criar um exemplo da classe `SearchServiceClient`.

```csharp
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
> 

## <a name="create-a-data-source"></a>Criar uma origem de dados

Crie um novo exemplo `DataSource` chamando `DataSource.AzureBlobStorage`. `DataSource.AzureBlobStorage` requer que especifique o nome da fonte de dados, a cadeia de ligação e o nome do recipiente blob.

Embora não seja usado neste tutorial, uma política de eliminação suave também é definida que é usada para identificar bolhas apagadas com base no valor de uma coluna de eliminação suave. A seguinte política considera que uma bolha deve ser eliminada se tiver uma propriedade de metadados `IsDeleted` com o valor `true`.

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

Agora que ininicializou o objeto `DataSource`, crie a fonte de dados. `SearchServiceClient` tem uma propriedade `DataSources`. Esta propriedade fornece todos os métodos necessários para criar, listar, atualizar ou eliminar fontes de dados de Pesquisa Cognitiva Azure.

Para um pedido bem sucedido, o método devolverá a fonte de dados que foi criada. Se houver algum problema com o pedido, como um parâmetro inválido, o método lançará uma exceção.

```csharp
try
{
    serviceClient.DataSources.CreateOrUpdate(dataSource);
}
catch (Exception e)
{
    // Handle the exception
}
```

Uma vez que este é o seu primeiro pedido, consulte o portal Azure para confirmar que a fonte de dados foi criada na Pesquisa Cognitiva Azure. Na página do dashboard do serviço de pesquisa, verifique se o mosaico Origens de Dados tem um novo item. Poderá ter de aguardar alguns minutos para a página do portal atualizar.

  ![Fontes de dados azulejos no portal](./media/cognitive-search-tutorial-blob/data-source-tile.png "Fontes de dados azulejos no portal")

## <a name="create-a-skillset"></a>Criar um conjunto de competências

Nesta secção, define um conjunto de passos de enriquecimento que pretende aplicar aos seus dados. Cada passo de enriquecimento é chamado de *habilidade* e o conjunto de passos de enriquecimento um conjunto de *habilidades.* Este tutorial usa [habilidades cognitivas incorporadas](cognitive-search-predefined-skills.md) para o skillset:

+ [Reconhecimento ótico de caracteres](cognitive-search-skill-ocr.md) para reconhecer texto impresso e manuscrito em ficheiros de imagem.

+ [Fusão](cognitive-search-skill-textmerger.md) de texto para consolidar texto de uma coleção de campos num único campo.

+ [Deteção de Idioma](cognitive-search-skill-language-detection.md) para identificar o idioma do conteúdo.

+ [Text Split](cognitive-search-skill-textsplit.md) para quebrar grandes conteúdos em pedaços menores antes de chamar a habilidade de extração de frases-chave e a habilidade de reconhecimento da entidade. A extração de frases-chave e o reconhecimento da entidade aceitam inputs de 50.000 caracteres ou menos. Alguns dos ficheiros de exemplo precisam de ser divididos para caberem dentro deste limite.

+ [Reconhecimento de entidades](cognitive-search-skill-entity-recognition.md) para extrair os nomes das organizações de conteúdos no recipiente blob.

+ [Extração de Expressões-Chave](cognitive-search-skill-keyphrases.md) para solicitar as principais expressões-chaves.

Durante o processamento inicial, a Pesquisa Cognitiva Azure quebra cada documento para ler conteúdo a partir de diferentes formatos de ficheiros. O texto encontrado proveniente do ficheiro de origem é colocado num campo ```content``` gerado (um para cada documento). Como tal, detete a entrada para que ```"/document/content"``` utilize este texto. 

As saídas podem ser mapeadas para um índice, utilizadas como entradas para uma competência a jusante ou ambas, como é o caso do código de idioma. No índice, o código de idioma é útil para a filtragem. Como entrada, o código de idioma é utilizado pelas competências de análise de texto para informar sobre as regras linguísticas em torno da separação de palavras.

Para obter mais informações acerca das noções básicas do conjunto de competências, veja [Como definir um conjunto de competências](cognitive-search-defining-skillset.md).

### <a name="ocr-skill"></a>Habilidade de OCR

A habilidade **OCR** extrai texto de imagens. Esta habilidade pressupõe que existe um campo normalized_images. Para gerar este campo, mais tarde no tutorial vamos definir a configuração ```"imageAction"``` na definição indexante para ```"generateNormalizedImages"```.

```csharp
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
```

### <a name="merge-skill"></a>Habilidade de fusão

Nesta secção, criará uma habilidade **de Fusão** que combina o campo de conteúdo de documentos com o texto produzido pela habilidade OCR.

```csharp
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
```

### <a name="language-detection-skill"></a>Habilidade de deteção de linguagem

A habilidade de **Deteção de Idiomas** deteta a linguagem do texto de entrada e reporta um único código linguístico para cada documento submetido a pedido. Usaremos a saída da habilidade de **deteção** de idiomas como parte da entrada para a habilidade **text split.**

```csharp
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
```

### <a name="text-split-skill"></a>Habilidade dividida de texto

A habilidade abaixo **Split** dividirá texto por páginas e limitará o comprimento da página a 4.000 caracteres medido si `String.Length`. O algoritmo tentará dividir o texto em pedaços que são, no máximo, `maximumPageLength` tamanho. Neste caso, o algoritmo fará o seu melhor para quebrar a frase num limite de sentença, pelo que o tamanho do pedaço pode ser ligeiramente inferior a `maximumPageLength`.

```csharp
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
```

### <a name="entity-recognition-skill"></a>Competência de reconhecimento de entidades

Esta `EntityRecognitionSkill` instância está definida para reconhecer o tipo de categoria `organization`. A habilidade de Reconhecimento de **Entidades** também pode reconhecer os tipos de categorias `person` e `location`.

Note que o campo "contexto" está definido para ```"/document/pages/*"``` com um asterisco, o que significa que o passo de enriquecimento é chamado para cada página sob ```"/document/pages"```.

```csharp
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
```

### <a name="key-phrase-extraction-skill"></a>Habilidade de extração de frase chave

Tal como o `EntityRecognitionSkill` instância que acabou de ser criada, a habilidade de extração de **frases-chave** é chamada para cada página do documento.

```csharp
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
```

### <a name="build-and-create-the-skillset"></a>Construir e criar a habilidade

Construa o `Skillset` usando as habilidades que criou.

```csharp
List<Skill> skills = new List<Skill>();
skills.Add(ocrSkill);
skills.Add(mergeSkill);
skills.Add(languageDetectionSkill);
skills.Add(splitSkill);
skills.Add(entityRecognitionSkill);
skills.Add(keyPhraseExtractionSkill);

Skillset skillset = new Skillset(
    name: "demoskillset",
    description: "Demo skillset",
    skills: skills);
```

Crie a habilidade no seu serviço de pesquisa.

```csharp
try
{
    serviceClient.Skillsets.CreateOrUpdate(skillset);
}
catch (Exception e)
{
    // Handle exception
}
```

## <a name="create-an-index"></a>Criar um índice

Nesta secção, pode definir o esquema de índice ao especificar os campos a incluir no índice pesquisável e os atributos de pesquisa para cada campo. Os campos têm um tipo e podem ter atributos que determinam a forma como o campo é utilizado (pesquisável, ordenável e assim por diante). Os nomes dos campos num índice não têm necessariamente de corresponder aos nomes dos campos na origem. Num passo posterior, vai adicionar mapeamentos de campos num indexador para ligar campos de origem-destino. Para este passo, defina o índice com convenções de nomenclatura de campo relevantes para a aplicação de pesquisa.

Neste exercício, utiliza os seguintes campos e tipos de campo:

| nomes de campo: | `id`       | conteúdo   | languageCode | keyPhrases         | organizações     |
|--------------|----------|-------|----------|--------------------|-------------------|
| tipos de campo: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |


### <a name="create-demoindex-class"></a>Criar classe DemoIndex

Os campos para este índice são definidos usando uma classe modelo. Cada propriedade da classe do modelo tem atributos que determinam os comportamentos relacionados com a pesquisa do campo de índice correspondente. 

Vamos adicionar a classe modelo C# a um novo ficheiro. Clique no seu projeto e selecione **Adicionar** > **Novo Item...** selecione "Class" e nomeie o ficheiro `DemoIndex.cs`, em seguida, selecione **Adicionar**.

Certifique-se de que pretende utilizar tipos dos espaços de nome `Microsoft.Azure.Search` e `Microsoft.Azure.Search.Models`.

```csharp
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
```

Adicione a definição de classe de modelo abaixo para `DemoIndex.cs` e inclua-a no mesmo espaço de nome onde irá criar o índice.

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
```

Agora que definiu uma classe modelo, de volta ao `Program.cs` pode criar uma definição de índice facilmente. O nome deste índice será "demoindex".

```csharp
var index = new Index()
{
    Name = "demoindex",
    Fields = FieldBuilder.BuildForType<DemoIndex>()
};
```

Durante os testes poderá descobrir que está a tentar criar o índice mais de uma vez. Por causa disso, verifique se o índice que está prestes a criar já existe antes de tentar criá-lo.

```csharp
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

Para saber mais sobre a definição de um índice, consulte [Create Index (Azure Cognitive Search REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index).

## <a name="create-an-indexer-map-fields-and-execute-transformations"></a>Criar um indexador, mapear campos e executar transformações

Até ao momento, criou uma origem de dados, um conjunto de competências e um índice. Estes três componentes tornam-se parte de um [indexador](search-indexer-overview.md) que une cada uma das peças numa única operação de várias fases. Para as associar num indexador, tem de definir os mapeamentos de campo.

+ Os mapeamentos de campo são processados antes do skillset, mapeando os campos de origem da fonte de dados para os campos-alvo em um índice. Se os nomes e tipos de campo forem os mesmos em ambas as extremidades, não é necessário mapeamento.

+ Os outputFieldMappings são processados após o skillset, referenciando sourceFieldNames que não existem até que a rachadura de documentos ou o enriquecimento os crie. O targetFieldName é um campo num índice.

Além de ligar as inputs às saídas, também pode utilizar mapeamentos de campo para aplainar estruturas de dados. Para mais informações, consulte [Como mapear campos enriquecidos para um índice pesquisável](cognitive-search-output-field-mapping.md).

```csharp
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
    // Handle exception
}
```

Espere que criar o indexante levará algum tempo para completar. Apesar de o conjunto de dados ser pequeno, as competências analíticas realizam um processo de computação intensa. Algumas competências, como a análise de imagem, são de execução longa.

> [!TIP]
> A criação de um indexador invoca o pipeline. Se houver problemas em atingir os dados, com o mapeamento de entradas e saídas ou com a ordem das operações, estes vão surgir nesta fase.

### <a name="explore-creating-the-indexer"></a>Explore a criação do indexador

O código define ```"maxFailedItems"``` a -1, o que instrui o motor de indexação a ignorar erros durante a importação de dados. Esta definição é útil pois existem poucos documentos na origem de dados de demonstração. Para uma origem de dados maior, deve definir o valor com um número maior que 0.

Note também que o ```"dataToExtract"``` está definido para ```"contentAndMetadata"```. Esta instrução informa o indexador para extrair automaticamente o conteúdo a partir de formatos de ficheiros diferentes, bem como de metadados relativos a cada ficheiro.

Quando o conteúdo é extraído, pode definir `imageAction` para extrair texto das imagens existentes na origem de dados. O ```"imageAction"``` definido para ```"generateNormalizedImages"``` configuração, combinado com a Habilidade De OCR e habilidade de fusão de texto, diz ao indexador para extrair texto das imagens (por exemplo, a palavra "parar" de um sinal de paragem de tráfego), e incorpora-o como parte do campo de conteúdo. Este comportamento aplica-se tanto às imagens incorporadas nos documentos (tal como uma imagem num PDF) como às imagens existentes na origem de dados, por exemplo, um ficheiro JPG.

## <a name="check-indexer-status"></a>Verificar o estado do indexador

Assim que o indexador é definido, este é executado automaticamente quando submete o pedido. Consoante as competências cognitivas que definiu, a indexação pode demorar mais do que o esperado. Para saber se o indexante ainda está em funcionamento, utilize o método `GetStatus`.

```csharp
try
{
    IndexerExecutionInfo demoIndexerExecutionInfo = serviceClient.Indexers.GetStatus(indexer.Name);

    switch (demoIndexerExecutionInfo.Status)
    {
        case IndexerStatus.Error:
            Console.WriteLine("Indexer has error status");
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
    // Handle exception
}
```

`IndexerExecutionInfo` representa o estado atual e o histórico de execução de um indexante.

Os avisos são comuns com algumas combinações de ficheiros de origem e competências e nem sempre indicam um problema. Neste tutorial, os avisos são benignos (por exemplo, os ficheiros JPEG não têm entradas de texto).
 
## <a name="query-your-index"></a>Consultar o índice

Após a classificação estar terminada, pode executar consultas que devolvam o conteúdo de campos individuais. Por padrão, a Pesquisa Cognitiva Azure devolve os 50 melhores resultados. Os dados de exemplo são pequenos, pelo que a predefinição funciona bem. No entanto, ao trabalhar com conjuntos de dados maiores, poderá ter de incluir parâmetros na cadeia de consulta para devolver mais resultados. Para obter instruções, consulte [como página resultados em Pesquisa Cognitiva Azure](search-pagination-page-layout.md).

Como passo de verificação, consulte o índice de todos os campos.

```csharp
DocumentSearchResult<DemoIndex> results;

ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*");
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

O resultado é o esquema de índice, com o nome, o tipo e os atributos de cada campo.

Submeta uma segunda consulta para `"*"` devolver todos os conteúdos de um único campo, tal como `organizations`.

```csharp
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

Repita para campos adicionais: conteúdo, idiomaCódigo, tecladoS e organizações neste exercício. Pode devolver vários campos através de `$select` com uma lista delimitada por vírgulas.

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Repor e executar novamente

Nas fases experimentais iniciais de desenvolvimento, a abordagem mais prática para iterações de design é apagar os objetos da Pesquisa Cognitiva Azure e permitir que o seu código os reconstrua. Os nomes dos recursos são exclusivos. Quando elimina um objeto, pode recriá-lo com o mesmo nome.

Este tutorial tratou de verificar os indexadores e índices existentes e apagando-os se já existiam para que possa reexecutar o seu código.

Também pode usar o portal para eliminar índices, indexadores e habilidades.

À medida que o seu código evoluiu, pode querer refinar uma estratégia de reconstrução. Para obter mais informações, veja [Como reconstruir um índice](search-howto-reindex.md).

## <a name="takeaways"></a>Conclusões

Este tutorial demonstrou os passos básicos para a construção de um gasoduto de indexação enriquecido através da criação de componentes: uma fonte de dados, skillset, indexado e indexante.

Foram introduzidas [competências incorporadas,](cognitive-search-predefined-skills.md) juntamente com a definição de skillset e a mecânica das habilidades de acorrentar através de inputs e saídas. Também descobriu que `outputFieldMappings` na definição de indexante é necessário para encaminhar valores enriquecidos do oleoduto para um índice pesquisável num serviço de Pesquisa Cognitiva Azure.

Por fim, aprendeu como testar os resultados e repor o sistema para iterações futuras. Aprendeu que a emissão de consultas acerca do índice devolve o resultado criado pelo pipeline de indexação melhorado. Também aprendeu como verificar o estado do indexador e quais os objetos a eliminar antes de executar novamente um pipeline.

## <a name="clean-up-resources"></a>Limpar recursos

A forma mais rápida de limpar depois de um tutorial é apagando o grupo de recursos que contém o serviço de Pesquisa Cognitiva Azure e o serviço Azure Blob. Assumindo que coloca ambos os serviços no mesmo grupo, elimine o grupo de recursos agora para eliminar definitivamente todo o seu conteúdo, incluindo os serviços e quaisquer conteúdos armazenados criados para este tutorial. No portal, o nome do grupo de recursos está na página Descrição geral de cada serviço.

## <a name="next-steps"></a>Passos seguintes

Personalize ou expanda o pipeline com competências personalizadas. A criação de uma competência personalizada e a sua adição a um conjunto de competências permite-lhe carregar análises de texto ou imagem que escreveu.

> [!div class="nextstepaction"]
> [Exemplo: Criar uma habilidade personalizada para enriquecimento de IA](cognitive-search-create-custom-skill-example.md)
