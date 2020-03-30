---
title: Lidar com tipos de conteúdo
description: Saiba como lidar com vários tipos de conteúdo em fluxos de trabalho durante o tempo de design e tempo de execução em Aplicações Lógicas Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 07/20/2018
ms.openlocfilehash: ae0abe288edda2ce01311d8533b1f104409efce0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75666878"
---
# <a name="handle-content-types-in-azure-logic-apps"></a>Lidar com tipos de conteúdo em Aplicações Lógicas Azure

Vários tipos de conteúdo podem fluir através de uma aplicação lógica, por exemplo, JSON, XML, ficheiros planos e dados binários. Enquanto as Aplicações Lógicas suportam todos os tipos de conteúdo, alguns têm suporte nativo e não requerem casting ou conversão nas suas aplicações lógicas. Outros tipos podem exigir fundição ou conversão, se necessário. Este artigo descreve como as Aplicações Lógicas lidam com tipos de conteúdo e como pode saqueá-los corretamente ou converter estes tipos quando necessário.

Para determinar a forma adequada de lidar com `Content-Type` os tipos de conteúdo, as Aplicações Lógicas baseiam-se no valor do cabeçalho nas chamadas HTTP, por exemplo:

* [aplicação/json](#application-json) (tipo nativo)
* [texto/planície](#text-plain) (tipo nativo)
* [aplicação/xml e aplicação/octeto-stream](#application-xml-octet-stream)
* [Outros tipos de conteúdo](#other-content-types)

<a name="application-json"></a>

## <a name="applicationjson"></a>application/json

As Aplicações Lógicas armazenam e tratam qualquer pedido com o tipo de conteúdo *aplicação/json* como um objeto de Notação JavaScript (JSON). Por predefinição, pode analisar o conteúdo da JSON sem qualquer casting. Para analisar um pedido que tenha um cabeçalho com o tipo de conteúdo "application/json", pode utilizar uma expressão. Este exemplo devolve `dog` o `animal-type` valor da matriz sem casting: 
 
`@body('myAction')['animal-type'][0]` 
  
  ```json
  {
    "client": {
       "name": "Fido",
       "animal-type": [ "dog", "cat", "rabbit", "snake" ]
    }
  }
  ```

Se estiver a trabalhar com dados da JSON que não especifiquem um cabeçalho, pode lançar manualmente esses dados à JSON utilizando a [função json() ()](../logic-apps/workflow-definition-language-functions-reference.md#json)por exemplo: 
  
`@json(triggerBody())['animal-type']`

### <a name="create-tokens-for-json-properties"></a>Criar fichas para propriedades JSON

As Aplicações Lógicas fornecem a capacidade de gerar fichas fáceis de utilizar que representam as propriedades em conteúdos JSON para que possa fazer referência e utilizar essas propriedades mais facilmente no fluxo de trabalho da sua aplicação lógica.

* **Gatilho de pedido**

  Quando utilizar este gatilho no Logic App Designer, pode fornecer um esquema JSON que descreve a carga útil que espera receber. 
  O designer analisa o conteúdo da JSON utilizando este esquema e gera tokens fáceis de utilizar que representam as propriedades no seu conteúdo JSON. 
  Em seguida, pode facilmente referir e usar essas propriedades em todo o fluxo de trabalho da sua aplicação lógica. 
  
  Se não tiver um esquema, pode gerar o esquema. 
  
  1. No gatilho Solicitar, selecione Utilize a carga útil da **amostra para gerar esquema**.  
  
  2. Em **'Entrar ou colar uma amostra De carga útil JSON',** fornecer uma carga útil da amostra e, em seguida, escolher **Feito**. Por exemplo: 

     ![Fornecer carga útil JSON da amostra](./media/logic-apps-content-type/request-trigger.png)

     O esquema gerado aparece agora no seu gatilho.

     ![Fornecer carga útil JSON da amostra](./media/logic-apps-content-type/generated-schema.png)

     Aqui está a definição subjacente para o seu gatilho de pedido no editor de visualização de código:

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

  3. No seu pedido, certifique-se de que inclui `Content-Type` um `application/json`cabeçalho e detetete o valor do cabeçalho para .

* **Ação Parse JSON**

  Quando utilizar esta ação no Logic App Designer, pode analisar a saída da JSON e gerar tokens fáceis de utilizar que representam as propriedades no seu conteúdo JSON. 
  Em seguida, pode facilmente referir e usar essas propriedades em todo o fluxo de trabalho da sua aplicação lógica. Semelhante ao gatilho do Pedido, pode fornecer ou gerar um esquema JSON que descreve o conteúdo JSON que pretende analisar. 
  Dessa forma, pode consumir mais facilmente dados do Azure Service Bus, Azure Cosmos DB, e assim por diante.

  ![Analisar JSON](./media/logic-apps-content-type/parse-json.png)

<a name="text-plain"></a>

## <a name="textplain"></a>texto/planície

Quando a sua aplicação lógica `Content-Type` recebe mensagens HTTP que têm o cabeçalho definido `text/plain`para , a sua aplicação lógica armazena essas mensagens em forma toda a forma bruta. Se incluir estas mensagens em ações subsequentes sem casting, os pedidos saem com o `Content-Type` cabeçalho definido para `text/plain`. 

Por exemplo, quando estiver a trabalhar com um ficheiro plano, `Content-Type` pode `text/plain` obter um pedido http com o conjunto de cabeçalho para o tipo de conteúdo:

`Date,Name,Address`</br>
`Oct-1,Frank,123 Ave`

Se, em seguida, enviar este pedido em uma ação posterior `@body('flatfile')`como o corpo `Content-Type` para outro pedido, `text/plain`por exemplo, esse segundo pedido também tem um cabeçalho que está definido para . Se estiver a trabalhar com dados que são texto simples mas não especificaum cabeçalho, pode lançar manualmente esses dados para texto utilizando a [função de cadeia()](../logic-apps/workflow-definition-language-functions-reference.md#string) como esta expressão: 

`@string(triggerBody())`

<a name="application-xml-octet-stream"></a>

## <a name="applicationxml-and-applicationoctet-stream"></a>aplicação/xml e aplicação/octeto-stream

As Aplicações Lógicas preservam sempre o `Content-Type` pedido ou resposta recebido em HTTP. Assim, se a sua aplicação `Content-Type` `application/octet-stream`lógica receber conteúdo definido para , e você incluir `Content-Type` esse `application/octet-stream`conteúdo em uma ação posterior sem casting, o pedido de saída também tem definido para . Desta forma, as Aplicações Lógicas podem garantir que os dados não se perdem ao mover-se através do fluxo de trabalho. No entanto, o estado de ação, ou inputs e saídas, é armazenado num objeto JSON enquanto o Estado se move através do fluxo de trabalho. 

## <a name="converter-functions"></a>Funções de conversor

Para preservar alguns tipos de dados, as Aplicações Lógicas convertem o conteúdo numa cadeia `$content` binária `$content-type`de base64 codificada com metadados apropriados que preservam tanto a carga útil como a , que são automaticamente convertidas. 

Esta lista descreve como as Aplicações Lógicas convertem conteúdo quando utiliza estas [funções:](../logic-apps/workflow-definition-language-functions-reference.md)

* `json()`: Moldes dados para`application/json`
* `xml()`: Moldes dados para`application/xml`
* `binary()`: Moldes dados para`application/octet-stream`
* `string()`: Moldes dados para`text/plain`
* `base64()`: Converte o conteúdo numa cadeia codificada base64
* `base64toString()`: Converte uma cadeia codificada com base 64 para`text/plain`
* `base64toBinary()`: Converte uma cadeia codificada com base 64 para`application/octet-stream`
* `dataUri()`: Converte uma cadeia para um URI de dados
* `dataUriToBinary()`: Converte um URI de dados para uma corda binária
* `dataUriToString()`: Converte um URI de dados numa corda

Por exemplo, se receber um `Content-Type` pedido `application/xml`http onde definido para, como este conteúdo:

```html
<?xml version="1.0" encoding="UTF-8" ?>
<CustomerName>Frank</CustomerName>
```

Pode lançar este conteúdo `@xml(triggerBody())` utilizando a `xml()` `triggerBody()` expressão com as funções e, em seguida, utilizar este conteúdo mais tarde. Ou pode usar `@xpath(xml(triggerBody()), '/CustomerName')` a expressão `xpath()` `xml()` com as e funções. 

## <a name="other-content-types"></a>Outros tipos de conteúdo

As Aplicações Lógicas funcionam e suportam outros tipos de conteúdo, mas `$content` podem exigir que obtenha manualmente o corpo da mensagem descodificando a variável.

Por exemplo, suponha que a sua `application/x-www-url-formencoded` aplicação lógica seja desencadeada por um pedido com o tipo de conteúdo. Para preservar todos os `$content` dados, a variável no organismo de pedido tem uma carga útil codificada como uma cadeia base64:

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
* [formulárioDataValue()](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) 
* [formDataMultiValues()](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues)

Ou, pode aceder manualmente aos dados utilizando uma expressão como este exemplo:

`@string(body('formdataAction'))` 

Se quiser que o pedido `application/x-www-url-formencoded` de saída tenha o mesmo cabeçalho do tipo de conteúdo, pode `@body('formdataAction')`adicionar o pedido ao corpo da ação sem qualquer casting utilizando uma expressão como . No entanto, este método só funciona quando `body` o corpo é o único parâmetro na entrada. Se tentar usar `@body('formdataAction')` a expressão `application/json` num pedido, obtém um erro de tempo de execução porque o corpo é enviado codificado.
