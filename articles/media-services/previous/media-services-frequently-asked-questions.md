---
title: Serviços de Mídia Azure frequentemente fazem perguntas
description: Este artigo dá respostas às perguntas frequentes sobre a Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: bdb5a43ad6669bfcd6e93ef4e3bf1646314e4606
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705875"
---
# <a name="media-services-v2-frequently-asked-questions"></a>Serviços de Media v2 frequentemente questionados

Este artigo aborda frequentemente questões levantadas pela comunidade de utilizadores da Azure Media Services (AMS).

## <a name="general-ams-faqs"></a>FaQs gerais da AMS

P: Como é que transmite para dispositivos apple iOS?

A: adicione o caminho "(formato=m3u8-aapl)" para a parte "/Manifesto" do URL para dizer ao servidor de origem streaming para devolver conteúdo hls de volta para consumo em dispositivos nativos apple iOS (para detalhes, ver [conteúdo de entrega),](media-services-deliver-content-overview.md)

P: Como escala a indexação?

R: As unidades reservadas são as mesmas para tarefas de codificação e indexação. Siga as instruções sobre [como escalar as unidades reservadas](media-services-scale-media-processing-overview.md)de codificação . **Note** que o desempenho do Indexer não é afetado pelo Tipo de Unidade Reservado.

P: Carreguei, codificado e publiquei um vídeo. Qual seria a razão pela qual o vídeo não toca quando tento transmiti-lo?

R: Uma das razões mais comuns é que não tens o ponto final de streaming a partir do qual estás a tentar jogar no estado **de Running.**  

P: Posso fazer compositing em um live stream?

R: A composição em streams ao vivo não é atualmente oferecida no Azure Media Services, pelo que teria de se pré-compor no seu computador.

P: Posso usar O CDN Azure com Live Streaming?

R: Media Services suporta integração com o Azure CDN (para mais informações, consulte [Como Gerir os Pontos Finais](media-services-portal-manage-streaming-endpoints.md)de Streaming numa Conta de Serviços de Media).  Pode utilizar o live streaming com o CDN. A Azure Media Services fornece saídas smooth streaming, HLS e MPEG-DASH. Todos estes formatos utilizam http para transferir dados e obter benefícios de cache http. Em streaming ao vivo os dados reais de vídeo/áudio são divididos em fragmentos e estes fragmentos individuais ficam em cache em CDN. Só os dados precisam de ser atualizados são os dados manifestos. A CDN atualiza periodicamente os dados manifestos.

P: Os serviços da Azure Media suportam armazenar imagens?

R: Se você está apenas procurando armazenar imagens JPEG ou PNG, você deve mantê-las em Armazenamento Azure Blob. Não há nenhum benefício em colocá-los na sua conta de Serviços de Media a menos que queira mantê-los associados aos seus Ativos de Vídeo ou Áudio. Ou se tiver a necessidade de usar as imagens como sobreposições no codificador de vídeo. Media Encoder Standard suporta sobrepor imagens em cima de vídeos, e é isso que ele lista JPEG e PNG como formatos de entrada suportados. Para mais informações, consulte [Creating Overlays](media-services-advanced-encoding-with-mes.md#overlay).

P: Como posso copiar ativos de uma conta de Serviços de Media para outra?

R: Para copiar ativos de uma conta de Media Services para outra utilizando .NET, utilize o método de extensão [IAsset.Copy](https://github.com/Azure/azure-sdk-for-media-services-extensions/blob/dev/MediaServices.Client.Extensions/IAssetExtensions.cs#L354) disponível no repositório de extensões de [extensões Azure Media Services .NET SDK.](https://github.com/Azure/azure-sdk-for-media-services-extensions/) Para mais informações, consulte [este](https://social.msdn.microsoft.com/Forums/azure/28912d5d-6733-41c1-b27d-5d5dff2695ca/migrate-media-services-across-subscription?forum=MediaServices) fio do fórum.

P: Quais são os caracteres suportados para nomear ficheiros ao trabalhar com a AMS?

R: Os Serviços de Media utilizam o valor da propriedade IAssetFile.Name ao construir URLs para o conteúdo de streaming (por exemplo, http://AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParâmetros.) Por esta razão, não é permitida a codificação por cento. O valor da propriedade **Name** não pode ter nenhum dos seguintes [caracteres reservados](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)por cento de codificação : !*'();@&=+$,/?%#[]". Além disso, só pode haver um '.' para a extensão do nome do ficheiro.

P: Como ligar-se usando o REST?

R: Para obter informações sobre como ligar à AMS API, consulte [Aceda à API dos Serviços de Comunicação Social Azure com autenticação Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

P: Como posso rodar um vídeo durante o processo de codificação?

R: A [Norma Media Encoder](media-services-dotnet-encode-with-media-encoder-standard.md) suporta a rotação por ângulos de 90/180/270. O comportamento predefinido é "Auto", onde tenta detetar os metadados de rotação no ficheiro MP4/MOV que se aproxima e compensar. Incluir o seguinte elemento **Fontes** a uma das predefinições json definidas [aqui:](media-services-mes-presets-overview.md)

    "Version": 1.0,
    "Sources": [
    {
      "Streams": [],
      "Filters": {
        "Rotation": "90"
      }
    }
    ],
    "Codecs": [

    ...


## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
