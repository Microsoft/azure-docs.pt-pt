---
title: Live Video Analytics em notas de lançamento IoT Edge - Azure
description: Este tópico fornece notas de lançamento de Live Video Analytics sobre lançamentos, melhorias, correções de bugs e problemas conhecidos.
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: 18da50fe763ef7c5fc3c06ac00052679c488cae7
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92015639"
---
# <a name="live-video-analytics-on-iot-edge-release-notes"></a>Vídeo ao vivo analítico em notas de lançamento do IoT Edge

>Seja notificado sobre quando revisitar esta página para obter atualizações copiando e colando este URL: `https://docs.microsoft.com/api/search/rss?search=%22Live+Video+Analytics+on+IoT+Edge+release+notes%22&locale=en-us` no seu leitor de feed RSS.

Este artigo fornece-lhe informações sobre:

* Os últimos lançamentos
* Problemas conhecidos
* Correções de erros
* Funcionalidade preterida

<hr width=100%>

## <a name="september-22-2020"></a>22 de setembro de 2020

Esta etiqueta de lançamento para a atualização de setembro de 2020 do módulo é:

```
mcr.microsoft.com/media/live-video-analytics:1.0.4
```

> [!NOTE]
> Nos quickstarts e tutoriais, os manifestos de implantação usam uma etiqueta de 1 (live-video-analytics:1). Por isso, simplesmente a recolocação desses manifestos deve atualizar o módulo na sua borda > dispositivos.

### <a name="module-updates"></a>Atualizações de módulos

* Um novo nó de extensão de gráfico, [MediaGraphCognitiveServicesVisionExtension](spatial-analysis-tutorial.md) está disponível para integrar com o módulo [de Análise Espacial](/legal/cognitive-services/computer-vision/intro-to-spatial-analysis-public-preview)(pré-visualização) dos Serviços Cognitivos.
* Suporte adicional para dispositivos Linux ARM64 - utilize [passos manuais](deploy-iot-edge-device.md) para a implantação destes dispositivos.

### <a name="documentation-updates"></a>Atualizações de documentação

* [Estão](deploy-azure-stack-edge-how-to.md) disponíveis instruções para a utilização de Vídeo Analytics ao Vivo em dispositivos IoT Edge em Azure Stack Edge.
* Novo tutorial sobre o desenvolvimento de modelos específicos de visão computacional usando [o serviço Custom Vision](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) e usando-o para analisar vídeo ao [vivo](custom-vision-tutorial.md) em tempo real.

<hr width=100%>

## <a name="august-19-2020"></a>19 de agosto de 2020

Esta etiqueta de lançamento para a atualização de agosto de 2020 do módulo é:

```
mcr.microsoft.com/media/live-video-analytics:1.0.3
```

> [!NOTE]
> Nos quickstarts e tutoriais, os manifestos de implantação usam uma etiqueta de 1 (live-video-analytics:1). Por isso, simplesmente a recolocação desses manifestos deve atualizar o módulo na sua borda > dispositivos.

### <a name="module-updates"></a>Atualizações de módulos

* Agora pode obter um desempenho de transferência de conteúdo de dados elevado entre o Live Video Analytics no IoT Edge e a sua extensão personalizada utilizando a estrutura gRPC. Vê [isto](analyze-live-video-use-your-grpc-model-quickstart.md) para começar.
* Uma implementação regional mais ampla do Live Video Analytics e apenas o serviço de nuvem foi atualizado.  
* Live Video Analytics está agora disponível em 25 regiões adicionais em todo o mundo. Aqui está a [lista](https://azure.microsoft.com/global-infrastructure/services/?products=media-services) de todas as regiões disponíveis.  
* A [criação](https://aka.ms/lva-edge/setup-resources-for-samples) para arranques rápidos foi também atualizada com o apoio de novas regiões.
    * Não há nenhuma chamada de ação para quem já tenha recursos de configuração

### <a name="bug-fixes"></a>Correções de erros 

* Remova o uso de uma extensão azul prevadida no script configurado

<hr width=100%>

## <a name="july-13-2020"></a>13 de julho de 2020

Esta etiqueta de lançamento para a atualização de julho de 2020 do módulo é:

```
mcr.microsoft.com/media/live-video-analytics:1.0.2
```

> [!NOTE]
> Nos quickstarts e tutoriais, os manifestos de implantação usam uma etiqueta de 1 (live-video-analytics:1). Por isso, simplesmente a recolocação desses manifestos deve atualizar o módulo na sua borda > dispositivos.

### <a name="module-updates"></a>Atualizações de módulos

* Agora pode criar topologias de gráficos que tenham um nó de pia de ativo, bem como um nó de lavatório de ficheiros a jusante de um nó do processador do portão de sinal. Veja [isto](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-motion-assets-files) como exemplo.

### <a name="bug-fixes"></a>Correções de erros

* Melhorias na validação das propriedades desejadas

<hr width=100%>

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