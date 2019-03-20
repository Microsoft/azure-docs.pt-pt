---
title: Tutorial de aplicação da Windows Store de transmissão em fluxo uniforme | Documentos da Microsoft
description: Saiba como utilizar os serviços de multimédia do Azure para criar um C# aplicação da Windows Store com um controle MediaElement do XML de reproduzir conteúdo de Stream uniforme.
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
ms.openlocfilehash: 92fc63ed45b6f1ea377138744d19137645c916e7
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57882731"
---
# <a name="how-to-build-a-smooth-streaming-windows-store-application"></a>Como criar um Smooth Streaming de aplicativos da Windows Store  

O Smooth Streaming cliente SDK para Windows 8 permite aos desenvolvedores criar aplicativos da Windows Store que podem reproduzir o conteúdo a pedido e live Smooth Streaming. Além da reprodução básica do conteúdo de transmissão em fluxo uniforme, o SDK também fornece recursos avançados, como o Microsoft PlayReady proteção, restrição de nível de qualidade, Live DVR, transmissão de áudio mudar, à escuta de atualizações de estado (como alterações no nível de qualidade) e eventos de erro e assim por diante. Para obter mais informações dos recursos suportados, consulte a [notas de versão](https://www.iis.net/learn/media/smooth-streaming/smooth-streaming-client-sdk-for-windows-8-release-notes). Para obter mais informações, consulte [Player Framework para Windows 8](https://playerframework.codeplex.com/). 

Este tutorial contém quatro lições:

1. Criar uma aplicação básica de Smooth Streaming Store
2. Adicionar uma barra de controlo de deslize para controlar o progresso de suporte de dados
3. Selecione fluxos de transmissão em fluxo uniforme
4. Selecione as faixas de transmissão em fluxo uniforme

## <a name="prerequisites"></a>Pré-requisitos
> [!NOTE]
> Versão de projetos de Store do Windows 8.1 e anterior não são suportadas no Visual Studio 2017.  Para obter mais informações, veja [Visual Studio 2017 Platform Targeting and Compatibility](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs) (Segmentação e Compatibilidade de Plataformas do Visual Studio 2017)

* Windows 8 de 32 bits ou 64 bits.
* Versões do Visual Studio 2012 até 2015.
* [Smooth Streaming SDK de cliente para o Windows 8 da Microsoft](https://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Home https://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Home).

A solução concluída para cada lição pode ser transferida a partir de exemplos de código do MSDN Developer (galeria de código): 

* [Lição 1](https://code.msdn.microsoft.com/Smooth-Streaming-Client-0bb1471f) – um uniforme de simples do Windows 8, o leitor de multimédia de transmissão em fluxo 
* [Lição 2](https://code.msdn.microsoft.com/A-simple-Windows-8-Smooth-ee98f63a) – um uniforme de 8 simples do Windows Media Player com uma barra de controlo de deslize de transmissão em fluxo controle, 
* [Lição 3](https://code.msdn.microsoft.com/A-Windows-8-Smooth-883c3b44) – um uniforme do Windows 8 com a seleção do Stream, o leitor de multimédia de transmissão em fluxo  
* [Lição 4](https://code.msdn.microsoft.com/A-Windows-8-Smooth-aa9e4907) – um uniforme do Windows 8 com a seleção de controlar o leitor de multimédia de transmissão em fluxo.

## <a name="lesson-1-create-a-basic-smooth-streaming-store-application"></a>Lição 1: Criar uma aplicação básica de Smooth Streaming Store

Nesta lição, irá criar um aplicativo da Windows Store com um controle MediaElement para reproduzir Stream Smooth conteúdo.  A aplicação em execução é semelhante a:

![Exemplo de aplicação de Smooth Streaming Windows Store][PlayerApplication]

Para obter mais informações sobre como desenvolver aplicativos da Windows Store, consulte [desenvolver Ótimos aplicativos para Windows 8](https://msdn.microsoft.com/windows/apps/br229512.aspx). Nesta lição contém os seguintes procedimentos:

1. Criar um projeto da Windows Store
2. Design da interface do usuário (XAML)
3. Modificar o código por trás do ficheiro
4. Compilar e testar a aplicação

**Para criar um projeto da Windows Store**

1. Executar o Visual Studio; são suportadas versões 2012 até 2015.
2. No menu **FICHEIRO**, clique em **Novo** e, em seguida, clique em **Projeto**.
3. Na caixa de diálogo novo projeto, escreva ou selecione os seguintes valores:

| Name | Value |
| --- | --- |
| Grupo de modelo |Instalado/modelos/Visual C#Windows Store |
| Modelo |Aplicação em branco (XAML) |
| Name |SSPlayer |
| Localização |C:\SSTutorials |
| Nome da solução |SSPlayer |
| Criar diretório para a solução |(selecionado) |

1. Clique em **OK**.

**Adicionar uma referência para o SDK do cliente de transmissão em fluxo uniforme**

1. No Explorador de soluções, clique com botão direito **SSPlayer**e, em seguida, clique em **Add Reference**.
2. Escreva ou selecione os seguintes valores:

| Name | Value |
| --- | --- |
| Grupo de referência |Windows/extensões |
| Referência |Selecione Smooth Streaming SDK de cliente para o Windows 8 e o pacote do Microsoft Visual C++ Runtime da Microsoft |

1. Clique em **OK**. 

Depois de adicionar as referências, tem de selecionar a plataforma de destino (x64 ou x86), a adição de referências não funcionará para configuração de plataforma de qualquer CPU.  No solution explorer, verá adicionado de marca de aviso amarelo para estas referências.

**Para o design da interface do usuário player**

1. No Explorador de soluções, faça duplo clique em **mainpage. XAML** abri-la no modo de exibição design.
2. Localize a **&lt;grelha&gt;** e **&lt;/Grid&gt;** com as etiquetas, o arquivo XAML e cole o seguinte código entre as duas etiquetas:
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
   O controle MediaElement é utilizado para reproduzir multimédia. O controlo de deslize com o nome sliderProgress será utilizado na próxima lição para controlar o progresso de suporte de dados.
3. Prima **CTRL + S** para guardar o ficheiro.

O controle MediaElement não suporta a transmissão em fluxo uniforme conteúdo out-of-box. Para ativar o suporte de transmissão em fluxo uniforme, deve registrar o manipulador de fluxo de bytes de transmissão em fluxo uniforme por extensão de nome de ficheiro e o tipo MIME.  Para registar, utilize o método MediaExtensionManager.RegisterByteStreamHandler do namespace do botão.

Nesse arquivo XAML, alguns manipuladores de eventos estão associados com os controles.  Deve definir esses manipuladores de eventos.

**Para modificar o código por trás do ficheiro**

1. No Explorador de soluções, clique com botão direito **mainpage. XAML**e, em seguida, clique em **View Code**.
2. Na parte superior do ficheiro, adicione o seguinte usando a instrução:
   
        using Windows.Media;
3. No início da **MainPage** de classe, adicione o seguinte membro de dados:
   
         private MediaExtensionManager extensions = new MediaExtensionManager();
4. No final da **MainPage** construtor, adicione as duas linhas seguintes:
   
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "text/xml");
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "application/vnd.ms-sstr+xml");
5. No final da **MainPage** de classe, cole o seguinte código:
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
   O manipulador de eventos de sliderProgress_PointerPressed é definido aqui.  Existem mais funciona para o fazer para que isso funcione, que será abordada na próxima deste tutorial.
6. Prima **CTRL + S** para guardar o ficheiro.

A concluir que o código por trás do ficheiro deve ter o seguinte aspeto:

![Codeview no aplicativo do Visual Studio de Smooth Streaming Windows Store][CodeViewPic]

**Para compilar e testar a aplicação**

1. Do **CRIE** menu, clique em **Configuration Manager**.
2. Alteração **plataforma de solução Active Directory** para corresponder à sua plataforma de desenvolvimento.
3. Prima **F6** para compilar o projeto. 
4. Prima **F5** para executar a aplicação.
5. Na parte superior da aplicação, pode utilizar o URL de transmissão em fluxo uniforme predefinido ou introduzir um diferente. 
6. Clique em **definir a origem**. Uma vez **reprodução automática** está ativada por predefinição, o suporte de dados deverá reproduzir automaticamente.  Pode controlar o suporte de dados através da **reproduzir**, **colocar em pausa** e **parar** botões.  Pode controlar o volume de suporte de dados com o controlo de deslize vertical.  No entanto o controlo de deslize horizontal para controlar o progresso de suporte de dados ainda não está completamente implementado. 

Concluiu lesson1.  Nesta lição, usar um controle MediaElement para reproduzir conteúdo de transmissão em fluxo uniforme.  Na próxima lição, irá adicionar um controlo de deslize para controlar o progresso do conteúdo de transmissão em fluxo uniforme.

## <a name="lesson-2-add-a-slider-bar-to-control-the-media-progress"></a>Lição 2: Adicionar uma barra de controlo de deslize para controlar o progresso de suporte de dados

Lição 1, criou um aplicativo da Windows Store com um controle XAML MediaElement para reproduzir conteúdo de multimédia de transmissão em fluxo uniforme.  Trata de algumas funções de suporte de dados básicas, como o início, stop e pause.  Nesta lição, irá adicionar um controle da barra de controlo de deslize para a aplicação.

Neste tutorial, utilizamos um temporizador para atualizar a posição de controlo de deslize com base na posição atual do controle MediaElement.  O início de controlo de deslize e de fim também tempo precisem de atualização em caso de conteúdos em direto.  Isso pode ser melhor Tratado no evento de atualização de origem adaptável.

Origens de suportes de dados são objetos que geram dados de suporte de dados.  O Resolvedor de origem considera um fluxo de URL ou bytes e cria a origem de mídia apropriada para esse conteúdo.  O Resolvedor de origem é a forma padrão para as aplicações criar origens de suportes de dados. 

Nesta lição contém os seguintes procedimentos:

1. Registrar o manipulador de transmissão em fluxo uniforme 
2. Adicione os manipuladores de eventos ao nível do Gestor de origem adaptável
3. Adicione os manipuladores de eventos de nível de origem adaptável
4. Adicionar manipuladores de eventos do MediaElement
5. Adicionar controlo de deslize de barra código relacionadas
6. Compilar e testar a aplicação

**Para se registrar o manipulador de fluxo de bytes de transmissão em fluxo uniforme e passar o propertyset**

1. No Explorador de soluções, o botão direito do rato clique em **mainpage. XAML**e, em seguida, clique em **View Code**.
2. No início do ficheiro, adicione o seguinte usando a instrução:

   ```csharp
        using Microsoft.Media.AdaptiveStreaming;
   ```
3. No início da classe MainPage, adicione os seguintes membros de dados:

   ```csharp
         private Windows.Foundation.Collections.PropertySet propertySet = new Windows.Foundation.Collections.PropertySet();             
         private IAdaptiveSourceManager adaptiveSourceManager;
   ```
4. Dentro de **MainPage** construtor, adicione o seguinte código após o **isso. Inicializar Components();**  o registo e de linha de código linhas escritas na lição anterior:

   ```csharp
        // Gets the default instance of AdaptiveSourceManager which manages Smooth 
        //Streaming media sources.
        adaptiveSourceManager = AdaptiveSourceManager.GetDefault();
        // Sets property key value to AdaptiveSourceManager default instance.
        // {A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}" must be hardcoded.
        propertySet["{A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}"] = adaptiveSourceManager;
   ```
5. Dentro do **MainPage** construtor, modifique os dois métodos de RegisterByteStreamHandler para adicionar o estipulado parâmetros:

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
6. Prima **CTRL + S** para guardar o ficheiro.

**Para adicionar o manipulador de eventos ao nível do Gestor de origem adaptável**

1. No Explorador de soluções, o botão direito do rato clique em **mainpage. XAML**e, em seguida, clique em **View Code**.
2. Dentro de **MainPage** de classe, adicione o seguinte membro de dados:

   ```csharp
     private AdaptiveSource adaptiveSource = null;
   ```
3. No final da **MainPage** de classe, adicione o seguinte manipulador de eventos:

   ```csharp
         # region Adaptive Source Manager Level Events
         private void mediaElement_AdaptiveSourceOpened(AdaptiveSource sender, AdaptiveSourceOpenedEventArgs args)
         {

            adaptiveSource = args.AdaptiveSource;
         }

         # endregion Adaptive Source Manager Level Events
   ```
4. No final da **MainPage** construtor, adicione a seguinte linha para subscrever o evento aberto de origem adaptável:

   ```csharp
         adaptiveSourceManager.AdaptiveSourceOpenedEvent += 
           new AdaptiveSourceOpenedEventHandler(mediaElement_AdaptiveSourceOpened);
   ```
5. Prima **CTRL + S** para guardar o ficheiro.

**Para adicionar manipuladores de eventos de nível de origem adaptável**

1. No Explorador de soluções, o botão direito do rato clique em **mainpage. XAML**e, em seguida, clique em **View Code**.
2. Dentro de **MainPage** de classe, adicione o seguinte membro de dados:

   ```csharp
     private AdaptiveSourceStatusUpdatedEventArgs adaptiveSourceStatusUpdate; 
     private Manifest manifestObject;
   ```
3. No final da **MainPage** de classe, adicione os manipuladores de eventos seguintes:

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
4. No final da **mediaElement AdaptiveSourceOpened** método, adicione o seguinte código para subscrever os eventos:

   ```csharp
         adaptiveSource.ManifestReadyEvent +=

                    mediaElement_ManifestReady;
         adaptiveSource.AdaptiveSourceStatusUpdatedEvent += 

            mediaElement_AdaptiveSourceStatusUpdated;
         adaptiveSource.AdaptiveSourceFailedEvent += 

            mediaElement_AdaptiveSourceFailed;
   ```
5. Prima **CTRL + S** para guardar o ficheiro.

Os mesmos eventos estão disponíveis na origem adaptável Manager nível, que pode ser usado para lidar com a funcionalidade comum a todos os elementos de multimédia na aplicação. Cada AdaptiveSource inclui seus próprios eventos e todos os eventos de AdaptiveSource irão ser cascaded em AdaptiveSourceManager.

**Para adicionar manipuladores de eventos de elemento de multimédia**

1. No Explorador de soluções, o botão direito do rato clique em **mainpage. XAML**e, em seguida, clique em **View Code**.
2. No final da **MainPage** de classe, adicione os manipuladores de eventos seguintes:

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
3. No final da **MainPage** construtor, adicione o seguinte código para dolní Index Je aos eventos:

   ```csharp
         mediaElement.MediaOpened += MediaOpened;
         mediaElement.MediaEnded += MediaEnded;
         mediaElement.MediaFailed += MediaFailed;
   ```
4. Prima **CTRL + S** para guardar o ficheiro.

**Para adicionar a barra de controlo de deslize relacionados com o código**

1. No Explorador de soluções, o botão direito do rato clique em **mainpage. XAML**e, em seguida, clique em **View Code**.
2. No início do ficheiro, adicione o seguinte usando a instrução:

   ```csharp
        using Windows.UI.Core;
   ```
3. Dentro de **MainPage** de classe, adicione os seguintes membros de dados:

   ```csharp
         public static CoreDispatcher _dispatcher;
         private DispatcherTimer sliderPositionUpdateDispatcher;
   ```
4. No final da **MainPage** construtor, adicione o seguinte código:

   ```csharp
         _dispatcher = Window.Current.Dispatcher;
         PointerEventHandler pointerpressedhandler = new PointerEventHandler(sliderProgress_PointerPressed);
         sliderProgress.AddHandler(Control.PointerPressedEvent, pointerpressedhandler, true);    
   ```
5. No final da **MainPage** , adicione o seguinte código:

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
   > CoreDispatcher é utilizado para fazer alterações para o thread de interface do Usuário do Thread de interface do Usuário não. Em caso de estrangulamento no dispatcher thread, o desenvolvedor pode optar por usar dispatcher fornecida pelo elemento de interface do Usuário, ele pretende atualizar.  Por exemplo:

   ```csharp
         await sliderProgress.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () => { TimeSpan 

         timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime); 
         double absvalue  = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero); 

         sliderProgress.Maximum = absvalue; }); 
   ```
6. No final da **mediaElement_AdaptiveSourceStatusUpdated** método, adicione o seguinte código:

   ```csharp
         setSliderStartTime(args.StartTime);
         setSliderEndTime(args.EndTime);
   ```
7. No final da **MediaOpened** método, adicione o seguinte código:

   ```csharp
         sliderProgress.StepFrequency = SliderFrequency(mediaElement.NaturalDuration.TimeSpan);
         sliderProgress.Width = mediaElement.Width;
         setupTimer();
   ```
8. Prima **CTRL + S** para guardar o ficheiro.

**Para compilar e testar a aplicação**

1. Prima **F6** para compilar o projeto. 
2. Prima **F5** para executar a aplicação.
3. Na parte superior da aplicação, pode utilizar o URL de transmissão em fluxo uniforme predefinido ou introduzir um diferente. 
4. Clique em **definir a origem**. 
5. Teste a barra de controlo de deslize.

Ter concluído a lição 2.  Nesta lição adicionou um controlo de deslize à aplicação. 

## <a name="lesson-3-select-smooth-streaming-streams"></a>Lição 3: Selecione fluxos de transmissão em fluxo uniforme
A transmissão em fluxo uniforme é capaz de transmitir conteúdo com várias faixas áudio de idioma que são selecionáveis pelos visualizadores.  Nesta lição, irá ativar visualizadores selecionar fluxos. Nesta lição contém os seguintes procedimentos:

1. Modifique o arquivo XAML.
2. Modificar o código por trás do ficheiro
3. Compilar e testar a aplicação

**Para modificar o arquivo XAML.**

1. No Explorador de soluções, clique com botão direito **mainpage. XAML**e, em seguida, clique em **estruturador de vistas**.
2. Localize &lt;Grid.RowDefinitions&gt;e modificar o RowDefinitions, para que eles se parece com:

   ```xml
         <Grid.RowDefinitions>            
            <RowDefinition Height="20"/>
            <RowDefinition Height="50"/>
            <RowDefinition Height="100"/>
            <RowDefinition Height="80"/>
            <RowDefinition Height="50"/>
         </Grid.RowDefinitions>
   ```
3. Dentro de &lt;grade&gt;&lt;/Grid&gt; etiquetas, adicione o seguinte código para definir um controlo de caixa de listagem, para que os utilizadores podem ver a lista de fluxos disponíveis e selecione fluxos:

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
4. Prima **CTRL + S** para guardar as alterações.

**Para modificar o código por trás do ficheiro**

1. No Explorador de soluções, clique com botão direito **mainpage. XAML**e, em seguida, clique em **View Code**.
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
3. No início da classe MainPage, adicione as seguintes definições de variável:

   ```csharp
         private List<Stream> availableStreams;
         private List<Stream> availableAudioStreams;
         private List<Stream> availableTextStreams;
         private List<Stream> availableVideoStreams;
   ```
4. Dentro da classe MainPage, adicione a região seguinte:
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
5. Localize o método mediaElement_ManifestReady, acrescente o seguinte código no final da função:
   ```csharp
        getStreams(manifestObject);
        refreshAvailableStreamsListBoxItemSource();
   ```
    Então, quando MediaElement manifesto estiver pronto, o código obtém uma lista de fluxos de disponíveis e preenche a caixa de listagem da interface do Usuário com a lista.
6. Dentro da classe MainPage, localize a interface do Usuário botões clique a região de eventos e, em seguida, adicione a seguinte definição de função:
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
   **Para compilar e testar a aplicação**

1. Prima **F6** para compilar o projeto. 
2. Prima **F5** para executar a aplicação.
3. Na parte superior da aplicação, pode utilizar o URL de transmissão em fluxo uniforme predefinido ou introduzir um diferente. 
4. Clique em **definir a origem**. 
5. O idioma predefinido é audio_eng. Tente alternar entre audio_eng e audio_es. Sempre que seleciona um novo fluxo, tem de clicar no botão Enviar.

Ter concluído a lição 3.  Nesta lição, adicionar a funcionalidade para escolher fluxos.

## <a name="lesson-4-select-smooth-streaming-tracks"></a>Lição 4: Selecione as faixas de transmissão em fluxo uniforme
Uma apresentação de transmissão em fluxo uniforme pode conter vários ficheiros de vídeo codificados com níveis de qualidade diferente (taxas de bits) e as resoluções. Nesta lição, irá permitir aos usuários selecionar roteiros. Nesta lição contém os seguintes procedimentos:

1. Modifique o arquivo XAML.
2. Modificar o código por trás do ficheiro
3. Compilar e testar a aplicação

**Para modificar o arquivo XAML.**

1. No Explorador de soluções, clique com botão direito **mainpage. XAML**e, em seguida, clique em **estruturador de vistas**.
2. Localize a &lt;grade&gt; etiqueta com o nome **gridStreamAndBitrateSelection**, acrescente o seguinte código no final da etiqueta:
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
3. Prima **CTRL + S** para guardar as alterações de he

**Para modificar o código por trás do ficheiro**

1. No Explorador de soluções, clique com botão direito **mainpage. XAML**e, em seguida, clique em **View Code**.
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
3. No início da classe MainPage, adicione as seguintes definições de variável:
   ```csharp
        private List<Track> availableTracks;
   ```
4. Dentro da classe MainPage, adicione a região seguinte:
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
5. Localize o método mediaElement_ManifestReady, acrescente o seguinte código no final da função:
   ```csharp
         getTracks(manifestObject);
         refreshAvailableTracksListBoxItemSource();
   ```
6. Dentro da classe MainPage, localize a interface do Usuário botões clique a região de eventos e, em seguida, adicione a seguinte definição de função:
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
   **Para compilar e testar a aplicação**

1. Prima **F6** para compilar o projeto. 
2. Prima **F5** para executar a aplicação.
3. Na parte superior da aplicação, pode utilizar o URL de transmissão em fluxo uniforme predefinido ou introduzir um diferente. 
4. Clique em **definir a origem**. 
5. Por predefinição, todas as faixas de fluxo de vídeo estão selecionadas. Para testar as alterações de taxa de bits, pode selecionar a taxa de bits mais baixa disponível e, em seguida, selecione a taxa de bits mais elevada disponível. Tem de clicar em enviar após cada alteração.  Pode ver as alterações de qualidade de vídeo.

Ter concluído a Lição 4.  Nesta lição, adicionar a funcionalidade para escolher os roteiros.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="other-resources"></a>Outros recursos:
* [Como criar uma aplicação de Smooth Streaming da Windows 8 JavaScript com funcionalidades avançadas](https://blogs.iis.net/cenkd/archive/2012/08/10/how-to-build-a-smooth-streaming-windows-8-javascript-application-with-advanced-features.aspx)
* [Smooth Streaming visão geral técnica do](https://www.iis.net/learn/media/on-demand-smooth-streaming/smooth-streaming-technical-overview)

[PlayerApplication]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-1.png
[CodeViewPic]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-2.png

