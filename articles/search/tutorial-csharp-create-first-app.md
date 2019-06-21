---
title: C#Tutorial para criar seu primeiro aplicativo - Azure Search
description: Este tutorial fornece um guia passo a passo para criar seu primeiro aplicativo para o Azure Search. O tutorial fornece os dois uma ligação para uma aplicação de trabalho no GitHub e o processo completo para criar a aplicação do zero. Saiba mais sobre os componentes essenciais do Azure Search.
services: search
ms.service: search
ms.topic: tutorial
ms.author: v-pettur
author: PeterTurcan
ms.date: 05/01/2019
ms.openlocfilehash: 71668b41125de323640dd668f733c1bd1982f583
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304431"
---
# <a name="c-tutorial-create-your-first-app---azure-search"></a>C#Tutorial: Crie seu primeiro aplicativo - Azure Search

Saiba como criar uma interface de web para consulta e apresentar resultados de pesquisa de um índice com o Azure Search. Este tutorial começa com um índice existente e hospedado, para que possa se concentrar na criação de uma página de pesquisa. O índice contém dados fictícios de hotel. Assim que tiver uma página básica, pode melhorá-lo nas lições subsequentes para incluir a paginação, facetas e uma experiência antecipada.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Configurar um ambiente de desenvolvimento
> * Estruturas de dados do modelo
> * Criar uma página da web
> * Definir métodos
> * Testar a aplicação

Também aprenderá como simples é de uma chamada de pesquisa. As declarações de chave no código que irá desenvolver são encapsuladas a seguir algumas linhas.

```cs
var parameters = new SearchParameters
{
    // Enter Hotel property names into this list, so only these values will be returned.
    Select = new[] { "HotelName", "Description" }
};

DocumentSearchResult<Hotel> results  = await _indexClient.Documents.SearchAsync<Hotel>("search text", parameters);
```

Esta chamada inicia uma pesquisa de dados do Azure e retorna os resultados.

![Procurar por "agrupamento"](./media/tutorial-csharp-create-first-app/azure-search-pool.png)


## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

[Instalar o Visual Studio](https://visualstudio.microsoft.com/) para utilizar como o IDE.

### <a name="install-and-run-the-project-from-github"></a>Instalar e executar o projeto do GitHub

1. Localize o exemplo no GitHub: [Criar a primeira aplicação](https://github.com/Azure-Samples/azure-search-dotnet-samples).
1. Selecione **clonar ou transferir** e fazer sua cópia local privada do projeto.
1. Com o Visual Studio, navegue até e abra a solução para a página de pesquisa básica e selecione **iniciar sem depuração** (ou prima F5).
1. Escreva algumas palavras (por exemplo "Wi-Fi", "view", "barra", "estacionamento") e examine os resultados!

    ![Procurar por "Wi-Fi"](./media/tutorial-csharp-create-first-app/azure-search-wifi.png)

Espero que este projeto será executado sem problemas e, em execução de aplicações do Azure. Muitos dos componentes essenciais para as pesquisas mais sofisticados estão incluídos numa aplicação, pelo que é uma boa idéia passar por ele e recriá-lo passo a passo.

Para criar esse projeto a partir do zero e, por conseguinte, ajudar a reforçar os componentes do Azure Search em sua mente, percorra os passos seguintes.

## <a name="set-up-a-development-environment"></a>Configurar um ambiente de desenvolvimento

1. No Visual Studio 2017 ou posterior, selecione **New/projeto** , em seguida, **aplicação Web ASP.NET Core**. Dê um nome como "FirstAzureSearchApp" ao projeto.

    ![Criar um projeto em nuvem](./media/tutorial-csharp-create-first-app/azure-search-project1.png)

2. Depois de clicar **OK** para este tipo de projeto, terá a chance um segundo conjunto de opções que se aplicam a este projeto. Selecione **(Model-View-Controller) do aplicativo Web**.

    ![Criando um projeto do MVC](./media/tutorial-csharp-create-first-app/azure-search-project2.png)

3. Em seguida, no **ferramentas** menu, selecione **Gestor de pacotes NuGet** e, em seguida, **gerir pacotes NuGet para solução de...** . Existe um pacote, que temos de instalar. Selecione o **procurar** separador, em seguida, escreva "Azure Search" na caixa de pesquisa. Instale **Microsoft.Azure.Search** quando for apresentada na lista (versão 9.0.1, ou posterior). Terá de clicar em algumas caixas de diálogo adicionais para concluir a instalação.

    ![Usando o NuGet para adicionar bibliotecas do Azure](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

### <a name="initialize-azure-search"></a>Inicializar o Azure Search

Para este exemplo, estamos a utilizar dados de hotel publicamente disponíveis. Estes dados são uma coleção arbitrária de 50 nomes de hotel fictícia e as descrições, criadas exclusivamente com o objetivo de fornecer dados de demonstração. Para aceder a estes dados, terá de especificar um nome e a chave para o mesmo.

1. Abrir o ficheiro appSettings JSON no seu novo projeto e substitua as linhas de padrão com o seguinte nome e a chave. A chave de API mostrada aqui não é um exemplo de uma chave, é _exatamente_ a chave que precisa acessar os dados de hotel. O ficheiro appSettings JSON deverá agora ser semelhante a esta.

    ```cs
    {
        "SearchServiceName": "azs-playground",
        "SearchServiceQueryApiKey": "EA4510A6219E14888741FCFC19BFBB82"
    }
    ```

2. Nós não são efetuadas com esse arquivo ainda, selecionar as propriedades para este ficheiro e alterar os **Copy to Output Directory** na definição **copiar se for mais recente**.

    ![Copiar as definições da aplicação para a saída](./media/tutorial-csharp-create-first-app/azure-search-copy-if-newer.png)

## <a name="model-data-structures"></a>Estruturas de dados do modelo

Modelos de (C# classes) são utilizados para comunicar dados entre o cliente (a exibição), o servidor (o controlador) e também a cloud do Azure com a arquitetura MVC (model, visualizar, controlador). Normalmente, esses modelos irão refletir a estrutura dos dados que está sendo acessados. Além disso, temos um modelo para lidar com as comunicações/controlador de vista.

1. Abra o **modelos** pasta do seu projeto, usando o Gerenciador de soluções e verá um modelo padrão aqui: **ErrorViewModel.cs**.

2. Com o botão direito a **modelos** pasta e selecione **Add** , em seguida, **Novo Item**. Em seguida, na caixa de diálogo que aparece, selecione **ASP.NET Core** , em seguida, a primeira opção **classe**. Mudar o nome do arquivo. CS para Hotel.cs e clique em **adicionar**. Substitua todo o conteúdo do Hotel.cs com o código a seguir. Observe que o **endereço** e **sala** membros da classe, estes campos são classes próprias, de modo que será necessário modelos para os mesmos.

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

3. Siga o mesmo processo de criação de um modelo para o **endereço** de classe, exceto pelo fato nomear o arquivo Address.cs. Substitua o conteúdo com o seguinte.

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

4. E novamente, siga o mesmo processo para criar o **sala** classe, um nome ao ficheiro Room.cs. Mais uma vez, substitua o conteúdo com o seguinte.

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

5. O conjunto de **Hotel**, **endereço**, e **sala** classes são o que é conhecido no Azure como [ _tipos complexos_ ](search-howto-complex-data-types.md), um recurso importante do Azure Search. Tipos complexos podem ser muitos níveis de profundidade de classes e subclasses e ativar as estruturas de dados muito mais complexas para ser representado que usar _tipos simples_ (uma classe que contém apenas os membros primitivos). É necessário um modelo mais, por isso, siga o processo de criação de uma nova classe de modelo novamente, mas desta vez chamar a classe SearchData.cs e substitua o código padrão com o seguinte.

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

    Essa classe contém a entrada do usuário (**searchText**), e o resultado de pesquisa (**resultList**). O tipo de saída é fundamental **DocumentSearchResult&lt;Hotel&gt;** , como este tipo corresponde exatamente os resultados da pesquisa, e precisamos passar essa referência por meio de para o modo de exibição.



## <a name="create-a-web-page"></a>Criar uma página da web

O projeto que criou por predefinição criará um número de cliente vistas. As vistas exatas dependem da versão do .NET Core que está a utilizar (usamos 2.1 neste exemplo). Eles estão todos no **vistas** pasta do projeto. Apenas terá de modificar o ficheiro Index. cshtml (no **Views/Home** pasta).

Elimine o conteúdo de index. cshtml em sua totalidade e recriar o ficheiro nos passos seguintes.

1. Utilizamos duas imagens pequenas na vista. Pode utilizar o seu próprio ou copiar entre as imagens do projeto GitHub: azure-logo.png e search.png. Estas duas imagens devem ser colocadas no **wwwroot/imagens** pasta.

2. A primeira linha do Index. cshtml deve referenciar o modelo que utilizará para comunicar dados entre o cliente (a exibição) e o servidor (o controlador), que é o **SearchData** modelo que criámos. Adicione esta linha no ficheiro Index. cshtml.

    ```cs
    @model FirstAzureSearchApp.Models.SearchData
    ```

3. É uma prática padrão para introduzir um título para a vista, pelo que devem ser as linhas seguintes:

    ```cs
    @{
        ViewData["Title"] = "Home Page";
    }
    ```

4. Seguindo o título, introduza uma referência a uma folha de estilo do HTML, que iremos criar em breve.

    ```cs
    <head>
        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

5. Agora, para a parte mais importante da vista. Um aspecto importante se lembrar é que a vista tem de lidar com duas situações. Em primeiro lugar, ele precisa lidar com a exibição quando o aplicativo é iniciado pela primeira vez, e o utilizador não tiver inserido ainda qualquer texto de pesquisa. Em segundo lugar, ele precisa lidar com a exibição dos resultados, além de caixa de texto de pesquisa, para uso repetido pelo utilizador. Para lidar com essas duas situações, precisamos de verificar se o modelo fornecido para o modo de exibição é nulo ou não. Um modelo nulo indica que estamos na primeira das duas situações (a execução inicial da aplicação). Adicione o seguinte no ficheiro Index. cshtml e leia os comentários.

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
                @Html.TextArea("desc", Model.resultList.Results[i].Document.Description, new { @class = "box2" })
            }
        }
    }
    </body>
    ```

6. Finalmente, adicionamos a folha de estilos. No Visual Studio, no **arquivo** menu select **New/ficheiro** , em seguida, **folha de estilos** (com **geral** realçada). Substitua o código padrão com o seguinte. Será não ser vamos para este ficheiro em qualquer mais detalhes, os estilos são HTML padrão.

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

7. Guarde o ficheiro de folha de estilo como hotels.css, para a pasta wwwroot/css, juntamente com o ficheiro de site.css predefinido.

Esta ação conclui a nossa exibição. Estamos a efetuar bom progresso. Os modelos e as vistas são concluídas, apenas o controlador é deixado para juntar tudo.

## <a name="define-methods"></a>Definir métodos

Precisamos modificar o conteúdo de um controlador (**controlador home page**), que é criado por predefinição.

1. Abra o ficheiro HomeController.cs e substitua a **usando** instruções com o seguinte.

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

### <a name="add-index-methods"></a>Adicione os métodos de índice

Precisamos de dois **índice** métodos, um tomar sem parâmetros (para o caso quando a aplicação é aberto pela primeira vez) e um levando um modelo como um parâmetro (para quando o usuário tiver digitado texto de pesquisa). O primeiro destes métodos é criado por predefinição. 

1. Adicione o seguinte método, depois da predefinição **Index()** método.

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

                // Make the Azure Search call.
                await RunQueryAsync(model);
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

    Observe que o **async** declaração do método e o **await** chamada para **RunQueryAsync**. Estas palavras-chave se encarrega de tornar o nosso chamadas assíncronas e, por isso, evite bloquear os threads no servidor.

    O **capturar** block usa o modelo de erro que foi criado para nós, por predefinição.

### <a name="note-the-error-handling-and-other-default-views-and-methods"></a>Tenha em atenção o tratamento de erros e outras vistas predefinidas e os métodos

Dependendo da versão do .NET Core estiver a utilizar, um conjunto um pouco diferente do predefinido vistas são criadas por predefinição. Para .NET Core 2.1 vistas predefinidas são de índice, acerca de, contacto, privacidade e erro. Para .NET Core 2.2, por exemplo, vistas predefinidas são Index, privacidade e erro. Em ambos os casos, pode ver estas páginas padrão, quando executar a aplicação e examinar a forma como são processadas no controlador.

Podemos testar a exibição de erro mais tarde neste tutorial.

O exemplo do GitHub, podemos ter eliminado os modos de exibição não utilizados e respetivas ações associadas.

### <a name="add-the-runqueryasync-method"></a>Adicione o método RunQueryAsync

A chamada de Azure Search é encapsulada em nosso **RunQueryAsync** método.

1. Primeiro, adicione algumas variáveis estáticas para configurar o serviço do Azure e uma chamada para iniciá-los.

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

2. Agora, adicione a **RunQueryAsync** método propriamente dito.

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

    Nesse método, vamos primeiro certifique-se de nossa configuração do Azure é iniciada, então definir alguns parâmetros de pesquisa. Os nomes dos campos no **selecionar** parâmetro correspondem exatamente os nomes de propriedade no **hotel** classe. É possível omitir a **selecione** parâmetro, caso em que todas as propriedades são devolvidas. No entanto, não a definição **selecione** parâmetros é ineficiente se só estamos interessados num subconjunto dos dados. Ao especificar as propriedades que estamos interessados em, apenas essas propriedades são devolvidas.

    Chamada assíncrona para pesquisa (**model.resultList = await _indexClient.Documents.SearchAsync&lt;Hotel&gt;(model.searchText, parâmetros);** ) é o que este tutorial e a aplicação estão totalmente relacionados. O **DocumentSearchResult** classe é interessante e uma boa idéia (quando a aplicação está em execução) é definir um ponto de interrupção aqui e usar um depurador para examinar o conteúdo do **model.resultList**. Deve encontrar o que é intuitivo, dando-lhe os dados que solicitou, e não muito mais.

Agora para o momento da verdade.

### <a name="test-the-app"></a>Testar a aplicação

Agora, vamos ver as execuções de aplicação corretamente.

1. Selecione **depuração/iniciar sem depuração** ou prima a tecla F5. Se codificou tem coisas corretamente, terá a vista de índice inicial.

     ![Abrir a aplicação](./media/tutorial-csharp-create-first-app/azure-search-index.png)

2. Introduza o texto como "praia" (ou qualquer texto que vem à mente) e clique no ícone de pesquisa. Deve obter alguns resultados.

     ![Procurar por "praia"](./media/tutorial-csharp-create-first-app/azure-search-beach.png)

3. Tente introduzir "cinco estrelas". Observe como não obter nenhum resultado. Uma pesquisa mais sofisticada seria tratar "cinco estrelas" como um sinónimo para "luxo" e retornar esses resultados. A utilização de sinónimos está disponível no Azure Search, embora nós será não ser que abrangem-lo nos tutoriais primeiro.
 
4. Tente introduzir a "acesso frequente" como texto de pesquisa. Ele faz _não_ retornar as entradas com a palavra "hotel" nos mesmos. Nossa pesquisa só está localizando palavras inteiras, embora alguns resultados são devolvidos.

5. Experimente outras palavras: "agrupamento", "sol", "ver" e seja qual for. Verá o Azure Search a trabalhar em sua forma mais simples, mas ainda convencer nível.

## <a name="test-edge-conditions-and-errors"></a>Condições de limite de teste e erros

É importante verificar se as nossas funcionalidades de tratamento de erros funcionam como devem, mesmo quando as coisas estão funcionando perfeitamente. 

1. Na **índice** método, após o **tente {** chamar, introduza a linha **lançar novos Exception()** . Essa exceção irá forçar a um erro quando estamos a pesquisa em texto.

2. Executar a aplicação, introduza "barra" como texto de pesquisa e clique no ícone de pesquisa. A exceção deve resultar na vista de erro.

     ![Forçar um erro](./media/tutorial-csharp-create-first-app/azure-search-error.png)

    > [!Important]
    > Ele é considerado um risco de segurança para devolver os números de erro interno em páginas de erro. Se destina-se a sua aplicação para utilização geral, faça alguma investigação segura e melhores práticas do que a devolver quando ocorre um erro.

3. Remova **lançar novos Exception()** quando estiver satisfeito o erro de processamento funciona como deveria.

## <a name="takeaways"></a>Conclusões

Considere o seguintes das novidades deste projeto:

* Uma chamada de Azure Search é concisa e é fácil interpretar os resultados.
* Chamadas assíncronas adicionar uma pequena quantidade de complexidade para o controlador, mas são a melhor prática, se pretende desenvolver aplicações de qualidade.
* Esta aplicação efetuar uma pesquisa em texto simples, definida pelo que é configurado no **searchParameters**. No entanto, essa um classe pode ser preenchida com muitos membros que adicionar mais sofisticados para uma pesquisa. Não muito trabalho adicional é necessário para tornar esta aplicação consideravelmente mais poderosas.

## <a name="next-steps"></a>Passos Seguintes

Para proporcionar a melhor experiência de utilizador com o Azure Search, é necessário adicionar mais recursos, notavelmente a paginação (usando página números ou de deslocamento infinito) e o preenchimento automático/sugestões. Também devemos considerar mais sofisticados de parâmetros de pesquisa (por exemplo, pesquisas geográficas em hotéis dentro de um raio especificado de um determinado ponto e os resultados da pesquisa de ordenação).

As próximas etapas são resolvidas numa série de tutoriais. Vamos começar com a paginação.

> [!div class="nextstepaction"]
> [C#Tutorial: Paginação de resultados de pesquisa - Azure Search](tutorial-csharp-paging.md)


