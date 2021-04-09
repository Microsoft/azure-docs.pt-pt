---
title: Use PowerShell para fazer o back up Windows Server para Azure
description: Neste artigo, aprenda a usar o PowerShell para configurar o Azure Backup no Windows Server ou num cliente Windows, e gerir a cópia de segurança e a recuperação.
ms.topic: conceptual
ms.date: 12/2/2019
ms.openlocfilehash: 582d8123f16b2d5a543d862b8eb3e45895087e4a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "90987110"
---
# <a name="deploy-and-manage-backup-to-azure-for-windows-serverwindows-client-using-powershell"></a>Implementar e gerir cópias de segurança para o Azure para o Windows Server/cliente Windows com o PowerShell

Este artigo mostra-lhe como usar o PowerShell para configurar o Azure Backup no Windows Server ou num cliente Windows, e gerir a cópia de segurança e a recuperação.

## <a name="install-azure-powershell"></a>Instalar o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para começar, [instale o mais recente lançamento powerShell](/powershell/azure/install-az-ps).

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 

Os passos seguintes levam-no através da criação de um cofre dos Serviços de Recuperação. Um cofre dos Serviços de Recuperação é diferente de um cofre de reserva.

1. Se estiver a utilizar o Azure Backup pela primeira vez, tem de utilizar o **cmdlet Register-AzResourceProvider** para registar o fornecedor do Serviço de Recuperação Azure com a sua subscrição.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

2. O cofre dos Serviços de Recuperação é um recurso Azure Resource Manager, pelo que é necessário colocá-lo dentro de um Grupo de Recursos. Pode utilizar um grupo de recursos existente ou criar um novo. Ao criar um novo grupo de recursos, especifique o nome e a localização do grupo de recursos.  

    ```powershell
    New-AzResourceGroup –Name "test-rg" –Location "WestUS"
    ```

3. Utilize o **cmdlet New-AzRecoveryServicesVault** para criar o novo cofre. Certifique-se de especificar o mesmo local para o cofre que foi usado para o grupo de recursos.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "WestUS"
    ```

4. Especifique o tipo de redundância de armazenamento a utilizar. Pode utilizar [armazenamento localmente redundante (LRS),](../storage/common/storage-redundancy.md#locally-redundant-storage) [armazenamento geo-redundante (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage) ou [armazenamento redundante de zona (ZRS)](../storage/common/storage-redundancy.md#zone-redundant-storage). O exemplo a seguir mostra a opção **-BackupStorageRedundancy** para *o testVault* definido para **GeoRedundant**.

   > [!TIP]
   > Muitos cmdlets do Azure Backup requerem o objeto do cofre dos Serviços de Recuperação como entrada. Por esta razão, é conveniente armazenar o objeto do cofre dos Serviços de Recuperação de Backup numa variável.
   >
   >

    ```powershell
    $Vault1 = Get-AzRecoveryServicesVault –Name "testVault"
    Set-AzRecoveryServicesBackupProperties -Vault $Vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>Ver os cofres numa subscrição

Utilize **o Get-AzRecoveryServicesVault** para ver a lista de todos os cofres da subscrição atual. Pode usar este comando para verificar se foi criado um novo cofre, ou para ver quais cofres estão disponíveis na subscrição.

Executar o comando, **Get-AzRecoveryServicesVault,** e todos os cofres da subscrição estão listados.

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

## <a name="installing-the-azure-backup-agent"></a>Instalação do agente Azure Backup

Antes de instalar o agente Azure Backup, precisa de ter o instalador descarregado e presente no Servidor do Windows. Pode obter a versão mais recente do instalador a partir do [Microsoft Download Center](https://aka.ms/azurebackup_agent) ou da página dashboard do cofre dos Serviços de Recuperação. Guarde o instalador para um local facilmente acessível, como `C:\Downloads\*` .

Em alternativa, utilize o PowerShell para obter o descarregador:

 ```powershell
 $MarsAURL = 'https://aka.ms/Azurebackup_Agent'
 $WC = New-Object System.Net.WebClient
 $WC.DownloadFile($MarsAURL,'C:\downloads\MARSAgentInstaller.EXE')
 C:\Downloads\MARSAgentInstaller.EXE /q
 ```

Para instalar o agente, executar o seguinte comando numa consola PowerShell elevada:

```powershell
MARSAgentInstaller.exe /q
```

Isto instala o agente com todas as opções predefinidos. A instalação demora alguns minutos em segundo plano. Se não especificar a opção */nu,* a janela **De Atualização** do Windows abrir-se-á no final da instalação para verificar se há atualizações. Uma vez instalado, o agente aparecerá na lista de programas instalados.

Para ver a lista de programas instalados, vá a Programas e Funcionalidades de **Programas do Painel de**  >    >  Controlo.

![Agente instalado](./media/backup-client-automation/installed-agent-listing.png)

### <a name="installation-options"></a>Opções de instalação

Para ver todas as opções disponíveis através da linha de comando, utilize o seguinte comando:

```powershell
MARSAgentInstaller.exe /?
```

As opções disponíveis incluem:

| Opção | Detalhes | Predefinição |
| --- | --- | --- |
| /q |Instalação silenciosa |- |
| /p:"Localização" |Caminho para a pasta de instalação para o agente de backup Azure. |C:\Ficheiros de programa\Microsoft Azure Recovery Services Agent |
| /s:"localização" |Caminho para a pasta de cache para o agente Azure Backup. |C:\Ficheiros de programa\Microsoft Azure Recovery Services Agent\Scratch |
| /m |Opt-in para Microsoft Update |- |
| /nu |Não Verifique se há atualizações após a conclusão da instalação |- |
| /d |Desinstala o Agente de Serviços de Recuperação do Microsoft Azure |- |
| /ph |Endereço de anfitrião proxy |- |
| /po |Número de porta de anfitrião proxy |- |
| /pu |Nome de utilizador do anfitrião proxy |- |
| /pw |Senha de procuração |- |

## <a name="registering-windows-server-or-windows-client-machine-to-a-recovery-services-vault"></a>Registar o Windows Server ou a máquina de clientes do Windows num cofre de Serviços de Recuperação

Depois de criar o cofre dos Serviços de Recuperação, descarregue as credenciais mais recentes e as credenciais do cofre e guarde-o num local conveniente como C:\Downloads.

```powershell
$CredsPath = "C:\downloads"
$CredsFilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $Vault1 -Path $CredsPath
```

### <a name="registering-using-the-ps-az-module"></a>Registo usando o módulo PS Az

> [!NOTE]
> Um bug com a geração de certificado de abóbada é fixado em versão Az 3.5.0. Use a versão de lançamento Az 3.5.0 ou maior para descarregar um certificado de cofre.

No mais recente módulo Az da PowerShell, devido às limitações subjacentes à plataforma, o download das credenciais do cofre requer um certificado auto-assinado. O exemplo a seguir mostra como fornecer um certificado auto-assinado e baixar as credenciais do cofre.

```powershell
$dt = $(Get-Date).ToString("M-d-yyyy")
$cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -FriendlyName 'test-vaultcredentials' -subject "Windows Azure Tools" -KeyExportPolicy Exportable -NotAfter $(Get-Date).AddHours(48) -NotBefore $(Get-Date).AddHours(-24) -KeyProtection None -KeyUsage None -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2") -Provider "Microsoft Enhanced Cryptographic Provider v1.0"
$certficate = [convert]::ToBase64String($cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pfx))
$CredsFilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $Vault -Path $CredsPath -Certificate $certficate
```

Na máquina cliente do Windows Server ou Do Windows, execute o cmdlet [start-OBRegistration](/powershell/module/msonlinebackup/start-obregistration) para registar a máquina com o cofre.
Este, e outros cmdlets utilizados para a cópia de segurança, são do módulo MSONLINE, que o Agente MARS adicionou como parte do processo de instalação.

O instalador do Agente não atualiza a variável $Env:PSModulePath. Isto significa que a carga automática do módulo falha. Para resolver isto, pode fazer o seguinte:

```powershell
$Env:PSModulePath += ';C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules'
```

Em alternativa, pode carregar manualmente o módulo no seu script da seguinte forma:

```powershell
Import-Module -Name 'C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup'
```

Uma vez carregado os cmdlets de backup on-line, regista as credenciais do cofre:

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
> Não use caminhos relativos para especificar o ficheiro de credenciais do cofre. Deve fornecer um caminho absoluto como entrada para o cmdlet.
>
>

## <a name="networking-settings"></a>Definições de rede

Quando a conectividade da máquina do Windows para a internet é através de um servidor proxy, as definições de procuração também podem ser fornecidas ao agente. Neste exemplo, não há servidor proxy, por isso estamos a limpar explicitamente qualquer informação relacionada com procuração.

O uso de largura de banda também pode ser controlado com as opções de `work hour bandwidth` e para um determinado conjunto de dias da `non-work hour bandwidth` semana.

A definição dos detalhes de procuração e largura de banda é feita utilizando o [cmdlet set-OBMachineSetting:](/powershell/module/msonlinebackup/set-obmachinesetting)

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

Os dados de backup enviados para a Azure Backup são encriptados para proteger a confidencialidade dos dados. A palavra-passe de encriptação é a "palavra-passe" para desencriptar os dados no momento da restauração.

Tem de gerar um pino de segurança selecionando **Generate**, em **Definições**  >  **Propriedades**  >  **O PIN de segurança** na secção de cofre dos Serviços de **Recuperação** do portal Azure.

>[!NOTE]
> O PIN de segurança só pode ser gerado através do portal Azure.

Então, use isto como `generatedPIN` o comando:

```powershell
$PassPhrase = ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force
Set-OBMachineSetting -EncryptionPassPhrase $PassPhrase -SecurityPin "<generatedPIN>"
```

```Output
Server properties updated successfully
```

> [!IMPORTANT]
> Mantenha a palavra-passe segura e segura uma vez definida. Não se pode restaurar os dados do Azure sem esta frase.
>
>

## <a name="back-up-files-and-folders"></a>Fazer o back-up de ficheiros e pastas

Todas as cópias de segurança dos Servidores do Windows e clientes para Azure Backup são regidas por uma política. A política inclui três partes:

1. Um **horário de backup** que especifica quando as cópias de segurança precisam de ser tomadas e sincronizadas com o serviço.
2. Um **calendário de retenção** que especifica quanto tempo para manter os pontos de recuperação em Azure.
3. Uma **especificação de inclusão/exclusão** de ficheiros que dita o que deve ser apoiado.

Neste documento, já que estamos a automatizar reforços, assumimos que nada foi configurado. Começamos por criar uma nova política de backup usando o [cmdlet New-OBPolicy.](/powershell/module/msonlinebackup/new-obpolicy)

```powershell
$NewPolicy = New-OBPolicy
```

Neste momento, a política está vazia e são necessários outros cmdlets para definir quais os itens que serão incluídos ou excluídos, quando as cópias de segurança serão executadas e onde as cópias de segurança serão armazenadas.

### <a name="configuring-the-backup-schedule"></a>Configurar o horário de backup

A primeira das três partes de uma política é o calendário de backup, que é criado usando o cmdlet [New-OBSchedule.](/powershell/module/msonlinebackup/new-obschedule) O horário de reserva define quando é necessário fazer backups. Ao criar um horário, tem de especificar dois parâmetros de entrada:

* **Dias da semana** em que o reforço deve funcionar. Podes fazer o trabalho de reserva em apenas um dia, ou todos os dias da semana, ou qualquer combinação no meio.
* **As horas do dia** em que o reforço deve funcionar. Pode definir até três horas diferentes do dia quando a cópia de segurança será acionada.

Por exemplo, pode configurar uma política de backup que funciona às 16:00 todos os sábados e domingos.

```powershell
$Schedule = New-OBSchedule -DaysOfWeek Saturday, Sunday -TimesOfDay 16:00
```

O calendário de backup tem de ser associado a uma política, o que pode ser conseguido utilizando o [cmdlet Set-OBSchedule.](/powershell/module/msonlinebackup/set-obschedule)

```powershell
Set-OBSchedule -Policy $NewPolicy -Schedule $Schedule
```

```Output
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s) DsList : PolicyName : RetentionPolicy : State : New PolicyState : Valid
```

### <a name="configuring-a-retention-policy"></a>Configurar uma política de retenção

A política de retenção define a duração dos pontos de recuperação criados a partir de postos de trabalho de reserva. Ao criar uma nova política de retenção utilizando o cmdlet [New-OBRetentionPolicy,](/powershell/module/msonlinebackup/new-obretentionpolicy) pode especificar o número de dias em que os pontos de recuperação de backup serão mantidos com O Reforço Azure. O exemplo abaixo define uma política de retenção de sete dias.

```powershell
$RetentionPolicy = New-OBRetentionPolicy -RetentionDays 7
```

A política de retenção deve ser associada à política principal utilizando a cmdlet [Set-OBRetentionPolicy](/powershell/module/msonlinebackup/set-obretentionpolicy):

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

### <a name="including-and-excluding-files-to-be-backed-up"></a>Incluindo e excluindo ficheiros a serem apoiados

Um `OBFileSpec` objeto define os ficheiros a serem incluídos e excluídos numa cópia de segurança. Este é um conjunto de regras que examinam os ficheiros e pastas protegidos numa máquina. Pode ter o máximo de regras de inclusão de ficheiros ou exclusão necessárias e associá-las a uma política. Ao criar um novo objeto OBFileSpec, pode:

* Especificar os ficheiros e pastas a incluir
* Especificar os ficheiros e pastas a excluir
* Especifique a cópia de segurança recursiva dos dados numa pasta (ou) se apenas os ficheiros de nível superior na pasta especificada devem ser apoiados.

Este último é conseguido utilizando a bandeira -Não-Recursiva no comando New-OBFileSpec.

No exemplo abaixo, vamos fazer o backup volume C: e D: e excluir as binárias de OS na pasta Windows e quaisquer pastas temporárias. Para tal, criaremos duas especificações de ficheiros utilizando o cmdlet [New-OBFileSpec](/powershell/module/msonlinebackup/new-obfilespec) - um para inclusão e outro para exclusão. Uma vez criadas as especificações do ficheiro, estão associadas à política utilizando o [cmdlet Add-OBFileSpec.](/powershell/module/msonlinebackup/add-obfilespec)

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

### <a name="applying-the-policy"></a>Aplicação da política

Agora o objeto da política está completo e tem um calendário de backup associado, política de retenção e uma lista de ficheiros de inclusão/exclusão. Esta política pode agora ser comprometida para que o Azure Backup utilize. Antes de aplicar a política recém-criada, certifique-se de que não existem políticas de backup existentes associadas ao servidor utilizando o cmdlet [Remove-OBPolicy.](/powershell/module/msonlinebackup/remove-obpolicy) A remoção da apólice solicitará a confirmação. Para saltar a confirmação, utilize a `-Confirm:$false` bandeira com o cmdlet.

```powershell
Get-OBPolicy | Remove-OBPolicy
```

```Output
Microsoft Azure Backup Are you sure you want to remove this backup policy? This will delete all the backed up data. [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
```

A demissão do objeto de política é feita utilizando o [cmdlet Set-OBPolicy.](/powershell/module/msonlinebackup/set-obpolicy) Isto também pedirá confirmação. Para saltar a confirmação, utilize a `-Confirm:$false` bandeira com o cmdlet.

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

Pode ver os detalhes da política de backup existente utilizando o cmdlet [Get-OBPolicy.](/powershell/module/msonlinebackup/get-obpolicy) Você pode aprofundar mais usando o cmdlet [Get-OBSchedule](/powershell/module/msonlinebackup/get-obschedule) para o horário de backup e o [cmdlet Get-OBRetentionPolicy](/powershell/module/msonlinebackup/get-obretentionpolicy) para as políticas de retenção

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

### <a name="performing-an-on-demand-backup"></a>Realização de um backup a pedido

Uma vez definida uma política de backup, as cópias de segurança ocorrerão de acordo com o horário. Desencadear uma cópia de segurança a pedido também é possível utilizando o [cmdlet Start-OBBackup:](/powershell/module/msonlinebackup/start-obbackup)

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

## <a name="back-up-windows-server-system-state-in-mars-agent"></a>Apoiar o Estado do Sistema do Servidor do Windows no agente MARS

Esta secção cobre o comando PowerShell para configurar o Estado do Sistema no agente MARS

### <a name="schedule"></a>Agenda

```powershell
$sched = New-OBSchedule -DaysOfWeek Sunday,Monday,Tuesday,Wednesday,Thursday,Friday,Saturday -TimesOfDay 2:00
```

### <a name="retention"></a>Retenção

```powershell
$rtn = New-OBRetentionPolicy -RetentionDays 32 -RetentionWeeklyPolicy -RetentionWeeks 13 -WeekDaysOfWeek Sunday -WeekTimesOfDay 2:00  -RetentionMonthlyPolicy -RetentionMonths 13 -MonthDaysOfMonth 1 -MonthTimesOfDay 2:00
```

### <a name="configuring-schedule-and-retention"></a>Agenda de configuração e retenção

```powershell
New-OBPolicy | Add-OBSystemState |  Set-OBRetentionPolicy -RetentionPolicy $rtn | Set-OBSchedule -Schedule $sched | Set-OBSystemStatePolicy
 ```

### <a name="verifying-the-policy"></a>Verificação da apólice

```powershell
Get-OBSystemStatePolicy
 ```

## <a name="restore-data-from-azure-backup"></a>Restaurar dados do Azure Backup

Esta secção irá guiá-lo através dos passos para automatizar a recuperação de dados da Azure Backup. Ao fazê-lo, trata-se dos seguintes passos:

1. Escolha o volume de origem
2. Escolha um ponto de reserva para restaurar
3. Especifique um item para restaurar
4. Desencadear o processo de restauro

### <a name="picking-the-source-volume"></a>Escolher o volume de origem

Para restaurar um item da Azure Backup, primeiro tem de identificar a origem do artigo. Uma vez que estamos a executar os comandos no contexto de um Servidor Windows ou de um cliente Windows, a máquina já está identificada. O próximo passo na identificação da fonte é identificar o volume que a contém. Uma lista de volumes ou fontes que estão a ser apoiadas a partir desta máquina pode ser recuperada executando o cmdlet [Get-OBRecoverableSource.](/powershell/module/msonlinebackup/get-obrecoverablesource) Este comando retorna uma série de todas as fontes apoiadas a partir deste servidor/cliente.

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

### <a name="choosing-a-backup-point-from-which-to-restore"></a>Escolher um ponto de reserva a partir do qual restaurar

Recupera uma lista de pontos de backup executando o cmdlet [Get-OBRecoverableItem](/powershell/module/msonlinebackup/get-obrecoverableitem) com parâmetros apropriados. No nosso exemplo, escolheremos o último ponto de backup para o volume de origem *C:* e usá-lo-emos para recuperar um ficheiro específico.

```powershell
$Rps = Get-OBRecoverableItem $Source[0]
$Rps
```

```Output

IsDir                : False
ItemNameFriendly     : C:\
ItemNameGuid         : \\?\Volume{297cbf7a-0000-0000-0000-401f00000000}\
LocalMountPoint      : C:\
MountPointName       : C:\
Name                 : C:\
PointInTime          : 10/17/2019 7:52:13 PM
ServerName           : myserver.microsoft.com
ItemSize             :
ItemLastModifiedTime :

IsDir                : False
ItemNameFriendly     : C:\
ItemNameGuid         : \\?\Volume{297cbf7a-0000-0000-0000-401f00000000}\
LocalMountPoint      : C:\
MountPointName       : C:\
Name                 : C:\
PointInTime          : 10/16/2019 7:00:19 PM
ServerName           : myserver.microsoft.com
ItemSize             :
ItemLastModifiedTime :
```

O objeto `$Rps` é uma série de pontos de reserva. O primeiro elemento é o último ponto e o elemento Nth é o ponto mais antigo. Para escolher o último ponto, `$Rps[0]` usaremos.

### <a name="specifying-an-item-to-restore"></a>Especificar um item para restaurar

Para restaurar um ficheiro específico, especifique o nome do ficheiro relativamente ao volume de raiz. Por exemplo, para recuperar C:\Test\Cat.job, execute o seguinte comando.

```powershell
$Item = New-OBRecoverableItem $Rps[0] "Test\cat.jpg" $FALSE
$Item
```

```Output
IsDir                : False
ItemNameFriendly     : C:\Test\cat.jpg
ItemNameGuid         :
LocalMountPoint      : C:\
MountPointName       : C:\
Name                 : cat.jpg
PointInTime          : 10/17/2019 7:52:13 PM
ServerName           : myserver.microsoft.com
ItemSize             :
ItemLastModifiedTime : 21-Jun-14 6:43:02 AM

```

### <a name="triggering-the-restore-process"></a>Desencadeando o processo de restauro

Para desencadear o processo de restauro, primeiro temos de especificar as opções de recuperação. Isto pode ser feito utilizando o [cmdlet New-OBRecoveryOption.](/powershell/module/msonlinebackup/new-obrecoveryoption) Para este exemplo, vamos supor que queremos restaurar os ficheiros para *C:\temperatura.* Vamos também assumir que queremos ignorar ficheiros que já existem na pasta de destino *C:\temp*. Para criar tal opção de recuperação, utilize o seguinte comando:

```powershell
$RecoveryOption = New-OBRecoveryOption -DestinationPath "C:\temp" -OverwriteType Skip
```

Agora desencadeie o processo de restauro utilizando o comando [Start-OBRecovery](/powershell/module/msonlinebackup/start-obrecovery) no selecionado `$Item` a partir da saída do `Get-OBRecoverableItem` cmdlet:

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

## <a name="uninstalling-the-azure-backup-agent"></a>Desinstalar o agente de backup Azure

Desinstalar o agente Azure Backup pode ser feito utilizando o seguinte comando:

```powershell
.\MARSAgentInstaller.exe /d /q
```

Desinstalar os binários do agente da máquina tem algumas consequências a ter em conta:

* Remove o filtro de ficheiro da máquina e o rastreio das alterações é interrompido.
* Todas as informações políticas são removidas da máquina, mas a informação da política continua a ser armazenada no serviço.
* Todos os horários de reserva são removidos e não são tomadas mais cópias de segurança.

No entanto, os dados armazenados no Azure permanecem e são mantidos de acordo com a configuração da política de retenção por si. Os pontos mais velhos são automaticamente envelhecidos.

## <a name="remote-management"></a>Gestão remota

Toda a gestão em torno do agente de backup Azure, políticas e fontes de dados pode ser feita remotamente através do PowerShell. A máquina que será gerida remotamente tem de ser preparada corretamente.

Por predefinição, o serviço WinRM está configurado para arranque manual. O tipo de arranque deve ser definido como *Automático* e o serviço deve ser iniciado. Para verificar se o serviço WinRM está em execução, o valor da propriedade Status deve estar *em execução*.

```powershell
Get-Service -Name WinRM
```

```Output
Status   Name               DisplayName
------   ----               -----------
Running  winrm              Windows Remote Management (WS-Manag...
```

PowerShell deve ser configurado para a remoting.

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

A máquina pode agora ser gerida remotamente - a partir da instalação do agente. Por exemplo, o seguinte script copia o agente para a máquina remota e instala-o.

```powershell
$DLoc = "\\REMOTESERVER01\c$\Windows\Temp"
$Agent = "\\REMOTESERVER01\c$\Windows\Temp\MARSAgentInstaller.exe"
$Args = "/q"
Copy-Item "C:\Downloads\MARSAgentInstaller.exe" -Destination $DLoc -Force

$Session = New-PSSession -ComputerName REMOTESERVER01
Invoke-Command -Session $Session -Script { param($D, $A) Start-Process -FilePath $D $A -Wait } -ArgumentList $Agent, $Args
```

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre a cópia de segurança do Azure para Windows Server/Cliente:

* [Introdução ao Azure Backup](./backup-overview.md)
* [Fazer o back up servidores do Windows](backup-windows-with-mars-agent.md)
