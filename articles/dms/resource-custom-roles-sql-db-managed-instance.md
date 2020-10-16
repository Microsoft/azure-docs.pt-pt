---
title: 'Funções personalizadas: Servidor SQL Online para migrações de instâncias geridas SQL'
titleSuffix: Azure Database Migration Service
description: Aprenda a usar as funções personalizadas para o SQL Server para Azure SQL Managed Instance migrações on-line.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: e950e34a1965e309d226c42bf4b3128cbe3bf3c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91291508"
---
# <a name="custom-roles-for-sql-server-to-azure-sql-managed-instance-online-migrations"></a>Funções personalizadas para o SQL Server para Azure SQL Managed Instance migração on-line

O Azure Database Migration Service utiliza um ID app para interagir com os Serviços Azure. O ID da APP requer o papel de Contribuinte ao nível de Subscrição (que muitos departamentos de segurança corporativa não permitem) ou a criação de funções personalizadas que concedam as permissões específicas que o Serviço de Migrações da base de dados Azure exige. Uma vez que existe um limite de 2.000 funções personalizadas no Azure Ative Directory, pode querer combinar todas as permissões necessárias especificamente pelo ID da APP em uma ou duas funções personalizadas, e, em seguida, conceder ao ID app o papel personalizado em objetos específicos ou grupos de recursos (vs. ao nível de subscrição). Se o número de funções personalizadas não for uma preocupação, pode dividir as funções personalizadas por tipo de recurso, para criar três funções personalizadas no total, conforme descrito abaixo.

A secção De AssignableScopes da definição de função json string permite-lhe controlar onde as permissões aparecem na UI **de atribuição de funções adicionar** no portal. É provável que defina o papel no grupo de recursos ou mesmo ao nível de recursos para evitar que a UI tenha papéis extra. Note que isto não desempenha a tarefa real.

## <a name="minimum-number-of-roles"></a>Número mínimo de funções

Atualmente recomendamos a criação de um mínimo de duas funções personalizadas para o ID app, uma ao nível dos recursos e outra ao nível da subscrição.

> [!NOTE]
> O último requisito de função personalizada pode eventualmente ser removido, uma vez que o novo código de instância gerida do SQL é implantado no Azure.

**Papel personalizado para o ID app.** Esta função é necessária para a migração do Serviço de Migração da Base de Dados Azure ao nível dos *recursos* ou *recursos* (para obter mais informações sobre o ID da APP, consulte o artigo [Utilize o portal para criar uma aplicação AD Azure e um responsável de serviço que possa aceder aos recursos).](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)

```json
{
  "Name": "DMS Role - App ID",
  "IsCustom": true,
  "Description": "DMS App ID access to complete MI migrations",
  "Actions": [
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageaccounts/blobservices/read",
        "Microsoft.Storage/storageaccounts/blobservices/write",
        "Microsoft.Sql/managedInstances/read",
        "Microsoft.Sql/managedInstances/write",
        "Microsoft.Sql/managedInstances/databases/read",
        "Microsoft.Sql/managedInstances/databases/write",
        "Microsoft.Sql/managedInstances/databases/delete",
        "Microsoft.Sql/managedInstances/metrics/read",
        "Microsoft.DataMigration/locations/*",
        "Microsoft.DataMigration/services/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/ResourceGroups/<StorageAccount_rg_name>",
    "/subscriptions/<subscription_id>/ResourceGroups/<ManagedInstance_rg_name>",
    "/subscriptions/<subscription_id>/ResourceGroups/<DMS_rg_name>",
  ]
}
```

**Papel personalizado para o ID app - subscrição.** Esta função é necessária para a migração do Serviço de Migração da Base de Dados Azure ao nível *da subscrição.*

```json
{
  "Name": "DMS Role - App ID - Sub",
  "IsCustom": true,
  "Description": "DMS App ID access at subscription level to complete MI migrations",
  "Actions": [
        "Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>"
  ]
}
```

O json acima deve ser armazenado em três ficheiros de texto, e pode utilizar os cmdlets AzureRM, AZ PowerShell ou Azure CLI para criar as funções utilizando a **Definição New-AzureRmRole (AzureRM)** ou **a New-AzRoleDefinition (AZ)**.

Para mais informações, consulte o artigo [Azure.](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)

Depois de criar estas funções personalizadas, deve adicionar atribuições de funções aos utilizadores e iD(s) de APP aos recursos ou grupos de recursos apropriados:

* A função "DMS Role - App ID" deve ser atribuída ao ID da APP que será utilizado para as migrações, e também na Conta de Armazenamento, na instância do Serviço de Migração da Base de Dados Azure e nos níveis de recursos de Instância Gerida SQL.
* A função "DMS Role - App ID - Sub" deve ser atribuída ao ID app ao nível da subscrição (a concessão no grupo de recursos ou recursos falhará). Este requisito é temporário até que seja implementada uma atualização de código.

## <a name="expanded-number-of-roles"></a>Número alargado de funções

Se o número de funções personalizadas no seu Diretório Ativo Azure não for uma preocupação, recomendamos que crie um total de três funções. Você ainda vai precisar do papel "DMS Role - App ID – Sub", mas o papel "DMS Role - App ID" acima é dividido por tipo de recurso em duas funções diferentes.

**Papel personalizado para o ID app para sql managed instance**

```json
{
  "Name": "DMS Role - App ID - SQL MI",
  "IsCustom": true,
  "Description": "DMS App ID access to complete MI migrations",
  "Actions": [
        "Microsoft.Sql/managedInstances/read",
        "Microsoft.Sql/managedInstances/write",
        "Microsoft.Sql/managedInstances/databases/read",
        "Microsoft.Sql/managedInstances/databases/write",
        "Microsoft.Sql/managedInstances/databases/delete",
        "Microsoft.Sql/managedInstances/metrics/read"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/resourceGroups/<ManagedInstance_rg_name>"
  ]
}
```

**Papel personalizado para o ID app para armazenamento**

```json
{
  "Name": "DMS Role - App ID - Storage",
  "IsCustom": true,
  "Description": "DMS App ID storage access to complete MI migrations",
  "Actions": [
"Microsoft.Storage/storageAccounts/read",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageaccounts/blobservices/read",
        "Microsoft.Storage/storageaccounts/blobservices/write"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/resourceGroups/<StorageAccount_rg_name>"
  ]
}
```

## <a name="role-assignment"></a>Atribuição de função

Para atribuir uma função aos utilizadores/ID app, abra o portal Azure, execute os seguintes passos:

1. Navegue para o grupo de recursos ou recursos (exceto para o papel que precisa ser concedido na subscrição), vá ao **Access Control**, e depois percorra para encontrar as funções personalizadas que acabou de criar.

2. Selecione a função adequada, selecione o ID APP e, em seguida, guarde as alterações.

  O seu(s) ID(s) app(s) aparece agora listado no separador **de atribuições de Função.**

## <a name="next-steps"></a>Passos seguintes

* Reveja a orientação de migração para o seu cenário no [Guia de Migração da Base de Dados da](https://datamigration.microsoft.com/)Microsoft.
