---
title: Utilize o modelo ARM para configurar a cópia de segurança contínua e restaurar o ponto no tempo em Azure Cosmos DB.
description: Saiba como fornecer uma conta com backup contínuo e restaurar dados usando modelos de gestor de recursos Azure.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 6c388a08a589cc89d83b7178e31e3f4497b924bb
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527807"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore---using-azure-resource-manager-templates"></a>Configure e gerencie a cópia de segurança contínua e o ponto no tempo de restauração - usando modelos de Gestor de Recursos Azure
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

A funcionalidade de restauro pontual da Azure Cosmos DB ajuda-o a recuperar de uma alteração acidental dentro de um contentor, a restaurar uma conta, base de dados ou um contentor apagado ou a restaurar em qualquer região (onde existiam cópias de segurança). O modo de backup contínuo permite-lhe restaurar qualquer ponto de tempo nos últimos 30 dias.

Este artigo descreve como fornecer uma conta com cópia de segurança contínua e restaurar dados usando modelos de gestor de recursos Azure.

## <a name="provision-an-account-with-continuous-backup"></a><a id="provision"></a>Disponibilização de uma conta com backup contínuo

Pode utilizar os modelos do Gestor de Recursos Azure para implementar uma conta DB Azure Cosmos com modo contínuo. Ao definir o modelo para fornecer uma conta, inclua o parâmetro "backupPolicy", como mostrado no exemplo seguinte:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "name": "ademo-pitr1",
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "apiVersion": "2016-03-31",
      "location": "West US",
      "properties": {
        "locations": [
          {
            "locationName": "West US"
          }
        ],
        "backupPolicy": {
          "type": "Continuous"
        },
        "databaseAccountOfferType": "Standard"
      }
    }
  ]
}
```

Em seguida, implemente o modelo utilizando Azure PowerShell ou CLI. O exemplo a seguir mostra como implantar o modelo com um comando CLI:

```azurecli-interactive
az group deployment create -g <ResourceGroup> --template-file <ProvisionTemplateFilePath>
```

## <a name="restore-using-the-resource-manager-template"></a><a id="restore"></a>Restaurar usando o modelo de Gestor de Recursos

Também pode restaurar uma conta usando o modelo de Gestor de Recursos. Ao definir o modelo incluem os seguintes parâmetros:

* Desaprote o parâmetro "createMode" para "Restaurar"
* Defina os "restoreParameters", note que o valor "restoreSource" é extraído da saída do comando para a `az cosmosdb restorable-database-account list` sua conta de origem. O atributo de identificação de exemplo para o seu nome de conta é usado para fazer a restauração.
* Defina o parâmetro "restaurar oMode" para "PointInTime" e configuure o valor "restaurarTimestampInUtc".

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "name": "vinhpitrarmrestore-kal3",
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "apiVersion": "2016-03-31",
      "location": "West US",
      "properties": {
        "locations": [
          {
            "locationName": "West US"
          }
        ],
        "databaseAccountOfferType": "Standard",
        "createMode": "Restore",
        "restoreParameters": {
            "restoreSource": "/subscriptions/2296c272-5d55-40d9-bc05-4d56dc2d7588/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/6a18ecb8-88c2-4005-8dce-07b44b9741df",
            "restoreMode": "PointInTime",
            "restoreTimestampInUtc": "6/24/2020 4:01:48 AM"
        }
      }
    }
  ]
}
```

Em seguida, implemente o modelo utilizando Azure PowerShell ou CLI. O exemplo a seguir mostra como implantar o modelo com um comando CLI:  

```azurecli-interactive
az group deployment create -g <ResourceGroup> --template-file <RestoreTemplateFilePath> 
```

## <a name="next-steps"></a>Passos seguintes

* Configure e gere a cópia de segurança contínua utilizando [o Azure CLI,](continuous-backup-restore-command-line.md) [o PowerShell](continuous-backup-restore-command-line.md)ou [o portal Azure](continuous-backup-restore-portal.md).
* [Modelo de recurso do modo de backup contínuo](continuous-backup-restore-resource-model.md)
* [Gerir as permissões necessárias](continuous-backup-restore-permissions.md) para restaurar os dados com o modo de backup contínuo.