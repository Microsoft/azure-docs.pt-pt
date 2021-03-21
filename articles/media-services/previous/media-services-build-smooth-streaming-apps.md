---
title: Tutorial de aplicativos windows store de streaming liso | Microsoft Docs
description: Saiba como utilizar o Azure Media Services para criar uma aplicação C# Windows Store com um controlo XML MediaElement para reproduzir conteúdo Smooth Stream.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 0fa5d8c5-3d5f-4886-ae55-fb6de4f5256d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 20c7068fa0cb56699fdbc6d75b279abadd583832
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103017125"
---
# <a name="how-to-build-a-smooth-streaming-windows-store-application"></a>Como construir uma aplicação de loja de Windows de streaming suave

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]  

O SDK do Cliente de Streaming Suave para o Windows 8 permite aos desenvolvedores construir aplicações windows Store que podem jogar a pedido e viver conteúdo smooth streaming. Além da reprodução básica de conteúdos de Streaming Suave, o SDK também fornece funcionalidades ricas como a proteção Microsoft PlayReady, restrição de nível de qualidade, DVR ao vivo, comutação de streaming de áudio, audição de atualizações de estado (como alterações de nível de qualidade) e eventos de erro, e assim por diante. Para obter mais informações sobre as funcionalidades suportadas, consulte as [notas de lançamento](https://www.iis.net/learn/media/smooth-streaming/smooth-streaming-client-sdk-for-windows-8-release-notes). Para obter mais informações, consulte [o Quadro do Jogador para o Windows 8](https://playerframework.codeplex.com/). 

Este tutorial contém quatro lições:

1. Criar uma aplicação básica de loja de streaming suave
2. Adicione uma barra de slider para controlar o progresso dos meios de comunicação
3. Selecione streams de streaming suaves
4. Selecione Faixas de streaming suaves

## <a name="prerequisites"></a>Pré-requisitos
> [!NOTE]
> Os projetos da Windows Store, versão 8.1 e anteriores, não são suportados no Visual Studio 2017.  Para obter mais informações, veja [Visual Studio 2017 Platform Targeting and Compatibility](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs) (Segmentação e Compatibilidade de Plataformas do Visual Studio 2017)

* Windows 8 32-bit ou 64-bit.
* Versões Visual Studio 2012 a 2015.
* [Microsoft Smooth Streaming Client SDK para o Windows 8](https://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Home).

A solução completa para cada lição pode ser descarregada a partir de amostras de código de desenvolvimento da MSDN (Code Gallery): 

* [Lição 1](https://code.msdn.microsoft.com/Smooth-Streaming-Client-0bb1471f) - Um simples leitor de multimédia de streaming suave Windows 8, 
* [Lição 2](https://code.msdn.microsoft.com/A-simple-Windows-8-Smooth-ee98f63a) - Um simples leitor de multimédia de streaming suave Windows 8 com um controlo de barra de slider, 
* [Lição 3](https://code.msdn.microsoft.com/A-Windows-8-Smooth-883c3b44) - Um leitor de multimédia de streaming suave do Windows 8 com seleção de stream,  
* [Lição 4](https://code.msdn.microsoft.com/A-Windows-8-Smooth-aa9e4907)  - Um leitor de multimédia de streaming suave do Windows 8 com seleção de faixas.

## <a name="lesson-1-create-a-basic-smooth-streaming-store-application"></a>Lição 1: Criar uma aplicação básica de loja de streaming suave

Nesta lição, irá criar uma aplicação Windows Store com um controlo MediaElement para reproduzir conteúdo Smooth Stream.  A aplicação de execução parece:

![Exemplo de aplicação do Windows Store de streaming liso][PlayerApplication]

Para obter mais informações sobre o desenvolvimento da aplicação Windows Store, consulte [Desenvolver Grandes Aplicações para o Windows 8](https://developer.microsoft.com/en-us/windows/). Esta lição contém os seguintes procedimentos:

1. Criar um projeto windows store
2. Desenhe a interface do utilizador (XAML)
3. Modificar o código por trás do ficheiro
4. Compilar e testar a aplicação

### <a name="to-create-a-windows-store-project"></a>Criar um projeto windows Store

1. Executar Estúdio Visual; as versões 2012 a 2015 são suportadas.
1. A partir do menu **FILE,** clique em **Novo** e, em seguida, clique em **Projeto**.
1. A partir do diálogo do Novo Projeto, escreva ou selecione os seguintes valores:

    | Name | Valor |
    | --- | --- |
    | Grupo de modelo |Instalado/Modelos/Visual C#/Windows Store |
    | Modelo |App em branco (XAML) |
    | Name |SSPlayer |
    | Localização |C:\SSTutorials |
    | Nome da Solução |SSPlayer |
    | Criar diretório para solução |(selecionado) |

1. Clique em **OK**.

### <a name="to-add-a-reference-to-the-smooth-streaming-client-sdk"></a>Para adicionar uma referência ao Cliente de Streaming Suave SDK

1. A partir do Solution Explorer, clique à direita **no SSPlayer** e, em seguida, clique em **Adicionar Referência**.
1. Escreva ou selecione os seguintes valores:

    | Name | Valor |
    | --- | --- |
    | Grupo de referência |Janelas/Extensões |
    | Referência |Selecione Microsoft Smooth Streaming Client SDK para Windows 8 e Microsoft Visual C++ Pacote de tempo de execução |

1. Clique em **OK**. 

Depois de adicionar as referências, deve selecionar a plataforma específica (x64 ou x86), adicionando referências não funcionarão para qualquer configuração da plataforma CPU.  No explorador de soluções, verá a marca de aviso amarelo para estas referências adicionadas.

### <a name="to-design-the-player-user-interface"></a>Para desenhar a interface de utilizador do jogador

1. A partir do Solution Explorer, clique duas **vezes no MainPage.xaml** para o abrir na vista de design.
2. Localize a **&lt; grelha &gt;** e **&lt; /grelha &gt;** marca o ficheiro XAML e cole o seguinte código entre as duas tags:

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
   O controlo MediaElement é utilizado para reproduzir meios de comunicação. O controlo de slider chamado sliderProgress será usado na próxima lição para controlar o progresso dos meios de comunicação.
3. Prima **CTRL+S** para guardar o ficheiro.

O controlo MediaElement não suporta o conteúdo de Streaming Suave fora da caixa. Para ativar o suporte de streaming suave, tem de registar o manipulador de fluxo de fluxo de fluxo suave por extensão de nome de ficheiro e tipo MIME.  Para se registar, utilize o método MediaExtensionManager.RegisterByteStreamHandler do windows.Media namespace.

Neste ficheiro XAML, alguns manipuladores de eventos estão associados aos controlos.  Tens de definir os manipuladores de eventos.

### <a name="to-modify-the-code-behind-file"></a>Para modificar o código por trás do ficheiro

1. A partir do Solution Explorer, clique à direita **no MainPage.xaml** e, em seguida, clique em **Ver Código**.
2. Na parte superior do ficheiro, adicione a seguinte declaração utilizando:

    ```csharp
        using Windows.Media;
    ```

3. No início da aula **mainPage,** adicione o seguinte membro de dados:

    ```csharp
        private MediaExtensionManager extensions = new MediaExtensionManager();
    ```

4. No final do construtor **MainPage,** adicione as seguintes duas linhas:

    ```csharp
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "text/xml");
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "application/vnd.ms-sstr+xml");
    ```

5. No final da aula **mainPage,** cole o seguinte código:
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
   O manipulador de eventos sliderProgress_PointerPressed é definido aqui.  Há mais trabalhos a fazer para que funcione, que será abordado na próxima lição deste tutorial.
6. Prima **CTRL+S** para guardar o ficheiro.

O código por trás do ficheiro terminado deve ser assim:

![Codeview em Estúdio Visual da aplicação Smooth Streaming Windows Store][CodeViewPic]

### <a name="to-compile-and-test-the-application"></a>Para compilar e testar a aplicação

1. A partir do menu **BUILD,** clique em **'Gestor de Configuração'.**
2. Alterar **plataforma de solução Ative** para combinar com a sua plataforma de desenvolvimento.
3. Pressione **F6** para compilar o projeto. 
4. Prima **F5** para executar a aplicação.
5. Na parte superior da aplicação, pode utilizar o URL de streaming liso padrão ou introduzir um outro. 
6. Clique **em Origem Definida**. Como **a Reprodução Automática** é ativada por padrão, os meios de comunicação devem ser reproduzidos automaticamente.  Pode controlar os meios de comunicação utilizando os botões **Reproduzir,** **pausar** e **parar.**  Pode controlar o volume dos meios de comunicação utilizando o deslizador vertical.  No entanto, o deslize horizontal para controlar o progresso dos meios de comunicação social ainda não está totalmente implementado. 

Completou a lição1.  Nesta lição, você usa um controlo MediaElement para reproduzir conteúdo smooth streaming.  Na próxima lição, irá adicionar um slider para controlar o progresso do conteúdo de Streaming Suave.

## <a name="lesson-2-add-a-slider-bar-to-control-the-media-progress"></a>Lição 2: Adicione uma barra de slider para controlar o progresso dos meios de comunicação

Na lição 1, criou uma aplicação Windows Store com um controlo MediaElement XAML para reproduzir conteúdo sonoro de streaming suave.  Vem algumas funções básicas dos meios de comunicação como começar, parar e parar.  Nesta lição, irá adicionar um controlo de barra de slider à aplicação.

Neste tutorial, usaremos um temporizador para atualizar a posição de slider com base na posição atual do controlo MediaElement.  O início e o fim do slider também precisam de ser atualizados em caso de conteúdo ao vivo.  Isto pode ser melhor manuseado no evento de atualização de fonte adaptativa.

As fontes de mídia são objetos que geram dados de mídia.  A fonte resolve um URL ou byte stream e cria a fonte de mídia apropriada para esse conteúdo.  A origem é a forma padrão para as aplicações criarem fontes de media. 

Esta lição contém os seguintes procedimentos:

1. Registar o manipulador de streaming suave 
2. Adicione os manipuladores de eventos de nível de gestor de fonte adaptativa
3. Adicione os manipuladores de eventos de nível de fonte adaptativo
4. Adicionar manipuladores de eventos MediaElement
5. Adicionar código relacionado com barra de slider
6. Compilar e testar a aplicação

### <a name="to-register-the-smooth-streaming-byte-stream-handler-and-pass-the-propertyset"></a>Para registar o manipulador de fluxo de streaming smooth e passar o conjunto de propriedades

1. A partir do Solution Explorer, clique no **mainPage.xaml** e, em seguida, clique em **Ver Código**.
2. No início do processo, adicione a seguinte declaração utilizando:

   ```csharp
        using Microsoft.Media.AdaptiveStreaming;
   ```
3. No início da aula mainPage, adicione os seguintes membros de dados:

   ```csharp
         private Windows.Foundation.Collections.PropertySet propertySet = new Windows.Foundation.Collections.PropertySet();             
         private IAdaptiveSourceManager adaptiveSourceManager;
   ```
4. Dentro do construtor **MainPage,** adicione o seguinte código após o **this.Initridimensionar componentes();** linha e as linhas de código de registo escritas na lição anterior:

   ```csharp
        // Gets the default instance of AdaptiveSourceManager which manages Smooth 
        //Streaming media sources.
        adaptiveSourceManager = AdaptiveSourceManager.GetDefault();
        // Sets property key value to AdaptiveSourceManager default instance.
        // {A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}" must be hardcoded.
        propertySet["{A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}"] = adaptiveSourceManager;
   ```
5. Dentro do construtor **MainPage,** modifique os dois métodos RegisterByteStreamHandler para adicionar os parâmetros de forth:

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
6. Prima **CTRL+S** para guardar o ficheiro.

### <a name="to-add-the-adaptive-source-manager-level-event-handler"></a>Para adicionar o manipulador de eventos de nível de gestor de fonte adaptativa

1. A partir do Solution Explorer, clique no **mainPage.xaml** e, em seguida, clique em **Ver Código**.
2. Dentro da classe **MainPage,** adicione o seguinte membro de dados:

   ```csharp
     private AdaptiveSource adaptiveSource = null;
   ```
3. No final da aula **mainPage,** adicione o seguinte manipulador de eventos:

   ```csharp
         # region Adaptive Source Manager Level Events
         private void mediaElement_AdaptiveSourceOpened(AdaptiveSource sender, AdaptiveSourceOpenedEventArgs args)
         {

            adaptiveSource = args.AdaptiveSource;
         }

         # endregion Adaptive Source Manager Level Events
   ```
4. No final do construtor **MainPage,** adicione a seguinte linha para subscrever o evento aberto de fonte adaptativa:

   ```csharp
         adaptiveSourceManager.AdaptiveSourceOpenedEvent += 
           new AdaptiveSourceOpenedEventHandler(mediaElement_AdaptiveSourceOpened);
   ```
5. Prima **CTRL+S** para guardar o ficheiro.

### <a name="to-add-adaptive-source-level-event-handlers"></a>Para adicionar manipuladores de eventos de nível de fonte adaptativo

1. A partir do Solution Explorer, clique no **mainPage.xaml** e, em seguida, clique em **Ver Código**.
2. Dentro da classe **MainPage,** adicione o seguinte membro de dados:

   ```csharp
     private AdaptiveSourceStatusUpdatedEventArgs adaptiveSourceStatusUpdate; 
     private Manifest manifestObject;
   ```
3. No final da aula **mainPage,** adicione os seguintes manipuladores de eventos:

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
4. No final do método **mediaElement AdaptiveSourceO,** adicione o seguinte código para subscrever os eventos:

   ```csharp
         adaptiveSource.ManifestReadyEvent +=

                    mediaElement_ManifestReady;
         adaptiveSource.AdaptiveSourceStatusUpdatedEvent += 

            mediaElement_AdaptiveSourceStatusUpdated;
         adaptiveSource.AdaptiveSourceFailedEvent += 

            mediaElement_AdaptiveSourceFailed;
   ```
5. Prima **CTRL+S** para guardar o ficheiro.

Os mesmos eventos também estão disponíveis no nível de manjedoura Adaptive Source, que pode ser usado para lidar com funcionalidades comuns a todos os elementos da aplicação. Cada AdaptiveSource inclui os seus próprios eventos e todos os eventos AdaptiveSource serão em cascata sob o AdaptiveSourceManager.

### <a name="to-add-media-element-event-handlers"></a>Para adicionar manipuladores de eventos Media Element

1. A partir do Solution Explorer, clique no **mainPage.xaml** e, em seguida, clique em **Ver Código**.
2. No final da aula **mainPage,** adicione os seguintes manipuladores de eventos:

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
3. No final do construtor **MainPage,** adicione o seguinte código para subscrito aos eventos:

   ```csharp
         mediaElement.MediaOpened += MediaOpened;
         mediaElement.MediaEnded += MediaEnded;
         mediaElement.MediaFailed += MediaFailed;
   ```
4. Prima **CTRL+S** para guardar o ficheiro.

### <a name="to-add-slider-bar-related-code"></a>Para adicionar código relacionado com a barra de slider

1. A partir do Solution Explorer, clique no **mainPage.xaml** e, em seguida, clique em **Ver Código**.
2. No início do processo, adicione a seguinte declaração utilizando:

   ```csharp
        using Windows.UI.Core;
   ```
3. Dentro da classe **MainPage,** adicione os seguintes membros de dados:

   ```csharp
         public static CoreDispatcher _dispatcher;
         private DispatcherTimer sliderPositionUpdateDispatcher;
   ```
4. No final do construtor **MainPage,** adicione o seguinte código:

   ```csharp
         _dispatcher = Window.Current.Dispatcher;
         PointerEventHandler pointerpressedhandler = new PointerEventHandler(sliderProgress_PointerPressed);
         sliderProgress.AddHandler(Control.PointerPressedEvent, pointerpressedhandler, true);    
   ```
5. No final da aula **mainPage,** adicione o seguinte código:

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
   > O CoreDispatcher é utilizado para fazer alterações na linha UI a partir de non UI Thread. Em caso de estrangulamento na linha do despachante, o desenvolvedor pode optar por utilizar o despachante fornecido pelo elemento UI que pretende atualizar.  Por exemplo:

   ```csharp
         await sliderProgress.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () => { TimeSpan 

         timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime); 
         double absvalue  = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero); 

         sliderProgress.Maximum = absvalue; }); 
   ```
6. No final do método **mediaElement_AdaptiveSourceStatusUpdated,** adicione o seguinte código:

   ```csharp
         setSliderStartTime(args.StartTime);
         setSliderEndTime(args.EndTime);
   ```
7. No final do método **mediaopenado,** adicione o seguinte código:

   ```csharp
         sliderProgress.StepFrequency = SliderFrequency(mediaElement.NaturalDuration.TimeSpan);
         sliderProgress.Width = mediaElement.Width;
         setupTimer();
   ```
8. Prima **CTRL+S** para guardar o ficheiro.

### <a name="to-compile-and-test-the-application"></a>Para compilar e testar a aplicação

1. Pressione **F6** para compilar o projeto. 
2. Prima **F5** para executar a aplicação.
3. Na parte superior da aplicação, pode utilizar o URL de streaming liso padrão ou introduzir um outro. 
4. Clique **em Origem Definida**. 
5. Teste a barra de slider.

Terminou a lição 2.  Nesta lição adicionou um slider à aplicação. 

## <a name="lesson-3-select-smooth-streaming-streams"></a>Lição 3: Selecione Streams de streaming suaves
O Smooth Streaming é capaz de transmitir conteúdo com múltiplas faixas de áudio linguística que são selecionáveis pelos espectadores.  Nesta lição, irá permitir aos espectadores selecionarem streams. Esta lição contém os seguintes procedimentos:

1. Modificar o ficheiro XAML
2. Modificar o código por trás do ficheiro
3. Compilar e testar a aplicação

### <a name="to-modify-the-xaml-file"></a>Para modificar o ficheiro XAML

1. A partir do Solution Explorer, clique à direita **no MainPage.xaml** e, em seguida, clique em **Ver Design**.
2. Localizar &lt; Grid.RowDefinitions &gt; e modificar as LinhaDefinitions para que pareçam:

   ```xml
         <Grid.RowDefinitions>            
            <RowDefinition Height="20"/>
            <RowDefinition Height="50"/>
            <RowDefinition Height="100"/>
            <RowDefinition Height="80"/>
            <RowDefinition Height="50"/>
         </Grid.RowDefinitions>
   ```
3. Dentro das &lt; tags Grid &gt; &lt; &gt; /Grid, adicione o seguinte código para definir um controlo de listbox, para que os utilizadores possam ver a lista de streams disponíveis e selecionar streams:

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
4. Prima **CTRL+S** para guardar as alterações.

### <a name="to-modify-the-code-behind-file"></a>Para modificar o código por trás do ficheiro

1. A partir do Solution Explorer, clique à direita **no MainPage.xaml** e, em seguida, clique em **Ver Código**.
2. Dentro do espaço de nomes SSPlayer, adicione uma nova classe:

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
3. No início da aula mainPage, adicione as seguintes definições variáveis:

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
5. Localizar o método mediaElement_ManifestReady, anexar o seguinte código no final da função:
   ```csharp
        getStreams(manifestObject);
        refreshAvailableStreamsListBoxItemSource();
   ```
    Assim, quando o manifesto do MediaElement estiver pronto, o código recebe uma lista dos streams disponíveis e povoa a caixa de lista de UI com a lista.
6. Dentro da classe MainPage, localize os botões de UI clique na região de eventos e, em seguida, adicione a seguinte definição de função:
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

### <a name="to-compile-and-test-the-application"></a>Para compilar e testar a aplicação

1. Pressione **F6** para compilar o projeto. 
2. Prima **F5** para executar a aplicação.
3. Na parte superior da aplicação, pode utilizar o URL de streaming liso padrão ou introduzir um outro. 
4. Clique **em Origem Definida**. 
5. A linguagem padrão é audio_eng. Tente mudar entre audio_eng e audio_es. Sempre que selecionar um novo fluxo, tem de clicar no botão Enviar.

Completou a lição 3.  Nesta lição, adiciona-se a funcionalidade para escolher streams.

## <a name="lesson-4-select-smooth-streaming-tracks"></a>Lição 4: Selecione faixas de streaming suaves

Uma apresentação de Streaming Suave pode conter vários ficheiros de vídeo codificados com diferentes níveis de qualidade (taxas de bit) e resoluções. Nesta lição, irá permitir que os utilizadores selecionem faixas. Esta lição contém os seguintes procedimentos:

1. Modificar o ficheiro XAML
2. Modificar o código por trás do ficheiro
3. Compilar e testar a aplicação

### <a name="to-modify-the-xaml-file"></a>Para modificar o ficheiro XAML

1. A partir do Solution Explorer, clique à direita **no MainPage.xaml** e, em seguida, clique em **Ver Design**.
2. Localizar a &lt; etiqueta Grid com o nome &gt; **gridStreamAndBitrateSelection,** apelece o seguinte código na extremidade da etiqueta:
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
3. Pressione **CTRL+S** para salvar as alterações

### <a name="to-modify-the-code-behind-file"></a>Para modificar o código por trás do ficheiro

1. A partir do Solution Explorer, clique à direita **no MainPage.xaml** e, em seguida, clique em **Ver Código**.
2. Dentro do espaço de nomes SSPlayer, adicione uma nova classe:
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
3. No início da aula mainPage, adicione as seguintes definições variáveis:
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
5. Localizar o método mediaElement_ManifestReady, anexar o seguinte código no final da função:
   ```csharp
         getTracks(manifestObject);
         refreshAvailableTracksListBoxItemSource();
   ```
6. Dentro da classe MainPage, localize os botões de UI clique na região de eventos e, em seguida, adicione a seguinte definição de função:
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
   
### <a name="to-compile-and-test-the-application"></a>Para compilar e testar a aplicação

1. Pressione **F6** para compilar o projeto. 
2. Prima **F5** para executar a aplicação.
3. Na parte superior da aplicação, pode utilizar o URL de streaming liso padrão ou introduzir um outro. 
4. Clique **em Origem Definida**. 
5. Por predefinição, todas as faixas do fluxo de vídeo são selecionadas. Para experimentar as alterações da taxa de bits, pode selecionar a taxa de bits mais baixa disponível e, em seguida, selecionar a taxa de bit mais alta disponível. Tem de clicar em Enviar após cada alteração.  Pode ver as mudanças de qualidade de vídeo.

Completou a lição 4.  Nesta lição, adiciona-se a funcionalidade para escolher faixas.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="other-resources"></a>Outros Recursos:
* [Como construir uma aplicação JavaScript Do Windows 8 de streaming suave com funcionalidades avançadas](https://blogs.iis.net/cenkd/archive/2012/08/10/how-to-build-a-smooth-streaming-windows-8-javascript-application-with-advanced-features.aspx)
* [Visão geral técnica de streaming suave](https://www.iis.net/learn/media/on-demand-smooth-streaming/smooth-streaming-technical-overview)

[PlayerApplication]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-1.png
[CodeViewPic]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-2.png
