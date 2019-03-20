---
title: Cópia de segurança do Azure - utilize o PowerShell para efetuar cópias de segurança de cargas de trabalho do DPM
description: Saiba como implementar e gerir a cópia de segurança do Azure para o Data Protection Manager (DPM) com o PowerShell
services: backup
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 1/23/2017
ms.author: adigan
ms.openlocfilehash: 639ccb2a0680793b50af52dc16c6d06505d5079b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57899575"
---
# <a name="deploy-and-manage-backup-to-azure-for-data-protection-manager-dpm-servers-using-powershell"></a>Implementar e gerir cópias de segurança para o Azure em servidores do Data Protection Manager (DPM) com o PowerShell
Este artigo mostra-lhe como utilizar o PowerShell para configuração de cópia de segurança do Azure num servidor DPM e para gerir a cópia de segurança e recuperação.

## <a name="setting-up-the-powershell-environment"></a>Configurar o ambiente do PowerShell

Antes de poder utilizar o PowerShell para gerir cópias de segurança do Data Protection Manager para o Azure, tem de ter o ambiente correto no PowerShell. No início de sessão do PowerShell, certifique-se de que execute o seguinte comando para importar os módulos certos e permitem-lhe referenciar corretamente os cmdlets do DPM:

```
PS C:> & "C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin\DpmCliInitScript.ps1"

Welcome to the DPM Management Shell!

Full list of cmdlets: Get-Command
Only DPM cmdlets: Get-DPMCommand
Get general help: help
Get help for a cmdlet: help <cmdlet-name> or <cmdlet-name> -?
Get definition of a cmdlet: Get-Command <cmdlet-name> -Syntax
Sample DPM scripts: Get-DPMSampleScript
```

## <a name="setup-and-registration"></a>Configuração e registo

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para começar, [transferir o Azure PowerShell mais recente](/powershell/azure/install-az-ps).

As seguintes tarefas de configuração e registo podem ser automatizadas com o PowerShell:

* Criar um cofre dos Serviços de Recuperação 
* Instalar o agente de cópia de segurança do Azure
* Registar com o serviço de cópia de segurança do Azure
* Definições de rede
* Definições de encriptação

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos serviços de recuperação
Os seguintes passos levá-lo através da criação de um cofre dos serviços de recuperação. Um cofre dos serviços de recuperação é diferente de um cofre de cópia de segurança.

1. Se estiver a utilizar o Azure Backup pela primeira vez, tem de utilizar o **Register-AzResourceProvider** cmdlet para registar o fornecedor de serviços de recuperação do Azure com a sua subscrição.

    ```
    PS C:\> Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
2. O Cofre dos serviços de recuperação é um recurso do ARM, por isso terá de colocá-lo dentro de um grupo de recursos. Pode utilizar um grupo de recursos existente ou crie um novo. Ao criar um novo grupo de recursos, especifique o nome e local para o grupo de recursos.  

    ```
    PS C:\> New-AzResourceGroup –Name "test-rg" –Location "West US"
    ```
3. Utilize o **New-AzRecoveryServicesVault** cmdlet para criar um novo cofre. Certifique-se de que especifique a mesma localização do cofre que foi utilizado para o grupo de recursos.

    ```
    PS C:\> New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "West US"
    ```
4. Especifique o tipo de redundância de armazenamento a utilizar. Pode usar [armazenamento localmente redundante (LRS)](../storage/common/storage-redundancy-lrs.md) ou [armazenamento Georredundante (GRS)](../storage/common/storage-redundancy-grs.md). O exemplo seguinte mostra que a opção - BackupStorageRedundancy para testVault está definida como GeoRedundant.

   > [!TIP]
   > Muitos cmdlets do Azure Backup requerem o objeto do cofre dos Serviços de Recuperação como entrada. Por este motivo, é conveniente armazenar o objeto do cofre dos Serviços de Recuperação do Backup numa variável.
   >
   >

    ```
    PS C:\> $vault1 = Get-AzRecoveryServicesVault –Name "testVault"
    PS C:\> Set-AzRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>Ver os cofres numa subscrição
Uso **Get-AzRecoveryServicesVault** para ver a lista de todos os cofres na subscrição atual. Pode utilizar este comando para verificar que foi criado um cofre novo, ou para ver quais os cofres estão disponíveis na subscrição.

Execute o comando, Get-AzRecoveryServicesVault, e a subscrição de todos os cofres estão listados.

```
PS C:\> Get-AzRecoveryServicesVault
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```


## <a name="installing-the-azure-backup-agent-on-a-dpm-server"></a>Instalar o agente de cópia de segurança do Azure num servidor DPM
Antes de instalar o agente de cópia de segurança do Azure, tem de ter o instalador transferido e presente no Windows Server. Pode obter a versão mais recente do programa de instalação do [Microsoft Download Center](https://aka.ms/azurebackup_agent) ou a partir da página do Dashboard do Cofre de serviços de recuperação. Guardar o instalador para uma localização facilmente acessível como * C:\Downloads\*.

Para instalar o agente, execute o seguinte comando numa consola elevada do PowerShell **no servidor do DPM**:

```
PS C:\> MARSAgentInstaller.exe /q
```

Esta ação instala o agente com todas as opções padrão. A instalação demora alguns minutos, em segundo plano. Se não especificar a */nu* opção a **Windows Update** é aberta a janela no final da instalação para verificar a existência de quaisquer atualizações.

O agente aparece na lista de programas instalados. Para ver a lista de programas instalados, aceda a **painel de controlo** > **programas** > **programas e funcionalidades**.

![Agente instalado](./media/backup-dpm-automation/installed-agent-listing.png)

### <a name="installation-options"></a>Opções de instalação
Para ver todas as opções disponíveis por meio da linha de comandos, utilize o seguinte comando:

```
PS C:\> MARSAgentInstaller.exe /?
```

As opções disponíveis incluem:

| Opção | Detalhes | Predefinição |
| --- | --- | --- |
| /q |Instalação silencioso |- |
| /p:"location" |Caminho para a pasta de instalação para o agente de cópia de segurança do Azure. |C:\Program Files\Microsoft Azure Recovery Services Agent |
| /s:"location" |Caminho para a pasta de cache para o agente de cópia de segurança do Azure. |C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch |
| /m |Inscreva-se no Microsoft Update |- |
| /nu |Não procurar atualizações depois de concluída a instalação |- |
| /d |Desinstala o agente de serviços de recuperação do Microsoft Azure |- |
| /ph |Endereço de anfitrião do proxy |- |
| /po |Número de porta de anfitrião do proxy |- |
| /pu |O nome de utilizador do proxy anfitrião |- |
| /pw |Palavra-passe do proxy |- |

## <a name="registering-dpm-to-a-recovery-services-vault"></a>O registo DPM para uma recuperação Cofre de serviços
Depois de criado o Cofre dos serviços de recuperação, transfira o agente mais recente e as credenciais do cofre e armazene-o numa localização conveniente como C:\Downloads.

```
PS C:\> $credspath = "C:\downloads"
PS C:\> $credsfilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $vault1 -Path  $credspath
PS C:\> $credsfilename
C:\downloads\testvault\_Sun Apr 10 2016.VaultCredentials
```

No servidor do DPM, execute o [Start-OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx) cmdlet para registar a máquina com o cofre.

```
PS C:\> $cred = $credspath + $credsfilename
PS C:\> Start-OBRegistration-VaultCredentials $cred -Confirm:$false
CertThumbprint      :7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName: testvault
Region              :West US
Machine registration succeeded.
```

### <a name="initial-configuration-settings"></a>Definições de configuração inicial
Assim que o servidor do DPM está registado com o Cofre dos serviços de recuperação, ele começa com predefinições de subscrição. Estas definições de subscrição incluem redes, encriptação e a área de transição. Para alterar as definições de subscrição tem de obter primeiro uma dica sobre as definições (predefinição) existentes usando o [Get-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612793) cmdlet:

```
$setting = Get-DPMCloudSubscriptionSetting -DPMServerName "TestingServer"
```

Todas as modificações são efetuadas a este objeto do PowerShell local ```$setting``` e, em seguida, o objeto completo está empenhado em DPM e o Azure Backup para salvá-los usando o [conjunto DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791) cmdlet. Tem de utilizar o ```–Commit``` sinalizador para garantir que as alterações serem mantidas. As definições não serão aplicadas e utilizadas pelo Azure Backup, a menos que consolidadas.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="networking"></a>Redes
Se a conectividade da máquina do DPM para o serviço de cópia de segurança do Azure na internet através de um servidor proxy, em seguida, as definições do servidor proxy devem ser fornecidas para cópias de segurança com êxito. Isso é feito utilizando o ```-ProxyServer```e ```-ProxyPort```, ```-ProxyUsername``` e o ```ProxyPassword``` parâmetros com o [conjunto DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791) cmdlet. Neste exemplo, não existe nenhum servidor de proxy, de modo que são explicitamente a limpar todas as informações relacionadas com o proxy.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoProxy
```

Utilização de largura de banda, também pode ser controlada com opções de ```-WorkHourBandwidth``` e ```-NonWorkHourBandwidth``` para um determinado conjunto de dias da semana. Neste exemplo, podemos não definem qualquer limitação.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoThrottle
```

## <a name="configuring-the-staging-area"></a>Configurar a área de transição
O agente de cópia de segurança do Azure em execução no servidor DPM precisa de armazenamento temporário para dados restaurados a partir da cloud (área de transição local). Configure a área de transição através da [DPMCloudSubscriptionSetting conjunto](https://technet.microsoft.com/library/jj612791) cmdlet e o ```-StagingAreaPath``` parâmetro.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -StagingAreaPath "C:\StagingArea"
```

No exemplo acima, a área de transição será definida como *C:\StagingArea* no objeto do PowerShell ```$setting```. Certifique-se de que a pasta especificada já existe; caso contrário, a consolidação final das definições de subscrição irá falhar.

### <a name="encryption-settings"></a>Definições de encriptação
Os dados de cópia de segurança enviados para o Azure Backup são encriptados para proteger a confidencialidade dos dados. A frase de acesso de encriptação é a "palavra-passe" para descriptografar os dados no momento do restauro. É importante manter essas informações seguros e protegidos depois de definida.

No exemplo abaixo, o primeiro comando converte a cadeia de caracteres ```passphrase123456789``` para uma cadeia segura e atribui a cadeia segura para a variável com o nome ```$Passphrase```. o segundo comando define a cadeia de caracteres segura em ```$Passphrase``` como a palavra-passe para encriptar as cópias de segurança.

```
PS C:\> $Passphrase = ConvertTo-SecureString -string "passphrase123456789" -AsPlainText -Force

PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -EncryptionPassphrase $Passphrase
```

> [!IMPORTANT]
> Manter as informações de frase de acesso seguro e fiável, depois de definida. Não será capaz de restaurar dados a partir do Azure sem esta frase de acesso.
>
>

Neste momento, deve efetuar todas as alterações necessárias para o ```$setting``` objeto. Lembre-se consolidar as alterações.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="protect-data-to-azure-backup"></a>Proteger os dados para o Azure Backup
Nesta secção, irá adicionar um servidor de produção ao DPM e, em seguida, proteger os dados para o armazenamento do DPM local e, em seguida, a cópia de segurança do Azure. Nos exemplos, demonstraremos como fazer uma cópia de segurança de ficheiros e pastas. A lógica pode ser facilmente estendida para qualquer origem de dados do DPM com suporte de cópia de segurança. Todas as suas cópias de segurança do DPM são regidas por um grupo de proteção (PG) com quatro partes:

1. **Os membros do grupo** é uma lista de todos os objetos protegíveis (também conhecido como *origens de dados* no DPM) que pretende proteger no mesmo grupo de proteção. Por exemplo, pode querer proteger VMs de produção num grupo de proteção e bases de dados do SQL Server noutro grupo de proteção, que podem ter diferentes requisitos de cópia de segurança. Antes de pode fazer backup de qualquer origem de dados num servidor de produção, que tem de certificar-se de que o agente do DPM está instalado no servidor e é gerido pelo DPM. Siga os passos para [instalar o agente do DPM](https://technet.microsoft.com/library/bb870935.aspx) e vinculando-o para o servidor de DPM adequado.
2. **Método de proteção de dados** Especifica as cópia de segurança localizações dos destinos de-banda, disco e na cloud. No nosso exemplo, irá proteger os dados para o disco local e na cloud.
3. R **agenda de cópia de segurança** que especifica quando precisam ser levadas cópias de segurança e a frequência com que os dados devem ser sincronizados entre o servidor DPM e o servidor de produção.
4. R **agenda de retenção** que especifica o período de tempo para reter os pontos de recuperação no Azure.

### <a name="creating-a-protection-group"></a>Criar um grupo de proteção
Comece por criar um novo grupo de proteção utilizando o [New-DPMProtectionGroup](https://technet.microsoft.com/library/hh881722) cmdlet.

```
PS C:\> $PG = New-DPMProtectionGroup -DPMServerName " TestingServer " -Name "ProtectGroup01"
```

O cmdlet acima irá criar um grupo de proteção com o nome *ProtectGroup01*. Um grupo de proteção existente também pode ser modificado posteriormente para adicionar a cópia de segurança na cloud do Azure. No entanto, para fazer alterações ao grupo de proteção - novo ou existente - temos de lidar com uma *modificável* objeto usando o [Get-DPMModifiableProtectionGroup](https://technet.microsoft.com/library/hh881713) cmdlet.

```
PS C:\> $MPG = Get-ModifiableProtectionGroup $PG
```

### <a name="adding-group-members-to-the-protection-group"></a>Adicionar membros de grupo para o grupo de proteção
Cada agente do DPM sabe a lista de origens de dados no servidor que está instalado no. Para adicionar uma origem de dados para o grupo de proteção, o agente do DPM tem de enviar primeiro uma lista das origens de dados no servidor do DPM. Um ou mais origens de dados, em seguida, são selecionadas e adicionadas ao grupo de proteção. Os passos de PowerShell necessários para atingir esse objetivo são:

1. Obter uma lista de todos os servidores geridos pelo DPM através do agente do DPM.
2. Escolha um servidor específico.
3. Obter uma lista de todas as origens de dados no servidor.
4. Escolha um ou mais origens de dados e adicioná-los ao grupo de proteção

A lista de servidores em que o agente do DPM está instalado e está a ser gerido pelo servidor DPM é obtida com o [Get-DPMProductionServer](https://technet.microsoft.com/library/hh881600) cmdlet. Neste exemplo, iremos filtrar e apenas de configurar PS com nome *productionserver01* para cópia de segurança.

```
PS C:\> $server = Get-ProductionServer -DPMServerName "TestingServer" | where {($_.servername) –contains “productionserver01”}
```

Agora, obter a lista de origens de dados no ```$server``` utilizando o [Get-DPMDatasource](https://technet.microsoft.com/library/hh881605) cmdlet. Neste exemplo, está a filtrar para o volume *D:\\*  que queremos configurar para cópia de segurança. Esta origem de dados, em seguida, é adicionada ao grupo de proteção utilizando o [Add-DPMChildDatasource](https://technet.microsoft.com/library/hh881732) cmdlet. Não se esqueça de utilizar o *modificável* objeto de grupo de proteção ```$MPG``` para tornar as adições.

```
PS C:\> $DS = Get-Datasource -ProductionServer $server -Inquire | where { $_.Name -contains “D:\” }

PS C:\> Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS
```

Repita este passo quantas vezes conforme necessário, até ter adicionado todas as origens de dados escolhidas para o grupo de proteção. Também pode começar com apenas uma origem de dados e concluir o fluxo de trabalho para criar o grupo de proteção e posteriormente, adicionar mais origens de dados para o grupo de proteção.

### <a name="selecting-the-data-protection-method"></a>Selecionar o método de proteção de dados
Depois das origens de dados tiverem sido adicionadas ao grupo de proteção, a próxima etapa é especificar o método de proteção utilizando o [Set-DPMProtectionType](https://technet.microsoft.com/library/hh881725) cmdlet. Neste exemplo, o grupo de proteção é a configuração de disco local e a cópia de segurança na cloud. Também tem de especificar a origem de dados que pretende proteger para a cloud com o [Add-DPMChildDatasource](https://technet.microsoft.com/library/hh881732.aspx) cmdlet com - sinalizador Online.

```
PS C:\> Set-DPMProtectionType -ProtectionGroup $MPG -ShortTerm Disk –LongTerm Online
PS C:\> Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS –Online
```

### <a name="setting-the-retention-range"></a>Definir o período de retenção
Definir o período de retenção para a cópia de segurança de pontos de utilizar o [Set-DPMPolicyObjective](https://technet.microsoft.com/library/hh881762) cmdlet. Enquanto pode parecer estranho definir o período de retenção, antes da agenda de cópia de segurança tiver sido definida, usando o ```Set-DPMPolicyObjective``` cmdlet define automaticamente uma agenda de cópia de segurança padrão que pode ser modificada. É sempre possível conjunto a cópia de segurança agendar primeiro e depois da política de retenção.

No exemplo abaixo, o cmdlet define os parâmetros de retenção para cópias de segurança do disco. Isto irá reter cópias de segurança para 10 dias e sincronizar dados entre o servidor de produção e o servidor do DPM a cada 6 horas. O ```SynchronizationFrequencyMinutes``` não define a frequência com que um ponto de cópia de segurança é criado, mas como muitas vezes, os dados são copiados para o servidor do DPM.  Esta definição impede que as cópias de segurança se torne demasiado grande.

```
PS C:\> Set-DPMPolicyObjective –ProtectionGroup $MPG -RetentionRangeInDays 10 -SynchronizationFrequencyMinutes 360
```

Para cópias de segurança vai para o Azure (DPM refere-se a eles como cópias de segurança Online) os intervalos de retenção podem ser configurados para [longo prazo retenção usando um esquema de avô-pai-filho (GFS)](backup-azure-backup-cloud-as-tape.md). Ou seja, pode definir uma política de retenção combinado que envolvem diária, semanal, mensal e anual políticas de retenção. Neste exemplo, criamos uma matriz que representa o esquema de retenção complexas que queremos e, em seguida, configure o intervalo de retenção utilizando o [Set-DPMPolicyObjective](https://technet.microsoft.com/library/hh881762) cmdlet.

```
PS C:\> $RRlist = @()
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 180, Days)
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 104, Weeks)
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 60, Month)
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 10, Years)
PS C:\> Set-DPMPolicyObjective –ProtectionGroup $MPG -OnlineRetentionRangeList $RRlist
```

### <a name="set-the-backup-schedule"></a>Defina a agenda de cópia de segurança
O DPM define uma agenda de cópia de segurança predefinida automaticamente se especificar o objetivo de proteção utilizando o ```Set-DPMPolicyObjective``` cmdlet. Para alterar as agendas predefinidas, utilize o [Get-DPMPolicySchedule](https://technet.microsoft.com/library/hh881749) seguido de cmdlet a [conjunto DPMPolicySchedule](https://technet.microsoft.com/library/hh881723) cmdlet.

```
PS C:\> $onlineSch = Get-DPMPolicySchedule -ProtectionGroup $mpg -LongTerm Online
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[0] -TimesOfDay 02:00
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[1] -TimesOfDay 02:00 -DaysOfWeek Sa,Su –Interval 1
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[2] -TimesOfDay 02:00 -RelativeIntervals First,Third –DaysOfWeek Sa
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[3] -TimesOfDay 02:00 -DaysOfMonth 2,5,8,9 -Months Jan,Jul
PS C:\> Set-DPMProtectionGroup -ProtectionGroup $MPG
```

No exemplo acima, ```$onlineSch``` é uma matriz com quatro elementos que contém a agenda de proteção online existente para o grupo de proteção no esquema de GFS:

1. ```$onlineSch[0]``` contém a agenda diária
2. ```$onlineSch[1]``` contém a agenda semanal
3. ```$onlineSch[2]``` contém a agenda mensal
4. ```$onlineSch[3]``` contém a agenda anual

Portanto, se precisar de modificar a agenda semanal, precisa fazer referência a ```$onlineSch[1]```.

### <a name="initial-backup"></a>Cópia de segurança inicial
Quando cópia de segurança de uma origem de dados pela primeira vez, o DPM tem de cria réplica inicial de que cria uma cópia completa da origem de dados protegidos no volume de réplica do DPM. Esta atividade podem ser agendada para uma hora específica ou pode ser acionada manualmente, utilizando o [Set-DPMReplicaCreationMethod](https://technet.microsoft.com/library/hh881715) cmdlet com o parâmetro ```-NOW```.

```
PS C:\> Set-DPMReplicaCreationMethod -ProtectionGroup $MPG -NOW
```
### <a name="changing-the-size-of-dpm-replica--recovery-point-volume"></a>Alterar o tamanho da réplica do DPM e o volume de pontos de recuperação
Também pode alterar o tamanho do volume de réplica do DPM e a utilização de volume de cópia de sombra [Set-DPMDatasourceDiskAllocation](https://technet.microsoft.com/library/hh881618.aspx) cmdlet como no exemplo seguinte: Get-DatasourceDiskAllocation - Datasource $DS Set-DatasourceDiskAllocation - Datasource $DS - ProtectionGroup $MPG-manual - ReplicaArea (2 gb) - ShadowCopyArea (2 gb)

### <a name="committing-the-changes-to-the-protection-group"></a>A consolidar as alterações ao grupo de proteção
Por fim, as alterações precisam ser atribuídos antes do DPM pode efetuar a cópia de segurança pela nova configuração do grupo de proteção. Isso pode ser obtido com o [Set-DPMProtectionGroup](https://technet.microsoft.com/library/hh881758) cmdlet.

```
PS C:\> Set-DPMProtectionGroup -ProtectionGroup $MPG
```
## <a name="view-the-backup-points"></a>Ver os pontos de cópia de segurança
Pode utilizar o [Get-DPMRecoveryPoint](https://technet.microsoft.com/library/hh881746) cmdlet para obter uma lista de todos os pontos de recuperação para uma origem de dados. Neste exemplo, iremos:

* obter todos os PGs no servidor DPM e armazenadas numa matriz ```$PG```
* obter as origens de dados correspondente para o ```$PG[0]```
* obter todos os pontos de recuperação para uma origem de dados.

```
PS C:\> $PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
PS C:\> $DS = Get-DPMDatasource -ProtectionGroup $PG[0]
PS C:\> $RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online
```

## <a name="restore-data-protected-on-azure"></a>Restaurar os dados protegidos no Azure
Restauro de dados são uma combinação de um ```RecoverableItem``` objeto e um ```RecoveryOption``` objeto. Na secção anterior, temos uma lista dos pontos de cópia de segurança para uma origem de dados.

No exemplo abaixo, demonstramos como restaurar uma máquina virtual de Hyper-V a partir de cópia de segurança do Azure através da combinação de pontos de cópia de segurança com o destino de recuperação. Este exemplo inclui:

* Criação de uma opção de recuperação utilizando a [New-DPMRecoveryOption](https://technet.microsoft.com/library/hh881592) cmdlet.
* A obter a matriz de pontos de cópia de segurança usando o ```Get-DPMRecoveryPoint``` cmdlet.
* Escolher um ponto de cópia de segurança para restaurar a partir de.

```
PS C:\> $RecoveryOption = New-DPMRecoveryOption -HyperVDatasource -TargetServer "HVDCenter02" -RecoveryLocation AlternateHyperVServer -RecoveryType Recover -TargetLocation “C:\VMRecovery”

PS C:\> $PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
PS C:\> $DS = Get-DPMDatasource -ProtectionGroup $PG[0]
PS C:\> $RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online

PS C:\> Restore-DPMRecoverableItem -RecoverableItem $RecoveryPoints[0] -RecoveryOption $RecoveryOption
```

Os comandos podem ser facilmente estendidos para qualquer tipo de origem de dados.

## <a name="next-steps"></a>Passos Seguintes
* Para obter mais informações sobre o DPM para cópia de segurança do Azure, consulte [introdução à cópia de segurança do DPM](backup-azure-dpm-introduction.md)
