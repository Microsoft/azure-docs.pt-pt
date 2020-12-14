---
title: Análise de vídeo ao vivo em quotas e limitações IoT Edge - Azure
description: Este artigo descreve live video analytics em quotas e limitações IoT Edge.
ms.topic: conceptual
ms.date: 05/22/2020
ms.openlocfilehash: 68c7b91bb1051348b5a8e52f841d443894f0a632
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400529"
---
# <a name="quotas-and-limitations"></a>Quotas e limitações

Este artigo enumera as quotas e limitações do live video analytics no módulo IoT Edge.

## <a name="maximum-period-of-disconnected-use"></a>Período máximo de utilização desligada

O módulo de borda pode sustentar a perda temporária da conectividade da internet. Se o módulo permanecer desligado por mais de 36 horas, irá desativar quaisquer instâncias de gráfico que estivessem em funcionamento. Todas as chamadas diretas serão bloqueadas.

Para retomar o módulo de borda para um estado operacional, terá de restaurar a conectividade da Internet para que o módulo seja capaz de comunicar com sucesso com a conta do Azure Media Service.

## <a name="maximum-number-of-graph-instances"></a>Número máximo de instâncias de gráficos

No máximo 1000 instâncias de gráfico por módulo (criadas via GraphInstanceSet) são suportadas.

## <a name="maximum-number-of-graph-topologies"></a>Número máximo de topologias de gráficos

No máximo, são suportadas 50 topologias por módulo (criadas via GraphTopologySet).

## <a name="limitations-on-graph-topologies-at-preview"></a>Limitações nas topologias de gráficos na pré-visualização

Com o lançamento da pré-visualização, existem limitações em diferentes nós que podem ser conectados entre si numa topologia de gráficos de mídia.

* Fonte RTSP
   * Apenas uma fonte de RTSP é permitida por topologia de gráfico.
* Processador de deteção de movimentos
   * Deve ser imediatamente a jusante da fonte RTSP.
   * Não é possível ser utilizado a jusante de um processador de extensão HTTP ou gRPC.
* Processador do portão de sinal
   * Deve ser imediatamente a jusante da fonte RTSP.
* Pia de ativos 
   * Deve ser imediatamente a jusante da fonte RTSP ou do processador do portão de sinal.
* Pia de arquivo
   * Deve ser imediatamente a jusante do processador do portão de sinalização.
   * Não pode ser imediatamente a jusante de um processador de extensão HTTP ou gRPC ou processador de deteção de movimentos
* Pia IoT Hub
   * Não pode ser imediatamente a jusante de uma fonte de hub IoT.

## <a name="limitations-on-media-service-operations-at-preview"></a>Limitações nas operações do Serviço de Mídia na pré-visualização

No momento do lançamento da pré-visualização, o Live Video Analytics no IoT Edge não suporta o seguinte:

* A capacidade de migrar a conta do Media Service de uma subscrição para outra sem interrupção.
* A capacidade de utilizar mais do que uma conta de Armazenamento na conta do Media Service.
* A capacidade de alterar a informação principal do serviço nas propriedades desejadas do módulo dinamicamente, sem um reinício.

Só pode utilizar câmaras IP que suportam o protocolo RTSP. Pode encontrar câmaras IP que suportam RTSP na página de [produtos conformantes ONVIF.](https://www.onvif.org/conformant-products) Procure dispositivos em conformidade com os perfis G, S ou T.

Além disso, deverá configurar estas câmaras para utilizar o vídeo H.264 e o áudio AAC. Atualmente, outros códigos não estão suportados. 

## <a name="next-steps"></a>Passos seguintes

[Descrição geral](overview.md)
