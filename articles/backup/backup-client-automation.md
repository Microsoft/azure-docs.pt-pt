---
title: Usar o PowerShell para fazer backup do Windows Server no Azure
description: Saiba como implantar e gerenciar o backup do Azure usando o PowerShell
ms.reviewer: shivamg
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 5/24/2018
ms.author: dacurwin
ms.openlocfilehash: bb488db036b99d3826a3060a7f4143bec7aea3e5
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68688576"
---
# <a name="deploy-and-manage-backup-to-azure-for-windows-serverwindows-client-using-powershell"></a>Implementar e gerir cópias de segurança para o Azure para o Windows Server/cliente Windows com o PowerShell

Este artigo mostra como usar o PowerShell para configurar o backup do Azure no Windows Server ou um cliente do Windows e gerenciar o backup e a recuperação.

## <a name="install-azure-powershell"></a>Instalar o Azure PowerShell
[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para começar, [Instale a versão mais recente do PowerShell](/powershell/azure/install-az-ps).

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos serviços de recuperação

As etapas a seguir orientarão você na criação de um cofre dos serviços de recuperação. Um cofre dos serviços de recuperação é diferente de um cofre de backup.

1. Se você estiver usando o backup do Azure pela primeira vez, deverá usar o cmdlet **Register-AzResourceProvider** para registrar o provedor de serviços de recuperação do Azure com sua assinatura.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

2. O cofre dos serviços de recuperação é um recurso do ARM, portanto, você precisa colocá-lo em um grupo de recursos. Você pode usar um grupo de recursos existente ou criar um novo. Ao criar um novo grupo de recursos, especifique o nome e o local do grupo de recursos.  

    ```powershell
    New-AzResourceGroup –Name "test-rg" –Location "WestUS"
    ```

3. Use o cmdlet **New-AzRecoveryServicesVault** para criar o novo cofre. Certifique-se de especificar o mesmo local para o cofre que foi usado para o grupo de recursos.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "WestUS"
    ```

4. Especifique o tipo de redundância de armazenamento a ser usado; Você pode usar o [LRS (armazenamento com redundância local)](../storage/common/storage-redundancy-lrs.md) ou o [grs (armazenamento com redundância geográfica)](../storage/common/storage-redundancy-grs.md). O exemplo a seguir mostra a opção-BackupStorageRedundancy para testVault é definida como georedundante.

   > [!TIP]
   > Muitos cmdlets do Azure Backup requerem o objeto do cofre dos Serviços de Recuperação como entrada. Por este motivo, é conveniente armazenar o objeto do cofre dos Serviços de Recuperação do Backup numa variável.
   >
   >

    ```powershell
    $Vault1 = Get-AzRecoveryServicesVault –Name "testVault"
    Set-AzRecoveryServicesBackupProperties -Vault $Vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>Exibir os cofres em uma assinatura

Use **Get-AzRecoveryServicesVault** para exibir a lista de todos os cofres na assinatura atual. Você pode usar esse comando para verificar se um novo cofre foi criado ou para ver quais cofres estão disponíveis na assinatura.

Execute o comando, **Get-AzRecoveryServicesVault**e todos os cofres na assinatura são listados.

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


[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="installing-the-azure-backup-agent"></a>Instalando o agente de backup do Azure

Antes de instalar o agente de backup do Azure, você precisa ter o instalador baixado e presente no Windows Server. Você pode obter a versão mais recente do instalador no [centro de download da Microsoft](https://aka.ms/azurebackup_agent) ou na página do painel do cofre dos serviços de recuperação. Salve o instalador em um local facilmente acessível, como *\*C:\Downloads.

Como alternativa, use o PowerShell para obter o Downloader:

 ```powershell
 $MarsAURL = 'https://aka.ms/Azurebackup_Agent'
 $WC = New-Object System.Net.WebClient
 $WC.DownloadFile($MarsAURL,'C:\downloads\MARSAgentInstaller.EXE')
 C:\Downloads\MARSAgentInstaller.EXE /q
 ```

Para instalar o agente, execute o seguinte comando em um console do PowerShell com privilégios elevados:

```powershell
MARSAgentInstaller.exe /q
```

Isso instala o agente com todas as opções padrão. A instalação leva alguns minutos em segundo plano. Se você não especificar a opção */nu* , a janela de **Windows Update** será aberta no final da instalação para verificar se há atualizações. Uma vez instalado, o agente será exibido na lista de programas instalados.

Para ver a lista de programas instalados, vá para **painel** > de controle**programas** > programas**e recursos**.

![Agente instalado](./media/backup-client-automation/installed-agent-listing.png)

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

## <a name="registering-windows-server-or-windows-client-machine-to-a-recovery-services-vault"></a>Registrando o Windows Server ou o computador cliente Windows em um cofre dos serviços de recuperação

Depois de criar o cofre dos serviços de recuperação, baixe o agente mais recente e as credenciais do cofre e armazene-os em um local conveniente, como C:\Downloads.

```powershell
$CredsPath = "C:\downloads"
$CredsFilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $Vault1 -Path $CredsPath
```

No computador cliente Windows Server ou Windows, execute o cmdlet [Start-OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx) para registrar o computador no cofre.
Isso, e outros cmdlets usados para backup, são do módulo MSONLINE que o Mars AgentInstaller adicionou como parte do processo de instalação.

O instalador do agente não atualiza o $Env:P variável SModulePath. Isso significa que o carregamento automático do módulo falha. Para resolver isso, você pode fazer o seguinte:

```powershell
$Env:PSModulePath += ';C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules'
```

Como alternativa, você pode carregar manualmente o módulo em seu script da seguinte maneira:

```powershell
Import-Module -Name 'C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup'
```

Depois de carregar os cmdlets de backup online, registre as credenciais do cofre:

```powershell
Start-OBRegistration -VaultCredentials $CredsFilename.FilePath -Confirm:$false
```

```Output
CertThumbprint      : 7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName : testvault
Region              : WestUS
Machine registration succeeded.
```

> [!IMPORTANT]
> Não use caminhos relativos para especificar o arquivo de credenciais do cofre. Você deve fornecer um caminho absoluto como uma entrada para o cmdlet.
>
>

## <a name="networking-settings"></a>Configurações de rede

Quando a conectividade do computador com Windows com a Internet é por meio de um servidor proxy, as configurações de proxy também podem ser fornecidas ao agente. Neste exemplo, não há nenhum servidor proxy, portanto, estamos limpando explicitamente qualquer informação relacionada ao proxy.

O uso da largura de banda também pode ser controlado `work hour bandwidth` com `non-work hour bandwidth` as opções de e para um determinado conjunto de dias da semana.

Definir os detalhes de proxy e largura de banda é feito usando o cmdlet [set-OBMachineSetting](https://technet.microsoft.com/library/hh770409%28v=wps.630%29.aspx) :

```powershell
Set-OBMachineSetting -NoProxy
```

```Output
Server properties updated successfully.
```

```powershell
Set-OBMachineSetting -NoThrottle
```

```Output
Server properties updated successfully.
```

## <a name="encryption-settings"></a>Definições de encriptação

Os dados de backup enviados para o backup do Azure são criptografados para proteger a confidencialidade dos dados. A senha de criptografia é a "senha" para descriptografar os dados no momento da restauração.

Você deve gerar um PIN de segurança selecionando **gerar**, em **configurações** > **Propriedades** > **PIN de segurança** na seção **cofre dos serviços de recuperação** do portal do Azure. Em seguida, use-o `generatedPIN` como no comando:

```powershell
$PassPhrase = ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force
Set-OBMachineSetting -EncryptionPassPhrase $PassPhrase -SecurityPin "<generatedPIN>"
```

```Output
Server properties updated successfully
```

> [!IMPORTANT]
> Mantenha as informações de senha seguras e seguras após sua definição. Não é possível restaurar dados do Azure sem essa frase secreta.
>
>

## <a name="back-up-files-and-folders"></a>Faça uma cópia de segurança de ficheiros e pastas

Todos os backups de servidores e clientes do Windows para o backup do Azure são regidos por uma política. A política consiste em três partes:

1. Um **agendamento de backup** que especifica quando os backups precisam ser feitos e sincronizados com o serviço.
2. Um **agendamento de retenção** que especifica por quanto tempo os pontos de recuperação são mantidos no Azure.
3. Uma **especificação de inclusão/exclusão de arquivo** que determina o que deve ser submetido a backup.

Neste documento, como estamos automatizando o backup, vamos supor que nada foi configurado. Começamos criando uma nova política de backup usando o cmdlet [New-OBPolicy](https://technet.microsoft.com/library/hh770416.aspx) .

```powershell
$NewPolicy = New-OBPolicy
```

Neste momento, a política está vazia e outros cmdlets são necessários para definir quais itens serão incluídos ou excluídos, quando os backups serão executados e onde os backups serão armazenados.

### <a name="configuring-the-backup-schedule"></a>Configurando o agendamento de backup

A primeira das três partes de uma política é o agendamento de backup, que é criado usando o cmdlet [New-OBSchedule](https://technet.microsoft.com/library/hh770401) . O agendamento de backup define quando os backups precisam ser feitos. Ao criar uma agenda, você precisa especificar dois parâmetros de entrada:

* **Dias da semana** em que o backup deve ser executado. Você pode executar o trabalho de backup em apenas um dia ou todos os dias da semana ou qualquer combinação entre eles.
* **Horas do dia** em que o backup deve ser executado. Você pode definir até três horas diferentes do dia em que o backup será disparado.

Por exemplo, você pode configurar uma política de backup que é executada às 16:00 todos os sábados e domingos.

```powershell
$Schedule = New-OBSchedule -DaysOfWeek Saturday, Sunday -TimesOfDay 16:00
```

O agendamento de backup precisa ser associado a uma política, e isso pode ser obtido usando o cmdlet [set-OBSchedule](https://technet.microsoft.com/library/hh770407) .

```powershell
Set-OBSchedule -Policy $NewPolicy -Schedule $Schedule
```

```Output
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s) DsList : PolicyName : RetentionPolicy : State : New PolicyState : Valid
```
### <a name="configuring-a-retention-policy"></a>Configurando uma política de retenção

A política de retenção define por quanto tempo os pontos de recuperação criados a partir de trabalhos de backup são mantidos. Ao criar uma nova política de retenção usando o cmdlet [New-OBRetentionPolicy](https://technet.microsoft.com/library/hh770425) , você pode especificar o número de dias que os pontos de recuperação de backup precisam ser retidos com o backup do Azure. O exemplo a seguir define uma política de retenção de 7 dias.

```powershell
$RetentionPolicy = New-OBRetentionPolicy -RetentionDays 7
```

A política de retenção deve ser associada à política principal usando o cmdlet [set-OBRetentionPolicy](https://technet.microsoft.com/library/hh770405):

```powershell
Set-OBRetentionPolicy -Policy $NewPolicy -RetentionPolicy $RetentionPolicy
```

```Output
BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          :
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```
### <a name="including-and-excluding-files-to-be-backed-up"></a>Incluindo e excluindo arquivos dos quais será feito backup

Um `OBFileSpec` objeto define os arquivos a serem incluídos e excluídos em um backup. Este é um conjunto de regras que definem o escopo dos arquivos e pastas protegidos em um computador. Você pode ter tantas regras de inclusão ou exclusão de arquivos, conforme necessário, e associá-las a uma política. Ao criar um novo objeto OBFileSpec, você pode:

* Especificar os arquivos e pastas a serem incluídos
* Especificar os arquivos e pastas a serem excluídos
* Especifique o backup recursivo de dados em uma pasta (ou), independentemente de ser feito backup apenas dos arquivos de nível superior na pasta especificada.

O último é obtido usando o sinalizador-NonRecursive no comando New-OBFileSpec.

No exemplo a seguir, vamos fazer backup do volume C: e D: e excluir os binários do sistema operacional na pasta do Windows e em todas as pastas temporárias. Para isso, criaremos duas especificações de arquivo usando o cmdlet [New-OBFileSpec](https://technet.microsoft.com/library/hh770408) , uma para inclusão e outra para exclusão. Depois que as especificações de arquivo tiverem sido criadas, elas serão associadas à política usando o cmdlet [Add-OBFileSpec](https://technet.microsoft.com/library/hh770424) .

```powershell
$Inclusions = New-OBFileSpec -FileSpec @("C:\", "D:\")
$Exclusions = New-OBFileSpec -FileSpec @("C:\windows", "C:\temp") -Exclude
Add-OBFileSpec -Policy $NewPolicy -FileSpec $Inclusions
```

```Output
BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          : {DataSource
                  DatasourceId:0
                  Name:C:\
                  FileSpec:FileSpec
                  FileSpec:C:\
                  IsExclude:False
                  IsRecursive:True

                  , DataSource
                  DatasourceId:0
                  Name:D:\
                  FileSpec:FileSpec
                  FileSpec:D:\
                  IsExclude:False
                  IsRecursive:True

                  }
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```

```powershell
Add-OBFileSpec -Policy $NewPolicy -FileSpec $Exclusions
```

```Output
BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          : {DataSource
                  DatasourceId:0
                  Name:C:\
                  FileSpec:FileSpec
                  FileSpec:C:\
                  IsExclude:False
                  IsRecursive:True
                  ,FileSpec
                  FileSpec:C:\windows
                  IsExclude:True
                  IsRecursive:True
                  ,FileSpec
                  FileSpec:C:\temp
                  IsExclude:True
                  IsRecursive:True

                  , DataSource
                  DatasourceId:0
                  Name:D:\
                  FileSpec:FileSpec
                  FileSpec:D:\
                  IsExclude:False
                  IsRecursive:True

                  }
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```
## <a name="back-up-windows-server-system-state-in-mabs-agent"></a>Fazer backup do estado do sistema do Windows Server no agente do MABS

Esta seção aborda o comando do PowerShell para configurar o estado do sistema no agente do MABS

### <a name="schedule"></a>Agenda
```powershell
$sched = New-OBSchedule -DaysOfWeek Sunday,Monday,Tuesday,Wednesday,Thursday,Friday,Saturday -TimesOfDay 2:00
```

### <a name="retention"></a>Retenção

```powershell
$rtn = New-OBRetentionPolicy -RetentionDays 32 -RetentionWeeklyPolicy -RetentionWeeks 13 -WeekDaysOfWeek Sunday -WeekTimesOfDay 2:00  -RetentionMonthlyPolicy -RetentionMonths 13 -MonthDaysOfMonth 1 -MonthTimesOfDay 2:00
```

### <a name="configuring-schedule-and-retention"></a>Configurando agendamento e retenção

```powershell
New-OBPolicy | Add-OBSystemState |  Set-OBRetentionPolicy -RetentionPolicy $rtn | Set-OBSchedule -Schedule $sched | Set-OBSystemStatePolicy
 ```

### <a name="verifying-the-policy"></a>Verificando a política

```powershell
Get-OBSystemStatePolicy
 ```

### <a name="applying-the-policy"></a>Aplicando a política

Agora, o objeto de política está completo e tem um agendamento de backup associado, uma política de retenção e uma lista de inclusão/exclusão de arquivos. Essa política agora pode ser confirmada para uso pelo backup do Azure. Antes de aplicar a política recém-criada, verifique se não há políticas de backup associadas ao servidor usando o cmdlet [Remove-OBPolicy](https://technet.microsoft.com/library/hh770415) . A remoção da política solicitará confirmação. Para ignorar a confirmação, use `-Confirm:$false` o sinalizador com o cmdlet.

```powershell
Get-OBPolicy | Remove-OBPolicy
```

```Output
Microsoft Azure Backup Are you sure you want to remove this backup policy? This will delete all the backed up data. [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
```

A confirmação do objeto de política é feita usando o cmdlet [set-OBPolicy](https://technet.microsoft.com/library/hh770421) . Isso também solicitará a confirmação. Para ignorar a confirmação, use `-Confirm:$false` o sinalizador com o cmdlet.

```powershell
Set-OBPolicy -Policy $NewPolicy
```

```Output
Microsoft Azure Backup Do you want to save this backup policy ? [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s)
DsList : {DataSource
         DatasourceId:4508156004108672185
         Name:C:\
         FileSpec:FileSpec
         FileSpec:C:\
         IsExclude:False
         IsRecursive:True,

         FileSpec
         FileSpec:C:\windows
         IsExclude:True
         IsRecursive:True,

         FileSpec
         FileSpec:C:\temp
         IsExclude:True
         IsRecursive:True,

         DataSource
         DatasourceId:4508156005178868542
         Name:D:\
         FileSpec:FileSpec
         FileSpec:D:\
         IsExclude:False
         IsRecursive:True
    }
PolicyName : c2eb6568-8a06-49f4-a20e-3019ae411bac
RetentionPolicy : Retention Days : 7
              WeeklyLTRSchedule :
              Weekly schedule is not set

              MonthlyLTRSchedule :
              Monthly schedule is not set

              YearlyLTRSchedule :
              Yearly schedule is not set
State : Existing PolicyState : Valid
```

Você pode exibir os detalhes da política de backup existente usando o cmdlet [Get-OBPolicy](https://technet.microsoft.com/library/hh770406) . Você pode fazer uma busca detalhada usando o cmdlet [Get-OBSchedule](https://technet.microsoft.com/library/hh770423) para o agendamento de backup e o cmdlet [Get-OBRetentionPolicy](https://technet.microsoft.com/library/hh770427) para as políticas de retenção

```powershell
Get-OBPolicy | Get-OBSchedule
```

```Output
SchedulePolicyName : 71944081-9950-4f7e-841d-32f0a0a1359a
ScheduleRunDays : {Saturday, Sunday}
ScheduleRunTimes : {16:00:00}
State : Existing
```

```powershell
Get-OBPolicy | Get-OBRetentionPolicy
```

```Output
RetentionDays : 7
RetentionPolicyName : ca3574ec-8331-46fd-a605-c01743a5265e
State : Existing
```

```powershell
Get-OBPolicy | Get-OBFileSpec
```

```Output
FileName : *
FilePath : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\
FileSpec : D:\
IsExclude : False
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\
FileSpec : C:\
IsExclude : False
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\windows
FileSpec : C:\windows
IsExclude : True
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\temp
FileSpec : C:\temp
IsExclude : True
IsRecursive : True
```

### <a name="performing-an-ad-hoc-backup"></a>Executando um backup ad hoc

Depois que uma política de backup tiver sido definida, os backups ocorrerão de acordo com o agendamento. O disparo de um backup ad hoc também é possível usando o cmdlet [Start-OBBackup](https://technet.microsoft.com/library/hh770426) :

```powershell
Get-OBPolicy | Start-OBBackup
```

```Output
Initializing
Taking snapshot of volumes...
Preparing storage...
Generating backup metadata information and preparing the metadata VHD...
Data transfer is in progress. It might take longer since it is the first backup and all data needs to be transferred...
Data transfer completed and all backed up data is in the cloud. Verifying data integrity...
Data transfer completed
In progress...
Job completed.
The backup operation completed successfully.
```

## <a name="restore-data-from-azure-backup"></a>Restaurar dados do backup do Azure

Esta seção irá orientá-lo pelas etapas para automatizar a recuperação de dados do backup do Azure. Isso envolve as seguintes etapas:

1. Escolha o volume de origem
2. Escolha um ponto de backup para restaurar
3. Escolher um item para restaurar
4. Disparar o processo de restauração

### <a name="picking-the-source-volume"></a>Escolhendo o volume de origem

Para restaurar um item do backup do Azure, você precisa primeiro identificar a origem do item. Como estamos executando os comandos no contexto de um Windows Server ou um cliente do Windows, o computador já está identificado. A próxima etapa na identificação da origem é identificar o volume que o contém. Uma lista de volumes ou fontes cujo backup está sendo feito deste computador pode ser recuperada executando o cmdlet [Get-OBRecoverableSource](https://technet.microsoft.com/library/hh770410) . Esse comando retorna uma matriz de todas as fontes de backup do servidor/cliente.

```powershell
$Source = Get-OBRecoverableSource
$Source
```

```Output
FriendlyName : C:\
RecoverySourceName : C:\
ServerName : myserver.microsoft.com

FriendlyName : D:\
RecoverySourceName : D:\
ServerName : myserver.microsoft.com
```

### <a name="choosing-a-backup-point-from-which-to-restore"></a>Escolhendo um ponto de backup do qual restaurar

Você recupera uma lista de pontos de backup executando o cmdlet [Get-OBRecoverableItem](https://technet.microsoft.com/library/hh770399.aspx) com os parâmetros apropriados. Em nosso exemplo, escolheremos o ponto de backup mais recente para o volume de origem *D:* e o utilizaremos para recuperar um arquivo específico.

```powershell
$Rps = Get-OBRecoverableItem -Source $Source[1]
```

```Output
IsDir : False
ItemNameFriendly : D:\
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\
LocalMountPoint : D:\
MountPointName : D:\
Name : D:\
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize :
ItemLastModifiedTime :

IsDir : False
ItemNameFriendly : D:\
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\
LocalMountPoint : D:\
MountPointName : D:\
Name : D:\
PointInTime : 17-Jun-15 6:31:31 AM
ServerName : myserver.microsoft.com
ItemSize :
ItemLastModifiedTime :
```

O objeto `$Rps` é uma matriz de pontos de backup. O primeiro elemento é o ponto mais recente e o enésimo elemento é o ponto mais antigo. Para escolher o ponto mais recente, `$Rps[0]`usaremos.

### <a name="choosing-an-item-to-restore"></a>Escolhendo um item para restaurar

Para identificar a pasta ou o arquivo exato a ser restaurado, use recursivamente o cmdlet [Get-OBRecoverableItem](https://technet.microsoft.com/library/hh770399.aspx) . Dessa forma, a hierarquia de pastas pode ser procurada somente `Get-OBRecoverableItem`usando o.

Neste exemplo, se quisermos restaurar o arquivo Finances *. xls* , podemos fazer referência a isso usando o objeto `$FilesFolders[1]`.

```powershell
$FilesFolders = Get-OBRecoverableItem $Rps[0]
$FilesFolders
```

```Output
IsDir : True
ItemNameFriendly : D:\MyData\
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\MyData\
LocalMountPoint : D:\
MountPointName : D:\
Name : MyData
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize :
ItemLastModifiedTime : 15-Jun-15 8:49:29 AM
```

```powershell
$FilesFolders = Get-OBRecoverableItem $FilesFolders[0]
$FilesFolders
```

```Output
IsDir : False
ItemNameFriendly : D:\MyData\screenshot.oxps
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\MyData\screenshot.oxps
LocalMountPoint : D:\
MountPointName : D:\
Name : screenshot.oxps
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize : 228313
ItemLastModifiedTime : 21-Jun-14 6:45:09 AM

IsDir : False
ItemNameFriendly : D:\MyData\finances.xls
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\MyData\finances.xls
LocalMountPoint : D:\
MountPointName : D:\
Name : finances.xls
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize : 96256
ItemLastModifiedTime : 21-Jun-14 6:43:02 AM
```

Você também pode pesquisar itens para restaurar usando o ```Get-OBRecoverableItem``` cmdlet. Em nosso exemplo, para procurar *finanças. xls* , podemos obter um identificador no arquivo executando este comando:

```powershell
$Item = Get-OBRecoverableItem -RecoveryPoint $Rps[0] -Location "D:\MyData" -SearchString "finance*"
```

### <a name="triggering-the-restore-process"></a>Disparando o processo de restauração

Para disparar o processo de restauração, primeiro precisamos especificar as opções de recuperação. Isso pode ser feito usando o cmdlet [New-OBRecoveryOption](https://technet.microsoft.com/library/hh770417.aspx) . Para este exemplo, vamos supor que desejamos restaurar os arquivos para *C:\temp*. Vamos supor também que queremos ignorar os arquivos que já existem na pasta de destino *C:\temp*. Para criar essa opção de recuperação, use o seguinte comando:

```powershell
$RecoveryOption = New-OBRecoveryOption -DestinationPath "C:\temp" -OverwriteType Skip
```

Agora dispare o processo de restauração usando o comando [Start-OBRecovery](https://technet.microsoft.com/library/hh770402.aspx) no selecionado `$Item` na saída do `Get-OBRecoverableItem` cmdlet:

```powershell
Start-OBRecovery -RecoverableItem $Item -RecoveryOption $RecoveryOption
```

```Output
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Job completed.
The recovery operation completed successfully.
```

## <a name="uninstalling-the-azure-backup-agent"></a>Desinstalando o agente de backup do Azure

A desinstalação do agente de backup do Azure pode ser feita usando o seguinte comando:

```powershell
.\MARSAgentInstaller.exe /d /q
```

A desinstalação dos binários de agente do computador tem algumas conseqüências a considerar:

* Ele remove o filtro de arquivo do computador e o rastreamento de alterações é interrompido.
* Todas as informações de política são removidas do computador, mas as informações da política continuam sendo armazenadas no serviço.
* Todas as agendas de backup são removidas e nenhum backup adicional é feito.

No entanto, os dados armazenados no Azure permanecem e são mantidos de acordo com a configuração da política de retenção por você. Os pontos mais antigos são automaticamente desatualizados.

## <a name="remote-management"></a>Gestão remota

Todo o gerenciamento em relação ao agente de backup do Azure, às políticas e às fontes de dados pode ser feito remotamente por meio do PowerShell. O computador que será gerenciado remotamente precisa ser preparado corretamente.

Por padrão, o serviço WinRM é configurado para inicialização manual. O tipo de inicialização deve ser definido como *automático* e o serviço deve ser iniciado. Para verificar se o serviço WinRM está em execução, o valor da propriedade status deve estar *em execução*.

```powershell
Get-Service -Name WinRM
```

```Output
Status   Name               DisplayName
------   ----               -----------
Running  winrm              Windows Remote Management (WS-Manag...
```

O PowerShell deve ser configurado para comunicação remota.

```powershell
Enable-PSRemoting -Force
```

```Output
WinRM is already set up to receive requests on this computer.
WinRM has been updated for remote management.
WinRM firewall exception enabled.
```

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force
```

O computador agora pode ser gerenciado remotamente-iniciando na instalação do agente. Por exemplo, o script a seguir copia o agente para o computador remoto e o instala.

```powershell
$DLoc = "\\REMOTESERVER01\c$\Windows\Temp"
$Agent = "\\REMOTESERVER01\c$\Windows\Temp\MARSAgentInstaller.exe"
$Args = "/q"
Copy-Item "C:\Downloads\MARSAgentInstaller.exe" -Destination $DLoc -Force

$Session = New-PSSession -ComputerName REMOTESERVER01
Invoke-Command -Session $Session -Script { param($D, $A) Start-Process -FilePath $D $A -Wait } -ArgumentList $Agent, $Args
```

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o backup do Azure para Windows Server/Client, consulte

* [Introdução ao Azure Backup](backup-introduction-to-azure-backup.md)
* [Fazer backup de servidores Windows](backup-configure-vault.md)
