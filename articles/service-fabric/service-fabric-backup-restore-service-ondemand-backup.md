---
title: Backup a pedido no Azure Service Fabric
description: Utilize a funcionalidade de backup e restauro no Service Fabric para fazer backup dos dados da sua aplicação numa base de necessidade.
author: aagup
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: d7986c8cd8d0714215c7b4dc57170be346e627ed
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98928045"
---
# <a name="on-demand-backup-in-azure-service-fabric"></a>Backup a pedido no Azure Service Fabric

Pode fazer o back up dados de serviços estatais fiáveis e de atores fiáveis para abordar cenários de desastres ou perda de dados.

O Azure Service Fabric possui funcionalidades para a [cópia de segurança periódica dos dados](service-fabric-backuprestoreservice-quickstart-azurecluster.md) e a cópia de segurança dos dados numa base de necessidade. O backup a pedido é útil porque protege contra a corrupção de dados de _perda_ de / _dados_ devido a alterações planeadas no serviço subjacente ou no seu ambiente.

As funcionalidades de backup a pedido são úteis para capturar o estado dos serviços antes de desencadear manualmente uma operação de ambiente de serviço ou de serviço. Por exemplo, se fizer uma alteração nos binários de serviço ao atualizar ou degradar o serviço. Neste caso, o backup a pedido pode ajudar a proteger os dados contra a corrupção através de erros de código de aplicação.
## <a name="prerequisites"></a>Pré-requisitos

- Instale microsoft.serviceFabric.Powershell.Http Module (Preview) para fazer chamadas de configuração.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

> [!NOTE]
> Se a versão PowerShellGet for inferior a 1.6.0, terá de atualizar para adicionar suporte à bandeira *-AllowPrerelease:*
>
> `Install-Module -Name PowerShellGet -Force`

- Certifique-se de que o Cluster está ligado utilizando o `Connect-SFCluster` comando antes de escoar qualquer pedido de configuração utilizando o Módulo Microsoft.ServiceFabric.Powershell.Http.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```


## <a name="triggering-on-demand-backup"></a>Desencadeando backup a pedido

A cópia de segurança a pedido requer detalhes de armazenamento para o upload de ficheiros de backup. Especifica a localização de backup a pedido, quer na política de backup periódica, quer num pedido de backup a pedido.

### <a name="on-demand-backup-to-storage-specified-by-a-periodic-backup-policy"></a>Backup a pedido para armazenamento especificado por uma política de backup periódica

Pode configurar a política de backup periódica para utilizar uma divisão de um serviço Stateful fiável ou um Ator Fiável para uma cópia de segurança extra a pedido para armazenamento.

Segue-se a continuação do cenário na [habilitação de backup periódico para um serviço de estado fiável e atores fiáveis.](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors) Neste caso, você permite que uma política de backup utilize uma partição e uma cópia de segurança ocorre em uma frequência definida no Azure Storage.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Powershell usando Microsoft.ServiceFabric.Powershell.Http Módulo

```powershell

Backup-SFPartition -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' 

```

#### <a name="rest-call-using-powershell"></a>Chamada de descanso usando Powershell

Utilize a API [de BackupPartition](/rest/api/servicefabric/sfclient-api-backuppartition) para configurar o gatilho para o backup a pedido para iD de partição `974bd92a-b395-4631-8a7f-53bd4ae9cf22` .

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Utilize a API [GetBackupProgress](/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) para permitir o rastreio para o [progresso de backup a pedido](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress).

### <a name="on-demand-backup-to-specified-storage"></a>Backup a pedido para armazenamento especificado

Você pode solicitar backup a pedido para uma divisão de um serviço stateful fiável ou ator fiável. Forneça a informação de armazenamento como parte do pedido de cópia de segurança a pedido.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Powershell usando Microsoft.ServiceFabric.Powershell.Http Módulo

```powershell

Backup-SFPartition -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' -AzureBlobStore -ConnectionString  'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>Chamada de descanso usando Powershell

Utilize a API [de BackupPartition](/rest/api/servicefabric/sfclient-api-backuppartition) para configurar o gatilho para o backup a pedido para iD de partição `974bd92a-b395-4631-8a7f-53bd4ae9cf22` . Inclua as seguintes informações de Armazenamento Azure:

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

Pode utilizar a [API GetBackupProgress](/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) para configurar o rastreio para o progresso de backup a [pedido.](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress)

### <a name="using-service-fabric-explorer"></a>Usando o Explorador de Tecido de Serviço
Certifique-se de que o Modo Avançado foi ativado nas definições do Explorador de Tecidos de Serviço.
1. Selecione as divisórias desejadas e clique em Ações. 
2. Selecione A cópia de segurança da partição do gatilho e preencha as informações para Azure:

    ![Backup de partição do gatilho][0]

    ou FileShare:

    ![Trigger Partition Backup FileShare][1]

## <a name="tracking-on-demand-backup-progress"></a>Acompanhar o progresso do backup a pedido

Uma divisão de um serviço de Stateful fiável ou um ator fiável aceita apenas um pedido de backup a pedido de cada vez. Outro pedido só pode ser aceite depois de concluído o atual pedido de backup a pedido.

Diferentes divisões podem desencadear pedidos de backup a pedido ao mesmo tempo.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Powershell usando Microsoft.ServiceFabric.Powershell.Http Módulo

```powershell

Get-SFPartitionBackupProgress -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22'

```
#### <a name="rest-call-using-powershell"></a>Chamada de descanso usando Powershell

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetBackupProgress?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3' 
$backupResponse = (ConvertFrom-Json $response.Content) 
$backupResponse
```

Os pedidos de backup a pedido podem estar nos seguintes Estados:

- **Aceite:** A cópia de segurança começou na partição e está em curso.
  ```
  BackupState             : Accepted
  TimeStampUtc            : 0001-01-01T00:00:00Z
  BackupId                : 00000000-0000-0000-0000-000000000000
  BackupLocation          :
  EpochOfLastBackupRecord :
  LsnOfLastBackupRecord   : 0
  FailureError            :
  ```
- **Sucesso**, **Fracasso**, ou **Timeout**: Um backup solicitado a pedido pode ser concluído em qualquer um dos seguintes estados:
  - **Sucesso**: Um estado de backup de _sucesso_ indica que o estado de partição tem apoiado com sucesso. A resposta fornece _BackupEpoch_ e _BackupLSN_ para a partição juntamente com o tempo na UTC.
    ```
    BackupState             : Success
    TimeStampUtc            : 2018-11-21T20:00:01Z
    BackupId                : 5d64b697-6acd-45a4-adbd-3d75e0078081
    BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-11-21 20.00.01.zip
    EpochOfLastBackupRecord : @{DataLossNumber=131873018908156893; ConfigurationNumber=8589934592}
    LsnOfLastBackupRecord   : 36
    FailureError            :
    ```
  - **Falha**: Um estado de backup _de falha_ indica que ocorreu uma falha durante a cópia de segurança do estado da partição. A causa da falha é declarada em resposta.
    ```
    BackupState             : Failure
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          :
    EpochOfLastBackupRecord :
    LsnOfLastBackupRecord   : 0
    FailureError            : @{Code=FABRIC_E_BACKUPCOPIER_UNEXPECTED_ERROR; Message=An error occurred during this operation.  Please check the trace logs for more details.}
    ```
  - **Timeout**: Um estado de backup _timeout_ indica que o backup do estado da partição não poderia ser criado num dado período de tempo. O valor de tempo limite padrão é de 10 minutos. Inicie um novo pedido de backup a pedido com maior [BackupTimeout](/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout) neste cenário.
    ```
    BackupState             : Timeout
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          :
    EpochOfLastBackupRecord :
    LsnOfLastBackupRecord   : 0
    FailureError            : @{Code=FABRIC_E_TIMEOUT; Message=The request of backup has timed out.}
    ```

## <a name="next-steps"></a>Próximos passos

- [Compreender a configuração de backup periódica](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Referência API backupRestore REST](/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/trigger-partition-backup.png
[1]: ./media/service-fabric-backuprestoreservice/trigger-backup-fileshare.png
