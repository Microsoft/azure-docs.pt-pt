---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 08/17/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: b48e5e28b15d17796138d493a7d9c628791df2e6
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "87830138"
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