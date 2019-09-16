---
title: Pull do Docker para o contêiner de Análise de Sentimento
titleSuffix: Azure Cognitive Services
description: Comando de pull do Docker para Análise de Sentimento contêiner
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: df00d469052fa30c3f2aaa5afe1881ef74587f9a
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966704"
---
#### <a name="docker-pull-for-the-sentiment-analysis-container"></a>Pull do Docker para o contêiner de Análise de Sentimento

Use o [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) comando para baixar uma imagem de contêiner do registro de contêiner da Microsoft.

Para obter uma descrição completa das marcas disponíveis para os contêineres de Análise de Texto, consulte o contêiner [análise de sentimento](https://go.microsoft.com/fwlink/?linkid=2018654) no Hub do Docker.

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```
