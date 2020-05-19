---
title: Docker puxar para o recipiente de análise de sentimento
titleSuffix: Azure Cognitive Services
description: Docker puxar comando para recipiente de análise de sentimento
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: a3db0e2ffdd4a75f02634ca2227c3c41416d4f65
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588413"
---
#### <a name="docker-pull-for-the-sentiment-analysis-v3-container"></a>Docker puxar para o recipiente De Análise de Sentimento v3

O recipiente v3 de análise de sentimentos está disponível em várias línguas. Para descarregar o recipiente para o recipiente inglês, utilize o comando abaixo. 

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-en
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