---
title: Etiquetas de imagem de contentor dos Serviços Cognitivos
titleSuffix: Azure Cognitive Services
description: Uma lista completa de todas as etiquetas de imagem do serviço cognitivo.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 11/17/2020
ms.author: aahi
ms.openlocfilehash: ba51776942ad28fc8d4b0db7dd2d0e162e5322b5
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94743342"
---
# <a name="azure-cognitive-services-container-image-tags-and-release-notes"></a>Etiquetas de imagem de contentores Azure Cognitive Services e notas de lançamento

A Azure Cognitive Services oferece muitas imagens de contentores. Os registos dos contentores e os repositórios correspondentes variam entre as imagens dos contentores. Cada nome de imagem do recipiente oferece várias tags. Uma etiqueta de imagem de recipiente é um mecanismo de versão da imagem do recipiente. Este artigo destina-se a ser usado como uma referência abrangente para a listagem de todas as imagens dos contentores dos Serviços Cognitivos e suas etiquetas disponíveis.

> [!TIP]
> Ao [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) utilizar, preste muita atenção ao invólucro do registo do contentor, repositório, nome de imagem do recipiente e etiqueta correspondente - uma vez que são **sensíveis ao caso**.

## <a name="anomaly-detector"></a>Detetor de Anomalias

A imagem do detetor [de anomalias][ad-containers] pode ser encontrada no sindicato do registo do `mcr.microsoft.com` contentor. Reside dentro do `azure-cognitive-services/decision` repositório e é `anomaly-detector` nomeado. O nome de imagem do recipiente totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector` .

Esta imagem do recipiente tem as seguintes etiquetas disponíveis. Também pode encontrar uma lista completa de [tags no MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/decision/anomaly-detector/tags/list).

# <a name="latest-version"></a>[Versão mais recente](#tab/current)

| Etiquetas de imagem                    | Notas |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013560003-amd64-preview` |      |

# <a name="previous-versions"></a>[Versões anteriores](#tab/previous)

| Etiquetas de imagem                    | Notas |
|-------------------------------|:------|
| `1.1.012300001-amd64-preview` |       |

---

## <a name="read-ocr-optical-character-recognition"></a>Ler OCR (Reconhecimento de Caracteres Óticos)

A imagem do contentor OCR da [Visão de Computador][cv-containers] pode ser encontrada no sindicato do registo do `mcr.microsoft.com` contentor. Reside dentro do `azure-cognitive-services` repositório e é `read` nomeado. O nome de imagem do recipiente totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/vision/read` .

Esta imagem do recipiente tem as seguintes etiquetas disponíveis. Também pode encontrar uma lista completa de [tags no MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/vision/read/tags/list).

# <a name="latest-version"></a>[Versão mais recente](#tab/current)

Notas de lançamento `v2.0.013250001-amd64-preview` para:

* Diminua ainda mais o uso da memória para o recipiente.
* É necessária cache externa para a configuração de várias cápsulas. Por exemplo, configurar Redis para o caching.
* Resultados em falta fixos quando a cache redis é configurada e `ResultExpirationPeriod` está definida para 0.
* Remova a limitação do tamanho do corpo do pedido de 26MB. O recipiente pode agora aceitar >ficheiros de 26MB.
* Adicione o carimbo de tempo e construa a versão para o registo de consolas.

| Etiquetas de imagem                    | Notas |
|-------------------------------|:------|
| `latest`                      |       |
| `2.0.013250001-amd64-preview` |  |

# <a name="previous-versions"></a>[Versões anteriores](#tab/previous)

Notas de lançamento para `1.1.013050001-amd64-preview`

* Configuração de inicialização de `ReadEngineConfig:ResultExpirationPeriod` contentores adicionada para especificar quando o sistema deve limpar os resultados do reconhecimento. 
    * A definição é em horas, e o valor predefinido é de 48 horas.
    * A definição pode reduzir a utilização da memória para o armazenamento de resultados, especialmente quando é utilizado o armazenamento em memória do recipiente.
    * Exemplo 1. ReadEngineConfig:ResultExpirationPeriod=1, o sistema limpará o resultado do reconhecimento 1hr após o processo.
    * Se esta configuração estiver definida para 0, o sistema limpará o resultado do reconhecimento após a recuperação do resultado.

* Corrigiu um erro de servidor interno de 500 quando um formato de imagem inválido é passado para o sistema. Irá agora devolver um erro de 400:

    ```json
    {
        "error": {
        "code": "InvalidImageSize",
        "message": "Image must be between 1024 and 209715200 bytes."
        }
    }
    ```

| Etiquetas de imagem                    | Notas |
|-------------------------------|:------|
| `1.1.013050001-amd64-preview` |       |
| `1.1.011580001-amd64-preview` |       |
| `1.1.009920003-amd64-preview` |       |
| `1.1.009910003-amd64-preview` |       |

---


## <a name="form-recognizer"></a>Reconhecedor de Formato

A imagem do contentor [do "Reconhecimento de Formulários"][fr-containers] pode ser encontrada no sindicato do registo do `mcr.microsoft.com` contentor. Reside dentro do `azure-cognitive-services/custom-form` repositório e é `labeltool` nomeado. O nome de imagem do recipiente totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool` .

Esta imagem do recipiente tem as seguintes etiquetas disponíveis. Também pode encontrar uma lista completa de [tags no MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/custom-form/labeltool/tags/list).

# <a name="latest-version"></a>[Versão mais recente](#tab/current)

| Etiquetas de imagem                    | Notas |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |


# <a name="previous-versions"></a>[Versões anteriores](#tab/previous)

| Etiquetas de imagem                    | Notas |
|-------------------------------|:------|
| `1.1.008640001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |

---

## <a name="language-understanding-luis"></a>Compreensão de Idiomas (LUIS)

A imagem do contentor [LUIS][lu-containers] pode ser encontrada no sindicato do `mcr.microsoft.com` registo de contentores. Reside dentro do `azure-cognitive-services/language` repositório e é `luis` nomeado. O nome de imagem do recipiente totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/language/luis` .

Esta imagem do recipiente tem as seguintes etiquetas disponíveis. Também pode encontrar uma lista completa de [tags no MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/language/luis/tags/list).

# <a name="latest-version"></a>[Versão mais recente](#tab/current)

| Etiquetas de imagem                    | Notas |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.012280003-amd64-preview` |       |


# <a name="previous-version"></a>[Versão anterior](#tab/previous)

| Etiquetas de imagem                    | Notas |
|-------------------------------|:------|
| `1.1.012130003-amd64-preview` |       |

---

## <a name="custom-speech-to-text"></a>Discurso-a-texto personalizado

A imagem do recipiente [personalizado discurso-a-texto][sp-cstt] pode ser encontrada no sindicato do registo do `mcr.microsoft.com` contentor. Reside dentro do `azure-cognitive-services/speechservices/` repositório e é `custom-speech-to-text` nomeado. O nome de imagem do recipiente totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text` . Também pode encontrar uma lista completa de [tags no MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/custom-speech-to-text/tags/list).


# <a name="latest-version"></a>[Versão mais recente](#tab/current)

Nota de lançamento `2.6.0-amd64` para:

**Funcionalidades**
* Suporte para lista de frases v2 
* As listas de frases são suportadas nos seguintes locais:
    * en-au
    * en-ca
    * en-gb
    * en-in
    * en-nz
    * pt-pt
    * zh-cn
* Suporte para download personalizado do modelo base. 
    * Usar `BaseModelLocale=<locale>` com o `docker run` comando
* Totalmente migrado para .NET 3.1

**Correções**
* Corrigiu um problema em que a pontuação de confiança era sempre 1 no modo de diarização
* Migrado para o TextAnalytics 3.0 api

Note que devido às listas de frases incluídas, o tamanho desta imagem do recipiente aumentou.

| Etiquetas de imagem                    | Notas |
|-------------------------------|:------|
| `latest`                      |       |
| `2.6.0-amd64`                 |       |


# <a name="previous-version"></a>[Versão anterior](#tab/previous)

Nota de lançamento `2.5.0-amd64` para:

**Funcionalidades**
* Suporte pronúncia personalizada em modelos personalizados
* Apoiar Azure e Azure US Government Cloud

**Correções**
* Correção executada como problema de utilizador não raiz no modo de diarização

| Etiquetas de imagem                    | Notas               |
|-------------------------------|:--------------------|
| `2.5.0-amd64`                 |   1ª versão GA    |

---

## <a name="custom-text-to-speech"></a>Texto-a-discurso personalizado

A imagem do recipiente [de texto para a fala personalizado][sp-ctts] pode ser encontrada no sindicato do registo do `mcr.microsoft.com` contentor. Reside dentro do `azure-cognitive-services/speechservices/` repositório e é `custom-text-to-speech` nomeado. O nome de imagem do recipiente totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech` . Também pode encontrar uma lista completa de [tags no MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/custom-text-to-speech/tags/list).


# <a name="latest-version"></a>[Versão mais recente](#tab/current)

Nota de lançamento `1.8.0-amd64` para:

**Funcionalidades**
* Totalmente migrado para .NET 3.1

| Etiquetas de imagem                    | Notas |
|-------------------------------|:------|
| `latest`                      |       |
| `1.8.0-amd64`                 |       |


# <a name="previous-version"></a>[Versão anterior](#tab/previous)

Nota de lançamento `1.7.0-amd64` para:

**Funcionalidade**
* Parcialmente migrado para .NET 3.1

| Etiquetas de imagem                    | Notas               |
|-------------------------------|:--------------------|
| `1.7.0-amd64`                 |   1ª versão GA    |

---

## <a name="speech-to-text"></a>Conversão de voz em texto

A imagem do contentor [discurso-a-texto][sp-stt] pode ser encontrada no sindicato do registo do `mcr.microsoft.com` contentor. Reside dentro do `azure-cognitive-services/speechservices/` repositório e é `speech-to-text` nomeado. O nome de imagem do recipiente totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text` . Pode encontrar uma lista completa de [etiquetas no MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/speech-to-text/tags/list).

Desde discurso-a-texto v2.5.0, as imagens são apoiadas na região *do governo dos EUA Virginia.* Por favor, use as chaves de ponta de faturação do *governo dos EUA* e apiítmo ao usar esta região.

# <a name="latest-version"></a>[Versão mais recente](#tab/current)

Nota de lançamento `2.6.0-amd64-<locale>` para:

**Funcionalidades**
* Atualizado para os modelos mais recentes e totalmente migrado para .NET 3.1
* Suporte para lista de frases v2
* As listas de frases são suportadas nos seguintes locais:
    * en-au
    * en-ca
    * en-gb
    * en-in
    * en-nz
    * pt-pt
    * zh-cn
* Apoio a novas localidades `cs-CZ` 
    * Atualmente, a capitalização e a pontuação não são apoiadas.

**Correções**
* Corrige um problema onde as pontuações de confiança foram sempre 1 no modo de Diarização
* Utilização migrada do TextAnalytics 3.0 API

Note que devido às listas de frases incluídas, o tamanho desta imagem do recipiente aumentou. 

| Etiquetas de imagem                    | Notas                                                                                                | 
|-------------------------------|:-----------------------------------------------------------------------------------------------------|
| `latest`                      | Imagem de recipiente com o `en-US` local.                                                             |
| `2.6.0-amd64-<locale>`        | Substitua `<locale>` por um dos locais disponíveis, listado abaixo. Por exemplo, `2.6.0-amd64-en-us`. |

Este recipiente tem os seguintes locais disponíveis.

| Local para v2.6.0           | Notas                                    |
|-----------------------------|:-----------------------------------------|
| `ar-ae`                     | Imagem de recipiente com o `ar-AE` local. |
| `ar-eg`                     | Imagem de recipiente com o `ar-EG` local. |
| `ar-kw`                     | Imagem de recipiente com o `ar-KW` local. |
| `ar-qa`                     | Imagem de recipiente com o `ar-QA` local. |
| `ar-sa`                     | Imagem de recipiente com o `ar-SA` local. |
| `ca-es`                     | Imagem de recipiente com o `ca-ES` local. |
| `cs-cz`                     | Imagem de recipiente com o `cs-CZ` local. |
| `da-dk`                     | Imagem de recipiente com o `da-DK` local. |
| `de-de`                     | Imagem de recipiente com o `de-DE` local. |
| `en-au`                     | Imagem de recipiente com o `en-AU` local. |
| `en-ca`                     | Imagem de recipiente com o `en-CA` local. |
| `en-gb`                     | Imagem de recipiente com o `en-GB` local. |
| `en-in`                     | Imagem de recipiente com o `en-IN` local. |
| `en-nz`                     | Imagem de recipiente com o `en-NZ` local. |
| `en-us`                     | Imagem de recipiente com o `en-US` local. |
| `es-es`                     | Imagem de recipiente com o `es-ES` local. |
| `es-mx`                     | Imagem de recipiente com o `es-MX` local. |
| `fi-fi`                     | Imagem de recipiente com o `fi-FI` local. |
| `fr-ca`                     | Imagem de recipiente com o `fr-CA` local. |
| `fr-fr`                     | Imagem de recipiente com o `fr-FR` local. |
| `gu-in`                     | Imagem de recipiente com o `gu-IN` local. |
| `hi-in`                     | Imagem de recipiente com o `hi-IN` local. |
| `it-it`                     | Imagem de recipiente com o `it-IT` local. |
| `ja-jp`                     | Imagem de recipiente com o `ja-JP` local. |
| `ko-kr`                     | Imagem de recipiente com o `ko-KR` local. |
| `mr-in`                     | Imagem de recipiente com o `mr-IN` local. |
| `nb-no`                     | Imagem de recipiente com o `nb-NO` local. |
| `nl-nl`                     | Imagem de recipiente com o `nl-NL` local. |
| `pl-pl`                     | Imagem de recipiente com o `pl-PL` local. |
| `pt-br`                     | Imagem de recipiente com o `pt-BR` local. |
| `pt-pt`                     | Imagem de recipiente com o `pt-PT` local. |
| `ru-ru`                     | Imagem de recipiente com o `ru-RU` local. |
| `sv-se`                     | Imagem de recipiente com o `sv-SE` local. |
| `ta-in`                     | Imagem de recipiente com o `ta-IN` local. |
| `te-in`                     | Imagem de recipiente com o `te-IN` local. |
| `th-th`                     | Imagem de recipiente com o `th-TH` local. |
| `tr-tr`                     | Imagem de recipiente com o `tr-TR` local. |
| `zh-cn`                     | Imagem de recipiente com o `zh-CN` local. |
| `zh-hk`                     | Imagem de recipiente com o `zh-HK` local. |
| `zh-tw`                     | Imagem de recipiente com o `zh-TW` local. |


# <a name="previous-version"></a>[Versão anterior](#tab/previous)

Nota de lançamento `2.5.0-amd64-<locale>` para:

**Funcionalidades**
* Apoio à Nuvem do Governo dos EUA do Azure

**Correções**
* Corrige um problema com a execução como utilizador não-raiz no modo de diarização

| Etiquetas de imagem                  | Notas                                    |
|-----------------------------|:-----------------------------------------|
| `2.5.0-amd64-<locale>`      | Substitua `<locale>` por um dos locais disponíveis, listado abaixo. Por exemplo, `2.5.0-amd64-en-us`.  |

Este recipiente tem os seguintes locais disponíveis.

| Local para v2.5.0           | Notas                                    |
|-----------------------------|:-----------------------------------------|
| `ar-ae`                     | Imagem de recipiente com o `ar-AE` local. |
| `ar-eg`                     | Imagem de recipiente com o `ar-EG` local. |
| `ar-kw`                     | Imagem de recipiente com o `ar-KW` local. |
| `ar-qa`                     | Imagem de recipiente com o `ar-QA` local. |
| `ar-sa`                     | Imagem de recipiente com o `ar-SA` local. |
| `ca-es`                     | Imagem de recipiente com o `ca-ES` local. |
| `da-dk`                     | Imagem de recipiente com o `da-DK` local. |
| `de-de`                     | Imagem de recipiente com o `de-DE` local. |
| `en-au`                     | Imagem de recipiente com o `en-AU` local. |
| `en-ca`                     | Imagem de recipiente com o `en-CA` local. |
| `en-gb`                     | Imagem de recipiente com o `en-GB` local. |
| `en-in`                     | Imagem de recipiente com o `en-IN` local. |
| `en-nz`                     | Imagem de recipiente com o `en-NZ` local. |
| `en-us`                     | Imagem de recipiente com o `en-US` local. |
| `es-es`                     | Imagem de recipiente com o `es-ES` local. |
| `es-mx`                     | Imagem de recipiente com o `es-MX` local. |
| `fi-fi`                     | Imagem de recipiente com o `fi-FI` local. |
| `fr-ca`                     | Imagem de recipiente com o `fr-CA` local. |
| `fr-fr`                     | Imagem de recipiente com o `fr-FR` local. |
| `gu-in`                     | Imagem de recipiente com o `gu-IN` local. |
| `hi-in`                     | Imagem de recipiente com o `hi-IN` local. |
| `it-it`                     | Imagem de recipiente com o `it-IT` local. |
| `ja-jp`                     | Imagem de recipiente com o `ja-JP` local. |
| `ko-kr`                     | Imagem de recipiente com o `ko-KR` local. |
| `mr-in`                     | Imagem de recipiente com o `mr-IN` local. |
| `nb-no`                     | Imagem de recipiente com o `nb-NO` local. |
| `nl-nl`                     | Imagem de recipiente com o `nl-NL` local. |
| `pl-pl`                     | Imagem de recipiente com o `pl-PL` local. |
| `pt-br`                     | Imagem de recipiente com o `pt-BR` local. |
| `pt-pt`                     | Imagem de recipiente com o `pt-PT` local. |
| `ru-ru`                     | Imagem de recipiente com o `ru-RU` local. |
| `sv-se`                     | Imagem de recipiente com o `sv-SE` local. |
| `ta-in`                     | Imagem de recipiente com o `ta-IN` local. |
| `te-in`                     | Imagem de recipiente com o `te-IN` local. |
| `th-th`                     | Imagem de recipiente com o `th-TH` local. |
| `tr-tr`                     | Imagem de recipiente com o `tr-TR` local. |
| `zh-cn`                     | Imagem de recipiente com o `zh-CN` local. |
| `zh-hk`                     | Imagem de recipiente com o `zh-HK` local. |
| `zh-tw`                     | Imagem de recipiente com o `zh-TW` local. |

---

## <a name="text-to-speech"></a>Conversão de texto em voz

A imagem do contentor [text-to-speech][sp-tts] pode ser encontrada no sindicato do registo do `mcr.microsoft.com` contentor. Reside dentro do `azure-cognitive-services/speechservices/` repositório e é `text-to-speech` nomeado. O nome de imagem do recipiente totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech` .

Esta imagem do recipiente tem as seguintes etiquetas disponíveis. Também pode encontrar uma lista completa de [tags no MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/text-to-speech/tags/list).


# <a name="latest-version"></a>[Versão mais recente](#tab/current)

Nota de lançamento `1.8.0-amd64-<locale-and-voice>` para:

**Funcionalidade**
* Totalmente migrado para .NET 3.1

| Etiquetas de imagem                                  | Notas                                                                                                         |
|---------------------------------------------|:--------------------------------------------------------------------------------------------------------------|
| `latest`                                    | Imagem de recipiente com o `en-US` local e `en-US-AriaRUS` a voz.                                            | 
| `1.8.0-amd64-<locale-and-voice>`            | Substitua `<locale>` por um dos locais disponíveis, listado abaixo. Por exemplo, `1.8.0-amd64-en-us-ariarus`.  |


| Locais para v1.8.0                          | Notas                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `ar-eg-hoda`                                | Imagem de recipiente com o `ar-EG` local e `ar-EG-Hoda` a voz.            |
| `ar-sa-naayf`                               | Imagem de recipiente com o `ar-SA` local e `ar-SA-Naayf` a voz.           |
| `bg-bg-ivan`                                | Imagem de recipiente com o `bg-BG` local e `bg-BG-Ivan` a voz.            |
| `ca-es-herenarus`                           | Imagem de recipiente com o `ca-ES` local e `ca-ES-HerenaRUS` a voz.       |
| `cs-cz-jakub`                               | Imagem de recipiente com o `cs-CZ` local e `cs-CZ-Jakub` a voz.           |
| `da-dk-hellerus`                            | Imagem de recipiente com o `da-DK` local e `da-DK-HelleRUS` a voz.        |
| `de-at-michael`                             | Imagem de recipiente com o `de-AT` local e `de-AT-Michael` a voz.         |
| `de-ch-karsten`                             | Imagem de recipiente com o `de-CH` local e `de-CH-Karsten` a voz.         |
| `de-de-hedda`                               | Imagem de recipiente com o `de-DE` local e `de-DE-Hedda` a voz.           |
| `de-de-heddarus`                            | Imagem de recipiente com o `de-DE` local e `de-DE-Hedda` a voz.           |
| `de-de-stefan-apollo`                       | Imagem de recipiente com o `de-DE` local e `de-DE-Stefan-Apollo` a voz.   |
| `el-gr-stefanos`                            | Imagem de recipiente com o `el-GR` local e `el-GR-Stefanos` a voz.        |
| `en-au-catherine`                           | Imagem de recipiente com o `en-AU` local e `en-AU-Catherine` a voz.       |
| `en-au-hayleyrus`                           | Imagem de recipiente com o `en-AU` local e `en-AU-HayleyRUS` a voz.       |
| `en-ca-heatherrus`                          | Imagem de recipiente com o `en-CA` local e `en-CA-HeatherRUS` a voz.      |
| `en-ca-linda`                               | Imagem de recipiente com o `en-CA` local e `en-CA-Linda` a voz.           |
| `en-gb-george-apollo`                       | Imagem de recipiente com o `en-GB` local e `en-GB-George-Apollo` a voz.   |
| `en-gb-hazelrus`                            | Imagem de recipiente com o `en-GB` local e `en-GB-HazelRUS` a voz.        |
| `en-gb-susan-apollo`                        | Imagem de recipiente com o `en-GB` local e `en-GB-Susan-Apollo` a voz.    |
| `en-ie-sean`                                | Imagem de recipiente com o `en-IE` local e `en-IE-Sean` a voz.            |
| `en-in-heera-apollo`                        | Imagem de recipiente com o `en-IN` local e `en-IN-Heera-Apollo` a voz.    |
| `en-in-priyarus`                            | Imagem de recipiente com o `en-IN` local e `en-IN-PriyaRUS` a voz.        |
| `en-in-ravi-apollo`                         | Imagem de recipiente com o `en-IN` local e `en-IN-Ravi-Apollo` a voz.     |
| `en-us-benjaminrus`                         | Imagem de recipiente com o `en-US` local e `en-US-BenjaminRUS` a voz.     |
| `en-us-guy24krus`                           | Imagem de recipiente com o `en-US` local e `en-US-Guy24kRUS` a voz.       |
| `en-us-aria24krus`                          | Imagem de recipiente com o `en-US` local e `en-US-Aria24kRUS` a voz.      |
| `en-us-ariarus`                             | Imagem de recipiente com o `en-US` local e `en-US-AriaRUS` a voz.         |
| `en-us-zirarus`                             | Imagem de recipiente com o `en-US` local e `en-US-ZiraRUS` a voz.         |
| `es-es-helenarus`                           | Imagem de recipiente com o `es-ES` local e `es-ES-HelenaRUS` a voz.       |
| `es-es-laura-apollo`                        | Imagem de recipiente com o `es-ES` local e `es-ES-Laura-Apollo` a voz.    |
| `es-es-pablo-apollo`                        | Imagem de recipiente com o `es-ES` local e `es-ES-Pablo-Apollo` a voz.    |
| `es-mx-hildarus`                            | Imagem de recipiente com o `es-MX` local e `es-MX-HildaRUS` a voz.        |
| `es-mx-raul-apollo`                         | Imagem de recipiente com o `es-MX` local e `es-MX-Raul-Apollo` a voz.     |
| `fi-fi-heidirus`                            | Imagem de recipiente com o `fi-FI` local e `fi-FI-HeidiRUS` a voz.        |
| `fr-ca-caroline`                            | Imagem de recipiente com o `fr-CA` local e `fr-CA-Caroline` a voz.        |
| `fr-ca-harmonierus`                         | Imagem de recipiente com o `fr-CA` local e `fr-CA-HarmonieRUS` a voz.     |
| `fr-ch-guillaume`                           | Imagem de recipiente com o `fr-CH` local e `fr-CH-Guillaume` a voz.       |
| `fr-fr-hortenserus`                         | Imagem de recipiente com o `fr-FR` local e `fr-FR-HortenseRUS` a voz.     |
| `fr-fr-julie-apollo`                        | Imagem de recipiente com o `fr-FR` local e `fr-FR-Julie-Apollo` a voz.    |
| `fr-fr-paul-apollo`                         | Imagem de recipiente com o `fr-FR` local e `fr-FR-Paul-Apollo` a voz.     |
| `he-il-asaf`                                | Imagem de recipiente com o `he-IL` local e `he-IL-Asaf` a voz.            |
| `hi-in-hemant`                              | Imagem de recipiente com o `hi-IN` local e `hi-IN-Hemant` a voz.          |
| `hi-in-kalpana-apollo`                      | Imagem de recipiente com o `hi-IN` local e `hi-IN-Kalpana-Apollo` a voz.  |
| `hi-in-kalpana`                             | Imagem de recipiente com o `hi-IN` local e `hi-IN-Kalpana` a voz.         |
| `hr-hr-matej`                               | Imagem de recipiente com o `hr-HR` local e `hr-HR-Matej` a voz.           |
| `hu-hu-szabolcs`                            | Imagem de recipiente com o `hu-HU` local e `hu-HU-Szabolcs` a voz.        |
| `id-id-andika`                              | Imagem de recipiente com o `id-ID` local e `id-ID-Andika` a voz.          |
| `it-it-cosimo-apollo`                       | Imagem de recipiente com o `it-IT` local e `it-IT-Cosimo-Apollo` a voz.   |
| `it-it-luciarus`                            | Imagem de recipiente com o `it-IT` local e `it-IT-LuciaRUS` a voz.        |
| `ja-jp-ayumi-apollo`                        | Imagem de recipiente com o `ja-JP` local e `ja-JP-Ayumi-Apollo` a voz.    |
| `ja-jp-harukarus`                           | Imagem de recipiente com o `ja-JP` local e `ja-JP-HarukaRUS` a voz.       |
| `ja-jp-ichiro-apollo`                       | Imagem de recipiente com o `ja-JP` local e `ja-JP-Ichiro-Apollo` a voz.   |
| `ko-kr-heamirus`                            | Imagem de recipiente com o `ko-KR` local e `ko-KR-HeamiRUS` a voz.        |
| `ms-my-rizwan`                              | Imagem de recipiente com o `ms-MY` local e `ms-MY-Rizwan` a voz.          |
| `nb-no-huldarus`                            | Imagem de recipiente com o `nb-NO` local e `nb-NO-HuldaRUS` a voz.        |
| `nl-nl-hannarus`                            | Imagem de recipiente com o `nl-NL` local e `nl-NL-HannaRUS` a voz.        |
| `pl-pl-paulinarus`                          | Imagem de recipiente com o `pl-PL` local e `pl-PL-PaulinaRUS` a voz.      |
| `pt-br-daniel-apollo`                       | Imagem de recipiente com o `pt-BR` local e `pt-BR-Daniel-Apollo` a voz.   |
| `pt-br-heloisarus`                          | Imagem de recipiente com o `pt-BR` local e `pt-BR-HeloisaRUS` a voz.      |
| `pt-pt-heliarus`                            | Imagem de recipiente com o `pt-PT` local e `pt-PT-HeliaRUS` a voz.        |
| `ro-ro-andrei`                              | Imagem de recipiente com o `ro-RO` local e `ro-RO-Andrei` a voz.          |
| `ru-ru-ekaterinarus`                        | Imagem de recipiente com o `ru-RU` local e `ru-RU-EkaterinaRUS` a voz.    |
| `ru-ru-irina-apollo`                        | Imagem de recipiente com o `ru-RU` local e `ru-RU-Irina-Apollo` a voz.    |
| `ru-ru-pavel-apollo`                        | Imagem de recipiente com o `ru-RU` local e `ru-RU-Pavel-Apollo` a voz.    |
| `sk-sk-filip`                               | Imagem de recipiente com o `sk-SK` local e `sk-SK-Filip` a voz.           |
| `sl-si-lado`                                | Imagem de recipiente com o `sl-SI` local e `sl-SI-Lado` a voz.            |
| `sv-se-hedvigrus`                           | Imagem de recipiente com o `sv-SE` local e `sv-SE-HedvigRUS` a voz.       |
| `ta-in-valluvar`                            | Imagem de recipiente com o `ta-IN` local e `ta-IN-Valluvar` a voz.        |
| `te-in-chitra`                              | Imagem de recipiente com o `te-IN` local e `te-IN-Chitra` a voz.          |
| `th-th-pattara`                             | Imagem de recipiente com o `th-TH` local e `th-TH-Pattara` a voz.         |
| `tr-tr-sedarus`                             | Imagem de recipiente com o `tr-TR` local e `tr-TR-SedaRUS` a voz.         |
| `vi-vn-an`                                  | Imagem de recipiente com o `vi-VN` local e `vi-VN-An` a voz.              |
| `zh-cn-huihuirus`                           | Imagem de recipiente com o `zh-CN` local e `zh-CN-HuihuiRUS` a voz.       |
| `zh-cn-kangkang-apollo`                     | Imagem de recipiente com o `zh-CN` local e `zh-CN-Kangkang-Apollo` a voz. |
| `zh-cn-yaoyao-apollo`                       | Imagem de recipiente com o `zh-CN` local e `zh-CN-Yaoyao-Apollo` a voz.   |
| `zh-hk-danny-apollo`                        | Imagem de recipiente com o `zh-HK` local e `zh-HK-Danny-Apollo` a voz.    |
| `zh-hk-tracy-apollo`                        | Imagem de recipiente com o `zh-HK` local e `zh-HK-Tracy-Apollo` a voz.    |
| `zh-hk-tracyrus`                            | Imagem de recipiente com o `zh-HK` local e `zh-HK-TracyRUS` a voz.        |
| `zh-tw-hanhanrus`                           | Imagem de recipiente com o `zh-TW` local e `zh-TW-HanHanRUS` a voz.       |
| `zh-tw-yating-apollo`                       | Imagem de recipiente com o `zh-TW` local e `zh-TW-Yating-Apollo` a voz.   |
| `zh-tw-zhiwei-apollo`                       | Imagem de recipiente com o `zh-TW` local e `zh-TW-Zhiwei-Apollo` a voz.   |


# <a name="previous-version"></a>[Versão anterior](#tab/previous)

Nota de lançamento `1.7.0-amd64-<locale-and-voice>` para:

**Funcionalidade**
* Componentes atualizados para .NET 3.1

| Etiquetas de imagem                                  | Notas                                                                                                         |
|---------------------------------------------|:--------------------------------------------------------------------------------------------------------------|
| `1.7.0-amd64-<locale-and-voice>`            | 1ª versão GA. Substitua `<locale>` por um dos locais disponíveis, listado abaixo. Por exemplo, `1.7.0-amd64-en-us-ariarus`.  |


| Locais para v1.7.0                          | Notas                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `ar-eg-hoda`                                | Imagem de recipiente com o `ar-EG` local e `ar-EG-Hoda` a voz.            |
| `ar-sa-naayf`                               | Imagem de recipiente com o `ar-SA` local e `ar-SA-Naayf` a voz.           |
| `bg-bg-ivan`                                | Imagem de recipiente com o `bg-BG` local e `bg-BG-Ivan` a voz.            |
| `ca-es-herenarus`                           | Imagem de recipiente com o `ca-ES` local e `ca-ES-HerenaRUS` a voz.       |
| `cs-cz-jakub`                               | Imagem de recipiente com o `cs-CZ` local e `cs-CZ-Jakub` a voz.           |
| `da-dk-hellerus`                            | Imagem de recipiente com o `da-DK` local e `da-DK-HelleRUS` a voz.        |
| `de-at-michael`                             | Imagem de recipiente com o `de-AT` local e `de-AT-Michael` a voz.         |
| `de-ch-karsten`                             | Imagem de recipiente com o `de-CH` local e `de-CH-Karsten` a voz.         |
| `de-de-hedda`                               | Imagem de recipiente com o `de-DE` local e `de-DE-Hedda` a voz.           |
| `de-de-heddarus`                            | Imagem de recipiente com o `de-DE` local e `de-DE-Hedda` a voz.           |
| `de-de-stefan-apollo`                       | Imagem de recipiente com o `de-DE` local e `de-DE-Stefan-Apollo` a voz.   |
| `el-gr-stefanos`                            | Imagem de recipiente com o `el-GR` local e `el-GR-Stefanos` a voz.        |
| `en-au-catherine`                           | Imagem de recipiente com o `en-AU` local e `en-AU-Catherine` a voz.       |
| `en-au-hayleyrus`                           | Imagem de recipiente com o `en-AU` local e `en-AU-HayleyRUS` a voz.       |
| `en-ca-heatherrus`                          | Imagem de recipiente com o `en-CA` local e `en-CA-HeatherRUS` a voz.      |
| `en-ca-linda`                               | Imagem de recipiente com o `en-CA` local e `en-CA-Linda` a voz.           |
| `en-gb-george-apollo`                       | Imagem de recipiente com o `en-GB` local e `en-GB-George-Apollo` a voz.   |
| `en-gb-hazelrus`                            | Imagem de recipiente com o `en-GB` local e `en-GB-HazelRUS` a voz.        |
| `en-gb-susan-apollo`                        | Imagem de recipiente com o `en-GB` local e `en-GB-Susan-Apollo` a voz.    |
| `en-ie-sean`                                | Imagem de recipiente com o `en-IE` local e `en-IE-Sean` a voz.            |
| `en-in-heera-apollo`                        | Imagem de recipiente com o `en-IN` local e `en-IN-Heera-Apollo` a voz.    |
| `en-in-priyarus`                            | Imagem de recipiente com o `en-IN` local e `en-IN-PriyaRUS` a voz.        |
| `en-in-ravi-apollo`                         | Imagem de recipiente com o `en-IN` local e `en-IN-Ravi-Apollo` a voz.     |
| `en-us-benjaminrus`                         | Imagem de recipiente com o `en-US` local e `en-US-BenjaminRUS` a voz.     |
| `en-us-guy24krus`                           | Imagem de recipiente com o `en-US` local e `en-US-Guy24kRUS` a voz.       |
| `en-us-aria24krus`                          | Imagem de recipiente com o `en-US` local e `en-US-Aria24kRUS` a voz.      |
| `en-us-ariarus`                             | Imagem de recipiente com o `en-US` local e `en-US-AriaRUS` a voz.         |
| `en-us-zirarus`                             | Imagem de recipiente com o `en-US` local e `en-US-ZiraRUS` a voz.         |
| `es-es-helenarus`                           | Imagem de recipiente com o `es-ES` local e `es-ES-HelenaRUS` a voz.       |
| `es-es-laura-apollo`                        | Imagem de recipiente com o `es-ES` local e `es-ES-Laura-Apollo` a voz.    |
| `es-es-pablo-apollo`                        | Imagem de recipiente com o `es-ES` local e `es-ES-Pablo-Apollo` a voz.    |
| `es-mx-hildarus`                            | Imagem de recipiente com o `es-MX` local e `es-MX-HildaRUS` a voz.        |
| `es-mx-raul-apollo`                         | Imagem de recipiente com o `es-MX` local e `es-MX-Raul-Apollo` a voz.     |
| `fi-fi-heidirus`                            | Imagem de recipiente com o `fi-FI` local e `fi-FI-HeidiRUS` a voz.        |
| `fr-ca-caroline`                            | Imagem de recipiente com o `fr-CA` local e `fr-CA-Caroline` a voz.        |
| `fr-ca-harmonierus`                         | Imagem de recipiente com o `fr-CA` local e `fr-CA-HarmonieRUS` a voz.     |
| `fr-ch-guillaume`                           | Imagem de recipiente com o `fr-CH` local e `fr-CH-Guillaume` a voz.       |
| `fr-fr-hortenserus`                         | Imagem de recipiente com o `fr-FR` local e `fr-FR-HortenseRUS` a voz.     |
| `fr-fr-julie-apollo`                        | Imagem de recipiente com o `fr-FR` local e `fr-FR-Julie-Apollo` a voz.    |
| `fr-fr-paul-apollo`                         | Imagem de recipiente com o `fr-FR` local e `fr-FR-Paul-Apollo` a voz.     |
| `he-il-asaf`                                | Imagem de recipiente com o `he-IL` local e `he-IL-Asaf` a voz.            |
| `hi-in-hemant`                              | Imagem de recipiente com o `hi-IN` local e `hi-IN-Hemant` a voz.          |
| `hi-in-kalpana-apollo`                      | Imagem de recipiente com o `hi-IN` local e `hi-IN-Kalpana-Apollo` a voz.  |
| `hi-in-kalpana`                             | Imagem de recipiente com o `hi-IN` local e `hi-IN-Kalpana` a voz.         |
| `hr-hr-matej`                               | Imagem de recipiente com o `hr-HR` local e `hr-HR-Matej` a voz.           |
| `hu-hu-szabolcs`                            | Imagem de recipiente com o `hu-HU` local e `hu-HU-Szabolcs` a voz.        |
| `id-id-andika`                              | Imagem de recipiente com o `id-ID` local e `id-ID-Andika` a voz.          |
| `it-it-cosimo-apollo`                       | Imagem de recipiente com o `it-IT` local e `it-IT-Cosimo-Apollo` a voz.   |
| `it-it-luciarus`                            | Imagem de recipiente com o `it-IT` local e `it-IT-LuciaRUS` a voz.        |
| `ja-jp-ayumi-apollo`                        | Imagem de recipiente com o `ja-JP` local e `ja-JP-Ayumi-Apollo` a voz.    |
| `ja-jp-harukarus`                           | Imagem de recipiente com o `ja-JP` local e `ja-JP-HarukaRUS` a voz.       |
| `ja-jp-ichiro-apollo`                       | Imagem de recipiente com o `ja-JP` local e `ja-JP-Ichiro-Apollo` a voz.   |
| `ko-kr-heamirus`                            | Imagem de recipiente com o `ko-KR` local e `ko-KR-HeamiRUS` a voz.        |
| `ms-my-rizwan`                              | Imagem de recipiente com o `ms-MY` local e `ms-MY-Rizwan` a voz.          |
| `nb-no-huldarus`                            | Imagem de recipiente com o `nb-NO` local e `nb-NO-HuldaRUS` a voz.        |
| `nl-nl-hannarus`                            | Imagem de recipiente com o `nl-NL` local e `nl-NL-HannaRUS` a voz.        |
| `pl-pl-paulinarus`                          | Imagem de recipiente com o `pl-PL` local e `pl-PL-PaulinaRUS` a voz.      |
| `pt-br-daniel-apollo`                       | Imagem de recipiente com o `pt-BR` local e `pt-BR-Daniel-Apollo` a voz.   |
| `pt-br-heloisarus`                          | Imagem de recipiente com o `pt-BR` local e `pt-BR-HeloisaRUS` a voz.      |
| `pt-pt-heliarus`                            | Imagem de recipiente com o `pt-PT` local e `pt-PT-HeliaRUS` a voz.        |
| `ro-ro-andrei`                              | Imagem de recipiente com o `ro-RO` local e `ro-RO-Andrei` a voz.          |
| `ru-ru-ekaterinarus`                        | Imagem de recipiente com o `ru-RU` local e `ru-RU-EkaterinaRUS` a voz.    |
| `ru-ru-irina-apollo`                        | Imagem de recipiente com o `ru-RU` local e `ru-RU-Irina-Apollo` a voz.    |
| `ru-ru-pavel-apollo`                        | Imagem de recipiente com o `ru-RU` local e `ru-RU-Pavel-Apollo` a voz.    |
| `sk-sk-filip`                               | Imagem de recipiente com o `sk-SK` local e `sk-SK-Filip` a voz.           |
| `sl-si-lado`                                | Imagem de recipiente com o `sl-SI` local e `sl-SI-Lado` a voz.            |
| `sv-se-hedvigrus`                           | Imagem de recipiente com o `sv-SE` local e `sv-SE-HedvigRUS` a voz.       |
| `ta-in-valluvar`                            | Imagem de recipiente com o `ta-IN` local e `ta-IN-Valluvar` a voz.        |
| `te-in-chitra`                              | Imagem de recipiente com o `te-IN` local e `te-IN-Chitra` a voz.          |
| `th-th-pattara`                             | Imagem de recipiente com o `th-TH` local e `th-TH-Pattara` a voz.         |
| `tr-tr-sedarus`                             | Imagem de recipiente com o `tr-TR` local e `tr-TR-SedaRUS` a voz.         |
| `vi-vn-an`                                  | Imagem de recipiente com o `vi-VN` local e `vi-VN-An` a voz.              |
| `zh-cn-huihuirus`                           | Imagem de recipiente com o `zh-CN` local e `zh-CN-HuihuiRUS` a voz.       |
| `zh-cn-kangkang-apollo`                     | Imagem de recipiente com o `zh-CN` local e `zh-CN-Kangkang-Apollo` a voz. |
| `zh-cn-yaoyao-apollo`                       | Imagem de recipiente com o `zh-CN` local e `zh-CN-Yaoyao-Apollo` a voz.   |
| `zh-hk-danny-apollo`                        | Imagem de recipiente com o `zh-HK` local e `zh-HK-Danny-Apollo` a voz.    |
| `zh-hk-tracy-apollo`                        | Imagem de recipiente com o `zh-HK` local e `zh-HK-Tracy-Apollo` a voz.    |
| `zh-hk-tracyrus`                            | Imagem de recipiente com o `zh-HK` local e `zh-HK-TracyRUS` a voz.        |
| `zh-tw-hanhanrus`                           | Imagem de recipiente com o `zh-TW` local e `zh-TW-HanHanRUS` a voz.       |
| `zh-tw-yating-apollo`                       | Imagem de recipiente com o `zh-TW` local e `zh-TW-Yating-Apollo` a voz.   |
| `zh-tw-zhiwei-apollo`                       | Imagem de recipiente com o `zh-TW` local e `zh-TW-Zhiwei-Apollo` a voz.   |

---

## <a name="neural-text-to-speech"></a>Texto-a-discurso neural

A imagem do recipiente [neural texto-a-fala][sp-ntts] pode ser encontrada no sindicato do registo do `mcr.microsoft.com` contentor. Reside dentro do `azure-cognitive-services/speechservices/` repositório e é `neural-text-to-speech` nomeado. O nome de imagem do recipiente totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech` .

Esta imagem do recipiente tem as seguintes etiquetas disponíveis. Também pode encontrar uma lista completa de [tags no MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/neural-text-to-speech/tags/list).


# <a name="latest-version"></a>[Versão mais recente](#tab/current)

Notas de lançamento `v1.3.0` para:
* O recipiente Neural Texto-a-fala está geralmente disponível. 

| Etiquetas de imagem                                  | Notas                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Imagem de recipiente com o `en-US` local e `en-US-AriaNeural` a voz.      |
| `1.3.0-amd64-<locale-and-voice>`    | Substitua `<locale>` por um dos locais disponíveis, listado abaixo. Por exemplo, `1.3.0-amd64-en-us-arianeural`. |


| v1.3.0 Locais e vozes           | Notas                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `de-de-katjaneural`                 | Imagem de recipiente com o `de-DE` local e `de-DE-KatjaNeural` a voz.     |
| `en-au-natashaneural`               | Imagem de recipiente com o `en-AU` local e `en-AU-NatashaNeural` a voz.   |
| `en-ca-claraneural`                 | Imagem de recipiente com o `en-CA` local e `en-CA-ClaraNeural` a voz.     |
| `en-gb-libbyneural`                 | Imagem de recipiente com o `en-GB` local e `en-GB-LibbyNeural` a voz.     |
| `en-gb-mianeural`                   | Imagem de recipiente com o `en-GB` local e `en-GB-MiaNeural` a voz.       |
| `en-us-arianeural`                  | Imagem de recipiente com o `en-US` local e `en-US-AriaNeural` a voz.      |
| `en-us-guyneural`                   | Imagem de recipiente com o `en-US` local e `en-US-GuyNeural` a voz.       |
| `es-es-elviraneural`                | Imagem de recipiente com o `es-ES` local e `es-ES-ElviraNeural` a voz.    |
| `es-mx-dalianeural`                 | Imagem de recipiente com o `es-MX` local e `es-MX-DaliaNeural` a voz.     |
| `fr-ca-sylvieneural`                | Imagem de recipiente com o `fr-CA` local e `fr-CA-SylvieNeural` a voz.    |
| `fr-fr-deniseneural`                | Imagem de recipiente com o `fr-FR` local e `fr-FR-DeniseNeural` a voz.    |
| `it-it-elsaneural`                  | Imagem de recipiente com o `it-IT` local e `it-IT-ElsaNeural` a voz.      |
| `ja-jp-nanamineural`                | Imagem de recipiente com o `ja-JP` local e `ja-JP-NanamiNeural` a voz.    |
| `ko-kr-sunhineural`                 | Imagem de recipiente com o `ko-KR` local e `ko-KR-SunHiNeural` a voz.     |
| `pt-br-franciscaneural`             | Imagem de recipiente com o `pt-BR` local e `pt-BR-FranciscaNeural` a voz. |
| `zh-cn-xiaoxiaoneural`              | Imagem de recipiente com o `zh-CN` local e `zh-CN-XiaoxiaoNeural` a voz.  |

# <a name="previous-version"></a>[Versão anterior](#tab/previous)

| Etiquetas de imagem                                  | Notas                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Imagem de recipiente com o `en-US` local e `en-US-AriaNeural` a voz.      |
| `1.2.0-amd64-<locale-and-voice>-preview`    | Substitua `<locale>` por um dos locais disponíveis, listado abaixo. Por exemplo, `1.2.0-amd64-en-us-arianeural-preview`. |


| v1.2.0 Visualização Locais e vozes           | Notas                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Imagem de recipiente com o `en-US` local e `en-US-AriaNeural` a voz.      |
| `de-de-katjaneural-preview`                 | Imagem de recipiente com o `de-DE` local e `de-DE-KatjaNeural` a voz.     |
| `en-au-natashaneural-preview`               | Imagem de recipiente com o `en-AU` local e `en-AU-NatashaNeural` a voz.   |
| `en-ca-claraneural-preview`                 | Imagem de recipiente com o `en-CA` local e `en-CA-ClaraNeural` a voz.     |
| `en-gb-libbyneural-preview`                 | Imagem de recipiente com o `en-GB` local e `en-GB-LibbyNeural` a voz.     |
| `en-gb-mianeural-preview`                   | Imagem de recipiente com o `en-GB` local e `en-GB-MiaNeural` a voz.       |
| `en-us-arianeural-preview`                  | Imagem de recipiente com o `en-US` local e `en-US-AriaNeural` a voz.      |
| `en-us-guyneural-preview`                   | Imagem de recipiente com o `en-US` local e `en-US-GuyNeural` a voz.       |
| `es-es-elviraneural-preview`                | Imagem de recipiente com o `es-ES` local e `es-ES-ElviraNeural` a voz.    |
| `es-mx-dalianeural-preview`                 | Imagem de recipiente com o `es-MX` local e `es-MX-DaliaNeural` a voz.     |
| `fr-ca-sylvieneural-preview`                | Imagem de recipiente com o `fr-CA` local e `fr-CA-SylvieNeural` a voz.    |
| `fr-fr-deniseneural-preview`                | Imagem de recipiente com o `fr-FR` local e `fr-FR-DeniseNeural` a voz.    |
| `it-it-elsaneural-preview`                  | Imagem de recipiente com o `it-IT` local e `it-IT-ElsaNeural` a voz.      |
| `ja-jp-nanamineural-preview`                | Imagem de recipiente com o `ja-JP` local e `ja-JP-NanamiNeural` a voz.    |
| `ko-kr-sunhineural-preview`                 | Imagem de recipiente com o `ko-KR` local e `ko-KR-SunHiNeural` a voz.     |
| `pt-br-franciscaneural-preview`             | Imagem de recipiente com o `pt-BR` local e `pt-BR-FranciscaNeural` a voz. |
| `zh-cn-xiaoxiaoneural-preview`              | Imagem de recipiente com o `zh-CN` local e `zh-CN-XiaoxiaoNeural` a voz.  |

---

## <a name="speech-language-detection"></a>Deteção de idioma de voz

A imagem do recipiente [de deteção da linguagem][sp-lid] da fala pode ser encontrada no sindicato do registo de `mcr.microsoft.com` contentores. Reside dentro do `azure-cognitive-services/speechservices/` repositório e é `language-detection` nomeado. O nome de imagem do recipiente totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection` .

Esta imagem do recipiente tem as seguintes etiquetas disponíveis. Também pode encontrar uma lista completa de [tags no MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/language-detection/tags/list).

| Etiquetas de imagem                                  | Notas                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                       |      |
| `1.1.0-amd64-preview`                       |      |

## <a name="key-phrase-extraction"></a>Extração de Expressões-Chave

a imagem do contentor pode ser encontrada no sindicato do registo do `mcr.microsoft.com` contentor. Reside dentro do `azure-cognitive-services/textanalytics/` repositório e é `keyphrase` nomeado. O nome de imagem do recipiente totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase` .

Esta imagem do recipiente tem as seguintes etiquetas disponíveis. Também pode encontrar uma lista completa de [tags no MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/keyphrase/tags/list).

# <a name="latest-version"></a>[Versão mais recente](#tab/current)


| Etiquetas de imagem                    | Notas |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013570001-amd64` |       |

# <a name="previous-versions"></a>[Versões anteriores](#tab/previous)

| Etiquetas de imagem                    | Notas |
|-------------------------------|:------|
| `1.1.012840001-amd64` |       |
| `1.1.012830001-amd64`    |       |

---

## <a name="text-language-detection"></a>Deteção de idioma do texto

A imagem do contentor [de deteção][ta-la] de linguagem pode ser encontrada no sindicato do `mcr.microsoft.com` registo do contentor. Reside dentro do `azure-cognitive-services/textanalytics/` repositório e é `language` nomeado. O nome de imagem do recipiente totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/textanalytics/language`


Esta imagem do recipiente tem as seguintes etiquetas disponíveis. Também pode encontrar uma lista completa de [tags no MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/language/tags/list).

# <a name="latest-versions"></a>[Versões mais recentes](#tab/current)

| Etiquetas de imagem                    | Notas |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013570001-amd64` | |
   

# <a name="previous-versions"></a>[Versões anteriores](#tab/previous)


| Etiquetas de imagem                    | Notas |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.012840001-amd64` |   |
| `1.1.012830001-amd64` |   |

---

## <a name="sentiment-analysis"></a>Análise de sentimentos

A imagem do contentor [de Análise de Sentimento][ta-se] pode ser encontrada no sindicato do registo do `mcr.microsoft.com` contentor. Reside dentro do `azure-cognitive-services/textanalytics/` repositório e é `sentiment` nomeado. O nome de imagem do recipiente totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment`

Esta imagem do recipiente tem as seguintes etiquetas disponíveis. Também pode encontrar uma lista completa de [tags no MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/sentiment/tags/list).

| Etiquetas de imagem | Notas                                         |
|------------|:----------------------------------------------|
| `latest`   |                                               |
| `3.0-en`   | Análise de Sentimento v3 (Inglês)               |
| `3.0-es`   | Análise de Sentimento v3 (Espanhol)               |
| `3.0-fr`   | Análise de Sentimento v3 (francês)                |
| `3.0-it`   | Análise de Sentimento v3 (Italiano)               |
| `3.0-de`   | Análise de Sentimento v3 (alemão)                |
| `3.0-zh`   | Análise de Sentimento v3 (chinês - simplificado)  |
| `3.0-zht`  | Análise de Sentimento v3 (chinês - tradicional) |
| `3.0-ja`   | Análise de Sentimento v3 (Japonês)              |
| `3.0-pt`   | Análise de Sentimento v3 (Português)            |
| `3.0-nl`   | Análise de Sentimento v3 (Holandês)                 |
| `2.1`    | Análise de Sentimento v2      |

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-stt]: ../speech-service/speech-container-howto.md?tabs=stt
[sp-cstt]: ../speech-service/speech-container-howto.md?tabs=cstt
[sp-tts]: ../speech-service/speech-container-howto.md?tabs=tts
[sp-ctts]: ../speech-service/speech-container-howto.md?tabs=ctts
[sp-ntts]: ../speech-service/speech-container-howto.md?tabs=ntts
[sp-lid]: ../speech-service/speech-container-howto.md?tabs=lid
[ta-kp]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-la]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-se]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment
