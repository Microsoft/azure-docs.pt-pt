---
title: Docker puxa para o recipiente de Análise de Sentimento
titleSuffix: Azure Cognitive Services
description: Docker puxa comando para o recipiente de Análise de Sentimento
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 32a550e120331a8255281d51725d2d5fc8ca1e05
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564460"
---
#### <a name="docker-pull-for-the-sentiment-analysis-v3-container"></a>Docker puxa para o recipiente de Análise de Sentimento v3

O recipiente de análise de sentimento v3 está disponível em várias línguas. Para descarregar o recipiente para o recipiente inglês, utilize o comando abaixo. 

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-en
```

Para descarregar o recipiente para outro idioma, `en` substitua-o por um dos códigos linguísticos abaixo. 

| Recipiente de análise de texto | Código do idioma |
|--|--|
| Chinese-Simplified    |   `zh-hans`   |
| Chinese-Traditional   |   `zh-hant`   |
| Neerlandês                 |     `nl`      |
| Inglês               |     `en`      |
| Francês                |     `fr`      |
| Alemão                |     `de`      |
| Hindi                 |    `hi`       |
| Italiano               |     `it`      |
| Japonês              |     `ja`      |
| Coreano                |     `ko`      |
| Norueguês (Bokmål)   |     `no`      |
| Português (Brasil)   |    `pt-BR`    |
| Português (Portugal) |    `pt-PT`    |
| Espanhol               |     `es`      |
| Turco               |     `tr`      |

Para obter uma descrição completa das etiquetas disponíveis para os recipientes Text Analytics, consulte [Docker Hub](https://go.microsoft.com/fwlink/?linkid=2018654).
