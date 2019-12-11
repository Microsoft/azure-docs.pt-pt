---
title: 'Início rápido: sintetizar a fala, o Objective-C-Speech Service'
titleSuffix: Azure Cognitive Services
description: Saiba como sintetizar a fala em Objective-C no macOS usando o SDK de fala
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: yulili
ms.openlocfilehash: 37eed03ed839411f1acf5d963d4118a3c6d2c379
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975929"
---
# <a name="quickstart-synthesize-speech-in-objective-c-on-macos-using-the-speech-sdk"></a>Início rápido: sintetizar a fala em Objective-C no macOS usando o SDK de fala

Neste artigo, você aprenderá a criar um aplicativo macOS em Objective-C usando o SDK de fala de serviços cognitivas para sintetizar a fala a partir do texto e reproduzi-lo com a saída de áudio padrão.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, eis uma lista de pré-requisitos:

* Uma [chave de assinatura](~/articles/cognitive-services/Speech-Service/get-started.md) para o serviço de fala
* Uma máquina macOS com o [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) ou posterior e o MacOS 10,13 ou posterior

## <a name="get-the-speech-sdk-for-macos"></a>Obtenha o SDK de fala para macOS

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Observe que este tutorial não funcionará com a versão do SDK anterior à 1.7.0.

O SDK de fala de serviços cognitivas para Mac é distribuído como um pacote de estrutura.
Ele pode ser usado em projetos do Xcode como um [CocoaPod](https://cocoapods.org/)ou baixado de https://aka.ms/csspeech/macosbinary e vinculado manualmente. Este guia usa um CocoaPod.

## <a name="create-an-xcode-project"></a>Criar um projeto do Xcode

Inicie o Xcode e um novo projeto ao clicar em **File (Ficheiro)**  > **New (Novo)**  > **Project (Projeto)** .
Na caixa de diálogo seleção de modelo, escolha o modelo "aplicativo Cocoa".

Nas caixas de diálogo que se seguem, faça as seleções seguintes:

1. Caixa de Diálogo Project Options (Opções do Projeto)
    1. Introduza um nome para a aplicação de início rápido, por exemplo `helloworld`.
    1. Insira um nome de organização apropriado e um identificador de organização, se você já tiver uma conta de desenvolvedor da Apple. Para fins de teste, pode escolher qualquer nome, como `testorg`. Para assinar o aplicativo, você precisa de um perfil de provisionamento adequado. Consulte o [site do desenvolvedor da Apple](https://developer.apple.com/) para obter detalhes.
    1. Certifique-se de que escolhe Objective-C como linguagem para o projeto.
    1. Desabilite as caixas de seleção para usar storyboards e criar um aplicativo baseado em documento. A interface do usuário simples para o aplicativo de exemplo será criada programaticamente.
    1. Desative todas as caixas de verificação para testes e dados principais.
    ![Project Settings](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-project-settings.png) (Definições do Projeto)
1. Selecionar o diretório do projeto
    1. Escolha um diretório no qual colocar o projeto. Isso cria um diretório `helloworld` em seu diretório base que contém todos os arquivos do projeto Xcode.
    1. Desative a criação de um repositório Git para este projeto de exemplo.
1. Defina os direitos de acesso à rede. Clique no nome do aplicativo na primeira linha da visão geral à esquerda para acessar a configuração do aplicativo e, em seguida, escolha a guia "recursos".
    1. Habilite a configuração "área restrita do aplicativo" para o aplicativo.
    1. Habilite as caixas de seleção para o acesso "conexões de saída".
    ![configurações da área restrita](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-sandbox-tts.png)
1. Feche o projeto Xcode. Você usará uma instância diferente dela posteriormente depois de configurar o CocoaPods.

## <a name="install-the-sdk-as-a-cocoapod"></a>Instalar o SDK como um CocoaPod

1. Instale o Gerenciador de dependência do CocoaPod conforme descrito em suas [instruções de instalação](https://guides.cocoapods.org/using/getting-started.html).
1. Navegue até o diretório do aplicativo de exemplo (`helloworld`). Coloque um arquivo de texto com o nome `Podfile` e o seguinte conteúdo nesse diretório:  
   [!code-ruby[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/text-to-speech/helloworld/Podfile)]
1. Navegue até o diretório `helloworld` em um terminal e execute o comando `pod install`. Isso irá gerar um espaço de trabalho `helloworld.xcworkspace` Xcode contendo o aplicativo de exemplo e o SDK de fala como uma dependência. Este espaço de trabalho será usado no seguinte.

## <a name="add-the-sample-code"></a>Adicionar o código de exemplo

1. Abra o espaço de trabalho `helloworld.xcworkspace` no Xcode.
1. Substitua o conteúdo do ficheiro `AppDelegate.m` gerado automaticamente por:  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/text-to-speech/helloworld/helloworld/AppDelegate.m#code)]
1. Substitua a cadeia de carateres `YourSubscriptionKey` pela sua chave de subscrição.
1. Substitua a cadeia de carateres `YourServiceRegion` pela [região](~/articles/cognitive-services/Speech-Service/regions.md) associada à subscrição (por exemplo, `westus` para a subscrição de avaliação gratuita).

## <a name="build-and-run-the-sample"></a>Criar e executar o exemplo

1. Torne a saída de depuração visível (**View (Ver)**  > **Debug Area (Área de Depuração)**  > **Activate Console (Ativar Consola)** ).
1. Crie e execute o código de exemplo selecionando **produto** -> **executar** no menu ou clicando no botão **reproduzir** .
1. Depois de inserir algum texto e clicar no botão no aplicativo, você deverá ouvir o áudio sintetizado reproduzido.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Explorar exemplos de Objective-C no GitHub](https://aka.ms/csspeech/samples)

