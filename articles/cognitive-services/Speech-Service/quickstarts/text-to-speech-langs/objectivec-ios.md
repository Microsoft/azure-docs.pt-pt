---
title: 'Quickstart: Sintetizar o discurso, Objective-C - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: Saiba como sintetizar o discurso em Objective-C no iOS usando o SDK do discurso
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 06/25/2020
ms.author: yulili
ms.openlocfilehash: 7a1cc2c45988e0c5a8c757e4d280b48dbbe56daa
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85391388"
---
# <a name="quickstart-synthesize-speech-in-objective-c-on-ios-using-the-speech-sdk"></a>Quickstart: Sintetizar o discurso em Objective-C no iOS usando o SDK de discurso

Neste artigo, aprende-se a criar uma aplicação iOS no Objective-C utilizando o SDK de Discurso dos Serviços Cognitivos para sintetizar o discurso a partir de texto.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, aqui está uma lista de pré-requisitos:

* Uma [chave de subscrição](~/articles/cognitive-services/Speech-Service/get-started.md) para o serviço Speech
* Uma máquina macOS com [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) ou mais tarde
* O objetivo definido para a versão 9.3 do iOS ou mais tarde

## <a name="get-the-speech-sdk-for-ios"></a>Obter o SDK de Voz para iOS

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Note que este tutorial não funcionará com a versão do SDK antes de 1.7.0.

O Discurso dos Serviços Cognitivos SDK para iOS está atualmente distribuído como Um Quadro de Cacau.
Pode ser usado em projetos Xcode como um [Cacau,](https://cocoapods.org/)ou descarregado https://aka.ms/csspeech/iosbinary e ligado manualmente. Este guia usa um Cacau.

## <a name="create-an-xcode-project"></a>Criar um Projeto Xcode

Inicie o Xcode e inicie um novo projeto clicando **em File**  >  **New**  >  **Project**.
Na caixa de diálogo de seleção do modelo, selecione o modelo "iOS Single View App" (Vista de Aplicação Única de iOS).

Nas caixas de diálogo que se seguem, faça as seleções seguintes:

1. Caixa de Diálogo Project Options (Opções do Projeto)
    1. Introduza um nome para a aplicação de início rápido, por exemplo `helloworld`.
    1. Introduza um nome de organização adequado e o identificador da organização, se já tiver uma conta de programador da Apple. Para fins de teste, pode escolher qualquer nome, como `testorg`. Para assinar a aplicação, precisa de um perfil de provisionamento adequado. Consulte o [site de desenvolvedores](https://developer.apple.com/) da Apple para obter mais detalhes.
    1. Certifique-se de que escolhe Objective-C como linguagem para o projeto.
    1. Desative todas as caixas de verificação para testes e dados principais.
    ![Project Settings](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-project-settings.png) (Definições do Projeto)
1. Selecionar o diretório do projeto
    1. Escolha o diretório raiz onde colocar o projeto. Isto cria um `helloworld` diretório no seu diretório de casa que contém todos os ficheiros para o projeto Xcode.
    1. Desative a criação de um repositório Git para este projeto de exemplo.

## <a name="install-the-sdk-as-a-cocoapod"></a>Instale o SDK como um Cacau

1. Instale o gestor de dependência do Cacau conforme descrito nas suas [instruções de instalação](https://guides.cocoapods.org/using/getting-started.html).
1. Navegue para o diretório da sua aplicação de amostras `helloworld` (). Coloque um ficheiro de texto com o nome `Podfile` e o seguinte conteúdo nesse diretório:  
   [!code-ruby[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/text-to-speech/helloworld/Podfile)]
1. Navegue até ao `helloworld` diretório num terminal e desave o comando `pod install` . Isto irá gerar um `helloworld.xcworkspace` espaço de trabalho Xcode contendo tanto a aplicação da amostra como o SDK do Discurso como uma dependência. Este espaço de trabalho será utilizado no seguinte.

## <a name="add-the-sample-code"></a>Adicionar o código de exemplo

1. Abra o `helloworld.xcworkspace` espaço de trabalho em Xcode.
1. Substitua o conteúdo do ficheiro `AppDelegate.m` gerado automaticamente por:  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/text-to-speech/helloworld/helloworld/AppDelegate.m#code)]
1. Substitua o conteúdo do ficheiro `ViewController.m` gerado automaticamente por:  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/text-to-speech/helloworld/helloworld/ViewController.m#code)]
1. Substitua a cadeia de carateres `YourSubscriptionKey` pela sua chave de subscrição.
1. Substitua a cadeia de carateres `YourServiceRegion` pela [região](~/articles/cognitive-services/Speech-Service/regions.md) associada à subscrição (por exemplo, `westus` para a subscrição de avaliação gratuita).

## <a name="build-and-run-the-sample"></a>Criar e executar o exemplo

1. Tornar visível a saída de depurar **(Ver**  >  **Debug Area**  >  **Activate Console).**
1. Escolha o simulador iOS ou um dispositivo iOS ligado à sua máquina de desenvolvimento como destino para a aplicação a partir da lista no menu **Destino do Produto.**  >  **Destination**
1. Construa e execute o código de exemplo no simulador iOS selecionando **o Produto**  >  **Run** a partir do menu ou clicando no botão **Reproduzir.**

   ![Aplicação iOS Simulada](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-simulated-app-tts.png)

1. Depois de inserir algum texto e clicar no botão na aplicação, deverá ouvir o áudio sintetizado reproduzido.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Explore amostras objectiva-C no GitHub](https://aka.ms/csspeech/samples)

