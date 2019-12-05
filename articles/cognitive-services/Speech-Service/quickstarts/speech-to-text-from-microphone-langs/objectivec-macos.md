---
title: 'Início rápido: reconhecer a fala de um microfone, o serviço Objective-C-Speech'
titleSuffix: Azure Cognitive Services
description: Saiba como reconhecer a fala em Objective-C no macOS usando o SDK de fala
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: be5f62a75d9a3998d843e9865768b46658b8386c
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815023"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-macos-by-using-the-speech-sdk"></a>Início rápido: reconhecer a fala em Objective-C no macOS usando o SDK de fala

Os guias de início rápido também estão disponíveis para [síntese de fala](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/objectivec-macos.md).

Neste artigo, você aprende a criar um aplicativo macOS em Objective-C usando o SDK de fala dos serviços cognitivas do Azure para transcrever a fala registrada de um microfone para texto.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você precisará de:

* Uma [chave de assinatura](~/articles/cognitive-services/Speech-Service/get-started.md) para o serviço de fala.
* Uma máquina macOS com o [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) ou posterior e o MacOS 10,13 ou posterior.

## <a name="get-the-speech-sdk-for-macos"></a>Obtenha o SDK de fala para macOS

[!INCLUDE [License notice](~/includes/cognitive-services-speech-service-license-notice.md)]

O SDK de fala de serviços cognitivas para Mac é distribuído como um pacote de estrutura. Ele pode ser usado em projetos do Xcode como um [CocoaPod](https://cocoapods.org/) ou baixado do https://aka.ms/csspeech/macosbinary e vinculado manualmente. Este artigo usa um CocoaPod.

## <a name="create-an-xcode-project"></a>Criar um projeto do Xcode

Inicie o Xcode e inicie um novo projeto selecionando **arquivo** > **novo** **projeto**de > . Na caixa de diálogo seleção de modelo, selecione o modelo de **aplicativo Cocoa** .

Nas caixas de diálogo a seguir, faça as seleções a seguir.

1. Na caixa de diálogo **Opções do projeto** :
    1. Insira um nome para o aplicativo de início rápido, por exemplo, *HelloWorld*.
    1. Insira um nome de organização apropriado e um identificador de organização se você já tiver uma conta de desenvolvedor da Apple. Para fins de teste, use um nome como *testorg*. Para assinar o aplicativo, você precisa de um perfil de provisionamento adequado. Para obter mais informações, consulte o [site do desenvolvedor da Apple](https://developer.apple.com/).
    1. Verifique se **Objective-C** está selecionado como o idioma do projeto.
    1. Desmarque as caixas de seleção para usar storyboards e criar um aplicativo baseado em documento. A interface do usuário simples para o aplicativo de exemplo é criada programaticamente.
    1. Desmarque todas as caixas de seleção dos testes e dos dados principais.

    ![Project settings](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-project-settings.png)

1. Selecione um diretório do projeto:
    1. Escolha um diretório no qual colocar o projeto. Esta etapa cria um diretório HelloWorld em seu diretório base que contém todos os arquivos do projeto Xcode.
    1. Desative a criação de um repositório Git para este projeto de exemplo.
1. Defina os direitos de acesso de rede e microfone. Selecione o nome do aplicativo na primeira linha da visão geral à esquerda para obter a configuração do aplicativo. Em seguida, selecione a guia **recursos** .
    1. Habilite a configuração de **área restrita do aplicativo** para o aplicativo.
    1. Marque as caixas de seleção para **conexões de saída** e acesso ao **microfone** .

    ![Configurações da área restrita](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-sandbox.png)

1. O aplicativo também precisa declarar o uso do microfone no arquivo de `Info.plist`. Selecione o arquivo na visão geral e adicione a chave de **Descrição privacidade-uso do microfone** com um valor como o *microfone é necessário para o reconhecimento de fala*.

    ![Configurações no info. plist](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-info-plist.png)

1. Feche o projeto Xcode. Você usará uma instância diferente dela posteriormente depois de configurar o CocoaPods.

## <a name="install-the-sdk-as-a-cocoapod"></a>Instalar o SDK como um CocoaPod

1. Instale o Gerenciador de dependência do CocoaPod conforme descrito em suas [instruções de instalação](https://guides.cocoapods.org/using/getting-started.html).
1. Vá para o diretório do aplicativo de exemplo, que é HelloWorld. Coloque um arquivo de texto com o nome *Podfile* e o seguinte conteúdo nesse diretório:

   [!code-ruby[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/from-microphone/helloworld/Podfile)]
1. Vá para o diretório HelloWorld em um terminal e execute o comando `pod install`. Esse comando gera um `helloworld.xcworkspace` espaço de trabalho do Xcode que contém o aplicativo de exemplo e o SDK de fala como uma dependência. Esse espaço de trabalho é usado nas etapas a seguir.

## <a name="add-the-sample-code"></a>Adicionar o código de exemplo

1. Abra o `helloworld.xcworkspace` do espaço de trabalho no Xcode.
1. Substitua o conteúdo do arquivo de `AppDelegate.m` gerado automaticamente pelo seguinte código:

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/from-microphone/helloworld/helloworld/AppDelegate.m#code)]
1. Substitua a cadeia de carateres `YourSubscriptionKey` pela sua chave de subscrição.
1. Substitua a cadeia de caracteres `YourServiceRegion` pela [região](~/articles/cognitive-services/Speech-Service/regions.md) associada à sua assinatura. Por exemplo, use `westus` para a assinatura de avaliação gratuita.

## <a name="build-and-run-the-sample"></a>Criar e executar o exemplo

1. Torne a saída de depuração visível selecionando **exibir** > **área de depuração** > **Ativar console**.
1. Crie e execute o código de exemplo selecionando **produto** > **executar** no menu. Você também pode selecionar **reproduzir**.
1. Depois de selecionar o botão e dizer algumas palavras, você deverá ver o texto que você fala na parte inferior da tela. Quando você executa o aplicativo pela primeira vez, deve ser solicitado a conceder ao aplicativo acesso ao microfone do seu computador.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Explorar exemplos de Objective-C no GitHub](https://aka.ms/csspeech/samples)
