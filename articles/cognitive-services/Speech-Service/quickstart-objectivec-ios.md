---
title: 'Início rápido: Reconhecer a conversão de voz, Objective-C - serviços de voz'
titleSuffix: Azure Cognitive Services
description: Aprender a reconhecer a conversão de voz no Objective-C no iOS com o SDK de voz
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 2/20/2019
ms.author: chlandsi
ms.openlocfilehash: 67acc7a074b26cb7614b5e08d25afd7b7c1caa74
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56958512"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-ios-using-the-speech-sdk"></a>Início rápido: Reconhecer a conversão de voz no Objective-C no iOS com o SDK de voz

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Neste artigo, vai aprender a criar uma aplicação iOS em Objective-C com o SDK de Voz dos Serviços Cognitivos para fazer a conversão de voz em texto de um ficheiro de áudio.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, eis uma lista de pré-requisitos:

* R [chave de subscrição](get-started.md) para o serviço de voz
* Um computador macOS com [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) ou posterior
* O destino definido como o iOS versão 11.4 ou posterior

## <a name="get-the-speech-sdk-for-ios"></a>Obter o SDK de Voz para iOS

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

A versão atual do SDK de Voz dos Serviços Cognitivos é `1.3.1`.

O SDK de Voz dos Serviços Cognitivos para Mac e iOS é atualmente distribuído como uma Arquitetura Cocoa.
Pode ser transferido a partir de https://aka.ms/csspeech/iosbinary. Transfira o ficheiro para o diretório raiz.

## <a name="create-an-xcode-project"></a>Criar um Projeto Xcode

Inicie o Xcode e um novo projeto ao clicar em **File (Ficheiro)** > **New (Novo)** > **Project (Projeto)**.
Na caixa de diálogo de seleção do modelo, selecione o modelo "iOS Single View App" (Vista de Aplicação Única de iOS).

Nas caixas de diálogo que se seguem, faça as seleções seguintes:

1. Caixa de Diálogo Project Options (Opções do Projeto)
    1. Introduza um nome para a aplicação de início rápido, por exemplo `helloworld`.
    1. Introduza um nome de organização adequado e o identificador da organização, se já tiver uma conta de programador da Apple. Para fins de teste, pode escolher qualquer nome, como `testorg`. Para assinar a aplicação, terá de um perfil de aprovisionamento correto. Consulte a [site do desenvolvedor Apple](https://developer.apple.com/) para obter detalhes.
    1. Certifique-se de que escolhe Objective-C como linguagem para o projeto.
    1. Desative todas as caixas de verificação para testes e dados principais.
    ![Project Settings](media/sdk/qs-objectivec-project-settings.png) (Definições do Projeto)
1. Selecionar o diretório do projeto
    1. Escolha o diretório raiz onde colocar o projeto. Esta ação cria um `helloworld` diretório no seu diretório de raiz que contém todos os ficheiros para o projeto Xcode.
    1. Desative a criação de um repositório Git para este projeto de exemplo.
    1. Ajuste os caminhos para o SDK em *Project Settings* (Definições do Projeto).
        1. Na **gerais** separador sob a **binários incorporados** cabeçalho, adicionar a biblioteca do SDK como uma estrutura: **Adicionar binários incorporados** > **adicionar outro...**  > Navegar para o diretório raiz e escolha o ficheiro `MicrosoftCognitiveServicesSpeech.framework`. Esta ação adiciona a biblioteca do SDK para o cabeçalho **ligado Framework e bibliotecas** automaticamente.
        ![Added Framework](media/sdk/qs-objectivec-framework.png) (Arquitetura Adicionada)
        1. Vá para o separador **Build Settings** (Definições de Compilação) e ative a definição **All** (Tudo).
        1. Adicione o diretório `$(SRCROOT)/..` a *Framework Search Paths* (Caminhos de Pesquisa da Arquitetura) no cabeçalho **Search Paths** (Caminhos de Pesquisa).
        ![Definição Framework Search Path](media/sdk/qs-objectivec-framework-search-paths.png) (Caminho de Pesquisa da Arquitetura)

## <a name="set-up-the-ui"></a>Configurar a IU

A aplicação de exemplo terá uma interface do Usuário muito simples: Dois botões para iniciar o reconhecimento de voz a partir de ficheiro ou de entrada do microfone e uma etiqueta de texto para exibir o resultado.
A IU é configurada na parte `Main.storyboard` do projeto.
Abra a vista XML do storyboard ao clicar com o botão direito do rato na entrada `Main.storyboard` da árvore do projeto e ao selecionar **Open As... (Abrir Como...)** > **Source Code (Código Fonte)**.
Substitua o gerado automaticamente XML com este código:

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/Base.lproj/Main.storyboard)]

## <a name="add-the-sample-code"></a>Adicionar o código de exemplo

1. Transfira o [ficheiro wav de exemplo](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav) ao clicar com o botão direito do rato na ligação e ao selecionar **Save target as...** (Guardar destino como...). Adicione o ficheiro wav ao projeto como um recurso ao arrastá-lo a partir de uma janela do Explorador para o nível da raiz da vista do Projeto.
Clique em **Finish** (Concluir) na caixa de diálogo seguinte sem alterar as definições.
1. Substitua o conteúdo do ficheiro `ViewController.m` gerado automaticamente por:

   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/ViewController.m#code)]
1. Substitua a cadeia de carateres `YourSubscriptionKey` pela sua chave de subscrição.
1. Substitua a cadeia de carateres `YourServiceRegion` pela [região](regions.md) associada à subscrição (por exemplo, `westus` para a subscrição de avaliação gratuita).
1. Adicione o pedido de acesso ao microfone. Com o botão direito a `Info.plist` entrada da árvore de projeto e selecione **aberto como...**   >  **Código-fonte**. Adicione as seguintes linhas à secção `<dict>` e, em seguida, guarde o ficheiro.
    ```xml
    <key>NSMicrophoneUsageDescription</key>
    <string>Need microphone access for speech recognition from microphone.</string>
    ```

## <a name="building-and-running-the-sample"></a>Criar e Executar o Exemplo

1. Torne a saída de depuração visível (**View (Ver)** > **Debug Area (Área de Depuração)** > **Activate Console (Ativar Consola)**).
1. Escolha o simulador iOS ou um dispositivo iOS ligado ao computador de desenvolvimento como destino para a aplicação a partir da lista no menu **Product (Produto)** -> **Destination (Destino)**.
1. Crie e execute o código de exemplo no simulador iOS ao selecionar **Product (Produto)** -> **Run (Executar)** no menu ou ao clicar no botão **Play (Reproduzir)**.
Atualmente, o SDK de Voz apenas suporta plataformas iOS de 64 bits.
1. Depois de clicar no botão "Recognize (File)" (Reconhecer (Ficheiro)) na aplicação, deverá ver o conteúdo do ficheiro de áudio "What's the weather like?" na parte inferior do ecrã.

 ![Aplicação iOS Simulada](media/sdk/qs-objectivec-simulated-app.png)

1. Depois de clicar no botão "Recognize (Microphone)" (Reconhecer (Microfone)) na aplicação e dizer algumas palavras, deverá ver o texto que falou na parte inferior do ecrã.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Explore exemplos de Objective-C no GitHub](https://aka.ms/csspeech/samples)
