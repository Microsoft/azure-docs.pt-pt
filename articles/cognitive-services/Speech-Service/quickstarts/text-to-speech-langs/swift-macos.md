---
title: 'Quickstart: Sintetizar discurso, Swift - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: Saiba como sintetizar o discurso em Swift sobre o macOS usando o Speech SDK
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 06/25/2020
ms.author: yulili
ms.openlocfilehash: 1ed68b2ba00a89ff7b20b8ed61457dfb268917e4
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85391269"
---
# <a name="quickstart-synthesize-speech-in-swift-on-macos-using-the-speech-sdk"></a>Quickstart: Sintetizar o discurso em Swift sobre o macOS usando o Speech SDK

Neste artigo, aprende-se a criar uma aplicação para macOS em Swift utilizando o Cognitive Services Speech SDK para sintetizar a fala a partir de texto e reproduzi-la com a saída de áudio padrão.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, aqui está uma lista de pré-requisitos:

* Uma [chave de subscrição](~/articles/cognitive-services/Speech-Service/get-started.md) para o serviço Discurso.
* Uma máquina macOS com [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) ou mais tarde e [Cacau](https://cocoapods.org/) instalado.

## <a name="get-the-speech-sdk-for-macos"></a>Obtenha o SDK de discurso para macOS

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

O SDK de Discurso de Serviços Cognitivos para macOS é distribuído como um pacote-quadro.
Pode ser usado em projetos Xcode como um [Cacau,](https://cocoapods.org/)ou descarregado https://aka.ms/csspeech/macosbinary e ligado manualmente. Este guia usa um Cacau.

> [!NOTE] 
> Este tutorial não funcionará com versões do SDK antes de 1.7.0.

## <a name="create-an-xcode-project"></a>Criar um projeto Xcode

Inicie o Xcode e inicie um novo projeto clicando **em File**  >  **New**  >  **Project**.
No diálogo de seleção do modelo, escolha o modelo "App cocoa".

Nas caixas de diálogo que se seguem, faça as seleções seguintes:

1. Caixa de Diálogo Project Options (Opções do Projeto)
    1. Introduza um nome para a aplicação de início rápido, por exemplo `helloworld`.
    1. Insira um nome de organização apropriado e um identificador de organização, se já tiver uma conta de desenvolvedor da Apple. Para fins de teste, pode escolher qualquer nome, como `testorg`. Para assinar a aplicação, precisa de um perfil de provisionamento adequado. Consulte o [site de desenvolvedores](https://developer.apple.com/) da Apple para obter mais detalhes.
    1. Certifique-se de que Swift é escolhido como a língua para o projeto.
    1. Desative as caixas de verificação para utilizar quadros de histórias e criar uma aplicação baseada em documentos. A UI simples para a aplicação da amostra será criada programáticamente.
    1. Desative todas as caixas de verificação para testes e dados principais.
1. Selecionar o diretório do projeto
    1. Escolha um diretório para colocar o projeto. Isto cria um `helloworld` diretório no diretório escolhido que contém todos os ficheiros para o projeto Xcode.
    1. Desative a criação de um repositório Git para este projeto de exemplo.
1. Feche o projeto Xcode. Utilizará um exemplo diferente mais tarde depois de configurar os CocoaPods.

## <a name="add-the-sample-code"></a>Adicionar o código de exemplo

1. Coloque um novo ficheiro de cabeçalho com o nome `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` no `helloworld` diretório dentro do projeto Helloworld e cole-o o seguinte código:

   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift/macos/text-to-speech/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]
1. Adicione o caminho relativo `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` ao cabeçalho de ponte para as definições do projeto Swift para o alvo helloworld nas propriedades do *cabeçalho de cabeçalho de ponte Objective-C* ![](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-macos-bridging-header.png)
1. Substitua o conteúdo do ficheiro `AppDelegate.swift` gerado automaticamente por:

   [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift/macos/text-to-speech/helloworld/helloworld/AppDelegate.swift#code)]
1. In `AppDelegate.swift` , substitua a cadeia `YourSubscriptionKey` pela sua chave de subscrição.
1. Substitua a cadeia de carateres `YourServiceRegion` pela região associada à subscrição (por exemplo, `westus` para a subscrição de avaliação gratuita).

## <a name="install-the-sdk-as-a-cocoapod"></a>Instale o SDK como um Cacau

1. Instale o gestor de dependência do Cacau conforme descrito nas suas [instruções de instalação](https://guides.cocoapods.org/using/getting-started.html).
1. Navegue para o diretório da sua aplicação de amostras `helloworld` (). Coloque um ficheiro de texto com o nome `Podfile` e o seguinte conteúdo nesse diretório:

   [!code-ruby[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift/macos/text-to-speech/helloworld/Podfile)]
1. Navegue até ao `helloworld` diretório num terminal e desave o comando `pod install` . Isto irá gerar um `helloworld.xcworkspace` espaço de trabalho Xcode contendo tanto a aplicação da amostra como o SDK do Discurso como uma dependência. Este espaço de trabalho será utilizado no seguinte.

## <a name="build-and-run-the-sample"></a>Criar e executar o exemplo

1. Abra o `helloworld.xcworkspace` espaço de trabalho em Xcode.
1. Tornar visível a saída de depurar **(Ver**  >  **Debug Area**  >  **Activate Console).**
1. Construa e execute o código de exemplo selecionando **o Produto**  >  **Executar** a partir do menu ou clicando no botão **Reproduzir.**
1. Depois de inserir algum texto e clicar no botão na aplicação, deverá ouvir o áudio sintetizado reproduzido.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Explore as nossas amostras no GitHub](https://aka.ms/csspeech/samples)

