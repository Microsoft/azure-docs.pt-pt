---
title: Docker puxa para o recipiente de extração de frase chave
titleSuffix: Azure Cognitive Services
description: Docker puxar o comando para o recipiente de extração de frase chave
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 02dde8a27b9687e58bf1a09c1a951f306937f0d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "90906088"
---
#### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Docker puxa para o recipiente de extração de frase chave

Utilize o [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) comando para descarregar uma imagem de contentor do Registo de Contentores da Microsoft.

Para obter uma descrição completa das etiquetas disponíveis para os recipientes Text Analytics, consulte o recipiente [de extração de frase-chave](https://go.microsoft.com/fwlink/?linkid=2018757) no estivador.

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase:latest
```
