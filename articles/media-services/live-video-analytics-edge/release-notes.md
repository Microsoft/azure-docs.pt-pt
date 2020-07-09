---
title: Live Video Analytics em notas de lançamento IoT Edge - Azure
description: Este tópico fornece notas de lançamento de Live Video Analytics sobre lançamentos, melhorias, correções de bugs e problemas conhecidos.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: fd607ee027b46cf386180338440e6530da911302
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84261038"
---
# <a name="live-video-analytics-on-iot-edge-release-notes"></a>Vídeo ao vivo analítico em notas de lançamento do IoT Edge

>Seja notificado sobre quando revisitar esta página para obter atualizações copiando e colando este URL: `https://docs.microsoft.com/api/search/rss?search=%22Live+Video+Analytics+on+IoT+Edge+release+notes%22&locale=en-us` no seu leitor de feed RSS.

Este artigo fornece-lhe informações sobre:

* Os últimos lançamentos
* Problemas conhecidos
* Correções de erros
* Funcionalidade preterida

## <a name="june-1-2020"></a>1 de junho de 2020

Este lançamento é o primeiro lançamento público de Video Analytics em IoT Edge. A etiqueta de lançamento é

```
     mcr.microsoft.com/media/live-video-analytics:1.0.0
```

### <a name="supported-functionalities"></a>Funcionalidades suportadas
* Analise os streams de vídeo ao vivo usando módulos de IA à sua escolha e grave opcionalmente vídeo no dispositivo de borda ou na nuvem
* Utilização em sistemas operativos Linux AMD64 [suportados](https://docs.microsoft.com/azure/iot-edge/support) pela IoT Edge
* Implementar e configurar o módulo através do IoT Hub utilizando o portal Azure ou o Código do Estúdio Visual
* Gerir [topologias de gráficos e instâncias de gráfico remota](media-graph-concept.md#media-graph-topologies-and-instances) ou localmente através das seguintes chamadas de método direto

    *   GraphTopologyGet
    *   GraphTopologySet
    *   GraphTopologyDelete
    *   Lista de Graftopologia
    *   GraphInstanceGet
    *   GraphInstanceSet
    *   GraphInstanceDelete
    *   GraphInstanceList


## <a name="next-steps"></a>Próximos passos

[Descrição geral](overview.md)
