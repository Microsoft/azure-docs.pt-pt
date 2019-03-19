---
title: Serviços de multimédia do Azure perguntas mais frequentes | Documentos da Microsoft
description: Perguntas mais frequentes (FAQ)
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
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 487fed72af6a259d4eda08c6700d95b376b64537
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57855168"
---
# <a name="frequently-asked-questions"></a>Perguntas mais frequentes

Este artigo aborda perguntas mais frequentes sobre gerado pela Comunidade de utilizador do Azure Media Services (AMS).

## <a name="general-ams-faqs"></a>FAQs de AMS geral

P: Como transmitir para dispositivos iOS da Apple

R: adicione "(formato Format=m3u8-aapl)" caminho para a parte "/ manifesto" do URL para indicar ao servidor de origem de transmissão em fluxo para retornar o back-conteúdo HLS para consumo no Apple iOS dispositivos nativos (para obter detalhes, consulte [entrega de conteúdos](media-services-deliver-content-overview.md)),

P: Como dimensionar indexação?

R: As unidades reservadas são os mesmos para tarefas de codificação e a indexação. Siga as instruções [como dimensionamento unidades de Encoding reservadas](media-services-scale-media-processing-overview.md). **Tenha em atenção** que o desempenho de indexador não é afetado por tipo de unidade reservada.

P: Eu carregou, codificou e publicado um vídeo. O que seria o motivo pelo qual o vídeo não sejam reproduzidos quando tento transmiti-lo em?

R: Uma das razões mais comuns é que não tiver o partir do qual está a tentar reproduzir ponto final de transmissão a **em execução** estado.  

P: Pode fazer a composição numa transmissão em direto?

R: Composição em transmissões em fluxo atualmente não é oferecida em serviços de multimédia do Azure, para que precisaria para compor previamente no seu computador.

P: Posso utilizar a CDN do Azure com a transmissão em direto?

R: Os Media Services suportam a integração com o CDN do Azure (para obter mais informações, consulte [como gerir transmissão em fluxo pontos finais na conta de Media Services](media-services-portal-manage-streaming-endpoints.md)).  Pode usar em direto de transmissão em fluxo com a CDN. Serviços de multimédia do Azure fornece saídas de Streaming, HLS e MPEG-DASH uniforme. Todos esses formatos utilizam HTTP para a transferência de dados e obtém benefícios de colocação em cache de HTTP. Na transmissão em direto real de dados de áudio/vídeo são divididos para fragmentos e este fragmentos individuais obterem armazenada em cache na CDN. Apenas as necessidades de dados sejam atualizados são os dados de manifestos. CDN atualiza periodicamente os dados de manifestos.

P: Serviços de multimédia do Azure suporta o armazenamento de imagens?

R: Se pretender apenas para armazenar imagens JPEG ou PNG, deve manter no armazenamento de Blobs do Azure. Não existe qualquer benefício para colocá-las na sua conta de Media Services, a menos que queira mantê-los associados com o seu vídeo ou áudio ativos. Ou, se pode ter uma necessidade de usá-las como sobreposições no codificador vídeo. Codificador de multimédia Standard suporta imagens de sobreposição por cima de vídeos e que é o que esta lista JPEG e PNG como suportadas formatos de entrada. Para obter mais informações, consulte [sobreposições de criação de](media-services-advanced-encoding-with-mes.md#overlay).

P: Como copiar recursos de uma conta de serviços de multimédia para outro?

R: Para copiar os elementos de uma conta de serviços de multimédia para outro usando o .NET, utilize [IAsset.Copy](https://github.com/Azure/azure-sdk-for-media-services-extensions/blob/dev/MediaServices.Client.Extensions/IAssetExtensions.cs#L354) método de extensão disponível na [extensões do SDK de .NET do Azure Media Services](https://github.com/Azure/azure-sdk-for-media-services-extensions/) repositório. Para obter mais informações, consulte [isso](https://social.msdn.microsoft.com/Forums/azure/28912d5d-6733-41c1-b27d-5d5dff2695ca/migrate-media-services-across-subscription?forum=MediaServices) thread de Fórum.

P: Quais são os carateres suportados à nomenclatura dos ficheiros ao trabalhar com o AMS?

R: Serviços de multimédia utiliza o valor da propriedade IAssetFile.Name ao criar os URLs para o conteúdo de transmissão em fluxo (por exemplo, http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Por esse motivo, por cento de codificação não é permitida. O valor do **Name** propriedade não pode ter qualquer um dos seguintes [carateres por cento de codificação-reservados](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters):! *' ();: @& = + $, /? [] # ". Além disso, só pode existir um '.' para a extensão de nome de ficheiro.

P: Como ligar com o REST?

R: Para obter informações sobre como ligar à AMS API, consulte [aceder a API de serviços de multimédia do Azure com a autenticação do Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

P: Como pode girar um vídeo durante o processo de codificação?

R: O [Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) suporta a rotação por ângulos de 90/180/270. O comportamento padrão é "Auto", onde ele tenta detetar os metadados de rotação no ficheiro de entrada MP4/MOV e compensar para o mesmo. Seguem **origens** elemento a uma das configurações predefinidas de json definidas [aqui](media-services-mes-presets-overview.md):

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
