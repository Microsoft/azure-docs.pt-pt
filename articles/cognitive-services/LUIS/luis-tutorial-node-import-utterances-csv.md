---
title: Importar expressões com node. js
titleSuffix: Azure
description: Saiba como criar uma aplicação LUIS programaticamente a partir dos dados pré-existentes no formato CSV com a API de criação de LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/30/2019
ms.author: diberry
ms.openlocfilehash: 314d121e8964ba1cdbb457260826d85bf8505fbc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60494902"
---
# <a name="build-a-luis-app-programmatically-using-nodejs"></a>Criar uma aplicação do LUIS por meio de programação com node. js

LUIS fornece uma API programática que faz tudo isso a [LUIS](luis-reference-regions.md) faz do Web site. Isso pode poupar tempo quando tiver que os dados pré-existentes e seria mais rápido para criar uma aplicação de LUIS por meio de programação que ao introduzir as informações manualmente. 

## <a name="prerequisites"></a>Pré-requisitos

* Iniciar sessão para o [LUIS](luis-reference-regions.md) Web site e localizar seu [chave de criação](luis-concept-keys.md#authoring-key) nas definições de conta. Esta chave é utilizada para chamar as APIs de criação.
* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
* Este tutorial começa com um CSV para ficheiros de registo de uma empresa hipotética de pedidos de utilizador. Baixá-lo [aqui](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/IoT.csv).
* Instale o node. js mais recente com NPM. Baixe-o a partir [aqui](https://nodejs.org/en/download/).
* **[Recomendável]**  Visual Studio Code para IntelliSense e depuração, transferi-lo no [aqui](https://code.visualstudio.com/) gratuitamente.

## <a name="map-preexisting-data-to-intents-and-entities"></a>Mapear dados preexistentes para intenções e entidades
Mesmo que tenha um sistema que não foi criado com os LUIS em mente, se este contiver dados textuais que é mapeado para ações diferentes que os utilizadores que pretende fazer, poderá conseguir propor um mapeamento de entre as categorias existentes de entrada do usuário para objetivos em LUIS. Se for possível identificar importantes palavras ou frases em que os utilizadores dito, essas palavras pode ser mapeado para entidades.

Abra o ficheiro `IoT.csv`. Ele contém um registo de consultas de utilizador para um serviço de automação residencial hipotética, incluindo como foram categorizadas, o que o usuário diz e algumas colunas com informações úteis retiradas-los. 

![Ficheiro CSV de dados já existentes](./media/luis-tutorial-node-import-utterances-csv/csv.png) 

Verá que o **RequestType** coluna pode ser intenções e o **pedir** coluna mostra uma expressão de exemplo. Os outros campos poderiam ser entidades, se a expressão que eles ocorrem. Como há intenções, entidades e expressões de exemplo, tem os requisitos para uma aplicação de exemplo simples.

## <a name="steps-to-generate-a-luis-app-from-non-luis-data"></a>Passos para gerar uma aplicação do LUIS dos dados não LUIS
Para gerar uma nova aplicação LUIS a partir do ficheiro de origem, primeiro analisar os dados de ficheiro. CSV e converter esses dados para um formato que pode carregar para o LUIS com a API de criação. Dos dados analisados, recolha informações sobre que intenções e entidades estão lá. Em seguida, efetuar chamadas de API para criar a aplicação e adicionar intenções e entidades que foram reunidas a partir de dados analisados. Depois de criar a aplicação do LUIS, pode adicionar as expressões de exemplo de dados analisados. Pode ver este fluxo na última parte o código a seguir. Cópia ou [baixe](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/index.js) esse código e guarde-o na `index.js`.

   [!code-javascript[Node.js code for calling the steps to build a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/index.js)]


## <a name="parse-the-csv"></a>Analisar o CSV

As entradas de coluna que contém as expressões no CSV tem de ser analisado em formato JSON que LUIS pode entender. Este formato JSON tem de conter um `intentName` campo que identifica a intenção da expressão. Também tem de conter um `entityLabels` campo, o que pode estar vazio se não existirem não existem entidades na expressão. 

Por exemplo, a entrada para "Ligar as luzes" é mapeado para este JSON:

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

Neste exemplo, o `intentName` é fornecido no pedido de utilizador sob o **pedido** cabeçalho de coluna no ficheiro CSV e o `entityName` é proveniente de outras colunas com informações da chave. Por exemplo, se houver uma entrada para o **operação** ou **dispositivo**e que cadeia de caracteres também ocorre no pedido real, em seguida, ele pode ser identificado como uma entidade. O código a seguir demonstra este processo de análise. Pode copiar ou [baixe](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_parse.js) -lo e guardá-lo para `_parse.js`.

   [!code-javascript[Node.js code for parsing a CSV file to extract intents, entities, and labeled utterances](~/samples-luis/examples/build-app-programmatically-csv/_parse.js)]



## <a name="create-the-luis-app"></a>Criar a aplicação LUIS
Depois dos dados tenham sido analisados em JSON, adicione-o para uma aplicação do LUIS. O código a seguir cria a aplicação do LUIS. Cópia ou [baixe](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_create.js) e guarde-o em `_create.js`.

   [!code-javascript[Node.js code for creating a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/_create.js)]


## <a name="add-intents"></a>Adicionar intenções
Assim que tiver uma aplicação, terá de objetivos para o mesmo. O código a seguir cria a aplicação do LUIS. Cópia ou [baixe](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_intents.js) e guarde-o em `_intents.js`.

   [!code-javascript[Node.js code for creating a series of intents](~/samples-luis/examples/build-app-programmatically-csv/_intents.js)]


## <a name="add-entities"></a>Adicionar entidades
O código a seguir adiciona as entidades para a aplicação do LUIS. Cópia ou [baixe](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_entities.js) e guarde-o em `_entities.js`.

   [!code-javascript[Node.js code for creating entities](~/samples-luis/examples/build-app-programmatically-csv/_entities.js)]
   


## <a name="add-utterances"></a>Adicionar expressões
Depois das entidades e intenções foram definidas na aplicação do LUIS, pode adicionar as expressões. O seguinte código utiliza a [Utterances_AddBatch](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09) API, que permite que adicione expressões com até 100 por vez.  Cópia ou [baixe](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_upload.js) e guarde-o em `_upload.js`.

   [!code-javascript[Node.js code for adding utterances](~/samples-luis/examples/build-app-programmatically-csv/_upload.js)]


## <a name="run-the-code"></a>Executar o código


### <a name="install-nodejs-dependencies"></a>Instalar as dependências de node. js
Instale as dependências de node. js a partir do NPM na linha de comando/terminal.

```console
> npm install
```

### <a name="change-configuration-settings"></a>Alterar definições de configuração
Para utilizar esta aplicação, terá de alterar os valores no ficheiro Index a sua própria chave de ponto final e forneça o nome que pretende que a aplicação tenha. Também pode definir a cultura da aplicação ou alterar o número de versão.

Abra o ficheiro Index js e alterar estes valores na parte superior do ficheiro.


```javascript
// Change these values
const LUIS_programmaticKey = "YOUR_PROGRAMMATIC_KEY";
const LUIS_appName = "Sample App";
const LUIS_appCulture = "en-us"; 
const LUIS_versionId = "0.1";
```

### <a name="run-the-script"></a>Executar o script
Execute o script numa terminal/linha de comandos com node. js.

```console
> node index.js
```

ou

```console
> npm start
```

### <a name="application-progress"></a>Progresso de aplicação
Durante o execução do aplicativo, a linha de comando mostra o progresso. A saída de linha de comando inclui o formato das respostas do LUIS.

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




## <a name="open-the-luis-app"></a>Abra a aplicação LUIS
Assim que o script tiver concluído, pode iniciar sessão [LUIS](luis-reference-regions.md) e ver a aplicação do LUIS criou sob **as minhas aplicações**. Deverá conseguir ver as expressões que adicionou sob o **TurnOn**, **TurnOff**, e **None** intenções.

![Intenção de TurnOn](./media/luis-tutorial-node-import-utterances-csv/imported-utterances-661.png)


## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Testar e preparar a sua aplicação num Web site do LUIS](luis-interactive-test.md)

## <a name="additional-resources"></a>Recursos adicionais

Este aplicativo de exemplo utiliza as APIs de LUIS seguintes:
- [Criar aplicação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)
- [Adicionar intenções](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0c)
- [adicionar entidades](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0e) 
- [Adicionar expressões](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09)
