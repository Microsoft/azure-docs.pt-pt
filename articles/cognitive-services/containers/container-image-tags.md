---
title: Etiquetas de imagem de contentor dos Serviços Cognitivos
titleSuffix: Azure Cognitive Services
description: Uma listagem abrangente de todas as etiquetas de imagem do recipiente do Serviço Cognitivo.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: eed2223dbfeee307b552cdd010530f27c379f5fe
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79219447"
---
# <a name="azure-cognitive-services-container-image-tags"></a>Etiquetas de imagem de contentores de serviços cognitivos Azure

O Azure Cognitive Services oferece muitas imagens de contentores. Os registos de contentores e os repositórios correspondentes variam entre imagens de contentores. Cada nome de imagem de recipiente oferece várias etiquetas. Uma etiqueta de imagem de recipiente é um mecanismo de versonio da imagem do recipiente. Este artigo destina-se a ser usado como uma referência abrangente para a listagem de todas as imagens de contentores dos Serviços Cognitivos e suas etiquetas disponíveis.

> [!TIP]
> Ao utilizar [`docker pull`, ](https://docs.docker.com/engine/reference/commandline/pull/)preste muita atenção ao invólucro do registo do contentor, do repositório, do nome da imagem do recipiente e da etiqueta correspondente - uma vez que são **sensíveis ao caso**.

## <a name="anomaly-detector"></a>Detetor de Anomalias

A imagem do recipiente do Detetor de [Anomalias][ad-containers] pode ser encontrada no registo de contentores `containerpreview.azurecr.io`. Reside no repositório `microsoft` e é nomeado `cognitive-services-anomaly-detector`. O nome de imagem do recipiente totalmente qualificado é, `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector`.

Esta imagem do recipiente tem as seguintes etiquetas disponíveis:

| Etiquetas de imagem                    | Notas |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008500001-amd64-preview` |       |

## <a name="computer-vision"></a>Imagem Digitalizada

A imagem do recipiente [Computer Vision][cv-containers] pode ser encontrada no registo de contentores `containerpreview.azurecr.io`. Reside no repositório `microsoft` e é nomeado `cognitive-services-read`. O nome de imagem do recipiente totalmente qualificado é, `containerpreview.azurecr.io/microsoft/cognitive-services-read`.

Esta imagem do recipiente tem as seguintes etiquetas disponíveis:

| Etiquetas de imagem                    | Notas |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.011580001-amd64-preview` |       |
| `1.1.009920003-amd64-preview` |       |
| `1.1.009910003-amd64-preview` |       |

## <a name="face"></a>Rostos

A imagem do recipiente [Face][fa-containers] pode ser encontrada no registo do recipiente `containerpreview.azurecr.io`. Reside no repositório `microsoft` e é nomeado `cognitive-services-face`. O nome de imagem do recipiente totalmente qualificado é, `containerpreview.azurecr.io/microsoft/cognitive-services-face`.

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

A imagem do recipiente ['Reconhecimento de formulários'][fr-containers] pode ser encontrada no registo de contentores `containerpreview.azurecr.io`. Reside no repositório `microsoft` e é nomeado `cognitive-services-form-recognizer`. O nome de imagem do recipiente totalmente qualificado é, `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer`.

Esta imagem do recipiente tem as seguintes etiquetas disponíveis:

| Etiquetas de imagem                    | Notas |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008640001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |

## <a name="language-understanding-luis"></a>Compreensão de Idiomas (LUIS)

A imagem do contentor [LUIS][lu-containers] pode ser encontrada no sindicato `mcr.microsoft.com` de registo de contentores. Reside no repositório `azure-cognitive-services` e é nomeado `luis`. O nome de imagem do recipiente totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/luis`.

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

A imagem personalizada do recipiente [de discurso a texto][sp-cstt] pode ser encontrada no registo de contentores `containerpreview.azurecr.io`. Reside no repositório `microsoft` e é nomeado `cognitive-services-custom-speech-to-text`. O nome de imagem do recipiente totalmente qualificado é, `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text`.

Esta imagem do recipiente tem as seguintes etiquetas disponíveis:

| Etiquetas de imagem            | Notas |
|-----------------------|:------|
| `latest`              |       |
| `2.1.1-amd64-preview` |       |
| `2.1.0-amd64-preview` |       |
| `2.0.2-amd64-preview` |       |
| `2.0.0-amd64-preview` |       |

## <a name="custom-text-to-speech"></a>Texto-a-fala personalizado

A imagem personalizada do recipiente [texto-a-fala][sp-ctts] pode ser encontrada no registo de contentores `containerpreview.azurecr.io`. Reside no repositório `microsoft` e é nomeado `cognitive-services-custom-text-to-speech`. O nome de imagem do recipiente totalmente qualificado é, `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech`.

Esta imagem do recipiente tem as seguintes etiquetas disponíveis:

| Etiquetas de imagem            | Notas |
|-----------------------|:------|
| `latest`              |       |
| `1.3.0-amd64-preview` |       |

## <a name="speech-to-text"></a>Conversão de voz em texto

A imagem do contentor [do discurso ao texto][sp-stt] pode ser encontrada no registo de contentores `containerpreview.azurecr.io`. Reside no repositório `microsoft` e é nomeado `cognitive-services-speech-to-text`. O nome de imagem do recipiente totalmente qualificado é, `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text`.

Esta imagem do recipiente tem as seguintes etiquetas disponíveis:

| Etiquetas de imagem                  | Notas                                    |
|-----------------------------|:-----------------------------------------|
| `latest`                    | Imagem de contentor com o `en-US` local. |
| `2.1.1-amd64-ar-ae-preview` | Imagem de contentor com o `ar-AE` local. |
| `2.1.1-amd64-ar-eg-preview` | Imagem de contentor com o `ar-EG` local. |
| `2.1.1-amd64-ar-kw-preview` | Imagem de contentor com o `ar-KW` local. |
| `2.1.1-amd64-ar-qa-preview` | Imagem de contentor com o `ar-QA` local. |
| `2.1.1-amd64-ar-sa-preview` | Imagem de contentor com o `ar-SA` local. |
| `2.1.1-amd64-ca-es-preview` | Imagem de contentor com o `ca-ES` local. |
| `2.1.1-amd64-da-dk-preview` | Imagem de contentor com o `da-DK` local. |
| `2.1.1-amd64-de-de-preview` | Imagem de contentor com o `de-DE` local. |
| `2.1.1-amd64-en-au-preview` | Imagem de contentor com o `en-AU` local. |
| `2.1.1-amd64-en-ca-preview` | Imagem de contentor com o `en-CA` local. |
| `2.1.1-amd64-en-gb-preview` | Imagem de contentor com o `en-GB` local. |
| `2.1.1-amd64-en-in-preview` | Imagem de contentor com o `en-IN` local. |
| `2.1.1-amd64-en-nz-preview` | Imagem de contentor com o `en-NZ` local. |
| `2.1.1-amd64-en-us-preview` | Imagem de contentor com o `en-US` local. |
| `2.1.1-amd64-es-es-preview` | Imagem de contentor com o `es-ES` local. |
| `2.1.1-amd64-es-mx-preview` | Imagem de contentor com o `es-MX` local. |
| `2.1.1-amd64-fi-fi-preview` | Imagem de contentor com o `fi-FI` local. |
| `2.1.1-amd64-fr-ca-preview` | Imagem de contentor com o `fr-CA` local. |
| `2.1.1-amd64-fr-fr-preview` | Imagem de contentor com o `fr-FR` local. |
| `2.1.1-amd64-gu-in-preview` | Imagem de contentor com o `gu-IN` local. |
| `2.1.1-amd64-hi-in-preview` | Imagem de contentor com o `hi-IN` local. |
| `2.1.1-amd64-it-it-preview` | Imagem de contentor com o `it-IT` local. |
| `2.1.1-amd64-ja-jp-preview` | Imagem de contentor com o `ja-JP` local. |
| `2.1.1-amd64-ko-kr-preview` | Imagem de contentor com o `ko-KR` local. |
| `2.1.1-amd64-mr-in-preview` | Imagem de contentor com o `mr-IN` local. |
| `2.1.1-amd64-nb-no-preview` | Imagem de contentor com o `nb-NO` local. |
| `2.1.1-amd64-nl-nl-preview` | Imagem de contentor com o `nl-NL` local. |
| `2.1.1-amd64-pl-pl-preview` | Imagem de contentor com o `pl-PL` local. |
| `2.1.1-amd64-pt-br-preview` | Imagem de contentor com o `pt-BR` local. |
| `2.1.1-amd64-pt-pt-preview` | Imagem de contentor com o `pt-PT` local. |
| `2.1.1-amd64-ru-ru-preview` | Imagem de contentor com o `ru-RU` local. |
| `2.1.1-amd64-sv-se-preview` | Imagem de contentor com o `sv-SE` local. |
| `2.1.1-amd64-ta-in-preview` | Imagem de contentor com o `ta-IN` local. |
| `2.1.1-amd64-te-in-preview` | Imagem de contentor com o `te-IN` local. |
| `2.1.1-amd64-th-th-preview` | Imagem de contentor com o `th-TH` local. |
| `2.1.1-amd64-tr-tr-preview` | Imagem de contentor com o `tr-TR` local. |
| `2.1.1-amd64-zh-cn-preview` | Imagem de contentor com o `zh-CN` local. |
| `2.1.1-amd64-zh-hk-preview` | Imagem de contentor com o `zh-HK` local. |
| `2.1.1-amd64-zh-tw-preview` | Imagem de contentor com o `zh-TW` local. |
| `2.1.0-amd64-ar-ae-preview` | Imagem de contentor com o `ar-AE` local. |
| `2.1.0-amd64-ar-eg-preview` | Imagem de contentor com o `ar-EG` local. |
| `2.1.0-amd64-ar-kw-preview` | Imagem de contentor com o `ar-KW` local. |
| `2.1.0-amd64-ar-qa-preview` | Imagem de contentor com o `ar-QA` local. |
| `2.1.0-amd64-ar-sa-preview` | Imagem de contentor com o `ar-SA` local. |
| `2.1.0-amd64-ca-es-preview` | Imagem de contentor com o `ca-ES` local. |
| `2.1.0-amd64-da-dk-preview` | Imagem de contentor com o `da-DK` local. |
| `2.1.0-amd64-de-de-preview` | Imagem de contentor com o `de-DE` local. |
| `2.1.0-amd64-en-au-preview` | Imagem de contentor com o `en-AU` local. |
| `2.1.0-amd64-en-ca-preview` | Imagem de contentor com o `en-CA` local. |
| `2.1.0-amd64-en-gb-preview` | Imagem de contentor com o `en-GB` local. |
| `2.1.0-amd64-en-in-preview` | Imagem de contentor com o `en-IN` local. |
| `2.1.0-amd64-en-nz-preview` | Imagem de contentor com o `en-NZ` local. |
| `2.1.0-amd64-en-us-preview` | Imagem de contentor com o `en-US` local. |
| `2.1.0-amd64-es-es-preview` | Imagem de contentor com o `es-ES` local. |
| `2.1.0-amd64-es-mx-preview` | Imagem de contentor com o `es-MX` local. |
| `2.1.0-amd64-fi-fi-preview` | Imagem de contentor com o `fi-FI` local. |
| `2.1.0-amd64-fr-ca-preview` | Imagem de contentor com o `fr-CA` local. |
| `2.1.0-amd64-fr-fr-preview` | Imagem de contentor com o `fr-FR` local. |
| `2.1.0-amd64-gu-in-preview` | Imagem de contentor com o `gu-IN` local. |
| `2.1.0-amd64-hi-in-preview` | Imagem de contentor com o `hi-IN` local. |
| `2.1.0-amd64-it-it-preview` | Imagem de contentor com o `it-IT` local. |
| `2.1.0-amd64-ja-jp-preview` | Imagem de contentor com o `ja-JP` local. |
| `2.1.0-amd64-ko-kr-preview` | Imagem de contentor com o `ko-KR` local. |
| `2.1.0-amd64-mr-in-preview` | Imagem de contentor com o `mr-IN` local. |
| `2.1.0-amd64-nb-no-preview` | Imagem de contentor com o `nb-NO` local. |
| `2.1.0-amd64-nl-nl-preview` | Imagem de contentor com o `nl-NL` local. |
| `2.1.0-amd64-pl-pl-preview` | Imagem de contentor com o `pl-PL` local. |
| `2.1.0-amd64-pt-br-preview` | Imagem de contentor com o `pt-BR` local. |
| `2.1.0-amd64-pt-pt-preview` | Imagem de contentor com o `pt-PT` local. |
| `2.1.0-amd64-ru-ru-preview` | Imagem de contentor com o `ru-RU` local. |
| `2.1.0-amd64-sv-se-preview` | Imagem de contentor com o `sv-SE` local. |
| `2.1.0-amd64-ta-in-preview` | Imagem de contentor com o `ta-IN` local. |
| `2.1.0-amd64-te-in-preview` | Imagem de contentor com o `te-IN` local. |
| `2.1.0-amd64-th-th-preview` | Imagem de contentor com o `th-TH` local. |
| `2.1.0-amd64-tr-tr-preview` | Imagem de contentor com o `tr-TR` local. |
| `2.1.0-amd64-zh-cn-preview` | Imagem de contentor com o `zh-CN` local. |
| `2.1.0-amd64-zh-hk-preview` | Imagem de contentor com o `zh-HK` local. |
| `2.1.0-amd64-zh-tw-preview` | Imagem de contentor com o `zh-TW` local. |
| `2.0.3-amd64-ja-jp-preview` | Imagem de contentor com o `ja-JP` local. |
| `2.0.2-amd64-ar-ae-preview` | Imagem de contentor com o `ar-AE` local. |
| `2.0.2-amd64-ar-eg-preview` | Imagem de contentor com o `ar-EG` local. |
| `2.0.2-amd64-ar-kw-preview` | Imagem de contentor com o `ar-KW` local. |
| `2.0.2-amd64-ar-qa-preview` | Imagem de contentor com o `ar-QA` local. |
| `2.0.2-amd64-ar-sa-preview` | Imagem de contentor com o `ar-SA` local. |
| `2.0.2-amd64-ca-es-preview` | Imagem de contentor com o `ca-ES` local. |
| `2.0.2-amd64-da-dk-preview` | Imagem de contentor com o `da-DK` local. |
| `2.0.2-amd64-de-de-preview` | Imagem de contentor com o `de-DE` local. |
| `2.0.2-amd64-en-au-preview` | Imagem de contentor com o `en-AU` local. |
| `2.0.2-amd64-en-ca-preview` | Imagem de contentor com o `en-CA` local. |
| `2.0.2-amd64-en-gb-preview` | Imagem de contentor com o `en-GB` local. |
| `2.0.2-amd64-en-in-preview` | Imagem de contentor com o `en-IN` local. |
| `2.0.2-amd64-en-nz-preview` | Imagem de contentor com o `en-NZ` local. |
| `2.0.2-amd64-en-us-preview` | Imagem de contentor com o `en-US` local. |
| `2.0.2-amd64-es-es-preview` | Imagem de contentor com o `es-ES` local. |
| `2.0.2-amd64-es-mx-preview` | Imagem de contentor com o `es-MX` local. |
| `2.0.2-amd64-fi-fi-preview` | Imagem de contentor com o `fi-FI` local. |
| `2.0.2-amd64-fr-ca-preview` | Imagem de contentor com o `fr-CA` local. |
| `2.0.2-amd64-fr-fr-preview` | Imagem de contentor com o `fr-FR` local. |
| `2.0.2-amd64-gu-in-preview` | Imagem de contentor com o `gu-IN` local. |
| `2.0.2-amd64-hi-in-preview` | Imagem de contentor com o `hi-IN` local. |
| `2.0.2-amd64-it-it-preview` | Imagem de contentor com o `it-IT` local. |
| `2.0.2-amd64-ja-jp-preview` | Imagem de contentor com o `ja-JP` local. |
| `2.0.2-amd64-ko-kr-preview` | Imagem de contentor com o `ko-KR` local. |
| `2.0.2-amd64-mr-in-preview` | Imagem de contentor com o `mr-IN` local. |
| `2.0.2-amd64-nb-no-preview` | Imagem de contentor com o `nb-NO` local. |
| `2.0.2-amd64-nl-nl-preview` | Imagem de contentor com o `nl-NL` local. |
| `2.0.2-amd64-pl-pl-preview` | Imagem de contentor com o `pl-PL` local. |
| `2.0.2-amd64-pt-br-preview` | Imagem de contentor com o `pt-BR` local. |
| `2.0.2-amd64-pt-pt-preview` | Imagem de contentor com o `pt-PT` local. |
| `2.0.2-amd64-ru-ru-preview` | Imagem de contentor com o `ru-RU` local. |
| `2.0.2-amd64-sv-se-preview` | Imagem de contentor com o `sv-SE` local. |
| `2.0.2-amd64-ta-in-preview` | Imagem de contentor com o `ta-IN` local. |
| `2.0.2-amd64-te-in-preview` | Imagem de contentor com o `te-IN` local. |
| `2.0.2-amd64-th-th-preview` | Imagem de contentor com o `th-TH` local. |
| `2.0.2-amd64-tr-tr-preview` | Imagem de contentor com o `tr-TR` local. |
| `2.0.2-amd64-zh-cn-preview` | Imagem de contentor com o `zh-CN` local. |
| `2.0.2-amd64-zh-hk-preview` | Imagem de contentor com o `zh-HK` local. |
| `2.0.2-amd64-zh-tw-preview` | Imagem de contentor com o `zh-TW` local. |
| `2.0.1-amd64-ar-ae-preview` | Imagem de contentor com o `ar-AE` local. |
| `2.0.1-amd64-ar-eg-preview` | Imagem de contentor com o `ar-EG` local. |
| `2.0.1-amd64-ar-kw-preview` | Imagem de contentor com o `ar-KW` local. |
| `2.0.1-amd64-ar-qa-preview` | Imagem de contentor com o `ar-QA` local. |
| `2.0.1-amd64-ar-sa-preview` | Imagem de contentor com o `ar-SA` local. |
| `2.0.1-amd64-ca-es-preview` | Imagem de contentor com o `ca-ES` local. |
| `2.0.1-amd64-da-dk-preview` | Imagem de contentor com o `da-DK` local. |
| `2.0.1-amd64-de-de-preview` | Imagem de contentor com o `de-DE` local. |
| `2.0.1-amd64-en-au-preview` | Imagem de contentor com o `en-AU` local. |
| `2.0.1-amd64-en-ca-preview` | Imagem de contentor com o `en-CA` local. |
| `2.0.1-amd64-en-gb-preview` | Imagem de contentor com o `en-GB` local. |
| `2.0.1-amd64-en-in-preview` | Imagem de contentor com o `en-IN` local. |
| `2.0.1-amd64-en-nz-preview` | Imagem de contentor com o `en-NZ` local. |
| `2.0.1-amd64-en-us-preview` | Imagem de contentor com o `en-US` local. |
| `2.0.1-amd64-es-es-preview` | Imagem de contentor com o `es-ES` local. |
| `2.0.1-amd64-es-mx-preview` | Imagem de contentor com o `es-MX` local. |
| `2.0.1-amd64-fi-fi-preview` | Imagem de contentor com o `fi-FI` local. |
| `2.0.1-amd64-fr-ca-preview` | Imagem de contentor com o `fr-CA` local. |
| `2.0.1-amd64-fr-fr-preview` | Imagem de contentor com o `fr-FR` local. |
| `2.0.1-amd64-gu-in-preview` | Imagem de contentor com o `gu-IN` local. |
| `2.0.1-amd64-hi-in-preview` | Imagem de contentor com o `hi-IN` local. |
| `2.0.1-amd64-it-it-preview` | Imagem de contentor com o `it-IT` local. |
| `2.0.1-amd64-ja-jp-preview` | Imagem de contentor com o `ja-JP` local. |
| `2.0.1-amd64-ko-kr-preview` | Imagem de contentor com o `ko-KR` local. |
| `2.0.1-amd64-mr-in-preview` | Imagem de contentor com o `mr-IN` local. |
| `2.0.1-amd64-nb-no-preview` | Imagem de contentor com o `nb-NO` local. |
| `2.0.1-amd64-nl-nl-preview` | Imagem de contentor com o `nl-NL` local. |
| `2.0.1-amd64-pl-pl-preview` | Imagem de contentor com o `pl-PL` local. |
| `2.0.1-amd64-pt-br-preview` | Imagem de contentor com o `pt-BR` local. |
| `2.0.1-amd64-pt-pt-preview` | Imagem de contentor com o `pt-PT` local. |
| `2.0.1-amd64-ru-ru-preview` | Imagem de contentor com o `ru-RU` local. |
| `2.0.1-amd64-sv-se-preview` | Imagem de contentor com o `sv-SE` local. |
| `2.0.1-amd64-ta-in-preview` | Imagem de contentor com o `ta-IN` local. |
| `2.0.1-amd64-te-in-preview` | Imagem de contentor com o `te-IN` local. |
| `2.0.1-amd64-th-th-preview` | Imagem de contentor com o `th-TH` local. |
| `2.0.1-amd64-tr-tr-preview` | Imagem de contentor com o `tr-TR` local. |
| `2.0.1-amd64-zh-cn-preview` | Imagem de contentor com o `zh-CN` local. |
| `2.0.1-amd64-zh-hk-preview` | Imagem de contentor com o `zh-HK` local. |
| `2.0.1-amd64-zh-tw-preview` | Imagem de contentor com o `zh-TW` local. |
| `2.0.0-amd64-ar-eg-preview` | Imagem de contentor com o `ar-EG` local. |
| `2.0.0-amd64-ca-es-preview` | Imagem de contentor com o `ca-ES` local. |
| `2.0.0-amd64-da-dk-preview` | Imagem de contentor com o `da-DK` local. |
| `2.0.0-amd64-de-de-preview` | Imagem de contentor com o `de-DE` local. |
| `2.0.0-amd64-en-au-preview` | Imagem de contentor com o `en-AU` local. |
| `2.0.0-amd64-en-ca-preview` | Imagem de contentor com o `en-CA` local. |
| `2.0.0-amd64-en-gb-preview` | Imagem de contentor com o `en-GB` local. |
| `2.0.0-amd64-en-in-preview` | Imagem de contentor com o `en-IN` local. |
| `2.0.0-amd64-en-nz-preview` | Imagem de contentor com o `en-NZ` local. |
| `2.0.0-amd64-en-us-preview` | Imagem de contentor com o `en-US` local. |
| `2.0.0-amd64-es-es-preview` | Imagem de contentor com o `es-ES` local. |
| `2.0.0-amd64-es-mx-preview` | Imagem de contentor com o `es-MX` local. |
| `2.0.0-amd64-fi-fi-preview` | Imagem de contentor com o `fi-FI` local. |
| `2.0.0-amd64-fr-ca-preview` | Imagem de contentor com o `fr-CA` local. |
| `2.0.0-amd64-fr-fr-preview` | Imagem de contentor com o `fr-FR` local. |
| `2.0.0-amd64-hi-in-preview` | Imagem de contentor com o `hi-IN` local. |
| `2.0.0-amd64-it-it-preview` | Imagem de contentor com o `it-IT` local. |
| `2.0.0-amd64-ja-jp-preview` | Imagem de contentor com o `ja-JP` local. |
| `2.0.0-amd64-ko-kr-preview` | Imagem de contentor com o `ko-KR` local. |
| `2.0.0-amd64-nb-no-preview` | Imagem de contentor com o `nb-NO` local. |
| `2.0.0-amd64-nl-nl-preview` | Imagem de contentor com o `nl-NL` local. |
| `2.0.0-amd64-pl-pl-preview` | Imagem de contentor com o `pl-PL` local. |
| `2.0.0-amd64-pt-br-preview` | Imagem de contentor com o `pt-BR` local. |
| `2.0.0-amd64-pt-pt-preview` | Imagem de contentor com o `pt-PT` local. |
| `2.0.0-amd64-ru-ru-preview` | Imagem de contentor com o `ru-RU` local. |
| `2.0.0-amd64-sv-se-preview` | Imagem de contentor com o `sv-SE` local. |
| `2.0.0-amd64-th-th-preview` | Imagem de contentor com o `th-TH` local. |
| `2.0.0-amd64-tr-tr-preview` | Imagem de contentor com o `tr-TR` local. |
| `2.0.0-amd64-zh-cn-preview` | Imagem de contentor com o `zh-CN` local. |
| `2.0.0-amd64-zh-hk-preview` | Imagem de contentor com o `zh-HK` local. |
| `2.0.0-amd64-zh-tw-preview` | Imagem de contentor com o `zh-TW` local. |
| `1.2.0-amd64-de-de-preview` | Imagem de contentor com o `de-DE` local. |
| `1.2.0-amd64-en-au-preview` | Imagem de contentor com o `en-AU` local. |
| `1.2.0-amd64-en-ca-preview` | Imagem de contentor com o `en-CA` local. |
| `1.2.0-amd64-en-gb-preview` | Imagem de contentor com o `en-GB` local. |
| `1.2.0-amd64-en-in-preview` | Imagem de contentor com o `en-IN` local. |
| `1.2.0-amd64-en-us-preview` | Imagem de contentor com o `en-US` local. |
| `1.2.0-amd64-es-es-preview` | Imagem de contentor com o `es-ES` local. |
| `1.2.0-amd64-es-mx-preview` | Imagem de contentor com o `es-MX` local. |
| `1.2.0-amd64-fr-ca-preview` | Imagem de contentor com o `fr-CA` local. |
| `1.2.0-amd64-fr-fr-preview` | Imagem de contentor com o `fr-FR` local. |
| `1.2.0-amd64-it-it-preview` | Imagem de contentor com o `it-IT` local. |
| `1.2.0-amd64-ja-jp-preview` | Imagem de contentor com o `ja-JP` local. |
| `1.2.0-amd64-pt-br-preview` | Imagem de contentor com o `pt-BR` local. |
| `1.2.0-amd64-zh-cn-preview` | Imagem de contentor com o `zh-CN` local. |
| `1.1.3-amd64-de-de-preview` | Imagem de contentor com o `de-DE` local. |
| `1.1.3-amd64-en-au-preview` | Imagem de contentor com o `en-AU` local. |
| `1.1.3-amd64-en-ca-preview` | Imagem de contentor com o `en-CA` local. |
| `1.1.3-amd64-en-gb-preview` | Imagem de contentor com o `en-GB` local. |
| `1.1.3-amd64-en-in-preview` | Imagem de contentor com o `en-IN` local. |
| `1.1.3-amd64-en-us-preview` | Imagem de contentor com o `en-US` local. |
| `1.1.3-amd64-es-es-preview` | Imagem de contentor com o `es-ES` local. |
| `1.1.3-amd64-es-mx-preview` | Imagem de contentor com o `es-MX` local. |
| `1.1.3-amd64-fr-ca-preview` | Imagem de contentor com o `fr-CA` local. |
| `1.1.3-amd64-fr-fr-preview` | Imagem de contentor com o `fr-FR` local. |
| `1.1.3-amd64-it-it-preview` | Imagem de contentor com o `it-IT` local. |
| `1.1.3-amd64-ja-jp-preview` | Imagem de contentor com o `ja-JP` local. |
| `1.1.3-amd64-pt-br-preview` | Imagem de contentor com o `pt-BR` local. |
| `1.1.3-amd64-zh-cn-preview` | Imagem de contentor com o `zh-CN` local. |
| `1.1.2-amd64-de-de-preview` | Imagem de contentor com o `de-DE` local. |
| `1.1.2-amd64-en-au-preview` | Imagem de contentor com o `en-AU` local. |
| `1.1.2-amd64-en-ca-preview` | Imagem de contentor com o `en-CA` local. |
| `1.1.2-amd64-en-gb-preview` | Imagem de contentor com o `en-GB` local. |
| `1.1.2-amd64-en-in-preview` | Imagem de contentor com o `en-IN` local. |
| `1.1.2-amd64-en-us-preview` | Imagem de contentor com o `en-US` local. |
| `1.1.2-amd64-es-es-preview` | Imagem de contentor com o `es-ES` local. |
| `1.1.2-amd64-es-mx-preview` | Imagem de contentor com o `es-MX` local. |
| `1.1.2-amd64-fr-ca-preview` | Imagem de contentor com o `fr-CA` local. |
| `1.1.2-amd64-fr-fr-preview` | Imagem de contentor com o `fr-FR` local. |
| `1.1.2-amd64-it-it-preview` | Imagem de contentor com o `it-IT` local. |
| `1.1.2-amd64-ja-jp-preview` | Imagem de contentor com o `ja-JP` local. |
| `1.1.2-amd64-pt-br-preview` | Imagem de contentor com o `pt-BR` local. |
| `1.1.2-amd64-zh-cn-preview` | Imagem de contentor com o `zh-CN` local. |
| `1.1.1-amd64-de-de-preview` | Imagem de contentor com o `de-DE` local. |
| `1.1.1-amd64-en-au-preview` | Imagem de contentor com o `en-AU` local. |
| `1.1.1-amd64-en-ca-preview` | Imagem de contentor com o `en-CA` local. |
| `1.1.1-amd64-en-gb-preview` | Imagem de contentor com o `en-GB` local. |
| `1.1.1-amd64-en-in-preview` | Imagem de contentor com o `en-IN` local. |
| `1.1.1-amd64-en-us-preview` | Imagem de contentor com o `en-US` local. |
| `1.1.1-amd64-es-es-preview` | Imagem de contentor com o `es-ES` local. |
| `1.1.1-amd64-es-mx-preview` | Imagem de contentor com o `es-MX` local. |
| `1.1.1-amd64-fr-ca-preview` | Imagem de contentor com o `fr-CA` local. |
| `1.1.1-amd64-fr-fr-preview` | Imagem de contentor com o `fr-FR` local. |
| `1.1.1-amd64-it-it-preview` | Imagem de contentor com o `it-IT` local. |
| `1.1.1-amd64-ja-jp-preview` | Imagem de contentor com o `ja-JP` local. |
| `1.1.1-amd64-pt-br-preview` | Imagem de contentor com o `pt-BR` local. |
| `1.1.1-amd64-zh-cn-preview` | Imagem de contentor com o `zh-CN` local. |
| `1.1.0-amd64-de-de-preview` | Imagem de contentor com o `de-DE` local. |
| `1.1.0-amd64-en-au-preview` | Imagem de contentor com o `en-AU` local. |
| `1.1.0-amd64-en-ca-preview` | Imagem de contentor com o `en-CA` local. |
| `1.1.0-amd64-en-gb-preview` | Imagem de contentor com o `en-GB` local. |
| `1.1.0-amd64-en-in-preview` | Imagem de contentor com o `en-IN` local. |
| `1.1.0-amd64-en-us-preview` | Imagem de contentor com o `en-US` local. |
| `1.1.0-amd64-es-es-preview` | Imagem de contentor com o `es-ES` local. |
| `1.1.0-amd64-es-mx-preview` | Imagem de contentor com o `es-MX` local. |
| `1.1.0-amd64-fr-ca-preview` | Imagem de contentor com o `fr-CA` local. |
| `1.1.0-amd64-fr-fr-preview` | Imagem de contentor com o `fr-FR` local. |
| `1.1.0-amd64-it-it-preview` | Imagem de contentor com o `it-IT` local. |
| `1.1.0-amd64-ja-jp-preview` | Imagem de contentor com o `ja-JP` local. |
| `1.1.0-amd64-pt-br-preview` | Imagem de contentor com o `pt-BR` local. |
| `1.1.0-amd64-zh-cn-preview` | Imagem de contentor com o `zh-CN` local. |
| `1.0.0-amd64-de-de-preview` | Imagem de contentor com o `de-DE` local. |
| `1.0.0-amd64-en-au-preview` | Imagem de contentor com o `en-AU` local. |
| `1.0.0-amd64-en-ca-preview` | Imagem de contentor com o `en-CA` local. |
| `1.0.0-amd64-en-gb-preview` | Imagem de contentor com o `en-GB` local. |
| `1.0.0-amd64-en-in-preview` | Imagem de contentor com o `en-IN` local. |
| `1.0.0-amd64-en-us-preview` | Imagem de contentor com o `en-US` local. |
| `1.0.0-amd64-es-es-preview` | Imagem de contentor com o `es-ES` local. |
| `1.0.0-amd64-es-mx-preview` | Imagem de contentor com o `es-MX` local. |
| `1.0.0-amd64-fr-ca-preview` | Imagem de contentor com o `fr-CA` local. |
| `1.0.0-amd64-fr-fr-preview` | Imagem de contentor com o `fr-FR` local. |
| `1.0.0-amd64-it-it-preview` | Imagem de contentor com o `it-IT` local. |
| `1.0.0-amd64-ja-jp-preview` | Imagem de contentor com o `ja-JP` local. |
| `1.0.0-amd64-pt-br-preview` | Imagem de contentor com o `pt-BR` local. |
| `1.0.0-amd64-zh-cn-preview` | Imagem de contentor com o `zh-CN` local. |

## <a name="text-to-speech"></a>Conversão de texto em voz

A imagem do recipiente [texto-a-fala][sp-tts] pode ser encontrada no registo de contentores `containerpreview.azurecr.io`. Reside no repositório `microsoft` e é nomeado `cognitive-services-text-to-speech`. O nome de imagem do recipiente totalmente qualificado é, `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech`.

Esta imagem do recipiente tem as seguintes etiquetas disponíveis:

| Etiquetas de imagem                                  | Notas                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Imagem de contentor com a `en-US` local e `en-US-JessaRUS` voz.        |
| `1.3.0-amd64-ar-eg-hoda-preview`            | Imagem de contentor com a `ar-EG` local e `ar-EG-Hoda` voz.            |
| `1.3.0-amd64-ar-sa-naayf-preview`           | Imagem de contentor com a `ar-SA` local e `ar-SA-Naayf` voz.           |
| `1.3.0-amd64-bg-bg-ivan-preview`            | Imagem de contentor com a `bg-BG` local e `bg-BG-Ivan` voz.            |
| `1.3.0-amd64-ca-es-herenarus-preview`       | Imagem de contentor com a `ca-ES` local e `ca-ES-HerenaRUS` voz.       |
| `1.3.0-amd64-cs-cz-jakub-preview`           | Imagem de contentor com a `cs-CZ` local e `cs-CZ-Jakub` voz.           |
| `1.3.0-amd64-da-dk-hellerus-preview`        | Imagem de contentor com a `da-DK` local e `da-DK-HelleRUS` voz.        |
| `1.3.0-amd64-de-at-michael-preview`         | Imagem de contentor com a `de-AT` local e `de-AT-Michael` voz.         |
| `1.3.0-amd64-de-ch-karsten-preview`         | Imagem de contentor com a `de-CH` local e `de-CH-Karsten` voz.         |
| `1.3.0-amd64-de-de-hedda-preview`           | Imagem de contentor com a `de-DE` local e `de-DE-Hedda` voz.           |
| `1.3.0-amd64-de-de-heddarus-preview`        | Imagem de contentor com a `de-DE` local e `de-DE-Hedda` voz.           |
| `1.3.0-amd64-de-de-heddarus-preview`        | Imagem de contentor com a `de-DE` local e `de-DE-HeddaRUS` voz.        |
| `1.3.0-amd64-de-de-stefan-apollo-preview`   | Imagem de contentor com a `de-DE` local e `de-DE-Stefan-Apollo` voz.   |
| `1.3.0-amd64-el-gr-stefanos-preview`        | Imagem de contentor com a `el-GR` local e `el-GR-Stefanos` voz.        |
| `1.3.0-amd64-en-au-catherine-preview`       | Imagem de contentor com a `en-AU` local e `en-AU-Catherine` voz.       |
| `1.3.0-amd64-en-au-hayleyrus-preview`       | Imagem de contentor com a `en-AU` local e `en-AU-HayleyRUS` voz.       |
| `1.3.0-amd64-en-ca-heatherrus-preview`      | Imagem de contentor com a `en-CA` local e `en-CA-HeatherRUS` voz.      |
| `1.3.0-amd64-en-ca-linda-preview`           | Imagem de contentor com a `en-CA` local e `en-CA-Linda` voz.           |
| `1.3.0-amd64-en-gb-george-apollo-preview`   | Imagem de contentor com a `en-GB` local e `en-GB-George-Apollo` voz.   |
| `1.3.0-amd64-en-gb-hazelrus-preview`        | Imagem de contentor com a `en-GB` local e `en-GB-HazelRUS` voz.        |
| `1.3.0-amd64-en-gb-susan-apollo-preview`    | Imagem de contentor com a `en-GB` local e `en-GB-Susan-Apollo` voz.    |
| `1.3.0-amd64-en-ie-sean-preview`            | Imagem de contentor com a `en-IE` local e `en-IE-Sean` voz.            |
| `1.3.0-amd64-en-in-heera-apollo-preview`    | Imagem de contentor com a `en-IN` local e `en-IN-Heera-Apollo` voz.    |
| `1.3.0-amd64-en-in-priyarus-preview`        | Imagem de contentor com a `en-IN` local e `en-IN-PriyaRUS` voz.        |
| `1.3.0-amd64-en-in-ravi-apollo-preview`     | Imagem de contentor com a `en-IN` local e `en-IN-Ravi-Apollo` voz.     |
| `1.3.0-amd64-en-us-benjaminrus-preview`     | Imagem de contentor com a `en-US` local e `en-US-BenjaminRUS` voz.     |
| `1.3.0-amd64-en-us-guy24krus-preview`       | Imagem de contentor com a `en-US` local e `en-US-Guy24kRUS` voz.       |
| `1.3.0-amd64-en-us-jessa24krus-preview`     | Imagem de contentor com a `en-US` local e `en-US-Jessa24kRUS` voz.     |
| `1.3.0-amd64-en-us-jessarus-preview`        | Imagem de contentor com a `en-US` local e `en-US-JessaRUS` voz.        |
| `1.3.0-amd64-en-us-zirarus-preview`         | Imagem de contentor com a `en-US` local e `en-US-ZiraRUS` voz.         |
| `1.3.0-amd64-es-es-helenarus-preview`       | Imagem de contentor com a `es-ES` local e `es-ES-HelenaRUS` voz.       |
| `1.3.0-amd64-es-es-laura-apollo-preview`    | Imagem de contentor com a `es-ES` local e `es-ES-Laura-Apollo` voz.    |
| `1.3.0-amd64-es-es-pablo-apollo-preview`    | Imagem de contentor com a `es-ES` local e `es-ES-Pablo-Apollo` voz.    |
| `1.3.0-amd64-es-mx-hildarus-preview`        | Imagem de contentor com a `es-MX` local e `es-MX-HildaRUS` voz.        |
| `1.3.0-amd64-es-mx-raul-apollo-preview`     | Imagem de contentor com a `es-MX` local e `es-MX-Raul-Apollo` voz.     |
| `1.3.0-amd64-fi-fi-heidirus-preview`        | Imagem de contentor com a `fi-FI` local e `fi-FI-HeidiRUS` voz.        |
| `1.3.0-amd64-fr-ca-caroline-preview`        | Imagem de contentor com a `fr-CA` local e `fr-CA-Caroline` voz.        |
| `1.3.0-amd64-fr-ca-harmonierus-preview`     | Imagem de contentor com a `fr-CA` local e `fr-CA-HarmonieRUS` voz.     |
| `1.3.0-amd64-fr-ch-guillaume-preview`       | Imagem de contentor com a `fr-CH` local e `fr-CH-Guillaume` voz.       |
| `1.3.0-amd64-fr-fr-hortenserus-preview`     | Imagem de contentor com a `fr-FR` local e `fr-FR-HortenseRUS` voz.     |
| `1.3.0-amd64-fr-fr-julie-apollo-preview`    | Imagem de contentor com a `fr-FR` local e `fr-FR-Julie-Apollo` voz.    |
| `1.3.0-amd64-fr-fr-paul-apollo-preview`     | Imagem de contentor com a `fr-FR` local e `fr-FR-Paul-Apollo` voz.     |
| `1.3.0-amd64-he-il-asaf-preview`            | Imagem de contentor com a `he-IL` local e `he-IL-Asaf` voz.            |
| `1.3.0-amd64-hi-in-hemant-preview`          | Imagem de contentor com a `hi-IN` local e `hi-IN-Hemant` voz.          |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview`  | Imagem de contentor com a `hi-IN` local e `hi-IN-Kalpana-Apollo` voz.  |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview`  | Imagem de contentor com a `hi-IN` local e `hi-IN-Kalpana` voz.         |
| `1.3.0-amd64-hi-in-kalpana-preview`         | Imagem de contentor com a `hi-IN` local e `hi-IN-Kalpana` voz.         |
| `1.3.0-amd64-hr-hr-matej-preview`           | Imagem de contentor com a `hr-HR` local e `hr-HR-Matej` voz.           |
| `1.3.0-amd64-hu-hu-szabolcs-preview`        | Imagem de contentor com a `hu-HU` local e `hu-HU-Szabolcs` voz.        |
| `1.3.0-amd64-id-id-andika-preview`          | Imagem de contentor com a `id-ID` local e `id-ID-Andika` voz.          |
| `1.3.0-amd64-it-it-cosimo-apollo-preview`   | Imagem de contentor com a `it-IT` local e `it-IT-Cosimo-Apollo` voz.   |
| `1.3.0-amd64-it-it-luciarus-preview`        | Imagem de contentor com a `it-IT` local e `it-IT-LuciaRUS` voz.        |
| `1.3.0-amd64-ja-jp-ayumi-apollo-preview`    | Imagem de contentor com a `ja-JP` local e `ja-JP-Ayumi-Apollo` voz.    |
| `1.3.0-amd64-ja-jp-harukarus-preview`       | Imagem de contentor com a `ja-JP` local e `ja-JP-HarukaRUS` voz.       |
| `1.3.0-amd64-ja-jp-ichiro-apollo-preview`   | Imagem de contentor com a `ja-JP` local e `ja-JP-Ichiro-Apollo` voz.   |
| `1.3.0-amd64-ko-kr-heamirus-preview`        | Imagem de contentor com a `ko-KR` local e `ko-KR-HeamiRUS` voz.        |
| `1.3.0-amd64-ms-my-rizwan-preview`          | Imagem de contentor com a `ms-MY` local e `ms-MY-Rizwan` voz.          |
| `1.3.0-amd64-nb-no-huldarus-preview`        | Imagem de contentor com a `nb-NO` local e `nb-NO-HuldaRUS` voz.        |
| `1.3.0-amd64-nl-nl-hannarus-preview`        | Imagem de contentor com a `nl-NL` local e `nl-NL-HannaRUS` voz.        |
| `1.3.0-amd64-pl-pl-paulinarus-preview`      | Imagem de contentor com a `pl-PL` local e `pl-PL-PaulinaRUS` voz.      |
| `1.3.0-amd64-pt-br-daniel-apollo-preview`   | Imagem de contentor com a `pt-BR` local e `pt-BR-Daniel-Apollo` voz.   |
| `1.3.0-amd64-pt-br-heloisarus-preview`      | Imagem de contentor com a `pt-BR` local e `pt-BR-HeloisaRUS` voz.      |
| `1.3.0-amd64-pt-pt-heliarus-preview`        | Imagem de contentor com a `pt-PT` local e `pt-PT-HeliaRUS` voz.        |
| `1.3.0-amd64-ro-ro-andrei-preview`          | Imagem de contentor com a `ro-RO` local e `ro-RO-Andrei` voz.          |
| `1.3.0-amd64-ru-ru-ekaterinarus-preview`    | Imagem de contentor com a `ru-RU` local e `ru-RU-EkaterinaRUS` voz.    |
| `1.3.0-amd64-ru-ru-irina-apollo-preview`    | Imagem de contentor com a `ru-RU` local e `ru-RU-Irina-Apollo` voz.    |
| `1.3.0-amd64-ru-ru-pavel-apollo-preview`    | Imagem de contentor com a `ru-RU` local e `ru-RU-Pavel-Apollo` voz.    |
| `1.3.0-amd64-sk-sk-filip-preview`           | Imagem de contentor com a `sk-SK` local e `sk-SK-Filip` voz.           |
| `1.3.0-amd64-sl-si-lado-preview`            | Imagem de contentor com a `sl-SI` local e `sl-SI-Lado` voz.            |
| `1.3.0-amd64-sv-se-hedvigrus-preview`       | Imagem de contentor com a `sv-SE` local e `sv-SE-HedvigRUS` voz.       |
| `1.3.0-amd64-ta-in-valluvar-preview`        | Imagem de contentor com a `ta-IN` local e `ta-IN-Valluvar` voz.        |
| `1.3.0-amd64-te-in-chitra-preview`          | Imagem de contentor com a `te-IN` local e `te-IN-Chitra` voz.          |
| `1.3.0-amd64-th-th-pattara-preview`         | Imagem de contentor com a `th-TH` local e `th-TH-Pattara` voz.         |
| `1.3.0-amd64-tr-tr-sedarus-preview`         | Imagem de contentor com a `tr-TR` local e `tr-TR-SedaRUS` voz.         |
| `1.3.0-amd64-vi-vn-an-preview`              | Imagem de contentor com a `vi-VN` local e `vi-VN-An` voz.              |
| `1.3.0-amd64-zh-cn-huihuirus-preview`       | Imagem de contentor com a `zh-CN` local e `zh-CN-HuihuiRUS` voz.       |
| `1.3.0-amd64-zh-cn-kangkang-apollo-preview` | Imagem de contentor com a `zh-CN` local e `zh-CN-Kangkang-Apollo` voz. |
| `1.3.0-amd64-zh-cn-yaoyao-apollo-preview`   | Imagem de contentor com a `zh-CN` local e `zh-CN-Yaoyao-Apollo` voz.   |
| `1.3.0-amd64-zh-hk-danny-apollo-preview`    | Imagem de contentor com a `zh-HK` local e `zh-HK-Danny-Apollo` voz.    |
| `1.3.0-amd64-zh-hk-tracy-apollo-preview`    | Imagem de contentor com a `zh-HK` local e `zh-HK-Tracy-Apollo` voz.    |
| `1.3.0-amd64-zh-hk-tracyrus-preview`        | Imagem de contentor com a `zh-HK` local e `zh-HK-TracyRUS` voz.        |
| `1.3.0-amd64-zh-tw-hanhanrus-preview`       | Imagem de contentor com a `zh-TW` local e `zh-TW-HanHanRUS` voz.       |
| `1.3.0-amd64-zh-tw-yating-apollo-preview`   | Imagem de contentor com a `zh-TW` local e `zh-TW-Yating-Apollo` voz.   |
| `1.3.0-amd64-zh-tw-zhiwei-apollo-preview`   | Imagem de contentor com a `zh-TW` local e `zh-TW-Zhiwei-Apollo` voz.   |
| `1.2.0-amd64-de-de-heddarus-preview`        | Imagem de contentor com a `de-DE` local e `de-DE-Hedda` voz.           |
| `1.2.0-amd64-de-de-heddarus-preview`        | Imagem de contentor com a `de-DE` local e `de-DE-HeddaRUS` voz.        |
| `1.2.0-amd64-de-de-stefan-apollo-preview`   | Imagem de contentor com a `de-DE` local e `de-DE-Stefan-Apollo` voz.   |
| `1.2.0-amd64-en-au-catherine-preview`       | Imagem de contentor com a `en-AU` local e `en-AU-Catherine` voz.       |
| `1.2.0-amd64-en-au-hayleyrus-preview`       | Imagem de contentor com a `en-AU` local e `en-AU-HayleyRUS` voz.       |
| `1.2.0-amd64-en-gb-george-apollo-preview`   | Imagem de contentor com a `en-GB` local e `en-GB-George-Apollo` voz.   |
| `1.2.0-amd64-en-gb-hazelrus-preview`        | Imagem de contentor com a `en-GB` local e `en-GB-HazelRUS` voz.        |
| `1.2.0-amd64-en-gb-susan-apollo-preview`    | Imagem de contentor com a `en-GB` local e `en-GB-Susan-Apollo` voz.    |
| `1.2.0-amd64-en-in-heera-apollo-preview`    | Imagem de contentor com a `en-IN` local e `en-IN-Heera-Apollo` voz.    |
| `1.2.0-amd64-en-in-priyarus-preview`        | Imagem de contentor com a `en-IN` local e `en-IN-PriyaRUS` voz.        |
| `1.2.0-amd64-en-in-ravi-apollo-preview`     | Imagem de contentor com a `en-IN` local e `en-IN-Ravi-Apollo` voz.     |
| `1.2.0-amd64-en-us-benjaminrus-preview`     | Imagem de contentor com a `en-US` local e `en-US-BenjaminRUS` voz.     |
| `1.2.0-amd64-en-us-guy24krus-preview`       | Imagem de contentor com a `en-US` local e `en-US-Guy24kRUS` voz.       |
| `1.2.0-amd64-en-us-jessa24krus-preview`     | Imagem de contentor com a `en-US` local e `en-US-Jessa24kRUS` voz.     |
| `1.2.0-amd64-en-us-jessarus-preview`        | Imagem de contentor com a `en-US` local e `en-US-JessaRUS` voz.        |
| `1.2.0-amd64-en-us-zirarus-preview`         | Imagem de contentor com a `en-US` local e `en-US-ZiraRUS` voz.         |
| `1.2.0-amd64-es-es-helenarus-preview`       | Imagem de contentor com a `es-ES` local e `es-ES-HelenaRUS` voz.       |
| `1.2.0-amd64-es-es-laura-apollo-preview`    | Imagem de contentor com a `es-ES` local e `es-ES-Laura-Apollo` voz.    |
| `1.2.0-amd64-es-es-pablo-apollo-preview`    | Imagem de contentor com a `es-ES` local e `es-ES-Pablo-Apollo` voz.    |
| `1.2.0-amd64-es-mx-hildarus-preview`        | Imagem de contentor com a `es-MX` local e `es-MX-HildaRUS` voz.        |
| `1.2.0-amd64-es-mx-raul-apollo-preview`     | Imagem de contentor com a `es-MX` local e `es-MX-Raul-Apollo` voz.     |
| `1.2.0-amd64-fr-ca-caroline-preview`        | Imagem de contentor com a `fr-CA` local e `fr-CA-Caroline` voz.        |
| `1.2.0-amd64-fr-ca-harmonierus-preview`     | Imagem de contentor com a `fr-CA` local e `fr-CA-HarmonieRUS` voz.     |
| `1.2.0-amd64-fr-fr-hortenserus-preview`     | Imagem de contentor com a `fr-FR` local e `fr-FR-HortenseRUS` voz.     |
| `1.2.0-amd64-fr-fr-julie-apollo-preview`    | Imagem de contentor com a `fr-FR` local e `fr-FR-Julie-Apollo` voz.    |
| `1.2.0-amd64-fr-fr-paul-apollo-preview`     | Imagem de contentor com a `fr-FR` local e `fr-FR-Paul-Apollo` voz.     |
| `1.2.0-amd64-it-it-cosimo-apollo-preview`   | Imagem de contentor com a `it-IT` local e `it-IT-Cosimo-Apollo` voz.   |
| `1.2.0-amd64-it-it-luciarus-preview`        | Imagem de contentor com a `it-IT` local e `it-IT-LuciaRUS` voz.        |
| `1.2.0-amd64-ja-jp-ayumi-apollo-preview`    | Imagem de contentor com a `ja-JP` local e `ja-JP-Ayumi-Apollo` voz.    |
| `1.2.0-amd64-ja-jp-harukarus-preview`       | Imagem de contentor com a `ja-JP` local e `ja-JP-HarukaRUS` voz.       |
| `1.2.0-amd64-ja-jp-ichiro-apollo-preview`   | Imagem de contentor com a `ja-JP` local e `ja-JP-Ichiro-Apollo` voz.   |
| `1.2.0-amd64-ko-kr-heamirus-preview`        | Imagem de contentor com a `ko-KR` local e `ko-KR-HeamiRUS` voz.        |
| `1.2.0-amd64-pt-br-daniel-apollo-preview`   | Imagem de contentor com a `pt-BR` local e `pt-BR-Daniel-Apollo` voz.   |
| `1.2.0-amd64-pt-br-heloisarus-preview`      | Imagem de contentor com a `pt-BR` local e `pt-BR-HeloisaRUS` voz.      |
| `1.2.0-amd64-zh-cn-huihuirus-preview`       | Imagem de contentor com a `zh-CN` local e `zh-CN-HuihuiRUS` voz.       |
| `1.2.0-amd64-zh-cn-kangkang-apollo-preview` | Imagem de contentor com a `zh-CN` local e `zh-CN-Kangkang-Apollo` voz. |
| `1.2.0-amd64-zh-cn-yaoyao-apollo-preview`   | Imagem de contentor com a `zh-CN` local e `zh-CN-Yaoyao-Apollo` voz.   |
| `1.1.0-amd64-de-de-hedda-preview`           | Imagem de contentor com a `de-DE` local e `de-DE-Hedda` voz.           |
| `1.1.0-amd64-de-de-heddarus-preview`        | Imagem de contentor com a `de-DE` local e `de-DE-Hedda` voz.           |
| `1.1.0-amd64-de-de-heddarus-preview`        | Imagem de contentor com a `de-DE` local e `de-DE-HeddaRUS` voz.        |
| `1.1.0-amd64-de-de-stefan-apollo-preview`   | Imagem de contentor com a `de-DE` local e `de-DE-Stefan-Apollo` voz.   |
| `1.1.0-amd64-en-au-catherine-preview`       | Imagem de contentor com a `en-AU` local e `en-AU-Catherine` voz.       |
| `1.1.0-amd64-en-au-hayleyrus-preview`       | Imagem de contentor com a `en-AU` local e `en-AU-HayleyRUS` voz.       |
| `1.1.0-amd64-en-gb-george-apollo-preview`   | Imagem de contentor com a `en-GB` local e `en-GB-George-Apollo` voz.   |
| `1.1.0-amd64-en-gb-hazelrus-preview`        | Imagem de contentor com a `en-GB` local e `en-GB-HazelRUS` voz.        |
| `1.1.0-amd64-en-gb-susan-apollo-preview`    | Imagem de contentor com a `en-GB` local e `en-GB-Susan-Apollo` voz.    |
| `1.1.0-amd64-en-in-heera-apollo-preview`    | Imagem de contentor com a `en-IN` local e `en-IN-Heera-Apollo` voz.    |
| `1.1.0-amd64-en-in-priyarus-preview`        | Imagem de contentor com a `en-IN` local e `en-IN-PriyaRUS` voz.        |
| `1.1.0-amd64-en-in-ravi-apollo-preview`     | Imagem de contentor com a `en-IN` local e `en-IN-Ravi-Apollo` voz.     |
| `1.1.0-amd64-en-us-benjaminrus-preview`     | Imagem de contentor com a `en-US` local e `en-US-BenjaminRUS` voz.     |
| `1.1.0-amd64-en-us-guy24krus-preview`       | Imagem de contentor com a `en-US` local e `en-US-Guy24kRUS` voz.       |
| `1.1.0-amd64-en-us-jessa24krus-preview`     | Imagem de contentor com a `en-US` local e `en-US-Jessa24kRUS` voz.     |
| `1.1.0-amd64-en-us-jessarus-preview`        | Imagem de contentor com a `en-US` local e `en-US-JessaRUS` voz.        |
| `1.1.0-amd64-en-us-zirarus-preview`         | Imagem de contentor com a `en-US` local e `en-US-ZiraRUS` voz.         |
| `1.1.0-amd64-es-es-helenarus-preview`       | Imagem de contentor com a `es-ES` local e `es-ES-HelenaRUS` voz.       |
| `1.1.0-amd64-es-es-laura-apollo-preview`    | Imagem de contentor com a `es-ES` local e `es-ES-Laura-Apollo` voz.    |
| `1.1.0-amd64-es-es-pablo-apollo-preview`    | Imagem de contentor com a `es-ES` local e `es-ES-Pablo-Apollo` voz.    |
| `1.1.0-amd64-es-mx-hildarus-preview`        | Imagem de contentor com a `es-MX` local e `es-MX-HildaRUS` voz.        |
| `1.1.0-amd64-es-mx-raul-apollo-preview`     | Imagem de contentor com a `es-MX` local e `es-MX-Raul-Apollo` voz.     |
| `1.1.0-amd64-fr-ca-caroline-preview`        | Imagem de contentor com a `fr-CA` local e `fr-CA-Caroline` voz.        |
| `1.1.0-amd64-fr-ca-harmonierus-preview`     | Imagem de contentor com a `fr-CA` local e `fr-CA-HarmonieRUS` voz.     |
| `1.1.0-amd64-fr-fr-hortenserus-preview`     | Imagem de contentor com a `fr-FR` local e `fr-FR-HortenseRUS` voz.     |
| `1.1.0-amd64-fr-fr-julie-apollo-preview`    | Imagem de contentor com a `fr-FR` local e `fr-FR-Julie-Apollo` voz.    |
| `1.1.0-amd64-fr-fr-paul-apollo-preview`     | Imagem de contentor com a `fr-FR` local e `fr-FR-Paul-Apollo` voz.     |
| `1.1.0-amd64-it-it-cosimo-apollo-preview`   | Imagem de contentor com a `it-IT` local e `it-IT-Cosimo-Apollo` voz.   |
| `1.1.0-amd64-it-it-luciarus-preview`        | Imagem de contentor com a `it-IT` local e `it-IT-LuciaRUS` voz.        |
| `1.1.0-amd64-ja-jp-ayumi-apollo-preview`    | Imagem de contentor com a `ja-JP` local e `ja-JP-Ayumi-Apollo` voz.    |
| `1.1.0-amd64-ja-jp-harukarus-preview`       | Imagem de contentor com a `ja-JP` local e `ja-JP-HarukaRUS` voz.       |
| `1.1.0-amd64-ja-jp-ichiro-apollo-preview`   | Imagem de contentor com a `ja-JP` local e `ja-JP-Ichiro-Apollo` voz.   |
| `1.1.0-amd64-ko-kr-heamirus-preview`        | Imagem de contentor com a `ko-KR` local e `ko-KR-HeamiRUS` voz.        |
| `1.1.0-amd64-pt-br-daniel-apollo-preview`   | Imagem de contentor com a `pt-BR` local e `pt-BR-Daniel-Apollo` voz.   |
| `1.1.0-amd64-pt-br-heloisarus-preview`      | Imagem de contentor com a `pt-BR` local e `pt-BR-HeloisaRUS` voz.      |
| `1.1.0-amd64-zh-cn-huihuirus-preview`       | Imagem de contentor com a `zh-CN` local e `zh-CN-HuihuiRUS` voz.       |
| `1.1.0-amd64-zh-cn-kangkang-apollo-preview` | Imagem de contentor com a `zh-CN` local e `zh-CN-Kangkang-Apollo` voz. |
| `1.1.0-amd64-zh-cn-yaoyao-apollo-preview`   | Imagem de contentor com a `zh-CN` local e `zh-CN-Yaoyao-Apollo` voz.   |
| `1.0.0-amd64-en-us-benjaminrus-preview`     | Imagem de contentor com a `en-US` local e `en-US-BenjaminRUS` voz.     |
| `1.0.0-amd64-en-us-guy24krus-preview`       | Imagem de contentor com a `en-US` local e `en-US-Guy24kRUS` voz.       |
| `1.0.0-amd64-en-us-jessa24krus-preview`     | Imagem de contentor com a `en-US` local e `en-US-Jessa24kRUS` voz.     |
| `1.0.0-amd64-en-us-jessarus-preview`        | Imagem de contentor com a `en-US` local e `en-US-JessaRUS` voz.        |
| `1.0.0-amd64-en-us-zirarus-preview`         | Imagem de contentor com a `en-US` local e `en-US-ZiraRUS` voz.         |
| `1.0.0-amd64-zh-cn-huihuirus-preview`       | Imagem de contentor com a `zh-CN` local e `zh-CN-HuihuiRUS` voz.       |
| `1.0.0-amd64-zh-cn-kangkang-apollo-preview` | Imagem de contentor com a `zh-CN` local e `zh-CN-Kangkang-Apollo` voz. |
| `1.0.0-amd64-zh-cn-yaoyao-apollo-preview`   | Imagem de contentor com a `zh-CN` local e `zh-CN-Yaoyao-Apollo` voz.   |

## <a name="key-phrase-extraction"></a>Extração de Expressões-Chave

A imagem do recipiente de [extração de frases-chave][ta-kp] pode ser encontrada no `mcr.microsoft.com` sindicato do registo de contentores. Reside no repositório `azure-cognitive-services` e é nomeado `keyphrase`. O nome de imagem do recipiente totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/keyphrase`.

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

A imagem do recipiente de deteção de [idiomas][ta-la] pode ser encontrada no `mcr.microsoft.com` sindicato do registo de contentores. Reside no repositório `azure-cognitive-services` e é nomeado `language`. O nome de imagem do recipiente totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/language`.

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

A imagem do contentor de análise de [sentimentopode][ta-se] ser encontrada no `mcr.microsoft.com` sindicato do registo de contentores. Reside no repositório `azure-cognitive-services` e é nomeado `sentiment`. O nome de imagem do recipiente totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/sentiment`.

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
