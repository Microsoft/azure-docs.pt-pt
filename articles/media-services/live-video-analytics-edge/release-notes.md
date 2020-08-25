---
title: Live Video Analytics em notas de lançamento IoT Edge - Azure
description: Este tópico fornece notas de lançamento de Live Video Analytics sobre lançamentos, melhorias, correções de bugs e problemas conhecidos.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: bad21297c5174c107a8eb03da0e2ed57ee7355d3
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750212"
---
# <a name="live-video-analytics-on-iot-edge-release-notes"></a>Vídeo ao vivo analítico em notas de lançamento do IoT Edge

>Seja notificado sobre quando revisitar esta página para obter atualizações copiando e colando este URL: `https://docs.microsoft.com/api/search/rss?search=%22Live+Video+Analytics+on+IoT+Edge+release+notes%22&locale=en-us` no seu leitor de feed RSS.

Este artigo fornece-lhe informações sobre:

* Os últimos lançamentos
* Problemas conhecidos
* Correções de erros
* Funcionalidade preterida

## <a name="august-19-2020"></a>19 de agosto de 2020

Esta etiqueta de lançamento para a atualização de agosto de 2020 do módulo é:

```
mcr.microsoft.com/media/live-video-analytics:1.0.3
```

> [!NOTE]
> Nos quickstarts e tutoriais, os manifestos de implantação usam uma etiqueta de 1 (live-video-analytics:1). Por isso, simplesmente a recolocação desses manifestos deve atualizar o módulo na sua borda > dispositivos.

## <a name="new-features"></a>Novas funcionalidades 

* Agora pode obter um desempenho de transferência de conteúdo de dados elevado entre o Live Video Analytics no IoT Edge e a sua extensão personalizada utilizando a estrutura gRPC. Vê [isto](analyze-live-video-use-your-grpc-model-quickstart.md) para começar.
* Uma implementação regional mais ampla do Live Video Analytics e apenas o serviço de nuvem foi atualizado.  
* Live Video Analytics está agora disponível em 25 regiões adicionais em todo o mundo. Aqui está a [lista](https://azure.microsoft.com/global-infrastructure/services/?products=media-services) de todas as regiões disponíveis.  
* A [criação](https://aka.ms/lva-edge/setup-resources-for-samples) para arranques rápidos foi também atualizada com o apoio de novas regiões.
    * Não há nenhuma chamada de ação para quem já tenha recursos de configuração

## <a name="bug-fixes"></a>Correções de erros 

* Remova o uso de uma extensão azul prevadida no script configurado

## <a name="july-13-2020"></a>13 de julho de 2020

Esta etiqueta de lançamento para a atualização de julho de 2020 do módulo é:

```
mcr.microsoft.com/media/live-video-analytics:1.0.2
```

> [!NOTE]
> Nos quickstarts e tutoriais, os manifestos de implantação usam uma etiqueta de 1 (live-video-analytics:1). Por isso, simplesmente a recolocação desses manifestos deve atualizar o módulo na sua borda > dispositivos.

### <a name="new-features"></a>Novas funcionalidades
* Agora pode criar topologias de gráficos que tenham um nó de pia de ativo, bem como um nó de lavatório de ficheiros a jusante de um nó do processador do portão de sinal. Veja [isto](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-motion-assets-files) como exemplo.

### <a name="bug-fixes"></a>Correções de erros
* Melhorias na validação das propriedades desejadas

## <a name="june-1-2020"></a>1 de junho de 2020

Este lançamento é o primeiro lançamento público de Video Analytics em IoT Edge. A etiqueta de lançamento é

```
     mcr.microsoft.com/media/live-video-analytics:1.0.0
```

### <a name="supported-functionalities"></a>Funcionalidades suportadas
* Analise os streams de vídeo ao vivo usando módulos de IA à sua escolha e grave opcionalmente vídeo no dispositivo de borda ou na nuvem
* Utilização em sistemas operativos Linux AMD64 [suportados](../../iot-edge/support.md) pela IoT Edge
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


## <a name="next-steps"></a>Passos seguintes

[Descrição geral](overview.md)
