---
title: Desenvolver aplicações de leitor de vídeo
description: O tópico fornece links para Quadros de Jogadores e plugins que pode usar para desenvolver as suas próprias aplicações de clientes que podem consumir meios de streaming a partir de Media Services.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 55e419fc-4c39-4902-9c62-f41cfcd86c6c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 39459267919747ed49e9fa3f05746294eaf741dc
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906836"
---
# <a name="develop-video-player-applications"></a>Desenvolver aplicações de leitor de vídeo
## <a name="overview"></a>Visão geral
Os Media Services do Azure fornecem as ferramentas necessárias para criar aplicações avançadas e dinâmicas de leitor de cliente para a maioria das plataformas, incluindo: Dispositivos iOS dispositivos, Dispositivos Android, Windows, Windows Phone, Xbox e Caixas descodificadoras. Este tópico também fornece links para SDKs e Quadros de Jogadores que você pode usar para desenvolver as suas próprias aplicações de cliente que podem consumir meios de streaming a partir de Azure Media Services.

>[!NOTE]
>Quando a sua conta AMS é criada, é adicionado um ponto final de transmissão em fluxo **predefinido** à sua conta no estado **Parado**. Para começar a transmitir o seu conteúdo em fluxo e a tirar partido do empacotamento e encriptação dinâmicos, o ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**. 
 
## <a name="azure-media-player"></a>Leitor de Multimédia do Azure
[O Azure Media Player](https://aka.ms/ampinfo) é um leitor de vídeo web construído para reproduzir conteúdos de media da Microsoft Azure Media Services numa grande variedade de navegadores e dispositivos. O Azure Media Player utiliza padrões industriais, tais como HTML5, Extensões de Fonte de Media (MSE) e Extensões de Meios Encriptados (EME) para proporcionar uma experiência de streaming adaptável enriquecida. Quando estes padrões não estão disponíveis num dispositivo ou num browser, o Azure Media Player utiliza flash e Silverlight como tecnologia de recuo. Independentemente da tecnologia de reprodução utilizada, os desenvolvedores terão uma interface JavaScript unificada para aceder a APIs. Isto permite que os conteúdos servidos pela Azure Media Services sejam reproduzidos através de uma vasta gama de dispositivos e navegadores sem qualquer esforço extra.

O Microsoft Azure Media Services permite que os conteúdos sejam servidos com formatos de streaming DASH, Smooth Streaming e HLS para reproduzir conteúdos. O Azure Media Player tem em conta estes vários formatos e reproduz automaticamente o melhor link com base nas capacidades da plataforma/navegador. O Microsoft Azure Media Services também permite encriptação dinâmica de ativos com encriptação PlayReady ou encriptação de envelopes aES-128. O Azure Media Player permite a desencriptação do conteúdo encriptado da BitReady e AES-128 quando devidamente configurado. 

Para mais informações:

* [Media Player do Azure](https://aka.ms/ampinfo)
* [Documentação do jogador azure media](https://aka.ms/ampdocs) 
* [Azure Media Player começando blog](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/)
* [Inscreva-se para se manter atualizado com as últimas novidades do Azure Media Player](https://aka.ms/ampsignup)
* [Adicione novos pedidos de funcionalidades, ideias, feedback](https://aka.ms/ampuservoice) 

## <a name="other-tools-for-creating-player-applications"></a>Outras ferramentas para criar aplicações de jogadores
Também pode utilizar qualquer um dos seguintes SDKs:

* [Cliente de streaming suave SDK](https://www.iis.net/downloads/microsoft/smooth-streaming) 
* [App de loja de janelas de streaming suave](media-services-build-smooth-streaming-apps.md)
* [Microsoft Media Platform: Player Framework](https://playerframework.codeplex.com/) 
* [Documentação-quadro do jogador HTML5](https://playerframework.codeplex.com/wikipage?title=HTML5%20Player&referringTitle=Documentation) 
* [Plugin de streaming suave da Microsoft para OSMF](https://www.microsoft.com/download/details.aspx?id=36057) 
* [Licenciamento Microsoft® Smooth Streaming Cliente Porting Kit](https://aka.ms/sspk) 
* [Desenvolvimento de aplicações de vídeo XBOX](https://www.xbox.com/en-US/developers) 

## <a name="advertising"></a>Publicidade
A Azure Media Services fornece suporte para a inserção de anúncios através da Plataforma Windows Media: Quadros de Jogadores. As estruturas dos jogadores com suporte a anúncios estão disponíveis para dispositivos Windows 8, Silverlight, Windows Phone 8 e iOS. Cada quadro do jogador contém um código de amostra que lhe mostra como implementar uma aplicação do jogador. Existem três tipos diferentes de anúncios que pode inserir nos seus meios de comunicação:

Linear – anúncios de moldura completa que pausam o vídeo principal

Anúncios de sobreposição que são exibidos à medida que o vídeo principal está a ser reproduzido, geralmente um logotipo ou outra imagem estática colocada dentro do leitor

Companheiro – anúncios que são exibidos fora do jogador

Os anúncios podem ser colocados em qualquer ponto da linha temporal do vídeo principal. Tens de dizer ao jogador quando jogar o anúncio e quais anúncios para jogar. Isto é feito usando um conjunto de ficheiros padrão baseados em XML: Modelo de serviço de anúncio de vídeo (VAST), Lista de anúncios de vídeo digital (VMAP), Modelo de Sequenciação Abstrata dos Media (MAST) e Definição de Interface de Anúncio de Vídeo Digital (VPAID). Os ficheiros VAST especificam quais anúncios a exibir. Os ficheiros VMAP especificam quando reproduzir vários anúncios e contêm VAST XML. Os ficheiros MAST são outra forma de sequenciar anúncios que também podem conter VAST XML. Os ficheiros VPAID definem uma interface entre o leitor de vídeo e o servidor de anúncios ou anúncios. Para mais informações, consulte [ADS Inserindo.](https://msdn.microsoft.com/library/dn387398.aspx)

Para obter informações sobre legendagem fechada e suporte de anúncios em vídeos de streaming ao vivo, consulte As Normas de [Legendagem Fechada e Desserção de Anúncios suportadas.](https://msdn.microsoft.com/library/c49e0b4d-357e-4cca-95e5-2288924d1ff3#caption_ad)

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Veja também
[Incorporar um Vídeo de Transmissão em fluxo Adaptivo MPEG-DASH numa Aplicação HTML5 com DASH.js](media-services-embed-mpeg-dash-in-html5.md)

[Repositório GitHub dash.js](https://github.com/Dash-Industry-Forum/dash.js)

