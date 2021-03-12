---
title: Cópia de segurança e restauro periódicos no Azure Service Fabric
description: Utilize a funcionalidade de backup e restauro periódicos do Service Fabric para permitir a cópia de segurança periódica dos dados da sua aplicação.
ms.topic: conceptual
ms.date: 5/24/2019
ms.openlocfilehash: 42097b50277e78b3f0e8f5e61a2bf70cc08dbc02
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2021
ms.locfileid: "103198717"
---
# <a name="periodic-backup-and-restore-in-an-azure-service-fabric-cluster"></a>Backup periódico e restauro em um cluster de tecido de serviço Azure
> [!div class="op_single_selector"]
> * [Clusters no Azure](service-fabric-backuprestoreservice-quickstart-azurecluster.md) 
> * [Aglomerados autónomos](service-fabric-backuprestoreservice-quickstart-standalonecluster.md)
> 

O Service Fabric é uma plataforma de sistemas distribuídos que facilita o desenvolvimento e gestão de aplicações em nuvem baseadas em microserviços fiáveis, distribuídas e distribuídas. Permite a execução de micro serviços apátridas e imponentes. Os serviços estatais podem manter um estado mutável e autoritário para além do pedido e resposta ou de uma transação completa. Se um serviço stateful cair por muito tempo ou perder informações devido a um desastre, pode ter de ser restaurado para algum apoio recente do seu estado, a fim de continuar a prestar serviço após o seu regresso.

O Service Fabric replica o estado em vários nós para garantir que o serviço está altamente disponível. Mesmo que um nó no cluster falhe, o serviço continua disponível. Em certos casos, porém, continua a ser desejável que os dados do serviço sejam fiáveis contra falhas mais amplas.
 
Por exemplo, o serviço pode querer fazer o back up dos seus dados de forma a proteger dos seguintes cenários:
- Em caso de perda permanente de todo um cluster de Tecidos de Serviço.
- Perda permanente da maioria das réplicas de uma divisória de serviço
- Erros administrativos em que o Estado é acidentalmente apagado ou corrompido. Por exemplo, um administrador com privilégio suficiente elimina erroneamente o serviço.
- Insetos no serviço que causam corrupção de dados. Por exemplo, isto pode acontecer quando uma atualização de código de serviço começa a escrever dados defeituosos para uma Coleção Fiável. Neste caso, tanto o código como os dados podem ter de ser revertidos para um estado anterior.
- Processamento de dados offline. Pode ser conveniente ter o processamento offline de dados para inteligência empresarial que acontece separadamente do serviço que gera os dados.

O Service Fabric fornece uma API incorporada para fazer [backup e restauro](service-fabric-reliable-services-backup-restore.md)no tempo. Os desenvolvedores de aplicações podem usar estas APIs para apoiar periodicamente o estado do serviço. Além disso, se os administradores de serviços quiserem desencadear uma cópia de segurança de fora do serviço num momento específico, como antes de atualizarem a aplicação, os desenvolvedores precisam de expor o backup (e restaurar) como uma API do serviço. Manter os backups é um custo adicional acima disso. Por exemplo, pode querer fazer cinco backups incrementais a cada meia hora, seguido de uma cópia de segurança completa. Após a cópia de segurança completa, pode eliminar as cópias de segurança incrementais anteriores. Esta abordagem requer um código adicional que conduza a custos adicionais durante o desenvolvimento da aplicação.

O serviço de backup e restauro no Tecido de Serviço permite uma cópia de segurança fácil e automática das informações armazenadas em serviços estatais. O backup dos dados da aplicação numa base periódica é fundamental para proteger contra a perda de dados e a indisponibilidade de serviço. O Service Fabric fornece um serviço de backup e restauro opcional, que lhe permite configurar a cópia de segurança periódica dos Serviços Fidedigdos (incluindo Serviços de Ator) sem ter de escrever nenhum código adicional. Também facilita a restauração de backups previamente tomadas. 


O Service Fabric fornece um conjunto de APIs para obter a seguinte funcionalidade relacionada com a funcionalidade de backup e restauro periódico:

- Agende backup periódico de serviços estatais fiáveis e atores fiáveis com suporte para carregar backup para locais de armazenamento (externos). Locais de armazenamento suportados
    - Storage do Azure
    - Partilha de Ficheiros (no local)
- Backups enumeradores
- Desencadear uma cópia de segurança ad hoc de uma partição
- Restaurar uma divisória usando cópias de segurança anteriores
- Suspender temporariamente os backups
- Gestão de retenção de backups (futuros)

## <a name="prerequisites"></a>Pré-requisitos
* Conjunto de tecido de serviço com versão 6.4 ou superior. Consulte este [artigo](service-fabric-cluster-creation-via-arm.md) para obter etapas para criar o cluster de tecido de serviço usando o modelo de recurso Azure.
* Certificado X.509 para encriptação de segredos necessários para ligar ao armazenamento para armazenar backups. Consulte [o artigo](service-fabric-cluster-creation-via-arm.md) para saber como obter ou criar um certificado X.509.
* Aplicação Stateful fiável do tecido de serviço construída com a versão 3.0 ou superior do Service Fabric. Para aplicações que se direcionam para o .NET Core 2.0, a aplicação deve ser construída utilizando a versão 3.1 ou superior do Service Fabric SDK.
* Crie a conta de armazenamento Azure para armazenar cópias de segurança da aplicação.
* Instale microsoft.serviceFabric.Powershell.Http Module (Preview) para fazer chamadas de configuração.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

> [!NOTE]
> Se a versão PowerShellGet for inferior a 1.6.0, terá de atualizar para adicionar suporte à bandeira *-AllowPrerelease:*
>
> `Install-Module -Name PowerShellGet -Force`

* Certifique-se de que o Cluster está ligado utilizando o `Connect-SFCluster` comando antes de escoar qualquer pedido de configuração utilizando o Módulo Microsoft.ServiceFabric.Powershell.Http.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```

## <a name="enabling-backup-and-restore-service"></a>Ativar o serviço de backup e restauro

### <a name="using-azure-portal"></a>Com o Portal do Azure

Ativar `Include backup restore service` a caixa de `+ Show optional settings` verificação no `Cluster Configuration` separador.

![Ativar o serviço de restauro de backup com portal][1]


### <a name="using-azure-resource-manager-template"></a>Usando o modelo de gestor de recursos Azure
Primeiro, tem de ativar o _serviço de backup e restauro_ no seu cluster. Obtenha o modelo para o cluster que pretende implementar. Pode utilizar os [modelos](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) de amostra ou criar um modelo de Gestor de Recursos. Ativar o _serviço de backup e restauro_ com os seguintes passos:

1. Verifique se o `apiversion` recurso está definido para o recurso **`2018-02-01`** `Microsoft.ServiceFabric/clusters` e, caso contrário, atualize-o como mostrado no seguinte corte:

    ```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Agora, ative o _serviço de backup e restauro_ adicionando a seguinte secção na `addonFeatures` `properties` secção, conforme mostrado no seguinte corte: 

    ```json
        "properties": {
            ...
            "addonFeatures":  ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```
3. Configure o certificado X.509 para encriptação de credenciais. Isto é importante para garantir que as credenciais fornecidas para ligar ao armazenamento são encriptadas antes de persistir. Configure o certificado de encriptação adicionando a seguinte `BackupRestoreService` secção na `fabricSettings` secção, tal como mostrado no seguinte corte: 

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

4. Uma vez atualizado o seu modelo de cluster com as alterações anteriores, aplique-as e deixe a implementação/atualização completa. Uma vez concluído, o _serviço de cópia de segurança e restauro_ começa a funcionar no seu cluster. O Uri deste serviço é `fabric:/System/BackupRestoreService` e o serviço pode ser localizado sob a secção de serviço do sistema no explorador de Tecido de Serviço. 

## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Habilitação de backup periódico para serviços estatais fiáveis e atores fiáveis
Vamos percorrer passos para permitir apoio periódico para um serviço de confiança e atores fiáveis. Estes passos assumem
- Que o cluster está configurado usando segurança X.509 com _serviço de backup e restauro_.
- Um serviço stateful fiável é implantado no cluster. Para efeitos deste guia de arranque rápido, a aplicação Uri é `fabric:/SampleApp` e o Uri para serviço estadual fiável pertencente a esta aplicação é `fabric:/SampleApp/MyStatefulService` . Este serviço é implantado com uma única partição, e o ID de partição é `974bd92a-b395-4631-8a7f-53bd4ae9cf22` .
- O certificado de cliente com funçãode administrador está instalado no nome da loja Desíduser na máquina de onde serão invocados scripts abaixo.   Este exemplo utiliza `1b7ebe2174649c45474a4819dafae956712c31d3` como impressão digital deste certificado. Para obter mais informações sobre certificados de cliente, consulte [o controlo de acesso baseado em funções para clientes de Service Fabric](service-fabric-cluster-security-roles.md).

### <a name="create-backup-policy"></a>Criar política de backup

O primeiro passo é criar uma política de backup que descreva o calendário de backup, o armazenamento de alvos para dados de backup, o nome da política, o máximo de backups incrementais a serem permitidos antes de desencadear a política completa de backup e retenção para armazenamento de backup. 

Para armazenamento de cópias de segurança, utilize a conta Azure Storage criada acima. O recipiente `backup-container` está configurado para armazenar cópias de segurança. É criado um recipiente com este nome, se já não existir, durante o upload de cópias de segurança. Povoar `ConnectionString` com uma cadeia de ligação válida para a conta de Armazenamento Azure, substituindo pelo nome da sua conta de armazenamento e pela chave da sua conta de `account-name` `account-key` armazenamento.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell utilizando microsoft.serviceFabric.Powershell.Http Módulo

Execute os cmdlets powerShell para criar uma nova política de backup. Substitua `account-name` pelo nome da sua conta de armazenamento e pela chave da sua conta de `account-key` armazenamento.

```powershell

New-SFBackupPolicy -Name 'BackupPolicy1' -AutoRestoreOnDataLoss $true -MaxIncrementalBackups 20 -FrequencyBased -Interval 00:15:00 -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container' -Basic -RetentionDuration '10.00:00:00'

```

#### <a name="rest-call-using-powershell"></a>Chamada de repouso usando PowerShell

Execute o seguinte script PowerShell para invocar a API de REST necessária para criar uma nova política. Substitua `account-name` pelo nome da sua conta de armazenamento e pela chave da sua conta de `account-key` armazenamento.

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

#### <a name="using-service-fabric-explorer"></a>Usando o Explorador de Tecido de Serviço

1. No Service Fabric Explorer, navegue no separador Backups e selecione Actions > Create Backup Policy.

    ![Criar Política de Backup][6]

2. Preencha a informação. Para os clusters Azure, a AzureBlobStore deve ser selecionada.

    ![Criar backup Policy Azure Blob Storage][7]

### <a name="enable-periodic-backup"></a>Ativar a cópia de segurança periódica
Depois de definir a política de backup para cumprir os requisitos de proteção de dados da aplicação, a política de backup deve ser associada à aplicação. Dependendo da exigência, a política de backup pode ser associada a uma aplicação, serviço ou a uma divisória.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell utilizando microsoft.serviceFabric.Powershell.Http Módulo

```powershell

Enable-SFApplicationBackup -ApplicationId 'SampleApp' -BackupPolicyName 'BackupPolicy1'

```
#### <a name="rest-call-using-powershell"></a>Chamada de repouso usando PowerShell

Execute o seguinte script PowerShell para invocar a API de REST necessária para associar a política de backup com o nome `BackupPolicy1` criado acima do passo com a aplicação `SampleApp` .

```powershell
$BackupPolicyReference = @{
    BackupPolicyName = 'BackupPolicy1'
}

$body = (ConvertTo-Json $BackupPolicyReference)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/EnableBackup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
``` 

#### <a name="using-service-fabric-explorer"></a>Usando o Explorador de Tecido de Serviço
Certifique-se de que o [modo avançado](service-fabric-visualizing-your-cluster.md#backup-and-restore) para o Service Fabric Explorer está ativado 

1. Selecione uma aplicação e vá à ação. Clique em Ativar/Atualizar Backup da aplicação.

    ![Ativar backup de aplicações][3]

2. Por fim, selecione a política desejada e clique em Ativar o Backup.

    ![Selecione Política][4]


### <a name="verify-that-periodic-backups-are-working"></a>Verifique se as cópias de segurança periódicas estão a funcionar

Após permitir o backup ao nível da aplicação, todas as divisórias pertencentes a serviços estatais fiáveis e atores fiáveis ao abrigo da aplicação começarão a receber apoio periodicamente de acordo com a política de backup associada. 

![Evento de Saúde BackedUp de Partição][0]

### <a name="list-backups"></a>Backups de listas

Cópias de segurança associadas a todas as divisórias pertencentes a serviços estatais confiáveis e atores fiáveis da aplicação podem ser enumeradas usando _a API getBackups._ As cópias de segurança podem ser enumeradas para uma aplicação, serviço ou uma divisória.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell utilizando microsoft.serviceFabric.Powershell.Http Módulo

```powershell
    
Get-SFApplicationBackupList -ApplicationId WordCount
```

#### <a name="rest-call-using-powershell"></a>Chamada de repouso usando PowerShell

Execute o seguinte script PowerShell para invocar a API HTTP para enumerar as cópias de segurança criadas para todas as divisórias dentro da `SampleApp` aplicação.

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

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

#### <a name="using-service-fabric-explorer"></a>Usando o Explorador de Tecido de Serviço

Para visualizar cópias de segurança no Service Fabric Explorer, navegue para uma partição e selecione o separador Backups.

![Backups enumeradores][5]

## <a name="limitation-caveats"></a>Limitação/ressalvas
- Os cmdlets PowerShell do tecido de serviço estão em modo de pré-visualização.
- Sem suporte para clusters de tecido de serviço em Linux.

## <a name="next-steps"></a>Passos seguintes
- [Compreender a configuração da cópia de segurança periódica](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Backup restaurar referência API REST](/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/partition-backedup-health-event-azure.png
[1]: ./media/service-fabric-backuprestoreservice/enable-backup-restore-service-with-portal.png
[3]: ./media/service-fabric-backuprestoreservice/enable-app-backup.png
[4]: ./media/service-fabric-backuprestoreservice/enable-application-backup.png
[5]: ./media/service-fabric-backuprestoreservice/backup-enumeration.png
[6]: ./media/service-fabric-backuprestoreservice/create-bp.png
[7]: ./media/service-fabric-backuprestoreservice/creation-bp.png
