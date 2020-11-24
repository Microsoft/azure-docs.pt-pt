---
title: incluir ficheiro
description: incluir ficheiro
services: app-service
author: msangapu
ms.service: app-service
ms.topic: include
ms.date: 02/27/2019
ms.author: msangapu
ms.custom: include file
ms.openlocfilehash: 08458bd170707b28c69fdad1d8aa115a7ad245a5
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95556740"
---
> [!NOTE]
> O comando `az webapp up` executa as seguintes ações:
>
>- Criar um grupo de [recursos](/cli/azure/group?view=azure-cli-latest#az-group-create)predefinidos .
>
>- Crie um plano de [serviço de aplicações predefinido.](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create)
>
>- [Crie uma aplicação](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) com o nome especificado.
>
>- [Zip implementa](../articles/app-service/deploy-zip.md) ficheiros do diretório de trabalho atual para a aplicação.
>