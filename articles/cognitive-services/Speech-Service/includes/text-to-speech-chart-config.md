---
title: Instalar recipientes de discurso
titleSuffix: Azure Cognitive Services
description: Detalha as opções de configuração do gráfico de timão text-to-speech.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 22168974ab8b285413b4fa6e947c05f65a73ae12
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002298"
---
### <a name="text-to-speech-sub-chart-chartstexttospeech"></a>Texto-a-Discurso (sub-gráfico: gráficos/textToSpeech)

Para anular o gráfico "guarda-chuva", adicione o prefixo `textToSpeech.` em qualquer parâmetro para torná-lo mais específico. Por exemplo, irá sobrepor-se ao parâmetro correspondente, por exemplo, `textToSpeech.numberOfConcurrentRequest` sobreposições `numberOfConcurrentRequest` .

|Parâmetro|Descrição|Predefinição|
| -- | -- | -- |
| `enabled` | Se o serviço **de texto para voz** está ativado. | `false` |
| `numberOfConcurrentRequest` | O número de pedidos simultâneos para o serviço **de texto-a-voz.** Este gráfico calcula automaticamente os recursos de CPU e memória, com base neste valor. | `2` |
| `optimizeForTurboMode`| Se o serviço precisa de otimizar para entrada de texto através de ficheiros de texto. Se, `true` este gráfico atribuir mais recursos CPU ao serviço. | `false` |
| `image.registry`| O registo de imagem de estivador **de texto para discurso.** | `containerpreview.azurecr.io` |
| `image.repository` | O repositório de imagem de estivador **de texto para discurso.** | `microsoft/cognitive-services-text-to-speech` |
| `image.tag` | A etiqueta de imagem de estivador **de texto para discurso.** | `latest` |
| `image.pullSecrets` | Os segredos da imagem para puxar a imagem de estivador **de texto para discurso.** | |
| `image.pullByHash`| Se a imagem do estivador é puxada por haxixe. Se `true` for `image.hash` necessário. | `false` |
| `image.hash`| O hash da imagem do estivador **de texto para o discurso.** Só é utilizado quando `image.pullByHash: true` .  | |
| `image.args.eula` (obrigatório) | Indica que aceitou a licença. O único valor válido é `accept` | |
| `image.args.billing` (obrigatório) | O valor URI do ponto final de faturação está disponível na página geral do portal Azure. | |
| `image.args.apikey` (obrigatório) | Usado para rastrear informação de faturação. ||
| `service.type` | O tipo de serviço Kubernetes do serviço **de texto-a-voz.** Consulte as [instruções dos tipos de serviço Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) para obter mais detalhes e verifique o suporte do fornecedor de nuvem. | `LoadBalancer` |
| `service.port`|  O porto do serviço **de texto-a-fala.** | `80` |
| `service.annotations` | As anotações **de texto para falar** para os metadados de serviço. Anotações são pares de valor chave. <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | Se o [Pod Horizontal Autoscaler](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) está ativado. Se, `true` o ser implantado no cluster `text-to-speech-autoscaler` Kubernetes. | `true` |
| `service.podDisruption.enabled` | Se o [Orçamento de Interrupção](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) do Pod está ativado. Se, `true` o ser implantado no cluster `text-to-speech-poddisruptionbudget` Kubernetes. | `true` |