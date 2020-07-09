---
title: Docker puxa para o recipiente de saúde
titleSuffix: Azure Cognitive Services
description: Docker puxa comando para Text Analytics para recipiente de saúde
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 4c33952ed56399b94a7db1088cb60919a4f88137
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108986"
---
Preencha e envie o formulário de [pedido de solicitação dos recipientes dos Serviços Cognitivos](https://aka.ms/cognitivegate) para solicitar o acesso ao contentor.

[!INCLUDE [Request access to the container registry](../../../../includes/cognitive-services-containers-request-access-only.md)]

Utilize o comando de login do estivador com credenciais fornecidas no seu email de embarque para ligar ao nosso registo privado de contentores para recipientes de Serviços Cognitivos.

```bash
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Use o [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) comando para descarregar esta imagem do contentor do nosso registo privado de contentores.

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest
```
