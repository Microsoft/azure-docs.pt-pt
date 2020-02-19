---
title: 'Quickstart: Reconheça o discurso C# de um microfone, (Xamarin) - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste artigo, cria-se uma C# aplicação Xamarin de plataforma cruzada para a Plataforma Universal windows (UWP), Android e iOS utilizando o Cognitive Services Speech SDK. Transcreve o discurso para texto em tempo real a partir do microfone do seu dispositivo ou simulador. A aplicação é construída com o Pacote NuGet Speech SDK e o Microsoft Visual Studio 2019.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: ed37e4f5b84431355b63b3d59b0e49b78eaeed86
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77445925"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

> [!div class="checklist"]
> * [Criar um recurso de fala azure](../../../../get-started.md)
> * [Configurar o seu ambiente de desenvolvimento](../../../../quickstarts/setup-platform.md?tabs=xamarin)
> * [Criar um projeto de amostra vazia](../../../../quickstarts/create-project.md?tabs=xamarin)
> * Certifique-se de que tem acesso a um microfone para captura de áudio

Se já fez isto, ótimo. Vamos continuar.

## <a name="add-sample-code-for-the-common-helloworld-project"></a>Adicione código de amostra para o projeto comum helloworld

O projeto comum helloworld contém implementações independentes de plataformas para a sua aplicação cross-platform. Adicione agora o código XAML que define a interface C# do utilizador da aplicação e adicione o código por detrás da implementação.

1. No **Solution Explorer,** no âmbito do projeto comum Helloworld, abre `MainPage.xaml`.

1. Na vista XAML do designer, insira o seguinte corte XAML na etiqueta **Grid** entre `<StackLayout>` e `</StackLayout>`:

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/MainPage.xaml)]

1. No **Solution Explorer,** abra o ficheiro fonte por trás do código `MainPage.xaml.cs`. Está agrupado sob `MainPage.xaml`.

1. Substitua todo o código nele pelo seguinte corte:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/MainPage.xaml.cs)]

1. No manipulador de `OnRecognitionButtonClicked` do ficheiro fonte, encontre o fio `YourSubscriptionKey`e substitua-o pela chave de subscrição.


1. No manipulador de `OnRecognitionButtonClicked`, encontre a cadeia `YourServiceregion`, e substitua-a pelo **identificador região** da [região](https://aka.ms/speech/sdkregion) associada à sua subscrição. (Por exemplo, utilize `westus` para a subscrição de teste gratuito.)

1. Em seguida, é necessário criar um [Serviço Xamarin](https://docs.microsoft.com/xamarin/android/app-fundamentals/services/creating-a-service/), que é usado para consultar permissões de microfones de diferentes projetos de plataformas, como UWP, Android e iOS. Para isso, adicione uma nova pasta chamada *Services* no âmbito C# do projeto Helloworld, e crie um novo ficheiro de origem sob o mesmo. Pode clicar na pasta *Serviços* e selecionar **adicionar** > **Novo Item** > Ficheiro de **Código**. Mude o nome do ficheiro `IMicrophoneService.cs`e coloque todo o código a partir do seguinte corte nesse ficheiro:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/Services/IMicrophoneService.cs)]

#### <a name="android"></a>[Android](#tab/x-android)
## <a name="add-sample-code-for-the-helloworldandroid-project"></a>Adicione código de amostra para o projeto `helloworld.Android`

Adicione agora C# o código que define a parte específica do Android da aplicação.

1. In **Solution Explorer,** sob o helloworld. Projeto Android, open `MainActivity.cs`.

1. Substitua todo o código nele pelo seguinte corte:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.Android/MainActivity.cs)]

1. Em seguida, adicione implementação específica do Android para `MicrophoneService` criando a nova pasta *Services* under the Helloworld. Projeto Android. Depois disso, crie um novo C# ficheiro de origem por baixo. Mude o nome do ficheiro `MicrophoneService.cs`. Copiar e colar o seguinte código snippet nesse ficheiro:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.Android/Services/MicrophoneService.cs)]

1. Depois disso, abra `AndroidManifest.xml` sob a pasta *Propriedades.* Adicione a seguinte definição de permissão de utilização para o microfone entre `<manifest>` e `</manifest>`:

   ```xml
   <uses-permission android:name="android.permission.RECORD_AUDIO" />
   ```
   
#### <a name="ios"></a>[iOS](#tab/ios)
## <a name="add-sample-code-for-the-helloworldios-project"></a>Adicione código de amostra para o projeto `helloworld.iOS`

Adicione agora C# o código que define a parte específica do iOS da aplicação. Também crie configurações específicas do dispositivo apple para o projeto helloworld.iOS.

1. In **Solution Explorer**, no âmbito do projeto helloworld.iOS, abre `AppDelegate.cs`.

1. Substitua todo o código nele pelo seguinte corte:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.iOS/AppDelegate.cs)]

1. Em seguida, adicione a implementação específica do iOS para `MicrophoneService` através da criação da nova pasta *Serviços* no âmbito do projeto helloworld.iO. Depois disso, crie um novo C# ficheiro de origem por baixo. Mude o nome do ficheiro `MicrophoneService.cs`. Copiar e colar o seguinte código snippet nesse ficheiro:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.iOS/Services/MicrophoneService.cs)]

1. Aberto `Info.plist` no âmbito do projeto helloworld.iOS no editor de texto. Adicione o seguinte par de valor-chave sob a secção dita:

   <key>NSMicrophoneUsageDescription</key>
   <string>Esta aplicação de amostra requer acesso ao microfone</string>

   > [!NOTE]
   > Se estiver a construir um dispositivo iPhone, certifique-se de que `Bundle Identifier` corresponde ao ID da aplicação de perfil de provisionamento do seu dispositivo. Caso contrário, a construção falhará. Com o iPhoneSimulator, pode deixá-lo como está.

1. Se estiver a construir um PC windows, estabeleça uma ligação ao dispositivo Mac para construir através **de Ferramentas** > **iOS** > **Pair a Mac**. Siga o assistente de instruções fornecido pelo Visual Studio para ativar a ligação ao dispositivo Mac.

#### <a name="uwp"></a>[UWP](#tab/helloworlduwp)
## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Adicione código de amostra para o projeto `helloworld.UWP`

## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Adicione código de amostra para o helloworld. Projeto UWP

Adicione agora C# o código que define a parte específica da aplicação da UWP.

1. In **Solution Explorer,** sob o helloworld. Projeto UWP, open `MainPage.xaml.cs`.

1. Substitua todo o código nele pelo seguinte corte:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.UWP/MainPage.xaml.cs)]

1. Em seguida, adicione uma implementação específica do UWP para `MicrophoneService` criando a nova pasta *Serviços* sob o helloworld. Projeto UWP. Depois disso, crie um novo C# ficheiro de origem por baixo. Mude o nome do ficheiro `MicrophoneService.cs`. Copiar e colar o seguinte código snippet nesse ficheiro:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.UWP/Services/MicrophoneService.cs)]

1. Em seguida, clique duas vezes no ficheiro `Package.appxmanifest` sob o helloworld. Projeto UWP dentro do Estúdio Visual. Em **Capacidades,** certifique-se de que o **Microfone** é selecionado e guarde o ficheiro.

1. O próximo clique duplo `Package.appxmanifest` ficheiro no âmbito do projeto `helloworld.UWP` dentro do Visual Studio e em **Capabilities** > **microfone** é verificado e guarde o ficheiro.
   > Nota: Caso veja aviso : O ficheiro de certificado não existe: helloworld. UWP_TemporaryKey.pfx, por favor, verifique [o discurso para](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp) a amostra de texto para mais informações.

1. A partir da barra de menus, selecione **File** > **Save All** para guardar as suas alterações.

## <a name="build-and-run-the-uwp-application"></a>Construir e executar a aplicação UWP

1. Definir helloworld. UWP como um projeto de arranque. Clique no helloworld. Projeto UWP, e **selecione Build** para construir a aplicação.

1. Selecione **Debug** > **Iniciar depuração** (ou selecione **F5)** para iniciar a aplicação. A janela do **Helloworld** aparece.

   ![Amostra UWP aplicação C# de reconhecimento de voz em - quickstart](../../../../media/sdk/qs-csharp-xamarin-helloworld-uwp-window.png)

1. Selecione **ativar o microfone**. Quando o pedido de autorização de acesso aparecer, selecione **Sim**.

   ![Pedido de permissão de acesso ao microfone](../../../../media/sdk/qs-csharp-xamarin-uwp-access-prompt.png)

1. Selecione **Start Speech recognition**, e diga uma frase ou frase em inglês no microfone do seu dispositivo. A sua voz é transmitida ao serviço de Voz e convertida para texto que é apresentado na janela.

   ![Interface de utilizador de reconhecimento de voz](../../../../media/sdk/qs-csharp-xamarin-uwp-ui-result.png)
* * *

## <a name="build-and-run-the-android-and-ios-applications"></a>Construir e executar as aplicações Android e iOS

A construção e execução de aplicações Android e iOS no dispositivo ou simulador acontecem de forma semelhante à do UWP. Certifique-se de que todos os SDKs estão instalados corretamente, conforme necessário na secção "Pré-requisitos" deste artigo.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]
