---
title: Executar exemplo de recipiente de estivador executar comando
titleSuffix: Azure Cognitive Services
description: Docker executar comando para recipiente de deteção de linguagem
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/29/2020
ms.author: aahi
ms.openlocfilehash: f1f36cc730ceca9a961769775d17de6be8d26948
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906014"
---
Para executar o recipiente *de Deteção de Idiomas,* execute o seguinte `docker run` comando. Substitua os espaços reservados abaixo pelos seus próprios valores:

| Marcador de posição | Valor | Formato ou exemplo |
|-------------|-------|---|
| **{API_KEY}** | A chave para o seu recurso Text Analytics. Pode encontrá-lo na página **chave e ponta final** do seu recurso, no portal Azure. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | O ponto final para aceder à API de Análise de Texto. Pode encontrá-lo na página **chave e ponta final** do seu recurso, no portal Azure. | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |


```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/textanalytics/language \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Este comando:

* Executa um recipiente de deteção de *idiomas* a partir da imagem do recipiente
* Atribui um núcleo de CPU e 4 gigabytes (GB) de memória
* Expõe a porta TCP 5000 e atribui uma pseudo-TTY para o contentor
* Remove automaticamente o recipiente depois de sair. A imagem do recipiente ainda está disponível no computador anfitrião.