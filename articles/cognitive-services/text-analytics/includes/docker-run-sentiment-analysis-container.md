---
title: Executar exemplo de recipiente de comando de execução de estivador
titleSuffix: Azure Cognitive Services
description: Docker executar comando para recipiente de análise de sentimento
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: f0e587fc39fa2cc6f5275ae16834372a206b37d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "70966823"
---
Para executar o recipiente de `docker run` Análise de *Sentimentos,* execute o seguinte comando.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/sentiment \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Este comando:

* Executa um recipiente de análise de *sentimento* a partir da imagem do recipiente
* Atribui um núcleo cpu e 4 gigabytes (GB) de memória
* Expõe a porta TCP 5000 e atribui um pseudo-TTY para o contentor
* Remove automaticamente o recipiente após a sua saída. A imagem do recipiente ainda está disponível no computador hospedeiro.