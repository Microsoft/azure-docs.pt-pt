---
title: C#Tutorial para chamar as APIs serviços cognitivos num pipeline de indexação - Azure Search
description: Neste tutorial, acompanhe um exemplo de extração de dados, linguagem natural e processamento de imagem por IA na indexação do Azure Search para extração e transformação de dados.
manager: eladz
author: MarkHeff
services: search
ms.service: search
ms.devlang: NA
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: maheff
ms.openlocfilehash: 2c77d509a0e66fd02bd949e481c5f0316fdd9afb
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672009"
---
# <a name="c-tutorial-call-cognitive-services-apis-in-an-azure-search-indexing-pipeline"></a>C#Tutorial: Chamar APIs serviços cognitivos num pipeline de indexação do Azure Search

Neste tutorial, vai aprender os mecanismos da programação do melhoramento de dados no Azure Search com as *competências cognitivas*. Habilidades são apoiadas por capacidades de análise de imagem nos serviços cognitivos e processamento de linguagem natural (NLP). Através do conjunto de capacidades composição e configuração, pode extrair representações de texto de uma imagem ou um ficheiro de documentos digitalizados e de texto. Também pode detetar idioma, entidades, expressões-chave e muito mais. O resultado final é o conteúdo mais avançado no índice da Azure Search, criado por um pipeline de indexação com tecnologia de IA.

Neste tutorial, vai utilizar o SDK do .NET para realizar as seguintes tarefas:

> [!div class="checklist"]
> * Criar um pipeline de indexação para melhorar os dados de exemplo na rota para um índice
> * Aplicar as habilidades internas: reconhecimento ótico de carateres, a fusão de texto, a deteção de idioma, a divisão de texto, o reconhecimento de entidades, extração de expressões-chave
> * Saber como encadear competências em conjunto através do mapeamento de entradas para saídas num conjunto de competências
> * Executar pedidos e rever resultados
> * Repor o índice e os indexadores para desenvolvimento adicional

O resultado é um índice pesquisável de texto completo no Azure Search. Pode melhorar o índice com outras funcionalidades standard, tais como [sinónimos](search-synonyms.md), [perfis de classificação](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [analisadores](search-analyzers.md) e [filtros](search-filters.md).

Este tutorial é executado no serviço gratuito, mas o número de transações gratuitos está limitado a 20 documentos por dia. Se pretender executar este tutorial mais de uma vez no mesmo dia, utilize um ficheiro mais pequeno, definir, para que caibam em mais execuções.

> [!NOTE]
> À medida que expande o âmbito, aumentando a frequência de processamento, adicionar mais documentos ou adicionar mais algoritmos de IA, terá de anexar um recurso dos serviços cognitivos faturável. Os encargos acumulam ao chamar APIs serviços cognitivos e para extração de imagem como parte da fase de aberturas de documentos no Azure Search. Não existem custos para extração de texto de documentos.
>
> Execução de habilidades internas é cobrada existente [dos serviços cognitivos pay as you go preço](https://azure.microsoft.com/pricing/details/cognitive-services/) . Preços de extração de imagem está descrito na [página de preços do Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes serviços, ferramentas e dados são utilizados neste tutorial. 

+ [Criar uma conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) para armazenar os dados de exemplo. Certifique-se a conta de armazenamento na mesma região que o Azure Search.

+ [Dados de exemplo](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) consiste num conjunto de pequenos ficheiros de diferentes tipos. 

+ [Instalar o Visual Studio](https://visualstudio.microsoft.com/) para utilizar como o IDE.

+ [Criar um serviço Azure Search](search-create-service-portal.md) ou [localizar um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na subscrição atual. Pode usar um serviço gratuito para este tutorial.

## <a name="get-a-key-and-url"></a>Obter uma chave e o URL

Para interagir com o serviço Azure Search terá o URL do serviço e uma chave de acesso. É criado um serviço de pesquisa com ambos os elementos, pelo que, se tiver adicionado o Azure Search à sua subscrição, siga estes passos para obter as informações necessárias:

1. [Inicie sessão no portal do Azure](https://portal.azure.com/)e no seu serviço de pesquisa **descrição geral** página, obter o URL. Um ponto final de exemplo poderá ser parecido com `https://mydemo.search.windows.net`.

1. Na **configurações** > **chaves**, obter uma chave de administrador para todos os direitos no serviço. Existem duas chaves de administração intercambiáveis, fornecidas para a continuidade do negócio, caso seja necessário fazer o rollover um. Pode utilizar tanto a chave primária ou secundária em pedidos para adicionar, modificar e eliminar objetos.

   ![Obter uma chave de acesso e de ponto final HTTP](media/search-get-started-postman/get-url-key.png "obter uma chave de acesso e de ponto final HTTP")

Ter uma chave válida estabelece fidedignidade, numa base por pedido, entre a aplicação a enviar o pedido e o serviço que o processa.

## <a name="prepare-sample-data"></a>Preparar dados de exemplo

O pipeline de melhoramento solicita conteúdo das origens de dados do Azure. Os dados de origem devem ter origem num tipo de origem de dados suportado de um [indexador do Azure Search](search-indexer-overview.md). Armazenamento de tabelas do Azure não é suportado para a pesquisa cognitiva. Para este exercício, vamos utilizar o armazenamento de blobs para demonstrar os vários tipos de conteúdo.

1. [Inicie sessão no portal do Azure](https://portal.azure.com), navegue até à sua conta de armazenamento do Azure, clique em **Blobs**e, em seguida, clique em **+ contentor**.

1. [Criar um contentor de BLOBs](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) para conter dados de exemplo. Pode definir o nível de acesso público a qualquer um dos respetivos valores válidos. Este tutorial parte do princípio de que o nome do contentor é "basic-demo-data-pr".

1. Depois do contentor é criado, abra-o e selecione **carregue** na barra de comandos para carregar o [dados de exemplo](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4).

   ![Ficheiros de origem no armazenamento de blobs do Azure](./media/cognitive-search-quickstart-blob/sample-data.png)

1. Depois de os ficheiros de exemplo serem carregados, obtenha o nome do contentor e uma cadeia de ligação para o seu armazenamento de Blobs. Pode fazer isso navegar para a sua conta de armazenamento no portal do Azure, selecionando **chaves de acesso**e, em seguida, copie a **cadeia de ligação** campo.

   A cadeia de ligação deve ter um URL semelhante ao seguinte exemplo:

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

Existem outras formas de especificar a cadeia de ligação, por exemplo, fornecer uma assinatura de acesso partilhada. Para saber mais sobre as credenciais da origem de dados, veja [Indexar o Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md#Credentials).

## <a name="set-up-your-environment"></a>Configurar o ambiente

Comece abrindo o Visual Studio e criando um novo projeto de aplicação de consola que pode ser executados no .NET Core.

### <a name="install-nuget-packages"></a>Instalar pacotes NuGet

O [SDK .NET da Azure Search](https://aka.ms/search-sdk) consiste em algumas bibliotecas de cliente que permitem que gerencie seus índices, origens de dados, indexadores e conjuntos de habilidades, bem como carregar e gerir documentos e executar consultas, tudo sem ter que lidar com o detalhes de HTTP e JSON. Estas bibliotecas de cliente são distribuídas como pacotes de NuGet.

Para este projeto, terá de instalar a versão 9 dos `Microsoft.Azure.Search` NuGet pacote e a versão mais recente `Microsoft.Extensions.Configuration.Json` pacote NuGet.

Instalar o `Microsoft.Azure.Search` pacote NuGet utilizando a consola do Gestor de pacotes no Visual Studio. Para abrir a selecione consola do Gestor de pacotes **ferramentas** > **Gestor de pacotes NuGet** > **Package Manager Console**. Para obter o comando a executar, navegue para o [página do pacote de Microsoft.Azure.Search NuGet](https://www.nuget.org/packages/Microsoft.Azure.Search), selecione a versão 9 e copie o comando de Gestor de pacotes. Na consola do Gestor de pacotes, execute este comando.

Para instalar o `Microsoft.Extensions.Configuration.Json` pacote de NuGet no Visual Studio, selecione **ferramentas** > **Gestor de pacotes NuGet** > **gerir pacotes NuGet para solução de...** . Selecione procurar e pesquisar o `Microsoft.Extensions.Configuration.Json` pacote NuGet. Depois de encontrá-lo, selecione o pacote, selecione seu projeto, confirmar que a versão é a versão estável mais recente, em seguida, selecione instalar.

## <a name="add-azure-search-service-information"></a>Adicionar informações do serviço Azure Search

Para ligar ao seu serviço de Azure Search terá de adicionar as informações do serviço de pesquisa ao seu projeto. Clique com o botão direito do rato no seu projeto no Solution Explorer e selecione **Add** > **Novo Item...**  . Nomeie o arquivo `appsettings.json` e selecione **adicionar**. 

Este ficheiro tem de ser incluídos no seu diretório de saída. Para tal, clique em `appsettings.json` e selecione **propriedades**. Alterar o valor de **Copy to Output Directory** ao **cópia da mais recente**.

Copiar o abaixo JSON para o novo arquivo JSON.

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "SearchServiceQueryApiKey": "Put your query API key here",
  "AzureBlobConnectionString": "Put your Azure Blob connection string here",
}
```

Adicione o search service e blob de armazenamento informações da sua conta.

Pode obter as informações do serviço de pesquisa a partir da sua página de conta de pesquisa no portal do Azure. O nome da conta será na página principal e as chaves podem ser encontradas selecionando **chaves**.

Pode obter a cadeia de ligação de blob ao navegar para a sua conta de armazenamento no portal do Azure, selecionando **chaves de acesso**e, em seguida, copiar o **cadeia de ligação** campo.

## <a name="add-namespaces"></a>Adicionar espaços de nomes

Este tutorial utiliza vários tipos diferentes de vários espaços de nomes. Para usar esses tipos de adicionar o seguinte procedimento para `Program.cs`.

```csharp
using System;
using System.Collections.Generic;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Extensions.Configuration;
```

## <a name="create-a-client"></a>Criar um cliente

Criar uma instância do `SearchServiceClient` classe.

```csharp
IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
IConfigurationRoot configuration = builder.Build();
SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);
```

`CreateSearchServiceClient` cria um novo `SearchServiceClient` com valores que são armazenadas no ficheiro de configuração do aplicativo (appSettings).

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

Criar uma nova `DataSource` instância chamando `DataSource.AzureBlobStorage`. `DataSource.AzureBlobStorage` requer que especifique o nome da origem de dados, a cadeia de ligação e o nome do contentor de Blobs.

Embora não é utilizado neste tutorial uma política de eliminação de forma recuperável também é definida que é utilizado para identificar os blobs eliminados com base no valor de uma coluna de eliminação de forma recuperável. A seguinte política considera um blob para ser excluído se ele tem uma propriedade de metadados `IsDeleted` com o valor `true`.

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

Agora que ter inicializado o `DataSource` de objeto, criar a origem de dados. `SearchServiceClient` tem uma propriedade `DataSources`. Esta propriedade fornece todos os métodos que necessários para criar, listar, atualizar ou eliminar origens de dados do Azure Search.

Para um pedido com êxito, o método devolve a origem de dados que foi criada. Se houver um problema com o pedido, como um parâmetro inválido, o método gerará uma exceção.

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

Uma vez que este é o seu primeiro pedido, verifique o portal do Azure para confirmar que a origem de dados foi criada no Azure Search. Na página do dashboard do serviço de pesquisa, verifique se o mosaico Origens de Dados tem um novo item. Poderá ter de aguardar alguns minutos para a página do portal atualizar.

  ![Mosaico Origens de dados no portal](./media/cognitive-search-tutorial-blob/data-source-tile.png "Mosaico Origens de dados no portal")

## <a name="create-a-skillset"></a>Criar um conjunto de competências

Nesta secção, vai definir um conjunto de passos de melhoria que pretende aplicar aos seus dados. Cada etapa de melhoria é chamada de um *habilidade* e o conjunto de passos de enriquecimento um *conjunto de capacidades*. Este tutorial utiliza [competências cognitivas predefinidas](cognitive-search-predefined-skills.md) para o conjunto de competências:

+ [Reconhecimento Ótico de carateres](cognitive-search-skill-ocr.md) reconhecer texto impresso e manuscrito nos arquivos de imagem.

+ [Fusão de texto](cognitive-search-skill-textmerger.md) consolidar o texto de uma coleção de campos num único campo.

+ [Deteção de Idioma](cognitive-search-skill-language-detection.md) para identificar o idioma do conteúdo.

+ [Divisão de texto](cognitive-search-skill-textsplit.md) dividir o conteúdo grandes em segmentos mais pequenos antes de chamar a habilidade de extração de expressões-chave e a habilidade de reconhecimento de entidades. Extração de expressões-chave e o reconhecimento de entidades aceitam entradas de 50.000 carateres ou menos. Alguns dos ficheiros de exemplo precisam de ser divididos para caberem dentro deste limite.

+ [Reconhecimento de entidades](cognitive-search-skill-entity-recognition.md) para extrair os nomes das organizações de conteúdo no contentor de Blobs.

+ [Extração de Expressões-Chave](cognitive-search-skill-keyphrases.md) para solicitar as principais expressões-chaves.

Durante o processamento inicial, o Azure Search cracks cada documento leiam o conteúdo a partir dos formatos de ficheiro diferente. O texto encontrado proveniente do ficheiro de origem é colocado num campo ```content``` gerado (um para cada documento). Como tal, defina a entrada como ```"/document/content"``` para usar esse texto. 

As saídas podem ser mapeadas para um índice, utilizadas como entradas para uma competência a jusante ou ambas, como é o caso do código de idioma. No índice, o código de idioma é útil para a filtragem. Como entrada, o código de idioma é utilizado pelas competências de análise de texto para informar sobre as regras linguísticas em torno da separação de palavras.

Para obter mais informações acerca das noções básicas do conjunto de competências, veja [Como definir um conjunto de competências](cognitive-search-defining-skillset.md).

### <a name="ocr-skill"></a>Habilidade de OCR

O **OCR** habilidade extrai o texto de imagens. Essa habilidade parte do princípio de que existe um campo de normalized_images. Para gerar este campo, mais tarde no tutorial vamos definir o ```"imageAction"``` configuração na definição do indexador para ```"generateNormalizedImages"```.

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

### <a name="merge-skill"></a>Habilidade de intercalação

Nesta secção irá criar um **intercalar** habilidade que mescla o campo de conteúdo do documento com o texto que foi produzido pela habilidade de OCR.

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

### <a name="language-detection-skill"></a>Habilidade de deteção de idioma

O **deteção de idioma** habilidade Deteta o idioma de texto de entrada e comunica um código de idioma único para cada documento enviado no pedido. Vamos utilizar a saída do **deteção de idioma** habilidades como parte da entrada para o **divisão de texto** habilidade.

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

### <a name="text-split-skill"></a>Dividir a habilidade de texto

O abaixo **Split** habilidade irá dividir o texto por páginas e limitar o comprimento de página a 4000 carateres conforme medido pela `String.Length`. O algoritmo irá tentar dividir o texto em partes que têm mais `maximumPageLength` de tamanho. Neste caso, o algoritmo fará o melhor para interromper a frase num limite de sentença, portanto, o tamanho do segmento pode ser um pouco menos de `maximumPageLength`.

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

### <a name="entity-recognition-skill"></a>Habilidade de reconhecimento de entidades

Isso `EntityRecognitionSkill` instância é definida para reconhecer o tipo de categoria `organization`. O **reconhecimento de entidades** habilidade também pode reconhecer os tipos de categoria `person` e `location`.

Tenha em atenção que o campo de "contexto" é definido como ```"/document/pages/*"``` com um asterisco, que significa que o passo de melhoria é chamado para cada página em ```"/document/pages"```.

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

### <a name="key-phrase-extraction-skill"></a>Habilidade de extração de expressões-chave

Como o `EntityRecognitionSkill` instância que acabou de ser criada, o **extração de expressões chave** habilidade é chamada para cada página do documento.

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

### <a name="build-and-create-the-skillset"></a>Compilar e criar o conjunto de capacidades

Criar o `Skillset` usando as habilidades que criou.

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

Crie o conjunto de capacidades no seu serviço de pesquisa.

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


### <a name="create-demoindex-class"></a>Criar a classe DemoIndex

Os campos para este índice são definidos usando uma classe de modelo. Cada propriedade da classe do modelo tem atributos que determinam os comportamentos relacionados com a pesquisa do campo de índice correspondente. 

Vamos adicionar a classe de modelo para um novo C# ficheiro. Clique com o botão direito do rato no seu projeto e selecione **Add** > **Novo Item...** , selecione "Class" e nomeie o arquivo `DemoIndex.cs`, em seguida, selecione **Add**.

Certifique-se indicar que pretende utilizar tipos a partir da `Microsoft.Azure.Search` e `Microsoft.Azure.Search.Models` espaços de nomes.

```csharp
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
```

Adicionar a abaixo da definição de classe de modelo para `DemoIndex.cs` e incluí-la no mesmo espaço de nomes onde irá criar o índice.

```csharp
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
```

Agora que definiu uma classe de modelo, efetuar uma cópia no `Program.cs` pode criar uma definição de índice muito facilmente. O nome para este índice será "demoindex".

```csharp
var index = new Index()
{
    Name = "demoindex",
    Fields = FieldBuilder.BuildForType<DemoIndex>()
};
```

Durante o teste pode achar que está a tentar criar o índice de mais de uma vez. Por este motivo, verifique se o índice que está prestes a criar já existe antes de tentar criá-la.

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

Para saber mais acerca da definição de um índice, veja [Create Index (Azure Search REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index) (Criar Índice (API REST do Azure Search)).

## <a name="create-an-indexer-map-fields-and-execute-transformations"></a>Criar um indexador, mapear campos e executar transformações

Até ao momento, criou uma origem de dados, um conjunto de competências e um índice. Estes três componentes tornam-se parte de um [indexador](search-indexer-overview.md) que une cada uma das peças numa única operação de várias fases. Para as associar num indexador, tem de definir os mapeamentos de campo.

+ Os fieldMappings são processadas antes das competências, mapeamento de campos de origem da origem de dados aos campos de destino num índice. Se os nomes de campos e tipos são os mesmos em ambas as extremidades, nenhum mapeamento é necessário.

+ Os outputFieldMappings processadas depois das competências, fazendo referência sourceFieldNames que não existem até aberturas de documentos ou enriquecimento cria-los. O targetFieldName é um campo num índice.

Para além de conectar entradas para saídas, também pode utilizar os mapeamentos de campo aplaná estruturas de dados. Para obter mais informações, consulte [como mapear campos plena para um índice pesquisável](cognitive-search-output-field-mapping.md).

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

Espera-se de que a criar o indexador irá demorar algum tempo a concluir. Apesar de o conjunto de dados ser pequeno, as competências analíticas realizam um processo de computação intensa. Algumas competências, como a análise de imagem, são de execução longa.

> [!TIP]
> A criação de um indexador invoca o pipeline. Se houver problemas em atingir os dados, com o mapeamento de entradas e saídas ou com a ordem das operações, estes vão surgir nesta fase.

### <a name="explore-creating-the-indexer"></a>Explorar a criação do indexador

Os conjuntos de código ```"maxFailedItems"``` como -1, que instrui o motor de indexação para ignorar erros durante a importação de dados. Esta definição é útil pois existem poucos documentos na origem de dados de demonstração. Para uma origem de dados maior, deve definir o valor com um número maior que 0.

Observe também o ```"dataToExtract"``` está definido como ```"contentAndMetadata"```. Esta instrução informa o indexador para extrair automaticamente o conteúdo a partir de formatos de ficheiros diferentes, bem como de metadados relativos a cada ficheiro.

Quando o conteúdo é extraído, pode definir `imageAction` para extrair texto das imagens existentes na origem de dados. O ```"imageAction"``` definido como ```"generateNormalizedImages"``` configuração, combinada com a habilidade de OCR e a habilidade de unir de texto, informa o indexador para extrair texto de imagens (por exemplo, a palavra "stop" de um sinal de tráfego) e incorporá-lo como parte do campo conteúdo. Este comportamento aplica-se tanto às imagens incorporadas nos documentos (tal como uma imagem num PDF) como às imagens existentes na origem de dados, por exemplo, um ficheiro JPG.

## <a name="check-indexer-status"></a>Verificar o estado do indexador

Assim que o indexador é definido, este é executado automaticamente quando submete o pedido. Consoante as competências cognitivas que definiu, a indexação pode demorar mais do que o esperado. Para saber se ainda está em execução o indexador, utilize o `GetStatus` método.

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

`IndexerExecutionInfo` representa o histórico de execução e o estado atual de um indexador.

Os avisos são comuns com algumas combinações de ficheiros de origem e competências e nem sempre indicam um problema. Neste tutorial, os avisos são benignos (por exemplo, os ficheiros JPEG não têm entradas de texto).
 
## <a name="query-your-index"></a>Consultar o índice

Depois de concluída a indexação, pode executar consultas que retornam o conteúdo de campos individuais. Por predefinição, o Azure Search devolve os 50 principais resultados. Os dados de exemplo são pequenos, pelo que a predefinição funciona bem. No entanto, ao trabalhar com conjuntos de dados maiores, poderá ter de incluir parâmetros na cadeia de consulta para devolver mais resultados. Para obter as instruções, veja [Como paginar os resultados no Azure Search](search-pagination-page-layout.md).

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

`CreateSearchIndexClient` cria um novo `SearchIndexClient` com valores que são armazenadas no ficheiro de configuração do aplicativo (appSettings). Tenha em atenção que a chave de consulta API do serviço de pesquisa é utilizada e não a chave de administrador.

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

Repita esta operação para campos adicionais: conteúdo, languageCode, keyPhrases e organizações neste exercício. Pode devolver vários campos através de `$select` com uma lista delimitada por vírgulas.

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Repor e executar novamente

Nos estágios iniciais experimentais do desenvolvimento, a abordagem mais prática para iterações de design é eliminar os objetos da Azure Search e permitir que o código para recriá-los. Os nomes dos recursos são exclusivos. Quando elimina um objeto, pode recriá-lo com o mesmo nome.

Este tutorial executou a verificação de indexadores existentes e os índices e excluí-los se eles já existiam, para que pode voltar a executar seu código.

Também pode utilizar o portal para eliminar índices, indexadores e conjuntos de competências.

À medida que o seu código evoluiu, pode querer refinar uma estratégia de reconstrução. Para obter mais informações, veja [Como reconstruir um índice](search-howto-reindex.md).

## <a name="takeaways"></a>Conclusões

Este tutorial demonstrou os passos básicos para a criação de um pipeline de indexação plena por meio da criação das partes de componente: uma origem de dados, o conjunto de capacidades, o índice e o indexador.

As [competências predefinidas](cognitive-search-predefined-skills.md) foram introduzidas, juntamente com a definição do conjunto de competências e os mecanismos de encadeamento de competências, através de entradas e saídas. Também aprendeu que precisa de `outputFieldMappings` na definição do indexador para encaminhar valores melhorados do pipeline para um índice pesquisável num serviço do Azure Search.

Por fim, aprendeu como testar os resultados e repor o sistema para iterações futuras. Aprendeu que a emissão de consultas acerca do índice devolve o resultado criado pelo pipeline de indexação melhorado. Também aprendeu como verificar o estado do indexador e quais os objetos a eliminar antes de executar novamente um pipeline.

## <a name="clean-up-resources"></a>Limpar recursos

A forma mais rápida de os limpar no final do tutorial passa por eliminar o grupo de recursos que contém o serviço do Azure Search e o serviço Blob do Azure. Assumindo que coloca ambos os serviços no mesmo grupo, elimine o grupo de recursos agora para eliminar definitivamente todo o seu conteúdo, incluindo os serviços e quaisquer conteúdos armazenados criados para este tutorial. No portal, o nome do grupo de recursos está na página Descrição geral de cada serviço.

## <a name="next-steps"></a>Passos Seguintes

Personalize ou expanda o pipeline com competências personalizadas. A criação de uma competência personalizada e a sua adição a um conjunto de competências permite-lhe carregar análises de texto ou imagem que escreveu.

> [!div class="nextstepaction"]
> [Exemplo: Criar uma habilidade personalizada para a pesquisa cognitiva](cognitive-search-create-custom-skill-example.md)
