---
title: Executar exemplo de recipiente de comando de execução de estivador
titleSuffix: Azure Cognitive Services
description: Docker executar comando para recipiente de extração de frase seletiva
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 8ed42d5b0461b6273c2f8d84a267b65461d160ef
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80877115"
---
Para executar o recipiente de extração de *frases-chave,* execute o seguinte `docker run` comando.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/keyphrase \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Este comando:

* Executa um recipiente de extração de *frases-chave* a partir da imagem do recipiente
* Atribui um núcleo cpu e 4 gigabytes (GB) de memória
* Expõe a porta TCP 5000 e atribui um pseudo-TTY para o contentor
* Remove automaticamente o recipiente após a sua saída. A imagem do recipiente ainda está disponível no computador hospedeiro.