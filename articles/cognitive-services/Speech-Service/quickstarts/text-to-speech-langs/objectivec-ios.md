---
title: 'Início rápido: sintetizar a fala, o Objective-C-Speech Service'
titleSuffix: Azure Cognitive Services
description: Saiba como sintetizar a fala em Objective-C no iOS usando o SDK de fala
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: yulili
ms.openlocfilehash: 1f5a569f9c3bfa91b78c836e37a22642238674be
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975946"
---
# <a name="quickstart-synthesize-speech-in-objective-c-on-ios-using-the-speech-sdk"></a>Início rápido: sintetizar a fala em Objective-C no iOS usando o SDK de fala

Neste artigo, você aprende a criar um aplicativo iOS em Objective-C usando o SDK de fala dos serviços cognitivas para sintetizar a fala a partir do texto.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, eis uma lista de pré-requisitos:

* Uma [chave de assinatura](~/articles/cognitive-services/Speech-Service/get-started.md) para o serviço de fala
* Um computador macOS com [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) ou posterior
* O destino definido como iOS versão 9,3 ou posterior

## <a name="get-the-speech-sdk-for-ios"></a>Obter o SDK de Voz para iOS

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Observe que este tutorial não funcionará com a versão do SDK anterior à 1.7.0.

O SDK de fala dos serviços cognitivas para iOS está atualmente distribuído como uma estrutura Cocoa.
Ele pode ser usado em projetos do Xcode como um [CocoaPod](https://cocoapods.org/)ou baixado de https://aka.ms/csspeech/iosbinary e vinculado manualmente. Este guia usa um CocoaPod.

## <a name="create-an-xcode-project"></a>Criar um Projeto Xcode

Inicie o Xcode e um novo projeto ao clicar em **File (Ficheiro)**  > **New (Novo)**  > **Project (Projeto)** .
Na caixa de diálogo de seleção do modelo, selecione o modelo "iOS Single View App" (Vista de Aplicação Única de iOS).

Nas caixas de diálogo que se seguem, faça as seleções seguintes:

1. Caixa de Diálogo Project Options (Opções do Projeto)
    1. Introduza um nome para a aplicação de início rápido, por exemplo `helloworld`.
    1. Introduza um nome de organização adequado e o identificador da organização, se já tiver uma conta de programador da Apple. Para fins de teste, pode escolher qualquer nome, como `testorg`. Para assinar o aplicativo, você precisa de um perfil de provisionamento adequado. Consulte o [site do desenvolvedor da Apple](https://developer.apple.com/) para obter detalhes.
    1. Certifique-se de que escolhe Objective-C como linguagem para o projeto.
    1. Desative todas as caixas de verificação para testes e dados principais.
    ![Project Settings](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-project-settings.png) (Definições do Projeto)
1. Selecionar o diretório do projeto
    1. Escolha o diretório raiz onde colocar o projeto. Isso cria um diretório `helloworld` em seu diretório base que contém todos os arquivos do projeto Xcode.
    1. Desative a criação de um repositório Git para este projeto de exemplo.

## <a name="install-the-sdk-as-a-cocoapod"></a>Instalar o SDK como um CocoaPod

1. Instale o Gerenciador de dependência do CocoaPod conforme descrito em suas [instruções de instalação](https://guides.cocoapods.org/using/getting-started.html).
1. Navegue até o diretório do aplicativo de exemplo (`helloworld`). Coloque um arquivo de texto com o nome `Podfile` e o seguinte conteúdo nesse diretório:  
   [!code-ruby[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/text-to-speech/helloworld/Podfile)]
1. Navegue até o diretório `helloworld` em um terminal e execute o comando `pod install`. Isso irá gerar um espaço de trabalho `helloworld.xcworkspace` Xcode contendo o aplicativo de exemplo e o SDK de fala como uma dependência. Este espaço de trabalho será usado no seguinte.

## <a name="add-the-sample-code"></a>Adicionar o código de exemplo

1. Abra o espaço de trabalho `helloworld.xcworkspace` no Xcode.
1. Substitua o conteúdo do ficheiro `AppDelegate.m` gerado automaticamente por:  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/text-to-speech/helloworld/helloworld/AppDelegate.m#code)]
1. Substitua o conteúdo do ficheiro `ViewController.m` gerado automaticamente por:  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/text-to-speech/helloworld/helloworld/ViewController.m#code)]
1. Substitua a cadeia de carateres `YourSubscriptionKey` pela sua chave de subscrição.
1. Substitua a cadeia de carateres `YourServiceRegion` pela [região](~/articles/cognitive-services/Speech-Service/regions.md) associada à subscrição (por exemplo, `westus` para a subscrição de avaliação gratuita).

## <a name="build-and-run-the-sample"></a>Criar e executar o exemplo

1. Torne a saída de depuração visível (**View (Ver)**  > **Debug Area (Área de Depuração)**  > **Activate Console (Ativar Consola)** ).
1. Escolha o simulador de iOS ou um dispositivo iOS conectado à sua máquina de desenvolvimento como o destino para o aplicativo na lista no menu de **destino** **produto** > .
1. Crie e execute o código de exemplo no simulador iOS ao selecionar **Product (Produto)**  > **Run (Executar)** no menu ou ao clicar no botão **Play (Reproduzir)** .

   ![Aplicação iOS Simulada](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-simulated-app-tts.png)

1. Depois de inserir algum texto e clicar no botão no aplicativo, você deverá ouvir o áudio sintetizado reproduzido.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Explorar exemplos de Objective-C no GitHub](https://aka.ms/csspeech/samples)

