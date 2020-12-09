---
title: Cancelar operações de gestão
titleSuffix: Azure SQL Managed Instance
description: Saiba como cancelar as operações de gestão de instância gerida da Azure SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: urosmil
ms.author: urmilano
ms.reviewer: sstein, bonova, MashaMSFT
ms.date: 09/03/2020
ms.openlocfilehash: 342491178d55dacbdc68e6c9042623d381dff898
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861549"
---
# <a name="canceling-azure-sql-managed-instance-management-operations"></a>Cancelamento de operações de gestão de instância gerida da Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

A Azure SQL Managed Instance fornece a capacidade de cancelar algumas [operações de gestão,](management-operations-overview.md)como quando implementa uma nova instância gerida ou atualizar propriedades de instância. 

## <a name="overview"></a>Descrição geral

 Todas as operações de gestão podem ser categorizadas da seguinte forma:

- Implantação de instância (criação de novos casos).
- Atualização de instâncias (alterar propriedades de instância, tais como vCores ou armazenamento reservado).
- Eliminação de exemplos.

Pode [monitorizar o progresso e o estado das operações de gestão](management-operations-monitor.md) e cancelar algumas delas, se necessário. 

O quadro que se segue resume as operações de gestão, quer possa ou não cancelá-las, e a sua duração global típica:

Categoria  |Operação  |Cancelável  |Duração estimada do cancelamento  |
|---------|---------|---------|---------|
|Implementação |Criação de exemplos |Sim |90% das operações terminam em 5 minutos. |
|Atualizar |Armazenamento de instâncias para cima/para baixo (Final geral) |Não |  |
|Atualizar |Armazenamento de instâncias escalando para cima/para baixo (Business Critical) |Sim |90% das operações terminam em 5 minutos. |
|Atualizar |Cálculo de instância (vCores) escalando para cima e para baixo (Final geral) |Sim |90% das operações terminam em 5 minutos. |
|Atualizar |Cálculo de exemplo (vCores) escalando para cima e para baixo (Business Critical) |Sim |90% das operações terminam em 5 minutos. |
|Atualizar |Alteração do nível de serviço de instância (Final geral para Business Critical e vice-versa) |Sim |90% das operações terminam em 5 minutos. |
|Eliminar |Eliminação da instância |Não |  |
|Eliminar |Eliminação de clusters virtuais (como operação iniciada pelo utilizador) |Não |  |

## <a name="cancel-management-operation"></a>Cancelar operação de gestão

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para cancelar as operações de gestão utilizando o portal Azure, siga estes passos:

1. Vá ao [portal Azure](https://portal.azure.com)
1. Aceda à lâmina **de visão geral** da sua SQL Managed Instance. 
1. Selecione a caixa **de Notificação** ao lado da operação em curso para abrir a página **Operação Em curso.** 

   :::image type="content" source="media/management-operations-cancel/open-ongoing-operation.png" alt-text="Selecione a caixa de funcionamento em curso para abrir a página de funcionamento em curso.":::

1. Selecione **Cancelar a operação** na parte inferior da página. 

   :::image type="content" source="media/management-operations-cancel/cancel-operation.png" alt-text="Selecione cancelar para cancelar a operação.":::

1. Confirme que deseja cancelar a operação. 


Se o pedido de cancelamento for bem sucedido, a operação de gestão é cancelada e resulta em falha. Receberá uma notificação de que o cancelamento tem sucesso ou falha.

![Cancelamento do resultado da operação](./media/management-operations-cancel/canceling-operation-result.png)


Se o pedido de cancelamento falhar ou o botão de cancelamento não estiver ativo, significa que a operação de gestão entrou em estado não cancelável e que terminará em breve.  A operação de gestão continuará a sua execução até que esteja concluída.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Se ainda não tiver o Azure PowerShell instalado, consulte [instalar o módulo Azure PowerShell](/powershell/azure/install-az-ps).

Para cancelar uma operação de gestão, tem de especificar o nome da operação de gestão. Por isso, utilize primeiro o comando get para recuperar a lista de operação e, em seguida, cancele a operação específica.

```powershell-interactive
$managedInstance = "<Your-instance-name>"
$resourceGroup = "<Your-resource-group-name>"

$managementOperations = Get-AzSqlInstanceOperation -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup

foreach ($mo in $managementOperations ) {
    if($mo.State -eq "InProgress" -and $mo.IsCancellable){
        $cancelRequest = Stop-AzSqlInstanceOperation -ResourceGroupName $resourceGroup -ManagedInstanceName $managedInstance -Name $mo.Name
        Get-AzSqlInstanceOperation -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup -Name $mo.Name
    }
}
```

Para obter uma explicação detalhada dos comandos, consulte [a Get-AzSqlInstanceOperation](/powershell/module/az.sql/get-azsqlinstanceoperation) e [a Stop-AzSqlInstanceOperation](/powershell/module/az.sql/stop-azsqlinstanceoperation).

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Se ainda não tiver o Azure CLI instalado, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli).

Para cancelar a operação de gestão, tem de especificar o nome da operação de gestão. Por isso, utilize primeiro o comando get para recuperar a lista de operação e, em seguida, cancele a operação específica.

```azurecli-interactive
az sql mi op list -g yourResourceGroupName --mi yourInstanceName |
   --query "[?state=='InProgress' && isCancellable].{Name: name}" -o tsv |
while read -r operationName; do

az sql mi op cancel -g yourResourceGroupName --mi yourInstanceName -n $operationName
done
```

Para obter uma explicação detalhada dos comandos, consulte [az sql mi op](/cli/azure/sql/mi/op).

---

## <a name="canceled-deployment-request"></a>Pedido de implantação cancelado

Com a versão API 2020-02-02, assim que o pedido de criação de instância é aceite, o caso começa a existir como recurso, independentemente do progresso do processo de implantação (o estado de instância gerida é **Provisioning).** Se cancelar o pedido de implantação de instância (criação de nova instância), a instância gerida passará do Estado de **Provisionamento** para **OCreato Falhado.**

Os casos que não conseguiram criar ainda estão presentes como recurso e: 

- Não são cobrados
- Não conte para limites de recursos (sub-rede ou quota vCore)


> [!NOTE]
> Para minimizar o ruído na lista de recursos ou casos geridos, elimine casos que não tenham implementado ou casos com implementações canceladas. 


## <a name="next-steps"></a>Passos seguintes

- Para aprender a criar o seu primeiro exemplo gerido, consulte o [guia Quickstart](instance-create-quickstart.md).
- Para obter uma lista de funcionalidades e comparação, consulte [as funcionalidades SqL Comum](../database/features-comparison.md).
- Para obter mais informações sobre a configuração VNet, consulte [a configuração VNet de instância gerida SQL](connectivity-architecture-overview.md).
- Para um arranque rápido que cria uma instância gerida e restaura uma base de dados a partir de um ficheiro de backup, consulte [Criar uma instância gerida](instance-create-quickstart.md).
- Para obter um tutorial sobre a utilização do Serviço de Migração da Base de Dados Azure para migração, consulte [a migração de instâncias geridas SQL utilizando o Serviço de Migração de Bases de Dados.](../../dms/tutorial-sql-server-to-managed-instance.md)
