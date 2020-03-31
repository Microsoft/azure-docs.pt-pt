---
title: Backup a pedido no Tecido de Serviço Azure
description: Utilize a função de backup e restauro no Tecido de Serviço para fazer cópia sinuosamente os dados da sua aplicação.
author: aagup
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: d5eada62bec49fe771373671e9438d2786d6b165
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458415"
---
# <a name="on-demand-backup-in-azure-service-fabric"></a>Backup a pedido no Tecido de Serviço Azure

Pode fazer o backup de dados de serviços fiáveis e atores fiáveis para abordar cenários de desastres ou perdas de dados.

O Tecido de Serviço Azure tem funcionalidades para a [cópia de segurança periódica dos dados](service-fabric-backuprestoreservice-quickstart-azurecluster.md) e a cópia de segurança dos dados numa base de necessidade. A cópia de segurança a pedido é útil porque protege contra a corrupção de dados de _perda_/de_dados_ devido às mudanças planeadas no serviço subjacente ou no seu ambiente.

As funcionalidades de backup a pedido são úteis para capturar o estado dos serviços antes de desencadear manualmente uma operação de ambiente de serviço ou serviço. Por exemplo, se fizer uma alteração nos binários de serviço ao atualizar ou desvalorizar o serviço. Neste caso, o backup a pedido pode ajudar a proteger os dados contra a corrupção através de bugs de código de aplicação.
## <a name="prerequisites"></a>Pré-requisitos

- Instale microsoft.serviceFabric.Powershell.Http Módulo [Em Pré-visualização] para fazer chamadas de configuração.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

- Certifique-se de que `Connect-SFCluster` o Cluster está ligado utilizando o comando antes de efazer qualquer pedido de configuração utilizando o Microsoft.ServiceFabric.Powershell.Http Module.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```


## <a name="triggering-on-demand-backup"></a>Desencadeando reforços a pedido

A cópia de segurança a pedido requer detalhes de armazenamento para o upload de ficheiros de backup. Especifica a localização de backup a pedido, quer na política de backup periódica, quer num pedido de backup a pedido.

### <a name="on-demand-backup-to-storage-specified-by-a-periodic-backup-policy"></a>Backup a pedido para armazenamento especificado por uma política de backup periódica

Pode configurar a política periódica de backup para utilizar uma partição de um serviço fiável estatal ou um ator fiável para uma cópia extra a pedido de reserva para armazenamento.

O caso seguinte é a continuação do cenário na habilitação de cópia de segurança periódica para o serviço fiável e fiável e os [atores fiáveis.](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors) Neste caso, permite que uma política de backup utilize uma partição e ocorre uma cópia de segurança numa frequência definida no Armazenamento Azure.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Powershell usando Microsoft.ServiceFabric.Powershell.Http Módulo

```powershell

Backup-SFPartition -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' 

```

#### <a name="rest-call-using-powershell"></a>Chamada de descanso usando Powershell

Utilize a API da [Divisão de Backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition) para configurar `974bd92a-b395-4631-8a7f-53bd4ae9cf22`o gatilho para a cópia de segurança a pedido para id de partição .

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Utilize a API [GetBackupProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) para permitir o rastreio do progresso de backup a [pedido](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress).

### <a name="on-demand-backup-to-specified-storage"></a>Backup a pedido para armazenamento especificado

Você pode solicitar backup on-demand para uma partição de um serviço fiável estatal ou ator confiável. Forneça as informações de armazenamento como parte do pedido de backup a pedido.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Powershell usando Microsoft.ServiceFabric.Powershell.Http Módulo

```powershell

Backup-SFPartition -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' -AzureBlobStore -ConnectionString  'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>Chamada de descanso usando Powershell

Utilize a API da [Divisão de Backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition) para configurar `974bd92a-b395-4631-8a7f-53bd4ae9cf22`o gatilho para a cópia de segurança a pedido para id de partição . Inclua as seguintes informações de Armazenamento Azure:

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

Pode utilizar a API [GetBackupProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) para configurar o rastreio para o progresso de backup a [pedido](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress).

### <a name="using-service-fabric-explorer"></a>Usando o Explorador de Tecido de Serviço
Certifique-se de que o Modo Avançado está ativado nas definições do Service Fabric Explorer.
1. Selecione as divisórias desejadas e clique em Ações. 
2. Selecione backup de partição do gatilho e preencha informações para o Azure:

    ![Cópia de apoio][0]

    ou FileShare:

    ![Trigger Partition Backup FileShare][1]

## <a name="tracking-on-demand-backup-progress"></a>Rastreio do progresso de backup a pedido

Uma partição de um serviço fiável ou ator fiável aceita apenas um pedido de backup a pedido de cada vez. Outro pedido só pode ser aceite depois de concluído o atual pedido de backup a pedido.

Divisórias diferentes podem desencadear pedidos de backup a pedido ao mesmo tempo.


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

Os pedidos de backup a pedido podem estar nos seguintes estados:

- **Aceite**: O backup começou na partição e está em curso.
  ```
  BackupState             : Accepted
  TimeStampUtc            : 0001-01-01T00:00:00Z
  BackupId                : 00000000-0000-0000-0000-000000000000
  BackupLocation          :
  EpochOfLastBackupRecord :
  LsnOfLastBackupRecord   : 0
  FailureError            :
  ```
- **Sucesso**, **Falha**, ou **Timeout**: Um backup solicitado a pedido pode ser concluído em qualquer um dos seguintes estados:
  - **Sucesso**: Um estado de apoio ao _sucesso_ indica que o estado de partição apoiou com sucesso. A resposta fornece _BackupEpoch_ e _BackupLSN_ para a partição juntamente com o tempo na UTC.
    ```
    BackupState             : Success
    TimeStampUtc            : 2018-11-21T20:00:01Z
    BackupId                : 5d64b697-6acd-45a4-adbd-3d75e0078081
    BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-11-21 20.00.01.zip
    EpochOfLastBackupRecord : @{DataLossNumber=131873018908156893; ConfigurationNumber=8589934592}
    LsnOfLastBackupRecord   : 36
    FailureError            :
    ```
  - **Falha**: Um estado de backup de _falha_ indica que ocorreu uma falha durante a cópia de segurança do estado da partição. A causa da falha é declarada em resposta.
    ```
    BackupState             : Failure
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          :
    EpochOfLastBackupRecord :
    LsnOfLastBackupRecord   : 0
    FailureError            : @{Code=FABRIC_E_BACKUPCOPIER_UNEXPECTED_ERROR; Message=An error occurred during this operation.  Please check the trace logs for more details.}
    ```
  - **Timeout**: Um estado de backup _timeout_ indica que o backup do estado de partição não poderia ser criado num determinado período de tempo. O valor de tempo de paragem padrão é de 10 minutos. Inicie um novo pedido de backup a pedido com um maior [BackupTimeout](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout) neste cenário.
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

- [Compreender a configuração periódica de backup](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Referência de BackupRestaurar REST API](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/trigger-partition-backup.png
[1]: ./media/service-fabric-backuprestoreservice/trigger-backup-fileshare.png