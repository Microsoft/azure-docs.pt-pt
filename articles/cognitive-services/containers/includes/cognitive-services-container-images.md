---
title: Repositórios e imagens de contentores
services: cognitive-services
author: aahill
manager: nitinme
description: Duas tabelas que representam os registos de contentores, repositórios e nomes de imagem para todas as ofertas do Serviço Cognitivo.
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: a854a090af908da691e9b26f5b0714c6560fc0ba
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80876841"
---
### <a name="container-repositories-and-images"></a>Repositórios e imagens de contentores

As tabelas abaixo são uma lista das imagens de contentores disponíveis oferecidas pelos Serviços Cognitivos Azure. Para obter uma lista completa de todos os nomes de imagens de contentores disponíveis e das suas etiquetas disponíveis, consulte as etiquetas de imagem do [recipiente dos Serviços Cognitivos](../container-image-tags.md). Atualmente, não existem recipientes de Serviços Cognitivos que estejam geralmente disponíveis (GA). Por enquanto, até que sejam feitos mais anúncios *Public Ungated* -- os contentores estão disponíveis como *Pré-visualização*pública ou fechada pública .

 - *Public Ungated*: os contentores estão disponíveis publicamente sem um mecanismo de marcha.
 - *Visualização fechada pública*: os contentores estão disponíveis publicamente, mas primeiro exigem um pedido formal de acesso ao registo do contentor.

#### <a name="public-ungated-container-registry-mcrmicrosoftcom"></a>Público "Ungated" (registo `mcr.microsoft.com`de contentores: )

O Registo de Contentores da Microsoft (MCR) sindicaliza todos os recipientes "ungated" publicamente disponíveis para serviços cognitivos. Os contentores também estão disponíveis diretamente a partir do centro de [Docker](https://hub.docker.com/_/microsoft-azure-cognitive-services).

| Serviço | Contentor | Registo de Contentores / Repositório / Nome de imagem |
|--|--|--|
| [LUIS](../../LUIS/luis-container-howto.md) | LUIS | `mcr.microsoft.com/azure-cogni'ive-services/luis` |
| [Análise de Texto](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Extração de Expressões-Chave | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Análise de Texto](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Deteção de Idioma | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Análise de Texto](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Análise de Sentimentos | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>Pré-visualização pública "Gated" `containerpreview.azurecr.io`(registo de contentores: )

O registo de pré-visualização de contentores acolhe todos os recipientes "fechados" publicamente disponíveis para serviços cognitivos. Estes contentores exigem um pedido formal de acesso através do seu registo de contentores.

| Serviço | Contentor | Registo de Contentores / Repositório / Nome de imagem |
|--|--|--|
| [Detetor de anomalias](../../anomaly-detector/anomaly-detector-container-howto.md) | Detetor de Anomalias | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector` |
| [Imagem Digitalizada](../../Computer-vision/computer-vision-how-to-install-containers.md) | Leitura | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [Rostos](../../face/face-how-to-install-containers.md) | Rostos | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Reconhecimento de formulários](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Reconhecedor de Formato | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [API de Serviço de Voz](../../speech-service/speech-container-howto.md?tab=stt) | Conversão de voz em texto | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [API de Serviço de Voz](../../speech-service/speech-container-howto.md?tab=cstt) | Discurso personalizado a texto | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` |
| [API de Serviço de Voz](../../speech-service/speech-container-howto.md?tab=tts) | Conversão de texto em voz | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
| [API de Serviço de Voz](../../speech-service/speech-container-howto.md?tab=ctts) | Texto-a-fala personalizado | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` |
