---
title: Instalar recipientes de fala
titleSuffix: Azure Cognitive Services
description: Detalha as opções de configuração do gráfico de leme do discurso ao texto.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/22/2019
ms.author: dapine
ms.openlocfilehash: 3f390affe7badb401277aa86d1867c763aa0ae3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "69971327"
---
### <a name="speech-to-text-sub-chart-chartsspeechtotext"></a>Discurso a Texto (sub-gráfico: gráficos/discursoToTexto)

Para anular o gráfico "guarda-chuva", `speechToText.` adicione o prefixo em qualquer parâmetro para torná-lo mais específico. Por exemplo, irá sobrepor-se ao `speechToText.numberOfConcurrentRequest` parâmetro `numberOfConcurrentRequest`correspondente, por exemplo, sobreposições.

|Parâmetro|Descrição|Predefinição|
| -- | -- | -- |
| `enabled` | Se o serviço **de fala-a-texto** está ativado. | `false` |
| `numberOfConcurrentRequest` | O número de pedidos simultâneos para o serviço **de fala-a-texto.** Este gráfico calcula automaticamente os recursos de CPU e memória, com base neste valor. | `2` |
| `optimizeForAudioFile`| Se o serviço precisa de otimizar para entrada de áudio através de ficheiros áudio. Se, `true`este gráfico irá atribuir mais recursos CPU ao serviço. | `false` |
| `image.registry`| O registo de imagem do **estivador de discurso a texto.** | `containerpreview.azurecr.io` |
| `image.repository` | O repositório de imagem **do estivador de discurso a texto.** | `microsoft/cognitive-services-speech-to-text` |
| `image.tag` | A etiqueta de imagem **do estivador de discurso a texto.** | `latest` |
| `image.pullSecrets` | Os segredos de imagem para puxar a imagem **do estivador do discurso ao texto.** | |
| `image.pullByHash`| Se a imagem do estivador é puxada por hash. Se `true` `image.hash` for necessário. | `false` |
| `image.hash`| O hash de imagem de estivador **de discurso a texto.** Só usado `image.pullByHash: true`quando.  | |
| `image.args.eula`(obrigatório) | Indica que aceitou a licença. O único valor válido é`accept` | |
| `image.args.billing`(obrigatório) | O valor final da faturação URI está disponível na página de visão geral do portal Azure. | |
| `image.args.apikey`(obrigatório) | Usado para rastrear informações de faturação. ||
| `service.type` | O tipo de serviço Kubernetes do serviço **de fala-a-texto.** Consulte as instruções dos tipos de [serviço Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) para obter mais detalhes e verifique o suporte do fornecedor de nuvem. | `LoadBalancer` |
| `service.port`|  O porto do serviço **de fala-a-texto.** | `80` |
| `service.annotations` | As anotações **de fala-a-texto** para os metadados do serviço. As anotações são pares de valor chave. <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | Se o [autoscaler de pod horizontal](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) está ativado. Se, `true` `speech-to-text-autoscaler` o será implantado no aglomerado kubernetes. | `true` |
| `service.podDisruption.enabled` | Se o Orçamento de Rutura do [Pod](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) está ativado. Se, `true` `speech-to-text-poddisruptionbudget` o será implantado no aglomerado kubernetes. | `true` |