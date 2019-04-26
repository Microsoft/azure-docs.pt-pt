---
title: Cópia de segurança a pedido no Azure Service Fabric | Documentos da Microsoft
description: Utilize a cópia de segurança e restaurar a funcionalidade do Service Fabric para fazer backup dos dados de aplicação de forma necessidade.
services: service-fabric
documentationcenter: .net
author: aagup
manager: chackdan
editor: aagup
ms.assetid: 02DA262A-EEF6-4F90-842E-FFC4A09003E5
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: c80a9ac30e79607d2a255debf73f6542df7c6498
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60310898"
---
# <a name="on-demand-backup-in-azure-service-fabric"></a>Cópia de segurança a pedido no Azure Service Fabric

Pode fazer backup de dados de serviços com estado fiável e Reliable Actors para cenários de perda de dados de desastres ou do endereço.

O Azure Service Fabric tem recursos para o [cópia de segurança periódica de dados](service-fabric-backuprestoreservice-quickstart-azurecluster.md) e a cópia de segurança de dados de forma necessidade. Cópia de segurança a pedido é útil porque ele protege contra _perda de dados_/_danos nos dados_ devido a alterações planeadas no serviço subjacente ou seu ambiente.

As funcionalidades de cópia de segurança a pedido são úteis para capturar o estado dos serviços antes de acionar manualmente um serviço ou operação de ambiente de serviço. Por exemplo, se fizer uma alteração em binários de serviço ao atualizar ou fazer downgrade o serviço. Nesse caso, cópia de segurança a pedido pode ajudar a proteger os dados contra corrupção por bugs de código do aplicativo de mensagens em fila.

## <a name="triggering-on-demand-backup"></a>Acionar cópia de segurança a pedido

Cópia de segurança a pedido requer detalhes de armazenamento para carregar ficheiros de cópia de segurança. Especifique a localização de cópia de segurança a pedido, na política de cópia de segurança periódica ou numa solicitação de cópia de segurança a pedido.

### <a name="on-demand-backup-to-storage-specified-by-a-periodic-backup-policy"></a>Cópia de segurança a pedido para o armazenamento especificado por uma política de cópia de segurança periódica

Pode configurar a política de cópia de segurança periódica a utilizar uma partição de um serviço com estado fiável ou dos Reliable Actors para cópia de segurança extra sob demanda para o armazenamento.

Caso seguinte é a continuação do cenário [cópia de segurança periódica do ativação de serviço com estado fiável e Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors). Neste caso, ativar uma política de cópia de segurança utilizar uma partição e uma cópia de segurança ocorre com uma frequência de conjunto no armazenamento do Azure.

Utilize o [BackupPartition](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition) API para configurar o acionamento para a cópia de segurança a pedido para o ID de partição `974bd92a-b395-4631-8a7f-53bd4ae9cf22`.

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Utilize o [GetBackupProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) API para ativar o controlo para o [progresso de cópia de segurança a pedido](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress).

### <a name="on-demand-backup-to-specified-storage"></a>Cópia de segurança a pedido para o armazenamento especificado

Pode pedir uma cópia de segurança a pedido para uma partição de um serviço com estado fiável ou Reliable Actor. Forneça as informações de armazenamento como parte do pedido de cópia de segurança a pedido.

Utilize o [BackupPartition](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition) API para configurar o acionamento para a cópia de segurança a pedido para o ID de partição `974bd92a-b395-4631-8a7f-53bd4ae9cf22`. Inclua as seguintes informações de armazenamento do Azure:

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

Pode utilizar o [GetBackupProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) API para configurar o controlo para o [progresso de cópia de segurança a pedido](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress).

## <a name="tracking-on-demand-backup-progress"></a>Acompanhando o progresso de cópia de segurança a pedido

Uma partição de um serviço com estado fiável ou Reliable Actor aceita somente uma solicitação de cópia de segurança a pedido, ao mesmo tempo. Outro pedido pode ser aceites apenas após a conclusão do pedido de cópia de segurança a pedido atual.

Partições diferentes podem acionar a pedidos de cópia de segurança a pedido, um mesmo tempo.

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetBackupProgress?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3' 
$backupResponse = (ConvertFrom-Json $response.Content) 
$backupResponse
```

Pedidos de cópia de segurança a pedido podem ser os seguintes Estados:

- **Aceite**: A cópia de segurança foi iniciado na partição e está em curso.
  ```
  BackupState             : Accepted
  TimeStampUtc            : 0001-01-01T00:00:00Z
  BackupId                : 00000000-0000-0000-0000-000000000000
  BackupLocation          :
  EpochOfLastBackupRecord :
  LsnOfLastBackupRecord   : 0
  FailureError            :
  ```
- **Sucesso**, **falha**, ou **tempo limite**: Uma cópia de segurança a pedido pedida pode ser concluída em qualquer um dos seguintes Estados:
  - **Success**: R _êxito_ cópia de segurança estado de funcionamento indica que o estado de partição tem uma cópia de segurança com êxito. A resposta fornece _BackupEpoch_ e _BackupLSN_ para a partição juntamente com a hora em UTC.
    ```
    BackupState             : Success
    TimeStampUtc            : 2018-11-21T20:00:01Z
    BackupId                : 5d64b697-6acd-45a4-adbd-3d75e0078081
    BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-11-21 20.00.01.zip
    EpochOfLastBackupRecord : @{DataLossNumber=131873018908156893; ConfigurationNumber=8589934592}
    LsnOfLastBackupRecord   : 36
    FailureError            :
    ```
  - **Falha de**: R _falha_ cópia de segurança estado de funcionamento indica que ocorreu uma falha durante a cópia de segurança do Estado da partição. A causa da falha é indicada na resposta.
    ```
    BackupState             : Failure
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          :
    EpochOfLastBackupRecord :
    LsnOfLastBackupRecord   : 0
    FailureError            : @{Code=FABRIC_E_BACKUPCOPIER_UNEXPECTED_ERROR; Message=An error occurred during this operation.  Please check the trace logs for more details.}
    ```
  - **Tempo limite**: R _tempo limite_ cópia de segurança estado de funcionamento indica que a cópia de segurança do Estado de partição não foi possível criar dentro de um determinado período de tempo. O valor de tempo limite predefinido é 10 minutos. Iniciar um novo pedido de cópia de segurança a pedido com maior [BackupTimeout](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout) neste cenário.
    ```
    BackupState             : Timeout
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          :
    EpochOfLastBackupRecord :
    LsnOfLastBackupRecord   : 0
    FailureError            : @{Code=FABRIC_E_TIMEOUT; Message=The request of backup has timed out.}
    ```

## <a name="next-steps"></a>Passos Seguintes

- [Compreender a configuração de cópia de segurança periódica](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Referência da API de REST de BackupRestore](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)
