---
title: 'Quickstart: Criar um índice de pesquisa em JavaScript'
titleSuffix: Azure Cognitive Search
description: Neste arranque rápido javaScript, aprenda a criar um índice, carregar dados e executar consultas na Pesquisa Cognitiva Azure usando JavaScript
author: dereklegenzoff
manager: luisca
ms.author: delegenz
ms.devlang: javascript
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 10/26/2020
ms.custom: devx-track-js
ms.openlocfilehash: 5ccbe1035c5cc73993e069c7683d6b15ae18e21c
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92795951"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-using-the-javascript-sdk"></a>Quickstart: Criar um índice de pesquisa cognitiva Azure utilizando o JavaScript SDK
> [!div class="op_single_selector"]
> * [JavaScript](search-get-started-javascript.md)
> * [C#](search-get-started-dotnet.md)
> * [Portal](search-get-started-portal.md)
> * [PowerShell](./search-get-started-powershell.md)
> * [Python](search-get-started-python.md)
> * [Postman](search-get-started-postman.md)


Utilize o [SDK Javascript/Typscript para Azure Cognitive Search](https://docs.microsoft.com/javascript/api/overview/azure/search-documents-readme?view=azure-node-latest) para criar uma aplicação Node.js no JavaScript que cria, carrega e consulta um índice de pesquisa.

Este artigo demonstra como criar a aplicação passo a passo. Em alternativa, pode [descarregar o código de origem e os dados](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/v11) e executar a aplicação a partir da linha de comando.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, tenha as seguintes ferramentas e serviços:

+ Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/)

+ Um serviço de Pesquisa Cognitiva Azure. [Crie um serviço](search-create-service-portal.md) ou [encontre um serviço existente.](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) Pode utilizar um serviço gratuito para este arranque rápido. 

+ [Node.js](https://nodejs.org) e [PNM](https://www.npmjs.com)

+ [Código visual do estúdio](https://code.visualstudio.com) ou outro IDE


## <a name="set-up-your-project"></a>Configurar o seu projeto

Comece por obter o ponto final e a chave para o seu serviço de pesquisa. Em seguida, crie um novo projeto com NPM como descrito abaixo.

<a name="get-service-info"></a>

### <a name="copy-a-key-and-endpoint"></a>Copiar uma chave e ponto final

As chamadas para o serviço requerem um ponto final URL e uma chave de acesso em cada pedido. Como primeiro passo, encontre a chave API e URL para adicionar ao seu projeto. Irá especificar ambos os valores ao criar o cliente num passo posterior.

1. [Inscreva-se no portal Azure,](https://portal.azure.com/)e na página **geral do** seu serviço de pesquisa, obtenha o URL. Um ponto final de exemplo poderá ser parecido com `https://mydemo.search.windows.net`.

2. Em **Settings**  >  **Definições Teclas** , obtenha uma chave de administração para todos os direitos sobre o serviço, necessários para criar ou eliminar objetos. Há duas chaves primárias e secundárias intercambiáveis. Pode usar qualquer um.

   ![Obtenha uma chave de acesso http e acesso](media/search-get-started-postman/get-url-key.png "Obtenha uma chave de acesso http e acesso")

Todos os pedidos requerem uma chave API em cada pedido enviado ao seu serviço. Ter uma chave válida estabelece fidedignidade, numa base por pedido, entre a aplicação a enviar o pedido e o serviço que o processa.

### <a name="create-a-new-npm-project"></a>Criar um novo projeto NPM

Comece por abrir o Código VS e o seu [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal) ou outro terminal, como o Node.js o comando.

1. Crie um diretório de desenvolvimento, dando-lhe o `quickstart` nome:

    ```cmd
    mkdir quickstart
    cd quickstart
    ```

2. Inicialize um projeto vazio com NPM executando 

    ```cmd
    npm init
    ```
     Aceite os valores predefinidos, com exceção da Licença, que deve definir para "MIT". 

3. Instale `@azure/search-documents` , o [SDK Javascript/Typscript para Azure Cognitive Search](https://docs.microsoft.com/javascript/api/overview/azure/search-documents-readme?view=azure-node-latest).

    ```cmd
    npm install @azure/search-documents
    ```

4. `dotenv`Instale, que é usado para importar variáveis ambientais como o nosso nome de serviço e chave api.
    ```cmd
    npm install dotenv
    ```

5. Confirme que configura os projetos e as suas dependências verificando se o seu  **package.jsem** ficheiro se parece com o seguinte json:

    ```json
    {
      "name": "quickstart",
      "version": "1.0.0",
      "description": "Azure Cognitive Search Quickstart",
      "main": "index.js",
      "scripts": {
        "test": "echo \"Error: no test specified\" && exit 1"
      },
      "keywords": [
        "Azure",
        "Search"
      ],
      "author": "Your Name",
      "license": "MIT",
      "dependencies": {
        "@azure/search-documents": "^11.0.3",
        "dotenv": "^8.2.0"
      }
    }
    ```

6. Crie um ficheiro **.env** para manter os parâmetros do seu serviço de pesquisa:

    ```
    SEARCH_API_KEY=<search-admin-key>
    SEARCH_API_ENDPOINT=https://<search-service-name>.search.windows.net
    ```

Substitua o `<search-service-name>` valor pelo nome do seu serviço de pesquisa. `<search-admin-key>`Substitua-o pelo valor chave que gravou anteriormente. 

### <a name="create-indexjs-file"></a>Criar index.js ficheiro

Em seguida, criamos um ficheiro **index.js,** que é o ficheiro principal que irá hospedar o nosso código.

No topo deste ficheiro, importamos a `@azure/search-documents` biblioteca:

```javascript
const { SearchIndexClient, SearchClient, AzureKeyCredential, odata } = require("@azure/search-documents");
```

Em seguida, precisamos exigir que o `dotenv` pacote leia nos parâmetros do ficheiro **.env** da seguinte forma:

```javascript
// Load the .env file if it exists
require("dotenv").config();

// Getting endpoint and apiKey from .env file
const endpoint = process.env.SEARCH_API_ENDPOINT || "";
const apiKey = process.env.SEARCH_API_KEY || "";
```

Com as nossas importações e variáveis ambientais no lugar, estamos prontos para definir a função principal.

A maior parte da funcionalidade no SDK é assíncronea, por isso fazemos a nossa principal `async` função. Também incluímos uma `main().catch()` função abaixo da função principal para capturar e registar quaisquer erros encontrados:

```javascript
async function main() {
    console.log(`Running Azure Cognitive Search Javascript quickstart...`);
    if (!endpoint || !apiKey) {
        console.log("Make sure to set valid values for endpoint and apiKey with proper authorization.");
        return;
    }

    // remaining quickstart code will go here
}

main().catch((err) => {
    console.error("The sample encountered an error:", err);
});
```

Com isso no lugar, estamos prontos para criar um índice.

## <a name="1---create-index"></a>1 - Criar índice 

Crie um ficheiro **hotels_quickstart_index.jsligado .**  Este ficheiro define como a Azure Cognitive Search funciona com os documentos que vai carregar no próximo passo. Cada campo será identificado por um `name` e terá um especificado `type` . Cada campo também tem uma série de atributos de índice que especificam se a Azure Cognitive Search pode pesquisar, filtrar, classificar e facet no campo. A maioria dos campos são tipos de dados simples, mas alguns, como `AddressType` são tipos complexos que permitem criar estruturas de dados ricas no seu índice.  Pode ler mais sobre [tipos de dados suportados](/rest/api/searchservice/supported-data-types) e [atributos de índice.](./search-what-is-an-index.md#index-attributes) 

Adicione o seguinte para **hotels_quickstart_index.js** ou [descarregue o ficheiro](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/v11/hotels_quickstart_index.json). 

```json
{
    "name": "hotels-quickstart",
    "fields": [
        {
            "name": "HotelId",
            "type": "Edm.String",
            "key": true,
            "filterable": true
        },
        {
            "name": "HotelName",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": true,
            "facetable": false
        },
        {
            "name": "Description",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": false,
            "facetable": false,
            "analyzer": "en.lucene"
        },
        {
            "name": "Description_fr",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": false,
            "facetable": false,
            "analyzer": "fr.lucene"
        },
        {
            "name": "Category",
            "type": "Edm.String",
            "searchable": true,
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Tags",
            "type": "Collection(Edm.String)",
            "searchable": true,
            "filterable": true,
            "sortable": false,
            "facetable": true
        },
        {
            "name": "ParkingIncluded",
            "type": "Edm.Boolean",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "LastRenovationDate",
            "type": "Edm.DateTimeOffset",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Rating",
            "type": "Edm.Double",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Address",
            "type": "Edm.ComplexType",
            "fields": [
                {
                    "name": "StreetAddress",
                    "type": "Edm.String",
                    "filterable": false,
                    "sortable": false,
                    "facetable": false,
                    "searchable": true
                },
                {
                    "name": "City",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "StateProvince",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "PostalCode",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "Country",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                }
            ]
        }
    ],
    "suggesters": [
        {
            "name": "sg",
            "searchMode": "analyzingInfixMatching",
            "sourceFields": [
                "HotelName"
            ]
        }
    ]
}
```

Com a nossa definição de índice em vigor, queremos importar  **hotels_quickstart_index.jsno** topo da **index.js** para que a função principal possa aceder à definição de índice.

```javascript
const indexDefinition = require('./hotels_quickstart_index.json');
```

Dentro da função principal, criamos então um `SearchIndexClient` , que é usado para criar e gerir índices para Azure Cognitive Search. 

```javascript
const indexClient = new SearchIndexClient(endpoint, new AzureKeyCredential(apiKey));
```

Em seguida, queremos apagar o índice se já existe. Esta é uma prática comum para o código de teste/demonstração.

Fazemos isso definindo uma função simples que tenta eliminar o índice.

```javascript
async function deleteIndexIfExists(indexClient, indexName) {
    try {
        await indexClient.deleteIndex(indexName);
        console.log('Deleting index...');
    } catch {
        console.log('Index does not exist yet.');
    }
}
```

Para executar a função, extraímos o nome do índice da definição de índice e passamos o `indexName` junto com o para a `indexClient` `deleteIndexIfExists()` função.

```javascript
const indexName = indexDefinition["name"];

console.log('Checking if index exists...');
await deleteIndexIfExists(indexClient, indexName);
```

Depois disso, estamos prontos para criar o índice com o `createIndex()` método.

```javascript
console.log('Creating index...');
let index = await indexClient.createIndex(indexDefinition);

console.log(`Index named ${index.name} has been created.`);
```

### <a name="run-the-sample"></a>Executar o exemplo

Neste momento, está pronto para executar a amostra. Utilize uma janela de terminal para executar o seguinte comando:

```cmd
node index.js
```

Se [descarregou o código fonte](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/v11) e ainda não instalou os pacotes necessários, corra `npm install` primeiro.

Deve ver uma série de mensagens descrevendo as ações que estão a ser tomadas pelo programa. 

Abra a **visão geral** do seu serviço de pesquisa no portal Azure. Selecione o separador **Índices.** Devia ver algo como o seguinte:

:::image type="content" source="media/search-get-started-javascript/create-index-no-data.png" alt-text="Screenshot do portal Azure, Visão geral do serviço de pesquisa, separador Índices" border="false":::

No próximo passo, irá adicionar dados ao indexar. 

## <a name="2---load-documents"></a>2 - Documentos de carga 


Na Pesquisa Cognitiva Azure, os documentos são estruturas de dados que são entradas para indexação e saídas de consultas. Pode empurrar esses dados para o índice ou utilizar um [indexante](search-indexer-overview.md). Neste caso, vamos apresentar os documentos para o índice.

As entradas de documentos podem ser linhas numa base de dados, bolhas no armazenamento blob, ou, como nesta amostra, documentos JSON no disco. Pode baixar [hotels.js](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/v11/hotels.json) ou criar o seu próprio **hotels.jsem** ficheiro com o seguinte conteúdo:

```json
{
    "value": [
        {
            "HotelId": "1",
            "HotelName": "Secret Point Motel",
            "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
            "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
            "Category": "Boutique",
            "Tags": ["pool", "air conditioning", "concierge"],
            "ParkingIncluded": false,
            "LastRenovationDate": "1970-01-18T00:00:00Z",
            "Rating": 3.6,
            "Address": {
                "StreetAddress": "677 5th Ave",
                "City": "New York",
                "StateProvince": "NY",
                "PostalCode": "10022"
            }
        },
        {
            "HotelId": "2",
            "HotelName": "Twin Dome Motel",
            "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
            "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
            "Category": "Boutique",
            "Tags": ["pool", "free wifi", "concierge"],
            "ParkingIncluded": "false",
            "LastRenovationDate": "1979-02-18T00:00:00Z",
            "Rating": 3.6,
            "Address": {
                "StreetAddress": "140 University Town Center Dr",
                "City": "Sarasota",
                "StateProvince": "FL",
                "PostalCode": "34243"
            }
        },
        {
            "HotelId": "3",
            "HotelName": "Triple Landscape Hotel",
            "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
            "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
            "Category": "Resort and Spa",
            "Tags": ["air conditioning", "bar", "continental breakfast"],
            "ParkingIncluded": "true",
            "LastRenovationDate": "2015-09-20T00:00:00Z",
            "Rating": 4.8,
            "Address": {
                "StreetAddress": "3393 Peachtree Rd",
                "City": "Atlanta",
                "StateProvince": "GA",
                "PostalCode": "30326"
            }
        },
        {
            "HotelId": "4",
            "HotelName": "Sublime Cliff Hotel",
            "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
            "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
            "Category": "Boutique",
            "Tags": ["concierge", "view", "24-hour front desk service"],
            "ParkingIncluded": true,
            "LastRenovationDate": "1960-02-06T00:00:00Z",
            "Rating": 4.6,
            "Address": {
                "StreetAddress": "7400 San Pedro Ave",
                "City": "San Antonio",
                "StateProvince": "TX",
                "PostalCode": "78216"
            }
        }
    ]
}
```

À semelhança do que fizemos com o índiceDefinição, precisamos também de importar no topo daindex.jspara `hotels.json` que os dados possam ser acedidos na nossa função principal. **index.js**

```javascript
const hotelData = require('./hotels.json');
```


Para indexar os dados no índice de pesquisa, precisamos agora de criar um `SearchClient` . Enquanto o `SearchIndexClient` é usado para criar e gerir um índice, o é usado para carregar `SearchClient` documentos e consultar o índice.

Há duas maneiras de criar `SearchClient` um. A primeira opção é criar um `SearchClient` de raiz:

```javascript
 const searchClient = new SearchClient(endpoint, indexName, new AzureKeyCredential(apiKey));
```

Em alternativa, pode utilizar `getSearchClient()` o método do para criar o `SearchIndexClient` `SearchClient` :

```javascript
const searchClient = indexClient.getSearchClient(indexName);
```

Agora que o cliente está definido, carre deixe os documentos no índice de pesquisa. Neste caso, utilizamos o `mergeOrUploadDocuments()` método, que irá carregar os documentos ou fundi-los com um documento existente se já existir um documento com a mesma chave.

```javascript
console.log('Uploading documents...');
let indexDocumentsResult = await searchClient.mergeOrUploadDocuments(hotelData['value']);

console.log(`Index operations succeeded: ${JSON.stringify(indexDocumentsResult.results[0].succeeded)}`);
```

Executar o programa novamente com `node index.js` . Deve ver um conjunto ligeiramente diferente de mensagens daquelas que viu no Passo 1. Desta vez, o índice *existe,* e deve ver uma mensagem sobre a sua eliminação antes que a app crie o novo índice e publique dados para o mesmo. 

Antes de fazermos as consultas no passo seguinte, defina uma função para que o programa espere um segundo. Isto é feito apenas para fins de teste/demonstração para garantir os acabamentos de indexação e que os documentos estão disponíveis no índice para as nossas consultas.

```javascript
function sleep(ms) {
    var d = new Date();
    var d2 = null;
    do {
        d2 = new Date();
    } while (d2 - d < ms);
}
```

Para que o programa espere um segundo, ligue para a `sleep` função como abaixo:

```javascript
sleep(1000);
```

## <a name="3---search-an-index"></a>3 - Pesquisar um índice

Com um índice criado e documentos carregados, está pronto para enviar consultas para o índice. Nesta secção, enviaremos cinco consultas diferentes para o índice de pesquisa para demonstrar diferentes peças de funcionalidade de consulta disponíveis para si.

As consultas são escritas numa `sendQueries()` função que chamaremos na função principal da seguinte forma:

```javascript
await sendQueries(searchClient);
```

As consultas são enviadas utilizando o `search()` método de `searchClient` . O primeiro parâmetro é o texto de pesquisa e o segundo parâmetro são quaisquer opções de pesquisa adicionais.

A primeira consulta procura `*` , o que equivale a pesquisar tudo e seleciona três dos campos do índice. É uma boa prática apenas para `select` os campos de que precisa, porque retirar dados desnecessários pode adicionar latência às suas consultas.

A `searchOptions` consulta para esta consulta também se `includeTotalCount` definiu para , que irá devolver o `true` número de resultados correspondentes encontrados.

```javascript
async function sendQueries(searchClient) {
    console.log('Query #1 - search everything:');
    let searchOptions = {
        includeTotalCount: true,
        select: ["HotelId", "HotelName", "Rating"]
    };

    let searchResults = await searchClient.search("*", searchOptions);
    for await (const result of searchResults.results) {
        console.log(`${JSON.stringify(result.document)}`);
    }
    console.log(`Result count: ${searchResults.count}`);

    // remaining queries go here
}
```

As restantes consultas descritas abaixo também devem ser adicionadas à `sendQueries()` função. Estão separados aqui para a legibilidade.

Na próxima consulta, especificamos o termo de pesquisa `"wifi"` e também incluímos um filtro para apenas devolver resultados em que o estado é igual a `'FL'` . Os resultados também são encomendados pelo `Rating` Hotel.

```javascript
console.log('Query #2 - Search with filter, orderBy, and select:');
let state = 'FL';
searchOptions = {
    filter: odata`Address/StateProvince eq ${state}`,
    orderBy: ["Rating desc"],
    select: ["HotelId", "HotelName", "Rating"]
};

searchResults = await searchClient.search("wifi", searchOptions);
for await (const result of searchResults.results) {
    console.log(`${JSON.stringify(result.document)}`);
}
```

Em seguida, a pesquisa é limitada a um único campo pesmável usando o `searchFields` parâmetro. Esta é uma ótima opção para tornar a sua consulta mais eficiente se você sabe que você só está interessado em jogos em determinados campos. 

```javascript
console.log('Query #3 - Limit searchFields:');
searchOptions = {
    select: ["HotelId", "HotelName", "Rating"],
    searchFields: ["HotelName"]
};

searchResults = await searchClient.search("sublime cliff", searchOptions);
for await (const result of searchResults.results) {
    console.log(`${JSON.stringify(result.document)}`);
}
console.log();
```

Outra opção comum a incluir numa consulta `facets` é. As facetas permitem-lhe construir filtros na sua UI para facilitar aos utilizadores saberem a que valores podem filtrar.

```javascript
console.log('Query #4 - Use facets:');
searchOptions = {
    facets: ["Category"],
    select: ["HotelId", "HotelName", "Rating"],
    searchFields: ["HotelName"]
};

searchResults = await searchClient.search("*", searchOptions);
for await (const result of searchResults.results) {
    console.log(`${JSON.stringify(result.document)}`);
}
```

A consulta final utiliza o `getDocument()` método do `searchClient` . Isto permite-lhe recuperar eficientemente um documento pela sua chave. 

```javascript
console.log('Query #5 - Lookup document:');
let documentResult = await searchClient.getDocument(key='3')
console.log(`HotelId: ${documentResult.HotelId}; HotelName: ${documentResult.HotelName}`)
```

### <a name="run-the-sample"></a>Executar o exemplo

Executar o programa com `node index.js` . Agora, para além dos passos anteriores, as consultas serão enviadas e os resultados escritos para a consola.

## <a name="clean-up-resources"></a>Limpar os recursos

Ao trabalhar na sua própria subscrição, recomendamos que verifique, depois de concluir um projeto, se irá precisar dos recursos que criou. Os recursos que deixar em execução podem custar-lhe dinheiro. Pode eliminar recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Pode encontrar e gerir recursos no portal, utilizando a ligação **de todos os recursos** ou **grupos** de recursos no painel de navegação à esquerda.

Se estiver a utilizar um serviço gratuito, lembre-se que está limitado a três índices, indexadores e fontes de dados. Pode eliminar itens individuais no portal para ficar abaixo do limite. 

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido do JavaScript, trabalhou através de uma série de tarefas para criar um índice, carregá-lo com documentos e executar consultas. 

Se já tiver algum fundo em Azure Cognitive Search, pode usar esta amostra como trampolim para experimentar sugestores (consultas de tipo à frente ou autocompletas), filtros e navegação frontal. Se é novo na Azure Cognitive Search, recomendamos experimentar outros tutoriais para desenvolver uma compreensão do que pode criar. Visite a nossa [página de documentação](https://azure.microsoft.com/documentation/services/search/) para obter mais recursos. 

> [!div class="nextstepaction"]
> [Construa uma linha frontal de reação para a pesquisa cognitiva do Azure](https://github.com/dereklegenzoff/azure-search-react-template)