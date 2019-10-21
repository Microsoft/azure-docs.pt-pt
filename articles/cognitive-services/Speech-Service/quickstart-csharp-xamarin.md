---
title: 'Início rápido: reconhecer fala C# , (Xamarin)-serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste artigo, você cria um aplicativo Xamarin de plataforma C# cruzada para o plataforma universal do Windows (UWP), Android e Ios usando o SDK de fala dos serviços cognitivas. Você transcreve a fala em texto em tempo real do microfone do seu dispositivo ou do simulador. O aplicativo é criado com o pacote NuGet do SDK de fala e Microsoft Visual Studio 2019.
services: cognitive-services
author: jhakulin
manager: robch
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: jhakulin
ms.custom: ''
ms.openlocfilehash: ad1c6f7d17d1e04fcfa5b187a29dadd2fa61edd8
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675612"
---
# <a name="quickstart-recognize-speech-by-using-a-cross-platform-xamarin-app-that-uses-the-speech-sdk"></a>Início rápido: reconhecer a fala usando um aplicativo Xamarin de plataforma cruzada que usa o SDK de fala

Os guias de início rápido também estão disponíveis para conversão de fala [em texto](quickstart-csharp-uwp.md), [texto em fala](quickstart-text-to-speech-csharp-uwp.md)e [tradução de fala](quickstart-translate-speech-uwp.md).

Neste artigo, você desenvolve um aplicativo de plataforma C# cruzada usando o Xamarin para o plataforma universal do Windows (UWP), Android e Ios que usa o SDK de [fala](speech-sdk.md)dos serviços cognitivas do Azure. O programa transcreve a fala em texto em tempo real do microfone do seu dispositivo. O aplicativo é criado com o [pacote NuGet do SDK de fala](https://aka.ms/csspeech/nuget) e qualquer edição do Microsoft Visual Studio 2019.

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Uma chave de assinatura do Azure para o serviço de fala. [Obtenha um gratuitamente](get-started.md).
* Um computador Windows com a atualização para criadores de outono do Windows 10 (10,0; Build 16299) ou posterior e com um microfone funcional.
* Uma [instalação do Xamarin para o Visual Studio](https://docs.microsoft.com/xamarin/get-started/installation/?pivots=windows).
* Uma [instalação do Xamarin Android no Windows](https://docs.microsoft.com/xamarin/android/get-started/installation/windows).
* Uma [instalação do Xamarin Ios no Windows](https://docs.microsoft.com/xamarin/ios/get-started/installation/windows/?pivots=windows).
* Para o Android de destino: 
   * Um dispositivo Android (ARM32/64, x86; API 23: Android 6,0 marshmallow ou superior) [habilitado para desenvolvimento](https://developer.android.com/studio/debug/dev-options) com um microfone funcional.
* Para o iOS de destino: 
    * Um dispositivo iOS (ARM64) ou um simulador de iOS (x64) [habilitado para desenvolvimento](https://docs.microsoft.com/xamarin/ios/get-started/installation/device-provisioning/) com um microfone funcional.
* Para o suporte do Build do Windows ARM64, instale as [ferramentas de compilação opcionais e o SDK do Windows 10 para ARM/ARM64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/).

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-xamarin-create-proj.md)]

## <a name="add-sample-code-for-the-common-helloworld-project"></a>Adicionar código de exemplo para o projeto HelloWorld comum

O projeto HelloWorld comum contém implementações independentes de plataforma para seu aplicativo de plataforma cruzada. Agora, adicione o código XAML que define a interface do usuário do aplicativo e adicione o C# código por trás da implementação.

1. No **Gerenciador de soluções**, no projeto HelloWorld comum, abra `MainPage.xaml`.

1. Na exibição XAML do designer, insira o seguinte trecho XAML na marca de **grade** entre `<StackLayout>` e `</StackLayout>`:

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/MainPage.xaml)]

1. No **Gerenciador de soluções**, abra o arquivo de origem code-behind `MainPage.xaml.cs`. Ele está agrupado em `MainPage.xaml`.

1. Substitua todo o código nele pelo seguinte trecho:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/MainPage.xaml.cs)]

1. No manipulador `OnRecognitionButtonClicked` do arquivo de origem, localize a cadeia de caracteres `YourSubscriptionKey` e substitua-a pela sua chave de assinatura.

1. No manipulador `OnRecognitionButtonClicked`, localize a cadeia de caracteres `YourServiceRegion` e substitua-a pela [região](regions.md) associada à sua assinatura. Por exemplo, use `westus` para a assinatura de avaliação gratuita.

1. Em seguida, você precisa criar um [serviço Xamarin](https://docs.microsoft.com/xamarin/android/app-fundamentals/services/creating-a-service/), que é usado para consultar permissões de microfone de diferentes projetos de plataforma, como UWP, Android e Ios. Para fazer isso, adicione uma nova pasta chamada *Serviços* no projeto HelloWorld e crie um novo C# arquivo de origem sob ele. Você pode clicar com o botão direito do mouse na pasta *Serviços* e selecionar **Adicionar**  > **novo item**  > **arquivo de código**. Renomeie o arquivo `IMicrophoneService.cs` e coloque todo o código do trecho a seguir nesse arquivo:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/Services/IMicrophoneService.cs)]

## <a name="add-sample-code-for-the-helloworldandroid-project"></a>Adicione o código de exemplo para o HelloWorld. Projeto do Android

Agora, adicione C# o código que define a parte específica do Android do aplicativo.

1. No **Gerenciador de soluções**, sob o HelloWorld. Projeto do Android, abra `MainActivity.cs`.

1. Substitua todo o código nele pelo seguinte trecho:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.Android/MainActivity.cs)]

1. Em seguida, adicione a implementação específica do Android para `MicrophoneService` criando os novos *Serviços* de pasta no HelloWorld. Projeto do Android. Depois disso, crie um novo C# arquivo de origem sob ele. Renomeie o arquivo `MicrophoneService.cs`. Copie e cole o seguinte trecho de código nesse arquivo:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.Android/Services/MicrophoneService.cs)]

1. Depois disso, abra `AndroidManifest.xml` na pasta *Propriedades* . Adicione a seguinte configuração de permissão de uso para o microfone entre `<manifest>` e `</manifest>`:

   ```xml
   <uses-permission android:name="android.permission.RECORD_AUDIO" />
   ```

## <a name="add-sample-code-for-the-helloworldios-project"></a>Adicionar código de exemplo para o projeto HelloWorld. iOS

Agora, adicione C# o código que define a parte específica do IOS do aplicativo. Além disso, crie configurações específicas de dispositivo da Apple para o projeto HelloWorld. iOS.

1. No **Gerenciador de soluções**, no projeto HelloWorld. Ios, abra `AppDelegate.cs`.

1. Substitua todo o código nele pelo seguinte trecho:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.iOS/AppDelegate.cs)]

1. Em seguida, adicione a implementação específica do iOS para `MicrophoneService` criando os novos *Serviços* de pasta no projeto HelloWorld.IO. Depois disso, crie um novo C# arquivo de origem sob ele. Renomeie o arquivo `MicrophoneService.cs`. Copie e cole o seguinte trecho de código nesse arquivo:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.iOS/Services/MicrophoneService.cs)]

1. Abra `Info.plist` no projeto HelloWorld. iOS no editor de texto. Adicione o seguinte par chave-valor na seção dicter:

   <key>NSMicrophoneUsageDescription</key> 
   <string>este aplicativo de exemplo requer acesso ao microfone</string>

   > [!NOTE] 
   > Se você estiver criando um dispositivo iPhone, verifique se `Bundle Identifier` corresponde à ID do aplicativo de perfil de provisionamento do seu dispositivo. Caso contrário, a compilação falhará. Com o iPhoneSimulator, você pode deixá-lo como está.

1. Se você estiver criando um computador Windows, estabeleça uma conexão com o dispositivo Mac para a criação por meio de **ferramentas**  > **Ios**  > **emparelhar com Mac**. Siga o assistente de instruções fornecido pelo Visual Studio para habilitar a conexão com o dispositivo Mac.

## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Adicione o código de exemplo para o HelloWorld. Projeto UWP

Agora, adicione C# o código que define a parte específica do UWP do aplicativo.

1. No **Gerenciador de soluções**, sob o HelloWorld. Projeto UWP, abra `MainPage.xaml.cs`.

1. Substitua todo o código nele pelo seguinte trecho:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.UWP/MainPage.xaml.cs)]

1. Em seguida, adicione uma implementação específica de UWP para `MicrophoneService` criando os novos *Serviços* de pasta no HelloWorld. Projeto UWP. Depois disso, crie um novo C# arquivo de origem sob ele. Renomeie o arquivo `MicrophoneService.cs`. Copie e cole o seguinte trecho de código nesse arquivo:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.UWP/Services/MicrophoneService.cs)]

1. Em seguida, clique duas vezes no arquivo `Package.appxmanifest` sob o HelloWorld. Projeto UWP dentro do Visual Studio. Em **recursos**, verifique se o **microfone** está selecionado e salve o arquivo.

   > [!NOTE] 
   > Se você vir o aviso "o arquivo de certificado não existe: HelloWorld. UWP_TemporaryKey. pfx, "Verifique o exemplo de [fala para texto](quickstart-csharp-uwp.md) para obter mais informações.

1. Na barra de menus, selecione **arquivo**  > **salvar tudo** para salvar suas alterações.

## <a name="build-and-run-the-uwp-application"></a>Compilar e executar o aplicativo UWP

1. Defina HelloWorld. UWP como um projeto de inicialização. Clique com o botão direito do mouse no HelloWorld. Projeto UWP e selecione **Compilar** para compilar o aplicativo. 

1. Selecione **depurar**  > **Iniciar Depuração** (ou selecione **F5**) para iniciar o aplicativo. A janela **HelloWorld** é exibida.

   ![Aplicativo de reconhecimento de fala de C# exemplo UWP no início rápido](media/sdk/qs-csharp-xamarin-helloworld-uwp-window.png)

1. Selecione **habilitar microfone**. Quando a solicitação de permissão de acesso for exibida, selecione **Sim**.

   ![Solicitação de permissão de acesso ao microfone](media/sdk/qs-csharp-xamarin-uwp-access-prompt.png)

1. Selecione **Iniciar reconhecimento de fala**e fale uma frase em inglês ou frase no microfone do dispositivo. Sua fala é transmitida para os serviços de fala e transcrita para texto, que aparece na janela.

   ![Interface do usuário de reconhecimento de fala](media/sdk/qs-csharp-xamarin-uwp-ui-result.png)

## <a name="build-and-run-the-android-and-ios-applications"></a>Compilar e executar os aplicativos Android e iOS

A criação e a execução de aplicativos Android e iOS no dispositivo ou simulador acontecem de forma semelhante ao UWP. Verifique se todos os SDKs estão instalados corretamente conforme necessário na seção "pré-requisitos" deste artigo.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Explorar C# exemplos no github](https://aka.ms/csspeech/samples)
