---
title: 'Início rápido: Reconhecer a conversão de voz, Swift - serviços de voz'
titleSuffix: Azure Cognitive Services
description: Aprender a reconhecer voz em Swift MacOS, utilizando o SDK de voz
services: cognitive-services
author: cbasoglu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 06/28/2019
ms.author: cbasoglu
ms.openlocfilehash: d81384c4a3b4691f179dc2b11330b25fe7fb42bb
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561518"
---
# <a name="quickstart-recognize-speech-in-swift-on-macos-using-the-speech-sdk"></a>Início rápido: Reconhecer voz em Swift MacOS, utilizando o SDK de voz

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Neste artigo, saiba como criar uma aplicação do macOS no Swift com o SDK de voz dos serviços cognitivos para transcrição de voz gravada usando um microfone para texto.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, eis uma lista de pré-requisitos:

* R [chave de subscrição](get-started.md) para o serviço de voz.
* Um computador macOS com [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) ou posterior e [CocoaPods](https://cocoapods.org/) instalado.

## <a name="get-the-speech-sdk-for-ios"></a>Obter o SDK de Voz para iOS

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

A versão atual do SDK de Voz dos Serviços Cognitivos é `1.6.0`. Tenha em atenção que este tutorial não funcionará sem alterações para qualquer versão anterior do SDK.

O SDK de voz dos serviços cognitivos para macOS é distribuído como um pacote do framework.
Ele pode ser usado em projetos do Xcode como um [CocoaPod](https://cocoapods.org/), ou transferidas a partir https://aka.ms/csspeech/macosbinary e ligada manualmente. Este guia utiliza um CocoaPod.

## <a name="create-an-xcode-project"></a>Criar um projeto do Xcode

Inicie o Xcode e um novo projeto ao clicar em **File (Ficheiro)**  > **New (Novo)**  > **Project (Projeto)** .
Na caixa de diálogo de seleção do modelo, selecione o modelo "iOS Single View App" (Vista de Aplicação Única de iOS).

Nas caixas de diálogo que se seguem, faça as seleções seguintes:

1. Caixa de Diálogo Project Options (Opções do Projeto)
    1. Introduza um nome para a aplicação de início rápido, por exemplo `helloworld`.
    1. Introduza um nome de organização apropriadas e um identificador de organização, se já tiver uma conta de programador da Apple. Para fins de teste, pode escolher qualquer nome, como `testorg`. Para assinar a aplicação, terá de um perfil de aprovisionamento correto. Consulte a [site do desenvolvedor Apple](https://developer.apple.com/) para obter detalhes.
    1. Certifique-se de que SWIFT for escolhido como o idioma para o projeto.
    1. Desative as caixas de verificação para utilizar storyboards e para criar uma aplicação baseada em documento. A interface do Usuário simple para a aplicação de exemplo será criada por meio de programação.
    1. Desative todas as caixas de verificação para testes e dados principais.
1. Selecionar o diretório do projeto
    1. Escolha um diretório para colocar o projeto. Esta ação cria um `helloworld` diretório no diretório escolhido que contém todos os ficheiros para o projeto Xcode.
    1. Desative a criação de um repositório Git para este projeto de exemplo.
1. A aplicação também tem de declarar o uso do microfone no `Info.plist` ficheiro. Clique no ficheiro na descrição geral e adicione a chave de "Descrição da utilização do microfone de – de privacidade", com um valor como "Microfone é necessário para o reconhecimento de fala".
    ![Definições no ficheiro info.](media/sdk/qs-swift-ios-info-plist.png)
1. Feche o projeto Xcode. Irá utilizar uma instância diferente do mesmo mais tarde depois de configurar o CocoaPods.

## <a name="add-the-sample-code"></a>Adicionar o código de exemplo

1. Colocar um novo arquivo de cabeçalho com o nome `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` para o `helloworld` diretório dentro da estrutura do projeto e cole o seguinte código para o mesmo: [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]
1. Adicionar o caminho relativo `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` para o cabeçalho de bridging para o Swift definições para o destino de helloworld do projeto a *cabeçalho de Bridging Objective-C* campo ![propriedades do cabeçalho](media/sdk/qs-swift-ios-bridging-header.png)
1. Substitua os conteúdos do gerado automaticamente `AppDelegate.swift` de ficheiros por: [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/AppDelegate.swift#code)]
1. Substitua os conteúdos do gerado automaticamente `ViewController.swift` de ficheiros por: [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/ViewController.swift#code)]
1. Na `ViewController.swift`, substitua a cadeia de caracteres `YourSubscriptionKey` com a sua chave de subscrição.
1. Substitua a cadeia de carateres `YourServiceRegion` pela [região](regions.md) associada à subscrição (por exemplo, `westus` para a subscrição de avaliação gratuita).

## <a name="install-the-sdk-as-a-cocoapod"></a>Instalar o SDK como um CocoaPod

1. Instalar o Gestor de dependência de CocoaPod, conforme descrito na respetiva [instruções de instalação](https://guides.cocoapods.org/using/getting-started.html).
1. Navegue para o diretório da sua aplicação de exemplo (`helloworld`). Colocar um ficheiro de texto com o nome `Podfile` e nesse diretório, o seguinte conteúdo: [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/Podfile)]
1. Navegue para o `helloworld` diretório num terminal e execute o comando `pod install`. Esta ação irá gerar um `helloworld.xcworkspace` área de trabalho do Xcode que contém a aplicação de exemplo e o SDK de voz como uma dependência. Esta área de trabalho será utilizada a seguir.

## <a name="build-and-run-the-sample"></a>Criar e executar o exemplo

1. Abra o `helloworld.xcworkspace` área de trabalho no Xcode.
1. Torne a saída de depuração visível (**View (Ver)**  > **Debug Area (Área de Depuração)**  > **Activate Console (Ativar Consola)** ).
1. Escolha o simulador do iOS ou um dispositivo iOS ligado para o seu computador de desenvolvimento como o destino para a aplicação na lista os **produto** > **destino** menu.
1. Crie e execute o código de exemplo no simulador iOS ao selecionar **Product (Produto)**  > **Run (Executar)** no menu ou ao clicar no botão **Play (Reproduzir)** .
1. Depois de clicar no botão "Recognize" na aplicação e dizer algumas palavras, deverá ver o texto que tenha se apresentou na parte inferior do ecrã.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Explore os nossos exemplos no GitHub](https://aka.ms/csspeech/samples)

