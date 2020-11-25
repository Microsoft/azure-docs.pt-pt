---
title: Instalar recipientes de discurso
titleSuffix: Azure Cognitive Services
description: Detalha as opções de configuração do gráfico do timão do guarda-chuva da fala.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 0257f3af44cc85d0a3656472db224ae5a7e19161
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002299"
---
### <a name="speech-umbrella-chart"></a>Discurso (gráfico de guarda-chuva)

Os valores na tabela "guarda-chuva" de nível superior sobrepõem-se aos valores correspondentes do sub-gráfico. Portanto, todos os valores personalizados no local devem ser adicionados aqui.

|Parâmetro|Descrição|Predefinição|
| -- | -- | -- | -- |
| `speechToText.enabled` | Se o serviço **de voz a texto** está ativado. | `true` |
| `speechToText.verification.enabled` | Se a `helm test` capacidade de serviço de voz para **texto** está ativada. | `true` |
| `speechToText.verification.image.registry` | O repositório de imagem estivador que `helm test` usa para testar o serviço de **fala-a-texto.** O leme cria uma cápsula separada dentro do cluster para testes e retira a imagem *de utilização* do teste a partir deste registo. | `docker.io` |
| `speechToText.verification.image.repository` | O repositório de imagem estivador que `helm test` usa para testar o serviço de **fala-a-texto.** A cápsula de teste de leme utiliza este repositório para puxar a imagem *de utilização do teste.* | `antsu/on-prem-client` |
| `speechToText.verification.image.tag` | A etiqueta de imagem do estivador usada `helm test` para o serviço de voz a **texto.** A cápsula de teste de leme utiliza esta etiqueta para puxar a imagem *de utilização do teste.* | `latest` |
| `speechToText.verification.image.pullByHash` | Se a imagem do estivador *de uso de teste* é puxada por haxixe. Se `true` , deve ser `speechToText.verification.image.hash` adicionado, com valor de haxixe de imagem válido. | `false` |
| `speechToText.verification.image.arguments` | Os argumentos usados para executar a imagem do estivador *de uso de teste.* A cápsula de teste de leme transmite estes argumentos ao recipiente durante a execução `helm test` . | `"./speech-to-text-client"`<br/> `"./audio/whatstheweatherlike.wav"` <br/> `"--expect=What's the weather like"`<br/>`"--host=$(SPEECH_TO_TEXT_HOST)"`<br/>`"--port=$(SPEECH_TO_TEXT_PORT)"` |
| `textToSpeech.enabled` | Se o serviço **de texto para voz** está ativado. | `true` |
| `textToSpeech.verification.enabled` | Se a `helm test` capacidade de serviço de voz para **texto** está ativada. | `true` |
| `textToSpeech.verification.image.registry` | O repositório de imagem estivador que `helm test` usa para testar o serviço de **fala-a-texto.** O leme cria uma cápsula separada dentro do cluster para testes e retira a imagem *de utilização* do teste a partir deste registo. | `docker.io` |
| `textToSpeech.verification.image.repository` | O repositório de imagem estivador que `helm test` usa para testar o serviço de **fala-a-texto.** A cápsula de teste de leme utiliza este repositório para puxar a imagem *de utilização do teste.* | `antsu/on-prem-client` |
| `textToSpeech.verification.image.tag` | A etiqueta de imagem do estivador usada `helm test` para o serviço de voz a **texto.** A cápsula de teste de leme utiliza esta etiqueta para puxar a imagem *de utilização do teste.* | `latest` |
| `textToSpeech.verification.image.pullByHash` | Se a imagem do estivador *de uso de teste* é puxada por haxixe. Se `true` , deve ser `textToSpeech.verification.image.hash` adicionado, com valor de haxixe de imagem válido. | `false` |
| `textToSpeech.verification.image.arguments` | Os argumentos para executar com a imagem do estivador *de uso de teste.* A cápsula de teste de leme passa estes argumentos para o contentor durante a execução `helm test` . | `"./text-to-speech-client"`<br/> `"--input='What's the weather like'"` <br/> `"--host=$(TEXT_TO_SPEECH_HOST)"`<br/>`"--port=$(TEXT_TO_SPEECH_PORT)"` |