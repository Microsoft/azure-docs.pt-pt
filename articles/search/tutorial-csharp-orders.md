---
title: C#tutorial sobre resultados de ordenação-Azure Search
description: Este tutorial se baseia no projeto "paginação de resultados da pesquisa-Azure Search", para adicionar a ordenação dos resultados da pesquisa. Saiba como ordenar resultados em uma propriedade primária e para resultados que têm a mesma propriedade primária, como ordenar resultados em uma propriedade secundária. Por fim, saiba como ordenar resultados com base em um perfil de pontuação.
services: search
ms.service: search
ms.topic: tutorial
ms.author: v-pettur
author: PeterTurcan
ms.date: 06/21/2019
ms.openlocfilehash: 684ce33e5ecf587aa2030a817680f2d405225117
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327654"
---
# <a name="c-tutorial-order-the-results---azure-search"></a>C#destina Ordenar os resultados-Azure Search

Até esse ponto em nossa série de tutoriais, os resultados são retornados e exibidos em uma ordem padrão. Essa pode ser a ordem na qual os dados estão localizados ou, possivelmente, um _perfil de Pontuação_ padrão definido, que será usado quando nenhum parâmetro de ordenação for especificado. Neste tutorial, veremos como ordenar os resultados com base em uma propriedade primária e, em seguida, para os resultados que têm a mesma propriedade primária, como ordenar essa seleção em uma propriedade secundária. Como uma alternativa à ordenação com base em valores numéricos, o exemplo final mostra como ordenar com base em um perfil de Pontuação personalizado. Também vamos mais fundo na exibição de _tipos complexos_.

Para comparar os resultados retornados com facilidade, este projeto se baseia no projeto de rolagem infinito criado no tutorial @no__t-C# 0: Paginação de resultados da pesquisa-Azure Search o tutorial @ no__t-0.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Ordenar resultados com base em uma propriedade
> * Ordenar resultados com base em várias propriedades
> * Filtrar resultados com base em uma distância de um ponto geográfico
> * Ordenar resultados com base em um perfil de Pontuação

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

Ter a versão de rolagem infinita do tutorial @no__tC# -0: Paginação dos resultados da pesquisa-Azure Search projeto @ no__t-0 em funcionamento. Este projeto pode ser sua própria versão ou instalá-lo do GitHub: [Crie o primeiro aplicativo](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="order-results-based-on-one-property"></a>Ordenar resultados com base em uma propriedade

Quando ordenamos os resultados com base em uma propriedade, digamos a classificação do Hotel, não só queremos os resultados ordenados, também queremos confirmar que a ordem está correta. Em outras palavras, se solicitarmos a classificação, devemos exibir a classificação na exibição.

Neste tutorial, também adicionaremos um pouco mais à exibição de resultados, à taxa de sala mais barata e à taxa de sala mais cara para cada hotel. Conforme nos aprofundamos na ordenação, também adicionaremos valores para garantir que o que estamos classificando também será exibido na exibição.

Não é necessário modificar nenhum dos modelos para habilitar a ordenação. A exibição e o controlador precisam ser atualizados. Comece abrindo o controlador doméstico.

### <a name="add-the-orderby-property-to-the-search-parameters"></a>Adicionar a propriedade OrderBy aos parâmetros de pesquisa

1. Tudo o que é necessário para ordenar os resultados com base em uma única propriedade numérica é definir o parâmetro **OrderBy** como o nome da propriedade. No método de **índice (modelo SearchData)** , adicione a seguinte linha aos parâmetros de pesquisa.

    ```cs
        OrderBy = new[] { "Rating desc" },
    ```

    >[!Note]
    > A ordem padrão é ascendente, embora você possa adicionar **ASC** à propriedade para tornar isso claro. Ordem decrescente é especificada adicionando **desc**.

2. Agora, execute o aplicativo e insira qualquer termo de pesquisa comum. Os resultados podem ou não estar na ordem correta, já que você não é o desenvolvedor, e não o usuário, tem qualquer maneira fácil de verificar os resultados!

3. Vamos deixar claro que os resultados são ordenados na classificação. Primeiro, substitua as classes **box1** e **box2** no arquivo Hotéis. css pelas seguintes classes (essas classes são todas as novas que precisamos para este tutorial).

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
    >Os navegadores normalmente armazenam em cache arquivos CSS, e isso pode levar a um antigo arquivo CSS que está sendo usado e suas edições são ignoradas. Uma boa maneira de arredondar isso é adicionar uma cadeia de caracteres de consulta com um parâmetro de versão ao link. Por exemplo:
    >
    >```html
    >   <link rel="stylesheet" href="~/css/hotels.css?v1.1" />
    >```
    >
    >Atualize o número de versão se você considerar que um arquivo CSS antigo está sendo usado pelo seu navegador.

4. Adicione a propriedade **rating** ao parâmetro **Select** , no método **Index (modelo SearchData)** .

    ```cs
    Select = new[] { "HotelName", "Description", "Rating"},
    ```

5. Abra o modo de exibição (index. cshtml) e substitua o loop de renderização ( **&lt;!--mostrar os dados do hotel.--&gt;** ) pelo código a seguir.

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

6. A classificação precisa estar disponível na primeira página exibida e nas páginas subsequentes que são chamadas por meio da rolagem infinita. Para a última dessas duas situações, precisamos atualizar a **próxima** ação no controlador e a função **rolada** no modo de exibição. Começando com o controlador, altere o **próximo** método para o código a seguir. Esse código cria e comunica o texto de classificação.

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

7. Agora, atualize a função **rolada** na exibição para exibir o texto de classificação.

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

8. Agora execute o aplicativo novamente. Pesquise em qualquer termo comum, como "WiFi", e verifique se os resultados são ordenados por ordem decrescente de classificação do hotel.

    ![Ordenação com base na classificação](./media/tutorial-csharp-create-first-app/azure-search-orders-rating.png)

    Você observará que vários hotéis têm uma classificação idêntica e, portanto, sua aparência na exibição é novamente a ordem na qual os dados são encontrados, o que é arbitrário.

    Antes de examinarmos a adição de um segundo nível de ordenação, vamos adicionar algum código para exibir o intervalo de taxas de sala. Estamos adicionando esse código para que ambos mostrem a extração de dados de um _tipo complexo_e, assim, podemos discutir os resultados da ordem com base no preço (mais barato primeiro, talvez).

### <a name="add-the-range-of-room-rates-to-the-view"></a>Adicionar o intervalo de taxas de sala à exibição

1. Adicione propriedades que contenham a taxa de sala mais barata e cara ao modelo Hotel.cs.

    ```cs
        // Room rate range
        public double cheapest { get; set; }
        public double expensive { get; set; }
    ```

2. Calcule as taxas de sala no final da ação de **índice (modelo de SearchData)** , no controlador doméstico. Adicione os cálculos após o armazenamento de dados temporários.

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

3. Adicione a propriedade **Rooms** ao parâmetro **Select** no método de ação **Index (modelo SearchData)** do controlador.

    ```cs
     Select = new[] { "HotelName", "Description", "Rating", "Rooms" },
    ```

4. Altere o loop de renderização na exibição para exibir o intervalo de taxa da primeira página de resultados.

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

5. Altere o **próximo** método no controlador Home para comunicar o intervalo de taxas para páginas subsequentes de resultados.

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

6. Atualize a função **rolada** na exibição para manipular o texto das taxas de sala.

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

7. Execute o aplicativo e verifique se os intervalos de taxa de sala são exibidos.

    ![Exibindo intervalos de taxa de sala](./media/tutorial-csharp-create-first-app/azure-search-orders-rooms.png)

A propriedade **OrderBy** dos parâmetros de pesquisa não aceitará uma entrada como **Rooms. BaseRate** para fornecer a taxa de sala mais barata, mesmo que as salas já tenham sido classificadas em taxa. Nesse caso, as salas não são classificadas em taxa. Para exibir os hotéis no conjunto de dados de exemplo, ordenado na taxa de sala, você teria que classificar os resultados em seu controlador doméstico e enviar esses resultados para a exibição na ordem desejada.

## <a name="order-results-based-on-multiple-values"></a>Ordenar resultados com base em vários valores

A pergunta agora é como diferenciar entre hotéis com a mesma classificação. Uma boa maneira seria ordenar a base da última vez em que o Hotel foi renovados. Em outras palavras, quanto mais recentemente o Hotel era renovados, mais alto o Hotel aparece nos resultados.

1. Para adicionar um segundo nível de ordenação, altere o **OrderBy** e **selecione** Propriedades no método **Index (modelo SearchData)** para incluir a propriedade **LastRenovationDate** .

    ```cs
    OrderBy = new[] { "Rating desc", "LastRenovationDate desc" },
    Select = new[] { "HotelName", "Description", "Rating", "Rooms", "LastRenovationDate" },
    ```

    >[!Tip]
    >Qualquer número de propriedades pode ser inserido na lista **OrderBy** . Se os hotéis tivessem a mesma classificação e a data de reforma, uma terceira Propriedade poderia ser inserida para diferenciá-los.

2. Novamente, precisamos ver a data de reforma na exibição, apenas para ter certeza de que a ordem está correta. Para isso como um reforma, provavelmente o ano é necessário. Altere o loop de renderização na exibição para o código a seguir.

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

3. Altere o **próximo** método no controlador Home para encaminhar o componente de ano da última data de reforma.

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

4. Altere a função **rolada** na exibição para exibir o texto reforma.

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

5. Execute o aplicativo. Pesquise em um termo comum, como "pool" ou "exibição", e verifique se os hotéis com a mesma classificação agora são exibidos em ordem decrescente de data de reforma.

    ![Ordenando na data de reforma](./media/tutorial-csharp-create-first-app/azure-search-orders-renovation.png)

## <a name="filter-results-based-on-a-distance-from-a-geographical-point"></a>Filtrar resultados com base em uma distância de um ponto geográfico

Classificação e data de reforma, são exemplos de propriedades que são mais bem exibidas em ordem decrescente. Uma listagem alfabética seria um exemplo de um bom uso de ordem crescente (por exemplo, se houvesse apenas uma propriedade **OrderBy** e foi definida como **hotelname** , uma ordem alfabética seria exibida). No entanto, para nossos dados de exemplo, a distância de um ponto geográfico seria mais apropriada.

Para exibir resultados com base em distância geográfica, várias etapas são necessárias.

1. Filtre todos os hotéis que estão fora de um raio especificado do ponto determinado, inserindo um filtro com os parâmetros de longitude, latitude e RADIUS. A longitude é dada primeiro à função POINT. O raio está em quilômetros.

    ```cs
        // "Location" must match the field name in the Hotel class.
        // Distance (the radius) is in kilometers.
        // Point order is Longitude then Latitude.
        Filter = $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') le {model.radius}",
    ```

2. O filtro acima não _ordena os_ resultados com base na distância, apenas remove as exceções. Para ordenar os resultados, insira uma configuração **OrderBy** que especifica o método de interurbano.

    ```cs
    OrderBy = new[] { $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') asc" },
    ```

3. Embora os resultados tenham sido retornados por Azure Search usando um filtro de distância, a distância calculada entre os dados e o ponto especificado _não_ é retornada. Recalcule esse valor na exibição, ou controlador, se você quiser exibi-lo nos resultados.

    O código a seguir calculará a distância entre dois pontos Lat/Lon.

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

4. Agora, você precisa reunir esses conceitos. No entanto, esses trechos de código estão no que diz respeito ao nosso tutorial, criar um aplicativo baseado em mapa é deixado como um exercício para o leitor. Para obter este exemplo mais detalhadamente, considere inserir um nome de cidade com um raio ou localizar um ponto em um mapa e selecionar um raio. Para investigar mais essas opções, consulte os seguintes recursos:

* [Documentação do Azure Maps](https://docs.microsoft.com/azure/azure-maps/)
* [Localizar um endereço usando o serviço de pesquisa do Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address)

## <a name="order-results-based-on-a-scoring-profile"></a>Ordenar resultados com base em um perfil de Pontuação

Os exemplos fornecidos no tutorial até agora mostram como ordenar valores numéricos (classificação, data de reforma, distância geográfica), fornecendo um processo _exato_ de ordenação. No entanto, algumas pesquisas e alguns dados não se prestam a uma comparação tão fácil entre dois elementos de dados. Azure Search inclui o conceito de _Pontuação_. Os _perfis de Pontuação_ podem ser especificados para um conjunto de dados que podem ser usados para fornecer comparações mais complexas e qualitativas, que devem ser mais valiosas quando, digamos, comparar dados baseados em texto para decidir quais devem ser exibidos primeiro.

Os perfis de pontuação não são definidos pelos usuários, mas normalmente por administradores de um conjunto de dados. Vários perfis de Pontuação foram configurados nos dados de hotéis. Vamos ver como um perfil de pontuação é definido e, em seguida, tentar escrever o código para pesquisá-los.

### <a name="how-scoring-profiles-are-defined"></a>Como os perfis de pontuação são definidos

Vamos examinar três exemplos de perfis de Pontuação e considerar como cada um _deve_ afetar a ordem dos resultados. Como desenvolvedor de aplicativos, você não escreve esses perfis, eles são escritos pelo administrador de dados, no entanto, é útil examinar a sintaxe.

1. Este é o perfil de Pontuação padrão para o conjunto de dados de hotéis, usado quando você não especifica nenhum parâmetro **OrderBy** ou **ScoringProfile** . Esse perfil aumenta a _Pontuação_ de um hotel se o texto de pesquisa estiver presente no nome do Hotel, na descrição ou na lista de marcas (comodidades). Observe como os pesos da Pontuação favorecem determinados campos. Se o texto de pesquisa aparecer em outro campo, não listado abaixo, ele terá um peso de 1. Obviamente, quanto mais alta a pontuação, mais cedo um resultado aparece na exibição.

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

2. O seguinte perfil de Pontuação aumenta a pontuação significativamente, se um parâmetro fornecido incluir uma ou mais das marcas (que estamos chamando "comodidades"). O ponto-chave desse perfil é que um parâmetro _deve_ ser fornecido, contendo o texto. Se o parâmetro estiver vazio ou não for fornecido, um erro será gerado.
 
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

3. Neste terceiro exemplo, a classificação fornece um aumento significativo da pontuação. A data da última renovados também aumentará a pontuação, mas somente se os dados estiverem dentro de 730 dias (2 anos) da data atual.

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

    Agora, vamos ver se esses perfis funcionam como achamos que deveriam!

### <a name="add-code-to-the-view-to-compare-profiles"></a>Adicionar código à exibição para comparar perfis

1. Abra o arquivo index. cshtml e substitua a seção &lt;body @ no__t-1 pelo código a seguir.

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

2. Abra o arquivo SearchData.cs e substitua a classe **SearchData** pelo código a seguir.

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

3. Abra o arquivo Hotéis. css e adicione as classes HTML a seguir.

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

### <a name="add-code-to-the-controller-to-specify-a-scoring-profile"></a>Adicionar código ao controlador para especificar um perfil de Pontuação

1. Abra o arquivo do controlador Home. Adicione a seguinte instrução **using** (para auxiliar na criação de listas).

    ```cs
    using System.Linq;
    ```

2.  Para este exemplo, precisamos da chamada inicial para **index** para fazer um pouco mais do que apenas retornar a exibição inicial. O método agora procura até 20 comodidades para exibir na exibição.

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

3. Precisamos de dois métodos privados para salvar as facetas no armazenamento temporário e recuperá-las do armazenamento temporário e preencher um modelo.

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

4. Precisamos definir os parâmetros **OrderBy** e **ScoringProfile** conforme necessário. Substitua o método de **índice existente (modelo SearchData)** pelo seguinte.

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

    Leia os comentários para cada uma das seleções de **comutador** .

5. Não precisamos fazer nenhuma alteração na **próxima** ação, se você tiver concluído o código adicional para a seção anterior sobre ordenação com base em várias propriedades.

### <a name="run-and-test-the-app"></a>Executar e testar o aplicativo

1. Execute o aplicativo. Você deve ver um conjunto completo de comodidades na exibição.

2. Para ordenação, selecionar "por classificação numérica" fornecerá a ordem numérica que você já implementou neste tutorial, com reforma data decidindo entre hotéis de classificação igual.

![Ordenando a "praia" com base na classificação](./media/tutorial-csharp-create-first-app/azure-search-orders-beach.png)

3. Agora, experimente o perfil "por comodidades". Faça várias seleções de comodidades e verifique se os hotéis com esses comodidades são promovidos para cima na lista de resultados.

![Ordenando a "praia" com base no perfil](./media/tutorial-csharp-create-first-app/azure-search-orders-beach-profile.png)

4. Experimente o "perfil de classificação/data de renovados" para ver se você tem o que espera. Somente os hotéis renovadoss recentemente devem obter um aumento de _atualização_ .

### <a name="resources"></a>Recursos

Para obter mais informações, consulte os seguintes [perfis de Pontuação de adição a um índice de Azure Search](https://docs.microsoft.com/azure/search/index-add-scoring-profiles).

## <a name="takeaways"></a>Conclusões

Considere as seguintes alternativas deste projeto:

* Os usuários esperam que os resultados da pesquisa sejam ordenados, mais relevantes primeiro.
* As necessidades de dados são estruturadas para que a ordem seja fácil. Não foi possível classificar o "mais barato" com mais facilidade, pois os dados não são estruturados para permitir que a ordem seja feita sem código adicional.
* Pode haver muitos níveis a serem ordenados, para diferenciar os resultados que têm o mesmo valor em um nível mais alto de ordenação.
* É natural que alguns resultados sejam ordenados em ordem crescente (digamos, distância de um ponto) e alguns em ordem decrescente (digamos, classificação do convidado).
* Os perfis de Pontuação podem ser definidos quando as comparações numéricas não estão disponíveis ou não são inteligentes o suficiente para um conjunto de dados. A pontuação de cada resultado ajudará a ordenar e exibir os resultados de forma inteligente.

## <a name="next-steps"></a>Passos seguintes

Você concluiu esta série de C# tutoriais: você deve ter obtido conhecimento valioso das APIs de Azure Search.

Para obter mais referências e tutoriais, considere procurar [Microsoft Learn](https://docs.microsoft.com/learn/browse/?products=azure)ou outros tutoriais na documentação do [Azure Search](https://docs.microsoft.com/azure/search/).
