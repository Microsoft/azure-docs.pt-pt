---
title: 'Início rápido: Reconhecer fala, Node. js-serviço de fala'
titleSuffix: Azure Cognitive Services
description: Use este guia para criar um aplicativo de console de conversão de fala em texto usando o SDK de fala para node. js. Quando terminar, pode utilizar o microfone do seu computador para converter voz em texto em tempo real.
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: fmegen
ms.openlocfilehash: 1a3d1bc62e995aff43d9538f49b436a7de16f1e9
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68554082"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-nodejs"></a>Início rápido: Reconhecer a fala com o SDK de fala para node. js

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Este artigo mostra como criar um projeto do node. js usando a ligação JavaScript do SDK de fala para serviços cognitivas do Azure para transcrever a fala para o texto.
O aplicativo é baseado no [SDK de fala para JavaScript](https://aka.ms/csspeech/npmpackage).

## <a name="prerequisites"></a>Pré-requisitos

* Uma chave de assinatura do Azure para o serviço de fala. [Obtenha um gratuitamente](get-started.md).
* Uma versão atual do [node. js](https://nodejs.org).

## <a name="create-a-new-project"></a>Criar um novo projeto

Crie uma nova pasta e inicialize o projeto:

```sh
npm init -f
```

Esse comando Inicializa os arquivos **Package. JSON** com valores padrão. Você provavelmente desejará editar esse arquivo mais tarde.

## <a name="install-the-speech-sdk"></a>Instalar o SDK de fala

Adicione o SDK de fala ao seu projeto do node. js:

```
npm install microsoft-cognitiveservices-speech-sdk
```

Esse comando baixa e instala a versão mais recente do SDK de fala e todos os pré-requisitos necessários do **npmjs**. O SDK é instalado no `node_modules` diretório dentro da pasta do projeto.

## <a name="use-the-speech-sdk"></a>Usar o SDK de fala

Crie um novo arquivo na pasta, chamado `index.js`, e abra esse arquivo com um editor de texto.

> [!NOTE]
> No node. js, o SDK de fala não dá suporte ao microfone ou ao tipo de dados de **arquivo** . Ambos têm suporte apenas em navegadores. Em vez disso, use a interface de **fluxo** para o SDK de `AudioInputStream.createPushStream()` fala `AudioInputStream.createPullStream()`, seja por meio de ou.

Neste exemplo, usamos a `PushAudioInputStream` interface.

Adicione este código JavaScript:

[!code-javascript[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/js-node/index.js#code)]

## <a name="run-the-sample"></a>Executar o exemplo

Para abrir o aplicativo, adaptar `YourSubscriptionKey`, `YourServiceRegion`e `YourAudioFile.wav` para sua configuração. Em seguida, execute-o chamando este comando:

```sh
node index.js
```

Ele dispara um reconhecimento usando o nome de arquivo fornecido. E apresenta a saída no console.

Este exemplo é a saída quando você executa `index.js` depois de atualizar a chave de assinatura e usar o `whatstheweatherlike.wav`arquivo:

```json
SpeechRecognitionResult {
  "privResultId": "9E30EEBD41AC4571BB77CF9164441F46",
  "privReason": 3,
  "privText": "What's the weather like?",
  "privDuration": 15900000,
  "privOffset": 300000,
  "privErrorDetails": null,
  "privJson": {
    "RecognitionStatus": "Success",
    "DisplayText": "What's the weather like?",
    "Offset": 300000,
    "Duration": 15900000
  },
  "privProperties": null
}
```

## <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Instalar e usar o SDK de fala com Visual Studio Code

Você também pode executar o exemplo de Visual Studio Code. Siga estas etapas para instalar, abrir e executar o início rápido:

1. Iniciar Visual Studio Code. Selecione **abrir pasta**. Em seguida, navegue até a pasta de início rápido.

   ![Abrir pasta](media/sdk/qs-js-node-01-open_project.png)

1. Abra um terminal no Visual Studio Code.

   ![A janela do terminal](media/sdk/qs-js-node-02_open_terminal.png)

1. Execute `npm` para instalar as dependências.

   ![NPM instalar](media/sdk/qs-js-node-03-npm_install.png)

1. Agora você está pronto para abrir `index.js`e definir um ponto de interrupção.

   ![index. js com um ponto de interrupção na linha 16](media/sdk/qs-js-node-04-setup_breakpoint.png)

1. Para iniciar a depuração, selecione F5 ou selecione **depurar/iniciar depuração** no menu.

   ![O menu Depurar](media/sdk/qs-js-node-05-start_debugging.png)

1. Quando um ponto de interrupção é atingido, você pode inspecionar a pilha de chamadas e as variáveis.

   ![Depurador](media/sdk/qs-js-node-06-hit_breakpoint.png)

1. Qualquer saída é mostrada na janela do console de depuração.

   ![Console de depuração](media/sdk/qs-js-node-07-debug_output.png)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Explore os exemplos de Node. js no GitHub](https://aka.ms/csspeech/samples)
