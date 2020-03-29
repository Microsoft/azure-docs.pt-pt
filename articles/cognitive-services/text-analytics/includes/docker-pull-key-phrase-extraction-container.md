---
title: Docker puxar para o recipiente de extração de frase chave
titleSuffix: Azure Cognitive Services
description: Docker puxar comando para recipiente de extração de frase chave
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: 59313528e4beb343f9ac1103d3f9f2faae5dfc16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "70966761"
---
#### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Docker puxar para o recipiente de extração de frase chave

Utilize [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) o comando para descarregar uma imagem de contentor do Registo de Contentores da Microsoft.

Para obter uma descrição completa das etiquetas disponíveis para os recipientes Text Analytics, consulte o recipiente de extração de [frases-chave](https://go.microsoft.com/fwlink/?linkid=2018757) no Centro de Docker.

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```
