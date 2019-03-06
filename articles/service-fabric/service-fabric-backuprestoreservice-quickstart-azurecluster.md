---
title: Cópia de segurança periódica e restauro no Azure Service Fabric | Documentos da Microsoft
description: Utilize a cópia de segurança periódica do Service Fabric e restaurar a funcionalidade para ativar a cópia de segurança de dados periódica de dados da sua aplicação.
services: service-fabric
documentationcenter: .net
author: hrushib
manager: timlt
editor: hrushib
ms.assetid: FAA58600-897E-4CEE-9D1C-93FACF98AD1C
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/29/2018
ms.author: hrushib
ms.openlocfilehash: 96911efb541fc861aa7dd5f735efca8309763428
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57443101"
---
# <a name="periodic-backup-and-restore-in-azure-service-fabric"></a>Cópia de segurança periódica e restauro no Azure Service Fabric 
> [!div class="op_single_selector"]
> * [Clusters no Azure](service-fabric-backuprestoreservice-quickstart-azurecluster.md) 
> * [Clusters autónomos](service-fabric-backuprestoreservice-quickstart-standalonecluster.md)
> 

Service Fabric é uma plataforma de sistemas distribuídos que facilita a desenvolver e gerir aplicações de cloud fiável e distribuída, microsserviços, com base. Ele permite que a execução de microsserviços com e sem monitoração de estado. Serviços com estado podem manter um Estado mutável e autoritativo para além do pedido e resposta ou uma transação completa. Se um serviço com estado fica inativo por um longo tempo ou perde informações devido a um desastre, ele poderá ter de ser restauradas para alguns recente cópia de segurança do seu estado para poder continuar a fornecer serviço depois que ele é reativado.

Service Fabric replica o estado em vários nós para garantir que o serviço está altamente disponível. Mesmo que um nó no cluster falhar, o serviço continua a estar disponível. No entanto, em certos casos, é ainda desejável para os dados de serviço ser confiável contra falhas mais amplas.
 
Por exemplo, o serviço pode querer fazer uma cópia dos respetivos dados para proteger contra os seguintes cenários:
- Em caso de perda permanente de um cluster do Service Fabric inteiro.
- Perda permanente de a maioria das réplicas de uma partição de serviço
- Erros administrativos no qual o estado acidentalmente obtém excluído ou danificado. Por exemplo, um administrador com privilégios suficientes erroneamente elimina o serviço.
- Bugs no serviço que danificar os dados. Por exemplo, isto pode acontecer quando uma atualização do código de serviço é iniciado a escrita de dados com falhas para uma coleção fiável. Nesse caso, o código e os dados podem ter a ser revertida para um estado anterior.
- Processamento de dados offline. Pode ser conveniente ter um processamento offline de dados para business intelligence que acontece em separado do serviço que gera os dados.

O Service Fabric fornece uma API incorporada para um ponto anterior no tempo [backup e restauração](service-fabric-reliable-services-backup-restore.md). Os desenvolvedores de aplicativos podem usar essas APIs para criar cópias de segurança do Estado do serviço periodicamente. Além disso, se quiser que os administradores de serviço acionar uma cópia de segurança a partir de fora o serviço a uma hora específica, como antes de atualizar a aplicação, os desenvolvedores precisam para expor a cópia de segurança (e restaurar) como uma API do serviço. Manter as cópias de segurança é um custo adicional acima isso. Por exemplo, poderá demorar cinco cópias de segurança incrementais cada meia hora, seguido de uma cópia de segurança completa. Após a cópia de segurança completa, pode eliminar as cópias de segurança incrementais anteriores. Essa abordagem exige código adicional que leva a custos adicionais durante o desenvolvimento de aplicativo.

O serviço de cópia de segurança e restauro no Service Fabric permite que a cópia de segurança fácil e automática de informações armazenadas nos serviços de monitorização de estado. Backup de dados de aplicação numa base periódica é fundamental para proteção contra indisponibilidade de serviço e perda de dados. O Service Fabric fornece uma cópia de segurança opcional e o serviço de restauro, que permite-lhe configurar a cópia de segurança periódica dos Reliable Services com monitorização de estado e (incluindo os serviços de Atores) sem ter de escrever código adicional. Ele também facilita a restaurar anteriormente efetuadas cópias de segurança. 


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
* Cluster do Service Fabric com recursos de infraestrutura versão 6.2 e versões posteriores. O cluster deve ser configurado no Windows Server. Consulte este [artigo](service-fabric-cluster-creation-via-arm.md) para obter os passos criar recursos de infraestrutura do serviço de cluster com o modelo de recursos do Azure.
* Certificado X.509 para a encriptação dos segredos necessárias para se ligar ao armazenamento para armazenar cópias de segurança. Consultar [artigo](service-fabric-cluster-creation-via-arm.md) saber como obter ou criar um certificado X.509.
* Aplicação de Service Fabric Reliable com monitoração de estado criada com o SDK do Service Fabric versão 3.0 ou superior. Para aplicativos destinados a .net Core 2.0, a aplicação deve ser criada usando o Service Fabric SDK versão 3.1 ou posterior.
* Crie conta de armazenamento do Azure para armazenar cópias de segurança do aplicativo.

## <a name="enabling-backup-and-restore-service"></a>Ativar o serviço de cópia de segurança e restauro
Primeiro tem de ativar a _cópia de segurança e restaurar o serviço_ no seu cluster. Obter o modelo para o cluster que pretende implementar. Pode utilizar o [modelos de exemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) ou criar um modelo do Resource Manager. Ativar a _cópia de segurança e restaurar o serviço_ com os seguintes passos:

1. Verifique se o `apiversion` está definido como **`2018-02-01`** para o `Microsoft.ServiceFabric/clusters` recurso e se não, atualize-a conforme mostrado no seguinte fragmento:

    ```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Agora ativar a _cópia de segurança e restaurar o serviço_ adicionando o seguinte procedimento `addonFeatures` secção em `properties` secção conforme mostrado no seguinte fragmento: 

    ```json
        "properties": {
            ...
            "addonFeatures":  ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```
3. Configure certificado X.509 para a encriptação de credenciais. Isso é importante certificar-se de que as credenciais fornecidas para ligar ao armazenamento são encriptadas antes de continuar. Configurar o certificado de encriptação ao adicionar o seguinte `BackupRestoreService` secção em `fabricSettings` secção conforme mostrado no seguinte fragmento: 

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

4. Depois de ter atualizado o seu modelo de cluster com as alterações anteriores, aplicá-las e permitir que concluir a implementação/atualização. Depois de concluído, o _cópia de segurança e restaurar o serviço_ começa a ser executado no seu cluster. O Uri deste serviço é `fabric:/System/BackupRestoreService` e o serviço pode ser localizado na secção de serviço de sistema no Explorador do Service Fabric. 

## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Ativar a cópia de segurança periódica para serviço com estado fiável e Reliable Actors
Vamos guiá-lo pelos passos para ativar cópia de segurança periódica para serviço com estado fiável e Reliable Actors. Estes passos partem do princípio
- Se o cluster está configurada com segurança X.509 com o _cópia de segurança e restaurar o serviço_.
- Um serviço com estado fiável é implementado no cluster. Para efeitos deste manual de início rápido, o Uri da aplicação é `fabric:/SampleApp` e o Uri de serviço com estado fiável que pertencem a esta aplicação é `fabric:/SampleApp/MyStatefulService`. Este serviço é implementado com a partição única e o ID de partição é `974bd92a-b395-4631-8a7f-53bd4ae9cf22`.
- O certificado de cliente com a função de administrador é instalado no _minha_ (_pessoais_) armazenar o nome do _CurrentUser_ localização de armazenamento na máquina local abaixo do certificado scripts serão invocados. Este exemplo utiliza `1b7ebe2174649c45474a4819dafae956712c31d3` como thumbprint deste certificado. Para obter mais informações sobre certificados de cliente, consulte [controlo de acesso baseado em funções para clientes do Service Fabric](service-fabric-cluster-security-roles.md).

### <a name="create-backup-policy"></a>Criar política de cópia de segurança

Primeira etapa é criar a política de cópia de segurança que descreve a agenda de cópia de segurança, armazenamento de dados de cópia de segurança, o nome da política, cópias de segurança incrementais máximas permitido antes de acionar a cópia de segurança completa e a política de retenção para o armazenamento de cópia de segurança de destino. 

Armazenamento de cópias de segurança, utilize o armazenamento do Azure conta criada acima. Contentor `backup-container` está configurado para armazenar cópias de segurança. Um contentor com este nome é criado, se ainda não exista, durante o carregamento de cópia de segurança. Preencher `ConnectionString` com uma cadeia de ligação válida para a conta de armazenamento do Azure, substituindo `account-name` com o nome da conta de armazenamento, e `account-key` pela chave da conta de armazenamento.

Execute a seguinte script do PowerShell para invocar a API de REST necessárias para criar nova política. Substitua `account-name` com o nome da conta de armazenamento, e `account-key` pela chave da conta de armazenamento.

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$ScheduleInfo = @{
    Interval = 'PT15M'
    ScheduleKind = 'FrequencyBased'
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
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/BackupRestore/BackupPolicies/$/Create?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

> [!IMPORTANT]
> Devido a um problema no tempo de execução, certifique-se de que a duração da retenção na política de retenção está configurada para ser inferior a 24 dias caso contrário, ele iria resultar no serviço de restauração de cópia de segurança de mensagens em fila para entrar em ativação pós-falha de réplica do quórum perda post.

### <a name="enable-periodic-backup"></a>Ativar cópia de segurança periódica
Depois de definir a política de cópia de segurança para atender a requisitos de proteção de dados do aplicativo, a política de cópia de segurança deve ser associada à aplicação. Dependendo do requisito, a política de cópia de segurança pode ser associada a uma aplicação, serviço ou uma partição.

Execute a seguinte script do PowerShell para invocar a API de REST necessárias para associar a política de cópia de segurança com o nome `BackupPolicy1` criada na acima passo com a aplicação `SampleApp`.

```powershell
$BackupPolicyReference = @{
    BackupPolicyName = 'BackupPolicy1'
}

$body = (ConvertTo-Json $BackupPolicyReference)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/EnableBackup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
``` 

### <a name="verify-that-periodic-backups-are-working"></a>Certifique-se de que as cópias de segurança periódicas estão a funcionar

Depois de ativar a cópia de segurança ao nível da aplicação, todas as partições que pertencem a Reliable Stateful services e Reliable Actors sob o aplicativo iniciará a obter uma cópia de segurança periodicamente acordo com a política de cópia de segurança associada. 

![Evento de estado de funcionamento do restaurados de partição][0]

### <a name="list-backups"></a>Lista as cópias de segurança

Cópias de segurança associadas a todas as partições que pertencem a Reliable Stateful services e Reliable Actors do aplicativo podem ser enumeradas usando _GetBackups_ API. As cópias de segurança podem ser enumeradas para uma aplicação, serviço ou uma partição.

Execute a seguinte script do PowerShell para invocar a API de HTTP para enumerar as cópias de segurança criadas para todas as partições dentro do `SampleApp` aplicação.

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

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

## <a name="limitation-caveats"></a>Limitação / advertências
- Não existem recursos de infraestrutura de serviço, criado nos cmdlets do PowerShell.
- Não há suporte para o Service Fabric clusters no Linux.

## <a name="known-issues"></a>Problemas Conhecidos
- Certifique-se de que o período de retenção está configurado para ser inferior a 24 dias. 

## <a name="next-steps"></a>Passos Seguintes
- [Configuração de cópia de segurança periódica compreensão](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Referência da REST API de restauro de cópia de segurança](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/PartitionBackedUpHealthEvent_Azure.png

