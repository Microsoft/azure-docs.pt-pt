---
title: Modelo de recursos para a função de restauro ponto-a-tempo da Azure Cosmos.
description: Este artigo explica o modelo de recursos para a funcionalidade de restauro ponto-a-tempo da Azure Cosmos. Explica os parâmetros que suportam a backup contínua e os recursos que podem ser restaurados em Azure Cosmos DB API para contas SQL e MongoDB.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: d838425583638aef5199b52df4869923c826553d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100369973"
---
# <a name="resource-model-for-the-azure-cosmos-db-point-in-time-restore-feature-preview"></a>Modelo de recursos para a funcionalidade de restauro ponto-a-tempo do Azure Cosmos (Preview)
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> A funcionalidade de restauro pontual (modo de backup contínuo) para Azure Cosmos DB está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo explica o modelo de recursos para a funcionalidade de restauro ponto-a-tempo do Azure Cosmos (Preview). Explica os parâmetros que suportam a backup contínua e os recursos que podem ser restaurados em Azure Cosmos DB API para contas SQL e MongoDB.

## <a name="database-accounts-resource-model"></a>Modelo de recursos da conta de base de dados

O modelo de recursos da conta de base de dados é atualizado com algumas propriedades extra para suportar os novos cenários de restauro. Estas propriedades são **BackupPolicy, CreateMode e RestoreParameters.**

### <a name="backuppolicy"></a>Política de Apoio

Uma nova propriedade na política de backup de nível de conta indicada `Type` no `backuppolicy` parâmetro permite funcionalidades de backup contínua e restauração pontual. Este modo **chama-se backup contínuo.** Na pré-visualização pública, só é possível definir este modo ao criar a conta. Depois de ativado, todos os contentores e bases de dados criados dentro desta conta terão funcionalidades de backup contínua e restauro pontual ativadas por padrão.

> [!NOTE]
> Atualmente, a funcionalidade de restauro pontual está em pré-visualização pública e está disponível para Azure Cosmos DB API para as contas MongoDB e SQL. Depois de criar uma conta com modo contínuo, não pode mudá-la para um modo periódico.

### <a name="createmode"></a>CriarMode

Esta propriedade indica como a conta foi criada. Os valores possíveis são *Predefinidos* e *Restaurados.* Para realizar uma restauração, desa estabeleça este valor para *Restaurar* e forneça os valores adequados na `RestoreParameters` propriedade.

### <a name="restoreparameters"></a>Restauradores DeParmetros

O `RestoreParameters` recurso contém os detalhes da operação de restauro, incluindo, o ID da conta, o tempo para restaurar, e recursos que precisam de ser restaurados.

|Nome da Propriedade |Description  |
|---------|---------|
|restaurarMode  | O modo de restauro deve ser *PointInTime* |
|restaurarSource   |  O casoId da conta de origem a partir da qual a restauração será iniciada.       |
|restaurarTimestampInUtc  | Ponto no tempo na UTC para o qual a conta deve ser restaurada. |
|bases de dadosToRestore   | Lista de `DatabaseRestoreSource` objetos para especificar quais bases de dados e contentores devem ser restaurados. Se este valor estiver vazio, então toda a conta é restaurada.   |

**DatabaseRestoreResource** - Cada recurso representa uma única base de dados e todas as coleções nessa base de dados.

|Nome da Propriedade |Description  |
|---------|---------|
|base de dados Nome | O nome da base de dados |
| coleçõesAmmos| A lista de contentores nesta base de dados |

### <a name="sample-resource"></a>Recurso de amostra

O seguinte JSON é um recurso de conta de base de dados de amostra com cópia de segurança contínua ativada:

```json
{
  "location": "westus",
  "properties": {
    "databaseAccountOfferType": "Standard",
    "locations": [
      {
        "failoverPriority": 0,
        "locationName": "southcentralus",
        "isZoneRedundant": false
      }
    ],
    "createMode": "Restore",
    "restoreParameters": {
      "restoreMode": "PointInTime",
      "restoreSource": "/subscriptions/subid/providers/Microsoft.DocumentDB/locations/westus/restorableDatabaseAccounts/1a97b4bb-f6a0-430e-ade1-638d781830cc",
      "restoreTimestampInUtc": "2020-06-11T22:05:09Z",
      "databasesToRestore": [
        {
          "databaseName": "db1",
          "collectionNames": [
            "collection1",
            "collection2"
          ]
        },
        {
          "databaseName": "db2",
          "collectionNames": [
            "collection3",
            "collection4"
          ]
        }
      ]
    },
    "backupPolicy": {
      "type": "Continuous"
    },
}
}
```

## <a name="restorable-resources"></a>Recursos restauradores

Um conjunto de novos recursos e APIs está disponível para ajudá-lo a descobrir informações críticas sobre recursos, que podem ser restaurados, locais onde podem ser restaurados, e os cartões de tempo quando foram realizadas operações-chave sobre estes recursos.

> [!NOTE]
> Toda a API utilizada para enumerar estes recursos requer as seguintes permissões:
> * `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read`
> * `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read`

### <a name="restorable-database-account"></a>Conta de base de dados ressoável

Este recurso contém uma instância de conta de base de dados que pode ser restaurada. A conta de base de dados pode ser eliminada ou uma conta ao vivo. Contém informações que lhe permitem encontrar a conta de base de dados de origem que pretende restaurar.

|Nome da Propriedade |Description  |
|---------|---------|
| ID | O identificador único do recurso. |
| accountName | O nome da conta da base de dados global. |
| criaçãoTime | O tempo na UTC quando a conta foi criada.  |
| eliminaçãoTime | A hora na UTC quando a conta foi apagada.  Este valor está vazio se a conta estiver ao vivo. |
| apiType | O tipo API da conta DB Azure Cosmos. |
| restauraçãoLocações | A lista de locais onde a conta existia. |
| restorableLocações: localizaçãoName | O nome da região da conta regional. |
| restorableLocações: regionalDatabaseAccountInstanceI | O GUID da conta regional. |
| RestorableLocações: criaçãoTime | O tempo na UTC quando a conta regional foi criada.|
| RestorableLocações: eliminaçãoTime | O tempo na UTC quando a conta regional foi suprimida. Este valor é vazio se a conta regional estiver ao vivo.|

Para obter uma lista de todas as contas ressaráveis, consulte [as Contas de Base de Dados Restauraveis - lista](restorable-database-accounts-list.md) ou Contas de Base de [Dados Restauradoras- por](restorable-database-accounts-list-by-location.md) artigos de localização.

### <a name="restorable-sql-database"></a>Base de dados SQL restauradora

Cada recurso contém informações de um evento de mutação, como criação e eliminação que ocorreu na Base de Dados SQL. Esta informação pode ajudar em cenários onde a base de dados foi acidentalmente eliminada e se você precisa descobrir quando esse evento aconteceu.

|Nome da Propriedade |Description  |
|---------|---------|
| eventTimestamp | A hora na UTC quando a base de dados é criada ou eliminada. |
| proprietárioId | O nome da base de dados SQL. |
| proprietárioResourceId | O ID de recursos da base de dados SQL|
| operationType | O tipo de operação deste evento de base de dados. Aqui estão os valores possíveis:<br/><ul><li>Criar: evento de criação de base de dados</li><li>Excluir: evento de eliminação de bases de dados</li><li>Substituir: evento de modificação da base de dados</li><li>Funcionação do Sistema: evento de modificação da base de dados desencadeado pelo sistema. Este evento não é iniciado pelo utilizador</li></ul> |
| base de dados |As propriedades da base de dados SQL no momento do evento|

Para obter uma lista de todas as mutações da base de dados, consulte [as Bases de Dados De Sql Restauradoras - Artigo da lista.](restorable-sql-databases-list.md)

### <a name="restorable-sql-container"></a>Recipiente SQL restaurador

Cada recurso contém informações de um evento de mutação, como a criação e eliminação que ocorreram no contentor SQL. Esta informação pode ajudar em cenários onde o recipiente foi modificado ou eliminado, e se você precisa descobrir quando esse evento aconteceu.

|Nome da Propriedade |Description  |
|---------|---------|
| eventTimestamp    | A hora na UTC quando este evento de contentores aconteceu.|
| proprietárioId| O nome do contentor SQL.|
| proprietárioResourceId   | O ID de recursos do contentor SQL.|
| operationType | O tipo de operação deste evento de contentores. Aqui estão os valores possíveis: <br/><ul><li>Criar: evento de criação de contentores</li><li>Excluir: evento de eliminação de contentores</li><li>Substituição: evento de modificação do contentor</li><li>Funcionação do Sistema: evento de modificação do contentor desencadeado pelo sistema. Este evento não é iniciado pelo utilizador</li></ul> |
| contentor | As propriedades do contentor SQL no momento do evento.|

Para obter uma lista de todas as mutações de contentores na mesma base de dados, consulte [os Recipientes De Sql Restauraable - List.](restorable-sql-containers-list.md)

### <a name="restorable-sql-resources"></a>Recursos SQL restauradores

Cada recurso representa uma única base de dados e todos os contentores debaixo dessa base de dados.

|Nome da Propriedade |Description  |
|---------|---------|
| base de dados Nome  | O nome da base de dados SQL.
| coleçõesAmmos   | A lista de contentores SQL nesta base de dados.|

Para obter uma lista de bases de dados SQL e combinação de contentores que existem na conta no determinado timetamp e localização, consulte o [artigo Restorable Sql Resources - List.](restorable-sql-resources-list.md)

### <a name="restorable-mongodb-database"></a>Base de dados MongoDB restauradora

Cada recurso contém informações de um evento de mutação, como criação e eliminação que ocorreram na Base de Dados mongoDB. Estas informações podem ajudar no cenário em que a base de dados foi acidentalmente eliminada e o utilizador precisa de saber quando esse evento aconteceu.

|Nome da Propriedade |Description  |
|---------|---------|
|eventTimestamp| A hora na UTC quando este evento de base de dados aconteceu.|
| proprietárioId| O nome da base de dados do MongoDB. |
| proprietárioResourceId   | A identificação de recursos da base de dados mongoDB. |
| operationType |   O tipo de operação deste evento de base de dados. Aqui estão os valores possíveis:<br/><ul><li> Criar: evento de criação de base de dados</li><li> Excluir: evento de eliminação de bases de dados</li><li> Substituir: evento de modificação da base de dados</li><li> Funcionação do Sistema: evento de modificação da base de dados desencadeado pelo sistema. Este evento não é iniciado pelo utilizador </li></ul> |

Para obter uma lista de todas as mutações na base de [dados, consulte as Bases de Dados de Mongodb Restauradoras - Artigo da lista.](restorable-mongodb-databases-list.md)

### <a name="restorable-mongodb-collection"></a>Coleção MongoDB restauradora

Cada recurso contém informações de um evento de mutação, como a criação e a eliminação que ocorreram na Coleção MongoDB. Esta informação pode ajudar em cenários onde a recolha foi modificada ou eliminada, e o utilizador precisa de saber quando esse evento aconteceu.

|Nome da Propriedade |Description  |
|---------|---------|
| eventTimestamp |A hora na UTC quando este evento de coleção aconteceu. |
| proprietárioId| O nome da coleção MongoDB. |
| proprietárioResourceId   | O ID de recursos da coleção MongoDB. |
| operationType |O tipo de operação deste evento de recolha. Aqui estão os valores possíveis:<br/><ul><li>Criar: evento de criação de coleção</li><li>Excluir: evento de eliminação de recolha</li><li>Substituir: evento de modificação de recolha</li><li>Funcionação do Sistema: evento de modificação de recolha desencadeado pelo sistema. Este evento não é iniciado pelo utilizador</li></ul> |

Para obter uma lista de todas as mutações de contentores na mesma base de dados, consulte [o Restorable Mongodb Collections - Artigo da lista.](restorable-mongodb-collections-list.md)

### <a name="restorable-mongodb-resources"></a>Recursos mongoDB restauradores

Cada recurso representa uma única base de dados e todas as coleções nessa base de dados.

|Nome da Propriedade |Description  |
|---------|---------|
| base de dados Nome  |O nome da base de dados do MongoDB. |
| coleçõesAmmos | A lista de coleções mongoDB nesta base de dados. |

Para obter uma lista de todas as combinações de bases de dados e recolhas mongoDB que existem na conta no determinado timetamp e localização, consulte [recursos de Mongodb Restauraable -](restorable-mongodb-resources-list.md) Artigo da lista.

## <a name="next-steps"></a>Passos seguintes

* Configure e gere a cópia de segurança contínua utilizando [o portal Azure](continuous-backup-restore-portal.md), [PowerShell,](continuous-backup-restore-powershell.md) [CLI](continuous-backup-restore-command-line.md)ou [Azure Resource Manager](continuous-backup-restore-template.md).
* [Gerir as permissões necessárias](continuous-backup-restore-permissions.md) para restaurar os dados com o modo de backup contínuo.
