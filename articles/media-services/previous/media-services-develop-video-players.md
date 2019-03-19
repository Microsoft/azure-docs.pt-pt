---
title: Desenvolver aplicações de leitor de vídeo
description: O tópico fornece ligações para estruturas de Player e plug-ins que pode usar para desenvolver suas próprias aplicações de cliente que podem consumir multimédia de transmissão em fluxo dos serviços de multimédia.
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
ms.date: 02/09/2019
ms.author: juliako
ms.openlocfilehash: ba8d9fd54b8b29a3e58f170402a64f5899fec002
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57880692"
---
# <a name="develop-video-player-applications"></a>Desenvolver aplicações de leitor de vídeo
## <a name="overview"></a>Descrição geral
Os Media Services do Azure fornecem as ferramentas necessárias para criar aplicações avançadas e dinâmicas de leitor de cliente para a maioria das plataformas, incluindo: Dispositivos iOS dispositivos, Dispositivos Android, Windows, Windows Phone, Xbox e Caixas descodificadoras. Este tópico também fornece ligações para SDKs e estruturas do leitor que pode usar para desenvolver suas próprias aplicações de cliente que podem consumir multimédia de transmissão em fluxo dos serviços de multimédia do Azure.

>[!NOTE]
>Quando a sua conta AMS é criada, é adicionado um ponto final de transmissão em fluxo **predefinido** à sua conta no estado **Parado**. Para começar a transmitir o seu conteúdo em fluxo e a tirar partido do empacotamento e encriptação dinâmicos, o ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**. 
 
## <a name="azure-media-player"></a>Media Player do Azure
[O Azure Media Player](https://aka.ms/ampinfo) é um leitor de vídeo da web criado para reproduzir conteúdo multimédia dos serviços de multimédia do Microsoft Azure numa grande variedade de navegadores e dispositivos. O leitor de multimédia do Azure utiliza padrões da indústria, como HTML5, extensões de origem de mídia (MSE) e Encrypted Media Extensions (EME) para fornecer uma experiência plena de transmissão em fluxo adaptativa. Quando esses padrões não estão disponíveis num dispositivo ou num browser, o leitor de multimédia do Azure utiliza Flash e Silverlight como tecnologia de contingência. Independentemente da tecnologia de reprodução utilizada, os desenvolvedores terão uma interface JavaScript unificada para aceder a APIs. Isso permite a reprodução de conteúdo servido pelos serviços de multimédia do Azure a reprodução numa variedade de toda a de dispositivos e browsers sem qualquer esforço adicional.

Serviços de multimédia do Microsoft Azure permitem que o conteúdo ser atendidas com DASH, Smooth Streaming e HLS formatos de transmissão em fluxo para reproduzir conteúdo de volta. O Azure Media Player leva em conta esses vários formatos e executa automaticamente a ligação melhor, com base nos recursos de plataforma/browser. Serviços de multimédia do Microsoft Azure também permite que a encriptação dinâmica de recursos com encriptação PlayReady ou AES de 128 bits de encriptação de envelope. O leitor de multimédia do Azure permite-lhe PlayReady e AES de 128 bits conteúdo encriptado quando configurado corretamente. 

Para obter mais informações:

* [Media Player do Azure](https://aka.ms/ampinfo)
* [Documentação do leitor de multimédia do Azure](https://aka.ms/ampdocs) 
* [Blog a utilizar o leitor de multimédia do Azure, introdução](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/)
* [Inscreva-se para se manter atualizado com a versão mais recente do leitor de multimédia do Azure](https://aka.ms/ampsignup)
* [Adicionar novos pedidos de funcionalidade, idéias, comentários](https://aka.ms/ampuservoice) 

## <a name="other-tools-for-creating-player-applications"></a>Outras ferramentas para a criação de aplicações de leitor
Também pode utilizar qualquer um dos SDKs seguintes:

* [Do cliente Smooth Streaming SDK](https://www.iis.net/downloads/microsoft/smooth-streaming) 
* [Aplicação de Windows Store de transmissão em fluxo uniforme](media-services-build-smooth-streaming-apps.md)
* [Plataforma de multimédia do Microsoft: Arquitetura do leitor](https://playerframework.codeplex.com/) 
* [HTML5 Documentação Player Framework](https://playerframework.codeplex.com/wikipage?title=HTML5%20Player&referringTitle=Documentation) 
* [Smooth Streaming Plug-in para OSMF da Microsoft](https://www.microsoft.com/download/details.aspx?id=36057) 
* [Licenciamento Microsoft® Smooth Streaming cliente Kit de migração](https://aka.ms/sspk) 
* [Desenvolvimento de aplicativos de vídeo do XBOX](https://xbox.create.msdn.com/) 

## <a name="advertising"></a>Publicidade
Serviços de multimédia do Azure fornece suporte para inserção de publicidade através da plataforma de suporte de dados do Windows: Estruturas de Player. Estruturas de Player com suporte ad estão disponíveis para dispositivos Windows 8, Silverlight, Windows Phone 8 e iOS. Cada arquitetura do leitor contém o código de exemplo que mostra como implementar uma aplicação de leitor. Existem três tipos diferentes de anúncios, que pode inserir em seu suporte de dados:

Linear – anúncios de quadro completo que colocar em pausa o vídeo principal

Não lineares – anúncios de sobreposição, que são apresentados como está a reproduzir o vídeo principal, normalmente, um logótipo ou outra imagem estática colocado no leitor

Complementar – anúncios que são apresentados fora o jogador

Anúncios podem ser colocados em qualquer ponto na linha de tempo do vídeo principal. Informe o jogador quando reproduzir o ad e os anúncios para reproduzir. Isso é feito usando um conjunto de arquivos padrão baseada em XML: Anúncio de vídeo de serviço de modelo (VAST), de vídeo Digital várias listas de reprodução do Ad (VMAP), suporte de dados abstraem o modelo de sequenciamento (MAST) e a definição de Interface de Ad do leitor de vídeo Digital (VPAID). Os arquivos de grandes especificam quais anúncios para apresentar. Os arquivos VMAP especificam quando reproduzir vários anúncios e conter XML grande. Arquivos MAST são outra forma de anúncios de sequência que também pode conter XML grande. Ficheiros VPAID definem uma interface entre o player de vídeo e o ad ou o servidor do ad. Para obter mais informações, consulte [inserir anúncios](https://msdn.microsoft.com/library/dn387398.aspx).

Para obter informações sobre a legendagem de áudio e suporte de anúncios nos vídeos de transmissão em fluxo em direto, consulte [suportado fechadas as legendas de áudio e padrões de inserção de Ad](https://msdn.microsoft.com/library/c49e0b4d-357e-4cca-95e5-2288924d1ff3#caption_ad).

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Consultar Também
[Incorporar um Vídeo de Transmissão em fluxo Adaptivo MPEG-DASH numa Aplicação HTML5 com DASH.js](media-services-embed-mpeg-dash-in-html5.md)

[Repositório do GitHub dash. js](https://github.com/Dash-Industry-Forum/dash.js)

