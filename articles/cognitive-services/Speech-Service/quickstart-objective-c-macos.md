---
title: 'Início rápido: Reconhecimento de fala, Objective-C-Speech Service'
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
ms.openlocfilehash: f843636b0c4f604af4984c6d75bb6c3cae75d275
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803303"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-macos-using-the-speech-sdk"></a>Início rápido: Reconhecer a fala em Objective-C no macOS usando o SDK de fala

Os guias de início rápido também estão disponíveis para [síntese de fala](quickstart-text-to-speech-objectivec-macos.md).

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Neste artigo, você aprende a criar um aplicativo macOS em Objective-C usando o SDK de fala de serviços cognitivas para transcrever a fala registrada de um microfone para texto.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, eis uma lista de pré-requisitos:

* R [chave de subscrição](get-started.md) para o serviço de voz
* Uma máquina macOS com o [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) ou posterior e o MacOS 10,13 ou posterior

## <a name="get-the-speech-sdk-for-macos"></a>Obtenha o SDK de fala para macOS

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

O SDK de fala de serviços cognitivas para Mac é distribuído como um pacote de estrutura.
Ele pode ser usado em projetos do Xcode como um [CocoaPod](https://cocoapods.org/)ou baixado do https://aka.ms/csspeech/macosbinary e vinculado manualmente. Este guia usa um CocoaPod.

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
    ![Project Settings](media/sdk/qs-objectivec-macos-project-settings.png) (Definições do Projeto)
1. Selecionar o diretório do projeto
    1. Escolha um diretório no qual colocar o projeto. Isso cria um diretório `helloworld` em seu diretório base que contém todos os arquivos do projeto Xcode.
    1. Desative a criação de um repositório Git para este projeto de exemplo.
1. Defina os direitos de acesso de rede e microfone. Clique no nome do aplicativo na primeira linha da visão geral à esquerda para acessar a configuração do aplicativo e, em seguida, escolha a guia "recursos".
    1. Habilite a configuração "área restrita do aplicativo" para o aplicativo.
    1. Habilite as caixas de seleção para o acesso "conexões de saída" e "microfone".
    Configurações de @no__t 0Sandbox @ no__t-1
1. O aplicativo também precisa declarar o uso do microfone no arquivo `Info.plist`. Clique no arquivo na visão geral e adicione a chave "privacidade – descrição de uso do microfone", com um valor como "o microfone é necessário para o reconhecimento de fala".
    ![Settings no info. plist @ no__t-1
1. Feche o projeto Xcode. Você usará uma instância diferente dela posteriormente depois de configurar o CocoaPods.

## <a name="install-the-sdk-as-a-cocoapod"></a>Instalar o SDK como um CocoaPod

1. Instale o Gerenciador de dependência do CocoaPod conforme descrito em suas [instruções de instalação](https://guides.cocoapods.org/using/getting-started.html).
1. Navegue até o diretório do aplicativo de exemplo (`helloworld`). Coloque um arquivo de texto com o nome `Podfile` e o seguinte conteúdo nesse diretório:  
   [!code-ruby[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-macos/helloworld/Podfile)]
1. Navegue até o diretório `helloworld` em um terminal e execute o comando `pod install`. Isso irá gerar um espaço de trabalho `helloworld.xcworkspace` Xcode contendo o aplicativo de exemplo e o SDK de fala como uma dependência. Este espaço de trabalho será usado no seguinte.

## <a name="add-the-sample-code"></a>Adicionar o código de exemplo

1. Abra o espaço de trabalho `helloworld.xcworkspace` no Xcode.
1. Substitua o conteúdo do ficheiro `AppDelegate.m` gerado automaticamente por:  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-macos/helloworld/helloworld/AppDelegate.m#code)]
1. Substitua a cadeia de carateres `YourSubscriptionKey` pela sua chave de subscrição.
1. Substitua a cadeia de carateres `YourServiceRegion` pela [região](regions.md) associada à subscrição (por exemplo, `westus` para a subscrição de avaliação gratuita).

## <a name="build-and-run-the-sample"></a>Criar e executar o exemplo

1. Torne a saída de depuração visível (**View (Ver)**  > **Debug Area (Área de Depuração)**  > **Activate Console (Ativar Consola)** ).
1. Crie e execute o código de exemplo selecionando **produto** > **executar** no menu ou clicando no botão **reproduzir** .
1. Depois de clicar no botão e dizer algumas palavras, você deverá ver o texto que você fala na parte inferior da tela. Quando você executa o aplicativo pela primeira vez, deve ser solicitado a conceder ao aplicativo acesso ao microfone do seu computador.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Explorar exemplos de Objective-C no GitHub](https://aka.ms/csspeech/samples)
