---
title: Etiquetas de imagem de contentor dos Serviços Cognitivos
titleSuffix: Azure Cognitive Services
description: Uma listagem abrangente de todas as etiquetas de imagem do recipiente do Serviço Cognitivo.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 786fde90e1bcf47e08857c26235a27862c5b3d75
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878770"
---
# <a name="azure-cognitive-services-container-image-tags"></a>Etiquetas de imagem de contentores de serviços cognitivos Azure

O Azure Cognitive Services oferece muitas imagens de contentores. Os registos de contentores e os repositórios correspondentes variam entre imagens de contentores. Cada nome de imagem de recipiente oferece várias etiquetas. Uma etiqueta de imagem de recipiente é um mecanismo de versonio da imagem do recipiente. Este artigo destina-se a ser usado como uma referência abrangente para a listagem de todas as imagens de contentores dos Serviços Cognitivos e suas etiquetas disponíveis.

> [!TIP]
> Ao [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/)utilizar, preste muita atenção ao invólucro do registo do contentor, do repositório, do nome da imagem do recipiente e da etiqueta correspondente - uma vez que são **sensíveis à caixa**.

## <a name="anomaly-detector"></a>Detetor de Anomalias

A imagem do recipiente do `containerpreview.azurecr.io` Detetor de [Anomalias][ad-containers] pode ser encontrada no registo do recipiente. Reside dentro do `microsoft` repositório e `cognitive-services-anomaly-detector`é nomeado. O nome da imagem `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector`do recipiente totalmente qualificado é, .

Esta imagem do recipiente tem as seguintes etiquetas disponíveis:

| Etiquetas de imagem                    | Notas |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008500001-amd64-preview` |       |

## <a name="computer-vision"></a>Imagem Digitalizada

A imagem do recipiente [Computer][cv-containers] `containerpreview.azurecr.io` Vision pode ser encontrada no registo do recipiente. Reside dentro do `microsoft` repositório e `cognitive-services-read`é nomeado. O nome da imagem `containerpreview.azurecr.io/microsoft/cognitive-services-read`do recipiente totalmente qualificado é, .

Esta imagem do recipiente tem as seguintes etiquetas disponíveis:

| Etiquetas de imagem                    | Notas |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.011580001-amd64-preview` |       |
| `1.1.009920003-amd64-preview` |       |
| `1.1.009910003-amd64-preview` |       |

## <a name="face"></a>Rostos

A [Face][fa-containers] imagem do recipiente Face `containerpreview.azurecr.io` pode ser encontrada no registo do recipiente. Reside dentro do `microsoft` repositório e `cognitive-services-face`é nomeado. O nome da imagem `containerpreview.azurecr.io/microsoft/cognitive-services-face`do recipiente totalmente qualificado é, .

Esta imagem do recipiente tem as seguintes etiquetas disponíveis:

| Etiquetas de imagem                    | Notas |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008710001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |
| `1.1.006490002-amd64-preview` |       |
| `1.0.005940002-amd64-preview` |       |
| `1.0.005550001-amd64-preview` |       |

## <a name="form-recognizer"></a>Reconhecedor de Formato

A imagem do recipiente ['Reconhecimento de Formulários'][fr-containers] pode ser encontrada no registo do `containerpreview.azurecr.io` contentor. Reside dentro do `microsoft` repositório e `cognitive-services-form-recognizer`é nomeado. O nome da imagem `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer`do recipiente totalmente qualificado é, .

Esta imagem do recipiente tem as seguintes etiquetas disponíveis:

| Etiquetas de imagem                    | Notas |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008640001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |

## <a name="language-understanding-luis"></a>Compreensão de Idiomas (LUIS)

A [LUIS][lu-containers] imagem do contentor LUIS `mcr.microsoft.com` pode ser encontrada no sindicato do registo de contentores. Reside dentro do `azure-cognitive-services` repositório e `luis`é nomeado. O nome da imagem `mcr.microsoft.com/azure-cognitive-services/luis`do recipiente totalmente qualificado é, .

Esta imagem do recipiente tem as seguintes etiquetas disponíveis:

| Etiquetas de imagem                    | Notas |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.010330004-amd64-preview` |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008710001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.008010002-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.007020001-amd64-preview` |       |

## <a name="custom-speech-to-text"></a>Discurso personalizado a texto

A imagem personalizada do recipiente de discurso `containerpreview.azurecr.io` a [texto][sp-cstt] pode ser encontrada no registo do recipiente. Reside dentro do `microsoft` repositório e `cognitive-services-custom-speech-to-text`é nomeado. O nome da imagem `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text`do recipiente totalmente qualificado é, .

Esta imagem do recipiente tem as seguintes etiquetas disponíveis:

| Etiquetas de imagem            | Notas |
|-----------------------|:------|
| `latest`              |       |
| `2.1.1-amd64-preview` |       |
| `2.1.0-amd64-preview` |       |
| `2.0.2-amd64-preview` |       |
| `2.0.0-amd64-preview` |       |

## <a name="custom-text-to-speech"></a>Texto-a-fala personalizado

A imagem personalizada do recipiente [texto-a-fala][sp-ctts] pode ser encontrada no registo do `containerpreview.azurecr.io` recipiente. Reside dentro do `microsoft` repositório e `cognitive-services-custom-text-to-speech`é nomeado. O nome da imagem `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech`do recipiente totalmente qualificado é, .

Esta imagem do recipiente tem as seguintes etiquetas disponíveis:

| Etiquetas de imagem            | Notas |
|-----------------------|:------|
| `latest`              |       |
| `1.3.0-amd64-preview` |       |

## <a name="speech-to-text"></a>Conversão de voz em texto

A imagem do recipiente [fala-a-texto][sp-stt] pode ser encontrada no registo do `containerpreview.azurecr.io` recipiente. Reside dentro do `microsoft` repositório e `cognitive-services-speech-to-text`é nomeado. O nome da imagem `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text`do recipiente totalmente qualificado é, .

Esta imagem do recipiente tem as seguintes etiquetas disponíveis:

| Etiquetas de imagem                  | Notas                                    |
|-----------------------------|:-----------------------------------------|
| `latest`                    | Imagem de `en-US` contentor com o local. |
| `2.1.1-amd64-ar-ae-preview` | Imagem de `ar-AE` contentor com o local. |
| `2.1.1-amd64-ar-eg-preview` | Imagem de `ar-EG` contentor com o local. |
| `2.1.1-amd64-ar-kw-preview` | Imagem de `ar-KW` contentor com o local. |
| `2.1.1-amd64-ar-qa-preview` | Imagem de `ar-QA` contentor com o local. |
| `2.1.1-amd64-ar-sa-preview` | Imagem de `ar-SA` contentor com o local. |
| `2.1.1-amd64-ca-es-preview` | Imagem de `ca-ES` contentor com o local. |
| `2.1.1-amd64-da-dk-preview` | Imagem de `da-DK` contentor com o local. |
| `2.1.1-amd64-de-de-preview` | Imagem de `de-DE` contentor com o local. |
| `2.1.1-amd64-en-au-preview` | Imagem de `en-AU` contentor com o local. |
| `2.1.1-amd64-en-ca-preview` | Imagem de `en-CA` contentor com o local. |
| `2.1.1-amd64-en-gb-preview` | Imagem de `en-GB` contentor com o local. |
| `2.1.1-amd64-en-in-preview` | Imagem de `en-IN` contentor com o local. |
| `2.1.1-amd64-en-nz-preview` | Imagem de `en-NZ` contentor com o local. |
| `2.1.1-amd64-en-us-preview` | Imagem de `en-US` contentor com o local. |
| `2.1.1-amd64-es-es-preview` | Imagem de `es-ES` contentor com o local. |
| `2.1.1-amd64-es-mx-preview` | Imagem de `es-MX` contentor com o local. |
| `2.1.1-amd64-fi-fi-preview` | Imagem de `fi-FI` contentor com o local. |
| `2.1.1-amd64-fr-ca-preview` | Imagem de `fr-CA` contentor com o local. |
| `2.1.1-amd64-fr-fr-preview` | Imagem de `fr-FR` contentor com o local. |
| `2.1.1-amd64-gu-in-preview` | Imagem de `gu-IN` contentor com o local. |
| `2.1.1-amd64-hi-in-preview` | Imagem de `hi-IN` contentor com o local. |
| `2.1.1-amd64-it-it-preview` | Imagem de `it-IT` contentor com o local. |
| `2.1.1-amd64-ja-jp-preview` | Imagem de `ja-JP` contentor com o local. |
| `2.1.1-amd64-ko-kr-preview` | Imagem de `ko-KR` contentor com o local. |
| `2.1.1-amd64-mr-in-preview` | Imagem de `mr-IN` contentor com o local. |
| `2.1.1-amd64-nb-no-preview` | Imagem de `nb-NO` contentor com o local. |
| `2.1.1-amd64-nl-nl-preview` | Imagem de `nl-NL` contentor com o local. |
| `2.1.1-amd64-pl-pl-preview` | Imagem de `pl-PL` contentor com o local. |
| `2.1.1-amd64-pt-br-preview` | Imagem de `pt-BR` contentor com o local. |
| `2.1.1-amd64-pt-pt-preview` | Imagem de `pt-PT` contentor com o local. |
| `2.1.1-amd64-ru-ru-preview` | Imagem de `ru-RU` contentor com o local. |
| `2.1.1-amd64-sv-se-preview` | Imagem de `sv-SE` contentor com o local. |
| `2.1.1-amd64-ta-in-preview` | Imagem de `ta-IN` contentor com o local. |
| `2.1.1-amd64-te-in-preview` | Imagem de `te-IN` contentor com o local. |
| `2.1.1-amd64-th-th-preview` | Imagem de `th-TH` contentor com o local. |
| `2.1.1-amd64-tr-tr-preview` | Imagem de `tr-TR` contentor com o local. |
| `2.1.1-amd64-zh-cn-preview` | Imagem de `zh-CN` contentor com o local. |
| `2.1.1-amd64-zh-hk-preview` | Imagem de `zh-HK` contentor com o local. |
| `2.1.1-amd64-zh-tw-preview` | Imagem de `zh-TW` contentor com o local. |
| `2.1.0-amd64-ar-ae-preview` | Imagem de `ar-AE` contentor com o local. |
| `2.1.0-amd64-ar-eg-preview` | Imagem de `ar-EG` contentor com o local. |
| `2.1.0-amd64-ar-kw-preview` | Imagem de `ar-KW` contentor com o local. |
| `2.1.0-amd64-ar-qa-preview` | Imagem de `ar-QA` contentor com o local. |
| `2.1.0-amd64-ar-sa-preview` | Imagem de `ar-SA` contentor com o local. |
| `2.1.0-amd64-ca-es-preview` | Imagem de `ca-ES` contentor com o local. |
| `2.1.0-amd64-da-dk-preview` | Imagem de `da-DK` contentor com o local. |
| `2.1.0-amd64-de-de-preview` | Imagem de `de-DE` contentor com o local. |
| `2.1.0-amd64-en-au-preview` | Imagem de `en-AU` contentor com o local. |
| `2.1.0-amd64-en-ca-preview` | Imagem de `en-CA` contentor com o local. |
| `2.1.0-amd64-en-gb-preview` | Imagem de `en-GB` contentor com o local. |
| `2.1.0-amd64-en-in-preview` | Imagem de `en-IN` contentor com o local. |
| `2.1.0-amd64-en-nz-preview` | Imagem de `en-NZ` contentor com o local. |
| `2.1.0-amd64-en-us-preview` | Imagem de `en-US` contentor com o local. |
| `2.1.0-amd64-es-es-preview` | Imagem de `es-ES` contentor com o local. |
| `2.1.0-amd64-es-mx-preview` | Imagem de `es-MX` contentor com o local. |
| `2.1.0-amd64-fi-fi-preview` | Imagem de `fi-FI` contentor com o local. |
| `2.1.0-amd64-fr-ca-preview` | Imagem de `fr-CA` contentor com o local. |
| `2.1.0-amd64-fr-fr-preview` | Imagem de `fr-FR` contentor com o local. |
| `2.1.0-amd64-gu-in-preview` | Imagem de `gu-IN` contentor com o local. |
| `2.1.0-amd64-hi-in-preview` | Imagem de `hi-IN` contentor com o local. |
| `2.1.0-amd64-it-it-preview` | Imagem de `it-IT` contentor com o local. |
| `2.1.0-amd64-ja-jp-preview` | Imagem de `ja-JP` contentor com o local. |
| `2.1.0-amd64-ko-kr-preview` | Imagem de `ko-KR` contentor com o local. |
| `2.1.0-amd64-mr-in-preview` | Imagem de `mr-IN` contentor com o local. |
| `2.1.0-amd64-nb-no-preview` | Imagem de `nb-NO` contentor com o local. |
| `2.1.0-amd64-nl-nl-preview` | Imagem de `nl-NL` contentor com o local. |
| `2.1.0-amd64-pl-pl-preview` | Imagem de `pl-PL` contentor com o local. |
| `2.1.0-amd64-pt-br-preview` | Imagem de `pt-BR` contentor com o local. |
| `2.1.0-amd64-pt-pt-preview` | Imagem de `pt-PT` contentor com o local. |
| `2.1.0-amd64-ru-ru-preview` | Imagem de `ru-RU` contentor com o local. |
| `2.1.0-amd64-sv-se-preview` | Imagem de `sv-SE` contentor com o local. |
| `2.1.0-amd64-ta-in-preview` | Imagem de `ta-IN` contentor com o local. |
| `2.1.0-amd64-te-in-preview` | Imagem de `te-IN` contentor com o local. |
| `2.1.0-amd64-th-th-preview` | Imagem de `th-TH` contentor com o local. |
| `2.1.0-amd64-tr-tr-preview` | Imagem de `tr-TR` contentor com o local. |
| `2.1.0-amd64-zh-cn-preview` | Imagem de `zh-CN` contentor com o local. |
| `2.1.0-amd64-zh-hk-preview` | Imagem de `zh-HK` contentor com o local. |
| `2.1.0-amd64-zh-tw-preview` | Imagem de `zh-TW` contentor com o local. |
| `2.0.3-amd64-ja-jp-preview` | Imagem de `ja-JP` contentor com o local. |
| `2.0.2-amd64-ar-ae-preview` | Imagem de `ar-AE` contentor com o local. |
| `2.0.2-amd64-ar-eg-preview` | Imagem de `ar-EG` contentor com o local. |
| `2.0.2-amd64-ar-kw-preview` | Imagem de `ar-KW` contentor com o local. |
| `2.0.2-amd64-ar-qa-preview` | Imagem de `ar-QA` contentor com o local. |
| `2.0.2-amd64-ar-sa-preview` | Imagem de `ar-SA` contentor com o local. |
| `2.0.2-amd64-ca-es-preview` | Imagem de `ca-ES` contentor com o local. |
| `2.0.2-amd64-da-dk-preview` | Imagem de `da-DK` contentor com o local. |
| `2.0.2-amd64-de-de-preview` | Imagem de `de-DE` contentor com o local. |
| `2.0.2-amd64-en-au-preview` | Imagem de `en-AU` contentor com o local. |
| `2.0.2-amd64-en-ca-preview` | Imagem de `en-CA` contentor com o local. |
| `2.0.2-amd64-en-gb-preview` | Imagem de `en-GB` contentor com o local. |
| `2.0.2-amd64-en-in-preview` | Imagem de `en-IN` contentor com o local. |
| `2.0.2-amd64-en-nz-preview` | Imagem de `en-NZ` contentor com o local. |
| `2.0.2-amd64-en-us-preview` | Imagem de `en-US` contentor com o local. |
| `2.0.2-amd64-es-es-preview` | Imagem de `es-ES` contentor com o local. |
| `2.0.2-amd64-es-mx-preview` | Imagem de `es-MX` contentor com o local. |
| `2.0.2-amd64-fi-fi-preview` | Imagem de `fi-FI` contentor com o local. |
| `2.0.2-amd64-fr-ca-preview` | Imagem de `fr-CA` contentor com o local. |
| `2.0.2-amd64-fr-fr-preview` | Imagem de `fr-FR` contentor com o local. |
| `2.0.2-amd64-gu-in-preview` | Imagem de `gu-IN` contentor com o local. |
| `2.0.2-amd64-hi-in-preview` | Imagem de `hi-IN` contentor com o local. |
| `2.0.2-amd64-it-it-preview` | Imagem de `it-IT` contentor com o local. |
| `2.0.2-amd64-ja-jp-preview` | Imagem de `ja-JP` contentor com o local. |
| `2.0.2-amd64-ko-kr-preview` | Imagem de `ko-KR` contentor com o local. |
| `2.0.2-amd64-mr-in-preview` | Imagem de `mr-IN` contentor com o local. |
| `2.0.2-amd64-nb-no-preview` | Imagem de `nb-NO` contentor com o local. |
| `2.0.2-amd64-nl-nl-preview` | Imagem de `nl-NL` contentor com o local. |
| `2.0.2-amd64-pl-pl-preview` | Imagem de `pl-PL` contentor com o local. |
| `2.0.2-amd64-pt-br-preview` | Imagem de `pt-BR` contentor com o local. |
| `2.0.2-amd64-pt-pt-preview` | Imagem de `pt-PT` contentor com o local. |
| `2.0.2-amd64-ru-ru-preview` | Imagem de `ru-RU` contentor com o local. |
| `2.0.2-amd64-sv-se-preview` | Imagem de `sv-SE` contentor com o local. |
| `2.0.2-amd64-ta-in-preview` | Imagem de `ta-IN` contentor com o local. |
| `2.0.2-amd64-te-in-preview` | Imagem de `te-IN` contentor com o local. |
| `2.0.2-amd64-th-th-preview` | Imagem de `th-TH` contentor com o local. |
| `2.0.2-amd64-tr-tr-preview` | Imagem de `tr-TR` contentor com o local. |
| `2.0.2-amd64-zh-cn-preview` | Imagem de `zh-CN` contentor com o local. |
| `2.0.2-amd64-zh-hk-preview` | Imagem de `zh-HK` contentor com o local. |
| `2.0.2-amd64-zh-tw-preview` | Imagem de `zh-TW` contentor com o local. |
| `2.0.1-amd64-ar-ae-preview` | Imagem de `ar-AE` contentor com o local. |
| `2.0.1-amd64-ar-eg-preview` | Imagem de `ar-EG` contentor com o local. |
| `2.0.1-amd64-ar-kw-preview` | Imagem de `ar-KW` contentor com o local. |
| `2.0.1-amd64-ar-qa-preview` | Imagem de `ar-QA` contentor com o local. |
| `2.0.1-amd64-ar-sa-preview` | Imagem de `ar-SA` contentor com o local. |
| `2.0.1-amd64-ca-es-preview` | Imagem de `ca-ES` contentor com o local. |
| `2.0.1-amd64-da-dk-preview` | Imagem de `da-DK` contentor com o local. |
| `2.0.1-amd64-de-de-preview` | Imagem de `de-DE` contentor com o local. |
| `2.0.1-amd64-en-au-preview` | Imagem de `en-AU` contentor com o local. |
| `2.0.1-amd64-en-ca-preview` | Imagem de `en-CA` contentor com o local. |
| `2.0.1-amd64-en-gb-preview` | Imagem de `en-GB` contentor com o local. |
| `2.0.1-amd64-en-in-preview` | Imagem de `en-IN` contentor com o local. |
| `2.0.1-amd64-en-nz-preview` | Imagem de `en-NZ` contentor com o local. |
| `2.0.1-amd64-en-us-preview` | Imagem de `en-US` contentor com o local. |
| `2.0.1-amd64-es-es-preview` | Imagem de `es-ES` contentor com o local. |
| `2.0.1-amd64-es-mx-preview` | Imagem de `es-MX` contentor com o local. |
| `2.0.1-amd64-fi-fi-preview` | Imagem de `fi-FI` contentor com o local. |
| `2.0.1-amd64-fr-ca-preview` | Imagem de `fr-CA` contentor com o local. |
| `2.0.1-amd64-fr-fr-preview` | Imagem de `fr-FR` contentor com o local. |
| `2.0.1-amd64-gu-in-preview` | Imagem de `gu-IN` contentor com o local. |
| `2.0.1-amd64-hi-in-preview` | Imagem de `hi-IN` contentor com o local. |
| `2.0.1-amd64-it-it-preview` | Imagem de `it-IT` contentor com o local. |
| `2.0.1-amd64-ja-jp-preview` | Imagem de `ja-JP` contentor com o local. |
| `2.0.1-amd64-ko-kr-preview` | Imagem de `ko-KR` contentor com o local. |
| `2.0.1-amd64-mr-in-preview` | Imagem de `mr-IN` contentor com o local. |
| `2.0.1-amd64-nb-no-preview` | Imagem de `nb-NO` contentor com o local. |
| `2.0.1-amd64-nl-nl-preview` | Imagem de `nl-NL` contentor com o local. |
| `2.0.1-amd64-pl-pl-preview` | Imagem de `pl-PL` contentor com o local. |
| `2.0.1-amd64-pt-br-preview` | Imagem de `pt-BR` contentor com o local. |
| `2.0.1-amd64-pt-pt-preview` | Imagem de `pt-PT` contentor com o local. |
| `2.0.1-amd64-ru-ru-preview` | Imagem de `ru-RU` contentor com o local. |
| `2.0.1-amd64-sv-se-preview` | Imagem de `sv-SE` contentor com o local. |
| `2.0.1-amd64-ta-in-preview` | Imagem de `ta-IN` contentor com o local. |
| `2.0.1-amd64-te-in-preview` | Imagem de `te-IN` contentor com o local. |
| `2.0.1-amd64-th-th-preview` | Imagem de `th-TH` contentor com o local. |
| `2.0.1-amd64-tr-tr-preview` | Imagem de `tr-TR` contentor com o local. |
| `2.0.1-amd64-zh-cn-preview` | Imagem de `zh-CN` contentor com o local. |
| `2.0.1-amd64-zh-hk-preview` | Imagem de `zh-HK` contentor com o local. |
| `2.0.1-amd64-zh-tw-preview` | Imagem de `zh-TW` contentor com o local. |
| `2.0.0-amd64-ar-eg-preview` | Imagem de `ar-EG` contentor com o local. |
| `2.0.0-amd64-ca-es-preview` | Imagem de `ca-ES` contentor com o local. |
| `2.0.0-amd64-da-dk-preview` | Imagem de `da-DK` contentor com o local. |
| `2.0.0-amd64-de-de-preview` | Imagem de `de-DE` contentor com o local. |
| `2.0.0-amd64-en-au-preview` | Imagem de `en-AU` contentor com o local. |
| `2.0.0-amd64-en-ca-preview` | Imagem de `en-CA` contentor com o local. |
| `2.0.0-amd64-en-gb-preview` | Imagem de `en-GB` contentor com o local. |
| `2.0.0-amd64-en-in-preview` | Imagem de `en-IN` contentor com o local. |
| `2.0.0-amd64-en-nz-preview` | Imagem de `en-NZ` contentor com o local. |
| `2.0.0-amd64-en-us-preview` | Imagem de `en-US` contentor com o local. |
| `2.0.0-amd64-es-es-preview` | Imagem de `es-ES` contentor com o local. |
| `2.0.0-amd64-es-mx-preview` | Imagem de `es-MX` contentor com o local. |
| `2.0.0-amd64-fi-fi-preview` | Imagem de `fi-FI` contentor com o local. |
| `2.0.0-amd64-fr-ca-preview` | Imagem de `fr-CA` contentor com o local. |
| `2.0.0-amd64-fr-fr-preview` | Imagem de `fr-FR` contentor com o local. |
| `2.0.0-amd64-hi-in-preview` | Imagem de `hi-IN` contentor com o local. |
| `2.0.0-amd64-it-it-preview` | Imagem de `it-IT` contentor com o local. |
| `2.0.0-amd64-ja-jp-preview` | Imagem de `ja-JP` contentor com o local. |
| `2.0.0-amd64-ko-kr-preview` | Imagem de `ko-KR` contentor com o local. |
| `2.0.0-amd64-nb-no-preview` | Imagem de `nb-NO` contentor com o local. |
| `2.0.0-amd64-nl-nl-preview` | Imagem de `nl-NL` contentor com o local. |
| `2.0.0-amd64-pl-pl-preview` | Imagem de `pl-PL` contentor com o local. |
| `2.0.0-amd64-pt-br-preview` | Imagem de `pt-BR` contentor com o local. |
| `2.0.0-amd64-pt-pt-preview` | Imagem de `pt-PT` contentor com o local. |
| `2.0.0-amd64-ru-ru-preview` | Imagem de `ru-RU` contentor com o local. |
| `2.0.0-amd64-sv-se-preview` | Imagem de `sv-SE` contentor com o local. |
| `2.0.0-amd64-th-th-preview` | Imagem de `th-TH` contentor com o local. |
| `2.0.0-amd64-tr-tr-preview` | Imagem de `tr-TR` contentor com o local. |
| `2.0.0-amd64-zh-cn-preview` | Imagem de `zh-CN` contentor com o local. |
| `2.0.0-amd64-zh-hk-preview` | Imagem de `zh-HK` contentor com o local. |
| `2.0.0-amd64-zh-tw-preview` | Imagem de `zh-TW` contentor com o local. |
| `1.2.0-amd64-de-de-preview` | Imagem de `de-DE` contentor com o local. |
| `1.2.0-amd64-en-au-preview` | Imagem de `en-AU` contentor com o local. |
| `1.2.0-amd64-en-ca-preview` | Imagem de `en-CA` contentor com o local. |
| `1.2.0-amd64-en-gb-preview` | Imagem de `en-GB` contentor com o local. |
| `1.2.0-amd64-en-in-preview` | Imagem de `en-IN` contentor com o local. |
| `1.2.0-amd64-en-us-preview` | Imagem de `en-US` contentor com o local. |
| `1.2.0-amd64-es-es-preview` | Imagem de `es-ES` contentor com o local. |
| `1.2.0-amd64-es-mx-preview` | Imagem de `es-MX` contentor com o local. |
| `1.2.0-amd64-fr-ca-preview` | Imagem de `fr-CA` contentor com o local. |
| `1.2.0-amd64-fr-fr-preview` | Imagem de `fr-FR` contentor com o local. |
| `1.2.0-amd64-it-it-preview` | Imagem de `it-IT` contentor com o local. |
| `1.2.0-amd64-ja-jp-preview` | Imagem de `ja-JP` contentor com o local. |
| `1.2.0-amd64-pt-br-preview` | Imagem de `pt-BR` contentor com o local. |
| `1.2.0-amd64-zh-cn-preview` | Imagem de `zh-CN` contentor com o local. |
| `1.1.3-amd64-de-de-preview` | Imagem de `de-DE` contentor com o local. |
| `1.1.3-amd64-en-au-preview` | Imagem de `en-AU` contentor com o local. |
| `1.1.3-amd64-en-ca-preview` | Imagem de `en-CA` contentor com o local. |
| `1.1.3-amd64-en-gb-preview` | Imagem de `en-GB` contentor com o local. |
| `1.1.3-amd64-en-in-preview` | Imagem de `en-IN` contentor com o local. |
| `1.1.3-amd64-en-us-preview` | Imagem de `en-US` contentor com o local. |
| `1.1.3-amd64-es-es-preview` | Imagem de `es-ES` contentor com o local. |
| `1.1.3-amd64-es-mx-preview` | Imagem de `es-MX` contentor com o local. |
| `1.1.3-amd64-fr-ca-preview` | Imagem de `fr-CA` contentor com o local. |
| `1.1.3-amd64-fr-fr-preview` | Imagem de `fr-FR` contentor com o local. |
| `1.1.3-amd64-it-it-preview` | Imagem de `it-IT` contentor com o local. |
| `1.1.3-amd64-ja-jp-preview` | Imagem de `ja-JP` contentor com o local. |
| `1.1.3-amd64-pt-br-preview` | Imagem de `pt-BR` contentor com o local. |
| `1.1.3-amd64-zh-cn-preview` | Imagem de `zh-CN` contentor com o local. |
| `1.1.2-amd64-de-de-preview` | Imagem de `de-DE` contentor com o local. |
| `1.1.2-amd64-en-au-preview` | Imagem de `en-AU` contentor com o local. |
| `1.1.2-amd64-en-ca-preview` | Imagem de `en-CA` contentor com o local. |
| `1.1.2-amd64-en-gb-preview` | Imagem de `en-GB` contentor com o local. |
| `1.1.2-amd64-en-in-preview` | Imagem de `en-IN` contentor com o local. |
| `1.1.2-amd64-en-us-preview` | Imagem de `en-US` contentor com o local. |
| `1.1.2-amd64-es-es-preview` | Imagem de `es-ES` contentor com o local. |
| `1.1.2-amd64-es-mx-preview` | Imagem de `es-MX` contentor com o local. |
| `1.1.2-amd64-fr-ca-preview` | Imagem de `fr-CA` contentor com o local. |
| `1.1.2-amd64-fr-fr-preview` | Imagem de `fr-FR` contentor com o local. |
| `1.1.2-amd64-it-it-preview` | Imagem de `it-IT` contentor com o local. |
| `1.1.2-amd64-ja-jp-preview` | Imagem de `ja-JP` contentor com o local. |
| `1.1.2-amd64-pt-br-preview` | Imagem de `pt-BR` contentor com o local. |
| `1.1.2-amd64-zh-cn-preview` | Imagem de `zh-CN` contentor com o local. |
| `1.1.1-amd64-de-de-preview` | Imagem de `de-DE` contentor com o local. |
| `1.1.1-amd64-en-au-preview` | Imagem de `en-AU` contentor com o local. |
| `1.1.1-amd64-en-ca-preview` | Imagem de `en-CA` contentor com o local. |
| `1.1.1-amd64-en-gb-preview` | Imagem de `en-GB` contentor com o local. |
| `1.1.1-amd64-en-in-preview` | Imagem de `en-IN` contentor com o local. |
| `1.1.1-amd64-en-us-preview` | Imagem de `en-US` contentor com o local. |
| `1.1.1-amd64-es-es-preview` | Imagem de `es-ES` contentor com o local. |
| `1.1.1-amd64-es-mx-preview` | Imagem de `es-MX` contentor com o local. |
| `1.1.1-amd64-fr-ca-preview` | Imagem de `fr-CA` contentor com o local. |
| `1.1.1-amd64-fr-fr-preview` | Imagem de `fr-FR` contentor com o local. |
| `1.1.1-amd64-it-it-preview` | Imagem de `it-IT` contentor com o local. |
| `1.1.1-amd64-ja-jp-preview` | Imagem de `ja-JP` contentor com o local. |
| `1.1.1-amd64-pt-br-preview` | Imagem de `pt-BR` contentor com o local. |
| `1.1.1-amd64-zh-cn-preview` | Imagem de `zh-CN` contentor com o local. |
| `1.1.0-amd64-de-de-preview` | Imagem de `de-DE` contentor com o local. |
| `1.1.0-amd64-en-au-preview` | Imagem de `en-AU` contentor com o local. |
| `1.1.0-amd64-en-ca-preview` | Imagem de `en-CA` contentor com o local. |
| `1.1.0-amd64-en-gb-preview` | Imagem de `en-GB` contentor com o local. |
| `1.1.0-amd64-en-in-preview` | Imagem de `en-IN` contentor com o local. |
| `1.1.0-amd64-en-us-preview` | Imagem de `en-US` contentor com o local. |
| `1.1.0-amd64-es-es-preview` | Imagem de `es-ES` contentor com o local. |
| `1.1.0-amd64-es-mx-preview` | Imagem de `es-MX` contentor com o local. |
| `1.1.0-amd64-fr-ca-preview` | Imagem de `fr-CA` contentor com o local. |
| `1.1.0-amd64-fr-fr-preview` | Imagem de `fr-FR` contentor com o local. |
| `1.1.0-amd64-it-it-preview` | Imagem de `it-IT` contentor com o local. |
| `1.1.0-amd64-ja-jp-preview` | Imagem de `ja-JP` contentor com o local. |
| `1.1.0-amd64-pt-br-preview` | Imagem de `pt-BR` contentor com o local. |
| `1.1.0-amd64-zh-cn-preview` | Imagem de `zh-CN` contentor com o local. |
| `1.0.0-amd64-de-de-preview` | Imagem de `de-DE` contentor com o local. |
| `1.0.0-amd64-en-au-preview` | Imagem de `en-AU` contentor com o local. |
| `1.0.0-amd64-en-ca-preview` | Imagem de `en-CA` contentor com o local. |
| `1.0.0-amd64-en-gb-preview` | Imagem de `en-GB` contentor com o local. |
| `1.0.0-amd64-en-in-preview` | Imagem de `en-IN` contentor com o local. |
| `1.0.0-amd64-en-us-preview` | Imagem de `en-US` contentor com o local. |
| `1.0.0-amd64-es-es-preview` | Imagem de `es-ES` contentor com o local. |
| `1.0.0-amd64-es-mx-preview` | Imagem de `es-MX` contentor com o local. |
| `1.0.0-amd64-fr-ca-preview` | Imagem de `fr-CA` contentor com o local. |
| `1.0.0-amd64-fr-fr-preview` | Imagem de `fr-FR` contentor com o local. |
| `1.0.0-amd64-it-it-preview` | Imagem de `it-IT` contentor com o local. |
| `1.0.0-amd64-ja-jp-preview` | Imagem de `ja-JP` contentor com o local. |
| `1.0.0-amd64-pt-br-preview` | Imagem de `pt-BR` contentor com o local. |
| `1.0.0-amd64-zh-cn-preview` | Imagem de `zh-CN` contentor com o local. |

## <a name="text-to-speech"></a>Conversão de texto em voz

A imagem do recipiente [texto-a-fala][sp-tts] pode ser encontrada no registo do `containerpreview.azurecr.io` recipiente. Reside dentro do `microsoft` repositório e `cognitive-services-text-to-speech`é nomeado. O nome da imagem `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech`do recipiente totalmente qualificado é, .

Esta imagem do recipiente tem as seguintes etiquetas disponíveis:

| Etiquetas de imagem                                  | Notas                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Imagem de `en-US` recipiente com `en-US-JessaRUS` o local e a voz.        |
| `1.3.0-amd64-ar-eg-hoda-preview`            | Imagem de `ar-EG` recipiente com `ar-EG-Hoda` o local e a voz.            |
| `1.3.0-amd64-ar-sa-naayf-preview`           | Imagem de `ar-SA` recipiente com `ar-SA-Naayf` o local e a voz.           |
| `1.3.0-amd64-bg-bg-ivan-preview`            | Imagem de `bg-BG` recipiente com `bg-BG-Ivan` o local e a voz.            |
| `1.3.0-amd64-ca-es-herenarus-preview`       | Imagem de `ca-ES` recipiente com `ca-ES-HerenaRUS` o local e a voz.       |
| `1.3.0-amd64-cs-cz-jakub-preview`           | Imagem de `cs-CZ` recipiente com `cs-CZ-Jakub` o local e a voz.           |
| `1.3.0-amd64-da-dk-hellerus-preview`        | Imagem de `da-DK` recipiente com `da-DK-HelleRUS` o local e a voz.        |
| `1.3.0-amd64-de-at-michael-preview`         | Imagem de `de-AT` recipiente com `de-AT-Michael` o local e a voz.         |
| `1.3.0-amd64-de-ch-karsten-preview`         | Imagem de `de-CH` recipiente com `de-CH-Karsten` o local e a voz.         |
| `1.3.0-amd64-de-de-hedda-preview`           | Imagem de `de-DE` recipiente com `de-DE-Hedda` o local e a voz.           |
| `1.3.0-amd64-de-de-heddarus-preview`        | Imagem de `de-DE` recipiente com `de-DE-Hedda` o local e a voz.           |
| `1.3.0-amd64-de-de-heddarus-preview`        | Imagem de `de-DE` recipiente com `de-DE-HeddaRUS` o local e a voz.        |
| `1.3.0-amd64-de-de-stefan-apollo-preview`   | Imagem de `de-DE` recipiente com `de-DE-Stefan-Apollo` o local e a voz.   |
| `1.3.0-amd64-el-gr-stefanos-preview`        | Imagem de `el-GR` recipiente com `el-GR-Stefanos` o local e a voz.        |
| `1.3.0-amd64-en-au-catherine-preview`       | Imagem de `en-AU` recipiente com `en-AU-Catherine` o local e a voz.       |
| `1.3.0-amd64-en-au-hayleyrus-preview`       | Imagem de `en-AU` recipiente com `en-AU-HayleyRUS` o local e a voz.       |
| `1.3.0-amd64-en-ca-heatherrus-preview`      | Imagem de `en-CA` recipiente com `en-CA-HeatherRUS` o local e a voz.      |
| `1.3.0-amd64-en-ca-linda-preview`           | Imagem de `en-CA` recipiente com `en-CA-Linda` o local e a voz.           |
| `1.3.0-amd64-en-gb-george-apollo-preview`   | Imagem de `en-GB` recipiente com `en-GB-George-Apollo` o local e a voz.   |
| `1.3.0-amd64-en-gb-hazelrus-preview`        | Imagem de `en-GB` recipiente com `en-GB-HazelRUS` o local e a voz.        |
| `1.3.0-amd64-en-gb-susan-apollo-preview`    | Imagem de `en-GB` recipiente com `en-GB-Susan-Apollo` o local e a voz.    |
| `1.3.0-amd64-en-ie-sean-preview`            | Imagem de `en-IE` recipiente com `en-IE-Sean` o local e a voz.            |
| `1.3.0-amd64-en-in-heera-apollo-preview`    | Imagem de `en-IN` recipiente com `en-IN-Heera-Apollo` o local e a voz.    |
| `1.3.0-amd64-en-in-priyarus-preview`        | Imagem de `en-IN` recipiente com `en-IN-PriyaRUS` o local e a voz.        |
| `1.3.0-amd64-en-in-ravi-apollo-preview`     | Imagem de `en-IN` recipiente com `en-IN-Ravi-Apollo` o local e a voz.     |
| `1.3.0-amd64-en-us-benjaminrus-preview`     | Imagem de `en-US` recipiente com `en-US-BenjaminRUS` o local e a voz.     |
| `1.3.0-amd64-en-us-guy24krus-preview`       | Imagem de `en-US` recipiente com `en-US-Guy24kRUS` o local e a voz.       |
| `1.3.0-amd64-en-us-jessa24krus-preview`     | Imagem de `en-US` recipiente com `en-US-Jessa24kRUS` o local e a voz.     |
| `1.3.0-amd64-en-us-jessarus-preview`        | Imagem de `en-US` recipiente com `en-US-JessaRUS` o local e a voz.        |
| `1.3.0-amd64-en-us-zirarus-preview`         | Imagem de `en-US` recipiente com `en-US-ZiraRUS` o local e a voz.         |
| `1.3.0-amd64-es-es-helenarus-preview`       | Imagem de `es-ES` recipiente com `es-ES-HelenaRUS` o local e a voz.       |
| `1.3.0-amd64-es-es-laura-apollo-preview`    | Imagem de `es-ES` recipiente com `es-ES-Laura-Apollo` o local e a voz.    |
| `1.3.0-amd64-es-es-pablo-apollo-preview`    | Imagem de `es-ES` recipiente com `es-ES-Pablo-Apollo` o local e a voz.    |
| `1.3.0-amd64-es-mx-hildarus-preview`        | Imagem de `es-MX` recipiente com `es-MX-HildaRUS` o local e a voz.        |
| `1.3.0-amd64-es-mx-raul-apollo-preview`     | Imagem de `es-MX` recipiente com `es-MX-Raul-Apollo` o local e a voz.     |
| `1.3.0-amd64-fi-fi-heidirus-preview`        | Imagem de `fi-FI` recipiente com `fi-FI-HeidiRUS` o local e a voz.        |
| `1.3.0-amd64-fr-ca-caroline-preview`        | Imagem de `fr-CA` recipiente com `fr-CA-Caroline` o local e a voz.        |
| `1.3.0-amd64-fr-ca-harmonierus-preview`     | Imagem de `fr-CA` recipiente com `fr-CA-HarmonieRUS` o local e a voz.     |
| `1.3.0-amd64-fr-ch-guillaume-preview`       | Imagem de `fr-CH` recipiente com `fr-CH-Guillaume` o local e a voz.       |
| `1.3.0-amd64-fr-fr-hortenserus-preview`     | Imagem de `fr-FR` recipiente com `fr-FR-HortenseRUS` o local e a voz.     |
| `1.3.0-amd64-fr-fr-julie-apollo-preview`    | Imagem de `fr-FR` recipiente com `fr-FR-Julie-Apollo` o local e a voz.    |
| `1.3.0-amd64-fr-fr-paul-apollo-preview`     | Imagem de `fr-FR` recipiente com `fr-FR-Paul-Apollo` o local e a voz.     |
| `1.3.0-amd64-he-il-asaf-preview`            | Imagem de `he-IL` recipiente com `he-IL-Asaf` o local e a voz.            |
| `1.3.0-amd64-hi-in-hemant-preview`          | Imagem de `hi-IN` recipiente com `hi-IN-Hemant` o local e a voz.          |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview`  | Imagem de `hi-IN` recipiente com `hi-IN-Kalpana-Apollo` o local e a voz.  |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview`  | Imagem de `hi-IN` recipiente com `hi-IN-Kalpana` o local e a voz.         |
| `1.3.0-amd64-hi-in-kalpana-preview`         | Imagem de `hi-IN` recipiente com `hi-IN-Kalpana` o local e a voz.         |
| `1.3.0-amd64-hr-hr-matej-preview`           | Imagem de `hr-HR` recipiente com `hr-HR-Matej` o local e a voz.           |
| `1.3.0-amd64-hu-hu-szabolcs-preview`        | Imagem de `hu-HU` recipiente com `hu-HU-Szabolcs` o local e a voz.        |
| `1.3.0-amd64-id-id-andika-preview`          | Imagem de `id-ID` recipiente com `id-ID-Andika` o local e a voz.          |
| `1.3.0-amd64-it-it-cosimo-apollo-preview`   | Imagem de `it-IT` recipiente com `it-IT-Cosimo-Apollo` o local e a voz.   |
| `1.3.0-amd64-it-it-luciarus-preview`        | Imagem de `it-IT` recipiente com `it-IT-LuciaRUS` o local e a voz.        |
| `1.3.0-amd64-ja-jp-ayumi-apollo-preview`    | Imagem de `ja-JP` recipiente com `ja-JP-Ayumi-Apollo` o local e a voz.    |
| `1.3.0-amd64-ja-jp-harukarus-preview`       | Imagem de `ja-JP` recipiente com `ja-JP-HarukaRUS` o local e a voz.       |
| `1.3.0-amd64-ja-jp-ichiro-apollo-preview`   | Imagem de `ja-JP` recipiente com `ja-JP-Ichiro-Apollo` o local e a voz.   |
| `1.3.0-amd64-ko-kr-heamirus-preview`        | Imagem de `ko-KR` recipiente com `ko-KR-HeamiRUS` o local e a voz.        |
| `1.3.0-amd64-ms-my-rizwan-preview`          | Imagem de `ms-MY` recipiente com `ms-MY-Rizwan` o local e a voz.          |
| `1.3.0-amd64-nb-no-huldarus-preview`        | Imagem de `nb-NO` recipiente com `nb-NO-HuldaRUS` o local e a voz.        |
| `1.3.0-amd64-nl-nl-hannarus-preview`        | Imagem de `nl-NL` recipiente com `nl-NL-HannaRUS` o local e a voz.        |
| `1.3.0-amd64-pl-pl-paulinarus-preview`      | Imagem de `pl-PL` recipiente com `pl-PL-PaulinaRUS` o local e a voz.      |
| `1.3.0-amd64-pt-br-daniel-apollo-preview`   | Imagem de `pt-BR` recipiente com `pt-BR-Daniel-Apollo` o local e a voz.   |
| `1.3.0-amd64-pt-br-heloisarus-preview`      | Imagem de `pt-BR` recipiente com `pt-BR-HeloisaRUS` o local e a voz.      |
| `1.3.0-amd64-pt-pt-heliarus-preview`        | Imagem de `pt-PT` recipiente com `pt-PT-HeliaRUS` o local e a voz.        |
| `1.3.0-amd64-ro-ro-andrei-preview`          | Imagem de `ro-RO` recipiente com `ro-RO-Andrei` o local e a voz.          |
| `1.3.0-amd64-ru-ru-ekaterinarus-preview`    | Imagem de `ru-RU` recipiente com `ru-RU-EkaterinaRUS` o local e a voz.    |
| `1.3.0-amd64-ru-ru-irina-apollo-preview`    | Imagem de `ru-RU` recipiente com `ru-RU-Irina-Apollo` o local e a voz.    |
| `1.3.0-amd64-ru-ru-pavel-apollo-preview`    | Imagem de `ru-RU` recipiente com `ru-RU-Pavel-Apollo` o local e a voz.    |
| `1.3.0-amd64-sk-sk-filip-preview`           | Imagem de `sk-SK` recipiente com `sk-SK-Filip` o local e a voz.           |
| `1.3.0-amd64-sl-si-lado-preview`            | Imagem de `sl-SI` recipiente com `sl-SI-Lado` o local e a voz.            |
| `1.3.0-amd64-sv-se-hedvigrus-preview`       | Imagem de `sv-SE` recipiente com `sv-SE-HedvigRUS` o local e a voz.       |
| `1.3.0-amd64-ta-in-valluvar-preview`        | Imagem de `ta-IN` recipiente com `ta-IN-Valluvar` o local e a voz.        |
| `1.3.0-amd64-te-in-chitra-preview`          | Imagem de `te-IN` recipiente com `te-IN-Chitra` o local e a voz.          |
| `1.3.0-amd64-th-th-pattara-preview`         | Imagem de `th-TH` recipiente com `th-TH-Pattara` o local e a voz.         |
| `1.3.0-amd64-tr-tr-sedarus-preview`         | Imagem de `tr-TR` recipiente com `tr-TR-SedaRUS` o local e a voz.         |
| `1.3.0-amd64-vi-vn-an-preview`              | Imagem de `vi-VN` recipiente com `vi-VN-An` o local e a voz.              |
| `1.3.0-amd64-zh-cn-huihuirus-preview`       | Imagem de `zh-CN` recipiente com `zh-CN-HuihuiRUS` o local e a voz.       |
| `1.3.0-amd64-zh-cn-kangkang-apollo-preview` | Imagem de `zh-CN` recipiente com `zh-CN-Kangkang-Apollo` o local e a voz. |
| `1.3.0-amd64-zh-cn-yaoyao-apollo-preview`   | Imagem de `zh-CN` recipiente com `zh-CN-Yaoyao-Apollo` o local e a voz.   |
| `1.3.0-amd64-zh-hk-danny-apollo-preview`    | Imagem de `zh-HK` recipiente com `zh-HK-Danny-Apollo` o local e a voz.    |
| `1.3.0-amd64-zh-hk-tracy-apollo-preview`    | Imagem de `zh-HK` recipiente com `zh-HK-Tracy-Apollo` o local e a voz.    |
| `1.3.0-amd64-zh-hk-tracyrus-preview`        | Imagem de `zh-HK` recipiente com `zh-HK-TracyRUS` o local e a voz.        |
| `1.3.0-amd64-zh-tw-hanhanrus-preview`       | Imagem de `zh-TW` recipiente com `zh-TW-HanHanRUS` o local e a voz.       |
| `1.3.0-amd64-zh-tw-yating-apollo-preview`   | Imagem de `zh-TW` recipiente com `zh-TW-Yating-Apollo` o local e a voz.   |
| `1.3.0-amd64-zh-tw-zhiwei-apollo-preview`   | Imagem de `zh-TW` recipiente com `zh-TW-Zhiwei-Apollo` o local e a voz.   |
| `1.2.0-amd64-de-de-heddarus-preview`        | Imagem de `de-DE` recipiente com `de-DE-Hedda` o local e a voz.           |
| `1.2.0-amd64-de-de-heddarus-preview`        | Imagem de `de-DE` recipiente com `de-DE-HeddaRUS` o local e a voz.        |
| `1.2.0-amd64-de-de-stefan-apollo-preview`   | Imagem de `de-DE` recipiente com `de-DE-Stefan-Apollo` o local e a voz.   |
| `1.2.0-amd64-en-au-catherine-preview`       | Imagem de `en-AU` recipiente com `en-AU-Catherine` o local e a voz.       |
| `1.2.0-amd64-en-au-hayleyrus-preview`       | Imagem de `en-AU` recipiente com `en-AU-HayleyRUS` o local e a voz.       |
| `1.2.0-amd64-en-gb-george-apollo-preview`   | Imagem de `en-GB` recipiente com `en-GB-George-Apollo` o local e a voz.   |
| `1.2.0-amd64-en-gb-hazelrus-preview`        | Imagem de `en-GB` recipiente com `en-GB-HazelRUS` o local e a voz.        |
| `1.2.0-amd64-en-gb-susan-apollo-preview`    | Imagem de `en-GB` recipiente com `en-GB-Susan-Apollo` o local e a voz.    |
| `1.2.0-amd64-en-in-heera-apollo-preview`    | Imagem de `en-IN` recipiente com `en-IN-Heera-Apollo` o local e a voz.    |
| `1.2.0-amd64-en-in-priyarus-preview`        | Imagem de `en-IN` recipiente com `en-IN-PriyaRUS` o local e a voz.        |
| `1.2.0-amd64-en-in-ravi-apollo-preview`     | Imagem de `en-IN` recipiente com `en-IN-Ravi-Apollo` o local e a voz.     |
| `1.2.0-amd64-en-us-benjaminrus-preview`     | Imagem de `en-US` recipiente com `en-US-BenjaminRUS` o local e a voz.     |
| `1.2.0-amd64-en-us-guy24krus-preview`       | Imagem de `en-US` recipiente com `en-US-Guy24kRUS` o local e a voz.       |
| `1.2.0-amd64-en-us-jessa24krus-preview`     | Imagem de `en-US` recipiente com `en-US-Jessa24kRUS` o local e a voz.     |
| `1.2.0-amd64-en-us-jessarus-preview`        | Imagem de `en-US` recipiente com `en-US-JessaRUS` o local e a voz.        |
| `1.2.0-amd64-en-us-zirarus-preview`         | Imagem de `en-US` recipiente com `en-US-ZiraRUS` o local e a voz.         |
| `1.2.0-amd64-es-es-helenarus-preview`       | Imagem de `es-ES` recipiente com `es-ES-HelenaRUS` o local e a voz.       |
| `1.2.0-amd64-es-es-laura-apollo-preview`    | Imagem de `es-ES` recipiente com `es-ES-Laura-Apollo` o local e a voz.    |
| `1.2.0-amd64-es-es-pablo-apollo-preview`    | Imagem de `es-ES` recipiente com `es-ES-Pablo-Apollo` o local e a voz.    |
| `1.2.0-amd64-es-mx-hildarus-preview`        | Imagem de `es-MX` recipiente com `es-MX-HildaRUS` o local e a voz.        |
| `1.2.0-amd64-es-mx-raul-apollo-preview`     | Imagem de `es-MX` recipiente com `es-MX-Raul-Apollo` o local e a voz.     |
| `1.2.0-amd64-fr-ca-caroline-preview`        | Imagem de `fr-CA` recipiente com `fr-CA-Caroline` o local e a voz.        |
| `1.2.0-amd64-fr-ca-harmonierus-preview`     | Imagem de `fr-CA` recipiente com `fr-CA-HarmonieRUS` o local e a voz.     |
| `1.2.0-amd64-fr-fr-hortenserus-preview`     | Imagem de `fr-FR` recipiente com `fr-FR-HortenseRUS` o local e a voz.     |
| `1.2.0-amd64-fr-fr-julie-apollo-preview`    | Imagem de `fr-FR` recipiente com `fr-FR-Julie-Apollo` o local e a voz.    |
| `1.2.0-amd64-fr-fr-paul-apollo-preview`     | Imagem de `fr-FR` recipiente com `fr-FR-Paul-Apollo` o local e a voz.     |
| `1.2.0-amd64-it-it-cosimo-apollo-preview`   | Imagem de `it-IT` recipiente com `it-IT-Cosimo-Apollo` o local e a voz.   |
| `1.2.0-amd64-it-it-luciarus-preview`        | Imagem de `it-IT` recipiente com `it-IT-LuciaRUS` o local e a voz.        |
| `1.2.0-amd64-ja-jp-ayumi-apollo-preview`    | Imagem de `ja-JP` recipiente com `ja-JP-Ayumi-Apollo` o local e a voz.    |
| `1.2.0-amd64-ja-jp-harukarus-preview`       | Imagem de `ja-JP` recipiente com `ja-JP-HarukaRUS` o local e a voz.       |
| `1.2.0-amd64-ja-jp-ichiro-apollo-preview`   | Imagem de `ja-JP` recipiente com `ja-JP-Ichiro-Apollo` o local e a voz.   |
| `1.2.0-amd64-ko-kr-heamirus-preview`        | Imagem de `ko-KR` recipiente com `ko-KR-HeamiRUS` o local e a voz.        |
| `1.2.0-amd64-pt-br-daniel-apollo-preview`   | Imagem de `pt-BR` recipiente com `pt-BR-Daniel-Apollo` o local e a voz.   |
| `1.2.0-amd64-pt-br-heloisarus-preview`      | Imagem de `pt-BR` recipiente com `pt-BR-HeloisaRUS` o local e a voz.      |
| `1.2.0-amd64-zh-cn-huihuirus-preview`       | Imagem de `zh-CN` recipiente com `zh-CN-HuihuiRUS` o local e a voz.       |
| `1.2.0-amd64-zh-cn-kangkang-apollo-preview` | Imagem de `zh-CN` recipiente com `zh-CN-Kangkang-Apollo` o local e a voz. |
| `1.2.0-amd64-zh-cn-yaoyao-apollo-preview`   | Imagem de `zh-CN` recipiente com `zh-CN-Yaoyao-Apollo` o local e a voz.   |
| `1.1.0-amd64-de-de-hedda-preview`           | Imagem de `de-DE` recipiente com `de-DE-Hedda` o local e a voz.           |
| `1.1.0-amd64-de-de-heddarus-preview`        | Imagem de `de-DE` recipiente com `de-DE-Hedda` o local e a voz.           |
| `1.1.0-amd64-de-de-heddarus-preview`        | Imagem de `de-DE` recipiente com `de-DE-HeddaRUS` o local e a voz.        |
| `1.1.0-amd64-de-de-stefan-apollo-preview`   | Imagem de `de-DE` recipiente com `de-DE-Stefan-Apollo` o local e a voz.   |
| `1.1.0-amd64-en-au-catherine-preview`       | Imagem de `en-AU` recipiente com `en-AU-Catherine` o local e a voz.       |
| `1.1.0-amd64-en-au-hayleyrus-preview`       | Imagem de `en-AU` recipiente com `en-AU-HayleyRUS` o local e a voz.       |
| `1.1.0-amd64-en-gb-george-apollo-preview`   | Imagem de `en-GB` recipiente com `en-GB-George-Apollo` o local e a voz.   |
| `1.1.0-amd64-en-gb-hazelrus-preview`        | Imagem de `en-GB` recipiente com `en-GB-HazelRUS` o local e a voz.        |
| `1.1.0-amd64-en-gb-susan-apollo-preview`    | Imagem de `en-GB` recipiente com `en-GB-Susan-Apollo` o local e a voz.    |
| `1.1.0-amd64-en-in-heera-apollo-preview`    | Imagem de `en-IN` recipiente com `en-IN-Heera-Apollo` o local e a voz.    |
| `1.1.0-amd64-en-in-priyarus-preview`        | Imagem de `en-IN` recipiente com `en-IN-PriyaRUS` o local e a voz.        |
| `1.1.0-amd64-en-in-ravi-apollo-preview`     | Imagem de `en-IN` recipiente com `en-IN-Ravi-Apollo` o local e a voz.     |
| `1.1.0-amd64-en-us-benjaminrus-preview`     | Imagem de `en-US` recipiente com `en-US-BenjaminRUS` o local e a voz.     |
| `1.1.0-amd64-en-us-guy24krus-preview`       | Imagem de `en-US` recipiente com `en-US-Guy24kRUS` o local e a voz.       |
| `1.1.0-amd64-en-us-jessa24krus-preview`     | Imagem de `en-US` recipiente com `en-US-Jessa24kRUS` o local e a voz.     |
| `1.1.0-amd64-en-us-jessarus-preview`        | Imagem de `en-US` recipiente com `en-US-JessaRUS` o local e a voz.        |
| `1.1.0-amd64-en-us-zirarus-preview`         | Imagem de `en-US` recipiente com `en-US-ZiraRUS` o local e a voz.         |
| `1.1.0-amd64-es-es-helenarus-preview`       | Imagem de `es-ES` recipiente com `es-ES-HelenaRUS` o local e a voz.       |
| `1.1.0-amd64-es-es-laura-apollo-preview`    | Imagem de `es-ES` recipiente com `es-ES-Laura-Apollo` o local e a voz.    |
| `1.1.0-amd64-es-es-pablo-apollo-preview`    | Imagem de `es-ES` recipiente com `es-ES-Pablo-Apollo` o local e a voz.    |
| `1.1.0-amd64-es-mx-hildarus-preview`        | Imagem de `es-MX` recipiente com `es-MX-HildaRUS` o local e a voz.        |
| `1.1.0-amd64-es-mx-raul-apollo-preview`     | Imagem de `es-MX` recipiente com `es-MX-Raul-Apollo` o local e a voz.     |
| `1.1.0-amd64-fr-ca-caroline-preview`        | Imagem de `fr-CA` recipiente com `fr-CA-Caroline` o local e a voz.        |
| `1.1.0-amd64-fr-ca-harmonierus-preview`     | Imagem de `fr-CA` recipiente com `fr-CA-HarmonieRUS` o local e a voz.     |
| `1.1.0-amd64-fr-fr-hortenserus-preview`     | Imagem de `fr-FR` recipiente com `fr-FR-HortenseRUS` o local e a voz.     |
| `1.1.0-amd64-fr-fr-julie-apollo-preview`    | Imagem de `fr-FR` recipiente com `fr-FR-Julie-Apollo` o local e a voz.    |
| `1.1.0-amd64-fr-fr-paul-apollo-preview`     | Imagem de `fr-FR` recipiente com `fr-FR-Paul-Apollo` o local e a voz.     |
| `1.1.0-amd64-it-it-cosimo-apollo-preview`   | Imagem de `it-IT` recipiente com `it-IT-Cosimo-Apollo` o local e a voz.   |
| `1.1.0-amd64-it-it-luciarus-preview`        | Imagem de `it-IT` recipiente com `it-IT-LuciaRUS` o local e a voz.        |
| `1.1.0-amd64-ja-jp-ayumi-apollo-preview`    | Imagem de `ja-JP` recipiente com `ja-JP-Ayumi-Apollo` o local e a voz.    |
| `1.1.0-amd64-ja-jp-harukarus-preview`       | Imagem de `ja-JP` recipiente com `ja-JP-HarukaRUS` o local e a voz.       |
| `1.1.0-amd64-ja-jp-ichiro-apollo-preview`   | Imagem de `ja-JP` recipiente com `ja-JP-Ichiro-Apollo` o local e a voz.   |
| `1.1.0-amd64-ko-kr-heamirus-preview`        | Imagem de `ko-KR` recipiente com `ko-KR-HeamiRUS` o local e a voz.        |
| `1.1.0-amd64-pt-br-daniel-apollo-preview`   | Imagem de `pt-BR` recipiente com `pt-BR-Daniel-Apollo` o local e a voz.   |
| `1.1.0-amd64-pt-br-heloisarus-preview`      | Imagem de `pt-BR` recipiente com `pt-BR-HeloisaRUS` o local e a voz.      |
| `1.1.0-amd64-zh-cn-huihuirus-preview`       | Imagem de `zh-CN` recipiente com `zh-CN-HuihuiRUS` o local e a voz.       |
| `1.1.0-amd64-zh-cn-kangkang-apollo-preview` | Imagem de `zh-CN` recipiente com `zh-CN-Kangkang-Apollo` o local e a voz. |
| `1.1.0-amd64-zh-cn-yaoyao-apollo-preview`   | Imagem de `zh-CN` recipiente com `zh-CN-Yaoyao-Apollo` o local e a voz.   |
| `1.0.0-amd64-en-us-benjaminrus-preview`     | Imagem de `en-US` recipiente com `en-US-BenjaminRUS` o local e a voz.     |
| `1.0.0-amd64-en-us-guy24krus-preview`       | Imagem de `en-US` recipiente com `en-US-Guy24kRUS` o local e a voz.       |
| `1.0.0-amd64-en-us-jessa24krus-preview`     | Imagem de `en-US` recipiente com `en-US-Jessa24kRUS` o local e a voz.     |
| `1.0.0-amd64-en-us-jessarus-preview`        | Imagem de `en-US` recipiente com `en-US-JessaRUS` o local e a voz.        |
| `1.0.0-amd64-en-us-zirarus-preview`         | Imagem de `en-US` recipiente com `en-US-ZiraRUS` o local e a voz.         |
| `1.0.0-amd64-zh-cn-huihuirus-preview`       | Imagem de `zh-CN` recipiente com `zh-CN-HuihuiRUS` o local e a voz.       |
| `1.0.0-amd64-zh-cn-kangkang-apollo-preview` | Imagem de `zh-CN` recipiente com `zh-CN-Kangkang-Apollo` o local e a voz. |
| `1.0.0-amd64-zh-cn-yaoyao-apollo-preview`   | Imagem de `zh-CN` recipiente com `zh-CN-Yaoyao-Apollo` o local e a voz.   |

## <a name="key-phrase-extraction"></a>Extração de Expressões-Chave

A imagem do recipiente de [extração de frases-chave][ta-kp] pode ser encontrada no sindicato do registo do `mcr.microsoft.com` contentor. Reside dentro do `azure-cognitive-services` repositório e `keyphrase`é nomeado. O nome da imagem `mcr.microsoft.com/azure-cognitive-services/keyphrase`do recipiente totalmente qualificado é, .

Esta imagem do recipiente tem as seguintes etiquetas disponíveis:

| Etiquetas de imagem                    | Notas |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

## <a name="language-detection"></a>Deteção de Idioma

A imagem do recipiente de `mcr.microsoft.com` deteção de [idiomas][ta-la] pode ser encontrada no sindicato do registo de contentores. Reside dentro do `azure-cognitive-services` repositório e `language`é nomeado. O nome da imagem `mcr.microsoft.com/azure-cognitive-services/language`do recipiente totalmente qualificado é, .

Esta imagem do recipiente tem as seguintes etiquetas disponíveis:

| Etiquetas de imagem                    | Notas |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

## <a name="sentiment-analysis"></a>Análise de Sentimentos

A imagem do recipiente de `mcr.microsoft.com` análise de [sentimentopode][ta-se] ser encontrada no sindicato do registo de contentores. Reside dentro do `azure-cognitive-services` repositório e `sentiment`é nomeado. O nome da imagem `mcr.microsoft.com/azure-cognitive-services/sentiment`do recipiente totalmente qualificado é, .

Esta imagem do recipiente tem as seguintes etiquetas disponíveis:

| Etiquetas de imagem                    | Notas |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-stt]: ../speech-service/speech-container-howto.md?tabs=stt
[sp-cstt]: ../speech-service/speech-container-howto.md?tabs=cstt
[sp-tts]: ../speech-service/speech-container-howto.md?tabs=tts
[sp-ctts]: ../speech-service/speech-container-howto.md?tabs=ctts
[ta-kp]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-la]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-se]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment
