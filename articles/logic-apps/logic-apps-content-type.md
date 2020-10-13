---
title: Lidar com tipos de conteúdo
description: Saiba como lidar com vários tipos de conteúdo em fluxos de trabalho durante o tempo de conceção e tempo de execução em Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 07/20/2018
ms.openlocfilehash: 04642e69adba82cf33b933829d72c51109ad2ee1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91578194"
---
# <a name="handle-content-types-in-azure-logic-apps"></a>Lidar com tipos de conteúdo em Azure Logic Apps

Vários tipos de conteúdo podem fluir através de uma aplicação lógica, por exemplo, JSON, XML, ficheiros planos e dados binários. Embora as Aplicações Lógicas suportem todos os tipos de conteúdo, alguns têm suporte nativo e não necessitam de casting ou conversão nas suas aplicações lógicas. Outros tipos podem exigir fundição ou conversão, se necessário. Este artigo descreve como as Aplicações Lógicas lidam com tipos de conteúdo e como pode lançar ou converter corretamente estes tipos quando necessário.

Para determinar a forma adequada de manusear tipos de conteúdo, as Aplicações Lógicas baseiam-se no valor do `Content-Type` cabeçalho nas chamadas HTTP, por exemplo:

* [aplicação/json](#application-json) (tipo nativo)
* [texto/planície](#text-plain) (tipo nativo)
* [aplicação/xml e aplicação/octeto-stream](#application-xml-octet-stream)
* [Outros tipos de conteúdo](#other-content-types)

<a name="application-json"></a>

## <a name="applicationjson"></a>application/json

A Logic Apps armazena e trata qualquer pedido com o tipo de conteúdo *de aplicação/json* como um objeto JavaScript Notation (JSON). Por predefinição, pode analisar o conteúdo JSON sem qualquer fundição. Para analisar um pedido que tenha um cabeçalho com o tipo de conteúdo "aplicação/json", pode utilizar uma expressão. Este exemplo devolve o valor `dog` da `animal-type` matriz sem casting: 
 
`@body('myAction')['animal-type'][0]` 
  
  ```json
  {
    "client": {
       "name": "Fido",
       "animal-type": [ "dog", "cat", "rabbit", "snake" ]
    }
  }
  ```

Se estiver a trabalhar com dados JSON que não especifiquem um cabeçalho, pode lançar manualmente esses dados para o JSON utilizando a [função json(),](../logic-apps/workflow-definition-language-functions-reference.md#json)por exemplo: 
  
`@json(triggerBody())['animal-type']`

### <a name="create-tokens-for-json-properties"></a>Criar fichas para propriedades JSON

A Logic Apps fornece a capacidade para gerar fichas fáceis de usar que representam as propriedades em conteúdo JSON para que possa fazer referência e utilizar essas propriedades mais facilmente no fluxo de trabalho da sua aplicação lógica.

* **Pedido de gatilho**

  Quando utilizar este gatilho no Logic App Designer, pode fornecer um esquema JSON que descreve a carga útil que espera receber. 
  O designer analisa o conteúdo do JSON utilizando este esquema e gera fichas fáceis de utilizar que representam as propriedades no seu conteúdo JSON. 
  Em seguida, pode facilmente referenciar e usar essas propriedades em todo o fluxo de trabalho da sua aplicação lógica. 
  
  Se não tiver um esquema, pode gerar o esquema. 
  
  1. No gatilho 'Pedido', **selecione Utilize a carga útil da amostra para gerar esquema**.  
  
  2. Em **Entrar ou colar uma amostra de carga JSON,** fornecer uma carga útil da amostra e, em seguida, escolher **Feito**. Por exemplo: 

     ![Screenshot que mostra a ação "Quando um pedido HTTP é recebido" com uma carga útil JSON de amostra.](./media/logic-apps-content-type/request-trigger.png)

     O esquema gerado aparece agora no seu gatilho.

     ![Fornecer a amostra JSON carga útil](./media/logic-apps-content-type/generated-schema.png)

     Aqui está a definição subjacente para o seu gatilho pedido no editor de visualização de código:

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

  3. No seu pedido, certifique-se de incluir um `Content-Type` cabeçalho e definir o valor do cabeçalho para `application/json` .

* **Ação parse JSON**

  Quando utilizar esta ação no Logic App Designer, pode analisar a saída JSON e gerar fichas fáceis de utilizar que representam as propriedades no seu conteúdo JSON. 
  Em seguida, pode facilmente referenciar e usar essas propriedades em todo o fluxo de trabalho da sua aplicação lógica. Semelhante ao gatilho 'Pedido', pode fornecer ou gerar um esquema JSON que descreve o conteúdo JSON que pretende analisar. 
  Desta forma, pode consumir mais facilmente dados da Azure Service Bus, Azure Cosmos DB, e assim por diante.

  ![Analisar JSON](./media/logic-apps-content-type/parse-json.png)

<a name="text-plain"></a>

## <a name="textplain"></a>texto/planície

Quando a sua aplicação lógica recebe mensagens HTTP que têm o `Content-Type` cabeçalho definido `text/plain` para, a sua aplicação lógica armazena essas mensagens em forma bruta. Se incluir estas mensagens em ações subsequentes sem casting, os pedidos saem com o `Content-Type` cabeçalho definido para `text/plain` . 

Por exemplo, quando estiver a trabalhar com um ficheiro plano, poderá receber um pedido HTTP com o `Content-Type` cabeçalho definido para `text/plain` o tipo de conteúdo:

`Date,Name,Address`</br>
`Oct-1,Frank,123 Ave`

Se enviar este pedido numa ação posterior como organismo para outro pedido, por exemplo, `@body('flatfile')` esse segundo pedido também tem um `Content-Type` cabeçalho definido para `text/plain` . Se estiver a trabalhar com dados que são texto simples mas não especificou um cabeçalho, pode lançar manualmente esses dados para texto utilizando a [função de cadeia()](../logic-apps/workflow-definition-language-functions-reference.md#string) como esta expressão: 

`@string(triggerBody())`

<a name="application-xml-octet-stream"></a>

## <a name="applicationxml-and-applicationoctet-stream"></a>aplicação/xml e aplicação/octeto-stream

A Logic Apps preserva sempre o `Content-Type` pedido ou resposta HTTP recebido. Assim, se a sua aplicação lógica receber conteúdo com `Content-Type` definido para , e você incluir esse conteúdo em uma `application/octet-stream` ação posterior sem casting, o pedido de saída também `Content-Type` está definido para `application/octet-stream` . Desta forma, as Aplicações Lógicas podem garantir que os dados não se perdem enquanto se movem através do fluxo de trabalho. No entanto, o estado de ação, ou entradas e saídas, é armazenado num objeto JSON enquanto o estado se move através do fluxo de trabalho. 

## <a name="converter-functions"></a>Funções de conversor

Para preservar alguns tipos de dados, a Logic Apps converte o conteúdo numa cadeia binária codificada com metadados apropriados que preservam tanto a carga útil como `$content` a , que são automaticamente `$content-type` convertidas. 

Esta lista descreve como as Aplicações Lógicas convertem o conteúdo quando utiliza estas [funções:](../logic-apps/workflow-definition-language-functions-reference.md)

* `json()`: Lança dados para `application/json`
* `xml()`: Lança dados para `application/xml`
* `binary()`: Lança dados para `application/octet-stream`
* `string()`: Lança dados para `text/plain`
* `base64()`: Converte o conteúdo numa cadeia codificada base64
* `base64toString()`: Converte uma cadeia codificada de base64 para `text/plain`
* `base64toBinary()`: Converte uma cadeia codificada de base64 para `application/octet-stream`
* `dataUri()`: Converte uma cadeia para um URI de dados
* `dataUriToBinary()`: Converte um URI de dados a uma cadeia binária
* `dataUriToString()`: Converte um URI de dados a uma cadeia

Por exemplo, se receber um pedido HTTP quando `Content-Type` `application/xml` definido, como este conteúdo:

```html
<?xml version="1.0" encoding="UTF-8" ?>
<CustomerName>Frank</CustomerName>
```

Pode lançar este conteúdo utilizando a `@xml(triggerBody())` expressão com as `xml()` funções e `triggerBody()` funções e, em seguida, utilizar este conteúdo mais tarde. Ou pode usar a `@xpath(xml(triggerBody()), '/CustomerName')` expressão com as `xpath()` `xml()` funções. 

## <a name="other-content-types"></a>Outros tipos de conteúdo

A Logic Apps trabalha e suporta outros tipos de conteúdo, mas pode exigir que obtenha manualmente o corpo da mensagem descodindo a `$content` variável.

Por exemplo, suponha que a sua aplicação lógica seja desencadeada por um pedido com o `application/x-www-url-formencoded` tipo de conteúdo. Para preservar todos os dados, a `$content` variável no corpo de pedido tem uma carga útil codificada como uma cadeia base64:

`CustomerName=Frank&Address=123+Avenue`

Como o pedido não é texto simples ou JSON, o pedido é armazenado na ação da seguinte forma:

```json
"body": {
   "$content-type": "application/x-www-url-formencoded",
   "$content": "AAB1241BACDFA=="
}
```

As Aplicações Lógicas fornecem funções nativas para o tratamento de dados de formulários, por exemplo: 

* [triggerFormDataValue()](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue)
* [triggerFormDataMultiValues()](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues)
* [formaDataValue()](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) 
* [formaDataMultiValues()](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues)

Ou, pode aceder manualmente aos dados utilizando uma expressão como este exemplo:

`@string(body('formdataAction'))` 

Se quiser que o pedido de saída tenha o mesmo `application/x-www-url-formencoded` cabeçalho do tipo de conteúdo, pode adicionar o pedido ao corpo da ação sem qualquer casting utilizando uma expressão como `@body('formdataAction')` . No entanto, este método só funciona quando o corpo é o único parâmetro na `body` entrada. Se tentar utilizar a `@body('formdataAction')` expressão num `application/json` pedido, obtém-se um erro de tempo de execução porque o corpo é enviado codificado.
