---
title: Utilize o Azure CLI para configurar a cópia de segurança contínua e restaurar o tempo no Azure Cosmos DB.
description: Saiba como fornecer uma conta com cópia de segurança contínua e restaurar dados usando o Azure CLI.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 735e85683bc72cb8d0ddc0d18d16f10f72ba7641
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527727"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore---using-azure-cli"></a>Configure e gerencie a cópia de segurança contínua e o ponto no tempo de restauração - usando O Azure CLI
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

A funcionalidade de restauro pontual da Azure Cosmos DB ajuda-o a recuperar de uma alteração acidental dentro de um contentor, a restaurar uma conta, base de dados ou um contentor apagado ou a restaurar em qualquer região (onde existiam cópias de segurança). O modo de backup contínuo permite-lhe restaurar qualquer ponto de tempo nos últimos 30 dias.

Este artigo descreve como fornecer uma conta com cópia de segurança contínua e restaurar dados usando O Azure CLI.

## <a name="install-azure-cli"></a><a id="install"></a>Instalar O Azure CLI

1. Instale a versão mais recente do Azure CLI

   * Instale a versão mais recente do [Azure CLI](/cli/azure/install-azure-cli) ou versão superior a 2.17.1.
   * Se já instalou o CLI, execute `az upgrade` o comando para atualizar para a versão mais recente. Este comando só funcionará com a versão CLI superior a 2.11. Se tiver uma versão anterior, utilize o link acima para instalar a versão mais recente.

1. Instale a `cosmosdb-preview` extensão CLI.

   * Os comandos de restauro pontual estão disponíveis sob `cosmosdb-preview` extensão.
   * Pode instalar esta extensão executando o seguinte comando: `az extension add --name cosmosdb-preview`
   * Pode desinstalar esta extensão executando o seguinte comando: `az extension remove --name cosmosdb-preview`

1. Iniciar sôr-se e selecionar a sua subscrição

   * Inscreva-se na sua conta Azure com `az login` o comando.
   * Selecione a subscrição necessária usando `az account set -s <subscriptionguid>` o comando.

## <a name="provision-a-sql-api-account-with-continuous-backup"></a><a id="provision-sql-api"></a>Fornecimento de uma conta API SQL com backup contínuo

Para fornecer uma conta API SQL com backup contínuo, deve ser passado um argumento extra `--backup-policy-type Continuous` juntamente com o comando de provisionamento regular. O seguinte comando é um exemplo de uma única conta de escrita de região nomeada `pitracct2` com uma política de backup contínua criada na região "West US" sob o grupo de recursos "myrg":

```azurecli-interactive

az cosmosdb create \
  --name pitracct2 \
  --resource-group myrg \
  --backup-policy-type Continuous \
  --default-consistency-level Session \
  --locations regionName="West US"

```

## <a name="provision-an-azure-cosmos-db-api-for-mongodb-account-with-continuous-backup"></a><a id="provision-mongo-api"></a>Fornecimento de um AZure Cosmos DB API para conta MongoDB com backup contínuo

O seguinte comando mostra um exemplo de uma única conta de escrita de região nomeada `pitracct3` com uma política de backup contínua criada a região "West US" sob o grupo de recursos "myrg":

```azurecli-interactive

az cosmosdb create \
  --name pitracct3 \
  --kind MongoDB \
  --resource-group myrg \
  --server-version "3.6" \
  --backup-policy-type Continuous \
  --default-consistency-level Session \
  --locations regionName="West US"

```

## <a name="trigger-a-restore-operation-with-cli"></a><a id="trigger-restore"></a>Desencadear uma operação de restauro com CLI

A forma mais simples de desencadear uma restauração é emitindo o comando de restauro com o nome da conta-alvo, conta de origem, localização, grupo de recursos, timetamp (na UTC), e opcionalmente a base de dados e os nomes dos contentores. Seguem-se alguns exemplos para desencadear a operação de restauro:

1. Crie uma nova conta DB Azure Cosmos restaurando a partir de uma conta existente.

   ```azurecli-interactive

   az cosmosdb restore \
    --target-database-account-name MyRestoredCosmosDBDatabaseAccount \
    --account-name MySourceAccount \
    --restore-timestamp 2020-07-13T16:03:41+0000 \
    --resource-group MyResourceGroup \
    --location "West US"

   ```

2. Crie uma nova conta DB Azure Cosmos restaurando apenas bases de dados e contentores selecionados a partir de uma conta de base de dados existente.

   ```azurecli-interactive

   az cosmosdb restore \
    --resource-group MyResourceGroup \
    --target-database-account-name MyRestoredCosmosDBDatabaseAccount \
    --account-name MySourceAccount \
    --restore-timestamp 2020-07-13T16:03:41+0000 \
    --location "West US" \
    --databases-to-restore name=MyDB1 collections=collection1 collection2 \
    --databases-to-restore name=MyDB2 collections=collection3 collection4

   ```

## <a name="enumerate-restorable-resources-for-sql-api"></a><a id="enumerate-sql-api"></a>Enumerar recursos ressurreíveis para a API SQL

Os comandos de enumeração descritos abaixo ajudam-no a descobrir os recursos disponíveis para restauro em vários picos de tempo. Além disso, fornecem também um feed de eventos-chave na conta, base de dados e recursos de contentores restauradores.

**Listar todas as contas que podem ser restauradas na subscrição atual**

Executar o seguinte comando CLI para listar todas as contas que podem ser restauradas na subscrição atual

```azurecli-interactive
az cosmosdb restorable-database-account list --account-name "pitrbb"
```

A resposta inclui todas as contas de base de dados (ao vivo e eliminadas) que podem ser restauradas e as regiões que podem ser restauradas a partir de:

```json
{
    "accountName": "pitrbb",
    "apiType": "Sql",
    "creationTime": "2021-01-08T23:34:11.095870+00:00",
    "deletionTime": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865",
    "identity": null,
    "location": "West US",
    "name": "7133a59a-d1c0-4645-a699-6e296d6ac865",
    "restorableLocations": [
      {
        "creationTime": "2021-01-08T23:34:11.095870+00:00",
        "deletionTime": null,
        "locationName": "West US",
        "regionalDatabaseAccountInstanceId": "f02df26b-c0ec-4829-8bef-3482d36e6230"
      }
    ],
    "tags": null,
    "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts"
  }
```

Tal como o "Tempo de Criação" ou "Hora da Eliminação" para a conta, existe também um "Tempo de Criação" ou "Hora da Eliminação" para a região. Estes tempos permitem-lhe escolher a região certa e um intervalo de tempo válido para restaurar naquela região.

**Listar todas as versões de bases de dados numa conta de base de dados ao vivo**

A listagem de todas as versões de bases de dados permite-lhe escolher a base de dados certa num cenário em que o tempo real de existência da base de dados é desconhecido.

Execute o seguinte comando CLI para listar todas as versões de bases de dados. Este comando só funciona com contas ao vivo. Os parâmetros "instanceId" e "localização" são obtidos a partir das propriedades "nome" e "localização" na resposta ao `az cosmosdb restorable-database-account list` comando. O atributo casoId é também uma propriedade da conta de base de dados de origem que está sendo restaurada:

```azurecli-interactive
az cosmosdb sql restorable-database list \
  --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
  --location "West US"
```

Esta saída de comando mostra agora quando uma base de dados foi criada e eliminada.

```json
[
  {
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865/restorableSqlDatabases/40e93dbd-2abe-4356-a31a-35567b777220",
    ..
    "name": "40e93dbd-2abe-4356-a31a-35567b777220",
    "resource": {
      "database": {
        "id": "db1"
      },
      "eventTimestamp": "2021-01-08T23:27:25Z",
      "operationType": "Create",
      "ownerId": "db1",
      "ownerResourceId": "YuZAAA=="
    },
    ..
  },
  {
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865/restorableSqlDatabases/243c38cb-5c41-4931-8cfb-5948881a40ea",
    ..
    "name": "243c38cb-5c41-4931-8cfb-5948881a40ea",
    "resource": {
      "database": {
        "id": "spdb1"
      },
      "eventTimestamp": "2021-01-08T23:25:25Z",
      "operationType": "Create",
      "ownerId": "spdb1",
      "ownerResourceId": "OIQ1AA=="
    },
   ..
  }
]
```

**Listar todas as versões de contentores SQL de uma base de dados numa conta de base de dados ao vivo**

Utilize o seguinte comando para listar todas as versões dos recipientes SQL. Este comando só funciona com contas ao vivo. O parâmetro "databaseRid" é o "ResourceId" da base de dados que pretende restaurar. É o valor do atributo "proprietárioResourceid" encontrado na resposta do `az cosmosdb sql restorable-database list` comando.

```azurecli-interactive
az cosmosdb sql restorable-container list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --database-rid "OIQ1AA==" \
    --location "West US"
```

Esta saída de comando mostra a lista de operações realizadas em todos os contentores dentro desta base de dados:

```json
[
  {
    ...
    
      "eventTimestamp": "2021-01-08T23:25:29Z",
      "operationType": "Replace",
      "ownerId": "procol3",
      "ownerResourceId": "OIQ1APZ7U18="
...
  },
  {
    ...
      "eventTimestamp": "2021-01-08T23:25:26Z",
      "operationType": "Create",
      "ownerId": "procol3",
      "ownerResourceId": "OIQ1APZ7U18="
  },
]
```

**Encontre bases de dados ou contentores que possam ser restaurados em qualquer momento**

Utilize o seguinte comando para obter a lista de bases de dados ou contentores que podem ser restaurados em qualquer data. Este comando só funciona com contas ao vivo.

```azurecli-interactive

az cosmosdb sql restorable-resource list \
  --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
  --location "West US" \
  --restore-location "West US" \  
  --restore-timestamp "2021-01-10 T01:00:00+0000"

```

```json
[
  {
    "collectionNames": [
      "procol1",
      "procol2"
    ],
    "databaseName": "db1"
  },
  {
    "collectionNames": [
      "procol3",
       "spcol1"
    ],
    "databaseName": "spdb1"
  }
]
```

## <a name="enumerate-restorable-resources-for-mongodb-api-account"></a><a id="enumerate-mongodb-api"></a>Enumerar recursos ressurreíveis para conta mongodb API

Os comandos de enumeração descritos abaixo ajudam-no a descobrir os recursos disponíveis para restauro em vários picos de tempo. Além disso, fornecem também um feed de eventos-chave na conta, base de dados e recursos de contentores restauradores. Tal como com a SQL API, pode utilizar o `az cosmosdb` comando mas com "mongodb" como parâmetro em vez de "sql". Estes comandos só funcionam para contas ao vivo.

**Listar todas as versões das bases de dados de Mongodb numa conta de base de dados ao vivo**

```azurecli-interactive
az cosmosdb mongodb restorable-database list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --location "West US"
```

**Listar todas as versões de coleções de mongodb de uma base de dados numa conta de base de dados ao vivo**

```azurecli-interactive
az cosmosdb mongodb restorable-collection list \
    --instance-id "<InstanceID>" \
    --database-rid "AoQ13r==" \
    --location "West US"
```

**Listar todos os recursos de uma conta de base de dados de mongodb que estão disponíveis para restaurar em um determinado timetamp e região**

```azurecli-interactive
az cosmosdb mongodb restorable-resource list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --location "West US" \
    --restore-location "West US" \
    --restore-timestamp "2020-07-20T16:09:53+0000"
```

## <a name="next-steps"></a>Passos seguintes

* Configure e gere a cópia de segurança contínua utilizando [o portal Azure](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md)ou [Azure Resource Manager](continuous-backup-restore-template.md).
* [Modelo de recurso do modo de backup contínuo](continuous-backup-restore-resource-model.md)
* [Gerir as permissões necessárias](continuous-backup-restore-permissions.md) para restaurar os dados com o modo de backup contínuo.
