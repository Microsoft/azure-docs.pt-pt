---
title: Tecnologia de reprodução de jogador estoma do Azure Media Player
description: Saiba mais sobre a tecnologia de reprodução usada para reproduzir o vídeo ou áudio.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 85eaa04836774b838da67e073017f4af3d2fe179
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81726488"
---
# <a name="playback-technology-tech"></a>Tecnologia de reprodução ("tech") #

A Playback Technology refere-se à tecnologia específica de navegador ou plugin usada para reproduzir o vídeo ou áudio.

- **azureHtml5JS**: utiliza as normas MSE e EME em conjunto com o elemento de vídeo para reprodução baseada sem plugin de conteúdo DASH com suporte para conteúdo encriptado de envelope aES-128 bit ou conteúdo encriptado comum DRM (via PlayReady e Widevine quando o navegador o suporta) a partir da Azure Media Services
- **flashSS**: utiliza tecnologia flash player para reproduzir conteúdo suave com suporte para desencriptação do envelope aES-128 bit da Azure Media Services - requer versão Flash de 11.4 ou superior
- **html5FairPlayHLS**: utiliza o Safari específico na tecnologia de reprodução baseada no navegador através do HLS com o elemento de vídeo. Esta tecnologia é necessária para reproduzir conteúdo protegido fairPlay da Azure Media Services e foi adicionada à techOrder a partir de 10/19/16
- **silverlightSS**: utiliza tecnologia silverlight para reproduzir conteúdo suave com suporte para conteúdo protegido PlayReady da Azure Media Services.
- **html5**: utiliza na tecnologia de reprodução baseada no navegador com o elemento de vídeo.  Quando num dispositivo Apple iOS ou Android, esta tecnologia permite a reprodução de streams hls com algum suporte básico para encriptação de envelopes aes-128 bits ou conteúdo DRM (via FairPlay quando o navegador o suporta).

## <a name="tech-order"></a>Ordem Técnica ##

De forma a garantir que o `techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"]` `<video>` seu ativo é jogável numa grande variedade de dispositivos, recomenda-se a seguinte ordem tecnológica e é o padrão se: e pode ser definido diretamente nas opções ou programaticamente:

`<video data-setup='{"techOrder": ["azureHtml5JS", "flashSS", "html5FairPlayHLS, "silverlightSS", "html5"]}`

ou

```javascript
    amp("vid1", {
          techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS, "silverlightSS", "html5"]
    });
```

## <a name="compatibility-matrix"></a>Matriz de compatibilidade ##

Dada a ordem tecnológica recomendada com conteúdo de streaming da Azure Media Services, espera-se a seguinte matriz de reprodução de compatibilidade

| Browser        | SO                                                       | Tecnologia Esperada (Clara)  | Tecnologia esperada (AES)  | Tecnologia Esperada (DRM)          |
|----------------|----------------------------------------------------------|------------------------|----------------------|------------------------------|
| Edgeie 11      | Windows 10, Windows 8.1, Windows Phone 101               | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (PlayReady)     |
| IE 11iE 9-101  | Windows 7, Windows Vista<sup>1</sup>                     | flashss                | flashss              | silverlightSS (PlayReady)    |
| IE 11          | Windows Phone 8.1                                        | azureHtml5JS           | azureHtml5JS         | não apoiado                |
| Microsoft Edge           | Xbox One<sup>1</sup> (atualização de nov 2015)                   | azureHtml5JS           | azureHtml5JS         | não apoiado                |
| Chrome 37+     | Windows 10, Windows 8.1, macOS X Yosemite<sup>1</sup>   | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Firefox 47+    | Windows 10, Windows 8.1, macOS X Yosemite+<sup>1</sup>  | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Firefox 42-46  | Windows 10, Windows 8.1, macOS X Yosemite+<sup>1</sup>  | azureHtml5JS           | azureHtml5JS         | silverlightSS (PlayReady)    |
| Firefox 35-41  | Windows 10, Windows 8.1                                  | flashss                | flashss              | silverlightSS (PlayReady)    |
| Safari         | iOS 6+                                                   | html5                  | html5 (sem token)3    | não apoiado                |
| Safari 8+      | OS X Yosemite+                                           | azureHtml5JS           | azureHtml5JS         | html5FairPlayHLS (FairPlay)  |
| Safari 6       | LEÕES X Leão da Montanha<sup>1</sup>                           | flashss                | flashss              | silverlightSS (PlayReady)    |
| Chrome 37+     | Android 4.4.4+<sup>2</sup>                               | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Chrome 37+     | Android 4.02                                             | html5                  | html5 (sem token)<sup>3</sup>    | não apoiado                |
| Firefox 42+    | Android 5.0+<sup>2</sup>                                 | azureHtml5JS           | azureHtml5JS         | não apoiado                |
| IE 8           | Windows                                                  | não apoiado          | não apoiado        | não apoiado                |

<sup>1</sup> Configuração não suportada ou testada; listado como referência para conclusão.

<sup>2</sup> A reprodução bem sucedida em dispositivos Android requer uma combinação de capacidades de dispositivo, suporte gráfico, renderização codec, suporte a OS e muito mais. Uma vez que o Android é uma plataforma de código aberto que permite aos fabricantes de telefones alterar o SISTEMA Android de Baunilha fornecido pela Google, isso causa alguma fragmentação no espaço Android, e alguns dispositivos podem não ser suportados por falta de funcionalidades. Além disso, alguns dispositivos Android não têm suporte para todos os códigos.  

<sup>3</sup> Nos casos em que não há suporte para ficha, um representante pode ser usado para adicionar esta funcionalidade. Confira este [blog](https://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/) para saber mais sobre esta solução.

> [!NOTE]
> Se a tecnologia esperada escolhida necessitar de um plugin, como o Flash, e que não esteja instalada na máquina do utilizador, a AMP continuará a verificar as capacidades da próxima tecnologia, em conjunto com os tipos de origem e informações de proteção, na lista de tecnologia. Por exemplo, se tentar ver um fluxo desprotegido a pedido no Safari 8 no OS X Yosemite, e tanto flash como Silverlight não estão instalados, a AMP selecionará a tecnologia nativa Html5 para reprodução.<br/><br/>As novas tecnologias do navegador estão a surgir diariamente, e como tal podem afetar esta matriz.

## <a name="next-steps"></a>Passos seguintes ##

- [Azure Media Player Quickstart](azure-media-player-quickstart.md)