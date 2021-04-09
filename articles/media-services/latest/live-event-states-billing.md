---
title: Estados de evento ao vivo e faturação nos Serviços Azure Media
description: Este tópico dá uma visão geral dos estados do evento ao vivo da Azure Media Services e da faturação.
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
ms.date: 10/26/2020
ms.author: inhenkel
ms.openlocfilehash: c9fa12e1ee3778d0865c75662064bd4067e56d89
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98897820"
---
# <a name="live-event-states-and-billing"></a>Estados de evento ao vivo e faturação

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Na Azure Media Services, um evento ao vivo começa a faturar assim que o seu estado transita para **Running** ou **StandBy**. Será cobrado mesmo que não haja vídeo a fluir através do serviço. Para impedir que o evento ao vivo faça a faturação, tem de parar o evento ao vivo. A transcrição ao vivo é cobrada da mesma forma que o evento ao vivo.

Quando **o LiveEventEncodingType** no seu [evento ao vivo](/rest/api/media/liveevents) está definido para Standard ou Premium1080p, o Media Services auto desliga qualquer evento ao vivo que ainda esteja no estado de **Funcionamento** 12 horas após a perda do feed de entrada, e não há **saída ao vivo** em funcionamento. No entanto, ainda será cobrado pelo tempo em que o evento ao vivo esteve no estado **de Running.**

> [!NOTE]
> Os eventos ao vivo de passagem não são automaticamente desligados e devem ser explicitamente interrompidos através da API para evitar faturações excessivas.

## <a name="states"></a>Estados

O evento ao vivo pode ser num dos seguintes estados.

|Estado|Descrição|
|---|---|
|**Parada**| Este é o estado inicial do evento ao vivo após a criação (a menos que o autostart foi definido como verdadeiro.) Não há faturação neste estado. Nenhuma entrada pode ser recebida pelo evento ao vivo. |
|**A iniciar**| O evento ao vivo está a começar e os recursos a serem atribuídos. Não há faturação neste estado.  Se ocorrer um erro, o evento ao vivo regressa ao estado Stop.|
| **Atribuição** | A ação de atribuição foi convocada para o evento ao vivo e estão a ser disponibilizadas verbas para este evento ao vivo. Uma vez que esta operação seja feita com sucesso, o evento ao vivo irá transitar para o estado de StandBy.
|**StandBy**| Recursos de eventos ao vivo foram a provisionados e está pronto para começar. A faturação ocorre neste estado.  A maioria das propriedades ainda pode ser atualizada, no entanto não é permitido ingerir ou streaming durante este estado.
|**Em Execução**| Os recursos do evento ao vivo foram atribuídos, foram gerados URLs de ingestão e pré-visualização, e é capaz de receber streams ao vivo. Neste momento, a faturação está ativa. Você deve explicitamente ligar para parar o recurso do evento ao vivo para parar mais faturação.|
|**A parar**| O evento ao vivo está a ser interrompido e os recursos estão a ser desavisionados. Nenhuma faturação ocorre neste estado transitório. |
|**Eliminar**| O evento ao vivo está a ser apagado. Nenhuma faturação ocorre neste estado transitório. |

Pode optar por ativar transcrições ao vivo quando criar o evento ao vivo. Se o fizer, será cobrado para Transcrições ao Vivo sempre que o evento ao vivo estiver no estado **de Corrida.** Note que será cobrado mesmo que não haja áudio fluindo através do evento ao vivo.

## <a name="next-steps"></a>Passos seguintes

- [Visão geral do streaming ao vivo](live-streaming-overview.md)
- [Tutorial de streaming ao vivo](stream-live-tutorial-with-api.md)
