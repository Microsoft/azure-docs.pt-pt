---
title: Utilize a Azure PowerShell para configurar a cópia de segurança contínua e restaurar o tempo no Azure Cosmos DB.
description: Saiba como fornecer uma conta com cópia de segurança contínua e restaurar dados usando a Azure PowerShell.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 00d9e2c3b77ce4ddae105d8115cd62b5f7796e30
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527822"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore---using-azure-powershell"></a>Configure e gerencie a cópia de segurança contínua e o ponto no tempo de restauração - usando a Azure PowerShell

A funcionalidade de restauro pontual da Azure Cosmos DB ajuda-o a recuperar de uma alteração acidental dentro de um contentor, a restaurar uma conta, base de dados ou um contentor apagado ou a restaurar em qualquer região (onde existiam cópias de segurança). O modo de backup contínuo permite-lhe restaurar qualquer ponto de tempo nos últimos 30 dias.

Este artigo descreve como fornecer uma conta com cópia de segurança contínua e restaurar dados usando a Azure PowerShell.

## <a name="install-azure-powershell"></a><a id="install-powershell"></a>Instalar o Azure PowerShell

1. Executar o seguinte comando a partir de Azure PowerShell para instalar o `Az.CosmosDB` módulo de pré-visualização, que contém os comandos relacionados com ponto no tempo restaurar:

   ```azurepowershell
   Install-Module -Name Az.CosmosDB -AllowPrerelease
   ```

1. Depois de instalar os módulos, faça login no Azure usando

   ```azurepowershell
   Connect-AzAccount
   ```

1. Selecione uma subscrição específica com o seguinte comando:

   ```azurepowershell
   Select-AzSubscription -Subscription <SubscriptionName>
   ```

## <a name="provision-a-sql-api-account-with-continuous-backup"></a><a id="provision-sql-api"></a>Fornecimento de uma conta API SQL com backup contínuo

Para fornecer uma conta com cópia de segurança contínua, adicione um argumento `-BackupPolicyType Continuous` juntamente com o comando de provisionamento regular.

O cmdlet seguinte é um exemplo de uma única conta de escrita de região `pitracct2` com uma política de backup contínua criada na região "West US" no âmbito do grupo de recursos "myrg":

```azurepowershell

New-AzCosmosDBAccount `
  -ResourceGroupName "myrg" `
  -Location "West US" `
  -BackupPolicyType Continuous `
  -Name "pitracct2" `
  -ApiKind "Sql"
      
```

## <a name="provision-a-mongodb-api-account-with-continuous-backup"></a><a id="provision-mongodb-api"></a>Fornecimento de uma conta API mongoDB com backup contínuo

O cmdlet seguinte é um exemplo de conta de backup contínua "pitracct2" criada na região "West US" no âmbito do grupo de recursos "myrg":

```azurepowershell

New-AzCosmosDBAccount `
  -ResourceGroupName "myrg" `
  -Location "West US" `
  -BackupPolicyType Continuous `
  -Name "pitracct3" `
  -ApiKind "MongoDB" `
  -ServerVersion "3.6"

```

## <a name="trigger-a-restore-operation"></a><a id="trigger-restore"></a>Desencadear uma operação de restauro

O cmdlet a seguir é um exemplo para desencadear uma operação de restauro com o comando de restauro utilizando a conta-alvo, a conta de origem, a localização, o grupo de recursos e o timetamp:

```azurepowershell

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName <resourceGroupName> `
  -TargetDatabaseAccountName <restored-account-name> `
  -SourceDatabaseAccountName <sourceDatabaseAccountName> `
  -RestoreTimestampInUtc <UTC time> `
  -Location <Azure Region Name>

```

**Exemplo 1:** Restaurar toda a conta:

```azurepowershell

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName "rg" `
  -TargetDatabaseAccountName "pitrbb-ps-1" `
  -SourceDatabaseAccountName "source-sql" `
  -RestoreTimestampInUtc "2021-01-05T22:06:00" `
  -Location "West US"

```

**Exemplo 2:** Restaurar coleções e bases de dados específicas. Este exemplo restaura as coleções myCol1, myCol2 do myDB1 e toda a base de dados myDB2, que inclui todos os recipientes por baixo.

```azurepowershell
$datatabaseToRestore1 = New-AzCosmosDBDatabaseToRestore -DatabaseName "myDB1" -CollectionName "myCol1", "myCol2"
$datatabaseToRestore2 = New-AzCosmosDBDatabaseToRestore -DatabaseName "myDB2"

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName "rg" `
  -TargetDatabaseAccountName "pitrbb-ps-1" `
  -SourceDatabaseAccountName "source-sql" `
  -RestoreTimestampInUtc "2021-01-05T22:06:00" `
  -DatabasesToRestore $datatabaseToRestore1, $datatabaseToRestore2 `
  -Location "West US"

```

## <a name="enumerate-restorable-resources-for-sql-api"></a><a id="enumerate-sql-api"></a>Enumerar recursos ressurreíveis para a API SQL

Os cmdlets de enumeração ajudam-no a descobrir os recursos que estão disponíveis para restauro em vários momentos. Além disso, fornecem também um feed de eventos-chave na conta, base de dados e recursos de contentores restauradores.

**Listar todas as contas que podem ser restauradas na subscrição atual**

Execute o `Get-AzCosmosDBRestorableDatabaseAccount` comando PowerShell para listar todas as contas que podem ser restauradas na subscrição atual.

A resposta inclui todas as contas de base de dados (ao vivo e eliminadas) que podem ser restauradas e as regiões a partir das qual podem ser restauradas.

```console
{
    "accountName": "sampleaccount",
    "apiType": "Sql",
    "creationTime": "2020-08-08T01:04:52.070190+00:00",
    "deletionTime": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/23e99a35-cd36-4df4-9614-f767a03b9995",
    "identity": null,
    "location": "West US",
    "name": "23e99a35-cd36-4df4-9614-f767a03b9995",
    "restorableLocations": [
      {
        "creationTime": "2020-08-08T01:04:52.945185+00:00",
        "deletionTime": null,
        "locationName": "West US",
        "regionalDatabaseAccountInstanceId": "30701557-ecf8-43ce-8810-2c8be01dccf9"
      },
      {
        "creationTime": "2020-08-08T01:15:43.507795+00:00",
        "deletionTime": null,
        "locationName": "East US",
        "regionalDatabaseAccountInstanceId": "8283b088-b67d-4975-bfbe-0705e3e7a599"
      }
    ],
    "tags": null,
    "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts"
  },
```

Tal como o "Tempo de Criação" ou "Hora da Eliminação" para a conta, existe também um "Tempo de Criação" ou "Hora da Eliminação" para a região. Estes tempos permitem-lhe escolher a região certa e um intervalo de tempo válido para restaurar naquela região.

**Listar todas as versões das bases de dados SQL numa conta de base de dados ao vivo**

A listagem de todas as versões de bases de dados permite-lhe escolher a base de dados certa num cenário em que o tempo real de existência da base de dados é desconhecido.

Execute o seguinte comando PowerShell para listar todas as versões das bases de dados. Este comando só funciona com contas ao vivo. Os parâmetros "DatabaseAccountInstanceId" e "LocationName" são obtidos a partir das propriedades "nome" e "localização" na resposta do `Get-AzCosmosDBRestorableDatabaseAccount` cmdlet. O atributo "DatabaseAccountInstanceId" refere-se à propriedade "instanceId" da conta de base de dados de origem que está a ser restaurada:


```azurepowershell

Get-AzCosmosdbSqlRestorableDatabase `
  -LocationName "East US" `
  -DatabaseAccountInstanceId <DatabaseAccountInstanceId>

```

**Lista todas as versões de contentores SQL de uma base de dados numa conta de base de dados ao vivo.**

Utilize o seguinte comando para listar todas as versões dos recipientes SQL. Este comando só funciona com contas ao vivo. O parâmetro "DatabaseRid" é o "ResourceId" da base de dados que pretende restaurar. É o valor do atributo "ownerResourceid" encontrado na resposta do `Get-AzCosmosdbSqlRestorableDatabase` cmdlet. A resposta inclui também uma lista de operações realizadas em todos os contentores dentro desta base de dados.

```azurepowershell

Get-AzCosmosdbSqlRestorableContainer `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -DatabaseRid "AoQ13r==" `
  -LocationName "West US"

```

**Encontre bases de dados ou contentores que possam ser restaurados em qualquer momento**

Utilize o seguinte comando para obter a lista de bases de dados ou contentores que podem ser restaurados em qualquer data. Este comando só funciona com contas ao vivo.

```azurepowershell

Get-AzCosmosdbSqlRestorableResource `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -LocationName "West US" `
  -RestoreLocation "East US" `
  -RestoreTimestamp "2020-07-20T16:09:53+0000"

```

## <a name="enumerate-restorable-resources-for-mongodb"></a><a id="enumerate-mongodb-api"></a>Enumerar recursos restauradores para o MongoDB

Os comandos de enumeração descritos abaixo ajudam-no a descobrir os recursos disponíveis para restauro em vários picos de tempo. Além disso, fornecem também um feed de eventos-chave na conta, base de dados e recursos de contentores restauradores. Estes comandos funcionam apenas para contas ao vivo e são semelhantes aos comandos API SQL, mas com "MongoDB" no nome de comando em vez de "sql".

**Listar todas as versões das bases de dados do MongoDB numa conta de base de dados ao vivo**

```azurepowershell

Get-AzCosmosdbMongoDBRestorableDatabase `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -LocationName "West US"

```

**Listar todas as versões de coleções de mongodb de uma base de dados numa conta de base de dados ao vivo**

```azurepowershell

Get-AzCosmosdbMongoDBRestorableCollection `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -DatabaseRid "AoQ13r==" `
  -LocationName "West US"
```

**Listar todos os recursos de uma conta de base de dados de mongodb que estão disponíveis para restaurar em um determinado timetamp e região**

```azurepowershell

Get-AzCosmosdbMongoDBRestorableResource `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -LocationName "West US" `
  -RestoreLocation "West US" `
  -RestoreTimestamp "2020-07-20T16:09:53+0000"
```

## <a name="next-steps"></a>Passos seguintes

* Configure e gere a cópia de segurança contínua utilizando [o Azure CLI,](continuous-backup-restore-command-line.md) [o Resource Manager](continuous-backup-restore-template.md)ou o portal [Azure](continuous-backup-restore-portal.md).
* [Modelo de recurso do modo de backup contínuo](continuous-backup-restore-resource-model.md)
* [Gerir as permissões necessárias](continuous-backup-restore-permissions.md) para restaurar os dados com o modo de backup contínuo.
