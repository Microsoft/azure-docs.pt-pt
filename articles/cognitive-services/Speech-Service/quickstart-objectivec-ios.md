---
title: 'Início rápido: reconhecer fala, Objective-C-Speech Service'
titleSuffix: Azure Cognitive Services
description: Saiba como reconhecer a fala em Objective-C no iOS usando o SDK de fala
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: f943f47cdef901f80aa455d3d1e02a753e0f06e4
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72327764"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-ios-by-using-the-speech-sdk"></a>Início rápido: reconhecer a fala em Objective-C no iOS usando o SDK de fala

Os guias de início rápido também estão disponíveis para [síntese de fala](quickstart-text-to-speech-objectivec-ios.md).

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Neste artigo, você aprende a criar um aplicativo iOS em Objective-C usando o SDK de fala dos serviços cognitivas do Azure para transcrever a fala para o texto de um microfone ou de um arquivo com áudio gravado.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você precisará de:

* Uma [chave de assinatura](get-started.md) para o serviço de fala.
* Uma máquina macOS com o [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) ou posterior.
* O destino definido como iOS versão 9,3 ou posterior.

## <a name="get-the-speech-sdk-for-ios"></a>Obter o SDK de Voz para iOS

[!INCLUDE [License notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

O SDK de fala dos serviços cognitivas para iOS está atualmente distribuído como uma estrutura Cocoa.
Ele pode ser baixado deste [site](https://aka.ms/csspeech/iosbinary). Transfira o ficheiro para o diretório raiz.

## <a name="create-an-xcode-project"></a>Criar um projeto do Xcode

Inicie o Xcode e inicie um novo projeto selecionando **arquivo** > **novo** **projeto** > .
Na caixa de diálogo seleção de modelo, selecione o modelo de **aplicativo de exibição única do IOS** .

Nas caixas de diálogo a seguir, faça as seleções a seguir.

1. Na caixa de diálogo **Opções do projeto** :
    1. Insira um nome para o aplicativo de início rápido, por exemplo, *HelloWorld*.
    1. Insira um nome de organização e um identificador de organização apropriados se você já tiver uma conta de desenvolvedor da Apple. Para fins de teste, use um nome como *testorg*. Para assinar o aplicativo, você precisa de um perfil de provisionamento adequado. Para obter mais informações, consulte o [site do desenvolvedor da Apple](https://developer.apple.com/).
    1. Verifique se **Objective-C** está selecionado como o idioma do projeto.
    1. Desmarque todas as caixas de seleção dos testes e dos dados principais.

    ![Configurações do projeto](media/sdk/qs-objectivec-project-settings.png)

1. Selecione um diretório do projeto:
   1. Escolha o diretório raiz onde colocar o projeto. Esta etapa cria um diretório HelloWorld em seu diretório base que contém todos os arquivos do projeto Xcode.
   1. Desative a criação de um repositório Git para este projeto de exemplo.
   1. Ajuste os caminhos para o SDK na tela Configurações do projeto.
      1. Na guia **geral** sob o cabeçalho **binários inseridos** , adicione a biblioteca do SDK como uma estrutura selecionando **adicionar binários inseridos** > **Adicionar outro**. Vá para o diretório base e selecione o arquivo `MicrosoftCognitiveServicesSpeech.framework`. Essa ação adiciona automaticamente a biblioteca do SDK à **estrutura e bibliotecas vinculadas** ao cabeçalho.
         estrutura ![Added @ no__t-1
      1. Vá para a guia **configurações de compilação** e selecione a configuração **tudo** .
      1. Adicione o diretório $ (SRCROOT)/.. para **caminhos de pesquisa de estrutura** no título **caminhos de pesquisa** .

      ![Configuração de caminhos de pesquisa de estrutura](media/sdk/qs-objectivec-framework-search-paths.png)

## <a name="set-up-the-ui"></a>Configurar a IU

O aplicativo de exemplo tem uma interface do usuário muito simples. Ele tem dois botões para iniciar o reconhecimento de fala do arquivo ou da entrada do microfone e de um rótulo de texto para exibir o resultado. A IU é configurada na parte `Main.storyboard` do projeto. Abra a exibição XML do storyboard clicando com o botão direito do mouse na entrada `Main.storyboard` da árvore do projeto e selecionando **abrir como** **código-fonte** > .

Substitua o XML gerado automaticamente por este código:

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/Base.lproj/Main.storyboard)]

## <a name="add-the-sample-code"></a>Adicionar o código de exemplo

1. Baixe o [arquivo wav de exemplo](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav) clicando com o botão direito do mouse no link e selecionando **Salvar destino como**.
   Adicione o ficheiro wav ao projeto como um recurso ao arrastá-lo a partir de uma janela do Explorador para o nível da raiz da vista do Projeto.
   Selecione **concluir** na caixa de diálogo a seguir sem alterar as configurações.
1. Substitua o conteúdo do arquivo `ViewController.m` gerado automaticamente pelo seguinte código:

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/ViewController.m#code)]
1. Substitua a cadeia de carateres `YourSubscriptionKey` pela sua chave de subscrição.
1. Substitua a cadeia de caracteres `YourServiceRegion` pela [região](regions.md) associada à sua assinatura. Por exemplo, use `westus` para a assinatura de avaliação gratuita.
1. Adicione o pedido de acesso ao microfone. Clique com o botão direito do mouse na entrada `Info.plist` da árvore do projeto e selecione **abrir como** **código-fonte** > . Adicione as seguintes linhas à seção `<dict>` e salve o arquivo.

    ```xml
    <key>NSMicrophoneUsageDescription</key>
    <string>Need microphone access for speech recognition from microphone.</string>
    ```

## <a name="build-and-run-the-sample"></a>Criar e executar o exemplo

1. Torne a saída de depuração visível selecionando **exibir** > **área de depuração** > **Ativar console**.
1. Escolha o simulador de iOS ou um dispositivo iOS conectado à sua máquina de desenvolvimento como o destino para o aplicativo na lista no menu de**destino** **produto** > .
1. Compile e execute o código de exemplo no simulador de iOS selecionando **produto** > **executar** no menu. Você também pode selecionar o botão **reproduzir** .
1. Depois de selecionar o botão **reconhecer (arquivo)** no aplicativo, você deverá ver o conteúdo do arquivo de áudio "Qual é o clima como?" na parte inferior do ecrã.

   ![Aplicativo iOS simulado](media/sdk/qs-objectivec-simulated-app.png)

1. Depois de selecionar o botão **reconhecer (microfone)** no aplicativo e dizer algumas palavras, você deverá ver o texto que você fala na parte inferior da tela.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Explorar exemplos de Objective-C no GitHub](https://aka.ms/csspeech/samples)
