---
title: Docker puxar para o recipiente de deteção de linguagem
titleSuffix: Azure Cognitive Services
description: Docker puxar comando para recipiente de deteção de linguagem
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 3444d6267c09056697abf8339cc688a606b6dc17
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80877104"
---
#### <a name="docker-pull-for-the-language-detection-container"></a>Docker puxar para o recipiente de deteção de linguagem

Utilize [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) o comando para descarregar uma imagem de contentor do Registo de Contentores da Microsoft.

Para obter uma descrição completa das etiquetas disponíveis para os recipientes Text Analytics, consulte o recipiente de deteção de [idiomas](https://go.microsoft.com/fwlink/?linkid=2018759) no Centro de Docker.

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```
