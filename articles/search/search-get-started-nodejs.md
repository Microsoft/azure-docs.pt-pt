---
title: 'Início rápido do node. js: Criar, carregar e consultar índices usando Azure Search APIs REST-Azure Search'
description: Exemplo de Node. js para Azure Search, demonstrando como criar, carregar dados e consultar a partir do JavaScript.
author: lobrien
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 07/30/2019
ms.author: laobri
ms.openlocfilehash: 3a0b5706b41bdc51a4fe6e49b20296d3824b717c
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947125"
---
# <a name="quickstart-create-an-azure-search-index-in-nodejs"></a>Início rápido: Criar um índice de Azure Search no node. js
> [!div class="op_single_selector"]
> * [JavaScript](search-get-started-nodejs.md)
> * [C#](search-get-started-dotnet.md)
> * [Portal](search-get-started-portal.md)
> * [PowerShell](search-create-index-rest-api.md)
> * [Python](search-get-started-python.md)
> * [Postman](search-get-started-postman.md)

Crie um aplicativo node. js que cria, carrega e consulta um índice de Azure Search. Este artigo demonstra como criar o aplicativo passo a passo. Como alternativa, você pode [baixar o código-fonte e os dados](https://github.com/Azure-Samples/azure-search-javascript-samples/quickstart/) e executar o aplicativo na linha de comando.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Os serviços, as ferramentas e os dados a seguir são usados neste guia de início rápido.

+ [Node.js](https://nodejs.org).
+ [NPM](https://www.npmjs.com) deve ser instalado pelo node. js.
+ Um exemplo de estrutura de índice e documentos correspondentes são fornecidos neste artigo ou no [repositório](https://github.com/Azure-Samples/azure-search-javascript-samples/quickstart/).
+ [Crie um serviço de Azure Search](search-create-service-portal.md) ou [Localize um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) em sua assinatura atual. Você pode usar um serviço gratuito para este guia de início rápido.

Aconselhável

* [Visual Studio Code](https://code.visualstudio.com).
* Extensões [melhores](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode) e [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint) para VSCode.

<a name="get-service-info"></a>
## <a name="get-keys-and-urls"></a>Obter chaves e URLs

Chamadas para o serviço exigem um ponto de extremidade de URL e uma chave de acesso em cada solicitação. É criado um serviço de pesquisa com ambos os elementos, pelo que, se tiver adicionado o Azure Search à sua subscrição, siga estes passos para obter as informações necessárias:

1. [Entre no portal do Azure](https://portal.azure.com/)e, em sua página de **visão geral** do serviço de pesquisa, obtenha o nome do serviço de pesquisa. Você pode confirmar o nome do serviço examinando a URL do ponto de extremidade. Se a URL do ponto `https://mydemo.search.windows.net`de extremidade for, o nome `mydemo`do serviço será.

2. Em **configurações** > **chaves**, obtenha uma chave de administração para obter direitos totais sobre o serviço. Há duas chaves de administração intercambiáveis, fornecidas para a continuidade dos negócios, caso você precise fazer uma sobreposição. Você pode usar a chave primária ou secundária em solicitações para adicionar, modificar e excluir objetos.

    Obtenha também a chave de consulta. É uma prática recomendada emitir solicitações de consulta com acesso somente leitura.

![Obter o nome do serviço e as chaves de consulta e de administrador](media/search-get-started-nodejs/service-name-and-keys.png)

Todas as solicitações exigem uma chave de API no cabeçalho de cada solicitação enviada ao seu serviço. Uma chave válida estabelece confiança, por solicitação, entre o aplicativo que envia a solicitação e o serviço que a manipula.

## <a name="set-up-your-environment"></a>Configurar o ambiente

Comece abrindo um console do PowerShell ou outro ambiente no qual você instalou o Node. js.

1. Crie um diretório de desenvolvimento, dando a ele `quickstart` o nome:

    ```powershell
    mkdir quickstart
    cd quickstart
    ```

2. Inicialize um projeto vazio com NPM executando `npm init`. Aceite os valores padrão, exceto para a licença, que você deve definir como "MIT". 

1. Adicionar pacotes que serão dependentes do código e auxiliar no desenvolvimento:

    ```powershell
    npm install nconf node-fetch
    npm install --save-dev eslint eslint-config-prettier eslint-config-airbnb-base eslint-plugin-import prettier
    ```

4. Confirme que você configurou os projetos e suas dependências verificando se o arquivo **Package. JSON** é semelhante ao seguinte:

    ```json
    {
      "name": "quickstart",
      "version": "1.0.0",
      "description": "Azure Search Quickstart",
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
Crie um arquivo **azure_search_config. JSON** para manter os dados do serviço de pesquisa:

```json
{
    "serviceName" : "[SERVICE_NAME]",
    "adminKey" : "[ADMIN_KEY]",
    "queryKey" : "[QUERY_KEY]",
    "indexName" : "hotels-quickstart"
}
```

Substitua o `[SERVICE_NAME]` valor pelo nome do serviço de pesquisa. Substitua `[ADMIN_KEY]` e`[QUERY_KEY]` pelos valores de chave que você registrou anteriormente. 

## <a name="1---create-index"></a>1-criar índice 

Crie um arquivo **hotels_quickstart_index. JSON**.  Esse arquivo define como Azure Search funciona com os documentos que serão carregados na próxima etapa. Cada campo será identificado por um `name` e terá um especificado. `type` Cada campo também tem uma série de atributos de índice que especificam se Azure Search pode pesquisar, filtrar, classificar e facetar sobre o campo. A maioria dos campos são tipos de dados simples, mas alguns, `AddressType` como são tipos complexos que permitem que você crie estruturas de dados ricas em seu índice.  Você pode ler mais sobre tipos de dados e [atributos de índice](https://docs.microsoft.com/azure/search/search-what-is-an-index#index-attributes) [com suporte](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) . 

Adicione o seguinte a **hotels_quickstart_index. JSON** ou [Baixe o arquivo](https://github.com/Azure-Samples/azure-search-javascript-samples/quickstart/blob/master/hotels_quickstart_index.json). 

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
    

É uma boa prática separar as especificidades de um cenário específico do código que será amplamente aplicável. A `AzureSearchClient` classe definida no arquivo **AzureSearchClient. js** saberá como construir URLs de solicitação, fazer uma solicitação usando a API de busca e reagir ao código de status da resposta.

Comece a trabalhar em **AzureSearchClient. js** importando o pacote de **busca de nó** e criando uma classe simples. Isole as partes alteráveis da `AzureSearchClient` classe passando para seu Construtor os vários valores de configuração:

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

A primeira responsabilidade da classe é saber como construir URLs para as quais enviar as várias solicitações. Crie essas URLs com métodos de instância que usam os dados de configuração passados para o construtor de classe. Observe que a URL que ele constrói é específica para uma versão de API e deve ter um argumento especificando essa versão (neste aplicativo `2019-05-06`). 

Adicione o seguinte método dentro do corpo da classe:

```javascript
    getIndexUrl() { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}?api-version=${this.apiVersion}`; }

```

A próxima responsabilidade é fazer uma solicitação assíncrona com a API de busca. O método `request` estático assíncrono usa uma URL, uma cadeia de caracteres especificando o método http ("Get", "Put", "post", "Delete"), a chave a ser usada na solicitação e um objeto JSON opcional. A `headers` variável mapeia o `queryKey` (se a chave de administração ou a chave de consulta somente leitura) para o cabeçalho de solicitação HTTP "chave de API". As opções de solicitação sempre contêm `method` o a ser usado e `headers`o. Se `bodyJson` não `null`for, o corpo da solicitação HTTP será definido como a representação de cadeia `bodyJson`de caracteres de. O `request` retorna a promessa da API de busca para executar a solicitação HTTP.

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

Para fins de demonstração, vamos lançar uma exceção apenas se a solicitação HTTP não for um sucesso. Em um aplicativo real, você provavelmente faria algum log e diagnóstico do código de status HTTP no `response` da solicitação de serviço de pesquisa. 
    
```javascript
    static throwOnHttpError(response) {
    const statusCode = response.status;
    if (statusCode >= 300){
        console.log(`Request failed: ${JSON.stringify(response, null, 4)}`);
        throw new Error(`Failure in request. HTTP Status was ${statusCode}`);
    }
    }
```

Por fim, adicione os métodos para detectar, excluir e criar o índice de Azure Search. Todos esses métodos têm a mesma estrutura:

* Obtenha o ponto de extremidade para o qual a solicitação será feita.
* Gere a solicitação com o ponto de extremidade apropriado, o verbo HTTP, a chave de API e o corpo. `queryAsync()`usa a chave de consulta, caso contrário, a chave de administrador é usada.
* `await`a resposta à solicitação.  
* Atue no código de status da resposta.
* Retornar uma promessa de algum valor apropriado (um booliano `this`, ou os resultados da consulta). 

```javascript
    async indexExistsAsync() { 
        console.log("\n Checking if index exists...");
        const endpoint = this.getIndexUrl();
        const response = await AzureSearchClient.request(endpoint, "GET", this.queryKey);
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

Confirme se os seus métodos estão dentro da classe e se você está exportando a classe. O escopo mais externo de **AzureSearchClient. js** deve ser:

```javascript
const fetch = require('node-fetch');

class AzureSearchClient {
    // ... code here ...
}

module.exports = AzureSearchClient;
```

Uma classe orientada a objeto foi uma boa opção para o módulo **AzureSearchClient. js** potencialmente reutilizável, mas não é necessário para o programa principal, que colocaremos em um arquivo chamado **index. js**. 

Crie **index. js** e comece trazendo:

* O pacote **nconf** , que oferece flexibilidade para especificar a configuração com JSON, variáveis de ambiente ou argumentos de linha de comando.
* Os dados do arquivo **hotels_quickstart_index. JSON** .
* O módulo `AzureSearchClient`.

```javascript
const nconf = require('nconf');

const indexDefinition = require('./hotels_quickstart_index.json');
const AzureSearchClient = require('./AzureSearchClient.js');
```

O [pacote **nconf** ](https://github.com/indexzero/nconf) permite que você especifique dados de configuração em uma variedade de formatos, como variáveis de ambiente ou linha de comando. Vamos usar **nconf** de uma maneira básica para ler o arquivo **azure_search_config. JSON** e retornar o conteúdo desse arquivo como um dicionário. Usandoa função `get(key)` do nconf, podemos fazer uma verificação rápida de que as informações de configuração foram personalizadas corretamente. Por fim, retornamos a configuração:

```javascript
function getAzureConfiguration() {
    const config = nconf.file({ file: 'azure_search_config.json' });
    if (config.get('serviceName') === '[SEARCH_SERVICE_NAME' ) {
    throw new Error("You have not set the values in your azure_search_config.json file. Change them to match your search service's values.");
    }
    return config;
}
```

A `sleep` função cria um `Promise` que resolve após um período de tempo especificado. O uso dessa função permite que o aplicativo seja pausado enquanto aguarda a conclusão das operações de índice assíncronos e sua disponibilização. A adição desse atraso geralmente é necessária apenas em demonstrações, testes e aplicativos de exemplo.

```javascript
function sleep(ms)
{
    return(
        new Promise(function(resolve, reject)
        {
            setTimeout(function() { resolve(); }, ms);
        })
    );
}
```

Por fim, especifique e chame a função `run` assíncrona principal. Essa função chama as outras funções em ordem, aguardando conforme necessário para resolver `Promise`os s.

* Recuperar a configuração com a `getAzureConfiguration()` que você escreveu anteriormente
* Criar uma nova `AzureSearchClient` instância, passando valores de sua configuração
* Verifique se o índice existe e, se tiver, exclua-o
* Criar um índice usando o `indexDefinition` Loaded de **hotels_quickstart_index. JSON**
* Adicione os documentos sobre os hotéis que você carregou de **Hotéis. JSON**
* Consultar o índice de Azure Search usando `doQueriesAsync()` o método que você escreveu

```javascript
const run = async () => {
    try {
        const cfg = getAzureConfiguration();
        const client = new AzureSearchClient(cfg.get("serviceName"), cfg.get("adminKey"), cfg.get("queryKey"), cfg.get["serviceName"]);
        
        const exists = await client.indexExistsAsync();
        await exists ? client.deleteIndexAsync() : Promise.resolve();
        // Deleting index can take a few seconds
        await sleep(2000);
        const indexDefinition = require('./hotels_quickstart_index.json');
        await client.createIndexAsync(indexDefinition);
    } catch (x) {
        console.log(x);
    }
}

run();
```

Não se esqueça dessa chamada final `run()`para! É o ponto de entrada para seu programa quando você executa `node index.js` na próxima etapa.

Observe que `AzureSearchClient.indexExistsAsync()` e `AzureSearchClient.deleteIndexAsync()` não usam parâmetros. Essas funções chamam `AzureSearchClient.request()` sem `bodyJson` argumento. Em `AzureSearchClient.request()`, `bodyJson === null` `indexExistsAsync()` desde o, a`init` estrutura é definida para ser apenas o verbo http ("Get" para e "Delete" para `deleteIndexAsync()`) e os cabeçalhos, que especificam a chave de solicitação. `true`  

Por outro lado, `AzureSearchClient.createIndexAsync(indexDefinition)` o método assume um parâmetro. A `run` função no `index.js`, passa o conteúdo do arquivo **hotels_quickstart_index. JSON** para o `AzureSearchClient.createIndexAsync(indexDefinition)` método. O `createIndexAsync()` método passa essa definição para `AzureSearchClient.request()`. No `AzureSearchClient.request()`, como `bodyJson === null` `init` `body` agora `false`é, a estrutura inclui não apenas o verbo http ("Put") e os cabeçalhos, mas define o para os dados de definição de índice.

### <a name="prepare-and-run-the-sample"></a>Preparar e executar o exemplo

Use uma janela de terminal para os comandos a seguir.

1. Navegue até a pasta que contém o arquivo **Package. JSON** e o restante do seu código.
1. Instale os pacotes para o exemplo com `npm install`.  Esse comando baixará os pacotes dos quais o código depende.
1. Execute o programa com `node index.js`o.

Você deve ver uma série de mensagens que descrevem as ações que estão sendo executadas pelo programa. Se desejar ver mais detalhes das solicitações, você poderá remover os comentários das [linhas no início do `AzureSearchClient.request()` método](https://github.com/Azure-Samples/azure-search-javascript-samples/quickstart/blob/master/AzureSearchClient.js#LL20-LL26) em **AzureSearchClient. js**. 

Abra a **visão geral** do serviço de pesquisa no portal do Azure. Selecione a guia **índices** . Deverá ver algo semelhante ao seguinte:

![Captura de tela de portal do Azure, Serviço de Pesquisa visão geral, guia índices](media/search-get-started-nodejs/create-index-no-data.png)

Na próxima etapa, adicionaremos dados ao índice. 

## <a name="2---load-documents"></a>2-carregar documentos 

No Azure Search, os documentos são estruturas de dados que são entradas para indexação e saídas de consultas. Você precisa postar esses dados no índice. Isso usa um ponto de extremidade diferente do que as operações realizadas na etapa anterior. Abra **AzureSearchClient. js** e adicione o seguinte método após `getIndexUrl()`:

```javascript
 getPostDataUrl() { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}/docs/index?api-version=${this.apiVersion}`;  }
```

Assim `AzureSearchClient.createIndexAsync(definition)`, você precisa de uma função que `AzureSearchClient.request()` chame e passe nos dados do hotel para ser seu corpo. Em **AzureSearchClient. js** , `postDataAsync(hotelsData)` Adicione `createIndexAsync(definition)`após:

```javascript
async postDataAsync(hotelsData) {
    console.log("\n Adding hotel data...");
    const endpoint = this.getPostDataUrl();
    const response = await AzureSearchClient.request(endpoint,"POST", this.adminKey, hotelsData);
    AzureSearchClient.throwOnHttpError(response);
    return this;
}
```

 As entradas de documento podem ser linhas em um banco de dados, BLOBs no armazenamento de BLOBs ou, como neste exemplo, documentos JSON em disco. Você pode baixar [Hotéis. JSON](https://github.com/Azure-Samples/azure-search-javascript-samples/quickstart/blob/master/hotels.json) ou criar seu próprio arquivo **Hotéis. JSON** com o seguinte conteúdo:

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

Para carregar esses dados em seu programa, modifique o **index. js** adicionando a linha que se refere `hotelData` ao próximo da parte superior:

```javascript
const nconf = require('nconf');

const hotelData = require('./hotels.json');
const indexDefinition = require('./hotels_quickstart_index.json');
```

Agora, modifique `run()` a função em **index. js**. Pode levar alguns segundos para que o índice fique disponível, portanto, adicione uma pausa de 2 segundos antes de chamar `AzureSearchClient.postDataAsync(hotelData)`:

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

Execute o programa novamente com `node index.js`. Você verá um conjunto de mensagens um pouco diferente daqueles vistos na etapa 1. Desta vez, o índice existe e você deve ver a mensagem sobre como excluí-lo excluído antes de o aplicativo criar o novo índice e postar dados nele. 

## <a name="3---search-an-index"></a>3 - Pesquisar um índice

Retorne à guia **índices** na **visão geral** do serviço de pesquisa no portal do Azure. O índice agora contém quatro documentos e consome alguma quantidade de armazenamento (pode levar alguns minutos para que a interface do usuário reflita corretamente o estado subjacente do índice). Clique no nome do índice a ser levado para o **Gerenciador de pesquisa**. Esta página permite que você experimente as consultas de dados. Tente pesquisar em uma cadeia de caracteres `*&$count=true` de consulta de e você deve recuperar todos os seus documentos e o número de resultados. Experimente com a cadeia de `historic&highlight=Description&$filter=Rating gt 4` caracteres de consulta e você deve obter um único documento, com a palavra "histórico" `<em></em>` encapsulada em marcas. Leia mais sobre [como compor uma consulta no Azure Search](https://docs.microsoft.com/azure/search/search-query-overview). 

Reproduza essas consultas no código abrindo **index. js** e adicionando esse código próximo à parte superior:

```javascript
const queries = [
    "*&$count=true",
    "historic&highlight=Description&$filter=Rating gt 4&"
];
```

No mesmo arquivo **index. js** , escreva a `doQueries()` função mostrada abaixo. Essa função usa um `AzureSearchClient` objeto e aplica o `AzureSearchClient.queryAsync` método a cada `queries` um dos valores na matriz. Ele usa a `Promise.all()` função para retornar um único `Promise` que só resolve quando todas as consultas foram resolvidas. A chamada para `JSON.stringify(body, null, 4)` formata o resultado da consulta para ser mais legível.

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

Modifique a `run()` função para pausar o tempo suficiente para o indexador funcionar e, em seguida `doQueriesAsync(client)` , chame a função:

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

Para implementar `AzureSearchClient.queryAsync(query)`, edite o arquivo **AzureSearchClient. js**. A pesquisa requer um ponto de extremidade diferente, portanto `getSearchUrl(searchTerm)` , adicione `getIndexUrl()` a `getPostDataUrl()` função ao lado dos métodos e que você já escreveu.

```javascript
getSearchUrl(searchTerm) { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}/docs?api-version=${this.apiVersion}&search=${searchTerm}&searchMode=all`; }
 ```

A `queryAsync(searchTerm)` função também entra em **AzureSearchClient. js** e segue a mesma estrutura que `postDataAsync(data)` e as outras funções de consulta: 

```javascript
async queryAsync(searchTerm) {
    console.log("\n Querying...")
    const endpoint = this.getSearchUrl(searchTerm);
    const response = await AzureSearchClient.request(endpoint, "GET", this.queryKey);
    AzureSearchClient.throwOnHttpError(response);
    return response;
}
```

A pesquisa é feita com o verbo "GET" e nenhum corpo, já que o termo de pesquisa faz parte da URL. Observe que, ao contrário das outras funções usadas `this.adminKey`, `queryAsync(searchTerm)` o `this.queryKey`usa. As chaves de consulta, como o nome indica, só podem ser usadas para consultar o índice e não podem ser usadas para modificar o índice de forma alguma. As chaves de consulta são, portanto, mais seguras para serem distribuídas aos aplicativos cliente.

Execute o programa com `node index.js`. Agora, além das etapas anteriores, as consultas serão enviadas e os resultados gravados no console.

### <a name="about-the-sample"></a>Sobre o exemplo

O exemplo usa uma pequena quantidade de dados de Hotel, suficiente para demonstrar as noções básicas de criação e consulta de um índice de Azure Search.

A classe **AzureSearchClient** encapsula a configuração, as URLs e as solicitações HTTP básicas para o serviço de pesquisa. O arquivo **index. js** carrega os dados de configuração para o serviço de Azure Search, os dados do hotel que serão carregados para indexação e, em sua `run` função, ordena e executa as várias operações.

O comportamento geral da `run` função é excluir o índice de Azure Search, se ele existir, criar o índice, adicionar alguns dados e executar algumas consultas.  

## <a name="clean-up"></a>Limpeza 

Quando você está trabalhando em sua própria assinatura, é uma boa ideia no final de um projeto identificar se você ainda precisa dos recursos que criou. Os recursos deixados em execução podem custar dinheiro. Você pode excluir os recursos individualmente ou excluir o grupo de recursos para excluir o conjunto inteiro de recursos.

Você pode encontrar e gerenciar recursos no portal, usando o link **todos os recursos** ou **grupos de recursos** no painel de navegação esquerdo.
Se você estiver usando um serviço gratuito, lembre-se de que você está limitado a três índices, indexadores e fontes de dados. Você pode excluir itens individuais no portal para permanecer abaixo do limite. 

## <a name="next-steps"></a>Passos Seguintes

Neste guia de início rápido do node. js, você trabalhou em uma série de tarefas para criar um índice, carregá-lo com documentos e executar consultas. Fizemos determinadas etapas, como ler a configuração e definir as consultas, da maneira mais simples possível. Em um aplicativo real, você desejaria colocar essas preocupações em módulos separados que forneceriam flexibilidade e encapsulamento. 
 
Se já tiver algum conhecimento sobre a Azure Search, pode utilizar este exemplo como ponto de partida para tentar sugestores (escrita antecipada ou consultas de conclusão automática), filtros e navegação por facetas. Se você for novo no Azure Search, é recomendável tentar outros tutoriais para desenvolver um entendimento do que você pode criar. Visite a nossa [página de documentação](https://azure.microsoft.com/documentation/services/search/) para obter mais recursos. 

> [!div class="nextstepaction"]
> [Chamar Azure Search de uma página da Web usando JavaScript](https://github.com/liamca/azure-search-javascript-samples)
