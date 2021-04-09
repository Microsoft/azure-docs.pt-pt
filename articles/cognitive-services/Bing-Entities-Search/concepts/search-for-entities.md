---
title: Pesquisa de entidades com a API de Pesquisa de Entidade Bing
titleSuffix: Azure Cognitive Services
description: Utilize a API de Pesquisa de Entidade Bing para extrair e procurar entidades e locais a partir de consultas de pesquisa.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 9dabceda17defb24f2a916cd641f625feb551c6a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "96353294"
---
# <a name="searching-for-entities-with-the-bing-entity-api"></a>Pesquisa de entidades com a Entidade Bing API

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)

## <a name="suggest-search-terms-with-the-bing-autosuggest-api"></a>Sugerir termos de pesquisa com a API Bing Autosuggest

Se disponibilizar uma caixa de pesquisa na qual o utilizador introduz o seu termo de pesquisa, utilize a [API de Sugestão Automática do Bing](../../bing-autosuggest/get-suggested-search-terms.md) para melhorar a experiência. A API devolve cadeias de consulta sugerida com base em termos de pesquisa parcial à medida que o utilizador escreve.

Depois de o utilizador introduzir o seu termo de pesquisa, o URL codifica o termo antes de definir o parâmetro de consulta [q](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query). Por exemplo, se o utilizador introduzir *Marcus Appel*, defina `q` para *Marcus+Appel* ou *Marcus%20Appel*.

Se o termo de pesquisa tiver um erro de ortografia, a resposta da pesquisa inclui um objeto [QueryContext](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#querycontext). O objeto mostra a ortografia original e a ortografia corrigida que o Bing utilizou para a pesquisa.

```json
"queryContext": {
    "originalQuery": "hollo wrld",
    "alteredQuery": "hello world",
    "alterationOverrideQuery": "+hollo wrld",
    "adultIntent": false
}
```

## <a name="the-bing-entity-search-api-response"></a>A resposta da API da Entidade Bing

A resposta da API contém um objeto [SearchResponse.](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#searchresponse) Se o Bing encontrar uma entidade ou local que seja relevante, o objeto inclui o campo `entities`, o campo `places`, ou ambos. Caso contrário, o objeto de resposta não inclui qualquer um dos campos.
> [!NOTE]
> As respostas de Entidades suportam vários mercados, mas a resposta de Locais suporta apenas as localizações de Empresas nos E.U.A. 

O campo `entities` é um objeto [EntityAnswer](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference) que contém uma lista de objetos de [Entidade](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entity) (veja o campo `value`). A lista pode conter uma única entidade dominante, várias entidades de desambiguação, ou dos dois tipos. 

Uma entidade dominante é devolvida quando Bing acredita ser a única entidade que satisfaz o pedido (não há ambiguidade quanto à entidade que satisfaz o pedido). Se várias entidades puderem satisfazer o pedido, a lista contém mais do que uma entidade de desambiguação. Por exemplo, se o pedido utilizar o título genérico do franchise de um filme, a lista provavelmente contém entidades de desambiguação. No entanto, se o pedido especificar um título específico do franchise, a lista provavelmente contém uma única entidade dominante.

As entidades incluem figuras bem conhecidas como cantores, atores, atletas, modelos, etc.; locais e pontos de referência, como Mount Rainier ou Lincoln Memorial; e coisas como uma banana, um cão da raça goldendoodle, um livro ou um filme. O campo [entityPresentationInfo](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entitypresentationinfo) contém sugestões que identificam o tipo da entidade. Por exemplo, se é uma pessoa, filme, animal ou atração turística. Para obter uma lista de tipos possíveis, veja [Tipos de Entidade](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entity-types)

```json
"entityPresentationInfo": {
    "entityScenario": "DominantEntity",
    "entityTypeHints": ["Attraction"],
    "entityTypeDisplayHint": "Mountain"
}, ...
```

O seguinte mostra uma resposta que inclui uma entidade dominante e de desambiguação.

```json
{
    "_type": "SearchResponse",
    "queryContext": {
        "originalQuery": "Mount Rainier"
    },
    "entities": {
        "value": [{
            "contractualRules": [{
                "_type": "ContractualRules/LicenseAttribution",
                "targetPropertyName": "description",
                "mustBeCloseToContent": true,
                "license": {
                    "name": "CC-BY-SA",
                    "url": "https://creativecommons.org/licenses/by-sa/3.0/"
                },
                "licenseNotice": "Text under CC-BY-SA license"
            },
            {
                "_type": "ContractualRules/LinkAttribution",
                "targetPropertyName": "description",
                "mustBeCloseToContent": true,
                "text": "contoso.com",
                "url": "http://contoso.com/mount_rainier"
            },
            {
                "_type": "ContractualRules/MediaAttribution",
                "targetPropertyName": "image",
                "mustBeCloseToContent": true,
                "url": "http://contoso.com/mount-rainier"
            }],
            "webSearchUrl": "https://www.bing.com/search?q=Mount%20Rainier...",
            "name": "Mount Rainier",
            "url": "http://www.northwindtraders.com/",
            "image": {
                "name": "Mount Rainier",
                "thumbnailUrl": "https://www.bing.com/th?id=A4ae343983daa4...",
                "provider": [{
                    "_type": "Organization",
                    "url": "http://contoso.com/mount_rainier"
                }],
                "hostPageUrl": "http://contoso.com/commons/7/72/mount_rain...",
                "width": 110,
                "height": 110
            },
            "description": "Mount Rainier is 14,411 ft tall and the highest mountain...",
            "entityPresentationInfo": {
                "entityScenario": "DominantEntity",
                "entityTypeHints": ["Attraction"]
            },
            "bingId": "38b9431e-cf91-93be-0584-c42a3ecbfdc7"
        },
        {
            "contractualRules": [{
                "_type": "ContractualRules/MediaAttribution",
                "targetPropertyName": "image",
                "mustBeCloseToContent": true,
                "url": "http://contoso.com/mount_rainier_national_park"
            }],
            "webSearchUrl": "https://www.bing.com/search?q=Mount%20Rainier%20National...",
            "name": "Mount Rainier National Park",
            "url": "http://worldwideimporters.com/",
            "image": {
                "name": "Mount Rainier National Park",
                "thumbnailUrl": "https://www.bing.com/th?id=A91bdc5a1b648a695a39...",
                "provider": [{
                    "_type": "Organization",
                    "url": "http://contoso.com/mount_rainier_national_park"
                }],
                "hostPageUrl": "http://contoso.com/en/7/7a...",
                "width": 50,
                "height": 50
            },
            "description": "Mount Rainier National Park is a United States National Park...",
            "entityPresentationInfo": {
                "entityScenario": "DisambiguationItem",
                "entityTypeHints": ["Organization"]
            },
            "bingId": "29d4b681-227a-3924-7bb1-8a54e8666b8c"
        }]
    }
}
```

A entidade inclui um campo `name`, `description` e `image`. Quando apresenta estes campos na sua experiência de utilizador, tem de atribuí-los. O campo `contractualRules` contém uma lista de atribuições que tem de aplicar. A regra contratual identifica o campo ao qual a atribuição se aplica. Para obter informações sobre como aplicar a atribuição, veja [Atribuição](#data-attribution).

```json
"contractualRules": [{
    "_type": "ContractualRules/LicenseAttribution",
    "targetPropertyName": "description",
    "mustBeCloseToContent": true,
    "license": {
        "name": "CC-BY-SA",
        "url": "https://creativecommons.org/licenses/by-sa/3.0/"
    },
    "licenseNotice": "Text under CC-BY-SA license"
},
{
    "_type": "ContractualRules/LinkAttribution",
    "targetPropertyName": "description",
    "mustBeCloseToContent": true,
    "text": "contoso.com",
    "url": "http://contoso.com/wiki/Mount_Rainier"
},
{
    "_type": "ContractualRules/MediaAttribution",
    "targetPropertyName": "image",
    "mustBeCloseToContent": true,
    "url": "http://contoso.com/wiki/Mount_Rainier"
}], ...
```

Quando apresentar as informações da entidade (nome, descrição e imagem), também tem de utilizar o URL do campo `webSearchUrl` para ligar à página de resultados da pesquisa do Bing que contém a entidade.

## <a name="find-places"></a>Encontrar lugares

O `places` campo é um objeto [LocalEntityAnswer](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference) que contém uma lista de objetos [Place](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#place) (consulte os Tipos de [Entidade](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entity-types) para obter mais informações). A lista contém uma ou mais entidades locais que satisfazem o pedido.

Os locais incluem restaurantes, hotéis ou empresas locais. O campo [entityPresentationInfo](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entitypresentationinfo) contém sugestões que identificam o tipo da entidade de local. A lista contém uma lista de sugestões, como Local, EmpresaLocal, Restaurante. Cada sugestão sucessiva na matriz restringe o tipo da entidade. Para obter uma lista de tipos possíveis, veja [Tipos de Entidade](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entity-types)

```json
"entityPresentationInfo": {
    "entityScenario": "ListItem",
    "entityTypeHints": ["Place",
    "LocalBusiness",
    "Restaurant"]
}, ...
```
> [!NOTE]
> As respostas de Entidades suportam vários mercados, mas a resposta de Locais suporta apenas as localizações de Empresas nos E.U.A. 

As consultas de entidade com reconhecimento de local como *restaurante perto de mim* requerem a localização do utilizador para fornecer resultados precisos. Os pedidos devem utilizar sempre os cabeçalhos X-Search-Location e X-MSEdge-ClientIP para especificar a localização do utilizador. Se o Bing concluir que a consulta poderia tirar partido da localização do utilizador, irá definir o campo `askUserForLocation` de [QueryContext](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#querycontext) como **verdadeiro**. 

```json
{
    "_type": "SearchResponse",
    "queryContext": {
        "originalQuery": "Sinful Bakery and Cafe",
        "askUserForLocation": true
    },
    ...
}
```

Um resultado de local inclui o nome, endereço e o número de telefone do local, bem como o URL para o site da entidade. Quando apresentar as informações da entidade, também tem de utilizar o URL do campo `webSearchUrl` para ligar à página de resultados da pesquisa do Bing que contém a entidade.

```json
"places": {
    "value": [{
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Sinful%20Bakery...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "http://libertysdelightfulsinfulbakeryandcafe.com/",
        "entityPresentationInfo": {
            "entityScenario": "ListItem",
            "entityTypeHints": ["Place",
            "LocalBusiness",
            "Restaurant"]
        },
        "address": {
            "addressLocality": "Seattle",
            "addressRegion": "WA",
            "postalCode": "98112",
            "addressCountry": "US",
            "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
    }]
}
```

> [!NOTE]
> O Utilizador ou terceiros em seu nome não poderão utilizar, reter, armazenar, colocar em cache, partilhar nem distribuir quaisquer dados da API de Entidades para fins de testes, desenvolvimento, formação, distribuição ou disponibilização de qualquer serviço ou funcionalidade que não seja da Microsoft.  

## <a name="data-attribution"></a>Atribuição de dados

A API de Entidades do Bing contém informações pertencentes a terceiros. É da sua responsável garantir a respetiva utilização adequada, por exemplo, ao respeitar o cumprimento de qualquer licença Creative Commons na qual a sua experiência de utilizador se baseie.

Se uma resposta ou resultado incluir os campos `contractualRules`, `attributions` ou `provider`, tem de atribuir os dados. Se a resposta não incluir nenhum desses campos, a atribuição não é necessária. Se a resposta incluir o campo `contractualRules` e os campos `attributions` e/ou `provider`, tem de utilizar as regras contratuais para atribuir os dados.

O exemplo seguinte mostra uma entidade que inclui uma regra contratual MediaAttribution e uma imagem que inclui um campo `provider`. A regra MediaAttribution identifica a imagem como o destino da regra, pelo que poderia ignorar o campo `provider` da imagem e utilizar, em alternativa, a regra MediaAttribution para fornecer a atribuição.  

```json
"value": [{
    "contractualRules": [
        ...
        {
            "_type": "ContractualRules/MediaAttribution",
            "targetPropertyName": "image",
            "mustBeCloseToContent": true,
            "url": "http://contoso.com/mount_rainier"
        }
    ],
    ...
    "image": {
        "name": "Mount Rainier",
        "thumbnailUrl": "https://www.bing.com/th?id=A46378861201...",
        "provider": [{
            "_type": "Organization",
            "url": "http://contoso.com/mount_rainier"
        }],
        "hostPageUrl": "http://www.graphicdesigninstitute.com/Uploaded...",
        "width": 110,
        "height": 110
    },
    ...
}]
```

Se uma regra contratual incluir o campo `targetPropertyName`, a regra aplica-se apenas ao campo visado. Caso contrário, a regra aplica-se ao objeto principal que contém o campo `contractualRules`.

No exemplo seguinte, a regra `LinkAttribution` inclui o campo `targetPropertyName`, pelo que a regra aplica-se ao campo `description`. Para regras que se aplicam a campos específicos, tem de incluir uma linha imediatamente após os dados visados que contenha uma hiperligação para o site do fornecedor. Por exemplo, para atribuir a descrição, inclua uma linha imediatamente após o texto de descrição, que contenha uma hiperligação para os dados no site do fornecedor, neste caso, crie uma ligação para contoso.com.

```json
"entities": {
    "value": [{
            ...
            "description": "Marcus Appel is a former American....",
            ...
            "contractualRules": [{
                    "_type": "ContractualRules/LinkAttribution",
                    "targetPropertyName": "description",
                    "mustBeCloseToContent": true,
                    "text": "contoso.com",
                    "url": "http://contoso.com/cr?IG=B8AD73..."
                 },
            ...
  
```

### <a name="license-attribution"></a>Atribuição de licença

Se a lista de regras contratuais incluir uma regra [LicenseAttribution](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#licenseattribution), tem de apresentar o aviso na linha imediatamente após o conteúdo ao qual a licença se aplica. A regra `LicenseAttribution` utiliza o campo `targetPropertyName` para identificar a propriedade à qual a licença se aplica.

O seguinte mostra um exemplo que inclui uma regra `LicenseAttribution`.

![Atribuição de licença](../media/cognitive-services-bing-entities-api/licenseattribution.png)

O aviso de licença que apresentar tem de incluir uma hiperligação para o site que contenha informações sobre a licença. Normalmente, o nome da licença é utilizado como hiperligação. Por exemplo, se o aviso for **Texto ao abrigo da licença CC-BY-SA** e CC-BY-SA for o nome da licença, CC-BY-SA seria utilizado como a hiperligação.

### <a name="link-and-text-attribution"></a>Atribuição de ligação e texto

As regras [LinkAttribution](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#linkattribution) e [TextAttribution](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#textattribution) são normalmente utilizadas para identificar o fornecedor dos dados. O campo `targetPropertyName` identifica o campo ao qual a regra se aplica.

Para atribuir os fornecedores, inclua uma linha imediatamente após o conteúdo ao qual as atribuições se aplicam (por exemplo, o ficheiro visado). A linha deve estar claramente identificada para indicar que os fornecedores são a origem dos dados. Por exemplo, “dados de: contoso.com”. Para as regras `LinkAttribution`, tem de criar uma hiperligação para o site do fornecedor.

O seguinte mostra um exemplo que inclui as regras `LinkAttribution` e `TextAttribution`.

![Atribuição de texto de ligação](../media/cognitive-services-bing-entities-api/linktextattribution.png)

### <a name="media-attribution"></a>Atribuição de suporte de dados

Se a entidade incluir uma imagem e a apresentar, tem de fornecer uma ligação clicável para o site do fornecedor. Se a entidade incluir uma regra [MediaAttribution](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#mediaattribution), utilize o URL da regra para criar a ligação clicável. Caso contrário, utilize o URL incluído no campo `provider` da imagem para criar a ligação clicável.

O seguinte mostra um exemplo que inclui um campo `provider` e as regras contratuais da imagem. Uma vez que o exemplo inclui a regra contratual, ignore o campo `provider` da imagem e aplique a regra `MediaAttribution`.

![Atribuição de suporte de dados](../media/cognitive-services-bing-entities-api/mediaattribution.png)

### <a name="search-or-search-like-experience"></a>Pesquisa ou experiência semelhante a pesquisa

Tal como a API de Pesquisa na Web do Bing, a API de Pesquisa de Entidades do Bing só pode ser utilizada devido a uma consulta ou pesquisa direta de utilizador ou devido a uma ação numa aplicação ou uma experiência que possa ser logicamente interpretada como um pedido de pesquisa de um utilizador. Para efeitos de ilustração, seguem-se alguns exemplos de pesquisa aceitável ou experiências semelhantes a pesquisa.

- O utilizador introduz uma consulta diretamente na caixa de pesquisa da aplicação
- O utilizador seleciona um texto ou imagem específica e pede “mais informações” ou “informações adicionais”
- O utilizador pede um bot de pesquisa sobre um tópico específico
- O utilizador insiste num determinado objeto ou entidade num cenário do tipo de pesquisa visual

Se não tiver a certeza se a sua experiência pode ser considerada uma experiência semelhante a pesquisa, recomenda-se que verifique junto da Microsoft.

## <a name="throttling-requests"></a>Limitar pedidos

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Passos seguintes

* Experimente um [Quickstart](../quickstarts/csharp.md) para começar a procurar entidades com a API de Pesquisa de Entidade Bing.