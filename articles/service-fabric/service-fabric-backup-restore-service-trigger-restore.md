---
title: Restaurar a cópia de segurança em Tecido de Serviço Azure
description: Utilize a função de cópia de segurança periódica e restauro no Tecido de Serviço para restaurar os dados de uma cópia de segurança dos dados da sua aplicação.
author: aagup
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: 1737102ee652cc2263bd0a908c1336bc93a6757b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75377910"
---
# <a name="restoring-backup-in-azure-service-fabric"></a>Restaurar a cópia de segurança em Tecido de Serviço Azure

No Tecido de Serviço Azure, os serviços fiáveis e os atores fiáveis podem manter um estado mutável e autoritário após a conclusão de uma operação de pedido e resposta. Um serviço audato pode cair por muito tempo ou perder informações por causa de um desastre. Se isso acontecer, o serviço tem de ser restaurado a partir do último backup aceitável para que possa continuar a funcionar.

Por exemplo, pode configurar um serviço para fazer o backup dos seus dados para proteger contra os seguintes cenários:

- **Caso de recuperação de desastres**: Perda permanente de todo um cluster de Tecido de Serviço.
- **Caso de perda de dados**: Perda permanente da maioria das réplicas de uma partição de serviço.
- **Caso de perda de dados**: Supressão acidental ou corrupção do serviço. Por exemplo, um administrador apaga erroneamente o serviço.
- **Caso de corrupção de dados**: Bugs no serviço causam corrupção de dados. Por exemplo, a corrupção de dados pode ocorrer quando uma atualização de código de serviço escreve dados defeituosos para uma Coleção Fiável. Nesse caso, poderá ter de restaurar o código e os dados para um estado anterior.

## <a name="prerequisites"></a>Pré-requisitos

- Para desencadear uma restauração, o Serviço de Análise de _Falhas (FAS)_ deve ser ativado para o cluster.
- O Serviço de _Restauro de Cópiade Cópia (BRS)_ criou a cópia de segurança.
- O restauro só pode ser acionado numa divisória.
- Instale microsoft.serviceFabric.Powershell.Http Módulo [Em Pré-visualização] para fazer chamadas de configuração.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

- Certifique-se de que `Connect-SFCluster` o Cluster está ligado utilizando o comando antes de efazer qualquer pedido de configuração utilizando o Microsoft.ServiceFabric.Powershell.Http Module.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```


## <a name="triggered-restore"></a>Restauro desencadeado

Um restauro pode ser desencadeado para qualquer um dos seguintes cenários:

- Recuperação de dados para _recuperação de desastres._
- Restaurar dados para _a corrupção de dados/perda_de dados .

### <a name="data-restore-in-the-case-of-disaster-recovery"></a>Recuperação de dados em caso de recuperação de desastres

Se todo um cluster de Tecido de Serviço for perdido, pode recuperar os dados para as divisórias do serviço Fiável Estatal e dos Atores Fiáveis. A cópia de segurança desejada pode ser selecionada a partir da lista quando utilizar o [GetBackupAPI com detalhes](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getbackupsfrombackuplocation)de armazenamento de cópia de segurança . A enumeração de cópia de segurança pode ser para uma aplicação, serviço ou partição.

Para o exemplo seguinte, assuma que o cluster perdido é o mesmo cluster referido em [Permitir backup periódico para serviço sinuoso fiável e atores fiáveis](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors). Neste caso, `SampleApp` é implementado com a política de backup ativada, e as cópias de segurança estão configuradas para o Armazenamento Azure.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Powershell usando Microsoft.ServiceFabric.Powershell.Http Módulo

```powershell
Get-SFBackupsFromBackupLocation -Application -ApplicationName 'fabric:/SampleApp' -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>Chamada de descanso usando Powershell

Execute um script PowerShell para utilizar a API REST para devolver uma `SampleApp` lista das cópias de segurança criadas para todas as divisórias dentro da aplicação. A API requer a informação de armazenamento de reserva para listar as cópias de segurança disponíveis.

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

Saída da amostra para o ensaio acima:

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

Para desencadear o restauro, escolha uma das cópias de segurança. Por exemplo, o atual backup para a recuperação de desastres pode ser o seguinte backup:

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

Também precisa escolher uma partição de destino no cluster alternativo, conforme detalhado no esquema de [partição.](service-fabric-concepts-partitioning.md#get-started-with-partitioning) A cópia de segurança alternativa do cluster é restaurada à divisória especificada no esquema de partição do cluster perdido original.

Se o ID de `1c42c47f-439e-4e09-98b9-88b8f60800c6`partição em cluster alternativo for, pode mapeá-lo para o ID `974bd92a-b395-4631-8a7f-53bd4ae9cf22` original da partilha de cluster, comparando a chave alta e a tecla baixa para a divisão de _variação (UniformInt64Partition)_.

Para _partição nomeada,_ o valor do nome é comparado para identificar a partição do alvo em cluster alternativo.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Powershell usando Microsoft.ServiceFabric.Powershell.Http Módulo

```powershell

Restore-SFPartition  -PartitionId '1c42c47f-439e-4e09-98b9-88b8f60800c6' -BackupId 'b0035075-b327-41a5-a58f-3ea94b68faa4' -BackupLocation 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip' -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>Chamada de descanso usando Powershell

Solicita a restauração contra a divisória do cluster de reserva utilizando a seguinte [API de restauro:](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition)

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

Pode acompanhar o progresso de um restauro com trackRestoreProgress.

### <a name="using-service-fabric-explorer"></a>Usando o Explorador de Tecido de Serviço
Pode desencadear um restauro do Service Fabric Explorer. Certifique-se de que o Modo Avançado está ativado nas definições do Service Fabric Explorer.
1. Selecione as divisórias desejadas e clique em Ações. 
2. Selecione restaurar a partição do gatilho e preencher informações para o Azure:

    ![Restaurar a partição do gatilho][2]

    ou FileShare:

    ![Partição de gatilho restaurar fileshare][3]

### <a name="data-restore-for-_data-corruption__data-loss_"></a>Recuperação de dados para perda de _dados de corrupção_/_de dados_

Para perda de _dados_ ou corrupção de _dados,_ divisórias apoiadas para serviços fiáveis e divisórias de Atores Fiáveis podem ser restauradas a qualquer uma das cópias de backup escolhidas.

O exemplo seguinte é a continuação da cópia de segurança periódica para o serviço fiável e fiável e os [atores fiáveis.](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors) Neste exemplo, uma política de backup está ativada para a partição, e o serviço está a fazer backups numa frequência desejada no Armazenamento Azure.

Selecione uma cópia de segurança da saída do [GetBackupAPI](service-fabric-backuprestoreservice-quickstart-azurecluster.md#list-backups). Neste cenário, a cópia de segurança é gerada a partir do mesmo cluster de antes.

Para desencadear o restauro, escolha uma cópia de segurança da lista. Para a corrupção de dados de _perda_/de_dados_atuais, selecione a seguinte cópia de segurança:

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

Para restaurar a API, forneça os detalhes _de BackupId_ e _BackupLocation._ O cluster tem cópia de segurança ativada para que o Serviço de Restauro de Backup de Tecidos de Serviço _(BRS)_ identifique a localização de armazenamento correta a partir da política de backup associada.


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

Pode rastrear o progresso do restauro utilizando o TrackRestoreProgress.

## <a name="track-restore-progress"></a>Pista restaurar o progresso

Uma partição de um serviço fiável ou ator fiável aceita apenas um pedido de restauro de cada vez. Uma partição só aceita outro pedido após a conclusão do pedido de restauro atual. Ao mesmo tempo, podem ser desencadeados múltiplos pedidos de restauro em diferentes divisórias.

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
2. **InProgress**: Um estado de restauro _inProgress_ indica que está a ocorrer um restauro na divisória com a cópia de segurança mencionada a pedido. A partição relata o estado de perda de _dados._
    ```
    RestoreState  : RestoreInProgress
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
    
3. **Sucesso**, **Falha**, ou **Timeout**: Um restauro solicitado pode ser concluído em qualquer um dos seguintes estados. Cada estado tem os seguintes detalhes de importância e resposta:
    - **Sucesso**: Um estado de restauração de _sucesso_ indica um estado de partição recuperado. Os relatórios de partição _RestoredEpoch_ e _RestoredLSN,_ juntamente com o tempo na UTC.

        ```
        RestoreState  : Success
        TimeStampUtc  : 2018-11-22T11:22:33Z
        RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
        RestoredLsn   : 3552
        ```        
    - **Falha**: Um estado de reestabelecimento de _falhas_ indica a falha do pedido de restauro. A causa da falha foi reportada.

        ```
        RestoreState  : Failure
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```
    - **Timeout**: Um estado de restabelecimento do _timeout_ indica que o pedido tem tempo de paragem. Crie um novo pedido de restauro com um maior [RestoreTimeout](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout). O tempo de paragem padrão é de 10 minutos. Certifique-se de que a partição não está num estado de perda de dados antes de pedir o restauro novamente.
     
        ```
        RestoreState  : Timeout
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```

## <a name="automatic-restore"></a>Restauro automático

Pode configurar o serviço fiável e as divisórias de Atores Fiáveis no cluster Service Fabric para _restauro automático_. Na política de `AutoRestore` backup definida para _verdade._ Permitir a _restauração automática_ restaura automaticamente os dados da última cópia de segurança quando a perda de dados é reportada. Para obter mais informações, consulte:

- [Facilitação de restauração automática na política de backup](service-fabric-backuprestoreservice-configure-periodic-backup.md#auto-restore-on-data-loss)
- [Referência da API da Divisão de Restauração](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition)
- [ObterPartitionRestoreProgress API referência](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionrestoreprogress)

## <a name="next-steps"></a>Passos seguintes
- [Compreender a configuração da cópia de segurança periódica](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Cópia de segurança restaurar referência REST API](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[2]: ./media/service-fabric-backuprestoreservice/restore-partition-backup.png
[3]: ./media/service-fabric-backuprestoreservice/restore-partition-fileshare.png