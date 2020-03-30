---
title: Cópia de segurança e restauro periódicos no Azure Service Fabric
description: Utilize a função de cópia de segurança periódica do Service Fabric para permitir a cópia de segurança periódica dos dados dos seus dados de aplicação.
author: hrushib
ms.topic: conceptual
ms.date: 5/24/2019
ms.author: hrushib
ms.openlocfilehash: f56fcb7d1dde700d954c3b55bcf8cd7759893521
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259009"
---
# <a name="periodic-backup-and-restore-in-an-azure-service-fabric-cluster"></a>Backup periódico e restauro em um cluster De Tecido de Serviço Azure
> [!div class="op_single_selector"]
> * [Clusters no Azure](service-fabric-backuprestoreservice-quickstart-azurecluster.md) 
> * [Aglomerados autónomos](service-fabric-backuprestoreservice-quickstart-standalonecluster.md)
> 

O Service Fabric é uma plataforma de sistemas distribuídos que facilita o desenvolvimento e gestão de aplicações de nuvem fiáveis, distribuídas e baseadas em microserviços. Permite o funcionamento de micro serviços apátridas e apátridas. Os serviços estatais podem manter um estado mutável e autoritário para além do pedido e resposta ou de uma transação completa. Se um serviço de estado se abater durante muito tempo ou perder informações devido a um desastre, poderá ter de ser restaurado a algum apoio recente do seu estado, a fim de continuar a prestar serviço depois de voltar a subir.

Service Fabric replica o estado através de vários nódosos para garantir que o serviço está altamente disponível. Mesmo que um nó no cluster falhe, o serviço continua disponível. Em certos casos, porém, continua a ser desejável que os dados dos serviços sejam fiáveis contra falhas mais amplas.
 
Por exemplo, o serviço pode querer fazer o backback dos seus dados de forma a proteger dos seguintes cenários:
- Em caso de perda permanente de todo um cluster de Tecido de Serviço.
- Perda permanente da maioria das réplicas de uma partição de serviço
- Erros administrativos em que o Estado é acidentalmente apagado ou corrompido. Por exemplo, um administrador com privilégios suficientes elimina erroneamente o serviço.
- Insetos no serviço que causam corrupção de dados. Por exemplo, isto pode acontecer quando uma atualização de código de serviço começa a escrever dados defeituosos para uma Coleção Fiável. Neste caso, tanto o código como os dados podem ter de ser revertidos para um estado anterior.
- Processamento de dados offline. Pode ser conveniente ter o processamento offline de dados para inteligência empresarial que acontece separadamente do serviço que gera os dados.

O Serviço Fabric fornece uma API incorporada para fazer o ponto de [reserva e restaurar](service-fabric-reliable-services-backup-restore.md)o tempo. Os desenvolvedores de aplicações podem utilizar estas APIs para apoiar periodicamente o estado do serviço. Além disso, se os administradores de serviços quiserem ativar uma cópia de segurança de fora do serviço num momento específico, como antes de atualizar em tempo a aplicação, os desenvolvedores precisam expor a cópia de segurança (e restaurar) como uma API do serviço. Manter os backups é um custo adicional acima deste. Por exemplo, pode querer fazer cinco backups incrementais a cada meia hora, seguidos de uma cópia de segurança completa. Após a cópia de segurança completa, pode eliminar as cópias de segurança incrementais anteriores. Esta abordagem requer um código adicional que conduza a custos adicionais durante o desenvolvimento da aplicação.

O serviço de backup e restauro no Tecido de Serviço permite uma cópia de segurança fácil e automática das informações armazenadas em serviços imponentes. O backup dos dados de aplicação numa base periódica é fundamental para a proteção contra a perda de dados e a indisponibilidade do serviço. O Service Fabric fornece um serviço de backup e restauro opcional, que lhe permite configurar cópias de segurança periódicas de serviços fiáveis (incluindo Serviços de Ator) sem ter de escrever qualquer código adicional. Também facilita a restauração de cópias de segurança previamente tomadas. 


O Service Fabric fornece um conjunto de APIs para obter a seguinte funcionalidade relacionada com a funcionalidade de backup e restauro periódica:

- Agendar backup periódico de serviços fiáveis e atores fiáveis com suporte para fazer o upload de backup para locais de armazenamento (externos). Locais de armazenamento suportados
    - Storage do Azure
    - Partilha de Ficheiros (no local)
- Enumerar backups
- Desencadear uma cópia de segurança ad hoc
- Restaurar uma divisória usando cópias de segurança anteriores
- Suspender temporariamente os backups
- Gestão de retenção de backups (próximo)

## <a name="prerequisites"></a>Pré-requisitos
* Cluster de tecido de serviço com versão Fabric 6.4 ou superior. Consulte este [artigo](service-fabric-cluster-creation-via-arm.md) para obter passos para criar cluster de tecido de serviço utilizando o modelo de recurso Azure.
* X.509 Certificado de encriptação de segredos necessários para ligar ao armazenamento para armazenar backups. Consulte o [artigo](service-fabric-cluster-creation-via-arm.md) para saber como obter ou criar um certificado X.509.
* Fabricação fiável aplicação imponente construída com recurso à versão SDK de tecido de serviço 3.0 ou superior. Para aplicações direcionadas para .NET Core 2.0, a aplicação deve ser construída utilizando a versão 3.1 do Service Fabric SDK.
* Crie a conta de Armazenamento Azure para armazenar cópias de segurança da aplicação.
* Instale microsoft.serviceFabric.Powershell.Http Módulo [Em Pré-visualização] para fazer chamadas de configuração.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

* Certifique-se de que `Connect-SFCluster` o Cluster está ligado utilizando o comando antes de efazer qualquer pedido de configuração utilizando o Microsoft.ServiceFabric.Powershell.Http Module.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```

## <a name="enabling-backup-and-restore-service"></a>Habilitar o serviço de backup e restauro

### <a name="using-azure-portal"></a>Com o Portal do Azure

Ativar `Include backup restore service` a `+ Show optional settings` `Cluster Configuration` caixa de verificação no separador.

![Ativar o serviço de restauro de backup com portal][1]


### <a name="using-azure-resource-manager-template"></a>Usando o modelo de gestor de recursos azure
Primeiro, tem de ativar o serviço de _backup e restaurar_ o serviço no seu cluster. Obtenha o modelo para o cluster que pretende implementar. Pode utilizar os [modelos](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) de amostra ou criar um modelo de Gestor de Recursos. Ativar o serviço de _backup e restaurar_ com os seguintes passos:

1. Verifique se `apiversion` o **`2018-02-01`** recurso `Microsoft.ServiceFabric/clusters` está definido e, se não, atualize-o como mostrado no seguinte corte:

    ```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Agora, ative o serviço de `addonFeatures` backup `properties` _e restaure_ adicionando a seguinte secção abaixo, como mostrado no seguinte corte: 

    ```json
        "properties": {
            ...
            "addonFeatures":  ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```
3. Configure o certificado X.509 para encriptação de credenciais. Isto é importante para garantir que as credenciais fornecidas para ligar ao armazenamento são encriptadas antes de persistir. Configure o certificado de `BackupRestoreService` encriptação `fabricSettings` adicionando a seguinte secção por baixo da secção, como mostrado no seguinte corte: 

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

4. Depois de ter atualizado o seu modelo de cluster com as alterações anteriores, aplique-as e deixe a implementação/atualização completa. Uma vez concluído, o serviço de _backup e restauro_ começa a funcionar no seu cluster. O Uri deste `fabric:/System/BackupRestoreService` serviço é e o serviço pode ser localizado sob a secção de serviço de sistema no explorador de Tecido de Serviço. 

## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Habilitar cópias de segurança periódicas para serviço fiável e atores fiáveis
Vamos percorrer etapas para permitir backup periódico para o serviço fiável e fiável e para os atores fiáveis. Estes passos assumem
- Que o cluster está configurado usando a segurança X.509 com serviço de _backup e restauro_.
- Um serviço fiável e imponente é implantado no cluster. Para efeitos deste guia de arranque `fabric:/SampleApp` rápido, a aplicação Uri é e `fabric:/SampleApp/MyStatefulService`o Uri para serviço fiável e imponente pertencente a esta aplicação é . Este serviço é implantado com uma única `974bd92a-b395-4631-8a7f-53bd4ae9cf22`divisória, e o ID da partição é .
- O certificado de cliente com função de administrador está instalado no nome da loja _My_ _(Personal)_ da localização da loja de certificados _CurrentUser_ na máquina de onde serão invocados scripts abaixo. Este exemplo `1b7ebe2174649c45474a4819dafae956712c31d3` usa como impressão digital deste certificado. Para obter mais informações sobre os certificados de cliente, consulte [o controlo de acesso baseado em Role para clientes de Tecido de Serviço](service-fabric-cluster-security-roles.md).

### <a name="create-backup-policy"></a>Criar política de backup

O primeiro passo é criar uma política de backup descrevendo o calendário de backup, armazenamento de alvos para dados de backup, nome de política, cópias de segurança incrementais máximas a serem permitidas antes de desencadear uma política completa de backup e retenção para armazenamento de backup. 

Para armazenamento de cópia seleções, utilize a conta De armazenamento Azure criada acima. O `backup-container` recipiente está configurado para armazenar cópias de segurança. É criado um recipiente com este nome, se ainda não existir, durante o upload de cópia de segurança. Povoe `ConnectionString` com uma cadeia de ligação válida `account-name` para a conta `account-key` De armazenamento Azure, substituindo pelo nome da sua conta de armazenamento e com a chave da sua conta de armazenamento.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell usando Microsoft.ServiceFabric.Powershell.Http Módulo

Execute os cmdlets powerShell para criar uma nova política de backup. Substitua-a `account-name` pelo nome `account-key` da sua conta de armazenamento e pela chave da sua conta de armazenamento.

```powershell

New-SFBackupPolicy -Name 'BackupPolicy1' -AutoRestoreOnDataLoss $true -MaxIncrementalBackups 20 -FrequencyBased -Interval 00:15:00 -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container' -Basic -RetentionDuration '10.00:00:00'

```

#### <a name="rest-call-using-powershell"></a>Chamada de descanso usando PowerShell

Execute o seguinte script PowerShell para invocar a Necessária API REST para criar uma nova política. Substitua-a `account-name` pelo nome `account-key` da sua conta de armazenamento e pela chave da sua conta de armazenamento.

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

1. No Service Fabric Explorer, navegue para o separador Backups e selecione Ações > Criar Política de Backup.

    ![Criar política de backup][6]

2. Preencha a informação. Para os clusters Azure, a AzureBlobStore deve ser selecionada.

    ![Criar política de backup Armazenamento De blob Azure][7]

### <a name="enable-periodic-backup"></a>Ativar cópia de segurança periódica
Após a definição da política de backup para cumprir os requisitos de proteção de dados da aplicação, a política de backup deve ser associada à aplicação. Dependendo da exigência, a política de backup pode ser associada a uma aplicação, serviço ou partição.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell usando Microsoft.ServiceFabric.Powershell.Http Módulo

```powershell

Enable-SFApplicationBackup -ApplicationId 'SampleApp' -BackupPolicyName 'BackupPolicy1'

```
#### <a name="rest-call-using-powershell"></a>Chamada de descanso usando PowerShell

Execute o seguinte script PowerShell para invocar a Necessária `BackupPolicy1` API REST para `SampleApp`associar a política de backup com o nome criado em passo acima com a aplicação .

```powershell
$BackupPolicyReference = @{
    BackupPolicyName = 'BackupPolicy1'
}

$body = (ConvertTo-Json $BackupPolicyReference)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/EnableBackup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
``` 

#### <a name="using-service-fabric-explorer"></a>Usando o Explorador de Tecido de Serviço

1. Selecione uma aplicação e vá para a ação. Clique na cópia de segurança da aplicação ativa/atualização.

    ![Ativar a cópia de segurança da aplicação][3]

2. Por fim, selecione a política desejada e clique em 'Ativar Backup'.

    ![Selecionar Política][4]


### <a name="verify-that-periodic-backups-are-working"></a>Verifique se as cópias de segurança periódicas estão a funcionar

Depois de permitir o backup a nível de aplicação, todas as divisórias pertencentes a serviços de Stateful Confiáveis e Atores Fiáveis ao abrigo da aplicação começarão a ser apoiadas periodicamente de acordo com a política de backup associada. 

![Evento de saúde apoiado por partição][0]

### <a name="list-backups"></a>Lista de Backups

As cópias de segurança associadas a todas as divisórias pertencentes a serviços de Stateful Confiáveis e A Agentes Fiáveis da aplicação podem ser enumeradas usando _a API GetBackups._ As cópias de segurança podem ser enumeradas para uma aplicação, serviço ou partição.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell usando Microsoft.ServiceFabric.Powershell.Http Módulo

```powershell
    
Get-SFApplicationBackupList -ApplicationId WordCount
```

#### <a name="rest-call-using-powershell"></a>Chamada de descanso usando PowerShell

Execute o seguinte script PowerShell para invocar o HTTP API para `SampleApp` enumerar as cópias de segurança criadas para todas as divisórias dentro da aplicação.

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

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

Para ver cópias de segurança no Service Fabric Explorer, navegue para uma partição e selecione o separador Backups.

![Enumerar backups][5]

## <a name="limitation-caveats"></a>Limitação/ressalvas
- Os cmdlets de powerShell de tecido de serviço estão no modo de pré-visualização.
- Sem suporte para clusters de tecido de serviço em Linux.

## <a name="next-steps"></a>Passos seguintes
- [Compreender a configuração da cópia de segurança periódica](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Cópia de segurança restaurar referência REST API](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/partition-backedup-health-event-azure.png
[1]: ./media/service-fabric-backuprestoreservice/enable-backup-restore-service-with-portal.png
[3]: ./media/service-fabric-backuprestoreservice/enable-app-backup.png
[4]: ./media/service-fabric-backuprestoreservice/enable-application-backup.png
[5]: ./media/service-fabric-backuprestoreservice/backup-enumeration.png
[6]: ./media/service-fabric-backuprestoreservice/create-bp.png
[7]: ./media/service-fabric-backuprestoreservice/creation-bp.png