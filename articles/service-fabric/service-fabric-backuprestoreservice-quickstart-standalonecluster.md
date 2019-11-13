---
title: Backup/restauração periódicos no Azure Service Fabric autônomo
description: Use o recurso de backup e restauração periódicos do Service Fabric para habilitar o backup de dados periódicos dos dados do seu aplicativo.
services: service-fabric
documentationcenter: .net
author: hrushib
manager: chackdan
editor: hrushib
ms.assetid: FAADBCAB-F0CF-4CBC-B663-4A6DCCB4DEE1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 5/24/2019
ms.author: hrushib
ms.openlocfilehash: 559f6cce7044d57c0a17fc3fe17f1c61f710913a
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013338"
---
# <a name="periodic-backup-and-restore-in-azure-service-fabric"></a>Backup e restauração periódicos no Azure Service Fabric
> [!div class="op_single_selector"]
> * [Clusters no Azure](service-fabric-backuprestoreservice-quickstart-azurecluster.md) 
> * [Clusters autônomos](service-fabric-backuprestoreservice-quickstart-standalonecluster.md)
> 

O Service Fabric é uma plataforma de sistemas distribuídos que facilita o desenvolvimento e o gerenciamento de aplicativos de nuvem baseados em microserviços confiáveis e distribuídos. Ele permite a execução de micro Services com e sem estado. Os serviços com estado podem manter o estado autoritativo e imutável além da solicitação e da resposta ou de uma transação completa. Se um serviço com estado ficar inativo por um longo tempo ou perder informações devido a um desastre, talvez seja necessário restaurá-lo para um backup recente de seu estado a fim de continuar a fornecer o serviço após o retorno do backup.

Service Fabric Replica o estado em vários nós para garantir que o serviço esteja altamente disponível. Mesmo se um nó do cluster falhar, o serviço continuará disponível. Em alguns casos, no entanto, ainda é desejável que os dados do serviço sejam confiáveis contra falhas mais amplas.
 
Por exemplo, um serviço pode querer fazer backup de seus dados para se proteger dos seguintes cenários:
- Perda permanente de um cluster de Service Fabric inteiro.
- Perda permanente de uma maioria das réplicas de uma partição de serviço
- Erros administrativos nos quais o estado é acidentalmente excluído ou corrompido. Por exemplo, um administrador com privilégios suficientes exclui erroneamente o serviço.
- Bugs no serviço que causam corrupção de dados. Por exemplo, isso pode acontecer quando uma atualização de código de serviço começa a gravar dados com falha em uma coleção confiável. Nesse caso, o código e os dados podem precisar ser revertidos para um estado anterior.
- Processamento de dados offline. Pode ser conveniente ter o processamento offline de dados para business intelligence que aconteça separadamente do serviço que gera os dados.

O Service Fabric fornece uma API embutida para fazer [backup e restauração](service-fabric-reliable-services-backup-restore.md)point-in-time. Os desenvolvedores de aplicativos podem usar essas APIs para fazer backup do estado do serviço periodicamente. Além disso, se os administradores de serviço desejarem disparar um backup de fora do serviço em um momento específico, como antes de atualizar o aplicativo, os desenvolvedores precisam expor backup (e restauração) como uma API do serviço. Manter os backups é um custo adicional acima disso. Por exemplo, talvez você queira fazer cinco backups incrementais a cada meia hora, seguido por um backup completo. Após o backup completo, você pode excluir os backups incrementais anteriores. Essa abordagem requer código adicional que leva a custo adicional durante o desenvolvimento de aplicativos.

O backup dos dados do aplicativo periodicamente é uma necessidade básica de gerenciar um aplicativo distribuído e de se proteger contra perda de dados ou perda prolongada de disponibilidade do serviço. O Service Fabric fornece um serviço opcional de backup e restauração, que permite configurar o backup periódico de Reliable Services com estado (incluindo serviços de ator) sem precisar escrever nenhum código adicional. Ele também facilita a restauração de backups feitos anteriormente. 

Service Fabric fornece um conjunto de APIs para obter a seguinte funcionalidade relacionada ao recurso de backup e restauração periódicos:

- Agendar backup periódico de serviços confiáveis com estado e Reliable Actors com suporte para upload de backup para locais de armazenamento (externos). Locais de armazenamento com suporte
    - Storage do Azure
    - Compartilhamento de arquivos (local)
- Enumerar backups
- Disparar um backup ad hoc de uma partição
- Restaurar uma partição usando o backup anterior
- Suspender backups temporariamente
- Gerenciamento de retenção de backups (futuro)

## <a name="prerequisites"></a>Pré-requisitos
* Service Fabric cluster com a versão 6,4 ou superior do fabric. Consulte este [artigo](service-fabric-cluster-creation-for-windows-server.md) para obter as etapas para baixar o pacote necessário.
* Certificado X. 509 para criptografia de segredos necessários para se conectar ao armazenamento para armazenar backups. Consulte o [artigo](service-fabric-windows-cluster-x509-security.md) para saber como adquirir ou criar um certificado X. 509 autoassinado.

* Service Fabric aplicativo com estado confiável criado usando o SDK do Service Fabric versão 3,0 ou superior. Para aplicativos voltados para o .Net Core 2,0, o aplicativo deve ser criado usando a versão 3,1 ou superior do SDK do Service Fabric.
* Instale o módulo Microsoft. perfabric. PowerShell. http [em versão prévia] para fazer chamadas de configuração.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

* Verifique se o cluster está conectado usando o comando `Connect-SFCluster` antes de fazer qualquer solicitação de configuração usando o módulo Microsoft. onfabric. PowerShell. http.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```

## <a name="enabling-backup-and-restore-service"></a>Habilitando o serviço de backup e restauração
Primeiro, você precisa habilitar o _serviço de backup e restauração_ no cluster. Obtenha o modelo para o cluster que você deseja implantar. Você pode usar os [modelos de exemplo](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples). Habilite o _serviço de backup e restauração_ com as seguintes etapas:

1. Verifique se a `apiversion` está definida como `10-2017` no arquivo de configuração de cluster e, se não estiver, atualize-a conforme mostrado no trecho a seguir:

    ```json
    {
        "apiVersion": "10-2017",
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        ...
    }
    ```

2. Agora, habilite o _serviço de backup e restauração_ adicionando a seção `addonFeatures` a seguir na seção `properties`, conforme mostrado no trecho a seguir: 

    ```json
        "properties": {
            ...
            "addonFeatures": ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```

3. Configure o certificado X. 509 para criptografia de credenciais. Isso é importante para garantir que as credenciais fornecidas, se houver, se conectem ao armazenamento, sejam criptografadas antes de persistirem. Configure o certificado de criptografia adicionando a seção `BackupRestoreService` a seguir na seção `fabricSettings`, conforme mostrado no trecho a seguir: 

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

4. Depois de atualizar o arquivo de configuração de cluster com as alterações anteriores, aplique-as e permita que a implantação/atualização seja concluída. Depois de concluído, o _serviço de backup e restauração_ inicia a execução em seu cluster. O URI desse serviço é `fabric:/System/BackupRestoreService` e o serviço pode estar localizado na seção serviço do sistema no Gerenciador de Service Fabric. 



## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Habilitando backup periódico para serviço confiável com estado e Reliable Actors
Vamos percorrer as etapas para habilitar o backup periódico para serviço confiável com estado e Reliable Actors. Estas etapas pressupõem que
- Se o cluster está configurado com o _serviço de backup e restauração_.
- Um serviço confiável com estado é implantado no cluster. Para fins deste guia de início rápido, o URI do aplicativo é `fabric:/SampleApp` e o URI para o serviço confiável com estado pertencente a esse aplicativo é `fabric:/SampleApp/MyStatefulService`. Esse serviço é implantado com uma única partição e a ID da partição é `23aebc1e-e9ea-4e16-9d5c-e91a614fefa7`.  

### <a name="create-backup-policy"></a>Criar política de backup

A primeira etapa é criar a política de backup que descreve o agendamento de backup, armazenamento de destino para dados de backup, nome da política, backups incrementais máximos a serem permitidos antes de disparar a política de backup e retenção completa para o armazenamento de backup. 

Para o armazenamento de backup, crie um compartilhamento de arquivos e forneça acesso ReadWrite a esse compartilhamento de arquivos para todas as máquinas de nó de Service Fabric. Este exemplo pressupõe que o compartilhamento com o nome `BackupStore` está presente no `StorageServer`.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell usando o módulo Microsoft. infabric. PowerShell. http

```powershell

New-SFBackupPolicy -Name 'BackupPolicy1' -AutoRestoreOnDataLoss $true -MaxIncrementalBackups 20 -FrequencyBased -Interval 00:15:00 -FileShare -Path '\\StorageServer\BackupStore' -Basic -RetentionDuration '10.00:00:00'

```
#### <a name="rest-call-using-powershell"></a>Chamada REST usando o PowerShell

Execute o seguinte script do PowerShell para invocar a API REST necessária para criar uma nova política.

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

### <a name="enable-periodic-backup"></a>Habilitar backup periódico
Depois de definir a política para atender aos requisitos de proteção de dados do aplicativo, a política de backup deve ser associada ao aplicativo. Dependendo do requisito, a política de backup pode ser associada a um aplicativo, serviço ou uma partição.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell usando o módulo Microsoft. infabric. PowerShell. http

```powershell
Enable-SFApplicationBackup -ApplicationId 'SampleApp' -BackupPolicyName 'BackupPolicy1'
```

#### <a name="rest-call-using-powershell"></a>Chamada REST usando o PowerShell
Execute o seguinte script do PowerShell para invocar a API REST necessária para associar a política de backup com o nome `BackupPolicy1` criado na etapa anterior com `SampleApp`de aplicativos.

```powershell
$BackupPolicyReference = @{
    BackupPolicyName = 'BackupPolicy1'
}

$body = (ConvertTo-Json $BackupPolicyReference)
$url = "http://localhost:19080/Applications/SampleApp/$/EnableBackup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json'
``` 

#### <a name="using-service-fabric-explorer"></a>Usando Service Fabric Explorer

1. Selecione um aplicativo e vá para a ação. Clique em Habilitar/atualizar backup do aplicativo.

    ![Habilitar o backup do aplicativo][3] 

2. Por fim, selecione a política desejada e clique em Habilitar backup.

    ![Selecionar política][4]

### <a name="verify-that-periodic-backups-are-working"></a>Verificar se os backups periódicos estão funcionando

Depois de habilitar o backup para o aplicativo, todas as partições que pertencem a serviços confiáveis com estado e Reliable Actors no aplicativo começarão a ser submetidas a backup periodicamente de acordo com a política de backup associada.

![Evento de integridade de backup de partição][0]

### <a name="list-backups"></a>Listar backups

Os backups associados a todas as partições que pertencem a serviços confiáveis com estado e Reliable Actors do aplicativo podem ser enumerados usando a API _Getbackups_ . Dependendo do requisito, os backups podem ser enumerados para o aplicativo, serviço ou uma partição.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell usando o módulo Microsoft. infabric. PowerShell. http

```powershell
    Get-SFApplicationBackupList -ApplicationId WordCount     
```

#### <a name="rest-call-using-powershell"></a>Chamada REST usando o PowerShell

Execute o seguinte script do PowerShell para invocar a API HTTP para enumerar os backups criados para todas as partições dentro do aplicativo `SampleApp`.

```powershell
$url = "http://localhost:19080/Applications/SampleApp/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get

$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```

Exemplo de saída para a execução acima:

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

#### <a name="using-service-fabric-explorer"></a>Usando Service Fabric Explorer

Para exibir os backups em Service Fabric Explorer, navegue até uma partição e selecione a guia backups.

![Enumerar backups][5]

## <a name="limitation-caveats"></a>Limitação/advertências
- Service Fabric cmdlets do PowerShell estão no modo de visualização.
- Não há suporte para clusters de Service Fabric no Linux.

## <a name="next-steps"></a>Passos seguintes
- [Compreendendo a configuração de backup periódico](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Referência da API REST de restauração de backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/partition-backedup-health-event.png
[3]: ./media/service-fabric-backuprestoreservice/enable-app-backup.png
[4]: ./media/service-fabric-backuprestoreservice/enable-application-backup.png
[5]: ./media/service-fabric-backuprestoreservice/backup-enumeration.png