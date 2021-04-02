---
title: Docker puxa para o recipiente de deteção de linguagem
titleSuffix: Azure Cognitive Services
description: Docker puxa o comando para o recipiente de deteção de linguagem
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 95bcb4b424010f63ac1ee4eb02f9e4793647051a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "90906091"
---
#### <a name="docker-pull-for-the-language-detection-container"></a>Docker puxa para o recipiente de deteção de linguagem

Utilize o [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) comando para descarregar uma imagem de contentor do Registo de Contentores da Microsoft.

Para obter uma descrição completa das etiquetas disponíveis para os recipientes Text Analytics, consulte o recipiente [de deteção de idiomas](https://go.microsoft.com/fwlink/?linkid=2018759) no Estivador.

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/language:latest
```
