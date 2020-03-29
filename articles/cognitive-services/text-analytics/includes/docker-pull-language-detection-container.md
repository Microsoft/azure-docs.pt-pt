---
title: Docker puxar para o recipiente de deteção de linguagem
titleSuffix: Azure Cognitive Services
description: Docker puxar comando para recipiente de deteção de linguagem
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: 23669a5440799ff071b3a30e3e41ab5d49f212df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "70966717"
---
#### <a name="docker-pull-for-the-language-detection-container"></a>Docker puxar para o recipiente de deteção de linguagem

Utilize [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) o comando para descarregar uma imagem de contentor do Registo de Contentores da Microsoft.

Para obter uma descrição completa das etiquetas disponíveis para os recipientes Text Analytics, consulte o recipiente de deteção de [idiomas](https://go.microsoft.com/fwlink/?linkid=2018759) no Centro de Docker.

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```
