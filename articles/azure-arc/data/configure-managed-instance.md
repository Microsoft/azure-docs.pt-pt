---
title: Configure Azure Arc permitiu que o SQL gerissem a instância
description: Configure Azure Arc permitiu que o SQL gerissem a instância
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: f687923f1185d848c2c4f2f40d949c71a7743d65
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90940633"
---
# <a name="configure-azure-arc-enabled-sql-managed-instance"></a>Configure Azure Arc permitiu que o SQL gerissem a instância

Este artigo explica como configurar a Azure Arc permitiu que o SQL gerisse a ocorrência.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="configure-resources-for-azure-arc-enabled-sql-managed-instance"></a>Configure Recursos para Azure Arc habilitado SQL Gestd Instance

### <a name="configure-using-azdata"></a>Configure usando azdata

Pode editar a configuração de Azure Arc ativado SQL Managed Instances com o `azdata` CLI. Executar o seguinte comando para ver opções de configuração. 

```
azdata arc sql mi edit --help
```

O exemplo a seguir define o núcleo do CPU e os pedidos e limites de memória.

```
azdata arc sql mi edit --cores-limit 4 --cores-request 2 --memory-limit 4Gi --memory-request 2Gi -n <NAME_OF_SQL_MI>
```

Para visualizar as alterações feitas à instância gerida pelo SQL, pode utilizar os seguintes comandos para visualizar o ficheiro yaml de configuração:

```
azdata arc sql mi show -n <NAME_OF_SQL_MI>
```

## <a name="configure-server-options"></a>Opções de Servidor de Configuração

Pode configurar as definições de configuração do servidor para Azure Arc ativado exemplo gerido pelo SQL após o tempo de criação. Este artigo descreve como configurar configurações como ativar ou desativar o agente mssql, permitir algugues de traços específicos para cenários de resolução de problemas.

Para alterar qualquer uma destas definições, siga estes passos:

1. Crie um ficheiro personalizado `mssql-custom.conf` que inclua definições direcionadas. O exemplo a seguir permite o Agente SQL e permite o traço da bandeira 1204.:

   ```
   [sqlagent]
   enabled=true
   
   [traceflag]
   traceflag0 = 1204
   ```

1. Copiar `mssql-custom.conf` o ficheiro para o recipiente na `/var/opt/mssql` `mssql-miaa` `master-0` cápsula. `<namespaceName>`Substitua-o pelo nome do cluster de dados grandes.

   ```bash
   kubectl cp mssql-custom.conf master-0:/var/opt/mssql/mssql-custom.conf -c mssql-server -n <namespaceName>
   ```

1. Reinicie a instância do Servidor SQL.  `<namespaceName>`Substitua-o pelo nome do cluster de dados grandes.

   ```bash
   kubectl exec -it master-0  -c mssql-server -n <namespaceName> -- /bin/bash
   supervisorctl restart mssql-server
   exit
   ```


**Limitações conhecidas**
- Os passos acima requerem permissões de administração de cluster Kubernetes
- Isto está sujeito a alterações ao longo da pré-visualização
