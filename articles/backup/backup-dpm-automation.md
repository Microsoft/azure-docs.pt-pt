---
title: Backup do Azure-usar o PowerShell para fazer backup de cargas de trabalho do DPM
description: Saiba como implantar e gerenciar o backup do Azure para Data Protection Manager (DPM) usando o PowerShell
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 1/23/2017
ms.author: adigan
ms.openlocfilehash: a269db3d97f23c16e848026ce78fc04c7a1182e8
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465139"
---
# <a name="deploy-and-manage-backup-to-azure-for-data-protection-manager-dpm-servers-using-powershell"></a>Implementar e gerir cópias de segurança para o Azure em servidores do Data Protection Manager (DPM) com o PowerShell

Este artigo mostra como usar o PowerShell para configurar o backup do Azure em um servidor DPM e para gerenciar o backup e a recuperação.

## <a name="setting-up-the-powershell-environment"></a>Configurando o ambiente do PowerShell

Antes de poder usar o PowerShell para gerenciar backups de Data Protection Manager para o Azure, você precisa ter o ambiente certo no PowerShell. No início da sessão do PowerShell, certifique-se de executar o seguinte comando para importar os módulos corretos e permitir que você referencie corretamente os cmdlets do DPM:

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

## <a name="setup-and-registration"></a>Instalação e registro

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para começar, [Baixe o Azure PowerShell mais recente](/powershell/azure/install-az-ps).

As seguintes tarefas de configuração e registro podem ser automatizadas com o PowerShell:

* Criar um cofre dos Serviços de Recuperação
* Instalando o agente de backup do Azure
* Registrando com o serviço de backup do Azure
* Configurações de rede
* Definições de encriptação

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos serviços de recuperação

As etapas a seguir orientarão você na criação de um cofre dos serviços de recuperação. Um cofre dos serviços de recuperação é diferente de um cofre de backup.

1. Se você estiver usando o backup do Azure pela primeira vez, deverá usar o cmdlet **Register-AzResourceProvider** para registrar o provedor de serviços de recuperação do Azure com sua assinatura.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

2. O cofre dos serviços de recuperação é um recurso do ARM, portanto, você precisa colocá-lo em um grupo de recursos. Você pode usar um grupo de recursos existente ou criar um novo. Ao criar um novo grupo de recursos, especifique o nome e o local do grupo de recursos.

    ```powershell
    New-AzResourceGroup –Name "test-rg" –Location "West US"
    ```

3. Use o cmdlet **New-AzRecoveryServicesVault** para criar um novo cofre. Certifique-se de especificar o mesmo local para o cofre que foi usado para o grupo de recursos.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "West US"
    ```

4. Especifique o tipo de redundância de armazenamento a ser usado; Você pode usar o [LRS (armazenamento com redundância local)](../storage/common/storage-redundancy-lrs.md) ou o [grs (armazenamento com redundância geográfica)](../storage/common/storage-redundancy-grs.md). O exemplo a seguir mostra a opção-BackupStorageRedundancy para testVault é definida como georedundante.

   > [!TIP]
   > Muitos cmdlets do Azure Backup requerem o objeto do cofre dos Serviços de Recuperação como entrada. Por este motivo, é conveniente armazenar o objeto do cofre dos Serviços de Recuperação do Backup numa variável.
   >
   >

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault –Name "testVault"
    Set-AzRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>Exibir os cofres em uma assinatura

Use **Get-AzRecoveryServicesVault** para exibir a lista de todos os cofres na assinatura atual. Você pode usar esse comando para verificar se um novo cofre foi criado ou para ver quais cofres estão disponíveis na assinatura.

Execute o comando, Get-AzRecoveryServicesVault e todos os cofres na assinatura são listados.

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


## <a name="installing-the-azure-backup-agent-on-a-dpm-server"></a>Instalando o agente de backup do Azure em um servidor DPM

Antes de instalar o agente de backup do Azure, você precisa ter o instalador baixado e presente no Windows Server. Você pode obter a versão mais recente do instalador no [centro de download da Microsoft](https://aka.ms/azurebackup_agent) ou na página do painel do cofre dos serviços de recuperação. Salve o instalador em um local facilmente acessível, como *\*C:\Downloads.

Para instalar o agente, execute o seguinte comando em um console do PowerShell **com privilégios elevados no servidor DPM**:

```powershell
MARSAgentInstaller.exe /q
```

Isso instala o agente com todas as opções padrão. A instalação leva alguns minutos em segundo plano. Se você não especificar a opção */nu* , a janela de **Windows Update** será aberta no final da instalação para verificar se há atualizações.

O agente aparece na lista de programas instalados. Para ver a lista de programas instalados, vá para **painel** > de controle**programas** > programas**e recursos**.

![Agente instalado](./media/backup-dpm-automation/installed-agent-listing.png)

### <a name="installation-options"></a>Opções de instalação

Para ver todas as opções disponíveis por meio da linha de comando, use o seguinte comando:

```powershell
MARSAgentInstaller.exe /?
```

As opções disponíveis incluem:

| Opção | Detalhes | Predefinição |
| --- | --- | --- |
| /q |Instalação silenciosa |- |
| /p:"location" |Caminho para a pasta de instalação do agente de backup do Azure. |C:\Arquivos de Programas\microsoft Azure Recovery Services Agent |
| /s:"location" |Caminho para a pasta de cache do agente de backup do Azure. |C:\Arquivos de Programas\microsoft Azure Recovery Services Agent\Scratch |
| /m |Aceitar para Microsoft Update |- |
| /nu |Não verificar se há atualizações após a conclusão da instalação |- |
| /d |Desinstala o agente de Serviços de Recuperação do Microsoft Azure |- |
| /ph |Endereço do host do proxy |- |
| /po |Número da porta do host proxy |- |
| /pu |Nome de usuário do host proxy |- |
| /pw |Senha do proxy |- |

## <a name="registering-dpm-to-a-recovery-services-vault"></a>Registrando o DPM em um cofre dos serviços de recuperação

Depois de criar o cofre dos serviços de recuperação, baixe o agente mais recente e as credenciais do cofre e armazene-os em um local conveniente, como C:\Downloads.

```powershell
$credspath = "C:\downloads"
$credsfilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $vault1 -Path  $credspath
$credsfilename
```

```Output
C:\downloads\testvault\_Sun Apr 10 2016.VaultCredentials
```

No servidor DPM, execute o cmdlet [Start-OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx) para registrar o computador no cofre.

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

Depois que o servidor DPM é registrado com o cofre dos serviços de recuperação, ele começa com as configurações de assinatura padrão. Essas configurações de assinatura incluem rede, criptografia e área de preparo. Para alterar as configurações de assinatura, você precisa primeiro obter um identificador nas configurações existentes (padrão) usando o cmdlet [Get-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612793) :

```powershell
$setting = Get-DPMCloudSubscriptionSetting -DPMServerName "TestingServer"
```

Todas as modificações são feitas nesse objeto ```$setting``` local do PowerShell e, em seguida, o objeto completo é confirmado no DPM e no backup do Azure para salvá-los usando o cmdlet [set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791) . Você precisa usar o ```–Commit``` sinalizador para garantir que as alterações sejam persistidas. As configurações não serão aplicadas e usadas pelo backup do Azure, a menos que confirmadas.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="networking"></a>Redes

Se a conectividade do computador do DPM com o serviço de backup do Azure na Internet for por meio de um servidor proxy, as configurações do servidor proxy deverão ser fornecidas para backups bem-sucedidos. Isso é feito usando o ```-ProxyServer``` ```-ProxyUsername``` e ```-ProxyPort```o e os ```ProxyPassword``` parâmetros com o cmdlet [set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791) . Neste exemplo, não há nenhum servidor proxy, portanto, estamos explicitamente limpando informações relacionadas ao proxy.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoProxy
```

O uso de largura de banda também pode ser ```-WorkHourBandwidth``` controlado ```-NonWorkHourBandwidth``` com opções de e para um determinado conjunto de dias da semana. Neste exemplo, não definimos nenhuma limitação.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoThrottle
```

## <a name="configuring-the-staging-area"></a>Configurando a área de preparação

O agente de backup do Azure em execução no servidor DPM precisa de armazenamento temporário para os dados restaurados da nuvem (área de preparo local). Configure a área de preparo usando o cmdlet [set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791) e o ```-StagingAreaPath``` parâmetro.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -StagingAreaPath "C:\StagingArea"
```

No exemplo acima, a área de preparação será definida como *C:\StagingArea* no objeto ```$setting```do PowerShell. Verifique se a pasta especificada já existe ou se a confirmação final das configurações de assinatura falhará.

### <a name="encryption-settings"></a>Definições de encriptação

Os dados de backup enviados para o backup do Azure são criptografados para proteger a confidencialidade dos dados. A senha de criptografia é a "senha" para descriptografar os dados no momento da restauração. É importante manter essas informações seguras e seguras após sua definição.

No exemplo a seguir, o primeiro comando converte a cadeia ```passphrase123456789``` de caracteres em uma cadeia de caracteres segura e atribui a cadeia de caracteres segura ```$Passphrase```à variável chamada. o segundo comando define a cadeia de caracteres ```$Passphrase``` segura como a senha para a criptografia de backups.

```powershell
$Passphrase = ConvertTo-SecureString -string "passphrase123456789" -AsPlainText -Force

Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -EncryptionPassphrase $Passphrase
```

> [!IMPORTANT]
> Mantenha as informações de senha seguras e seguras após sua definição. Você não poderá restaurar dados do Azure sem essa frase secreta.
>
>

Neste ponto, você deve ter feito todas as alterações ```$setting``` necessárias no objeto. Lembre-se de confirmar as alterações.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="protect-data-to-azure-backup"></a>Proteger dados no backup do Azure

Nesta seção, você adicionará um servidor de produção ao DPM e, em seguida, protegerá os dados para o armazenamento local do DPM e, em seguida, para o backup do Azure. Nos exemplos, demonstraremos como fazer backup de arquivos e pastas. A lógica pode ser facilmente estendida para fazer backup de qualquer fonte de dados com suporte do DPM. Todos os backups do DPM são regidos por um PG (grupo de proteção) com quatro partes:

1. **Membros do grupo** é uma lista de todos os objetos protegíveis (também conhecidos como *fontes de fonte* no DPM) que você deseja proteger no mesmo grupo de proteção. Por exemplo, talvez você queira proteger as VMs de produção em um grupo de proteção e SQL Server bancos de dados em outro grupo de proteção, pois eles podem ter requisitos de backup diferentes. Antes de fazer backup de qualquer fonte de origem em um servidor de produção, você precisa verificar se o agente do DPM está instalado no servidor e é gerenciado pelo DPM. Siga as etapas para [instalar o agente do DPM](https://technet.microsoft.com/library/bb870935.aspx) e vinculá-lo ao servidor DPM apropriado.
2. O **método de proteção de dados** especifica os locais de backup de destino-fita, disco e nuvem. Em nosso exemplo, protegeremos os dados para o disco local e para a nuvem.
3. Um **agendamento de backup** que especifica quando os backups precisam ser feitos e com que frequência os dados devem ser sincronizados entre o servidor DPM e o servidor de produção.
4. Um **agendamento de retenção** que especifica por quanto tempo os pontos de recuperação são mantidos no Azure.

### <a name="creating-a-protection-group"></a>Criando um grupo de proteção

Comece criando um novo grupo de proteção usando o cmdlet [New-DPMProtectionGroup](https://technet.microsoft.com/library/hh881722) .

```powershell
$PG = New-DPMProtectionGroup -DPMServerName " TestingServer " -Name "ProtectGroup01"
```

O cmdlet acima criará um grupo de proteção chamado *ProtectGroup01*. Um grupo de proteção existente também pode ser modificado posteriormente para adicionar backup à nuvem do Azure. No entanto, para fazer qualquer alteração no grupo de proteção – novo ou existente, precisamos obter um identificador em um  objeto modificável usando o cmdlet [Get-DPMModifiableProtectionGroup](https://technet.microsoft.com/library/hh881713) .

```powershell
$MPG = Get-ModifiableProtectionGroup $PG
```

### <a name="adding-group-members-to-the-protection-group"></a>Adicionando membros do grupo ao grupo de proteção

Cada agente do DPM sabe a lista de fontes de fonte no servidor em que ele está instalado. Para adicionar uma fonte de fontes ao grupo de proteção, o agente do DPM precisa primeiro enviar uma lista das fontes de origem de volta para o servidor DPM. Uma ou mais fontes de fonte são selecionadas e adicionadas ao grupo de proteção. As etapas do PowerShell necessárias para alcançar isso são:

1. Busque uma lista de todos os servidores gerenciados pelo DPM por meio do agente do DPM.
2. Escolha um servidor específico.
3. Busque uma lista de todas as fontes de fonte no servidor.
4. Escolher uma ou mais fontes de fonte e adicioná-las ao grupo de proteção

A lista de servidores em que o agente do DPM está instalado e que está sendo gerenciado pelo servidor DPM é adquirida com o cmdlet [Get-DPMProductionServer](https://technet.microsoft.com/library/hh881600) . Neste exemplo, vamos filtrar e configurar somente o PS com o nome *ProductionServer01* para backup.

```powershell
$server = Get-ProductionServer -DPMServerName "TestingServer" | Where-Object {($_.servername) –contains “productionserver01”}
```

Agora, busque a lista de fontes de fonte ```$server``` de código usando o cmdlet [Get-DPMDatasource](https://technet.microsoft.com/library/hh881605) . Neste exemplo, estamos filtrando para o volume *D:\\*  que desejamos configurar para backup. Essa fonte de origem é adicionada ao grupo de proteção usando o cmdlet [Add-DPMChildDatasource](https://technet.microsoft.com/library/hh881732) . Lembre-se de  usar o objeto ```$MPG``` de grupo de proteção modificável para fazer as adições.

```powershell
$DS = Get-Datasource -ProductionServer $server -Inquire | Where-Object { $_.Name -contains “D:\” }

Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS
```

Repita essa etapa quantas vezes forem necessárias, até que você tenha adicionado todas as fontes de fonte escolhidas ao grupo de proteção. Você também pode começar com apenas uma fonte de fontes e concluir o fluxo de trabalho para criar o grupo de proteção e, em um ponto posterior, adicionar mais fontes de fonte ao grupo de proteção.

### <a name="selecting-the-data-protection-method"></a>Selecionando o método de proteção de dados

Depois que as fontes de fonte tiverem sido adicionadas ao grupo de proteção, a próxima etapa será especificar o método de proteção usando o cmdlet [set-DPMProtectionType](https://technet.microsoft.com/library/hh881725) . Neste exemplo, o grupo de proteção é configurado para o backup em disco local e na nuvem. Você também precisa especificar a fonte de fontes que deseja proteger para a nuvem usando o cmdlet [Add-DPMChildDatasource](https://technet.microsoft.com/library/hh881732.aspx) com o sinalizador-online.

```powershell
Set-DPMProtectionType -ProtectionGroup $MPG -ShortTerm Disk –LongTerm Online
Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS –Online
```

### <a name="setting-the-retention-range"></a>Configurando o período de retenção

Defina a retenção para os pontos de backup usando o cmdlet [set-DPMPolicyObjective](https://technet.microsoft.com/library/hh881762) . Embora possa parecer estranho definir a retenção antes da definição do agendamento de backup, usar o ```Set-DPMPolicyObjective``` cmdlet define automaticamente um agendamento de backup padrão que pode ser modificado. Sempre é possível definir o agendamento de backup primeiro e a política de retenção após.

No exemplo a seguir, o cmdlet define os parâmetros de retenção para backups em disco. Isso manterá os backups por 10 dias e sincronizará os dados a cada 6 horas entre o servidor de produção e o servidor DPM. O ```SynchronizationFrequencyMinutes``` não define com que frequência um ponto de backup é criado, mas com que frequência os dados são copiados para o servidor DPM.  Essa configuração impede que os backups se tornem muito grandes.

```powershell
Set-DPMPolicyObjective –ProtectionGroup $MPG -RetentionRangeInDays 10 -SynchronizationFrequencyMinutes 360
```

Para backups que vão para o Azure (o DPM se refere a eles como backups online), os intervalos de retenção podem ser configurados para [retenção de longo prazo usando um GFS (esquema avô-pai-filho)](backup-azure-backup-cloud-as-tape.md). Ou seja, você pode definir uma política de retenção combinada envolvendo políticas de retenção diárias, semanais, mensais e anuais. Neste exemplo, criamos uma matriz que representa o esquema de retenção complexo que desejamos e, em seguida, configuramos o período de retenção usando o cmdlet [set-DPMPolicyObjective](https://technet.microsoft.com/library/hh881762) .

```powershell
$RRlist = @()
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 180, Days)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 104, Weeks)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 60, Month)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 10, Years)
Set-DPMPolicyObjective –ProtectionGroup $MPG -OnlineRetentionRangeList $RRlist
```

### <a name="set-the-backup-schedule"></a>Definir o agendamento de backup

O DPM definirá um agendamento de backup padrão automaticamente se você especificar o objetivo ```Set-DPMPolicyObjective``` de proteção usando o cmdlet. Para alterar as agendas padrão, use o cmdlet [Get-DPMPolicySchedule](https://technet.microsoft.com/library/hh881749) seguido pelo cmdlet [set-DPMPolicySchedule](https://technet.microsoft.com/library/hh881723) .

```powershell
$onlineSch = Get-DPMPolicySchedule -ProtectionGroup $mpg -LongTerm Online
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[0] -TimesOfDay 02:00
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[1] -TimesOfDay 02:00 -DaysOfWeek Sa,Su –Interval 1
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[2] -TimesOfDay 02:00 -RelativeIntervals First,Third –DaysOfWeek Sa
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[3] -TimesOfDay 02:00 -DaysOfMonth 2,5,8,9 -Months Jan,Jul
Set-DPMProtectionGroup -ProtectionGroup $MPG
```

No exemplo acima, ```$onlineSch``` é uma matriz com quatro elementos que contém o agendamento de proteção online existente para o grupo de proteção no esquema GFS:

1. ```$onlineSch[0]```contém o agendamento diário
2. ```$onlineSch[1]```contém a agenda semanal
3. ```$onlineSch[2]```contém o agendamento mensal
4. ```$onlineSch[3]```contém o agendamento anual

Portanto, se precisar modificar o agendamento semanal, você precisará fazer referência ao ```$onlineSch[1]```.

### <a name="initial-backup"></a>Cópia de segurança inicial

Ao fazer backup de uma fonte de fontes pela primeira vez, o DPM precisa criar uma réplica inicial que cria uma cópia completa da fonte de fontes a ser protegida no volume da réplica do DPM. Essa atividade pode ser agendada para um horário específico ou pode ser disparada manualmente, usando o cmdlet [set-DPMReplicaCreationMethod](https://technet.microsoft.com/library/hh881715) com o ```-NOW```parâmetro.

```powershell
Set-DPMReplicaCreationMethod -ProtectionGroup $MPG -NOW
```

### <a name="changing-the-size-of-dpm-replica--recovery-point-volume"></a>Alterando o tamanho da réplica do DPM & volume do ponto de recuperação

Você também pode alterar o tamanho do volume da réplica do DPM e do volume da cópia de sombra usando o cmdlet [set-dpmdatasourcediskallocation,](https://technet.microsoft.com/library/hh881618.aspx) como no exemplo a seguir: Get-DatasourceDiskAllocation-DataSource $DS Set-DatasourceDiskAllocation-DataSource $DS-grupo de proteção $MPG-manual-ReplicaArea (2GB)-ShadowCopyArea (2GB)

### <a name="committing-the-changes-to-the-protection-group"></a>Confirmando as alterações no grupo de proteção

Por fim, as alterações precisam ser confirmadas antes que o DPM possa fazer o backup de acordo com a nova configuração do grupo de proteção. Isso pode ser feito usando o cmdlet [set-DPMProtectionGroup](https://technet.microsoft.com/library/hh881758) .

```powershell
Set-DPMProtectionGroup -ProtectionGroup $MPG
```

## <a name="view-the-backup-points"></a>Exibir os pontos de backup

Você pode usar o cmdlet [Get-DPMRecoveryPoint](https://technet.microsoft.com/library/hh881746) para obter uma lista de todos os pontos de recuperação de uma fonte de fontes. Neste exemplo, iremos:

* buscar todos os PGs no servidor DPM e armazenados em uma matriz```$PG```
* obter as fontes de fonte correspondentes ao```$PG[0]```
* obter todos os pontos de recuperação de uma fonte de uma.

```powershell
$PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
$DS = Get-DPMDatasource -ProtectionGroup $PG[0]
$RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online
```

## <a name="restore-data-protected-on-azure"></a>Restaurar dados protegidos no Azure

A restauração de dados é uma combinação ```RecoverableItem``` de um objeto ```RecoveryOption``` e um objeto. Na seção anterior, obtivemos uma lista dos pontos de backup de uma fonte de fontes.

No exemplo a seguir, demonstramos como restaurar uma máquina virtual Hyper-V do backup do Azure combinando pontos de backup com o destino para recuperação. Este exemplo inclui:

* Criando uma opção de recuperação usando o cmdlet [New-DPMRecoveryOption](https://technet.microsoft.com/library/hh881592) .
* Buscando a matriz de pontos de backup usando o ```Get-DPMRecoveryPoint``` cmdlet.
* Escolhendo um ponto de backup do qual restaurar.

```powershell
$RecoveryOption = New-DPMRecoveryOption -HyperVDatasource -TargetServer "HVDCenter02" -RecoveryLocation AlternateHyperVServer -RecoveryType Recover -TargetLocation “C:\VMRecovery”

$PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
$DS = Get-DPMDatasource -ProtectionGroup $PG[0]
$RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online

Restore-DPMRecoverableItem -RecoverableItem $RecoveryPoints[0] -RecoveryOption $RecoveryOption
```

Os comandos podem ser facilmente estendidos para qualquer tipo de DataSource.

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre o backup do DPM para o Azure, consulte [introdução ao backup do DPM](backup-azure-dpm-introduction.md)
