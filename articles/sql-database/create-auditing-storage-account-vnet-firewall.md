---
title: Auditoria à conta de armazenamento por trás da VNet e firewall
description: Configure a auditoria para escrever eventos de base de dados numa conta de armazenamento por trás da rede virtual e firewall
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/19/2020
ms.custom: azure-synapse
ms.openlocfilehash: 556b9c2861461ffdfc63f5607cca2157226e15bb
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83658296"
---
# <a name="write-audit-to-a-storage-account-behind-vnet-and-firewall"></a>Escreva auditoria a uma conta de armazenamento atrás da VNet e firewall

A auditoria da [Azure SQL Database](sql-database-technical-overview.md) e [da Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) suporta a escrita de eventos de base de dados para uma conta de Armazenamento [Azure](../storage/common/storage-account-overview.md) por trás de uma rede virtual e firewall. 

Este artigo explica duas formas de configurar o Azure SQL Server e a conta de armazenamento Azure para esta opção. O primeiro usa o portal Azure, o segundo usa REST.

### <a name="background"></a>Fundo

[A Rede Virtual Azure (VNet)](../virtual-network/virtual-networks-overview.md) é o bloco de construção fundamental da sua rede privada em Azure. A VNet permite que muitos tipos de recursos Azure, como as Máquinas Virtuais Azure (VM), se comuniquem de forma segura entre si, a internet e as redes no local. O VNet é semelhante a uma rede tradicional no seu próprio centro de dados, mas traz consigo benefícios adicionais da infraestrutura Azure, como escala, disponibilidade e isolamento.

Para saber mais sobre os conceitos VNet, As Melhores Práticas e muito mais, veja o que é a [Rede Virtual Azure.](../virtual-network/virtual-networks-overview.md)

Para saber mais sobre como criar uma rede virtual, consulte [Quickstart: Criar uma rede virtual utilizando o portal Azure](../virtual-network/quick-create-portal.md).

## <a name="prerequisites"></a>Pré-requisitos

Para que a auditoria escreva numa conta de armazenamento atrás de uma VNet ou firewall, são necessários os seguintes pré-requisitos:

> [!div class="checklist"]
> * Uma conta de armazenamento v2 de uso geral. Se tiver uma conta de armazenamento v1 ou blob de uso geral, atualize para uma conta de [armazenamento v2 de uso geral](../storage/common/storage-account-upgrade.md). Para mais informações, consulte [tipos de contas de armazenamento](../storage/common/storage-account-overview.md#types-of-storage-accounts).
> * A conta de armazenamento deve estar na mesma subscrição e no mesmo local que o servidor de base de dados Azure SQL. 
> * A conta de Armazenamento Azure requer `Allow trusted Microsoft services to access this storage account` . Delineie isto nas firewalls da conta de armazenamento **e nas redes virtuais.**
> * Deve ter permissão na conta de `Microsoft.Authorization/roleAssignments/write` armazenamento selecionada. Para mais informações, consulte [as funções de Azure incorporadas.](../role-based-access-control/built-in-roles.md)

## <a name="configure-in-azure-portal"></a>Configurar no portal do Azure

Ligue-se ao [portal Azure](https://portal.azure.com) com a sua subscrição. Navegue para o grupo de recursos e para o servidor de base de dados Azure SQL.

1. Clique em **Auditoria** sob a rubrica Segurança. Selecione **on**.

2. Selecione **Armazenamento**. Selecione a conta de armazenamento onde os registos serão guardados. A conta de armazenamento deve cumprir os requisitos enumerados nos [pré-requisitos](#prerequisites).

3. Detalhes **de armazenamento** aberto 

  > [!NOTE]
  > Se a conta de Armazenamento selecionada estiver por trás da VNet, verá a seguinte mensagem:
  >
  >`You have selected a storage account that is behind a firewall or in a virtual network. Using this storage requires to enable 'Allow trusted Microsoft services to access this storage account' on the storage account and creates a server managed identity with 'storage blob data contributor' RBAC.`
  >
  >Se não vir esta mensagem, então a conta de armazenamento não está atrás de um VNet.

4. Selecione o número de dias para o período de retenção. Em seguida, clique em **OK**. Os registos mais antigos do que o período de retenção são eliminados.

5. Selecione **Guardar** nas definições de auditoria.

Configurou com sucesso uma auditoria para escrever numa conta de armazenamento atrás de uma VNet ou firewall. 

## <a name="configure-with-rest-commands"></a>Configure com comandos REST

Como alternativa à utilização do portal Azure, pode utilizar comandos REST para configurar auditoria para escrever eventos de base de dados numa conta de armazenamento atrás de uma VNet e Firewall. 

Os scripts de amostra nesta secção exigem que atualize o script antes de os executar. Substitua os seguintes valores nos scripts:

|Valor da amostra|Descrição da amostra|
|:-----|:-----|
|`<subscriptionId>`| ID de subscrição azure|
|`<resource group>`| Grupo de recursos|
|`<sql database server>`| Nome do servidor de servidor de base de dados Azure SQL|
|`<administrator login>`| Conta de administrador de base de dados SQL |
|`<complex password>`| Senha complexa para a conta de administrador|

Para configurar a Auditoria SQL para escrever eventos numa conta de armazenamento atrás de uma VNet ou Firewall:

1. Registe o seu servidor de base de dados Azure SQL com o Azure Ative Directory (Azure AD). Utilize a PowerShell ou a REST API.

   **PowerShell**
   
   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName <your resource group> -ServerName <azure server name> -AssignIdentity
   ```
   
   [**API DE REPOUSO:**](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)

   Pedido de amostra

   ```html
   PUT https://management.azure.com/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Sql/servers/<azure server name>?api-version=2015-05-01-preview
   ```

   Corpo do pedido

   ```json
   {
   "identity": {
              "type": "SystemAssigned",
              },
   "properties": {
     "fullyQualifiedDomainName": "<azure server name>.database.windows.net",
     "administratorLogin": "<administrator login>",
     "administratorLoginPassword": "<complex password>",
     "version": "12.0",
     "state": "Ready"
   }
   ```

2. Abre o [Portal do Azure](https://portal.azure.com). Navegue até à sua conta de armazenamento. Localizar o Controlo de **Acesso (IAM)** e clique em **Adicionar a atribuição de funções**. Atribuir papel de RBAC do Colaborador de **Dados blob** de armazenamento ao seu Servidor Azure SQL que acolhe a sua base de dados Azure SQL que registou com o Azure Ative Directory (Azure AD) como no passo anterior.

   > [!NOTE]
   > Só os membros com privilégio proprietário podem executar este passo. Para várias funções incorporadas para os recursos Azure, consulte as [funções azure incorporadas.](../role-based-access-control/built-in-roles.md)

3. Configure a política de [auditoria blob do servidor Azure SQL,](/rest/api/sql/server%20auditing%20settings/createorupdate)sem especificar um *storageAccountAccessKey:*

   Pedido de amostra

   ```html
   PUT https://management.azure.com/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Sql/servers/<azure server name>?api-version=2017-03-01-preview
   ```

   Corpo do pedido

   ```json
   {
     "properties": {
      "state": "Enabled",
      "storageEndpoint": "https://<storage account>.blob.core.windows.net"
     }
   }
   ```

## <a name="using-azure-powershell"></a>Utilizar o Azure PowerShell

- [Criar ou atualizar política de auditoria da base de dados (Set-AzSqlDatabaseAudit)](/powershell/module/az.sql/set-azsqldatabaseaudit)
- [Criar ou atualizar a política de auditoria do servidor (Set-AzSqlServerAudit)](/powershell/module/az.sql/set-azsqlserveraudit)

## <a name="next-steps"></a>Passos seguintes

- [Utilize o PowerShell para criar um ponto final de serviço de rede virtual e, em seguida, uma regra de rede virtual para a Base de Dados Azure SQL.](sql-database-vnet-service-endpoint-rule-powershell.md)
- [Regras da Rede Virtual: Operações com APIs REST](/rest/api/sql/virtualnetworkrules)
- [Utilize pontos finais e regras de serviço de rede virtual para servidores de base de dados](sql-database-vnet-service-endpoint-rule-overview.md)
