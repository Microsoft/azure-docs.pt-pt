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
ms.openlocfilehash: e502b886c8031174180d40f1c5b7e373a974ee8e
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877064"
---
#### <a name="docker-pull-for-the-sentiment-analysis-container"></a>Docker puxar para o recipiente de análise de sentimento

Utilize [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) o comando para descarregar uma imagem de contentor do Registo de Contentores da Microsoft.

Para obter uma descrição completa das etiquetas disponíveis para os recipientes Text Analytics, consulte o recipiente de Análise de [Sentimentos](https://go.microsoft.com/fwlink/?linkid=2018654) no Centro de Docker.

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```
