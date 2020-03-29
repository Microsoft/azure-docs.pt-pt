---
title: Declarações de importação usando Nó.js - LUIS
titleSuffix: Azure Cognitive Services
description: Saiba como construir uma app LUIS programáticamente a partir de dados pré-existentes em formato CSV utilizando a API autora luis.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: ef5f6967b7ad9500672d00d93dd8acaca99e5948
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73499467"
---
# <a name="build-a-luis-app-programmatically-using-nodejs"></a>Construa uma app LUIS programáticamente usando node.js

A LUIS fornece uma API programática que faz tudo o que o site da [LUIS](luis-reference-regions.md) faz. Isto pode economizar tempo quando tem dados pré-existentes e seria mais rápido criar uma app LUIS programáticamente do que introduzir informação manualmente. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Inscreva-se no site da [LUIS](luis-reference-regions.md) e encontre [a sua chave de autor](luis-concept-keys.md#authoring-key) em Definições de Conta. Usa esta chave para chamar as APIs de autoria.
* Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
* Este artigo começa com um CSV para os ficheiros de registo de uma empresa hipotética de pedidos de utilizador. Descarregue [aqui.](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/IoT.csv)
* Instale os mais recentes Nós com NPM. Descarregue-o a partir [daqui.](https://nodejs.org/en/download/)
* **[Recomendado]** Visual Studio Code for IntelliSense e depurando, descarregue-o a partir [daqui](https://code.visualstudio.com/) gratuitamente.

Todo o código deste artigo está disponível no [repositório GitHub de compreensão linguística de amostras azure.](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/examples/build-app-programmatically-csv) 

## <a name="map-preexisting-data-to-intents-and-entities"></a>Mapear dados pré-existentes para intenções e entidades
Mesmo que tenha um sistema que não tenha sido criado com o LUIS em mente, se contiver dados textuais que mapeiem para diferentes coisas que os utilizadores querem fazer, poderá ser capaz de elaborar um mapeamento das categorias existentes de entrada de utilizadores para intenções em LUIS. Se conseguir identificar palavras ou frases importantes no que os utilizadores disseram, estas palavras podem mapear as entidades.

Abra [`IoT.csv`](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/IoT.csv) o arquivo. Contém um registo de consultas de utilizadores a um hipotético serviço de domótica, incluindo como foram categorizados, o que o utilizador disse, e algumas colunas com informações úteis retiradas delas. 

![Arquivo CSV de dados pré-existentes](./media/luis-tutorial-node-import-utterances-csv/csv.png) 

Vê-se que a coluna **RequestType** pode ser intenção, e a coluna **Request** mostra uma expressão de exemplo. Os outros campos podem ser entidades se ocorrerem na expressão. Como existem intenções, entidades e declarações de exemplo, você tem os requisitos para uma simples aplicação de amostra.

## <a name="steps-to-generate-a-luis-app-from-non-luis-data"></a>Passos para gerar uma app LUIS a partir de dados não-LUIS
Para gerar uma nova aplicação LUIS a partir do ficheiro CSV:

* Analisar os dados do ficheiro CSV:
    * Converta-se num formato que pode fazer upload para O LUIS utilizando a API de autoria. 
    * A partir dos dados analisados, recolher informações sobre intenções e entidades. 
* Faça chamadas de autoria da API para:
    * Crie a aplicação.
    * Adicione intençãos e entidades que foram recolhidas a partir dos dados analisados. 
    * Uma vez criada a aplicação LUIS, pode adicionar as declarações de exemplo a partir dos dados analisados. 

Pode ver este programa fluir na `index.js` última parte do ficheiro. Copie ou [descarregue](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/index.js) `index.js`este código e guarde-o em .

   [!code-javascript[Node.js code for calling the steps to build a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/index.js)]


## <a name="parse-the-csv"></a>Parse o CSV

As entradas da coluna que contenham as expressões no CSV têm de ser analisadas num formato JSON que o LUIS possa compreender. Este formato JSON `intentName` deve conter um campo que identifique a intenção da expressão. Deve também conter `entityLabels` um campo, que pode estar vazio se não houver entidades na expressão. 

Por exemplo, a entrada para mapas "Acenda as luzes" para este JSON:

```json
        {
            "text": "Turn on the lights",
            "intentName": "TurnOn",
            "entityLabels": [
                {
                    "entityName": "Operation",
                    "startCharIndex": 5,
                    "endCharIndex": 6
                },
                {
                    "entityName": "Device",
                    "startCharIndex": 12,
                    "endCharIndex": 17
                }
            ]
        }
```

Neste exemplo, `intentName` o pedido do utilizador sob a rubrica da coluna **Request** no ficheiro CSV, e as `entityName` outras colunas com informações-chave. Por exemplo, se houver uma entrada para **operação** ou **dispositivo**, e essa cadeia também ocorrer no pedido real, então pode ser rotulado como uma entidade. O código que se segue demonstra este processo de análise. Pode copiá-lo ou [descarregá-lo](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_parse.js) e guardá-lo para `_parse.js`.

   [!code-javascript[Node.js code for parsing a CSV file to extract intents, entities, and labeled utterances](~/samples-luis/examples/build-app-programmatically-csv/_parse.js)]



## <a name="create-the-luis-app"></a>Criar a app LUIS
Uma vez analisados os dados em JSON, adicione-os a uma aplicação LUIS. O seguinte código cria a aplicação LUIS. Copie ou [descarregue-o](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_create.js) e guarde-o para `_create.js`.

   [!code-javascript[Node.js code for creating a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/_create.js)]


## <a name="add-intents"></a>Adicionar intenções
Uma vez que tenha uma aplicação, tem de ter intenção. O seguinte código cria a aplicação LUIS. Copie ou [descarregue-o](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_intents.js) e guarde-o para `_intents.js`.

   [!code-javascript[Node.js code for creating a series of intents](~/samples-luis/examples/build-app-programmatically-csv/_intents.js)]


## <a name="add-entities"></a>Adicionar entidades
O seguinte código adiciona as entidades à app LUIS. Copie ou [descarregue-o](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_entities.js) e guarde-o para `_entities.js`.

   [!code-javascript[Node.js code for creating entities](~/samples-luis/examples/build-app-programmatically-csv/_entities.js)]
   


## <a name="add-utterances"></a>Adicionar expressões
Uma vez definidas as entidades e intenções na app LUIS, pode adicionar as palavras. O código que se segue utiliza a [API Utterances_AddBatch,](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09) que permite adicionar até 100 expressões de cada vez.  Copie ou [descarregue-o](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_upload.js) e guarde-o para `_upload.js`.

   [!code-javascript[Node.js code for adding utterances](~/samples-luis/examples/build-app-programmatically-csv/_upload.js)]


## <a name="run-the-code"></a>Executar o código


### <a name="install-nodejs-dependencies"></a>Instale dependências do Nó.js
Instale as dependências do Nó.js a partir de NPM na linha de terminais/comandos.

```console
> npm install
```

### <a name="change-configuration-settings"></a>Alterar definições de configuração
Para utilizar esta aplicação, é necessário alterar os valores do ficheiro index.js para a sua própria chave de ponto final e fornecer o nome que pretende que a app tenha. Também pode definir a cultura da aplicação ou alterar o número da versão.

Abra o ficheiro index.js e altere estes valores no topo do ficheiro.


```javascript
// Change these values
const LUIS_programmaticKey = "YOUR_AUTHORING_KEY";
const LUIS_appName = "Sample App";
const LUIS_appCulture = "en-us"; 
const LUIS_versionId = "0.1";
```

### <a name="run-the-script"></a>Executar o script
Execute o script a partir de uma linha de terminais/comando com Node.js.

```console
> node index.js
```

ou

```console
> npm start
```

### <a name="application-progress"></a>Progresso da aplicação
Enquanto a aplicação está em execução, a linha de comando mostra progresso. A saída da linha de comando inclui o formato das respostas da LUIS.

```console
> node index.js
intents: ["TurnOn","TurnOff","Dim","Other"]
entities: ["Operation","Device","Room"]
parse done
JSON file should contain utterances. Next step is to create an app with the intents and entities it found.
Called createApp, created app with ID 314b306c-0033-4e09-92ab-94fe5ed158a2
Called addIntents for intent named TurnOn.
Called addIntents for intent named TurnOff.
Called addIntents for intent named Dim.
Called addIntents for intent named Other.
Results of all calls to addIntent = [{"response":"e7eaf224-8c61-44ed-a6b0-2ab4dc56f1d0"},{"response":"a8a17efd-f01c-488d-ad44-a31a818cf7d7"},{"response":"bc7c32fc-14a0-4b72-bad4-d345d807f965"},{"response":"727a8d73-cd3b-4096-bc8d-d7cfba12eb44"}]
called addEntity for entity named Operation.
called addEntity for entity named Device.
called addEntity for entity named Room.
Results of all calls to addEntity= [{"response":"6a7e914f-911d-4c6c-a5bc-377afdce4390"},{"response":"56c35237-593d-47f6-9d01-2912fa488760"},{"response":"f1dd440c-2ce3-4a20-a817-a57273f169f3"}]
retrying add examples...

Results of add utterances = [{"response":[{"value":{"UtteranceText":"turn on the lights","ExampleId":-67649},"hasError":false},{"value":{"UtteranceText":"turn the heat on","ExampleId":-69067},"hasError":false},{"value":{"UtteranceText":"switch on the kitchen fan","ExampleId":-3395901},"hasError":false},{"value":{"UtteranceText":"turn off bedroom lights","ExampleId":-85402},"hasError":false},{"value":{"UtteranceText":"turn off air conditioning","ExampleId":-8991572},"hasError":false},{"value":{"UtteranceText":"kill the lights","ExampleId":-70124},"hasError":false},{"value":{"UtteranceText":"dim the lights","ExampleId":-174358},"hasError":false},{"value":{"UtteranceText":"hi how are you","ExampleId":-143722},"hasError":false},{"value":{"UtteranceText":"answer the phone","ExampleId":-69939},"hasError":false},{"value":{"UtteranceText":"are you there","ExampleId":-149588},"hasError":false},{"value":{"UtteranceText":"help","ExampleId":-81949},"hasError":false},{"value":{"UtteranceText":"testing the circuit","ExampleId":-11548708},"hasError":false}]}]
upload done
```




## <a name="open-the-luis-app"></a>Abra a app LUIS
Assim que o script estiver concluído, pode iniciar sessão no [LUIS](luis-reference-regions.md) e ver a app LUIS que criou no âmbito das **Minhas Apps.** Deve poder ver as declarações que adicionou sob o **TurnOn**, **TurnOff**e **nenhuma** intenção.

![Intenção TurnOn](./media/luis-tutorial-node-import-utterances-csv/imported-utterances-661.png)


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Teste e treine a sua aplicação no site da LUIS](luis-interactive-test.md)

## <a name="additional-resources"></a>Recursos adicionais

Esta aplicação de amostra utiliza as seguintes APIs LUIS:
- [criar app](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)
- [adicionar intenções](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0c)
- [adicionar entidades](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0e) 
- [adicionar expressões](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09)
