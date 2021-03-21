---
title: Tutorial C# para criar a sua primeira app
titleSuffix: Azure Cognitive Search
description: Aprenda a construir a sua primeira aplicação de pesquisa C# passo a passo. O tutorial fornece tanto um link de descarregamento para uma aplicação de trabalho no GitHub, como o processo completo para construir a app de raiz.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/26/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 0a57e45b264badffd0305eb6ac5b3c8f7c42adf3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101695129"
---
# <a name="tutorial-create-your-first-search-app-using-the-net-sdk"></a>Tutorial: Crie a sua primeira app de pesquisa utilizando o .NET SDK

Este tutorial mostra-lhe como criar uma aplicação web que questiona e devolve resultados de um índice de pesquisa usando Azure Cognitive Search and Visual Studio.

Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Criar um ambiente de desenvolvimento
> * Estruturas de dados de modelo
> * Criar uma página web para recolher entradas de consulta e mostrar resultados
> * Definir um método de pesquisa
> * Testar a aplicação

Também vai aprender o quão simples é uma chamada de pesquisa. As declarações-chave no código que irá desenvolver estão encapsuladas nas seguintes linhas.

```csharp
var options = new SearchOptions()
{
    // The Select option specifies fields for the result set
    options.Select.Add("HotelName");
    options.Select.Add("Description");
};

var searchResult = await _searchClient.SearchAsync<Hotel>(model.searchText, options).ConfigureAwait(false);
model.resultList = searchResult.Value.GetResults().ToList();
```

Apenas uma chamada consulta o índice e os resultados de retorno.

:::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-pool.png" alt-text="À procura de *piscina*" border="true":::

## <a name="overview"></a>Descrição geral

Este tutorial utiliza o índice de amostras de hotéis, que pode criar rapidamente no seu próprio serviço de pesquisa, através do [quickstart de dados de Importação.](search-get-started-portal.md) O índice contém dados fictícios do hotel, disponíveis como fonte de dados incorporados em todos os serviços de pesquisa.

A primeira lição deste tutorial cria uma estrutura de consulta básica e página de pesquisa, que você irá melhorar em aulas subsequentes para incluir paging, facetas e uma experiência de tipo de futuro.

Uma versão acabada do código pode ser encontrada no seguinte projeto:

* [1-base-pesquisa-page (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/1-basic-search-page)

Este tutorial foi atualizado para utilizar o pacote Azure.Search.Documents (versão 11). Para obter uma versão anterior do .NET SDK, consulte [a amostra de código Microsoft.Azure.Search (versão 10).](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10)

## <a name="prerequisites"></a>Pré-requisitos

* [Criar](search-create-service-portal.md) ou [encontrar um serviço de pesquisa existente.](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)

* Crie o índice de amostras de hotéis utilizando as instruções em [Quickstart: Crie um índice de pesquisa](search-get-started-portal.md).

* [Visual Studio](https://visualstudio.microsoft.com/)

* [Biblioteca de clientes Azure Cognitive Search (versão 11)](https://www.nuget.org/packages/Azure.Search.Documents/)

### <a name="install-and-run-the-project-from-github"></a>Instale e execute o projeto a partir do GitHub

Se quiser saltar à frente de uma aplicação de trabalho, siga os passos abaixo para descarregar e executar o código acabado.

1. Localize a amostra no GitHub: [Crie a primeira aplicação.](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11)

1. Na [pasta raiz](https://github.com/Azure-Samples/azure-search-dotnet-samples), selecione **Código,** seguido de **Clone** ou **Download ZIP** para fazer a sua cópia local privada do projeto.

1. Utilizando o Visual Studio, navegue para e abra a solução para a página de pesquisa básica ("1-basic-search-page"), e selecione **Iniciar sem depurar** (ou pressionar F5) para construir e executar o programa.

1. Este é um índice de hotéis, por isso escreva em algumas palavras que você pode usar para pesquisar hotéis (por exemplo, "wifi", "view", "bar", "parking"), e examinar os resultados.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-wifi.png" alt-text="À procura de *wifi*" border="true":::

Esperemos que este projeto corra bem e que tenhas uma aplicação web a funcionar. Muitos dos componentes essenciais para pesquisas mais sofisticadas estão incluídos nesta aplicação, por isso é uma boa ideia passar por ela, e recriá-la passo a passo. As seguintes secções cobrem estes passos.

## <a name="set-up-a-development-environment"></a>Criar um ambiente de desenvolvimento

Para criar este projeto do zero, e assim reforçar os conceitos de Azure Cognitive Search na sua mente, comece com um projeto de Estúdio Visual.

1. No Estúdio Visual, selecione **New**  >  **Project,** em **seguida, ASP.NET Core Web Application**.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-project1.png" alt-text="Criar um projeto em nuvem" border="true":::

1. Dê ao projeto um nome como "FirstSearchApp" e desista da localização. Selecione **Criar**.

1. Escolha o modelo de projeto **de aplicação web (Model-View-Controller).**

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-project2.png" alt-text="Criação de um projeto MVC" border="true":::

1. Instale a biblioteca do cliente. Em **Tools**  >  **NuGet Package Manager**  >  **Gerencie pacotes nuget para solução...** selecione **Procurar** "azure.search.documents". Instale **Azure.Search.Documents** (versão 11 ou posterior), aceitando os contratos de licença e dependências.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png" alt-text="Utilização do NuGet para adicionar bibliotecas Azure" border="true":::

### <a name="initialize-azure-cognitive-search"></a>Inicialize a pesquisa cognitiva do Azure

Para esta amostra, está a utilizar dados do hotel disponíveis ao público. Estes dados são uma recolha arbitrária de 50 nomes e descrições de hotéis fictícios, criados unicamente com o propósito de fornecer dados de demonstração. Para aceder a estes dados, especifique um nome e uma chave API.

1. Abra **appsettings.js** e substitua as linhas predefinidos pelo URL do serviço de pesquisa (no `https://<service-name>.search.windows.net` formato) e uma chave API de [administração ou consulta](search-security-api-keys.md) do seu serviço de pesquisa. Uma vez que não precisa de criar ou atualizar um índice, pode utilizar a chave de consulta para este tutorial.

    ```csharp
    {
        "SearchServiceName": "<YOUR-SEARCH-SERVICE-URI>",
        "SearchServiceQueryApiKey": "<YOUR-SEARCH-SERVICE-API-KEY>"
    }
    ```

1. No Solution Explorer, selecione o ficheiro e, em Propriedades, altere a definição **copy para Output Directory** para **Copy se for mais recente**.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-copy-if-newer.png" alt-text="Copiar as definições da aplicação para a saída" border="true":::

## <a name="model-data-structures"></a>Estruturas de dados de modelo

Os modelos (classes C#) são utilizados para comunicar dados entre o cliente (a vista), o servidor (o controlador) e também a nuvem Azure utilizando a arquitetura MVC (modelo, vista, controlador). Tipicamente, estes modelos refletirão a estrutura dos dados que estão a ser acedidos.

Neste passo, irá modelar as estruturas de dados do índice de pesquisa, bem como a cadeia de pesquisa utilizada nas comunicações view/controller. No índice de hotéis, cada hotel tem muitos quartos, e cada hotel tem um endereço multi-partes. No total, a representação completa de um hotel é uma estrutura hierárquica e aninhada de dados. Vai precisar de três aulas para criar cada componente.

O conjunto de aulas de **Hotel,** **Endereço** e **Quarto** são conhecidos como [*tipos complexos,*](search-howto-complex-data-types.md)uma característica importante da Pesquisa Cognitiva Azure. Tipos complexos podem ser muitos níveis profundos de classes e subclasses, e permitir que estruturas de dados muito mais complexas sejam representadas do que usar *tipos simples* (uma classe que contém apenas membros primitivos).

1. No Solution Explorer, **os modelos** de clique à direita  >  **adicionam**  >  **novo item**.

1. Selecione **Class** e nomeie o item Hotel.cs. Substitua todos os conteúdos do Hotel.cs pelo seguinte código. Note os **membros** do **Endereço** e sala da classe, estes campos são as próprias aulas, por isso também vai precisar de modelos para eles.

    ```csharp
    using Azure.Search.Documents.Indexes;
    using Azure.Search.Documents.Indexes.Models;
    using Microsoft.Spatial;
    using System;
    using System.Text.Json.Serialization;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Hotel
        {
            [SimpleField(IsFilterable = true, IsKey = true)]
            public string HotelId { get; set; }

            [SearchableField(IsSortable = true)]
            public string HotelName { get; set; }

            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnLucene)]
            public string Description { get; set; }

            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.FrLucene)]
            [JsonPropertyName("Description_fr")]
            public string DescriptionFr { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string Category { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string[] Tags { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public bool? ParkingIncluded { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public DateTimeOffset? LastRenovationDate { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public double? Rating { get; set; }

            public Address Address { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true)]
            public GeographyPoint Location { get; set; }

            public Room[] Rooms { get; set; }
        }
    }
    ```

1. Repita o mesmo processo de criação de um modelo para a classe **Address,** nomeando o endereço de ficheiro.cs. Substitua o conteúdo pelo seguinte.

    ```csharp
    using Azure.Search.Documents.Indexes;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Address
        {
            [SearchableField]
            public string StreetAddress { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string City { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string StateProvince { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string PostalCode { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string Country { get; set; }
        }
    }
    ```

1. E mais uma vez, siga o mesmo processo para criar a classe **Room,** nomeando a sala de arquivo.cs.

    ```csharp
    using Azure.Search.Documents.Indexes;
    using Azure.Search.Documents.Indexes.Models;
    using System.Text.Json.Serialization;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Room
        {
            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnMicrosoft)]
            public string Description { get; set; }

            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.FrMicrosoft)]
            [JsonPropertyName("Description_fr")]
            public string DescriptionFr { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string Type { get; set; }

            [SimpleField(IsFilterable = true, IsFacetable = true)]
            public double? BaseRate { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string BedOptions { get; set; }

            [SimpleField(IsFilterable = true, IsFacetable = true)]
            public int SleepsCount { get; set; }

            [SimpleField(IsFilterable = true, IsFacetable = true)]
            public bool? SmokingAllowed { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string[] Tags { get; set; }
        }
    }
    ```

1. O último modelo que irá criar neste tutorial é uma classe chamada **SearchData** e representa a entrada do utilizador **(SearchText),** e a saída da pesquisa **(resultadoList).** O tipo de saída é crítico, **SearchResults &lt; Hotel, &gt;** pois este tipo corresponde exatamente aos resultados da pesquisa, e você precisa passar esta referência através da vista. Substitua o modelo predefinido pelo seguinte código.

    ```csharp
    using Azure.Search.Documents.Models;

    namespace FirstAzureSearchApp.Models
    {
        public class SearchData
        {
            // The text to search for.
            public string searchText { get; set; }

            // The list of results.
            public SearchResults<Hotel> resultList;
        }
    }
    ```

## <a name="create-a-web-page"></a>Criar uma página web

Os modelos do projeto vêm com uma série de vistas do cliente localizadas na pasta **Views.** As vistas exatas dependem da versão do Core .NET que está a utilizar (3.1 é utilizada nesta amostra). Para este tutorial, irá modificar **Index.cshtml** para incluir os elementos de uma página de pesquisa.

Elimine o conteúdo do Index.cshtml na sua totalidade e reconstrua o ficheiro nos seguintes passos.

1. O tutorial usa duas pequenas imagens na vista: um logótipo Azure e um ícone de ampliação de pesquisa (azure-logo.png e search.png). Copie as imagens do projeto GitHub para a pasta **wwwroot/images** do seu projeto.

1. A primeira linha de Index.cshtml deve referenciar o modelo utilizado para comunicar dados entre o cliente (a vista) e o servidor (o controlador), que é o modelo **SearchData** anteriormente criado. Adicione esta linha ao ficheiro Index.cshtml.

    ```csharp
    @model FirstAzureSearchApp.Models.SearchData
    ```

1. É prática corrente introduzir um título para a vista, por isso as próximas linhas devem ser:

    ```csharp
    @{
        ViewData["Title"] = "Home Page";
    }
    ```

1. Seguindo o título, insira uma referência a uma folha de estilo HTML, que irá criar em breve.

    ```csharp
    <head>
        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

1. O corpo da vista lida com duas caixas de uso. Em primeiro lugar, deve fornecer uma página vazia na primeira utilização, antes de qualquer texto de pesquisa ser introduzido. Em segundo lugar, deve tratar os resultados, para além da caixa de texto de pesquisa, para consultas repetidas.

   Para lidar com ambos os casos, é necessário verificar se o modelo fornecido à vista é nulo. Um modelo nulo indica a primeira caixa de utilização (o funcionamento inicial da aplicação). Adicione o seguinte ao ficheiro Index.cshtml e leia os comentários.

    ```csharp
    <body>
    <h1 class="sampleTitle">
        <img src="~/images/azure-logo.png" width="80" />
        Hotels Search
    </h1>

    @using (Html.BeginForm("Index", "Home", FormMethod.Post))
    {
        // Display the search text box, with the search icon to the right of it.
        <div class="searchBoxForm">
            @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input class="searchBoxSubmit" type="submit" value="">
        </div>

        @if (Model != null)
        {
            // Show the result count.
            <p class="sampleText">
                @Model.resultList.Count Results
            </p>

            @for (var i = 0; i < Model.resultList.Count; i++)
            {
                // Display the hotel name and description.
                @Html.TextAreaFor(m => m.resultList[i].Document.HotelName, new { @class = "box1" })
                @Html.TextArea($"desc{i}", Model.resultList[i].Document.Description, new { @class = "box2" })
            }
        }
    }
    </body>
    ```

1. Adicione a folha de estilo. No Estúdio Visual, em **Ficheiro** >    >  **Novo,** selecione **Style Sheet** (com destaque **geral).**

   Substitua o código predefinido pelo seguinte. Não entraremos neste ficheiro com mais detalhes, os estilos são HTML padrão.

    ```html
    textarea.box1 {
        width: 648px;
        height: 30px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        padding-left: 5px;
    }

    textarea.box2 {
        width: 648px;
        height: 100px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        padding-left: 5px;
        margin-bottom: 24px;
    }

    .sampleTitle {
        font: 32px/normal 'Segoe UI Light',Arial,Helvetica,Sans-Serif;
        margin: 20px 0;
        font-size: 32px;
        text-align: left;
    }

    .sampleText {
        font: 16px/bold 'Segoe UI Light',Arial,Helvetica,Sans-Serif;
        margin: 20px 0;
        font-size: 14px;
        text-align: left;
        height: 30px;
    }

    .searchBoxForm {
        width: 648px;
        box-shadow: 0 0 0 1px rgba(0,0,0,.1), 0 2px 4px 0 rgba(0,0,0,.16);
        background-color: #fff;
        display: inline-block;
        border-collapse: collapse;
        border-spacing: 0;
        list-style: none;
        color: #666;
    }

    .searchBox {
        width: 568px;
        font-size: 16px;
        margin: 5px 0 1px 20px;
        padding: 0 10px 0 0;
        border: 0;
        max-height: 30px;
        outline: none;
        box-sizing: content-box;
        height: 35px;
        vertical-align: top;
    }

    .searchBoxSubmit {
        background-color: #fff;
        border-color: #fff;
        background-image: url(/images/search.png);
        background-repeat: no-repeat;
        height: 20px;
        width: 20px;
        text-indent: -99em;
        border-width: 0;
        border-style: solid;
        margin: 10px;
        outline: 0;
    }
    ```

1. Guarde o ficheiro de folha de estilo como hotéis.css, na pasta **wwwroot/css,** juntamente com o ficheiro .css site predefinido.

Isso completa a nossa visão. Neste ponto, tanto os modelos como as vistas estão concluídos. Só resta o controlador para ligar tudo.

## <a name="define-methods"></a>Definir métodos

Neste passo, modifique o conteúdo do **Home Controller**.

1. Abra o ficheiro HomeController.cs e substitua as declarações **de utilização** pelas seguintes.

    ```csharp
    using Azure;
    using Azure.Search.Documents;
    using Azure.Search.Documents.Indexes;
    using FirstAzureSearchApp.Models;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Configuration;
    using System;
    using System.Diagnostics;
    using System.Linq;
    using System.Threading.Tasks;
    ```

### <a name="add-index-methods"></a>Adicionar métodos de índice

Numa aplicação MVC, o método **Index()** é um método de ação padrão para qualquer controlador. Abre a página html índice. O método predefinido, que não tem parâmetros, é utilizado neste tutorial para o caso de utilização da aplicação: render uma página de pesquisa vazia.

Nesta secção, alargamos o método para suportar um segundo caso de utilização: renderizar a página quando um utilizador tiver introduzido texto de pesquisa. Para apoiar este caso, o método do índice é estendido para tomar um modelo como parâmetro.

1. Adicione o seguinte método, após o método **de Índice** predefinido.).

    ```csharp
        [HttpPost]
        public async Task<ActionResult> Index(SearchData model)
        {
            try
            {
                // Ensure the search string is valid.
                if (model.searchText == null)
                {
                    model.searchText = "";
                }

                // Make the Azure Cognitive Search call.
                await RunQueryAsync(model);
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

    Note a declaração de **async** do método, e a **chamada aguardada** para **RunQueryAsync**. Estas palavras-chave cuidam de fazer chamadas assíncronos e, assim, evitam bloquear fios no servidor.

    O bloco **de captura** utiliza o modelo de erro que foi criado por padrão.

### <a name="note-the-error-handling-and-other-default-views-and-methods"></a>Note o tratamento de erros e outros pontos de vista e métodos padrão

Dependendo da versão do Núcleo .NET que está a utilizar, um conjunto ligeiramente diferente de pontos de vista padrão são criados por padrão. Para .NET Core 3.1 as vistas predefinições são Índice, Privacidade e Erro. Pode ver estas páginas predefinidos ao executar a aplicação e examinar como são tratadas no controlador.

Você estará testando a vista De Erro mais tarde neste tutorial.

Na amostra do GitHub, são suprimidas opiniões não useditadas e as suas ações associadas.

### <a name="add-the-runqueryasync-method"></a>Adicione o método RunQueryAsync

A chamada de Pesquisa Cognitiva Azure está encapsulada no nosso método **RunQueryAsync.**

1. Primeiro adicione algumas variáveis estáticas para configurar o serviço Azure, e uma chamada para iniciá-las.

    ```csharp
        private static SearchClient _searchClient;
        private static SearchIndexClient _indexClient;
        private static IConfigurationBuilder _builder;
        private static IConfigurationRoot _configuration;

        private void InitSearch()
        {
            // Create a configuration using appsettings.json
            _builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
            _configuration = _builder.Build();

            // Read the values from appsettings.json
            string searchServiceUri = _configuration["SearchServiceUri"];
            string queryApiKey = _configuration["SearchServiceQueryApiKey"];

            // Create a service and index client.
            _indexClient = new SearchIndexClient(new Uri(searchServiceUri), new AzureKeyCredential(queryApiKey));
            _searchClient = _indexClient.GetSearchClient("hotels");
        }
    ```

2. Agora, adicione o método **RunQueryAsync** em si.

    ```csharp
    private async Task<ActionResult> RunQueryAsync(SearchData model)
    {
        InitSearch();

        var options = new SearchOptions() { };

        // Enter Hotel property names into this list so only these values will be returned.
        // If Select is empty, all values will be returned, which can be inefficient.
        options.Select.Add("HotelName");
        options.Select.Add("Description");

        // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
        var searchResult = await _searchClient.SearchAsync<Hotel>(model.searchText, options).ConfigureAwait(false);
        model.resultList = searchResult.Value.GetResults().ToList();

        // Display the results.
        return View("Index", model);
    }
    ```

    Neste método, primeiro certifique-se de que a nossa configuração Azure é iniciada e, em seguida, definir algumas opções de pesquisa. A opção **Select** especifica quais os campos a devolver nos resultados e, assim, corresponder aos nomes da propriedade na classe **hoteleira.** Se omitir **Select**, todos os campos não obdenados são devolvidos, o que pode ser ineficiente se estiver apenas interessado num subconjunto de todos os campos possíveis.

    A chamada assíncronea para pesquisar formula o pedido (modelado como **searchText)** e resposta (modelada como **searchResult).** Se estiver a depurar este código, a classe **SearchResult** é um bom candidato para definir um ponto de rutura se precisar de examinar o conteúdo do **modelo.resultList**. Deve descobrir que é intuitivo, fornecendo-lhe os dados que pediu, e pouco mais.

### <a name="test-the-app"></a>Testar a aplicação

Agora, vamos verificar se a aplicação funciona corretamente.

1. Selecione **Debug**  >  **Start Without Debugging** ou prima **F5**. Se a aplicação correr como esperado, deverá obter a visão inicial do Índice.

     :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-index.png" alt-text="Abertura da app" border="true":::

1. Introduza uma cadeia de consulta como "praia" (ou qualquer texto que venha à mente) e clique no ícone de pesquisa para enviar o pedido.

     :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-beach.png" alt-text="Procurando *praia*" border="true":::

1. Tente entrar no "cinco estrelas". Note que esta consulta não retorna a resultados. Uma pesquisa mais sofisticada trataria a "cinco estrelas" como sinónimo de "luxo" e devolveria esses resultados. O suporte para [sinónimos](search-synonyms.md) está disponível em Azure Cognitive Search, mas não está coberto nesta série tutorial.

1. Tente introduzir "hot" como texto de pesquisa. _Não_ devolve entradas com a palavra "hotel" neles. A nossa pesquisa é apenas a localização de palavras inteiras, embora alguns resultados sejam devolvidos.

1. Tente outras palavras: "piscina", "sol", "vista", e o que for. Você verá Azure Cognitive Search trabalhando no seu nível mais simples, mas ainda convincente.

## <a name="test-edge-conditions-and-errors"></a>Condições e erros de borda de teste

É importante verificar se as nossas funcionalidades de manipulação de erros funcionam como deveriam, mesmo quando as coisas estão a funcionar perfeitamente. 

1. No método **Índice,** após a **chamada {,** introduza a linha **Lançar nova exceção()**. Esta exceção forçará um erro quando pesquisar por texto.

2. Executar a aplicação, introduzir "bar" como texto de pesquisa e clicar no ícone de pesquisa. A exceção deve resultar na visão de erro.

     :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-error.png" alt-text="Forçar um erro" border="true":::

    > [!Important]
    > Considera-se um risco de segurança devolver números de erro internos em páginas de erro. Se a sua aplicação se destinar a uso geral, faça alguma investigação sobre as melhores práticas e seguras do que devolver quando ocorrer um erro.

3. Remova **lançar nova exceção()** quando estiver convencido de que o manuseamento de erros funciona como deveria.

## <a name="takeaways"></a>Conclusões

Considere os seguintes takeaways deste projeto:

* Uma chamada de Pesquisa Cognitiva Azure é concisa, e é fácil interpretar os resultados.
* As chamadas assíncronos adicionam uma pequena quantidade de complexidade ao controlador, mas são as melhores práticas se pretende desenvolver apps de qualidade.
* Esta aplicação realizou uma pesquisa de texto simples, definida pelo que é configurado em **searchOptions**. No entanto, esta classe pode ser povoada com muitos membros que adicionam sofisticação a uma pesquisa. Não é necessário muito trabalho adicional para tornar esta aplicação consideravelmente mais poderosa.

## <a name="next-steps"></a>Passos seguintes

Para melhorar a experiência do utilizador, adicione mais funcionalidades, nomeadamente paging (utilizando números de página, ou deslocamento infinito) e autocomplete/sugestões. Você também pode considerar opções de pesquisa mais sofisticadas (por exemplo, pesquisas geográficas em hotéis dentro de um determinado raio de um determinado ponto, e pedido de resultados de pesquisa).

Estes próximos passos são abordados nos restantes tutoriais. Vamos começar com a paging.

> [!div class="nextstepaction"]
> [C# Tutorial: Paginação de resultados de pesquisa - Azure Cognitive Search](tutorial-csharp-paging.md)