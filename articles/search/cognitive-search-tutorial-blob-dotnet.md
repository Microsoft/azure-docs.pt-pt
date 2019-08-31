---
title: C#Tutorial para chamar API de Serviços Cognitivos em um pipeline de indexação-Azure Search
description: Neste tutorial, acompanhe um exemplo de extração de dados, linguagem natural e processamento de imagem por IA na indexação do Azure Search para extração e transformação de dados.
manager: nitinme
author: MarkHeff
services: search
ms.service: search
ms.subservice: cognitive-search
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: maheff
ms.openlocfilehash: ad0710a3492eeecd56989e627211da5d4a5e0e0a
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70186250"
---
# <a name="c-tutorial-call-cognitive-services-apis-in-an-azure-search-indexing-pipeline"></a>C#Destina Chamar API de Serviços Cognitivos em um pipeline de indexação de Azure Search

Neste tutorial, vai aprender os mecanismos da programação do melhoramento de dados no Azure Search com as *competências cognitivas*. As habilidades são apoiadas por recursos de análise de imagem e NLP (processamento de idioma natural) em serviços cognitivas. Por meio de composição e configuração do consentset, você pode extrair texto e representações de texto de uma imagem ou arquivo de documento digitalizado. Você também pode detectar idioma, entidades, frases-chave e muito mais. O resultado final é um conteúdo adicional extra em um índice de Azure Search, criado por um pipeline de indexação alimentado por ia.

Neste tutorial, você usa o SDK do .NET para executar as seguintes tarefas:

> [!div class="checklist"]
> * Criar um pipeline de indexação para melhorar os dados de exemplo na rota para um índice
> * Aplique habilidades internas: reconhecimento óptico de caracteres, fusão de texto, detecção de idioma, divisão de texto, reconhecimento de entidade, extração de frases-chave
> * Saber como encadear competências em conjunto através do mapeamento de entradas para saídas num conjunto de competências
> * Executar pedidos e rever resultados
> * Repor o índice e os indexadores para desenvolvimento adicional

O resultado é um índice pesquisável de texto completo no Azure Search. Pode melhorar o índice com outras funcionalidades standard, tais como [sinónimos](search-synonyms.md), [perfis de classificação](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [analisadores](search-analyzers.md) e [filtros](search-filters.md).

Este tutorial é executado no serviço gratuito, mas o número de transações gratuitas é limitado a 20 documentos por dia. Se você quiser executar este tutorial mais de uma vez no mesmo dia, use um conjunto de arquivos menor para que você possa se ajustar a mais execuções.

> [!NOTE]
> Ao expandir o escopo aumentando a frequência de processamento, adicionando mais documentos ou adicionando mais algoritmos de ia, você precisará anexar um recurso de serviços cognitivas cobráveis. As cobranças são acumuladas ao chamar APIs em serviços cognitivas e para extração de imagem como parte do estágio de quebra de documento no Azure Search. Não há encargos para a extração de texto de documentos.
>
> A execução de habilidades internas é cobrada pelo [preço pago pelo uso dos serviços cognitivas](https://azure.microsoft.com/pricing/details/cognitive-services/) existentes. O preço de extração de imagem é descrito na [página de preços de Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Os serviços, as ferramentas e os dados a seguir são usados neste tutorial. 

+ [Crie uma conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) para armazenar os dados de exemplo. Verifique se a conta de armazenamento está na mesma região que Azure Search.

+ [Os dados de exemplo](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) consistem em um pequeno conjunto de arquivos de tipos diferentes. 

+ [Instale o Visual Studio](https://visualstudio.microsoft.com/) para usar como o IDE.

+ [Crie um serviço de Azure Search](search-create-service-portal.md) ou [Localize um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) em sua assinatura atual. Você pode usar um serviço gratuito para este tutorial.

## <a name="get-a-key-and-url"></a>Obter uma chave e uma URL

Para interagir com o serviço de Azure Search, você precisará da URL do serviço e de uma chave de acesso. É criado um serviço de pesquisa com ambos os elementos, pelo que, se tiver adicionado o Azure Search à sua subscrição, siga estes passos para obter as informações necessárias:

1. [Entre no portal do Azure](https://portal.azure.com/)e, em sua página de **visão geral** do serviço de pesquisa, obtenha a URL. Um ponto final de exemplo poderá ser parecido com `https://mydemo.search.windows.net`.

1. Em **configurações** > **chaves**, obtenha uma chave de administração para obter direitos totais sobre o serviço. Há duas chaves de administração intercambiáveis, fornecidas para a continuidade dos negócios, caso você precise fazer uma sobreposição. Você pode usar a chave primária ou secundária em solicitações para adicionar, modificar e excluir objetos.

   ![Obter um ponto de extremidade http e uma chave de acesso](media/search-get-started-postman/get-url-key.png "Obter um ponto de extremidade http e uma chave de acesso")

Ter uma chave válida estabelece fidedignidade, numa base por pedido, entre a aplicação a enviar o pedido e o serviço que o processa.

## <a name="prepare-sample-data"></a>Preparar dados de exemplo

O pipeline de melhoramento solicita conteúdo das origens de dados do Azure. Os dados de origem devem ter origem num tipo de origem de dados suportado de um [indexador do Azure Search](search-indexer-overview.md). O armazenamento de tabelas do Azure não tem suporte para pesquisa cognitiva. Para este exercício, vamos utilizar o armazenamento de blobs para demonstrar os vários tipos de conteúdo.

1. [Entre no portal do Azure](https://portal.azure.com), navegue até sua conta de armazenamento do Azure, cliqueem BLOBs e, em seguida, clique em **+ contêiner**.

1. [Crie um contêiner](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) de BLOBs para conter dados de exemplo. Você pode definir o nível de acesso público para qualquer um de seus valores válidos. Este tutorial pressupõe que o nome do contêiner seja "Basic-demo-data-PR".

1. Depois que o contêiner for criado, abra-o e selecione **carregar** na barra de comandos para carregar os [dados de exemplo](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4).

   ![Ficheiros de origem no armazenamento de blobs do Azure](./media/cognitive-search-quickstart-blob/sample-data.png)

1. Depois de os ficheiros de exemplo serem carregados, obtenha o nome do contentor e uma cadeia de ligação para o seu armazenamento de Blobs. Você pode fazer isso navegando até sua conta de armazenamento no portal do Azure, selecionando **chaves de acesso**e, em seguida, copia o campo de **cadeia de conexão** .

   A cadeia de ligação deve ter um URL semelhante ao seguinte exemplo:

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

Existem outras formas de especificar a cadeia de ligação, por exemplo, fornecer uma assinatura de acesso partilhada. Para saber mais sobre as credenciais da origem de dados, veja [Indexar o Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md#Credentials).

## <a name="set-up-your-environment"></a>Configurar o ambiente

Comece abrindo o Visual Studio e criando um novo projeto de aplicativo de console que pode ser executado no .NET Core.

### <a name="install-nuget-packages"></a>Instalar pacotes NuGet

O [SDK do .net Azure Search](https://aka.ms/search-sdk) consiste em algumas bibliotecas de cliente que permitem gerenciar seus índices, fontes de dados, indexadores e habilidades, bem como carregar e gerenciar documentos e executar consultas, tudo isso sem precisar lidar com os detalhes de http e JSON . Essas bibliotecas de cliente são todas distribuídas como pacotes NuGet.

Para este projeto, será necessário instalar a versão 9 do `Microsoft.Azure.Search` pacote NuGet e o pacote NuGet mais recente. `Microsoft.Extensions.Configuration.Json`

Instale o `Microsoft.Azure.Search` pacote NuGet usando o console do Gerenciador de pacotes no Visual Studio. Para abrir o console do Gerenciador de pacotes, selecione **ferramentas** > **Gerenciador** > de pacotes NuGet**console do Gerenciador de pacotes**. Para obter o comando a ser executado, navegue até a [página Microsoft. Azure. Search NuGet Package](https://www.nuget.org/packages/Microsoft.Azure.Search), selecione versão 9 e copie o comando Package Manager. No console do Gerenciador de pacotes, execute este comando.

Para instalar o `Microsoft.Extensions.Configuration.Json` pacote NuGet no Visual Studio, selecione **ferramentas** > **Gerenciador** > de pacotes NuGet**gerenciar pacotes NuGet para solução...** . Selecione procurar e pesquise o `Microsoft.Extensions.Configuration.Json` pacote NuGet. Depois de encontrá-lo, selecione o pacote, selecione o projeto, confirme se a versão é a versão estável mais recente e, em seguida, selecione instalar.

## <a name="add-azure-search-service-information"></a>Adicionar informações do serviço Azure Search

Para se conectar ao serviço de Azure Search, você precisará adicionar as informações do serviço de pesquisa ao seu projeto. Clique com o botão direito do mouse em seu projeto na Gerenciador de soluções e selecione **Adicionar** > **novo item...** . Nomeie o arquivo `appsettings.json` e selecione **Adicionar**. 

Esse arquivo precisará ser incluído no diretório de saída. Para fazer isso, clique com o `appsettings.json` botão direito do mouse em e selecione **Propriedades**. Altere o valor de **copiar para diretório de saída** para **cópia mais recente**.

Copie o JSON abaixo em seu novo arquivo JSON.

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "SearchServiceQueryApiKey": "Put your query API key here",
  "AzureBlobConnectionString": "Put your Azure Blob connection string here",
}
```

Adicione suas informações de conta de armazenamento de BLOB e serviço de pesquisa.

Você pode obter as informações do serviço de pesquisa na página da sua conta de pesquisa no portal do Azure. O nome da conta estará na página principal e as chaves poderão ser encontradas selecionando **chaves**.

Você pode obter a cadeia de conexão de blob navegando até sua conta de armazenamento no portal do Azure, selecionando **chaves de acesso**e copiando o campo **cadeia de conexão** .

## <a name="add-namespaces"></a>Adicionar namespaces

Este tutorial usa vários tipos diferentes de vários namespaces. Para usar esses tipos, adicione o seguinte a `Program.cs`.

```csharp
using System;
using System.Collections.Generic;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Extensions.Configuration;
```

## <a name="create-a-client"></a>Criar um cliente

Crie uma instância da `SearchServiceClient` classe.

```csharp
IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
IConfigurationRoot configuration = builder.Build();
SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);
```

`CreateSearchServiceClient`Cria um novo `SearchServiceClient` usando valores que são armazenados no arquivo de configuração do aplicativo (appSettings. JSON).

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

Crie uma nova `DataSource` instância chamando. `DataSource.AzureBlobStorage` `DataSource.AzureBlobStorage`requer que você especifique o nome da fonte de dados, a cadeia de conexão e o nome do contêiner de BLOB.

Embora não seja usado neste tutorial, uma política de exclusão reversível também é definida, que é usada para identificar BLOBs excluídos com base no valor de uma coluna de exclusão reversível. A política a seguir considera um blob a ser excluído se ele tiver uma propriedade `IsDeleted` de metadados com `true`o valor.

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

Agora que você inicializou o `DataSource` objeto, crie a fonte de dados. `SearchServiceClient` tem uma propriedade `DataSources`. Essa propriedade fornece todos os métodos necessários para criar, listar, atualizar ou excluir Azure Search fontes de dados.

Para uma solicitação bem-sucedida, o método retornará a fonte de dados que foi criada. Se houver um problema com a solicitação, como um parâmetro inválido, o método gerará uma exceção.

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

Nesta seção, você define um conjunto de etapas de enriquecimento que deseja aplicar aos seus dados. Cada etapa de enriqueceção é chamada de *habilidade* e o conjunto de etapas de enriquecimento é uma *habilidade*. Este tutorial utiliza [competências cognitivas predefinidas](cognitive-search-predefined-skills.md) para o conjunto de competências:

+ [Reconhecimento óptico de caracteres](cognitive-search-skill-ocr.md) para reconhecer texto impresso e manuscrito em arquivos de imagem.

+ [Fusão de texto](cognitive-search-skill-textmerger.md) para consolidar o texto de uma coleção de campos em um único campo.

+ [Deteção de Idioma](cognitive-search-skill-language-detection.md) para identificar o idioma do conteúdo.

+ [Divida o texto](cognitive-search-skill-textsplit.md) para dividir o conteúdo grande em partes menores antes de chamar a habilidade de extração de frases-chave e a habilidade de reconhecimento de entidade. Extração de frases-chave e reconhecimento de entidade aceitam entradas de 50.000 caracteres ou menos. Alguns dos ficheiros de exemplo precisam de ser divididos para caberem dentro deste limite.

+ [Reconhecimento de entidade](cognitive-search-skill-entity-recognition.md) para extrair os nomes de organizações do conteúdo no contêiner de BLOB.

+ [Extração de Expressões-Chave](cognitive-search-skill-keyphrases.md) para solicitar as principais expressões-chaves.

Durante o processamento inicial, Azure Search rachadura cada documento para ler o conteúdo de diferentes formatos de arquivo. O texto encontrado proveniente do ficheiro de origem é colocado num campo ```content``` gerado (um para cada documento). Como tal, defina a entrada para como ```"/document/content"``` para usar esse texto. 

As saídas podem ser mapeadas para um índice, utilizadas como entradas para uma competência a jusante ou ambas, como é o caso do código de idioma. No índice, o código de idioma é útil para a filtragem. Como entrada, o código de idioma é utilizado pelas competências de análise de texto para informar sobre as regras linguísticas em torno da separação de palavras.

Para obter mais informações acerca das noções básicas do conjunto de competências, veja [Como definir um conjunto de competências](cognitive-search-defining-skillset.md).

### <a name="ocr-skill"></a>Habilidade de OCR

A habilidade de **OCR** extrai texto de imagens. Essa habilidade pressupõe que exista um campo normalized_images. Para gerar esse campo, mais adiante no tutorial, definiremos a ```"imageAction"``` configuração na definição do indexador como ```"generateNormalizedImages"```.

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

### <a name="merge-skill"></a>Mesclar habilidade

Nesta seção, você criará uma habilidade de mesclagem que mescla o campo de conteúdo do documento com o texto que foi produzido pela habilidade de OCR.

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

### <a name="language-detection-skill"></a>Habilidade de detecção de idioma

A **detecção de idioma** habilidade detecta o idioma do texto de entrada e relata um único código de idioma para cada documento enviado na solicitação. Usaremos a saída da **detecção de idioma** habilidade como parte da entrada para a habilidade de divisão de **texto** .

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

### <a name="text-split-skill"></a>Habilidade de divisão de texto

A habilidade de **divisão** abaixo dividirá o texto por páginas e limitará o comprimento da página a 4.000 `String.Length`caracteres conforme medido por. O algoritmo tentará dividir o texto em partes que tenham no máximo `maximumPageLength` tamanho. Nesse caso, o algoritmo fará o melhor para quebrar a frase em um limite de frase, de modo que o tamanho da parte pode ser um pouco menor `maximumPageLength`do que.

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

### <a name="entity-recognition-skill"></a>Habilidade de reconhecimento de entidade

Esta `EntityRecognitionSkill` instância está definida para reconhecer o tipo `organization`de categoria. A habilidade de **reconhecimento de entidade** também pode reconhecer `person` tipos `location`de categoria e.

Observe que o campo "contexto" é definido como ```"/document/pages/*"``` com um asterisco, o que significa que a etapa de enriquecimento é chamada para cada ```"/document/pages"```página em.

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

### <a name="key-phrase-extraction-skill"></a>Habilidade de extração de frases-chave

Assim como `EntityRecognitionSkill` a instância que acabou de ser criada, a **extração de frases-chave** habilidade é chamada para cada página do documento.

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

### <a name="build-and-create-the-skillset"></a>Compilar e criar o conconhecimento

Crie o `Skillset` usando as habilidades que você criou.

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

Crie o conconhecimento em seu serviço de pesquisa.

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

Os campos para esse índice são definidos usando uma classe de modelo. Cada propriedade da classe do modelo tem atributos que determinam os comportamentos relacionados com a pesquisa do campo de índice correspondente. 

Adicionaremos a classe de modelo a um novo C# arquivo. Clique com o botão direito do mouse no projeto e selecione **Adicionar** > **novo item...** , selecione "classe" `DemoIndex.cs`e nomeie o arquivo e, em seguida, selecione **Adicionar**.

Certifique-se de indicar que você deseja usar tipos dos `Microsoft.Azure.Search` namespaces e. `Microsoft.Azure.Search.Models`

```csharp
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
```

Adicione a definição de classe de modelo `DemoIndex.cs` abaixo para e inclua-a no mesmo namespace em que você criará o índice.

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

Agora que você definiu uma classe de modelo, de `Program.cs` volta, você pode criar uma definição de índice com bastante facilidade. O nome desse índice será "demoindex".

```csharp
var index = new Index()
{
    Name = "demoindex",
    Fields = FieldBuilder.BuildForType<DemoIndex>()
};
```

Durante os testes, você pode achar que está tentando criar o índice mais de uma vez. Por isso, verifique se o índice que você está prestes a criar já existe antes de tentar criá-lo.

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

+ Os fieldMappings são processados antes do Configurador de habilidades, mapeando os campos de origem da fonte de dados para os campos de destino em um índice. Se os nomes e tipos de campo forem os mesmos em ambas as extremidades, nenhum mapeamento será necessário.

+ Os outputFieldMappings são processados após o contextset, fazendo referência a sourceFieldNames que não existe até que a quebra de documentos ou o enriquecimento os crie. O targetFieldName é um campo em um índice.

Além de conectar entradas a saídas, você também pode usar mapeamentos de campo para mesclar estruturas de dados. Para obter mais informações, consulte [como mapear campos aprimorados para um índice pesquisável](cognitive-search-output-field-mapping.md).

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

Espere que a criação do indexador levará um pouco de tempo para ser concluída. Apesar de o conjunto de dados ser pequeno, as competências analíticas realizam um processo de computação intensa. Algumas competências, como a análise de imagem, são de execução longa.

> [!TIP]
> A criação de um indexador invoca o pipeline. Se houver problemas em atingir os dados, com o mapeamento de entradas e saídas ou com a ordem das operações, estes vão surgir nesta fase.

### <a name="explore-creating-the-indexer"></a>Explorar a criação do indexador

O código define ```"maxFailedItems"``` como-1, o que instrui o mecanismo de indexação a ignorar erros durante a importação de dados. Esta definição é útil pois existem poucos documentos na origem de dados de demonstração. Para uma origem de dados maior, deve definir o valor com um número maior que 0.

Observe também que ```"dataToExtract"``` o está definido ```"contentAndMetadata"```como. Esta instrução informa o indexador para extrair automaticamente o conteúdo a partir de formatos de ficheiros diferentes, bem como de metadados relativos a cada ficheiro.

Quando o conteúdo é extraído, pode definir `imageAction` para extrair texto das imagens existentes na origem de dados. ```"imageAction"``` O```"generateNormalizedImages"``` conjunto de configuração, combinado com as habilidades de OCR e de mesclagem de texto, diz ao indexador para extrair texto das imagens (por exemplo, a palavra "Stop" de um sinal de parada de tráfego) e inseri-la como parte do campo de conteúdo. Este comportamento aplica-se tanto às imagens incorporadas nos documentos (tal como uma imagem num PDF) como às imagens existentes na origem de dados, por exemplo, um ficheiro JPG.

## <a name="check-indexer-status"></a>Verificar o estado do indexador

Assim que o indexador é definido, este é executado automaticamente quando submete o pedido. Consoante as competências cognitivas que definiu, a indexação pode demorar mais do que o esperado. Para descobrir se o indexador ainda está em execução, use o `GetStatus` método.

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

`IndexerExecutionInfo`representa o status atual e o histórico de execução de um indexador.

Os avisos são comuns com algumas combinações de ficheiros de origem e competências e nem sempre indicam um problema. Neste tutorial, os avisos são benignos (por exemplo, os ficheiros JPEG não têm entradas de texto).
 
## <a name="query-your-index"></a>Consultar o índice

Depois que a indexação for concluída, você poderá executar consultas que retornam o conteúdo de campos individuais. Por predefinição, o Azure Search devolve os 50 principais resultados. Os dados de exemplo são pequenos, pelo que a predefinição funciona bem. No entanto, ao trabalhar com conjuntos de dados maiores, poderá ter de incluir parâmetros na cadeia de consulta para devolver mais resultados. Para obter as instruções, veja [Como paginar os resultados no Azure Search](search-pagination-page-layout.md).

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

`CreateSearchIndexClient`Cria um novo `SearchIndexClient` usando valores que são armazenados no arquivo de configuração do aplicativo (appSettings. JSON). Observe que a chave de API de consulta do serviço de pesquisa é usada e não a chave de administração.

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

Repita para campos adicionais: conteúdo, languageCode, prefrases e organizações neste exercício. Pode devolver vários campos através de `$select` com uma lista delimitada por vírgulas.

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Repor e executar novamente

Nos estágios experimentais antecipados do desenvolvimento, a abordagem mais prática para iterações de design é excluir os objetos de Azure Search e permitir que seu código os reconstrua. Os nomes dos recursos são exclusivos. Quando elimina um objeto, pode recriá-lo com o mesmo nome.

Este tutorial se encarrega de verificar indexadores e índices existentes e excluí-los, se eles já existiam, para que você possa executar novamente o código.

Você também pode usar o portal para excluir índices, indexadores e habilidades.

À medida que o seu código evoluiu, pode querer refinar uma estratégia de reconstrução. Para obter mais informações, veja [Como reconstruir um índice](search-howto-reindex.md).

## <a name="takeaways"></a>Conclusões

Este tutorial demonstrou as etapas básicas para criar um pipeline de indexação aprimorado por meio da criação de partes de componente: uma fonte de dados, um DataSet, um índice e um indexador.

As [competências predefinidas](cognitive-search-predefined-skills.md) foram introduzidas, juntamente com a definição do conjunto de competências e os mecanismos de encadeamento de competências, através de entradas e saídas. Também aprendeu que precisa de `outputFieldMappings` na definição do indexador para encaminhar valores melhorados do pipeline para um índice pesquisável num serviço do Azure Search.

Por fim, aprendeu como testar os resultados e repor o sistema para iterações futuras. Aprendeu que a emissão de consultas acerca do índice devolve o resultado criado pelo pipeline de indexação melhorado. Também aprendeu como verificar o estado do indexador e quais os objetos a eliminar antes de executar novamente um pipeline.

## <a name="clean-up-resources"></a>Limpar recursos

A forma mais rápida de os limpar no final do tutorial passa por eliminar o grupo de recursos que contém o serviço do Azure Search e o serviço Blob do Azure. Assumindo que coloca ambos os serviços no mesmo grupo, elimine o grupo de recursos agora para eliminar definitivamente todo o seu conteúdo, incluindo os serviços e quaisquer conteúdos armazenados criados para este tutorial. No portal, o nome do grupo de recursos está na página Descrição geral de cada serviço.

## <a name="next-steps"></a>Passos Seguintes

Personalize ou expanda o pipeline com competências personalizadas. A criação de uma competência personalizada e a sua adição a um conjunto de competências permite-lhe carregar análises de texto ou imagem que escreveu.

> [!div class="nextstepaction"]
> [Exemplo: Criando uma habilidade personalizada para pesquisa cognitiva](cognitive-search-create-custom-skill-example.md)
