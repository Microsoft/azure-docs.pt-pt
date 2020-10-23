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
ms.openlocfilehash: 6e2443014f6788504a11784945078187a5a72de4
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92311129"
---
# <a name="configure-azure-arc-enabled-sql-managed-instance"></a>Configure Azure Arc permitiu que o SQL gerissem a instância

Este artigo explica como configurar a Azure Arc permitiu que o SQL gerisse a ocorrência.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="configure-resources"></a>Configure recursos

### <a name="configure-using-azure-data-cli-azdata"></a>Configurar usando [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]

Pode editar a configuração de Azure Arc ativado SQL Managed Instances com o [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] . Executar o seguinte comando para ver opções de configuração. 

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
