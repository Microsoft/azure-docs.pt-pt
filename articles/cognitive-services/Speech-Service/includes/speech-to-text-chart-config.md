---
title: Instalar os contentores de voz
titleSuffix: Azure Cognitive Services
description: Fornece detalhes sobre as opções de configuração de gráfico do helm de voz em texto.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 1b46c58d3f3c804052e637f7bde2e1a456764dba
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717209"
---
### <a name="speech-to-text-sub-chart-chartsspeechtotext"></a>Conversão de voz em texto (gráfico secundárias: gráficos/speechToText)

Para substituir o gráfico "guarda-chuva", adicione o prefixo `speechToText.` em qualquer parâmetro para torná-lo mais específico. Por exemplo, ele irá substituir o parâmetro correspondente por exemplo, `speechToText.numberOfConcurrentRequest` substitui `numberOfConcurrentRequest`.

|Parâmetro|Descrição|Predefinição|
| -- | -- | -- |
| `enabled` | Se o **voz em texto** serviço está ativado. | `false` |
| `numberOfConcurrentRequest` | O número de pedidos simultâneos para o **voz em texto** serviço. Este gráfico calcula automaticamente os recursos de CPU e memória, com base nesse valor. | `2` |
| `optimizeForAudioFile`| Se o serviço precisa otimizar a entrada de áudio através de arquivos de áudio. Se `true`, este gráfico irá alocar mais recursos de CPU para o serviço. | `false` |
| `image.registry`| O **voz em texto** registo de imagem do docker. | `containerpreview.azurecr.io` |
| `image.repository` | O **voz em texto** repositório de imagens do docker. | `microsoft/cognitive-services-speech-to-text` |
| `image.tag` | O **voz em texto** tag de imagem do docker. | `latest` |
| `image.pullSecrets` | Os segredos de imagem para extrair os **voz em texto** imagem do docker. | |
| `image.pullByHash`| Se a imagem do docker é solicitada por hash. Se `true`, `image.hash` é necessária. | `false` |
| `image.hash`| O **voz em texto** hash de imagem do docker. Apenas utilizado quando `image.pullByHash: true`.  | |
| `image.args.eula` (obrigatório) | Indica que aceite a licença. É o único valor válido `accept` | |
| `image.args.billing` (obrigatório) | O valor do URI de ponto de extremidade faturação está disponível na página de descrição geral de voz do portal do Azure. | |
| `image.args.apikey` (obrigatório) | Utilizado para controlar informações de faturação. ||
| `service.type` | O Kubernetes do tipo de serviço do **voz em texto** serviço. Consulte a [instruções de tipos de serviço do Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) para obter mais detalhes e verifique se o suporte de fornecedor de cloud. | `LoadBalancer` |
| `service.port`|  A porta do **voz em texto** serviço. | `80` |
| `service.autoScaler.enabled` | Se o [Horizontal de Pods de dimensionamento automático](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) está ativada. Se `true`, o `speech-to-text-autoscaler` será implementado no cluster de Kubernetes. | `true` |
| `service.podDisruption.enabled` | Se o [orçamento de interrupção de Pod](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) está ativada. Se `true`, o `speech-to-text-poddisruptionbudget` será implementado no cluster de Kubernetes. | `true` |