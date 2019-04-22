---
title: 'Início rápido: Explorar as APIs REST no Postman – o Azure Search'
description: Como utilizar o Postman para emitir pedidos HTTP e a REST API chama-se para o Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: quickstart
ms.date: 04/08/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 0e14131ce45d20b99c1b5d5885cb1eb24c975d03
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59269117"
---
# <a name="quickstart-explore-azure-search-rest-apis-using-postman"></a>Início rápido: Explorar as APIs REST de pesquisa do Azure com o Postman
> [!div class="op_single_selector"]
> * [Postman](search-fiddler.md)
> * [C#](search-create-index-dotnet.md)
> * [Portal](search-get-started-portal.md)
> * [PowerShell](search-howto-dotnet-sdk.md)
>*

Uma das formas mais simples de explorar a [API REST da Azure Search](https://docs.microsoft.com/rest/api/searchservice) está a utilizar o Postman ou web outra ferramenta de teste para formular pedidos HTTP e inspecionar as respostas. Com as ferramentas certas e estas instruções, pode enviar pedidos e ver respostas antes de escrever código.

> [!div class="checklist"]
> * Transferir uma ferramenta de teste de api Web
> * Obter uma chave e o URL para o serviço de pesquisa
> * Ligar ao Azure Search
> * Criar um índice
> * Carregar um índice
> * Pesquisar um índice

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar e [inscreva-se no Azure Search](search-create-service-portal.md).

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes serviços e ferramentas são utilizadas neste início rápido. 

[Criar um serviço Azure Search](search-create-service-portal.md) ou [localizar um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na subscrição atual. Pode usar um serviço gratuito para este início rápido. 

[Aplicação de ambiente de trabalho do postman](https://www.getpostman.com/) ou [Telerik Fiddler](https://www.telerik.com/fiddler) é utilizado para enviar pedidos para o Azure Search.

## <a name="get-a-key-and-url"></a>Obter uma chave e o URL

As chamadas à API precisam do URL de serviço e de uma chave de acesso em todos os pedidos. É criado um serviço de pesquisa com ambos os elementos, pelo que, se tiver adicionado o Azure Search à sua subscrição, siga estes passos para obter as informações necessárias:

1. [Inicie sessão no portal do Azure](https://portal.azure.com/)e no seu serviço de pesquisa **descrição geral** página, obter o URL. Um ponto final de exemplo poderá ser parecido com `https://mydemo.search.windows.net`.

1. Na **configurações** > **chaves**, obter uma chave de administrador para todos os direitos no serviço. Existem duas chaves de administração intercambiáveis, fornecidas para a continuidade do negócio, caso seja necessário fazer o rollover um. Pode utilizar tanto a chave primária ou secundária em pedidos para adicionar, modificar e eliminar objetos.

![Obter uma chave de acesso e de ponto final HTTP](media/search-fiddler/get-url-key.png "obter uma chave de acesso e de ponto final HTTP")

Todos os pedidos requerem uma chave de api em cada pedido enviado ao seu serviço. Ter uma chave válida estabelece fidedignidade, numa base por pedido, entre a aplicação a enviar o pedido e o serviço que o processa.

## <a name="connect-to-azure-search"></a>Ligar ao Azure Search

Nesta secção, utilize a sua ferramenta de web à escolha para configurar ligações para o Azure Search. Cada ferramenta mantém as informações de cabeçalho de pedido para a sessão, o que significa que apenas tem de introduzir a chave de api e Content-Type única.

Para qualquer uma das ferramentas, precisa de escolher um comando (GET, POST, PUT e assim por diante), fornecer um ponto de final do URL e, para algumas tarefas, forneça JSON no corpo do pedido. Um URL completo é semelhante ao seguinte:

    https://<placeholder-for-your-service-name>.search.windows.net/indexes?api-version=2017-11-11

Tenha em atenção o prefixo HTTPS, o nome do serviço, o nome de um objeto (nesse caso, a coleção de índices) e o [versão de api](search-api-versions.md). A api-version é uma cadeia de caracteres em minúsculas, necessária especificada como "? api-version = 2017-11-11" para a versão atual. Versões de API são atualizadas regularmente. Incluir a versão de api em cada pedido dá-lhe controlo total sobre qual das versões é utilizada.  

Composição do cabeçalho do pedido inclui dois elementos, tipo de conteúdo, além da chave de api, utilizado para autenticar para o Azure Search:

    api-key: <placeholder-api-key-for-your-service>
    Content-Type: application/json

No Postman, formule um pedido parecido com a seguinte captura de ecrã. Escolher **Obtenha** como o verbo, forneça o URL e clique em **enviar**. Este comando liga ao Azure Search, lê a coleção de índices e retorna o código de estado HTTP 200 numa ligação com êxito. Se o seu serviço já foi de índices, a resposta também irão incluir definições de índice.

![Cabeçalho de pedido do Postman][6]

## <a name="1---create-an-index"></a>1 - Criar um índice

No Azure Search, normalmente, crie o índice antes de carregá-lo com dados. O [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) REST API é utilizada para esta tarefa. 

O URL é expandido para incluir o `hotel` nome do índice.

Para fazer isso no Postman:

1. Altere o verbo para **colocar**
2. Copie este URL `https://<placeholder-for-your-service-name>.search.windows.net/indexes/hotel?api-version=2017-11-11`
3. Fornecer a definição de índice (mostrada abaixo) no corpo do pedido
4. Clique em **enviar**

![Corpo de pedido do Postman][8]

### <a name="index-definition"></a>Definição do índice

A coleção de campos define a estrutura do documento. Cada documento tem de ter estes campos e cada campo tem de ter um tipo de dados. Os campos de cadeia são utilizados em pesquisas em texto completo, pelo que pode transmitir os dados numéricos como cadeias se quiser que esse conteúdo seja pesquisável.

Os atributos no campo determinam a ação permitida. As APIs REST permitem muitas ações, por predefinição. Por exemplo, todas as cadeias são pesquisáveis, recuperáveis, filtráveis e facetáveis por predefinição. Muitas vezes, só tem de definir atributos quando tem de desativar um comportamento.

          {
         "name": "hotels",  
         "fields": [
           {"name": "hotelId", "type": "Edm.String", "key":true, "searchable": false},
           {"name": "baseRate", "type": "Edm.Double"},
           {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
           {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
           {"name": "hotelName", "type": "Edm.String"},
           {"name": "category", "type": "Edm.String"},
           {"name": "tags", "type": "Collection(Edm.String)"},
           {"name": "parkingIncluded", "type": "Edm.Boolean"},
           {"name": "smokingAllowed", "type": "Edm.Boolean"},
           {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
           {"name": "rating", "type": "Edm.Int32"},
           {"name": "location", "type": "Edm.GeographyPoint"}
          ]
         }

Quando submete este pedido, deverá receber uma resposta HTTP 201, que indica que o índice foi criado com êxito. Pode verificar esta ação no portal, mas tenha em conta que a página do portal tem intervalos de atualização, de modo que poderá demorar um minuto ou dois a estar atualizada.

> [!TIP]
> Se obtiver HTTP 504, certifique-se de que o URL especifica HTTPS. Se vir HTTP 400 ou 404, verifique o corpo do pedido para verificar que não ocorreram erros ao copiar-colar. Um HTTP 403 normalmente indica um problema com a chave de API (uma chave inválida ou um problema de sintaxe com a forma como a chave de API é especificada).

## <a name="2---load-documents"></a>2 - carregar documentos

A criação e o preenchimento do índice são dois passos distintos. No Azure Search, o índice contém todos os dados pesquisáveis, que pode fornecer como documentos JSON. O [adicionar, atualizar ou eliminar documentos](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) REST API é utilizada para esta tarefa. 

O URL é expandido para incluir o `docs` coleções e `index` operação.

Para fazer isso no Postman:

1. Altere o verbo para **POST**
2. Copie este URL `https://<placeholder-for-your-service-name>.search.windows.net/indexes/hotels/docs/index?api-version=2017-11-11`
3. Forneça os documentos JSON (mostrados abaixo) no corpo do pedido
4. Clique em **enviar**

![Payload de pedido do Postman][10]

### <a name="json-documents-to-load-into-the-index"></a>Documentos JSON para carregar para o índice

O Corpo do Pedido contém quatro documentos que devem ser adicionados ao índice dos hotéis.

         {
         "value": [
         {
             "@search.action": "upload",
             "hotelId": "1",
             "baseRate": 199.0,
             "description": "Best hotel in town",
             "description_fr": "Meilleur hôtel en ville",
             "hotelName": "Fancy Stay",
             "category": "Luxury",
             "tags": ["pool", "view", "wifi", "concierge"],
             "parkingIncluded": false,
             "smokingAllowed": false,
             "lastRenovationDate": "2010-06-27T00:00:00Z",
             "rating": 5,
             "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
           },
           {
             "@search.action": "upload",
             "hotelId": "2",
             "baseRate": 79.99,
             "description": "Cheapest hotel in town",
             "description_fr": "Hôtel le moins cher en ville",
             "hotelName": "Roach Motel",
             "category": "Budget",
             "tags": ["motel", "budget"],
             "parkingIncluded": true,
             "smokingAllowed": true,
             "lastRenovationDate": "1982-04-28T00:00:00Z",
             "rating": 1,
             "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
           },
           {
             "@search.action": "upload",
             "hotelId": "3",
             "baseRate": 279.99,
             "description": "Surprisingly expensive",
             "hotelName": "Dew Drop Inn",
             "category": "Bed and Breakfast",
             "tags": ["charming", "quaint"],
             "parkingIncluded": true,
             "smokingAllowed": false,
             "lastRenovationDate": null,
             "rating": 4,
             "location": { "type": "Point", "coordinates": [-122.33207, 47.60621] }
           },
           {
             "@search.action": "upload",
             "hotelId": "4",
             "baseRate": 220.00,
             "description": "This could be the one",
             "hotelName": "A Hotel for Everyone",
             "category": "Basic hotel",
             "tags": ["pool", "wifi"],
             "parkingIncluded": true,
             "smokingAllowed": false,
             "lastRenovationDate": null,
             "rating": 4,
             "location": { "type": "Point", "coordinates": [-122.12151, 47.67399] }
           }
          ]
         }

Em alguns segundos, deve ver uma resposta HTTP 200 na lista de sessão. Isto indica que os documentos foram criados com êxito. 

Se obtiver um 207, pelo menos um documento falhou ao carregar. Se obtiver um 404, tem um erro de sintaxe no cabeçalho ou no corpo do pedido. Confirme que alterou o ponto final, de modo a incluir `/docs/index`.

> [!Tip]
> Em determinadas origens de dados, pode escolher a abordagem de *indexador* alternativa, que simplifica e reduz a quantidade de código necessário para a indexação. Para obter mais informações, veja [Indexer operations](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) (Operações do indexador).


## <a name="3---search-an-index"></a>3 - Pesquisar um índice

Agora que um índice e os documentos são carregados, pode emitir consultas contra eles usando [documentos sobre pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents) REST API.

O URL é expandido para incluir uma cadeia de caracteres de consulta, especificada com o operador de pesquisa.

Para fazer isso no Postman:

+ Altere o verbo para **obter**
+ Copie este URL `https://<placeholder-for-your-service-name>.search.windows.net/indexes/hotels/docs?search=motel&$count=true&api-version=2017-11-11`
+ Clique em **enviar**

Esta consulta pesquisa o termo "motel" e devolve uma contagem dos documentos nos resultados da pesquisa. O pedido e resposta devem ser semelhantes à seguinte captura de ecrã do Postman depois de clicar em **enviar**. O código de estado deve ser 200.

 ![Resposta de consulta do Postman][11]


## <a name="get-index-properties"></a>Obter as propriedades do índice
Também pode consultar informações do sistema para obter contagens de documentos e o consumo de armazenamento: `https://mydemo.search.windows.net/indexes/hotels/stats?api-version=2017-11-11`

No Postman, o pedido deve ter um aspeto semelhante ao seguinte e a resposta inclui uma contagem de documentos e o espaço utilizado em bytes.

 ![Consulta de sistema do Postman][12]

Repare que a sintaxe da versão de api é diferente. Para este pedido, utilize `?` para acrescentar a versão de api. O `?` separa o caminho do URL da cadeia de consulta, ao passo que & separa cada ' name = value' par na cadeia de consulta. Nesta consulta, a versão de api é o primeiro e único item na cadeia de consulta.

Para obter mais informações sobre esta API, veja [Get Index Statistics (REST)](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics) (Obter Estatísticas de Índice [REST]).


## <a name="use-fiddler"></a>Utilizar o Fiddler

Esta secção é equivalente a seções anteriores, apenas com o Fiddler capturas de ecrã e instruções

### <a name="connect-to-azure-search"></a>Ligar ao Azure Search

Formule um pedido parecido com a seguinte captura de ecrã. Escolher **obter** como o verbo. O Fiddler adiciona `User-Agent=Fiddler`. Pode colar os dois cabeçalhos de pedido adicionais em novas linhas abaixo do mesmo. Inclua o tipo de conteúdo e a chave de api do seu serviço mediante a utilização da chave de acesso administrativa do mesmo.

Para o destino, copie numa versão modificada deste URL: `https://<placeholder-for-your-service-name>.search.windows.net/indexes?api-version=2017-11-11`

![Cabeçalho de pedido do Fiddler][1]

> [!Tip]
> Desative o tráfego da web para ocultar atividades HTTP de estranhas, não relacionadas. Do Fiddler **arquivo** menu, desativar **capturar tráfego**. 

### <a name="1---create-an-index"></a>1 - Criar um índice

Altere o verbo para **colocar**. Cópia numa versão modificada deste URL: `https://<placeholder-for-your-service-name>.search.windows.net/indexes/hotel?api-version=2017-11-11`. Copie a definição de índice fornecida acima para o corpo do pedido. A página deve ser semelhante à seguinte captura de ecrã. Clique em **Execute** no canto superior direito, para enviar o pedido concluído.

![Corpo de pedido do Fiddler][7]

### <a name="2---load-documents"></a>2 - carregar documentos

Altere o verbo para **POST**. Altere o URL para incluir `/docs/index`. Copie os documentos para o corpo do pedido, semelhante à seguinte captura de ecrã e, em seguida, execute o pedido.

![Payload de pedido do Fiddler][9]

### <a name="tips-for-running-our-sample-queries-in-fiddler"></a>Sugestões para executar as nossas consultas de exemplo no Fiddler

A consulta de exemplo seguinte é retirada do artigo [Search Index operation (Azure Search API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (Operação de Pesquisa no Índice [API do Azure Search]). Muitas das consultas de exemplo neste artigo incluem espaços, algo não permitido no Fiddler. Substitua cada espaço por um caráter de + antes de colar a cadeia de consulta, antes de tentar a consulta no Fiddler.

**Antes de os espaços serem substituídos (em lastRenovationDate desc):**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2017-11-11

**Depois de os espaços serem substituídos por + (em lastRenovationDate+desc):**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2017-11-11

### <a name="tips-for-viewing-index-statistic-in-fiddler"></a>Sugestões para ver estatísticas de índice no Fiddler

No Fiddler, clique no separador **Inspectors** (Inspetores), clique no separador **Headers** (Cabeçalhos) e selecione o formato JSON. Deve ver a contagem dos documentos e o tamanho de armazenamento (em KB).

## <a name="next-steps"></a>Passos Seguintes

Os clientes REST são indispensáveis para explorações improvisadas, mas agora que já sabe como é que as APIs REST funcionam, pode avançar com código. Para os passos seguintes, veja as ligações abaixo:

+ [Quickstart: Criar um índice com o .NET SDK](search-create-index-dotnet.md)
+ [Quickstart: Criar um índice (REST) com o PowerShell](search-create-index-rest-api.md)

<!--Image References-->
[1]: ./media/search-fiddler/fiddler-url.png
[2]: ./media/search-fiddler/AzureSearch_Fiddler2_PostDocs.png
[3]: ./media/search-fiddler/AzureSearch_Fiddler3_Query.png
[4]: ./media/search-fiddler/AzureSearch_Fiddler4_QueryResults.png
[5]: ./media/search-fiddler/AzureSearch_Fiddler5_QueryStats.png
[6]: ./media/search-fiddler/postman-url.png
[7]: ./media/search-fiddler/fiddler-request.png
[8]: ./media/search-fiddler/postman-request.png
[9]: ./media/search-fiddler/fiddler-docs.png
[10]: ./media/search-fiddler/postman-docs.png
[11]: ./media/search-fiddler/postman-query.png
[12]: ./media/search-fiddler/postman-system-query.png