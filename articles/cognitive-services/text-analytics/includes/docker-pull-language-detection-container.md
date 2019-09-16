---
title: Pull do Docker para o contêiner de Detecção de Idioma
titleSuffix: Azure Cognitive Services
description: Comando de pull do Docker para Detecção de Idioma contêiner
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: 23669a5440799ff071b3a30e3e41ab5d49f212df
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966717"
---
#### <a name="docker-pull-for-the-language-detection-container"></a>Pull do Docker para o contêiner de Detecção de Idioma

Use o [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) comando para baixar uma imagem de contêiner do registro de contêiner da Microsoft.

Para obter uma descrição completa das marcas disponíveis para os contêineres de Análise de Texto, consulte o contêiner [detecção de idioma](https://go.microsoft.com/fwlink/?linkid=2018759) no Hub do Docker.

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```
