---
title: Cópia de segurança periódica e restauro no Azure Service Fabric | Documentos da Microsoft
description: Utilize a cópia de segurança periódica do Service Fabric e restaurar a funcionalidade para ativar a cópia de segurança de dados periódica de dados da sua aplicação.
services: service-fabric
documentationcenter: .net
author: hrushib
manager: timlt
editor: hrushib
ms.assetid: FAADBCAB-F0CF-4CBC-B663-4A6DCCB4DEE1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/29/2018
ms.author: hrushib
ms.openlocfilehash: 2ff7221a3742f59cdef2c5c7c220cc80148b94d0
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2018
ms.locfileid: "52721566"
---
# <a name="periodic-backup-and-restore-in-azure-service-fabric"></a>Cópia de segurança periódica e restauro no Azure Service Fabric
> [!div class="op_single_selector"]
> * [Clusters no Azure](service-fabric-backuprestoreservice-quickstart-azurecluster.md) 
> * [Clusters autónomos](service-fabric-backuprestoreservice-quickstart-standalonecluster.md)
> 

Service Fabric é uma plataforma de sistemas distribuídos que facilita a desenvolver e gerir aplicações de cloud fiável e distribuída, microsserviços, com base. Ele permite que a execução de microsserviços com e sem monitoração de estado. Serviços com estado podem manter um Estado mutável e autoritativo para além do pedido e resposta ou uma transação completa. Se um serviço com estado fica inativo por um longo tempo ou perde informações devido a um desastre, ele poderá ter de ser restauradas para alguns recente cópia de segurança do seu estado para poder continuar a fornecer serviço depois que ele é reativado.

Service Fabric replica o estado em vários nós para garantir que o serviço está altamente disponível. Mesmo que um nó no cluster falhar, o serviço continua a estar disponível. No entanto, em certos casos, é ainda desejável para os dados de serviço ser confiável contra falhas mais amplas.
 
Por exemplo, um serviço pode querer fazer uma cópia dos respetivos dados para proteger contra os seguintes cenários:
- Perda permanente de um cluster do Service Fabric inteiro.
- Perda permanente de a maioria das réplicas de uma partição de serviço
- Erros administrativos no qual o estado acidentalmente obtém excluído ou danificado. Por exemplo, um administrador com privilégios suficientes erroneamente elimina o serviço.
- Bugs no serviço que danificar os dados. Por exemplo, isto pode acontecer quando uma atualização do código de serviço é iniciado a escrita de dados com falhas para uma coleção fiável. Nesse caso, o código e os dados podem ter a ser revertida para um estado anterior.
- Processamento de dados offline. Pode ser conveniente ter um processamento offline de dados para business intelligence que acontece em separado do serviço que gera os dados.

O Service Fabric fornece uma API incorporada para um ponto anterior no tempo [backup e restauração](service-fabric-reliable-services-backup-restore.md). Os desenvolvedores de aplicativos podem usar essas APIs para criar cópias de segurança do Estado do serviço periodicamente. Além disso, se quiser que os administradores de serviço acionar uma cópia de segurança a partir de fora o serviço a uma hora específica, como antes de atualizar a aplicação, os desenvolvedores precisam para expor a cópia de segurança (e restaurar) como uma API do serviço. Manter as cópias de segurança é um custo adicional acima isso. Por exemplo, poderá demorar cinco cópias de segurança incrementais cada meia hora, seguido de uma cópia de segurança completa. Após a cópia de segurança completa, pode eliminar as cópias de segurança incrementais anteriores. Essa abordagem exige código adicional que leva a custos adicionais durante o desenvolvimento de aplicativo.

Cópia de segurança dos dados de aplicação numa base periódica é uma necessidade básica para gerir uma aplicação distribuída e protege contra a perda de dados ou prolongada perda de disponibilidade do serviço. O Service Fabric fornece uma cópia de segurança opcional e o serviço de restauro, que permite-lhe configurar a cópia de segurança periódica dos Reliable Services com monitorização de estado e (incluindo os serviços de Atores) sem ter de escrever código adicional. Ele também facilita a restaurar anteriormente efetuadas cópias de segurança. 

O Service Fabric fornece um conjunto de APIs para alcançar a seguinte funcionalidade relacionados periódica para cópia de segurança e restaurar a funcionalidade:

- Agendar periódica cópia de segurança de Reliable Stateful services e Reliable Actors com suporte para carregar as localizações de armazenamento de cópia de segurança para (externo). Localizações de armazenamento suportadas
    - Storage do Azure
    - Partilha de ficheiros (local)
- Enumerar as cópias de segurança
- Acione uma cópia de segurança ad hoc de uma partição
- Restaurar uma partição com a cópia de segurança anterior
- Suspender temporariamente as cópias de segurança
- Gestão de retenção de cópias de segurança (breve)

## <a name="prerequisites"></a>Pré-requisitos
* Cluster do Service Fabric com recursos de infraestrutura versão 6.2 e versões posteriores. O cluster deve ser configurado no Windows Server. Consulte este [artigo](service-fabric-cluster-creation-for-windows-server.md) para obter os passos transferir o pacote necessário.
* Certificado X.509 para a encriptação dos segredos necessárias para se ligar ao armazenamento para armazenar cópias de segurança. Consultar [artigo](service-fabric-windows-cluster-x509-security.md) saber como adquirir ou para criar um certificado X.509 autoassinado.
* Aplicação de Service Fabric Reliable com monitoração de estado criada com o SDK do Service Fabric versão 3.0 ou superior. Para aplicativos destinados a .net Core 2.0, a aplicação deve ser criada usando o Service Fabric SDK versão 3.1 ou posterior.

## <a name="enabling-backup-and-restore-service"></a>Ativar o serviço de cópia de segurança e restauro
Primeiro tem de ativar a _cópia de segurança e restaurar o serviço_ no seu cluster. Obter o modelo para o cluster que pretende implementar. Pode utilizar o [modelos de exemplo](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples). Ativar a _cópia de segurança e restaurar o serviço_ com os seguintes passos:

1. Verifique se o `apiversion` está definido como `10-2017` na configuração do cluster de ficheiros e, se não, atualize-a conforme mostrado no seguinte fragmento:

    ```json
    {
        "apiVersion": "10-2017",
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        ...
    }
    ```

2. Agora ativar a _cópia de segurança e restaurar o serviço_ adicionando o seguinte procedimento `addonFeatures` secção em `properties` secção conforme mostrado no seguinte fragmento: 

    ```json
        "properties": {
            ...
            "addonFeatures": ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```

3. Configure certificado X.509 para a encriptação de credenciais. Isso é importante para garantir que as credenciais fornecidas, se existir, para ligar ao armazenamento são encriptados antes de continuar. Configurar o certificado de encriptação ao adicionar o seguinte `BackupRestoreService` secção em `fabricSettings` secção conforme mostrado no seguinte fragmento: 

    ```json
    "properties": {
        ...
        "addonFeatures": ["BackupRestoreService"],
        "fabricSettings": [{
            "name": "BackupRestoreService",
            "parameters":  [{
                "name": "SecretEncryptionCertThumbprint",
                "value": "[Thumbprint]"
            }]
        }
        ...
    }
    ```

4. Assim que tiver atualizado o seu ficheiro de configuração de cluster com as alterações anteriores, aplicá-las e permitir que concluir a implementação/atualização. Depois de concluído, o _cópia de segurança e restaurar o serviço_ começa a ser executado no seu cluster. O Uri deste serviço é `fabric:/System/BackupRestoreService` e o serviço pode ser localizado na secção de serviço de sistema no Explorador do Service Fabric. 

## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Ativar a cópia de segurança periódica para serviço com estado fiável e Reliable Actors
Vamos guiá-lo pelos passos para ativar cópia de segurança periódica para serviço com estado fiável e Reliable Actors. Estes passos partem do princípio
- Que o cluster está configurado com _cópia de segurança e restaurar o serviço_.
- Um serviço com estado fiável é implementado no cluster. Para efeitos deste manual de início rápido, o Uri da aplicação é `fabric:/SampleApp` e o Uri de serviço com estado fiável que pertencem a esta aplicação é `fabric:/SampleApp/MyStatefulService`. Este serviço é implementado com a partição única e o ID de partição é `23aebc1e-e9ea-4e16-9d5c-e91a614fefa7`.  

### <a name="create-backup-policy"></a>Criar política de cópia de segurança

Primeira etapa é criar a política de cópia de segurança que descreve a agenda de cópia de segurança, armazenamento de dados de cópia de segurança, o nome da política, cópias de segurança incrementais máximas permitido antes de acionar a cópia de segurança completa e a política de retenção para o armazenamento de cópia de segurança de destino. 

Armazenamento de cópias de segurança, criar partilha de ficheiros e conceda acesso de ReadWrite para esta partilha de ficheiros para todas as máquinas de nó do Service Fabric. Este exemplo assume a partilha com o nome `BackupStore` está presente no `StorageServer`.

Execute a seguinte script do PowerShell para invocar a API de REST necessárias para criar nova política.

```powershell
$ScheduleInfo = @{
    Interval = 'PT15M'
    ScheduleKind = 'FrequencyBased'
}   

$StorageInfo = @{
    Path = '\\StorageServer\BackupStore'
    StorageKind = 'FileShare'
}

$RetentionPolicy = @{ 
    RetentionPolicyType = 'Basic'
    RetentionDuration =  'P10D'
}

$BackupPolicy = @{
    Name = 'BackupPolicy1'
    MaxIncrementalBackups = 20
    Schedule = $ScheduleInfo
    Storage = $StorageInfo
    RetentionPolicy = $RetentionPolicy
}

$body = (ConvertTo-Json $BackupPolicy)
$url = "http://localhost:19080/BackupRestore/BackupPolicies/$/Create?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json'
```

### <a name="enable-periodic-backup"></a>Ativar cópia de segurança periódica
Depois de definir a política para atender a requisitos de proteção de dados do aplicativo, a política de cópia de segurança deve ser associada à aplicação. Dependendo do requisito, a política de cópia de segurança pode ser associada a uma aplicação, serviço ou uma partição.

Execute a seguinte script do PowerShell para invocar a API de REST necessárias para associar a política de cópia de segurança com o nome `BackupPolicy1` criada na acima passo com a aplicação `SampleApp`.

```powershell
$BackupPolicyReference = @{
    BackupPolicyName = 'BackupPolicy1'
}

$body = (ConvertTo-Json $BackupPolicyReference)
$url = "http://localhost:19080/Applications/SampleApp/$/EnableBackup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json'
``` 

### <a name="verify-that-periodic-backups-are-working"></a>Certifique-se de que as cópias de segurança periódicas estão a funcionar

Depois de ativar a cópia de segurança para o aplicativo, todas as partições que pertencem a Reliable Stateful services e Reliable Actors sob o aplicativo iniciará a obter uma cópia de segurança periodicamente acordo com a política de cópia de segurança associada. 

![Evento de estado de funcionamento do restaurados de partição][0]

### <a name="list-backups"></a>Lista as cópias de segurança

Cópias de segurança associadas a todas as partições que pertencem a Reliable Stateful services e Reliable Actors do aplicativo podem ser enumeradas usando _GetBackups_ API. Dependendo do requisito, as cópias de segurança podem ser enumeradas para aplicações, serviço ou uma partição.

Execute a seguinte script do PowerShell para invocar a API de HTTP para enumerar as cópias de segurança criadas para todas as partições dentro do `SampleApp` aplicação.

```powershell
$url = "http://localhost:19080/Applications/SampleApp/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get

$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```
Execute a saída de exemplo acima:

```
BackupId                : d7e4038e-2c46-47c6-9549-10698766e714
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 19.39.40.zip
BackupType              : Full
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2058
CreationTimeUtc         : 2018-04-01T19:39:40Z
FailureError            : 

BackupId                : 8c21398a-2141-4133-b4d7-e1a35f0d7aac
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 19.54.38.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2237
CreationTimeUtc         : 2018-04-01T19:54:38Z
FailureError            : 

BackupId                : fc75bd4c-798c-4c9a-beee-e725321f73b2
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 20.09.44.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2437
CreationTimeUtc         : 2018-04-01T20:09:44Z
FailureError            : 
```

## <a name="limitation-caveats"></a>Limitação / advertências
- Não existem recursos de infraestrutura de serviço, criado nos cmdlets do PowerShell.
- Não há suporte para a CLI do Service Fabric.
- Não há suporte para o Service Fabric clusters no Linux.

## <a name="next-steps"></a>Passos Seguintes
- [Configuração de cópia de segurança periódica compreensão](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Referência da REST API de restauro de cópia de segurança](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/PartitionBackedUpHealthEvent.png

