---
title: Estados e cobrança do LiveEvent nos serviços de mídia do Azure | Microsoft Docs
description: Este tópico fornece uma visão geral dos Estados de LiveEvent e da cobrança dos serviços de mídia do Azure.
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
ms.openlocfilehash: af3d4b51dadfaa99a166ca0ce475c5a110d8f6e8
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933687"
---
# <a name="live-event-states-and-billing"></a>Estados e cobrança de eventos ao vivo

Nos serviços de mídia do Azure, um evento ao vivo começa a cobrança assim que sua transição de estado para **em execução**. Para interromper o evento ao vivo da cobrança, você precisa parar o evento ao vivo.

Quando o **LiveEventEncodingType** em seu [evento ao vivo](https://docs.microsoft.com/rest/api/media/liveevents) é definido como Standard ou Premium1080p, os serviços de mídia desligam automaticamente qualquer evento ao vivo que ainda esteja no estado de **execução** 12 horas após o feed de entrada ser perdido e não há nenhuma **saída ao vivo** s em execução. No entanto, você ainda será cobrado pelo tempo em que o evento ao vivo estava no estado de **execução** .

> [!NOTE]
> Eventos ao vivo de passagem não são desligados automaticamente e devem ser explicitamente interrompidos por meio da API para evitar uma cobrança excessiva. 

## <a name="states"></a>Estados

O evento ao vivo pode estar em um dos Estados a seguir.

|Estado|Descrição|
|---|---|
|**Interrompido**| Esse é o estado inicial do evento ao vivo após a criação (a menos que AutoStart tenha sido definido como true). Nenhuma cobrança ocorre nesse estado. Neste estado, as propriedades do Evento em Direto podem ser atualizadas, mas a transmissão em fluxo não é permitida.|
|**Comece**| O evento ao vivo está sendo iniciado e os recursos estão sendo alocados. Nenhuma cobrança ocorre nesse estado. Não são permitidas atualizações ou streaming durante esse estado. Se ocorrer um erro, o evento ao vivo retornará ao estado parado.|
|**Executado**| Os recursos de evento ao vivo foram alocados, as URLs de ingestão e de visualização foram geradas e são capazes de receber fluxos ao vivo. Neste ponto, a cobrança está ativa. Você deve chamar Stop explicitamente no recurso de evento ao vivo para interromper mais cobranças.|
|**Impedir**| O evento ao vivo está sendo interrompido e os recursos estão sendo desprovisionados. Nenhuma cobrança ocorre nesse estado transitório. Não são permitidas atualizações ou streaming durante esse estado.|
|**Excluído**| O Evento em Direto está a ser eliminado. Nenhuma cobrança ocorre nesse estado transitório. Não são permitidas atualizações ou streaming durante esse estado.|

## <a name="next-steps"></a>Passos seguintes

- [Visão geral da transmissão ao vivo](live-streaming-overview.md)
- [Tutorial de transmissão ao vivo](stream-live-tutorial-with-api.md)
