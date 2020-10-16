---
title: Etiquetas de imagem de contentor dos Serviços Cognitivos
titleSuffix: Azure Cognitive Services
description: Uma lista completa de todas as etiquetas de imagem do serviço cognitivo.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 08/31/2020
ms.author: aahi
ms.openlocfilehash: 2a24433389e738bf5d0ecb7ecac6bf369c8ba183
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91369489"
---
# <a name="azure-cognitive-services-container-image-tags"></a>Etiquetas de imagem de recipientes de serviços cognitivos Azure

A Azure Cognitive Services oferece muitas imagens de contentores. Os registos dos contentores e os repositórios correspondentes variam entre as imagens dos contentores. Cada nome de imagem do recipiente oferece várias tags. Uma etiqueta de imagem de recipiente é um mecanismo de versão da imagem do recipiente. Este artigo destina-se a ser usado como uma referência abrangente para a listagem de todas as imagens dos contentores dos Serviços Cognitivos e suas etiquetas disponíveis.

> [!TIP]
> Ao [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) utilizar, preste muita atenção ao invólucro do registo do contentor, repositório, nome de imagem do recipiente e etiqueta correspondente - uma vez que são **sensíveis ao caso**.

## <a name="anomaly-detector"></a>Detetor de Anomalias

A imagem do detetor [de anomalias][ad-containers] pode ser encontrada no sindicato do registo do `mcr.microsoft.com` contentor. Reside dentro do `azure-cognitive-services` repositório e é `anomaly-detector` nomeado. O nome de imagem do recipiente totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/anomaly-detector` .

Esta imagem do recipiente tem as seguintes etiquetas disponíveis:

| Etiquetas de imagem                    | Notas |
|-------------------------------|:------|
| `latest`                      |       |

## <a name="computer-vision"></a>Imagem Digitalizada

A imagem do recipiente OCR de [Visão Por Computador][cv-containers] pode ser encontrada no registo do `containerpreview.azurecr.io` contentor. Reside dentro do `microsoft` repositório e é `cognitive-services-read` nomeado. O nome de imagem do recipiente totalmente qualificado é, `containerpreview.azurecr.io/microsoft/cognitive-services-read` .

Esta imagem do recipiente tem as seguintes etiquetas disponíveis:

| Etiquetas de imagem                    | Notas |
|-------------------------------|:------|
| `latest ( (2.0.013250001-amd64-preview)` | • Diminuir ainda mais a utilização da memória para o recipiente. |
|                                          | • É necessária cache externa para a configuração de multi-cápsulas. Por exemplo, configurar Redis para o caching. |
|                                          | • Corrija o problema dos resultados em falta quando a cache redis estiver configurada e resultadoExpirationPeriod=0.  |
|                                          | • Remover a limitação do tamanho do corpo do pedido de 26MB. O recipiente pode agora aceitar >ficheiros de 26MB.  |
|                                          | • Adicione a carimbo de tempo e construa a versão para o registo de consolas.  |
| `1.1.013050001-amd64-preview`            | * ReadEngineConfig Adicionado:ResultadoExpirationPeriod configuração de inicialização do contentor para especificar quando o sistema deve limpar os resultados do reconhecimento. |
|                                          | A definição é em horas, e o valor padrão é de 48hr.   |
|                                          |   A definição pode reduzir a utilização da memória para o armazenamento de resultados, especialmente quando é utilizado o armazenamento em memória do recipiente.  |
|                                          |    * Exemplo 1. ReadEngineConfig:ResultExpirationPeriod=1, o sistema limpará o resultado do reconhecimento 1hr após o processo.   |
|                                          |    * Exemplo 2. ReadEngineConfig:ResultExpirationPeriod=0, o sistema limpará o resultado do reconhecimento após a recuperação do resultado.  |
|                                          | Fixo um Erro de Servidor Interno 500 quando o formato de imagem inválido é transmitido para o sistema. Irá agora devolver um erro de 400:   |
|                                          | `{`  |
|                                          | `"error": {`  |
|                                          |      `"code": "InvalidImageSize",`  |
|                                          |      `"message": "Image must be between 1024 and 209715200 bytes."`  |
|                                          |          `}`  |
|                                          | `}`  |
| `1.1.011580001-amd64-preview` |       |
| `1.1.009920003-amd64-preview` |       |
| `1.1.009910003-amd64-preview` |       |

## <a name="face"></a>Rostos

A imagem do recipiente [face][fa-containers] pode ser encontrada no registo do `containerpreview.azurecr.io` contentor. Reside dentro do `microsoft` repositório e é `cognitive-services-face` nomeado. O nome de imagem do recipiente totalmente qualificado é, `containerpreview.azurecr.io/microsoft/cognitive-services-face` .

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

A imagem do recipiente [do "Reconhecimento de Formulário"][fr-containers] pode ser encontrada no registo do `containerpreview.azurecr.io` contentor. Reside dentro do `microsoft` repositório e é `cognitive-services-form-recognizer` nomeado. O nome de imagem do recipiente totalmente qualificado é, `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` .

Esta imagem do recipiente tem as seguintes etiquetas disponíveis:

| Etiquetas de imagem                    | Notas |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008640001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |

## <a name="language-understanding-luis"></a>Compreensão de Idiomas (LUIS)

A imagem do contentor [LUIS][lu-containers] pode ser encontrada no sindicato do `mcr.microsoft.com` registo de contentores. Reside dentro do `azure-cognitive-services` repositório e é `luis` nomeado. O nome de imagem do recipiente totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/luis` .

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

## <a name="custom-speech-to-text"></a>Discurso-a-texto personalizado

A imagem do recipiente [personalizado discurso-a-texto][sp-cstt] pode ser encontrada no registo do `containerpreview.azurecr.io` contentor. Reside dentro do `microsoft` repositório e é `cognitive-services-custom-speech-to-text` nomeado. O nome de imagem do recipiente totalmente qualificado é, `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` .

Esta imagem do recipiente tem as seguintes etiquetas disponíveis:

| Etiquetas de imagem            | Notas |
|-----------------------|:------|
| `latest`              |       |
| `2.5.0-amd64`         |       |
| `2.4.0-amd64-preview` |       |
| `2.3.1-amd64-preview` |       | 
| `2.3.0-amd64-preview` |       |
| `2.2.0-amd64-preview` |       |
| `2.1.1-amd64-preview` |       |
| `2.1.0-amd64-preview` |       |
| `2.0.2-amd64-preview` |       |
| `2.0.0-amd64-preview` |       |

## <a name="custom-text-to-speech"></a>Texto-a-discurso personalizado

A imagem do recipiente [de texto para a fala personalizado][sp-ctts] pode ser encontrada no registo do `containerpreview.azurecr.io` contentor. Reside dentro do `microsoft` repositório e é `cognitive-services-custom-text-to-speech` nomeado. O nome de imagem do recipiente totalmente qualificado é, `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` .

Esta imagem do recipiente tem as seguintes etiquetas disponíveis:

| Etiquetas de imagem            | Notas |
|-----------------------|:------|
| `latest`              |       |
| `1.7.0-amd64`         |       |
| `1.6.0-amd64-preview` |       |
| `1.6.0-amd64-preview` |       |
| `1.5.0-amd64-preview` |       |
| `1.4.0-amd64-preview` |       |
| `1.3.0-amd64-preview` |       |

## <a name="speech-to-text"></a>Conversão de voz em texto

A imagem do recipiente [discurso-texto][sp-stt] pode ser encontrada no registo do `containerpreview.azurecr.io` contentor. Reside dentro do `microsoft` repositório e é `cognitive-services-speech-to-text` nomeado. O nome de imagem do recipiente totalmente qualificado é, `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` .
As imagens de discurso a texto v2.5.0 são apoiadas na *Virgínia do Governo dos EUA.* Por favor, use o ponto final de faturação *do governo dos EUA* e as chaves api para tentar.

Esta imagem do recipiente tem as seguintes etiquetas disponíveis:

| Etiquetas de imagem                  | Notas                                    |
|-----------------------------|:-----------------------------------------|
| `latest`                    | Imagem de recipiente com o `en-US` local. |
| `2.5.0-amd64-ar-ae`         | Imagem de recipiente com o `ar-AE` local. |
| `2.5.0-amd64-ar-eg`         | Imagem de recipiente com o `ar-EG` local. |
| `2.5.0-amd64-ar-kw`         | Imagem de recipiente com o `ar-KW` local. |
| `2.5.0-amd64-ar-qa`         | Imagem de recipiente com o `ar-QA` local. |
| `2.5.0-amd64-ar-sa`         | Imagem de recipiente com o `ar-SA` local. |
| `2.5.0-amd64-ca-es`         | Imagem de recipiente com o `ca-ES` local. |
| `2.5.0-amd64-da-dk`         | Imagem de recipiente com o `da-DK` local. |
| `2.5.0-amd64-de-de`         | Imagem de recipiente com o `de-DE` local. |
| `2.5.0-amd64-en-au`         | Imagem de recipiente com o `en-AU` local. |
| `2.5.0-amd64-en-ca`         | Imagem de recipiente com o `en-CA` local. |
| `2.5.0-amd64-en-gb`         | Imagem de recipiente com o `en-GB` local. |
| `2.5.0-amd64-en-in`         | Imagem de recipiente com o `en-IN` local. |
| `2.5.0-amd64-en-nz`         | Imagem de recipiente com o `en-NZ` local. |
| `2.5.0-amd64-en-us`         | Imagem de recipiente com o `en-US` local. |
| `2.5.0-amd64-es-es`         | Imagem de recipiente com o `es-ES` local. |
| `2.5.0-amd64-es-mx`         | Imagem de recipiente com o `es-MX` local. |
| `2.5.0-amd64-fi-fi`         | Imagem de recipiente com o `fi-FI` local. |
| `2.5.0-amd64-fr-ca`         | Imagem de recipiente com o `fr-CA` local. |
| `2.5.0-amd64-fr-fr`         | Imagem de recipiente com o `fr-FR` local. |
| `2.5.0-amd64-gu-in`         | Imagem de recipiente com o `gu-IN` local. |
| `2.5.0-amd64-hi-in`         | Imagem de recipiente com o `hi-IN` local. |
| `2.5.0-amd64-it-it`         | Imagem de recipiente com o `it-IT` local. |
| `2.5.0-amd64-ja-jp`         | Imagem de recipiente com o `ja-JP` local. |
| `2.5.0-amd64-ko-kr`         | Imagem de recipiente com o `ko-KR` local. |
| `2.5.0-amd64-mr-in`         | Imagem de recipiente com o `mr-IN` local. |
| `2.5.0-amd64-nb-no`         | Imagem de recipiente com o `nb-NO` local. |
| `2.5.0-amd64-nl-nl`         | Imagem de recipiente com o `nl-NL` local. |
| `2.5.0-amd64-pl-pl`         | Imagem de recipiente com o `pl-PL` local. |
| `2.5.0-amd64-pt-br`         | Imagem de recipiente com o `pt-BR` local. |
| `2.5.0-amd64-pt-pt`         | Imagem de recipiente com o `pt-PT` local. |
| `2.5.0-amd64-ru-ru`         | Imagem de recipiente com o `ru-RU` local. |
| `2.5.0-amd64-sv-se`         | Imagem de recipiente com o `sv-SE` local. |
| `2.5.0-amd64-ta-in`         | Imagem de recipiente com o `ta-IN` local. |
| `2.5.0-amd64-te-in`         | Imagem de recipiente com o `te-IN` local. |
| `2.5.0-amd64-th-th`         | Imagem de recipiente com o `th-TH` local. |
| `2.5.0-amd64-tr-tr`         | Imagem de recipiente com o `tr-TR` local. |
| `2.5.0-amd64-zh-cn`         | Imagem de recipiente com o `zh-CN` local. |
| `2.5.0-amd64-zh-hk`         | Imagem de recipiente com o `zh-HK` local. |
| `2.5.0-amd64-zh-tw`         | Imagem de recipiente com o `zh-TW` local. |
| `2.4.0-amd64-ar-ae-preview` | Imagem de recipiente com o `ar-AE` local. |
| `2.4.0-amd64-ar-eg-preview` | Imagem de recipiente com o `ar-EG` local. |
| `2.4.0-amd64-ar-kw-preview` | Imagem de recipiente com o `ar-KW` local. |
| `2.4.0-amd64-ar-qa-preview` | Imagem de recipiente com o `ar-QA` local. |
| `2.4.0-amd64-ar-sa-preview` | Imagem de recipiente com o `ar-SA` local. |
| `2.4.0-amd64-ca-es-preview` | Imagem de recipiente com o `ca-ES` local. |
| `2.4.0-amd64-da-dk-preview` | Imagem de recipiente com o `da-DK` local. |
| `2.4.0-amd64-de-de-preview` | Imagem de recipiente com o `de-DE` local. |
| `2.4.0-amd64-en-au-preview` | Imagem de recipiente com o `en-AU` local. |
| `2.4.0-amd64-en-ca-preview` | Imagem de recipiente com o `en-CA` local. |
| `2.4.0-amd64-en-gb-preview` | Imagem de recipiente com o `en-GB` local. |
| `2.4.0-amd64-en-in-preview` | Imagem de recipiente com o `en-IN` local. |
| `2.4.0-amd64-en-nz-preview` | Imagem de recipiente com o `en-NZ` local. |
| `2.4.0-amd64-en-us-preview` | Imagem de recipiente com o `en-US` local. |
| `2.4.0-amd64-es-es-preview` | Imagem de recipiente com o `es-ES` local. |
| `2.4.0-amd64-es-mx-preview` | Imagem de recipiente com o `es-MX` local. |
| `2.4.0-amd64-fi-fi-preview` | Imagem de recipiente com o `fi-FI` local. |
| `2.4.0-amd64-fr-ca-preview` | Imagem de recipiente com o `fr-CA` local. |
| `2.4.0-amd64-fr-fr-preview` | Imagem de recipiente com o `fr-FR` local. |
| `2.4.0-amd64-gu-in-preview` | Imagem de recipiente com o `gu-IN` local. |
| `2.4.0-amd64-hi-in-preview` | Imagem de recipiente com o `hi-IN` local. |
| `2.4.0-amd64-it-it-preview` | Imagem de recipiente com o `it-IT` local. |
| `2.4.0-amd64-ja-jp-preview` | Imagem de recipiente com o `ja-JP` local. |
| `2.4.0-amd64-ko-kr-preview` | Imagem de recipiente com o `ko-KR` local. |
| `2.4.0-amd64-mr-in-preview` | Imagem de recipiente com o `mr-IN` local. |
| `2.4.0-amd64-nb-no-preview` | Imagem de recipiente com o `nb-NO` local. |
| `2.4.0-amd64-nl-nl-preview` | Imagem de recipiente com o `nl-NL` local. |
| `2.4.0-amd64-pl-pl-preview` | Imagem de recipiente com o `pl-PL` local. |
| `2.4.0-amd64-pt-br-preview` | Imagem de recipiente com o `pt-BR` local. |
| `2.4.0-amd64-pt-pt-preview` | Imagem de recipiente com o `pt-PT` local. |
| `2.4.0-amd64-ru-ru-preview` | Imagem de recipiente com o `ru-RU` local. |
| `2.4.0-amd64-sv-se-preview` | Imagem de recipiente com o `sv-SE` local. |
| `2.4.0-amd64-ta-in-preview` | Imagem de recipiente com o `ta-IN` local. |
| `2.4.0-amd64-te-in-preview` | Imagem de recipiente com o `te-IN` local. |
| `2.4.0-amd64-th-th-preview` | Imagem de recipiente com o `th-TH` local. |
| `2.4.0-amd64-tr-tr-preview` | Imagem de recipiente com o `tr-TR` local. |
| `2.4.0-amd64-zh-cn-preview` | Imagem de recipiente com o `zh-CN` local. |
| `2.4.0-amd64-zh-hk-preview` | Imagem de recipiente com o `zh-HK` local. |
| `2.4.0-amd64-zh-tw-preview` | Imagem de recipiente com o `zh-TW` local. |
| `2.3.1-amd64-ar-ae-preview` | Imagem de recipiente com o `ar-AE` local. |
| `2.3.1-amd64-ar-eg-preview` | Imagem de recipiente com o `ar-EG` local. |
| `2.3.1-amd64-ar-kw-preview` | Imagem de recipiente com o `ar-KW` local. |
| `2.3.1-amd64-ar-qa-preview` | Imagem de recipiente com o `ar-QA` local. |
| `2.3.1-amd64-ar-sa-preview` | Imagem de recipiente com o `ar-SA` local. |
| `2.3.1-amd64-ca-es-preview` | Imagem de recipiente com o `ca-ES` local. |
| `2.3.1-amd64-da-dk-preview` | Imagem de recipiente com o `da-DK` local. |
| `2.3.1-amd64-de-de-preview` | Imagem de recipiente com o `de-DE` local. |
| `2.3.1-amd64-en-au-preview` | Imagem de recipiente com o `en-AU` local. |
| `2.3.1-amd64-en-ca-preview` | Imagem de recipiente com o `en-CA` local. |
| `2.3.1-amd64-en-gb-preview` | Imagem de recipiente com o `en-GB` local. |
| `2.3.1-amd64-en-in-preview` | Imagem de recipiente com o `en-IN` local. |
| `2.3.1-amd64-en-nz-preview` | Imagem de recipiente com o `en-NZ` local. |
| `2.3.1-amd64-en-us-preview` | Imagem de recipiente com o `en-US` local. |
| `2.3.1-amd64-es-es-preview` | Imagem de recipiente com o `es-ES` local. |
| `2.3.1-amd64-es-mx-preview` | Imagem de recipiente com o `es-MX` local. |
| `2.3.1-amd64-fi-fi-preview` | Imagem de recipiente com o `fi-FI` local. |
| `2.3.1-amd64-fr-ca-preview` | Imagem de recipiente com o `fr-CA` local. |
| `2.3.1-amd64-fr-fr-preview` | Imagem de recipiente com o `fr-FR` local. |
| `2.3.1-amd64-gu-in-preview` | Imagem de recipiente com o `gu-IN` local. |
| `2.3.1-amd64-hi-in-preview` | Imagem de recipiente com o `hi-IN` local. |
| `2.3.1-amd64-it-it-preview` | Imagem de recipiente com o `it-IT` local. |
| `2.3.1-amd64-ja-jp-preview` | Imagem de recipiente com o `ja-JP` local. |
| `2.3.1-amd64-ko-kr-preview` | Imagem de recipiente com o `ko-KR` local. |
| `2.3.1-amd64-mr-in-preview` | Imagem de recipiente com o `mr-IN` local. |
| `2.3.1-amd64-nb-no-preview` | Imagem de recipiente com o `nb-NO` local. |
| `2.3.1-amd64-nl-nl-preview` | Imagem de recipiente com o `nl-NL` local. |
| `2.3.1-amd64-pl-pl-preview` | Imagem de recipiente com o `pl-PL` local. |
| `2.3.1-amd64-pt-br-preview` | Imagem de recipiente com o `pt-BR` local. |
| `2.3.1-amd64-pt-pt-preview` | Imagem de recipiente com o `pt-PT` local. |
| `2.3.1-amd64-ru-ru-preview` | Imagem de recipiente com o `ru-RU` local. |
| `2.3.1-amd64-sv-se-preview` | Imagem de recipiente com o `sv-SE` local. |
| `2.3.1-amd64-ta-in-preview` | Imagem de recipiente com o `ta-IN` local. |
| `2.3.1-amd64-te-in-preview` | Imagem de recipiente com o `te-IN` local. |
| `2.3.1-amd64-th-th-preview` | Imagem de recipiente com o `th-TH` local. |
| `2.3.1-amd64-tr-tr-preview` | Imagem de recipiente com o `tr-TR` local. |
| `2.3.1-amd64-zh-cn-preview` | Imagem de recipiente com o `zh-CN` local. |
| `2.3.1-amd64-zh-hk-preview` | Imagem de recipiente com o `zh-HK` local. |
| `2.3.1-amd64-zh-tw-preview` | Imagem de recipiente com o `zh-TW` local. |
| `2.3.0-amd64-ar-ae-preview` | Imagem de recipiente com o `ar-AE` local. |
| `2.3.0-amd64-ar-eg-preview` | Imagem de recipiente com o `ar-EG` local. |
| `2.3.0-amd64-ar-kw-preview` | Imagem de recipiente com o `ar-KW` local. |
| `2.3.0-amd64-ar-qa-preview` | Imagem de recipiente com o `ar-QA` local. |
| `2.3.0-amd64-ar-sa-preview` | Imagem de recipiente com o `ar-SA` local. |
| `2.3.0-amd64-ca-es-preview` | Imagem de recipiente com o `ca-ES` local. |
| `2.3.0-amd64-da-dk-preview` | Imagem de recipiente com o `da-DK` local. |
| `2.3.0-amd64-de-de-preview` | Imagem de recipiente com o `de-DE` local. |
| `2.3.0-amd64-en-au-preview` | Imagem de recipiente com o `en-AU` local. |
| `2.3.0-amd64-en-ca-preview` | Imagem de recipiente com o `en-CA` local. |
| `2.3.0-amd64-en-gb-preview` | Imagem de recipiente com o `en-GB` local. |
| `2.3.0-amd64-en-in-preview` | Imagem de recipiente com o `en-IN` local. |
| `2.3.0-amd64-en-nz-preview` | Imagem de recipiente com o `en-NZ` local. |
| `2.3.0-amd64-en-us-preview` | Imagem de recipiente com o `en-US` local. |
| `2.3.0-amd64-es-es-preview` | Imagem de recipiente com o `es-ES` local. |
| `2.3.0-amd64-es-mx-preview` | Imagem de recipiente com o `es-MX` local. |
| `2.3.0-amd64-fi-fi-preview` | Imagem de recipiente com o `fi-FI` local. |
| `2.3.0-amd64-fr-ca-preview` | Imagem de recipiente com o `fr-CA` local. |
| `2.3.0-amd64-fr-fr-preview` | Imagem de recipiente com o `fr-FR` local. |
| `2.3.0-amd64-gu-in-preview` | Imagem de recipiente com o `gu-IN` local. |
| `2.3.0-amd64-hi-in-preview` | Imagem de recipiente com o `hi-IN` local. |
| `2.3.0-amd64-it-it-preview` | Imagem de recipiente com o `it-IT` local. |
| `2.3.0-amd64-ja-jp-preview` | Imagem de recipiente com o `ja-JP` local. |
| `2.3.0-amd64-ko-kr-preview` | Imagem de recipiente com o `ko-KR` local. |
| `2.3.0-amd64-mr-in-preview` | Imagem de recipiente com o `mr-IN` local. |
| `2.3.0-amd64-nb-no-preview` | Imagem de recipiente com o `nb-NO` local. |
| `2.3.0-amd64-nl-nl-preview` | Imagem de recipiente com o `nl-NL` local. |
| `2.3.0-amd64-pl-pl-preview` | Imagem de recipiente com o `pl-PL` local. |
| `2.3.0-amd64-pt-br-preview` | Imagem de recipiente com o `pt-BR` local. |
| `2.3.0-amd64-pt-pt-preview` | Imagem de recipiente com o `pt-PT` local. |
| `2.3.0-amd64-ru-ru-preview` | Imagem de recipiente com o `ru-RU` local. |
| `2.3.0-amd64-sv-se-preview` | Imagem de recipiente com o `sv-SE` local. |
| `2.3.0-amd64-ta-in-preview` | Imagem de recipiente com o `ta-IN` local. |
| `2.3.0-amd64-te-in-preview` | Imagem de recipiente com o `te-IN` local. |
| `2.3.0-amd64-th-th-preview` | Imagem de recipiente com o `th-TH` local. |
| `2.3.0-amd64-tr-tr-preview` | Imagem de recipiente com o `tr-TR` local. |
| `2.3.0-amd64-zh-cn-preview` | Imagem de recipiente com o `zh-CN` local. |
| `2.3.0-amd64-zh-hk-preview` | Imagem de recipiente com o `zh-HK` local. |
| `2.3.0-amd64-zh-tw-preview` | Imagem de recipiente com o `zh-TW` local. |
| `2.2.0-amd64-ar-ae-preview` | Imagem de recipiente com o `ar-AE` local. |
| `2.2.0-amd64-ar-eg-preview` | Imagem de recipiente com o `ar-EG` local. |
| `2.2.0-amd64-ar-kw-preview` | Imagem de recipiente com o `ar-KW` local. |
| `2.2.0-amd64-ar-qa-preview` | Imagem de recipiente com o `ar-QA` local. |
| `2.2.0-amd64-ar-sa-preview` | Imagem de recipiente com o `ar-SA` local. |
| `2.2.0-amd64-ca-es-preview` | Imagem de recipiente com o `ca-ES` local. |
| `2.2.0-amd64-da-dk-preview` | Imagem de recipiente com o `da-DK` local. |
| `2.2.0-amd64-de-de-preview` | Imagem de recipiente com o `de-DE` local. |
| `2.2.0-amd64-en-au-preview` | Imagem de recipiente com o `en-AU` local. |
| `2.2.0-amd64-en-ca-preview` | Imagem de recipiente com o `en-CA` local. |
| `2.2.0-amd64-en-gb-preview` | Imagem de recipiente com o `en-GB` local. |
| `2.2.0-amd64-en-in-preview` | Imagem de recipiente com o `en-IN` local. |
| `2.2.0-amd64-en-nz-preview` | Imagem de recipiente com o `en-NZ` local. |
| `2.2.0-amd64-en-us-preview` | Imagem de recipiente com o `en-US` local. |
| `2.2.0-amd64-es-es-preview` | Imagem de recipiente com o `es-ES` local. |
| `2.2.0-amd64-es-mx-preview` | Imagem de recipiente com o `es-MX` local. |
| `2.2.0-amd64-fi-fi-preview` | Imagem de recipiente com o `fi-FI` local. |
| `2.2.0-amd64-fr-ca-preview` | Imagem de recipiente com o `fr-CA` local. |
| `2.2.0-amd64-fr-fr-preview` | Imagem de recipiente com o `fr-FR` local. |
| `2.2.0-amd64-gu-in-preview` | Imagem de recipiente com o `gu-IN` local. |
| `2.2.0-amd64-hi-in-preview` | Imagem de recipiente com o `hi-IN` local. |
| `2.2.0-amd64-it-it-preview` | Imagem de recipiente com o `it-IT` local. |
| `2.2.0-amd64-ja-jp-preview` | Imagem de recipiente com o `ja-JP` local. |
| `2.2.0-amd64-ko-kr-preview` | Imagem de recipiente com o `ko-KR` local. |
| `2.2.0-amd64-mr-in-preview` | Imagem de recipiente com o `mr-IN` local. |
| `2.2.0-amd64-nb-no-preview` | Imagem de recipiente com o `nb-NO` local. |
| `2.2.0-amd64-nl-nl-preview` | Imagem de recipiente com o `nl-NL` local. |
| `2.2.0-amd64-pl-pl-preview` | Imagem de recipiente com o `pl-PL` local. |
| `2.2.0-amd64-pt-br-preview` | Imagem de recipiente com o `pt-BR` local. |
| `2.2.0-amd64-pt-pt-preview` | Imagem de recipiente com o `pt-PT` local. |
| `2.2.0-amd64-ru-ru-preview` | Imagem de recipiente com o `ru-RU` local. |
| `2.2.0-amd64-sv-se-preview` | Imagem de recipiente com o `sv-SE` local. |
| `2.2.0-amd64-ta-in-preview` | Imagem de recipiente com o `ta-IN` local. |
| `2.2.0-amd64-te-in-preview` | Imagem de recipiente com o `te-IN` local. |
| `2.2.0-amd64-th-th-preview` | Imagem de recipiente com o `th-TH` local. |
| `2.2.0-amd64-tr-tr-preview` | Imagem de recipiente com o `tr-TR` local. |
| `2.2.0-amd64-zh-cn-preview` | Imagem de recipiente com o `zh-CN` local. |
| `2.2.0-amd64-zh-hk-preview` | Imagem de recipiente com o `zh-HK` local. |
| `2.2.0-amd64-zh-tw-preview` | Imagem de recipiente com o `zh-TW` local. |
| `2.1.1-amd64-en-us-preview` | Imagem de recipiente com o `en-US` local. |
| `2.1.1-amd64-ar-ae-preview` | Imagem de recipiente com o `ar-AE` local. |
| `2.1.1-amd64-ar-eg-preview` | Imagem de recipiente com o `ar-EG` local. |
| `2.1.1-amd64-ar-kw-preview` | Imagem de recipiente com o `ar-KW` local. |
| `2.1.1-amd64-ar-qa-preview` | Imagem de recipiente com o `ar-QA` local. |
| `2.1.1-amd64-ar-sa-preview` | Imagem de recipiente com o `ar-SA` local. |
| `2.1.1-amd64-ca-es-preview` | Imagem de recipiente com o `ca-ES` local. |
| `2.1.1-amd64-da-dk-preview` | Imagem de recipiente com o `da-DK` local. |
| `2.1.1-amd64-de-de-preview` | Imagem de recipiente com o `de-DE` local. |
| `2.1.1-amd64-en-au-preview` | Imagem de recipiente com o `en-AU` local. |
| `2.1.1-amd64-en-ca-preview` | Imagem de recipiente com o `en-CA` local. |
| `2.1.1-amd64-en-gb-preview` | Imagem de recipiente com o `en-GB` local. |
| `2.1.1-amd64-en-in-preview` | Imagem de recipiente com o `en-IN` local. |
| `2.1.1-amd64-en-nz-preview` | Imagem de recipiente com o `en-NZ` local. |
| `2.1.1-amd64-en-us-preview` | Imagem de recipiente com o `en-US` local. |
| `2.1.1-amd64-es-es-preview` | Imagem de recipiente com o `es-ES` local. |
| `2.1.1-amd64-es-mx-preview` | Imagem de recipiente com o `es-MX` local. |
| `2.1.1-amd64-fi-fi-preview` | Imagem de recipiente com o `fi-FI` local. |
| `2.1.1-amd64-fr-ca-preview` | Imagem de recipiente com o `fr-CA` local. |
| `2.1.1-amd64-fr-fr-preview` | Imagem de recipiente com o `fr-FR` local. |
| `2.1.1-amd64-gu-in-preview` | Imagem de recipiente com o `gu-IN` local. |
| `2.1.1-amd64-hi-in-preview` | Imagem de recipiente com o `hi-IN` local. |
| `2.1.1-amd64-it-it-preview` | Imagem de recipiente com o `it-IT` local. |
| `2.1.1-amd64-ja-jp-preview` | Imagem de recipiente com o `ja-JP` local. |
| `2.1.1-amd64-ko-kr-preview` | Imagem de recipiente com o `ko-KR` local. |
| `2.1.1-amd64-mr-in-preview` | Imagem de recipiente com o `mr-IN` local. |
| `2.1.1-amd64-nb-no-preview` | Imagem de recipiente com o `nb-NO` local. |
| `2.1.1-amd64-nl-nl-preview` | Imagem de recipiente com o `nl-NL` local. |
| `2.1.1-amd64-pl-pl-preview` | Imagem de recipiente com o `pl-PL` local. |
| `2.1.1-amd64-pt-br-preview` | Imagem de recipiente com o `pt-BR` local. |
| `2.1.1-amd64-pt-pt-preview` | Imagem de recipiente com o `pt-PT` local. |
| `2.1.1-amd64-ru-ru-preview` | Imagem de recipiente com o `ru-RU` local. |
| `2.1.1-amd64-sv-se-preview` | Imagem de recipiente com o `sv-SE` local. |
| `2.1.1-amd64-ta-in-preview` | Imagem de recipiente com o `ta-IN` local. |
| `2.1.1-amd64-te-in-preview` | Imagem de recipiente com o `te-IN` local. |
| `2.1.1-amd64-th-th-preview` | Imagem de recipiente com o `th-TH` local. |
| `2.1.1-amd64-tr-tr-preview` | Imagem de recipiente com o `tr-TR` local. |
| `2.1.1-amd64-zh-cn-preview` | Imagem de recipiente com o `zh-CN` local. |
| `2.1.1-amd64-zh-hk-preview` | Imagem de recipiente com o `zh-HK` local. |
| `2.1.1-amd64-zh-tw-preview` | Imagem de recipiente com o `zh-TW` local. |
| `2.1.0-amd64-ar-ae-preview` | Imagem de recipiente com o `ar-AE` local. |
| `2.1.0-amd64-ar-eg-preview` | Imagem de recipiente com o `ar-EG` local. |
| `2.1.0-amd64-ar-kw-preview` | Imagem de recipiente com o `ar-KW` local. |
| `2.1.0-amd64-ar-qa-preview` | Imagem de recipiente com o `ar-QA` local. |
| `2.1.0-amd64-ar-sa-preview` | Imagem de recipiente com o `ar-SA` local. |
| `2.1.0-amd64-ca-es-preview` | Imagem de recipiente com o `ca-ES` local. |
| `2.1.0-amd64-da-dk-preview` | Imagem de recipiente com o `da-DK` local. |
| `2.1.0-amd64-de-de-preview` | Imagem de recipiente com o `de-DE` local. |
| `2.1.0-amd64-en-au-preview` | Imagem de recipiente com o `en-AU` local. |
| `2.1.0-amd64-en-ca-preview` | Imagem de recipiente com o `en-CA` local. |
| `2.1.0-amd64-en-gb-preview` | Imagem de recipiente com o `en-GB` local. |
| `2.1.0-amd64-en-in-preview` | Imagem de recipiente com o `en-IN` local. |
| `2.1.0-amd64-en-nz-preview` | Imagem de recipiente com o `en-NZ` local. |
| `2.1.0-amd64-en-us-preview` | Imagem de recipiente com o `en-US` local. |
| `2.1.0-amd64-es-es-preview` | Imagem de recipiente com o `es-ES` local. |
| `2.1.0-amd64-es-mx-preview` | Imagem de recipiente com o `es-MX` local. |
| `2.1.0-amd64-fi-fi-preview` | Imagem de recipiente com o `fi-FI` local. |
| `2.1.0-amd64-fr-ca-preview` | Imagem de recipiente com o `fr-CA` local. |
| `2.1.0-amd64-fr-fr-preview` | Imagem de recipiente com o `fr-FR` local. |
| `2.1.0-amd64-gu-in-preview` | Imagem de recipiente com o `gu-IN` local. |
| `2.1.0-amd64-hi-in-preview` | Imagem de recipiente com o `hi-IN` local. |
| `2.1.0-amd64-it-it-preview` | Imagem de recipiente com o `it-IT` local. |
| `2.1.0-amd64-ja-jp-preview` | Imagem de recipiente com o `ja-JP` local. |
| `2.1.0-amd64-ko-kr-preview` | Imagem de recipiente com o `ko-KR` local. |
| `2.1.0-amd64-mr-in-preview` | Imagem de recipiente com o `mr-IN` local. |
| `2.1.0-amd64-nb-no-preview` | Imagem de recipiente com o `nb-NO` local. |
| `2.1.0-amd64-nl-nl-preview` | Imagem de recipiente com o `nl-NL` local. |
| `2.1.0-amd64-pl-pl-preview` | Imagem de recipiente com o `pl-PL` local. |
| `2.1.0-amd64-pt-br-preview` | Imagem de recipiente com o `pt-BR` local. |
| `2.1.0-amd64-pt-pt-preview` | Imagem de recipiente com o `pt-PT` local. |
| `2.1.0-amd64-ru-ru-preview` | Imagem de recipiente com o `ru-RU` local. |
| `2.1.0-amd64-sv-se-preview` | Imagem de recipiente com o `sv-SE` local. |
| `2.1.0-amd64-ta-in-preview` | Imagem de recipiente com o `ta-IN` local. |
| `2.1.0-amd64-te-in-preview` | Imagem de recipiente com o `te-IN` local. |
| `2.1.0-amd64-th-th-preview` | Imagem de recipiente com o `th-TH` local. |
| `2.1.0-amd64-tr-tr-preview` | Imagem de recipiente com o `tr-TR` local. |
| `2.1.0-amd64-zh-cn-preview` | Imagem de recipiente com o `zh-CN` local. |
| `2.1.0-amd64-zh-hk-preview` | Imagem de recipiente com o `zh-HK` local. |
| `2.1.0-amd64-zh-tw-preview` | Imagem de recipiente com o `zh-TW` local. |
| `2.0.3-amd64-ja-jp-preview` | Imagem de recipiente com o `ja-JP` local. |
| `2.0.2-amd64-ar-ae-preview` | Imagem de recipiente com o `ar-AE` local. |
| `2.0.2-amd64-ar-eg-preview` | Imagem de recipiente com o `ar-EG` local. |
| `2.0.2-amd64-ar-kw-preview` | Imagem de recipiente com o `ar-KW` local. |
| `2.0.2-amd64-ar-qa-preview` | Imagem de recipiente com o `ar-QA` local. |
| `2.0.2-amd64-ar-sa-preview` | Imagem de recipiente com o `ar-SA` local. |
| `2.0.2-amd64-ca-es-preview` | Imagem de recipiente com o `ca-ES` local. |
| `2.0.2-amd64-da-dk-preview` | Imagem de recipiente com o `da-DK` local. |
| `2.0.2-amd64-de-de-preview` | Imagem de recipiente com o `de-DE` local. |
| `2.0.2-amd64-en-au-preview` | Imagem de recipiente com o `en-AU` local. |
| `2.0.2-amd64-en-ca-preview` | Imagem de recipiente com o `en-CA` local. |
| `2.0.2-amd64-en-gb-preview` | Imagem de recipiente com o `en-GB` local. |
| `2.0.2-amd64-en-in-preview` | Imagem de recipiente com o `en-IN` local. |
| `2.0.2-amd64-en-nz-preview` | Imagem de recipiente com o `en-NZ` local. |
| `2.0.2-amd64-en-us-preview` | Imagem de recipiente com o `en-US` local. |
| `2.0.2-amd64-es-es-preview` | Imagem de recipiente com o `es-ES` local. |
| `2.0.2-amd64-es-mx-preview` | Imagem de recipiente com o `es-MX` local. |
| `2.0.2-amd64-fi-fi-preview` | Imagem de recipiente com o `fi-FI` local. |
| `2.0.2-amd64-fr-ca-preview` | Imagem de recipiente com o `fr-CA` local. |
| `2.0.2-amd64-fr-fr-preview` | Imagem de recipiente com o `fr-FR` local. |
| `2.0.2-amd64-gu-in-preview` | Imagem de recipiente com o `gu-IN` local. |
| `2.0.2-amd64-hi-in-preview` | Imagem de recipiente com o `hi-IN` local. |
| `2.0.2-amd64-it-it-preview` | Imagem de recipiente com o `it-IT` local. |
| `2.0.2-amd64-ja-jp-preview` | Imagem de recipiente com o `ja-JP` local. |
| `2.0.2-amd64-ko-kr-preview` | Imagem de recipiente com o `ko-KR` local. |
| `2.0.2-amd64-mr-in-preview` | Imagem de recipiente com o `mr-IN` local. |
| `2.0.2-amd64-nb-no-preview` | Imagem de recipiente com o `nb-NO` local. |
| `2.0.2-amd64-nl-nl-preview` | Imagem de recipiente com o `nl-NL` local. |
| `2.0.2-amd64-pl-pl-preview` | Imagem de recipiente com o `pl-PL` local. |
| `2.0.2-amd64-pt-br-preview` | Imagem de recipiente com o `pt-BR` local. |
| `2.0.2-amd64-pt-pt-preview` | Imagem de recipiente com o `pt-PT` local. |
| `2.0.2-amd64-ru-ru-preview` | Imagem de recipiente com o `ru-RU` local. |
| `2.0.2-amd64-sv-se-preview` | Imagem de recipiente com o `sv-SE` local. |
| `2.0.2-amd64-ta-in-preview` | Imagem de recipiente com o `ta-IN` local. |
| `2.0.2-amd64-te-in-preview` | Imagem de recipiente com o `te-IN` local. |
| `2.0.2-amd64-th-th-preview` | Imagem de recipiente com o `th-TH` local. |
| `2.0.2-amd64-tr-tr-preview` | Imagem de recipiente com o `tr-TR` local. |
| `2.0.2-amd64-zh-cn-preview` | Imagem de recipiente com o `zh-CN` local. |
| `2.0.2-amd64-zh-hk-preview` | Imagem de recipiente com o `zh-HK` local. |
| `2.0.2-amd64-zh-tw-preview` | Imagem de recipiente com o `zh-TW` local. |
| `2.0.1-amd64-ar-ae-preview` | Imagem de recipiente com o `ar-AE` local. |
| `2.0.1-amd64-ar-eg-preview` | Imagem de recipiente com o `ar-EG` local. |
| `2.0.1-amd64-ar-kw-preview` | Imagem de recipiente com o `ar-KW` local. |
| `2.0.1-amd64-ar-qa-preview` | Imagem de recipiente com o `ar-QA` local. |
| `2.0.1-amd64-ar-sa-preview` | Imagem de recipiente com o `ar-SA` local. |
| `2.0.1-amd64-ca-es-preview` | Imagem de recipiente com o `ca-ES` local. |
| `2.0.1-amd64-da-dk-preview` | Imagem de recipiente com o `da-DK` local. |
| `2.0.1-amd64-de-de-preview` | Imagem de recipiente com o `de-DE` local. |
| `2.0.1-amd64-en-au-preview` | Imagem de recipiente com o `en-AU` local. |
| `2.0.1-amd64-en-ca-preview` | Imagem de recipiente com o `en-CA` local. |
| `2.0.1-amd64-en-gb-preview` | Imagem de recipiente com o `en-GB` local. |
| `2.0.1-amd64-en-in-preview` | Imagem de recipiente com o `en-IN` local. |
| `2.0.1-amd64-en-nz-preview` | Imagem de recipiente com o `en-NZ` local. |
| `2.0.1-amd64-en-us-preview` | Imagem de recipiente com o `en-US` local. |
| `2.0.1-amd64-es-es-preview` | Imagem de recipiente com o `es-ES` local. |
| `2.0.1-amd64-es-mx-preview` | Imagem de recipiente com o `es-MX` local. |
| `2.0.1-amd64-fi-fi-preview` | Imagem de recipiente com o `fi-FI` local. |
| `2.0.1-amd64-fr-ca-preview` | Imagem de recipiente com o `fr-CA` local. |
| `2.0.1-amd64-fr-fr-preview` | Imagem de recipiente com o `fr-FR` local. |
| `2.0.1-amd64-gu-in-preview` | Imagem de recipiente com o `gu-IN` local. |
| `2.0.1-amd64-hi-in-preview` | Imagem de recipiente com o `hi-IN` local. |
| `2.0.1-amd64-it-it-preview` | Imagem de recipiente com o `it-IT` local. |
| `2.0.1-amd64-ja-jp-preview` | Imagem de recipiente com o `ja-JP` local. |
| `2.0.1-amd64-ko-kr-preview` | Imagem de recipiente com o `ko-KR` local. |
| `2.0.1-amd64-mr-in-preview` | Imagem de recipiente com o `mr-IN` local. |
| `2.0.1-amd64-nb-no-preview` | Imagem de recipiente com o `nb-NO` local. |
| `2.0.1-amd64-nl-nl-preview` | Imagem de recipiente com o `nl-NL` local. |
| `2.0.1-amd64-pl-pl-preview` | Imagem de recipiente com o `pl-PL` local. |
| `2.0.1-amd64-pt-br-preview` | Imagem de recipiente com o `pt-BR` local. |
| `2.0.1-amd64-pt-pt-preview` | Imagem de recipiente com o `pt-PT` local. |
| `2.0.1-amd64-ru-ru-preview` | Imagem de recipiente com o `ru-RU` local. |
| `2.0.1-amd64-sv-se-preview` | Imagem de recipiente com o `sv-SE` local. |
| `2.0.1-amd64-ta-in-preview` | Imagem de recipiente com o `ta-IN` local. |
| `2.0.1-amd64-te-in-preview` | Imagem de recipiente com o `te-IN` local. |
| `2.0.1-amd64-th-th-preview` | Imagem de recipiente com o `th-TH` local. |
| `2.0.1-amd64-tr-tr-preview` | Imagem de recipiente com o `tr-TR` local. |
| `2.0.1-amd64-zh-cn-preview` | Imagem de recipiente com o `zh-CN` local. |
| `2.0.1-amd64-zh-hk-preview` | Imagem de recipiente com o `zh-HK` local. |
| `2.0.1-amd64-zh-tw-preview` | Imagem de recipiente com o `zh-TW` local. |
| `2.0.0-amd64-ar-eg-preview` | Imagem de recipiente com o `ar-EG` local. |
| `2.0.0-amd64-ca-es-preview` | Imagem de recipiente com o `ca-ES` local. |
| `2.0.0-amd64-da-dk-preview` | Imagem de recipiente com o `da-DK` local. |
| `2.0.0-amd64-de-de-preview` | Imagem de recipiente com o `de-DE` local. |
| `2.0.0-amd64-en-au-preview` | Imagem de recipiente com o `en-AU` local. |
| `2.0.0-amd64-en-ca-preview` | Imagem de recipiente com o `en-CA` local. |
| `2.0.0-amd64-en-gb-preview` | Imagem de recipiente com o `en-GB` local. |
| `2.0.0-amd64-en-in-preview` | Imagem de recipiente com o `en-IN` local. |
| `2.0.0-amd64-en-nz-preview` | Imagem de recipiente com o `en-NZ` local. |
| `2.0.0-amd64-en-us-preview` | Imagem de recipiente com o `en-US` local. |
| `2.0.0-amd64-es-es-preview` | Imagem de recipiente com o `es-ES` local. |
| `2.0.0-amd64-es-mx-preview` | Imagem de recipiente com o `es-MX` local. |
| `2.0.0-amd64-fi-fi-preview` | Imagem de recipiente com o `fi-FI` local. |
| `2.0.0-amd64-fr-ca-preview` | Imagem de recipiente com o `fr-CA` local. |
| `2.0.0-amd64-fr-fr-preview` | Imagem de recipiente com o `fr-FR` local. |
| `2.0.0-amd64-hi-in-preview` | Imagem de recipiente com o `hi-IN` local. |
| `2.0.0-amd64-it-it-preview` | Imagem de recipiente com o `it-IT` local. |
| `2.0.0-amd64-ja-jp-preview` | Imagem de recipiente com o `ja-JP` local. |
| `2.0.0-amd64-ko-kr-preview` | Imagem de recipiente com o `ko-KR` local. |
| `2.0.0-amd64-nb-no-preview` | Imagem de recipiente com o `nb-NO` local. |
| `2.0.0-amd64-nl-nl-preview` | Imagem de recipiente com o `nl-NL` local. |
| `2.0.0-amd64-pl-pl-preview` | Imagem de recipiente com o `pl-PL` local. |
| `2.0.0-amd64-pt-br-preview` | Imagem de recipiente com o `pt-BR` local. |
| `2.0.0-amd64-pt-pt-preview` | Imagem de recipiente com o `pt-PT` local. |
| `2.0.0-amd64-ru-ru-preview` | Imagem de recipiente com o `ru-RU` local. |
| `2.0.0-amd64-sv-se-preview` | Imagem de recipiente com o `sv-SE` local. |
| `2.0.0-amd64-th-th-preview` | Imagem de recipiente com o `th-TH` local. |
| `2.0.0-amd64-tr-tr-preview` | Imagem de recipiente com o `tr-TR` local. |
| `2.0.0-amd64-zh-cn-preview` | Imagem de recipiente com o `zh-CN` local. |
| `2.0.0-amd64-zh-hk-preview` | Imagem de recipiente com o `zh-HK` local. |
| `2.0.0-amd64-zh-tw-preview` | Imagem de recipiente com o `zh-TW` local. |
| `1.2.0-amd64-de-de-preview` | Imagem de recipiente com o `de-DE` local. |
| `1.2.0-amd64-en-au-preview` | Imagem de recipiente com o `en-AU` local. |
| `1.2.0-amd64-en-ca-preview` | Imagem de recipiente com o `en-CA` local. |
| `1.2.0-amd64-en-gb-preview` | Imagem de recipiente com o `en-GB` local. |
| `1.2.0-amd64-en-in-preview` | Imagem de recipiente com o `en-IN` local. |
| `1.2.0-amd64-en-us-preview` | Imagem de recipiente com o `en-US` local. |
| `1.2.0-amd64-es-es-preview` | Imagem de recipiente com o `es-ES` local. |
| `1.2.0-amd64-es-mx-preview` | Imagem de recipiente com o `es-MX` local. |
| `1.2.0-amd64-fr-ca-preview` | Imagem de recipiente com o `fr-CA` local. |
| `1.2.0-amd64-fr-fr-preview` | Imagem de recipiente com o `fr-FR` local. |
| `1.2.0-amd64-it-it-preview` | Imagem de recipiente com o `it-IT` local. |
| `1.2.0-amd64-ja-jp-preview` | Imagem de recipiente com o `ja-JP` local. |
| `1.2.0-amd64-pt-br-preview` | Imagem de recipiente com o `pt-BR` local. |
| `1.2.0-amd64-zh-cn-preview` | Imagem de recipiente com o `zh-CN` local. |
| `1.1.3-amd64-de-de-preview` | Imagem de recipiente com o `de-DE` local. |
| `1.1.3-amd64-en-au-preview` | Imagem de recipiente com o `en-AU` local. |
| `1.1.3-amd64-en-ca-preview` | Imagem de recipiente com o `en-CA` local. |
| `1.1.3-amd64-en-gb-preview` | Imagem de recipiente com o `en-GB` local. |
| `1.1.3-amd64-en-in-preview` | Imagem de recipiente com o `en-IN` local. |
| `1.1.3-amd64-en-us-preview` | Imagem de recipiente com o `en-US` local. |
| `1.1.3-amd64-es-es-preview` | Imagem de recipiente com o `es-ES` local. |
| `1.1.3-amd64-es-mx-preview` | Imagem de recipiente com o `es-MX` local. |
| `1.1.3-amd64-fr-ca-preview` | Imagem de recipiente com o `fr-CA` local. |
| `1.1.3-amd64-fr-fr-preview` | Imagem de recipiente com o `fr-FR` local. |
| `1.1.3-amd64-it-it-preview` | Imagem de recipiente com o `it-IT` local. |
| `1.1.3-amd64-ja-jp-preview` | Imagem de recipiente com o `ja-JP` local. |
| `1.1.3-amd64-pt-br-preview` | Imagem de recipiente com o `pt-BR` local. |
| `1.1.3-amd64-zh-cn-preview` | Imagem de recipiente com o `zh-CN` local. |
| `1.1.2-amd64-de-de-preview` | Imagem de recipiente com o `de-DE` local. |
| `1.1.2-amd64-en-au-preview` | Imagem de recipiente com o `en-AU` local. |
| `1.1.2-amd64-en-ca-preview` | Imagem de recipiente com o `en-CA` local. |
| `1.1.2-amd64-en-gb-preview` | Imagem de recipiente com o `en-GB` local. |
| `1.1.2-amd64-en-in-preview` | Imagem de recipiente com o `en-IN` local. |
| `1.1.2-amd64-en-us-preview` | Imagem de recipiente com o `en-US` local. |
| `1.1.2-amd64-es-es-preview` | Imagem de recipiente com o `es-ES` local. |
| `1.1.2-amd64-es-mx-preview` | Imagem de recipiente com o `es-MX` local. |
| `1.1.2-amd64-fr-ca-preview` | Imagem de recipiente com o `fr-CA` local. |
| `1.1.2-amd64-fr-fr-preview` | Imagem de recipiente com o `fr-FR` local. |
| `1.1.2-amd64-it-it-preview` | Imagem de recipiente com o `it-IT` local. |
| `1.1.2-amd64-ja-jp-preview` | Imagem de recipiente com o `ja-JP` local. |
| `1.1.2-amd64-pt-br-preview` | Imagem de recipiente com o `pt-BR` local. |
| `1.1.2-amd64-zh-cn-preview` | Imagem de recipiente com o `zh-CN` local. |
| `1.1.1-amd64-de-de-preview` | Imagem de recipiente com o `de-DE` local. |
| `1.1.1-amd64-en-au-preview` | Imagem de recipiente com o `en-AU` local. |
| `1.1.1-amd64-en-ca-preview` | Imagem de recipiente com o `en-CA` local. |
| `1.1.1-amd64-en-gb-preview` | Imagem de recipiente com o `en-GB` local. |
| `1.1.1-amd64-en-in-preview` | Imagem de recipiente com o `en-IN` local. |
| `1.1.1-amd64-en-us-preview` | Imagem de recipiente com o `en-US` local. |
| `1.1.1-amd64-es-es-preview` | Imagem de recipiente com o `es-ES` local. |
| `1.1.1-amd64-es-mx-preview` | Imagem de recipiente com o `es-MX` local. |
| `1.1.1-amd64-fr-ca-preview` | Imagem de recipiente com o `fr-CA` local. |
| `1.1.1-amd64-fr-fr-preview` | Imagem de recipiente com o `fr-FR` local. |
| `1.1.1-amd64-it-it-preview` | Imagem de recipiente com o `it-IT` local. |
| `1.1.1-amd64-ja-jp-preview` | Imagem de recipiente com o `ja-JP` local. |
| `1.1.1-amd64-pt-br-preview` | Imagem de recipiente com o `pt-BR` local. |
| `1.1.1-amd64-zh-cn-preview` | Imagem de recipiente com o `zh-CN` local. |
| `1.1.0-amd64-de-de-preview` | Imagem de recipiente com o `de-DE` local. |
| `1.1.0-amd64-en-au-preview` | Imagem de recipiente com o `en-AU` local. |
| `1.1.0-amd64-en-ca-preview` | Imagem de recipiente com o `en-CA` local. |
| `1.1.0-amd64-en-gb-preview` | Imagem de recipiente com o `en-GB` local. |
| `1.1.0-amd64-en-in-preview` | Imagem de recipiente com o `en-IN` local. |
| `1.1.0-amd64-en-us-preview` | Imagem de recipiente com o `en-US` local. |
| `1.1.0-amd64-es-es-preview` | Imagem de recipiente com o `es-ES` local. |
| `1.1.0-amd64-es-mx-preview` | Imagem de recipiente com o `es-MX` local. |
| `1.1.0-amd64-fr-ca-preview` | Imagem de recipiente com o `fr-CA` local. |
| `1.1.0-amd64-fr-fr-preview` | Imagem de recipiente com o `fr-FR` local. |
| `1.1.0-amd64-it-it-preview` | Imagem de recipiente com o `it-IT` local. |
| `1.1.0-amd64-ja-jp-preview` | Imagem de recipiente com o `ja-JP` local. |
| `1.1.0-amd64-pt-br-preview` | Imagem de recipiente com o `pt-BR` local. |
| `1.1.0-amd64-zh-cn-preview` | Imagem de recipiente com o `zh-CN` local. |
| `1.0.0-amd64-de-de-preview` | Imagem de recipiente com o `de-DE` local. |
| `1.0.0-amd64-en-au-preview` | Imagem de recipiente com o `en-AU` local. |
| `1.0.0-amd64-en-ca-preview` | Imagem de recipiente com o `en-CA` local. |
| `1.0.0-amd64-en-gb-preview` | Imagem de recipiente com o `en-GB` local. |
| `1.0.0-amd64-en-in-preview` | Imagem de recipiente com o `en-IN` local. |
| `1.0.0-amd64-en-us-preview` | Imagem de recipiente com o `en-US` local. |
| `1.0.0-amd64-es-es-preview` | Imagem de recipiente com o `es-ES` local. |
| `1.0.0-amd64-es-mx-preview` | Imagem de recipiente com o `es-MX` local. |
| `1.0.0-amd64-fr-ca-preview` | Imagem de recipiente com o `fr-CA` local. |
| `1.0.0-amd64-fr-fr-preview` | Imagem de recipiente com o `fr-FR` local. |
| `1.0.0-amd64-it-it-preview` | Imagem de recipiente com o `it-IT` local. |
| `1.0.0-amd64-ja-jp-preview` | Imagem de recipiente com o `ja-JP` local. |
| `1.0.0-amd64-pt-br-preview` | Imagem de recipiente com o `pt-BR` local. |
| `1.0.0-amd64-zh-cn-preview` | Imagem de recipiente com o `zh-CN` local. |

## <a name="text-to-speech"></a>Conversão de texto em voz

A imagem do recipiente [text-to-speech][sp-tts] pode ser encontrada no registo do `containerpreview.azurecr.io` contentor. Reside dentro do `microsoft` repositório e é `cognitive-services-text-to-speech` nomeado. O nome de imagem do recipiente totalmente qualificado é, `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` .

Esta imagem do recipiente tem as seguintes etiquetas disponíveis:

| Etiquetas de imagem                                  | Notas                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Imagem de recipiente com o `en-US` local e `en-US-AriaRUS` a voz.         |
| `1.7.0-amd64-ar-eg-hoda`                    | Imagem de recipiente com o `ar-EG` local e `ar-EG-Hoda` a voz.            |
| `1.7.0-amd64-ar-sa-naayf`                   | Imagem de recipiente com o `ar-SA` local e `ar-SA-Naayf` a voz.           |
| `1.7.0-amd64-bg-bg-ivan`                    | Imagem de recipiente com o `bg-BG` local e `bg-BG-Ivan` a voz.            |
| `1.7.0-amd64-ca-es-herenarus`               | Imagem de recipiente com o `ca-ES` local e `ca-ES-HerenaRUS` a voz.       |
| `1.7.0-amd64-cs-cz-jakub`                   | Imagem de recipiente com o `cs-CZ` local e `cs-CZ-Jakub` a voz.           |
| `1.7.0-amd64-da-dk-hellerus`                | Imagem de recipiente com o `da-DK` local e `da-DK-HelleRUS` a voz.        |
| `1.7.0-amd64-de-at-michael`                 | Imagem de recipiente com o `de-AT` local e `de-AT-Michael` a voz.         |
| `1.7.0-amd64-de-ch-karsten`                 | Imagem de recipiente com o `de-CH` local e `de-CH-Karsten` a voz.         |
| `1.7.0-amd64-de-de-hedda`                   | Imagem de recipiente com o `de-DE` local e `de-DE-Hedda` a voz.           |
| `1.7.0-amd64-de-de-heddarus`                | Imagem de recipiente com o `de-DE` local e `de-DE-Hedda` a voz.           |
| `1.7.0-amd64-de-de-stefan-apollo`           | Imagem de recipiente com o `de-DE` local e `de-DE-Stefan-Apollo` a voz.   |
| `1.7.0-amd64-el-gr-stefanos`                | Imagem de recipiente com o `el-GR` local e `el-GR-Stefanos` a voz.        |
| `1.7.0-amd64-en-au-catherine`               | Imagem de recipiente com o `en-AU` local e `en-AU-Catherine` a voz.       |
| `1.7.0-amd64-en-au-hayleyrus`               | Imagem de recipiente com o `en-AU` local e `en-AU-HayleyRUS` a voz.       |
| `1.7.0-amd64-en-ca-heatherrus`              | Imagem de recipiente com o `en-CA` local e `en-CA-HeatherRUS` a voz.      |
| `1.7.0-amd64-en-ca-linda`                   | Imagem de recipiente com o `en-CA` local e `en-CA-Linda` a voz.           |
| `1.7.0-amd64-en-gb-george-apollo`           | Imagem de recipiente com o `en-GB` local e `en-GB-George-Apollo` a voz.   |
| `1.7.0-amd64-en-gb-hazelrus`                | Imagem de recipiente com o `en-GB` local e `en-GB-HazelRUS` a voz.        |
| `1.7.0-amd64-en-gb-susan-apollo`            | Imagem de recipiente com o `en-GB` local e `en-GB-Susan-Apollo` a voz.    |
| `1.7.0-amd64-en-ie-sean`                    | Imagem de recipiente com o `en-IE` local e `en-IE-Sean` a voz.            |
| `1.7.0-amd64-en-in-heera-apollo`            | Imagem de recipiente com o `en-IN` local e `en-IN-Heera-Apollo` a voz.    |
| `1.7.0-amd64-en-in-priyarus`                | Imagem de recipiente com o `en-IN` local e `en-IN-PriyaRUS` a voz.        |
| `1.7.0-amd64-en-in-ravi-apollo`             | Imagem de recipiente com o `en-IN` local e `en-IN-Ravi-Apollo` a voz.     |
| `1.7.0-amd64-en-us-benjaminrus`             | Imagem de recipiente com o `en-US` local e `en-US-BenjaminRUS` a voz.     |
| `1.7.0-amd64-en-us-guy24krus`               | Imagem de recipiente com o `en-US` local e `en-US-Guy24kRUS` a voz.       |
| `1.7.0-amd64-en-us-aria24krus`              | Imagem de recipiente com o `en-US` local e `en-US-Aria24kRUS` a voz.      |
| `1.7.0-amd64-en-us-ariarus`                 | Imagem de recipiente com o `en-US` local e `en-US-AriaRUS` a voz.         |
| `1.7.0-amd64-en-us-zirarus`                 | Imagem de recipiente com o `en-US` local e `en-US-ZiraRUS` a voz.         |
| `1.7.0-amd64-es-es-helenarus`               | Imagem de recipiente com o `es-ES` local e `es-ES-HelenaRUS` a voz.       |
| `1.7.0-amd64-es-es-laura-apollo`            | Imagem de recipiente com o `es-ES` local e `es-ES-Laura-Apollo` a voz.    |
| `1.7.0-amd64-es-es-pablo-apollo`            | Imagem de recipiente com o `es-ES` local e `es-ES-Pablo-Apollo` a voz.    |
| `1.7.0-amd64-es-mx-hildarus`                | Imagem de recipiente com o `es-MX` local e `es-MX-HildaRUS` a voz.        |
| `1.7.0-amd64-es-mx-raul-apollo`             | Imagem de recipiente com o `es-MX` local e `es-MX-Raul-Apollo` a voz.     |
| `1.7.0-amd64-fi-fi-heidirus`                | Imagem de recipiente com o `fi-FI` local e `fi-FI-HeidiRUS` a voz.        |
| `1.7.0-amd64-fr-ca-caroline`                | Imagem de recipiente com o `fr-CA` local e `fr-CA-Caroline` a voz.        |
| `1.7.0-amd64-fr-ca-harmonierus`             | Imagem de recipiente com o `fr-CA` local e `fr-CA-HarmonieRUS` a voz.     |
| `1.7.0-amd64-fr-ch-guillaume`               | Imagem de recipiente com o `fr-CH` local e `fr-CH-Guillaume` a voz.       |
| `1.7.0-amd64-fr-fr-hortenserus`             | Imagem de recipiente com o `fr-FR` local e `fr-FR-HortenseRUS` a voz.     |
| `1.7.0-amd64-fr-fr-julie-apollo`            | Imagem de recipiente com o `fr-FR` local e `fr-FR-Julie-Apollo` a voz.    |
| `1.7.0-amd64-fr-fr-paul-apollo`             | Imagem de recipiente com o `fr-FR` local e `fr-FR-Paul-Apollo` a voz.     |
| `1.7.0-amd64-he-il-asaf`                    | Imagem de recipiente com o `he-IL` local e `he-IL-Asaf` a voz.            |
| `1.7.0-amd64-hi-in-hemant`                  | Imagem de recipiente com o `hi-IN` local e `hi-IN-Hemant` a voz.          |
| `1.7.0-amd64-hi-in-kalpana-apollo`          | Imagem de recipiente com o `hi-IN` local e `hi-IN-Kalpana-Apollo` a voz.  |
| `1.7.0-amd64-hi-in-kalpana`                 | Imagem de recipiente com o `hi-IN` local e `hi-IN-Kalpana` a voz.         |
| `1.7.0-amd64-hr-hr-matej`                   | Imagem de recipiente com o `hr-HR` local e `hr-HR-Matej` a voz.           |
| `1.7.0-amd64-hu-hu-szabolcs`                | Imagem de recipiente com o `hu-HU` local e `hu-HU-Szabolcs` a voz.        |
| `1.7.0-amd64-id-id-andika`                  | Imagem de recipiente com o `id-ID` local e `id-ID-Andika` a voz.          |
| `1.7.0-amd64-it-it-cosimo-apollo`           | Imagem de recipiente com o `it-IT` local e `it-IT-Cosimo-Apollo` a voz.   |
| `1.7.0-amd64-it-it-luciarus`                | Imagem de recipiente com o `it-IT` local e `it-IT-LuciaRUS` a voz.        |
| `1.7.0-amd64-ja-jp-ayumi-apollo`            | Imagem de recipiente com o `ja-JP` local e `ja-JP-Ayumi-Apollo` a voz.    |
| `1.7.0-amd64-ja-jp-harukarus`               | Imagem de recipiente com o `ja-JP` local e `ja-JP-HarukaRUS` a voz.       |
| `1.7.0-amd64-ja-jp-ichiro-apollo`           | Imagem de recipiente com o `ja-JP` local e `ja-JP-Ichiro-Apollo` a voz.   |
| `1.7.0-amd64-ko-kr-heamirus`                | Imagem de recipiente com o `ko-KR` local e `ko-KR-HeamiRUS` a voz.        |
| `1.7.0-amd64-ms-my-rizwan`                  | Imagem de recipiente com o `ms-MY` local e `ms-MY-Rizwan` a voz.          |
| `1.7.0-amd64-nb-no-huldarus`                | Imagem de recipiente com o `nb-NO` local e `nb-NO-HuldaRUS` a voz.        |
| `1.7.0-amd64-nl-nl-hannarus`                | Imagem de recipiente com o `nl-NL` local e `nl-NL-HannaRUS` a voz.        |
| `1.7.0-amd64-pl-pl-paulinarus`              | Imagem de recipiente com o `pl-PL` local e `pl-PL-PaulinaRUS` a voz.      |
| `1.7.0-amd64-pt-br-daniel-apollo`           | Imagem de recipiente com o `pt-BR` local e `pt-BR-Daniel-Apollo` a voz.   |
| `1.7.0-amd64-pt-br-heloisarus`              | Imagem de recipiente com o `pt-BR` local e `pt-BR-HeloisaRUS` a voz.      |
| `1.7.0-amd64-pt-pt-heliarus`                | Imagem de recipiente com o `pt-PT` local e `pt-PT-HeliaRUS` a voz.        |
| `1.7.0-amd64-ro-ro-andrei`                  | Imagem de recipiente com o `ro-RO` local e `ro-RO-Andrei` a voz.          |
| `1.7.0-amd64-ru-ru-ekaterinarus`            | Imagem de recipiente com o `ru-RU` local e `ru-RU-EkaterinaRUS` a voz.    |
| `1.7.0-amd64-ru-ru-irina-apollo`            | Imagem de recipiente com o `ru-RU` local e `ru-RU-Irina-Apollo` a voz.    |
| `1.7.0-amd64-ru-ru-pavel-apollo`            | Imagem de recipiente com o `ru-RU` local e `ru-RU-Pavel-Apollo` a voz.    |
| `1.7.0-amd64-sk-sk-filip`                   | Imagem de recipiente com o `sk-SK` local e `sk-SK-Filip` a voz.           |
| `1.7.0-amd64-sl-si-lado`                    | Imagem de recipiente com o `sl-SI` local e `sl-SI-Lado` a voz.            |
| `1.7.0-amd64-sv-se-hedvigrus`               | Imagem de recipiente com o `sv-SE` local e `sv-SE-HedvigRUS` a voz.       |
| `1.7.0-amd64-ta-in-valluvar`                | Imagem de recipiente com o `ta-IN` local e `ta-IN-Valluvar` a voz.        |
| `1.7.0-amd64-te-in-chitra`                  | Imagem de recipiente com o `te-IN` local e `te-IN-Chitra` a voz.          |
| `1.7.0-amd64-th-th-pattara`                 | Imagem de recipiente com o `th-TH` local e `th-TH-Pattara` a voz.         |
| `1.7.0-amd64-tr-tr-sedarus`                 | Imagem de recipiente com o `tr-TR` local e `tr-TR-SedaRUS` a voz.         |
| `1.7.0-amd64-vi-vn-an`                      | Imagem de recipiente com o `vi-VN` local e `vi-VN-An` a voz.              |
| `1.7.0-amd64-zh-cn-huihuirus`               | Imagem de recipiente com o `zh-CN` local e `zh-CN-HuihuiRUS` a voz.       |
| `1.7.0-amd64-zh-cn-kangkang-apollo`         | Imagem de recipiente com o `zh-CN` local e `zh-CN-Kangkang-Apollo` a voz. |
| `1.7.0-amd64-zh-cn-yaoyao-apollo`           | Imagem de recipiente com o `zh-CN` local e `zh-CN-Yaoyao-Apollo` a voz.   |
| `1.7.0-amd64-zh-hk-danny-apollo`            | Imagem de recipiente com o `zh-HK` local e `zh-HK-Danny-Apollo` a voz.    |
| `1.7.0-amd64-zh-hk-tracy-apollo`            | Imagem de recipiente com o `zh-HK` local e `zh-HK-Tracy-Apollo` a voz.    |
| `1.7.0-amd64-zh-hk-tracyrus`                | Imagem de recipiente com o `zh-HK` local e `zh-HK-TracyRUS` a voz.        |
| `1.7.0-amd64-zh-tw-hanhanrus`               | Imagem de recipiente com o `zh-TW` local e `zh-TW-HanHanRUS` a voz.       |
| `1.7.0-amd64-zh-tw-yating-apollo`           | Imagem de recipiente com o `zh-TW` local e `zh-TW-Yating-Apollo` a voz.   |
| `1.7.0-amd64-zh-tw-zhiwei-apollo`           | Imagem de recipiente com o `zh-TW` local e `zh-TW-Zhiwei-Apollo` a voz.   |
| `1.6.0-amd64-ar-eg-hoda-preview`            | Imagem de recipiente com o `ar-EG` local e `ar-EG-Hoda` a voz.            |
| `1.6.0-amd64-ar-sa-naayf-preview`           | Imagem de recipiente com o `ar-SA` local e `ar-SA-Naayf` a voz.           |
| `1.6.0-amd64-bg-bg-ivan-preview`            | Imagem de recipiente com o `bg-BG` local e `bg-BG-Ivan` a voz.            |
| `1.6.0-amd64-ca-es-herenarus-preview`       | Imagem de recipiente com o `ca-ES` local e `ca-ES-HerenaRUS` a voz.       |
| `1.6.0-amd64-cs-cz-jakub-preview`           | Imagem de recipiente com o `cs-CZ` local e `cs-CZ-Jakub` a voz.           |
| `1.6.0-amd64-da-dk-hellerus-preview`        | Imagem de recipiente com o `da-DK` local e `da-DK-HelleRUS` a voz.        |
| `1.6.0-amd64-de-at-michael-preview`         | Imagem de recipiente com o `de-AT` local e `de-AT-Michael` a voz.         |
| `1.6.0-amd64-de-ch-karsten-preview`         | Imagem de recipiente com o `de-CH` local e `de-CH-Karsten` a voz.         |
| `1.6.0-amd64-de-de-hedda-preview`           | Imagem de recipiente com o `de-DE` local e `de-DE-Hedda` a voz.           |
| `1.6.0-amd64-de-de-heddarus-preview`        | Imagem de recipiente com o `de-DE` local e `de-DE-Hedda` a voz.           |
| `1.6.0-amd64-de-de-stefan-apollo-preview`   | Imagem de recipiente com o `de-DE` local e `de-DE-Stefan-Apollo` a voz.   |
| `1.6.0-amd64-el-gr-stefanos-preview`        | Imagem de recipiente com o `el-GR` local e `el-GR-Stefanos` a voz.        |
| `1.6.0-amd64-en-au-catherine-preview`       | Imagem de recipiente com o `en-AU` local e `en-AU-Catherine` a voz.       |
| `1.6.0-amd64-en-au-hayleyrus-preview`       | Imagem de recipiente com o `en-AU` local e `en-AU-HayleyRUS` a voz.       |
| `1.6.0-amd64-en-ca-heatherrus-preview`      | Imagem de recipiente com o `en-CA` local e `en-CA-HeatherRUS` a voz.      |
| `1.6.0-amd64-en-ca-linda-preview`           | Imagem de recipiente com o `en-CA` local e `en-CA-Linda` a voz.           |
| `1.6.0-amd64-en-gb-george-apollo-preview`   | Imagem de recipiente com o `en-GB` local e `en-GB-George-Apollo` a voz.   |
| `1.6.0-amd64-en-gb-hazelrus-preview`        | Imagem de recipiente com o `en-GB` local e `en-GB-HazelRUS` a voz.        |
| `1.6.0-amd64-en-gb-susan-apollo-preview`    | Imagem de recipiente com o `en-GB` local e `en-GB-Susan-Apollo` a voz.    |
| `1.6.0-amd64-en-ie-sean-preview`            | Imagem de recipiente com o `en-IE` local e `en-IE-Sean` a voz.            |
| `1.6.0-amd64-en-in-heera-apollo-preview`    | Imagem de recipiente com o `en-IN` local e `en-IN-Heera-Apollo` a voz.    |
| `1.6.0-amd64-en-in-priyarus-preview`        | Imagem de recipiente com o `en-IN` local e `en-IN-PriyaRUS` a voz.        |
| `1.6.0-amd64-en-in-ravi-apollo-preview`     | Imagem de recipiente com o `en-IN` local e `en-IN-Ravi-Apollo` a voz.     |
| `1.6.0-amd64-en-us-benjaminrus-preview`     | Imagem de recipiente com o `en-US` local e `en-US-BenjaminRUS` a voz.     |
| `1.6.0-amd64-en-us-guy24krus-preview`       | Imagem de recipiente com o `en-US` local e `en-US-Guy24kRUS` a voz.       |
| `1.6.0-amd64-en-us-aria24krus-preview`      | Imagem de recipiente com o `en-US` local e `en-US-Aria24kRUS` a voz.      |
| `1.6.0-amd64-en-us-ariarus-preview`         | Imagem de recipiente com o `en-US` local e `en-US-AriaRUS` a voz.         |
| `1.6.0-amd64-en-us-zirarus-preview`         | Imagem de recipiente com o `en-US` local e `en-US-ZiraRUS` a voz.         |
| `1.6.0-amd64-es-es-helenarus-preview`       | Imagem de recipiente com o `es-ES` local e `es-ES-HelenaRUS` a voz.       |
| `1.6.0-amd64-es-es-laura-apollo-preview`    | Imagem de recipiente com o `es-ES` local e `es-ES-Laura-Apollo` a voz.    |
| `1.6.0-amd64-es-es-pablo-apollo-preview`    | Imagem de recipiente com o `es-ES` local e `es-ES-Pablo-Apollo` a voz.    |
| `1.6.0-amd64-es-mx-hildarus-preview`        | Imagem de recipiente com o `es-MX` local e `es-MX-HildaRUS` a voz.        |
| `1.6.0-amd64-es-mx-raul-apollo-preview`     | Imagem de recipiente com o `es-MX` local e `es-MX-Raul-Apollo` a voz.     |
| `1.6.0-amd64-fi-fi-heidirus-preview`        | Imagem de recipiente com o `fi-FI` local e `fi-FI-HeidiRUS` a voz.        |
| `1.6.0-amd64-fr-ca-caroline-preview`        | Imagem de recipiente com o `fr-CA` local e `fr-CA-Caroline` a voz.        |
| `1.6.0-amd64-fr-ca-harmonierus-preview`     | Imagem de recipiente com o `fr-CA` local e `fr-CA-HarmonieRUS` a voz.     |
| `1.6.0-amd64-fr-ch-guillaume-preview`       | Imagem de recipiente com o `fr-CH` local e `fr-CH-Guillaume` a voz.       |
| `1.6.0-amd64-fr-fr-hortenserus-preview`     | Imagem de recipiente com o `fr-FR` local e `fr-FR-HortenseRUS` a voz.     |
| `1.6.0-amd64-fr-fr-julie-apollo-preview`    | Imagem de recipiente com o `fr-FR` local e `fr-FR-Julie-Apollo` a voz.    |
| `1.6.0-amd64-fr-fr-paul-apollo-preview`     | Imagem de recipiente com o `fr-FR` local e `fr-FR-Paul-Apollo` a voz.     |
| `1.6.0-amd64-he-il-asaf-preview`            | Imagem de recipiente com o `he-IL` local e `he-IL-Asaf` a voz.            |
| `1.6.0-amd64-hi-in-hemant-preview`          | Imagem de recipiente com o `hi-IN` local e `hi-IN-Hemant` a voz.          |
| `1.6.0-amd64-hi-in-kalpana-apollo-preview`  | Imagem de recipiente com o `hi-IN` local e `hi-IN-Kalpana-Apollo` a voz.  |
| `1.6.0-amd64-hi-in-kalpana-preview`         | Imagem de recipiente com o `hi-IN` local e `hi-IN-Kalpana` a voz.         |
| `1.6.0-amd64-hr-hr-matej-preview`           | Imagem de recipiente com o `hr-HR` local e `hr-HR-Matej` a voz.           |
| `1.6.0-amd64-hu-hu-szabolcs-preview`        | Imagem de recipiente com o `hu-HU` local e `hu-HU-Szabolcs` a voz.        |
| `1.6.0-amd64-id-id-andika-preview`          | Imagem de recipiente com o `id-ID` local e `id-ID-Andika` a voz.          |
| `1.6.0-amd64-it-it-cosimo-apollo-preview`   | Imagem de recipiente com o `it-IT` local e `it-IT-Cosimo-Apollo` a voz.   |
| `1.6.0-amd64-it-it-luciarus-preview`        | Imagem de recipiente com o `it-IT` local e `it-IT-LuciaRUS` a voz.        |
| `1.6.0-amd64-ja-jp-ayumi-apollo-preview`    | Imagem de recipiente com o `ja-JP` local e `ja-JP-Ayumi-Apollo` a voz.    |
| `1.6.0-amd64-ja-jp-harukarus-preview`       | Imagem de recipiente com o `ja-JP` local e `ja-JP-HarukaRUS` a voz.       |
| `1.6.0-amd64-ja-jp-ichiro-apollo-preview`   | Imagem de recipiente com o `ja-JP` local e `ja-JP-Ichiro-Apollo` a voz.   |
| `1.6.0-amd64-ko-kr-heamirus-preview`        | Imagem de recipiente com o `ko-KR` local e `ko-KR-HeamiRUS` a voz.        |
| `1.6.0-amd64-ms-my-rizwan-preview`          | Imagem de recipiente com o `ms-MY` local e `ms-MY-Rizwan` a voz.          |
| `1.6.0-amd64-nb-no-huldarus-preview`        | Imagem de recipiente com o `nb-NO` local e `nb-NO-HuldaRUS` a voz.        |
| `1.6.0-amd64-nl-nl-hannarus-preview`        | Imagem de recipiente com o `nl-NL` local e `nl-NL-HannaRUS` a voz.        |
| `1.6.0-amd64-pl-pl-paulinarus-preview`      | Imagem de recipiente com o `pl-PL` local e `pl-PL-PaulinaRUS` a voz.      |
| `1.6.0-amd64-pt-br-daniel-apollo-preview`   | Imagem de recipiente com o `pt-BR` local e `pt-BR-Daniel-Apollo` a voz.   |
| `1.6.0-amd64-pt-br-heloisarus-preview`      | Imagem de recipiente com o `pt-BR` local e `pt-BR-HeloisaRUS` a voz.      |
| `1.6.0-amd64-pt-pt-heliarus-preview`        | Imagem de recipiente com o `pt-PT` local e `pt-PT-HeliaRUS` a voz.        |
| `1.6.0-amd64-ro-ro-andrei-preview`          | Imagem de recipiente com o `ro-RO` local e `ro-RO-Andrei` a voz.          |
| `1.6.0-amd64-ru-ru-ekaterinarus-preview`    | Imagem de recipiente com o `ru-RU` local e `ru-RU-EkaterinaRUS` a voz.    |
| `1.6.0-amd64-ru-ru-irina-apollo-preview`    | Imagem de recipiente com o `ru-RU` local e `ru-RU-Irina-Apollo` a voz.    |
| `1.6.0-amd64-ru-ru-pavel-apollo-preview`    | Imagem de recipiente com o `ru-RU` local e `ru-RU-Pavel-Apollo` a voz.    |
| `1.6.0-amd64-sk-sk-filip-preview`           | Imagem de recipiente com o `sk-SK` local e `sk-SK-Filip` a voz.           |
| `1.6.0-amd64-sl-si-lado-preview`            | Imagem de recipiente com o `sl-SI` local e `sl-SI-Lado` a voz.            |
| `1.6.0-amd64-sv-se-hedvigrus-preview`       | Imagem de recipiente com o `sv-SE` local e `sv-SE-HedvigRUS` a voz.       |
| `1.6.0-amd64-ta-in-valluvar-preview`        | Imagem de recipiente com o `ta-IN` local e `ta-IN-Valluvar` a voz.        |
| `1.6.0-amd64-te-in-chitra-preview`          | Imagem de recipiente com o `te-IN` local e `te-IN-Chitra` a voz.          |
| `1.6.0-amd64-th-th-pattara-preview`         | Imagem de recipiente com o `th-TH` local e `th-TH-Pattara` a voz.         |
| `1.6.0-amd64-tr-tr-sedarus-preview`         | Imagem de recipiente com o `tr-TR` local e `tr-TR-SedaRUS` a voz.         |
| `1.6.0-amd64-vi-vn-an-preview`              | Imagem de recipiente com o `vi-VN` local e `vi-VN-An` a voz.              |
| `1.6.0-amd64-zh-cn-huihuirus-preview`       | Imagem de recipiente com o `zh-CN` local e `zh-CN-HuihuiRUS` a voz.       |
| `1.6.0-amd64-zh-cn-kangkang-apollo-preview` | Imagem de recipiente com o `zh-CN` local e `zh-CN-Kangkang-Apollo` a voz. |
| `1.6.0-amd64-zh-cn-yaoyao-apollo-preview`   | Imagem de recipiente com o `zh-CN` local e `zh-CN-Yaoyao-Apollo` a voz.   |
| `1.6.0-amd64-zh-hk-danny-apollo-preview`    | Imagem de recipiente com o `zh-HK` local e `zh-HK-Danny-Apollo` a voz.    |
| `1.6.0-amd64-zh-hk-tracy-apollo-preview`    | Imagem de recipiente com o `zh-HK` local e `zh-HK-Tracy-Apollo` a voz.    |
| `1.6.0-amd64-zh-hk-tracyrus-preview`        | Imagem de recipiente com o `zh-HK` local e `zh-HK-TracyRUS` a voz.        |
| `1.6.0-amd64-zh-tw-hanhanrus-preview`       | Imagem de recipiente com o `zh-TW` local e `zh-TW-HanHanRUS` a voz.       |
| `1.6.0-amd64-zh-tw-yating-apollo-preview`   | Imagem de recipiente com o `zh-TW` local e `zh-TW-Yating-Apollo` a voz.   |
| `1.6.0-amd64-zh-tw-zhiwei-apollo-preview`   | Imagem de recipiente com o `zh-TW` local e `zh-TW-Zhiwei-Apollo` a voz.   |
| `1.5.0-amd64-ar-eg-hoda-preview`            | Imagem de recipiente com o `ar-EG` local e `ar-EG-Hoda` a voz.            |
| `1.5.0-amd64-ar-sa-naayf-preview`           | Imagem de recipiente com o `ar-SA` local e `ar-SA-Naayf` a voz.           |
| `1.5.0-amd64-bg-bg-ivan-preview`            | Imagem de recipiente com o `bg-BG` local e `bg-BG-Ivan` a voz.            |
| `1.5.0-amd64-ca-es-herenarus-preview`       | Imagem de recipiente com o `ca-ES` local e `ca-ES-HerenaRUS` a voz.       |
| `1.5.0-amd64-cs-cz-jakub-preview`           | Imagem de recipiente com o `cs-CZ` local e `cs-CZ-Jakub` a voz.           |
| `1.5.0-amd64-da-dk-hellerus-preview`        | Imagem de recipiente com o `da-DK` local e `da-DK-HelleRUS` a voz.        |
| `1.5.0-amd64-de-at-michael-preview`         | Imagem de recipiente com o `de-AT` local e `de-AT-Michael` a voz.         |
| `1.5.0-amd64-de-ch-karsten-preview`         | Imagem de recipiente com o `de-CH` local e `de-CH-Karsten` a voz.         |
| `1.5.0-amd64-de-de-hedda-preview`           | Imagem de recipiente com o `de-DE` local e `de-DE-Hedda` a voz.           |
| `1.5.0-amd64-de-de-heddarus-preview`        | Imagem de recipiente com o `de-DE` local e `de-DE-Hedda` a voz.           |
| `1.5.0-amd64-de-de-stefan-apollo-preview`   | Imagem de recipiente com o `de-DE` local e `de-DE-Stefan-Apollo` a voz.   |
| `1.5.0-amd64-el-gr-stefanos-preview`        | Imagem de recipiente com o `el-GR` local e `el-GR-Stefanos` a voz.        |
| `1.5.0-amd64-en-au-catherine-preview`       | Imagem de recipiente com o `en-AU` local e `en-AU-Catherine` a voz.       |
| `1.5.0-amd64-en-au-hayleyrus-preview`       | Imagem de recipiente com o `en-AU` local e `en-AU-HayleyRUS` a voz.       |
| `1.5.0-amd64-en-ca-heatherrus-preview`      | Imagem de recipiente com o `en-CA` local e `en-CA-HeatherRUS` a voz.      |
| `1.5.0-amd64-en-ca-linda-preview`           | Imagem de recipiente com o `en-CA` local e `en-CA-Linda` a voz.           |
| `1.5.0-amd64-en-gb-george-apollo-preview`   | Imagem de recipiente com o `en-GB` local e `en-GB-George-Apollo` a voz.   |
| `1.5.0-amd64-en-gb-hazelrus-preview`        | Imagem de recipiente com o `en-GB` local e `en-GB-HazelRUS` a voz.        |
| `1.5.0-amd64-en-gb-susan-apollo-preview`    | Imagem de recipiente com o `en-GB` local e `en-GB-Susan-Apollo` a voz.    |
| `1.5.0-amd64-en-ie-sean-preview`            | Imagem de recipiente com o `en-IE` local e `en-IE-Sean` a voz.            |
| `1.5.0-amd64-en-in-heera-apollo-preview`    | Imagem de recipiente com o `en-IN` local e `en-IN-Heera-Apollo` a voz.    |
| `1.5.0-amd64-en-in-priyarus-preview`        | Imagem de recipiente com o `en-IN` local e `en-IN-PriyaRUS` a voz.        |
| `1.5.0-amd64-en-in-ravi-apollo-preview`     | Imagem de recipiente com o `en-IN` local e `en-IN-Ravi-Apollo` a voz.     |
| `1.5.0-amd64-en-us-benjaminrus-preview`     | Imagem de recipiente com o `en-US` local e `en-US-BenjaminRUS` a voz.     |
| `1.5.0-amd64-en-us-guy24krus-preview`       | Imagem de recipiente com o `en-US` local e `en-US-Guy24kRUS` a voz.       |
| `1.5.0-amd64-en-us-aria24krus-preview`      | Imagem de recipiente com o `en-US` local e `en-US-Aria24kRUS` a voz.     |
| `1.5.0-amd64-en-us-ariarus-preview`         | Imagem de recipiente com o `en-US` local e `en-US-AriaRUS` a voz.        |
| `1.5.0-amd64-en-us-zirarus-preview`         | Imagem de recipiente com o `en-US` local e `en-US-ZiraRUS` a voz.         |
| `1.5.0-amd64-es-es-helenarus-preview`       | Imagem de recipiente com o `es-ES` local e `es-ES-HelenaRUS` a voz.       |
| `1.5.0-amd64-es-es-laura-apollo-preview`    | Imagem de recipiente com o `es-ES` local e `es-ES-Laura-Apollo` a voz.    |
| `1.5.0-amd64-es-es-pablo-apollo-preview`    | Imagem de recipiente com o `es-ES` local e `es-ES-Pablo-Apollo` a voz.    |
| `1.5.0-amd64-es-mx-hildarus-preview`        | Imagem de recipiente com o `es-MX` local e `es-MX-HildaRUS` a voz.        |
| `1.5.0-amd64-es-mx-raul-apollo-preview`     | Imagem de recipiente com o `es-MX` local e `es-MX-Raul-Apollo` a voz.     |
| `1.5.0-amd64-fi-fi-heidirus-preview`        | Imagem de recipiente com o `fi-FI` local e `fi-FI-HeidiRUS` a voz.        |
| `1.5.0-amd64-fr-ca-caroline-preview`        | Imagem de recipiente com o `fr-CA` local e `fr-CA-Caroline` a voz.        |
| `1.5.0-amd64-fr-ca-harmonierus-preview`     | Imagem de recipiente com o `fr-CA` local e `fr-CA-HarmonieRUS` a voz.     |
| `1.5.0-amd64-fr-ch-guillaume-preview`       | Imagem de recipiente com o `fr-CH` local e `fr-CH-Guillaume` a voz.       |
| `1.5.0-amd64-fr-fr-hortenserus-preview`     | Imagem de recipiente com o `fr-FR` local e `fr-FR-HortenseRUS` a voz.     |
| `1.5.0-amd64-fr-fr-julie-apollo-preview`    | Imagem de recipiente com o `fr-FR` local e `fr-FR-Julie-Apollo` a voz.    |
| `1.5.0-amd64-fr-fr-paul-apollo-preview`     | Imagem de recipiente com o `fr-FR` local e `fr-FR-Paul-Apollo` a voz.     |
| `1.5.0-amd64-he-il-asaf-preview`            | Imagem de recipiente com o `he-IL` local e `he-IL-Asaf` a voz.            |
| `1.5.0-amd64-hi-in-hemant-preview`          | Imagem de recipiente com o `hi-IN` local e `hi-IN-Hemant` a voz.          |
| `1.5.0-amd64-hi-in-kalpana-apollo-preview`  | Imagem de recipiente com o `hi-IN` local e `hi-IN-Kalpana-Apollo` a voz.  |
| `1.5.0-amd64-hi-in-kalpana-preview`         | Imagem de recipiente com o `hi-IN` local e `hi-IN-Kalpana` a voz.         |
| `1.5.0-amd64-hr-hr-matej-preview`           | Imagem de recipiente com o `hr-HR` local e `hr-HR-Matej` a voz.           |
| `1.5.0-amd64-hu-hu-szabolcs-preview`        | Imagem de recipiente com o `hu-HU` local e `hu-HU-Szabolcs` a voz.        |
| `1.5.0-amd64-id-id-andika-preview`          | Imagem de recipiente com o `id-ID` local e `id-ID-Andika` a voz.          |
| `1.5.0-amd64-it-it-cosimo-apollo-preview`   | Imagem de recipiente com o `it-IT` local e `it-IT-Cosimo-Apollo` a voz.   |
| `1.5.0-amd64-it-it-luciarus-preview`        | Imagem de recipiente com o `it-IT` local e `it-IT-LuciaRUS` a voz.        |
| `1.5.0-amd64-ja-jp-ayumi-apollo-preview`    | Imagem de recipiente com o `ja-JP` local e `ja-JP-Ayumi-Apollo` a voz.    |
| `1.5.0-amd64-ja-jp-harukarus-preview`       | Imagem de recipiente com o `ja-JP` local e `ja-JP-HarukaRUS` a voz.       |
| `1.5.0-amd64-ja-jp-ichiro-apollo-preview`   | Imagem de recipiente com o `ja-JP` local e `ja-JP-Ichiro-Apollo` a voz.   |
| `1.5.0-amd64-ko-kr-heamirus-preview`        | Imagem de recipiente com o `ko-KR` local e `ko-KR-HeamiRUS` a voz.        |
| `1.5.0-amd64-ms-my-rizwan-preview`          | Imagem de recipiente com o `ms-MY` local e `ms-MY-Rizwan` a voz.          |
| `1.5.0-amd64-nb-no-huldarus-preview`        | Imagem de recipiente com o `nb-NO` local e `nb-NO-HuldaRUS` a voz.        |
| `1.5.0-amd64-nl-nl-hannarus-preview`        | Imagem de recipiente com o `nl-NL` local e `nl-NL-HannaRUS` a voz.        |
| `1.5.0-amd64-pl-pl-paulinarus-preview`      | Imagem de recipiente com o `pl-PL` local e `pl-PL-PaulinaRUS` a voz.      |
| `1.5.0-amd64-pt-br-daniel-apollo-preview`   | Imagem de recipiente com o `pt-BR` local e `pt-BR-Daniel-Apollo` a voz.   |
| `1.5.0-amd64-pt-br-heloisarus-preview`      | Imagem de recipiente com o `pt-BR` local e `pt-BR-HeloisaRUS` a voz.      |
| `1.5.0-amd64-pt-pt-heliarus-preview`        | Imagem de recipiente com o `pt-PT` local e `pt-PT-HeliaRUS` a voz.        |
| `1.5.0-amd64-ro-ro-andrei-preview`          | Imagem de recipiente com o `ro-RO` local e `ro-RO-Andrei` a voz.          |
| `1.5.0-amd64-ru-ru-ekaterinarus-preview`    | Imagem de recipiente com o `ru-RU` local e `ru-RU-EkaterinaRUS` a voz.    |
| `1.5.0-amd64-ru-ru-irina-apollo-preview`    | Imagem de recipiente com o `ru-RU` local e `ru-RU-Irina-Apollo` a voz.    |
| `1.5.0-amd64-ru-ru-pavel-apollo-preview`    | Imagem de recipiente com o `ru-RU` local e `ru-RU-Pavel-Apollo` a voz.    |
| `1.5.0-amd64-sk-sk-filip-preview`           | Imagem de recipiente com o `sk-SK` local e `sk-SK-Filip` a voz.           |
| `1.5.0-amd64-sl-si-lado-preview`            | Imagem de recipiente com o `sl-SI` local e `sl-SI-Lado` a voz.            |
| `1.5.0-amd64-sv-se-hedvigrus-preview`       | Imagem de recipiente com o `sv-SE` local e `sv-SE-HedvigRUS` a voz.       |
| `1.5.0-amd64-ta-in-valluvar-preview`        | Imagem de recipiente com o `ta-IN` local e `ta-IN-Valluvar` a voz.        |
| `1.5.0-amd64-te-in-chitra-preview`          | Imagem de recipiente com o `te-IN` local e `te-IN-Chitra` a voz.          |
| `1.5.0-amd64-th-th-pattara-preview`         | Imagem de recipiente com o `th-TH` local e `th-TH-Pattara` a voz.         |
| `1.5.0-amd64-tr-tr-sedarus-preview`         | Imagem de recipiente com o `tr-TR` local e `tr-TR-SedaRUS` a voz.         |
| `1.5.0-amd64-vi-vn-an-preview`              | Imagem de recipiente com o `vi-VN` local e `vi-VN-An` a voz.              |
| `1.5.0-amd64-zh-cn-huihuirus-preview`       | Imagem de recipiente com o `zh-CN` local e `zh-CN-HuihuiRUS` a voz.       |
| `1.5.0-amd64-zh-cn-kangkang-apollo-preview` | Imagem de recipiente com o `zh-CN` local e `zh-CN-Kangkang-Apollo` a voz. |
| `1.5.0-amd64-zh-cn-yaoyao-apollo-preview`   | Imagem de recipiente com o `zh-CN` local e `zh-CN-Yaoyao-Apollo` a voz.   |
| `1.5.0-amd64-zh-hk-danny-apollo-preview`    | Imagem de recipiente com o `zh-HK` local e `zh-HK-Danny-Apollo` a voz.    |
| `1.5.0-amd64-zh-hk-tracy-apollo-preview`    | Imagem de recipiente com o `zh-HK` local e `zh-HK-Tracy-Apollo` a voz.    |
| `1.5.0-amd64-zh-hk-tracyrus-preview`        | Imagem de recipiente com o `zh-HK` local e `zh-HK-TracyRUS` a voz.        |
| `1.5.0-amd64-zh-tw-hanhanrus-preview`       | Imagem de recipiente com o `zh-TW` local e `zh-TW-HanHanRUS` a voz.       |
| `1.5.0-amd64-zh-tw-yating-apollo-preview`   | Imagem de recipiente com o `zh-TW` local e `zh-TW-Yating-Apollo` a voz.   |
| `1.5.0-amd64-zh-tw-zhiwei-apollo-preview`   | Imagem de recipiente com o `zh-TW` local e `zh-TW-Zhiwei-Apollo` a voz.   |
| `1.4.0-amd64-ar-eg-hoda-preview`            | Imagem de recipiente com o `ar-EG` local e `ar-EG-Hoda` a voz.            |
| `1.4.0-amd64-ar-sa-naayf-preview`           | Imagem de recipiente com o `ar-SA` local e `ar-SA-Naayf` a voz.           |
| `1.4.0-amd64-bg-bg-ivan-preview`            | Imagem de recipiente com o `bg-BG` local e `bg-BG-Ivan` a voz.            |
| `1.4.0-amd64-ca-es-herenarus-preview`       | Imagem de recipiente com o `ca-ES` local e `ca-ES-HerenaRUS` a voz.       |
| `1.4.0-amd64-cs-cz-jakub-preview`           | Imagem de recipiente com o `cs-CZ` local e `cs-CZ-Jakub` a voz.           |
| `1.4.0-amd64-da-dk-hellerus-preview`        | Imagem de recipiente com o `da-DK` local e `da-DK-HelleRUS` a voz.        |
| `1.4.0-amd64-de-at-michael-preview`         | Imagem de recipiente com o `de-AT` local e `de-AT-Michael` a voz.         |
| `1.4.0-amd64-de-ch-karsten-preview`         | Imagem de recipiente com o `de-CH` local e `de-CH-Karsten` a voz.         |
| `1.4.0-amd64-de-de-hedda-preview`           | Imagem de recipiente com o `de-DE` local e `de-DE-Hedda` a voz.           |
| `1.4.0-amd64-de-de-heddarus-preview`        | Imagem de recipiente com o `de-DE` local e `de-DE-Hedda` a voz.           |
| `1.4.0-amd64-de-de-stefan-apollo-preview`   | Imagem de recipiente com o `de-DE` local e `de-DE-Stefan-Apollo` a voz.   |
| `1.4.0-amd64-el-gr-stefanos-preview`        | Imagem de recipiente com o `el-GR` local e `el-GR-Stefanos` a voz.        |
| `1.4.0-amd64-en-au-catherine-preview`       | Imagem de recipiente com o `en-AU` local e `en-AU-Catherine` a voz.       |
| `1.4.0-amd64-en-au-hayleyrus-preview`       | Imagem de recipiente com o `en-AU` local e `en-AU-HayleyRUS` a voz.       |
| `1.4.0-amd64-en-ca-heatherrus-preview`      | Imagem de recipiente com o `en-CA` local e `en-CA-HeatherRUS` a voz.      |
| `1.4.0-amd64-en-ca-linda-preview`           | Imagem de recipiente com o `en-CA` local e `en-CA-Linda` a voz.           |
| `1.4.0-amd64-en-gb-george-apollo-preview`   | Imagem de recipiente com o `en-GB` local e `en-GB-George-Apollo` a voz.   |
| `1.4.0-amd64-en-gb-hazelrus-preview`        | Imagem de recipiente com o `en-GB` local e `en-GB-HazelRUS` a voz.        |
| `1.4.0-amd64-en-gb-susan-apollo-preview`    | Imagem de recipiente com o `en-GB` local e `en-GB-Susan-Apollo` a voz.    |
| `1.4.0-amd64-en-ie-sean-preview`            | Imagem de recipiente com o `en-IE` local e `en-IE-Sean` a voz.            |
| `1.4.0-amd64-en-in-heera-apollo-preview`    | Imagem de recipiente com o `en-IN` local e `en-IN-Heera-Apollo` a voz.    |
| `1.4.0-amd64-en-in-priyarus-preview`        | Imagem de recipiente com o `en-IN` local e `en-IN-PriyaRUS` a voz.        |
| `1.4.0-amd64-en-in-ravi-apollo-preview`     | Imagem de recipiente com o `en-IN` local e `en-IN-Ravi-Apollo` a voz.     |
| `1.4.0-amd64-en-us-benjaminrus-preview`     | Imagem de recipiente com o `en-US` local e `en-US-BenjaminRUS` a voz.     |
| `1.4.0-amd64-en-us-guy24krus-preview`       | Imagem de recipiente com o `en-US` local e `en-US-Guy24kRUS` a voz.       |
| `1.4.0-amd64-en-us-aria24krus-preview`      | Imagem de recipiente com o `en-US` local e `en-US-Aria24kRUS` a voz.     |
| `1.4.0-amd64-en-us-ariarus-preview`         | Imagem de recipiente com o `en-US` local e `en-US-AriaRUS` a voz.        |
| `1.4.0-amd64-en-us-zirarus-preview`         | Imagem de recipiente com o `en-US` local e `en-US-ZiraRUS` a voz.         |
| `1.4.0-amd64-es-es-helenarus-preview`       | Imagem de recipiente com o `es-ES` local e `es-ES-HelenaRUS` a voz.       |
| `1.4.0-amd64-es-es-laura-apollo-preview`    | Imagem de recipiente com o `es-ES` local e `es-ES-Laura-Apollo` a voz.    |
| `1.4.0-amd64-es-es-pablo-apollo-preview`    | Imagem de recipiente com o `es-ES` local e `es-ES-Pablo-Apollo` a voz.    |
| `1.4.0-amd64-es-mx-hildarus-preview`        | Imagem de recipiente com o `es-MX` local e `es-MX-HildaRUS` a voz.        |
| `1.4.0-amd64-es-mx-raul-apollo-preview`     | Imagem de recipiente com o `es-MX` local e `es-MX-Raul-Apollo` a voz.     |
| `1.4.0-amd64-fi-fi-heidirus-preview`        | Imagem de recipiente com o `fi-FI` local e `fi-FI-HeidiRUS` a voz.        |
| `1.4.0-amd64-fr-ca-caroline-preview`        | Imagem de recipiente com o `fr-CA` local e `fr-CA-Caroline` a voz.        |
| `1.4.0-amd64-fr-ca-harmonierus-preview`     | Imagem de recipiente com o `fr-CA` local e `fr-CA-HarmonieRUS` a voz.     |
| `1.4.0-amd64-fr-ch-guillaume-preview`       | Imagem de recipiente com o `fr-CH` local e `fr-CH-Guillaume` a voz.       |
| `1.4.0-amd64-fr-fr-hortenserus-preview`     | Imagem de recipiente com o `fr-FR` local e `fr-FR-HortenseRUS` a voz.     |
| `1.4.0-amd64-fr-fr-julie-apollo-preview`    | Imagem de recipiente com o `fr-FR` local e `fr-FR-Julie-Apollo` a voz.    |
| `1.4.0-amd64-fr-fr-paul-apollo-preview`     | Imagem de recipiente com o `fr-FR` local e `fr-FR-Paul-Apollo` a voz.     |
| `1.4.0-amd64-he-il-asaf-preview`            | Imagem de recipiente com o `he-IL` local e `he-IL-Asaf` a voz.            |
| `1.4.0-amd64-hi-in-hemant-preview`          | Imagem de recipiente com o `hi-IN` local e `hi-IN-Hemant` a voz.          |
| `1.4.0-amd64-hi-in-kalpana-apollo-preview`  | Imagem de recipiente com o `hi-IN` local e `hi-IN-Kalpana-Apollo` a voz.  |
| `1.4.0-amd64-hi-in-kalpana-preview`         | Imagem de recipiente com o `hi-IN` local e `hi-IN-Kalpana` a voz.         |
| `1.4.0-amd64-hr-hr-matej-preview`           | Imagem de recipiente com o `hr-HR` local e `hr-HR-Matej` a voz.           |
| `1.4.0-amd64-hu-hu-szabolcs-preview`        | Imagem de recipiente com o `hu-HU` local e `hu-HU-Szabolcs` a voz.        |
| `1.4.0-amd64-id-id-andika-preview`          | Imagem de recipiente com o `id-ID` local e `id-ID-Andika` a voz.          |
| `1.4.0-amd64-it-it-cosimo-apollo-preview`   | Imagem de recipiente com o `it-IT` local e `it-IT-Cosimo-Apollo` a voz.   |
| `1.4.0-amd64-it-it-luciarus-preview`        | Imagem de recipiente com o `it-IT` local e `it-IT-LuciaRUS` a voz.        |
| `1.4.0-amd64-ja-jp-ayumi-apollo-preview`    | Imagem de recipiente com o `ja-JP` local e `ja-JP-Ayumi-Apollo` a voz.    |
| `1.4.0-amd64-ja-jp-harukarus-preview`       | Imagem de recipiente com o `ja-JP` local e `ja-JP-HarukaRUS` a voz.       |
| `1.4.0-amd64-ja-jp-ichiro-apollo-preview`   | Imagem de recipiente com o `ja-JP` local e `ja-JP-Ichiro-Apollo` a voz.   |
| `1.4.0-amd64-ko-kr-heamirus-preview`        | Imagem de recipiente com o `ko-KR` local e `ko-KR-HeamiRUS` a voz.        |
| `1.4.0-amd64-ms-my-rizwan-preview`          | Imagem de recipiente com o `ms-MY` local e `ms-MY-Rizwan` a voz.          |
| `1.4.0-amd64-nb-no-huldarus-preview`        | Imagem de recipiente com o `nb-NO` local e `nb-NO-HuldaRUS` a voz.        |
| `1.4.0-amd64-nl-nl-hannarus-preview`        | Imagem de recipiente com o `nl-NL` local e `nl-NL-HannaRUS` a voz.        |
| `1.4.0-amd64-pl-pl-paulinarus-preview`      | Imagem de recipiente com o `pl-PL` local e `pl-PL-PaulinaRUS` a voz.      |
| `1.4.0-amd64-pt-br-daniel-apollo-preview`   | Imagem de recipiente com o `pt-BR` local e `pt-BR-Daniel-Apollo` a voz.   |
| `1.4.0-amd64-pt-br-heloisarus-preview`      | Imagem de recipiente com o `pt-BR` local e `pt-BR-HeloisaRUS` a voz.      |
| `1.4.0-amd64-pt-pt-heliarus-preview`        | Imagem de recipiente com o `pt-PT` local e `pt-PT-HeliaRUS` a voz.        |
| `1.4.0-amd64-ro-ro-andrei-preview`          | Imagem de recipiente com o `ro-RO` local e `ro-RO-Andrei` a voz.          |
| `1.4.0-amd64-ru-ru-ekaterinarus-preview`    | Imagem de recipiente com o `ru-RU` local e `ru-RU-EkaterinaRUS` a voz.    |
| `1.4.0-amd64-ru-ru-irina-apollo-preview`    | Imagem de recipiente com o `ru-RU` local e `ru-RU-Irina-Apollo` a voz.    |
| `1.4.0-amd64-ru-ru-pavel-apollo-preview`    | Imagem de recipiente com o `ru-RU` local e `ru-RU-Pavel-Apollo` a voz.    |
| `1.4.0-amd64-sk-sk-filip-preview`           | Imagem de recipiente com o `sk-SK` local e `sk-SK-Filip` a voz.           |
| `1.4.0-amd64-sl-si-lado-preview`            | Imagem de recipiente com o `sl-SI` local e `sl-SI-Lado` a voz.            |
| `1.4.0-amd64-sv-se-hedvigrus-preview`       | Imagem de recipiente com o `sv-SE` local e `sv-SE-HedvigRUS` a voz.       |
| `1.4.0-amd64-ta-in-valluvar-preview`        | Imagem de recipiente com o `ta-IN` local e `ta-IN-Valluvar` a voz.        |
| `1.4.0-amd64-te-in-chitra-preview`          | Imagem de recipiente com o `te-IN` local e `te-IN-Chitra` a voz.          |
| `1.4.0-amd64-th-th-pattara-preview`         | Imagem de recipiente com o `th-TH` local e `th-TH-Pattara` a voz.         |
| `1.4.0-amd64-tr-tr-sedarus-preview`         | Imagem de recipiente com o `tr-TR` local e `tr-TR-SedaRUS` a voz.         |
| `1.4.0-amd64-vi-vn-an-preview`              | Imagem de recipiente com o `vi-VN` local e `vi-VN-An` a voz.              |
| `1.4.0-amd64-zh-cn-huihuirus-preview`       | Imagem de recipiente com o `zh-CN` local e `zh-CN-HuihuiRUS` a voz.       |
| `1.4.0-amd64-zh-cn-kangkang-apollo-preview` | Imagem de recipiente com o `zh-CN` local e `zh-CN-Kangkang-Apollo` a voz. |
| `1.4.0-amd64-zh-cn-yaoyao-apollo-preview`   | Imagem de recipiente com o `zh-CN` local e `zh-CN-Yaoyao-Apollo` a voz.   |
| `1.4.0-amd64-zh-hk-danny-apollo-preview`    | Imagem de recipiente com o `zh-HK` local e `zh-HK-Danny-Apollo` a voz.    |
| `1.4.0-amd64-zh-hk-tracy-apollo-preview`    | Imagem de recipiente com o `zh-HK` local e `zh-HK-Tracy-Apollo` a voz.    |
| `1.4.0-amd64-zh-hk-tracyrus-preview`        | Imagem de recipiente com o `zh-HK` local e `zh-HK-TracyRUS` a voz.        |
| `1.4.0-amd64-zh-tw-hanhanrus-preview`       | Imagem de recipiente com o `zh-TW` local e `zh-TW-HanHanRUS` a voz.       |
| `1.4.0-amd64-zh-tw-yating-apollo-preview`   | Imagem de recipiente com o `zh-TW` local e `zh-TW-Yating-Apollo` a voz.   |
| `1.4.0-amd64-zh-tw-zhiwei-apollo-preview`   | Imagem de recipiente com o `zh-TW` local e `zh-TW-Zhiwei-Apollo` a voz.   |
| `1.3.0-amd64-ar-eg-hoda-preview`            | Imagem de recipiente com o `ar-EG` local e `ar-EG-Hoda` a voz.            |
| `1.3.0-amd64-ar-sa-naayf-preview`           | Imagem de recipiente com o `ar-SA` local e `ar-SA-Naayf` a voz.           |
| `1.3.0-amd64-bg-bg-ivan-preview`            | Imagem de recipiente com o `bg-BG` local e `bg-BG-Ivan` a voz.            |
| `1.3.0-amd64-ca-es-herenarus-preview`       | Imagem de recipiente com o `ca-ES` local e `ca-ES-HerenaRUS` a voz.       |
| `1.3.0-amd64-cs-cz-jakub-preview`           | Imagem de recipiente com o `cs-CZ` local e `cs-CZ-Jakub` a voz.           |
| `1.3.0-amd64-da-dk-hellerus-preview`        | Imagem de recipiente com o `da-DK` local e `da-DK-HelleRUS` a voz.        |
| `1.3.0-amd64-de-at-michael-preview`         | Imagem de recipiente com o `de-AT` local e `de-AT-Michael` a voz.         |
| `1.3.0-amd64-de-ch-karsten-preview`         | Imagem de recipiente com o `de-CH` local e `de-CH-Karsten` a voz.         |
| `1.3.0-amd64-de-de-hedda-preview`           | Imagem de recipiente com o `de-DE` local e `de-DE-Hedda` a voz.           |
| `1.3.0-amd64-de-de-heddarus-preview`        | Imagem de recipiente com o `de-DE` local e `de-DE-HeddaRUS` a voz.        |
| `1.3.0-amd64-de-de-stefan-apollo-preview`   | Imagem de recipiente com o `de-DE` local e `de-DE-Stefan-Apollo` a voz.   |
| `1.3.0-amd64-el-gr-stefanos-preview`        | Imagem de recipiente com o `el-GR` local e `el-GR-Stefanos` a voz.        |
| `1.3.0-amd64-en-au-catherine-preview`       | Imagem de recipiente com o `en-AU` local e `en-AU-Catherine` a voz.       |
| `1.3.0-amd64-en-au-hayleyrus-preview`       | Imagem de recipiente com o `en-AU` local e `en-AU-HayleyRUS` a voz.       |
| `1.3.0-amd64-en-ca-heatherrus-preview`      | Imagem de recipiente com o `en-CA` local e `en-CA-HeatherRUS` a voz.      |
| `1.3.0-amd64-en-ca-linda-preview`           | Imagem de recipiente com o `en-CA` local e `en-CA-Linda` a voz.           |
| `1.3.0-amd64-en-gb-george-apollo-preview`   | Imagem de recipiente com o `en-GB` local e `en-GB-George-Apollo` a voz.   |
| `1.3.0-amd64-en-gb-hazelrus-preview`        | Imagem de recipiente com o `en-GB` local e `en-GB-HazelRUS` a voz.        |
| `1.3.0-amd64-en-gb-susan-apollo-preview`    | Imagem de recipiente com o `en-GB` local e `en-GB-Susan-Apollo` a voz.    |
| `1.3.0-amd64-en-ie-sean-preview`            | Imagem de recipiente com o `en-IE` local e `en-IE-Sean` a voz.            |
| `1.3.0-amd64-en-in-heera-apollo-preview`    | Imagem de recipiente com o `en-IN` local e `en-IN-Heera-Apollo` a voz.    |
| `1.3.0-amd64-en-in-priyarus-preview`        | Imagem de recipiente com o `en-IN` local e `en-IN-PriyaRUS` a voz.        |
| `1.3.0-amd64-en-in-ravi-apollo-preview`     | Imagem de recipiente com o `en-IN` local e `en-IN-Ravi-Apollo` a voz.     |
| `1.3.0-amd64-en-us-benjaminrus-preview`     | Imagem de recipiente com o `en-US` local e `en-US-BenjaminRUS` a voz.     |
| `1.3.0-amd64-en-us-guy24krus-preview`       | Imagem de recipiente com o `en-US` local e `en-US-Guy24kRUS` a voz.       |
| `1.3.0-amd64-en-us-jessa24krus-preview`     | Imagem de recipiente com o `en-US` local e `en-US-Jessa24kRUS` a voz.     |
| `1.3.0-amd64-en-us-jessarus-preview`        | Imagem de recipiente com o `en-US` local e `en-US-JessaRUS` a voz.        |
| `1.3.0-amd64-en-us-zirarus-preview`         | Imagem de recipiente com o `en-US` local e `en-US-ZiraRUS` a voz.         |
| `1.3.0-amd64-es-es-helenarus-preview`       | Imagem de recipiente com o `es-ES` local e `es-ES-HelenaRUS` a voz.       |
| `1.3.0-amd64-es-es-laura-apollo-preview`    | Imagem de recipiente com o `es-ES` local e `es-ES-Laura-Apollo` a voz.    |
| `1.3.0-amd64-es-es-pablo-apollo-preview`    | Imagem de recipiente com o `es-ES` local e `es-ES-Pablo-Apollo` a voz.    |
| `1.3.0-amd64-es-mx-hildarus-preview`        | Imagem de recipiente com o `es-MX` local e `es-MX-HildaRUS` a voz.        |
| `1.3.0-amd64-es-mx-raul-apollo-preview`     | Imagem de recipiente com o `es-MX` local e `es-MX-Raul-Apollo` a voz.     |
| `1.3.0-amd64-fi-fi-heidirus-preview`        | Imagem de recipiente com o `fi-FI` local e `fi-FI-HeidiRUS` a voz.        |
| `1.3.0-amd64-fr-ca-caroline-preview`        | Imagem de recipiente com o `fr-CA` local e `fr-CA-Caroline` a voz.        |
| `1.3.0-amd64-fr-ca-harmonierus-preview`     | Imagem de recipiente com o `fr-CA` local e `fr-CA-HarmonieRUS` a voz.     |
| `1.3.0-amd64-fr-ch-guillaume-preview`       | Imagem de recipiente com o `fr-CH` local e `fr-CH-Guillaume` a voz.       |
| `1.3.0-amd64-fr-fr-hortenserus-preview`     | Imagem de recipiente com o `fr-FR` local e `fr-FR-HortenseRUS` a voz.     |
| `1.3.0-amd64-fr-fr-julie-apollo-preview`    | Imagem de recipiente com o `fr-FR` local e `fr-FR-Julie-Apollo` a voz.    |
| `1.3.0-amd64-fr-fr-paul-apollo-preview`     | Imagem de recipiente com o `fr-FR` local e `fr-FR-Paul-Apollo` a voz.     |
| `1.3.0-amd64-he-il-asaf-preview`            | Imagem de recipiente com o `he-IL` local e `he-IL-Asaf` a voz.            |
| `1.3.0-amd64-hi-in-hemant-preview`          | Imagem de recipiente com o `hi-IN` local e `hi-IN-Hemant` a voz.          |
| `1.3.0-amd64-hi-in-kalpana-preview`         | Imagem de recipiente com o `hi-IN` local e `hi-IN-Kalpana` a voz.         |
| `1.3.0-amd64-hi-in-kalpana-preview`         | Imagem de recipiente com o `hi-IN` local e `hi-IN-Kalpana` a voz.         |
| `1.3.0-amd64-hr-hr-matej-preview`           | Imagem de recipiente com o `hr-HR` local e `hr-HR-Matej` a voz.           |
| `1.3.0-amd64-hu-hu-szabolcs-preview`        | Imagem de recipiente com o `hu-HU` local e `hu-HU-Szabolcs` a voz.        |
| `1.3.0-amd64-id-id-andika-preview`          | Imagem de recipiente com o `id-ID` local e `id-ID-Andika` a voz.          |
| `1.3.0-amd64-it-it-cosimo-apollo-preview`   | Imagem de recipiente com o `it-IT` local e `it-IT-Cosimo-Apollo` a voz.   |
| `1.3.0-amd64-it-it-luciarus-preview`        | Imagem de recipiente com o `it-IT` local e `it-IT-LuciaRUS` a voz.        |
| `1.3.0-amd64-ja-jp-ayumi-apollo-preview`    | Imagem de recipiente com o `ja-JP` local e `ja-JP-Ayumi-Apollo` a voz.    |
| `1.3.0-amd64-ja-jp-harukarus-preview`       | Imagem de recipiente com o `ja-JP` local e `ja-JP-HarukaRUS` a voz.       |
| `1.3.0-amd64-ja-jp-ichiro-apollo-preview`   | Imagem de recipiente com o `ja-JP` local e `ja-JP-Ichiro-Apollo` a voz.   |
| `1.3.0-amd64-ko-kr-heamirus-preview`        | Imagem de recipiente com o `ko-KR` local e `ko-KR-HeamiRUS` a voz.        |
| `1.3.0-amd64-ms-my-rizwan-preview`          | Imagem de recipiente com o `ms-MY` local e `ms-MY-Rizwan` a voz.          |
| `1.3.0-amd64-nb-no-huldarus-preview`        | Imagem de recipiente com o `nb-NO` local e `nb-NO-HuldaRUS` a voz.        |
| `1.3.0-amd64-nl-nl-hannarus-preview`        | Imagem de recipiente com o `nl-NL` local e `nl-NL-HannaRUS` a voz.        |
| `1.3.0-amd64-pl-pl-paulinarus-preview`      | Imagem de recipiente com o `pl-PL` local e `pl-PL-PaulinaRUS` a voz.      |
| `1.3.0-amd64-pt-br-daniel-apollo-preview`   | Imagem de recipiente com o `pt-BR` local e `pt-BR-Daniel-Apollo` a voz.   |
| `1.3.0-amd64-pt-br-heloisarus-preview`      | Imagem de recipiente com o `pt-BR` local e `pt-BR-HeloisaRUS` a voz.      |
| `1.3.0-amd64-pt-pt-heliarus-preview`        | Imagem de recipiente com o `pt-PT` local e `pt-PT-HeliaRUS` a voz.        |
| `1.3.0-amd64-ro-ro-andrei-preview`          | Imagem de recipiente com o `ro-RO` local e `ro-RO-Andrei` a voz.          |
| `1.3.0-amd64-ru-ru-ekaterinarus-preview`    | Imagem de recipiente com o `ru-RU` local e `ru-RU-EkaterinaRUS` a voz.    |
| `1.3.0-amd64-ru-ru-irina-apollo-preview`    | Imagem de recipiente com o `ru-RU` local e `ru-RU-Irina-Apollo` a voz.    |
| `1.3.0-amd64-ru-ru-pavel-apollo-preview`    | Imagem de recipiente com o `ru-RU` local e `ru-RU-Pavel-Apollo` a voz.    |
| `1.3.0-amd64-sk-sk-filip-preview`           | Imagem de recipiente com o `sk-SK` local e `sk-SK-Filip` a voz.           |
| `1.3.0-amd64-sl-si-lado-preview`            | Imagem de recipiente com o `sl-SI` local e `sl-SI-Lado` a voz.            |
| `1.3.0-amd64-sv-se-hedvigrus-preview`       | Imagem de recipiente com o `sv-SE` local e `sv-SE-HedvigRUS` a voz.       |
| `1.3.0-amd64-ta-in-valluvar-preview`        | Imagem de recipiente com o `ta-IN` local e `ta-IN-Valluvar` a voz.        |
| `1.3.0-amd64-te-in-chitra-preview`          | Imagem de recipiente com o `te-IN` local e `te-IN-Chitra` a voz.          |
| `1.3.0-amd64-th-th-pattara-preview`         | Imagem de recipiente com o `th-TH` local e `th-TH-Pattara` a voz.         |
| `1.3.0-amd64-tr-tr-sedarus-preview`         | Imagem de recipiente com o `tr-TR` local e `tr-TR-SedaRUS` a voz.         |
| `1.3.0-amd64-vi-vn-an-preview`              | Imagem de recipiente com o `vi-VN` local e `vi-VN-An` a voz.              |
| `1.3.0-amd64-zh-cn-huihuirus-preview`       | Imagem de recipiente com o `zh-CN` local e `zh-CN-HuihuiRUS` a voz.       |
| `1.3.0-amd64-zh-cn-kangkang-apollo-preview` | Imagem de recipiente com o `zh-CN` local e `zh-CN-Kangkang-Apollo` a voz. |
| `1.3.0-amd64-zh-cn-yaoyao-apollo-preview`   | Imagem de recipiente com o `zh-CN` local e `zh-CN-Yaoyao-Apollo` a voz.   |
| `1.3.0-amd64-zh-hk-danny-apollo-preview`    | Imagem de recipiente com o `zh-HK` local e `zh-HK-Danny-Apollo` a voz.    |
| `1.3.0-amd64-zh-hk-tracy-apollo-preview`    | Imagem de recipiente com o `zh-HK` local e `zh-HK-Tracy-Apollo` a voz.    |
| `1.3.0-amd64-zh-hk-tracyrus-preview`        | Imagem de recipiente com o `zh-HK` local e `zh-HK-TracyRUS` a voz.        |
| `1.3.0-amd64-zh-tw-hanhanrus-preview`       | Imagem de recipiente com o `zh-TW` local e `zh-TW-HanHanRUS` a voz.       |
| `1.3.0-amd64-zh-tw-yating-apollo-preview`   | Imagem de recipiente com o `zh-TW` local e `zh-TW-Yating-Apollo` a voz.   |
| `1.3.0-amd64-zh-tw-zhiwei-apollo-preview`   | Imagem de recipiente com o `zh-TW` local e `zh-TW-Zhiwei-Apollo` a voz.   |
| `1.2.0-amd64-de-de-hedda-preview`           | Imagem de recipiente com o `de-DE` local e `de-DE-Hedda` a voz.           |
| `1.2.0-amd64-de-de-heddarus-preview`        | Imagem de recipiente com o `de-DE` local e `de-DE-HeddaRUS` a voz.        |
| `1.2.0-amd64-de-de-stefan-apollo-preview`   | Imagem de recipiente com o `de-DE` local e `de-DE-Stefan-Apollo` a voz.   |
| `1.2.0-amd64-en-au-catherine-preview`       | Imagem de recipiente com o `en-AU` local e `en-AU-Catherine` a voz.       |
| `1.2.0-amd64-en-au-hayleyrus-preview`       | Imagem de recipiente com o `en-AU` local e `en-AU-HayleyRUS` a voz.       |
| `1.2.0-amd64-en-gb-george-apollo-preview`   | Imagem de recipiente com o `en-GB` local e `en-GB-George-Apollo` a voz.   |
| `1.2.0-amd64-en-gb-hazelrus-preview`        | Imagem de recipiente com o `en-GB` local e `en-GB-HazelRUS` a voz.        |
| `1.2.0-amd64-en-gb-susan-apollo-preview`    | Imagem de recipiente com o `en-GB` local e `en-GB-Susan-Apollo` a voz.    |
| `1.2.0-amd64-en-in-heera-apollo-preview`    | Imagem de recipiente com o `en-IN` local e `en-IN-Heera-Apollo` a voz.    |
| `1.2.0-amd64-en-in-priyarus-preview`        | Imagem de recipiente com o `en-IN` local e `en-IN-PriyaRUS` a voz.        |
| `1.2.0-amd64-en-in-ravi-apollo-preview`     | Imagem de recipiente com o `en-IN` local e `en-IN-Ravi-Apollo` a voz.     |
| `1.2.0-amd64-en-us-benjaminrus-preview`     | Imagem de recipiente com o `en-US` local e `en-US-BenjaminRUS` a voz.     |
| `1.2.0-amd64-en-us-guy24krus-preview`       | Imagem de recipiente com o `en-US` local e `en-US-Guy24kRUS` a voz.       |
| `1.2.0-amd64-en-us-jessa24krus-preview`     | Imagem de recipiente com o `en-US` local e `en-US-Jessa24kRUS` a voz.     |
| `1.2.0-amd64-en-us-jessarus-preview`        | Imagem de recipiente com o `en-US` local e `en-US-JessaRUS` a voz.        |
| `1.2.0-amd64-en-us-zirarus-preview`         | Imagem de recipiente com o `en-US` local e `en-US-ZiraRUS` a voz.         |
| `1.2.0-amd64-es-es-helenarus-preview`       | Imagem de recipiente com o `es-ES` local e `es-ES-HelenaRUS` a voz.       |
| `1.2.0-amd64-es-es-laura-apollo-preview`    | Imagem de recipiente com o `es-ES` local e `es-ES-Laura-Apollo` a voz.    |
| `1.2.0-amd64-es-es-pablo-apollo-preview`    | Imagem de recipiente com o `es-ES` local e `es-ES-Pablo-Apollo` a voz.    |
| `1.2.0-amd64-es-mx-hildarus-preview`        | Imagem de recipiente com o `es-MX` local e `es-MX-HildaRUS` a voz.        |
| `1.2.0-amd64-es-mx-raul-apollo-preview`     | Imagem de recipiente com o `es-MX` local e `es-MX-Raul-Apollo` a voz.     |
| `1.2.0-amd64-fr-ca-caroline-preview`        | Imagem de recipiente com o `fr-CA` local e `fr-CA-Caroline` a voz.        |
| `1.2.0-amd64-fr-ca-harmonierus-preview`     | Imagem de recipiente com o `fr-CA` local e `fr-CA-HarmonieRUS` a voz.     |
| `1.2.0-amd64-fr-fr-hortenserus-preview`     | Imagem de recipiente com o `fr-FR` local e `fr-FR-HortenseRUS` a voz.     |
| `1.2.0-amd64-fr-fr-julie-apollo-preview`    | Imagem de recipiente com o `fr-FR` local e `fr-FR-Julie-Apollo` a voz.    |
| `1.2.0-amd64-fr-fr-paul-apollo-preview`     | Imagem de recipiente com o `fr-FR` local e `fr-FR-Paul-Apollo` a voz.     |
| `1.2.0-amd64-it-it-cosimo-apollo-preview`   | Imagem de recipiente com o `it-IT` local e `it-IT-Cosimo-Apollo` a voz.   |
| `1.2.0-amd64-it-it-luciarus-preview`        | Imagem de recipiente com o `it-IT` local e `it-IT-LuciaRUS` a voz.        |
| `1.2.0-amd64-ja-jp-ayumi-apollo-preview`    | Imagem de recipiente com o `ja-JP` local e `ja-JP-Ayumi-Apollo` a voz.    |
| `1.2.0-amd64-ja-jp-harukarus-preview`       | Imagem de recipiente com o `ja-JP` local e `ja-JP-HarukaRUS` a voz.       |
| `1.2.0-amd64-ja-jp-ichiro-apollo-preview`   | Imagem de recipiente com o `ja-JP` local e `ja-JP-Ichiro-Apollo` a voz.   |
| `1.2.0-amd64-ko-kr-heamirus-preview`        | Imagem de recipiente com o `ko-KR` local e `ko-KR-HeamiRUS` a voz.        |
| `1.2.0-amd64-pt-br-daniel-apollo-preview`   | Imagem de recipiente com o `pt-BR` local e `pt-BR-Daniel-Apollo` a voz.   |
| `1.2.0-amd64-pt-br-heloisarus-preview`      | Imagem de recipiente com o `pt-BR` local e `pt-BR-HeloisaRUS` a voz.      |
| `1.2.0-amd64-zh-cn-huihuirus-preview`       | Imagem de recipiente com o `zh-CN` local e `zh-CN-HuihuiRUS` a voz.       |
| `1.2.0-amd64-zh-cn-kangkang-apollo-preview` | Imagem de recipiente com o `zh-CN` local e `zh-CN-Kangkang-Apollo` a voz. |
| `1.2.0-amd64-zh-cn-yaoyao-apollo-preview`   | Imagem de recipiente com o `zh-CN` local e `zh-CN-Yaoyao-Apollo` a voz.   |
| `1.1.0-amd64-de-de-hedda-preview`           | Imagem de recipiente com o `de-DE` local e `de-DE-Hedda` a voz.           |
| `1.1.0-amd64-de-de-heddarus-preview`        | Imagem de recipiente com o `de-DE` local e `de-DE-HeddaRUS` a voz.        |
| `1.1.0-amd64-de-de-stefan-apollo-preview`   | Imagem de recipiente com o `de-DE` local e `de-DE-Stefan-Apollo` a voz.   |
| `1.1.0-amd64-en-au-catherine-preview`       | Imagem de recipiente com o `en-AU` local e `en-AU-Catherine` a voz.       |
| `1.1.0-amd64-en-au-hayleyrus-preview`       | Imagem de recipiente com o `en-AU` local e `en-AU-HayleyRUS` a voz.       |
| `1.1.0-amd64-en-gb-george-apollo-preview`   | Imagem de recipiente com o `en-GB` local e `en-GB-George-Apollo` a voz.   |
| `1.1.0-amd64-en-gb-hazelrus-preview`        | Imagem de recipiente com o `en-GB` local e `en-GB-HazelRUS` a voz.        |
| `1.1.0-amd64-en-gb-susan-apollo-preview`    | Imagem de recipiente com o `en-GB` local e `en-GB-Susan-Apollo` a voz.    |
| `1.1.0-amd64-en-in-heera-apollo-preview`    | Imagem de recipiente com o `en-IN` local e `en-IN-Heera-Apollo` a voz.    |
| `1.1.0-amd64-en-in-priyarus-preview`        | Imagem de recipiente com o `en-IN` local e `en-IN-PriyaRUS` a voz.        |
| `1.1.0-amd64-en-in-ravi-apollo-preview`     | Imagem de recipiente com o `en-IN` local e `en-IN-Ravi-Apollo` a voz.     |
| `1.1.0-amd64-en-us-benjaminrus-preview`     | Imagem de recipiente com o `en-US` local e `en-US-BenjaminRUS` a voz.     |
| `1.1.0-amd64-en-us-guy24krus-preview`       | Imagem de recipiente com o `en-US` local e `en-US-Guy24kRUS` a voz.       |
| `1.1.0-amd64-en-us-jessa24krus-preview`     | Imagem de recipiente com o `en-US` local e `en-US-Jessa24kRUS` a voz.     |
| `1.1.0-amd64-en-us-jessarus-preview`        | Imagem de recipiente com o `en-US` local e `en-US-JessaRUS` a voz.        |
| `1.1.0-amd64-en-us-zirarus-preview`         | Imagem de recipiente com o `en-US` local e `en-US-ZiraRUS` a voz.         |
| `1.1.0-amd64-es-es-helenarus-preview`       | Imagem de recipiente com o `es-ES` local e `es-ES-HelenaRUS` a voz.       |
| `1.1.0-amd64-es-es-laura-apollo-preview`    | Imagem de recipiente com o `es-ES` local e `es-ES-Laura-Apollo` a voz.    |
| `1.1.0-amd64-es-es-pablo-apollo-preview`    | Imagem de recipiente com o `es-ES` local e `es-ES-Pablo-Apollo` a voz.    |
| `1.1.0-amd64-es-mx-hildarus-preview`        | Imagem de recipiente com o `es-MX` local e `es-MX-HildaRUS` a voz.        |
| `1.1.0-amd64-es-mx-raul-apollo-preview`     | Imagem de recipiente com o `es-MX` local e `es-MX-Raul-Apollo` a voz.     |
| `1.1.0-amd64-fr-ca-caroline-preview`        | Imagem de recipiente com o `fr-CA` local e `fr-CA-Caroline` a voz.        |
| `1.1.0-amd64-fr-ca-harmonierus-preview`     | Imagem de recipiente com o `fr-CA` local e `fr-CA-HarmonieRUS` a voz.     |
| `1.1.0-amd64-fr-fr-hortenserus-preview`     | Imagem de recipiente com o `fr-FR` local e `fr-FR-HortenseRUS` a voz.     |
| `1.1.0-amd64-fr-fr-julie-apollo-preview`    | Imagem de recipiente com o `fr-FR` local e `fr-FR-Julie-Apollo` a voz.    |
| `1.1.0-amd64-fr-fr-paul-apollo-preview`     | Imagem de recipiente com o `fr-FR` local e `fr-FR-Paul-Apollo` a voz.     |
| `1.1.0-amd64-it-it-cosimo-apollo-preview`   | Imagem de recipiente com o `it-IT` local e `it-IT-Cosimo-Apollo` a voz.   |
| `1.1.0-amd64-it-it-luciarus-preview`        | Imagem de recipiente com o `it-IT` local e `it-IT-LuciaRUS` a voz.        |
| `1.1.0-amd64-ja-jp-ayumi-apollo-preview`    | Imagem de recipiente com o `ja-JP` local e `ja-JP-Ayumi-Apollo` a voz.    |
| `1.1.0-amd64-ja-jp-harukarus-preview`       | Imagem de recipiente com o `ja-JP` local e `ja-JP-HarukaRUS` a voz.       |
| `1.1.0-amd64-ja-jp-ichiro-apollo-preview`   | Imagem de recipiente com o `ja-JP` local e `ja-JP-Ichiro-Apollo` a voz.   |
| `1.1.0-amd64-ko-kr-heamirus-preview`        | Imagem de recipiente com o `ko-KR` local e `ko-KR-HeamiRUS` a voz.        |
| `1.1.0-amd64-pt-br-daniel-apollo-preview`   | Imagem de recipiente com o `pt-BR` local e `pt-BR-Daniel-Apollo` a voz.   |
| `1.1.0-amd64-pt-br-heloisarus-preview`      | Imagem de recipiente com o `pt-BR` local e `pt-BR-HeloisaRUS` a voz.      |
| `1.1.0-amd64-zh-cn-huihuirus-preview`       | Imagem de recipiente com o `zh-CN` local e `zh-CN-HuihuiRUS` a voz.       |
| `1.1.0-amd64-zh-cn-kangkang-apollo-preview` | Imagem de recipiente com o `zh-CN` local e `zh-CN-Kangkang-Apollo` a voz. |
| `1.1.0-amd64-zh-cn-yaoyao-apollo-preview`   | Imagem de recipiente com o `zh-CN` local e `zh-CN-Yaoyao-Apollo` a voz.   |
| `1.0.0-amd64-en-us-benjaminrus-preview`     | Imagem de recipiente com o `en-US` local e `en-US-BenjaminRUS` a voz.     |
| `1.0.0-amd64-en-us-guy24krus-preview`       | Imagem de recipiente com o `en-US` local e `en-US-Guy24kRUS` a voz.       |
| `1.0.0-amd64-en-us-jessa24krus-preview`     | Imagem de recipiente com o `en-US` local e `en-US-Jessa24kRUS` a voz.     |
| `1.0.0-amd64-en-us-jessarus-preview`        | Imagem de recipiente com o `en-US` local e `en-US-JessaRUS` a voz.        |
| `1.0.0-amd64-en-us-zirarus-preview`         | Imagem de recipiente com o `en-US` local e `en-US-ZiraRUS` a voz.         |
| `1.0.0-amd64-zh-cn-huihuirus-preview`       | Imagem de recipiente com o `zh-CN` local e `zh-CN-HuihuiRUS` a voz.       |
| `1.0.0-amd64-zh-cn-kangkang-apollo-preview` | Imagem de recipiente com o `zh-CN` local e `zh-CN-Kangkang-Apollo` a voz. |
| `1.0.0-amd64-zh-cn-yaoyao-apollo-preview`   | Imagem de recipiente com o `zh-CN` local e `zh-CN-Yaoyao-Apollo` a voz.   |

## <a name="neural-text-to-speech"></a>Texto-a-discurso neural

A imagem do contentor [Texto-a-fala] [sp-ntts] pode ser encontrada no registo do `containerpreview.azurecr.io` contentor. Reside dentro do `microsoft` repositório e é `cognitive-services-neural-text-to-speech` nomeado. O nome de imagem do recipiente totalmente qualificado é, `containerpreview.azurecr.io/microsoft/cognitive-services-neural-text-to-speech` .

Esta imagem do recipiente tem as seguintes etiquetas disponíveis:

| Etiquetas de imagem                                  | Notas                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Imagem de recipiente com o `en-US` local e `en-US-AriaNeural` a voz.      |
| `1.2.0-amd64-de-de-katjaneural-preview`     | Imagem de recipiente com o `de-DE` local e `de-DE-KatjaNeural` a voz.     |
| `1.2.0-amd64-en-au-natashaneural-preview`   | Imagem de recipiente com o `en-AU` local e `en-AU-NatashaNeural` a voz.   |
| `1.2.0-amd64-en-ca-claraneural-preview`     | Imagem de recipiente com o `en-CA` local e `en-CA-ClaraNeural` a voz.     |
| `1.2.0-amd64-en-gb-libbyneural-preview`     | Imagem de recipiente com o `en-GB` local e `en-GB-LibbyNeural` a voz.     |
| `1.2.0-amd64-en-gb-mianeural-preview`       | Imagem de recipiente com o `en-GB` local e `en-GB-MiaNeural` a voz.       |
| `1.2.0-amd64-en-us-arianeural-preview`      | Imagem de recipiente com o `en-US` local e `en-US-AriaNeural` a voz.      |
| `1.2.0-amd64-en-us-guyneural-preview`       | Imagem de recipiente com o `en-US` local e `en-US-GuyNeural` a voz.       |
| `1.2.0-amd64-es-es-elviraneural-preview`    | Imagem de recipiente com o `es-ES` local e `es-ES-ElviraNeural` a voz.    |
| `1.2.0-amd64-es-mx-dalianeural-preview`     | Imagem de recipiente com o `es-MX` local e `es-MX-DaliaNeural` a voz.     |
| `1.2.0-amd64-fr-ca-sylvieneural-preview`    | Imagem de recipiente com o `fr-CA` local e `fr-CA-SylvieNeural` a voz.    |
| `1.2.0-amd64-fr-fr-deniseneural-preview`    | Imagem de recipiente com o `fr-FR` local e `fr-FR-DeniseNeural` a voz.    |
| `1.2.0-amd64-it-it-elsaneural-preview`      | Imagem de recipiente com o `it-IT` local e `it-IT-ElsaNeural` a voz.      |
| `1.2.0-amd64-ja-jp-nanamineural-preview`    | Imagem de recipiente com o `ja-JP` local e `ja-JP-NanamiNeural` a voz.    |
| `1.2.0-amd64-ko-kr-sunhineural-preview`     | Imagem de recipiente com o `ko-KR` local e `ko-KR-SunHiNeural` a voz.     |
| `1.2.0-amd64-pt-br-franciscaneural-preview` | Imagem de recipiente com o `pt-BR` local e `pt-BR-FranciscaNeural` a voz. |
| `1.2.0-amd64-zh-cn-xiaoxiaoneural-preview`  | Imagem de recipiente com o `zh-CN` local e `zh-CN-XiaoxiaoNeural` a voz.  |

## <a name="key-phrase-extraction"></a>Extração de Expressões-Chave

A imagem do recipiente [de extração de frase-chave][ta-kp] pode ser encontrada no sindicato do registo do `mcr.microsoft.com` contentor. Reside dentro do `azure-cognitive-services` repositório e é `keyphrase` nomeado. O nome de imagem do recipiente totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/keyphrase` .

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

A imagem do contentor [de deteção][ta-la] de linguagem pode ser encontrada no sindicato do `mcr.microsoft.com` registo do contentor. Reside dentro do `azure-cognitive-services` repositório e é `language` nomeado. O nome de imagem do recipiente totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/language` .

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

A imagem do contentor [de Análise de Sentimento][ta-se] pode ser encontrada no sindicato do registo do `mcr.microsoft.com` contentor. Reside dentro do `azure-cognitive-services` repositório e é `sentiment` nomeado. O nome de imagem do recipiente totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/sentiment` .

Esta imagem do recipiente tem as seguintes etiquetas disponíveis:

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
| `1.1.009301-amd64-preview`    | Análise de Sentimento v2      |
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
