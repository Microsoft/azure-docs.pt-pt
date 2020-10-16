---
title: Expressões de importação usando Node.js - LUIS
titleSuffix: Azure Cognitive Services
description: Saiba como construir uma aplicação LUIS programáticamente a partir de dados pré-existentes em formato CSV utilizando a API de Autoria LUIS.
services: cognitive-services
manager: nitinme
ms.custom: seodec18, devx-track-js
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 09/05/2019
ms.openlocfilehash: 58eb92f4d0bc3de4671ca2ece14a178a876e4a6b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541051"
---
# <a name="build-a-luis-app-programmatically-using-nodejs"></a>Construa uma aplicação LUIS programáticamente utilizando Node.js

A LUIS disponibiliza uma API programática que faz tudo o que o site [da LUIS](luis-reference-regions.md) faz. Isto pode economizar tempo quando tiver dados pré-existentes e seria mais rápido criar uma aplicação LUIS programáticamente do que introduzindo informações manualmente.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Inscreva-se no site da [LUIS](luis-reference-regions.md) e encontre a sua [chave de autoria](luis-how-to-azure-subscription.md#authoring-key) nas Definições de Conta. Usa esta chave para chamar as APIs de autoria.
* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de começar.
* Este artigo começa com um CSV para os ficheiros de registo de registo de pedidos de utilizador de uma empresa hipotética. Descarregue [aqui.](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/IoT.csv)
* Instale as últimas Node.js com OPM. Descarregue a partir [daqui.](https://nodejs.org/en/download/)
* **[Recomendado]** Código de Estúdio Visual para IntelliSense e depurando, descarregue-o a partir [daqui](https://code.visualstudio.com/) gratuitamente.

Todo o código deste artigo está disponível no [repositório de Idiomas Azure-Samples GitHub](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/examples/build-app-programmatically-csv).

## <a name="map-preexisting-data-to-intents-and-entities"></a>Mapear dados pré-existentes para intenções e entidades
Mesmo que tenha um sistema que não tenha sido criado com o LUIS em mente, se contiver dados textuais que mapeiem para diferentes coisas que os utilizadores querem fazer, poderá ser capaz de criar um mapeamento das categorias existentes de entrada de utilizador para intenções no LUIS. Se conseguir identificar palavras ou frases importantes no que os utilizadores disseram, estas palavras podem mapear para entidades.

Abra o [`IoT.csv`](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/IoT.csv) ficheiro. Contém um registo de consultas de utilizador para um hipotético serviço de domótica, incluindo como foram categorizados, o que o utilizador disse, e algumas colunas com informações úteis retiradas deles.

![Ficheiro CSV de dados pré-existentes](./media/luis-tutorial-node-import-utterances-csv/csv.png)

Vê que a coluna **RequestType** pode ser intenção, e a coluna **Request** mostra uma expressão de exemplo. Os outros campos podem ser entidades se ocorrerem na expressão. Como existem intenções, entidades e palavras de exemplo, você tem os requisitos para uma simples aplicação de amostra.

## <a name="steps-to-generate-a-luis-app-from-non-luis-data"></a>Passos para gerar uma app LUIS a partir de dados não-LUIS
Para gerar uma nova aplicação LUIS a partir do ficheiro CSV:

* Analise os dados do ficheiro CSV:
    * Converta-se num formato que pode fazer o upload para o LUIS utilizando a API de Autoria.
    * A partir dos dados analisados, recolha informação sobre intenções e entidades.
* Faça chamadas de autoria da API para:
    * Crie a aplicação.
    * Adicione intenções e entidades que foram recolhidas a partir dos dados analisados.
    * Uma vez criada a app LUIS, pode adicionar o exemplo dos dados analisados.

Pode ver este fluxo de programa na última parte do `index.js` ficheiro. Copie ou [descarregue](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/index.js) este código e guarde-o em `index.js` .

   [!code-javascript[Node.js code for calling the steps to build a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/index.js)]


## <a name="parse-the-csv"></a>Parse o CSV

As entradas de coluna que contêm as expressões no CSV têm de ser analisadas num formato JSON que o LUIS possa compreender. Este formato JSON deve conter um `intentName` campo que identifique a intenção da expressão. Deve também conter um `entityLabels` campo, que pode estar vazio se não houver entidades na expressão.

Por exemplo, a entrada para mapas "Ligue as luzes" a este JSON:

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

Neste exemplo, o `intentName` vem do pedido do utilizador sob a coluna **Request** que se dirige no ficheiro CSV, e vem `entityName` das outras colunas com informações-chave. Por exemplo, se houver uma entrada para **Operação** ou **Dispositivo**, e essa cadeia também ocorrer no pedido real, então pode ser rotulado como uma entidade. O seguinte código demonstra este processo de análise. Pode copiá-lo ou [descarregá-lo](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_parse.js) e guardá-lo para `_parse.js` .

   [!code-javascript[Node.js code for parsing a CSV file to extract intents, entities, and labeled utterances](~/samples-luis/examples/build-app-programmatically-csv/_parse.js)]



## <a name="create-the-luis-app"></a>Crie a app LUIS
Uma vez analisados os dados no JSON, adicione-os a uma aplicação LUIS. O seguinte código cria a aplicação LUIS. Copie ou [descarregue](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_create.js) e guarde-o em `_create.js` .

   [!code-javascript[Node.js code for creating a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/_create.js)]


## <a name="add-intents"></a>Adicionar intenções
Uma vez que tenha uma aplicação, você precisa intenção. O seguinte código cria a aplicação LUIS. Copie ou [descarregue](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_intents.js) e guarde-o em `_intents.js` .

   [!code-javascript[Node.js code for creating a series of intents](~/samples-luis/examples/build-app-programmatically-csv/_intents.js)]


## <a name="add-entities"></a>Adicionar entidades
O seguinte código adiciona as entidades à app LUIS. Copie ou [descarregue](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_entities.js) e guarde-o em `_entities.js` .

   [!code-javascript[Node.js code for creating entities](~/samples-luis/examples/build-app-programmatically-csv/_entities.js)]



## <a name="add-utterances"></a>Adicionar expressões
Uma vez definidas as entidades e intenções na app LUIS, pode adicionar as declarações. O seguinte código utiliza a [API Utterances_AddBatch,](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09) que permite adicionar até 100 expressões de cada vez.  Copie ou [descarregue](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_upload.js) e guarde-o em `_upload.js` .

   [!code-javascript[Node.js code for adding utterances](~/samples-luis/examples/build-app-programmatically-csv/_upload.js)]


## <a name="run-the-code"></a>Executar o código


### <a name="install-nodejs-dependencies"></a>Instalar dependências Node.js
Instale as dependências de Node.js de NPM na linha terminal/comando.

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
Executar o script a partir de uma linha terminal/comando com Node.js.

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
Assim que o script estiver concluído, pode iniciar sôms no [LUIS](luis-reference-regions.md) e ver a aplicação LUIS que criou ao abrigo **das Minhas Apps.** Você deve ser capaz de ver as expressões que você acrescentou sob o **TurnOn**, **TurnOff**, e **Nenhuma** intenção.

![Intenção turnOn](./media/luis-tutorial-node-import-utterances-csv/imported-utterances-661.png)


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Teste e treine a sua app no site da LUIS](luis-interactive-test.md)

## <a name="additional-resources"></a>Recursos adicionais

Esta aplicação de amostra utiliza as seguintes APIs LUIS:
- [criar app](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)
- [adicionar intenções](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0c)
- [adicionar entidades](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0e)
- [adicionar expressões](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09)
