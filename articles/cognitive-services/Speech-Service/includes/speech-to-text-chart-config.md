---
title: Instalar recipientes de discurso
titleSuffix: Azure Cognitive Services
description: Detalha as opções de configuração do gráfico de timoneiro de discurso para texto.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/05/2020
ms.author: trbye
ms.openlocfilehash: 85c4e0641e1989ddea6c8aa8b8a8895a966a5ddb
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002297"
---
### <a name="speech-to-text-sub-chart-chartsspeechtotext"></a>Discurso-a-Texto (sub-gráfico: gráficos/speechToText)

Para anular o gráfico "guarda-chuva", adicione o prefixo `speechToText.` em qualquer parâmetro para torná-lo mais específico. Por exemplo, irá sobrepor-se ao parâmetro correspondente, por exemplo, `speechToText.numberOfConcurrentRequest` sobreposições `numberOfConcurrentRequest` .

|Parâmetro|Descrição|Predefinição|
| -- | -- | -- |
| `enabled` | Se o serviço **de voz a texto** está ativado. | `false` |
| `numberOfConcurrentRequest` | O número de pedidos simultâneos para o serviço **de voz a texto.** Este gráfico calcula automaticamente os recursos de CPU e memória, com base neste valor. | `2` |
| `optimizeForAudioFile`| Se o serviço precisa de otimizar para a entrada de áudio através de ficheiros áudio. Se, `true` este gráfico atribuir mais recursos CPU ao serviço. | `false` |
| `image.registry`| O registo de imagem **de estivador de texto.** | `containerpreview.azurecr.io` |
| `image.repository` | O repositório de imagem de estivador de discurso para **texto.** | `microsoft/cognitive-services-speech-to-text` |
| `image.tag` | A etiqueta de imagem do estivador **de discurso para texto.** | `latest` |
| `image.pullSecrets` | Os segredos da imagem para puxar a imagem do estivador **de texto.** | |
| `image.pullByHash`| Se a imagem do estivador é puxada por haxixe. Se `true` for `image.hash` necessário. | `false` |
| `image.hash`| O hash de imagem de estivador **de discurso para texto.** Só é utilizado quando `image.pullByHash: true` .  | |
| `image.args.eula` (obrigatório) | Indica que aceitou a licença. O único valor válido é `accept` | |
| `image.args.billing` (obrigatório) | O valor URI do ponto final de faturação está disponível na página geral do portal Azure. | |
| `image.args.apikey` (obrigatório) | Usado para rastrear informação de faturação. ||
| `service.type` | O tipo de serviço Kubernetes do serviço **de voz a texto.** Consulte as [instruções dos tipos de serviço Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) para obter mais detalhes e verifique o suporte do fornecedor de nuvem. | `LoadBalancer` |
| `service.port`|  O porto do serviço **de fala-a-texto.** | `80` |
| `service.annotations` | As anotações **de discurso para texto** para os metadados de serviço. Anotações são pares de valor chave. <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | Se o [Pod Horizontal Autoscaler](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) está ativado. Se, `true` o ser implantado no cluster `speech-to-text-autoscaler` Kubernetes. | `true` |
| `service.podDisruption.enabled` | Se o [Orçamento de Interrupção](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) do Pod está ativado. Se, `true` o ser implantado no cluster `speech-to-text-poddisruptionbudget` Kubernetes. | `true` |

#### <a name="sentiment-analysis-sub-chart-chartsspeechtotext"></a>Análise de sentimento (sub-gráfico: gráficos/speechToText)

Começando com v2.2.0 do recipiente de voz para texto e v0.2.0 da tabela Helm, os seguintes parâmetros são utilizados para a análise de sentimentos utilizando a API text Analytics.

|Parâmetro|Descrição|Valores|Predefinição|
| --- | --- | --- | --- |
|`textanalytics.enabled`| Se o serviço **de análise de texto** está ativado| verdadeiro/falso| `false`|
|`textanalytics.image.registry`| O registo de imagem de estivador **de texto**| registo de imagem de estivador válido| |
|`textanalytics.image.repository`| O repositório de imagem de estivador **de texto**| repositório de imagem de estiva válido| |
|`textanalytics.image.tag`| A etiqueta de imagem de estivador **de texto**| marca de imagem de estiva válido| |
|`textanalytics.image.pullSecrets`| Os segredos da imagem para puxar imagem de estivador **de texto-análise**| nome de segredos válidos| |
|`textanalytics.image.pullByHash`| Especifica se está a puxar a imagem do estivador por haxixe.  Se `yes` for necessário ter `image.hash` também. Se `no` , defini-lo como 'falso'. A predefinição é `false`.| verdadeiro/falso| `false`|
|`textanalytics.image.hash`| O **haxixe de** imagem de estivador de texto. Só o utilize com `image.pullByHash:true` .| haxixe de imagem de estiva válido | |
|`textanalytics.image.args.eula`| Um dos argumentos necessários por recipiente **de análise de texto,** o que indica que aceitou a licença. O valor desta opção deve ser: `accept` .| `accept`, se quiser usar o recipiente | |
|`textanalytics.image.args.billing`| Um dos argumentos necessários por recipiente **de análise de texto,** que especifica o ponto final de faturação URI. O valor URI do ponto final de faturação está disponível na página geral do portal Azure.|URI de ponto final de faturação válido||
|`textanalytics.image.args.apikey`| Um dos argumentos necessários por recipiente **de análise de texto,** que é usado para rastrear informações de faturação.| apikey válido||
|`textanalytics.cpuRequest`| O CPU solicitado para o recipiente **de análise de texto**| int| `3000m`|
|`textanalytics.cpuLimit`| O CPU limitado para recipiente **de análise de texto**| | `8000m`|
|`textanalytics.memoryRequest`| A memória solicitada para o recipiente **de análise de texto**| | `3Gi`|
|`textanalytics.memoryLimit`| A memória limitada para recipiente **de análise de texto**| | `8Gi`|
|`textanalytics.service.sentimentURISuffix`| A análise de sentimento sufixo URI, todo o URI está em formato "http:// `<service>` : `<port>` / `<sentimentURISuffix>` ". | | `text/analytics/v3.0-preview/sentiment`|
|`textanalytics.service.type`| O tipo de serviço **de análise de texto** em Kubernetes. Ver [tipos de serviço Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) | tipo de serviço Kubernetes válido | `LoadBalancer` |
|`textanalytics.service.port`| A porta do serviço **de análise de texto**| int| `50085`|
|`textanalytics.service.annotations`| As anotações que os utilizadores podem adicionar aos metadados do serviço **de análise de texto.** Por exemplo:<br/> **anotações:**<br/>`   `**some/anotação1: valor1**<br/>`  `**alguma/anotação2: valor2** | anotações, uma por cada linha| |
|`textanalytics.serivce.autoScaler.enabled`| Se o [Pod Horizontal Autoscaler](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) está ativado. Se ativado, `text-analytics-autoscaler` será implantado no cluster Kubernetes | verdadeiro/falso| `true`|
|`textanalytics.service.podDisruption.enabled`| Se [o Orçamento de Interrupção do Pod](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) está ativado. Se ativado, `text-analytics-poddisruptionbudget` será implantado no cluster Kubernetes| verdadeiro/falso| `true`|
