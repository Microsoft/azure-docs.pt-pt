---
title: incluir ficheiro
description: incluir ficheiro
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 08/17/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 579ea74c4767ba1afb6e0128c9f12ff83f7f3998
ms.sourcegitcommit: e7179fa4708c3af01f9246b5c99ab87a6f0df11c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/30/2020
ms.locfileid: "97826652"
---
## <a name="enable-the-event-grid-resource-provider"></a>Ativar o fornecedor de recursos da Grelha de Eventos

Se ainda não utilizou a Grade de Eventos na sua subscrição Azure, poderá ter de registar o fornecedor de recursos da Grelha de Eventos. Execute o seguinte comando para registar o fornecedor:

```azurecli-interactive
az provider register --namespace Microsoft.EventGrid
```

Pode levar um momento para o registo terminar. Para ver o estado, execute:

```azurecli-interactive
az provider show --namespace Microsoft.EventGrid --query "registrationState"
```

Quando `registrationState` está `Registered`, está pronto para continuar.
