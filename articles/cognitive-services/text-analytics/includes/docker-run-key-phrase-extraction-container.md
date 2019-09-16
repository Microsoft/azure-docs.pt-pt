---
title: Exemplo de execução de contêiner do comando de execução do Docker
titleSuffix: Azure Cognitive Services
description: Comando de execução do Docker para Extração de Frases-chave Contêiner
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: b8c3bdfc05e855139b595cd0ba7bd723cdeaee45
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966676"
---
Para executar o contêiner de *extração de frases-chave* , execute o `docker run` comando a seguir.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/keyphrase \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Este comando:

* Executa um contêiner de *extração de frases-chave* a partir da imagem de contêiner
* Aloca um núcleo de CPU e 4 gigabytes (GB) de memória
* Expõe a porta TCP 5000 e aloca um TTY pseudo para o contentor
* Remove automaticamente o contêiner depois que ele é encerrado. A imagem de contêiner ainda está disponível no computador host.