---
title: Importar declarações usando node. js-LUIS
titleSuffix: Azure Cognitive Services
description: Saiba como criar um aplicativo LUIS programaticamente de dados preexistentes no formato CSV usando a API de criação do LUIS.
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
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499467"
---
# <a name="build-a-luis-app-programmatically-using-nodejs"></a>Criar um aplicativo LUIS programaticamente usando node. js

O LUIS fornece uma API programática que faz tudo o que o site da [Luis](luis-reference-regions.md) faz. Isso pode poupar tempo quando você tem dados pré-existentes e seria mais rápido criar um aplicativo LUIS programaticamente do que inserindo informações manualmente. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Entre no site do [Luis](luis-reference-regions.md) e localize a [chave de criação](luis-concept-keys.md#authoring-key) nas configurações da conta. Você usa essa chave para chamar as APIs de criação.
* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
* Este artigo começa com um CSV para os arquivos de log de solicitações do usuário de uma empresa hipotética. Baixe-o [aqui](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/IoT.csv).
* Instale o Node. js mais recente com NPM. Baixe-o [aqui](https://nodejs.org/en/download/).
* **[Recomendado]** Visual Studio Code para IntelliSense e depuração, baixe-o [aqui](https://code.visualstudio.com/) gratuitamente.

Todo o código deste artigo está disponível no [repositório do GitHub Azure-samples reconhecimento vocal](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/examples/build-app-programmatically-csv). 

## <a name="map-preexisting-data-to-intents-and-entities"></a>Mapear dados preexistentes para intenções e entidades
Mesmo que você tenha um sistema que não foi criado com LUIS em mente, se ele contiver dados textuais que mapeiam para coisas diferentes que os usuários desejam fazer, você poderá criar um mapeamento das categorias existentes de entrada do usuário para as intenções no LUIS. Se você puder identificar palavras ou frases importantes no que os usuários disseram, essas palavras poderão ser mapeadas para entidades.

Abra o arquivo [`IoT.csv`](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/IoT.csv) . Ele contém um log de consultas de usuário para um serviço de automação de início hipotético, incluindo como elas foram categorizadas, o que o usuário disse e algumas colunas com informações úteis extraídas delas. 

![Arquivo CSV de dados pré-existentes](./media/luis-tutorial-node-import-utterances-csv/csv.png) 

Você verá que a coluna **RequestType** pode ser intenções e a coluna **solicitação** mostra um exemplo de expressão. Os outros campos podem ser entidades se ocorrerem no expressão. Como há intenções, entidades e exemplos de declarações, você tem os requisitos para um aplicativo de exemplo simples.

## <a name="steps-to-generate-a-luis-app-from-non-luis-data"></a>Etapas para gerar um aplicativo LUIS de dados não LUIS
Para gerar um novo aplicativo LUIS a partir do arquivo CSV:

* Analise os dados do arquivo CSV:
    * Converta em um formato que você possa carregar para o LUIS usando a API de criação. 
    * A partir dos dados analisados, reúna informações sobre intenções e entidades. 
* Crie chamadas à API de criação para:
    * Crie o aplicativo.
    * Adicione tentativas e entidades que foram coletadas dos dados analisados. 
    * Depois de criar o aplicativo LUIS, você pode adicionar o declarações de exemplo dos dados analisados. 

Você pode ver esse fluxo de programa na última parte do arquivo de `index.js`. Copie ou [Baixe](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/index.js) esse código e salve-o em `index.js`.

   [!code-javascript[Node.js code for calling the steps to build a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/index.js)]


## <a name="parse-the-csv"></a>Analisar o CSV

As entradas de coluna que contêm o declarações no CSV precisam ser analisadas em um formato JSON que o LUIS possa entender. Esse formato JSON deve conter um campo `intentName` que identifica a intenção do expressão. Ele também deve conter um campo `entityLabels`, que pode ser vazio se não houver nenhuma entidade no expressão. 

Por exemplo, a entrada para "ativar as luzes" é mapeada para esse JSON:

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

Neste exemplo, a `intentName` vem da solicitação do usuário no cabeçalho da coluna de **solicitação** no arquivo CSV, e o `entityName` vem de outras colunas com informações de chave. Por exemplo, se houver uma entrada para **operação** ou **dispositivo**e essa cadeia de caracteres também ocorrer na solicitação real, ela poderá ser rotulada como uma entidade. O código a seguir demonstra esse processo de análise. Você pode copiá-lo ou [baixá](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_parse.js) -lo e salvá-lo no `_parse.js`.

   [!code-javascript[Node.js code for parsing a CSV file to extract intents, entities, and labeled utterances](~/samples-luis/examples/build-app-programmatically-csv/_parse.js)]



## <a name="create-the-luis-app"></a>Criar o aplicativo LUIS
Depois que os dados forem analisados no JSON, adicione-os a um aplicativo LUIS. O código a seguir cria o aplicativo LUIS. Copie ou [Baixe](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_create.js) -o e salve-o em `_create.js`.

   [!code-javascript[Node.js code for creating a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/_create.js)]


## <a name="add-intents"></a>Adicionar intenções
Quando você tiver um aplicativo, precisará fazer isso. O código a seguir cria o aplicativo LUIS. Copie ou [Baixe](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_intents.js) -o e salve-o em `_intents.js`.

   [!code-javascript[Node.js code for creating a series of intents](~/samples-luis/examples/build-app-programmatically-csv/_intents.js)]


## <a name="add-entities"></a>Adicionar entidades
O código a seguir adiciona as entidades ao aplicativo LUIS. Copie ou [Baixe](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_entities.js) -o e salve-o em `_entities.js`.

   [!code-javascript[Node.js code for creating entities](~/samples-luis/examples/build-app-programmatically-csv/_entities.js)]
   


## <a name="add-utterances"></a>Adicionar expressões
Depois que as entidades e as intenções tiverem sido definidas no aplicativo LUIS, você poderá adicionar o declarações. O código a seguir usa a API [Utterances_AddBatch](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09) , que permite que você adicione até 100 declarações de cada vez.  Copie ou [Baixe](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_upload.js) -o e salve-o em `_upload.js`.

   [!code-javascript[Node.js code for adding utterances](~/samples-luis/examples/build-app-programmatically-csv/_upload.js)]


## <a name="run-the-code"></a>Executar o código


### <a name="install-nodejs-dependencies"></a>Instalar dependências do node. js
Instale as dependências do node. js de NPM na linha de comando/terminal.

```console
> npm install
```

### <a name="change-configuration-settings"></a>Alterar definições de configuração
Para usar esse aplicativo, você precisa alterar os valores no arquivo index. js para sua própria chave de ponto de extremidade e fornecer o nome que deseja que o aplicativo tenha. Você também pode definir a cultura do aplicativo ou alterar o número de versão.

Abra o arquivo index. js e altere esses valores na parte superior do arquivo.


```javascript
// Change these values
const LUIS_programmaticKey = "YOUR_AUTHORING_KEY";
const LUIS_appName = "Sample App";
const LUIS_appCulture = "en-us"; 
const LUIS_versionId = "0.1";
```

### <a name="run-the-script"></a>Executar o script
Execute o script de uma linha de comando/terminal com o Node. js.

```console
> node index.js
```

ou

```console
> npm start
```

### <a name="application-progress"></a>Progresso do aplicativo
Enquanto o aplicativo está em execução, a linha de comando mostra o progresso. A saída da linha de comando inclui o formato das respostas de LUIS.

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




## <a name="open-the-luis-app"></a>Abra o aplicativo LUIS
Quando o script for concluído, você poderá entrar no [Luis](luis-reference-regions.md) e ver o aplicativo Luis que você criou em **meus aplicativos**. Você deve ser capaz de ver as declarações que adicionou sob as intenções de ativação **,** **desativação**e **nenhuma** .

![Intenção de ativação](./media/luis-tutorial-node-import-utterances-csv/imported-utterances-661.png)


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Testar e treinar seu aplicativo no site do LUIS](luis-interactive-test.md)

## <a name="additional-resources"></a>Recursos adicionais

Este aplicativo de exemplo usa as seguintes APIs LUIS:
- [criar aplicativo](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)
- [Adicionar tentativas](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0c)
- [adicionar entidades](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0e) 
- [adicionar declarações](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09)
