---
title: Orientação de migração de proteção de conteúdos
description: Este artigo dá-lhe orientação baseada em cenário de proteção de conteúdos que irá ajudá-lo a migrar de Azure Media Services v2 para v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 8853f5234cdbd5244d4624c91b7e3914948cf704
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105561309"
---
# <a name="content-protection-scenario-based-migration-guidance"></a>Orientação de migração baseada em cenários de proteção de conteúdos

![logotipo do guia de migração](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![etapas de migração 2](./media/migration-guide/steps-4.svg)

Este artigo dá-lhe orientação baseada em cenário de proteção de conteúdos que irá ajudá-lo a migrar de Azure Media Services v2 para v3.

## <a name="protect-content-in-v3-api"></a>Proteger o conteúdo em V3 API

Utilize o suporte para [funcionalidades multi-chave](design-multi-drm-system-with-access-control.md) na nova API v3.

Consulte conceitos de proteção de conteúdos, tutoriais e como guiar abaixo para etapas específicas.

## <a name="content-protection-concepts-tutorials-and-how-to-guides"></a>Conceitos de proteção de conteúdos, tutoriais e como guiar

### <a name="concepts"></a>Conceitos

- [Proteja o seu conteúdo com encriptação dinâmica dos Media Services](content-protection-overview.md)
- [Conceção de um sistema de proteção de conteúdos multi-DRM com controlo de acesso](design-multi-drm-system-with-access-control.md)
- [Serviços de Mídia v3 com modelo de licença PlayReady](playready-license-template-overview.md)
- [Serviços de Mídia v3 com visão geral do modelo de licença widevine](widevine-license-template-overview.md)
- [Requisitos de licença e configuração do Apple FairPlay](fairplay-license-overview.md)
- [Políticas de streaming](streaming-policy-concept.md)
- [Políticas-chave de conteúdo](content-key-policy-concept.md)

### <a name="tutorials"></a>Tutoriais

[Quickstart: Use o portal para encriptar conteúdo](encrypt-content-quickstart.md)

### <a name="how-to-guides"></a>Guias de procedimentos

- [Obter uma chave de assinatura da política existente](get-content-key-policy-dotnet-howto.md)
- [Streaming Offline FairPlay para iOS com Media Services v3](offline-fairplay-for-ios.md)
- [Streaming offline Widevine para Android com Media Services v3](offline-widevine-for-android.md)
- [Streaming Offline PlayReady para Windows 10 com Media Services v3](offline-plaready-streaming-for-windows-10.md)

## <a name="samples"></a>Amostras

Também pode [comparar o código V2 e V3 nas amostras de código](migrate-v-2-v-3-migration-samples.md).
