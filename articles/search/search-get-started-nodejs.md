---
title: 'Quickstart: Criar um índice de pesquisa em Node.js usando APIs REST'
titleSuffix: Azure Cognitive Search
description: Neste Node.js quickstart, aprenda a criar um índice, carregar dados e executar consultas na Azure Cognitive Search usando JavaScript e as APIs REST REST.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.devlang: nodejs
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 06/23/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: 2c97a770dc10168284bebbc038d8c48145c2a385
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88917895"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-nodejs-using-rest-apis"></a>Quickstart: Criar um índice de pesquisa cognitiva Azure em Node.js usando APIs REST
> [!div class="op_single_selector"]
> * [JavaScript](search-get-started-nodejs.md)
> * [C#](search-get-started-dotnet.md)
> * [Portal](search-get-started-portal.md)
> * [PowerShell](./search-get-started-powershell.md)
> * [Python](search-get-started-python.md)
> * [Postman](search-get-started-postman.md)

Crie uma aplicação Node.js que cria, carrega e consulta um índice de Pesquisa Cognitiva Azure. Este artigo demonstra como criar a aplicação passo a passo. Em alternativa, pode [descarregar o código de origem e os dados](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/) e executar a aplicação a partir da linha de comando.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Utilizamos o seguinte software e serviços para construir e testar este arranque rápido:

+ [Node.js](https://nodejs.org)

+ [O NPM](https://www.npmjs.com) deve ser instalado por Node.js

+ Uma estrutura de índice de amostra e documentos correspondentes são fornecidos neste artigo, ou a partir do [ **diretório** de arranque rápido do repo](https://github.com/Azure-Samples/azure-search-javascript-samples/)

+ [Crie um serviço de Pesquisa Cognitiva Azure](search-create-service-portal.md) ou [encontre um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) sob a sua subscrição atual. Pode utilizar um serviço gratuito para este arranque rápido.

Recomendação:

* [Visual Studio Code](https://code.visualstudio.com)

* [Extensões mais](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode) bonitas e [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint) para VSCode.

<a name="get-service-info"></a>

## <a name="get-keys-and-urls"></a>Obter chaves e URLs

As chamadas para o serviço requerem um ponto final URL e uma chave de acesso em cada pedido. Um serviço de pesquisa é criado com ambos, por isso, se adicionar Azure Cognitive Search à sua subscrição, siga estes passos para obter as informações necessárias:

1. [Inscreva-se no portal Azure,](https://portal.azure.com/)e na página **de Visão Geral** do seu serviço de pesquisa, obtenha o nome do seu serviço de pesquisa. Pode confirmar o seu nome de serviço revendo o URL do ponto final. Se o seu URL de ponto final `https://mydemo.search.windows.net` fosse, o seu nome de serviço seria `mydemo` .

2. Em **Definições**  >  **Teclas,** obtenha uma chave de administração para todos os direitos sobre o serviço. Existem duas chaves de administração intercambiáveis, previstas para a continuidade do negócio, caso precise de rolar uma. Pode utilizar a tecla primária ou secundária nos pedidos de adição, modificação e eliminação de objetos.

    Pegue a chave de consulta também. É uma boa prática emitir pedidos de consulta com acesso apenas de leitura.

![Obtenha o nome de serviço e as chaves de administração e consulta](media/search-get-started-nodejs/service-name-and-keys.png)

Todos os pedidos requerem uma chave API no cabeçalho de cada pedido enviado ao seu serviço. Uma chave válida estabelece confiança, por pedido, entre o pedido de envio do pedido e o serviço que o trata.

## <a name="set-up-your-environment"></a>Configurar o ambiente

Comece por abrir uma consola Powershell ou outro ambiente no qual instalou Node.js.

1. Crie um diretório de desenvolvimento, dando-lhe o `quickstart` nome:

    ```powershell
    mkdir quickstart
    cd quickstart
    ```

2. Inicialize um projeto vazio com NPM `npm init` executando. Aceite os valores predefinidos, com exceção da Licença, que deve definir para "MIT". 

1. Adicione pacotes que serão dependentes pelo código e ajuda no desenvolvimento:

    ```powershell
    npm install nconf node-fetch
    npm install --save-dev eslint eslint-config-prettier eslint-config-airbnb-base eslint-plugin-import prettier
    ```

4. Confirme que configura os projetos e as suas dependências verificando se o seu  **package.jsem** ficheiros se parece com o seguinte:

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

5. Crie um ficheiro **azure_search_config.js** para guardar os dados do seu serviço de pesquisa:

    ```json
    {
        "serviceName" : "[SEARCH_SERVICE_NAME]",
        "adminKey" : "[ADMIN_KEY]",
        "queryKey" : "[QUERY_KEY]",
        "indexName" : "hotels-quickstart"
    }
    ```

Substitua o `[SERVICE_NAME]` valor pelo nome do seu serviço de pesquisa. Substitua `[ADMIN_KEY]` e `[QUERY_KEY]` pelos valores-chave que registou anteriormente. 

## <a name="1---create-index"></a>1 - Criar índice 

Crie um ficheiro **hotels_quickstart_index.jsligado .**  Este ficheiro define como a Azure Cognitive Search funciona com os documentos que vai carregar no próximo passo. Cada campo será identificado por um `name` e terá um especificado `type` . Cada campo também tem uma série de atributos de índice que especificam se a Azure Cognitive Search pode pesquisar, filtrar, classificar e facet no campo. A maioria dos campos são tipos de dados simples, mas alguns, como `AddressType` são tipos complexos que permitem criar estruturas de dados ricas no seu índice.  Pode ler mais sobre [tipos de dados suportados](/rest/api/searchservice/supported-data-types) e [atributos de índice.](./search-what-is-an-index.md#index-attributes) 

Adicione o seguinte para **hotels_quickstart_index.js** ou [descarregue o ficheiro](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/hotels_quickstart_index.json). 

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
    

É uma boa prática separar as especificidades de um determinado cenário do código que será amplamente aplicável. A `AzureSearchClient` classe definida no ficheiro **AzureSearchClient.js** saberá como construir URLs de pedido, fazer um pedido usando a API Fetch e reagir ao código de estado da resposta.

Comece a trabalhar em **AzureSearchClient.js** importando o pacote **de busca de nó** e criando uma classe simples. Isolar as partes mutáveis da classe passando para o `AzureSearchClient` seu construtor os vários valores de configuração:

```javascript
const fetch = require('node-fetch');

class AzureSearchClient {
    constructor(searchServiceName, adminKey, queryKey, indexName) {
        this.searchServiceName = searchServiceName;
        this.adminKey = adminKey;
        // The query key is used for read-only requests and so can be distributed with less risk of abuse.
        this.queryKey = queryKey;
        this.indexName = indexName;
        this.apiVersion = '2020-06-30';
    }

    // All methods go inside class body here!
}

module.exports = AzureSearchClient;
```

A primeira responsabilidade da classe é saber como construir URLs para os quais enviar os vários pedidos. Construa estes URLs com métodos de exemplo que utilizam os dados de configuração passados para o construtor de classes. Note que o URL que constroem é específico de uma versão API e deve ter um argumento que especifique essa versão (nesta aplicação, `2020-06-30` ). 

O primeiro destes métodos devolverá o URL para o próprio índice. Adicione o seguinte método dentro do corpo da classe:

```javascript
getIndexUrl() { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}?api-version=${this.apiVersion}`; }

```

A próxima responsabilidade `AzureSearchClient` é fazer um pedido assíncrono com a API fetch. O método assíncrona estático `request` requer um URL, uma cadeia especificando o método HTTP ("GET", "PUT", "POST", "DELETE"), a chave a ser usada no pedido, e um objeto JSON opcional. A `headers` variável mapeia o (seja a chave de `queryKey` administração ou a chave de consulta apenas de leitura) para o cabeçalho de pedido HTTP "api-key". As opções de pedido contêm sempre o `method` a ser utilizado e o `headers` . Se `bodyJson` não `null` for, o corpo do pedido HTTP é definido para a representação de cadeia de `bodyJson` . O `request` método devolve a Promessa da API da Fetch para executar o pedido HTTP.

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

Para fins de demonstração, basta lançar uma exceção se o pedido HTTP não for um sucesso. Numa aplicação real, provavelmente faria algum registo e diagnóstico do código de estado HTTP no `response` pedido do serviço de pesquisa. 
    
```javascript
static throwOnHttpError(response) {
    const statusCode = response.status;
    if (statusCode >= 300){
        console.log(`Request failed: ${JSON.stringify(response, null, 4)}`);
        throw new Error(`Failure in request. HTTP Status was ${statusCode}`);
    }
}
```

Finalmente, adicione os métodos para detetar, eliminar e criar o índice de Pesquisa Cognitiva Azure. Todos estes métodos têm a mesma estrutura:

* Obtenha o ponto final para o qual o pedido será feito.
* Gerencie o pedido com o ponto final apropriado, verbo HTTP, tecla API e, se for caso disso, um corpo JSON. `indexExistsAsync()` e `deleteIndexAsync()` não tem um corpo JSON, mas `createIndexAsync(definition)` tem.
* `await` a resposta ao pedido.  
* Agir sobre o código de estado da resposta.
* Devolva uma Promessa de algum valor apropriado (um `this` Boolean, ou os resultados da consulta). 

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

Confirme que os seus métodos estão dentro da aula e que está a exportar a classe. O âmbito mais externo das **AzureSearchClient.js** deve ser:

```javascript
const fetch = require('node-fetch');

class AzureSearchClient {
    // ... code here ...
}

module.exports = AzureSearchClient;
```

Uma aula orientada a objetos foi uma boa escolha para o módulo ** deAzureSearchClient.js** potencialmente reutilizável, mas não é necessário para o programa principal, que deve colocar num ficheiro chamado **index.js**. 

Crie **index.js** e comece por trazer:

* O pacote **nconf,** que lhe dá flexibilidade para especificar a configuração com JSON, variáveis ambientais ou argumentos de linha de comando.
* Os dados do **hotels_quickstart_index.jsarquivados.**
* O módulo `AzureSearchClient`.

```javascript
const nconf = require('nconf');

const indexDefinition = require('./hotels_quickstart_index.json');
const AzureSearchClient = require('./AzureSearchClient.js');
```

O pacote [ **nconf** ](https://github.com/indexzero/nconf) permite especificar dados de configuração em vários formatos, tais como variáveis ambientais ou a linha de comando. Esta amostra utiliza **nconf** de forma básica para ler o ficheiro **azure_search_config.js** e devolver o conteúdo desse ficheiro como um dicionário. Utilizando a função **nconf,** `get(key)` pode fazer uma verificação rápida de que as informações de configuração foram devidamente personalizadas. Finalmente, a função devolve a configuração:

```javascript
function getAzureConfiguration() {
    const config = nconf.file({ file: 'azure_search_config.json' });
    if (config.get('serviceName') === '[SEARCH_SERVICE_NAME]' ) {
        throw new Error("You have not set the values in your azure_search_config.json file. Change them to match your search service's values.");
    }
    return config;
}
```

A `sleep` função cria uma `Promise` que se resolve após um determinado período de tempo. A utilização desta função permite que a app pare enquanto espera que as operações de índice assíncronos completem e se tornem disponíveis. A adição de tal atraso é normalmente apenas necessária em demos, testes e aplicações de amostra.

```javascript
function sleep(ms) {
    return(
        new Promise(function(resolve, reject) {
            setTimeout(function() { resolve(); }, ms);
        })
    );
}
```

Finalmente, especifique e chame a função assíncronea `run` principal. Esta função chama as outras funções em ordem, aguardando-se se necessário para resolver `Promise` s.

* Recupere a configuração com a `getAzureConfiguration()` que escreveu anteriormente
* Crie um novo `AzureSearchClient` exemplo, passando em valores a partir da sua configuração
* Verifique se o índice existe e, se o faz, elimine-o
* Criar um índice usando o `indexDefinition` carregado a partir dehotels_quickstart_index.js** em**

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
    } catch (x) {
        console.log(x);
    }
}

run();
```

Não se esqueça da chamada final `run()` para! É o ponto de entrada do seu programa quando corre `node index.js` no próximo passo.

Note isso `AzureSearchClient.indexExistsAsync()` e `AzureSearchClient.deleteIndexAsync()` não tome parâmetros. Estas funções chamam `AzureSearchClient.request()` sem `bodyJson` argumentos. Dentro `AzureSearchClient.request()` de , uma vez que é , a estrutura está definida para ser `bodyJson === null` `true` `init` apenas o verbo HTTP ("GET" para `indexExistsAsync()` e "DELETE" `deleteIndexAsync()` para) e os cabeçalhos, que especificam a tecla de pedido.  

Em contraste, o `AzureSearchClient.createIndexAsync(indexDefinition)` método tem um parâmetro. _does_ A `run` função em `index.js` , passa o conteúdo do ficheirohotels_quickstart_index.js** para** o `AzureSearchClient.createIndexAsync(indexDefinition)` método. O `createIndexAsync()` método passa esta definição para `AzureSearchClient.request()` . Em `AzureSearchClient.request()` , já que é agora , a estrutura inclui não só o `bodyJson === null` `false` `init` verbo HTTP ("PUT") e os cabeçalhos, mas define os `body` dados de definição de índice.

### <a name="prepare-and-run-the-sample"></a>Prepare e execute a amostra

Utilize uma janela terminal para os seguintes comandos.

1. Navegue para a pasta que contém o **package.jsno** ficheiro e o resto do seu código.
1. Instale as embalagens para a amostra com `npm install` .  Este comando irá descarregar os pacotes de que depende o código.
1. Executar o seu programa com `node index.js` .

Deve ver uma série de mensagens descrevendo as ações que estão a ser tomadas pelo programa. Se quiser ver mais detalhes sobre os pedidos, pode descomprometer as [linhas no início do `AzureSearchClient.request()` método] https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/AzureSearchClient.js#L21-L27) em **AzureSearchClient.js**. 

Abra a **visão geral** do seu serviço de pesquisa no portal Azure. Selecione o separador **Índices.** Devia ver algo como o seguinte:

![Screenshot do portal Azure, Visão geral do serviço de pesquisa, separador Índices](media/search-get-started-nodejs/create-index-no-data.png)

No próximo passo, irá adicionar dados ao indexar. 

## <a name="2---load-documents"></a>2 - Documentos de Carga 

Na Pesquisa Cognitiva Azure, os documentos são estruturas de dados que são entradas para indexação e saídas de consultas. É necessário colocar esses dados no índice. Isto utiliza um ponto final diferente das operações efetuadas no passo anterior. Abra **AzureSearchClient.js** e adicione o seguinte método `getIndexUrl()` depois:

```javascript
 getPostDataUrl() { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}/docs/index?api-version=${this.apiVersion}`;  }
```

Como, `AzureSearchClient.createIndexAsync(definition)` você precisa de uma função que ligue e passe nos `AzureSearchClient.request()` dados do hotel para ser o seu corpo. Em **AzureSearchClient.js** adicione `postDataAsync(hotelsData)` `createIndexAsync(definition)` depois:

```javascript
async postDataAsync(hotelsData) {
    console.log("\n Adding hotel data...");
    const endpoint = this.getPostDataUrl();
    const response = await AzureSearchClient.request(endpoint,"POST", this.adminKey, hotelsData);
    AzureSearchClient.throwOnHttpError(response);
    return this;
}
```

 As entradas de documentos podem ser linhas numa base de dados, bolhas no armazenamento blob, ou, como nesta amostra, documentos JSON no disco. Pode baixar [hotels.js](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/hotels.json) ou criar o seu próprio **hotels.jsem** ficheiro com o seguinte conteúdo:

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

Para carregar estes dados no seu programa, modifique **index.js** adicionando a linha referente ao `hotelData` topo:

```javascript
const nconf = require('nconf');

const hotelData = require('./hotels.json');
const indexDefinition = require('./hotels_quickstart_index.json');
```

Agora modificar a `run()` função em **index.js**. Pode levar alguns segundos para que o índice fique disponível, por isso adicione uma pausa de 2 segundos antes de `AzureSearchClient.postDataAsync(hotelData)` ligar:

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

Executar o programa novamente com `node index.js` . Deve ver um conjunto ligeiramente diferente de mensagens daquelas que viu no Passo 1. Desta vez, o índice _existe,_ e deve ver mensagem sobre a sua eliminação antes que a app crie o novo índice e publique dados para o mesmo. 

## <a name="3---search-an-index"></a>3 - Pesquisar um índice

Volte ao separador **Índices** na **Visão Geral** do seu serviço de pesquisa no portal Azure. O seu índice contém agora quatro documentos e consome alguma quantidade de armazenamento (pode levar alguns minutos para a UI refletir adequadamente o estado subjacente do índice). Clique no nome do índice a ser levado para o **Explorador de Pesquisa.** Esta página permite-lhe experimentar consultas de dados. Tente pesquisar uma série de consultas `*&$count=true` e deve recuperar todos os seus documentos e o número de resultados. Experimente com a cadeia de consulta `historic&highlight=Description&$filter=Rating gt 4` e deve recuperar um único documento, com a palavra "histórico" embrulhada em `<em></em>` etiquetas. Leia mais sobre [como compor uma consulta em Azure Cognitive Search](./search-query-overview.md). 

Reproduza estas consultas em código, abrindo **index.js** e adicionando este código perto do topo:

```javascript
const queries = [
    "*&$count=true",
    "historic&highlight=Description&$filter=Rating gt 4&"
];
```

No mesmo ** ficheiroindex.js, ** escreva a `doQueriesAsync()` função abaixo mostrada. Esta função pega num `AzureSearchClient` objeto e aplica o método a cada um dos `AzureSearchClient.queryAsync` valores da `queries` matriz. Usa a `Promise.all()` função para devolver um único `Promise` que só se resolve quando todas as consultas estão resolvidas. A chamada para `JSON.stringify(body, null, 4)` formatos da consulta resulta para ser mais legível.

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

Modificar a `run()` função para fazer uma pausa suficiente para que o indexante funcione e, em seguida, chame a `doQueriesAsync(client)` função:

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

Para `AzureSearchClient.queryAsync(query)` implementar, edite o ficheiro **AzureSearchClient.js**. A pesquisa requer um ponto final diferente, e os termos de pesquisa tornam-se argumentos URL, por isso adicione a função `getSearchUrl(searchTerm)` ao lado dos `getIndexUrl()` `getPostDataUrl()` métodos e métodos que já escreveu.

```javascript
getSearchUrl(searchTerm) { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}/docs?api-version=${this.apiVersion}&search=${searchTerm}&searchMode=all`; }
 ```

A `queryAsync(searchTerm)` função também vai em **AzureSearchClient.js** e segue a mesma estrutura que e `postDataAsync(data)` as outras funções de consulta: 

```javascript
async queryAsync(searchTerm) {
    console.log("\n Querying...")
    const endpoint = this.getSearchUrl(searchTerm);
    const response = await AzureSearchClient.request(endpoint, "GET", this.queryKey);
    AzureSearchClient.throwOnHttpError(response);
    return response;
}
```

A pesquisa é feita com o verbo "GET" e nenhum corpo, uma vez que o termo de pesquisa faz parte do URL. Note que `queryAsync(searchTerm)` usa , ao contrário das `this.queryKey` outras funções que usaram a tecla de administração. As teclas de consulta, como o nome indica, só podem ser usadas para consulta do índice e não podem ser usadas para modificar o índice de forma alguma. As chaves de consulta são, portanto, mais seguras para distribuir às aplicações do cliente.

Executar o programa com `node index.js` . Agora, para além dos passos anteriores, as consultas serão enviadas e os resultados escritos para a consola.

### <a name="about-the-sample"></a>Sobre a amostra

A amostra utiliza uma pequena quantidade de dados do hotel, suficientes para demonstrar os fundamentos da criação e consulta de um índice de Pesquisa Cognitiva Azure.

A classe **AzureSearchClient** encapsula a configuração, URLs e pedidos http básicos para o serviço de pesquisa. O ficheiro **index.js** carrega os dados de configuração do serviço de Pesquisa Cognitiva Azure, os dados do hotel que serão carregados para indexação e, na sua `run` função, encomendas e executa as várias operações.

O comportamento geral da `run` função é eliminar o índice de Pesquisa Cognitiva Azure se existir, criar o índice, adicionar alguns dados e realizar algumas consultas.  

## <a name="clean-up-resources"></a>Limpar os recursos

Ao trabalhar na sua própria subscrição, recomendamos que verifique, depois de concluir um projeto, se irá precisar dos recursos que criou. Os recursos que deixar em execução podem custar-lhe dinheiro. Pode eliminar recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Pode encontrar e gerir recursos no portal, utilizando a ligação **de todos os recursos** ou **grupos** de recursos no painel de navegação à esquerda.

Se estiver a utilizar um serviço gratuito, lembre-se que está limitado a três índices, indexadores e fontes de dados. Pode eliminar itens individuais no portal para ficar abaixo do limite. 

## <a name="next-steps"></a>Passos seguintes

Neste Node.js quickstart, trabalhou através de uma série de tarefas para criar um índice, carregá-lo com documentos e executar consultas. Fizemos certos passos, como ler a configuração e definir as consultas, da forma mais simples possível. Numa aplicação real, gostaria de colocar essas preocupações em módulos separados que proporcionassem flexibilidade e encapsulamento. 
 
Se já tiver algum fundo em Azure Cognitive Search, pode usar esta amostra como trampolim para experimentar sugestores (consultas de tipo à frente ou autocompletas), filtros e navegação frontal. Se é novo na Azure Cognitive Search, recomendamos experimentar outros tutoriais para desenvolver uma compreensão do que pode criar. Visite a nossa [página de documentação](https://azure.microsoft.com/documentation/services/search/) para obter mais recursos. 

> [!div class="nextstepaction"]
> [Ligue para a Azure Cognitive Search a partir de um WebPage usando Javascript](https://github.com/liamca/azure-search-javascript-samples)