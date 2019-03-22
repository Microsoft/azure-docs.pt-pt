---
title: 'Início rápido: Reconhecer a conversão de voz, o JavaScript (navegador) - serviços de voz'
titleSuffix: Azure Cognitive Services
description: Aprender a reconhecer a voz em JavaScript num navegador com o SDK de voz
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 2/20/2019
ms.author: fmegen
ms.openlocfilehash: 0a06293a648460694bbe222f36f81bea2d6acdac
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57841179"
---
# <a name="quickstart-recognize-speech-in-javascript-in-a-browser-using-the-speech-sdk"></a>Início rápido: Reconhecer voz em JavaScript num navegador com o SDK de voz

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Neste artigo, vai aprender a criar um site com o enlace de JavaScript do SDK de Voz dos Serviços Cognitivos para fazer a conversão de voz em texto.
A aplicação baseia-se o SDK de voz de serviços cognitivos da Microsoft ([transfira a versão 1.3.0](https://aka.ms/csspeech/jsbrowserpackage)).

## <a name="prerequisites"></a>Pré-requisitos

* Uma chave de subscrição para o serviço de Voz. Ver [experimentar gratuitamente os serviços de voz](get-started.md).
* Um PC ou Mac, com um microfone a funcionar.
* Um editor de texto.
* Uma versão atual do Chrome ou do Microsoft Edge.
* Opcionalmente, um servidor Web que suporte o alojamento de scripts PHP.

## <a name="create-a-new-website-folder"></a>Criar uma nova pasta de sites

Crie uma nova pasta vazia. No caso de querer alojar o exemplo num servidor Web, certifique-se de que este pode aceder à pasta.

## <a name="unpack-the-speech-sdk-for-javascript-into-that-folder"></a>Descompactar o SDK de Voz para JavaScript nessa pasta

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Transfira o SDK de Voz como um [pacote .zip](https://aka.ms/csspeech/jsbrowserpackage) e descompacte-o para a pasta recentemente criada. Isso resulta em dois arquivos, a ser descompactados `microsoft.cognitiveservices.speech.sdk.bundle.js` e `microsoft.cognitiveservices.speech.sdk.bundle.js.map`.
O último arquivo é opcional e é útil para depurar o código do SDK.

## <a name="create-an-indexhtml-page"></a>Criar uma página index.html

Crie um novo ficheiro na pasta, com o nome `index.html`, e abra este ficheiro com um editor de texto.

1. Crie a seguinte estrutura HTML:

   ```html
   <html>
   <head>
      <title>Microsoft Cognitive Service Speech SDK JavaScript Quickstart</title>
   </head>
   <body>
    <!-- UI code goes here -->

    <!-- SDK reference goes here -->

    <!-- Optional authorization token request goes here -->

    <!-- Sample code goes here -->
   </body>
   </html>
   ```

1. Adicione o seguinte código de IU ao seu ficheiro, abaixo do primeiro comentário:

   [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#uidiv)]

1. Adicionar uma referência ao SDK de Voz

   [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#speechsdkref)]

1. Conectar manipuladores para o botão de reconhecimento, o resultado do reconhecimento e a campos relacionados com a subscrição definidos pelo código da interface do Usuário:

   [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#quickstartcode)]

## <a name="create-the-token-source-optional"></a>Criar a origem do token (opcional)

No caso de querer alojar a página Web num servidor Web, pode fornecer opcionalmente uma origem de token para a aplicação de demonstração.
Dessa forma, a chave de subscrição nunca vai sair do servidor, o que permite aos utilizadores usufruir das capacidades de voz sem introduzir qualquer código de autorização.

1. Crie um novo ficheiro com o nome `token.php`. Neste exemplo, presumimos que o servidor Web suporta a linguagem de scripts PHP. Introduza o seguinte código:

   [!code-php[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/token.php)]

1. Edite o ficheiro `index.html` e adicione o seguinte código ao mesmo:

   [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#authorizationfunction)]

> [!NOTE]
> Os tokens de autorização têm uma duração limitada.
> Este exemplo simplificado não mostra como atualizar automaticamente os tokens de autorização. Como utilizador, pode recarregar manualmente a página ou premir F5 para atualizar.

## <a name="build-and-run-the-sample-locally"></a>Criar e executar o exemplo localmente

Para iniciar a aplicação, faça duplo clique no ficheiro index.html ou abra-o com o seu browser favorito. Será apresentada uma GUI simples que lhe permite introduzir a chave de subscrição e a [região](regions.md), e acionar um reconhecimento com o microfone.

## <a name="build-and-run-the-sample-via-a-web-server"></a>Criar e executar o exemplo através de um servidor Web

Inicie a aplicação, abra o seu browser favorito, aponte-o para o URL público onde está alojada a pasta, introduza a [região](regions.md) e acione um reconhecimento com o microfone. Se estiver configurado, vai adquirir um token a partir da origem de token.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Explore exemplos de JavaScript no GitHub](https://aka.ms/csspeech/samples)
