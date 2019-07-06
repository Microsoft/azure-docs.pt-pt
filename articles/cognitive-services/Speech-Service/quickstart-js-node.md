---
title: 'Início rápido: Reconhecer a conversão de voz, node. js - serviços de voz'
titleSuffix: Azure Cognitive Services
description: Utilize este guia para criar uma aplicação de consola de voz em texto com o SDK de voz para node. js. Quando terminar, pode utilizar o microfone do seu computador para converter voz em texto em tempo real.
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: fmegen
ms.openlocfilehash: 9d233de8a9cdd4b9a3637edcd1c6196b4ad16fd2
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605119"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-nodejs"></a>Início rápido: Reconhecer a conversão de voz com o SDK de voz para node. js

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Este artigo mostra-lhe como criar um projeto de node. js ao utilizar o enlace de JavaScript do SDK de voz para os serviços cognitivos do Azure para a transcrição de voz em texto.
O aplicativo se baseia a [SDK de voz para JavaScript](https://aka.ms/csspeech/npmpackage).

## <a name="prerequisites"></a>Pré-requisitos

* Uma chave de subscrição do Azure para o serviço de voz. [Obter uma gratuitamente](get-started.md).
* Uma versão atual do [node. js](https://nodejs.org).

## <a name="create-a-new-project"></a>Criar um novo projeto

Criar uma nova pasta e inicializar o projeto:

```sh
npm init -f
```

Este comando inicializa a **Package. JSON** ficheiros com os valores predefinidos. Provavelmente desejará editar este ficheiro mais tarde.

## <a name="install-the-speech-sdk"></a>Instalar o SDK de voz

Adicione o SDK de voz ao seu projeto de node. js:

```
npm install microsoft-cognitiveservices-speech-sdk
```

Este comando transfere e instala a versão mais recente do SDK de voz e quaisquer pré-requisitos necessários partir **npmjs**. O SDK instala no `node_modules` diretório dentro da pasta do projeto.

## <a name="use-the-speech-sdk"></a>Utilizar o SDK de voz

Crie um novo ficheiro na pasta, com o nome `index.js`e abra este ficheiro com um editor de texto.

> [!NOTE]
> No node. js, o SDK de voz não suporta o microfone ou o **ficheiro** tipo de dados. Ambos são suportadas apenas em browsers. Em alternativa, utilize o **Stream** interface para o SDK de voz através de `AudioInputStream.createPushStream()` ou `AudioInputStream.createPullStream()`.

Neste exemplo, utilizamos o `PushAudioInputStream` interface.

Adicione este código JavaScript:

[!code-javascript[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/js-node/index.js#code)]

## <a name="run-the-sample"></a>Executar o exemplo

Para abrir a aplicação, adaptar `YourSubscriptionKey`, `YourServiceRegion`, e `YourAudioFile.wav` à sua configuração. Em seguida, executá-la ao chamar este comando:

```sh
node index.js
```

Aciona um reconhecimento utilizando o nome de ficheiro fornecido. E ele apresenta o resultado no console.

Este exemplo é a saída quando executa `index.js` depois de atualizar a chave de subscrição e usar o arquivo `whatstheweatherlike.wav`:

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

## <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Instalar e utilizar o SDK de voz com o Visual Studio Code

Também pode executar o exemplo do Visual Studio Code. Siga estes passos para instalar, abra e execute o início rápido:

1. Inicie o Visual Studio Code. Selecione **Abrir pasta**. Em seguida, navegue até à pasta de início rápido.

   ![Abrir pasta](media/sdk/qs-js-node-01-open_project.png)

1. Abra um terminal no Visual Studio Code.

   ![A janela de terminal](media/sdk/qs-js-node-02_open_terminal.png)

1. Executar `npm` para instalar as dependências.

   ![instalação de npm](media/sdk/qs-js-node-03-npm_install.png)

1. Agora, está pronto para abrir `index.js`e defina um ponto de interrupção.

   ![Index com um ponto de interrupção na linha 16](media/sdk/qs-js-node-04-setup_breakpoint.png)

1. Para iniciar a depuração, selecione F5 ou selecione **depuração/iniciar depuração** no menu.

   ![Debug menu](media/sdk/qs-js-node-05-start_debugging.png)

1. Quando for atingido um ponto de interrupção, pode inspecionar a pilha de chamadas e as variáveis.

   ![Depurador](media/sdk/qs-js-node-06-hit_breakpoint.png)

1. Qualquer saída mostra na janela da consola de depuração.

   ![Consola de depuração](media/sdk/qs-js-node-07-debug_output.png)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Explore exemplos de node. js no GitHub](https://aka.ms/csspeech/samples)
