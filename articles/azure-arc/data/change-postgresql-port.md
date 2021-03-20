---
title: Alterar a porta PostgreSQL
description: Altere a porta na qual o grupo de servidores de hiperescala PostgreSQL ativado está a ouvir.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/02/2020
ms.topic: how-to
ms.openlocfilehash: 45424408c790e4921be48464818c55fe74313fd3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93328755"
---
# <a name="change-the-port-on-which-the-server-group-is-listening"></a>Altere a porta na qual o grupo de servidores está a ouvir 

Mudar a porta é uma operação de edição padrão do grupo de servidor. Para alterar a porta, executar o seguinte comando:
```console
 azdata arc postgres server edit -n <server group name> --port <desired port number>
```

Por exemplo, vamos assumir que o nome do seu grupo de servidor é _postgres01_ e gostaria que ouvisse na porta _866_. Executaria o seguinte comando:
```console
 azdata arc postgres server edit -n postgres01 --port 866
```

## <a name="verify-that-the-port-was-changed"></a>Verifique se a porta foi alterada

Para verificar se a porta foi alterada, execute o seguinte comando para mostrar a configuração do seu grupo de servidor:
```console
azdata arc postgres server show -n <server group name>
```

Na saída desse comando, veja o número de porta apresentado para o item "porta" na secção "serviço" das especificações do seu grupo de servidor.
Em alternativa, pode verificar no ponto externo do ponto de situação da secção de estado das especificações do seu grupo de servidor que o endereço IP é seguido pelo número de porta configurado.

Como ilustração, se continuarmos o exemplo acima, executará o comando:
```console
azdata arc postgres server show -n postgres01
```

e veria o porto 866 aqui referido:

```console
"service": {
      "port": 866,
      "type": "LoadBalancer"
    },
```
e aqui.

```console
"status": {
    "externalEndpoint": "12.678.345.09:866",
    "logSearchDashboard": "https://12.345.678.90:30777/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:postgres01'))",
    "metricsDashboard": "https://12.345.678.90:30777/grafana/d/postgres-metrics?var-Namespace=arc&var-Name=postgres01",
    "readyPods": "3/3",
    "state": "Ready"
  }
```
## <a name="next-steps"></a>Passos seguintes
- Leia sobre [como ligar-se ao seu grupo de servidores](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md).
- Leia sobre como pode configurar outros aspetos do seu grupo de servidor na secção Como gerir\Configure & secção de escala da documentação.
