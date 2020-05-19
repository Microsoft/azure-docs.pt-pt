---
title: Executar exemplo de recipiente de comando de execução de estivador
titleSuffix: Azure Cognitive Services
description: Docker executar comando para recipiente de deteção de idiomas
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/29/2020
ms.author: aahi
ms.openlocfilehash: 41b926e3c44da85e7cb2c2e62405bd8f14488fbd
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588414"
---
Para executar o recipiente de *deteção* de idiomas, execute o seguinte `docker run` comando.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/language \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Este comando:

* Executa um recipiente de *deteção* de linguagem a partir da imagem do recipiente
* Atribui um núcleo cpu e 4 gigabytes (GB) de memória
* Expõe a porta TCP 5000 e atribui um pseudo-TTY para o contentor
* Remove automaticamente o recipiente após a sua saída. A imagem do recipiente ainda está disponível no computador hospedeiro.