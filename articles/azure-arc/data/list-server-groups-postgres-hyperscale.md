---
title: Listar os grupos de servidores de hiperescala pós-escala do Arco Azure criados num Controlador de Dados do Arco Azure
description: Listar os grupos de servidores de hiperescala pós-escala do Arco Azure criados num Controlador de Dados do Arco Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 7577ca4b8a1d7db7ea99aadfef4fd2a445b66425
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "90940789"
---
# <a name="list-the-azure-arc-enabled-postgresql-hyperscale-server-groups-created-in-an-azure-arc-data-controller"></a>Listar os grupos de servidores de hiperescala pós-escala do Arco Azure criados num Controlador de Dados do Arco Azure

Este artigo explica como pode recuperar a lista de grupos de servidores criados no seu Controlador de Dados do Arco.

Para recuperar esta lista, utilize qualquer um dos seguintes métodos uma vez ligado ao Controlador de Dados do Arco:

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="from-cli-with-azdata"></a>Do CLI com azdata
O formato geral do comando é:
```console
azdata arc postgres server list
```

Devolverá uma saída como:
```console
Name        State    Workers
----------  -------  ---------
postgres01  Ready    2
postgres02  Ready    2
```
Para mais detalhes sobre os parâmetros disponíveis para este comando, corra:
```console
azdata arc postgres server list --help
```

## <a name="from-cli-with-kubectl"></a>De CLI com kubectl
Executar qualquer um dos seguintes comandos.

**Para listar os grupos de servidores independentemente da versão de Postgres, corra:**
```console
kubectl get postgresqls
```
Devolverá uma saída como:
```console
NAME                                             STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgresql-12.arcdata.microsoft.com/postgres01   Ready   3/3          10.0.0.4:30499      51s
postgresql-12.arcdata.microsoft.com/postgres02   Ready   3/3          10.0.0.4:31066      6d
```

**Para listar os grupos de servidores de uma versão específica de Postgres, corra:**
```console
kubectl get postgresql-12
```

Para listar os grupos de servidor que executam a versão 11 de Postgres, substitua _o postgresql-12_ por _postgresql-11_.

## <a name="next-steps"></a>Passos seguintes:

* [Leia o artigo sobre como obter os pontos finais de ligação e forme as cadeias de ligação para ligar ao seu grupo de servidor](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md)
* [Leia o artigo sobre a configuração de um grupo de servidores de hiperescala PósgreSQL](show-configuration-postgresql-hyperscale-server-group.md)
