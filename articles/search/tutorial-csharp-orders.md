---
title: C#tutorial sobre a encomenda de resultados
titleSuffix: Azure Cognitive Search
description: Este tutorial demonstra como encomendar resultados de pesquisa. Baseia-se num projeto de hotéis anteriores, encomendando por propriedade primária, propriedade secundária, e inclui um perfil de pontuação para adicionar critérios de reforço.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/10/2020
ms.openlocfilehash: 812085a5a4b3e8d1233f19c947d2fd5e433f6ab7
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77121560"
---
# <a name="c-tutorial-order-the-results---azure-cognitive-search"></a>C#Tutorial: Encomende os resultados - Pesquisa Cognitiva Azure

Até este ponto da nossa série de tutoriais, os resultados são devolvidos e apresentados por ordem padrão. Esta pode ser a ordem em que os dados estão localizados, ou possivelmente um perfil de _pontuação_ padrão foi definido, que será usado quando não forem especificados parâmetros de encomenda. Neste tutorial, vamos entrar em como encomendar resultados com base numa propriedade primária, e depois para resultados que têm a mesma propriedade primária, como encomendar essa seleção em uma propriedade secundária. Como alternativa ao pedido com base em valores numéricos, o exemplo final mostra como encomendar com base num perfil de pontuação personalizado. Iremos também um pouco mais fundo na exibição de _tipos complexos._

De forma a comparar facilmente os resultados devolvidos, este projeto baseia-se no projeto de scrolling infinito criado no [ C# Tutorial: Pesquisa resultados paginação - Tutorial de Pesquisa Cognitiva Azure.](tutorial-csharp-paging.md)

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Resultados da encomenda com base num imóvel
> * Resultados da encomenda com base em múltiplas propriedades
> * Resultados do filtro com base numa distância de um ponto geográfico
> * Resultados da encomenda com base num perfil de pontuação

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

Tenha a versão infinita de scrolling do [ C# Tutorial: Resultados de pesquisa paginação - Projeto](tutorial-csharp-paging.md) de Pesquisa Cognitiva Azure em funcionamento. Este projeto pode ser a sua própria versão, ou instalá-lo a partir do GitHub: [Criar a primeira aplicação](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="order-results-based-on-one-property"></a>Resultados da encomenda com base num imóvel

Quando encomendamos resultados com base numa propriedade, por exemplo, classificação hoteleira, não só queremos os resultados ordenados, como também queremos a confirmação de que a encomenda está correta. Por outras palavras, se encomendarmos a classificação, devemos exibir a classificação na vista.

Neste tutorial, vamos também adicionar um pouco mais à exibição de resultados, a tarifa de quarto mais barata, e a tarifa de quarto mais cara, para cada hotel. À medida que nos debruçamos sobre a encomenda, também vamos adicionar valores para garantir que o que estamos a encomendar também é exibido na vista.

Não há necessidade de modificar nenhum dos modelos para permitir a encomenda. A vista e o controlador precisam de ser atualizados. Comece por abrir o controlador da casa.

### <a name="add-the-orderby-property-to-the-search-parameters"></a>Adicione a propriedade OrderBy aos parâmetros de pesquisa

1. Tudo o que é preciso para encomendar resultados com base numa única propriedade numérica, é definir o parâmetro **OrderBy** para o nome da propriedade. No método **Index(SearchData),** adicione a seguinte linha aos parâmetros de pesquisa.

    ```cs
        OrderBy = new[] { "Rating desc" },
    ```

    >[!Note]
    > A ordem padrão está a subir, embora possa adicionar **asc** à propriedade para deixar isso claro. A ordem descendente é especificada adicionando **desc**.

2. Agora execute a aplicação e introduza qualquer termo de pesquisa comum. Os resultados podem ou não estar na ordem correta, uma vez que nem o desenvolvedor, nem o utilizador, tem uma forma fácil de verificar os resultados!

3. Vamos deixar claro que os resultados são encomendados na classificação. Em primeiro lugar, substitua as aulas **box1** e **box2** nos ficheiros hotels.css pelas seguintes aulas (estas aulas são todas as novas que precisamos para este tutorial).

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

    >[!Tip]
    >Os navegadores geralmente cache css ficheiros, e isso pode levar a que um ficheiro CSS antigo seja usado, e suas edidas ignoradas. Uma boa maneira de contornar isto é adicionar uma corda de consulta com um parâmetro de versão ao link. Por exemplo:
    >
    >```html
    >   <link rel="stylesheet" href="~/css/hotels.css?v1.1" />
    >```
    >
    >Atualize o número da versão se achar que um ficheiro CSS antigo está a ser utilizado pelo seu navegador.

4. Adicione a propriedade **Rating** ao parâmetro **Select,** no método **Index(SearchData).**

    ```cs
    Select = new[] { "HotelName", "Description", "Rating"},
    ```

5. Abra a vista (index.cshtml) e substitua o loop de renderização **(&lt;!-- Mostrar os dados do hotel&gt;.**

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

6. A classificação precisa de estar disponível tanto na primeira página apresentada, como nas páginas seguintes que são chamadas através do pergaminho infinito. Para esta última destas duas situações, precisamos de atualizar tanto a **próxima** ação no controlador como a função **deslocada** na vista. A partir do controlador, altere o método **Seguinte** para o seguinte código. Este código cria e comunica o texto de classificação.

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

7. Agora atualize a função **deslocada** na vista, para visualizar o texto de classificação.

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

8. Agora, execute a aplicação novamente. Procure em qualquer termo comum, como "wifi", e verifique se os resultados são encomendados por ordem descendente da classificação hoteleira.

    ![Encomenda com base na classificação](./media/tutorial-csharp-create-first-app/azure-search-orders-rating.png)

    Você vai notar que vários hotéis têm uma classificação idêntica, e assim a sua aparência no ecrã é novamente a ordem em que os dados são encontrados, que é arbitrário.

    Antes de adicionarmos um segundo nível de encomenda, vamos adicionar algum código para mostrar a gama de tarifas de quarto. Estamos adicionando este código para mostrar dados extratos de um _tipo complexo_, e também para que possamos discutir a encomenda de resultados com base no preço (primeiro mais barato, talvez).

### <a name="add-the-range-of-room-rates-to-the-view"></a>Adicione a gama de tarifas de quarto à vista

1. Adicione propriedades que contenham a tarifa de quarto mais barata e cara ao modelo Hotel.cs.

    ```cs
        // Room rate range
        public double cheapest { get; set; }
        public double expensive { get; set; }
    ```

2. Calcular as taxas de quarto no final da ação **Index(SearchData)** no controlador doméstico. Adicione os cálculos após o armazenamento de dados temporários.

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

3. Adicione a propriedade **dos Quartos** ao parâmetro **Select** no método de ação **Index (SearchData)** do controlador.

    ```cs
     Select = new[] { "HotelName", "Description", "Rating", "Rooms" },
    ```

4. Altere o ciclo de renderização na vista para visualizar a gama de tarifas para a primeira página dos resultados.

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

5. Altere o método **Seguinte** no controlador doméstico para comunicar a gama de tarifas, para páginas subsequentes de resultados.

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

6. Atualize a função **deslocada** na vista, para lidar com o texto das tarifas do quarto.

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

7. Executar a aplicação e verificar se as gamas de tarifas do quarto são exibidas.

    ![Exibindo gamas de tarifas de quarto](./media/tutorial-csharp-create-first-app/azure-search-orders-rooms.png)

A propriedade **OrderBy** dos parâmetros de pesquisa não aceitará uma entrada como **Rooms.BaseRate** para fornecer a tarifa de quarto mais barata, mesmo que os quartos já estivessem classificados a preço. Neste caso, os quartos não estão classificados à taxa. Para exibir hotéis no conjunto de dados da amostra, encomendado sondar a tarifa do quarto, você teria que classificar os resultados no seu controlador doméstico, e enviar estes resultados para a vista na ordem desejada.

## <a name="order-results-based-on-multiple-values"></a>Resultados da encomenda com base em vários valores

A questão agora é como diferenciar os hotéis com a mesma classificação. Uma boa maneira seria encomendar com base na última vez que o hotel foi renovado. Por outras palavras, quanto mais recente o hotel foi renovado, maior o hotel aparece nos resultados.

1. Para adicionar um segundo nível de encomenda, altere as propriedades **OrderBy** e **Select** no método **Index(SearchData)** para incluir a propriedade **LastRenovationDate.**

    ```cs
    OrderBy = new[] { "Rating desc", "LastRenovationDate desc" },
    Select = new[] { "HotelName", "Description", "Rating", "Rooms", "LastRenovationDate" },
    ```

    >[!Tip]
    >Qualquer número de propriedades pode ser inserida na lista **OrderBy.** Se os hotéis tivessem a mesma classificação e data de renovação, um terceiro imóvel poderia ser introduzido para diferenciar entre eles.

2. Mais uma vez, precisamos de ver a data de renovação na vista, só para ter a certeza de que a encomenda está correta. Para uma coisa como uma renovação, provavelmente apenas o ano é necessário. Altere o laço de renderização na vista para o seguinte código.

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

3. Altere o método **Seguinte** no controlador doméstico, para encaminhar a componente do ano da última data de renovação.

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

4. Altere a função **deslocada** na vista para exibir o texto de renovação.

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

5. Executar a aplicação. Procure um termo comum, como "pool" ou "view", e verifique se os hotéis com a mesma classificação são agora apresentados por ordem descendente de data de renovação.

    ![Encomenda na data de renovação](./media/tutorial-csharp-create-first-app/azure-search-orders-renovation.png)

## <a name="filter-results-based-on-a-distance-from-a-geographical-point"></a>Resultados do filtro com base numa distância de um ponto geográfico

Classificação e data de renovação, são exemplos de propriedades que são melhor exibidas em uma ordem descendente. Uma listagem alfabética seria um exemplo de um bom uso da ordem ascendente (por exemplo, se houvesse apenas uma propriedade **OrderBy,** e foi definido para **HotelName** então uma ordem alfabética seria exibida). No entanto, para os nossos dados de amostra, a distância de um ponto geográfico seria mais adequada.

Para apresentar resultados baseados na distância geográfica, são necessários vários passos.

1. Filtre todos os hotéis que estão fora de um raio especificado a partir do ponto dado, introduzindo um filtro com longitude, latitude e parâmetros de raio. A longitude é dada primeiro à função POINT. O raio está em quilómetros.

    ```cs
        // "Location" must match the field name in the Hotel class.
        // Distance (the radius) is in kilometers.
        // Point order is Longitude then Latitude.
        Filter = $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') le {model.radius}",
    ```

2. O filtro acima _não_ encomenda os resultados com base na distância, apenas remove os outliers. Para encomendar os resultados, introduza uma definição **OrderBy** que especifica o método geoDistance.

    ```cs
    OrderBy = new[] { $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') asc" },
    ```

3. Embora os resultados tenham sido devolvidos pela Azure Cognitive Search utilizando um filtro de distância, a distância calculada entre os dados e o ponto especificado _não_ é devolvida. Recalcule este valor na vista, ou no controlador, se quiser exibi-lo nos resultados.

    O código seguinte calculará a distância entre dois pontos lat/lon.

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

4. Agora tens de juntar estes conceitos. No entanto, estes códigos são tão longe quanto o nosso tutorial vai, construir uma aplicação baseada em mapas é deixado como um exercício para o leitor. Para levar este exemplo mais longe, considere entrar num nome da cidade com um raio, ou localizar um ponto num mapa, e selecionar um raio. Para investigar mais aprofundadamente estas opções, consulte os seguintes recursos:

* [Documentação do Azure Maps](https://docs.microsoft.com/azure/azure-maps/)
* [Encontre um endereço usando o serviço de pesquisa Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address)

## <a name="order-results-based-on-a-scoring-profile"></a>Resultados da encomenda com base num perfil de pontuação

Os exemplos dados no tutorial até agora mostram como encomendar valores numéricos (classificação, data de renovação, distância geográfica), proporcionando um processo _exato_ de encomenda. No entanto, algumas pesquisas e alguns dados não se prestam a uma comparação tão fácil entre dois elementos de dados. A Pesquisa Cognitiva Azure inclui o conceito de _pontuação._ Os perfis de _pontuação_ podem ser especificados para um conjunto de dados que podem ser usados para fornecer comparações mais complexas e qualitativas, que devem ser mais valiosas quando, por exemplo, comparar dados baseados em texto para decidir quais devem ser apresentados primeiro.

Os perfis de pontuação não são definidos pelos utilizadores, mas tipicamente por administradores de um conjunto de dados. Vários perfis de pontuação foram criados nos dados dos hotéis. Vamos ver como um perfil de pontuação é definido, em seguida, tentar escrever código para procurá-los.

### <a name="how-scoring-profiles-are-defined"></a>Como os perfis de pontuação são definidos

Vamos olhar para três exemplos de perfis de pontuação, e considerar como cada um _deve_ afetar a ordem de resultados. Como desenvolvedor de aplicações, não escreve estes perfis, são escritos pelo administrador de dados, no entanto, é útil olhar para a sintaxe.

1. Este é o perfil de pontuação padrão para o conjunto de dados dos hotéis, usado quando não especifica nenhum parâmetro **OrderBy** ou **ScoringProfile.** Este perfil aumenta a _pontuação_ para um hotel se o texto de pesquisa estiver presente no nome, descrição ou lista de etiquetas do hotel (amenidades). Repare como os pesos da pontuação favorecem certos campos. Se o texto de pesquisa aparecer noutro campo, não listado abaixo, terá um peso de 1. Obviamente, quanto maior for a pontuação, mais cedo um resultado aparece na vista.

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

2. O perfil de pontuação seguinte aumenta significativamente a pontuação, se um parâmetro fornecido incluir uma ou mais das etiquetas (a que chamamos "amenidades"). O ponto-chave deste perfil é que _deve_ ser fornecido um parâmetro, contendo texto. Se o parâmetro estiver vazio ou não for fornecido, será lançado um erro.
 
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

3. Neste terceiro exemplo, a classificação dá um impulso significativo à pontuação. A última data renovada também irá aumentar a pontuação, mas apenas se esses dados se enquadrarem no prazo de 730 dias (2 anos) da data atual.

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

1. Abra o ficheiro index.cshtml e substitua a secção &lt;&gt; corpo pelo seguinte código.

    ```cs
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

2. Abra o ficheiro SearchData.cs e substitua a classe **SearchData** pelo seguinte código.

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

3. Abra o ficheiro hotels.css e adicione as seguintes aulas html.

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

1. Abra o ficheiro do controlador da casa. Adicione a seguinte declaração **utilizando** (para ajudar na criação de listas).

    ```cs
    using System.Linq;
    ```

2.  Para este exemplo, precisamos da chamada inicial para o **Index** para fazer um pouco mais do que apenas devolver a visão inicial. O método procura agora até 20 comodidades para exibir na vista.

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

3. Precisamos de dois métodos privados para salvar as facetas do armazenamento temporário, e para recuperá-las do armazenamento temporário e povoar um modelo.

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

4. Precisamos definir os parâmetros **OrderBy** e **ScoringProfile** conforme necessário. Substitua o método de **Índice (Modelo SearchData)** existente, pelo seguinte.

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

    Leia os comentários para cada uma das seleções de **switch.**

5. Não precisamos de fazer alterações à **ação Seguinte,** se tiver concluído o código adicional para a secção anterior sobre a encomenda com base em múltiplas propriedades.

### <a name="run-and-test-the-app"></a>Executar e testar a app

1. Executar a aplicação. Devia ver um conjunto completo de comodidades na vista.

2. Para encomendar, a seleção "Por Classificação Numérica" lhe dará a ordem numérica que já implementou neste tutorial, com data de renovação a decidir entre hotéis de igual classificação.

![Encomendar "praia" com base na classificação](./media/tutorial-csharp-create-first-app/azure-search-orders-beach.png)

3. Agora tente o perfil "Por amenidades". Faça várias seleções de comodidades e verifique se os hotéis com essas comodidades são promovidos na lista de resultados.

![Encomendar "praia" com base no perfil](./media/tutorial-csharp-create-first-app/azure-search-orders-beach-profile.png)

4. Experimente o "Por Perfil de Data/Classificação Renovado" para ver se obtém o que espera. Só os hotéis recentemente renovados deverão receber um aumento de _frescura._

### <a name="resources"></a>Recursos

Para mais informações, consulte os [seguintes perfis de pontuação adicionar a um índice](https://docs.microsoft.com/azure/search/index-add-scoring-profiles)de pesquisa cognitiva Azure .

## <a name="takeaways"></a>Conclusões

Considere os seguintes takeaways deste projeto:

* Os utilizadores esperam que os resultados da pesquisa sejam encomendados, o mais relevante primeiro.
* Os dados precisam de ser estruturados para que a encomenda seja fácil. Não conseguimos classificar-nos facilmente em "mais barato", uma vez que os dados não são estruturados para permitir que a encomenda seja feita sem código adicional.
* Pode haver muitos níveis para ordenar, para diferenciar entre resultados que têm o mesmo valor a um nível mais elevado de ordenação.
* É natural que alguns resultados sejam encomendados por ordem ascendente (por exemplo, distância de um ponto), e alguns em ordem descendente (por exemplo, classificação do hóspede).
* Os perfis de pontuação podem ser definidos quando as comparações numéricas não estão disponíveis, ou não são inteligentes o suficiente para um conjunto de dados. Marcar cada resultado ajudará a encomendar e a exibir os resultados de forma inteligente.

## <a name="next-steps"></a>Passos seguintes

Você completou esta série C# de tutoriais - você deveria ter adquirido conhecimento valioso das APIs de Pesquisa Cognitiva Azure.

Para mais referências e tutoriais, considere navegar no [Microsoft Learn,](https://docs.microsoft.com/learn/browse/?products=azure)ou os outros tutoriais na Documentação de [Pesquisa Cognitiva Azure.](https://docs.microsoft.com/azure/search/)
