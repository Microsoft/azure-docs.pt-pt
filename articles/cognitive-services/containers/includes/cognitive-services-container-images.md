---
title: Repositórios e imagens de contentores
services: cognitive-services
author: aahill
manager: nitinme
description: Duas tabelas que representam os registos de contentores, repositórios e nomes de imagem para todas as ofertas do Serviço Cognitivo.
ms.service: cognitive-services
ms.topic: include
ms.date: 04/24/2020
ms.author: aahi
ms.openlocfilehash: 9025e016725a966061c557f16b610d8897c04c11
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83590673"
---
### <a name="container-repositories-and-images"></a>Repositórios e imagens de contentores

As tabelas abaixo são uma lista das imagens de contentores disponíveis oferecidas pelos Serviços Cognitivos Azure. Para obter uma lista completa de todos os nomes de imagens de contentores disponíveis e das suas etiquetas disponíveis, consulte as etiquetas de imagem do [recipiente dos Serviços Cognitivos](../container-image-tags.md). 

#### <a name="generally-available"></a>Disponível em Geral 

O Registo de Contentores da Microsoft (MCR) sindica todos os recipientes geralmente disponíveis para serviços cognitivos. Os contentores também estão disponíveis diretamente a partir do centro de [Docker](https://hub.docker.com/_/microsoft-azure-cognitive-services).

#### <a name="luis"></a>[LUIS](#tab/luis)

| Contentor LUIS | Registo de Contentores / Repositório / Nome de imagem |
|--|--|
| LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |

Veja [Como correr e instalar recipientes LUIS](../../LUIS/luis-container-howto.md) para obter mais informações.

#### <a name="text-analytics"></a>[Análise de Texto](#tab/text-analytics)

| Recipiente de análise de texto | Registo de Contentores / Repositório / Nome de imagem |
|--|--|
| Análise de Sentimento v3 (Inglês) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-en` |
| Análise de Sentimento v3 (espanhol) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-es` |
| Análise de Sentimento v3 (francês) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-fr` |
| Análise de Sentimento v3 (italiano) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-it` |
| Análise de Sentimento v3 (alemão) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-de` |
| Análise de Sentimento v3 (chinês - simplificado) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-zh` |
| Análise de Sentimento v3 (chinês - tradicional) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-zht` |
| Análise de Sentimento v3 (Japonês) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-ja` |
| Análise de Sentimento v3 (Português) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-pt` |
| Análise de Sentimento v3 (Holandês) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-nl` |

Consulte [como executar e instalar recipientes De Análise](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) de Texto para obter mais informações.

---

#### <a name="public-ungated-preview-container-registry-mcrmicrosoftcom"></a>Pré-visualização pública "Ungated" (registo de contentores: `mcr.microsoft.com` )

Os seguintes recipientes de pré-visualização estão disponíveis publicamente. O Registo de Contentores da Microsoft (MCR) sindica todos os recipientes ungated publicamente disponíveis para serviços cognitivos. Os contentores também estão disponíveis diretamente a partir do centro de [Docker](https://hub.docker.com/_/microsoft-azure-cognitive-services).

| Serviço | Contentor | Registo de Contentores / Repositório / Nome de imagem |
|--|--|--|
| [Análise de Texto](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Extração de Expressões-Chave | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Análise de Texto](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Deteção de Idioma | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Detetor de anomalias](../../anomaly-detector/anomaly-detector-container-howto.md) | Detetor de Anomalias | `mcr.microsoft.com/azure-cognitive-services/anomaly-detector` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>Pré-visualização pública "Gated" (registo de contentores: `containerpreview.azurecr.io` )

Os seguintes recipientes de pré-visualização fechados estão alojados no registo de pré-visualização do contentor e requerem uma aplicação de acesso. Consulte o processo de [gating dos Serviços Cognitivos](../../cognitive-services-gating-process.md) para obter mais informações.

| Serviço | Contentor | Registo de Contentores / Repositório / Nome de imagem |
|--|--|--|
| [Imagem Digitalizada](../../Computer-vision/computer-vision-how-to-install-containers.md) | Leitura | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [Face](../../face/face-how-to-install-containers.md) | Face | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Reconhecimento de formulários](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Reconhecedor de Formato | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [API de Serviço de Voz](../../speech-service/speech-container-howto.md?tab=stt) | Conversão de voz em texto | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [API de Serviço de Voz](../../speech-service/speech-container-howto.md?tab=cstt) | Discurso personalizado a texto | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` |
| [API de Serviço de Voz](../../speech-service/speech-container-howto.md?tab=tts) | Conversão de texto em voz | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
| [API de Serviço de Voz](../../speech-service/speech-container-howto.md?tab=ctts) | Texto-a-fala personalizado | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` |
