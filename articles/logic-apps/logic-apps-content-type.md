---
title: Lidar com tipos de conteúdo - Azure Logic Apps | Documentos da Microsoft
description: Saiba como os identificadores de aplicações lógicas tipos de conteúdo em tempo de design e tempo de execução
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: article
ms.date: 07/20/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 2a9318317d5a01136a42b4fb6d580bafaf53ec4e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60685772"
---
# <a name="handle-content-types-in-azure-logic-apps"></a>Lidar com tipos de conteúdo no Azure Logic Apps

Vários tipos de conteúdos podem fluir por meio de uma aplicação lógica, por exemplo, JSON, XML, arquivos simples e dados binários. Embora o Logic Apps suporta todos os tipos de conteúdo, alguns tem suporte nativo e não requerem conversão ou conversão nas suas aplicações lógicas. Outros tipos poderão exigir uma conversão ou conversão conforme necessário. Este artigo descreve como as aplicações lógicas processam os tipos de conteúdo e como pode corretamente converter ou converter esses tipos quando necessário.

Para determinar a forma adequada para lidar com tipos de conteúdo, o Logic Apps se baseia no `Content-Type` chama o valor de cabeçalho HTTP, por exemplo:

* [aplicação/json](#application-json) (tipo nativo)
* [text/plain](#text-plain) (tipo nativo)
* [aplicação/xml e application/octet-stream](#application-xml-octet-stream)
* [Outros tipos de conteúdo](#other-content-types)

<a name="application-json"></a>

## <a name="applicationjson"></a>application/json

O Logic Apps armazena e manipula a qualquer pedido com o *application/json* tipo como um objeto JavaScript Notation (JSON) de conteúdo. Por predefinição, pode analisar o conteúdo JSON, sem qualquer conversão. Para analisar um pedido com um cabeçalho com o tipo de conteúdo "application/json", pode usar uma expressão. Este exemplo retorna o valor `dog` partir o `animal-type` matriz sem a conversão: 
 
`@body('myAction')['animal-type'][0]` 
  
  ```json
  {
    "client": {
       "name": "Fido",
       "animal-type": [ "dog", "cat", "rabbit", "snake" ]
    }
  }
  ```

Se estiver trabalhando com dados JSON que não especifica um cabeçalho, poderá lançá manualmente esses dados em JSON utilizando o [json() função](../logic-apps/workflow-definition-language-functions-reference.md#json), por exemplo: 
  
`@json(triggerBody())['animal-type']`

### <a name="create-tokens-for-json-properties"></a>Criar tokens para as propriedades JSON

O Logic Apps fornece a capacidade para gerar tokens amigáveis que representam as propriedades no conteúdo JSON, para que possa referenciar e usar essas propriedades mais facilmente no fluxo de trabalho da sua aplicação lógica.

* **Acionador de pedido**

  Quando utiliza este acionador no Estruturador da aplicação lógica, pode fornecer um esquema JSON que descreve o payload que espera de receber. 
  O designer analisa o conteúdo JSON ao utilizar esse esquema e gera tokens amigáveis que representam as propriedades no seu conteúdo JSON. 
  Pode, em seguida, facilmente referenciar e usar essas propriedades durante todo o fluxo de trabalho da sua aplicação lógica. 
  
  Se não tiver um esquema, pode gerar o esquema. 
  
  1. No acionador do pedido, selecione **utilizar payload de amostra para gerar esquema**.  
  
  2. Sob **introduza ou cole um payload JSON de exemplo**, forneça um payload de exemplo e, em seguida, escolha **feito**. Por exemplo: 

     ![Fornecer o payload JSON de exemplo](./media/logic-apps-content-type/request-trigger.png)

     O esquema gerado é apresentada no seu acionador.

     ![Fornecer o payload JSON de exemplo](./media/logic-apps-content-type/generated-schema.png)

     Segue-se a definição subjacente para o acionador de pedido no editor de vista de código:

     ```json
     "triggers": { 
        "manual": {
           "type": "Request",
           "kind": "Http",
           "inputs": { 
              "schema": {
                 "type": "object",
                 "properties": {
                    "client": {
                       "type": "object",
                       "properties": {
                          "animal-type": {
                             "type": "array",
                             "items": {
                                "type": "string"
                             },
                          },
                          "name": {
                             "type": "string"
                          }
                       }
                    }
                 }
              }
           }
        }
     }
     ```

  3. No seu pedido, certifique-se de incluir um `Content-Type` cabeçalho e defina o valor do cabeçalho para `application/json`.

* **Analisar a ação de JSON**

  Quando utiliza esta ação no Estruturador da aplicação lógica, pode analisar a saída JSON e gerar tokens amigáveis que representam as propriedades no seu conteúdo JSON. 
  Pode, em seguida, facilmente referenciar e usar essas propriedades durante todo o fluxo de trabalho da sua aplicação lógica. Assim como o acionador de pedido, pode fornecer ou gerar um esquema JSON que descreve o conteúdo JSON que pretende analisar. 
  Dessa forma, mais facilmente podem consumir dados do Azure Service Bus do Azure Cosmos DB e assim por diante.

  ![Analisar JSON](./media/logic-apps-content-type/parse-json.png)

<a name="text-plain"></a>

## <a name="textplain"></a>text/plain

Quando a aplicação lógica recebe as mensagens HTTP com o `Content-Type` cabeçalho definido como `text/plain`, a aplicação lógica armazena essas mensagens no formato não processado. Se incluir essas mensagens em ações subsequentes sem transmissão, pedidos sair com o `Content-Type` cabeçalho definido como `text/plain`. 

Por exemplo, quando está trabalhando com um arquivo simples, poderá receber um pedido HTTP com o `Content-Type` cabeçalho definido como `text/plain` tipo de conteúdo:

`Date,Name,Address`</br>
`Oct-1,Frank,123 Ave`

Se, em seguida, envia esta solicitação numa ação posterior como o corpo de pedido de outro, por exemplo, `@body('flatfile')`, essa segunda solicitação também tem uma `Content-Type` cabeçalho que está definido como `text/plain`. Se estiver trabalhando com dados que é o texto simples, mas não especificou um cabeçalho, poderá lançá manualmente esses dados para o texto ao utilizar o [String () função](../logic-apps/workflow-definition-language-functions-reference.md#string) como esta expressão: 

`@string(triggerBody())`

<a name="application-xml-octet-stream"></a>

## <a name="applicationxml-and-applicationoctet-stream"></a>aplicação/xml e application/octet-stream

O Logic Apps preserva sempre o `Content-Type` no pedido HTTP ou numa resposta recebida. Então, se a sua aplicação lógica recebe o conteúdo com `Content-Type` definido como `application/octet-stream`, e incluir que conteúdo numa ação posterior sem transmissão, o pedido de saída também tem `Content-Type` definido como `application/octet-stream`. Dessa forma, Logic Apps pode garantir que dados não é perdidos ao mover o fluxo de trabalho. No entanto, o estado da ação, ou entradas e saídas, são armazenados num objeto JSON, enquanto o estado passa pelo fluxo de trabalho. 

## <a name="converter-functions"></a>Funções de conversor

Para preservar alguns tipos de dados, o Logic Apps converte o conteúdo numa cadeia com codificação base64 binário com metadados apropriado que preserva a ambos os `$content` payload e o `$content-type`, que são automaticamente convertido. 

Esta lista descreve como o Logic Apps converte conteúdo quando usar esses [funções](../logic-apps/workflow-definition-language-functions-reference.md):

* `json()`: Conversões de dados para `application/json`
* `xml()`: Conversões de dados para `application/xml`
* `binary()`: Conversões de dados para `application/octet-stream`
* `string()`: Conversões de dados para `text/plain`
* `base64()`: Converte o conteúdo numa cadeia de base64
* `base64toString()`: Converte uma cadeia codificada em base64 `text/plain`
* `base64toBinary()`: Converte uma cadeia codificada em base64 `application/octet-stream`
* `encodeDataUri()`: Codifica uma cadeia de caracteres como uma matriz de bytes de URI de dados
* `decodeDataUri()`: Descodifica um `dataUri` numa matriz de bytes

Por exemplo, se receber um pedido HTTP onde `Content-Type` definido como `application/xml`, por exemplo, este conteúdo:

```html
<?xml version="1.0" encoding="UTF-8" ?>
<CustomerName>Frank</CustomerName>
```

Pode converter este conteúdo utilizando o `@xml(triggerBody())` expressão com o `xml()` e `triggerBody()` funciona e, em seguida, utilizar esse conteúdo mais tarde. Em alternativa, pode utilizar o `@xpath(xml(triggerBody()), '/CustomerName')` expressão com o `xpath()` e `xml()` funções. 

## <a name="other-content-types"></a>Outros tipos de conteúdo

O Logic Apps funciona com e suporta outros tipos de conteúdo, mas poderão necessitar de obter manualmente o corpo da mensagem ao decodificar o `$content` variável.

Por exemplo, suponha que a aplicação lógica é acionada por um pedido com o `application/x-www-url-formencoded` tipo de conteúdo. Para preservar a todos os dados, o `$content` variável no corpo do pedido tem uma carga que é codificada como uma cadeia base64:

`CustomerName=Frank&Address=123+Avenue`

Uma vez que o pedido não é texto sem formatação ou JSON, o pedido é armazenado na ação da seguinte forma:

```json
"body": {
   "$content-type": "application/x-www-url-formencoded",
   "$content": "AAB1241BACDFA=="
}
```

O Logic Apps fornece funções nativas para manipulação de dados do formulário, por exemplo: 

* [triggerFormDataValue()](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue)
* [triggerFormDataMultiValues()](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues)
* [formDataValue()](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) 
* [formDataMultiValues()](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues)

Em alternativa, pode aceder manualmente os dados utilizando uma expressão, como neste exemplo:

`@string(body('formdataAction'))` 

Se quisesse que o pedido de saída para ter o mesmo `application/x-www-url-formencoded` cabeçalho de tipo de conteúdo, pode adicionar o pedido para o corpo da ação sem qualquer conversão utilizando uma expressão como `@body('formdataAction')`. No entanto, este método só funciona quando o corpo é o único parâmetro no `body` entrada. Se tentar usar a `@body('formdataAction')` expressão numa `application/json` solicitar, obter um erro de tempo de execução, uma vez que o corpo é enviado codificado.
