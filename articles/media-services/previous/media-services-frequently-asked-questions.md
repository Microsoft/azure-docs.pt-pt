---
title: Perguntas frequentes sobre os serviços de mídia do Azure | Microsoft Docs
description: Este artigo fornece respostas para as perguntas frequentes sobre os serviços de mídia do Azure.
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
ms.openlocfilehash: 3aeff680392fbe966682b57ca1318fac9f0d1d93
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895982"
---
# <a name="frequently-asked-questions"></a>Perguntas mais frequentes

Este artigo aborda as perguntas frequentes levantadas pela comunidade de usuários dos serviços de mídia do Azure (AMS).

## <a name="general-ams-faqs"></a>Perguntas frequentes gerais do AMS

P: como transmitir para dispositivos iOS da Apple?

R: Adicione o caminho "(Format = M3U8-AAPL)" à parte "/manifest" da URL para informar ao servidor de origem de streaming para retornar o conteúdo do HLS para consumo em dispositivos Apple iOS nativos (para obter detalhes, consulte [entregando conteúdo](media-services-deliver-content-overview.md)),

P: como dimensionar a indexação?

R: as unidades reservadas são as mesmas para tarefas de codificação e indexação. Siga as instruções sobre [como dimensionar unidades reservadas para codificação](media-services-scale-media-processing-overview.md). **Observe** que o desempenho do indexador não é afetado pelo tipo de unidade reservada.

P: Eu carreguei, codificai e publiquei um vídeo. Qual seria o motivo pelo qual o vídeo não é reproduzido quando tento transmiti-lo?

R: um dos motivos mais comuns é que você não tem o ponto de extremidade de streaming do qual está tentando reproduzir no estado de **execução** .  

P: posso fazer a composição em uma transmissão ao vivo?

R: A composição em transmissões ao vivo atualmente não é oferecida nos serviços de mídia do Azure, portanto, você precisaria compor previamente em seu computador.

P: posso usar a CDN do Azure com a transmissão ao vivo?

R: os serviços de mídia dão suporte à integração com a CDN do Azure (para obter mais informações, consulte [como gerenciar pontos de extremidade de streaming em uma conta dos serviços de mídia](media-services-portal-manage-streaming-endpoints.md)).  Você pode usar a transmissão ao vivo com a CDN. Os serviços de mídia do Azure fornecem saídas Smooth Streaming, HLS e MPEG-DASH. Todos esses formatos usam HTTP para transferir dados e obter benefícios do cache HTTP. No Live streaming, os dados reais de vídeo/áudio são divididos em fragmentos e esses fragmentos individuais são armazenados em cache na CDN. Somente os dados que precisam ser atualizados são os dados de manifesto. A CDN atualiza periodicamente os dados de manifesto.

P: os serviços de mídia do Azure dão suporte ao armazenamento de imagens?

R: se você estiver apenas procurando armazenar imagens JPEG ou PNG, deverá mantê-las no armazenamento de BLOBs do Azure. Não há nenhum benefício em colocá-los em sua conta de serviços de mídia, a menos que você queira mantê-los associados aos seus ativos de áudio ou vídeo. Ou, se você tiver a necessidade de usar as imagens como sobreposições no codificador de vídeo. O Media Encoder Standard dá suporte à sobreposição de imagens sobre vídeos, e é isso que lista JPEG e PNG como formatos de entrada com suporte. Para obter mais informações, consulte [criando sobreposições](media-services-advanced-encoding-with-mes.md#overlay).

P: como posso copiar ativos de uma conta dos serviços de mídia para outra?

R: para copiar ativos de uma conta dos serviços de mídia para outra usando o .NET, use o método de extensão [IAsset. Copy](https://github.com/Azure/azure-sdk-for-media-services-extensions/blob/dev/MediaServices.Client.Extensions/IAssetExtensions.cs#L354) disponível no repositório de [extensões do SDK do .net dos serviços de mídia do Azure](https://github.com/Azure/azure-sdk-for-media-services-extensions/) . Para obter mais informações, consulte [este](https://social.msdn.microsoft.com/Forums/azure/28912d5d-6733-41c1-b27d-5d5dff2695ca/migrate-media-services-across-subscription?forum=MediaServices) thread de fórum.

P: quais são os caracteres com suporte para nomear arquivos ao trabalhar com o AMS?

R: os serviços de mídia usam o valor da propriedade IAssetFile.Name ao compilar URLs para o conteúdo de streaming (por exemplo, http://{AMSAccount}. Origin. mediaservices. Windows. net/{GUID}/{IAssetFile. Name}/streamingparameters.) Por esse motivo, a codificação por porcentagem não é permitida. O valor da propriedade **Name** não pode ter nenhum dos seguintes [caracteres reservados para codificação de porcentagem](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters):! * ' ();: @ & = + $,/?% # [] ". Além disso, só pode haver um '. ' para a extensão de nome de arquivo.

P: como se conectar usando REST?

R: para obter informações sobre como se conectar à API do AMS, consulte [acessar a API dos serviços de mídia do Azure com a autenticação do Azure ad](media-services-use-aad-auth-to-access-ams-api.md). 

P: como posso girar um vídeo durante o processo de codificação?

R: o [Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) dá suporte à rotação por ângulos de 90/180/270. O comportamento padrão é "auto", em que ele tenta detectar os metadados de rotação no arquivo MP4/MOV de entrada e compensar isso. Inclua o seguinte elemento **sources** em uma das predefinições JSON definidas [aqui](media-services-mes-presets-overview.md):

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
