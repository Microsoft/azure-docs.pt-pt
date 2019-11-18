---
title: Repositórios e imagens de contêiner
services: cognitive-services
author: IEvangelist
manager: nitinme
description: Duas tabelas que representam os registros de contêiner, repositórios e nomes de imagem para todas as ofertas de serviço cognitiva.
ms.service: cognitive-services
ms.topic: include
ms.date: 11/15/2019
ms.author: dapine
ms.openlocfilehash: 2058dd6e52ddb417e24368b27384df9a222c378e
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2019
ms.locfileid: "74142213"
---
### <a name="container-repositories-and-images"></a>Repositórios e imagens de contêiner

As tabelas a seguir são uma lista das imagens de contêiner disponíveis oferecidas pelos serviços cognitivas do Azure. Para obter uma lista completa de todos os nomes de imagem de contêiner disponíveis e suas marcas disponíveis, consulte [marcas de imagem de contêiner de serviços cognitivas](../container-image-tags.md).

#### <a name="public-ungated-container-registry-mcrmicrosoftcom"></a>"Não restrito" público (registro de contêiner: `mcr.microsoft.com`)

O MCR (registro de contêiner da Microsoft) distribui todos os contêineres "não restritos" publicamente disponíveis para serviços cognitivas. Eles também estão disponíveis diretamente do [Hub do Docker](https://hub.docker.com/_/microsoft-azure-cognitive-services).

| Serviço | Contentor | Registro de contêiner/repositório/nome da imagem |
|--|--|--|
| [LUIS](../../LUIS/luis-container-howto.md) | LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |
| [Análise de Texto](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Extração de Expressões-Chave | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Análise de Texto](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Deteção de Idioma | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Análise de Texto](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Análise de Sentimentos | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>Visualização pública "restrita" (registro de contêiner: `containerpreview.azurecr.io`)

O registro de visualização de contêiner hospeda todos os contêineres "restritos" publicamente disponíveis para serviços cognitivas. Esses contêineres exigem uma solicitação formal para acesso a fim de consumi-los.

| Serviço | Contentor | Registro de contêiner/repositório/nome da imagem |
|--|--|--|
| [Detetor de anomalias](../../anomaly-detector/anomaly-detector-container-howto.md) | Detetor de Anomalias | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector` |
| [Imagem Digitalizada](../../Computer-vision/computer-vision-how-to-install-containers.md) | Leitura | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [Face](../../face/face-how-to-install-containers.md) | Rostos | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Reconhecedor de formulário](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Reconhecedor de Formato | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [API de Serviço de Voz](../../speech-service/speech-container-howto.md?tab=stt) | Conversão de voz em texto | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [API de Serviço de Voz](../../speech-service/speech-container-howto.md?tab=cstt) | Fala Personalizada para texto | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` |
| [API de Serviço de Voz](../../speech-service/speech-container-howto.md?tab=tts) | Conversão de texto em voz | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
| [API de Serviço de Voz](../../speech-service/speech-container-howto.md?tab=ctts) | Conversão de texto em fala personalizada | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` |
