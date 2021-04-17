---
author: baanders
description: incluir arquivo descrevendo uma solução simbólica para a limitação do inquilino cruzado com Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 4/13/2021
ms.author: baanders
ms.openlocfilehash: 5bc7e8af356ebe5968fe8a74783edc215788e5b4
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589361"
---
Uma maneira de fazê-lo é com o seguinte comando CLI, onde `<home-tenant-ID>` está a ID do inquilino AD Azure que contém a instância Azure Digital Twins:

```azurecli-interactive
az account get-access-token --tenant <home-tenant-ID> --resource https://digitaltwins.azure.net
```

Após a solicitação, a identidade receberá um símbolo emitido para o *https://digitaltwins.azure.net* recurso AZURE AD, que tem um pedido de identificação de inquilino correspondente à instância Azure Digital Twins. A utilização deste token em pedidos da API ou com o seu `Azure.Identity` código deverá permitir que a identidade federada aceda ao recurso Azure Digital Twins.