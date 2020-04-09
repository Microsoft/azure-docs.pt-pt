---
title: Docker puxar para o recipiente de extração de frase chave
titleSuffix: Azure Cognitive Services
description: Docker puxar comando para recipiente de extração de frase chave
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: db5d0cfecde32d3dd6afe181d67a6ce6c0826eda
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877124"
---
#### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Docker puxar para o recipiente de extração de frase chave

Utilize [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) o comando para descarregar uma imagem de contentor do Registo de Contentores da Microsoft.

Para obter uma descrição completa das etiquetas disponíveis para os recipientes Text Analytics, consulte o recipiente de extração de [frases-chave](https://go.microsoft.com/fwlink/?linkid=2018757) no Centro de Docker.

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```
