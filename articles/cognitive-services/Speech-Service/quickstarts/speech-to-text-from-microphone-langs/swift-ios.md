---
title: 'Início rápido: reconhecer a fala de um microfone, serviço de fala Swift'
titleSuffix: Azure Cognitive Services
description: Saiba como reconhecer a fala no Swift no iOS usando o SDK de fala
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/23/2019
ms.author: chlandsi
ms.openlocfilehash: 31f5339c70d52019400ca5f1fe873de4790a3bd6
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75380529"
---
# <a name="quickstart-recognize-speech-in-swift-on-ios-by-using-the-speech-sdk"></a>Início rápido: reconhecer a fala no Swift no iOS usando o SDK de fala

Os guias de início rápido também estão disponíveis para [síntese de fala](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/swift-ios.md).

Neste artigo, você aprende a criar um aplicativo iOS no Swift usando o SDK de fala dos serviços cognitivas do Azure para transcrever a fala registrada de um microfone para texto.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você precisará de:

* Uma [chave de assinatura](~/articles/cognitive-services/Speech-Service/get-started.md) para o serviço de fala.
* Uma máquina macOS com o [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) ou posterior e o [CocoaPods](https://cocoapods.org/) instalado.

## <a name="get-the-speech-sdk-for-ios"></a>Obter o SDK de Voz para iOS

[!INCLUDE [License notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Este tutorial não funcionará com uma versão do SDK anterior à 1.6.0.

O SDK de fala dos serviços cognitivas para iOS é distribuído como um pacote de estrutura. Ele pode ser usado em projetos do Xcode como um [CocoaPod](https://cocoapods.org/) ou baixado do https://aka.ms/csspeech/iosbinary e vinculado manualmente. Este artigo usa um CocoaPod.

## <a name="create-an-xcode-project"></a>Criar um projeto do Xcode

Inicie o Xcode e inicie um novo projeto selecionando **arquivo** > **novo** **projeto**de > .
Na caixa de diálogo seleção de modelo, selecione o modelo de **aplicativo de exibição única do IOS** .

Nas caixas de diálogo a seguir, faça as seleções a seguir.

1. Na caixa de diálogo **Opções do projeto** :
    1. Insira um nome para o aplicativo de início rápido, por exemplo, *HelloWorld*.
    1. Insira um nome de organização apropriado e um identificador de organização se você já tiver uma conta de desenvolvedor da Apple. Para fins de teste, use um nome como *testorg*. Para assinar o aplicativo, você precisa de um perfil de provisionamento adequado. Para obter mais informações, consulte o [site do desenvolvedor da Apple](https://developer.apple.com/).
    1. Verifique se o **Swift** é escolhido como o idioma do projeto.
    1. Desabilite as caixas de seleção para usar storyboards e criar um aplicativo baseado em documento. A interface do usuário simples para o aplicativo de exemplo é criada programaticamente.
    1. Desmarque todas as caixas de seleção dos testes e dos dados principais.
1. Selecione um diretório do projeto:
    1. Escolha um diretório no qual colocar o projeto. Esta etapa cria um diretório HelloWorld no diretório escolhido que contém todos os arquivos do projeto Xcode.
    1. Desative a criação de um repositório Git para este projeto de exemplo.
1. O aplicativo também precisa declarar o uso do microfone no arquivo de `Info.plist`. Selecione o arquivo na visão geral e adicione a chave de **Descrição privacidade-uso do microfone** com um valor como o *microfone é necessário para o reconhecimento de fala*.

    ![Configurações no info. plist](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-ios-info-plist.png)

1. Feche o projeto Xcode. Você usará uma instância diferente dela posteriormente depois de configurar o CocoaPods.

## <a name="add-the-sample-code"></a>Adicionar o código de exemplo

1. Coloque um novo arquivo de cabeçalho com o nome `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` no diretório HelloWorld dentro do projeto HelloWorld. Cole o código a seguir nele:

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]

1. Adicione o caminho relativo `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` ao cabeçalho de ponte para as configurações de projeto Swift para o destino HelloWorld no campo de **cabeçalho ponte Objective-C** .

   ![Propriedades do cabeçalho](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-ios-bridging-header.png)

1. Substitua o conteúdo do arquivo de `AppDelegate.swift` gerado automaticamente pelo seguinte código:

   [!code-swift[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/helloworld/AppDelegate.swift#code)]
1. Substitua o conteúdo do arquivo de `ViewController.swift` gerado automaticamente pelo seguinte código:

   [!code-swift[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/helloworld/ViewController.swift#code)]
1. Em `ViewController.swift`, substitua a cadeia de caracteres `YourSubscriptionKey` pela sua chave de assinatura.
1. Substitua a cadeia de caracteres `YourServiceRegion` pela [região](~/articles/cognitive-services/Speech-Service/regions.md) associada à sua assinatura. Por exemplo, use `westus` para a assinatura de avaliação gratuita.

## <a name="install-the-sdk-as-a-cocoapod"></a>Instalar o SDK como um CocoaPod

1. Instale o Gerenciador de dependência do CocoaPod conforme descrito em suas [instruções de instalação](https://guides.cocoapods.org/using/getting-started.html).
1. Vá para o diretório do aplicativo de exemplo, que é HelloWorld. Coloque um arquivo de texto com o nome *Podfile* e o seguinte conteúdo nesse diretório:

   [!code-ruby[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/Podfile)]
1. Vá para o diretório HelloWorld em um terminal e execute o comando `pod install`. Esse comando gera um `helloworld.xcworkspace` espaço de trabalho do Xcode que contém o aplicativo de exemplo e o SDK de fala como uma dependência. Esse espaço de trabalho é usado nas etapas a seguir.

## <a name="build-and-run-the-sample"></a>Criar e executar o exemplo

1. Abra o `helloworld.xcworkspace` do espaço de trabalho no Xcode.
1. Torne a saída de depuração visível selecionando **exibir** > **área de depuração** > **Ativar console**.
1. Escolha o simulador de iOS ou um dispositivo iOS conectado à sua máquina de desenvolvimento como o destino para o aplicativo na lista no menu de **destino** **produto** > .
1. Crie e execute o código de exemplo no simulador de iOS selecionando **produto** > **executar** no menu. Você também pode selecionar o botão **reproduzir** .
1. Depois de selecionar o botão **reconhecer** no aplicativo e dizer algumas palavras, você deverá ver o texto falado na parte inferior da tela.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Explore nossos exemplos no GitHub](https://aka.ms/csspeech/samples)
