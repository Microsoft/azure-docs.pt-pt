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
ms.openlocfilehash: ac8e49c85147576e489b16a3a421c75d60ad4aee
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86104419"
---
### <a name="container-repositories-and-images"></a>Repositórios e imagens de contentores

As tabelas abaixo são uma lista das imagens de contentores disponíveis oferecidas pela Azure Cognitive Services. Para obter uma lista completa de todos os nomes de imagem dos contentores disponíveis e as suas etiquetas disponíveis, consulte [as etiquetas de imagem dos serviços cognitivos](../container-image-tags.md). 

#### <a name="generally-available"></a>Disponível em Geral 

O Microsoft Container Registry (MCR) sindicaliza todos os contentores geralmente disponíveis para serviços cognitivos. Os contentores também estão disponíveis diretamente a partir do [centro do Docker.](https://hub.docker.com/_/microsoft-azure-cognitive-services)

#### <a name="luis"></a>[LUIS](#tab/luis)

| Recipiente LUIS | Registo de contentores / Repositório / Nome da imagem |
|--|--|
| LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |

Veja [como executar e instalar recipientes LUIS](../../LUIS/luis-container-howto.md) para obter mais informações.

#### <a name="text-analytics"></a>[Análise de Texto](#tab/text-analytics)

| Recipiente de análise de texto | Registo de contentores / Repositório / Nome da imagem |
|--|--|
| Análise de Sentimento v3 (Inglês) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-en` |
| Análise de Sentimento v3 (Espanhol) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-es` |
| Análise de Sentimento v3 (francês) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-fr` |
| Análise de Sentimento v3 (Italiano) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-it` |
| Análise de Sentimento v3 (alemão) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-de` |
| Análise de Sentimento v3 (chinês - simplificado) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-zh` |
| Análise de Sentimento v3 (chinês - tradicional) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-zht` |
| Análise de Sentimento v3 (Japonês) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-ja` |
| Análise de Sentimento v3 (Português) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-pt` |
| Análise de Sentimento v3 (Holandês) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-nl` |

Veja [como executar e instalar recipientes Text Analytics](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) para obter mais informações.

---

#### <a name="public-ungated-preview-container-registry-mcrmicrosoftcom"></a>Visualização pública "Ungated" (registo de contentores: `mcr.microsoft.com` )

Os seguintes recipientes de pré-visualização estão disponíveis publicamente. O Microsoft Container Registry (MCR) sindicalmente todos os contentores não comprados publicamente para serviços cognitivos. Os contentores também estão disponíveis diretamente a partir do [centro do Docker.](https://hub.docker.com/_/microsoft-azure-cognitive-services)

| Serviço | Contentor | Registo de contentores / Repositório / Nome da imagem |
|--|--|--|
| [Análise de Texto](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Extração de Expressões-Chave | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Análise de Texto](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Deteção de Idioma | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Detetor de anomalias](../../anomaly-detector/anomaly-detector-container-howto.md) | Detetor de Anomalias | `mcr.microsoft.com/azure-cognitive-services/anomaly-detector` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>Visualização pública "Gated" (registo do contentor: `containerpreview.azurecr.io` )

Os seguintes recipientes de pré-visualização fechados estão alojados no registo de pré-visualização do contentor e exigem uma aplicação de acesso. Consulte os seguintes artigos de recipiente para obter mais informações.

| Serviço | Contentor | Registo de contentores / Repositório / Nome da imagem |
|--|--|--|
| [Visão computacional](../../Computer-vision/computer-vision-how-to-install-containers.md) | Leitura | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [Face](../../face/face-how-to-install-containers.md) | Face | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Reconhecedor de formulários](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Reconhecedor de Formato | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [API de Serviço de Voz](../../speech-service/speech-container-howto.md?tab=stt) | Conversão de voz em texto | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [API de Serviço de Voz](../../speech-service/speech-container-howto.md?tab=cstt) | Discurso-a-texto personalizado | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` |
| [API de Serviço de Voz](../../speech-service/speech-container-howto.md?tab=tts) | Conversão de texto em voz | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
| [API de Serviço de Voz](../../speech-service/speech-container-howto.md?tab=ctts) | Texto-a-discurso personalizado | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` |
| [Análise de texto para a saúde](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=health) | Análise de texto para a saúde | `containerpreview.azurecr.io/microsoft/cognitive-services-healthcare` |
