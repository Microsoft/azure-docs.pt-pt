---
title: 'Quickstart: Discurso sintetizador, Objective-C - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: Saiba como sintetizar o discurso no Objective-C sobre iOS usando o SDK da fala
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: yulili
ms.openlocfilehash: 1f5a569f9c3bfa91b78c836e37a22642238674be
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74975946"
---
# <a name="quickstart-synthesize-speech-in-objective-c-on-ios-using-the-speech-sdk"></a>Quickstart: Discurso sintetizador no Objectivo-C sobre iOS utilizando o SDK da fala

Neste artigo, aprende-se a criar uma aplicação iOS no Objective-C utilizando o SDK de Discurso dos Serviços Cognitivos para sintetizar o discurso a partir de texto.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, aqui está uma lista de pré-requisitos:

* Uma [chave de subscrição](~/articles/cognitive-services/Speech-Service/get-started.md) para o serviço Speech
* Uma máquina macOS com [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) ou mais tarde
* O alvo definido para a versão iOS 9.3 ou posterior

## <a name="get-the-speech-sdk-for-ios"></a>Obter o SDK de Voz para iOS

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Note que este tutorial não funcionará com a versão do SDK antes de 1.7.0.

O SDK de Discurso dos Serviços Cognitivos para iOS é atualmente distribuído como quadro de cacau.
Pode ser usado em projetos Xcode como um https://aka.ms/csspeech/iosbinary [CocoaPod,](https://cocoapods.org/)ou descarregado de e ligado manualmente. Este guia utiliza um CocoaPod.

## <a name="create-an-xcode-project"></a>Criar um Projeto Xcode

Inicie o Xcode e inicie um novo projeto clicando em **File** > **New** > **Project**.
Na caixa de diálogo de seleção do modelo, selecione o modelo "iOS Single View App" (Vista de Aplicação Única de iOS).

Nas caixas de diálogo que se seguem, faça as seleções seguintes:

1. Caixa de Diálogo Project Options (Opções do Projeto)
    1. Introduza um nome para a aplicação de início rápido, por exemplo `helloworld`.
    1. Introduza um nome de organização adequado e o identificador da organização, se já tiver uma conta de programador da Apple. Para fins de teste, pode escolher qualquer nome, como `testorg`. Para assinar a aplicação, precisa de um perfil de provisionamento adequado. Consulte o site de desenvolvimento da [Apple](https://developer.apple.com/) para mais detalhes.
    1. Certifique-se de que escolhe Objective-C como linguagem para o projeto.
    1. Desative todas as caixas de verificação para testes e dados principais.
    ![Project Settings](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-project-settings.png) (Definições do Projeto)
1. Selecionar o diretório do projeto
    1. Escolha o diretório raiz onde colocar o projeto. Isto cria `helloworld` um diretório no seu diretório doméstico que contém todos os ficheiros para o projeto Xcode.
    1. Desative a criação de um repositório Git para este projeto de exemplo.

## <a name="install-the-sdk-as-a-cocoapod"></a>Instale o SDK como um CocoaPod

1. Instale o gestor de dependência do CocoaPod conforme descrito nas suas instruções de [instalação](https://guides.cocoapods.org/using/getting-started.html).
1. Navegue para o diretório`helloworld`da sua aplicação de amostras ( ). Coloque um ficheiro de `Podfile` texto com o nome e o seguinte conteúdo nesse diretório:  
   [!code-ruby[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/text-to-speech/helloworld/Podfile)]
1. Navegue `helloworld` para o diretório num `pod install`terminal e dirija o comando. Isto gerará `helloworld.xcworkspace` um espaço de trabalho Xcode contendo tanto a aplicação da amostra como o SDK de Fala como uma dependência. Este espaço de trabalho será utilizado no seguinte.

## <a name="add-the-sample-code"></a>Adicionar o código de exemplo

1. Abra `helloworld.xcworkspace` o espaço de trabalho em Xcode.
1. Substitua o conteúdo do ficheiro `AppDelegate.m` gerado automaticamente por:  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/text-to-speech/helloworld/helloworld/AppDelegate.m#code)]
1. Substitua o conteúdo do ficheiro `ViewController.m` gerado automaticamente por:  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/text-to-speech/helloworld/helloworld/ViewController.m#code)]
1. Substitua a cadeia de carateres `YourSubscriptionKey` pela sua chave de subscrição.
1. Substitua a cadeia de carateres `YourServiceRegion` pela [região](~/articles/cognitive-services/Speech-Service/regions.md) associada à subscrição (por exemplo, `westus` para a subscrição de avaliação gratuita).

## <a name="build-and-run-the-sample"></a>Criar e executar o exemplo

1. Tornar visível a saída de depuração **(Ver** > **debug Area** > **Activate Console**).
1. Escolha o simulador iOS ou um dispositivo iOS ligado à sua máquina de desenvolvimento como destino da aplicação a partir da lista no menu**Destino** do **Produto.** > 
1. Construa e execute o código de exemplo no simulador iOS selecionando o **Product** > **Run** a partir do menu ou clicando no botão **Reproduzir.**

   ![Aplicação iOS Simulada](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-simulated-app-tts.png)

1. Depois de inserir algum texto e clicar no botão da aplicação, deve ouvir o áudio sintetizado reproduzido.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Explore as amostras do Objectivo-C no GitHub](https://aka.ms/csspeech/samples)

