---
title: 'Quickstart: Reconhecer discurso a partir de um microfone, Objective-C - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: Saiba reconhecer o discurso no ObjectivoC sobre o macOS utilizando o SDK da fala
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/23/2019
ms.author: chlandsi
ms.openlocfilehash: c2f0fbe66b26c6eca6e0c0b2530efacba9bae958
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75380597"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-macos-by-using-the-speech-sdk"></a>Quickstart: Reconhecer discurso no ObjectivoC sobre o macOS utilizando o SDK da fala

Os quickstarts também estão disponíveis para [síntese de fala.](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/objectivec-macos.md)

Neste artigo, aprende-se a criar uma aplicação macOS no Objective-C utilizando o Azure Cognitive Services Speech SDK para transcrever a fala gravada de um microfone para texto.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, vai precisar de:

* Uma [chave de subscrição](~/articles/cognitive-services/Speech-Service/get-started.md) para o serviço De Fala.
* Uma máquina macOS com [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) ou mais tarde e macOS 10.13 ou posterior.

## <a name="get-the-speech-sdk-for-macos"></a>Obtenha o SDK de Fala para macOS

[!INCLUDE [License notice](~/includes/cognitive-services-speech-service-license-notice.md)]

O SDK de Discurso dos Serviços Cognitivos para Mac é distribuído como um pacote de quadros. Pode ser usado em projetos Xcode como um https://aka.ms/csspeech/macosbinary [CocoaPod](https://cocoapods.org/) ou descarregado de e ligado manualmente. Este artigo usa um CocoaPod.

## <a name="create-an-xcode-project"></a>Criar um projeto Xcode

Inicie o Xcode e inicie um novo projeto selecionando **File** > **New** > **Project**. Na caixa de diálogo de seleção do modelo, selecione o modelo da **App cacau.**

Nas caixas de diálogo que se seguem, faça as seguintes seleções.

1. Na caixa de diálogo **Opções de Projeto:**
    1. Introduza um nome para a aplicação quickstart, por exemplo, *helloworld*.
    1. Introduza um nome de organização apropriado e um identificador de organização se já tiver uma conta de desenvolvimento da Apple. Para efeitos de teste, utilize um nome como *testorg*. Para assinar a aplicação, precisa de um perfil de provisionamento adequado. Para mais informações, consulte o site de desenvolvimento da [Apple](https://developer.apple.com/).
    1. Certifique-se de que o **Objectivo-C** é selecionado como o idioma do projeto.
    1. Limpe as caixas de verificação para usar storyboards e para criar uma aplicação baseada em documentos. A simples UI para a aplicação de amostras é criada programáticamente.
    1. Limpe todas as caixas de verificação para testes e dados fundamentais.

    ![Configurações do projeto](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-project-settings.png)

1. Selecione um diretório de projeto:
    1. Escolha um diretório para colocar o projeto. Este passo cria um diretório helloworld no seu diretório doméstico que contém todos os ficheiros para o projeto Xcode.
    1. Desative a criação de um repositório Git para este projeto de exemplo.
1. Detete os direitos de acesso à rede e ao microfone. Selecione o nome da aplicação na primeira linha na visão geral à esquerda para chegar à configuração da aplicação. Em seguida, selecione o separador **Capabilities.**
    1. Ative a definição de **caixa de areia** da App para a aplicação.
    1. Selecione as caixas de verificação para **ligações de saída** e acesso ao **microfone.**

    ![Definições de caixa de areia](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-sandbox.png)

1. A aplicação também precisa de declarar `Info.plist` a utilização do microfone no ficheiro. Selecione o ficheiro na visão geral e adicione a tecla Descrição de Descrição privacidade **- Utilização** do microfone com um valor como *microfone para reconhecimento*de voz .

    ![Definições em Info.plist](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-info-plist.png)

1. Feche o projeto Xcode. Usa-se uma instância diferente depois de configurar os CocoaPods.

## <a name="install-the-sdk-as-a-cocoapod"></a>Instale o SDK como um CocoaPod

1. Instale o gestor de dependência do CocoaPod conforme descrito nas suas instruções de [instalação](https://guides.cocoapods.org/using/getting-started.html).
1. Vá ao diretório da sua aplicação de amostras, que é helloworld. Coloque um ficheiro de texto com o nome *Podfile* e o seguinte conteúdo nesse diretório:

   [!code-ruby[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/from-microphone/helloworld/Podfile)]
1. Vá ao diretório helloworld em um terminal, e dirija o comando. `pod install` Este comando gera `helloworld.xcworkspace` um espaço de trabalho Xcode que contém tanto a aplicação da amostra como o SDK de Fala como uma dependência. Este espaço de trabalho é utilizado nos seguintes passos.

## <a name="add-the-sample-code"></a>Adicionar o código de exemplo

1. Abra o `helloworld.xcworkspace` espaço de trabalho em Xcode.
1. Substitua o conteúdo do `AppDelegate.m` ficheiro autogerado pelo seguinte código:

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/from-microphone/helloworld/helloworld/AppDelegate.m#code)]
1. Substitua a cadeia de carateres `YourSubscriptionKey` pela sua chave de subscrição.
1. Substitua `YourServiceRegion` a cadeia pela [região](~/articles/cognitive-services/Speech-Service/regions.md) associada à sua subscrição. Por exemplo, `westus` utilize para a subscrição de teste gratuito.

## <a name="build-and-run-the-sample"></a>Criar e executar o exemplo

1. Tornar a saída de depuração visível selecionando a**consola de ativação**de área de**depuração** > do ponto de **vista** > .
1. Construa e execute o código de exemplo selecionando o **Product** > **Run** a partir do menu. Também pode selecionar **Play**.
1. Depois de selecionar o botão e dizer algumas palavras, deve ver o texto que falou na parte inferior do ecrã. Quando executar a aplicação pela primeira vez, deve ser solicitado a dar acesso à aplicação ao microfone do seu computador.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Explore as amostras do Objectivo-C no GitHub](https://aka.ms/csspeech/samples)
