---
title: incluir ficheiro
description: incluir ficheiro
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 08/17/2018
ms.author: tomfitz
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 6c5ab187eb1307a95bbae4ff39d387221e094ea5
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92736873"
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
