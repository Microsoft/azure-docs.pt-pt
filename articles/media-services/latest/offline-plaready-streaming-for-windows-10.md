---
title: Configurar o streaming do PlayReady offline com os serviços de mídia do Azure v3
description: Este artigo mostra como configurar sua conta dos serviços de mídia do Azure para o PlayReady de streaming para Windows 10 offline.
services: media-services
keywords: DASH, DRM, modo offline Widevine, ExoPlayer, Android
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/01/2019
ms.author: willzhan
ms.openlocfilehash: ceb6de6556968385d88ac799c11bdb6393072864
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513121"
---
# <a name="offline-playready-streaming-for-windows-10-with-media-services-v3"></a>Streaming do PlayReady offline para Windows 10 com os serviços de mídia v3

Os serviços de mídia do Azure dão suporte a download/reprodução offline com proteção DRM. Este artigo aborda o suporte offline dos serviços de mídia do Azure para clientes Windows 10/PlayReady. Você pode ler sobre o suporte do modo offline para dispositivos iOS/FairPlay e Android/Widevine nos seguintes artigos:

- [Offline FairPlay Streaming for iOS](offline-fairplay-for-ios.md) (Transmissão Offline do FairPlay para iOS)
- [Streaming de Widevine offline para Android](offline-widevine-for-android.md)

> [!NOTE]
> O DRM offline é cobrado apenas para fazer uma única solicitação de licença quando você baixa o conteúdo. Quaisquer erros não são cobrados.

## <a name="overview"></a>Visão geral

Esta seção fornece alguns planos de fundo sobre reprodução em modo offline, especialmente por que:

* Em alguns países/regiões, a disponibilidade e/ou a largura de banda da Internet ainda é limitada. Os usuários podem optar por baixar primeiro para poder assistir conteúdo em resolução alta o suficiente para uma experiência de exibição satisfatória. Nesse caso, com mais frequência, o problema não é a disponibilidade da rede, em vez disso, a largura de banda da rede é limitada. Os provedores de OTT/OVP estão solicitando suporte ao modo offline.
* Como divulgado em Netflix 2016 T3, a conferência de acionistas, o download de conteúdo é um "recurso solicitado", e "Estamos abertos", dito por Reed Hastings, Netflix CEO.
* Alguns provedores de conteúdo podem impedir a entrega de licença do DRM além da borda de um país/região. Se um usuário precisar viajar para o exterior e ainda quiser assistir ao conteúdo, será necessário fazer o download offline.
 
O desafio que enfrentamos na implementação do modo offline é o seguinte:

* Há suporte para MP4 em muitos players, ferramentas de codificador, mas não há associação entre o contêiner MP4 e o DRM;
* A longo prazo, o CFF com CENC é a maneira de ir. No entanto, hoje, o ecossistema de suporte de ferramentas/jogador ainda não está lá. Precisamos de uma solução, hoje.
 
A ideia é: formato de arquivo de Smooth streaming ([PIFF](https://docs.microsoft.com/iis/media/smooth-streaming/protected-interoperable-file-format)) com H264/AAC tem uma associação com PLAYREADY (AES-128 CTR). O arquivo. ismv de Smooth streaming individual (supondo que o áudio é muxed em vídeo) é um fMP4 e pode ser usado para reprodução. Se um conteúdo de Smooth streaming passar pela criptografia PlayReady, cada arquivo. ismv se tornará um MP4 fragmentado protegido pelo PlayReady. Podemos escolher um arquivo. ismv com a taxa de bits preferida e renomeá-lo como. MP4 para download.

Há duas opções para hospedar o MP4 protegido do PlayReady para download progressivo:

* Um pode colocar esse MP4 no mesmo contêiner/ativo de serviço de mídia e aproveitar o ponto de extremidade de streaming dos serviços de mídia do Azure para download progressivo;
* Um pode usar o localizador SAS para download progressivo diretamente do armazenamento do Azure, ignorando os serviços de mídia do Azure.
 
Você pode usar dois tipos de entrega de licença do PlayReady:

* Serviço de entrega de licença do PlayReady nos serviços de mídia do Azure;
* Servidores de licença PlayReady hospedados em qualquer lugar.

Abaixo estão dois conjuntos de ativos de teste, o primeiro usando a entrega de licença do PlayReady no AMS, enquanto o segundo usa meu servidor de licença do PlayReady hospedado em uma VM do Azure:

#1 do ativo:

* URL de download progressivo: [https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4")
* LA_URL do PlayReady (AMS): [https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/](https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/)

#2 do ativo:

* URL de download progressivo: [https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4)
* LA_URL PlayReady (local): [https://willzhan12.cloudapp.net/playready/rightsmanager.asmx](https://willzhan12.cloudapp.net/playready/rightsmanager.asmx)

Para testes de reprodução, usamos um aplicativo universal do Windows no Windows 10. Em [exemplos universais do Windows 10](https://github.com/Microsoft/Windows-universal-samples), há um exemplo de player básico chamado [amostra de streaming adaptável](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/AdaptiveStreaming). Tudo o que precisamos fazer é adicionar o código para que possamos escolher o vídeo baixado e usá-lo como a origem, em vez da fonte de streaming adaptável. As alterações estão no manipulador de eventos de clique do botão:

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

![Reprodução em modo offline de fMP4 protegido do PlayReady](./media/offline-playready-for-windows/offline-playready1.jpg)

Como o vídeo está sob a proteção do PlayReady, a captura de tela não poderá incluir o vídeo.

Em resumo, atingimos o modo offline nos serviços de mídia do Azure:

* A transcodificação de conteúdo e a criptografia PlayReady podem ser feitas nos serviços de mídia do Azure ou outras ferramentas;
* O conteúdo pode ser hospedado nos serviços de mídia do Azure ou no armazenamento do Azure para download progressivo;
* A entrega de licença do PlayReady pode ser dos serviços de mídia do Azure ou em outro lugar;
* O conteúdo de Smooth streaming preparado ainda pode ser usado para streaming online via DASH ou Smooth com PlayReady como o DRM.

## <a name="additional-notes"></a>Notas adicionais

* O Widevine é um serviço fornecido pela Google Inc. e sujeito aos termos de serviço e à política de privacidade da Google, Inc.

## <a name="next-steps"></a>Passos seguintes

[Conceção de um sistema de proteção de conteúdos multi-DRM com controlo de acesso](design-multi-drm-system-with-access-control.md)
