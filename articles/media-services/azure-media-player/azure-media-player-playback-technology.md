---
title: Tecnologia de reprodução do Azure Media Player
description: Saiba mais sobre a tecnologia de reprodução usada para reproduzir o vídeo ou áudio.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.custom: devx-track-js
ms.openlocfilehash: 24a38fde3d1b6bbe1efef9241458da846c186112
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97916342"
---
# <a name="playback-technology-tech"></a>Tecnologia de reprodução ("tech") #

A tecnologia de reprodução refere-se à tecnologia específica de navegador ou plugin utilizada para reproduzir o vídeo ou áudio.

- **azureHtml5JS**: utiliza as normas MSE e EME em conjunto com o elemento de vídeo para a reprodução sem plugin de conteúdo DASH com suporte para conteúdo encriptado envelope AES-128 ou conteúdo encriptado comum DRM (via PlayReady e Widevine quando o navegador o suporta) da Azure Media Services
- **flashSS**: utiliza a tecnologia flash player para reproduzir conteúdo suave com suporte para desencriptação de envelope de bitS-128 da Azure Media Services - requer versão Flash de 11.4 ou superior
- **html5FairPlayHLS**: utiliza o Safari específico na tecnologia de reprodução baseada no navegador via HLS com o elemento de vídeo. Esta tecnologia é necessária para reproduzir o fairPlay conteúdo protegido da Azure Media Services e foi adicionado ao techOrder a partir de 10/19/16
- **silverlightSS**: utiliza tecnologia silverlight para reproduzir conteúdo suave com suporte para o conteúdo protegido playReady da Azure Media Services.
- **html5**: utiliza na tecnologia de reprodução baseada no navegador com o elemento de vídeo.  Quando num dispositivo Apple iOS ou Android, esta tecnologia permite a reprodução de streams HLS com algum suporte básico para encriptação de envelopes AES-128 ou conteúdo DRM (via FairPlay quando o navegador o suporta).

## <a name="tech-order"></a>Ordem Tecnológica ##

Para garantir que o seu ativo é jogável numa grande variedade de dispositivos, recomenda-se a seguinte ordem tecnológica e é o padrão se: `techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"]` e pode ser definido diretamente nas `<video>` opções ou programaticamente:

`<video data-setup='{"techOrder": ["azureHtml5JS", "flashSS", "html5FairPlayHLS, "silverlightSS", "html5"]}`

ou

```javascript
    amp("vid1", {
          techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS, "silverlightSS", "html5"]
    });
```

## <a name="compatibility-matrix"></a>Matriz de compatibilidade ##

Dada a ordem tecnológica recomendada com conteúdo de streaming da Azure Media Services, espera-se a seguinte matriz de reprodução de compatibilidade

| Browser        | SO                                                       | Tecnologia Esperada (Clara)  | Tecnologia Esperada (AES)  | Tecnologia Esperada (DRM)          |
|----------------|----------------------------------------------------------|------------------------|----------------------|------------------------------|
| EdgeIE 11      | Windows 10, Windows 8.1, Windows Phone 10<sup>1</sup>               | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (PlayReady)     |
| IE 11          | Windows 7, Windows Vista<sup>1</sup>                     | flashss                | flashss              | silverlightSS (PlayReady)    |
| IE 11          | Windows Phone 8.1                                        | azureHtml5JS           | azureHtml5JS         | não apoiado                |
| Microsoft Edge           | Xbox One<sup>1</sup> (atualização nov 2015)                   | azureHtml5JS           | azureHtml5JS         | não apoiado                |
| Cromo 37+     | Windows 10, Windows 8.1, macOS X Yosemite<sup>1</sup>   | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Firefox 47+    | Windows 10, Windows 8.1, macOS X Yosemite+<sup>1</sup>  | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Firefox 42-46  | Windows 10, Windows 8.1, macOS X Yosemite+<sup>1</sup>  | azureHtml5JS           | azureHtml5JS         | silverlightSS (PlayReady)    |
| Firefox 35-41  | Windows 10, Windows 8.1                                  | flashss                | flashss              | silverlightSS (PlayReady)    |
| Safari         | iOS 6+                                                   | html5                  | html5 (sem token)3    | não apoiado                |
| Safari 8+      | OS X Yosemite+                                           | azureHtml5JS           | azureHtml5JS         | html5FairPlayHLS (FairPlay)  |
| Safari 6       | Leão da Montanha OS X<sup>1</sup>                           | flashss                | flashss              | silverlightSS (PlayReady)    |
| Cromo 37+     | Android 4.4.4+<sup>2</sup>                               | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Cromo 37+     | Android 4.02                                             | html5                  | html5 (sem token)<sup>3</sup>    | não apoiado                |
| Firefox 42+    | Android 5.0+<sup>2</sup>                                 | azureHtml5JS           | azureHtml5JS         | não apoiado                |
| Ou é 8, iE 9, iE 10  | Windows                                                  | não apoiado          | não apoiado        | não apoiado                |

<sup>1</sup> Configuração não suportada ou testada; listado como referência para a conclusão.

<sup>2</sup> A reprodução bem sucedida em dispositivos Android requer uma combinação de capacidades do dispositivo, suporte gráfico, renderização de codec, suporte de SO e muito mais. Uma vez que o Android é uma plataforma de código aberto que permite aos fabricantes de telefones alterar o Sistema Desnudo Android Fornecido pela Google, isto causa alguma fragmentação no espaço Android, e alguns dispositivos podem não ser suportados por falta de funcionalidades. Além disso, alguns dispositivos Android não têm suporte para todos os codecs.  

<sup>3</sup> Nos casos em que não exista suporte para token, pode ser utilizado um proxy para adicionar esta funcionalidade. Confira este [blog](https://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/) para saber mais sobre esta solução.

> [!NOTE]
> Se a tecnologia escolhida exigir a instalação de um plugin, como o Flash, e que não esteja instalada na máquina do utilizador, a AMP continuará a verificar as capacidades da próxima tecnologia, em conjunto com os tipos de origem e informações de proteção, na lista de tecnologia. Por exemplo, se tentar visualizar um fluxo on demand desprotegido no Safari 8 no OS X Yosemite, e tanto flash como Silverlight não estiverem instalados, a AMP irá selecionar a tecnologia nativa Html5 para reprodução.<br/><br/>As novas tecnologias de navegador estão a surgir diariamente e, como tal, podem afetar esta matriz.

## <a name="next-steps"></a>Passos seguintes ##

- [Azure Media Player Quickstart](azure-media-player-quickstart.md)