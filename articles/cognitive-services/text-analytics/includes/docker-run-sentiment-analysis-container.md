---
title: Executar exemplo de recipiente de estivador executar comando
titleSuffix: Azure Cognitive Services
description: Docker executar comando para o recipiente de Análise de Sentimento
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/29/2020
ms.author: aahi
ms.openlocfilehash: 459ab7a254994929174e4f81ad25f0729f43fa0b
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108975"
---
Para executar o recipiente *Análise de Sentimento v3,* execute o seguinte `docker run` comando. Substitua os espaços reservados abaixo pelos seus próprios valores:

| Marcador de posição | Valor | Formato ou exemplo |
|-------------|-------|---|
| **{API_KEY}** | A chave para o seu recurso Text Analytics. Pode encontrá-lo na página **chave e ponta final** do seu recurso, no portal Azure. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | O ponto final para aceder à API de Análise de Texto. Pode encontrá-lo na página **chave e ponta final** do seu recurso, no portal Azure. | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |

```bash
docker run --rm -it -p 5000:5000 --memory 8g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/sentiment \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Este comando:

* Executa um recipiente de Análise de *Sentimento* a partir da imagem do recipiente
* Atribui um núcleo de CPU e 8 gigabytes (GB) de memória
* Expõe a porta TCP 5000 e atribui uma pseudo-TTY para o contentor
* Remove automaticamente o recipiente depois de sair. A imagem do recipiente ainda está disponível no computador anfitrião.