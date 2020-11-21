---
title: Integrar numa aplicação cliente com o SDK de Voz
titleSuffix: Azure Cognitive Services
description: como fazer pedidos para uma aplicação de Comandos Personalizados publicada a partir do SDK de fala em execução numa aplicação UWP.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.custom: devx-track-csharp
ms.openlocfilehash: fa3a6d16b79800043bdcd3f183dd86fa278dd1a9
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026032"
---
# <a name="integrate-with-a-client-application-using-speech-sdk"></a>Integre com uma aplicação de cliente usando a Speech SDK

Neste artigo, aprende-se a fazer pedidos para uma aplicação de Comandos Personalizados publicada a partir do SDK de fala em execução numa aplicação UWP. Para estabelecer uma ligação à aplicação Comandos Personalizados, é necessário:

- Publique uma aplicação de Comandos Personalizados e obtenha um identificador de aplicações (ID da aplicação)
- Crie uma aplicação de cliente Universal Windows Platform (UWP) utilizando o Speech SDK para lhe permitir falar com a sua aplicação de Comandos Personalizados

## <a name="prerequisites"></a>Pré-requisitos

É necessária uma aplicação de Comandos Personalizados para completar este artigo. Se não criou uma aplicação de Comandos Personalizados, pode fazê-lo seguindo os arranques rápidos:
> [!div class = "checklist"]
> * [Criar uma aplicação de comandos personalizados](quickstart-custom-commands-application.md)

Também vai precisar de:
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) ou superior. Este guia é baseado no Visual Studio 2019.
> * Uma chave de subscrição Azure para serviços de fala. [Obtenha um de graça](overview.md#try-the-speech-service-for-free) ou crie-o no [portal Azure](https://portal.azure.com)
> * [Ativar o seu dispositivo para o desenvolvimento](/windows/uwp/get-started/enable-your-device-for-development)

## <a name="step-1-publish-custom-commands-application"></a>Passo 1: Publicar aplicação de comandos personalizados

1. Abra a sua aplicação de Comandos Personalizados previamente criada
1. Ir a **Definições**, selecione **recurso LUIS**
1. Se **o recurso de previsão** não for atribuído, selecione uma chave de previsão de consulta ou crie uma nova

    A chave de previsão de consulta é sempre necessária antes de publicar uma aplicação. Para mais informações sobre os recursos LUIS, consulte [o Recurso LUIS](../luis/luis-how-to-azure-subscription.md)

1. Volte a editar Comandos, **Selecione Publicar**

   > [!div class="mx-imgBorder"]
   > ![Publicar a aplicação](media/custom-commands/setup-speech-sdk-publish-application.png)

1. Copie o ID da aplicação a partir da notificação de publicação para posterior utilização
1. Copie a chave de recursos de voz para utilização posterior

## <a name="step-2-create-a-visual-studio-project"></a>Passo 2: Criar um projeto de Estúdio Visual

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="step-3-add-sample-code"></a>Passo 3: Adicionar código de amostra

Neste passo, adicionamos o código XAML que define a interface de utilizador da aplicação e adicionamos a implementação do código C# por trás.

### <a name="xaml-code"></a>Código XAML

Crie a interface de utilizador da aplicação adicionando o código XAML.

1. In **Solution Explorer,** open `MainPage.xaml`

1. Na vista XAML do designer, substitua todo o conteúdo pelo seguinte corte de código:

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
                       Margin="0,10,10,0" Click="EnableMicrophone_ButtonClicked"
                       Height="35"/>
               <Button x:Name="ListenButton" Content="Talk"
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

A vista Design é atualizada para mostrar a interface de utilizador da aplicação.

### <a name="c-code-behind-source"></a>C# fonte por trás do código

Adicione a fonte por trás do código para que a aplicação funcione como esperado. A fonte por trás do código inclui:

- `using`Declarações necessárias para os `Speech` espaços e `Speech.Dialog` nomes
- Uma implementação simples para garantir o acesso ao microfone, ligada a um manipulador de botões
- Ajudantes básicos da UI para apresentar mensagens e erros na aplicação
- Um ponto de aterragem para o caminho do código de inicialização que será povoado mais tarde
- Um ajudante para reproduzir texto-a-discurso (sem suporte de streaming)
- Um manipulador de botões vazio para começar a ouvir que será povoado mais tarde

Adicione a fonte por trás do código da seguinte forma:

1. No **Solution Explorer,** abra o ficheiro de código por trás `MainPage.xaml.cs` (agrupado em `MainPage.xaml` )

1. Substitua o conteúdo do ficheiro pelo seguinte código: 

   ```csharp
   using Microsoft.CognitiveServices.Speech;
   using Microsoft.CognitiveServices.Speech.Audio;
   using Microsoft.CognitiveServices.Speech.Dialog;
   using System;
   using System.IO;
   using System.Text;
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
    > [!NOTE]
    > Se vir erro: "O tipo 'Objeto' é definido num conjunto que não é referenciado"
    > 1. Cliente certo a sua solução.
    > 1. Escolha **Gerir pacotes nuget para solução,** selecione **atualizações** 
    > 1. Se vir **microsoft.NETCore.UniversalWindowsPlatform** na lista de atualizações, atualize **Microsoft.NETCore.UniversalWindowsPlatform** para a versão mais recente

1. Adicione o seguinte código ao corpo do método de `InitializeDialogServiceConnector`

   ```csharp
   // This code creates the `DialogServiceConnector` with your subscription information.
   // create a DialogServiceConfig by providing a Custom Commands application id and Cognitive Services subscription key
   // the RecoLanguage property is optional (default en-US); note that only en-US is supported in Preview
   const string speechCommandsApplicationId = "YourApplicationId"; // Your application id
   const string speechSubscriptionKey = "YourSpeechSubscriptionKey"; // Your subscription key
   const string region = "YourServiceRegion"; // The subscription service region. Note: only 'westus2' is currently supported

   var speechCommandsConfig = CustomCommandsConfig.FromSubscription(speechCommandsApplicationId, speechSubscriptionKey, region);
   speechCommandsConfig.SetProperty(PropertyId.SpeechServiceConnection_RecoLanguage, "en-us");
   connector = new DialogServiceConnector(speechCommandsConfig);
   ```

1. Substitua as `YourApplicationId` cordas, `YourSpeechSubscriptionKey` e pelos seus `YourServiceRegion` próprios valores para a sua app, subscrição de discurso e [região](regions.md)

1. Anexar o seguinte corte de código para o fim do corpo método de `InitializeDialogServiceConnector`

   ```csharp
   //
   // This code sets up handlers for events relied on by `DialogServiceConnector` to communicate its activities,
   // speech recognition results, and other information.
   //
   // ActivityReceived is the main way your client will receive messages, audio, and events
   connector.ActivityReceived += (sender, activityReceivedEventArgs) =>
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

1. Adicione o seguinte corte de código ao corpo do `ListenButton_ButtonClicked` método na `MainPage` classe

   ```csharp
   // This code sets up `DialogServiceConnector` to listen, since you already established the configuration and
   // registered the event handlers.
   if (connector == null)
   {
       InitializeDialogServiceConnector();
       // Optional step to speed up first interaction: if not called,
       // connection happens automatically on first use
       var connectTask = connector.ConnectAsync();
   }

   try
   {
       // Start sending audio
       await connector.ListenOnceAsync();
   }
   catch (Exception ex)
   {
       NotifyUser($"Exception: {ex.ToString()}", NotifyType.ErrorMessage);
   }
   ```

1. A partir da barra de menu, escolha **'Guardar**  >  **tudo'** para guardar as alterações

## <a name="try-it-out"></a>Experimente

1. A partir da barra de menu, escolha **Build**  >  **Build Solution** para construir a aplicação. O código deve ser compilado sem erros.

1. Escolha **Debug**  >  **Start Debugging** (ou prima **F5**) para iniciar a aplicação. A janela **helloworld** aparece.

   ![Experimente a aplicação de assistente virtual UWP em C# - quickstart](media/sdk/qs-voice-assistant-uwp-helloworld-window.png)

1. Selecione **O microfone de ativação**. Se o pedido de autorização de acesso aparecer, selecione **Sim**.

   ![Pedido de permissão de acesso ao microfone](media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. **Selecione Talk**, e diga uma frase ou frase em inglês no microfone do seu dispositivo. O seu discurso é transmitido para o canal Direct Line Speech e transcrito para texto, que aparece na janela.

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Como: enviar atividade para a aplicação do cliente (Pré-visualização)](./how-to-custom-commands-send-activity-to-client.md)