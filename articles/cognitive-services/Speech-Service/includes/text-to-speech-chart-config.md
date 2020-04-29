---
title: Instalar recipientes de fala
titleSuffix: Azure Cognitive Services
description: Detalha as opções de configuração do gráfico de gráfico sonantes de texto a fala.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 22168974ab8b285413b4fa6e947c05f65a73ae12
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80874359"
---
### <a name="text-to-speech-sub-chart-chartstexttospeech"></a>Texto-a-fala (sub-gráfico: gráficos/textToSpeech)

Para anular o gráfico "guarda-chuva", `textToSpeech.` adicione o prefixo em qualquer parâmetro para torná-lo mais específico. Por exemplo, irá sobrepor-se ao `textToSpeech.numberOfConcurrentRequest` parâmetro `numberOfConcurrentRequest`correspondente, por exemplo, sobreposições.

|Parâmetro|Descrição|Predefinição|
| -- | -- | -- |
| `enabled` | Se o serviço **de texto-a-fala** está ativado. | `false` |
| `numberOfConcurrentRequest` | O número de pedidos simultâneos para o serviço **de texto-a-fala.** Este gráfico calcula automaticamente os recursos de CPU e memória, com base neste valor. | `2` |
| `optimizeForTurboMode`| Se o serviço precisa de otimizar para entrada de texto através de ficheiros de texto. Se, `true`este gráfico irá atribuir mais recursos CPU ao serviço. | `false` |
| `image.registry`| O registo de imagem do estivador **de texto a fala.** | `containerpreview.azurecr.io` |
| `image.repository` | O repositório de imagem de **estivador de texto a fala.** | `microsoft/cognitive-services-text-to-speech` |
| `image.tag` | A etiqueta de imagem do estivador **de texto para a fala.** | `latest` |
| `image.pullSecrets` | Os segredos de imagem para puxar a imagem do estivador **de texto para a fala.** | |
| `image.pullByHash`| Se a imagem do estivador é puxada por hash. Se `true` `image.hash` for necessário. | `false` |
| `image.hash`| O hash de imagem de **estivador de texto a fala.** Só usado `image.pullByHash: true`quando.  | |
| `image.args.eula`(obrigatório) | Indica que aceitou a licença. O único valor válido é`accept` | |
| `image.args.billing`(obrigatório) | O valor final da faturação URI está disponível na página de visão geral do portal Azure. | |
| `image.args.apikey`(obrigatório) | Usado para rastrear informações de faturação. ||
| `service.type` | O tipo de serviço Kubernetes do serviço **de texto-a-fala.** Consulte as instruções dos tipos de [serviço Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) para obter mais detalhes e verifique o suporte do fornecedor de nuvem. | `LoadBalancer` |
| `service.port`|  O porto do serviço **de texto-para-fala.** | `80` |
| `service.annotations` | As anotações **de texto para a fala** para os metadados do serviço. As anotações são pares de valor chave. <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | Se o [autoscaler de pod horizontal](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) está ativado. Se, `true` `text-to-speech-autoscaler` o será implantado no aglomerado kubernetes. | `true` |
| `service.podDisruption.enabled` | Se o Orçamento de Rutura do [Pod](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) está ativado. Se, `true` `text-to-speech-poddisruptionbudget` o será implantado no aglomerado kubernetes. | `true` |