---
title: Backup periódico/restauro em tecido de serviço autónomo Azure
description: Utilize a cópia de segurança periódica de um Serviço autónomo e restabeleça a funcionalidade de restauro para permitir a cópia de segurança periódica dos dados da sua aplicação.
ms.topic: conceptual
ms.date: 5/24/2019
ms.openlocfilehash: d78a627c0c50a3e2ec57138e40cb5bc97486d6f7
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2021
ms.locfileid: "103198697"
---
# <a name="periodic-backup-and-restore-in-a-standalone-service-fabric"></a>Backup periódico e restauro em um tecido de serviço autónomo
> [!div class="op_single_selector"]
> * [Clusters no Azure](service-fabric-backuprestoreservice-quickstart-azurecluster.md) 
> * [Aglomerados autónomos](service-fabric-backuprestoreservice-quickstart-standalonecluster.md)
> 

O Service Fabric é uma plataforma de sistemas distribuídos que facilita o desenvolvimento e gestão de aplicações em nuvem baseadas em microserviços fiáveis, distribuídas e distribuídas. Permite a execução de micro serviços apátridas e imponentes. Os serviços estatais podem manter um estado mutável e autoritário para além do pedido e resposta ou de uma transação completa. Se um serviço stateful cair por muito tempo ou perder informações por causa de um desastre, pode ter de ser restaurado para algum apoio recente do seu estado, a fim de continuar a prestar serviço após o seu regresso.

O Service Fabric replica o estado em vários nós para garantir que o serviço está altamente disponível. Mesmo que um nó no cluster falhe, o serviço continua disponível. Em certos casos, porém, continua a ser desejável que os dados do serviço sejam fiáveis contra falhas mais amplas.
 
Por exemplo, um serviço pode precisar de fazer o back up dos seus dados para proteger dos seguintes cenários:
- Perda permanente de todo um cluster de Tecido de Serviço.
- Perda permanente da maioria das réplicas de uma divisória de serviço
- Erros administrativos em que o Estado é acidentalmente apagado ou corrompido. Por exemplo, um administrador com privilégio suficiente elimina erroneamente o serviço.
- Insetos no serviço que causam corrupção de dados. Por exemplo, isto pode acontecer quando uma atualização de código de serviço começa a escrever dados defeituosos para uma Coleção Fiável. Neste caso, tanto o código como os dados podem ter de ser revertidos para um estado anterior.
- Processamento de dados offline. Pode ser conveniente ter o processamento offline de dados para inteligência empresarial que acontece separadamente do serviço que gera os dados.

O Service Fabric fornece uma API incorporada para fazer [backup e restauro](service-fabric-reliable-services-backup-restore.md)no tempo. Os desenvolvedores de aplicações podem usar estas APIs para apoiar periodicamente o estado do serviço. Além disso, se os administradores de serviços quiserem desencadear uma cópia de segurança de fora do serviço num momento específico (como antes de atualizar a aplicação), os desenvolvedores precisam de expor o backup (e restaurar) como uma API do serviço. Manter os backups é um custo adicional acima disso. Por exemplo, pode querer fazer cinco backups incrementais a cada meia hora, seguido de uma cópia de segurança completa. Após a cópia de segurança completa, pode eliminar as cópias de segurança incrementais anteriores. Esta abordagem requer um código adicional que conduza a custos adicionais durante o desenvolvimento da aplicação.

A cópia de segurança dos dados da aplicação numa base periódica é uma necessidade básica para gerir uma aplicação distribuída e proteger contra a perda de dados ou a perda prolongada da disponibilidade do serviço. O Service Fabric fornece um serviço de backup e restauro opcional, que lhe permite configurar a cópia de segurança periódica dos Serviços Fidedigdos (incluindo Serviços de Ator) sem ter de escrever nenhum código adicional. Também facilita a restauração de backups previamente tomadas. 

O Service Fabric fornece um conjunto de APIs para obter a seguinte funcionalidade relacionada com a funcionalidade de backup e restauro periódico:

- Agende backup periódico de serviços estatais fiáveis e atores fiáveis com suporte para carregar backup para locais de armazenamento (externos). Locais de armazenamento suportados
    - Storage do Azure
    - Partilha de Ficheiros (no local)
- Backups enumeradores
- Desencadear uma cópia não planeada de uma partição
- Restaurar uma divisória usando cópias de segurança anteriores
- Suspender temporariamente os backups
- Gestão de retenção de backups (futuros)

## <a name="prerequisites"></a>Pré-requisitos
* Conjunto de tecido de serviço com versão 6.4 ou posterior. Consulte este [artigo](service-fabric-cluster-creation-for-windows-server.md) para obter as etapas para descarregar o pacote necessário.
* Certificado X.509 para encriptação de segredos necessários para ligar ao armazenamento para armazenar backups. Consulte [o artigo](service-fabric-windows-cluster-x509-security.md) para saber como adquirir ou criar um certificado X.509 auto-assinado.

* Aplicação Stateful fiável do tecido de serviço construída com a versão 3.0 ou superior do Service Fabric. Para aplicações que se direcionam para o .NET Core 2.0, a aplicação deve ser construída utilizando a versão 3.1 ou posterior do Service Fabric SDK.
* Instale microsoft.serviceFabric.PowerShell.Http Module (Preview) para fazer chamadas de configuração.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.PowerShell.Http -AllowPrerelease
```

> [!NOTE]
> Se a versão PowerShellGet for inferior a 1.6.0, terá de atualizar para adicionar suporte à bandeira *-AllowPrerelease:*
>
> `Install-Module -Name PowerShellGet -Force`

* Certifique-se de que o Cluster está ligado utilizando o `Connect-SFCluster` comando antes de escoar qualquer pedido de configuração utilizando o Módulo Microsoft.ServiceFabric.PowerShell.Http.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```

## <a name="enabling-backup-and-restore-service"></a>Ativar o serviço de backup e restauro
Primeiro, tem de ativar o _serviço de backup e restauro_ no seu cluster. Obtenha o modelo para o cluster que pretende implementar. Pode utilizar os [modelos de amostra.](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples) Ativar o _serviço de backup e restauro_ com os seguintes passos:

1. Verifique se o `apiversion` conjunto está definido para o ficheiro de `10-2017` configuração do cluster e, se não o fizer, atualize-o como mostrado no seguinte corte:

    ```json
    {
        "apiVersion": "10-2017",
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        ...
    }
    ```

2. Agora, ative o _serviço de backup e restauro_ adicionando a seguinte secção na `addonFeatures` `properties` secção, conforme mostrado no seguinte corte: 

    ```json
        "properties": {
            ...
            "addonFeatures": ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```

3. Configure o certificado X.509 para encriptação de credenciais. Isto é importante para garantir que as credenciais fornecidas, se houver, para ligar ao armazenamento são encriptadas antes de persistir. Configure o certificado de encriptação adicionando a seguinte `BackupRestoreService` secção na `fabricSettings` secção, tal como mostrado no seguinte corte: 

    ```json
    "properties": {
        ...
        "addonFeatures": ["BackupRestoreService"],
        "fabricSettings": [{
            "name": "BackupRestoreService",
            "parameters":  [{
                "name": "SecretEncryptionCertThumbprint",
                "value": "[Thumbprint]"
            },
            {
                "name": "SecretEncryptionCertX509StoreName",
                "value": "My"
            }]
        }
        ...
    }
    ```

4. Depois de ter atualizado o ficheiro de configuração do cluster com as alterações anteriores, aplique-as e deixe a implementação/atualização completa. Uma vez concluído, o _serviço de cópia de segurança e restauro_ começa a funcionar no seu cluster. O Uri deste serviço é `fabric:/System/BackupRestoreService` e o serviço pode ser localizado sob a secção de serviço do sistema no explorador de Tecido de Serviço. 



## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Habilitação de backup periódico para serviços estatais fiáveis e atores fiáveis
Vamos percorrer passos para permitir apoio periódico para um serviço de confiança e atores fiáveis. Estes passos assumem
- O cluster está configurado com cópia de segurança e restauro service_.
- Um serviço stateful fiável é implantado no cluster. Para efeitos deste guia de arranque rápido, a aplicação Uri é `fabric:/SampleApp` e o Uri para serviço estadual fiável pertencente a esta aplicação é `fabric:/SampleApp/MyStatefulService` . Este serviço é implantado com uma única partição, e o ID de partição é `23aebc1e-e9ea-4e16-9d5c-e91a614fefa7` .  

### <a name="create-backup-policy"></a>Criar política de backup

O primeiro passo é criar uma política de backup que descreva o calendário de backup, o armazenamento de alvos para dados de backup, o nome da política, o máximo de backups incrementais a serem permitidos antes de desencadear a política completa de backup e retenção para armazenamento de backup. 

Para armazenamento de cópias de segurança, crie partilha de ficheiros e dê acesso ao ReadWrite a esta partilha de ficheiros para todas as máquinas de nó de tecido de serviço. Este exemplo pressupõe que a partilha com o nome `BackupStore` está presente `StorageServer` em .


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell utilizando microsoft.serviceFabric.PowerShell.http Module

```powershell

New-SFBackupPolicy -Name 'BackupPolicy1' -AutoRestoreOnDataLoss $true -MaxIncrementalBackups 20 -FrequencyBased -Interval 00:15:00 -FileShare -Path '\\StorageServer\BackupStore' -Basic -RetentionDuration '10.00:00:00'

```
#### <a name="rest-call-using-powershell"></a>Chamada de repouso usando PowerShell

Execute o seguinte script PowerShell para invocar a API de REST necessária para criar uma nova política.

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

#### <a name="using-service-fabric-explorer"></a>Usando o Explorador de Tecido de Serviço

1. No Service Fabric Explorer, navegue no separador Backups e selecione Actions > Create Backup Policy.

    ![Criar Política de Backup][6]

2. Preencha a informação. Para clusters autónomos, o FileShare deve ser selecionado.

    ![Criar Backup Policy FileShare][7]

### <a name="enable-periodic-backup"></a>Ativar a cópia de segurança periódica
Depois de definir a política para cumprir os requisitos de proteção de dados da aplicação, a política de backup deve ser associada à aplicação. Dependendo da exigência, a política de backup pode ser associada a uma aplicação, serviço ou a uma divisória.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell utilizando microsoft.serviceFabric.PowerShell.http Module

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
$url = "http://localhost:19080/Applications/SampleApp/$/EnableBackup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json'
``` 

#### <a name="using-service-fabric-explorer"></a>Usando o Explorador de Tecido de Serviço

1. Selecione uma aplicação e vá à ação. Clique em Ativar/Atualizar Backup da aplicação.

    ![Ativar backup de aplicações][3] 

2. Por fim, selecione a política desejada e selecione *Enable Backup*.

    ![Selecione Política][4]

### <a name="verify-that-periodic-backups-are-working"></a>Verifique se as cópias de segurança periódicas estão a funcionar

Após permitir o backup para a aplicação, todas as divisórias pertencentes a serviços estatais fiáveis e atores fiáveis ao abrigo da aplicação começarão a receber apoio periodicamente de acordo com a política de backup associada.

![Evento de Saúde BackedUp de Partição][0]

### <a name="list-backups"></a>Backups de listas

Cópias de segurança associadas a todas as divisórias pertencentes a serviços estatais confiáveis e atores fiáveis da aplicação podem ser enumeradas usando _a API getBackups._ Dependendo da exigência, as cópias de segurança podem ser enumeradas para aplicação, serviço ou partição.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell utilizando microsoft.serviceFabric.PowerShell.http Module

```powershell
    Get-SFApplicationBackupList -ApplicationId WordCount     
```

#### <a name="rest-call-using-powershell"></a>Chamada de repouso usando PowerShell

Execute o seguinte script PowerShell para invocar a API HTTP para enumerar as cópias de segurança criadas para todas as divisórias dentro da `SampleApp` aplicação.

```powershell
$url = "http://localhost:19080/Applications/SampleApp/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get

$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```

Saída da amostra para a execução acima:

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

#### <a name="using-service-fabric-explorer"></a>Usando o Explorador de Tecido de Serviço

Para visualizar cópias de segurança no Service Fabric Explorer, navegue para uma partição e selecione o separador Backups.

![Backups enumeradores][5]

## <a name="limitation-caveats"></a>Limitação/ressalvas
- Os cmdlets PowerShell do tecido de serviço estão em modo de pré-visualização.
- Sem suporte para clusters de tecido de serviço em Linux.

## <a name="next-steps"></a>Passos seguintes
- [Compreender a configuração da cópia de segurança periódica](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Backup restaurar referência API REST](/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/partition-backedup-health-event.png
[3]: ./media/service-fabric-backuprestoreservice/enable-app-backup.png
[4]: ./media/service-fabric-backuprestoreservice/enable-application-backup.png
[5]: ./media/service-fabric-backuprestoreservice/backup-enumeration.png
[6]: ./media/service-fabric-backuprestoreservice/create-bp.png
[7]: ./media/service-fabric-backuprestoreservice/create-bp-fileshare.png
