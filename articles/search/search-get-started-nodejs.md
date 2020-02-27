---
title: 'Quickstart: Criar um índice de pesquisa em Node.js usando APIs REST'
titleSuffix: Azure Cognitive Search
description: Neste Início de Nó.js quickstart, aprenda a criar um índice, dados de carga e executar consultas na Pesquisa Cognitiva Azure usando JavaScript e as APIs REST.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.devlang: nodejs
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 02/25/2020
ms.openlocfilehash: cbef6029b93f134f95ee54aa87ce0dd65bcdf50d
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624001"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-nodejs-using-rest-apis"></a>Quickstart: Criar um índice de pesquisa cognitiva azure em Node.js usando APIs REST
> [!div class="op_single_selector"]
> * [JavaScript](search-get-started-nodejs.md)
> * [C#](search-get-started-dotnet.md)
> * [Portal](search-get-started-portal.md)
> * [PowerShell](search-create-index-rest-api.md)
> * [python](search-get-started-python.md)
> * [Postman](search-get-started-postman.md)

Crie uma aplicação Node.js que cria, carrega e consulta um índice de Pesquisa Cognitiva Azure. Este artigo demonstra como criar a aplicação passo a passo. Em alternativa, pode [descarregar o código e os dados de origem](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/) e executar a aplicação a partir da linha de comando.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Usamos o seguinte software e serviços para construir e testar este quickstart:

+ [Node.js](https://nodejs.org)

+ [NPM](https://www.npmjs.com) deve ser instalado por Node.js

+ Neste artigo, ou no diretório do repo, é fornecida uma estrutura de índice de amostra e documentos correspondentes ou a partir do [diretório **quickstart** do repo](https://github.com/Azure-Samples/azure-search-javascript-samples/)

+ [Crie um serviço de Pesquisa Cognitiva Azure](search-create-service-portal.md) ou [encontre um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) sob a sua subscrição atual. Pode utilizar um serviço gratuito para este arranque rápido.

Recomendação:

* [Visual Studio Code](https://code.visualstudio.com)

* [Extensões Mais bonitas](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode) e [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint) para VSCode.

<a name="get-service-info"></a>

## <a name="get-keys-and-urls"></a>Obter chaves e URLs

As chamadas para o serviço requerem um ponto final url e uma chave de acesso em cada pedido. Um serviço de pesquisa é criado com ambos, por isso, se você adicionar Pesquisa Cognitiva Azure à sua subscrição, siga estes passos para obter as informações necessárias:

1. [Faça sessão no portal Azure](https://portal.azure.com/), e na página **'Visão Geral** do serviço de pesquisa', obtenha o nome do seu serviço de pesquisa. Pode confirmar o seu nome de serviço revendo o URL do ponto final. Se o seu URL final fosse `https://mydemo.search.windows.net`, o seu nome de serviço seria `mydemo`.

2. Em **Definições** > **Teclas,** obtenha uma chave de administração para todos os direitos sobre o serviço. Existem duas chaves de administração intercambiáveis, previstas para a continuidade do negócio no caso de precisar de rolar uma. Pode utilizar a chave primária ou secundária nos pedidos de adição, modificação e aparas de objetos.

    Pegue a chave de consulta também. É uma boa prática emitir pedidos de consulta com acesso só para leitura.

![Obtenha o nome de serviço e as chaves de administração e consulta](media/search-get-started-nodejs/service-name-and-keys.png)

Todos os pedidos requerem uma chave api no cabeçalho de cada pedido enviado ao seu serviço. Uma chave válida estabelece confiança, por pedido, entre o pedido que envia o pedido e o serviço que o trata.

## <a name="set-up-your-environment"></a>Configurar o ambiente

Comece por abrir uma consola Powershell ou outro ambiente no qual instalou nonóde.js.

1. Crie um diretório de desenvolvimento, dando-lhe o nome `quickstart`:

    ```powershell
    mkdir quickstart
    cd quickstart
    ```

2. Inicialize um projeto vazio com APm, executando `npm init`. Aceite os valores predefinidos, exceto a Licença, que deve definir para "MIT". 

1. Adicione pacotes que serão dependentes do código e da ajuda no desenvolvimento:

    ```powershell
    npm install nconf node-fetch
    npm install --save-dev eslint eslint-config-prettier eslint-config-airbnb-base eslint-plugin-import prettier
    ```

4. Confirme que configurou os projetos e as suas dependências verificando se o seu ficheiro **package.json** se parece com o seguinte:

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
        "Azure_Search"
      ],
      "author": "Your Name",
      "license": "MIT",
      "dependencies": {
        "nconf": "^0.10.0",
        "node-fetch": "^2.6.0"
      },
      "devDependencies": {
        "eslint": "^6.1.0",
        "eslint-config-airbnb-base": "^13.2.0",
        "eslint-config-prettier": "^6.0.0",
        "eslint-plugin-import": "^2.18.2",
        "prettier": "^1.18.2"
      }
    }
    ```

5. Crie um ficheiro **azure_search_config.json** para guardar os dados do serviço de pesquisa:

    ```json
    {
        "serviceName" : "[SEARCH_SERVICE_NAME]",
        "adminKey" : "[ADMIN_KEY]",
        "queryKey" : "[QUERY_KEY]",
        "indexName" : "hotels-quickstart"
    }
    ```

Substitua o valor `[SERVICE_NAME]` pelo nome do seu serviço de pesquisa. Substitua `[ADMIN_KEY]` e `[QUERY_KEY]` os valores-chave que gravou anteriormente. 

## <a name="1---create-index"></a>1 - Criar índice 

Crie um ficheiro **hotels_quickstart_index.json.**  Este ficheiro define como a Pesquisa Cognitiva Azure funciona com os documentos que vai carregar no próximo passo. Cada campo será identificado por um `name` e terá um `type`especificado . Cada campo também tem uma série de atributos de índice que especificam se a Pesquisa Cognitiva Azure pode pesquisar, filtrar, classificar e enfrentar o campo. A maioria dos campos são tipos simples de dados, mas alguns, como `AddressType` são tipos complexos que permitem criar estruturas de dados ricas no seu índice.  Pode ler mais sobre tipos de [dados suportados](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) e [atributos de índice.](https://docs.microsoft.com/azure/search/search-what-is-an-index#index-attributes) 

Adicione o seguinte a **hotels_quickstart_index.json** ou [descarregue o ficheiro](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/hotels_quickstart_index.json). 

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
    

É uma boa prática separar as especificidades de um determinado cenário do código que será amplamente aplicável. A classe `AzureSearchClient` definida no ficheiro **AzureSearchClient.js** saberá como construir URLs de pedido, fazer um pedido utilizando a Fetch API, e reagir ao código de estado da resposta.

Comece a trabalhar em **AzureSearchClient.js** importando o pacote **de busca de nó** e criando uma classe simples. Isolar as partes mutáveis da classe `AzureSearchClient`, passando para o seu construtor os vários valores de configuração:

```javascript
const fetch = require('node-fetch');

class AzureSearchClient {
    constructor(searchServiceName, adminKey, queryKey, indexName) {
        this.searchServiceName = searchServiceName;
        this.adminKey = adminKey;
        // The query key is used for read-only requests and so can be distributed with less risk of abuse.
        this.queryKey = queryKey;
        this.indexName = indexName;
        this.apiVersion = '2019-05-06';
    }

    // All methods go inside class body here!
}

module.exports = AzureSearchClient;
```

A primeira responsabilidade da classe é saber como construir URLs para os quais enviar os vários pedidos. Construa estes URLs com métodos de instância que utilizam os dados de configuração passados para o construtor de classes. Note que o URL que constroem é específico de uma versão API e deve ter um argumento especificando essa versão (nesta aplicação, `2019-05-06`). 

O primeiro destes métodos devolverá o URL para o próprio índice. Adicione o seguinte método dentro do corpo de classe:

```javascript
getIndexUrl() { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}?api-version=${this.apiVersion}`; }

```

A próxima responsabilidade da `AzureSearchClient` é fazer um pedido assíncrono com a Fetch API. O método estática assíncrono `request` toma um URL, uma cadeia que especifica o método HTTP ("GET", "PUT", "POST", "DELETE"), a chave a utilizar no pedido e um objeto JSON opcional. A variável `headers` mapeia o `queryKey` (seja a chave de administração ou a chave de consulta apenas de leitura) para o cabeçalho de pedido HTTP "api-key". As opções de pedido contêm sempre o `method` a utilizar e a `headers`. Se `bodyJson` não for `null`, o corpo do pedido HTTP é definido para a representação de cordas de `bodyJson`. O método `request` devolve a Promessa da Fetch API de executar o pedido HTTP.

```javascript
static async request(url, method, apiKey, bodyJson = null) {
    // Uncomment the following for request details:
    /*
    console.log(`\n${method} ${url}`);
    console.log(`\nKey ${apiKey}`);
    if (bodyJson !== null) {
        console.log(`\ncontent: ${JSON.stringify(bodyJson, null, 4)}`);
    }
    */

    const headers = {
        'content-type' : 'application/json',
        'api-key' : apiKey
    };
    const init = bodyJson === null ?
        { 
            method, 
            headers
        }
        : 
        {
            method, 
            headers,
            body : JSON.stringify(bodyJson)
        };
    return fetch(url, init);
}
```

Para fins de demonstração, basta lançar uma exceção se o pedido http não for um sucesso. Numa aplicação real, provavelmente faria alguma sessão de registo e diagnóstico do código de estado HTTP no `response` a partir do pedido de serviço de pesquisa. 
    
```javascript
static throwOnHttpError(response) {
    const statusCode = response.status;
    if (statusCode >= 300){
        console.log(`Request failed: ${JSON.stringify(response, null, 4)}`);
        throw new Error(`Failure in request. HTTP Status was ${statusCode}`);
    }
}
```

Por fim, adicione os métodos para detetar, eliminar e criar o índice de Pesquisa Cognitiva Azure. Todos estes métodos têm a mesma estrutura:

* Obtenha o ponto final para o qual o pedido será feito.
* Gere o pedido com o ponto final apropriado, verbo HTTP, chave API e, se for caso disso, um corpo JSON. `indexExistsAsync()` e `deleteIndexAsync()` não têm um corpo JSON, mas `createIndexAsync(definition)` tem.
* `await` a resposta ao pedido.  
* Aja no código de estado da resposta.
* Devolver uma Promessa de algum valor apropriado (um Boolean, `this`, ou os resultados da consulta). 

```javascript
async indexExistsAsync() { 
    console.log("\n Checking if index exists...");
    const endpoint = this.getIndexUrl();
    const response = await AzureSearchClient.request(endpoint, "GET", this.adminKey);
    // Success has a few likely status codes: 200 or 204 (No Content), but accept all in 200 range...
    const exists = response.status >= 200 && response.status < 300;
    return exists;
}

async deleteIndexAsync() {
    console.log("\n Deleting existing index...");
    const endpoint = this.getIndexUrl();
    const response = await AzureSearchClient.request(endpoint, "DELETE", this.adminKey);
    AzureSearchClient.throwOnHttpError(response);
    return this;
}

async createIndexAsync(definition) {
    console.log("\n Creating index...");
    const endpoint = this.getIndexUrl();
    const response = await AzureSearchClient.request(endpoint, "PUT", this.adminKey, definition);
    AzureSearchClient.throwOnHttpError(response);
    return this;
}
```

Confirme que os seus métodos estão dentro da classe e que está a exportar a classe. O âmbito mais exterior do **AzureSearchClient.js** deve ser:

```javascript
const fetch = require('node-fetch');

class AzureSearchClient {
    // ... code here ...
}

module.exports = AzureSearchClient;
```

Uma classe orientada para objetos foi uma boa escolha para o módulo potencialmente reutilizável **AzureSearchClient.js,** mas não é necessário para o programa principal, que deve colocar num ficheiro chamado **index.js**. 

Crie **index.js** e comece por trazer:

* O pacote **nconf,** que lhe dá flexibilidade para especificar a configuração com JSON, variáveis ambientais ou argumentos de linha de comando.
* Os dados do ficheiro **hotels_quickstart_index.json.**
* O módulo `AzureSearchClient`.

```javascript
const nconf = require('nconf');

const indexDefinition = require('./hotels_quickstart_index.json');
const AzureSearchClient = require('./AzureSearchClient.js');
```

O pacote [ **nconf** ](https://github.com/indexzero/nconf) permite especificar dados de configuração em vários formatos, tais como variáveis ambientais ou a linha de comando. Esta amostra utiliza **o nconf** de forma básica para ler o ficheiro **azure_search_config.json** e devolver o conteúdo desse ficheiro como um dicionário. Utilizando a função `get(key)` da **Nconf,** pode fazer uma verificação rápida de que a informação de configuração foi devidamente personalizada. Finalmente, a função devolve a configuração:

```javascript
function getAzureConfiguration() {
    const config = nconf.file({ file: 'azure_search_config.json' });
    if (config.get('serviceName') === '[SEARCH_SERVICE_NAME]' ) {
        throw new Error("You have not set the values in your azure_search_config.json file. Change them to match your search service's values.");
    }
    return config;
}
```

A função `sleep` cria uma `Promise` que se resolve após um determinado período de tempo. A utilização desta função permite que a aplicação pare enquanto espera que as operações de índice assíncronos completem e fiquem disponíveis. A adição de tal atraso é normalmente necessária apenas em demonstrações, testes e aplicações de amostra.

```javascript
function sleep(ms) {
    return(
        new Promise(function(resolve, reject) {
            setTimeout(function() { resolve(); }, ms);
        })
    );
}
```

Por fim, especifique e ligue para a principal função assíncrona `run`. Esta função chama as outras funções em ordem, aguardando se necessário para resolver `Promise`s.

* Recupere a configuração com o `getAzureConfiguration()` que escreveu anteriormente
* Crie um novo `AzureSearchClient` instância, passando em valores a partir da sua configuração
* Verifique se o índice existe e, se o fizer, elimine-o
* Crie um índice usando o `indexDefinition` carregado de **hotels_quickstart_index.json**

```javascript
const run = async () => {
    try {
        const cfg = getAzureConfiguration();
        const client = new AzureSearchClient(cfg.get("serviceName"), cfg.get("adminKey"), cfg.get("queryKey"), cfg.get("indexName));
        
        const exists = await client.indexExistsAsync();
        await exists ? client.deleteIndexAsync() : Promise.resolve();
        // Deleting index can take a few seconds
        await sleep(2000);
        await client.createIndexAsync(indexDefinition);
    } catch (x) {
        console.log(x);
    }
}

run();
```

Não se esqueça da última chamada para `run()`! É o ponto de entrada do seu programa quando corre `node index.js` no próximo passo.

Note que `AzureSearchClient.indexExistsAsync()` e `AzureSearchClient.deleteIndexAsync()` não têm parâmetros. Estas funções chamam `AzureSearchClient.request()` sem argumentos `bodyJson`. No `AzureSearchClient.request()`, uma vez que `bodyJson === null` é `true`, a estrutura `init` deverá ser apenas o verbo HTTP ("GET" para `indexExistsAsync()` e "DELETE" para `deleteIndexAsync()`) e os cabeçalhos, que especificam a chave de pedido.  

Em contraste, o método `AzureSearchClient.createIndexAsync(indexDefinition)` _leva_ um parâmetro. A função `run` em `index.js`, passa o conteúdo do ficheiro **hotels_quickstart_index.json** para o método `AzureSearchClient.createIndexAsync(indexDefinition)`. O método `createIndexAsync()` passa esta definição para `AzureSearchClient.request()`. Em `AzureSearchClient.request()`, uma vez que `bodyJson === null` é agora `false`, a estrutura `init` inclui não só o verbo HTTP ("PUT") e os cabeçalhos, mas define a `body` aos dados de definição de índice.

### <a name="prepare-and-run-the-sample"></a>Preparar e executar a amostra

Utilize uma janela de terminais para os seguintes comandos.

1. Navegue para a pasta que contém o ficheiro **package.json** e o resto do seu código.
1. Instale as embalagens para a amostra com `npm install`.  Este comando irá descarregar os pacotes de que o código depende.
1. Execute o seu programa com `node index.js`.

Devia ver uma série de mensagens descrevendo as ações que estão a ser tomadas pelo programa. Se quiser ver mais detalhes sobre os pedidos, pode descomentar as [linhas no início do método `AzureSearchClient.request()`]https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/AzureSearchClient.js#L21-L27) em **AzureSearchClient.js**. 

Abra a **visão geral** do seu serviço de pesquisa no portal Azure. Selecione o separador **Indexes.** Devia ver algo como o seguinte:

![Screenshot do portal Azure, visão geral do serviço de pesquisa, separador de índices](media/search-get-started-nodejs/create-index-no-data.png)

No próximo passo, irá adicionar dados ao índice. 

## <a name="2---load-documents"></a>2 - Documentos de Carga 

Na Pesquisa Cognitiva Azure, os documentos são estruturas de dados que são tanto inputs para indexação como saídas de consultas. É necessário publicar esses dados no índice. Isto utiliza um ponto final diferente do que as operações efetuadas no passo anterior. Abra **o AzureSearchClient.js** e adicione o seguinte método após `getIndexUrl()`:

```javascript
 getPostDataUrl() { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}/docs/index?api-version=${this.apiVersion}`;  }
```

Tal como `AzureSearchClient.createIndexAsync(definition)`, precisa de uma função que chame `AzureSearchClient.request()` e passe os dados do hotel para ser o seu corpo. Em **AzureSearchClient.js** adicione `postDataAsync(hotelsData)` após `createIndexAsync(definition)`:

```javascript
async postDataAsync(hotelsData) {
    console.log("\n Adding hotel data...");
    const endpoint = this.getPostDataUrl();
    const response = await AzureSearchClient.request(endpoint,"POST", this.adminKey, hotelsData);
    AzureSearchClient.throwOnHttpError(response);
    return this;
}
```

 As inputs de documentos podem ser filas numa base de dados, bolhas no armazenamento blob, ou, como nesta amostra, documentos JSON no disco. Você pode baixar [hotéis.json](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/hotels.json) ou criar o seu próprio **ficheiro hotels.json** com o seguinte conteúdo:

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

Para carregar estes dados no seu programa, modifique **o index.js** adicionando a linha referente a `hotelData` perto do topo:

```javascript
const nconf = require('nconf');

const hotelData = require('./hotels.json');
const indexDefinition = require('./hotels_quickstart_index.json');
```

Agora modifique a função `run()` em **index.js**. Pode levar alguns segundos para que o índice fique disponível, por isso adicione uma pausa de 2 segundos antes de chamar `AzureSearchClient.postDataAsync(hotelData)`:

```javascript
const run = async () => {
    try {
        const cfg = getAzureConfiguration();
        const client = new AzureSearchClient(cfg.get("serviceName"), cfg.get("adminKey"), cfg.get("queryKey"), cfg.get("indexName"));
        
        const exists = await client.indexExistsAsync();
        await exists ? client.deleteIndexAsync() : Promise.resolve();
        // Deleting index can take a few seconds
        await sleep(2000);
        await client.createIndexAsync(indexDefinition);
        // Index availability can take a few seconds
        await sleep(2000);
        await client.postDataAsync(hotelData);
    } catch (x) {
        console.log(x);
    }
}
```

Volte a executar o programa com `node index.js`. Deve ver um conjunto ligeiramente diferente de mensagens daquelas que viu no Passo 1. Desta vez, o índice _existe,_ e deve ver mensagem sobre a sua amortecedor antes que a app crie o novo índice e publique dados para o mesmo. 

## <a name="3---search-an-index"></a>3 - Pesquisar um índice

Volte ao separador **Indexes** na **visão geral** do seu serviço de pesquisa no portal Azure. O seu índice contém agora quatro documentos e consome alguma quantidade de armazenamento (pode levar alguns minutos para que a UI reflita adequadamente o estado subjacente do índice). Clique no nome do índice a ser levado para o Explorador de **Pesquisa**. Esta página permite-lhe experimentar consultas de dados. Experimente uma série de `*&$count=true` de consulta e deve recuperar todos os seus documentos e o número de resultados. Experimente com a cadeia de consulta `historic&highlight=Description&$filter=Rating gt 4` e deve recuperar um único documento, com a palavra "histórico" embrulhada em etiquetas `<em></em>`. Leia mais sobre [como compor uma consulta em Pesquisa Cognitiva Azure](https://docs.microsoft.com/azure/search/search-query-overview). 

Reproduza estas consultas em código abrindo **o index.js** e adicionando este código perto do topo:

```javascript
const queries = [
    "*&$count=true",
    "historic&highlight=Description&$filter=Rating gt 4&"
];
```

No mesmo ficheiro **index.js,** escreva a função `doQueriesAsync()` mostrada abaixo. Esta função pega num objeto `AzureSearchClient` e aplica o método `AzureSearchClient.queryAsync` a cada um dos valores da matriz `queries`. Usa a função `Promise.all()` para devolver um único `Promise` que só resolve quando todas as consultas tiverem resolvido. A chamada para `JSON.stringify(body, null, 4)` forma o resultado da consulta para ser mais legível.

```javascript
async function doQueriesAsync(client) {
    return Promise.all(
        queries.map( async query => {
            const result = await client.queryAsync(query);
            const body = await result.json();
            const str = JSON.stringify( body, null, 4);
            console.log(`Query: ${query} \n ${str}`);
        })
    );
}
```

Modifique a função `run()` para parar o tempo suficiente para o indexante funcionar e, em seguida, chamar a função `doQueriesAsync(client)`:

```javascript
const run = async () => {
    try {
        const cfg = getAzureConfiguration();
        const client = new AzureSearchClient(cfg.get("serviceName"), cfg.get("adminKey"), cfg.get("queryKey"), cfg.get("indexName"));
        
        const exists = await client.indexExistsAsync();
        await exists ? client.deleteIndexAsync() : Promise.resolve();
        // Deleting index can take a few seconds
        await sleep(2000);
        await client.createIndexAsync(indexDefinition);
        // Index availability can take a few seconds
        await sleep(2000);
        await client.postDataAsync(hotelData);
        // Data availability can take a few seconds
        await sleep(5000);
        await doQueriesAsync(client);
    } catch (x) {
        console.log(x);
    }
}
```

Para implementar `AzureSearchClient.queryAsync(query)`, edite o ficheiro **AzureSearchClient.js**. A procura requer um ponto final diferente, e os termos de pesquisa tornam-se argumentos URL, por isso adicione a função `getSearchUrl(searchTerm)` ao lado dos métodos `getIndexUrl()` e `getPostDataUrl()` que já escreveu.

```javascript
getSearchUrl(searchTerm) { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}/docs?api-version=${this.apiVersion}&search=${searchTerm}&searchMode=all`; }
 ```

A função `queryAsync(searchTerm)` também vai em **AzureSearchClient.js** e segue a mesma estrutura que `postDataAsync(data)` e outras funções de consulta: 

```javascript
async queryAsync(searchTerm) {
    console.log("\n Querying...")
    const endpoint = this.getSearchUrl(searchTerm);
    const response = await AzureSearchClient.request(endpoint, "GET", this.queryKey);
    AzureSearchClient.throwOnHttpError(response);
    return response;
}
```

A pesquisa é feita com o verbo "GET" e sem corpo, uma vez que o termo de pesquisa faz parte do URL. Note que `queryAsync(searchTerm)` usa `this.queryKey`, ao contrário das outras funções que usaram a chave de administração. As teclas de consulta, como o nome indica, só podem ser usadas para consultar o índice e não podem ser usadas para modificar o índice de forma alguma. As chaves de consulta são, portanto, mais seguras para distribuir para aplicações de clientes.

Executar o programa com `node index.js`. Agora, para além dos passos anteriores, as consultas serão enviadas e os resultados escritos para a consola.

### <a name="about-the-sample"></a>Sobre a amostra

A amostra utiliza uma pequena quantidade de dados do hotel, suficiente para demonstrar o básico de criar e consultar um índice de Pesquisa Cognitiva Azure.

A classe **AzureSearchClient** encapsula a configuração, URLs e pedidos básicos de HTTP para o serviço de pesquisa. O ficheiro **index.js** carrega os dados de configuração do serviço de Pesquisa Cognitiva Azure, os dados do hotel que serão carregados para indexação e, na sua função `run`, encomendas e executa as várias operações.

O comportamento geral da função `run` é apagar o índice de Pesquisa Cognitiva Azure se existir, criar o índice, adicionar alguns dados e realizar algumas consultas.  

## <a name="clean-up-resources"></a>Limpar recursos

Quando está a trabalhar na sua própria subscrição, é uma boa ideia no final de um projeto identificar se ainda precisa dos recursos que criou. Os recursos deixados a funcionar podem custar-lhe dinheiro. Pode eliminar os recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Pode encontrar e gerir recursos no portal, utilizando a ligação **De Todos os recursos** ou **grupos de Recursos** no painel de navegação à esquerda.

Se estiver a utilizar um serviço gratuito, lembre-se de que está limitado a três índices, indexadores e fontes de dados. Pode eliminar itens individuais no portal para se manter abaixo do limite. 

## <a name="next-steps"></a>Passos seguintes

Neste Início rápido do Node.js, trabalhou através de uma série de tarefas para criar um índice, carregá-lo com documentos e executar consultas. Fizemos certos passos, como ler a configuração e definir as consultas, da forma mais simples possível. Numa aplicação real, gostaria de colocar essas preocupações em módulos separados que proporcionassem flexibilidade e encapsulamento. 
 
Se já tiver algum passado em Pesquisa Cognitiva Azure, pode utilizar esta amostra como trampolim para experimentar sugestionantes (consultas de tipo à frente ou auto-completas), filtros e navegação facial. Se você é novo na Pesquisa Cognitiva Azure, recomendamos tentar outros tutoriais para desenvolver uma compreensão do que você pode criar. Visite a nossa [página de documentação](https://azure.microsoft.com/documentation/services/search/) para obter mais recursos. 

> [!div class="nextstepaction"]
> [Ligue para a pesquisa cognitiva azure a partir de uma WebPage usando Javascript](https://github.com/liamca/azure-search-javascript-samples)
