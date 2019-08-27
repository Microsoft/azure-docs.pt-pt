---
title: Pull do Docker para o contêiner de Análise de Sentimento
titleSuffix: Azure Cognitive Services
description: Comando de pull do Docker para Análise de Sentimento contêiner
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/20/2019
ms.author: dapine
ms.openlocfilehash: 0954ef88f6917159156fbb73103b7b7af3283c00
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051371"
---
## <a name="pull-the-sentiment-analysis-container"></a>Efetuar pull do contêiner de Análise de Sentimento

As imagens de contêiner para Análise de Texto estão disponíveis no registro de contêiner da Microsoft.

| Contentor | Registro de contêiner/repositório/nome da imagem |
|-----------|------------|
| Análise de Sentimentos | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

Use o [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) comando para baixar uma imagem de contêiner do registro de contêiner da Microsoft.

Para obter uma descrição completa das marcas disponíveis para os contêineres de Análise de Texto, consulte o contêiner [análise de sentimento](https://go.microsoft.com/fwlink/?linkid=2018654) no Hub do Docker.

### <a name="docker-pull-for-the-sentiment-analysis-container"></a>Pull do Docker para o contêiner de Análise de Sentimento

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```
