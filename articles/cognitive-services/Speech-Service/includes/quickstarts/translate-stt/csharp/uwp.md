---
title: 'Quickstart: Traduzir discurso a C# texto, (UWP) - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: lisaweixu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 12/09/2019
ms.author: erhopf
ms.topic: include
ms.openlocfilehash: e84c7423e0f061004a827ac45c211d7db9c556df
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2020
ms.locfileid: "78924774"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de:

> [!div class="checklist"]
> * [Criar um recurso de fala azure](../../../../get-started.md)
> * [Crie o seu ambiente de desenvolvimento e crie um projeto vazio](../../../../quickstarts/setup-platform.md?tabs=uwp)

## <a name="add-sample-code"></a>Adicionar código de exemplo

Adicione agora o código XAML que define a interface C# do utilizador da aplicação e adicione a implementação por detrás de código.

1. No **Solution Explorer,** abra `MainPage.xaml`.

1. Na vista XAML do designer, insira o seguinte corte XAML na etiqueta **Grid** (entre `<Grid>` e `</Grid>`):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/translate-speech-to-text/helloworld/MainPage.xaml#StackPanel)]

1. No **Solution Explorer,** abra o ficheiro fonte por trás do código `MainPage.xaml.cs`. (Está agrupado sob `MainPage.xaml`.)

1. Substitua todo o código nele pelo seguinte corte:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/translate-speech-to-text/helloworld/MainPage.xaml.cs#code)]

1. No manipulador `SpeechTranslationFromMicrophone_ButtonClicked` neste ficheiro, encontre o `YourSubscriptionKey`de cadeias e substitua-o pela sua chave de subscrição.

1. No manipulador de `SpeechTranslationFromMicrophone_ButtonClicked`, encontre a cadeia `YourServiceRegion`, e substitua-a pela [região](~/articles/cognitive-services/Speech-Service/regions.md) associada à sua subscrição. (Por exemplo, utilize `westus` para a subscrição de teste gratuito.)

1. A partir da barra de menus, escolha **File** > **Save All** para guardar as suas alterações.

## <a name="build-and-run-the-application"></a>Compilar e executar a aplicação

Agora está pronto para construir e testar a sua aplicação.

1. A partir da barra de menus, selecione **Build** > **Build Solution** para construir a aplicação. Agora o código deverá ser compilado sem erros.

1. Escolha **depurar** > **Iniciar depuração** (ou prima **F5)** para iniciar a aplicação. A janela do **Helloworld** aparece.

   ![Amostra UWP aplicação de tradução em C# - quickstart](~/articles/cognitive-services/Speech-Service/media/sdk/qs-translate-speech-uwp-helloworld-window.png)

1. Selecione **Ativar o microfone**e quando o pedido de autorização de acesso aparecer, selecione **Sim**.

   ![Pedido de permissão de acesso ao microfone](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. **Selecione O discurso De traduzir a partir da entrada do microfone**e diga uma frase ou frase em inglês no microfone do seu dispositivo. A aplicação transmite o seu discurso ao serviço da Fala, que traduz o discurso em texto noutra língua (neste caso, alemão). O serviço de Fala envia o texto traduzido de volta para a aplicação, que exibe a tradução na janela.

   ![Interface de utilizador de tradução de discurso](~/articles/cognitive-services/Speech-Service/media/sdk/qs-translate-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]
