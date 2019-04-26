---
title: Restaurar cópia de segurança no Azure Service Fabric | Documentos da Microsoft
description: Utilize a cópia de segurança periódica e restaurar a funcionalidade do Service Fabric para restaurar dados a partir de uma cópia de segurança de dados da sua aplicação.
services: service-fabric
documentationcenter: .net
author: aagup
manager: chackdan
editor: aagup
ms.assetid: 802F55B6-6575-4AE1-8A8E-C9B03512FF88
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: a82004fdd6bbb4eda0842670f210f846f9446384
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60310881"
---
# <a name="restoring-backup-in-azure-service-fabric"></a>Restaurar cópia de segurança no Azure Service Fabric

No Azure Service Fabric, com monitoração de estado de Reliable services e Reliable Actors podem manter um Estado mutável e autoritativo após a conclusão de uma transação de solicitação e resposta. Um serviço com estado pode descer durante muito tempo ou perderá informações devido a um desastre. Se isto acontecer, o serviço tem de ser restaurado a partir da mais recente aceitável cópia de segurança para que ele pode continuar a trabalhar.

Por exemplo, pode configurar um serviço para fazer backup de seus dados para proteção contra os seguintes cenários:

- **Caso de recuperação após desastre**: Perda permanente de um cluster do Service Fabric inteiro.
- **Caso de perda de dados**: Perda permanente de a maioria das réplicas de uma partição de serviço.
- **Caso de perda de dados**: Eliminação ou corrupção acidental do serviço. Por exemplo, um administrador elimina incorretamente o serviço.
- **Caso de danos nos dados**: Bugs no serviço danificar os dados. Por exemplo, danos nos dados podem ocorrer quando uma atualização de código do serviço escreve dados com falhas para uma coleção fiável. Nesse caso, poderá ter de restaurar o código e os dados para um estado anterior.

## <a name="prerequisites"></a>Pré-requisitos

- Para acionar uma restauração, o _serviço de análise de falhas (FAS)_ tem de estar ativada para o cluster.
- O _serviço de restaurar a cópia de segurança (BRS)_ criada a cópia de segurança.
- Só é possível acionar o restauro numa partição.

## <a name="triggered-restore"></a>Restauro acionado

Um restauro pode ser acionado por qualquer um dos seguintes cenários:

- Restauro de dados para _recuperação após desastre_.
- Restauro de dados para _perda de dados/Corrupção de dados_.

### <a name="data-restore-in-the-case-of-disaster-recovery"></a>Restauro de dados no caso de recuperação após desastre

Se um cluster do Service Fabric completo é perdido, pode recuperar os dados para as partições do serviço com estado fiável e Reliable Actors. A cópia de segurança pretendida pode ser selecionada na lista quando usa [GetBackupAPI com detalhes de armazenamento de cópia de segurança](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getbackupsfrombackuplocation). A enumeração de cópia de segurança pode ser para uma aplicação, serviço ou partição.

No exemplo seguinte, vamos supor que o cluster perder-se é o mesmo cluster que é mencionado nos [cópia de segurança periódica do ativação de serviço com estado fiável e Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors). Neste caso, `SampleApp` é implementado com a política de cópia de segurança ativada, e as cópias de segurança estão configuradas para o armazenamento do Azure.

Executar um script do PowerShell para utilizar a API REST para devolver uma lista das cópias de segurança criados para todas as partições dentro do `SampleApp` aplicação. A API requer que as informações de armazenamento de cópia de segurança para listar as cópias de segurança disponíveis.

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$BackupEntity = @{
    EntityKind = 'Application'
    ApplicationName='fabric:/SampleApp'
}

$BackupLocationAndEntityInfo = @{
    Storage = $StorageInfo
    BackupEntity = $BackupEntity
}

$body = (ConvertTo-Json $BackupLocationAndEntityInfo)
$url = "https://myalternatesfcluster.southcentralus.cloudapp.azure.com:19080/BackupRestore/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```

Execute a saída de exemplo acima:

```
BackupId                : b9577400-1131-4f88-b309-2bb1e943322c
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 20.55.16.zip
BackupType              : Full
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3334
CreationTimeUtc         : 2018-04-06T20:55:16Z
FailureError            : 
*
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            :
*
BackupId                : 69436834-c810-4163-9386-a7a800f78359
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.25.36.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3764
CreationTimeUtc         : 2018-04-06T21:25:36Z
FailureError            :
```

Para acionar o restauro, escolha uma das cópias de segurança. Por exemplo, a cópia de segurança atual para recuperação após desastre pode ser a cópia de segurança seguinte:

```
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            :
```

Para a API de restauro, tem de fornecer a _BackupId_ e _BackupLocation_ detalhes.

Também tem de escolher uma partição de destino no cluster conforme detalhado no alternativo a [esquema de partição](service-fabric-concepts-partitioning.md#get-started-with-partitioning). A cópia de segurança do cluster alternativo é restaurada para a partição especificada no esquema de partição do cluster original perdido.

Se o ID de partição no cluster alternativo for `1c42c47f-439e-4e09-98b9-88b8f60800c6`, pode mapeá-lo para o ID de partição de cluster original `974bd92a-b395-4631-8a7f-53bd4ae9cf22` comparando a chave de alta e baixa chave para _Ranged (UniformInt64Partition) de criação de partições_.

Para _com o nome de criação de partições_, o valor de nome é comparado para identificar a partição de destino em cluster alternativo.

Pedir o restauro em relação a partição de cluster de cópia de segurança através do seguinte [restaurar API](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition):

```powershell
$RestorePartitionReference = @{
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4'
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'
}

$body = (ConvertTo-Json $RestorePartitionReference) 
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/1c42c47f-439e-4e09-98b9-88b8f60800c6/$/Restore?api-version=6.4" 

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Pode controlar o progresso de um restauro com TrackRestoreProgress.

### <a name="data-restore-for-data-corruptiondata-loss"></a>Restauro de dados para _Corrupção de dados_/_perda de dados_

Para _perda de dados_ ou _danos nos dados_, partições de cópia de segurança para serviço com estado fiável e partições de Reliable Actors podem ser restauradas a qualquer uma das cópias de segurança escolhidas.

O exemplo seguinte é uma continuação de [cópia de segurança periódica do ativação de serviço com estado fiável e Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors). Neste exemplo, uma política de cópia de segurança está ativada para a partição e o serviço está a efetuar cópias de segurança a uma frequência pretendida no armazenamento do Azure.

Selecione uma cópia de segurança a partir da saída [GetBackupAPI](service-fabric-backuprestoreservice-quickstart-azurecluster.md#list-backups). Neste cenário, a cópia de segurança é gerada a partir do mesmo cluster como antes.

Para acionar o restauro, escolha uma cópia de segurança da lista. Para a atual _perda de dados_/_danos nos dados_, selecione a cópia de segurança seguinte:

```
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            :
```

Para a API de restauro, forneça o _BackupId_ e _BackupLocation_ detalhes. O cluster tem de cópia de segurança ativada por isso, o Service Fabric _serviço de restaurar a cópia de segurança (BRS)_ identifica a localização de armazenamento correta da política de cópia de segurança associada.

```powershell
$RestorePartitionReference = @{
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4',
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'
}

$body = (ConvertTo-Json $RestorePartitionReference)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Restore?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Pode acompanhar o progresso de restauro utilizando TrackRestoreProgress.

## <a name="track-restore-progress"></a>Controlar o progresso de restauro

Uma partição de um serviço com estado fiável ou Reliable Actor aceita apenas um pedido de restauro por vez. Uma partição só aceita a outro pedido depois de concluído o pedido de restauro atual. Vários pedidos de restauro podem ser acionados em partições diferentes ao mesmo tempo.

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetRestoreProgress?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

$restoreResponse = (ConvertFrom-Json $response.Content)
$restoreResponse | Format-List
```

O andamento de pedido de restauro na seguinte ordem:

1. **Aceite**: Uma _aceite_ restaurar estado indica que a partição pedida foi acionada com parâmetros de pedido correto.
    ```
    RestoreState  : Accepted
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
2. **InProgress**: Uma _InProgress_ restaurar estado indica que um restauro está a ocorrer na partição com a cópia de segurança mencionada no pedido. Os relatórios de partição a _correção de perda_ estado.
    ```
    RestoreState  : RestoreInProgress
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
    
3. **Sucesso**, **falha**, ou **tempo limite**: Um pedido de restauro pode ser concluído em qualquer um dos seguintes Estados. Cada Estado possui os seguintes detalhes de significado e a resposta:
    - **Success**: R _êxito_ restaurar estado indica um Estado de partição voltou a ter. Os relatórios de partição _RestoredEpoch_ e _RestoredLSN_ Estados juntamente com a hora em UTC.

        ```
        RestoreState  : Success
        TimeStampUtc  : 2018-11-22T11:22:33Z
        RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
        RestoredLsn   : 3552
        ```        
    - **Falha de**: R _falha_ restaurar estado indica a falha do pedido de restauro. A causa da falha é comunicada.

        ```
        RestoreState  : Failure
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```
    - **Tempo limite**: R _tempo limite_ restaurar estado indica que o pedido tem o tempo limite. Criar um novo pedido de restauro com maior [RestoreTimeout](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout). O tempo limite predefinido é 10 minutos. Certifique-se de que a partição não está num Estado de perda de dados antes de solicitar novamente o restauro.
     
        ```
        RestoreState  : Timeout
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```

## <a name="automatic-restore"></a>Restauro automático

Pode configurar o serviço com estado fiável e partições de Reliable Actors em recursos de infraestrutura do serviço de cluster para _auto restauro_. Na política de cópia de segurança definido `AutoRestore` para _true_. Habilitando _auto restauro_ restaura automaticamente os dados da cópia de segurança mais recente do partição quando é reportada perda de dados. Para obter mais informações, consulte:

- [Ativação de restauro automático na política de cópia de segurança](service-fabric-backuprestoreservice-configure-periodic-backup.md#auto-restore-on-data-loss)
- [Referência da API de RestorePartition](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition)
- [Referência da API de GetPartitionRestoreProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionrestoreprogress)

## <a name="next-steps"></a>Passos Seguintes
- [Configuração de cópia de segurança periódica compreensão](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Referência da REST API de restauro de cópia de segurança](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)
