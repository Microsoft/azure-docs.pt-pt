---
title: C#tutorial sobre como ordenar resultados - Azure Search
description: Este tutorial baseia-se no projeto "Paginação - Azure Search de resultados de pesquisa", para adicionar a ordenação dos resultados da pesquisa. Saiba como ordenar os resultados numa propriedade primária e para obter os resultados que tenham a mesma propriedade principal, como solicitar os resultados numa propriedade secundária. Por fim, saiba como ordenar resultados com base num perfil de classificação.
services: search
ms.service: search
ms.topic: tutorial
ms.author: v-pettur
author: PeterTurcan
ms.date: 06/21/2019
ms.openlocfilehash: 32e253b4e131d753ab6937d0aa2a49bda471e091
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466602"
---
# <a name="c-tutorial-order-the-results---azure-search"></a>C#Tutorial: Ordena os resultados - Azure Search

Até este ponto de nossa série de tutoriais, os resultados são devolvidos e exibidos numa ordem de predefinição. Isso pode ser a ordem em que os dados se encontra ou, possivelmente, uma predefinição _perfil de classificação_ tiver sido definida, que será utilizado quando não existem parâmetros de ordenação especificados. Neste tutorial, Entraremos em como encomendar resultados com base numa propriedade principal e, em seguida, para obter os resultados que tenham a mesma propriedade principal, como encomendar nessa seleção numa propriedade secundária. Como alternativa a ordenação com base em valores numéricos, o exemplo final mostra como ordenar com base num perfil de classificação personalizado. Será também está um pouco mais para a exibição dos _tipos complexos_.

Para comparar os resultados devolvidos facilmente, este projeto é compilado para o projeto de deslocamento infinito criado no [ C# Tutorial: Resultados da pesquisa paginação - Azure Search](tutorial-csharp-paging.md) tutorial.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Resultados de ordem, com base numa propriedade
> * Resultados de ordem, com base em várias propriedades
> * Filtrar os resultados com base numa distância de um ponto geográfico
> * Resultados de ordem, com base num perfil de classificação

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

Tem a versão de deslocamento infinita do [ C# Tutorial: Resultados da pesquisa paginação - Azure Search](tutorial-csharp-paging.md) projeto em funcionamento. Este projeto pode ser a sua própria versão ou instalá-lo a partir do GitHub: [Criar a primeira aplicação](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="order-results-based-on-one-property"></a>Resultados de ordem, com base numa propriedade

Quando vamos ordenar resultados com base numa propriedade, digamos que o hotel classificação, queremos não apenas os resultados ordenados, também Queremos que a ordem é correta de confirmação. Em outras palavras, se podemos solicitar em classificação, podemos deverá apresentar a classificação na vista.

Neste tutorial, também adicionaremos um pouco mais para a exibição dos resultados, a taxa de espaço mais barato e a taxa de espaço mais cara, para cada hotel. Como nós mergulhamos no ordenação, também adicionaremos valores certificar-se de ordenação, são também são apresentadas na vista.

Não é necessário modificar qualquer um dos modelos para ativar a ordenação. A exibição e a necessidade de controlador atualizado. Comece por abrir o controlador home.

### <a name="add-the-orderby-property-to-the-search-parameters"></a>Adicione a propriedade de OrderBy para os parâmetros de pesquisa

1. Tudo o que é preciso para os resultados de pedidos com base numa única propriedade numérica, consiste em definir os **OrderBy** parâmetro para o nome da propriedade. Na **índice (modelo de SearchData)** método, adicione a seguinte linha para os parâmetros de pesquisa.

    ```cs
        OrderBy = new[] { "Rating desc" },
    ```

    >[!Note]
    > A ordem predefinida é ascendente, embora possa adicionar **asc** para a propriedade para tornar isso claro. Por ordem descendente for especificado, adicionando **desc**.

2. Agora execute a aplicação e introduza qualquer termo de pesquisa comuns. Os resultados podem ou não estar na ordem correta, pois nem, como desenvolvedor, não pelo usuário, tem uma forma fácil de verificar os resultados!

3. Vamos torná-lo claro os resultados são ordenados na classificação. Em primeiro lugar, substitua a **box1** e **box2** classes no ficheiro hotels.css com as seguintes classes (essas classes são todos os novos que precisamos para este tutorial).

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
    >Browsers colocam em cache normalmente arquivos css, e isso pode levar a um arquivo css antigo a ser utilizado e as suas edições ignoradas. Uma boa maneira de arredondar isso é adicionar uma cadeia de consulta com um parâmetro de versão para a ligação. Por exemplo:
    >
    >```html
    >   <link rel="stylesheet" href="~/css/hotels.css?v1.1" />
    >```
    >
    >Atualize o número de versão, se acha que um arquivo css antigo está a ser utilizado pelo browser.

4. Adicionar a **classificação** propriedade para o **selecione** parâmetro, no **índice (modelo de SearchData)** método.

    ```cs
    Select = new[] { "HotelName", "Description", "Rating"},
    ```

5. Abra o modo de exibição (Index. cshtml) e substitua o loop de processamento ( **&lt;! – Mostrar dados de hotel. –&gt;** ) com o código a seguir.

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

6. A classificação tem de estar disponível na primeira página apresentada e nas páginas subsequentes que são chamadas por meio de deslocamento infinito. Para as últimas duas situações, é necessário atualizar ambos os **próxima** ação no controlador e o **rolado** função na vista de. Começando com o controlador, alterar os **seguinte** método para o código a seguir. Este código cria e comunica o texto de classificação.

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

7. Agora, atualizar o **rolado** função na vista de, para apresentar o texto de classificação.

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

8. Agora execute novamente a aplicação. Procurar em qualquer período de vigência comuns, tais como "Wi-Fi" e certifique-se de que os resultados são ordenados, por ordem descendente de classificação de hotel.

    ![Ordenação com base na classificação](./media/tutorial-csharp-create-first-app/azure-search-orders-rating.png)

    Notará que vários hotéis tem uma classificação idêntica, e, portanto, sua aparência na exibição novamente é a ordem na qual os dados são encontrados, que é arbitrária.

    Antes de olhamos para a inclusão de um segundo nível de ordenação, vamos adicionar algum código para exibir o intervalo das taxas de espaço. Estamos a adicionar este código para ambos os show extrair dados a partir de um _tipo complexo_, e também para que podemos analisar ordenação resultados com base no preço (primeiro mais barato talvez).

### <a name="add-the-range-of-room-rates-to-the-view"></a>Adicione o intervalo das taxas de espaço para a vista

1. Adicione as propriedades que contém a taxa de espaço mais barato e mais cara para o modelo de Hotel.cs.

    ```cs
        // Room rate range
        public double cheapest { get; set; }
        public double expensive { get; set; }
    ```

2. Calcular as taxas de espaço no final da **índice (modelo de SearchData)** ação, no controlador home. Adicione os cálculos após o armazenamento de dados temporários.

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

3. Adicionar a **salas** propriedade para o **selecione** parâmetro no **índice (modelo de SearchData)** método de ação do controlador de.

    ```cs
     Select = new[] { "HotelName", "Description", "Rating", "Rooms" },
    ```

4. Altere o loop de processamento na vista para apresentar o intervalo de velocidade para a primeira página de resultados.

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

5. Alteração da **seguinte** método no controlador de casa para comunicar o intervalo de taxa, para as páginas subsequentes de resultados.

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

6. Atualização do **rolado** taxas de função na vista de, para lidar com a sala de texto.

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

7. Executar a aplicação e verifique se que são apresentados os intervalos de taxa de espaço.

    ![Intervalos de taxa de sala de exibição](./media/tutorial-csharp-create-first-app/azure-search-orders-rooms.png)

O **OrderBy** propriedade dos parâmetros de pesquisa não aceitará uma entrada, tais como **Rooms.BaseRate** para fornecer a taxa de espaço mais barato, mesmo que as salas já estiverem classificadas na taxa (que não são). Para exibir hotéis no conjunto de dados de exemplo, ordenado na taxa de espaço, teria de classificar os resultados no seu controlador home e enviar esses resultados para a vista na ordem pretendida.

## <a name="order-results-based-on-multiple-values"></a>Resultados de ordem, com base em múltiplos valores

Agora a pergunta é como diferenciar entre hotéis com a mesma classificação. Seria uma boa maneira de ordem com base na última vez que foi renovated o hotel. Em outras palavras, mais recentemente foi renovated o hotel, quanto maior for o hotel onde é apresentada nos resultados.

1. Para adicionar um segundo nível de ordenação, altere a **OrderBy** e **selecione** propriedades no **índice (modelo de SearchData)** método para incluir o  **LastRenovationDate** propriedade.

    ```cs
    OrderBy = new[] { "Rating desc", "LastRenovationDate desc" },
    Select = new[] { "HotelName", "Description", "Rating", "Rooms", "LastRenovationDate" },
    ```

    >[!Tip]
    >Qualquer número de propriedades pode ser introduzido na **OrderBy** lista. Se hotéis tivessem a mesma classificação e a data da renovação, uma terceira propriedade poderia ser inserida para diferenciá-los.

2. Novamente, é necessário ver a data de renovação na vista, apenas para ter a certeza de que a ordenação está correta. Para tal como uma renovação, provavelmente o ano é necessário. Altere o loop de processamento na exibição para o código a seguir.

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

3. Alteração da **seguinte** método no controlador de raiz, para reencaminhar o componente de ano da data da última renovação.

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

4. Alteração da **rolado** função numa vista para apresentar o texto de renovação.

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

5. Execute a aplicação. Procurar um termo comuns, como "conjunto" ou "view" e certifique-se de que os hotéis com a mesma classificação são agora apresentados por ordem descendente de data da renovação.

    ![Ordenação na data de renovação](./media/tutorial-csharp-create-first-app/azure-search-orders-renovation.png)

## <a name="filter-results-based-on-a-distance-from-a-geographical-point"></a>Filtrar os resultados com base numa distância de um ponto geográfico

Classificação e a data da renovação, são exemplos de propriedades que são apresentadas melhor numa ordem descendente. Uma lista alfabética seria um exemplo de uma boa utilização por ordem ascendente (por exemplo, se houve apenas uma **OrderBy** propriedade e como ele foi definido como **HotelName** , em seguida, uma ordem alfabética seria exibida ). No entanto, para os nossos dados de exemplo, a distância de um ponto geográfico poderá ser mais adequada.

Para apresentar resultados com base na distância geográfica, vários passos são necessários.

1. Filtre hotéis todos os que estão fora de um raio especificado de determinado ponto, introduzindo um filtro com longitude, latitude e os parâmetros de radius. Longitude é atribuído pela primeira vez para a função de ponto. RADIUS é em quilômetros.

    ```cs
        // "Location" must match the field name in the Hotel class.
        // Distance (the radius) is in kilometers.
        // Point order is Longitude then Latitude.
        Filter = $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') le {model.radius}",
    ```

2. O filtro acima faz _não_ ordenar os resultados com base na distância, ele apenas remove os valores atípicos. Para ordenar os resultados, introduza um **OrderBy** definição que especifica o método geoDistance.

    ```cs
    OrderBy = new[] { $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') asc" },
    ```

3. Embora os resultados foram retornados pelo Azure Search utilizando um filtro de distância, a distância calculada entre os dados e o ponto especificado está _não_ devolvido. Recalcule este valor na vista ou o controlador, se deseja apresentá-lo nos resultados.

    O código a seguir será calcular a distância entre dois pontos lat/lon.

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

4. Agora, tem de vincular esses conceitos em conjunto. No entanto, estes trechos de código são como nosso tutorial fica, criar uma aplicação baseada em mapa é deixada como um exercício para o leitor. Para tirar ainda mais neste exemplo, considere introduzir um nome de cidade com um raio, ou a localização de um ponto num mapa e selecionar um raio. Para investigar essas opções ainda mais, consulte os seguintes recursos:

* [Documentação do Azure Maps](https://docs.microsoft.com/azure/azure-maps/)
* [Localizar um endereço com o serviço de pesquisa do Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address)

## <a name="order-results-based-on-a-scoring-profile"></a>Resultados de ordem, com base num perfil de classificação

Os exemplos apresentados até aqui no tutorial mostram como solicitar em valores numéricos (classificação, data de renovação, à distância geográfica), fornecendo uma _exata_ processos de ordenação. No entanto, algumas pesquisas e alguns dados não abrem-se para uma comparação fácil entre elementos de dados de dois. O Azure Search inclui o conceito de _classificação_. _Perfis de classificação_ pode ser especificado para um conjunto de dados que podem ser utilizados para fornecer mais complexas e qualitativos comparações, que devem ser mais valioso quando, digamos, comparação de dados baseado em texto para decidir o que deve ser apresentado primeiro.

Perfis de classificação não estão definidos pelos usuários, mas normalmente por administradores de um conjunto de dados. Vários perfis de classificação foram definidos nos dados hotéis. Vamos examinar como é definido um perfil de classificação, em seguida, tentar escrever código para pesquisar nos mesmos.

### <a name="how-scoring-profiles-are-defined"></a>Como perfis de classificação são definidas

Vamos analisar três exemplos de perfis de classificação e considere como cada _deve_ afetam a ordem de resultados. Como desenvolvedor de aplicativos, não escrever estes perfis, eles são escritos pelo administrador de dados, no entanto, é útil examinar a sintaxe.

1. Esta é a predefinição de perfil de classificação para o conjunto de dados de hotéis utilizado quando não especificar nenhuma **OrderBy** ou **ScoringProfile** parâmetro. Aumenta a este perfil a _pontuação_ para um hotel, se o texto de pesquisa estiver presente no nome de hotel, descrição ou lista de etiquetas (características). Observe como os pesos da classificação de favorece determinados campos. Se o texto de pesquisa é apresentada no outro campo, não listados abaixo, que pode ter uma ponderação de 1. Obviamente, maior a pontuação, quanto mais cedo um resultado é apresentado na vista.

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

2. O seguinte perfil de classificação aumenta significativamente, a pontuação, se um parâmetro fornecido inclui um ou mais da lista de etiquetas (o que estamos a ligar "características"). O ponto principal deste perfil é que um parâmetro _tem_ de ser fornecido, que contém texto. Se o parâmetro está vazio ou não é fornecido, um erro será emitido.
 
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

3. Neste terceiro exemplo, a classificação proporciona um aumento significativo para a classificação. A última data renovated também irá aumentar a classificação, mas apenas se esses dados se encontrem dentro e 730 dias (dois anos) da data atual.

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

    Agora, vamos ver se estes perfis funcionam como podemos posicioná deveriam!

### <a name="add-code-to-the-view-to-compare-profiles"></a>Adicione o código para a vista para comparar os perfis

1. Abra o ficheiro Index. cshtml e substitua a &lt;corpo&gt; secção com o código a seguir.

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

2. Abra o ficheiro de SearchData.cs e substitua a **SearchData** classe com o código a seguir.

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

3. Abra o ficheiro de hotels.css e adicione as seguintes classes HTML.

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

### <a name="add-code-to-the-controller-to-specify-a-scoring-profile"></a>Adicione o código para o controlador para especificar um perfil de classificação

1. Abra o ficheiro de controlador home. Adicione as seguintes **usando** instrução (para ajudar com a criação de listas).

    ```cs
    using System.Linq;
    ```

2.  Neste exemplo, precisamos inicial da chamada para **índice** um pouco mais do que simplesmente retornar a exibição inicial. O método de pesquisa agora até 20 características apresentar na vista.

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

3. Precisamos de dois métodos privados ao guardar as facetas de um armazenamento temporário e para recuperá-los a partir do armazenamento temporário e preencher um modelo.

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

4. É preciso definir o **OrderBy** e **ScoringProfile** parâmetros conforme necessário. Substituir o existente **índice (modelo de SearchData)** método, com o seguinte.

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

    Leia os comentários para cada um da **mudar** seleções.

5. Não é necessário fazer nenhuma alteração para o **seguinte** ação, se concluiu o código adicional para a seção anterior sobre a classificação baseada em várias propriedades.

### <a name="run-and-test-the-app"></a>Executar e testar a aplicação

1. Execute a aplicação. Deverá ver um conjunto completo de características na vista.

2. Para ordenação, selecionando "por classificação numérica" irá dar-lhe a ordem numérica que já implementou neste tutorial, com data de renovação decidir entre hotéis de rating igual.

![Ordenação "praia", com base na classificação](./media/tutorial-csharp-create-first-app/azure-search-orders-beach.png)

3. Experimente agora o perfil "por características". Faça as seleções várias das características e certifique-se de que hotéis com essas características são promovidos em lista de resultados.

!["Praia", com base no perfil de ordenação](./media/tutorial-csharp-create-first-app/azure-search-orders-beach-profile.png)

4. Experimente o "Por Renovated data/perfil de classificação" para ver se consegue obter o que esperar. Apenas hotéis recentemente renovated devem obter um _atualização_ boost.

### <a name="resources"></a>Recursos

Para obter mais informações, consulte o seguinte procedimento [adicionar perfis de classificação para um índice da Azure Search](https://docs.microsoft.com/azure/search/index-add-scoring-profiles).

## <a name="takeaways"></a>Conclusões

Considere o seguintes das novidades deste projeto:

* Os usuários passarão a ser ordenada, mais relevantes primeiro os resultados da pesquisa.
* Dados estruturados às suas necessidades, para que a ordenação são fácil. Não foi possível a classificação "mais barato" primeiro facilmente, como os dados não estão estruturados para ativar a ordenação ser feito sem código adicional.
* Pode haver muitos níveis para ordenação, para diferenciar entre os resultados que têm o mesmo valor num nível mais elevado de ordenação.
* É natural para alguns resultados ser ordenados em ordem crescente ordem (por exemplo, a distância para fora de um ponto de) e alguns em ordem decrescente (Digamos, convidados da classificação).
* Perfis de classificação podem ser definido quando comparações numéricas não estão disponíveis ou não inteligente, para um conjunto de dados. Cada resultado de classificação irá ajudar a ordem e exibir os resultados de forma inteligente.

## <a name="next-steps"></a>Passos Seguintes

Concluiu esta série de C# tutoriais - deve obtido valioso conhecimento das APIs de pesquisa do Azure.

Para referência mais e tutoriais, considere a navegação [Microsoft Learn](https://docs.microsoft.com/learn/browse/?products=azure), ou os outros tutoriais a [documentação do Azure Search](https://docs.microsoft.com/azure/search/).
