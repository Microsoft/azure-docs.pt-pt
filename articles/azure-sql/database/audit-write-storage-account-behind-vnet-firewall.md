---
title: Auditoria à conta de armazenamento por trás do VNet e firewall
description: Configurar a auditoria para escrever eventos de base de dados numa conta de armazenamento por trás da rede virtual e firewall
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: how-to
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 06/17/2020
ms.custom: azure-synapse
ms.openlocfilehash: dde6cf40e7609e902540e08fcaff65d9fe32c85c
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289643"
---
# <a name="write-audit-to-a-storage-account-behind-vnet-and-firewall"></a>Escreva auditoria a uma conta de armazenamento por trás do VNet e firewall
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


A auditoria ao [Azure SQL Database](sql-database-paas-overview.md) e [ao Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) suporta a escrita de eventos de base de dados para uma conta de Armazenamento [Azure](../../storage/common/storage-account-overview.md) por trás de uma rede virtual e firewall.

Este artigo explica duas formas de configurar a Base de Dados Azure SQL e a conta de armazenamento Azure para esta opção. O primeiro usa o portal Azure, o segundo usa REST.

## <a name="background"></a>Fundo

[A Azure Virtual Network (VNet)](../../virtual-network/virtual-networks-overview.md) é o bloco de construção fundamental para a sua rede privada em Azure. O VNet permite que muitos tipos de recursos Azure, como as Máquinas Virtuais Azure (VM), comuniquem-se de forma segura entre si, a internet e as redes no local. O VNet é semelhante a uma rede tradicional no seu próprio centro de dados, mas traz consigo benefícios adicionais da infraestrutura Azure, como escala, disponibilidade e isolamento.

Para saber mais sobre os conceitos VNet, Boas Práticas e muito mais, consulte [o que é a Rede Virtual Azure.](../../virtual-network/virtual-networks-overview.md)

Para saber mais sobre como criar uma rede virtual, consulte [Quickstart: Criar uma rede virtual utilizando o portal Azure](../../virtual-network/quick-create-portal.md).

## <a name="prerequisites"></a>Pré-requisitos

Para que a auditoria escreva numa conta de armazenamento por trás de um VNet ou firewall, são necessários os seguintes pré-requisitos:

> [!div class="checklist"]
>
> * Uma conta de armazenamento v2 para fins gerais. Se tiver uma conta de armazenamento v1 ou blob para fins gerais, [faça upgrade para uma conta de armazenamento V2 para fins gerais](../../storage/common/storage-account-upgrade.md). Para obter mais informações, consulte [tipos de contas de armazenamento.](../../storage/common/storage-account-overview.md#types-of-storage-accounts)
> * A conta de armazenamento deve estar na mesma subscrição e no mesmo local que o [lógico SQL Server](logical-servers.md).
> * A conta de Armazenamento Azure requer `Allow trusted Microsoft services to access this storage account` . Coloque isto nas **firewalls** de conta de armazenamento e nas redes virtuais.
> * Tem de ter `Microsoft.Authorization/roleAssignments/write` permissão na conta de armazenamento selecionada. Para obter mais informações, veja [Funções incorporadas do Azure](../../role-based-access-control/built-in-roles.md).

## <a name="configure-in-azure-portal"></a>Configurar no portal do Azure

Ligue-se ao [portal Azure](https://portal.azure.com) com a sua subscrição. Navegue para o grupo de recursos e servidor.

1. Clique em **Auditoria sob** o título de Segurança. **Selecione on**.

2. Selecione **Armazenamento**. Selecione a conta de armazenamento onde os registos serão guardados. A conta de armazenamento deve estar em conformidade com os requisitos enumerados nos [Pré-requisitos](#prerequisites).

3. Detalhes **de armazenamento aberto**

  > [!NOTE]
  > Se a conta de Armazenamento selecionada estiver por trás do VNet, verá a seguinte mensagem:
  >
  >`You have selected a storage account that is behind a firewall or in a virtual network. Using this storage requires to enable 'Allow trusted Microsoft services to access this storage account' on the storage account and creates a server managed identity with 'storage blob data contributor' RBAC.`
  >
  >Se não vir esta mensagem, então a conta de armazenamento não está por trás de um VNet.

4. Selecione o número de dias para o período de retenção. Em seguida, clique em **OK**. Os registos mais antigos do que o período de retenção são eliminados.

5. **Selecione Guardar** nas definições de auditoria.

Configurada com sucesso a auditoria para escrever numa conta de armazenamento atrás de um VNet ou firewall.

## <a name="configure-with-rest-commands"></a>Configure com comandos REST

Como alternativa à utilização do portal Azure, pode utilizar comandos REST para configurar a auditoria para escrever eventos de base de dados numa conta de armazenamento atrás de um VNet e Firewall.

Os scripts de amostra nesta secção requerem que atualize o script antes de executá-los. Substitua os seguintes valores nos scripts:

|Valor da amostra|Descrição da amostra|
|:-----|:-----|
|`<subscriptionId>`| ID de assinatura Azure|
|`<resource group>`| Grupo de recursos|
|`<logical SQL Server>`| Nome do servidor|
|`<administrator login>`| Conta de administrador |
|`<complex password>`| Senha complexa para a conta do administrador|

Para configurar a Auditoria SQL para escrever eventos numa conta de armazenamento por trás de um VNet ou Firewall:

1. Registe o seu servidor com o Azure Ative Directory (Azure AD). Utilize a PowerShell ou a REST API.

   **PowerShell**

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName <your resource group> -ServerName <azure server name> -AssignIdentity
   ```

   [**REST API:**](/rest/api/sql/servers/createorupdate)

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

2. Abre o [Portal do Azure](https://portal.azure.com). Navegue até à sua conta de armazenamento. Localizar **controlo de acesso (IAM)** e clicar em adicionar a atribuição de **função**. Atribua o papel de Azure **do Contribuinte de Dados de Armazenamento blob** ao servidor que hospeda a base de dados que registou no Azure Ative Directory (AD) como no passo anterior.

   > [!NOTE]
   > Só os membros com privilégio proprietário podem realizar este passo. Para vários papéis embutidos em Azure, consulte as [funções incorporadas do Azure.](../../role-based-access-control/built-in-roles.md)

3. Configure a [política de auditoria do blob do servidor,](/rest/api/sql/server%20auditing%20settings/createorupdate)sem especificar um *armazenamentoAccountAccessKey* :

   Pedido de amostra

   ```html
     PUT https://management.azure.com/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Sql/servers/<azure server name>/auditingSettings/default?api-version=2017-03-01-preview
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

- [Criar ou atualizar a política de auditoria da base de dados (Set-AzSqlDatabaseAudit)](/powershell/module/az.sql/set-azsqldatabaseaudit)
- [Criar ou atualizar a política de auditoria do servidor (Set-AzSqlServerAudit)](/powershell/module/az.sql/set-azsqlserveraudit)

## <a name="using-azure-resource-manager-template"></a>Com o modelo do Azure Resource Manager

Pode configurar a auditoria para escrever eventos de base de dados numa conta de armazenamento por trás da rede virtual e firewall utilizando o modelo [do Gestor de Recursos Azure,](../../azure-resource-manager/management/overview.md) como mostra o exemplo seguinte:

> [!IMPORTANT]
> Para utilizar a conta de armazenamento por trás da rede virtual e firewall, você precisa definir o parâmetro **StorageBehindVnet** para ser verdadeiro

- [Implementar um Servidor SQL Azure com Auditoria habilitado a escrever registos de auditoria para um armazenamento de bolhas](https://azure.microsoft.com/resources/templates/201-sql-auditing-server-policy-to-blob-storage)

> [!NOTE]
> A amostra ligada encontra-se num repositório público externo e é fornecida "como está", sem garantia, e não é suportada sob qualquer programa/serviço de suporte da Microsoft.

## <a name="next-steps"></a>Passos seguintes

* [Utilize o PowerShell para criar um ponto final de serviço de rede virtual e, em seguida, uma regra de rede virtual para Azure SQL Database.](scripts/vnet-service-endpoint-rule-powershell-create.md)
* [Regras de Rede Virtual: Operações com APIs REST](/rest/api/sql/virtualnetworkrules)
* [Utilize pontos finais de serviço de rede virtual e regras para servidores](vnet-service-endpoint-rule-overview.md)
