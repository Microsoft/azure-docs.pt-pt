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
ms.openlocfilehash: d285b48606485fbd7f53511a15be1e2a31791829
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "102245090"
---
> [!NOTE]
> O comando `az webapp up` executa as seguintes ações:
>
>- Criar um grupo de [recursos](/cli/azure/group#az-group-create)predefinidos .
>
>- Crie um plano de [serviço de aplicações predefinido.](/cli/azure/appservice/plan#az-appservice-plan-create)
>
>- [Crie uma aplicação](/cli/azure/webapp#az-webapp-create) com o nome especificado.
>
>- [Zip implementa](../articles/app-service/deploy-zip.md) ficheiros do diretório de trabalho atual para a aplicação.
>