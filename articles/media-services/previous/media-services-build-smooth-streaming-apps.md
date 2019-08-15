---
title: Tutorial de Smooth Streaming aplicativo da Windows Store | Microsoft Docs
description: Saiba como usar os serviços de mídia do Azure para C# criar um aplicativo da Windows Store com um controle XML MediaElement para reproduzir conteúdo de fluxo suave.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 0fa5d8c5-3d5f-4886-ae55-fb6de4f5256d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: 9ff961638aa170948d51793a21e86d18dd7e1d80
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "69016797"
---
# <a name="how-to-build-a-smooth-streaming-windows-store-application"></a>Como criar um aplicativo Smooth Streaming Windows Store  

O SDK do cliente Smooth Streaming para Windows 8 permite que os desenvolvedores criem aplicativos da Windows Store que podem reproduzir conteúdo Smooth Streaming sob demanda e ao vivo. Além da reprodução básica de conteúdo de Smooth Streaming, o SDK também fornece recursos avançados, como proteção do Microsoft PlayReady, restrição de nível de qualidade, DVR ao vivo, troca de fluxo de áudio, escuta de atualizações de status (como alterações no nível de qualidade) e eventos de erro e assim por diante. Para obter mais informações sobre os recursos com suporte, consulte as [notas de versão](https://www.iis.net/learn/media/smooth-streaming/smooth-streaming-client-sdk-for-windows-8-release-notes). Para obter mais informações, consulte [Player Framework para Windows 8](https://playerframework.codeplex.com/). 

Este tutorial contém quatro lições:

1. Criar um aplicativo de armazenamento de Smooth Streaming básica
2. Adicionar uma barra deslizante para controlar o progresso da mídia
3. Selecionar fluxos de Smooth Streaming
4. Selecionar faixas de Smooth Streaming

## <a name="prerequisites"></a>Pré-requisitos
> [!NOTE]
> Os projetos da Windows Store versão 8,1 e anteriores não têm suporte no Visual Studio 2017.  Para obter mais informações, veja [Visual Studio 2017 Platform Targeting and Compatibility](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs) (Segmentação e Compatibilidade de Plataformas do Visual Studio 2017)

* Windows 8 32 bits ou 64 bits.
* Visual Studio versões 2012 a 2015.
* [SDK do Microsoft Smooth streaming client para Windows 8](https://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Home).

A solução concluída para cada lição pode ser baixada em exemplos de código do desenvolvedor MSDN (Galeria de códigos): 

* [Lição 1](https://code.msdn.microsoft.com/Smooth-Streaming-Client-0bb1471f) – um player de mídia simples do Windows 8 Smooth streaming, 
* [Lição 2](https://code.msdn.microsoft.com/A-simple-Windows-8-Smooth-ee98f63a) – um player de mídia simples do Windows 8 Smooth streaming com um controle de barra deslizante, 
* [Lição 3](https://code.msdn.microsoft.com/A-Windows-8-Smooth-883c3b44) – um player de mídia Smooth streaming do Windows 8 com seleção de fluxo,  
* [Lição 4](https://code.msdn.microsoft.com/A-Windows-8-Smooth-aa9e4907) -um player de mídia Smooth streaming do Windows 8 com seleção de controle.

## <a name="lesson-1-create-a-basic-smooth-streaming-store-application"></a>Lição 1: Criar um aplicativo de armazenamento de Smooth Streaming básica

Nesta lição, você criará um aplicativo da Windows Store com um controle MediaElement para reproduzir conteúdo de fluxo suave.  O aplicativo em execução é semelhante a:

![Smooth Streaming exemplo de aplicativo da Windows Store][PlayerApplication]

Para obter mais informações sobre como desenvolver aplicativos da Windows Store, consulte [desenvolver ótimos aplicativos para o Windows 8](https://msdn.microsoft.com/windows/apps/br229512.aspx). Esta lição contém os seguintes procedimentos:

1. Criar um projeto da Windows Store
2. Criar a interface do usuário (XAML)
3. Modificar o arquivo code-behind
4. Compilar e testar o aplicativo

### <a name="to-create-a-windows-store-project"></a>Para criar um projeto da Windows Store

1. Executar o Visual Studio; Há suporte para as versões 2012 a 2015.
1. No menu **FICHEIRO**, clique em **Novo** e, em seguida, clique em **Projeto**.
1. Na caixa de diálogo novo projeto, digite ou selecione os seguintes valores:

    | Nome | Value |
    | --- | --- |
    | Grupo de modelos |Instalação/modelos/Visual C#/Windows Store |
    | Modelo |Aplicativo em branco (XAML) |
    | Nome |SSPlayer |
    | Location |C:\SSTutorials |
    | Nome da solução |SSPlayer |
    | Criar diretório para solução |Selecione |

1. Clique em **OK**.

### <a name="to-add-a-reference-to-the-smooth-streaming-client-sdk"></a>Para adicionar uma referência ao SDK do cliente do Smooth Streaming

1. Em Gerenciador de Soluções, clique com o botão direito do mouse em **SSPlayer**e clique em **Adicionar referência**.
1. Escreva ou selecione os seguintes valores:

    | Nome | Value |
    | --- | --- |
    | Grupo de referência |Windows/extensões |
    | Referência |Selecione o SDK do Microsoft Smooth Streaming Client para Windows 8 e C++ pacote de tempo de execução do Microsoft Visual |

1. Clique em **OK**. 

Depois de adicionar as referências, você deve selecionar a plataforma de destino (x64 ou x86), a adição de referências não funcionará para nenhuma configuração de plataforma de CPU.  No Gerenciador de soluções, você verá uma marca de aviso amarela para essas referências adicionadas.

### <a name="to-design-the-player-user-interface"></a>Para criar a interface do usuário do Player

1. Em Gerenciador de Soluções, clique duas vezes em **MainPage. XAML** para abri-lo no modo de exibição de design.
2. Localize as  **&lt;marcas&gt; Grid** e **/GriddoarquivoXAMLecoleocódigoaseguirentreasduasmarcas:&gt; &lt;**

   ```xml
         <Grid.RowDefinitions>

            <RowDefinition Height="20"/>    <!-- spacer -->
            <RowDefinition Height="50"/>    <!-- media controls -->
            <RowDefinition Height="100*"/>  <!-- media element -->
            <RowDefinition Height="80*"/>   <!-- media stream and track selection -->
            <RowDefinition Height="50"/>    <!-- status bar -->
         </Grid.RowDefinitions>

         <StackPanel Name="spMediaControl" Grid.Row="1" Orientation="Horizontal">
            <TextBlock x:Name="tbSource" Text="Source :  " FontSize="16" FontWeight="Bold" VerticalAlignment="Center" />
            <TextBox x:Name="txtMediaSource" Text="https://ecn.channel9.msdn.com/o9/content/smf/smoothcontent/elephantsdream/Elephants_Dream_1024-h264-st-aac.ism/manifest" FontSize="10" Width="700" Margin="0,4,0,10" />
            <Button x:Name="btnSetSource" Content="Set Source" Width="111" Height="43" Click="btnSetSource_Click"/>
            <Button x:Name="btnPlay" Content="Play" Width="111" Height="43" Click="btnPlay_Click"/>
            <Button x:Name="btnPause" Content="Pause"  Width="111" Height="43" Click="btnPause_Click"/>
            <Button x:Name="btnStop" Content="Stop"  Width="111" Height="43" Click="btnStop_Click"/>
            <CheckBox x:Name="chkAutoPlay" Content="Auto Play" Height="55" Width="Auto" IsChecked="{Binding AutoPlay, ElementName=mediaElement, Mode=TwoWay}"/>
            <CheckBox x:Name="chkMute" Content="Mute" Height="55" Width="67" IsChecked="{Binding IsMuted, ElementName=mediaElement, Mode=TwoWay}"/>
         </StackPanel>

         <StackPanel Name="spMediaElement" Grid.Row="2" Height="435" Width="1072"
                    HorizontalAlignment="Center" VerticalAlignment="Center">
            <MediaElement x:Name="mediaElement" Height="356" Width="924" MinHeight="225"
                          HorizontalAlignment="Center" VerticalAlignment="Center" 
                          AudioCategory="BackgroundCapableMedia" />
            <StackPanel Orientation="Horizontal">
                <Slider x:Name="sliderProgress" Width="924" Height="44"
                        HorizontalAlignment="Center" VerticalAlignment="Center"
                        PointerPressed="sliderProgress_PointerPressed"/>
                <Slider x:Name="sliderVolume" 
                        HorizontalAlignment="Right" VerticalAlignment="Center" Orientation="Vertical" 
                        Height="79" Width="148" Minimum="0" Maximum="1" StepFrequency="0.1" 
                        Value="{Binding Volume, ElementName=mediaElement, Mode=TwoWay}" 
                        ToolTipService.ToolTip="{Binding Value, RelativeSource={RelativeSource Mode=Self}}"/>
            </StackPanel>
         </StackPanel>

         <StackPanel Name="spStatus" Grid.Row="4" Orientation="Horizontal">
            <TextBlock x:Name="tbStatus" Text="Status :  " 
               FontSize="16" FontWeight="Bold" VerticalAlignment="Center" HorizontalAlignment="Center" />
            <TextBox x:Name="txtStatus" FontSize="10" Width="700" VerticalAlignment="Center"/>
         </StackPanel>
   ```
   O controle MediaElement é usado para reproduzir mídia. O controle deslizante chamado sliderProgress será usado na próxima lição para controlar o progresso da mídia.
3. Pressione **Ctrl + S** para salvar o arquivo.

O controle MediaElement não oferece suporte a Smooth Streaming conteúdo pronto para uso. Para habilitar o suporte a Smooth Streaming, você deve registrar o Smooth Streaming o manipulador de fluxo de bytes por extensão de nome de arquivo e tipo MIME.  Para se registrar, use o método deve. RegisterByteStreamHandler do namespace Windows. Media.

Nesse arquivo XAML, alguns manipuladores de eventos estão associados aos controles.  Você deve definir esses manipuladores de eventos.

### <a name="to-modify-the-code-behind-file"></a>Para modificar o arquivo code-behind

1. Em Gerenciador de Soluções, clique com o botão direito do mouse em **MainPage. XAML**e clique em **Exibir código**.
2. Na parte superior do arquivo, adicione a seguinte instrução Using:
   
        using Windows.Media;
3. No início da classe **MainPage** , adicione o seguinte membro de dados:
   
         private MediaExtensionManager extensions = new MediaExtensionManager();
4. No final do construtor **MainPage** , adicione as duas linhas a seguir:
   
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "text/xml");
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "application/vnd.ms-sstr+xml");
5. No final da classe **MainPage** , Cole o seguinte código:
   ```csharp
         # region UI Button Click Events
         private void btnPlay_Click(object sender, RoutedEventArgs e)
         {

         mediaElement.Play();
         txtStatus.Text = "MediaElement is playing ...";
         }
         private void btnPause_Click(object sender, RoutedEventArgs e)
         {

         mediaElement.Pause();
         txtStatus.Text = "MediaElement is paused";
         }
         private void btnSetSource_Click(object sender, RoutedEventArgs e)
         {

         sliderProgress.Value = 0;
         mediaElement.Source = new Uri(txtMediaSource.Text);

         if (chkAutoPlay.IsChecked == true)
         {
             txtStatus.Text = "MediaElement is playing ...";
         }
         else
         {
             txtStatus.Text = "Click the Play button to play the media source.";
         }
         }
         private void btnStop_Click(object sender, RoutedEventArgs e)
         {

         mediaElement.Stop();
         txtStatus.Text = "MediaElement is stopped";
         }
         private void sliderProgress_PointerPressed(object sender, PointerRoutedEventArgs e)
         {

         txtStatus.Text = "Seek to position " + sliderProgress.Value;
         mediaElement.Position = new TimeSpan(0, 0, (int)(sliderProgress.Value));
         }
         # endregion
   ```
   O manipulador de eventos sliderProgress_PointerPressed é definido aqui.  Há mais trabalhos a serem usados para fazê-lo funcionar, que será abordado na próxima lição deste tutorial.
6. Pressione **Ctrl + S** para salvar o arquivo.

O arquivo code-behind concluído deverá ter esta aparência:

![CodeView no Visual Studio de Smooth Streaming aplicativo da Windows Store][CodeViewPic]

### <a name="to-compile-and-test-the-application"></a>Para compilar e testar o aplicativo

1. No menu **Compilar** , clique em **Configuration Manager**.
2. Altere a **plataforma de solução ativa** para corresponder à sua plataforma de desenvolvimento.
3. Pressione **F6** para compilar o projeto. 
4. Prima **F5** para executar a aplicação.
5. Na parte superior do aplicativo, você pode usar a URL de Smooth Streaming padrão ou inserir uma diferente. 
6. Clique em **definir origem**. Como a **reprodução automática** está habilitada por padrão, a mídia deve ser reproduzida automaticamente.  Você pode controlar a mídia usando os botões **reproduzir**, pausar e **parar** .  Você pode controlar o volume de mídia usando o controle deslizante vertical.  No entanto, o controle deslizante horizontal para controlar o progresso da mídia ainda não está totalmente implementado. 

Você concluiu o lição 1.  Nesta lição, você usará um controle MediaElement para reproduzir Smooth Streaming conteúdo.  Na próxima lição, você adicionará um controle deslizante para controlar o progresso do conteúdo do Smooth Streaming.

## <a name="lesson-2-add-a-slider-bar-to-control-the-media-progress"></a>Lição 2: Adicionar uma barra deslizante para controlar o progresso da mídia

Na lição 1, você criou um aplicativo da Windows Store com um controle XAML MediaElement para reproduzir Smooth Streaming conteúdo de mídia.  Ele vem com algumas funções básicas de mídia como iniciar, parar e pausar.  Nesta lição, você adicionará um controle de barra deslizante ao aplicativo.

Neste tutorial, usaremos um temporizador para atualizar a posição do controle deslizante com base na posição atual do controle MediaElement.  A hora de início e de término do controle deslizante também precisam ser atualizadas no caso de conteúdo ao vivo.  Isso pode ser tratado melhor no evento de atualização de origem adaptável.

As fontes de mídia são objetos que geram dados de mídia.  O resolvedor de origem usa um fluxo de URL ou de bytes e cria a origem de mídia apropriada para esse conteúdo.  O resolvedor de origem é a maneira padrão para que os aplicativos criem fontes de mídia. 

Esta lição contém os seguintes procedimentos:

1. Registrar o manipulador de Smooth Streaming 
2. Adicionar manipuladores de eventos no nível do Gerenciador de origem adaptável
3. Adicionar os manipuladores de eventos de nível de origem adaptável
4. Adicionar manipuladores de eventos MediaElement
5. Adicionar código relacionado à barra de controle deslizante
6. Compilar e testar o aplicativo

### <a name="to-register-the-smooth-streaming-byte-stream-handler-and-pass-the-propertyset"></a>Para registrar o manipulador de fluxo de bytes Smooth Streaming e passar o PropertySet

1. Em Gerenciador de Soluções, clique com o botão direito do mouse em **MainPage. XAML**e clique em **Exibir código**.
2. No início do arquivo, adicione a seguinte instrução Using:

   ```csharp
        using Microsoft.Media.AdaptiveStreaming;
   ```
3. No início da classe MainPage, adicione os seguintes membros de dados:

   ```csharp
         private Windows.Foundation.Collections.PropertySet propertySet = new Windows.Foundation.Collections.PropertySet();             
         private IAdaptiveSourceManager adaptiveSourceManager;
   ```
4. Dentro do construtor **MainPage** , adicione o código a seguir depois **disso. Inicializar componentes ();** linha e as linhas de código de registro escritas na lição anterior:

   ```csharp
        // Gets the default instance of AdaptiveSourceManager which manages Smooth 
        //Streaming media sources.
        adaptiveSourceManager = AdaptiveSourceManager.GetDefault();
        // Sets property key value to AdaptiveSourceManager default instance.
        // {A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}" must be hardcoded.
        propertySet["{A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}"] = adaptiveSourceManager;
   ```
5. Dentro do construtor **MainPage** , modifique os dois métodos RegisterByteStreamHandler para adicionar os parâmetros em diante:

   ```csharp
         // Registers Smooth Streaming byte-stream handler for ".ism" extension and, 
         // "text/xml" and "application/vnd.ms-ss" mime-types and pass the propertyset. 
         // http://*.ism/manifest URI resources will be resolved by Byte-stream handler.
         extensions.RegisterByteStreamHandler(

            "Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", 
            ".ism", 
            "text/xml", 
            propertySet );
         extensions.RegisterByteStreamHandler(

            "Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", 
            ".ism", 
            "application/vnd.ms-sstr+xml", 
         propertySet);
   ```
6. Pressione **Ctrl + S** para salvar o arquivo.

### <a name="to-add-the-adaptive-source-manager-level-event-handler"></a>Para adicionar o manipulador de eventos de nível de Gerenciador de origem adaptável

1. Em Gerenciador de Soluções, clique com o botão direito do mouse em **MainPage. XAML**e clique em **Exibir código**.
2. Dentro da classe **MainPage** , adicione o seguinte membro de dados:

   ```csharp
     private AdaptiveSource adaptiveSource = null;
   ```
3. No final da classe **MainPage** , adicione o seguinte manipulador de eventos:

   ```csharp
         # region Adaptive Source Manager Level Events
         private void mediaElement_AdaptiveSourceOpened(AdaptiveSource sender, AdaptiveSourceOpenedEventArgs args)
         {

            adaptiveSource = args.AdaptiveSource;
         }

         # endregion Adaptive Source Manager Level Events
   ```
4. No final do construtor **MainPage** , adicione a seguinte linha para assinar o evento de origem adaptável aberto:

   ```csharp
         adaptiveSourceManager.AdaptiveSourceOpenedEvent += 
           new AdaptiveSourceOpenedEventHandler(mediaElement_AdaptiveSourceOpened);
   ```
5. Pressione **Ctrl + S** para salvar o arquivo.

### <a name="to-add-adaptive-source-level-event-handlers"></a>Para adicionar manipuladores de eventos de nível de fonte adaptável

1. Em Gerenciador de Soluções, clique com o botão direito do mouse em **MainPage. XAML**e clique em **Exibir código**.
2. Dentro da classe **MainPage** , adicione o seguinte membro de dados:

   ```csharp
     private AdaptiveSourceStatusUpdatedEventArgs adaptiveSourceStatusUpdate; 
     private Manifest manifestObject;
   ```
3. No final da classe **MainPage** , adicione os seguintes manipuladores de eventos:

   ```csharp
         # region Adaptive Source Level Events
         private void mediaElement_ManifestReady(AdaptiveSource sender, ManifestReadyEventArgs args)
         {

            adaptiveSource = args.AdaptiveSource;
            manifestObject = args.AdaptiveSource.Manifest;
         }

         private void mediaElement_AdaptiveSourceStatusUpdated(AdaptiveSource sender, AdaptiveSourceStatusUpdatedEventArgs args)
         {

            adaptiveSourceStatusUpdate = args;
         }

         private void mediaElement_AdaptiveSourceFailed(AdaptiveSource sender, AdaptiveSourceFailedEventArgs args)
         {

            txtStatus.Text = "Error: " + args.HttpResponse;
         }

         # endregion Adaptive Source Level Events
   ```
4. No final do método **MediaElement AdaptiveSourceOpened** , adicione o seguinte código para assinar os eventos:

   ```csharp
         adaptiveSource.ManifestReadyEvent +=

                    mediaElement_ManifestReady;
         adaptiveSource.AdaptiveSourceStatusUpdatedEvent += 

            mediaElement_AdaptiveSourceStatusUpdated;
         adaptiveSource.AdaptiveSourceFailedEvent += 

            mediaElement_AdaptiveSourceFailed;
   ```
5. Pressione **Ctrl + S** para salvar o arquivo.

Os mesmos eventos também estão disponíveis no nível do Gerenciador de origem adaptável, que pode ser usado para manipular a funcionalidade comum a todos os elementos de mídia no aplicativo. Cada AdaptiveSource inclui seus próprios eventos e todos os eventos de AdaptiveSource serão colocados em cascata em AdaptiveSourceManager.

### <a name="to-add-media-element-event-handlers"></a>Para adicionar manipuladores de eventos de elemento de mídia

1. Em Gerenciador de Soluções, clique com o botão direito do mouse em **MainPage. XAML**e clique em **Exibir código**.
2. No final da classe **MainPage** , adicione os seguintes manipuladores de eventos:

   ```csharp
         # region Media Element Event Handlers
         private void MediaOpened(object sender, RoutedEventArgs e)
         {

            txtStatus.Text = "MediaElement opened";
         }

         private void MediaFailed(object sender, ExceptionRoutedEventArgs e)
         {

            txtStatus.Text= "MediaElement failed: " + e.ErrorMessage;
         }

         private void MediaEnded(object sender, RoutedEventArgs e)
         {

            txtStatus.Text ="MediaElement ended.";
         }

         # endregion Media Element Event Handlers
   ```
3. No final do construtor **MainPage** , adicione o seguinte código para subscrito aos eventos:

   ```csharp
         mediaElement.MediaOpened += MediaOpened;
         mediaElement.MediaEnded += MediaEnded;
         mediaElement.MediaFailed += MediaFailed;
   ```
4. Pressione **Ctrl + S** para salvar o arquivo.

### <a name="to-add-slider-bar-related-code"></a>Para adicionar um código relacionado à barra de controle deslizante

1. Em Gerenciador de Soluções, clique com o botão direito do mouse em **MainPage. XAML**e clique em **Exibir código**.
2. No início do arquivo, adicione a seguinte instrução Using:

   ```csharp
        using Windows.UI.Core;
   ```
3. Dentro da classe **MainPage** , adicione os seguintes membros de dados:

   ```csharp
         public static CoreDispatcher _dispatcher;
         private DispatcherTimer sliderPositionUpdateDispatcher;
   ```
4. No final do construtor **MainPage** , adicione o seguinte código:

   ```csharp
         _dispatcher = Window.Current.Dispatcher;
         PointerEventHandler pointerpressedhandler = new PointerEventHandler(sliderProgress_PointerPressed);
         sliderProgress.AddHandler(Control.PointerPressedEvent, pointerpressedhandler, true);    
   ```
5. No final da classe **MainPage** , adicione o seguinte código:

   ```csharp
         # region sliderMediaPlayer
         private double SliderFrequency(TimeSpan timevalue)
         {

            long absvalue = 0;
            double stepfrequency = -1;

            if (manifestObject != null)
            {
                absvalue = manifestObject.Duration - (long)manifestObject.StartTime;
            }
            else
            {
                absvalue = mediaElement.NaturalDuration.TimeSpan.Ticks;
            }

            TimeSpan totalDVRDuration = new TimeSpan(absvalue);

            if (totalDVRDuration.TotalMinutes >= 10 && totalDVRDuration.TotalMinutes < 30)
            {
               stepfrequency = 10;
            }
            else if (totalDVRDuration.TotalMinutes >= 30 
                     && totalDVRDuration.TotalMinutes < 60)
            {
                stepfrequency = 30;
            }
            else if (totalDVRDuration.TotalHours >= 1)
            {
                stepfrequency = 60;
            }

            return stepfrequency;
         }

         void updateSliderPositionoNTicks(object sender, object e)
         {

            sliderProgress.Value = mediaElement.Position.TotalSeconds;
         }

         public void setupTimer()
         {

            sliderPositionUpdateDispatcher = new DispatcherTimer();
            sliderPositionUpdateDispatcher.Interval = new TimeSpan(0, 0, 0, 0, 300);
            startTimer();
         }

         public void startTimer()
         {

            sliderPositionUpdateDispatcher.Tick += updateSliderPositionoNTicks;
            sliderPositionUpdateDispatcher.Start();
         }

         // Slider start and end time must be updated in case of live content
         public async void setSliderStartTime(long startTime)
         {

            await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
                TimeSpan timespan = new TimeSpan(adaptiveSourceStatusUpdate.StartTime);
                double absvalue = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero);
                sliderProgress.Minimum = absvalue;
            });
         }

         // Slider start and end time must be updated in case of live content
         public async void setSliderEndTime(long startTime)
         {

            await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
                TimeSpan timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime);
                double absvalue = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero);
                sliderProgress.Maximum = absvalue;
            });
         }

         # endregion sliderMediaPlayer
   ```

   > [!NOTE]
   > CoreDispatcher é usado para fazer alterações no thread da interface do usuário do thread não pertencente à interface do usuário. No caso de afunilamento no thread do Dispatcher, o desenvolvedor pode optar por usar o Dispatcher fornecido pelo elemento da interface do usuário que pretende atualizar.  Por exemplo:

   ```csharp
         await sliderProgress.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () => { TimeSpan 

         timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime); 
         double absvalue  = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero); 

         sliderProgress.Maximum = absvalue; }); 
   ```
6. No final do método **mediaElement_AdaptiveSourceStatusUpdated** , adicione o seguinte código:

   ```csharp
         setSliderStartTime(args.StartTime);
         setSliderEndTime(args.EndTime);
   ```
7. No final do método **MediaOpened** , adicione o seguinte código:

   ```csharp
         sliderProgress.StepFrequency = SliderFrequency(mediaElement.NaturalDuration.TimeSpan);
         sliderProgress.Width = mediaElement.Width;
         setupTimer();
   ```
8. Pressione **Ctrl + S** para salvar o arquivo.

### <a name="to-compile-and-test-the-application"></a>Para compilar e testar o aplicativo

1. Pressione **F6** para compilar o projeto. 
2. Prima **F5** para executar a aplicação.
3. Na parte superior do aplicativo, você pode usar a URL de Smooth Streaming padrão ou inserir uma diferente. 
4. Clique em **definir origem**. 
5. Teste a barra deslizante.

Você concluiu a lição 2.  Nesta lição, você adicionou um controle deslizante ao aplicativo. 

## <a name="lesson-3-select-smooth-streaming-streams"></a>Lição 3: Selecionar fluxos de Smooth Streaming
Smooth Streaming é capaz de transmitir conteúdo com várias faixas de áudio de idioma que são selecionáveis pelos visualizadores.  Nesta lição, você permitirá que os visualizadores selecionem fluxos. Esta lição contém os seguintes procedimentos:

1. Modificar o arquivo XAML
2. Modificar o arquivo code-behind
3. Compilar e testar o aplicativo

### <a name="to-modify-the-xaml-file"></a>Para modificar o arquivo XAML

1. Em Gerenciador de Soluções, clique com o botão direito do mouse em **MainPage. XAML**e clique em **Designer de exibição**.
2. Localize &lt;Grid.&gt;datadefinitions e modifique as datadefinitions para que elas se pareçam com o seguinte:

   ```xml
         <Grid.RowDefinitions>            
            <RowDefinition Height="20"/>
            <RowDefinition Height="50"/>
            <RowDefinition Height="100"/>
            <RowDefinition Height="80"/>
            <RowDefinition Height="50"/>
         </Grid.RowDefinitions>
   ```
3. Dentro das &lt;marcas&gt;de grade&lt;/Grid&gt; , adicione o código a seguir para definir um controle ListBox, para que os usuários possam ver a lista de fluxos disponíveis e selecionar fluxos:

   ```xml
         <Grid Name="gridStreamAndBitrateSelection" Grid.Row="3">
            <Grid.RowDefinitions>
                <RowDefinition Height="300"/>
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="250*"/>
                <ColumnDefinition Width="250*"/>
            </Grid.ColumnDefinitions>
            <StackPanel Name="spStreamSelection" Grid.Row="1" Grid.Column="0">
                <StackPanel Orientation="Horizontal">
                    <TextBlock Name="tbAvailableStreams" Text="Available Streams:" FontSize="16" VerticalAlignment="Center"></TextBlock>
                    <Button Name="btnChangeStreams" Content="Submit" Click="btnChangeStream_Click"/>
                </StackPanel>
                <ListBox x:Name="lbAvailableStreams" Height="200" Width="200" Background="Transparent" HorizontalAlignment="Left" 
                    ScrollViewer.VerticalScrollMode="Enabled" ScrollViewer.VerticalScrollBarVisibility="Visible">
                    <ListBox.ItemTemplate>
                        <DataTemplate>
                            <CheckBox Content="{Binding Name}" IsChecked="{Binding isChecked, Mode=TwoWay}" />
                        </DataTemplate>
                    </ListBox.ItemTemplate>
                </ListBox>
            </StackPanel>
         </Grid>
   ```
4. Pressione **Ctrl + S** para salvar as alterações.

### <a name="to-modify-the-code-behind-file"></a>Para modificar o arquivo code-behind

1. Em Gerenciador de Soluções, clique com o botão direito do mouse em **MainPage. XAML**e clique em **Exibir código**.
2. Dentro do namespace SSPlayer, adicione uma nova classe:

   ```csharp
        #region class Stream
   
        public class Stream
        {
            private IManifestStream stream;
            public bool isCheckedValue;
            public string name;
   
            public string Name
            {
                get { return name; }
                set { name = value; }
            }
   
            public IManifestStream ManifestStream
            {
                get { return stream; }
                set { stream = value; }
            }
   
            public bool isChecked
            {
                get { return isCheckedValue; }
                set
                {
                    // mMke the video stream always checked.
                    if (stream.Type == MediaStreamType.Video)
                    {
                        isCheckedValue = true;
                    }
                    else
                    {
                        isCheckedValue = value;
                    }
                }
            }
   
            public Stream(IManifestStream streamIn)
            {
                stream = streamIn;
                name = stream.Name;
            }
        }
        #endregion class Stream
   ```
3. No início da classe MainPage, adicione as seguintes definições de variável:

   ```csharp
         private List<Stream> availableStreams;
         private List<Stream> availableAudioStreams;
         private List<Stream> availableTextStreams;
         private List<Stream> availableVideoStreams;
   ```
4. Dentro da classe MainPage, adicione a seguinte região:
   ```csharp
        #region stream selection
        ///<summary>
        ///Functionality to select streams from IManifestStream available streams
        /// </summary>
   
        // This function is called from the mediaElement_ManifestReady event handler 
        // to retrieve the streams and populate them to the local data members.
        public void getStreams(Manifest manifestObject)
        {
            availableStreams = new List<Stream>();
            availableVideoStreams = new List<Stream>();
            availableAudioStreams = new List<Stream>();
            availableTextStreams = new List<Stream>();
   
            try
            {
                for (int i = 0; i<manifestObject.AvailableStreams.Count; i++)
                {
                    Stream newStream = new Stream(manifestObject.AvailableStreams[i]);
                    newStream.isChecked = false;
   
                    //populate the stream lists based on the types
                    availableStreams.Add(newStream);
   
                    switch (newStream.ManifestStream.Type)
                    {
                        case MediaStreamType.Video:
                            availableVideoStreams.Add(newStream);
                            break;
                        case MediaStreamType.Audio:
                            availableAudioStreams.Add(newStream);
                            break;
                        case MediaStreamType.Text:
                            availableTextStreams.Add(newStream);
                            break;
                    }
   
                    // Select the default selected streams from the manifest.
                    for (int j = 0; j<manifestObject.SelectedStreams.Count; j++)
                    {
                        string selectedStreamName = manifestObject.SelectedStreams[j].Name;
                        if (selectedStreamName.Equals(newStream.Name))
                        {
                            newStream.isChecked = true;
                            break;
                        }
                    }
                }
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
   
        // This function set the list box ItemSource
        private async void refreshAvailableStreamsListBoxItemSource()
        {
            try
            {
                //update the stream check box list on the UI
                await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, ()
                    => { lbAvailableStreams.ItemsSource = availableStreams; });
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
   
        // This function creates a selected streams list
        private void createSelectedStreamsList(List<IManifestStream> selectedStreams)
        {
            bool isOneVideoSelected = false;
            bool isOneAudioSelected = false;
   
            // Only one video stream can be selected
            for (int j = 0; j<availableVideoStreams.Count; j++)
            {
                if (availableVideoStreams[j].isChecked && (!isOneVideoSelected))
                {
                    selectedStreams.Add(availableVideoStreams[j].ManifestStream);
                    isOneVideoSelected = true;
                }
            }
   
            // Select the first video stream from the list if no video stream is selected
            if (!isOneVideoSelected)
            {
                availableVideoStreams[0].isChecked = true;
                selectedStreams.Add(availableVideoStreams[0].ManifestStream);
            }
   
            // Only one audio stream can be selected
            for (int j = 0; j<availableAudioStreams.Count; j++)
            {
                if (availableAudioStreams[j].isChecked && (!isOneAudioSelected))
                {
                    selectedStreams.Add(availableAudioStreams[j].ManifestStream);
                    isOneAudioSelected = true;
                    txtStatus.Text = "The audio stream is changed to " + availableAudioStreams[j].ManifestStream.Name;
                }
            }
   
            // Select the first audio stream from the list if no audio steam is selected.
            if (!isOneAudioSelected)
            {
                availableAudioStreams[0].isChecked = true;
                selectedStreams.Add(availableAudioStreams[0].ManifestStream);
            }
   
            // Multiple text streams are supported.
            for (int j = 0; j < availableTextStreams.Count; j++)
            {
                if (availableTextStreams[j].isChecked)
                {
                    selectedStreams.Add(availableTextStreams[j].ManifestStream);
                }
            }
        }
   
        // Change streams on a smooth streaming presentation with multiple video streams.
        private async void changeStreams(List<IManifestStream> selectStreams)
        {
            try
            {
                IReadOnlyList<IStreamChangedResult> returnArgs =
                    await manifestObject.SelectStreamsAsync(selectStreams);
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
        #endregion stream selection
   ```
5. Localize o método mediaElement_ManifestReady, acrescente o seguinte código ao final da função:
   ```csharp
        getStreams(manifestObject);
        refreshAvailableStreamsListBoxItemSource();
   ```
    Assim, quando o manifesto do MediaElement estiver pronto, o código obterá uma lista dos fluxos disponíveis e preencherá a caixa de listagem da interface do usuário com a lista.
6. Dentro da classe MainPage, localize os botões da interface do usuário e clique em região de eventos e, em seguida, adicione a seguinte definição de função:
   ```csharp
        private void btnChangeStream_Click(object sender, RoutedEventArgs e)
        {
            List<IManifestStream> selectedStreams = new List<IManifestStream>();
   
            // Create a list of the selected streams
            createSelectedStreamsList(selectedStreams);
   
            // Change streams on the presentation
            changeStreams(selectedStreams);
        }
   ```

### <a name="to-compile-and-test-the-application"></a>Para compilar e testar o aplicativo

1. Pressione **F6** para compilar o projeto. 
2. Prima **F5** para executar a aplicação.
3. Na parte superior do aplicativo, você pode usar a URL de Smooth Streaming padrão ou inserir uma diferente. 
4. Clique em **definir origem**. 
5. O idioma padrão é audio_eng. Tente alternar entre audio_eng e audio_es. Toda vez que você selecionar um novo fluxo, deverá clicar no botão enviar.

Você concluiu a lição 3.  Nesta lição, você adicionará a funcionalidade para escolher fluxos.

## <a name="lesson-4-select-smooth-streaming-tracks"></a>Lição 4: Selecionar faixas de Smooth Streaming

Uma apresentação Smooth Streaming pode conter vários arquivos de vídeo codificados com diferentes níveis de qualidade (taxas de bits) e resoluções. Nesta lição, você permitirá que os usuários selecionem faixas. Esta lição contém os seguintes procedimentos:

1. Modificar o arquivo XAML
2. Modificar o arquivo code-behind
3. Compilar e testar o aplicativo

### <a name="to-modify-the-xaml-file"></a>Para modificar o arquivo XAML

1. Em Gerenciador de Soluções, clique com o botão direito do mouse em **MainPage. XAML**e clique em **Designer de exibição**.
2. Localize a &lt;marca&gt; de grade com o nome **gridStreamAndBitrateSelection**, acrescente o seguinte código ao final da marca:
   ```xml
         <StackPanel Name="spBitRateSelection" Grid.Row="1" Grid.Column="1">
         <StackPanel Orientation="Horizontal">
             <TextBlock Name="tbBitRate" Text="Available Bitrates:" FontSize="16" VerticalAlignment="Center"/>
             <Button Name="btnChangeTracks" Content="Submit" Click="btnChangeTrack_Click" />
         </StackPanel>
         <ListBox x:Name="lbAvailableVideoTracks" Height="200" Width="200" Background="Transparent" HorizontalAlignment="Left" 
                  ScrollViewer.VerticalScrollMode="Enabled" ScrollViewer.VerticalScrollBarVisibility="Visible">
             <ListBox.ItemTemplate>
                 <DataTemplate>
                     <CheckBox Content="{Binding Bitrate}" IsChecked="{Binding isChecked, Mode=TwoWay}"/>
                 </DataTemplate>
             </ListBox.ItemTemplate>
         </ListBox>
         </StackPanel>
   ```
3. Pressione **Ctrl + S** para salvar as alterações

### <a name="to-modify-the-code-behind-file"></a>Para modificar o arquivo code-behind

1. Em Gerenciador de Soluções, clique com o botão direito do mouse em **MainPage. XAML**e clique em **Exibir código**.
2. Dentro do namespace SSPlayer, adicione uma nova classe:
   ```csharp
        #region class Track
        public class Track
        {
            private IManifestTrack trackInfo;
            public string _bitrate;
            public bool isCheckedValue;
   
            public IManifestTrack TrackInfo
            {
                get { return trackInfo; }
                set { trackInfo = value; }
            }
   
            public string Bitrate
            {
                get { return _bitrate; }
                set { _bitrate = value; }
            }
   
            public bool isChecked
            {
                get { return isCheckedValue; }
                set
                {
                    isCheckedValue = value;
                }
            }
   
            public Track(IManifestTrack trackInfoIn)
            {
                trackInfo = trackInfoIn;
                _bitrate = trackInfoIn.Bitrate.ToString();
            }
            //public Track() { }
        }
        #endregion class Track
   ```
3. No início da classe MainPage, adicione as seguintes definições de variável:
   ```csharp
        private List<Track> availableTracks;
   ```
4. Dentro da classe MainPage, adicione a seguinte região:
   ```csharp
        #region track selection
        /// <summary>
        /// Functionality to select video streams
        /// </summary>
   
        /// This Function gets the tracks for the selected video stream
        public void getTracks(Manifest manifestObject)
        {
            availableTracks = new List<Track>();
   
            IManifestStream videoStream = getVideoStream();
            IReadOnlyList<IManifestTrack> availableTracksLocal = videoStream.AvailableTracks;
            IReadOnlyList<IManifestTrack> selectedTracksLocal = videoStream.SelectedTracks;
   
            try
            {
                for (int i = 0; i < availableTracksLocal.Count; i++)
                {
                    Track thisTrack = new Track(availableTracksLocal[i]);
                    thisTrack.isChecked = true;
   
                    for (int j = 0; j < selectedTracksLocal.Count; j++)
                    {
                        string selectedTrackName = selectedTracksLocal[j].Bitrate.ToString();
                        if (selectedTrackName.Equals(thisTrack.Bitrate))
                        {
                            thisTrack.isChecked = true;
                            break;
                        }
                    }
                    availableTracks.Add(thisTrack);
                }
            }
            catch (Exception e)
            {
                txtStatus.Text = e.Message;
            }
        }
   
        // This function gets the video stream that is playing
        private IManifestStream getVideoStream()
        {
            IManifestStream videoStream = null;
            for (int i = 0; i < manifestObject.SelectedStreams.Count; i++)
            {
                if (manifestObject.SelectedStreams[i].Type == MediaStreamType.Video)
                {
                    videoStream = manifestObject.SelectedStreams[i];
                    break;
                }
            }
            return videoStream;
        }
   
        // This function set the UI list box control ItemSource
        private async void refreshAvailableTracksListBoxItemSource()
        {
            try
            {
                // Update the track check box list on the UI 
                await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, ()
                    => { lbAvailableVideoTracks.ItemsSource = availableTracks; });
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }        
        }
   
        // This function creates a list of the selected tracks.
        private void createSelectedTracksList(List<IManifestTrack> selectedTracks)
        {
            // Create a list of selected tracks
            for (int j = 0; j < availableTracks.Count; j++)
            {
                if (availableTracks[j].isCheckedValue == true)
                {
                    selectedTracks.Add(availableTracks[j].TrackInfo);
                }
            }
        }
   
        // This function selects the tracks based on user selection 
        private void changeTracks(List<IManifestTrack> selectedTracks)
        {
            IManifestStream videoStream = getVideoStream();
            try
            {
                videoStream.SelectTracks(selectedTracks);
            }
            catch (Exception ex)
            {
                txtStatus.Text = ex.Message;
            }
        }
        #endregion track selection
   ```
5. Localize o método mediaElement_ManifestReady, acrescente o seguinte código ao final da função:
   ```csharp
         getTracks(manifestObject);
         refreshAvailableTracksListBoxItemSource();
   ```
6. Dentro da classe MainPage, localize os botões da interface do usuário e clique em região de eventos e, em seguida, adicione a seguinte definição de função:
   ```csharp
         private void btnChangeStream_Click(object sender, RoutedEventArgs e)
         {
            List<IManifestStream> selectedStreams = new List<IManifestStream>();

            // Create a list of the selected streams
            createSelectedStreamsList(selectedStreams);

            // Change streams on the presentation
            changeStreams(selectedStreams);
         }
   ```
   
### <a name="to-compile-and-test-the-application"></a>Para compilar e testar o aplicativo

1. Pressione **F6** para compilar o projeto. 
2. Prima **F5** para executar a aplicação.
3. Na parte superior do aplicativo, você pode usar a URL de Smooth Streaming padrão ou inserir uma diferente. 
4. Clique em **definir origem**. 
5. Por padrão, todas as faixas do fluxo de vídeo são selecionadas. Para testar as alterações na taxa de bits, você pode selecionar a taxa de bits mais baixa disponível e, em seguida, selecionar a taxa de bits mais alta disponível. Você deve clicar em enviar após cada alteração.  Você pode ver as alterações na qualidade do vídeo.

Você concluiu a lição 4.  Nesta lição, você adicionará a funcionalidade para escolher faixas.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="other-resources"></a>Outros recursos:
* [Como criar um Smooth Streaming aplicativo JavaScript do Windows 8 com recursos avançados](https://blogs.iis.net/cenkd/archive/2012/08/10/how-to-build-a-smooth-streaming-windows-8-javascript-application-with-advanced-features.aspx)
* [Visão geral técnica de Smooth Streaming](https://www.iis.net/learn/media/on-demand-smooth-streaming/smooth-streaming-technical-overview)

[PlayerApplication]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-1.png
[CodeViewPic]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-2.png

