---
title: Configure a sua conta para o streaming offline de conteúdo protegido playReady - Azure
description: Este artigo mostra como configurar a sua conta Azure Media Services para o streaming PlayReady para o Windows 10 offline.
services: media-services
keywords: DASH, DRM, Widevine Offline Mode, ExoPlayer, Android
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2019
ms.author: willzhan
ms.custom: devx-track-csharp
ms.openlocfilehash: bd1d50003ef4334f1ea83ac256b371b20d021936
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98693897"
---
# <a name="offline-playready-streaming-for-windows-10"></a>Offline PlayReady Streaming for Windows 10 (Transmissão em Fluxo Offline do PlayReady para Windows 10)

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector" title1="Selecione a versão dos Serviços de Media que está a utilizar:"]
> * [Versão 3](../latest/offline-plaready-streaming-for-windows-10.md)
> * [Versão 2](offline-playready-streaming-windows-10.md)

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Confira a versão mais recente, [Media Services v3](../latest/index.yml). Além disso, consulte [a orientação de migração de v2 para v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Azure Media Services suporta descarregamento/reprodução offline com proteção DRM. Este artigo abrange suporte offline da Azure Media Services para clientes Windows 10/PlayReady. Pode ler sobre o suporte ao modo offline para dispositivos iOS/FairPlay e Android/Widevine nos seguintes artigos:

- [Offline FairPlay Streaming for iOS](media-services-protect-hls-with-offline-fairplay.md) (Transmissão Offline do FairPlay para iOS)
- [Streaming de Widevine offline para Android](offline-widevine-for-android.md)

## <a name="overview"></a>Descrição geral

Esta secção dá algum fundo na reprodução do modo offline, especialmente por que:

* Em alguns países/regiões, a disponibilidade de Internet e/ou largura de banda ainda é limitada. Os utilizadores podem optar por descarregar primeiro para poderem ver conteúdos em resolução suficientemente alta para uma experiência de visualização satisfatória. Neste caso, mais frequentemente, a questão não é a disponibilidade da rede, mas sim a largura de banda de rede limitada. Os fornecedores de OTT/OVP estão a pedir suporte ao modo offline.
* Conforme divulgado na conferência de acionistas do Q3 da Netflix de 2016, o download de conteúdos é uma "funcionalidade solicitada", e "estamos abertos a isso" dito por Reed Hastings, CEO da Netflix.
* Alguns fornecedores de conteúdo podem não permitir a entrega de licenças de DRM para além da fronteira entre um país e uma região. Se um utilizador precisar de viajar para o estrangeiro e ainda quiser ver conteúdos, é necessário descarregar offline.
 
O desafio que enfrentamos na implementação do modo offline é o seguinte:

* O MP4 é suportado por muitos intervenientes, ferramentas codificantes, mas não existe ligação entre o contentor MP4 e o DRM;
* A longo prazo, cff com CENC é o caminho a seguir. No entanto, hoje em dia, o ecossistema de suporte de ferramentas/jogador ainda não está lá. Precisamos de uma solução, hoje.
 
A ideia é: o formato de ficheiro de streaming suave[(PIFF)](/iis/media/smooth-streaming/protected-interoperable-file-format)com H264/AAC tem uma ligação com o PlayReady (AES-128 CTR). O ficheiro de streaming suave individual .ismv (assumindo que o áudio é usado em vídeo) é em si um fMP4 e pode ser usado para reprodução. Se um conteúdo de streaming suave passar pela encriptação PlayReady, cada ficheiro .ismv torna-se um MP4 fragmentado protegido do PlayReady. Podemos escolher um ficheiro .ismv com o bitrate preferido e renomeá-lo como .mp4 para download.

Existem duas opções para hospedar o MP4 protegido do PlayReady para download progressivo:

* Pode-se colocar este MP4 no mesmo ativo de serviço de contentores/meios de comunicação e alavancar o ponto final de streaming da Azure Media Services para download progressivo;
* Pode-se utilizar o localizador SAS para download progressivo diretamente do Azure Storage, contornando o Azure Media Services.
 
Pode utilizar dois tipos de entrega de licença PlayReady:

* Serviço de entrega de licenças PlayReady em Azure Media Services;
* Os servidores de licença PlayReady hospedados em qualquer lugar.

Abaixo estão dois conjuntos de ativos de teste, o primeiro usando a entrega de licença PlayReady em AMS, enquanto o segundo usando o meu servidor de licença PlayReady hospedado num Azure VM:

#1 de ativos:

* URL de descarregamento progressivo: [https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4)
* PlayReady LA_URL (AMS): `https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/`

#2 de ativos:

* URL de descarregamento progressivo: [https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4)
* PlayReady LA_URL (on-prem): `https://willzhan12.cloudapp.net/playready/rightsmanager.asmx`

Para testes de reprodução, usei uma aplicação universal do Windows no Windows 10. Nas [amostras universais do Windows 10,](https://github.com/Microsoft/Windows-universal-samples)existe uma amostra básica do jogador chamada [Amostra de Streaming Adaptativa.](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/AdaptiveStreaming) Tudo o que temos de fazer é adicionar o código para que possamos escolher o vídeo descarregado e usá-lo como fonte, em vez de uma fonte de streaming adaptativa. As alterações estão no botão clique no manipulador de eventos:

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

 ![Reprodução offline do modo PlayReady protegido fMP4](./media/offline-playready/offline-playready1.jpg)

Uma vez que o vídeo está sob proteção PlayReady, a imagem não será capaz de incluir o vídeo.

Em resumo, conseguimos o modo offline nos Serviços Azure Media:

* A transcodização de conteúdos e a encriptação PlayReady podem ser feitas em Azure Media Services ou outras ferramentas;
* Os conteúdos podem ser hospedados nos Azure Media Services ou no Azure Storage para download progressivo;
* A entrega da licença PlayReady pode ser da Azure Media Services ou em qualquer outro lugar;
* O conteúdo de streaming suave preparado ainda pode ser utilizado para streaming on-line via DASH ou suave com o PlayReady como DRM.

## <a name="additional-notes"></a>Notas adicionais

* Widevine é um serviço fornecido pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.

## <a name="next-steps"></a>Passos seguintes

[Estrutura de sistema de DRM híbrido](hybrid-design-drm-sybsystem.md)
