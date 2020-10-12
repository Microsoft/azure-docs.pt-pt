---
title: Restaurar a cópia de segurança no Tecido de Serviço Azure
description: Utilize a funcionalidade de backup e restauro periódico no Tecido de Serviço para restaurar os dados a partir de uma cópia de segurança dos dados da sua aplicação.
author: aagup
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: f98bf4f4518abd5f1b1a826e355c851acc055852
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86246695"
---
# <a name="restoring-backup-in-azure-service-fabric"></a>Restaurar a cópia de segurança no Tecido de Serviço Azure

Na Azure Service Fabric, os serviços estatais fiáveis e os atores fiáveis podem manter um estado mutável e autoritário após a conclusão de uma transação de pedido e resposta. Um serviço estatal pode ficar por muito tempo ou perder informação por causa de um desastre. Se isso acontecer, o serviço tem de ser restaurado a partir da mais recente cópia de segurança aceitável para que possa continuar a funcionar.

Por exemplo, pode configurar um serviço para fazer o back up dos seus dados para proteger contra os seguintes cenários:

- **Caso de recuperação de desastres**: Perda permanente de todo um cluster de Tecidos de Serviço.
- **Caso de perda de dados:** Perda permanente da maioria das réplicas de uma divisória de serviço.
- **Caso de perda de dados:** Supressão acidental ou corrupção do serviço. Por exemplo, um administrador elimina erroneamente o serviço.
- **Caso de corrupção de dados**: Bugs no serviço causam corrupção de dados. Por exemplo, a corrupção de dados pode ocorrer quando uma atualização de código de serviço escreve dados defeituosos para uma Coleção Fiável. Neste caso, poderá ter de devolver o código e os dados a um estado anterior.

## <a name="prerequisites"></a>Pré-requisitos

- Para desencadear uma restauração, o _Serviço de Análise de Falhas (FAS)_ tem de ser ativado para o cluster.
- O _Serviço de Restauro de Cópias de Segurança (BRS)_ criou a cópia de segurança.
- O restauro só pode ser desencadeado numa partição.
- Instale microsoft.serviceFabric.Powershell.Http Module [In Preview] para fazer chamadas de configuração.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

- Certifique-se de que o Cluster está ligado utilizando o `Connect-SFCluster` comando antes de escoar qualquer pedido de configuração utilizando o Módulo Microsoft.ServiceFabric.Powershell.Http.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```


## <a name="triggered-restore"></a>Restauro desencadeado

Um restauro pode ser desencadeado para qualquer um dos seguintes cenários:

- Recuperação de dados para _recuperação de desastres._
- Os dados são restaurados para _a corrupção/perda de dados._

### <a name="data-restore-in-the-case-of-disaster-recovery"></a>Recuperação de dados em caso de recuperação de desastres

Se um cluster de tecido de serviço inteiro for perdido, você pode recuperar os dados para as divisórias do serviço Reliable Stateful e de Reliable Actors. A cópia de segurança desejada pode ser selecionada na lista quando utilizar o [GetBackupAPI com detalhes de armazenamento de cópias de segurança.](/rest/api/servicefabric/sfclient-api-getbackupsfrombackuplocation) A enumeração de backup pode ser para uma aplicação, serviço ou partição.

Para o exemplo seguinte, assuma que o cluster perdido é o mesmo cluster que é referido em [Habilitar backup periódico para serviços fiáveis stateful e atores fiáveis](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors). Neste caso, `SampleApp` é implementado com a política de backup ativada, e as cópias de segurança são configuradas para o Azure Storage.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Powershell usando Microsoft.ServiceFabric.Powershell.Http Módulo

```powershell
Get-SFBackupsFromBackupLocation -Application -ApplicationName 'fabric:/SampleApp' -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>Chamada de descanso usando Powershell

Execute um script PowerShell para utilizar a API REST para devolver uma lista das cópias de segurança criadas para todas as divisórias dentro da `SampleApp` aplicação. A API requer a informação de armazenamento de backup para listar as cópias de segurança disponíveis.

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

Saída da amostra para a execução acima:

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

Para ativar a restauração, escolha uma das cópias de segurança. Por exemplo, o atual backup para a recuperação de desastres pode ser o seguinte backup:

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

Para restaurar a API, você precisa fornecer os detalhes _backupId_ e _BackupLocation._

Também é necessário escolher uma divisória de destino no cluster alternativo, conforme detalhado no [esquema de partição.](service-fabric-concepts-partitioning.md#get-started-with-partitioning) A cópia de segurança do cluster alternativo é restaurada à partição especificada no esquema de partição do cluster perdido original.

Se o ID de partição em cluster alternativo `1c42c47f-439e-4e09-98b9-88b8f60800c6` for, pode mapeá-lo para o ID original de partição `974bd92a-b395-4631-8a7f-53bd4ae9cf22` de cluster, comparando a chave alta e a tecla baixa para _partição ranged (UniformInt64Partition)_.

Para _Partição Nomeada,_ o valor do nome é comparado para identificar a partição-alvo em cluster alternativo.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Powershell usando Microsoft.ServiceFabric.Powershell.Http Módulo

```powershell

Restore-SFPartition  -PartitionId '1c42c47f-439e-4e09-98b9-88b8f60800c6' -BackupId 'b0035075-b327-41a5-a58f-3ea94b68faa4' -BackupLocation 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip' -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>Chamada de descanso usando Powershell

Solicita a restauração contra a partição do cluster de backup utilizando a seguinte [API de Restauro:](/rest/api/servicefabric/sfclient-api-restorepartition)

```powershell

$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$RestorePartitionReference = @{
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4'
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'
    BackupStorage  = $StorageInfo
}

$body = (ConvertTo-Json $RestorePartitionReference) 
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/1c42c47f-439e-4e09-98b9-88b8f60800c6/$/Restore?api-version=6.4" 

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Você pode acompanhar o progresso de um restauro com TrackRestoreProgress.

### <a name="using-service-fabric-explorer"></a>Usando o Explorador de Tecido de Serviço
Pode desencadear uma restauração do Service Fabric Explorer. Certifique-se de que o Modo Avançado foi ativado nas definições do Explorador de Tecidos de Serviço.
1. Selecione as divisórias desejadas e clique em Ações. 
2. Selecione Trigger Partition Restore e preencha as informações para Azure:

    ![Restaurar a partição do gatilho][2]

    ou FileShare:

    ![Trigger Partition Restore Fileshare][3]

### <a name="data-restore-for-_data-corruption__data-loss_"></a>Recuperação de dados para perda de _dados_de / _corrupção de dados_

Para a perda de _dados_ ou corrupção de _dados,_ as divisórias apoiadas para serviços estatais fiáveis e divisórias de atores fiáveis podem ser restauradas em qualquer uma das cópias de segurança escolhidas.

O exemplo a seguir é a continuação da [reserva periódica para um serviço estadual fiável e para os atores fiáveis.](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors) Neste exemplo, uma política de backup está ativada para a partição, e o serviço está a fazer backups numa frequência desejada no Azure Storage.

Selecione uma cópia de segurança da saída do  [GetBackupAPI](service-fabric-backuprestoreservice-quickstart-azurecluster.md#list-backups). Neste cenário, a cópia de segurança é gerada a partir do mesmo cluster que antes.

Para ativar a restauração, escolha uma cópia de segurança da lista. Para a corrupção de _dados_de perda de / _dados_em vigor, selecione a seguinte cópia de segurança:

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

Para restaurar a API, forneça os detalhes _do BackupId_ e _backupLocation._ O cluster tem a cópia de segurança ativada para que o Serviço de Restauro de Cópia de Segurança do Tecido de Serviço _(BRS)_ identifique a localização de armazenamento correta da política de backup associada.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Powershell usando Microsoft.ServiceFabric.Powershell.Http Módulo

```powershell
Restore-SFPartition  -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' -BackupId 'b0035075-b327-41a5-a58f-3ea94b68faa4' -BackupLocation 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'

```

#### <a name="rest-call-using-powershell"></a>Chamada de descanso usando Powershell

```powershell
$RestorePartitionReference = @{
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4',
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'
}

$body = (ConvertTo-Json $RestorePartitionReference)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Restore?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Pode acompanhar o progresso do restauro utilizando trackRestoreProgress.

## <a name="track-restore-progress"></a>Faixa restaurar o progresso

Uma divisão de um serviço de stateful fiável ou um ator fiável aceita apenas um pedido de restauro de cada vez. Uma divisória só aceita outro pedido após a conclusão do pedido de restauro atual. Vários pedidos de restauro podem ser desencadeados em diferentes divisórias ao mesmo tempo.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Powershell usando Microsoft.ServiceFabric.Powershell.Http Módulo

```powershell
    Get-SFPartitionRestoreProgress -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22'
```

#### <a name="rest-call-using-powershell"></a>Chamada de descanso usando Powershell

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetRestoreProgress?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

$restoreResponse = (ConvertFrom-Json $response.Content)
$restoreResponse | Format-List
```

O pedido de restauro progride na seguinte ordem:

1. **Aceite**: Um estado de restauro _aceite_ indica que a partição solicitada foi acionada com parâmetros de pedido corretos.
    ```
    RestoreState  : Accepted
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
2. **InProgress**: Um estado de restauro _inProgress_ indica que está a ocorrer uma restauração na partição com o backup mencionado a pedido. A partição reporta o estado _da perda de dados._
    ```
    RestoreState  : RestoreInProgress
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
    
3. **Sucesso**, **Fracasso**, ou **Timeout**: Uma restauração solicitada pode ser concluída em qualquer um dos seguintes estados. Cada estado tem os seguintes dados de significado e resposta:
    - **Sucesso**: Um estado de restauração _do sucesso_ indica um estado de partição recuperado. Os relatórios de partição _RestoredEpoch_ e _RestoredLSN_ afirma, juntamente com o tempo na UTC.

        ```
        RestoreState  : Success
        TimeStampUtc  : 2018-11-22T11:22:33Z
        RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
        RestoredLsn   : 3552
        ```        
    - **Falha**: Um estado de restauro _de falha_ indica a falha do pedido de restauro. A causa da falha é reportada.

        ```
        RestoreState  : Failure
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```
    - **Tempo limite**: Um estado de restauro _do timeout_ indica que o pedido tem tempo limite. Crie um novo pedido de restauro com maior [RestoreTimeout](/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout). O tempo limite de tempo é de 10 minutos. Certifique-se de que a partição não está num estado de perda de dados antes de pedir restauro novamente.
     
        ```
        RestoreState  : Timeout
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```

## <a name="automatic-restore"></a>Restauro automático

Pode configurar o serviço Reliable Stateful e as divisórias Reliable Actors no cluster de tecido de serviço para _restauro automático._ Na política de backup definida `AutoRestore` como _verdadeira_. Permitir a _restauração automática_ restaura automaticamente os dados da cópia de segurança mais recente da partição quando a perda de dados é reportada. Para obter mais informações, consulte:

- [Ativação de restauro automático na política de backup](service-fabric-backuprestoreservice-configure-periodic-backup.md#auto-restore-on-data-loss)
- [Referência da API do RestorePartition](/rest/api/servicefabric/sfclient-api-restorepartition)
- [Referência API GetPartitionRestoreProgress](/rest/api/servicefabric/sfclient-api-getpartitionrestoreprogress)

## <a name="next-steps"></a>Passos seguintes
- [Compreender a configuração da cópia de segurança periódica](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Backup restaurar referência API REST](/rest/api/servicefabric/sfclient-index-backuprestore)

[2]: ./media/service-fabric-backuprestoreservice/restore-partition-backup.png
[3]: ./media/service-fabric-backuprestoreservice/restore-partition-fileshare.png
