---
title: C# tutorial sobre resultados de encomenda
titleSuffix: Azure Cognitive Search
description: Este tutorial C# demonstra como encomendar resultados de pesquisa. Baseia-se num projeto de hotéis anteriores, classificando por propriedade primária, propriedade secundária, e inclui um perfil de pontuação para adicionar critérios de reforço.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 10/02/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 5a55a330f6f4fefb86f2c056cd0ca3b2ba5f4b29
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499599"
---
# <a name="tutorial-order-search-results-using-the-net-sdk"></a>Tutorial: Encomende os resultados da pesquisa utilizando o .NET SDK

Ao longo desta série tutorial, os resultados foram devolvidos e apresentados por [ordem padrão](index-add-scoring-profiles.md#what-is-default-scoring). Neste tutorial, irá adicionar critérios de classificação primário e secundário. Como alternativa à encomenda com base em valores numéricos, o exemplo final mostra como classificar os resultados com base num perfil de pontuação personalizado. Também iremos um pouco mais fundo na exibição de _tipos complexos._

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Resultados da encomenda com base numa propriedade
> * Resultados da encomenda com base em múltiplas propriedades
> * Filtrar resultados com base numa distância de um ponto geográfico
> * Resultados da encomenda com base num perfil de pontuação

## <a name="overview"></a>Descrição geral

Este tutorial estende o projeto de scrolling infinito criado no [Add paging para pesquisar resultados](tutorial-csharp-paging.md) tutoriais.

Uma versão acabada do código neste tutorial pode ser encontrada no seguinte projeto:

* [Resultados de 5 encomendas (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/5-order-results)

## <a name="prerequisites"></a>Pré-requisitos

* [Solução 2b-add-infinite-scroll (GitHub).](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/2b-add-infinite-scroll) Este projeto pode ser a sua própria versão construída a partir do tutorial anterior ou uma cópia do GitHub.

Este tutorial foi atualizado para utilizar o pacote [Azure.Search.Documents (versão 11).](https://www.nuget.org/packages/Azure.Search.Documents/) Para obter uma versão anterior do .NET SDK, consulte [a amostra de código Microsoft.Azure.Search (versão 10).](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10)

## <a name="order-results-based-on-one-property"></a>Resultados da encomenda com base numa propriedade

Ao encomendar resultados com base numa propriedade, como a classificação do hotel, não só queremos os resultados encomendados, como também queremos a confirmação de que a encomenda está correta. A adição do campo de classificação aos resultados permite-nos confirmar que os resultados estão corretamente classificados.

Neste exercício, vamos também adicionar um pouco mais à exibição de resultados: a tarifa de quarto mais barata, e a tarifa de quarto mais cara, para cada hotel.

Não é necessário modificar nenhum dos modelos para permitir a encomenda. Apenas a vista e o controlador requerem atualizações. Comece por abrir o controlador doméstico.

### <a name="add-the-orderby-property-to-the-search-parameters"></a>Adicione a propriedade OrderBy aos parâmetros de pesquisa

1. Adicione a opção **OrderBy** ao nome do imóvel. No método **Índice (modelo SearchData),** adicione a seguinte linha aos parâmetros de pesquisa.

    ```cs
    OrderBy = new[] { "Rating desc" },
    ```

    >[!Note]
    > A ordem predefinida está a subir, embora possa adicionar **asc** à propriedade para deixar isso claro. A ordem descendente é especificada adicionando **desc**.

1. Agora executar a aplicação, e inserir qualquer termo de pesquisa comum. Os resultados podem ou não estar na ordem correta, uma vez que nem você como desenvolvedor, nem o utilizador, tem uma maneira fácil de verificar os resultados!

1. Vamos deixar claro que os resultados são ordenados na classificação. Primeiro, substitua as classes **box1** e **box2** nos hotéis.css arquivo pelas seguintes aulas (estas aulas são todas as novas que precisamos para este tutorial).

    ```html
    textarea.box1A {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        padding-left: 5px;
        text-align: left;
    }

    textarea.box1B {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        text-align: right;
        padding-right: 5px;
    }

    textarea.box2A {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        color: blue;
        padding-left: 5px;
        text-align: left;
    }

    textarea.box2B {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        color: blue;
        text-align: right;
        padding-right: 5px;
    }

    textarea.box3 {
        width: 648px;
        height: 100px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        padding-left: 5px;
        margin-bottom: 24px;
    }
    ```

    > [!Tip]
    > Os navegadores geralmente cache css ficheiros, e isso pode levar à utilização de um ficheiro css antigo, e as suas edições ignoradas. Uma boa maneira de contornar isto é adicionar uma cadeia de consulta com um parâmetro de versão ao link. Por exemplo:
    >
    >```html
    >   <link rel="stylesheet" href="~/css/hotels.css?v1.1" />
    >```
    >
    > Atualize o número da versão se achar que um ficheiro css antigo está a ser utilizado pelo seu navegador.

1. Adicione a propriedade **'Rating'** ao parâmetro **Select,** no método **Index (SearchData model).**

    ```cs
    Select = new[] { "HotelName", "Description", "Rating"},
    ```

1. Abra a vista (index.cshtml) e substitua o laço de renderização **&lt; (!-- Mostre os &gt; dados do hotel.--**) com o seguinte código.

    ```cs
                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                    // Display the hotel details.
                    @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                    @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box3" })
                }
    ```

1. A classificação deve estar disponível tanto na primeira página exibida, como nas páginas subsequentes que são chamadas através do pergaminho infinito. Para esta última destas duas situações, precisamos de atualizar tanto a ação **seguinte** no controlador, como a função **de deslocalizado** na vista. A partir do controlador, altere o método **Seguinte** para o seguinte código. Este código cria e comunica o texto de classificação.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                var ratingText = $"Rating: {model.resultList.Results[n].Document.Rating}";

                // Add three strings to the list.
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

1. Agora atualize a função **deslocalizada** na vista, para exibir o texto de classificação.

    ```javascript
            <script>
                function scrolled() {
                    if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                        $.getJSON("/Home/Next", function (data) {
                            var div = document.getElementById('myDiv');

                            // Append the returned data to the current list of hotels.
                            for (var i = 0; i < data.length; i += 3) {
                                div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box3">' + data[i + 2] + '</textarea>';
                            }
                        });
                    }
                }
            </script>

    ```

1. Agora, executar a aplicação novamente. Pesse em qualquer termo comum, como "wifi", e verifique se os resultados são ordenados por ordem descendente da classificação do hotel.

    ![Encomenda com base na classificação](./media/tutorial-csharp-create-first-app/azure-search-orders-rating.png)

    Você vai notar que vários hotéis têm uma classificação idêntica, e assim a sua aparição no ecrã é novamente a ordem em que os dados são encontrados, o que é arbitrário.

    Antes de procurarmos adicionar um segundo nível de encomenda, vamos adicionar algum código para mostrar a gama de tarifas de quarto. Estamos adicionando este código para mostrar dados extraindo de um _tipo complexo_, e também para que possamos discutir resultados de encomendas com base no preço (o mais barato em primeiro lugar, talvez).

### <a name="add-the-range-of-room-rates-to-the-view"></a>Adicione a gama de tarifas de quarto à vista

1. Adicione propriedades que contenham a tarifa de quarto mais barata e cara ao modelo Hotel.cs.

    ```cs
        // Room rate range
        public double cheapest { get; set; }
        public double expensive { get; set; }
    ```

1. Calcular as taxas de quarto no final da ação **Index (SearchData model)** no controlador doméstico. Adicione os cálculos após o armazenamento de dados temporários.

    ```cs
                // Ensure TempData is stored for the next call.
                TempData["page"] = page;
                TempData["searchfor"] = model.searchText;

                // Calculate the room rate ranges.
                for (int n = 0; n < model.resultList.Results.Count; n++)
                {
                    // Calculate room rates.
                    var cheapest = 0d;
                    var expensive = 0d;

                    for (var r = 0; r < model.resultList.Results[n].Document.Rooms.Length; r++)
                    {
                        var rate = model.resultList.Results[n].Document.Rooms[r].BaseRate;
                        if (rate < cheapest || cheapest == 0)
                        {
                            cheapest = (double)rate;
                        }
                        if (rate > expensive)
                        {
                            expensive = (double)rate;
                        }
                    }
                    model.resultList.Results[n].Document.cheapest = cheapest;
                    model.resultList.Results[n].Document.expensive = expensive;
                }
    ```

1. Adicione a propriedade **Quartos** ao parâmetro **Select** no método de ação **Index (SearchData)** do controlador.

    ```cs
     Select = new[] { "HotelName", "Description", "Rating", "Rooms" },
    ```

1. Altere o laço de renderização na vista para visualizar o intervalo de velocidade para a primeira página de resultados.

    ```cs
                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                    var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                    // Display the hotel details.
                    @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                    @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                    @Html.TextArea($"rates{i}" , rateText, new { @class = "box2A" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box3" })
                }
    ```

1. Altere o método **Seguinte** no controlador doméstico para comunicar o intervalo de tarifas, para as páginas de resultados subsequentes.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                var ratingText = $"Rating: {model.resultList.Results[n].Document.Rating}";
                var rateText = $"Rates from ${model.resultList.Results[n].Document.cheapest} to ${model.resultList.Results[n].Document.expensive}";

                // Add strings to the list.
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(rateText);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

1. Atualize a função **deslocalizada** na vista, para lidar com o texto das tarifas do quarto.

    ```javascript
            <script>
                function scrolled() {
                    if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                        $.getJSON("/Home/Next", function (data) {
                            var div = document.getElementById('myDiv');

                            // Append the returned data to the current list of hotels.
                            for (var i = 0; i < data.length; i += 4) {
                                div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                            }
                        });
                    }
                }
            </script>
    ```

1. Executar a aplicação e verificar se os intervalos de tarifa do quarto são apresentados.

    ![Gama de tarifas de quarto de exibição](./media/tutorial-csharp-create-first-app/azure-search-orders-rooms.png)

A **propriedade OrderBy** dos parâmetros de pesquisa não aceitará uma entrada como **quartos.BaseRate** para fornecer a tarifa de quarto mais barata, mesmo que os quartos já estivessem classificados com tarifa. Neste caso, os quartos não estão classificados com taxa. Para exibir hotéis no conjunto de dados da amostra, encomendado na tarifa do quarto, você teria que classificar os resultados no seu controlador de casa, e enviar estes resultados para a vista na ordem desejada.

## <a name="order-results-based-on-multiple-values"></a>Resultados da encomenda com base em vários valores

A questão agora é como diferenciar entre hotéis com a mesma classificação. Uma boa maneira seria encomendar com base na última vez que o hotel foi renovado. Por outras palavras, quanto mais recentemente o hotel foi renovado, maior o hotel aparece nos resultados.

1. Para adicionar um segundo nível de encomenda, altere as propriedades **OrderBy** e **Select** no método **Index (SearchData model)** para incluir a propriedade **LastRenovationDate.**

    ```cs
    OrderBy = new[] { "Rating desc", "LastRenovationDate desc" },
    Select = new[] { "HotelName", "Description", "Rating", "Rooms", "LastRenovationDate" },
    ```

    >[!Tip]
    >Qualquer número de imóveis pode ser inscrito na lista **OrderBy.** Se os hotéis tivessem a mesma classificação e data de renovação, uma terceira propriedade poderia ser inserida para diferenciar entre eles.

1. Mais uma vez, precisamos de ver a data da renovação na vista, só para ter a certeza de que a encomenda está correta. Para uma renovação, provavelmente só é necessário um ano. Altere o laço de renderização na vista para o seguinte código.

    ```cs
                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                    var lastRenovatedText = $"Last renovated: { Model.resultList.Results[i].Document.LastRenovationDate.Value.Year}";
                    var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                    // Display the hotel details.
                    @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                    @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                    @Html.TextArea($"rates{i}" , rateText, new { @class = "box2A" })
                    @Html.TextArea($"renovation{i}", lastRenovatedText, new { @class = "box2B" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box3" })
                }
    ```

1. Altere o método **Seguinte** no controlador doméstico, para encaminhar o componente do ano da última data de renovação.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                var ratingText = $"Rating: {model.resultList.Results[n].Document.Rating}";
                var rateText = $"Rates from ${model.resultList.Results[n].Document.cheapest} to ${model.resultList.Results[n].Document.expensive}";
                var lastRenovatedText = $"Last renovated: {model.resultList.Results[n].Document.LastRenovationDate.Value.Year}";

                // Add strings to the list.
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(rateText);
                nextHotels.Add(lastRenovatedText);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

1. Altere a função **deslocalizada** na vista para visualizar o texto de renovação.

    ```javascript
            <script>
                function scrolled() {
                    if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                        $.getJSON("/Home/Next", function (data) {
                            var div = document.getElementById('myDiv');

                            // Append the returned data to the current list of hotels.
                            for (var i = 0; i < data.length; i += 5) {
                                div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box2B">' + data[i + 3] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                            }
                        });
                    }
                }
            </script>
    ```

1. Execute a aplicação. Procure num termo comum, como "pool" ou "view", e verifique se os hotéis com a mesma classificação são agora exibidos em ordem descendente de data de renovação.

    ![Encomendando na data da renovação](./media/tutorial-csharp-create-first-app/azure-search-orders-renovation.png)

## <a name="filter-results-based-on-a-distance-from-a-geographical-point"></a>Filtrar resultados com base numa distância de um ponto geográfico

Rating, e data de renovação, são exemplos de propriedades que são melhor exibidas numa ordem descendente. Uma listagem alfabética seria um exemplo de um bom uso da ordem ascendente (por exemplo, se houvesse apenas uma propriedade **OrderBy,** e fosse definida para **HotelName** então uma ordem alfabética seria exibida). No entanto, para os nossos dados de amostra, a distância de um ponto geográfico seria mais adequada.

Para apresentar resultados baseados na distância geográfica, são necessários vários passos.

1. Filtre todos os hotéis que estão fora de um raio especificado a partir do ponto dado, entrando num filtro com longitude, latitude e parâmetros de raio. A longitude é dada primeiro à função POINT. O raio está em quilómetros.

    ```cs
        // "Location" must match the field name in the Hotel class.
        // Distance (the radius) is in kilometers.
        // Point order is Longitude then Latitude.
        Filter = $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') le {model.radius}",
    ```

1. O filtro acima _não_ ordena os resultados com base na distância, apenas remove os outliers. Para encomendar os resultados, introduza uma definição **OrderBy** que especifique o método geodistância.

    ```cs
    OrderBy = new[] { $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') asc" },
    ```

1. Embora os resultados foram devolvidos pela Azure Cognitive Search usando um filtro de distância, a distância calculada entre os dados e o ponto especificado _não_ é devolvida. Recalcule este valor na vista, ou controlador, se quiser exibi-lo nos resultados.

    O seguinte código calculará a distância entre dois pontos lat/lon.

    ```cs
        const double EarthRadius = 6371;

        public static double Degrees2Radians(double deg)
        {
            return deg * Math.PI / 180;
        }

        public static double DistanceInKm( double lat1,  double lon1, double lat2, double lon2)
        {
            double dlon = Degrees2Radians(lon2 - lon1);
            double dlat = Degrees2Radians(lat2 - lat1);

            double a = (Math.Sin(dlat / 2) * Math.Sin(dlat / 2)) + Math.Cos(Degrees2Radians(lat1)) * Math.Cos(Degrees2Radians(lat2)) * (Math.Sin(dlon / 2) * Math.Sin(dlon / 2));
            double angle = 2 * Math.Atan2(Math.Sqrt(a), Math.Sqrt(1 - a));
            return angle * EarthRadius;
        }
    ```

1. Agora tens de juntar estes conceitos. No entanto, estes snippets de código são tão longe quanto o nosso tutorial vai, a construção de uma app baseada em mapas é deixada como um exercício para o leitor. Para levar este exemplo mais longe, considere inserir um nome de cidade com um raio, ou localizar um ponto num mapa, e selecionar um raio. Para investigar mais aprofundadamente estas opções, consulte os seguintes recursos:

* [Documentação do Azure Maps](../azure-maps/index.yml)
* [Encontre um endereço utilizando o serviço de pesquisa Azure Maps](../azure-maps/how-to-search-for-address.md)

## <a name="order-results-based-on-a-scoring-profile"></a>Resultados da encomenda com base num perfil de pontuação

Os exemplos dados no tutorial até agora mostram como encomendar valores numéricos (classificação, data de renovação, distância geográfica), proporcionando um processo _exato_ de encomenda. No entanto, algumas pesquisas e alguns dados não se prestam a uma comparação tão fácil entre dois elementos de dados. A Azure Cognitive Search inclui o conceito de _pontuação._ _Os perfis de pontuação_ podem ser especificados para um conjunto de dados que podem ser usados para fornecer comparações mais complexas e qualitativas, que devem ser mais valiosos quando, por exemplo, comparar dados baseados em texto para decidir qual deve ser exibido primeiro.

Os perfis de pontuação não são definidos pelos utilizadores, mas tipicamente por administradores de um conjunto de dados. Vários perfis de pontuação foram criados nos dados dos hotéis. Vamos ver como um perfil de pontuação é definido, e depois tentar escrever código para pesquisar neles.

### <a name="how-scoring-profiles-are-defined"></a>Como os perfis de pontuação são definidos

Vamos olhar para três exemplos de perfis de pontuação, e considerar como cada um _deve_ afetar a ordem de resultados. Como desenvolvedor de aplicações, não escreve estes perfis, são escritos pelo administrador de dados, no entanto, é útil olhar para a sintaxe.

1. Este é o perfil de pontuação padrão para o conjunto de dados do hotéis, utilizado quando não especifica qualquer parâmetro **OrderBy** ou **ScoringProfile.** Este perfil aumenta a _pontuação_ de um hotel se o texto de pesquisa estiver presente no nome do hotel, descrição ou lista de tags (amenidades). Reparem como os pesos da pontuação favorecem certos campos. Se o texto de pesquisa aparecer noutro campo, não listado abaixo, terá um peso de 1. Obviamente, quanto maior for a pontuação, mais cedo um resultado aparece na vista.

     ```cs
    {
            "name": "boostByField",
            "text": {
                "weights": {
                    "HotelName": 2,
                    "Description": 1.5,
                    "Description_fr": 1.5,
                    "Tags": 3
                }
            }
        }

    ```

1. O perfil de pontuação seguinte aumenta significativamente a pontuação, se um parâmetro fornecido inclui uma ou mais das listas de tags (a que chamamos "amenities"). O ponto-chave deste perfil é que _deve_ ser fornecido um parâmetro, contendo texto. Se o parâmetro estiver vazio ou não for fornecido, será lançado um erro.
 
    ```cs
            {
            "name": "boostAmenities",
            "functions": [
                {
                    "type": "tag",
                    "fieldName": "Tags",
                    "boost": 5,
                    "tag": {
                        "tagsParameter": "amenities"
                    }
                }
            ]
        }
    ```

1. Neste terceiro exemplo, a classificação dá um impulso significativo à pontuação. A última data renovada também aumentará a pontuação, mas apenas se esses dados forem dentro de 730 dias (2 anos) da data atual.

    ```cs
            {
            "name": "renovatedAndHighlyRated",
            "functions": [
                {
                    "type": "magnitude",
                    "fieldName": "Rating",
                    "boost": 20,
                    "interpolation": "linear",
                    "magnitude": {
                        "boostingRangeStart": 0,
                        "boostingRangeEnd": 5,
                        "constantBoostBeyondRange": false
                    }
                },
                {
                    "type": "freshness",
                    "fieldName": "LastRenovationDate",
                    "boost": 10,
                    "interpolation": "quadratic",
                    "freshness": {
                        "boostingDuration": "P730D"
                    }
                }
            ]
        }

    ```

    Agora, vamos ver se estes perfis funcionam como achamos que devem!

### <a name="add-code-to-the-view-to-compare-profiles"></a>Adicione código à vista para comparar perfis

1. Abra o ficheiro index.cshtml e substitua a &lt; secção do corpo pelo seguinte &gt; código.

    ```html
    <body>

    @using (Html.BeginForm("Index", "Home", FormMethod.Post))
    {
        <table>
            <tr>
                <td></td>
                <td>
                    <h1 class="sampleTitle">
                        <img src="~/images/azure-logo.png" width="80" />
                        Hotels Search - Order Results
                    </h1>
                </td>
            </tr>
            <tr>
                <td></td>
                <td>
                    <!-- Display the search text box, with the search icon to the right of it. -->
                    <div class="searchBoxForm">
                        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input class="searchBoxSubmit" type="submit" value="">
                    </div>

                    <div class="searchBoxForm">
                        <b>&nbsp;Order:&nbsp;</b>
                        @Html.RadioButtonFor(m => m.scoring, "Default") Default&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "RatingRenovation") By numerical Rating&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "boostAmenities") By Amenities&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "renovatedAndHighlyRated") By Renovated date/Rating profile&nbsp;&nbsp;
                    </div>
                </td>
            </tr>

            <tr>
                <td valign="top">
                    <div id="facetplace" class="facetchecks">

                        @if (Model != null && Model.facetText != null)
                        {
                            <h5 class="facetheader">Amenities:</h5>
                            <ul class="facetlist">
                                @for (var c = 0; c < Model.facetText.Length; c++)
                                {
                                    <li> @Html.CheckBoxFor(m => m.facetOn[c], new { @id = "check" + c.ToString() }) @Model.facetText[c] </li>
                                }

                            </ul>
                        }
                    </div>
                </td>
                <td>
                    @if (Model != null && Model.resultList != null)
                    {
                        // Show the total result count.
                        <p class="sampleText">
                            @Html.DisplayFor(m => m.resultList.Count) Results <br />
                        </p>

                        <div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()">

                            <!-- Show the hotel data. -->
                            @for (var i = 0; i < Model.resultList.Results.Count; i++)
                            {
                                var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                                var lastRenovatedText = $"Last renovated: { Model.resultList.Results[i].Document.LastRenovationDate.Value.Year}";
                                var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                                string amenities = string.Join(", ", Model.resultList.Results[i].Document.Tags);
                                string fullDescription = Model.resultList.Results[i].Document.Description;
                                fullDescription += $"\nAmenities: {amenities}";

                                // Display the hotel details.
                                @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                                @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                                @Html.TextArea($"rates{i}", rateText, new { @class = "box2A" })
                                @Html.TextArea($"renovation{i}", lastRenovatedText, new { @class = "box2B" })
                                @Html.TextArea($"desc{i}", fullDescription, new { @class = "box3" })
                            }
                        </div>

                        <script>
                            function scrolled() {
                                if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                                    $.getJSON("/Home/Next", function (data) {
                                        var div = document.getElementById('myDiv');

                                        // Append the returned data to the current list of hotels.
                                        for (var i = 0; i < data.length; i += 5) {
                                            div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                            div.innerHTML += '<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                            div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                                            div.innerHTML += '<textarea class="box2B">' + data[i + 3] + '</textarea>';
                                            div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                                        }
                                    });
                                }
                            }
                        </script>
                    }
                </td>
            </tr>
        </table>
    }
    </body>
    ```

1. Abra o ficheiro SearchData.cs e substitua a classe **SearchData** pelo seguinte código.

    ```cs
    public class SearchData
    {
        public SearchData()
        {
        }

        // Constructor to initialize the list of facets sent from the controller.
        public SearchData(List<string> facets)
        {
            facetText = new string[facets.Count];

            for (int i = 0; i < facets.Count; i++)
            {
                facetText[i] = facets[i];
            }
        }

        // Array to hold the text for each amenity.
        public string[] facetText { get; set; }

        // Array to hold the setting for each amenitity.
        public bool[] facetOn { get; set; }

        // The text to search for.
        public string searchText { get; set; }

        // Record if the next page is requested.
        public string paging { get; set; }

        // The list of results.
        public DocumentSearchResult<Hotel> resultList;

        public string scoring { get; set; }       
    }
    ```

1. Abra o arquivo .css hotéis e adicione as seguintes classes HTML.

    ```html
    .facetlist {
        list-style: none;
    }
    
    .facetchecks {
        width: 250px;
        display: normal;
        color: #666;
        margin: 10px;
        padding: 5px;
    }

    .facetheader {
        font-size: 10pt;
        font-weight: bold;
        color: darkgreen;
    }
    ```

### <a name="add-code-to-the-controller-to-specify-a-scoring-profile"></a>Adicione código ao controlador para especificar um perfil de pontuação

1. Abra o ficheiro do controlador doméstico. Adicione a seguinte **declaração usando** (para ajudar na criação de listas).

    ```cs
    using System.Linq;
    ```

1. Para este exemplo, precisamos da chamada inicial para **Index** para fazer um pouco mais do que apenas devolver a vista inicial. O método procura agora até 20 comodidades para exibir na vista.

    ```cs
        public async Task<ActionResult> Index()
        {
            InitSearch();

            // Set up the facets call in the search parameters.
            SearchParameters sp = new SearchParameters()
            {
                // Search for up to 20 amenities.
                Facets = new List<string> { "Tags,count:20" },
            };

            DocumentSearchResult<Hotel> searchResult = await _indexClient.Documents.SearchAsync<Hotel>("*", sp);

            // Convert the results to a list that can be displayed in the client.
            List<string> facets = searchResult.Facets["Tags"].Select(x => x.Value.ToString()).ToList();

            // Initiate a model with a list of facets for the first view.
            SearchData model = new SearchData(facets);

            // Save the facet text for the next view.
            SaveFacets(model, false);

            // Render the view including the facets.
            return View(model);
        }
    ```

1. Precisamos de dois métodos privados para salvar as facetas para armazenamento temporário, e recuperá-las do armazenamento temporário e povoar um modelo.

    ```cs
        // Save the facet text to temporary storage, optionally saving the state of the check boxes.
        private void SaveFacets(SearchData model, bool saveChecks = false)
        {
            for (int i = 0; i < model.facetText.Length; i++)
            {
                TempData["facet" + i.ToString()] = model.facetText[i];
                if (saveChecks)
                {
                    TempData["faceton" + i.ToString()] = model.facetOn[i];
                }
            }
            TempData["facetcount"] = model.facetText.Length;
        }

        // Recover the facet text to a model, optionally recoving the state of the check boxes.
        private void RecoverFacets(SearchData model, bool recoverChecks = false)
        {
            // Create arrays of the appropriate length.
            model.facetText = new string[(int)TempData["facetcount"]];
            if (recoverChecks)
            {
                model.facetOn = new bool[(int)TempData["facetcount"]];
            }

            for (int i = 0; i < (int)TempData["facetcount"]; i++)
            {
                model.facetText[i] = TempData["facet" + i.ToString()].ToString();
                if (recoverChecks)
                {
                    model.facetOn[i] = (bool)TempData["faceton" + i.ToString()];
                }
            }
        }
    ```

1. Temos de definir os parâmetros **OrderBy** e **ScoringProfile,** se necessário. Substitua o método **índice existente (modelo SearchData)** com o seguinte.

    ```cs
        public async Task<ActionResult> Index(SearchData model)
        {
            try
            {
                InitSearch();

                int page;

                if (model.paging != null && model.paging == "next")
                {
                    // Recover the facet text, and the facet check box settings.
                    RecoverFacets(model, true);

                    // Increment the page.
                    page = (int)TempData["page"] + 1;

                    // Recover the search text.
                    model.searchText = TempData["searchfor"].ToString();
                }
                else
                {
                    // First search with text. 
                    // Recover the facet text, but ignore the check box settings, and use the current model settings.
                    RecoverFacets(model,false);

                    // First call. Check for valid text input, and valid scoring profile.
                    if (model.searchText == null)
                    {
                        model.searchText = "";
                    }
                    if (model.scoring == null)
                    {
                        model.scoring = "Default";
                    }
                    page = 0;
                }

                // Set empty defaults for ordering and scoring parameters.
                var orderby = new List<string>();
                string profile = "";
                var scoringParams = new List<ScoringParameter>();

                // Set the ordering based on the user's radio button selection.
                switch (model.scoring)
                {
                    case "RatingRenovation":
                        orderby.Add("Rating desc");
                        orderby.Add("LastRenovationDate desc");
                        break;

                    case "boostAmenities":
                        {
                            profile = model.scoring;
                            var setAmenities = new List<string>();

                            // Create a string list of amenities that have been clicked.
                            for (int a = 0; a < model.facetOn.Length; a++)
                            {
                                if (model.facetOn[a])
                                {
                                    setAmenities.Add(model.facetText[a]);
                                }
                            }
                            if (setAmenities.Count > 0)
                            {
                                // Only set scoring parameters if there are any.
                                var sp = new ScoringParameter("amenities", setAmenities);
                                scoringParams.Add(sp);
                            }
                            else
                            {
                                // No amenities selected, so set profile back to default.
                                profile = "";
                            }
                        }
                        break;

                    case "renovatedAndHighlyRated":
                        profile = model.scoring;
                        break;

                    default:
                        break;
                }

                // Setup the search parameters.
                var parameters = new SearchParameters
                {
                    // Set the ordering/scoring parameters.
                    OrderBy = orderby,
                    ScoringProfile = profile,
                    ScoringParameters = scoringParams,

                    // Select the data properties to be returned.
                    Select = new[] { "HotelName", "Description", "Tags", "Rooms", "Rating", "LastRenovationDate" },
                    SearchMode = SearchMode.All,

                    // Skip past results that have already been returned.
                    Skip = page * GlobalVariables.ResultsPerPage,

                    // Take only the next page worth of results.
                    Top = GlobalVariables.ResultsPerPage,

                    // Include the total number of results.
                    IncludeTotalResultCount = true,
                };

                // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
                model.resultList = await _indexClient.Documents.SearchAsync<Hotel>(model.searchText, parameters);

                // Ensure TempData is stored for the next call.
                TempData["page"] = page;
                TempData["searchfor"] = model.searchText;
                TempData["scoring"] = model.scoring;
                SaveFacets(model,true);

                // Calculate the room rate ranges.
                for (int n = 0; n < model.resultList.Results.Count; n++)
                {
                    var cheapest = 0d;
                    var expensive = 0d;

                    for (var r = 0; r < model.resultList.Results[n].Document.Rooms.Length; r++)
                    {
                        var rate = model.resultList.Results[n].Document.Rooms[r].BaseRate;
                        if (rate < cheapest || cheapest == 0)
                        {
                            cheapest = (double)rate;
                        }
                        if (rate > expensive)
                        {
                            expensive = (double)rate;
                        }
                    }
                    model.resultList.Results[n].Document.cheapest = cheapest;
                    model.resultList.Results[n].Document.expensive = expensive;
                }
            }
            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View("Index", model);
        }
    ```

    Leia os comentários de cada uma das seleções do **switch.**

5. Não precisamos de fazer quaisquer alterações à ação **Seguinte,** se tiver concluído o código adicional para a secção anterior sobre a encomenda com base em múltiplas propriedades.

### <a name="run-and-test-the-app"></a>Executar e testar a aplicação

1. Execute a aplicação. Você deve ver um conjunto completo de comodidades na vista.

1. Para encomendar, a seleção de "Por Classificação numérica" vai dar-lhe o pedido numérico que já implementou neste tutorial, com data de renovação a decidir entre hotéis de igual classificação.

   ![Encomendar "praia" com base na classificação](./media/tutorial-csharp-create-first-app/azure-search-orders-beach.png)

1. Agora tente o perfil "Por amenidades". Faça várias seleções de comodidades e verifique se os hotéis com essas comodidades são promovidos na lista de resultados.

   ![Encomendando "praia" com base no perfil](./media/tutorial-csharp-create-first-app/azure-search-orders-beach-profile.png)

1. Experimente o "By Renovated date/Rating profile" para ver se consegue o que espera. Só os hotéis recentemente renovados devem ter um aumento _de frescura._

### <a name="resources"></a>Recursos

Para obter mais informações, consulte os [seguintes perfis de pontuação adicionar a um índice de Pesquisa Cognitiva Azure](./index-add-scoring-profiles.md).

## <a name="takeaways"></a>Conclusões

Considere os seguintes takeaways deste projeto:

* Os utilizadores esperam que os resultados da pesquisa sejam encomendados, o mais relevante primeiro.
* Os dados precisam de ser estruturados para que a encomenda seja fácil. Não fomos capazes de classificar o "mais barato" primeiro facilmente, uma vez que os dados não são estruturados para permitir que a encomenda seja feita sem código adicional.
* Pode haver muitos níveis para encomendar, para diferenciar entre resultados que têm o mesmo valor a um nível mais elevado de encomenda.
* É natural que alguns resultados sejam encomendados por ordem ascendente (por exemplo, distância de um ponto), e alguns em ordem descendente (por exemplo, classificação do hóspede).
* Os perfis de pontuação podem ser definidos quando as comparações numéricas não estão disponíveis, ou não são inteligentes o suficiente, para um conjunto de dados. Marcar cada resultado ajudará a encomendar e mostrar os resultados de forma inteligente.

## <a name="next-steps"></a>Passos seguintes

Você completou esta série de tutoriais C# - você deveria ter adquirido conhecimento valioso das APIs de Pesquisa Cognitiva Azure.

Para mais referências e tutoriais, considere navegar no [Microsoft Learn,](/learn/browse/?products=azure)ou nos outros tutoriais da Documentação de Pesquisa Cognitiva do [Azure](./index.yml).