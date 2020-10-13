---
title: LiveEvent afirma e faturação na Azure Media Services / Microsoft Docs
description: Este tópico dá uma visão geral dos estados do Azure Media Services LiveEvent e da faturação.
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
ms.openlocfilehash: 1b058eefe22238b60c3482c55b5ae340f4e597f0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89296741"
---
# <a name="live-event-states-and-billing"></a>Estados do Evento Ao Vivo e faturação

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Na Azure Media Services, um Evento Ao Vivo começa a faturar assim que o seu estado transita para **o Running.** Será cobrado mesmo que não haja vídeo a fluir através do serviço. Para impedir que o Evento Ao Vivo faça a faturação, tem de parar o Evento Ao Vivo. A transcrição ao vivo é cobrada da mesma forma que o Evento Ao Vivo.

Quando **o LiveEventEncodingType** no seu [Evento Ao Vivo](/rest/api/media/liveevents) está definido para Standard ou Premium1080p, o Media Services desliga qualquer Evento Live que ainda se encontra no estado de **Funcionamento** 12 horas após a perda do feed de entrada, e não há **saída ao vivo**em funcionamento. No entanto, ainda será cobrado pelo tempo em que o Live Event esteve no estado **de Running.**

> [!NOTE]
> Os Eventos Ao Vivo passam por passagem não são automaticamente desligados e devem ser explicitamente interrompidos através da API para evitar faturações excessivas. 

## <a name="states"></a>Estados

O Live Event pode estar num dos seguintes estados.

|Estado|Descrição|
|---|---|
|**Parada**| Este é o estado inicial do Live Event após a criação (a menos que o arranque automático tenha sido definido como verdadeiro.) Não há faturação neste estado. Neste estado, as propriedades do Live Event podem ser atualizadas, mas o streaming não é permitido.|
|**A iniciar**| O Live Event está a ser iniciado e estão a ser atribuídos recursos. Não há faturação neste estado. Não são permitidas atualizações ou streamings durante este estado. Se ocorrer um erro, o Evento Ao Vivo regressa ao estado Stop.|
|**Em Execução**| Os recursos do Live Event foram atribuídos, foram gerados URLs de ingerção e pré-visualização, e é capaz de receber streams ao vivo. Neste momento, a faturação está ativa. Tem de ligar explicitamente para parar o recurso Live Event para travar a faturação adicional.|
|**A parar**| O Evento Ao Vivo está a ser interrompido e os recursos estão a ser desavisionados. Nenhuma faturação ocorre neste estado transitório. Não são permitidas atualizações ou streamings durante este estado.|
|**Eliminar**| O Evento Ao Vivo está a ser apagado. Nenhuma faturação ocorre neste estado transitório. Não são permitidas atualizações ou streamings durante este estado.|

Pode optar por ativar transcrições ao vivo quando criar o Evento Ao Vivo. Se o fizer, será cobrado para transcrições ao vivo sempre que o Evento Ao Vivo estiver no estado **de Corrida.** Note que será cobrado mesmo que não haja áudio fluindo através do Evento Ao Vivo.

## <a name="next-steps"></a>Passos seguintes

- [Visão geral do streaming ao vivo](live-streaming-overview.md)
- [Tutorial de streaming ao vivo](stream-live-tutorial-with-api.md)
