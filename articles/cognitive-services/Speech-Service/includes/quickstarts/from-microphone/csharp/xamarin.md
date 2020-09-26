---
title: 'Quickstart: Reconhecer a fala a partir de um microfone, C# (Xamarin) - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste artigo, cria-se uma aplicação Xamarin de plataforma cruzada C# para a Plataforma Universal windows (UWP), Android e iOS utilizando o Cognitive Services Speech SDK. Transcreve a fala para texto em tempo real a partir do microfone do seu dispositivo ou do simulador. A aplicação é construída com o Pacote NuGet Speech SDK e o Microsoft Visual Studio 2019.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/02/2020
ms.author: erhopf
ms.custom: devx-track-csharp
ms.openlocfilehash: f89bdbfd144fb327c1daae020a1e371c00045859
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376327"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

> [!div class="checklist"]
> * [Criar um recurso de fala azul](../../../../overview.md#try-the-speech-service-for-free)
> * [Configurar o seu ambiente de desenvolvimento e criar um projeto vazio](../../../../quickstarts/setup-platform.md?tabs=xamarin&pivots=programming-language-csharp)
> * Certifique-se de que tem acesso a um microfone para captura de áudio

Se já fez isto, ótimo. Vamos continuar.

## <a name="add-sample-code-for-the-common-helloworld-project"></a>Adicione o código de amostra para o projeto comum helloworld

O projeto comum helloworld contém implementações independentes da plataforma para a sua aplicação cross-platform. Agora adicione o código XAML que define a interface do utilizador da aplicação e adicione o código C# por trás da implementação.

1. In **Solution Explorer,** no âmbito do projeto comum Helloworld, `MainPage.xaml` aberto.

1. Na vista XAML do designer, insira o seguinte corte XAML na etiqueta **Grid** `<StackLayout>` entre: `</StackLayout>`

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/MainPage.xaml)]

1. No **Solution Explorer,** abra o ficheiro de código por trás `MainPage.xaml.cs` . Está agrupado `MainPage.xaml` sob.

1. Substitua todo o código nele com o seguinte corte:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/MainPage.xaml.cs)]

1. No manipulador do ficheiro de `OnRecognitionButtonClicked` origem, encontre a cadeia e `YourSubscriptionKey` substitua-a pela sua chave de subscrição.


1. No `OnRecognitionButtonClicked` manipulador, encontre a corda `YourServiceregion` e substitua-a pelo identificador da **Região** da [região](https://aka.ms/speech/sdkregion) associada à sua subscrição. 

1. Em seguida, é necessário criar um [Serviço Xamarin](https://docs.microsoft.com/xamarin/android/app-fundamentals/services/creating-a-service/), que é utilizado para consultar permissões de microfone de diferentes projetos da plataforma, como UWP, Android e iOS. Para isso, adicione uma nova pasta chamada *Serviços* no âmbito do projeto Helloworld e crie um novo ficheiro de origem C# sob o mesmo. Pode clicar com o botão correto na pasta *Serviços* e selecionar **Adicionar**Novo Ficheiro de Código  >  **de Item**  >  **Code File**. Rebatize o ficheiro `IMicrophoneService.cs` , e coloque todo o código a partir do seguinte corte nesse ficheiro:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/Services/IMicrophoneService.cs)]

#### <a name="android"></a>[Android](#tab/x-android)
## <a name="add-sample-code-for-the-helloworldandroid-project"></a>Adicione código de amostra para o `helloworld.Android` projeto

Agora adicione o código C# que define a parte específica do Android da aplicação.

1. In **Solution Explorer,** under the helloworld. Projeto Android, `MainActivity.cs` aberto.

1. Substitua todo o código nele com o seguinte corte:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.Android/MainActivity.cs)]

1. Em seguida, adicione implementação específica do Android para `MicrophoneService` criar os novos *serviços* de pasta no âmbito do helloworld. Projeto Android. Depois disso, crie um novo ficheiro de origem C# por baixo. Mude o nome do `MicrophoneService.cs` ficheiro. Copiar e colar o seguinte código corta-código nesse ficheiro:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.Android/Services/MicrophoneService.cs)]

1. Depois disso, abra `AndroidManifest.xml` por baixo da pasta *Propriedades.* Adicione a seguinte definição de permissão de utilização para o microfone `<manifest>` entre: `</manifest>`

   ```xml
   <uses-permission android:name="android.permission.RECORD_AUDIO" />
   ```
   
#### <a name="ios"></a>[iOS](#tab/ios)
## <a name="add-sample-code-for-the-helloworldios-project"></a>Adicione código de amostra para o `helloworld.iOS` projeto

Adicione agora o código C# que define a parte específica do iOS da aplicação. Também crie configurações específicas para dispositivos apple para o projeto helloworld.iOS.

1. In **Solution Explorer**, no âmbito do projeto helloworld.iOS, `AppDelegate.cs` aberto.

1. Substitua todo o código nele com o seguinte corte:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.iOS/AppDelegate.cs)]

1. Em seguida, adicione a implementação específica do iOS para `MicrophoneService` criar os novos *Serviços* de pasta no âmbito do projeto helloworld.iO. Depois disso, crie um novo ficheiro de origem C# por baixo. Mude o nome do `MicrophoneService.cs` ficheiro. Copiar e colar o seguinte código corta-código nesse ficheiro:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.iOS/Services/MicrophoneService.cs)]

1. Aberto `Info.plist` no âmbito do projeto helloworld.iOS no editor de texto. Adicione o seguinte par de valores chave na secção dict:

   <key>NSMicrophoneUsageDescription</key> 
    <string>Esta aplicação de amostra requer acesso ao microfone</string>

   > [!NOTE]
   > Se estiver a construir um dispositivo iPhone, certifique-se de que `Bundle Identifier` corresponde ao ID da aplicação de perfil de provisionamento do seu dispositivo. Caso contrário, a construção falhará. Com o iPhoneSimulator, pode deixá-lo como está.

1. Se estiver a construir um PC do Windows, estabeleça uma ligação ao dispositivo Mac para a construção através de **Tools**  >  **iOS**  >  **Pair a Mac**. Siga o assistente de instruções fornecido pelo Visual Studio para ativar a ligação ao dispositivo Mac.

#### <a name="uwp"></a>[UWP](#tab/helloworlduwp)
## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Adicione código de amostra para o `helloworld.UWP` projeto

## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Adicione o código de amostra para o helloworld. Projeto UWP

Adicione agora o código C# que define a parte específica do UWP da aplicação.

1. In **Solution Explorer,** under the helloworld. Projeto UWP, `MainPage.xaml.cs` aberto.

1. Substitua todo o código nele com o seguinte corte:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.UWP/MainPage.xaml.cs)]

1. Em seguida, adicione uma implementação específica do UWP para `MicrophoneService` criar os novos *serviços* de pasta no âmbito do helloworld. Projeto UWP. Depois disso, crie um novo ficheiro de origem C# por baixo. Mude o nome do `MicrophoneService.cs` ficheiro. Copiar e colar o seguinte código corta-código nesse ficheiro:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.UWP/Services/MicrophoneService.cs)]

1. Em seguida, clique duas vezes no `Package.appxmanifest` ficheiro sob o helloworld. Projeto UWP dentro do Estúdio Visual. Em **Capabilites**, certifique-se de que **o microfone** está selecionado e guarde o ficheiro.

1. O próximo ficheiro de clique duplo `Package.appxmanifest` no âmbito do projeto dentro do Estúdio Visual e no `helloworld.UWP` Microfone de **Capacidades**  >  **Microphone** é verificado e guardar o ficheiro.
   > Nota: Caso veja aviso: O ficheiro do certificado não existe: oláworld. UWP_TemporaryKey.pfx, por favor, consulte [a amostra de texto para](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp) obter mais informações.

1. A partir da barra de menus, selecione **'Guardar**  >  **tudo'** para guardar as alterações.

## <a name="build-and-run-the-uwp-application"></a>Construa e execute a aplicação UWP

1. Definir oláworld. UWP como um projeto de startup. Clique à direita no mundo da olá. Projeto UWP, e selecione **Build** para construir a aplicação.

1. Selecione **Debug**  >  **Start Debugging** (ou selecione **F5**) para iniciar a aplicação. A janela **helloworld** aparece.

   ![Exemplo da aplicação de reconhecimento de voz UWP em C# - quickstart](../../../../media/sdk/qs-csharp-xamarin-helloworld-uwp-window.png)

1. Selecione **O microfone de ativação**. Quando o pedido de autorização de acesso aparecer, selecione **Sim**.

   ![Pedido de permissão de acesso ao microfone](../../../../media/sdk/qs-csharp-xamarin-uwp-access-prompt.png)

1. Selecione **Start Speech recognition**, e diga uma frase ou frase em inglês no microfone do seu dispositivo. A sua voz é transmitida ao serviço de Voz e convertida para texto que é apresentado na janela.

   ![Interface de utilizador de reconhecimento de voz](../../../../media/sdk/qs-csharp-xamarin-uwp-ui-result.png)
* * *

## <a name="build-and-run-the-android-and-ios-applications"></a>Construa e execute as aplicações Android e iOS

A construção e execução de aplicações Android e iOS no dispositivo ou simulador acontecem de forma semelhante à UWP. Certifique-se de que todos os SDKs estão instalados corretamente, conforme necessário na secção "Pré-requisitos" deste artigo.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]
