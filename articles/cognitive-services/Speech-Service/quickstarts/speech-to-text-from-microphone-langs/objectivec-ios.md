---
title: 'Quickstart: Reconhecer discurso a partir de um microfone, Objective-C - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: Saiba reconhecer o discurso no Objectivo-C sobre iOS utilizando o SDK da fala
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/23/2019
ms.author: chlandsi
ms.openlocfilehash: c1246b19670a18f8dadc0c5e1c64dd5af4c1b210
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75380784"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-ios-by-using-the-speech-sdk"></a>Quickstart: Reconhecer discurso no ObjectivoC sobre iOS utilizando o SDK da fala

Os quickstarts também estão disponíveis para [síntese de fala.](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/objectivec-ios.md)

Neste artigo, aprende-se a criar uma aplicação iOS no Objective-C utilizando o Azure Cognitive Services Speech SDK para transcrever a fala a partir de um microfone ou a partir de um ficheiro com áudio gravado.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, vai precisar de:

* Uma [chave de subscrição](~/articles/cognitive-services/Speech-Service/get-started.md) para o serviço De Fala.
* Uma máquina macOS com [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) ou mais tarde.
* O alvo definido para a versão iOS 9.3 ou posterior.

## <a name="get-the-speech-sdk-for-ios"></a>Obter o SDK de Voz para iOS

[!INCLUDE [License notice](~/includes/cognitive-services-speech-service-license-notice.md)]

O SDK de Discurso dos Serviços Cognitivos para iOS é atualmente distribuído como um quadro de Cacau.
Pode ser descarregado a partir [deste site.](https://aka.ms/csspeech/iosbinary) Transfira o ficheiro para o diretório raiz.

## <a name="create-an-xcode-project"></a>Criar um projeto Xcode

Inicie o Xcode e inicie um novo projeto selecionando **File** > **New** > **Project**.
Na caixa de diálogo de seleção de modelos, selecione o modelo **de aplicação de aplicação de visão única iOS.**

Nas caixas de diálogo que se seguem, faça as seguintes seleções.

1. Na caixa de diálogo **Opções de Projeto:**
    1. Introduza um nome para a aplicação quickstart, por exemplo, *helloworld*.
    1. Introduza um nome e identificador de organização apropriados se já tiver uma conta de desenvolvimento da Apple. Para efeitos de teste, utilize um nome como *testorg*. Para assinar a aplicação, precisa de um perfil de provisionamento adequado. Para mais informações, consulte o site de desenvolvimento da [Apple](https://developer.apple.com/).
    1. Certifique-se de que o **Objectivo-C** é selecionado como o idioma do projeto.
    1. Limpe todas as caixas de verificação para testes e dados fundamentais.

    ![Configurações do projeto](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-project-settings.png)

1. Selecione um diretório de projeto:
   1. Escolha o diretório raiz onde colocar o projeto. Este passo cria um diretório helloworld no seu diretório doméstico que contém todos os ficheiros para o projeto Xcode.
   1. Desative a criação de um repositório Git para este projeto de exemplo.
   1. Ajuste os caminhos para o SDK no ecrã de definições do projeto.
      1. No separador **Geral** sob o cabeçalho **Embedded Binaries,** adicione a biblioteca SDK como uma estrutura selecionando **add binárias incorporadas** > **Adicione outros**. Vá ao seu diretório de `MicrosoftCognitiveServicesSpeech.framework`casa e selecione o ficheiro . Esta ação adiciona automaticamente a biblioteca SDK ao cabeçalho **Linked Framework and Libraries.**
         ![Quadro adicional](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-framework.png)
      1. Vá ao separador **'Definições de Construção'** e selecione a definição **de Todas** as definições.
      1. Adicione o diretório $(SRCROOT)/.. para **Caminhos de Busca-Quadro** sob a rubrica Caminhos de **Busca.**

      ![Definição de caminhos de pesquisa-quadro](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-framework-search-paths.png)

## <a name="set-up-the-ui"></a>Configurar a IU

A aplicação de exemplo tem uma UI muito simples. Tem dois botões para iniciar o reconhecimento da fala, quer a partir do ficheiro, quer a partir da entrada do microfone e de uma etiqueta de texto para exibir o resultado. A IU é configurada na parte `Main.storyboard` do projeto. Abra a vista XML do storyboard clicando na `Main.storyboard` entrada da árvore do projeto e selecionando Open As **Source** > **Code**.

Substitua o XML autogerado por este código:

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/from-microphone/helloworld/helloworld/Base.lproj/Main.storyboard)]

## <a name="add-the-sample-code"></a>Adicionar o código de exemplo

1. Descarregue o [ficheiro de acenar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav) da amostra clicando no link e selecionando o alvo Save **como**.
   Adicione o ficheiro wav ao projeto como um recurso ao arrastá-lo a partir de uma janela do Explorador para o nível da raiz da vista do Projeto.
   Selecione **Terminar** na seguinte caixa de diálogo sem alterar as definições.
1. Substitua o conteúdo do `ViewController.m` ficheiro autogerado pelo seguinte código:

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/from-microphone/helloworld/helloworld/ViewController.m#code)]
1. Substitua a cadeia de carateres `YourSubscriptionKey` pela sua chave de subscrição.
1. Substitua `YourServiceRegion` a cadeia pela [região](~/articles/cognitive-services/Speech-Service/regions.md) associada à sua subscrição. Por exemplo, `westus` utilize para a subscrição de teste gratuito.
1. Adicione o pedido de acesso ao microfone. Clique à `Info.plist` direita na entrada da árvore do projeto e selecione **Open As** > **Source Code**. Adicione as seguintes `<dict>` linhas na secção e, em seguida, guarde o ficheiro.

    ```xml
    <key>NSMicrophoneUsageDescription</key>
    <string>Need microphone access for speech recognition from microphone.</string>
    ```

## <a name="build-and-run-the-sample"></a>Criar e executar o exemplo

1. Tornar a saída de depuração visível selecionando a**consola de ativação**de área de**depuração** > do ponto de **vista** > .
1. Escolha o simulador iOS ou um dispositivo iOS ligado à sua máquina de desenvolvimento como destino da aplicação a partir da lista no menu**Destino** do **Produto.** > 
1. Construa e execute o código de exemplo no simulador iOS selecionando o **Product** > **Run** a partir do menu. Também pode selecionar o botão **Reproduzir.**
1. Depois de selecionar o botão **Reconhecer (Arquivo)** na aplicação, deve ver o conteúdo do ficheiro áudio "Como está o tempo?" na parte inferior do ecrã.

   ![Aplicação simulada de iOS](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-simulated-app.png)

1. Depois de selecionar o botão **Reconhecer (Microfone)** na aplicação e dizer algumas palavras, deve ver o texto que falou na parte inferior do ecrã.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Explore as amostras do Objectivo-C no GitHub](https://aka.ms/csspeech/samples)
