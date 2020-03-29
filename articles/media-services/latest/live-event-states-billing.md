---
title: LiveEvent afirma e faturação nos Serviços De Mídia Azure [ Microsoft Docs
description: Este tema dá uma visão geral dos estados do Azure Media Services LiveEvent e da faturação.
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
ms.date: 10/24/2019
ms.author: juliako
ms.openlocfilehash: e4eee3b9f3f97bf7cd7a7b61425ec5c9a3a198ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76543080"
---
# <a name="live-event-states-and-billing"></a>Estados do Evento Ao Vivo e faturação

No Azure Media Services, um Evento Ao Vivo começa a faturar assim que o seu estado transita para **Running**. Será cobrado mesmo que não haja vídeo a fluir pelo serviço. Para impedir que o Evento Ao Vivo faça faturação, tem de parar o Evento Ao Vivo. A Transcrição ao Vivo é cobrada da mesma forma que o Evento Ao Vivo.

Quando o **LiveEventEncodingType** no seu [Evento Ao Vivo](https://docs.microsoft.com/rest/api/media/liveevents) está definido para Standard ou Premium1080p, o Media Services desliga automaticamente qualquer Evento Ao Vivo que ainda se encontra no estado de **Execução** 12 horas após a perda do feed de entrada, e não há saída sem saída ao **vivo.** No entanto, ainda será cobrado pelo tempo em que o Evento Ao Vivo esteve no estado **de Corrida.**

> [!NOTE]
> Os Eventos Ao Vivo pass-through não são desligados automaticamente e devem ser explicitamente interrompidos através da API para evitar faturações excessivas. 

## <a name="states"></a>Estados

O Evento Ao Vivo pode estar num dos seguintes estados.

|Estado|Descrição|
|---|---|
|**Parada**| Este é o estado inicial do Evento Ao Vivo após a criação (a menos que o autoarranque tenha sido definido para verdade.) Não há faturação neste estado. Neste estado, as propriedades do Live Event podem ser atualizadas, mas o streaming não é permitido.|
|**Início**| O Evento Ao Vivo está a ser iniciado e os recursos estão a ser atribuídos. Não há faturação neste estado. Não são permitidas atualizações ou streaming durante este estado. Se ocorrer um erro, o Evento Ao Vivo regressa ao estado de parada.|
|**A executar**| Os recursos do Live Event foram atribuídos, ingerir e pré-visualizar URLs, e é capaz de receber streams ao vivo. Neste momento, a faturação está ativa. Deve ligar explicitamente para parar o recurso Stop on the Live Event para parar a faturação adicional.|
|**A parar**| O Evento Ao Vivo está a ser interrompido e os recursos estão a ser desprovisionados. Não ocorre nenhuma faturação neste estado transitório. Não são permitidas atualizações ou streaming durante este estado.|
|**Apagando**| O Evento Ao Vivo está a ser apagado. Não ocorre nenhuma faturação neste estado transitório. Não são permitidas atualizações ou streaming durante este estado.|

Pode optar por ativar transcrições ao vivo quando criar o Live Event. Se o fizer, será cobrado para Transcrições ao Vivo sempre que o Evento Ao Vivo estiver no estado **de Corrida.** Note que será cobrado mesmo que não haja áudio fluindo através do Live Event.

## <a name="next-steps"></a>Passos seguintes

- [Visão geral do streaming ao vivo](live-streaming-overview.md)
- [Tutorial de streaming ao vivo](stream-live-tutorial-with-api.md)
