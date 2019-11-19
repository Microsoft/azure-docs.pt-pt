---
title: Marcas de imagem de contêiner de serviços cognitivas
titleSuffix: Azure Cognitive Services
description: Uma listagem abrangente de todas as marcas de imagem de contêiner de serviço cognitiva.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 11/18/2019
ms.author: dapine
ms.openlocfilehash: 0d8c7a36582c30975f3a408a2ea6e95d39e560ef
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173759"
---
# <a name="azure-cognitive-services-container-image-tags"></a>Marcas de imagem de contêiner de serviços cognitivas do Azure

Os serviços cognitivas do Azure oferecem muitas imagens de contêiner. Os registros de contêiner e os repositórios correspondentes variam entre as imagens de contêiner. Cada nome de imagem de contêiner oferece várias marcas. Uma marca de imagem de contêiner é um mecanismo de controle de versão da imagem de contêiner. Este artigo destina-se a ser usado como uma referência abrangente para listar todas as imagens de contêiner de serviços cognitivas e suas marcas disponíveis.

> [!TIP]
> Ao usar [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/), preste muita atenção à capitalização do registro de contêiner, do repositório, do nome da imagem de contêiner e da marca correspondente, pois eles diferenciam **maiúsculas de minúsculas**.

## <a name="anomaly-detector"></a>Detetor de Anomalias

A imagem de contêiner do [detector de anomalias][ad-containers] pode ser encontrada no registro de contêiner `containerpreview.azurecr.io`. Ele reside no repositório `microsoft` e é nomeado `cognitive-services-anomaly-detector`. O nome da imagem de contêiner totalmente qualificado é, `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector`.

Essa imagem de contêiner tem as seguintes marcas disponíveis:

| Marcas de imagem | Notas |
|------------|:------|
| `latest` | |
| `1.1.009301-amd64-preview` | |
| `1.1.008500001-amd64-preview` | |

## <a name="computer-vision"></a>Imagem Digitalizada

A imagem de contêiner [Pesquisa Visual computacional][cv-containers] pode ser encontrada no registro de contêiner de `containerpreview.azurecr.io`. Ele reside no repositório `microsoft` e é nomeado `cognitive-services-read`. O nome da imagem de contêiner totalmente qualificado é, `containerpreview.azurecr.io/microsoft/cognitive-services-read`.

Essa imagem de contêiner tem as seguintes marcas disponíveis:

| Marcas de imagem | Notas |
|------------|:------|
| `latest` | |
| `1.1.009920003-amd64-preview` | |
| `1.1.009910003-amd64-preview` | |

## <a name="face"></a>Rostos

A imagem de contêiner de [face][fa-containers] pode ser encontrada no registro de contêiner `containerpreview.azurecr.io`. Ele reside no repositório `microsoft` e é nomeado `cognitive-services-face`. O nome da imagem de contêiner totalmente qualificado é, `containerpreview.azurecr.io/microsoft/cognitive-services-face`.

Essa imagem de contêiner tem as seguintes marcas disponíveis:

| Marcas de imagem | Notas |
|------------|:------|
| `latest` | |
| `1.1.009301-amd64-preview` | |
| `1.1.008710001-amd64-preview` | |
| `1.1.007750002-amd64-preview` | |
| `1.1.007360001-amd64-preview` | |
| `1.1.006770001-amd64-preview` | |
| `1.1.006490002-amd64-preview` | |
| `1.0.005940002-amd64-preview` | |
| `1.0.005550001-amd64-preview` | |

## <a name="form-recognizer"></a>Reconhecedor de Formato

A imagem de contêiner do [reconhecedor de formulário][fr-containers] pode ser encontrada no registro de contêiner `containerpreview.azurecr.io`. Ele reside no repositório `microsoft` e é nomeado `cognitive-services-form-recognizer`. O nome da imagem de contêiner totalmente qualificado é, `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer`.

Essa imagem de contêiner tem as seguintes marcas disponíveis:

| Marcas de imagem | Notas |
|------------|:------|
| `latest` | |
| `1.1.009301-amd64-preview` | |
| `1.1.008640001-amd64-preview` | |
| `1.1.008510001-amd64-preview` | |

## <a name="language-understanding-luis"></a>Compreensão de Idiomas (LUIS)

A imagem de contêiner [Luis][lu-containers] pode ser encontrada na agregação do registro de contêiner `mcr.microsoft.com`. Ele reside no repositório `azure-cognitive-services` e é nomeado `luis`. O nome da imagem de contêiner totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/luis`.

Essa imagem de contêiner tem as seguintes marcas disponíveis:

| Marcas de imagem | Notas |
|------------|:------|
| `latest` | |
| `1.1.010330004-amd64-preview` | |
| `1.1.009301-amd64-preview` | |
| `1.1.008710001-amd64-preview` | |
| `1.1.008510001-amd64-preview` | |
| `1.1.008010002-amd64-preview` | |
| `1.1.007750002-amd64-preview` | |
| `1.1.007360001-amd64-preview` | |
| `1.1.007020001-amd64-preview` | |

## <a name="custom-speech-to-text"></a>Fala Personalizada para texto

A imagem de contêiner de [fala personalizada para texto][sp-cstt] pode ser encontrada no registro de contêiner de `containerpreview.azurecr.io`. Ele reside no repositório `microsoft` e é nomeado `cognitive-services-custom-speech-to-text`. O nome da imagem de contêiner totalmente qualificado é, `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text`.

Essa imagem de contêiner tem as seguintes marcas disponíveis:

| Marcas de imagem | Notas |
|------------|:------|
| `latest` | |
| `2.0.0-amd64-preview` | |

## <a name="custom-text-to-speech"></a>Conversão de texto em fala personalizada

A imagem de contêiner de [texto em fala personalizado][sp-ctts] pode ser encontrada no registro de contêiner `containerpreview.azurecr.io`. Ele reside no repositório `microsoft` e é nomeado `cognitive-services-custom-text-to-speech`. O nome da imagem de contêiner totalmente qualificado é, `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech`.

Essa imagem de contêiner tem as seguintes marcas disponíveis:

| Marcas de imagem | Notas |
|------------|:------|
| `latest` | |
| `1.3.0-amd64-preview` | |

## <a name="speech-to-text"></a>Conversão de voz em texto

A imagem de contêiner de [fala em texto][sp-stt] pode ser encontrada no registro de contêiner `containerpreview.azurecr.io`. Ele reside no repositório `microsoft` e é nomeado `cognitive-services-speech-to-text`. O nome da imagem de contêiner totalmente qualificado é, `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text`.

Essa imagem de contêiner tem as seguintes marcas disponíveis:

| Marcas de imagem | Notas |
|------------|:------|
| `latest` | Imagem de contêiner com a localidade `en-US`. |
| `2.0.0-amd64-ar-eg-preview` | Imagem de contêiner com a localidade `ar-EG`. |
| `2.0.0-amd64-ca-es-preview` | Imagem de contêiner com a localidade `ca-ES`. |
| `2.0.0-amd64-da-dk-preview` | Imagem de contêiner com a localidade `da-DK`. |
| `2.0.0-amd64-de-de-preview` | Imagem de contêiner com a localidade `de-DE`. |
| `2.0.0-amd64-en-au-preview` | Imagem de contêiner com a localidade `en-AU`. |
| `2.0.0-amd64-en-ca-preview` | Imagem de contêiner com a localidade `en-CA`. |
| `2.0.0-amd64-en-gb-preview` | Imagem de contêiner com a localidade `en-GB`. |
| `2.0.0-amd64-en-in-preview` | Imagem de contêiner com a localidade `en-IN`. |
| `2.0.0-amd64-en-nz-preview` | Imagem de contêiner com a localidade `en-NZ`. |
| `2.0.0-amd64-en-us-preview` | Imagem de contêiner com a localidade `en-US`. |
| `2.0.0-amd64-es-es-preview` | Imagem de contêiner com a localidade `es-ES`. |
| `2.0.0-amd64-es-mx-preview` | Imagem de contêiner com a localidade `es-MX`. |
| `2.0.0-amd64-fi-fi-preview` | Imagem de contêiner com a localidade `fi-FI`. |
| `2.0.0-amd64-fr-ca-preview` | Imagem de contêiner com a localidade `fr-CA`. |
| `2.0.0-amd64-fr-fr-preview` | Imagem de contêiner com a localidade `fr-FR`. |
| `2.0.0-amd64-hi-in-preview` | Imagem de contêiner com a localidade `hi-IN`. |
| `2.0.0-amd64-it-it-preview` | Imagem de contêiner com a localidade `it-IT`. |
| `2.0.0-amd64-ja-jp-preview` | Imagem de contêiner com a localidade `ja-JP`. |
| `2.0.0-amd64-ko-kr-preview` | Imagem de contêiner com a localidade `ko-KR`. |
| `2.0.0-amd64-nb-no-preview` | Imagem de contêiner com a localidade `nb-NO`. |
| `2.0.0-amd64-nl-nl-preview` | Imagem de contêiner com a localidade `nl-NL`. |
| `2.0.0-amd64-pl-pl-preview` | Imagem de contêiner com a localidade `pl-PL`. |
| `2.0.0-amd64-pt-br-preview` | Imagem de contêiner com a localidade `pt-BR`. |
| `2.0.0-amd64-pt-pt-preview` | Imagem de contêiner com a localidade `pt-PT`. |
| `2.0.0-amd64-ru-ru-preview` | Imagem de contêiner com a localidade `ru-RU`. |
| `2.0.0-amd64-sv-se-preview` | Imagem de contêiner com a localidade `sv-SE`. |
| `2.0.0-amd64-th-th-preview` | Imagem de contêiner com a localidade `th-TH`. |
| `2.0.0-amd64-tr-tr-preview` | Imagem de contêiner com a localidade `tr-TR`. |
| `2.0.0-amd64-zh-cn-preview` | Imagem de contêiner com a localidade `zh-CN`. |
| `2.0.0-amd64-zh-hk-preview` | Imagem de contêiner com a localidade `zh-HK`. |
| `2.0.0-amd64-zh-tw-preview` | Imagem de contêiner com a localidade `zh-TW`. |
| `1.2.0-amd64-de-de-preview` | Imagem de contêiner com a localidade `de-DE`. |
| `1.2.0-amd64-en-au-preview` | Imagem de contêiner com a localidade `en-AU`. |
| `1.2.0-amd64-en-ca-preview` | Imagem de contêiner com a localidade `en-CA`. |
| `1.2.0-amd64-en-gb-preview` | Imagem de contêiner com a localidade `en-GB`. |
| `1.2.0-amd64-en-in-preview` | Imagem de contêiner com a localidade `en-IN`. |
| `1.2.0-amd64-en-us-preview` | Imagem de contêiner com a localidade `en-US`. |
| `1.2.0-amd64-es-es-preview` | Imagem de contêiner com a localidade `es-ES`. |
| `1.2.0-amd64-es-mx-preview` | Imagem de contêiner com a localidade `es-MX`. |
| `1.2.0-amd64-fr-ca-preview` | Imagem de contêiner com a localidade `fr-CA`. |
| `1.2.0-amd64-fr-fr-preview` | Imagem de contêiner com a localidade `fr-FR`. |
| `1.2.0-amd64-it-it-preview` | Imagem de contêiner com a localidade `it-IT`. |
| `1.2.0-amd64-ja-jp-preview` | Imagem de contêiner com a localidade `ja-JP`. |
| `1.2.0-amd64-pt-br-preview` | Imagem de contêiner com a localidade `pt-BR`. |
| `1.2.0-amd64-zh-cn-preview` | Imagem de contêiner com a localidade `zh-CN`. |
| `1.1.3-amd64-de-de-preview` | Imagem de contêiner com a localidade `de-DE`. |
| `1.1.3-amd64-en-au-preview` | Imagem de contêiner com a localidade `en-AU`. |
| `1.1.3-amd64-en-ca-preview` | Imagem de contêiner com a localidade `en-CA`. |
| `1.1.3-amd64-en-gb-preview` | Imagem de contêiner com a localidade `en-GB`. |
| `1.1.3-amd64-en-in-preview` | Imagem de contêiner com a localidade `en-IN`. |
| `1.1.3-amd64-en-us-preview` | Imagem de contêiner com a localidade `en-US`. |
| `1.1.3-amd64-es-es-preview` | Imagem de contêiner com a localidade `es-ES`. |
| `1.1.3-amd64-es-mx-preview` | Imagem de contêiner com a localidade `es-MX`. |
| `1.1.3-amd64-fr-ca-preview` | Imagem de contêiner com a localidade `fr-CA`. |
| `1.1.3-amd64-fr-fr-preview` | Imagem de contêiner com a localidade `fr-FR`. |
| `1.1.3-amd64-it-it-preview` | Imagem de contêiner com a localidade `it-IT`. |
| `1.1.3-amd64-ja-jp-preview` | Imagem de contêiner com a localidade `ja-JP`. |
| `1.1.3-amd64-pt-br-preview` | Imagem de contêiner com a localidade `pt-BR`. |
| `1.1.3-amd64-zh-cn-preview` | Imagem de contêiner com a localidade `zh-CN`. |
| `1.1.2-amd64-de-de-preview` | Imagem de contêiner com a localidade `de-DE`. |
| `1.1.2-amd64-en-au-preview` | Imagem de contêiner com a localidade `en-AU`. |
| `1.1.2-amd64-en-ca-preview` | Imagem de contêiner com a localidade `en-CA`. |
| `1.1.2-amd64-en-gb-preview` | Imagem de contêiner com a localidade `en-GB`. |
| `1.1.2-amd64-en-in-preview` | Imagem de contêiner com a localidade `en-IN`. |
| `1.1.2-amd64-en-us-preview` | Imagem de contêiner com a localidade `en-US`. |
| `1.1.2-amd64-es-es-preview` | Imagem de contêiner com a localidade `es-ES`. |
| `1.1.2-amd64-es-mx-preview` | Imagem de contêiner com a localidade `es-MX`. |
| `1.1.2-amd64-fr-ca-preview` | Imagem de contêiner com a localidade `fr-CA`. |
| `1.1.2-amd64-fr-fr-preview` | Imagem de contêiner com a localidade `fr-FR`. |
| `1.1.2-amd64-it-it-preview` | Imagem de contêiner com a localidade `it-IT`. |
| `1.1.2-amd64-ja-jp-preview` | Imagem de contêiner com a localidade `ja-JP`. |
| `1.1.2-amd64-pt-br-preview` | Imagem de contêiner com a localidade `pt-BR`. |
| `1.1.2-amd64-zh-cn-preview` | Imagem de contêiner com a localidade `zh-CN`. |
| `1.1.1-amd64-de-de-preview` | Imagem de contêiner com a localidade `de-DE`. |
| `1.1.1-amd64-en-au-preview` | Imagem de contêiner com a localidade `en-AU`. |
| `1.1.1-amd64-en-ca-preview` | Imagem de contêiner com a localidade `en-CA`. |
| `1.1.1-amd64-en-gb-preview` | Imagem de contêiner com a localidade `en-GB`. |
| `1.1.1-amd64-en-in-preview` | Imagem de contêiner com a localidade `en-IN`. |
| `1.1.1-amd64-en-us-preview` | Imagem de contêiner com a localidade `en-US`. |
| `1.1.1-amd64-es-es-preview` | Imagem de contêiner com a localidade `es-ES`. |
| `1.1.1-amd64-es-mx-preview` | Imagem de contêiner com a localidade `es-MX`. |
| `1.1.1-amd64-fr-ca-preview` | Imagem de contêiner com a localidade `fr-CA`. |
| `1.1.1-amd64-fr-fr-preview` | Imagem de contêiner com a localidade `fr-FR`. |
| `1.1.1-amd64-it-it-preview` | Imagem de contêiner com a localidade `it-IT`. |
| `1.1.1-amd64-ja-jp-preview` | Imagem de contêiner com a localidade `ja-JP`. |
| `1.1.1-amd64-pt-br-preview` | Imagem de contêiner com a localidade `pt-BR`. |
| `1.1.1-amd64-zh-cn-preview` | Imagem de contêiner com a localidade `zh-CN`. |
| `1.1.0-amd64-de-de-preview` | Imagem de contêiner com a localidade `de-DE`. |
| `1.1.0-amd64-en-au-preview` | Imagem de contêiner com a localidade `en-AU`. |
| `1.1.0-amd64-en-ca-preview` | Imagem de contêiner com a localidade `en-CA`. |
| `1.1.0-amd64-en-gb-preview` | Imagem de contêiner com a localidade `en-GB`. |
| `1.1.0-amd64-en-in-preview` | Imagem de contêiner com a localidade `en-IN`. |
| `1.1.0-amd64-en-us-preview` | Imagem de contêiner com a localidade `en-US`. |
| `1.1.0-amd64-es-es-preview` | Imagem de contêiner com a localidade `es-ES`. |
| `1.1.0-amd64-es-mx-preview` | Imagem de contêiner com a localidade `es-MX`. |
| `1.1.0-amd64-fr-ca-preview` | Imagem de contêiner com a localidade `fr-CA`. |
| `1.1.0-amd64-fr-fr-preview` | Imagem de contêiner com a localidade `fr-FR`. |
| `1.1.0-amd64-it-it-preview` | Imagem de contêiner com a localidade `it-IT`. |
| `1.1.0-amd64-ja-jp-preview` | Imagem de contêiner com a localidade `ja-JP`. |
| `1.1.0-amd64-pt-br-preview` | Imagem de contêiner com a localidade `pt-BR`. |
| `1.1.0-amd64-zh-cn-preview` | Imagem de contêiner com a localidade `zh-CN`. |
| `1.0.0-amd64-de-de-preview` | Imagem de contêiner com a localidade `de-DE`. |
| `1.0.0-amd64-en-au-preview` | Imagem de contêiner com a localidade `en-AU`. |
| `1.0.0-amd64-en-ca-preview` | Imagem de contêiner com a localidade `en-CA`. |
| `1.0.0-amd64-en-gb-preview` | Imagem de contêiner com a localidade `en-GB`. |
| `1.0.0-amd64-en-in-preview` | Imagem de contêiner com a localidade `en-IN`. |
| `1.0.0-amd64-en-us-preview` | Imagem de contêiner com a localidade `en-US`. |
| `1.0.0-amd64-es-es-preview` | Imagem de contêiner com a localidade `es-ES`. |
| `1.0.0-amd64-es-mx-preview` | Imagem de contêiner com a localidade `es-MX`. |
| `1.0.0-amd64-fr-ca-preview` | Imagem de contêiner com a localidade `fr-CA`. |
| `1.0.0-amd64-fr-fr-preview` | Imagem de contêiner com a localidade `fr-FR`. |
| `1.0.0-amd64-it-it-preview` | Imagem de contêiner com a localidade `it-IT`. |
| `1.0.0-amd64-ja-jp-preview` | Imagem de contêiner com a localidade `ja-JP`. |
| `1.0.0-amd64-pt-br-preview` | Imagem de contêiner com a localidade `pt-BR`. |
| `1.0.0-amd64-zh-cn-preview` | Imagem de contêiner com a localidade `zh-CN`. |

## <a name="text-to-speech"></a>Conversão de texto em voz

A imagem de contêiner de [conversão de texto em fala][sp-tts] pode ser encontrada no registro de contêiner `containerpreview.azurecr.io`. Ele reside no repositório `microsoft` e é nomeado `cognitive-services-text-to-speech`. O nome da imagem de contêiner totalmente qualificado é, `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech`.

Essa imagem de contêiner tem as seguintes marcas disponíveis:

| Marcas de imagem | Notas |
|------------|:------|
| `latest` | Imagem de contêiner com a localidade de `en-US` e `en-US-JessaRUS` voz. |
| `1.3.0-amd64-ar-eg-hoda-preview` | Imagem de contêiner com a localidade de `ar-EG` e `ar-EG-Hoda` voz. |
| `1.3.0-amd64-ar-sa-naayf-preview` | Imagem de contêiner com a localidade de `ar-SA` e `ar-SA-Naayf` voz. |
| `1.3.0-amd64-bg-bg-ivan-preview` | Imagem de contêiner com a localidade de `bg-BG` e `bg-BG-Ivan` voz. |
| `1.3.0-amd64-ca-es-herenarus-preview` | Imagem de contêiner com a localidade de `ca-ES` e `ca-ES-HerenaRUS` voz. |
| `1.3.0-amd64-cs-cz-jakub-preview` | Imagem de contêiner com a localidade de `cs-CZ` e `cs-CZ-Jakub` voz. |
| `1.3.0-amd64-da-dk-hellerus-preview` | Imagem de contêiner com a localidade de `da-DK` e `da-DK-HelleRUS` voz. |
| `1.3.0-amd64-de-at-michael-preview` | Imagem de contêiner com a localidade de `de-AT` e `de-AT-Michael` voz. |
| `1.3.0-amd64-de-ch-karsten-preview` | Imagem de contêiner com a localidade de `de-CH` e `de-CH-Karsten` voz. |
| `1.3.0-amd64-de-de-hedda-preview` | Imagem de contêiner com a localidade de `de-DE` e `de-DE-Hedda` voz. |
| `1.3.0-amd64-de-de-heddarus-preview` | Imagem de contêiner com a localidade de `de-DE` e `de-DE-Hedda` voz. |
| `1.3.0-amd64-de-de-heddarus-preview` | Imagem de contêiner com a localidade de `de-DE` e `de-DE-HeddaRUS` voz. |
| `1.3.0-amd64-de-de-stefan-apollo-preview` | Imagem de contêiner com a localidade de `de-DE` e `de-DE-Stefan-Apollo` voz. |
| `1.3.0-amd64-el-gr-stefanos-preview` | Imagem de contêiner com a localidade de `el-GR` e `el-GR-Stefanos` voz. |
| `1.3.0-amd64-en-au-catherine-preview` | Imagem de contêiner com a localidade de `en-AU` e `en-AU-Catherine` voz. |
| `1.3.0-amd64-en-au-hayleyrus-preview` | Imagem de contêiner com a localidade de `en-AU` e `en-AU-HayleyRUS` voz. |
| `1.3.0-amd64-en-ca-heatherrus-preview` | Imagem de contêiner com a localidade de `en-CA` e `en-CA-HeatherRUS` voz. |
| `1.3.0-amd64-en-ca-linda-preview` | Imagem de contêiner com a localidade de `en-CA` e `en-CA-Linda` voz. |
| `1.3.0-amd64-en-gb-george-apollo-preview` | Imagem de contêiner com a localidade de `en-GB` e `en-GB-George-Apollo` voz. |
| `1.3.0-amd64-en-gb-hazelrus-preview` | Imagem de contêiner com a localidade de `en-GB` e `en-GB-HazelRUS` voz. |
| `1.3.0-amd64-en-gb-susan-apollo-preview` | Imagem de contêiner com a localidade de `en-GB` e `en-GB-Susan-Apollo` voz. |
| `1.3.0-amd64-en-ie-sean-preview` | Imagem de contêiner com a localidade de `en-IE` e `en-IE-Sean` voz. |
| `1.3.0-amd64-en-in-heera-apollo-preview` | Imagem de contêiner com a localidade de `en-IN` e `en-IN-Heera-Apollo` voz. |
| `1.3.0-amd64-en-in-priyarus-preview` | Imagem de contêiner com a localidade de `en-IN` e `en-IN-PriyaRUS` voz. |
| `1.3.0-amd64-en-in-ravi-apollo-preview` | Imagem de contêiner com a localidade de `en-IN` e `en-IN-Ravi-Apollo` voz. |
| `1.3.0-amd64-en-us-benjaminrus-preview` | Imagem de contêiner com a localidade de `en-US` e `en-US-BenjaminRUS` voz. |
| `1.3.0-amd64-en-us-guy24krus-preview` | Imagem de contêiner com a localidade de `en-US` e `en-US-Guy24kRUS` voz. |
| `1.3.0-amd64-en-us-jessa24krus-preview` | Imagem de contêiner com a localidade de `en-US` e `en-US-Jessa24kRUS` voz. |
| `1.3.0-amd64-en-us-jessarus-preview` | Imagem de contêiner com a localidade de `en-US` e `en-US-JessaRUS` voz. |
| `1.3.0-amd64-en-us-zirarus-preview` | Imagem de contêiner com a localidade de `en-US` e `en-US-ZiraRUS` voz. |
| `1.3.0-amd64-es-es-helenarus-preview` | Imagem de contêiner com a localidade de `es-ES` e `es-ES-HelenaRUS` voz. |
| `1.3.0-amd64-es-es-laura-apollo-preview` | Imagem de contêiner com a localidade de `es-ES` e `es-ES-Laura-Apollo` voz. |
| `1.3.0-amd64-es-es-pablo-apollo-preview` | Imagem de contêiner com a localidade de `es-ES` e `es-ES-Pablo-Apollo` voz. |
| `1.3.0-amd64-es-mx-hildarus-preview` | Imagem de contêiner com a localidade de `es-MX` e `es-MX-HildaRUS` voz. |
| `1.3.0-amd64-es-mx-raul-apollo-preview` | Imagem de contêiner com a localidade de `es-MX` e `es-MX-Raul-Apollo` voz. |
| `1.3.0-amd64-fi-fi-heidirus-preview` | Imagem de contêiner com a localidade de `fi-FI` e `fi-FI-HeidiRUS` voz. |
| `1.3.0-amd64-fr-ca-caroline-preview` | Imagem de contêiner com a localidade de `fr-CA` e `fr-CA-Caroline` voz. |
| `1.3.0-amd64-fr-ca-harmonierus-preview` | Imagem de contêiner com a localidade de `fr-CA` e `fr-CA-HarmonieRUS` voz. |
| `1.3.0-amd64-fr-ch-guillaume-preview` | Imagem de contêiner com a localidade de `fr-CH` e `fr-CH-Guillaume` voz. |
| `1.3.0-amd64-fr-fr-hortenserus-preview` | Imagem de contêiner com a localidade de `fr-FR` e `fr-FR-HortenseRUS` voz. |
| `1.3.0-amd64-fr-fr-julie-apollo-preview` | Imagem de contêiner com a localidade de `fr-FR` e `fr-FR-Julie-Apollo` voz. |
| `1.3.0-amd64-fr-fr-paul-apollo-preview` | Imagem de contêiner com a localidade de `fr-FR` e `fr-FR-Paul-Apollo` voz. |
| `1.3.0-amd64-he-il-asaf-preview` | Imagem de contêiner com a localidade de `he-IL` e `he-IL-Asaf` voz. |
| `1.3.0-amd64-hi-in-hemant-preview` | Imagem de contêiner com a localidade de `hi-IN` e `hi-IN-Hemant` voz. |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview` | Imagem de contêiner com a localidade de `hi-IN` e `hi-IN-Kalpana-Apollo` voz. |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview` | Imagem de contêiner com a localidade de `hi-IN` e `hi-IN-Kalpana` voz. |
| `1.3.0-amd64-hi-in-kalpana-preview` | Imagem de contêiner com a localidade de `hi-IN` e `hi-IN-Kalpana` voz. |
| `1.3.0-amd64-hr-hr-matej-preview` | Imagem de contêiner com a localidade de `hr-HR` e `hr-HR-Matej` voz. |
| `1.3.0-amd64-hu-hu-szabolcs-preview` | Imagem de contêiner com a localidade de `hu-HU` e `hu-HU-Szabolcs` voz. |
| `1.3.0-amd64-id-id-andika-preview` | Imagem de contêiner com a localidade de `id-ID` e `id-ID-Andika` voz. |
| `1.3.0-amd64-it-it-cosimo-apollo-preview` | Imagem de contêiner com a localidade de `it-IT` e `it-IT-Cosimo-Apollo` voz. |
| `1.3.0-amd64-it-it-luciarus-preview` | Imagem de contêiner com a localidade de `it-IT` e `it-IT-LuciaRUS` voz. |
| `1.3.0-amd64-ja-jp-ayumi-apollo-preview` | Imagem de contêiner com a localidade de `ja-JP` e `ja-JP-Ayumi-Apollo` voz. |
| `1.3.0-amd64-ja-jp-harukarus-preview` | Imagem de contêiner com a localidade de `ja-JP` e `ja-JP-HarukaRUS` voz. |
| `1.3.0-amd64-ja-jp-ichiro-apollo-preview` | Imagem de contêiner com a localidade de `ja-JP` e `ja-JP-Ichiro-Apollo` voz. |
| `1.3.0-amd64-ko-kr-heamirus-preview` | Imagem de contêiner com a localidade de `ko-KR` e `ko-KR-HeamiRUS` voz. |
| `1.3.0-amd64-ms-my-rizwan-preview` | Imagem de contêiner com a localidade de `ms-MY` e `ms-MY-Rizwan` voz. |
| `1.3.0-amd64-nb-no-huldarus-preview` | Imagem de contêiner com a localidade de `nb-NO` e `nb-NO-HuldaRUS` voz. |
| `1.3.0-amd64-nl-nl-hannarus-preview` | Imagem de contêiner com a localidade de `nl-NL` e `nl-NL-HannaRUS` voz. |
| `1.3.0-amd64-pl-pl-paulinarus-preview` | Imagem de contêiner com a localidade de `pl-PL` e `pl-PL-PaulinaRUS` voz. |
| `1.3.0-amd64-pt-br-daniel-apollo-preview` | Imagem de contêiner com a localidade de `pt-BR` e `pt-BR-Daniel-Apollo` voz. |
| `1.3.0-amd64-pt-br-heloisarus-preview` | Imagem de contêiner com a localidade de `pt-BR` e `pt-BR-HeloisaRUS` voz. |
| `1.3.0-amd64-pt-pt-heliarus-preview` | Imagem de contêiner com a localidade de `pt-PT` e `pt-PT-HeliaRUS` voz. |
| `1.3.0-amd64-ro-ro-andrei-preview` | Imagem de contêiner com a localidade de `ro-RO` e `ro-RO-Andrei` voz. |
| `1.3.0-amd64-ru-ru-ekaterinarus-preview` | Imagem de contêiner com a localidade de `ru-RU` e `ru-RU-EkaterinaRUS` voz. |
| `1.3.0-amd64-ru-ru-irina-apollo-preview` | Imagem de contêiner com a localidade de `ru-RU` e `ru-RU-Irina-Apollo` voz. |
| `1.3.0-amd64-ru-ru-pavel-apollo-preview` | Imagem de contêiner com a localidade de `ru-RU` e `ru-RU-Pavel-Apollo` voz. |
| `1.3.0-amd64-sk-sk-filip-preview` | Imagem de contêiner com a localidade de `sk-SK` e `sk-SK-Filip` voz. |
| `1.3.0-amd64-sl-si-lado-preview` | Imagem de contêiner com a localidade de `sl-SI` e `sl-SI-Lado` voz. |
| `1.3.0-amd64-sv-se-hedvigrus-preview` | Imagem de contêiner com a localidade de `sv-SE` e `sv-SE-HedvigRUS` voz. |
| `1.3.0-amd64-ta-in-valluvar-preview` | Imagem de contêiner com a localidade de `ta-IN` e `ta-IN-Valluvar` voz. |
| `1.3.0-amd64-te-in-chitra-preview` | Imagem de contêiner com a localidade de `te-IN` e `te-IN-Chitra` voz. |
| `1.3.0-amd64-th-th-pattara-preview` | Imagem de contêiner com a localidade de `th-TH` e `th-TH-Pattara` voz. |
| `1.3.0-amd64-tr-tr-sedarus-preview` | Imagem de contêiner com a localidade de `tr-TR` e `tr-TR-SedaRUS` voz. |
| `1.3.0-amd64-vi-vn-an-preview` | Imagem de contêiner com a localidade de `vi-VN` e `vi-VN-An` voz. |
| `1.3.0-amd64-zh-cn-huihuirus-preview` | Imagem de contêiner com a localidade de `zh-CN` e `zh-CN-HuihuiRUS` voz. |
| `1.3.0-amd64-zh-cn-kangkang-apollo-preview` | Imagem de contêiner com a localidade de `zh-CN` e `zh-CN-Kangkang-Apollo` voz. |
| `1.3.0-amd64-zh-cn-yaoyao-apollo-preview` | Imagem de contêiner com a localidade de `zh-CN` e `zh-CN-Yaoyao-Apollo` voz. |
| `1.3.0-amd64-zh-hk-danny-apollo-preview` | Imagem de contêiner com a localidade de `zh-HK` e `zh-HK-Danny-Apollo` voz. |
| `1.3.0-amd64-zh-hk-tracy-apollo-preview` | Imagem de contêiner com a localidade de `zh-HK` e `zh-HK-Tracy-Apollo` voz. |
| `1.3.0-amd64-zh-hk-tracyrus-preview` | Imagem de contêiner com a localidade de `zh-HK` e `zh-HK-TracyRUS` voz. |
| `1.3.0-amd64-zh-tw-hanhanrus-preview` | Imagem de contêiner com a localidade de `zh-TW` e `zh-TW-HanHanRUS` voz. |
| `1.3.0-amd64-zh-tw-yating-apollo-preview` | Imagem de contêiner com a localidade de `zh-TW` e `zh-TW-Yating-Apollo` voz. |
| `1.3.0-amd64-zh-tw-zhiwei-apollo-preview` | Imagem de contêiner com a localidade de `zh-TW` e `zh-TW-Zhiwei-Apollo` voz. |
| `1.2.0-amd64-de-de-heddarus-preview` | Imagem de contêiner com a localidade de `de-DE` e `de-DE-Hedda` voz. |
| `1.2.0-amd64-de-de-heddarus-preview` | Imagem de contêiner com a localidade de `de-DE` e `de-DE-HeddaRUS` voz. |
| `1.2.0-amd64-de-de-stefan-apollo-preview` | Imagem de contêiner com a localidade de `de-DE` e `de-DE-Stefan-Apollo` voz. |
| `1.2.0-amd64-en-au-catherine-preview` | Imagem de contêiner com a localidade de `en-AU` e `en-AU-Catherine` voz. |
| `1.2.0-amd64-en-au-hayleyrus-preview` | Imagem de contêiner com a localidade de `en-AU` e `en-AU-HayleyRUS` voz. |
| `1.2.0-amd64-en-gb-george-apollo-preview` | Imagem de contêiner com a localidade de `en-GB` e `en-GB-George-Apollo` voz. |
| `1.2.0-amd64-en-gb-hazelrus-preview` | Imagem de contêiner com a localidade de `en-GB` e `en-GB-HazelRUS` voz. |
| `1.2.0-amd64-en-gb-susan-apollo-preview` | Imagem de contêiner com a localidade de `en-GB` e `en-GB-Susan-Apollo` voz. |
| `1.2.0-amd64-en-in-heera-apollo-preview` | Imagem de contêiner com a localidade de `en-IN` e `en-IN-Heera-Apollo` voz. |
| `1.2.0-amd64-en-in-priyarus-preview` | Imagem de contêiner com a localidade de `en-IN` e `en-IN-PriyaRUS` voz. |
| `1.2.0-amd64-en-in-ravi-apollo-preview` | Imagem de contêiner com a localidade de `en-IN` e `en-IN-Ravi-Apollo` voz. |
| `1.2.0-amd64-en-us-benjaminrus-preview` | Imagem de contêiner com a localidade de `en-US` e `en-US-BenjaminRUS` voz. |
| `1.2.0-amd64-en-us-guy24krus-preview` | Imagem de contêiner com a localidade de `en-US` e `en-US-Guy24kRUS` voz. |
| `1.2.0-amd64-en-us-jessa24krus-preview` | Imagem de contêiner com a localidade de `en-US` e `en-US-Jessa24kRUS` voz. |
| `1.2.0-amd64-en-us-jessarus-preview` | Imagem de contêiner com a localidade de `en-US` e `en-US-JessaRUS` voz. |
| `1.2.0-amd64-en-us-zirarus-preview` | Imagem de contêiner com a localidade de `en-US` e `en-US-ZiraRUS` voz. |
| `1.2.0-amd64-es-es-helenarus-preview` | Imagem de contêiner com a localidade de `es-ES` e `es-ES-HelenaRUS` voz. |
| `1.2.0-amd64-es-es-laura-apollo-preview` | Imagem de contêiner com a localidade de `es-ES` e `es-ES-Laura-Apollo` voz. |
| `1.2.0-amd64-es-es-pablo-apollo-preview` | Imagem de contêiner com a localidade de `es-ES` e `es-ES-Pablo-Apollo` voz. |
| `1.2.0-amd64-es-mx-hildarus-preview` | Imagem de contêiner com a localidade de `es-MX` e `es-MX-HildaRUS` voz. |
| `1.2.0-amd64-es-mx-raul-apollo-preview` | Imagem de contêiner com a localidade de `es-MX` e `es-MX-Raul-Apollo` voz. |
| `1.2.0-amd64-fr-ca-caroline-preview` | Imagem de contêiner com a localidade de `fr-CA` e `fr-CA-Caroline` voz. |
| `1.2.0-amd64-fr-ca-harmonierus-preview` | Imagem de contêiner com a localidade de `fr-CA` e `fr-CA-HarmonieRUS` voz. |
| `1.2.0-amd64-fr-fr-hortenserus-preview` | Imagem de contêiner com a localidade de `fr-FR` e `fr-FR-HortenseRUS` voz. |
| `1.2.0-amd64-fr-fr-julie-apollo-preview` | Imagem de contêiner com a localidade de `fr-FR` e `fr-FR-Julie-Apollo` voz. |
| `1.2.0-amd64-fr-fr-paul-apollo-preview` | Imagem de contêiner com a localidade de `fr-FR` e `fr-FR-Paul-Apollo` voz. |
| `1.2.0-amd64-it-it-cosimo-apollo-preview` | Imagem de contêiner com a localidade de `it-IT` e `it-IT-Cosimo-Apollo` voz. |
| `1.2.0-amd64-it-it-luciarus-preview` | Imagem de contêiner com a localidade de `it-IT` e `it-IT-LuciaRUS` voz. |
| `1.2.0-amd64-ja-jp-ayumi-apollo-preview` | Imagem de contêiner com a localidade de `ja-JP` e `ja-JP-Ayumi-Apollo` voz. |
| `1.2.0-amd64-ja-jp-harukarus-preview` | Imagem de contêiner com a localidade de `ja-JP` e `ja-JP-HarukaRUS` voz. |
| `1.2.0-amd64-ja-jp-ichiro-apollo-preview` | Imagem de contêiner com a localidade de `ja-JP` e `ja-JP-Ichiro-Apollo` voz. |
| `1.2.0-amd64-ko-kr-heamirus-preview` | Imagem de contêiner com a localidade de `ko-KR` e `ko-KR-HeamiRUS` voz. |
| `1.2.0-amd64-pt-br-daniel-apollo-preview` | Imagem de contêiner com a localidade de `pt-BR` e `pt-BR-Daniel-Apollo` voz. |
| `1.2.0-amd64-pt-br-heloisarus-preview` | Imagem de contêiner com a localidade de `pt-BR` e `pt-BR-HeloisaRUS` voz. |
| `1.2.0-amd64-zh-cn-huihuirus-preview` | Imagem de contêiner com a localidade de `zh-CN` e `zh-CN-HuihuiRUS` voz. |
| `1.2.0-amd64-zh-cn-kangkang-apollo-preview` | Imagem de contêiner com a localidade de `zh-CN` e `zh-CN-Kangkang-Apollo` voz. |
| `1.2.0-amd64-zh-cn-yaoyao-apollo-preview` | Imagem de contêiner com a localidade de `zh-CN` e `zh-CN-Yaoyao-Apollo` voz. |
| `1.1.0-amd64-de-de-hedda-preview` | Imagem de contêiner com a localidade de `de-DE` e `de-DE-Hedda` voz. |
| `1.1.0-amd64-de-de-heddarus-preview` | Imagem de contêiner com a localidade de `de-DE` e `de-DE-Hedda` voz. |
| `1.1.0-amd64-de-de-heddarus-preview` | Imagem de contêiner com a localidade de `de-DE` e `de-DE-HeddaRUS` voz. |
| `1.1.0-amd64-de-de-stefan-apollo-preview` | Imagem de contêiner com a localidade de `de-DE` e `de-DE-Stefan-Apollo` voz. |
| `1.1.0-amd64-en-au-catherine-preview` | Imagem de contêiner com a localidade de `en-AU` e `en-AU-Catherine` voz. |
| `1.1.0-amd64-en-au-hayleyrus-preview` | Imagem de contêiner com a localidade de `en-AU` e `en-AU-HayleyRUS` voz. |
| `1.1.0-amd64-en-gb-george-apollo-preview` | Imagem de contêiner com a localidade de `en-GB` e `en-GB-George-Apollo` voz. |
| `1.1.0-amd64-en-gb-hazelrus-preview` | Imagem de contêiner com a localidade de `en-GB` e `en-GB-HazelRUS` voz. |
| `1.1.0-amd64-en-gb-susan-apollo-preview` | Imagem de contêiner com a localidade de `en-GB` e `en-GB-Susan-Apollo` voz. |
| `1.1.0-amd64-en-in-heera-apollo-preview` | Imagem de contêiner com a localidade de `en-IN` e `en-IN-Heera-Apollo` voz. |
| `1.1.0-amd64-en-in-priyarus-preview` | Imagem de contêiner com a localidade de `en-IN` e `en-IN-PriyaRUS` voz. |
| `1.1.0-amd64-en-in-ravi-apollo-preview` | Imagem de contêiner com a localidade de `en-IN` e `en-IN-Ravi-Apollo` voz. |
| `1.1.0-amd64-en-us-benjaminrus-preview` | Imagem de contêiner com a localidade de `en-US` e `en-US-BenjaminRUS` voz. |
| `1.1.0-amd64-en-us-guy24krus-preview` | Imagem de contêiner com a localidade de `en-US` e `en-US-Guy24kRUS` voz. |
| `1.1.0-amd64-en-us-jessa24krus-preview` | Imagem de contêiner com a localidade de `en-US` e `en-US-Jessa24kRUS` voz. |
| `1.1.0-amd64-en-us-jessarus-preview` | Imagem de contêiner com a localidade de `en-US` e `en-US-JessaRUS` voz. |
| `1.1.0-amd64-en-us-zirarus-preview` | Imagem de contêiner com a localidade de `en-US` e `en-US-ZiraRUS` voz. |
| `1.1.0-amd64-es-es-helenarus-preview` | Imagem de contêiner com a localidade de `es-ES` e `es-ES-HelenaRUS` voz. |
| `1.1.0-amd64-es-es-laura-apollo-preview` | Imagem de contêiner com a localidade de `es-ES` e `es-ES-Laura-Apollo` voz. |
| `1.1.0-amd64-es-es-pablo-apollo-preview` | Imagem de contêiner com a localidade de `es-ES` e `es-ES-Pablo-Apollo` voz. |
| `1.1.0-amd64-es-mx-hildarus-preview` | Imagem de contêiner com a localidade de `es-MX` e `es-MX-HildaRUS` voz. |
| `1.1.0-amd64-es-mx-raul-apollo-preview` | Imagem de contêiner com a localidade de `es-MX` e `es-MX-Raul-Apollo` voz. |
| `1.1.0-amd64-fr-ca-caroline-preview` | Imagem de contêiner com a localidade de `fr-CA` e `fr-CA-Caroline` voz. |
| `1.1.0-amd64-fr-ca-harmonierus-preview` | Imagem de contêiner com a localidade de `fr-CA` e `fr-CA-HarmonieRUS` voz. |
| `1.1.0-amd64-fr-fr-hortenserus-preview` | Imagem de contêiner com a localidade de `fr-FR` e `fr-FR-HortenseRUS` voz. |
| `1.1.0-amd64-fr-fr-julie-apollo-preview` | Imagem de contêiner com a localidade de `fr-FR` e `fr-FR-Julie-Apollo` voz. |
| `1.1.0-amd64-fr-fr-paul-apollo-preview` | Imagem de contêiner com a localidade de `fr-FR` e `fr-FR-Paul-Apollo` voz. |
| `1.1.0-amd64-it-it-cosimo-apollo-preview` | Imagem de contêiner com a localidade de `it-IT` e `it-IT-Cosimo-Apollo` voz. |
| `1.1.0-amd64-it-it-luciarus-preview` | Imagem de contêiner com a localidade de `it-IT` e `it-IT-LuciaRUS` voz. |
| `1.1.0-amd64-ja-jp-ayumi-apollo-preview` | Imagem de contêiner com a localidade de `ja-JP` e `ja-JP-Ayumi-Apollo` voz. |
| `1.1.0-amd64-ja-jp-harukarus-preview` | Imagem de contêiner com a localidade de `ja-JP` e `ja-JP-HarukaRUS` voz. |
| `1.1.0-amd64-ja-jp-ichiro-apollo-preview` | Imagem de contêiner com a localidade de `ja-JP` e `ja-JP-Ichiro-Apollo` voz. |
| `1.1.0-amd64-ko-kr-heamirus-preview` | Imagem de contêiner com a localidade de `ko-KR` e `ko-KR-HeamiRUS` voz. |
| `1.1.0-amd64-pt-br-daniel-apollo-preview` | Imagem de contêiner com a localidade de `pt-BR` e `pt-BR-Daniel-Apollo` voz. |
| `1.1.0-amd64-pt-br-heloisarus-preview` | Imagem de contêiner com a localidade de `pt-BR` e `pt-BR-HeloisaRUS` voz. |
| `1.1.0-amd64-zh-cn-huihuirus-preview` | Imagem de contêiner com a localidade de `zh-CN` e `zh-CN-HuihuiRUS` voz. |
| `1.1.0-amd64-zh-cn-kangkang-apollo-preview` | Imagem de contêiner com a localidade de `zh-CN` e `zh-CN-Kangkang-Apollo` voz. |
| `1.1.0-amd64-zh-cn-yaoyao-apollo-preview` | Imagem de contêiner com a localidade de `zh-CN` e `zh-CN-Yaoyao-Apollo` voz. |
| `1.0.0-amd64-en-us-benjaminrus-preview` | Imagem de contêiner com a localidade de `en-US` e `en-US-BenjaminRUS` voz. |
| `1.0.0-amd64-en-us-guy24krus-preview` | Imagem de contêiner com a localidade de `en-US` e `en-US-Guy24kRUS` voz. |
| `1.0.0-amd64-en-us-jessa24krus-preview` | Imagem de contêiner com a localidade de `en-US` e `en-US-Jessa24kRUS` voz. |
| `1.0.0-amd64-en-us-jessarus-preview` | Imagem de contêiner com a localidade de `en-US` e `en-US-JessaRUS` voz. |
| `1.0.0-amd64-en-us-zirarus-preview` | Imagem de contêiner com a localidade de `en-US` e `en-US-ZiraRUS` voz. |
| `1.0.0-amd64-zh-cn-huihuirus-preview` | Imagem de contêiner com a localidade de `zh-CN` e `zh-CN-HuihuiRUS` voz. |
| `1.0.0-amd64-zh-cn-kangkang-apollo-preview` | Imagem de contêiner com a localidade de `zh-CN` e `zh-CN-Kangkang-Apollo` voz. |
| `1.0.0-amd64-zh-cn-yaoyao-apollo-preview` | Imagem de contêiner com a localidade de `zh-CN` e `zh-CN-Yaoyao-Apollo` voz. |

## <a name="key-phrase-extraction"></a>Extração de Expressões-Chave

A imagem de contêiner [extração de frases-chave][ta-kp] pode ser encontrada na agregação do registro de contêiner `mcr.microsoft.com`. Ele reside no repositório `azure-cognitive-services` e é nomeado `keyphrase`. O nome da imagem de contêiner totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/keyphrase`.

Essa imagem de contêiner tem as seguintes marcas disponíveis:

| Marcas de imagem | Notas |
|------------|:------|
| `latest` | |
| `1.1.009301-amd64-preview` | |
| `1.1.008510001-amd64-preview` | |
| `1.1.007750002-amd64-preview` | |
| `1.1.007360001-amd64-preview` | |
| `1.1.006770001-amd64-preview` | |

## <a name="language-detection"></a>Deteção de Idioma

A imagem de contêiner [detecção de idioma][ta-la] pode ser encontrada na agregação do registro de contêiner `mcr.microsoft.com`. Ele reside no repositório `azure-cognitive-services` e é nomeado `language`. O nome da imagem de contêiner totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/language`.

Essa imagem de contêiner tem as seguintes marcas disponíveis:

| Marcas de imagem | Notas |
|------------|:------|
| `latest` | |
| `1.1.009301-amd64-preview` | |
| `1.1.008510001-amd64-preview` | |
| `1.1.007750002-amd64-preview` | |
| `1.1.007360001-amd64-preview` | |
| `1.1.006770001-amd64-preview` | |

## <a name="sentiment-analysis"></a>Análise de Sentimentos

A imagem de contêiner [análise de sentimento][ta-se] pode ser encontrada na agregação do registro de contêiner `mcr.microsoft.com`. Ele reside no repositório `azure-cognitive-services` e é nomeado `sentiment`. O nome da imagem de contêiner totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/sentiment`.

Essa imagem de contêiner tem as seguintes marcas disponíveis:

| Marcas de imagem | Notas |
|------------|:------|
| `latest` | |
| `1.1.009301-amd64-preview` | |
| `1.1.008510001-amd64-preview` | |
| `1.1.007750002-amd64-preview` | |
| `1.1.007360001-amd64-preview` | |
| `1.1.006770001-amd64-preview` | |

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
