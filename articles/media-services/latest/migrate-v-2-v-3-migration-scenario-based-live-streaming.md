---
title: Orientação de migração em streaming de media ao vivo
description: Este artigo dá-lhe orientação baseada em cenários de streaming ao vivo que o ajudarão a migrar do Azure Media Services v2 para v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 273a1b5986a72acc1d6725fbb3101c9c1df17e45
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106122055"
---
# <a name="live-streaming-scenario-based-migration-guidance"></a>Orientação de migração baseada em cenário de streaming ao vivo

![logotipo do guia de migração](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![etapas de migração 2](./media/migration-guide/steps-4.svg)

O portal Azure agora suporta eventos ao vivo criados e gestão.  Você é encorajado a experimentá-lo enquanto testa a sua migração V2 para V3.

## <a name="test-the-v3-live-event-workflow"></a>Teste o fluxo de trabalho do evento v3 ao vivo

> [!NOTE]
> Canais e Programas criados com v2 (que são mapeados para eventos ao vivo e saídas ao vivo em v3) ser geridos com a API V3. A orientação é mudar para eventos v3 ao vivo e saídas ao vivo numa altura conveniente em que você pode parar o seu canal V2 existente. Atualmente, não existe suporte para migrar sem problemas um canal ao vivo 24x7 continuamente em execução para os novos eventos ao vivo V3. Assim, o tempo de inatividade da manutenção precisa de ser coordenado à melhor conveniência para o seu público e negócios.

Teste a nova forma de entregar eventos ao vivo com serviços de mídia antes de mover o seu conteúdo de V2 para V3. Aqui estão as características V3 para trabalhar e considerar para a migração.

- Crie um novo V3 [Live Event](live-event-outputs-concept.md#live-events) para codificação. Pode ativar [predefinições de codificação de 1080P e 720P.](live-event-types-comparison-reference.md#system-presets)
- Utilize a entidade [de saída ao vivo](live-event-outputs-concept.md#live-outputs) em vez de programas
- Criar [localizadores de streaming](streaming-locators-concept.md).
- Considere a sua necessidade de streaming ao vivo [HLS e DASH.](encode-dynamic-packaging-concept.md)
- Se necessitar de um rápido início de eventos ao vivo, explore as novas funcionalidades do [modo Standby.](live-event-outputs-concept.md#standby-mode)
- Se quiser transcrever o seu evento ao vivo enquanto está a acontecer, explore a nova funcionalidade [de transcrição ao vivo.](live-event-live-transcription-how-to.md)
- Crie eventos ao vivo 24x7x365 em V3 se precisar de uma duração de streaming mais longa.
- Use a [Grade de Eventos](monitoring/monitor-events-portal-how-to.md) para monitorizar os seus eventos ao vivo.

Veja conceitos de eventos ao vivo, tutoriais e como guiar abaixo para etapas específicas.

## <a name="live-events-concepts-tutorials-and-how-to-guides"></a>Conceitos de eventos ao vivo, tutoriais e como guiar

### <a name="concepts"></a>Conceitos

- [Streaming em direto com a Azure Media Services v3](stream-live-streaming-concept.md)
- [Eventos ao vivo e saídas ao vivo nos Serviços de Media](live-event-outputs-concept.md)
- [Codificadores de streaming verificados no local](recommended-on-premises-live-encoders.md)
- [Utilize a mudança de tempo e as saídas ao vivo para criar reprodução de vídeo a pedido](live-event-cloud-dvr-time-how-to.md)
- [live-event-live-transcription-how-to (pré-visualização)](live-event-live-transcription-how-to.md)
- [Comparação de tipos de eventos ao vivo](live-event-types-comparison-reference.md)
- [Estados de evento ao vivo e faturação](live-event-states-billing-concept.md)
- [Configurações de baixa latência do Evento Ao Vivo](live-event-latency-reference.md)
- [Códigos de erro do Evento Ao Vivo dos Serviços De Media](live-event-error-codes-reference.md)

### <a name="tutorials-and-quickstarts"></a>Tutoriais e quickstarts

- [Tutorial: Stream ao vivo com serviços de mídia](stream-live-tutorial-with-api.md)
- [Criar um live stream Azure Media Services com OBS](live-event-obs-quickstart.md)
- [Quickstart: Carregar, codificar e transmitir conteúdo com portal](asset-create-asset-upload-portal-quickstart.md)
- [Quickstart: Criar um live stream da Azure Media Services com o Wirecast](live-event-wirecast-quickstart.md)

## <a name="samples"></a>Amostras

Também pode [comparar o código V2 e V3 nas amostras de código](migrate-v-2-v-3-migration-samples.md).
