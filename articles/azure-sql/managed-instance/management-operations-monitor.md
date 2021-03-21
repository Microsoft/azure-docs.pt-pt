---
title: Monitorizar operações de gestão
titleSuffix: Azure SQL Managed Instance
description: Conheça diferentes formas de monitorização das operações de gestão de instância gerida do Azure SQL.
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
ms.openlocfilehash: 6cdd3137798e221974dadda78dd55b6ae944bc78
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100589338"
---
# <a name="monitoring-azure-sql-managed-instance-management-operations"></a>Monitorização das operações de gestão de instâncias geridas Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

A Azure SQL Managed Instance fornece monitorização das operações de [gestão](management-operations-overview.md) que utiliza para implementar novos casos geridos, atualizar propriedades de instância ou apagar casos quando já não é necessário. 

## <a name="overview"></a>Descrição geral

Todas as operações de gestão podem ser categorizadas da seguinte forma:

- Implantação de instância (criação de novos casos).
- Atualização de instâncias (alterar propriedades de instância, tais como vCores ou armazenamento reservado).
- Eliminação de exemplos.

A maioria das operações de gestão são [operações de longa duração.](management-operations-overview.md#duration) Por conseguinte, é necessário monitorizar o estado ou acompanhar o progresso das etapas de operação. 

Existem várias formas de monitorizar as operações de gestão de instâncias geridas:

- [Implementações de grupos de recursos](../../azure-resource-manager/templates/deployment-history.md)
- [Registo de atividades](../../azure-monitor/essentials/activity-log.md)
- [Operações de caso gerido API](#managed-instance-operations-api)


O quadro a seguir compara as opções de monitorização da operação de gestão: 

| Opção | Retenção | Os suportes cancelam | Criar | Atualizar | Eliminar | Cancelar | Passos |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Implementações de grupos de recursos | Infinito<sup>1</sup> | Nº<sup>2</sup> | Visible | Visible | Não visível | Visible | Não visível |
| Registo de atividades | 90 dias | No | Visible | Visible | Visible | Visible |  Não visível |
| Operações de caso gerido API | 24 horas | [Sim](management-operations-cancel.md) | Visible | Visible | Visible | Visible | Visible |
|  |  |  |  |  |  |  | |

<sup>1</sup> O histórico de implantação de um grupo de recursos está limitado a 800 implementações.

<sup>2 As</sup> implementações do grupo de recursos suportam a operação de cancelamento. No entanto, devido à lógica de cancelamento, apenas uma operação programada para implantação após a ação de cancelamento será cancelada. A implementação em curso não é cancelada quando a implementação do grupo de recursos é cancelada. Uma vez que a implementação de instâncias geridas consiste num passo de longo prazo (do ponto de vista da Manger de Recursos Azure), cancelar a implementação do grupo de recursos não cancelará a implementação de instâncias geridas e a operação estará concluída. 

## <a name="managed-instance-operations-api"></a>Operações de caso gerido API

As APIs de operações de gestão são especialmente concebidas para monitorizar as operações. A monitorização das operações de instância geridas pode fornecer informações sobre os parâmetros de operação e as etapas de operação, bem como [cancelar operações específicas](management-operations-cancel.md). Além de detalhes de operação e cancelamento de comando, esta API pode ser usada em scripts de automação com implementações multi-recursos - com base no passo de progresso, você pode iniciar alguma implementação de recursos dependentes.

Estas são as APIs: 

| Comando | Descrição |
| --- | --- |
|[Operações de instância geridas - Obter](/rest/api/sql/managedinstanceoperations/get)|Obtém uma operação de gestão num caso gerido.|
|[Operações de Ocorrência Gerida - Cancelar](/rest/api/sql/managedinstanceoperations/cancel)|Cancela a operação assíncronea sobre a instância gerida.|
|[Operações de instância geridas - Lista por Instância Gerida](/rest/api/sql/managedinstanceoperations/listbymanagedinstance)|Obtém uma lista de operações realizadas na instância gerida.|

> [!NOTE]
> Utilize a versão API 2020-02-02 para ver a instância gerida criar operação na lista de operações. Esta é a versão padrão utilizada no portal Azure e nos mais recentes pacotes PowerShell e Azure CLI.

## <a name="monitor-operations"></a>Monitorizar operações

# <a name="portal"></a>[Portal](#tab/azure-portal)

No portal Azure, utilize a página **de visão geral** de instância gerida para monitorizar as operações de instância geridas. 

Por exemplo, a **operação Criar** é visível no início do processo de criação na página **'Vista Geral':** 

![Caso gerido cria progresso](./media/management-operations-monitor/monitoring-create-operation.png)

Selecione **a operação em curso** para abrir a página de **funcionamento em curso** e ver operações **criar** ou **atualizar.** Também pode [cancelar](management-operations-cancel.md) operações a partir desta página.  

![Detalhes da operação de instância gerida](./media/management-operations-monitor/monitoring-operation-details.png)

> [!NOTE]
> Criar operações submetidas através do portal Azure, PowerShell, Azure CLI ou outras ferramentas utilizando a versão REST API 2020-02-02 [podem ser canceladas](management-operations-cancel.md). As versões REST API com mais de 2020-02-02 utilizadas para submeter uma operação de criação iniciarão a implementação do caso, mas a implementação não será listada na API de Operações e não pode ser cancelada.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

O Get-AzSqlInstanceOperation cmdlet obtém informações sobre as operações num caso gerido. Pode visualizar todas as operações numa instância gerida ou ver uma operação específica fornecendo o nome da operação.

```powershell-interactive
$managedInstance = "yourInstanceName"
$resourceGroup = "yourResourceGroupName"

$managementOperations = Get-AzSqlInstanceOperation `
    -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup
```

Para obter uma explicação detalhada [dos comandos, consulte a Get-AzSqlInstanceOperation](/powershell/module/az.sql/get-azsqlinstanceoperation).

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

A lista de ops az sql mi obtém uma lista de operações realizadas na instância gerida. Se ainda não tiver o Azure CLI instalado, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli).

```azurecli-interactive
az sql mi op list -g yourResourceGroupName --mi yourInstanceName 
```

Para obter uma explicação detalhada dos comandos, consulte [az sql mi op](/cli/azure/sql/mi/op).

---

## <a name="next-steps"></a>Passos seguintes

- Para aprender a criar o seu primeiro exemplo gerido, consulte o [guia Quickstart](instance-create-quickstart.md).
- Para obter uma lista de funcionalidades e comparação, consulte [as funcionalidades SQL comuns.](../database/features-comparison.md)
- Para obter mais informações sobre a configuração VNet, consulte [a configuração VNet de instância gerida SQL](connectivity-architecture-overview.md).
- Para um arranque rápido que cria uma instância gerida e restaura uma base de dados a partir de um ficheiro de backup, consulte [Criar uma instância gerida](instance-create-quickstart.md).
- Para obter um tutorial sobre a utilização do Serviço de Migração da Base de Dados Azure para migração, consulte [a migração de instâncias geridas SQL utilizando o Serviço de Migração de Bases de Dados.](../../dms/tutorial-sql-server-to-managed-instance.md)