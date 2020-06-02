---
title: Análise de vídeo ao vivo nas quotas IoT Edge - Azure
description: Este artigo descreve live video analytics em quotas e limitações IoT Edge.
ms.topic: conceptual
ms.date: 05/22/2020
ms.openlocfilehash: 9b01db8f1120174806f4b687f7e9ebc4e2386f3d
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261052"
---
# <a name="quotas-and-limitations"></a>Quotas e limitações

Este artigo enumera as quotas e limitações do live video analytics no módulo IoT Edge.

## <a name="maximum-period-of-disconnected-use"></a>Período máximo de utilização desligada

O módulo de borda pode sustentar a perda temporária da conectividade da rede. Se o módulo permanecer desligado por mais de 36 horas, irá desativar quaisquer instâncias de gráfico que estavam em execução, e novas chamadas de métodos diretos serão bloqueadas.

Para retomar o módulo de borda para um estado operacional, terá de restaurar a conectividade da rede, e o módulo precisa de ser capaz de comunicar com sucesso com a conta do Azure Media Service.

## <a name="maximum-number-of-graph-instances"></a>Número máximo de instâncias de gráficos

Pode ter no máximo 1000 instâncias de gráfico por módulo (criadas via GraphInstanceSet).

## <a name="maximum-number-of-graph-topologies"></a>Número máximo de topologias de gráficos

Pode ter no máximo 50 topologias de gráficos por módulo (criadas via GraphTopologySet).

## <a name="limitations-on-graph-topologies-at-preview"></a>Limitações nas topologias de gráficos na pré-visualização

Com o lançamento da pré-visualização, existem limitações em diferentes nós que podem ser conectados entre si numa topologia de gráficos de mídia.

* Fonte RTSP
   * Apenas uma fonte de RTSP é permitida por topologia de gráfico.
* Processador de filtro de taxa de fotogramas
   * Deve ser imediatamente a jusante a partir de fonte RTSP ou processador de deteção de movimentos.
   * Não é possível ser utilizado a jusante de um processador de extensão HTTP.
   * Não pode estar a montante de um processador de deteção de movimentos.
* Processador de extensão HTTP
   * Pode haver no máximo um desses processadores por topologia de gráficos.
* Processador de deteção de movimentos
   * Deve ser imediatamente a jusante da fonte RTSP.
   * Pode haver no máximo um desses processadores por topologia de gráficos.
   * Não é possível ser utilizado a jusante de um processador de extensão HTTP.
* Processador do portão de sinal
   * Deve ser imediatamente a jusante da fonte RTSP.
* Pia de ativos 
   * Pode haver no máximo um nó por gráfico topologia.
      * Se for utilizado um lavatório de ativos, então uma pia de ficheiro não pode estar presente, ou vice-versa.
   * Deve ser imediatamente a jusante da fonte RTSP ou do processador do portão de sinal.
* Pia de arquivo
   * Pode haver no máximo um tal nó por topologia de gráfico (ver nota acima sobre o lavatório de ativos).
   * Deve ser imediatamente a jusante do processador do portão de sinalização.
   * Não pode ser imediatamente a jusante do processador de extensão HTTP ou processador de deteção de movimentos
* Pia IoT Hub
   * Não pode ser imediatamente a jusante de uma fonte de hub IoT.

Se forem utilizados nós de processador de deteção de movimentos e de taxa de filtro, devem estar na mesma cadeia de nós que conduz ao nó de origem RTSP.

## <a name="limitations-on-media-service-operations-at-preview"></a>Limitações nas operações do Serviço de Mídia na pré-visualização

No momento do lançamento da pré-visualização, o Live Video Analytics no IoT Edge não suporta o seguinte:

* A capacidade de migrar a conta do Media Service de uma subscrição para outra sem interrupção.
* A capacidade de utilizar mais do que uma conta de Armazenamento na conta do Media Service.
* A capacidade de alterar a informação principal do serviço nas propriedades desejadas do módulo dinamicamente, sem um reinício.

## <a name="next-steps"></a>Passos seguintes

[Descrição geral](overview.md)
