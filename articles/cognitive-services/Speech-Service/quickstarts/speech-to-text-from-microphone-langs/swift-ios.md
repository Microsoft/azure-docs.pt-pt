---
title: 'Quickstart: Reconhecer discurso, Swift - Serviço de fala (iOS)'
titleSuffix: Azure Cognitive Services
description: Saiba como criar uma app para reconhecer a fala em Swift para um dispositivo iOS usando o Cognitive Services Speech SDK.
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 06/25/2020
ms.author: chlandsi
ms.openlocfilehash: c4a66b1581049b90a419a1b62ba837fc832fd748
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86512735"
---
# <a name="quickstart-recognize-speech-in-swift-on-ios-by-using-the-speech-sdk"></a>Quickstart: Reconheça o discurso em Swift no iOS usando o Discurso SDK

Os quickstarts também estão disponíveis para [síntese de fala.](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/swift-ios.md)

Neste artigo, aprende-se a criar uma aplicação iOS em Swift, utilizando o Azure Cognitive Services Speech SDK para transcrever o discurso gravado de um microfone para texto.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, vai precisar:

* Uma [chave de subscrição](~/articles/cognitive-services/Speech-Service/get-started.md) para o serviço Discurso.
* Uma máquina macOS com [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) ou mais tarde e [Cacau](https://cocoapods.org/) instalado.

## <a name="get-the-speech-sdk-for-ios"></a>Obter o SDK de Voz para iOS

[!INCLUDE [License notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Este tutorial não funcionará com uma versão do SDK antes de 1.6.0.

O SDK de Discurso de Serviços Cognitivos para iOS é distribuído como um pacote-quadro. Pode ser usado em projetos Xcode como um [Cacau](https://cocoapods.org/) ou descarregado https://aka.ms/csspeech/iosbinary e ligado manualmente. Este artigo usa um Cacau.

## <a name="create-an-xcode-project"></a>Criar um projeto Xcode

Inicie o Xcode e inicie um novo projeto selecionando **o File**  >  **New**  >  **Project**.
Na caixa de diálogo de seleção do modelo, selecione o modelo **de aplicação de visualização única do iOS.**

Nas caixas de diálogo que se seguem, faça as seguintes seleções.

1. Na caixa de diálogo **'Opções de Projecto':**
    1. Insira um nome para a aplicação quickstart, por exemplo, *helloworld*.
    1. Insira um nome de organização apropriado e um identificador de organização se já tiver uma conta de desenvolvedor da Apple. Para efeitos de teste, utilize um nome como *testorg*. Para assinar a aplicação, precisa de um perfil de provisionamento adequado. Para mais informações, consulte o [site de desenvolvedores da Apple.](https://developer.apple.com/)
    1. Certifique-se de que **Swift** é escolhido como a língua para o projeto.
    1. Desative as caixas de verificação para utilizar storyboards e criar uma aplicação baseada em documentos. A UI simples para a aplicação da amostra é criada programáticamente.
    1. Limpe todas as caixas de verificação para testes e dados principais.
1. Selecione um diretório de projeto:
    1. Escolha um diretório para colocar o projeto. Este passo cria um diretório helloworld no diretório escolhido que contém todos os ficheiros para o projeto Xcode.
    1. Desative a criação de um repositório Git para este projeto de exemplo.
1. A aplicação também precisa de declarar o uso do microfone no `Info.plist` ficheiro. Selecione o ficheiro na visão geral e adicione a tecla **Privacidade - Descrição da utilização do microfone** com um valor como microfone para reconhecimento de *voz*.

    ![Definições em Info.plist](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-ios-info-plist.png)

1. Feche o projeto Xcode. Usa-se uma instância diferente depois de configurar os CocoaPods.

## <a name="add-the-sample-code"></a>Adicionar o código de exemplo

1. Coloque um novo ficheiro de cabeçalho com o nome `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` no diretório helloworld dentro do projeto Helloworld. Cole-lhe o seguinte código:

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]

1. Adicione o caminho relativo `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` ao cabeçalho de ponte para as definições do projeto Swift para o alvo helloworld no campo **Cabeçalho de Ponte Objective-C.**

   ![Propriedades do cabeçalho](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-ios-bridging-header.png)

1. Substitua o conteúdo do ficheiro autogerado `AppDelegate.swift` pelo seguinte código:

   [!code-swift[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/helloworld/AppDelegate.swift#code)]
1. Substitua o conteúdo do ficheiro autogerado `ViewController.swift` pelo seguinte código:

   [!code-swift[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/helloworld/ViewController.swift#code)]
1. In `ViewController.swift` , substitua a cadeia `YourSubscriptionKey` pela sua chave de subscrição.
1. Substitua a cadeia `YourServiceRegion` pela [região](~/articles/cognitive-services/Speech-Service/regions.md) associada à sua subscrição. Por exemplo, utilize `westus` para a subscrição de teste gratuito.

## <a name="install-the-sdk-as-a-cocoapod"></a>Instale o SDK como um Cacau

1. Instale o gestor de dependência do Cacau conforme descrito nas suas [instruções de instalação](https://guides.cocoapods.org/using/getting-started.html).
1. Vá ao diretório da sua aplicação de amostras, que é a Helloworld. Coloque um ficheiro de texto com o nome *Podfile* e o seguinte conteúdo nesse diretório:

   [!code-ruby[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/Podfile)]
1. Vá ao diretório helloworld em um terminal, e executar o comando `pod install` . Este comando gera um `helloworld.xcworkspace` espaço de trabalho Xcode que contém tanto a aplicação da amostra como o SDK do discurso como uma dependência. Este espaço de trabalho é utilizado nos seguintes passos.

## <a name="build-and-run-the-sample"></a>Criar e executar o exemplo

1. Abra o espaço de trabalho `helloworld.xcworkspace` em Xcode.
1. Tornar visível a saída de depuramento selecionando **a**  >  Consola Ativada da Área de**Debug**  >  **vervisão.**
1. Escolha o simulador iOS ou um dispositivo iOS ligado à sua máquina de desenvolvimento como destino para a aplicação a partir da lista no menu **Destino do Produto.**  >  **Destination**
1. Construa e execute o código de exemplo no simulador iOS selecionando **o Produto**  >  **Run** a partir do menu. Também pode selecionar o botão **Reproduzir.**
1. Depois de selecionar o botão **Reconhecer** na aplicação e dizer algumas palavras, deve ver o texto que falou na parte inferior do ecrã.

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Explore as nossas amostras no GitHub](https://aka.ms/csspeech/samples)
