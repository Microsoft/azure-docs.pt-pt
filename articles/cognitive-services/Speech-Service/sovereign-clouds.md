---
title: Nuvens Soberanas - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como usar nuvens soberanas
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.custom: references_regions
ms.date: 01/07/2021
ms.author: alexeyo
ms.openlocfilehash: f30b1f0f14bba54b8b4fcd7c5190f3c533f199a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "98061757"
---
# <a name="speech-services-in-sovereign-clouds"></a>Serviços de fala em nuvens soberanas

## <a name="azure-government-united-states"></a>Governo de Azure (Estados Unidos)

Disponível apenas para entidades governamentais dos EUA e seus parceiros. Veja mais informações sobre o Governo Azure [aqui](../../azure-government/documentation-government-welcome.md) e [aqui.](../../azure-government/compare-azure-government-global-azure.md)

- **Portal Azure:**
  - [https://portal.azure.us/](https://portal.azure.us/)
- **Regiões:**
  - US Gov - Arizona
  - US Gov - Virginia
- **Níveis de preços disponíveis:**
  - Grátis (F0) e Standard (S0). Veja mais detalhes [aqui](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)
- **Características suportadas:**
  - Conversão de voz em texto
    - Discurso personalizado (Modelo Acústico (AM) e Adaptação do Modelo de Linguagem (LM)
      - [Speech Studio](https://speech.azure.us/)
  - Conversão de texto em voz
    - Voz padrão
    - Voz neural
  - Tradutor de fala
- **Características não suportadas:**
  - Voz Personalizada
- **Línguas apoiadas:**
  - Veja a lista de línguas apoiadas [aqui](language-support.md)

### <a name="endpoint-information"></a>Informações sobre ponto final

Esta secção contém informações sobre os serviços de voz para a utilização com [O SDK de voz,](speech-sdk.md) [a API de voz para texto](rest-speech-to-text.md)e a [API DE REPOUSO text-to-speech](rest-text-to-speech.md).

#### <a name="speech-services-rest-api"></a>Serviços de Fala REST API

Serviços de Fala REST Os pontos finais da API no Governo Azure têm o seguinte formato:

|  REST Tipo/operação API | Formato endpoint |
|--|--|
| Ficha de acesso | `https://<REGION_IDENTIFIER>.api.cognitive.microsoft.us/sts/v1.0/issueToken`
| [A API DE REPOUSO de expressão em texto v3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) | `https://<REGION_IDENTIFIER>.api.cognitive.microsoft.us/<URL_PATH>` |
| [API DE REPOUSO de voz para texto para áudio curto](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) | `https://<REGION_IDENTIFIER>.stt.speech.azure.us/<URL_PATH>` |
| [API REST de conversão de texto em voz](rest-text-to-speech.md) | `https://<REGION_IDENTIFIER>.tts.speech.azure.us/<URL_PATH>` |

`<REGION_IDENTIFIER>`Substitua-a pelo identificador correspondente à região da sua subscrição a partir desta tabela:

|                     | Identificador da região |
|--|--|
| **US Gov - Arizona**  | `usgovarizona` |
| **US Gov - Virginia** | `usgovvirginia` |

#### <a name="speech-sdk"></a>API de Voz

Para o Discurso SDK em nuvens soberanas, você precisa usar a instantânea "do anfitrião" da `SpeechConfig` classe ou `--host` opção de Speech [CLI](spx-overview.md). (Também pode usar instantâneo "do ponto final" e `--endpoint` Opção CLI da fala).

`SpeechConfig` a classe deve ser instantânea assim:
```csharp
var config = SpeechConfig.FromHost(usGovHost, subscriptionKey);
```
O CLI da fala deve ser usado assim (note a `--host` opção):
```dos
spx recognize --host "usGovHost" --file myaudio.wav
```
`subscriptionKey`Substitua-a pela tecla de recursos da Fala. `usGovHost`Substitua-a pela expressão correspondente à oferta de serviço exigida e à região da sua subscrição a partir desta tabela:

|  Oferta de região / serviço | Expressão do anfitrião |
|--|--|
| **US Gov - Arizona** | |
| Conversão de voz em texto | `wss://usgovarizona.stt.speech.azure.us` |
| Conversão de Texto em Voz | `https://usgovarizona.tts.speech.azure.us` |
| **US Gov - Virginia** | |
| Conversão de voz em texto | `wss://usgovvirginia.stt.speech.azure.us` |
| Conversão de Texto em Voz | `https://usgovvirginia.tts.speech.azure.us` |


## <a name="azure-china"></a>Azure China

Disponível para organizações com presença comercial na China. Veja mais informações sobre a Azure China [aqui.](/azure/china/overview-operations) 


- **Portal Azure:**
  - [https://portal.azure.cn/](https://portal.azure.cn/)
- **Regiões:**
  - China Leste 2
- **Níveis de preços disponíveis:**
  - Grátis (F0) e Standard (S0). Veja mais detalhes [aqui](https://www.azure.cn/pricing/details/cognitive-services/index.html)
- **Características suportadas:**
  - Conversão de voz em texto
    - Discurso personalizado (Modelo Acústico (AM) e Adaptação do Modelo de Linguagem (LM)
      - [Speech Studio](https://speech.azure.cn/)
  - Conversão de texto em voz
    - Voz padrão
    - Voz neural
  - Tradutor de fala
- **Características não suportadas:**
  - Voz Personalizada
- **Línguas apoiadas:**
  - Veja a lista de línguas apoiadas [aqui](language-support.md)

### <a name="endpoint-information"></a>Informações sobre ponto final

Esta secção contém informações sobre os serviços de voz para a utilização com [O SDK de voz,](speech-sdk.md) [a API de voz para texto](rest-speech-to-text.md)e a [API DE REPOUSO text-to-speech](rest-text-to-speech.md).

#### <a name="speech-services-rest-api"></a>Serviços de Fala REST API

Serviços de Fala REST Os pontos finais da API na Azure China têm o seguinte formato:

|  REST Tipo/operação API | Formato endpoint |
|--|--|
| Ficha de acesso | `https://<REGION_IDENTIFIER>.api.cognitive.azure.cn/sts/v1.0/issueToken`
| [A API DE REPOUSO de expressão em texto v3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) | `https://<REGION_IDENTIFIER>.api.cognitive.azure.cn/<URL_PATH>` |
| [API DE REPOUSO de voz para texto para áudio curto](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) | `https://<REGION_IDENTIFIER>.stt.speech.azure.cn/<URL_PATH>` |
| [API REST de conversão de texto em voz](rest-text-to-speech.md) | `https://<REGION_IDENTIFIER>.tts.speech.azure.cn/<URL_PATH>` |

`<REGION_IDENTIFIER>`Substitua-a pelo identificador correspondente à região da sua subscrição a partir desta tabela:

|                     | Identificador da região |
|--|--|
| **China Leste 2**  | `chinaeast2` |

#### <a name="speech-sdk"></a>API de Voz

Para o Discurso SDK em nuvens soberanas, você precisa usar a instantânea "do anfitrião" da `SpeechConfig` classe ou `--host` opção de Speech [CLI](spx-overview.md). (Também pode usar instantâneo "do ponto final" e `--endpoint` Opção CLI da fala).

`SpeechConfig` a classe deve ser instantânea assim:
```csharp
var config = SpeechConfig.FromHost(azCnHost, subscriptionKey);
```
O CLI da fala deve ser usado assim (note a `--host` opção):
```dos
spx recognize --host "azCnHost" --file myaudio.wav
```
`subscriptionKey`Substitua-a pela tecla de recursos da Fala. `azCnHost`Substitua-a pela expressão correspondente à oferta de serviço exigida e à região da sua subscrição a partir desta tabela:

|  Oferta de região / serviço | Expressão do anfitrião |
|--|--|
| **China Leste 2** | |
| Conversão de voz em texto | `wss://chinaeast2.stt.speech.azure.cn` |
| Conversão de Texto em Voz | `https://chinaeast2.tts.speech.azure.cn` |