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
ms.openlocfilehash: 549ecf940c53267db6b3caa576c4c24db414337a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90906122"
---
#### <a name="docker-pull-for-the-sentiment-analysis-v3-container"></a>Docker puxa para o recipiente de Análise de Sentimento v3

O recipiente de análise de sentimento v3 está disponível em várias línguas. Para descarregar o recipiente para o recipiente inglês, utilize o comando abaixo. 

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-en
```

Para descarregar o recipiente para outro idioma, `en` substitua-o por um dos códigos linguísticos abaixo. 

| Recipiente de análise de texto | Código do idioma |
|--|--|
| Inglês | `en` |
| Espanhol | `es` |
| Francês | `fr` |
| Italiano | `it` |
| Alemão | `de` |
| Chinês - simplificado | `zh` |
| Chinês - tradicional | `zht` |
| Japonês | `ja` |
| Português | `pt` |
| Neerlandês | `nl` |

Para obter uma descrição completa das etiquetas disponíveis para os recipientes Text Analytics, consulte [Docker Hub](https://go.microsoft.com/fwlink/?linkid=2018654).