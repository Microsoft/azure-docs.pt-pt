---
title: Backup sob demanda no Azure Service Fabric
description: Use o recurso de backup e restauração no Service Fabric para fazer backup dos dados do aplicativo de acordo com a necessidade.
author: aagup
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: d5eada62bec49fe771373671e9438d2786d6b165
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75458415"
---
# <a name="on-demand-backup-in-azure-service-fabric"></a>Backup sob demanda no Azure Service Fabric

Você pode fazer backup de dados de serviços confiáveis com estado e Reliable Actors para resolver os desastres ou cenários de perda de dados.

O Azure Service Fabric tem recursos para o [backup periódico de dados](service-fabric-backuprestoreservice-quickstart-azurecluster.md) e o backup de dados de acordo com a necessidade. O backup sob demanda é útil porque protege contra _perda de dados_/_corrupção de dados_ devido a alterações planejadas no serviço subjacente ou em seu ambiente.

Os recursos de backup sob demanda são úteis para capturar o estado dos serviços antes de disparar manualmente uma operação de serviço ou de ambiente de serviço. Por exemplo, se você fizer uma alteração em binários de serviço ao atualizar ou fazer downgrade do serviço. Nesse caso, o backup sob demanda pode ajudar a proteger os dados contra corrupção de bugs de código do aplicativo.
## <a name="prerequisites"></a>Pré-requisitos

- Instale o módulo Microsoft. perfabric. PowerShell. http [em versão prévia] para fazer chamadas de configuração.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

- Verifique se o cluster está conectado usando o comando `Connect-SFCluster` antes de fazer qualquer solicitação de configuração usando o módulo Microsoft. onfabric. PowerShell. http.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```


## <a name="triggering-on-demand-backup"></a>Disparando backup sob demanda

O backup sob demanda requer detalhes de armazenamento para carregar arquivos de backup. Especifique o local de backup sob demanda, na política de backup periódico ou em uma solicitação de backup sob demanda.

### <a name="on-demand-backup-to-storage-specified-by-a-periodic-backup-policy"></a>Backup sob demanda para o armazenamento especificado por uma política de backup periódico

Você pode configurar a política de backup periódico para usar uma partição de um serviço confiável com estado ou um ator confiável para backup extra sob demanda para armazenamento.

O caso a seguir é a continuação do cenário na [habilitação de backup periódico para serviço confiável com estado e Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors). Nesse caso, você permite que uma política de backup use uma partição e um backup ocorra em uma frequência definida no armazenamento do Azure.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell usando o módulo Microsoft. infabric. PowerShell. http

```powershell

Backup-SFPartition -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' 

```

#### <a name="rest-call-using-powershell"></a>Chamada REST usando o PowerShell

Use a API [BackupPartition](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition) para configurar o disparo para o backup sob demanda para a ID de partição `974bd92a-b395-4631-8a7f-53bd4ae9cf22`.

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Use a API [GetBackupProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) para habilitar o acompanhamento do [progresso do backup sob demanda](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress).

### <a name="on-demand-backup-to-specified-storage"></a>Backup sob demanda para o armazenamento especificado

Você pode solicitar backup sob demanda para uma partição de um serviço confiável com estado ou um ator confiável. Forneça as informações de armazenamento como parte da solicitação de backup sob demanda.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell usando o módulo Microsoft. infabric. PowerShell. http

```powershell

Backup-SFPartition -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' -AzureBlobStore -ConnectionString  'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>Chamada REST usando o PowerShell

Use a API [BackupPartition](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition) para configurar o disparo para o backup sob demanda para a ID de partição `974bd92a-b395-4631-8a7f-53bd4ae9cf22`. Inclua as seguintes informações de armazenamento do Azure:

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$OnDemandBackupRequest = @{
    BackupStorage = $StorageInfo
}

$body = (ConvertTo-Json $OnDemandBackupRequest)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Você pode usar a API [GetBackupProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) para configurar o acompanhamento do [progresso do backup sob demanda](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress).

### <a name="using-service-fabric-explorer"></a>Usando Service Fabric Explorer
Verifique se o modo avançado foi habilitado nas configurações de Service Fabric Explorer.
1. Selecione as partições desejadas e clique em ações. 
2. Selecione disparar backup de partição e preencha as informações do Azure:

    ![Disparar backup de partição][0]

    ou FileShare:

    ![Disparar compartilhamento de backup de partição][1]

## <a name="tracking-on-demand-backup-progress"></a>Acompanhamento do progresso do backup sob demanda

Uma partição de um serviço confiável com estado ou um ator confiável aceita apenas uma solicitação de backup sob demanda por vez. Outra solicitação pode ser aceita somente após a conclusão da solicitação de backup sob demanda atual.

Partições diferentes podem disparar solicitações de backup sob demanda ao mesmo tempo.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell usando o módulo Microsoft. infabric. PowerShell. http

```powershell

Get-SFPartitionBackupProgress -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22'

```
#### <a name="rest-call-using-powershell"></a>Chamada REST usando o PowerShell

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetBackupProgress?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3' 
$backupResponse = (ConvertFrom-Json $response.Content) 
$backupResponse
```

As solicitações de backup sob demanda podem estar nos seguintes Estados:

- **Aceito**: o backup foi iniciado na partição e está em andamento.
  ```
  BackupState             : Accepted
  TimeStampUtc            : 0001-01-01T00:00:00Z
  BackupId                : 00000000-0000-0000-0000-000000000000
  BackupLocation          :
  EpochOfLastBackupRecord :
  LsnOfLastBackupRecord   : 0
  FailureError            :
  ```
- **Êxito**, **falha**ou **tempo limite**: um backup sob demanda solicitado pode ser concluído em qualquer um dos seguintes Estados:
  - **Êxito**: um estado de backup _bem-sucedido_ indica que o backup do estado da partição foi realizado com êxito. A resposta fornece _BackupEpoch_ e _BackupLSN_ para a partição junto com o tempo em UTC.
    ```
    BackupState             : Success
    TimeStampUtc            : 2018-11-21T20:00:01Z
    BackupId                : 5d64b697-6acd-45a4-adbd-3d75e0078081
    BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-11-21 20.00.01.zip
    EpochOfLastBackupRecord : @{DataLossNumber=131873018908156893; ConfigurationNumber=8589934592}
    LsnOfLastBackupRecord   : 36
    FailureError            :
    ```
  - **Falha**: um estado de backup de _falha_ indica que ocorreu uma falha durante o backup do estado da partição. A causa da falha é indicada em resposta.
    ```
    BackupState             : Failure
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          :
    EpochOfLastBackupRecord :
    LsnOfLastBackupRecord   : 0
    FailureError            : @{Code=FABRIC_E_BACKUPCOPIER_UNEXPECTED_ERROR; Message=An error occurred during this operation.  Please check the trace logs for more details.}
    ```
  - **Tempo limite**: um estado de backup de _tempo limite_ indica que o backup do estado da partição não pôde ser criado em um determinado período de tempo. O valor de tempo limite padrão é 10 minutos. Inicie uma nova solicitação de backup sob demanda com um maior [tempo de inatividade](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout) nesse cenário.
    ```
    BackupState             : Timeout
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          :
    EpochOfLastBackupRecord :
    LsnOfLastBackupRecord   : 0
    FailureError            : @{Code=FABRIC_E_TIMEOUT; Message=The request of backup has timed out.}
    ```

## <a name="next-steps"></a>Passos seguintes

- [Entender a configuração de backup periódico](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Referência da API REST do BackupRestore](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/trigger-partition-backup.png
[1]: ./media/service-fabric-backuprestoreservice/trigger-backup-fileshare.png