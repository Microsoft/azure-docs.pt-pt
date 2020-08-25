---
title: Tutorial C# para criar a sua primeira app
titleSuffix: Azure Cognitive Search
description: Saiba como construir a sua primeira aplicação de pesquisa C# passo a passo. O tutorial fornece tanto um link para uma aplicação de trabalho no GitHub, como o processo completo para construir a app de raiz. Conheça os componentes essenciais da Pesquisa Cognitiva Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 06/20/2020
ms.openlocfilehash: 4c96dfa078ec12e0681574629276c48af2c1760d
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2020
ms.locfileid: "85261465"
---
# <a name="tutorial-create-your-first-search-app-using-the-net-sdk"></a>Tutorial: Crie a sua primeira app de pesquisa utilizando o .NET SDK

Aprenda a criar uma interface web para consultar e apresentar resultados de pesquisa a partir de um índice usando Azure Cognitive Search. Este tutorial começa com um índice existente e hospedado para que possa focar-se na construção de uma página de pesquisa. O índice contém dados fictícios do hotel. Uma vez que tenha uma página básica, você pode realçá-lo em lições subsequentes para incluir paging, facetas e uma experiência de tipo-futuro.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Configurar um ambiente de desenvolvimento
> * Estruturas de dados de modelo
> * Criar uma página web
> * Definir métodos
> * Testar a aplicação

Também vai aprender o quão simples é uma chamada de pesquisa. As declarações-chave no código que irá desenvolver estão encapsuladas nas seguintes linhas.

```cs
var parameters = new SearchParameters
{
    // Enter Hotel property names into this list, so only these values will be returned.
    Select = new[] { "HotelName", "Description" }
};

DocumentSearchResult<Hotel> results  = await _indexClient.Documents.SearchAsync<Hotel>("search text", parameters);
```

Esta chamada inicia uma pesquisa de dados do Azure e devolve os resultados.

![À procura de "piscina"](./media/tutorial-csharp-create-first-app/azure-search-pool.png)


## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

[Instale o Visual Studio](https://visualstudio.microsoft.com/) para utilizar como IDE.

### <a name="install-and-run-the-project-from-github"></a>Instale e execute o projeto a partir do GitHub

1. Localize a amostra no GitHub: [Crie a primeira aplicação.](https://github.com/Azure-Samples/azure-search-dotnet-samples)
1. Selecione **Clone ou faça o download** e faça a sua cópia local privada do projeto.
1. Utilizando o Visual Studio, navegue para e abra a solução para a página de pesquisa básica e selecione **Iniciar sem depurar** (ou pressionar F5).
1. Escreva algumas palavras (por exemplo" "wifi", "view", "bar", "parking"), e examine os resultados!

    ![À procura de "wifi"](./media/tutorial-csharp-create-first-app/azure-search-wifi.png)

Esperemos que este projeto corra bem, e você tem a app Azure em execução. Muitos dos componentes essenciais para pesquisas mais sofisticadas estão incluídos nesta aplicação, por isso é uma boa ideia passar por ela, e recriá-la passo a passo.

Para criar este projeto do zero, e assim ajudar a reforçar os componentes da Azure Cognitive Search na sua mente, passe pelos seguintes passos.

## <a name="set-up-a-development-environment"></a>Criar um ambiente de desenvolvimento

1. No Visual Studio 2017, ou mais tarde, selecione **New/Project,** em **seguida, ASP.NET Core Web Application**. Dê ao projeto um nome como "FirstAzureSearchApp".

    ![Criar um projeto em nuvem](./media/tutorial-csharp-create-first-app/azure-search-project1.png)

2. Depois de clicar **ok** para este tipo de projeto, será-lhe dado um segundo conjunto de opções que se aplicam a este projeto. Selecione **Aplicação Web (Controlador de visualização de modelos)**.

    ![Criação de um projeto MVC](./media/tutorial-csharp-create-first-app/azure-search-project2.png)

3. Em seguida, no menu **Ferramentas,** selecione **NuGet Package Manager** e, em seguida, **Gerencie pacotes NuGet para solução...**. Há um pacote que precisamos instalar. Selecione o separador Procurar e, em seguida, **digite** "Azure Cognitive Search" na caixa de pesquisa. Instale **o Microsoft.Azure.Search** quando aparecer na lista (versão 9.0.1, ou posterior). Terá de clicar em alguns diálogos adicionais para completar a instalação.

    ![Utilização do NuGet para adicionar bibliotecas Azure](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

### <a name="initialize-azure-cognitive-search"></a>Inicialize a pesquisa cognitiva do Azure

Para esta amostra, estamos a usar dados do hotel publicamente disponíveis. Estes dados são uma recolha arbitrária de 50 nomes e descrições de hotéis fictícios, criados unicamente com o propósito de fornecer dados de demonstração. Para aceder a estes dados, é necessário especificar um nome e uma chave para os mesmos.

1. Abra as appsettings.jsno ficheiro do seu novo projeto e substitua as linhas predefinidos pelo seguinte nome e tecla. A chave API mostrada aqui não é um exemplo de uma chave, é _exatamente_ a chave que você precisa para aceder aos dados do hotel. O seu appsettings.jsarquivado deve ser assim.

    ```cs
    {
        "SearchServiceName": "azs-playground",
        "SearchServiceQueryApiKey": "EA4510A6219E14888741FCFC19BFBB82"
    }
    ```

2. Ainda não terminamos este ficheiro, selecione as propriedades para este ficheiro e altere a definição **copy to Output Directory** para **Copy se for mais recente**.

    ![Copiar as definições da aplicação para a saída](./media/tutorial-csharp-create-first-app/azure-search-copy-if-newer.png)

## <a name="model-data-structures"></a>Estruturas de dados de modelo

Os modelos (classes C#) são utilizados para comunicar dados entre o cliente (a vista), o servidor (o controlador) e também a nuvem Azure utilizando a arquitetura MVC (modelo, vista, controlador). Tipicamente, estes modelos refletirão a estrutura dos dados que estão a ser acedidos. Além disso, precisamos de um modelo para lidar com as comunicações de visualização/controlador.

1. Abra a pasta **Modelos** do seu projeto, utilizando o Solution Explorer, e verá um modelo padrão: **ErrorViewModel.cs**.

2. Clique com o botão direito na pasta **Modelos** e **selecione Adicione** em seguida **novo item**. Em seguida, no diálogo que aparece, selecione **ASP.NET Core** e, em seguida, a primeira opção **Classe**. Mude o nome do ficheiro .cs para Hotel.cs e clique em **Adicionar**. Substitua todo o conteúdo da Hotel.cs pelo seguinte código. Reparem nos **membros** do **Endereço** e sala da classe, estes campos são as próprias aulas, por isso vamos precisar de modelos para eles também.

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

3. Siga o mesmo processo de criação de um modelo para a classe **Address,** exceto nomear o ficheiro Address.cs. Substitua o conteúdo pelo seguinte.

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

4. E mais uma vez, siga o mesmo processo para criar a classe **Room,** nomeando o ficheiro Room.cs. Mais uma vez, substitua o conteúdo pelo seguinte.

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

5. O conjunto de aulas de **Hotel,** **Endereço**e **Quarto** são o que é conhecido em Azure como [_tipos complexos,_](search-howto-complex-data-types.md)uma característica importante da Pesquisa Cognitiva Azure. Tipos complexos podem ser muitos níveis profundos de classes e subclasses, e permitir que estruturas de dados muito mais complexas sejam representadas do que usar _tipos simples_ (uma classe que contém apenas membros primitivos). Precisamos de mais um modelo, por isso, passe pelo processo de criação de uma nova classe de modelo, embora desta vez chame a classe SearchData.cs e substitua o código predefinido pelo seguinte.

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

    Esta classe contém a entrada do utilizador **(searchText),** e a saída da pesquisa **(resultadoList).** O tipo de saída é crítico, **DocumentSearchResult &lt; Hotel, &gt; **pois este tipo corresponde exatamente aos resultados da pesquisa, e precisamos passar esta referência através da vista.



## <a name="create-a-web-page"></a>Criar uma página web

O projeto que criou criará, por padrão, uma série de visualizações do cliente. As vistas exatas dependem da versão do Core .NET que está a utilizar (utilizamos 2.1 nesta amostra). Estão todos na pasta **Views** do projeto. Só será necessário modificar o ficheiro Index.cshtml (na pasta **Vistas/Casa).**

Elimine o conteúdo do Index.cshtml na sua totalidade e reconstrua o ficheiro nos seguintes passos.

1. Usamos duas pequenas imagens na vista. Pode usar o seu próprio, ou copiar através das imagens do projeto GitHub: azure-logo.png e search.png. Estas duas imagens devem ser colocadas na pasta **wwwroot/images.**

2. A primeira linha de Index.cshtml deve referenciar o modelo que iremos utilizar para comunicar dados entre o cliente (a vista) e o servidor (o controlador), que é o modelo **SearchData** que criamos. Adicione esta linha ao ficheiro Index.cshtml.

    ```cs
    @model FirstAzureSearchApp.Models.SearchData
    ```

3. É prática corrente introduzir um título para a vista, por isso as próximas linhas devem ser:

    ```cs
    @{
        ViewData["Title"] = "Home Page";
    }
    ```

4. Seguindo o título, insira uma referência a uma folha de estilo HTML, que iremos criar em breve.

    ```cs
    <head>
        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

5. Agora a carne da vista. Uma coisa fundamental a lembrar é que a vista tem que lidar com duas situações. Em primeiro lugar, deve tratar do ecrã quando a aplicação for lançada pela primeira vez, e o utilizador ainda não introduziu nenhum texto de pesquisa. Em segundo lugar, deve tratar da exibição de resultados, para além da caixa de texto de pesquisa, para utilização repetida pelo utilizador. Para lidar com estas duas situações, temos de verificar se o modelo fornecido à vista é ou não nulo. Um modelo nulo indica que estamos na primeira das duas situações (o funcionamento inicial da app). Adicione o seguinte ao ficheiro Index.cshtml e leia os comentários.

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

6. Finalmente, adicionamos a folha de estilo. No Estúdio Visual, no menu **Ficheiro** selecione **New/File** e depois **Style Sheet** (com **destaque geral).** Substitua o código predefinido pelo seguinte. Não entraremos neste ficheiro com mais detalhes, os estilos são HTML padrão.

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

7. Guarde o ficheiro de folha de estilo como hotels.css, na pasta wwwroot/css, juntamente com o ficheiro site.css predefinido.

Isso completa a nossa visão. Estamos a fazer bons progressos. Os modelos e vistas estão completos, apenas o controlador é deixado para ligar tudo.

## <a name="define-methods"></a>Definir métodos

Precisamos modificar o conteúdo do controlador único (**Home Controller),** que é criado por padrão.

1. Abra o ficheiro HomeController.cs e substitua as declarações **de utilização** com as seguintes.

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

Precisamos de dois métodos **Index,** um sem parâmetros (para o caso quando a aplicação é aberta pela primeira vez), e um tomando um modelo como parâmetro (para quando o utilizador tiver introduzido texto de pesquisa). O primeiro destes métodos é criado por defeito. 

1. Adicione o seguinte método, após o método **de Índice** predefinido.).

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

    Note a declaração de **async** do método, e a **chamada aguardada** para **RunQueryAsync**. Estas palavras-chave cuidam de tornar as nossas chamadas assíncronos assíncronos, evitando assim bloquear fios no servidor.

    O bloco **de capturas** utiliza o modelo de erro que foi criado para nós por padrão.

### <a name="note-the-error-handling-and-other-default-views-and-methods"></a>Note o tratamento de erros e outros pontos de vista e métodos padrão

Dependendo da versão do Núcleo .NET que está a utilizar, um conjunto ligeiramente diferente de pontos de vista padrão são criados por padrão. Para .NET Core 2.1 as vistas predefinições são Índice, Sobre, Contacto, Privacidade e Erro. Para .NET Core 2.2, por exemplo, as vistas padrão são Índice, Privacidade e Erro. Em qualquer dos casos, pode ver estas páginas predefinidos ao executar a aplicação e examinar como são tratadas no controlador.

Vamos testar a vista De Erro mais tarde neste tutorial.

Na amostra do GitHub, apagámos as opiniões não reutilizadas e as suas ações associadas.

### <a name="add-the-runqueryasync-method"></a>Adicione o método RunQueryAsync

A chamada de Pesquisa Cognitiva Azure está encapsulada no nosso método **RunQueryAsync.**

1. Primeiro adicione algumas variáveis estáticas para configurar o serviço Azure, e uma chamada para iniciá-las.

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

2. Agora, adicione o método **RunQueryAsync** em si.

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

    Neste método, primeiro garantimos que a nossa configuração Azure é iniciada e, em seguida, definir alguns parâmetros de pesquisa. Os nomes dos campos no parâmetro **Select correspondem** exatamente aos nomes da propriedade na classe **hoteleira.** É possível deixar de fora o parâmetro **Select,** caso em que todas as propriedades são devolvidas. No entanto, a definição de parâmetros **Select** não é ineficiente se estivermos apenas interessados num subconjunto dos dados. Ao especificar as propriedades em que estamos interessados, apenas estas propriedades são devolvidas.

    A chamada assíncronal para a procura **(model.resultList = aguarde _indexClient.Documents. SearchAsync &lt; Hotel &gt; (model.searchText, parâmetros);**) é o que este tutorial e app são todos sobre. A classe **DocumentSearchResult** é interessante, e uma boa ideia (quando a aplicação está em execução) é definir um ponto de rutura aqui, e usar um depurante para examinar o conteúdo do **model.resultList**. Deve descobrir que é intuitivo, fornecendo-lhe os dados que pediu, e pouco mais.

Agora o momento da verdade.

### <a name="test-the-app"></a>Testar a aplicação

Agora, vamos verificar se a aplicação funciona corretamente.

1. Selecione **Debug/Start Sem depurar** ou prima a tecla F5. Se tiver codificado as coisas corretamente, obterá a visão inicial do Índice.

     ![Abertura da app](./media/tutorial-csharp-create-first-app/azure-search-index.png)

2. Introduza texto como "praia" (ou qualquer texto que venha à mente) e clique no ícone de pesquisa. Devia obter alguns resultados.

     ![À procura de "praia"](./media/tutorial-csharp-create-first-app/azure-search-beach.png)

3. Tente entrar no "cinco estrelas". Note como não obtém resultados. Uma pesquisa mais sofisticada trataria a "cinco estrelas" como sinónimo de "luxo" e devolveria esses resultados. O uso de sinónimos está disponível na Pesquisa Cognitiva Azure, embora não o estejamos a cobrir nos primeiros tutoriais.
 
4. Tente introduzir "hot" como texto de pesquisa. _Não_ devolve entradas com a palavra "hotel" neles. A nossa pesquisa é apenas a localização de palavras inteiras, embora alguns resultados sejam devolvidos.

5. Tente outras palavras: "piscina", "sol", "vista", e o que for. Você verá Azure Cognitive Search trabalhando no seu nível mais simples, mas ainda convincente.

## <a name="test-edge-conditions-and-errors"></a>Condições e erros de borda de teste

É importante verificar se as nossas funcionalidades de manipulação de erros funcionam como deveriam, mesmo quando as coisas estão a funcionar perfeitamente. 

1. No método **Índice,** após a **chamada {,** introduza a linha **Lançar nova exceção()**. Esta exceção forçará um erro quando pesquisarmos por texto.

2. Executar a aplicação, introduzir "bar" como texto de pesquisa e clicar no ícone de pesquisa. A exceção deve resultar na visão de erro.

     ![Forçar um erro](./media/tutorial-csharp-create-first-app/azure-search-error.png)

    > [!Important]
    > Considera-se um risco de segurança devolver números de erro internos em páginas de erro. Se a sua aplicação se destinar a uso geral, faça alguma investigação sobre as melhores práticas e seguras do que devolver quando ocorrer um erro.

3. Remova **lançar nova exceção()** quando estiver convencido de que o manuseamento de erros funciona como deveria.

## <a name="takeaways"></a>Conclusões

Considere os seguintes takeaways deste projeto:

* Uma chamada de Pesquisa Cognitiva Azure é concisa, e é fácil interpretar os resultados.
* As chamadas assíncronos adicionam uma pequena quantidade de complexidade ao controlador, mas são as melhores práticas se pretende desenvolver apps de qualidade.
* Esta aplicação realizou uma pesquisa de texto simples, definida pelo que é configurado em **searchParameters**. No entanto, esta classe pode ser povoada com muitos membros que adicionam sofisticação a uma pesquisa. Não é necessário muito trabalho adicional para tornar esta aplicação consideravelmente mais poderosa.

## <a name="next-steps"></a>Passos seguintes

Para proporcionar a melhor experiência do utilizador usando a Azure Cognitive Search, precisamos de adicionar mais funcionalidades, nomeadamente paging (utilizando números de página, ou scrolling infinito) e autocomplete/sugestões. Devemos também considerar parâmetros de pesquisa mais sofisticados (por exemplo, pesquisas geográficas em hotéis dentro de um determinado raio de um determinado ponto, e pedido de resultados de pesquisa).

Estes próximos passos são abordados numa série de tutoriais. Vamos começar com a paging.

> [!div class="nextstepaction"]
> [C# Tutorial: Paginação de resultados de pesquisa - Azure Cognitive Search](tutorial-csharp-paging.md)


