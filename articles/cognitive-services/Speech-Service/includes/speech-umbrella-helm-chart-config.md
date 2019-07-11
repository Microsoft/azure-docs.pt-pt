---
title: Instalar os contentores de voz
titleSuffix: Azure Cognitive Services
description: Fornece detalhes sobre as opções de configuração de gráfico de helm de síntese de fala.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: ed64412ccf9d192506fafe546b1ccee7941aa43a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717247"
---
### <a name="speech-umbrella-chart"></a>Conversão de voz (gráfico de síntese)

Valores no gráfico de nível superior "guarda-chuva" substituem os valores de gráfico secundárias correspondente. Por conseguinte, todos os locais valores personalizados devem ser adicionados aqui.

|Parâmetro|Descrição|Predefinição|
| -- | -- | -- | -- |
| `speechToText.enabled` | Se o **voz em texto** serviço está ativado. | `true` |
| `speechToText.verification.enabled` | Se o `helm test` capacidade para **voz em texto** serviço está ativado. | `true` |
| `speechToText.verification.image.registry` | O repositório de imagens do docker que `helm test` para testar **voz em texto** serviço. Helm cria o pod separado dentro do cluster para fins de teste e extrai o *utilização de teste* imagem a partir deste registo. | `docker.io` |
| `speechToText.verification.image.repository` | O repositório de imagens do docker que `helm test` para testar **voz em texto** serviço. Pod de teste do Helm utiliza este repositório para extrair *utilização de teste* imagem. | `antsu/on-prem-client` |
| `speechToText.verification.image.tag` | A tag de imagem de docker utilizado com `helm test` para **voz em texto** serviço. Pod de teste do Helm utiliza esta etiqueta para extrair *utilização de teste* imagem. | `latest` |
| `speechToText.verification.image.pullByHash` | Se o *utilização de teste* imagem do docker é solicitada por hash. Se `true`, `speechToText.verification.image.hash` devem ser adicionados, com valor de hash de imagem válido. | `false` |
| `speechToText.verification.image.arguments` | Os argumentos utilizados para executar o *utilização de teste* imagem do docker. Pod de teste do Helm passa esses argumentos para o contentor ao executar `helm test`. | `"./speech-to-text-client"`<br/> `"./audio/whatstheweatherlike.wav"` <br/> `"--expect=What's the weather like"`<br/>`"--host=$(SPEECH_TO_TEXT_HOST)"`<br/>`"--port=$(SPEECH_TO_TEXT_PORT)"` |
| `textToSpeech.enabled` | Se o **voz** serviço está ativado. | `true` |
| `textToSpeech.verification.enabled` | Se o `helm test` capacidade para **voz em texto** serviço está ativado. | `true` |
| `textToSpeech.verification.image.registry` | O repositório de imagens do docker que `helm test` para testar **voz em texto** serviço. Helm cria o pod separado dentro do cluster para fins de teste e extrai o *utilização de teste* imagem a partir deste registo. | `docker.io` |
| `textToSpeech.verification.image.repository` | O repositório de imagens do docker que `helm test` para testar **voz em texto** serviço. Pod de teste do Helm utiliza este repositório para extrair *utilização de teste* imagem. | `antsu/on-prem-client` |
| `textToSpeech.verification.image.tag` | A tag de imagem de docker utilizado com `helm test` para **voz em texto** serviço. Pod de teste do Helm utiliza esta etiqueta para extrair *utilização de teste* imagem. | `latest` |
| `textToSpeech.verification.image.pullByHash` | Se o *utilização de teste* imagem do docker é solicitada por hash. Se `true`, `textToSpeech.verification.image.hash` devem ser adicionados, com valor de hash de imagem válido. | `false` |
| `textToSpeech.verification.image.arguments` | Os argumentos para executar com o *utilização de teste* imagem do docker. O pod de teste do helm passa esses argumentos para contentor, ao executar `helm test`. | `"./text-to-speech-client"`<br/> `"--input='What's the weather like'"` <br/> `"--host=$(TEXT_TO_SPEECH_HOST)"`<br/>`"--port=$(TEXT_TO_SPEECH_PORT)"` |