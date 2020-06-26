---
title: 'Quickstart: Reconhecer a fala a partir de um microfone, Objective-C - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: Aprenda a reconhecer a fala no Objective-C sobre o macOS utilizando o Discurso SDK
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 06/25/2020
ms.author: chlandsi
ms.openlocfilehash: 8f94f2ed810204d739dfd2e6d5e88ef0977ad9a4
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85391524"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-macos-by-using-the-speech-sdk"></a>Quickstart: Reconhecer a fala no Objective-C sobre o macOS utilizando o SDK de discurso

Os quickstarts também estão disponíveis para [síntese de fala.](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/objectivec-macos.md)

Neste artigo, aprende-se a criar uma aplicação para macOS no Objective-C, utilizando o Azure Cognitive Services Speech SDK para transcrever a fala gravada de um microfone para texto.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, vai precisar:

* Uma [chave de subscrição](~/articles/cognitive-services/Speech-Service/get-started.md) para o serviço Discurso.
* Uma máquina macOS com [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) ou mais tarde e o macOS 10.13 ou mais tarde.

## <a name="get-the-speech-sdk-for-macos"></a>Obtenha o SDK de discurso para macOS

[!INCLUDE [License notice](~/includes/cognitive-services-speech-service-license-notice.md)]

O SDK de Discurso de Serviços Cognitivos para Mac é distribuído como um pacote de quadros. Pode ser usado em projetos Xcode como um [Cacau](https://cocoapods.org/) ou descarregado https://aka.ms/csspeech/macosbinary e ligado manualmente. Este artigo usa um Cacau.

## <a name="create-an-xcode-project"></a>Criar um projeto Xcode

Inicie o Xcode e inicie um novo projeto selecionando **o File**  >  **New**  >  **Project**. Na caixa de diálogo de seleção do modelo, selecione o modelo **de App Cocoa.**

Nas caixas de diálogo que se seguem, faça as seguintes seleções.

1. Na caixa de diálogo **'Opções de Projecto':**
    1. Insira um nome para a aplicação quickstart, por exemplo, *helloworld*.
    1. Insira um nome de organização apropriado e um identificador de organização se já tiver uma conta de desenvolvedor da Apple. Para efeitos de teste, utilize um nome como *testorg*. Para assinar a aplicação, precisa de um perfil de provisionamento adequado. Para mais informações, consulte o [site de desenvolvedores da Apple.](https://developer.apple.com/)
    1. Certifique-se de que o **Objective-C** é selecionado como o idioma do projeto.
    1. Limpe as caixas de verificação para usar storyboards e criar uma aplicação baseada em documentos. A UI simples para a aplicação da amostra é criada programáticamente.
    1. Limpe todas as caixas de verificação para testes e dados principais.

    ![Definições de projeto](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-project-settings.png)

1. Selecione um diretório de projeto:
    1. Escolha um diretório para colocar o projeto. Este passo cria um diretório helloworld no seu diretório de casa que contém todos os ficheiros para o projeto Xcode.
    1. Desative a criação de um repositório Git para este projeto de exemplo.
1. Desa estade os direitos de acesso à rede e ao microfone. Selecione o nome da aplicação na primeira linha na visão geral da esquerda para chegar à configuração da aplicação. Em seguida, selecione o separador **Capabilites.**
    1. Ativar a definição da **caixa de areia app** para a aplicação.
    1. Selecione as caixas de verificação para **ligações de saída** e acesso **ao microfone.**

    ![Configurações de caixa de areia](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-sandbox.png)

1. A aplicação também precisa de declarar o uso do microfone no `Info.plist` ficheiro. Selecione o ficheiro na visão geral e adicione a tecla **Privacidade - Descrição da utilização do microfone** com um valor como microfone para reconhecimento de *voz*.

    ![Definições em Info.plist](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-info-plist.png)

1. Feche o projeto Xcode. Usa-se uma instância diferente depois de configurar os CocoaPods.

## <a name="install-the-sdk-as-a-cocoapod"></a>Instale o SDK como um Cacau

1. Instale o gestor de dependência do Cacau conforme descrito nas suas [instruções de instalação](https://guides.cocoapods.org/using/getting-started.html).
1. Vá ao diretório da sua aplicação de amostras, que é a Helloworld. Coloque um ficheiro de texto com o nome *Podfile* e o seguinte conteúdo nesse diretório:

   [!code-ruby[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/from-microphone/helloworld/Podfile)]
1. Vá ao diretório helloworld em um terminal, e executar o comando `pod install` . Este comando gera um `helloworld.xcworkspace` espaço de trabalho Xcode que contém tanto a aplicação da amostra como o SDK do discurso como uma dependência. Este espaço de trabalho é utilizado nos seguintes passos.

## <a name="add-the-sample-code"></a>Adicionar o código de exemplo

1. Abra o espaço de trabalho `helloworld.xcworkspace` em Xcode.
1. Substitua o conteúdo do ficheiro autogerado `AppDelegate.m` pelo seguinte código:

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/from-microphone/helloworld/helloworld/AppDelegate.m#code)]
1. Substitua a cadeia de carateres `YourSubscriptionKey` pela sua chave de subscrição.
1. Substitua a cadeia `YourServiceRegion` pela [região](~/articles/cognitive-services/Speech-Service/regions.md) associada à sua subscrição. Por exemplo, utilize `westus` para a subscrição de teste gratuito.

## <a name="build-and-run-the-sample"></a>Criar e executar o exemplo

1. Tornar visível a saída de depuramento selecionando **a**  >  Consola Ativada da Área de**Debug**  >  **vervisão.**
1. Construa e execute o código de exemplo selecionando **o Produto**  >  **Executar** a partir do menu. Também pode selecionar **Reproduzir**.
1. Depois de selecionar o botão e dizer algumas palavras, deve ver o texto que falou na parte inferior do ecrã. Quando executar a aplicação pela primeira vez, deve ser solicitado a dar acesso à aplicação ao microfone do seu computador.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Explore amostras objectiva-C no GitHub](https://aka.ms/csspeech/samples)
