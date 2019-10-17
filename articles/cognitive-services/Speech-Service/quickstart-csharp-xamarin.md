---
title: 'Início rápido: reconhecer fala C# , (Xamarin)-serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste artigo, você cria um aplicativo Xamarin de plataforma C# cruzada para o Windows UWP, Android e Ios usando o SDK de fala dos serviços cognitivas. Você transcreve a fala em texto em tempo real do microfone do seu dispositivo ou do simulador. O aplicativo é criado com o pacote NuGet do SDK de fala e Microsoft Visual Studio 2019.
services: cognitive-services
author: jhakulin
manager: robch
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: jhakulin
ms.custom: ''
ms.openlocfilehash: c9467bac8b5998252c021faca4eb4177c42a1736
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387388"
---
# <a name="quickstart-recognize-speech-using-cross-platform-xamarin-app-by-using-the-speech-sdk"></a>Início rápido: reconhecer a fala usando o aplicativo Xamarin de plataforma cruzada usando o SDK de fala

Os guias de início rápido também estão disponíveis para [fala em texto](quickstart-csharp-uwp.md), [conversão](quickstart-translate-speech-uwp.md) [de texto em fala](quickstart-text-to-speech-csharp-uwp.md) e fala.

Neste artigo, você desenvolverá um aplicativo de plataforma C# cruzada usando o Xamarin for plataforma universal do Windows (UWP), Android e Ios usando o [SDK de fala](speech-sdk.md). O programa transcreve a fala em texto em tempo real do microfone do seu dispositivo. O aplicativo é criado com o [pacote NuGet do SDK de fala](https://aka.ms/csspeech/nuget) e Microsoft Visual Studio 2019 (qualquer edição).

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Uma chave de assinatura do Azure para o serviço de fala. [Obtenha um gratuitamente](get-started.md).
* Um computador Windows com a atualização para criadores de outono do Windows 10 (10,0; Build 16299) ou posterior e com um microfone funcional.
* [Instalação do Xamarin no Visual Studio](https://docs.microsoft.com/xamarin/get-started/installation/?pivots=windows).
* [Instalação do Xamarin Android no Windows](https://docs.microsoft.com/xamarin/android/get-started/installation/windows).
* [Instalação do Xamarin Ios no Windows](https://docs.microsoft.com/xamarin/ios/get-started/installation/windows/?pivots=windows).
* Para o Android de destino: 
   * Um dispositivo Android (ARM32/64, x86; API 23: Android 6,0 marshmallow ou superior) [habilitado para desenvolvimento](https://developer.android.com/studio/debug/dev-options) com um microfone funcional.
* Para o iOS de destino: um dispositivo iOS (ARM64) ou um simulador de iOS (x64) [habilitado para desenvolvimento](https://docs.microsoft.com/xamarin/ios/get-started/installation/device-provisioning/) com um microfone funcional.
* Para o suporte do Build do Windows ARM64, instale as [ferramentas de compilação opcionais e o SDK do Windows 10 para ARM/ARM64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/).

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-xamarin-create-proj.md)]

## <a name="add-sample-code-for-the-common-helloworld-project"></a>Adicionar código de exemplo para o projeto Common `helloworld`

O projeto Common `helloworld` contém implementações independentes de plataforma para o aplicativo de plataforma cruzada.
Agora, adicione o código XAML que define a interface do usuário do aplicativo e adicione a C# implementação code-behind.

1. No **Gerenciador de soluções**, no projeto comum de `helloworld` aberto `MainPage.xaml`.

1. Na exibição XAML do designer, insira o seguinte trecho XAML na marca de **grade** (entre `<StackLayout>` e `</StackLayout>`):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/MainPage.xaml)]

1. No **Gerenciador de soluções**, abra o arquivo de origem code-behind `MainPage.xaml.cs`. (Está agrupado em `MainPage.xaml`.)

1. Substitua todo o código nele pelo seguinte trecho:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/MainPage.xaml.cs)]

1. No manipulador `OnRecognitionButtonClicked` do arquivo de origem, localize a cadeia de caracteres `YourSubscriptionKey` e substitua-a pela sua chave de assinatura.

1. No manipulador `OnRecognitionButtonClicked`, localize a cadeia de caracteres `YourServiceRegion` e substitua-a pela [região](regions.md) associada à sua assinatura. (Por exemplo, use `westus` para a assinatura de avaliação gratuita.)

1. Em seguida, você precisa criar um [serviço Xamarin](https://docs.microsoft.com/xamarin/android/app-fundamentals/services/creating-a-service/), que é usado para consultar permissões de microfone de diferentes projetos de plataforma (UWP, Android e Ios). Para fazer isso, adicione nova pasta `Services` no projeto `helloworld` e crie um C# novo arquivo de origem nele (clique com o botão direito do mouse em `Services` pasta e **adicione** > **novo item**de**código** > ) e renomeie-o como `IMicrophoneService.cs` e coloque todo o código do trecho a seguir no arquivo:

[!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/Services/IMicrophoneService.cs)]

## <a name="add-sample-code-for-the-helloworldandroid-project"></a>Adicionar código de exemplo para o projeto `helloworld.Android`

Agora, adicione C# o código que define a parte específica do Android do aplicativo.

1. No **Gerenciador de soluções**, no projeto `helloworld.Android` aberto `MainActivity.cs`.

1. Substitua todo o código nele pelo seguinte trecho:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.Android/MainActivity.cs)]

1. Em seguida, adicione a implementação específica do Android para `MicrophoneService` criando uma nova pasta `Services` no projeto `helloworld.Android`. Depois de criar um C# novo arquivo de origem sob ele e renomeá-lo como `MicrophoneService.cs` e copiar, Cole o trecho de código a seguir nesse arquivo.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.Android/Services/MicrophoneService.cs)]

1. Depois disso, abra `AndroidManifest.xml` na pasta `Properties` e adicione a seguinte configuração de permissão de uso para o microfone entre `<manifest>` e `</manifest>`.
```xml
   <uses-permission android:name="android.permission.RECORD_AUDIO" />
```

## <a name="add-sample-code-for-the-helloworldios-project"></a>Adicionar código de exemplo para o projeto `helloworld.iOS`

Agora, adicione C# o código que define a parte específica do IOS do aplicativo e também crie configurações específicas do dispositivo Apple para o projeto HelloWorld. Ios.

1. No **Gerenciador de soluções**, no projeto `helloworld.iOS` aberto `AppDelegate.cs`.

1. Substitua todo o código nele pelo seguinte trecho:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.iOS/AppDelegate.cs)]

1. Em seguida, adicione a implementação específica do iOS para `MicrophoneService` criando uma nova pasta `Services` no projeto `helloworld.iOS`. Depois de criar um C# novo arquivo de origem sob ele e renomeá-lo como `MicrophoneService.cs` e copiar, Cole o trecho de código a seguir nesse arquivo.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.iOS/Services/MicrophoneService.cs)]

1. Abra o info. plist no projeto `helloworld.iOS` para o editor de texto e adicione o seguinte par chave-valor na seção de dict <key>NSMicrophoneUsageDescription</key>
   <string>este aplicativo de exemplo requer acesso ao microfone</string>
   > Observação: caso você esteja se concentrando para compilar para dispositivo iPhone, verifique se `Bundle Identifier` corresponde à ID do aplicativo de perfil de provisionamento do seu dispositivo. caso contrário, a compilação falhará. Com o iPhoneSimulator, você pode deixá-lo como está.

1. Caso esteja criando no computador Windows, você precisa estabelecer conexão com o dispositivo Mac para a criação por meio de **ferramentas** > **Ios** > **par ao Mac**. Siga o assistente de instruções fornecido pelo Visual Studio para habilitar a conexão com o dispositivo Mac.

## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Adicionar código de exemplo para o projeto `helloworld.UWP`

Agora, adicione C# o código que define a parte específica do UWP do aplicativo.

1. No **Gerenciador de soluções**, no projeto `helloworld.UWP` aberto `MainPage.xaml.cs`.

1. Substitua todo o código nele pelo seguinte trecho:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.UWP/MainPage.xaml.cs)]

1. Em seguida, adicione a implementação específica de UWP para `MicrophoneService` criando uma nova pasta `Services` no projeto `helloworld.UWP`. Depois de criar um C# novo arquivo de origem sob ele e renomeá-lo como `MicrophoneService.cs` e copiar, Cole o trecho de código a seguir nesse arquivo.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.UWP/Services/MicrophoneService.cs)]

1. Em seguida, clique duas vezes no arquivo `Package.appxmanifest` no projeto `helloworld.UWP` dentro do Visual Studio e em **recursos** > **microfone** está marcado e salve o arquivo.
   > Observação: caso você veja aviso: o arquivo de certificado não existe: HelloWorld. UWP_TemporaryKey. pfx, consulte [a amostra de fala para texto](quickstart-csharp-uwp.md) para obter mais informações.

1. Na barra de menus, escolha **arquivo** > **salvar tudo** para salvar suas alterações.

## <a name="build-and-run-the-uwp-application"></a>Compilar e executar o aplicativo UWP

1. Defina `helloworld.UWP` como projeto de inicialização e clique com o botão direito do mouse no projeto `helloworld.UWP` e escolha **Compilar** para compilar o aplicativo. 

1. Escolha **depurar** > **Iniciar Depuração** (ou pressione **F5**) para iniciar o aplicativo. A janela **HelloWorld** é exibida.

   ![Aplicativo de reconhecimento de fala de C# exemplo UWP no início rápido](media/sdk/qs-csharp-xamarin-helloworld-uwp-window.png)

1. Selecione **habilitar microfone**e, quando a solicitação de permissão de acesso for exibida, selecione **Sim**.

   ![Solicitação de permissão de acesso ao microfone](media/sdk/qs-csharp-xamarin-uwp-access-prompt.png)

1. Selecione **Iniciar reconhecimento de fala**e fale uma frase em inglês ou frase no microfone do dispositivo. Sua fala é transmitida para os serviços de fala e transcrita para texto, que aparece na janela.

   ![Interface do usuário de reconhecimento de fala](media/sdk/qs-csharp-xamarin-uwp-ui-result.png)

## <a name="build-and-run-the-android-and-ios-applications"></a>Compilar e executar os aplicativos Android e iOS

A criação e a execução de aplicativos Android e iOS no dispositivo ou simulador ocorrem de forma semelhante ao UWP. Importante é certificar-se de que todos os SDKs instalados corretamente são necessários na seção `Prerequisites` deste documento.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Explorar C# exemplos no github](https://aka.ms/csspeech/samples)
