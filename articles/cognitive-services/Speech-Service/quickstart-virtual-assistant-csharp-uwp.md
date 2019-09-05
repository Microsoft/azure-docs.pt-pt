---
title: 'Início rápido: Personalizar voz-primeiro assistente virtual (versão prévia) C# , (UWP)-serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste artigo, você cria um C# aplicativo plataforma universal do Windows (UWP) usando o SDK (Software Development Kit) de fala dos serviços cognitivas. Você conecta o aplicativo cliente a um bot da estrutura de bot criado anteriormente configurado para usar o canal de fala de linha direta. O aplicativo é criado com o pacote NuGet do SDK de fala e Microsoft Visual Studio 2019.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/19/2019
ms.author: travisw
ms.openlocfilehash: c676e98eb812a31d6fb8d7cc0f58929f803c868e
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382069"
---
# <a name="quickstart-create-a-voice-first-virtual-assistant-with-the-speech-sdk-uwp"></a>Início rápido: Criar um assistente virtual de voz-primeiro com o SDK de fala, UWP

Os guias de início rápido também estão disponíveis para [reconhecimento de fala](quickstart-csharp-uwp.md), [síntese de fala](quickstart-text-to-speech-csharp-uwp.md)e tradução de [fala](quickstart-translate-speech-uwp.md).

Neste artigo, você desenvolverá um C# aplicativo plataforma universal do Windows (UWP) usando o SDK de [fala](speech-sdk.md). O programa se conectará a um bot previamente criado e configurado para habilitar uma experiência de assistente virtual de voz primeiro do aplicativo cliente. O aplicativo é criado com o [pacote NuGet do SDK de fala](https://aka.ms/csspeech/nuget) e Microsoft Visual Studio 2019 (qualquer edição).

> [!NOTE]
> A Plataforma Universal do Windows permite-lhe desenvolver aplicações que são executadas em qualquer dispositivo que suporte o Windows 10, incluindo PCs, Xbox, Surface Hub e outros dispositivos.

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Uma chave de assinatura do Azure para serviços de fala. [Obtenha um gratuitamente](get-started.md) ou crie-o no [portal do Azure](https://portal.azure.com).
* Um bot criado anteriormente configurado com o [canal de fala de linha direta](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

  > [!NOTE]
  > A Direct line Speech (visualização) está disponível atualmente em um subconjunto de regiões de serviços de fala. Consulte [a lista de regiões com suporte para assistentes virtuais primeiro de voz](regions.md#voice-first-virtual-assistants) e garanta que seus recursos sejam implantados em uma dessas regiões.

## <a name="optional-get-started-fast"></a>Opcional: Comece rapidamente

Este guia de início rápido descreverá, passo a passo, como fazer um aplicativo cliente se conectar ao bot habilitado para fala. Se você preferir se aprofundar diretamente no, o código-fonte completo e pronto para compilar usado neste guia de início rápido está disponível nos [exemplos](https://aka.ms/csspeech/samples) `quickstart` do SDK de fala na pasta.

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>Adicionar código de exemplo

Agora, adicione o código XAML que define a interface do usuário do aplicativo e adicione a C# implementação code-behind.

### <a name="xaml-code"></a>Código XAML

Primeiro, você criará a interface do usuário do aplicativo adicionando o código XAML:

1. Em **Gerenciador de soluções**, abra `MainPage.xaml`.

1. Na exibição XAML do designer, substitua todo o conteúdo pelo seguinte trecho de código:

    ```xml
    <Page
        x:Class="helloworld.MainPage"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:local="using:helloworld"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        mc:Ignorable="d"
        Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">

        <Grid>
            <StackPanel Orientation="Vertical" HorizontalAlignment="Center"  
                        Margin="20,50,0,0" VerticalAlignment="Center" Width="800">
                <Button x:Name="EnableMicrophoneButton" Content="Enable Microphone"  
                        Margin="0,0,10,0" Click="EnableMicrophone_ButtonClicked" 
                        Height="35"/>
                <Button x:Name="ListenButton" Content="Talk to your bot" 
                        Margin="0,10,10,0" Click="ListenButton_ButtonClicked" 
                        Height="35"/>
                <StackPanel x:Name="StatusPanel" Orientation="Vertical" 
                            RelativePanel.AlignBottomWithPanel="True" 
                            RelativePanel.AlignRightWithPanel="True" 
                            RelativePanel.AlignLeftWithPanel="True">
                    <TextBlock x:Name="StatusLabel" Margin="0,10,10,0" 
                               TextWrapping="Wrap" Text="Status:" FontSize="20"/>
                    <Border x:Name="StatusBorder" Margin="0,0,0,0">
                        <ScrollViewer VerticalScrollMode="Auto"  
                                      VerticalScrollBarVisibility="Auto" MaxHeight="200">
                            <!-- Use LiveSetting to enable screen readers to announce 
                                 the status update. -->
                            <TextBlock 
                                x:Name="StatusBlock" FontWeight="Bold" 
                                AutomationProperties.LiveSetting="Assertive"
                                MaxWidth="{Binding ElementName=Splitter, Path=ActualWidth}" 
                                Margin="10,10,10,20" TextWrapping="Wrap"  />
                        </ScrollViewer>
                    </Border>
                </StackPanel>
            </StackPanel>
            <MediaElement x:Name="mediaElement"/>
        </Grid>
    </Page>
    ```

O modo de exibição de Design é atualizado para mostrar a interface do usuário do aplicativo.

### <a name="c-code-behind-source"></a>C#origem code-behind

Em seguida, você adiciona a fonte code-behind para que o aplicativo funcione conforme o esperado. A fonte code-behind inclui:

- `using`instruções para os `Speech` namespaces e `Speech.Dialog`
- Uma implementação simples para garantir o acesso ao microfone, com fio a um manipulador de botão
- Auxiliares básicos da interface do usuário para apresentar mensagens e erros no aplicativo
- Um ponto de chegada para o caminho do código de inicialização que será preenchido posteriormente
- Um auxiliar para reproduzir conversão de texto em fala (sem suporte de streaming)
- Um manipulador de botão vazio para iniciar a escuta que será populado posteriormente

Para adicionar a origem code-behind, siga estas etapas:

1. Em **Gerenciador de soluções**, abra o arquivo `MainPage.xaml.cs`de origem code-behind. (Está agrupado em `MainPage.xaml`.)

1. Substitua o conteúdo do arquivo pelo seguinte trecho de código:

    ```csharp
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Audio;
    using Microsoft.CognitiveServices.Speech.Dialog;
    using System;
    using System.Diagnostics;
    using System.IO;
    using System.Text;
    using Windows.Foundation;
    using Windows.Storage.Streams;
    using Windows.UI.Xaml;
    using Windows.UI.Xaml.Controls;
    using Windows.UI.Xaml.Media;

    namespace helloworld
    {
        public sealed partial class MainPage : Page
        {
            private DialogServiceConnector connector;

            private enum NotifyType
            {
                StatusMessage,
                ErrorMessage
            };

            public MainPage()
            {
                this.InitializeComponent();
            }

            private async void EnableMicrophone_ButtonClicked(
                object sender, RoutedEventArgs e)
            {
                bool isMicAvailable = true;
                try
                {
                    var mediaCapture = new Windows.Media.Capture.MediaCapture();
                    var settings = 
                        new Windows.Media.Capture.MediaCaptureInitializationSettings();
                    settings.StreamingCaptureMode = 
                        Windows.Media.Capture.StreamingCaptureMode.Audio;
                    await mediaCapture.InitializeAsync(settings);
                }
                catch (Exception)
                {
                    isMicAvailable = false;
                }
                if (!isMicAvailable)
                {
                    await Windows.System.Launcher.LaunchUriAsync(
                        new Uri("ms-settings:privacy-microphone"));
                }
                else
                {
                    NotifyUser("Microphone was enabled", NotifyType.StatusMessage);
                }
            }

            private void NotifyUser(
                string strMessage, NotifyType type = NotifyType.StatusMessage)
            {
                // If called from the UI thread, then update immediately.
                // Otherwise, schedule a task on the UI thread to perform the update.
                if (Dispatcher.HasThreadAccess)
                {
                    UpdateStatus(strMessage, type);
                }
                else
                {
                    var task = Dispatcher.RunAsync(
                        Windows.UI.Core.CoreDispatcherPriority.Normal, 
                        () => UpdateStatus(strMessage, type));
                }
            }

            private void UpdateStatus(string strMessage, NotifyType type)
            {
                switch (type)
                {
                    case NotifyType.StatusMessage:
                        StatusBorder.Background = new SolidColorBrush(
                            Windows.UI.Colors.Green);
                        break;
                    case NotifyType.ErrorMessage:
                        StatusBorder.Background = new SolidColorBrush(
                            Windows.UI.Colors.Red);
                        break;
                }
                StatusBlock.Text += string.IsNullOrEmpty(StatusBlock.Text) 
                    ? strMessage : "\n" + strMessage;

                if (!string.IsNullOrEmpty(StatusBlock.Text))
                {
                    StatusBorder.Visibility = Visibility.Visible;
                    StatusPanel.Visibility = Visibility.Visible;
                }
                else
                {
                    StatusBorder.Visibility = Visibility.Collapsed;
                    StatusPanel.Visibility = Visibility.Collapsed;
                }
                // Raise an event if necessary to enable a screen reader 
                // to announce the status update.
                var peer = Windows.UI.Xaml.Automation.Peers.FrameworkElementAutomationPeer.FromElement(StatusBlock);
                if (peer != null)
                {
                    peer.RaiseAutomationEvent(
                        Windows.UI.Xaml.Automation.Peers.AutomationEvents.LiveRegionChanged);
                }
            }

            // Waits for and accumulates all audio associated with a given 
            // PullAudioOutputStream and then plays it to the MediaElement. Long spoken 
            // audio will create extra latency and a streaming playback solution 
            // (that plays audio while it continues to be received) should be used -- 
            // see the samples for examples of this.
            private void SynchronouslyPlayActivityAudio(
                PullAudioOutputStream activityAudio)
            {
                var playbackStreamWithHeader = new MemoryStream();
                playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("RIFF"), 0, 4); // ChunkID
                playbackStreamWithHeader.Write(BitConverter.GetBytes(UInt32.MaxValue), 0, 4); // ChunkSize: max
                playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("WAVE"), 0, 4); // Format
                playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("fmt "), 0, 4); // Subchunk1ID
                playbackStreamWithHeader.Write(BitConverter.GetBytes(16), 0, 4); // Subchunk1Size: PCM
                playbackStreamWithHeader.Write(BitConverter.GetBytes(1), 0, 2); // AudioFormat: PCM
                playbackStreamWithHeader.Write(BitConverter.GetBytes(1), 0, 2); // NumChannels: mono
                playbackStreamWithHeader.Write(BitConverter.GetBytes(16000), 0, 4); // SampleRate: 16kHz
                playbackStreamWithHeader.Write(BitConverter.GetBytes(32000), 0, 4); // ByteRate
                playbackStreamWithHeader.Write(BitConverter.GetBytes(2), 0, 2); // BlockAlign
                playbackStreamWithHeader.Write(BitConverter.GetBytes(16), 0, 2); // BitsPerSample: 16-bit
                playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("data"), 0, 4); // Subchunk2ID
                playbackStreamWithHeader.Write(BitConverter.GetBytes(UInt32.MaxValue), 0, 4); // Subchunk2Size

                byte[] pullBuffer = new byte[2056];

                uint lastRead = 0;
                do
                {
                    lastRead = activityAudio.Read(pullBuffer);
                    playbackStreamWithHeader.Write(pullBuffer, 0, (int)lastRead);
                }
                while (lastRead == pullBuffer.Length);

                var task = Dispatcher.RunAsync(
                    Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
                {
                    mediaElement.SetSource(
                        playbackStreamWithHeader.AsRandomAccessStream(), "audio/wav");
                    mediaElement.Play();
                });
            }

            private void InitializeDialogServiceConnector()
            {
                // New code will go here
            }

            private async void ListenButton_ButtonClicked(
                object sender, RoutedEventArgs e)
            {
                // New code will go here
            }
        }
    }
    ```

1. Adicione o seguinte trecho de código ao corpo do método `InitializeDialogServiceConnector`de. Esse código cria o `DialogServiceConnector` com suas informações de assinatura.

    ```csharp
    // create a DialogServiceConfig by providing a bot secret key 
    // and Cognitive Services subscription key
    // the RecoLanguage property is optional (default en-US); 
    // note that only en-US is supported in Preview
    const string channelSecret = "YourChannelSecret"; // Your channel secret
    const string speechSubscriptionKey = "YourSpeechSubscriptionKey"; // Your subscription key

    // Your subscription service region. 
    // Note: only a subset of regions are currently supported
    const string region = "YourServiceRegion"; 

    var botConfig = DialogServiceConfig.FromBotSecret(
        channelSecret, speechSubscriptionKey, region);
    botConfig.SetProperty(PropertyId.SpeechServiceConnection_RecoLanguage, "en-US");
    connector = new DialogServiceConnector(botConfig);
    ```

   > [!NOTE]
   > A Direct line Speech (visualização) está disponível atualmente em um subconjunto de regiões de serviços de fala. Consulte [a lista de regiões com suporte para assistentes virtuais primeiro de voz](regions.md#voice-first-virtual-assistants) e garanta que seus recursos sejam implantados em uma dessas regiões.

   > [!NOTE]
   > Para obter informações sobre como configurar o bot e recuperar um segredo de canal, consulte a documentação do bot Framework para [o canal de fala de linha direta](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

1. Substitua as cadeias `YourSpeechSubscriptionKey`de caracteres `YourServiceRegion` `YourChannelSecret`, e pelos seus próprios valores para o bot, a assinatura de fala e a [região](regions.md).

1. Acrescente o trecho de código a seguir ao final do corpo do método `InitializeDialogServiceConnector`de. Esse código configura manipuladores para eventos com os quais `DialogServiceConnector` conta com o para comunicar suas atividades de bot, resultados de reconhecimento de fala e outras informações.

    ```csharp
    // ActivityReceived is the main way your bot will communicate with the client 
    // and uses bot framework activities
    connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
    {
        NotifyUser(
            $"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

        if (activityReceivedEventArgs.HasAudio)
        {
            SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
        }
    };

    // Canceled will be signaled when a turn is aborted or experiences an error condition
    connector.Canceled += (sender, canceledEventArgs) =>
    {
        NotifyUser($"Canceled, reason={canceledEventArgs.Reason}");
        if (canceledEventArgs.Reason == CancellationReason.Error)
        {
            NotifyUser(
                $"Error: code={canceledEventArgs.ErrorCode}, details={canceledEventArgs.ErrorDetails}");
        }
    };

    // Recognizing (not 'Recognized') will provide the intermediate recognized text 
    // while an audio stream is being processed
    connector.Recognizing += (sender, recognitionEventArgs) =>
    {
        NotifyUser($"Recognizing! in-progress text={recognitionEventArgs.Result.Text}");
    };

    // Recognized (not 'Recognizing') will provide the final recognized text 
    // once audio capture is completed
    connector.Recognized += (sender, recognitionEventArgs) =>
    {
        NotifyUser($"Final speech-to-text result: '{recognitionEventArgs.Result.Text}'");
    };

    // SessionStarted will notify when audio begins flowing to the service for a turn
    connector.SessionStarted += (sender, sessionEventArgs) =>
    {
        NotifyUser($"Now Listening! Session started, id={sessionEventArgs.SessionId}");
    };

    // SessionStopped will notify when a turn is complete and 
    // it's safe to begin listening again
    connector.SessionStopped += (sender, sessionEventArgs) =>
    {
        NotifyUser($"Listening complete. Session ended, id={sessionEventArgs.SessionId}");
    };
    ```

1. Adicione o trecho de código a seguir ao corpo do `ListenButton_ButtonClicked` método `MainPage` na classe. Esse código configura `DialogServiceConnector` para escutar, pois você já estabeleceu a configuração e registrou os manipuladores de eventos.

    ```csharp
    if (connector == null)
    {
        InitializeDialogServiceConnector();
        // Optional step to speed up first interaction: if not called, 
        // connection happens automatically on first use
        var connectTask = connector.ConnectAsync();
    }

    try
    {
        // Start sending audio to your speech-enabled bot
        var listenTask = connector.ListenOnceAsync();

        // You can also send activities to your bot as JSON strings -- 
        // Microsoft.Bot.Schema can simplify this
        string speakActivity = 
            @"{""type"":""message"",""text"":""Greeting Message"", ""speak"":""Hello there!""}";
        await connector.SendActivityAsync(speakActivity);

    }
    catch (Exception ex)
    {
        NotifyUser($"Exception: {ex.ToString()}", NotifyType.ErrorMessage);
    }
    ```

1. Na barra de menus, escolha **arquivo** > **salvar tudo** para salvar suas alterações.

## <a name="build-and-run-the-application"></a>Compilar e executar a aplicação

Agora você está pronto para criar e testar seu aplicativo.

1. Na barra de menus, escolha **Compilar** > compilar**solução** para compilar o aplicativo. Agora o código deverá ser compilado sem erros.

1. Escolha **depurar** > **Iniciar Depuração** (ou pressione **F5**) para iniciar o aplicativo. A janela **HelloWorld** é exibida.

   ![Aplicativo de assistente virtual UWP de C# exemplo em-início rápido](media/sdk/qs-virtual-assistant-uwp-helloworld-window.png)

1. Selecione **habilitar microfone**e, quando a solicitação de permissão de acesso for exibida, selecione **Sim**.

   ![Solicitação de permissão de acesso ao microfone](media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Selecione **conversar com o bot**e fale uma frase ou sentença em inglês no microfone do seu dispositivo. Sua fala é transmitida para o canal de fala de linha direta e transcrita para texto, que aparece na janela.
<!--
    ![Successful bot response](media/voice-first-virtual-assistants/quickstart-cs-uwp-bot-successful-turn.png)
-->
## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Create and deploy a basic bot](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0) (Criar e implementar um bot básico)

## <a name="see-also"></a>Consulte também

- [Sobre os assistentes virtuais da primeira voz](voice-first-virtual-assistants.md)
- [Obtenha uma chave de assinatura de serviços de fala gratuitamente](get-started.md)
- [Palavras de ativação personalizadas](speech-devices-sdk-create-kws.md)
- [Conecte a fala de linha direta ao bot](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
- [Explorar C# exemplos no github](https://aka.ms/csspeech/samples)
