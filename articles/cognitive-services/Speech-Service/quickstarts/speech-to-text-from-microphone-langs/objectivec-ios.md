---
title: 'Quickstart: Reconhecer a fala a partir de um microfone, Objective-C - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: Aprenda a reconhecer a fala no Objective-C no iOS utilizando o Discurso SDK
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 06/25/2020
ms.author: chlandsi
ms.openlocfilehash: c355071c4bbaff5ddb22b2db5fc4a3a0fdb561d4
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85391558"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-ios-by-using-the-speech-sdk"></a>Quickstart: Reconhecer a fala no Objective-C no iOS utilizando o SDK de discurso

Os quickstarts também estão disponíveis para [síntese de fala.](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/objectivec-ios.md)

Neste artigo, aprende-se a criar uma aplicação iOS no Objective-C utilizando o Azure Cognitive Services Speech SDK para transcrever o discurso para texto a partir de um microfone ou de um ficheiro com áudio gravado.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, vai precisar:

* Uma [chave de subscrição](~/articles/cognitive-services/Speech-Service/get-started.md) para o serviço Discurso.
* Uma máquina macOS com [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) ou mais tarde.
* O objetivo definido para a versão 9.3 do iOS ou posterior.

## <a name="get-the-speech-sdk-for-ios"></a>Obter o SDK de Voz para iOS

[!INCLUDE [License notice](~/includes/cognitive-services-speech-service-license-notice.md)]

O Discurso dos Serviços Cognitivos SDK para iOS está atualmente distribuído como um quadro de cacau.
Pode ser descarregado a partir [deste site.](https://aka.ms/csspeech/iosbinary) Transfira o ficheiro para o diretório raiz.

## <a name="create-an-xcode-project"></a>Criar um projeto Xcode

Inicie o Xcode e inicie um novo projeto selecionando **o File**  >  **New**  >  **Project**.
Na caixa de diálogo de seleção do modelo, selecione o modelo **de aplicação de visualização única do iOS.**

Nas caixas de diálogo que se seguem, faça as seguintes seleções.

1. Na caixa de diálogo **'Opções de Projecto':**
    1. Insira um nome para a aplicação quickstart, por exemplo, *helloworld*.
    1. Insira um identificador de organização e nome de organização apropriado se já tiver uma conta de desenvolvedor da Apple. Para efeitos de teste, utilize um nome como *testorg*. Para assinar a aplicação, precisa de um perfil de provisionamento adequado. Para mais informações, consulte o [site de desenvolvedores da Apple.](https://developer.apple.com/)
    1. Certifique-se de que o **Objective-C** é selecionado como o idioma do projeto.
    1. Limpe todas as caixas de verificação para testes e dados principais.

    ![Definições de projeto](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-project-settings.png)

1. Selecione um diretório de projeto:
   1. Escolha o diretório raiz onde colocar o projeto. Este passo cria um diretório helloworld no seu diretório de casa que contém todos os ficheiros para o projeto Xcode.
   1. Desative a criação de um repositório Git para este projeto de exemplo.
   1. Ajuste os caminhos para o SDK no ecrã das definições do projeto.
      1. No separador **Geral** sob o cabeçalho **dos Binaries Incorporados,** adicione a biblioteca SDK como estrutura selecionando **Binaries incorporados Adicione**  >  **outros**. Vá ao seu diretório de casa e selecione o `MicrosoftCognitiveServicesSpeech.framework` ficheiro. Esta ação adiciona automaticamente a biblioteca SDK ao quadro **e bibliotecas ligados** ao cabeçalho.
         ![Quadro adicionado](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-framework.png)
      1. Vá ao separador **Definições de Construção** e selecione a definição **'Todas'.**
      1. Adicione o diretório $(SRCROOT)/.. para **caminhos de pesquisa de enquadramento** sob o título de Caminhos de **Busca.**

      ![Definição de caminhos de pesquisa de quadros](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-framework-search-paths.png)

## <a name="set-up-the-ui"></a>Configurar a IU

A aplicação de exemplo tem uma UI muito simples. Tem dois botões para iniciar o reconhecimento da fala, quer a partir do ficheiro, quer a partir da entrada do microfone, quer de uma etiqueta de texto para visualizar o resultado. A IU é configurada na parte `Main.storyboard` do projeto. Abra a vista XML do storyboard clicando à direita na `Main.storyboard` entrada da árvore do projeto e selecionando Open **As**  >  **Source Code**.

Substitua o XML autogerado por este código:

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/from-microphone/helloworld/helloworld/Base.lproj/Main.storyboard)]

## <a name="add-the-sample-code"></a>Adicionar o código de exemplo

1. Descarregue o [ficheiro de wav](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav) de amostra clicando no link e selecionando o alvo de Guardar **como**.
   Adicione o ficheiro wav ao projeto como um recurso ao arrastá-lo a partir de uma janela do Explorador para o nível da raiz da vista do Projeto.
   **Selecione Termine** na seguinte caixa de diálogo sem alterar as definições.
1. Substitua o conteúdo do ficheiro autogerado `ViewController.m` pelo seguinte código:

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/from-microphone/helloworld/helloworld/ViewController.m#code)]
1. Substitua a cadeia de carateres `YourSubscriptionKey` pela sua chave de subscrição.
1. Substitua a cadeia `YourServiceRegion` pela [região](~/articles/cognitive-services/Speech-Service/regions.md) associada à sua subscrição. Por exemplo, utilize `westus` para a subscrição de teste gratuito.
1. Adicione o pedido de acesso ao microfone. Clique com o botão direito na `Info.plist` entrada da árvore do projeto e selecione Open **As**  >  **Source Code**. Adicione as seguintes linhas na `<dict>` secção e guarde o ficheiro.

    ```xml
    <key>NSMicrophoneUsageDescription</key>
    <string>Need microphone access for speech recognition from microphone.</string>
    ```

## <a name="build-and-run-the-sample"></a>Criar e executar o exemplo

1. Tornar visível a saída de depuramento selecionando **a**  >  Consola Ativada da Área de**Debug**  >  **vervisão.**
1. Escolha o simulador iOS ou um dispositivo iOS ligado à sua máquina de desenvolvimento como destino para a aplicação a partir da lista no menu **Destino do Produto.**  >  **Destination**
1. Construa e execute o código de exemplo no simulador iOS selecionando **o Produto**  >  **Run** a partir do menu. Também pode selecionar o botão **Reproduzir.**
1. Depois de selecionar o botão **Reconhecer (Ficheiro)** na aplicação, deve ver o conteúdo do ficheiro áudio "Como está o tempo?" na parte inferior do ecrã.

   ![Aplicação simulada do iOS](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-simulated-app.png)

1. Depois de selecionar o botão **Reconhecer (Microfone)** na aplicação e dizer algumas palavras, deverá ver o texto que falou na parte inferior do ecrã.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Explore amostras objectiva-C no GitHub](https://aka.ms/csspeech/samples)
