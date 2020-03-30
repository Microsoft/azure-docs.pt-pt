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
ms.openlocfilehash: ace22a6896a121f5cd8af838c7b0e427bd0287dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "67183941"
---
## <a name="enable-event-grid-resource-provider"></a>Ativar o fornecedor de recursos do Event Grid

Se ainda não tiver utilizado anteriormente o Event Grid na sua subscrição do Azure, precisa de registar o fornecedor de recursos do Event Grid. Execute o seguinte comando para registar o fornecedor:

```azurecli-interactive
az provider register --namespace Microsoft.EventGrid
```

Pode demorar algum tempo para o registo terminar. Para ver o estado, execute:

```azurecli-interactive
az provider show --namespace Microsoft.EventGrid --query "registrationState"
```

Quando `registrationState` está `Registered`, está pronto para continuar.