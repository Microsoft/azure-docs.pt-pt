---
title: Instalar os contentores de voz
titleSuffix: Azure Cognitive Services
description: Fornece detalhes sobre as opções de configuração de gráfico do helm texto para voz.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: e6c7dcd3015b0b8ab5b3c719ebd2397bc814b81a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717243"
---
### <a name="text-to-speech-sub-chart-chartstexttospeech"></a>Voz (gráfico secundárias: gráficos/textToSpeech)

Para substituir o gráfico "guarda-chuva", adicione o prefixo `textToSpeech.` em qualquer parâmetro para torná-lo mais específico. Por exemplo, ele irá substituir o parâmetro correspondente por exemplo, `textToSpeech.numberOfConcurrentRequest` substitui `numberOfConcurrentRequest`.

|Parâmetro|Descrição|Predefinição|
| -- | -- | -- |
| `enabled` | Se o **voz** serviço está ativado. | `false` |
| `numberOfConcurrentRequest` | O número de pedidos simultâneos para o **voz** serviço. Este gráfico calcula automaticamente os recursos de CPU e memória, com base nesse valor. | `2` |
| `optimizeForTurboMode`| Se o serviço precisa otimizar o texto de entrada por meio de arquivos de texto. Se `true`, este gráfico irá alocar mais recursos de CPU para o serviço. | `false` |
| `image.registry`| O **voz** registo de imagem do docker. | `containerpreview.azurecr.io` |
| `image.repository` | O **voz** repositório de imagens do docker. | `microsoft/cognitive-services-text-to-speech` |
| `image.tag` | O **voz** tag de imagem do docker. | `latest` |
| `image.pullSecrets` | Os segredos de imagem para extrair os **voz** imagem do docker. | |
| `image.pullByHash`| Se a imagem do docker é solicitada por hash. Se `true`, `image.hash` é necessária. | `false` |
| `image.hash`| O **voz** hash de imagem do docker. Apenas utilizado quando `image.pullByHash: true`.  | |
| `image.args.eula` (obrigatório) | Indica que aceite a licença. É o único valor válido `accept` | |
| `image.args.billing` (obrigatório) | O valor do URI de ponto de extremidade faturação está disponível na página de descrição geral de voz do portal do Azure. | |
| `image.args.apikey` (obrigatório) | Utilizado para controlar informações de faturação. ||
| `service.type` | O Kubernetes do tipo de serviço do **voz** serviço. Consulte a [instruções de tipos de serviço do Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) para obter mais detalhes e verifique se o suporte de fornecedor de cloud. | `LoadBalancer` |
| `service.port`|  A porta do **voz** serviço. | `80` |
| `service.autoScaler.enabled` | Se o [Horizontal de Pods de dimensionamento automático](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) está ativada. Se `true`, o `text-to-speech-autoscaler` será implementado no cluster de Kubernetes. | `true` |
| `service.podDisruption.enabled` | Se o [orçamento de interrupção de Pod](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) está ativada. Se `true`, o `text-to-speech-poddisruptionbudget` será implementado no cluster de Kubernetes. | `true` |