---
title: Lidar com tipos de conteúdo
description: Saiba como lidar com vários tipos de conteúdo em fluxos de trabalho durante o tempo de design e o tempo de execução em aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 07/20/2018
ms.openlocfilehash: ae0abe288edda2ce01311d8533b1f104409efce0
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666878"
---
# <a name="handle-content-types-in-azure-logic-apps"></a>Manipular tipos de conteúdo em aplicativos lógicos do Azure

Vários tipos de conteúdo podem fluir por meio de um aplicativo lógico, por exemplo, JSON, XML, arquivos simples e dados binários. Embora os aplicativos lógicos ofereçam suporte a todos os tipos de conteúdo, alguns têm suporte nativo e não exigem a conversão ou converter em seus aplicativos lógicos. Outros tipos podem exigir elenco ou conversão, conforme necessário. Este artigo descreve como os aplicativos lógicos tratam tipos de conteúdo e como é possível converter ou converter corretamente esses tipos quando necessário.

Para determinar a maneira apropriada de lidar com tipos de conteúdo, os aplicativos lógicos dependem do valor do cabeçalho de `Content-Type` em chamadas HTTP, por exemplo:

* [Application/JSON](#application-json) (tipo nativo)
* [texto/simples](#text-plain) (tipo nativo)
* [Application/XML e application/octet-stream](#application-xml-octet-stream)
* [Outros tipos de conteúdo](#other-content-types)

<a name="application-json"></a>

## <a name="applicationjson"></a>application/json

Os aplicativos lógicos armazenam e tratam qualquer solicitação com o tipo de conteúdo *Application/JSON* como um objeto JSON (JavaScript Notation). Por padrão, você pode analisar o conteúdo JSON sem qualquer conversão. Para analisar uma solicitação que tem um cabeçalho com o tipo de conteúdo "Application/JSON", você pode usar uma expressão. Este exemplo retorna o valor `dog` da matriz de `animal-type` sem conversão: 
 
`@body('myAction')['animal-type'][0]` 
  
  ```json
  {
    "client": {
       "name": "Fido",
       "animal-type": [ "dog", "cat", "rabbit", "snake" ]
    }
  }
  ```

Se você estiver trabalhando com dados JSON que não especificam um cabeçalho, poderá converter manualmente esses dados para JSON usando a [função JSON ()](../logic-apps/workflow-definition-language-functions-reference.md#json), por exemplo: 
  
`@json(triggerBody())['animal-type']`

### <a name="create-tokens-for-json-properties"></a>Criar tokens para propriedades JSON

Os aplicativos lógicos fornecem a capacidade de gerar tokens amigáveis para o usuário que representam as propriedades no conteúdo JSON para que você possa referenciar e usar essas propriedades com mais facilidade no fluxo de trabalho do aplicativo lógico.

* **Gatilho de solicitação**

  Ao usar esse gatilho no designer de aplicativo lógico, você pode fornecer um esquema JSON que descreve a carga que você espera receber. 
  O designer analisa o conteúdo JSON usando esse esquema e gera tokens amigáveis para o usuário que representam as propriedades no seu conteúdo JSON. 
  Você pode facilmente referenciar e usar essas propriedades em todo o fluxo de trabalho do aplicativo lógico. 
  
  Se você não tiver um esquema, poderá gerar o esquema. 
  
  1. No gatilho de solicitação, selecione **usar conteúdo de exemplo para gerar o esquema**.  
  
  2. Em **Inserir ou colar um exemplo de carga JSON**, forneça uma carga de exemplo e, em seguida, escolha **concluído**. Por exemplo: 

     ![Fornecer conteúdo JSON de exemplo](./media/logic-apps-content-type/request-trigger.png)

     O esquema gerado agora aparece em seu gatilho.

     ![Fornecer conteúdo JSON de exemplo](./media/logic-apps-content-type/generated-schema.png)

     Aqui está a definição subjacente para o seu gatilho de solicitação no editor de exibição de código:

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

  3. Em sua solicitação, certifique-se de incluir um cabeçalho de `Content-Type` e definir o valor do cabeçalho como `application/json`.

* **Analisar ação JSON**

  Ao usar essa ação no designer de aplicativo lógico, você pode analisar a saída JSON e gerar tokens amigáveis para o usuário que representam as propriedades no seu conteúdo JSON. 
  Você pode facilmente referenciar e usar essas propriedades em todo o fluxo de trabalho do aplicativo lógico. Semelhante ao gatilho de solicitação, você pode fornecer ou gerar um esquema JSON que descreve o conteúdo JSON que você deseja analisar. 
  Dessa forma, você pode consumir com mais facilidade os dados do barramento de serviço do Azure, Azure Cosmos DB e assim por diante.

  ![Analisar JSON](./media/logic-apps-content-type/parse-json.png)

<a name="text-plain"></a>

## <a name="textplain"></a>text/plain

Quando seu aplicativo lógico recebe mensagens HTTP que têm o cabeçalho `Content-Type` definido como `text/plain`, seu aplicativo lógico armazena essas mensagens em formato bruto. Se você incluir essas mensagens em ações subsequentes sem conversão, as solicitações passarão com o cabeçalho `Content-Type` definido como `text/plain`. 

Por exemplo, quando você estiver trabalhando com um arquivo simples, poderá receber uma solicitação HTTP com o cabeçalho `Content-Type` definido como `text/plain` tipo de conteúdo:

`Date,Name,Address`</br>
`Oct-1,Frank,123 Ave`

Se você enviar essa solicitação em uma ação posterior como o corpo de outra solicitação, por exemplo, `@body('flatfile')`, essa segunda solicitação também terá um cabeçalho de `Content-Type` definido como `text/plain`. Se você estiver trabalhando com dados com texto sem formatação, mas não tiver especificado um cabeçalho, poderá converter manualmente esses dados em texto usando a [função String ()](../logic-apps/workflow-definition-language-functions-reference.md#string) , como esta expressão: 

`@string(triggerBody())`

<a name="application-xml-octet-stream"></a>

## <a name="applicationxml-and-applicationoctet-stream"></a>Application/XML e application/octet-stream

Os aplicativos lógicos sempre preservam o `Content-Type` em uma solicitação ou resposta HTTP recebida. Portanto, se o aplicativo lógico receber conteúdo com `Content-Type` definido como `application/octet-stream`e você incluir esse conteúdo em uma ação posterior sem conversão, a solicitação de saída também terá `Content-Type` definido como `application/octet-stream`. Dessa forma, os aplicativos lógicos podem garantir que os dados não sejam perdidos durante a movimentação do fluxo de trabalho. No entanto, o estado de ação, ou entradas e saídas, é armazenado em um objeto JSON enquanto o estado se move pelo fluxo de trabalho. 

## <a name="converter-functions"></a>Funções de conversor

Para preservar alguns tipos de dados, os aplicativos lógicos convertem o conteúdo em uma cadeia de caracteres binária codificada em base64 com metadados apropriados que preservam a carga de `$content` e a `$content-type`, que são automaticamente convertidas. 

Esta lista descreve como os aplicativos lógicos convertem o conteúdo quando você usa essas [funções](../logic-apps/workflow-definition-language-functions-reference.md):

* `json()`: converte dados em `application/json`
* `xml()`: converte dados em `application/xml`
* `binary()`: converte dados em `application/octet-stream`
* `string()`: converte dados em `text/plain`
* `base64()`: converte o conteúdo em uma cadeia de caracteres codificada em base64
* `base64toString()`: converte uma cadeia de caracteres codificada em base64 em `text/plain`
* `base64toBinary()`: converte uma cadeia de caracteres codificada em base64 em `application/octet-stream`
* `dataUri()`: converte uma cadeia de caracteres em um URI de dados
* `dataUriToBinary()`: converte um URI de dados em uma cadeia de caracteres binária
* `dataUriToString()`: converte um URI de dados em uma cadeia de caracteres

Por exemplo, se você receber uma solicitação HTTP onde `Content-Type` definido como `application/xml`, como este conteúdo:

```html
<?xml version="1.0" encoding="UTF-8" ?>
<CustomerName>Frank</CustomerName>
```

Você pode converter esse conteúdo usando a expressão `@xml(triggerBody())` com as funções `xml()` e `triggerBody()` e, em seguida, usar esse conteúdo posteriormente. Ou, você pode usar a expressão `@xpath(xml(triggerBody()), '/CustomerName')` com as funções `xpath()` e `xml()`. 

## <a name="other-content-types"></a>Outros tipos de conteúdo

Os aplicativos lógicos funcionam com o e dão suporte a outros tipos de conteúdo, mas podem exigir que você obtenha manualmente o corpo da mensagem decodificando a variável `$content`.

Por exemplo, suponha que seu aplicativo lógico é disparado por uma solicitação com o tipo de conteúdo `application/x-www-url-formencoded`. Para preservar todos os dados, a variável `$content` no corpo da solicitação tem uma carga que é codificada como uma cadeia de caracteres Base64:

`CustomerName=Frank&Address=123+Avenue`

Como a solicitação não é texto sem formatação ou JSON, a solicitação é armazenada na ação da seguinte maneira:

```json
"body": {
   "$content-type": "application/x-www-url-formencoded",
   "$content": "AAB1241BACDFA=="
}
```

Os aplicativos lógicos fornecem funções nativas para manipular dados de formulário, por exemplo: 

* [triggerFormDataValue()](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue)
* [triggerFormDataMultiValues()](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues)
* [formDataValue()](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) 
* [formDataMultiValues()](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues)

Ou, você pode acessar manualmente os dados usando uma expressão como este exemplo:

`@string(body('formdataAction'))` 

Se desejar que a solicitação de saída tenha o mesmo cabeçalho de tipo de conteúdo `application/x-www-url-formencoded`, você poderá adicionar a solicitação ao corpo da ação sem qualquer conversão usando uma expressão como `@body('formdataAction')`. No entanto, esse método só funciona quando o corpo é o único parâmetro na entrada `body`. Se você tentar usar a expressão de `@body('formdataAction')` em uma solicitação de `application/json`, obterá um erro de tempo de execução porque o corpo será enviado codificado.
