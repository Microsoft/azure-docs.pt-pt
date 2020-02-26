---
title: Use powerShell para fazer o backup cargas de trabalho do DPM
description: Saiba como implementar e gerir o Backup Azure para o Gestor de Proteção de Dados (DPM) usando o PowerShell
ms.topic: conceptual
ms.date: 01/23/2017
ms.openlocfilehash: cd735406a19ca1e03f520f75a7d2f39322725b8d
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77583151"
---
# <a name="deploy-and-manage-backup-to-azure-for-data-protection-manager-dpm-servers-using-powershell"></a>Implementar e gerir cópias de segurança para o Azure em servidores do Data Protection Manager (DPM) com o PowerShell

Este artigo mostra-lhe como usar o PowerShell para configurar o Azure Backup num servidor DPM e gerir a cópia de segurança e a recuperação.

## <a name="setting-up-the-powershell-environment"></a>Configuração do ambiente PowerShell

Antes de poder utilizar o PowerShell para gerir cópias de segurança do Gestor de Proteção de Dados para o Azure, é necessário ter o ambiente certo no PowerShell. No início da sessão PowerShell, certifique-se de que executa o seguinte comando para importar os módulos certos e permitir-lhe fazer referência correta aos cmdlets DPM:

```powershell
& "C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin\DpmCliInitScript.ps1"
```

```Output
Welcome to the DPM Management Shell!

Full list of cmdlets: Get-Command
Only DPM cmdlets: Get-DPMCommand
Get general help: help
Get help for a cmdlet: help <cmdlet-name> or <cmdlet-name> -?
Get definition of a cmdlet: Get-Command <cmdlet-name> -Syntax
Sample DPM scripts: Get-DPMSampleScript
```

## <a name="setup-and-registration"></a>Configuração e Registo

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para começar, [baixe o mais recente Azure PowerShell.](/powershell/azure/install-az-ps)

As seguintes tarefas de configuração e registo podem ser automatizadas com a PowerShell:

* Criar um cofre dos Serviços de Recuperação
* Instalação do agente de backup Azure
* Registo no serviço de backup Azure
* Definições de rede
* Definições de encriptação

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos serviços de recuperação

Os seguintes passos levam-no através da criação de um cofre dos Serviços de Recuperação. Um cofre dos Serviços de Recuperação é diferente de um cofre de reserva.

1. Se estiver a utilizar o Azure Backup pela primeira vez, deve utilizar o **cmdlet Register-AzResourceProvider** para registar o prestador do Serviço de Recuperação Azure com a sua subscrição.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

2. O cofre dos Serviços de Recuperação é um recurso ARM, por isso é necessário colocá-lo dentro de um Grupo de Recursos. Você pode usar um grupo de recursos existente, ou criar um novo. Ao criar um novo grupo de recursos, especifique o nome e a localização do grupo de recursos.

    ```powershell
    New-AzResourceGroup –Name "test-rg" –Location "West US"
    ```

3. Utilize o **cmdlet New-AzRecoveryServicesVault** para criar um novo cofre. Certifique-se de especificar a mesma localização para o cofre que foi usado para o grupo de recursos.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "West US"
    ```

4. Especificar o tipo de redundância de armazenamento a utilizar; pode utilizar [armazenamento redundante localmente (LRS)](../storage/common/storage-redundancy-lrs.md) ou [Armazenamento Geo Redundante (GRS)](../storage/common/storage-redundancy-grs.md). O exemplo seguinte mostra a opção -BackupStorageRedundancy para testeVault está definida para GeoRedundant.

   > [!TIP]
   > Muitos cmdlets do Azure Backup requerem o objeto do cofre dos Serviços de Recuperação como entrada. Por este motivo, é conveniente armazenar o objeto do cofre dos Serviços de Recuperação do Backup numa variável.
   >
   >

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault –Name "testVault"
    Set-AzRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>Veja os cofres em uma subscrição

Utilize o **Get-AzRecoveryServicesVault** para ver a lista de todos os cofres da subscrição atual. Você pode usar este comando para verificar se um novo cofre foi criado, ou para ver que cofres estão disponíveis na subscrição.

Executar o comando, Get-AzRecoveryServicesVault, e todos os cofres da subscrição estão listados.

```powershell
Get-AzRecoveryServicesVault
```

```Output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

## <a name="installing-the-azure-backup-agent-on-a-dpm-server"></a>Instalação do agente de backup Azure num servidor DPM

Antes de instalar o agente De reserva Azure, é necessário que o instalador seja descarregado e presente no Windows Server. Pode obter a versão mais recente do instalador a partir do [Microsoft Download Center](https://aka.ms/azurebackup_agent) ou da página do Painel de Instrumentos de Recuperação dos Serviços de Recuperação. Guarde o instalador para um local facilmente acessível, como *C:\Downloads\*.

Para instalar o agente, execute o seguinte comando numa consola PowerShell elevada **no servidor DPM:**

```powershell
MARSAgentInstaller.exe /q
```

Isto instala o agente com todas as opções predefinidas. A instalação demora alguns minutos em segundo plano. Se não especificar a opção */nu,* a janela **Windows Update** abre no final da instalação para verificar se há novidades.

O agente aparece na lista de programas instalados. Para ver a lista de programas instalados, vá ao Painel de **Controlo** > **Programas** > Programas **e Funcionalidades**.

![Agente instalado](./media/backup-dpm-automation/installed-agent-listing.png)

### <a name="installation-options"></a>Opções de instalação

Para ver todas as opções disponíveis através da linha de comando, utilize o seguinte comando:

```powershell
MARSAgentInstaller.exe /?
```

As opções disponíveis incluem:

| Opção | Detalhes | Predefinição |
| --- | --- | --- |
| /q |Instalação tranquila |- |
| /p:"location" |Caminho para a pasta de instalação para o agente de reserva Azure. |C:\Program Files\Microsoft Azure Recovery Services Agent |
| /s:"location" |Caminho para a pasta cache para o agente de backup Azure. |C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch |
| /m |Opt-in para microsoft update |- |
| /nu |Não verifique se há atualizações após a instalação estar concluída |- |
| /d |Desinstala o Agente de Serviços de Recuperação do Microsoft Azure |- |
| /ph |Endereço de anfitrião proxy |- |
| /po |Número de porta de anfitrião proxy |- |
| /pu |Nome de utilizador do anfitrião proxy |- |
| /pw |Palavra-Passe do Proxy |- |

## <a name="registering-dpm-to-a-recovery-services-vault"></a>Registar DPM num cofre de serviços de recuperação

Depois de criar o cofre dos Serviços de Recuperação, descarregue as credenciais mais recentes do agente e do cofre e guarde-o num local conveniente como C:\Downloads.

```powershell
$credspath = "C:\downloads"
$credsfilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $vault1 -Path  $credspath
$credsfilename
```

```Output
C:\downloads\testvault\_Sun Apr 10 2016.VaultCredentials
```

No servidor DPM, execute o cmdlet [Start-OBRegistration](https://docs.microsoft.com/powershell/module/msonlinebackup/start-obregistration?view=winserver2012-ps) para registar a máquina com o cofre.

```powershell
$cred = $credspath + $credsfilename
Start-OBRegistration-VaultCredentials $cred -Confirm:$false
```

```Output
CertThumbprint      :7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName: testvault
Region              :West US
Machine registration succeeded.
```

### <a name="initial-configuration-settings"></a>Definições de configuração inicial

Uma vez registado o Servidor DPM com o cofre dos Serviços de Recuperação, começa com definições de subscrição predefinidas. Estas definições de subscrição incluem Networking, Encriptação e a área de encenação. Para alterar as definições de subscrição, é necessário obter primeiro uma pega nas definições existentes (predefinidos) utilizando o cmdlet [Get-DPMCloudSubscriptionSetting:](https://docs.microsoft.com/powershell/module/dataprotectionmanager/get-dpmcloudsubscriptionsetting?view=systemcenter-ps-2019)

```powershell
$setting = Get-DPMCloudSubscriptionSetting -DPMServerName "TestingServer"
```

Todas as modificações são feitas a este objeto local PowerShell ```$setting``` e, em seguida, o objeto completo está comprometido com dPM e Azure Backup para salvá-los usando o [cmdlet Set-DPMCloudSubscriptionSetting.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmcloudsubscriptionsetting?view=systemcenter-ps-2019) É necessário utilizar a bandeira ```–Commit``` para garantir que as alterações sejam persistidas. As definições não serão aplicadas e utilizadas pela Azure Backup a menos que sejam comprometidas.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="networking"></a>Redes

Se a conectividade da máquina DPM para o serviço de backup Azure na internet for através de um servidor proxy, então as definições do servidor proxy devem ser fornecidas para backups bem sucedidos. Isto é feito utilizando os parâmetros ```-ProxyServer```e ```-ProxyPort```, ```-ProxyUsername``` e ```ProxyPassword``` com o [cmdlet Set-DPMCloudSubscriptionSetting.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmcloudsubscriptionsetting?view=systemcenter-ps-2019) Neste exemplo, não existe um servidor proxy, pelo que estamos explicitamente a limpar qualquer informação relacionada com procuração.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoProxy
```

O uso da largura de banda também pode ser controlado com opções de ```-WorkHourBandwidth``` e ```-NonWorkHourBandwidth``` para um determinado conjunto de dias da semana. Neste exemplo, não estamos a estabelecer qualquer estrangulamento.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoThrottle
```

## <a name="configuring-the-staging-area"></a>Configurar a área de preparação

O agente de backup Azure que funciona no servidor DPM precisa de armazenamento temporário para dados restaurados a partir da nuvem (área de paragem local). Configure a área de preparação utilizando o [cmdlet set-DPMCloudSubscriptionSetting](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmcloudsubscriptionsetting?view=systemcenter-ps-2019) e o parâmetro ```-StagingAreaPath```.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -StagingAreaPath "C:\StagingArea"
```

No exemplo acima, a área de paragem será definida para *C:\StagingArea* no objeto PowerShell ```$setting```. Certifique-se de que a pasta especificada já existe, ou então o compromisso final das definições de subscrição falhará.

### <a name="encryption-settings"></a>Definições de encriptação

Os dados de backup enviados para o Azure Backup são encriptados para proteger a confidencialidade dos dados. A frase-passe de encriptação é a "palavra-passe" para desencriptar os dados no momento da restauração. É importante manter esta informação segura e segura uma vez definida.

No exemplo abaixo, o primeiro comando converte a cadeia ```passphrase123456789``` a uma corda segura e atribui a corda segura à variável denominada ```$Passphrase```. o segundo comando define a cadeia segura em ```$Passphrase``` como a palavra-passe para encriptar cópias de segurança.

```powershell
$Passphrase = ConvertTo-SecureString -string "passphrase123456789" -AsPlainText -Force

Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -EncryptionPassphrase $Passphrase
```

> [!IMPORTANT]
> Mantenha as informações de palavra-passe seguras e seguras uma vez definidas. Não poderá restaurar os dados do Azure sem esta frase-passe.
>
>

Neste ponto, deveria ter feito todas as alterações necessárias ao objeto ```$setting```. Lembre-se de comprometer as mudanças.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="protect-data-to-azure-backup"></a>Proteja os dados para a Cópia de Segurança Azure

Nesta secção, você adicionará um servidor de produção ao DPM e, em seguida, protegerá os dados para armazenamento local de DPM e, em seguida, para Azure Backup. Nos exemplos, vamos demonstrar como fazer o backup de ficheiros e pastas. A lógica pode ser facilmente estendida para fazer backup qualquer fonte de dados apoiada pelo DPM. Todos os seus backups DPM são regidos por um Grupo de Proteção (PG) com quatro partes:

1. **Os membros** do grupo são uma lista de todos os objetos protegidos (também conhecidos como *Datasources* em DPM) que pretende proteger no mesmo grupo de proteção. Por exemplo, pode querer proteger os VMs de produção num grupo de proteção e bases de dados do SQL Server noutro grupo de proteção, uma vez que podem ter diferentes requisitos de backup. Antes de poder fazer o backback de qualquer fonte de dados num servidor de produção, tem de se certificar de que o Agente DPM está instalado no servidor e é gerido pelo DPM. Siga os passos para [instalar o Agente DPM](https://docs.microsoft.com/previous-versions/system-center/data-protection-manager-2007/bb870935(v=technet.10)) e ligue-o ao servidor DPM apropriado.
2. O método de **proteção de dados** especifica as localizações de backup do alvo - fita, disco e nuvem. No nosso exemplo, protegeremos os dados para o disco local e para a nuvem.
3. Um **calendário de cópia** de segurança que especifica quando as cópias de segurança precisam de ser tomadas e com que frequência os dados devem ser sincronizados entre o Servidor DPM e o servidor de produção.
4. Um calendário de **retenção** que especifica quanto tempo para manter os pontos de recuperação em Azure.

### <a name="creating-a-protection-group"></a>Criação de um grupo de proteção

Comece por criar um novo Grupo de Proteção utilizando o [cmdlet New-DPMProtectionGroup.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/new-dpmprotectiongroup?view=systemcenter-ps-2019)

```powershell
$PG = New-DPMProtectionGroup -DPMServerName " TestingServer " -Name "ProtectGroup01"
```

O cmdlet acima criará um Grupo de Proteção chamado *ProtectGroup01*. Um grupo de proteção existente também pode ser modificado mais tarde para adicionar backup à nuvem Azure. No entanto, para fazer quaisquer alterações ao Grupo de Proteção - novas ou existentes - precisamos de obter uma pega num objeto *modificável* utilizando o cmdlet [Get-DPMModifiableProtectionGroup.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/get-dpmmodifiableprotectiongroup?view=systemcenter-ps-2019)

```powershell
$MPG = Get-ModifiableProtectionGroup $PG
```

### <a name="adding-group-members-to-the-protection-group"></a>Adicionar membros do grupo ao Grupo de Proteção

Cada Agente DPM conhece a lista de fontes de dados no servidor em que está instalado. Para adicionar uma fonte de dados ao Grupo de Proteção, o Agente DPM precisa primeiro enviar uma lista das fontes de dados de volta para o servidor DPM. Uma ou mais fontes de dados são selecionadas e adicionadas ao Grupo de Proteção. Os passos powerShell necessários para o conseguir são:

1. Pegue uma lista de todos os servidores geridos pela DPM através do Agente DPM.
2. Escolha um servidor específico.
3. Pegue uma lista de todas as fontes de dados no servidor.
4. Escolha uma ou mais fontes de dados e adicione-as ao Grupo de Proteção

A lista de servidores em que o Agente DPM está instalado e está a ser gerida pelo Servidor DPM é adquirida com o [cmdlet Get-DPMProductionServer.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/get-dpmproductionserver?view=systemcenter-ps-2019) Neste exemplo vamos filtrar e apenas configurar PS com nome *productionserver01* para backup.

```powershell
$server = Get-ProductionServer -DPMServerName "TestingServer" | Where-Object {($_.servername) –contains "productionserver01"}
```

Agora, obtenha a lista de fontes de dados em ```$server``` utilizando o cmdlet [Get-DPMDatasource.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/get-dpmdatasource?view=systemcenter-ps-2019) Neste exemplo estamos a filtrar o volume *D:\\* que queremos configurar para cópia de segurança. Esta fonte de dados é adicionada ao Grupo de Proteção utilizando o cmdlet [Add-DPMChildDatasource.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/add-dpmchilddatasource?view=systemcenter-ps-2019) Lembre-se de usar o objeto de grupo de proteção *modificável* ```$MPG``` para fazer as adições.

```powershell
$DS = Get-Datasource -ProductionServer $server -Inquire | Where-Object { $_.Name -contains "D:\" }

Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS
```

Repita este passo quantas vezes for em função do necessário, até que tenha adicionado todas as fontes de dados escolhidas ao grupo de proteção. Também pode começar com apenas uma fonte de dados, e completar o fluxo de trabalho para a criação do Grupo de Proteção, e em um ponto posterior adicionar mais fontes de dados ao Grupo de Proteção.

### <a name="selecting-the-data-protection-method"></a>Seleção do método de proteção de dados

Uma vez adicionadas as fontes de dados ao Grupo de Proteção, o próximo passo é especificar o método de proteção utilizando o cmdlet [set-DPMProtectionType.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmprotectiontype?view=systemcenter-ps-2019) Neste exemplo, o Grupo de Proteção está configurado para o disco local e cópia de segurança em nuvem. Também precisa especificar a fonte de dados que pretende proteger para cloud utilizando o cmdlet [Add-DPMChildDatasource](https://docs.microsoft.com/powershell/module/dataprotectionmanager/add-dpmchilddatasource?view=systemcenter-ps-2019) com a bandeira -Online.

```powershell
Set-DPMProtectionType -ProtectionGroup $MPG -ShortTerm Disk –LongTerm Online
Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS –Online
```

### <a name="setting-the-retention-range"></a>Definição da gama de retenção

Detete a retenção para os pontos de backup utilizando o cmdlet [Set-DPMPolicyObject.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmpolicyobjective?view=systemcenter-ps-2019) Embora possa parecer estranho definir a retenção antes de o calendário de cópia seletiva ter sido definido, o uso do ```Set-DPMPolicyObjective``` cmdlet define automaticamente um calendário de cópia de segurança predefinido que pode ser modificado. É sempre possível definir o calendário de backup primeiro e a política de retenção depois.

No exemplo abaixo, o cmdlet define os parâmetros de retenção para cópias de segurança do disco. Isto irá reter cópias de segurança durante 10 dias, e sincronizar dados a cada 6 horas entre o servidor de produção e o servidor DPM. O ```SynchronizationFrequencyMinutes``` não define com que frequência é criado um ponto de backup, mas com que frequência os dados são copiados para o servidor DPM.  Esta definição impede que os backups se tornem demasiado grandes.

```powershell
Set-DPMPolicyObjective –ProtectionGroup $MPG -RetentionRangeInDays 10 -SynchronizationFrequencyMinutes 360
```

Para cópias de segurança que vão para o Azure (DPM refere-se a eles como backups online) as gamas de retenção podem ser configuradas para retenção a [longo prazo usando um esquema avô-pai-filho (GFS)](backup-azure-backup-cloud-as-tape.md). Ou seja, pode definir uma política de retenção combinada que envolva políticas de retenção diárias, semanais, mensais e anuais. Neste exemplo, criamos um conjunto que representa o complexo esquema de retenção que queremos e, em seguida, configuramos a gama de retenção utilizando o [cmdlet Set-DPMPolicyObjective.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmpolicyobjective?view=systemcenter-ps-2019)

```powershell
$RRlist = @()
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 180, Days)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 104, Weeks)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 60, Month)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 10, Years)
Set-DPMPolicyObjective –ProtectionGroup $MPG -OnlineRetentionRangeList $RRlist
```

### <a name="set-the-backup-schedule"></a>Definir o horário de backup

O DPM define automaticamente um calendário de cópia de segurança predefinido se especificar o objetivo de proteção utilizando o ```Set-DPMPolicyObjective``` cmdlet. Para alterar os horários predefinidos, utilize o cmdlet [Get-DPMPolicySchedule,](https://docs.microsoft.com/powershell/module/dataprotectionmanager/get-dpmpolicyschedule?view=systemcenter-ps-2019) seguido do [cmdlet Set-DPMPolicySchedule.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmpolicyschedule?view=systemcenter-ps-2019)

```powershell
$onlineSch = Get-DPMPolicySchedule -ProtectionGroup $mpg -LongTerm Online
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[0] -TimesOfDay 02:00
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[1] -TimesOfDay 02:00 -DaysOfWeek Sa,Su –Interval 1
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[2] -TimesOfDay 02:00 -RelativeIntervals First,Third –DaysOfWeek Sa
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[3] -TimesOfDay 02:00 -DaysOfMonth 2,5,8,9 -Months Jan,Jul
Set-DPMProtectionGroup -ProtectionGroup $MPG
```

No exemplo acima, ```$onlineSch``` é um conjunto com quatro elementos que contém o calendário de proteção online existente para o Grupo de Proteção no regime GFS:

1. ```$onlineSch[0]``` contém o horário diário
2. ```$onlineSch[1]``` contém o horário semanal
3. ```$onlineSch[2]``` contém o horário mensal
4. ```$onlineSch[3]``` contém o horário anual

Por isso, se precisar de modificar o horário semanal, tem de se referir ao ```$onlineSch[1]```.

### <a name="initial-backup"></a>Cópia de segurança inicial

Ao fazer backup de uma fonte de dados pela primeira vez, o DPM precisa de criar uma réplica inicial que cria uma cópia completa da fonte de dados a proteger no volume de réplica spm dPM. Esta atividade pode ser programada para um determinado tempo, ou pode ser acionada manualmente, utilizando o [cmdlet Set-DPMReplicaCreationMethod](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmreplicacreationmethod?view=systemcenter-ps-2019) com o parâmetro ```-NOW```.

```powershell
Set-DPMReplicaCreationMethod -ProtectionGroup $MPG -NOW
```

### <a name="changing-the-size-of-dpm-replica--recovery-point-volume"></a>Alteração do tamanho do volume de réplica e ponto de recuperação dPM

Também pode alterar o tamanho do volume de réplica dPM e volume de cópia de sombra usando [o set-DPMDatasourceDiskDiskAllocation](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmdatasourcediskallocation?view=systemcenter-ps-2019) cmdlet como no exemplo seguinte: Get-DatasourceDiskAllocation -Datasource $DS Set-DatasourceDiskAllocation -Datasource $DS -ProtectionGroup $MPG -manual -ReplicaArea (2gb) -ShadowCopyArea (2gb)

### <a name="committing-the-changes-to-the-protection-group"></a>Comprometer as alterações ao Grupo de Proteção

Finalmente, as alterações têm de ser cometidas antes que o DPM possa obter a cópia de segurança de acordo com a configuração do novo Grupo de Proteção. Isto pode ser conseguido utilizando o cmdlet [Set-DPMProtectionGroup.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmprotectiongroup?view=systemcenter-ps-2019)

```powershell
Set-DPMProtectionGroup -ProtectionGroup $MPG
```

## <a name="view-the-backup-points"></a>Ver os pontos de backup

Pode utilizar o cmdlet [Get-DPMRecoveryPoint](https://docs.microsoft.com/powershell/module/dataprotectionmanager/get-dpmrecoverypoint?view=systemcenter-ps-2019) para obter uma lista de todos os pontos de recuperação para uma fonte de dados. Neste exemplo, iremos:

* buscar todos os PGs no servidor DPM e armazenados em uma matriz ```$PG```
* obter as fontes de dados correspondentes ao ```$PG[0]```
* obter todos os pontos de recuperação para uma fonte de dados.

```powershell
$PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
$DS = Get-DPMDatasource -ProtectionGroup $PG[0]
$RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online
```

## <a name="restore-data-protected-on-azure"></a>Restaurar dados protegidos em Azure

Restaurar dados é uma combinação de um objeto ```RecoverableItem``` e um objeto ```RecoveryOption```. Na secção anterior, temos uma lista dos pontos de backup para uma fonte de dados.

No exemplo abaixo, demonstramos como restaurar uma máquina virtual Hyper-V do Azure Backup, combinando pontos de backup com o alvo para a recuperação. Este exemplo inclui:

* Criar uma opção de recuperação utilizando o cmdlet [New-DPMRecoveryOption.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/new-dpmrecoveryoption?view=systemcenter-ps-2019)
* Buscar a gama de pontos de reserva utilizando o ```Get-DPMRecoveryPoint``` cmdlet.
* Escolher um ponto de apoio para restaurar.

```powershell
$RecoveryOption = New-DPMRecoveryOption -HyperVDatasource -TargetServer "HVDCenter02" -RecoveryLocation AlternateHyperVServer -RecoveryType Recover -TargetLocation "C:\VMRecovery"

$PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
$DS = Get-DPMDatasource -ProtectionGroup $PG[0]
$RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online

Restore-DPMRecoverableItem -RecoverableItem $RecoveryPoints[0] -RecoveryOption $RecoveryOption
```

Os comandos podem ser facilmente estendidos para qualquer tipo de fonte de dados.

## <a name="next-steps"></a>Passos seguintes

* Para mais informações sobre dPM para Backup Azure ver [Introdução ao DPM Backup](backup-azure-dpm-introduction.md)
