---
title: Instalar recipientes de fala
titleSuffix: Azure Cognitive Services
description: Detalha as opções de configuração do gráfico de leme do discurso ao texto.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/15/2020
ms.author: trbye
ms.openlocfilehash: 267c2fb72b38053429019746a573c740d812c38c
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82608387"
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

#### <a name="sentiment-analysis-sub-chart-chartsspeechtotext"></a>Análise de sentimento (sub-gráfico: gráficos/speechToText)

A partir do v2.2.0 do recipiente de fala-a-texto, são utilizados os seguintes parâmetros para a análise de sentimentos utilizando a API text analytics.

|Parâmetro|Descrição|Valores|Predefinição|
| --- | --- | --- | --- |
|`textanalytics.enabled`| Se o serviço **de análise de texto** está ativado| verdadeiro/falso| `false`|
|`textanalytics.image.registry`| O registo de imagem do estivador **de análise de texto**| registo de imagem de estivador válido| |
|`textanalytics.image.repository`| O repositório de imagem de estivador **de análise de texto**| repositório de imagem de estiva válido| |
|`textanalytics.image.tag`| A etiqueta de imagem do estivador **de análise de texto**| etiqueta de imagem de estivador válido| |
|`textanalytics.image.pullSecrets`| Os segredos de imagem para puxar imagem de estivador **de análise de texto**| nome de segredos válidos| |
|`textanalytics.image.pullByHash`| Especifica se está a puxar a imagem do estivador por hash.  `yes`Se, `image.hash` é obrigado a ter também. Se, `no`definir como "falso". A predefinição é `false`.| verdadeiro/falso| `false`|
|`textanalytics.image.hash`| O hash de imagem de estivador **de análise de texto.** Use-o `image.pullByHash:true`apenas com.| hash de imagem de estiva válido | |
|`textanalytics.image.args.eula`| Um dos argumentos exigidos pelo recipiente **de análise de texto,** que indica que aceitou a licença. O valor desta opção `accept`deve ser: .| `accept`, se quiser usar o recipiente | |
|`textanalytics.image.args.billing`| Um dos argumentos exigidos pelo recipiente **de análise de texto,** que especifica o ponto final de faturação URI. O valor final da faturação URI está disponível na página de visão geral do portal Azure.|ponto final de faturação válido URI||
|`textanalytics.image.args.apikey`| Um dos argumentos exigidos pelo recipiente **de análise de texto,** que é utilizado para rastrear informações de faturação.| apikey válido||
|`textanalytics.cpuRequest`| O CPU solicitado para recipiente **de análise de texto**| int| `3000m`|
|`textanalytics.cpuLimit`| O CPU limitado para recipiente **de análise de texto**| | `8000m`|
|`textanalytics.memoryRequest`| A memória solicitada para o recipiente **de análise de texto**| | `3Gi`|
|`textanalytics.memoryLimit`| A memória limitada para recipiente **de análise de texto**| | `8Gi`|
|`textanalytics.service.sentimentURISuffix`| A análise de sentimento sufixo URI, todo`<service>``<port>`/`<sentimentURISuffix>`o URI está em formato "http://: ". | | `text/analytics/v3.0-preview/sentiment`|
|`textanalytics.service.type`| O tipo de serviço **de análise de texto** em Kubernetes. Ver tipos de [serviço kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) | tipo de serviço kubernetes válido | `LoadBalancer` |
|`textanalytics.service.port`| O porto do serviço **de análise de texto**| int| `50085`|
|`textanalytics.service.annotations`| As anotações que os utilizadores podem adicionar aos metadados do serviço **de análise de texto.** Por exemplo:<br/> **anotações:**<br/>`   `**alguma/anotação1: valor1**<br/>`  `**alguma/anotação2: valor2** | anotações, uma por cada linha| |
|`textanalytics.serivce.autoScaler.enabled`| Se o [autoscaler horizontal pod](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) está ativado. Se ativado, `text-analytics-autoscaler` será implantado no cluster Kubernetes | verdadeiro/falso| `true`|
|`textanalytics.service.podDisruption.enabled`| Se o Orçamento de Rutura do [Pod](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) está ativado. Se ativado, `text-analytics-poddisruptionbudget` será implantado no cluster Kubernetes| verdadeiro/falso| `true`|
