---
title: C#tutorial para criar seu primeiro aplicativo
titleSuffix: Azure Cognitive Search
description: Saiba como criar seu primeiro aplicativo de pesquisa passo a passo. O tutorial fornece um link para um aplicativo de trabalho no GitHub e o processo completo para criar o aplicativo do zero. Saiba mais sobre os componentes essenciais do Azure Pesquisa Cognitiva.
manager: nitinme
author: PeterTurcan
ms.author: v-pettur
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 3f234a11aeaf7af4e47fb0cf6310ecd68d35e4da
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72794141"
---
# <a name="c-tutorial-create-your-first-app---azure-cognitive-search"></a>C#Tutorial: criar seu primeiro aplicativo-Azure Pesquisa Cognitiva

Saiba como criar uma interface da Web para consultar e apresentar os resultados da pesquisa de um índice usando o Azure Pesquisa Cognitiva. Este tutorial começa com um índice existente hospedado para que você possa se concentrar na criação de uma página de pesquisa. O índice contém dados fictícios do hotel. Depois de ter uma página básica, você pode aprimorá-la nas lições subsequentes para incluir paginação, facetas e uma experiência de tipo antecipado.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Configurar um ambiente de desenvolvimento
> * Estruturas de dados de modelo
> * Criar uma página da Web
> * Definir métodos
> * Testar a aplicação

Você também aprenderá como uma chamada de pesquisa é direta. As principais instruções do código que você desenvolverá são encapsuladas nas poucas linhas a seguir.

```cs
var parameters = new SearchParameters
{
    // Enter Hotel property names into this list, so only these values will be returned.
    Select = new[] { "HotelName", "Description" }
};

DocumentSearchResult<Hotel> results  = await _indexClient.Documents.SearchAsync<Hotel>("search text", parameters);
```

Essa chamada inicia uma pesquisa de dados do Azure e retorna os resultados.

![Pesquisando "pool"](./media/tutorial-csharp-create-first-app/azure-search-pool.png)


## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

[Instale o Visual Studio](https://visualstudio.microsoft.com/) para usar como o IDE.

### <a name="install-and-run-the-project-from-github"></a>Instalar e executar o projeto do GitHub

1. Localize o exemplo no GitHub: [criar primeiro aplicativo](https://github.com/Azure-Samples/azure-search-dotnet-samples).
1. Selecione **clonar ou baixar** e faça sua cópia local privada do projeto.
1. Usando o Visual Studio, navegue até e abra a solução para a página de pesquisa básica e selecione **Iniciar sem depuração** (ou pressione F5).
1. Digite algumas palavras (por exemplo, "WiFi", "exibição", "barra", "estacionamento") e examine os resultados!

    ![Pesquisando "WiFi"](./media/tutorial-csharp-create-first-app/azure-search-wifi.png)

Espero que este projeto seja executado sem problemas e que você tenha o aplicativo do Azure em execução. Muitos dos componentes essenciais para pesquisas mais sofisticadas estão incluídos neste aplicativo, portanto, é uma boa ideia passar por ele e recriá-lo passo a passo.

Para criar esse projeto do zero e, portanto, ajudar a reforçar os componentes do Azure Pesquisa Cognitiva em mente, siga as etapas a seguir.

## <a name="set-up-a-development-environment"></a>Configurar um ambiente de desenvolvimento

1. No Visual Studio 2017 ou posterior, selecione **novo/projeto** e, em seguida, **ASP.NET Core aplicativo Web**. Dê um nome ao projeto, como "FirstAzureSearchApp".

    ![Criando um projeto de nuvem](./media/tutorial-csharp-create-first-app/azure-search-project1.png)

2. Depois de clicar em **OK** para esse tipo de projeto, você receberá um segundo conjunto de opções que se aplicam a este projeto. Selecione **aplicativo Web (Model-View-Controller)** .

    ![Criando um projeto MVC](./media/tutorial-csharp-create-first-app/azure-search-project2.png)

3. Em seguida, no menu **ferramentas** , selecione **Gerenciador de pacotes NuGet** e, em seguida, **gerenciar pacotes NuGet para a solução...** . Há um pacote que precisamos instalar. Selecione a guia **procurar** e digite "Azure pesquisa cognitiva" na caixa de pesquisa. Instale o **Microsoft. Azure. Search** quando ele aparecer na lista (versão 9.0.1 ou posterior). Você precisará clicar em algumas caixas de diálogo adicionais para concluir a instalação.

    ![Usando o NuGet para adicionar bibliotecas do Azure](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

### <a name="initialize-azure-cognitive-search"></a>Inicializar Pesquisa Cognitiva do Azure

Para este exemplo, estamos usando dados de Hotel disponíveis publicamente. Esses dados são uma coleção arbitrária de nomes e descrições de hotéis fictícios de 50, criados exclusivamente para fins de fornecimento de dados de demonstração. Para acessar esses dados, você precisa especificar um nome e uma chave para ele.

1. Abra o arquivo appSettings. JSON em seu novo projeto e substitua as linhas padrão pelo nome e pela chave a seguir. A chave de API mostrada aqui não é um exemplo de uma chave; é _exatamente_ a chave de que você precisa para acessar os dados do hotel. O arquivo appSettings. JSON agora deve ser assim.

    ```cs
    {
        "SearchServiceName": "azs-playground",
        "SearchServiceQueryApiKey": "EA4510A6219E14888741FCFC19BFBB82"
    }
    ```

2. Ainda não fizemos esse arquivo, selecione as propriedades desse arquivo e altere a configuração **copiar para diretório de saída** para **copiar se mais recente**.

    ![Copiando as configurações do aplicativo para a saída](./media/tutorial-csharp-create-first-app/azure-search-copy-if-newer.png)

## <a name="model-data-structures"></a>Estruturas de dados de modelo

Modelos (C# classes) são usados para comunicar dados entre o cliente (a exibição), o servidor (o controlador) e também a nuvem do Azure usando a arquitetura MVC (modelo, exibição, controlador). Normalmente, esses modelos refletirão a estrutura dos dados que estão sendo acessados. Além disso, precisamos de um modelo para lidar com as comunicações de exibição/controlador.

1. Abra a pasta **modelos** do seu projeto, usando Gerenciador de soluções, e você verá um modelo padrão aqui: **ErrorViewModel.cs**.

2. Clique com o botão direito do mouse na pasta **modelos** e selecione **Adicionar** e **novo item**. Em seguida, na caixa de diálogo que aparece, selecione **ASP.NET Core** , em seguida, a primeira **classe**de opção. Renomeie o arquivo. cs para Hotel.cs e clique em **Adicionar**. Substitua todo o conteúdo de Hotel.cs pelo código a seguir. Observe os membros de **endereço** e **sala** da classe, esses campos são classes em si, de modo que também precisaremos de modelos para eles.

    ```cs
    using System;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Spatial;
    using Newtonsoft.Json;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Hotel
        {
            [System.ComponentModel.DataAnnotations.Key]
            [IsFilterable]
            public string HotelId { get; set; }

            [IsSearchable, IsSortable]
            public string HotelName { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.EnLucene)]
            public string Description { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.FrLucene)]
            [JsonProperty("Description_fr")]
            public string DescriptionFr { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string Category { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string[] Tags { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public bool? ParkingIncluded { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public DateTimeOffset? LastRenovationDate { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public double? Rating { get; set; }

            public Address Address { get; set; }

            [IsFilterable, IsSortable]
            public GeographyPoint Location { get; set; }

            public Room[] Rooms { get; set; }
        }
    }
    ```

3. Siga o mesmo processo de criação de um modelo para a classe **Address** , exceto o nome do arquivo address.cs. Substitua o conteúdo pelo seguinte.

    ```cs
    using Microsoft.Azure.Search;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Address
        {
            [IsSearchable]
            public string StreetAddress { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string City { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string StateProvince { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string PostalCode { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string Country { get; set; }
        }
    }
    ```

4. E, novamente, siga o mesmo processo para criar a classe **Room** , nomeando o arquivo Room.cs. Novamente, substitua o conteúdo pelo seguinte.

    ```cs
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Newtonsoft.Json;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Room
        {
            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.EnMicrosoft)]

            public string Description { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.FrMicrosoft)]
            [JsonProperty("Description_fr")]
            public string DescriptionFr { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string Type { get; set; }

            [IsFilterable, IsFacetable]
            public double? BaseRate { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string BedOptions { get; set; }

            [IsFilterable, IsFacetable]

            public int SleepsCount { get; set; }

            [IsFilterable, IsFacetable]
            public bool? SmokingAllowed { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string[] Tags { get; set; }
        }
    }
    ```

5. O conjunto de classes de **Hotel**, **endereço**e **sala** é o que é conhecido no Azure como [_tipos complexos_](search-howto-complex-data-types.md), um recurso importante do Azure pesquisa cognitiva. Tipos complexos podem ser muitos níveis de profundidade de classes e subclasses e permitem que estruturas de dados muito mais complexas sejam representadas do que usar _tipos simples_ (uma classe que contém apenas Membros primitivos). Precisamos de mais um modelo, então passemos pelo processo de criação de uma nova classe de modelo novamente, embora esse tempo chame a classe SearchData.cs e substitua o código padrão pelo seguinte.

    ```cs
    using Microsoft.Azure.Search.Models;

    namespace FirstAzureSearchApp.Models
    {
        public class SearchData
        {
            // The text to search for.
            public string searchText { get; set; }

            // The list of results.
            public DocumentSearchResult<Hotel> resultList;
        }
    }
    ```

    Essa classe contém a entrada do usuário (**ProcurarTexto**) e a saída da pesquisa (**resultList**). O tipo de saída é crítico, **DocumentSearchResult&lt;Hotel&gt;** , pois esse tipo corresponde exatamente aos resultados da pesquisa e precisamos passar essa referência para a exibição.



## <a name="create-a-web-page"></a>Criar uma página da Web

O projeto criado por padrão criará uma série de exibições de cliente. As exibições exatas dependem da versão do Core .NET que você está usando (usamos 2,1 neste exemplo). Eles estão todos na pasta **views** do projeto. Você só precisará modificar o arquivo index. cshtml (na pasta **views/Home** ).

Exclua o conteúdo de index. cshtml em sua totalidade e recrie o arquivo nas etapas a seguir.

1. Usamos duas imagens pequenas na exibição. Você pode usar seus próprios ou copiar entre as imagens do projeto do GitHub: Azure-logo. png e Search. png. Essas duas imagens devem ser colocadas na pasta **wwwroot/images** .

2. A primeira linha de index. cshtml deve fazer referência ao modelo que usaremos para comunicar dados entre o cliente (a exibição) e o servidor (o controlador), que é o modelo **SearchData** que criamos. Adicione essa linha ao arquivo index. cshtml.

    ```cs
    @model FirstAzureSearchApp.Models.SearchData
    ```

3. É uma prática padrão inserir um título para a exibição, de modo que as próximas linhas devem ser:

    ```cs
    @{
        ViewData["Title"] = "Home Page";
    }
    ```

4. Após o título, insira uma referência a uma folha de estilos HTML, que criaremos em breve.

    ```cs
    <head>
        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

5. Agora, à parte da exibição. Uma coisa importante a ser lembrada é que a exibição precisa lidar com duas situações. Em primeiro lugar, ele deve lidar com a exibição quando o aplicativo é iniciado pela primeira vez e o usuário ainda não inseriu nenhum texto de pesquisa. Em segundo lugar, ele deve lidar com a exibição de resultados, além da caixa de texto de pesquisa, para uso repetido pelo usuário. Para lidar com essas duas situações, precisamos verificar se o modelo fornecido para a exibição é nulo ou não. Um modelo nulo indica que estamos na primeira das duas situações (a execução inicial do aplicativo). Adicione o seguinte ao arquivo index. cshtml e leia os comentários.

    ```cs
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
                @Html.DisplayFor(m => m.resultList.Results.Count) Results
            </p>

            @for (var i = 0; i < Model.resultList.Results.Count; i++)
            {
                // Display the hotel name and description.
                @Html.TextAreaFor(m => Model.resultList.Results[i].Document.HotelName, new { @class = "box1" })
                @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box2" })
            }
        }
    }
    </body>
    ```

6. Por fim, adicionamos a folha de estilos. No Visual Studio, no menu **arquivo** , selecione **novo/arquivo** e **folha de estilo** (com realce **geral** ). Substitua o código padrão pelo seguinte. Não vamos entrar nesse arquivo mais detalhadamente, os estilos são HTML padrão.

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

7. Salve o arquivo de folha de estilos como hotéis. CSS, na pasta wwwroot/CSS, junto com o arquivo site. CSS padrão.

Isso conclui a nossa exibição. Estamos fazendo um bom progresso. Os modelos e as exibições são concluídos, apenas o controlador é deixado a unir tudo.

## <a name="define-methods"></a>Definir métodos

Precisamos modificar para o conteúdo de um controlador (**controlador doméstico**), que é criado por padrão.

1. Abra o arquivo HomeController.cs e substitua as instruções **using** pelo seguinte.

    ```cs
    using System;
    using System.Diagnostics;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using FirstAzureSearchApp.Models;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    ```

### <a name="add-index-methods"></a>Adicionar métodos de índice

Precisamos de dois métodos de **índice** , um não realizando nenhum parâmetro (para o caso em que o aplicativo é aberto pela primeira vez) e outro assume um modelo como parâmetro (para quando o usuário inseriu o texto de pesquisa). O primeiro desses métodos é criado por padrão. 

1. Adicione o método a seguir, após o método de **índice padrão ()** .

    ```cs
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

    Observe a Declaração **Async** do método e a chamada **Await** para **RunQueryAsync**. Essas palavras-chave cuidam da realização de nossas chamadas assíncronas e, portanto, evitam o bloqueio de threads no servidor.

    O bloco **Catch** usa o modelo de erro que foi criado para nós por padrão.

### <a name="note-the-error-handling-and-other-default-views-and-methods"></a>Observe o tratamento de erros e outros métodos e exibições padrão

Dependendo da versão do .NET Core que você está usando, um conjunto ligeiramente diferente de exibições padrão é criado por padrão. Para o .NET Core 2,1, as exibições padrão são índice, sobre, contato, privacidade e erro. Para o .NET Core 2,2, por exemplo, as exibições padrão são index, Privacy e Error. Em ambos os casos, você pode exibir essas páginas padrão ao executar o aplicativo e examinar como elas são tratadas no controlador.

Testaremos o modo de exibição de erro mais adiante neste tutorial.

No exemplo do GitHub, excluimos as exibições não usadas e suas ações associadas.

### <a name="add-the-runqueryasync-method"></a>Adicionar o método RunQueryAsync

A chamada de Pesquisa Cognitiva do Azure é encapsulada em nosso método **RunQueryAsync** .

1. Primeiro, adicione algumas variáveis estáticas para configurar o serviço do Azure e uma chamada para iniciá-las.

    ```cs
        private static SearchServiceClient _serviceClient;
        private static ISearchIndexClient _indexClient;
        private static IConfigurationBuilder _builder;
        private static IConfigurationRoot _configuration;

        private void InitSearch()
        {
            // Create a configuration using the appsettings file.
            _builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
            _configuration = _builder.Build();

            // Pull the values from the appsettings.json file.
            string searchServiceName = _configuration["SearchServiceName"];
            string queryApiKey = _configuration["SearchServiceQueryApiKey"];

            // Create a service and index client.
            _serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(queryApiKey));
            _indexClient = _serviceClient.Indexes.GetClient("hotels");
        }
    ```

2. Agora, adicione o próprio método **RunQueryAsync** .

    ```cs
        private async Task<ActionResult> RunQueryAsync(SearchData model)
        {
            InitSearch();

            var parameters = new SearchParameters
            {
                // Enter Hotel property names into this list so only these values will be returned.
                // If Select is empty, all values will be returned, which can be inefficient.
                Select = new[] { "HotelName", "Description" }
            };

            // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
            model.resultList = await _indexClient.Documents.SearchAsync<Hotel>(model.searchText, parameters);

            // Display the results.
            return View("Index", model);
        }
    ```

    Nesse método, primeiro garantimos que a configuração do Azure seja iniciada e, em seguida, defina alguns parâmetros de pesquisa. Os nomes dos campos no parâmetro **Select** correspondem exatamente aos nomes de propriedade na classe **Hotel** . É possível deixar o parâmetro **Select** fora do caso em que todas as propriedades são retornadas. No entanto, a definição de nenhum parâmetro de **seleção** não é eficiente se estamos interessados apenas em um subconjunto dos dados. Ao especificar as propriedades em que estamos interessados, apenas essas propriedades são retornadas.

    A chamada assíncrona para Search (**Model. resultList = Await _indexClient. Documents. SearchAsync&lt;Hotel&gt;(Model. ProcurarTexto, Parameters);** ) é assim que este tutorial e aplicativo. A classe **DocumentSearchResult** é um interessante e uma boa ideia (quando o aplicativo está em execução) é definir um ponto de interrupção aqui e usar um depurador para examinar o conteúdo de **Model. resultList**. Você deve descobrir que ele é intuitivo, fornecendo os dados que você solicitou e não muito mais.

Agora, no momento da verdade.

### <a name="test-the-app"></a>Testar a aplicação

Agora, vamos verificar se o aplicativo é executado corretamente.

1. Selecione **depurar/iniciar sem Depurar** ou pressione a tecla F5. Se você tiver codificado as coisas corretamente, obterá a exibição de índice inicial.

     ![Abrindo o aplicativo](./media/tutorial-csharp-create-first-app/azure-search-index.png)

2. Insira um texto como "praia" (ou qualquer texto que tenha a mente) e clique no ícone de pesquisa. Você deve obter alguns resultados.

     ![Pesquisando "praia"](./media/tutorial-csharp-create-first-app/azure-search-beach.png)

3. Tente inserir "cinco estrelas". Observe como você não obtém nenhum resultado. Uma pesquisa mais sofisticada trataria "cinco estrelas" como um sinônimo para "luxo" e retornará esses resultados. O uso de sinônimos está disponível no Azure Pesquisa Cognitiva, embora não possamos discuti-lo nos primeiros tutoriais.
 
4. Tente inserir "quente" como texto de pesquisa. Ele _não_ retorna entradas com a palavra "Hotel" nelas. Nossa pesquisa está apenas localizando palavras inteiras, embora alguns resultados sejam retornados.

5. Tente outras palavras: "pool", "sol", "View" e qualquer coisa. Você verá o Azure Pesquisa Cognitiva trabalhando em seu nível mais simples, mas ainda convincente.

## <a name="test-edge-conditions-and-errors"></a>Testar condições e erros de borda

É importante verificar se nossos recursos de tratamento de erros funcionam como deveriam, mesmo quando as coisas estão funcionando perfeitamente. 

1. No método **index** , após a chamada **try {** , insira a linha **lançar nova exceção ()** . Essa exceção forçará um erro quando pesquisarmos em texto.

2. Execute o aplicativo, insira "barra" como texto de pesquisa e clique no ícone de pesquisa. A exceção deve resultar na exibição de erro.

     ![Forçar um erro](./media/tutorial-csharp-create-first-app/azure-search-error.png)

    > [!Important]
    > Ele é considerado um risco de segurança para retornar números de erro internos em páginas de erro. Se seu aplicativo for destinado ao uso geral, faça algumas investigações em práticas seguras e práticas recomendadas sobre o que retornar quando ocorrer um erro.

3. Remover **lançar nova exceção ()** quando você estiver satisfeito, o tratamento de erros funcionará como deveria.

## <a name="takeaways"></a>Conclusões

Considere as seguintes alternativas deste projeto:

* Uma chamada de Pesquisa Cognitiva do Azure é concisa e é fácil interpretar os resultados.
* As chamadas assíncronas adicionam uma pequena quantidade de complexidade ao controlador, mas são a melhor prática se você pretende desenvolver aplicativos de qualidade.
* Esse aplicativo executou uma pesquisa de texto simples, definida pelo que está configurado em **SearchParameters**. No entanto, essa classe pode ser populada com muitos membros que adicionam sofisticação a uma pesquisa. Não é necessário muito trabalho adicional para tornar esse aplicativo consideravelmente mais potente.

## <a name="next-steps"></a>Passos seguintes

Para fornecer a melhor experiência de usuário usando o Azure Pesquisa Cognitiva, precisamos adicionar mais recursos, especialmente paginação (usando números de página ou rolagem infinita) e AutoCompletar/sugestões. Também devemos considerar os parâmetros de pesquisa mais sofisticados (por exemplo, pesquisas geográficas em hotéis dentro de um raio especificado de um determinado ponto e a ordem dos resultados da pesquisa).

Essas próximas etapas são abordadas em uma série de tutoriais. Vamos começar com a paginação.

> [!div class="nextstepaction"]
> [C#Tutorial: paginação dos resultados da pesquisa-Azure Pesquisa Cognitiva](tutorial-csharp-paging.md)


