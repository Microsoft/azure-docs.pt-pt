---
title: 'Quickstart: Reconheça o discurso de um microfone, Swift - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: Saiba como reconhecer o discurso em Swift sobre iOS usando o SDK de Fala
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/23/2019
ms.author: chlandsi
ms.openlocfilehash: 31f5339c70d52019400ca5f1fe873de4790a3bd6
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75380529"
---
# <a name="quickstart-recognize-speech-in-swift-on-ios-by-using-the-speech-sdk"></a>Quickstart: Reconheça o discurso em Swift sobre iOS usando o SDK de Fala

Os quickstarts também estão disponíveis para [síntese de fala.](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/swift-ios.md)

Neste artigo, aprende-se a criar uma aplicação iOS em Swift, utilizando o Azure Cognitive Services Speech SDK para transcrever a fala gravada de um microfone para texto.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, vai precisar de:

* Uma [chave de subscrição](~/articles/cognitive-services/Speech-Service/get-started.md) para o serviço De Fala.
* Uma máquina macOS com [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) ou mais tarde e [CocoaPods instalados.](https://cocoapods.org/)

## <a name="get-the-speech-sdk-for-ios"></a>Obter o SDK de Voz para iOS

[!INCLUDE [License notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Este tutorial não funcionará com uma versão do SDK antes de 1.6.0.

O SDK de Discurso dos Serviços Cognitivos para iOS é distribuído como um pacote-quadro. Pode ser usado em projetos Xcode como um https://aka.ms/csspeech/iosbinary [CocoaPod](https://cocoapods.org/) ou descarregado de e ligado manualmente. Este artigo usa um CocoaPod.

## <a name="create-an-xcode-project"></a>Criar um projeto Xcode

Inicie o Xcode e inicie um novo projeto selecionando **File** > **New** > **Project**.
Na caixa de diálogo de seleção de modelos, selecione o modelo **de aplicação de aplicação de visão única iOS.**

Nas caixas de diálogo que se seguem, faça as seguintes seleções.

1. Na caixa de diálogo **Opções de Projeto:**
    1. Introduza um nome para a aplicação quickstart, por exemplo, *helloworld*.
    1. Introduza um nome de organização apropriado e um identificador de organização se já tiver uma conta de desenvolvimento da Apple. Para efeitos de teste, utilize um nome como *testorg*. Para assinar a aplicação, precisa de um perfil de provisionamento adequado. Para mais informações, consulte o site de desenvolvimento da [Apple](https://developer.apple.com/).
    1. Certifique-se de que **Swift** é escolhido como o idioma para o projeto.
    1. Desative as caixas de verificação para utilizar storyboards e criar uma aplicação baseada em documentos. A simples UI para a aplicação de amostras é criada programáticamente.
    1. Limpe todas as caixas de verificação para testes e dados fundamentais.
1. Selecione um diretório de projeto:
    1. Escolha um diretório para colocar o projeto. Este passo cria um diretório helloworld no diretório escolhido que contém todos os ficheiros para o projeto Xcode.
    1. Desative a criação de um repositório Git para este projeto de exemplo.
1. A aplicação também precisa de declarar `Info.plist` a utilização do microfone no ficheiro. Selecione o ficheiro na visão geral e adicione a tecla Descrição de Descrição privacidade **- Utilização** do microfone com um valor como *microfone para reconhecimento*de voz .

    ![Definições em Info.plist](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-ios-info-plist.png)

1. Feche o projeto Xcode. Usa-se uma instância diferente depois de configurar os CocoaPods.

## <a name="add-the-sample-code"></a>Adicionar o código de exemplo

1. Coloque um novo ficheiro `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` de cabeçalho com o nome no diretório helloworld dentro do projeto Helloworld. Colá-lo o seguinte código:

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]

1. Adicione o `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` caminho relativo ao cabeçalho de ponte para as definições do projeto Swift para o alvo helloworld no campo **Objective-C Bridging Header.**

   ![Propriedades de cabeçalho](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-ios-bridging-header.png)

1. Substitua o conteúdo do `AppDelegate.swift` ficheiro autogerado pelo seguinte código:

   [!code-swift[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/helloworld/AppDelegate.swift#code)]
1. Substitua o conteúdo do `ViewController.swift` ficheiro autogerado pelo seguinte código:

   [!code-swift[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/helloworld/ViewController.swift#code)]
1. Em `ViewController.swift`, substitua a corda `YourSubscriptionKey` com a sua chave de subscrição.
1. Substitua `YourServiceRegion` a cadeia pela [região](~/articles/cognitive-services/Speech-Service/regions.md) associada à sua subscrição. Por exemplo, `westus` utilize para a subscrição de teste gratuito.

## <a name="install-the-sdk-as-a-cocoapod"></a>Instale o SDK como um CocoaPod

1. Instale o gestor de dependência do CocoaPod conforme descrito nas suas instruções de [instalação](https://guides.cocoapods.org/using/getting-started.html).
1. Vá ao diretório da sua aplicação de amostras, que é helloworld. Coloque um ficheiro de texto com o nome *Podfile* e o seguinte conteúdo nesse diretório:

   [!code-ruby[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/Podfile)]
1. Vá ao diretório helloworld em um terminal, e dirija o comando. `pod install` Este comando gera `helloworld.xcworkspace` um espaço de trabalho Xcode que contém tanto a aplicação da amostra como o SDK de Fala como uma dependência. Este espaço de trabalho é utilizado nos seguintes passos.

## <a name="build-and-run-the-sample"></a>Criar e executar o exemplo

1. Abra o `helloworld.xcworkspace` espaço de trabalho em Xcode.
1. Tornar a saída de depuração visível selecionando a**consola de ativação**de área de**depuração** > do ponto de **vista** > .
1. Escolha o simulador iOS ou um dispositivo iOS ligado à sua máquina de desenvolvimento como destino da aplicação a partir da lista no menu**Destino** do **Produto.** > 
1. Construa e execute o código de exemplo no simulador iOS selecionando o **Product** > **Run** a partir do menu. Também pode selecionar o botão **Reproduzir.**
1. Depois de selecionar o botão **Reconhecer** na aplicação e dizer algumas palavras, deve ver o texto que falou na parte inferior do ecrã.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Explore as nossas amostras no GitHub](https://aka.ms/csspeech/samples)
