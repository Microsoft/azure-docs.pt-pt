---
title: 'Início rápido: Reconhecer a conversão de voz, Objective-C - serviços de voz'
titleSuffix: Azure Cognitive Services
description: Aprender a reconhecer a conversão de voz no Objective-C no macOS com o SDK de voz
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/03/2019
ms.author: chlandsi
ms.openlocfilehash: 3eb65b5d41317a4cfc31917a4b08d931ba9c7aa6
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466618"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-macos-using-the-speech-sdk"></a>Início rápido: Reconhecer a conversão de voz no Objective-C no macOS com o SDK de voz

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Neste artigo, saiba como criar uma aplicação do macOS no Objective-C com o SDK de voz dos serviços cognitivos para transcrição de voz gravada usando um microfone para texto.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, eis uma lista de pré-requisitos:

* R [chave de subscrição](get-started.md) para o serviço de voz
* Um computador macOS com [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) ou posterior e o macOS 10.13 ou posterior

## <a name="get-the-speech-sdk-for-macos"></a>Obtenha o SDK de voz para macOS

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

A versão atual do SDK de Voz dos Serviços Cognitivos é `1.5.1`.

O SDK de voz dos serviços cognitivos para Mac é distribuído como um pacote do framework.
Ele pode ser usado em projetos do Xcode como um [CocoaPod](https://cocoapods.org/), ou transferidas a partir https://aka.ms/csspeech/macosbinary e ligada manualmente. Este guia utiliza um CocoaPod.

## <a name="create-an-xcode-project"></a>Criar um projeto do Xcode

Inicie o Xcode e um novo projeto ao clicar em **File (Ficheiro)**  > **New (Novo)**  > **Project (Projeto)** .
Na caixa de diálogo de seleção de modelo, escolha o modelo de "Cocoa App".

Nas caixas de diálogo que se seguem, faça as seleções seguintes:

1. Caixa de Diálogo Project Options (Opções do Projeto)
    1. Introduza um nome para a aplicação de início rápido, por exemplo `helloworld`.
    1. Introduza um nome de organização apropriadas e um identificador de organização, se já tiver uma conta de programador da Apple. Para fins de teste, pode escolher qualquer nome, como `testorg`. Para assinar a aplicação, terá de um perfil de aprovisionamento correto. Consulte a [site do desenvolvedor Apple](https://developer.apple.com/) para obter detalhes.
    1. Certifique-se de que escolhe Objective-C como linguagem para o projeto.
    1. Desative as caixas de verificação para utilizar storyboards e para criar uma aplicação baseada em documento. A interface do Usuário simple para a aplicação de exemplo será criada por meio de programação.
    1. Desative todas as caixas de verificação para testes e dados principais.
    ![Project Settings](media/sdk/qs-objectivec-macos-project-settings.png) (Definições do Projeto)
1. Selecionar o diretório do projeto
    1. Escolha um diretório para colocar o projeto. Esta ação cria um `helloworld` diretório no seu diretório de raiz que contém todos os ficheiros para o projeto Xcode.
    1. Desative a criação de um repositório Git para este projeto de exemplo.
1. Defina a elegibilidade para o acesso de rede e de microfone. Clique no nome de aplicação na primeira linha na descrição geral do lado esquerdo para ir para a configuração de aplicação e, em seguida, selecione o separador "Capacidades".
    1. Ative a definição de "Área de segurança de aplicação" para a aplicação.
    1. Ative as caixas de verificação para o acesso de "Ligações de saída" e "Microfone".
    ![Definições de proteção de segurança](media/sdk/qs-objectivec-macos-sandbox.png)
1. A aplicação também tem de declarar o uso do microfone no `Info.plist` ficheiro. Clique no ficheiro na descrição geral e adicione a chave de "Descrição da utilização do microfone de – de privacidade", com um valor como "Microfone é necessário para o reconhecimento de fala".
    ![Definições no ficheiro info.](media/sdk/qs-objectivec-macos-info-plist.png)
1. Feche o projeto Xcode. Irá utilizar uma instância diferente do mesmo mais tarde depois de configurar o CocoaPods.

## <a name="install-the-sdk-as-a-cocoapod"></a>Instalar o SDK como um CocoaPod

1. Instalar o Gestor de dependência de CocoaPod, conforme descrito na respetiva [instruções de instalação](https://guides.cocoapods.org/using/getting-started.html).
1. Navegue para o diretório da sua aplicação de exemplo (`helloworld`). Colocar um ficheiro de texto com o nome `Podfile` e nesse diretório, o seguinte conteúdo: [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-macos/helloworld/Podfile)]
1. Navegue para o `helloworld` diretório num terminal e execute o comando `pod install`. Esta ação irá gerar um `helloworld.xcworkspace` área de trabalho do Xcode que contém a aplicação de exemplo e o SDK de voz como uma dependência. Esta área de trabalho será utilizada a seguir.

## <a name="add-the-sample-code"></a>Adicionar o código de exemplo

1. Abra o `helloworld.xcworkspace` área de trabalho no Xcode.
1. Substitua o conteúdo do ficheiro `AppDelegate.m` gerado automaticamente por:  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-macos/helloworld/helloworld/AppDelegate.m#code)]
1. Substitua a cadeia de carateres `YourSubscriptionKey` pela sua chave de subscrição.
1. Substitua a cadeia de carateres `YourServiceRegion` pela [região](regions.md) associada à subscrição (por exemplo, `westus` para a subscrição de avaliação gratuita).

## <a name="build-and-run-the-sample"></a>Criar e executar o exemplo

1. Torne a saída de depuração visível (**View (Ver)**  > **Debug Area (Área de Depuração)**  > **Activate Console (Ativar Consola)** ).
1. Criar e executar o código de exemplo selecionando **produto** > **executar** no menu ou clicando no **reproduzir** botão.
1. Depois de clicar no botão e dizer algumas palavras, deverá ver o texto que tenha se apresentou na parte inferior do ecrã. Quando executar a aplicação pela primeira vez, deverá receber um pedido para dar o acesso ao microfone do seu computador.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Explore exemplos de Objective-C no GitHub](https://aka.ms/csspeech/samples)

