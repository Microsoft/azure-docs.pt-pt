---
title: 'Funções personalizadas: Servidor SQL online para sql gerido migrações de instâncias'
titleSuffix: Azure Database Migration Service
description: Aprenda a usar as funções personalizadas para O Servidor SQL para a Base de Dados Azure SQL gerida por migrações online.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 10/25/2019
ms.openlocfilehash: e9a1024ca3ab68841474ab051c029042df4915b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78254949"
---
# <a name="custom-roles-for-sql-server-to-sql-database-managed-instance-online-migrations"></a>Funções personalizadas para SQL Server para SQL Database gerida sondar migrações on-line

O Serviço de Migração de Bases de Dados Azure utiliza um ID APP para interagir com os Serviços Azure. O ID app requer o papel de Contribuinte a nível de Subscrição (que muitos departamentos de segurança corporativa não permitem) ou criação de funções personalizadas que concedem as permissões específicas que o Serviço de Migrações de Base de Dados Azure necessita. Uma vez que existe um limite de 2.000 funções personalizadas no Diretório Ativo do Azure, pode querer combinar todas as permissões exigidas especificamente pelo ID da APP em uma ou duas funções personalizadas, e, em seguida, conceder ao APP ID o papel personalizado em objetos ou grupos de recursos específicos (vs. no id da APP em uma ou duas funções personalizadas, e, em seguida, conceder ao APP ID o papel personalizado em objetos ou grupos de recursos específicos (vs. no the app ID em uma ou duas funções personalizadas, e, em seguida, conceder ao APP ID o papel personalizado em objetos ou grupos de recursos específicos (vs. no the app ID em uma ou duas funções personalizadas, e, em seguida, conceder ao APP ID o papel personalizado em objetos ou grupos de recursos específicos (vs. no nível de subscrição). Se o número de papéis personalizados não for uma preocupação, pode dividir as funções personalizadas por tipo de recurso, para criar três funções personalizadas no total, como descrito abaixo.

A secção De Scopes Atribuidores da linha json definição de função permite controlar onde as permissões aparecem no UI de Atribuição de **Funções adicionais** no portal. É provável que queira definir o papel no grupo de recursos ou mesmo ao nível de recursos para evitar a confusão da UI com papéis extra. Note que isto não desempenha a tarefa real.

## <a name="minimum-number-of-roles"></a>Número mínimo de funções

Recomendamos atualmente a criação de um mínimo de duas funções personalizadas para o ID app, uma ao nível de recursos e outra ao nível da subscrição.

> [!NOTE]
> O último requisito de função personalizado pode eventualmente ser removido, uma vez que o novo código de instância gerido pela Base de Dados SQL é implantado no Azure.

**Papel personalizado para o ID APP**. Esta função é necessária para a migração do Serviço de Migração de Bases de Dados Azure ao nível dos *recursos* ou *grupos* de recursos (para mais informações sobre o ID app, consulte o artigo [Utilize o portal para criar uma aplicação e diretor](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)de serviço seletiva azure que possa aceder a recursos).

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

**Papel personalizado para o ID APP - subscrição**. Esta função é necessária para a migração do Serviço de Migração de Bases de Dados Azure a nível de *subscrição.*

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

O json acima deve ser armazenado em três ficheiros de texto, e pode utilizar os cmdlets AzureRM, AZ PowerShell ou Azure CLI para criar as funções utilizando a **New-AzureRmRoleDefinition (AzureRM)** ou **a New-AzRoleDefinition (AZ)**.

Para mais informações, consulte o artigo [Papéis personalizados para recursos Azure.](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)

Depois de criar estas funções personalizadas, deve adicionar atribuições de funções aos utilizadores e ID app aos recursos ou grupos de recursos apropriados:

* A função "DMS Role - App ID" deve ser concedida ao ID app que será utilizado para as migrações, bem como na Conta de Armazenamento, na instância do Serviço de Migração da Base de Dados Azure e na Base de Dados SQL gerido sem níveis de recursos de instância.
* A função "DMS Role - App ID - Sub" deve ser concedida ao ID da APP ao nível da subscrição (a concessão ao grupo de recursos ou recursos falhará). Este requisito é temporário até que seja implementada uma atualização de código.

## <a name="expanded-number-of-roles"></a>Número alargado de funções

Se o número de funções personalizadas no seu Diretório Ativo Azure não for uma preocupação, recomendamos que crie um total de três funções. Você ainda vai precisar da função "DMS Role - App ID – Sub", mas a função "DMS Role - App ID" acima é dividida por tipo de recurso em duas funções diferentes.

**Função personalizada para o ID app para a instância gerida pela Base de Dados SQL**

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

Para atribuir uma função aos utilizadores/APP ID, abra o portal Azure, execute os seguintes passos:

1. Navegue para o grupo de recursos ou recursos (exceto a função que precisa de ser concedida na subscrição), vá ao **Access Control**, e depois percorra para encontrar as funções personalizadas que acabou de criar.

2. Selecione a função adequada, selecione o ID da APP e, em seguida, guarde as alterações.

  O seu ID(s) app aparece agora listado no **separador de atribuições de funções.**

## <a name="next-steps"></a>Passos seguintes

* Reveja a orientação de migração para o seu cenário no Guia de [Migração](https://datamigration.microsoft.com/)da Base de Dados da Microsoft .
