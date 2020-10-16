---
title: Azure Media Services frequentemente fez perguntas
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
ms.openlocfilehash: ff15206e2373ce6481cb65cbbeeb3c88cd2b7154
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89269425"
---
# <a name="media-services-v2-frequently-asked-questions"></a>Serviços de Comunicação Social v2 frequentemente perguntas

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Este artigo aborda questões frequentemente colocadas pela comunidade de utilizadores da Azure Media Services (AMS).

## <a name="general-ams-faqs"></a>Perguntas gerais da AMS

P: Como é que transmite para dispositivos Apple iOS?

A: adicionar caminho "(formato=m3u8-aapl)" à parte "/Manifesto" do URL para dizer ao servidor de origem de streaming para devolver o conteúdo HLS para consumo em dispositivos nativos Apple iOS (para mais detalhes, ver [o conteúdo da entrega),](media-services-deliver-content-overview.md)

P: Como escalar a indexação?

R: As unidades reservadas são as mesmas para tarefas de codificação e indexação. Siga as instruções sobre [como escalar as unidades reservadas de codificação](media-services-scale-media-processing-overview.md). **Note** que o desempenho do Indexer não é afetado pelo Tipo de Unidade Reservada.

P: Fiz o upload, codificado e publiquei um vídeo. Qual seria a razão pela qual o vídeo não é reproduzo quando tento transmiti-lo?

R: Uma das razões mais comuns é que não tens o ponto final de streaming a partir do qual estás a tentar reproduzir o estado **de Corrida.**  

P: Posso fazer a composição num live stream?

R: A composição em streams ao vivo não é atualmente oferecida no Azure Media Services, pelo que teria de pré-compor no seu computador.

P: Posso usar a Azure CDN com live streaming?

R: Os Serviços de Media suportam a integração com a Azure CDN (para mais informações, ver [Como Gerir os Pontos Finais de Streaming numa Conta de Serviços de Mídia).](media-services-portal-manage-streaming-endpoints.md)  Pode utilizar o streaming ao vivo com CDN. A Azure Media Services fornece saídas Smooth Streaming, HLS e MPEG-DASH. Todos estes formatos utilizam HTTP para transferir dados e obter benefícios do caching HTTP. Em transmissão ao vivo, os dados reais de vídeo/áudio são divididos em fragmentos e estes fragmentos individuais ficam em cache na CDN. Apenas os dados precisam de ser atualizados são os dados manifestos. A CDN atualiza periodicamente os dados manifestos.

P: Os serviços da Azure Media suportam o armazenamento de imagens?

R: Se estiver apenas à procura de armazenar imagens JPEG ou PNG, deve guardá-las no Azure Blob Storage. Não há qualquer benefício em colocá-los na sua conta de Media Services a menos que queira mantê-los associados aos seus Ativos de Vídeo ou Áudio. Ou se tiver necessidade de usar as imagens como sobreposições no codificar de vídeo. A Media Encoder Standard suporta a sobreposição de imagens em cima de vídeos, e é isso que lista JPEG e PNG como formatos de entrada suportados. Para obter mais informações, consulte [Criar Sobreposições.](media-services-advanced-encoding-with-mes.md#overlay)

P: Como posso copiar os ativos de uma conta dos Serviços de Comunicação Social para outra?

R: Para copiar ativos de uma conta dos Serviços de Mídia para outra utilizando .NET, utilize o método de extensão [IAsset.Copy](https://github.com/Azure/azure-sdk-for-media-services-extensions/blob/dev/MediaServices.Client.Extensions/IAssetExtensions.cs#L354) disponível no repositório de [extensões Azure Media Services .NET SDK Extensions.](https://github.com/Azure/azure-sdk-for-media-services-extensions/) Para mais informações, consulte [este](https://social.msdn.microsoft.com/Forums/azure/28912d5d-6733-41c1-b27d-5d5dff2695ca/migrate-media-services-across-subscription?forum=MediaServices) fio do fórum.

P: Quais são os caracteres suportados para nomear ficheiros quando trabalham com a AMS?

R: Os Serviços de Mídia utilizam o valor da propriedade IAssetFile.Name ao construir URLs para o conteúdo de streaming (por exemplo, http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Por esta razão, não é permitido codificar por cento. O valor da propriedade **Name** não pode ter nenhum dos [seguintes caracteres reservados por cento:](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)!*'(;:@&=+$,/?%#]". Além disso, só pode haver um ''' para a extensão do nome do ficheiro.

P: Como ligar usando REST?

R: Para obter informações sobre como ligar-se à AMS API, consulte [Aceda à API dos Serviços de Media Azure com a autenticação AD Azure](media-services-use-aad-auth-to-access-ams-api.md). 

P: Como posso rodar um vídeo durante o processo de codificação?

R: A [Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) suporta a rotação por ângulos de 90/180/270. O comportamento predefinido é "Auto", onde tenta detetar os metadados de rotação no ficheiro MP4/MOV que chega e compensar o mesmo. Inclua o seguinte elemento **Fontes** a uma das predefinições json definidas [aqui:](media-services-mes-presets-overview.md)

```json
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
```


## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
