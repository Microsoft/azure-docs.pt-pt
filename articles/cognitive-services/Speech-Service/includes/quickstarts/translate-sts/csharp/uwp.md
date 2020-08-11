---
title: 'Quickstart: Traduzir discurso-a-discurso, C# (UWP) - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: lisaweixu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 04/04/2020
ms.author: jhakulin
ms.topic: include
ms.openlocfilehash: 2af0d0eb2411c5ffe9f80498a3e93d48fe0a769b
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2020
ms.locfileid: "88057705"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de:

> [!div class="checklist"]
> * [Criar um recurso de fala azul](../../../../get-started.md)
> * [Configurar o seu ambiente de desenvolvimento e criar um projeto vazio](../../../../quickstarts/setup-platform.md?tabs=uwp&pivots=programming-language-csharp)

## <a name="add-sample-code"></a>Adicionar código de exemplo

Agora adicione o código XAML que define a interface do utilizador da aplicação e adicione a implementação de código C# por trás.

1. In **Solution Explorer,** abra `MainPage.xaml` .

1. Na vista XAML do designer, insira o seguinte corte XAML na etiqueta **Grid** (entre `<Grid>` `</Grid>` e):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/translate-speech-to-text/helloworld/MainPage.xaml#StackPanel)]

1. No **Solution Explorer,** abra o ficheiro de código por trás `MainPage.xaml.cs` . (Está agrupado em `MainPage.xaml` .)

1. Substitua todo o código nele com o seguinte corte:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/translate-speech-to-text/helloworld/MainPage.xaml.cs#code)]

1. No `SpeechTranslationFromMicrophone_ButtonClicked` manipulador deste ficheiro, encontre a cadeia `YourSubscriptionKey` e substitua-a pela sua chave de subscrição.

1. No `SpeechTranslationFromMicrophone_ButtonClicked` manipulador, encontre a corda `YourServiceRegion` e substitua-a pela [região](~/articles/cognitive-services/Speech-Service/regions.md) associada à sua subscrição. (Por exemplo, utilize `westus` para a subscrição experimental gratuita.)

1. A partir da barra de menu, escolha **'Guardar**  >  **tudo'** para guardar as alterações.

## <a name="build-and-run-the-application"></a>Compilar e executar a aplicação

Agora está pronto para construir e testar a sua aplicação.

1. A partir da barra de menu, selecione **Build**  >  **Build Solution** para construir a aplicação. Agora o código deverá ser compilado sem erros.

1. Escolha **Debug**  >  **Start Debugging** (ou prima **F5**) para iniciar a aplicação. A janela **helloworld** aparece.

   ![Aplicação de tradução UWP em C# - quickstart](~/articles/cognitive-services/Speech-Service/media/sdk/qs-translate-speech-uwp-helloworld-window.png)

1. Selecione **Ativar o Microfone**, e quando o pedido de autorização de acesso aparecer, selecione **Sim**.

   ![Pedido de permissão de acesso ao microfone](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. **Selecione Traduzir a fala a partir da entrada do microfone**e fale uma frase ou frase em inglês no microfone do seu dispositivo. A aplicação transmite o seu discurso para o serviço de Discurso, que traduz o discurso em texto em outra língua (neste caso, alemão). O serviço Desemação envia o texto traduzido de volta para a aplicação, que exibe a tradução na janela.

   ![Interface de utilizador de tradução de fala](~/articles/cognitive-services/Speech-Service/media/sdk/qs-translate-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]
