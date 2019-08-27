---
title: Pull do Docker para o contêiner de Extração de Frases-chave
titleSuffix: Azure Cognitive Services
description: Comando de pull do Docker para Extração de Frases-chave Contêiner
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/20/2019
ms.author: dapine
ms.openlocfilehash: e71db5455a3d6726cd6567d568a13fcdcf9cb520
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051251"
---
## <a name="pull-the-key-phrase-extraction-container"></a>Efetuar pull do contêiner de Extração de Frases-chave

As imagens de contêiner para Análise de Texto estão disponíveis no registro de contêiner da Microsoft.

| Contentor | Registro de contêiner/repositório/nome da imagem |
|-----------|------------|
| Extração de Expressões-Chave | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |

Use o [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) comando para baixar uma imagem de contêiner do registro de contêiner da Microsoft.

Para obter uma descrição completa das marcas disponíveis para os contêineres de Análise de Texto, consulte o contêiner [extração de frases-chave](https://go.microsoft.com/fwlink/?linkid=2018757) no Hub do Docker.

### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Pull do Docker para o contêiner de Extração de Frases-chave

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```
