---
title: 'Início rápido: Reconhecer fala, serviço de fala Swift'
titleSuffix: Azure Cognitive Services
description: Saiba como reconhecer a fala em Swift no macOS usando o SDK de fala
services: cognitive-services
author: cbasoglu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 06/28/2019
ms.author: cbasoglu
ms.openlocfilehash: 64f02a33a3aeecddf751b8293542776bf5819574
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70061899"
---
# <a name="quickstart-recognize-speech-in-swift-on-macos-using-the-speech-sdk"></a>Início rápido: Reconhecer a fala no Swift no macOS usando o SDK de fala

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Neste artigo, você aprenderá a criar um aplicativo macOS no Swift usando o SDK de fala dos serviços cognitivas para transcrever a fala registrada de um microfone para um texto.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, eis uma lista de pré-requisitos:

* Uma [chave de assinatura](get-started.md) para o serviço de fala.
* Uma máquina macOS com o [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) ou posterior e o [CocoaPods](https://cocoapods.org/) instalado.

## <a name="get-the-speech-sdk-for-macos"></a>Obtenha o SDK de fala para macOS

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

A versão atual do SDK de Voz dos Serviços Cognitivos é `1.6.0`. Observe que este tutorial não funcionará sem alterações em nenhuma versão anterior do SDK.

O SDK de fala dos serviços cognitivas para macOS é distribuído como um pacote de estrutura.
Ele pode ser usado em projetos do Xcode como um [CocoaPod](https://cocoapods.org/)ou baixado de https://aka.ms/csspeech/macosbinary e vinculado manualmente. Este guia usa um CocoaPod.

## <a name="create-an-xcode-project"></a>Criar um projeto do Xcode

Inicie o Xcode e um novo projeto ao clicar em **File (Ficheiro)**  > **New (Novo)**  > **Project (Projeto)** .
Na caixa de diálogo seleção de modelo, escolha o modelo "aplicativo Cocoa".

Nas caixas de diálogo que se seguem, faça as seleções seguintes:

1. Caixa de Diálogo Project Options (Opções do Projeto)
    1. Introduza um nome para a aplicação de início rápido, por exemplo `helloworld`.
    1. Insira um nome de organização apropriado e um identificador de organização, se você já tiver uma conta de desenvolvedor da Apple. Para fins de teste, pode escolher qualquer nome, como `testorg`. Para assinar o aplicativo, você precisa de um perfil de provisionamento adequado. Consulte o [site do desenvolvedor da Apple](https://developer.apple.com/) para obter detalhes.
    1. Verifique se o Swift é escolhido como o idioma do projeto.
    1. Desabilite as caixas de seleção para usar storyboards e criar um aplicativo baseado em documento. A interface do usuário simples para o aplicativo de exemplo será criada programaticamente.
    1. Desative todas as caixas de verificação para testes e dados principais.
1. Selecionar o diretório do projeto
    1. Escolha um diretório no qual colocar o projeto. Isso cria um `helloworld` diretório no diretório escolhido que contém todos os arquivos do projeto Xcode.
    1. Desative a criação de um repositório Git para este projeto de exemplo.
1. Defina os direitos de acesso de rede e microfone. Clique no nome do aplicativo na primeira linha da visão geral à esquerda para acessar a configuração do aplicativo e, em seguida, escolha a guia "recursos".
    1. Habilite a configuração "área restrita do aplicativo" para o aplicativo.
    1. Habilite as caixas de seleção para o acesso "conexões de saída" e "microfone".
    ![Configurações da área restrita](media/sdk/qs-swift-macos-sandbox.png)
1. O aplicativo também precisa declarar o uso do microfone no `Info.plist` arquivo. Clique no arquivo na visão geral e adicione a chave "privacidade – descrição de uso do microfone", com um valor como "o microfone é necessário para o reconhecimento de fala".
    ![Configurações no info. plist](media/sdk/qs-swift-macos-info-plist.png)
1. Feche o projeto Xcode. Você usará uma instância diferente dela posteriormente depois de configurar o CocoaPods.

## <a name="add-the-sample-code"></a>Adicionar o código de exemplo

1. Coloque um novo arquivo de cabeçalho com o `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` nome `helloworld` no diretório dentro do projeto HelloWorld e cole o seguinte código nele:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-macos/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]
1. Adicione o caminho `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` relativo ao cabeçalho de ponte às configurações do projeto Swift para o destino HelloWorld nas propriedades do cabeçalho do campo ![de cabeçalho de *ponte Objective-C*](media/sdk/qs-swift-macos-bridging-header.png)
1. Substitua o conteúdo do ficheiro `AppDelegate.swift` gerado automaticamente por:

   [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-macos/helloworld/helloworld/AppDelegate.swift#code)]
1. No `AppDelegate.swift`, substitua a cadeia `YourSubscriptionKey` de caracteres pela sua chave de assinatura.
1. Substitua a cadeia `YourServiceRegion` de caracteres pela região associada à sua assinatura (por exemplo `westus` , para a assinatura de avaliação gratuita).

## <a name="install-the-sdk-as-a-cocoapod"></a>Instalar o SDK como um CocoaPod

1. Instale o Gerenciador de dependência do CocoaPod conforme descrito em suas [instruções de instalação](https://guides.cocoapods.org/using/getting-started.html).
1. Navegue até o diretório do aplicativo de exemplo (`helloworld`). Coloque um arquivo de texto com o `Podfile` nome e o seguinte conteúdo nesse diretório:

   [!code-ruby[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-macos/helloworld/Podfile)]
1. Navegue até o `helloworld` diretório em um terminal e execute o comando `pod install`. Isso irá gerar um `helloworld.xcworkspace` espaço de trabalho do Xcode contendo o aplicativo de exemplo e o SDK de fala como uma dependência. Este espaço de trabalho será usado no seguinte.

## <a name="build-and-run-the-sample"></a>Criar e executar o exemplo

1. Abra o `helloworld.xcworkspace` espaço de trabalho no Xcode.
1. Torne a saída de depuração visível (**View (Ver)**  > **Debug Area (Área de Depuração)**  > **Activate Console (Ativar Consola)** ).
1. Crie e execute o código de exemplo selecionando**executar** **produto** > no menu ou clicando no botão **reproduzir** .
1. Depois de clicar no botão "reconhecer" no aplicativo e dizer algumas palavras, você deverá ver o texto falado na parte inferior da janela do aplicativo.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Explore nossos exemplos no GitHub](https://aka.ms/csspeech/samples)

