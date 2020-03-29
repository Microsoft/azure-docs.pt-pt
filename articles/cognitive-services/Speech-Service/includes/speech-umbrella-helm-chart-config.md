---
title: Instalar recipientes de fala
titleSuffix: Azure Cognitive Services
description: Detalha as opções de configuração do gráfico do leme do guarda-chuva da fala.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: ed64412ccf9d192506fafe546b1ccee7941aa43a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73523638"
---
### <a name="speech-umbrella-chart"></a>Discurso (gráfico de guarda-chuva)

Os valores do gráfico "guarda-chuva" de alto nível sobrepõem-se aos valores subgráficos correspondentes. Portanto, todos os valores personalizados no local devem ser adicionados aqui.

|Parâmetro|Descrição|Predefinição|
| -- | -- | -- | -- |
| `speechToText.enabled` | Se o serviço **de fala-a-texto** está ativado. | `true` |
| `speechToText.verification.enabled` | Se a `helm test` capacidade do serviço **de fala-a-texto** está ativada. | `true` |
| `speechToText.verification.image.registry` | O repositório de `helm test` imagem estivador que usa para testar o serviço **de fala a texto.** O leme cria uma cápsula separada dentro do cluster para testar e retira a imagem *de utilização de teste* deste registo. | `docker.io` |
| `speechToText.verification.image.repository` | O repositório de `helm test` imagem estivador que usa para testar o serviço **de fala a texto.** A cápsula de teste de leme utiliza este repositório para puxar a imagem *de utilização do teste.* | `antsu/on-prem-client` |
| `speechToText.verification.image.tag` | A etiqueta de `helm test` imagem do estivador usada para o serviço **de fala-a-texto.** A cápsula de teste de leme utiliza esta etiqueta para puxar a imagem *de utilização do teste.* | `latest` |
| `speechToText.verification.image.pullByHash` | Se a imagem *de estivador de teste* é puxada por haxixe. `true`Se, `speechToText.verification.image.hash` deve ser adicionado, com valor de hash de imagem válido. | `false` |
| `speechToText.verification.image.arguments` | Os argumentos usados para executar a imagem *de estivador de teste.* O ventre de teste de leme `helm test`passa estes argumentos para o recipiente quando estiver em funcionamento . | `"./speech-to-text-client"`<br/> `"./audio/whatstheweatherlike.wav"` <br/> `"--expect=What's the weather like"`<br/>`"--host=$(SPEECH_TO_TEXT_HOST)"`<br/>`"--port=$(SPEECH_TO_TEXT_PORT)"` |
| `textToSpeech.enabled` | Se o serviço **de texto-a-fala** está ativado. | `true` |
| `textToSpeech.verification.enabled` | Se a `helm test` capacidade do serviço **de fala-a-texto** está ativada. | `true` |
| `textToSpeech.verification.image.registry` | O repositório de `helm test` imagem estivador que usa para testar o serviço **de fala a texto.** O leme cria uma cápsula separada dentro do cluster para testar e retira a imagem *de utilização de teste* deste registo. | `docker.io` |
| `textToSpeech.verification.image.repository` | O repositório de `helm test` imagem estivador que usa para testar o serviço **de fala a texto.** A cápsula de teste de leme utiliza este repositório para puxar a imagem *de utilização do teste.* | `antsu/on-prem-client` |
| `textToSpeech.verification.image.tag` | A etiqueta de `helm test` imagem do estivador usada para o serviço **de fala-a-texto.** A cápsula de teste de leme utiliza esta etiqueta para puxar a imagem *de utilização do teste.* | `latest` |
| `textToSpeech.verification.image.pullByHash` | Se a imagem *de estivador de teste* é puxada por haxixe. `true`Se, `textToSpeech.verification.image.hash` deve ser adicionado, com valor de hash de imagem válido. | `false` |
| `textToSpeech.verification.image.arguments` | Os argumentos para executar com a imagem *de estivador de teste.* O vagem de teste do leme `helm test`passa estes argumentos para o contentor quando estiver em execução . | `"./text-to-speech-client"`<br/> `"--input='What's the weather like'"` <br/> `"--host=$(TEXT_TO_SPEECH_HOST)"`<br/>`"--port=$(TEXT_TO_SPEECH_PORT)"` |