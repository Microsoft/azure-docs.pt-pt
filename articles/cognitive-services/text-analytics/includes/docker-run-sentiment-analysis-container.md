---
title: Executar exemplo de recipiente de comando de execução de estivador
titleSuffix: Azure Cognitive Services
description: Docker executar comando para recipiente de análise de sentimento
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: e67f65d252be0ea638d3b5fa241d9413e76f1a98
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80877055"
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