---
title: Utilize a mudança de tempo para criar reprodução de vídeo a pedido
description: Este artigo descreve como usar a mudança de tempo e as saídas ao vivo para gravar Live Streams e criar reprodução a pedido.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: a2bb876c164f0df56a8b7f3c4a3666ff306e9416
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98955941"
---
# <a name="use-time-shifting-and-live-outputs-to-create-on-demand-video-playback"></a>Utilize a mudança de tempo e as saídas ao vivo para criar reprodução de vídeo a pedido

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

No Azure Media Services, um objeto [live output](/rest/api/media/liveoutputs) é como um gravador de vídeo digital que irá capturar e gravar o seu live stream num ativo na sua conta de Media Services. O conteúdo registado é persistido no contentor definido pelo recurso [Ativo](/rest/api/media/assets) (o contentor está na conta Azure Storage anexada à sua conta). A Saída Ao Vivo também permite controlar algumas propriedades do live stream de saída, como a quantidade de fluxo que é mantida na gravação de arquivo (por exemplo, a capacidade do DVR em nuvem) ou quando os espectadores podem começar a ver o live stream. O arquivo no disco é uma "janela" de arquivo circular que contém apenas a quantidade de conteúdo especificado no **arquivoWindowLength** propriedade da Saída Ao Vivo. O conteúdo que cai fora desta janela é automaticamente descartado do recipiente de armazenamento e não é recuperável. O valor de comprimento de arquivoWindowLength representa uma duração de períodos ISO-8601 (por exemplo, PTHH:MM:SS), que especifica a capacidade do DVR. O valor pode ser definido de um mínimo de um minuto para um máximo de 25 horas.

A relação entre um Live Event e as suas Saídas Ao Vivo é semelhante à emissão televisiva tradicional, na medida em que um canal (Live Event) representa um fluxo constante de vídeo e uma gravação (Live Output) é traçada para um segmento de tempo específico (por exemplo, notícias noturnas das 18h30 às 19h00). Assim que tiver o stream fluindo para o Live Event, pode iniciar o evento de streaming criando um ativo, Live Output e localizador de streaming. A Live Output irá arquivar o fluxo e disponibilizá-lo aos espectadores através do [Streaming Endpoint.](/rest/api/media/streamingendpoints) Pode criar várias Saídas ao Vivo (até três no máximo) num Evento Ao Vivo com diferentes comprimentos e configurações de arquivo. Para obter informações sobre o fluxo de trabalho em streaming ao vivo, consulte a secção [de passos gerais.](live-streaming-overview.md#general-steps)

## <a name="using-a-dvr-during-an-event"></a>Usando um DVR durante um evento

Esta secção discute como usar um DVR durante um evento para controlar que partes do fluxo está disponível para 'rebobinar'.

O `archiveWindowLength` valor determina até onde um espectador pode ir da posição ao vivo atual. O `archiveWindowLength` valor também determina quanto tempo os manifestos do cliente podem crescer.

Suponha que está transmitindo um jogo de futebol, e tem `ArchiveWindowLength` apenas 30 minutos. Um espectador que comece a ver o seu evento 45 minutos após o início do jogo pode procurar de volta para a marca de 15 minutos. As suas saídas ao vivo para o jogo continuarão até que o Live Event seja interrompido. O conteúdo que cai fora do arquivoWindowLength é continuamente descartado do armazenamento e não é recuperável. Neste exemplo, o vídeo entre o início do evento e a marca de 15 minutos teria sido purgado do seu DVR e do contentor em armazenamento de bolhas para o ativo. O arquivo não é recuperável e é removido do recipiente no armazenamento da bolha Azure.

Um Evento Ao Vivo suporta até três saídas ao vivo em simultâneo (pode criar no máximo 3 gravações/arquivos a partir de uma transmissão ao vivo ao mesmo tempo). Este suporte permite-lhe publicar e arquivar diferentes partes de um evento conforme necessário. Suponha que você precisa transmitir um feed linear 24x7, e criar "gravações" dos diferentes programas ao longo do dia para oferecer aos clientes como conteúdo on-demand para visualização de catch-up. Para este cenário, cria-se primeiro uma saída primária ao vivo com uma pequena janela de arquivo de 1 hora ou menos - este é o principal live stream em que os seus espectadores iriam sintonizar. Criaria um Localizador de Streaming para esta Saída Ao Vivo e publicá-lo-ia na sua aplicação ou site como o feed "Live". Enquanto o Live Event está em execução, pode criar programáticamente uma segunda saída ao vivo simultânea no início de um programa (ou 5 minutos mais cedo para fornecer algumas pegas para aparar mais tarde). Esta segunda saída ao vivo pode ser eliminada 5 minutos após o fim do programa. Com este segundo ativo, pode criar um novo Localizador de Streaming para publicar este programa como um ativo a pedido no catálogo da sua aplicação. Pode repetir este processo várias vezes para outros limites do programa ou realça que deseja partilhar como vídeos a pedido, enquanto o feed "Live" da primeira Saída Ao Vivo continua a transmitir o feed linear.

## <a name="creating-an-archive-for-on-demand-playback"></a>Criação de um arquivo para reprodução a pedido

O ativo a que a Saída Ao Vivo está a arquivar automaticamente torna-se um ativo a pedido quando a Saída Ao Vivo é eliminada. Tem de eliminar todas as saídas ao vivo antes de um Evento Ao Vivo poder ser interrompido. Pode utilizar uma proteção de bandeira [opcionalOutputsOnStop](/rest/api/media/liveevents/stop#request-body) para remover automaticamente as saídas ao vivo no ponto de paragem.

Mesmo depois de parar e apagar o evento, os utilizadores podem transmitir o seu conteúdo arquivado como um vídeo a pedido, desde que não apague o ativo. Um ativo não deve ser eliminado se for usado por um evento; o evento deve ser apagado primeiro.

Se tiver publicado o ativo da sua Saída Ao Vivo utilizando um localizador de streaming, o Live Event (até ao comprimento da janela DVR) continuará a ser visível até ao termo ou supressão do localizador de streaming, o que vier primeiro.

Para obter mais informações, consulte:

- [Visão geral do streaming ao vivo](live-streaming-overview.md)
- [Tutorial de streaming ao vivo](stream-live-tutorial-with-api.md)

> [!NOTE]
> Quando elimina a Saída Ao Vivo, não está a excluir o ativo e o conteúdo subjacentes no ativo.

## <a name="next-steps"></a>Passos seguintes

* [Subclip seus vídeos](subclip-video-rest-howto.md).
* [Defina filtros para os seus ativos.](filters-dynamic-manifest-rest-howto.md)
