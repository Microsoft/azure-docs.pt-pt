---
title: Repositórios e imagens de contentores
services: cognitive-services
author: aahill
manager: nitinme
description: Duas tabelas que representam os registos de contentores, repositórios e nomes de imagem para todas as ofertas do Serviço Cognitivo.
ms.service: cognitive-services
ms.topic: include
ms.date: 09/03/2020
ms.author: aahi
ms.openlocfilehash: 3f2611c9535b9721cccadf35e56bdd21a3020257
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92677440"
---
### <a name="container-repositories-and-images"></a>Repositórios e imagens de contentores

As tabelas abaixo são uma lista das imagens de contentores disponíveis oferecidas pela Azure Cognitive Services. Para obter uma lista completa de todos os nomes de imagem dos contentores disponíveis e as suas etiquetas disponíveis, consulte [as etiquetas de imagem dos serviços cognitivos](../container-image-tags.md). 

#### <a name="generally-available"></a>Disponível em Geral 

O Microsoft Container Registry (MCR) sindicaliza todos os contentores geralmente disponíveis para serviços cognitivos. Os contentores também estão disponíveis diretamente a partir do [centro do Docker.](https://hub.docker.com/_/microsoft-azure-cognitive-services)

**LUIS**

| Contentor | Registo de contentores / Repositório / Nome da imagem |
|--|--|
| LUIS | `mcr.microsoft.com/azure-cognitive-services/language/luis` |

Veja [como executar e instalar recipientes LUIS](../../LUIS/luis-container-howto.md) para obter mais informações.

**Análise de Texto**

| Contentor | Registo de contentores / Repositório / Nome da imagem |
|--|--|
| Análise de Sentimento v3 (Inglês) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-en` |
| Análise de Sentimento v3 (Espanhol) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-es` |
| Análise de Sentimento v3 (francês) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-fr` |
| Análise de Sentimento v3 (Italiano) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-it` |
| Análise de Sentimento v3 (alemão) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-de` |
| Análise de Sentimento v3 (chinês - simplificado) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-zh` |
| Análise de Sentimento v3 (chinês - tradicional) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-zht` |
| Análise de Sentimento v3 (Japonês) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-ja` |
| Análise de Sentimento v3 (Português) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-pt` |
| Análise de Sentimento v3 (Holandês) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-nl` |

Veja [como executar e instalar recipientes Text Analytics](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) para obter mais informações.

**Detetor de Anomalias** 

| Contentor | Registo de contentores / Repositório / Nome da imagem |
|--|--|
| Detetor de anomalias | `mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector` |

Consulte [como executar e instalar recipientes de detetores de anomalias](../../anomaly-detector/anomaly-detector-container-howto.md) para obter mais informações.

**Serviço de Fala**

> [!NOTE]
> Para utilizar os recipientes de discurso, terá de preencher um [formulário de pedido on-line](https://aka.ms/csgate).

| Contentor | Registo de contentores / Repositório / Nome da imagem |
|--|--|
| [Conversão de voz em texto](../../speech-service/speech-container-howto.md?tab=stt) | `mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text` |
| [Discurso-a-texto personalizado](../../speech-service/speech-container-howto.md?tab=cstt) | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text` |
| [Conversão de texto em voz](../../speech-service/speech-container-howto.md?tab=tts) | `mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech` |

#### <a name="ungated-preview"></a>Pré-visualização "Ungated" 

Os seguintes recipientes de pré-visualização estão disponíveis publicamente. O Microsoft Container Registry (MCR) sindicalmente todos os contentores não comprados publicamente para serviços cognitivos. Os contentores também estão disponíveis diretamente a partir do [centro do Docker.](https://hub.docker.com/_/microsoft-azure-cognitive-services)

| Serviço | Contentor | Registo de contentores / Repositório / Nome da imagem |
|--|--|--|
| [Análise de Texto](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Extração de Expressões-Chave | `mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase` |
| [Análise de Texto](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Deteção de Idioma | `mcr.microsoft.com/azure-cognitive-services/textanalytics/language` |


#### <a name="gated-preview"></a>Pré-visualização "Gated"

Anteriormente, os recipientes de pré-visualização fechados estavam alojados no `containerpreview.azurecr.io` repositório. A partir de 22 de setembro de 2020, estes contentores (exceto Text Analytics for health) estão hospedados no Microsoft Container Registry (MCR), e o descarregamento não requer a utilização do comando de login do estivador. Para utilizar o recipiente, terá de:

1. Preencha um [formulário de pedido](https://aka.ms/csgate) com o seu ID de subscrição Azure e cenário de utilizador. 
2. Após aprovação, descarregue o contentor do MCR. 
3. Utilize a chave e o ponto final a partir de um recurso Azure apropriado para autenticar o recipiente em tempo de funcionamento. 

| Serviço | Contentor | Registo de contentores / Repositório / Nome da imagem |
|--|--|--|
| [Imagem Digitalizada](../../Computer-vision/computer-vision-how-to-install-containers.md) | Ler v2.0 | `mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview` |
| [Imagem Digitalizada](../../Computer-vision/computer-vision-how-to-install-containers.md) | Ler v3.1 | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview` |
| [Imagem Digitalizada](https://docs.microsoft.com/azure/cognitive-services/computer-vision/spatial-analysis-container) | Análise Espacial | `mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis` |
| [API de Serviço de Voz](../../speech-service/speech-container-howto.md?tab=ctts) | Texto-a-discurso personalizado | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech` |
| [API de Serviço de Voz](../../speech-service/speech-container-howto.md?tab=lid) | Deteção de Idioma | `mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection` |
| [API de Serviço de Voz](../../speech-service/speech-container-howto.md?tab=ntts) | Texto-a-discurso neural | `mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech` |
| [Análise de texto para a saúde](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=health) | Análise de Texto para a saúde | `containerpreview.azurecr.io/microsoft/cognitive-services-healthcare` |

