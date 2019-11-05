---
title: Repositórios e imagens de contêiner
services: cognitive-services
author: IEvangelist
manager: nitinme
description: Duas tabelas que representam os registros de contêiner, repositórios e nomes de imagem para todas as ofertas de serviço cognitiva.
ms.service: cognitive-services
ms.topic: include
ms.date: 10/09/2019
ms.author: dapine
ms.openlocfilehash: c1593cb3dad7ee1370a66747fa3fe47e93c19957
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499168"
---
### <a name="container-repositories-and-images"></a>Repositórios e imagens de contêiner

As tabelas a seguir são uma lista abrangente das imagens de contêiner disponíveis oferecidas pelos serviços cognitivas do Azure.

#### <a name="public-ungated-container-registry-mcrmicrosoftcom"></a>"Não restrito" público (registro de contêiner: `mcr.microsoft.com`)

O registro de contêiner da Microsoft hospeda todos os contêineres "não restritos" publicamente disponíveis para serviços cognitivas.

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
| [Sorridente](../../face/face-how-to-install-containers.md) | Rostos | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Reconhecedor de formulário](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Reconhecedor de Formato | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
< < < < < < < HEAD | [API do serviço de fala](../../speech-service/speech-container-howto.md?tab=stt) | Conversão de fala em texto | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` | | [API do serviço de fala](../../speech-service/speech-container-howto.md?tab=cstt) | Fala Personalizada para texto | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` | | [API do serviço de fala](../../speech-service/speech-container-howto.md?tab=tts) | Conversão de texto em fala | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` | | [API do serviço de fala](../../speech-service/speech-container-howto.md?tab=ctts) | Conversão de texto em fala personalizada | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` | ======= | [API do serviço de fala](../../speech-service/speech-container-howto.md) | Conversão de fala em texto | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` | | [API do serviço de fala](../../speech-service/speech-container-howto.md) | Conversão de texto em fala | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` | | [Tradução de texto](../../translator/how-to-install-containers.md) | Tradução de Texto | `containerpreview.azurecr.io/microsoft/cognitive-services-translator-text` |
>>>>>>> refs/remotos/MicrosoftDocs/mestre
