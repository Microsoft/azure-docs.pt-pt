---
title: Use as saídas de mudança de tempo e as saídas ao vivo para criar reprodução de vídeo a pedido
titleSuffix: Azure Media Services
description: Este artigo descreve como usar as saídas de tempo e saídas ao vivo para gravar Live Streams e criar reprodução a pedido.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 08/27/2019
ms.author: juliako
ms.openlocfilehash: 4c7618b60e5fd86a9b8b3f22fb3333c00cfdfa61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74899787"
---
# <a name="use-time-shifting-and-live-outputs-to-create-on-demand-video-playback"></a>Use as saídas de mudança de tempo e as saídas ao vivo para criar reprodução de vídeo a pedido

No Azure Media Services, um objeto [de Saída Ao Vivo](https://docs.microsoft.com/rest/api/media/liveoutputs) é como um gravador de vídeo digital que vai capturar e gravar o seu live stream em um ativo na sua conta media Services. O conteúdo registado é permanecido no recipiente definido pelo recurso [Ativo](https://docs.microsoft.com/rest/api/media/assets) (o recipiente está na conta De armazenamento Azure anexada à sua conta). A Saída ao Vivo também permite controlar algumas propriedades do live stream de saída, como quanto do fluxo é mantido na gravação de arquivo (por exemplo, a capacidade do DVR em nuvem) ou quando os espectadores podem começar a ver o live stream. O arquivo no disco é uma "janela" de arquivo circular que apenas contém a quantidade de conteúdo especificada na propriedade **archiveWindowLength** da Saída Ao Vivo. O conteúdo que cai fora desta janela é automaticamente descartado do recipiente de armazenamento e não é recuperável. O valor archiveWindowLength representa uma duração da pádeia ISO-8601 (por exemplo, PTHH:MM:SS), que especifica a capacidade do DVR. O valor pode ser definido de um mínimo de três minutos para um máximo de 25 horas.

A relação entre um Live Event e as suas Saídas ao Vivo é semelhante à transmissão tradicional de TV, na medida em que um canal (Live Event) representa um fluxo constante de vídeo e uma gravação (Live Output) é amplamente difundida para um segmento de tempo específico (por exemplo, notícias noturnas de 18h30 às 19h00). Assim que tiver o fluxo fluindo para o Live Event, pode iniciar o evento de streaming criando um ativo, Live Output e localizador de streaming. A Live Output irá arquivar o stream e disponibilizá-lo aos espectadores através do [Streaming Endpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints). Pode criar várias Saídas ao Vivo (até três no máximo) num Evento Ao Vivo com diferentes comprimentos e configurações de arquivo. Para obter informações sobre o fluxo de trabalho em streaming ao vivo, consulte a secção [de passos gerais.](live-streaming-overview.md#general-steps)

## <a name="using-a-dvr-during-an-event"></a>Usando um DVR durante um evento

Esta secção discute como usar um DVR durante um evento para controlar que partes do fluxo estão disponíveis para 'rebobinar'.

O `archiveWindowLength` valor determina até onde um espectador pode ir da atual posição ao vivo. O `archiveWindowLength` valor também determina quanto tempo os manifestos do cliente podem crescer.

Suponha que esteja a transmitir um `ArchiveWindowLength` jogo de futebol, e tem apenas 30 minutos. Um espectador que começa a assistir ao seu evento 45 minutos após o início do jogo pode procurar de volta ao máximo a marca de 15 minutos. As suas saídas ao vivo para o jogo continuarão até que o Live Event seja interrompido. O conteúdo que se encontra fora do arquivoWindowLength é continuamente descartado do armazenamento e não é recuperável. Neste exemplo, o vídeo entre o início do evento e a marca de 15 minutos teria sido purgado do seu DVR e do contentor em armazenamento de bolhas para o ativo. O arquivo não é recuperável e é removido do recipiente no armazenamento de blob Azure.

Um Live Event suporta até três saídas ao vivo em simultâneo (pode criar no máximo 3 gravações/arquivos a partir de um live stream ao mesmo tempo). Este suporte permite-lhe publicar e arquivar diferentes partes de um evento conforme necessário. Suponha que precisa transmitir um feed linear 24x7 ao vivo e criar "gravações" dos diferentes programas ao longo do dia para oferecer aos clientes como conteúdo on-demand para visualização de recuperação. Para este cenário, cria-se primeiro uma saída ao vivo primária com uma janela de arquivo curta de 1 hora ou menos - este é o principal fluxo ao vivo que os seus espectadores iriam sintonizar. Criaria um Localizador de Streaming para esta Saída Ao Vivo e publicaria-o na sua aplicação ou web site como o feed "Live". Enquanto o Live Event está em execução, pode criar programáticamente uma segunda saída ao vivo simultânea no início de um programa (ou 5 minutos antes para fornecer algumas pegas para aparar mais tarde). Esta segunda saída ao vivo pode ser eliminada 5 minutos após o fim do programa. Com este segundo ativo, pode criar um novo Localizador de Streaming para publicar este programa como um ativo a pedido no catálogo da sua aplicação. Pode repetir este processo várias vezes para outros limites do programa ou destaques que pretende partilhar como vídeos a pedido, enquanto o feed "Live" da primeira Saída ao Vivo continua a transmitir o feed linear.

## <a name="creating-an-archive-for-on-demand-playback"></a>Criação de um arquivo para reprodução a pedido

O ativo que a Saída Ao Vivo está a arquivar torna-se automaticamente um ativo a pedido quando a Saída Ao Vivo é eliminada. Tem de eliminar todas as saídas ao vivo antes de um Evento Ao Vivo poder ser interrompido. Pode utilizar uma bandeira opcional [removendoOutputsOnStop](https://docs.microsoft.com/rest/api/media/liveevents/stop#request-body) para remover automaticamente as saídas ao vivo em paragem.

Mesmo depois de parar e apagar o evento, os utilizadores podem transmitir o seu conteúdo arquivado como um vídeo a pedido, desde que não apague o ativo. Um ativo não deve ser apagado se for usado por um evento; o evento deve ser eliminado primeiro.

Se publicou o ativo da sua Saída Ao Vivo utilizando um localizador de streaming, o Live Event (até ao comprimento da janela DVR) continuará a ser visível até à expiração ou eliminação do localizador de streaming, o que vier primeiro.

Para obter mais informações, consulte:

- [Visão geral do streaming ao vivo](live-streaming-overview.md)
- [Tutorial de streaming ao vivo](stream-live-tutorial-with-api.md)

> [!NOTE]
> Ao eliminar a Saída Ao Vivo, não está a eliminar o ativo e o conteúdo subjacentes no ativo.

## <a name="next-steps"></a>Passos seguintes

* [Subclipe os seus vídeos](subclip-video-rest-howto.md).
* [Defina filtros para os seus ativos](filters-dynamic-manifest-rest-howto.md).
