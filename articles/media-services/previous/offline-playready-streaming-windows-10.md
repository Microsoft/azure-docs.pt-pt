---
title: Configurar a sua conta para a transmissão em fluxo offline do conteúdo de PlayReady protegido - Azure
description: Este artigo mostra como configurar a sua conta de Media Services do Azure para transmissão em fluxo offline PlayReady para Windows 10.
services: media-services
keywords: Android de modo Offline, ExoPlayer, DASH, DRM, Widevine
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2019
ms.author: willzhan
ms.openlocfilehash: 7d907e3d06e8c2817b1fc4a30a7f808d06b022ce
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57884715"
---
# <a name="offline-playready-streaming-for-windows-10"></a>PlayReady offline de transmissão em fluxo para o Windows 10  

> [!div class="op_single_selector" title1="Select the version of Media Services that you are using:"]
> * [Versão 3](../latest/offline-plaready-streaming-for-windows-10.md)
> * [Versão 2](offline-playready-streaming-windows-10.md)

Serviços de multimédia do Azure suportam offline download/reprodução com proteção DRM. Este artigo aborda o suporte offline de clientes de 10/PlayReady de serviços de multimédia do Azure para Windows. Pode ler sobre o suporte de modo offline para iOS/FairPlay e dispositivos Android/Widevine nos seguintes artigos:

- [Offline FairPlay Streaming para iOS](media-services-protect-hls-with-offline-fairplay.md)
- [Offline Widevine para transmissão em fluxo para Android](offline-widevine-for-android.md)

## <a name="overview"></a>Descrição geral

Esta secção fornece algumas informações básicas sobre a reprodução de modo offline, especialmente por que motivo:

* Em alguns países, disponibilidade de Internet e/ou de largura de banda é continua a ser limitada. Os utilizadores podem optar por transferir primeiro para conseguir ver o conteúdo na resolução de alto o suficiente para a experiência de visualização satisfatória. Neste caso, mais freqüentemente, o problema não é a disponibilidade de rede, em vez disso, é a largura de banda de rede limitada. Fornecedores OTT/OVP Pedimos para suporte ao modo offline.
* Como divulgadas na conferência de acionistas Netflix 2016 P3, transferência do conteúdo é um "recurso solicitado lema freqüentemente" e "estamos abertos ao mesmo" disse por Reed Hastings, CEO da Netflix.
* Alguns fornecedores de conteúdo pode não permitir a entrega de licenças DRM além do limite de um país. Se um usuário precisa ir exterior e ainda quiser ver conteúdo, é necessário o offline download.
 
O desafio que enfrentamos na implementação do modo offline é a seguinte:

* MP4 é suportada por muitos leitores, ferramentas de codificador, mas não existe nenhum vínculo entre o contentor de MP4 e DRM;
* A longo prazo, CFF com CENC é a melhor opção. No entanto, hoje em dia, o ecossistema de suporte de ferramentas/player ainda não está lá. É necessário uma solução, hoje em dia.
 
A idéia é: transmissão em fluxo uniforme ([PIFF](https://go.microsoft.com/?linkid=9682897)) formato de ficheiro com h264 taxa de bits/AAC tem um vínculo com PlayReady (AES-128 CTR). Smooth streaming .ismv arquivo individual (partindo do princípio de áudio é muxed vídeo) em si é um fMP4 e pode ser utilizado para a reprodução. Se der um conteúdo de transmissão em fluxo uniforme através da encriptação do PlayReady, cada ficheiro .ismv torna-se um PlayReady protegido real de MP4 fragmentado. Podemos escolher um ficheiro de .ismv com a velocidade de transmissão preferencial e renomeá-lo como. mp4 para download.

Existem duas opções para alojar o PlayReady protegidas MP4 para transferência progressiva:

* Um pode colocar este MP4 no mesmo elemento de serviço de contentor/suporte de dados e tire partido dos serviços de multimédia do Azure ponto final para transferência progressiva; de transmissão em fluxo
* É possível usar o localizador SAS para transferência progressiva diretamente a partir do armazenamento do Azure, ignorando os serviços de multimédia do Azure.
 
Pode usar dois tipos de entrega de licenças do PlayReady:

* Serviço de entrega de licença PlayReady nos serviços de multimédia do Azure;
* Servidores de licença do PlayReady alojados em qualquer lugar.

Seguem-se dois conjuntos de recursos de teste, primeiro com disponibilização de licença PlayReady no AMS ao segundo aquele com meu servidor de licenças do PlayReady alojada numa VM do Azure:

Asset #1:

* URL de transferência progressiva: [https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4)
* PlayReady LA_URL (AMS): [https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/](https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/)

Asset #2:

* URL de transferência progressiva: [https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4)
* PlayReady LA_URL (no local): [https://willzhan12.cloudapp.net/playready/rightsmanager.asmx](https://willzhan12.cloudapp.net/playready/rightsmanager.asmx)

Para fins de teste de reprodução, usei uma aplicação Universal do Windows no Windows 10. Na [exemplos do Windows 10 Universal](https://github.com/Microsoft/Windows-universal-samples), há um exemplo de leitor básico chamado [exemplo de transmissão em fluxo adaptável](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/AdaptiveStreaming). Tudo o que precisamos fazer é adicionar o código para nós escolher o vídeo transferido e utilizá-lo como a origem, em vez de origem de transmissão em fluxo adaptável. As alterações são no botão de manipulador de eventos de clique:

```csharp
private async void LoadUri_Click(object sender, RoutedEventArgs e)
{
    //Uri uri;
    //if (!Uri.TryCreate(UriBox.Text, UriKind.Absolute, out uri))
    //{
    // Log("Malformed Uri in Load text box.");
    // return;
    //}
    //LoadSourceFromUriTask = LoadSourceFromUriAsync(uri);
    //await LoadSourceFromUriTask;

    //willzhan change start
    // Create and open the file picker
    FileOpenPicker openPicker = new FileOpenPicker();
    openPicker.ViewMode = PickerViewMode.Thumbnail;
    openPicker.SuggestedStartLocation = PickerLocationId.ComputerFolder;
    openPicker.FileTypeFilter.Add(".mp4");
    openPicker.FileTypeFilter.Add(".ismv");
    //openPicker.FileTypeFilter.Add(".mkv");
    //openPicker.FileTypeFilter.Add(".avi");

    StorageFile file = await openPicker.PickSingleFileAsync();

    if (file != null)
    {
       //rootPage.NotifyUser("Picked video: " + file.Name, NotifyType.StatusMessage);
       this.mediaPlayerElement.MediaPlayer.Source = MediaSource.CreateFromStorageFile(file);
       this.mediaPlayerElement.MediaPlayer.Play();
       UriBox.Text = file.Path;
    }
    else
    {
       // rootPage.NotifyUser("Operation cancelled.", NotifyType.ErrorMessage);
    }

    // On small screens, hide the description text to make room for the video.
    DescriptionText.Visibility = (ActualHeight < 500) ? Visibility.Collapsed : Visibility.Visible;
}
```

 ![Reprodução de modo offline PlayReady protegidos fMP4](./media/offline-playready/offline-playready1.jpg)

Uma vez que o vídeo estiver com proteção PlayReady, a captura de ecrã não será capaz de incluir o vídeo.

Em resumo, alcançámos modo offline nos serviços de multimédia do Azure:

* Transcodificação de conteúdo e encriptação PlayReady podem ser feitos em serviços de multimédia do Azure ou outras ferramentas;
* Conteúdo pode ser hospedado em serviços de multimédia do Azure ou o armazenamento do Azure para transferência progressiva;
* Entrega de licenças do PlayReady pode ser dos serviços de multimédia do Azure ou em outro lugar;
* O conteúdo de transmissão em fluxo uniforme preparado ainda podem ser utilizados para transmissão em fluxo online por meio de TRAÇO ou uniforme com PlayReady como o DRM.

## <a name="next-steps"></a>Passos Seguintes

[Estrutura de sistema de DRM híbrido](hybrid-design-drm-sybsystem.md)
