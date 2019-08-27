---
title: Pull do Docker para o contêiner de Detecção de Idioma
titleSuffix: Azure Cognitive Services
description: Comando de pull do Docker para Detecção de Idioma contêiner
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/20/2019
ms.author: dapine
ms.openlocfilehash: da6f04f1042d1a02178f345c5fe67387ae0a7d0f
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051411"
---
## <a name="pull-the-language-detection-container"></a>Efetuar pull do contêiner de Detecção de Idioma

As imagens de contêiner para Análise de Texto estão disponíveis no registro de contêiner da Microsoft.

| Contentor | Registro de contêiner/repositório/nome da imagem |
|-----------|------------|
| Deteção de Idioma | `mcr.microsoft.com/azure-cognitive-services/language` |

Use o [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) comando para baixar uma imagem de contêiner do registro de contêiner da Microsoft.

Para obter uma descrição completa das marcas disponíveis para os contêineres de Análise de Texto, consulte o contêiner [detecção de idioma](https://go.microsoft.com/fwlink/?linkid=2018759) no Hub do Docker.


### <a name="docker-pull-for-the-language-detection-container"></a>Pull do Docker para o contêiner de Detecção de Idioma

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```
