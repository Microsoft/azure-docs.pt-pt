---
title: Use o PowerShell para fazer o back-up do Windows Server para o Azure
description: Neste artigo, aprenda a utilizar o PowerShell para configurar o Azure Backup no Windows Server ou num cliente windows, e gerir a cópia de segurança e recuperação.
ms.topic: conceptual
ms.date: 12/2/2019
ms.openlocfilehash: 67c80a76720dd544da355ee00540cd11a22bfb10
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83848171"
---
# <a name="deploy-and-manage-backup-to-azure-for-windows-serverwindows-client-using-powershell"></a>Implementar e gerir cópias de segurança para o Azure para o Windows Server/cliente Windows com o PowerShell

Este artigo mostra-lhe como utilizar o PowerShell para configurar o Azure Backup no Windows Server ou num cliente windows, e gerir a cópia de segurança e recuperação.

## <a name="install-azure-powershell"></a>Instalar o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para começar, [instale o mais recente lançamento da PowerShell.](/powershell/azure/install-az-ps)

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos serviços de recuperação

Os seguintes passos levam-no através da criação de um cofre dos Serviços de Recuperação. Um cofre dos Serviços de Recuperação é diferente de um cofre de reserva.

1. Se estiver a utilizar o Azure Backup pela primeira vez, deve utilizar o **cmdlet Register-AzResourceProvider** para registar o prestador do Serviço de Recuperação Azure com a sua subscrição.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

2. O cofre dos Serviços de Recuperação é um recurso ARM, por isso é necessário colocá-lo dentro de um Grupo de Recursos. Você pode usar um grupo de recursos existente, ou criar um novo. Ao criar um novo grupo de recursos, especifique o nome e a localização do grupo de recursos.  

    ```powershell
    New-AzResourceGroup –Name "test-rg" –Location "WestUS"
    ```

3. Utilize o **cmdlet New-AzRecoveryServicesVault** para criar o novo cofre. Certifique-se de especificar a mesma localização para o cofre que foi usado para o grupo de recursos.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "WestUS"
    ```

4. Especificar o tipo de redundância de armazenamento a utilizar; pode utilizar [armazenamento redundante localmente (LRS)](../storage/common/storage-redundancy-lrs.md) ou [Armazenamento Geo Redundante (GRS)](../storage/common/storage-redundancy-grs.md). O exemplo seguinte mostra a opção -BackupStorageRedundancy para testeVault está definida para GeoRedundant.

   > [!TIP]
   > Muitos cmdlets do Azure Backup requerem o objeto do cofre dos Serviços de Recuperação como entrada. Por este motivo, é conveniente armazenar o objeto do cofre dos Serviços de Recuperação do Backup numa variável.
   >
   >

    ```powershell
    $Vault1 = Get-AzRecoveryServicesVault –Name "testVault"
    Set-AzRecoveryServicesBackupProperties -Vault $Vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>Veja os cofres em uma subscrição

Utilize o **Get-AzRecoveryServicesVault** para ver a lista de todos os cofres da subscrição atual. Você pode usar este comando para verificar se um novo cofre foi criado, ou para ver que cofres estão disponíveis na subscrição.

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

## <a name="installing-the-azure-backup-agent"></a>Instalação do agente de backup Azure

Antes de instalar o agente De reserva Azure, é necessário que o instalador seja descarregado e presente no Windows Server. Pode obter a versão mais recente do instalador a partir do [Microsoft Download Center](https://aka.ms/azurebackup_agent) ou da página do Painel de Instrumentos de Recuperação dos Serviços de Recuperação. Guarde o instalador para um local facilmente acessível como `C:\Downloads\*` .

Em alternativa, utilize o PowerShell para obter o downloader:

 ```powershell
 $MarsAURL = 'https://aka.ms/Azurebackup_Agent'
 $WC = New-Object System.Net.WebClient
 $WC.DownloadFile($MarsAURL,'C:\downloads\MARSAgentInstaller.EXE')
 C:\Downloads\MARSAgentInstaller.EXE /q
 ```

Para instalar o agente, execute o seguinte comando numa consola powerShell elevada:

```powershell
MARSAgentInstaller.exe /q
```

Isto instala o agente com todas as opções predefinidas. A instalação demora alguns minutos em segundo plano. Se não especificar a opção */nu,* a janela **Desatualização do Windows** abrir-se-á no final da instalação para verificar se há atualizações. Uma vez instalado, o agente aparecerá na lista de programas instalados.

Para ver a lista de programas instalados, vá a Programas e Funcionalidades de **Painéis**de Controlo  >  **Programs**  >  **Programs and Features**.

![Agente instalado](./media/backup-client-automation/installed-agent-listing.png)

### <a name="installation-options"></a>Opções de instalação

Para ver todas as opções disponíveis através da linha de comando, utilize o seguinte comando:

```powershell
MARSAgentInstaller.exe /?
```

As opções disponíveis incluem:

| Opção | Detalhes | Predefinição |
| --- | --- | --- |
| /q |Instalação tranquila |- |
| /p:"localização" |Caminho para a pasta de instalação para o agente de reserva Azure. |C:\Program Files\Microsoft Azure Recovery Services Agent |
| /s:"localização" |Caminho para a pasta cache para o agente de backup Azure. |C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch |
| /m |Opt-in para microsoft update |- |
| /nu |Não verifique se há atualizações após a instalação estar concluída |- |
| /d |Desinstala o Agente de Serviços de Recuperação do Microsoft Azure |- |
| /ph |Endereço de anfitrião proxy |- |
| /po |Número de porta de anfitrião proxy |- |
| /pu |Nome de utilizador do anfitrião proxy |- |
| /pw |Senha de procuração |- |

## <a name="registering-windows-server-or-windows-client-machine-to-a-recovery-services-vault"></a>Registar o Windows Server ou a máquina cliente windows para um cofre de serviços de recuperação

Depois de criar o cofre dos Serviços de Recuperação, descarregue as credenciais mais recentes do agente e do cofre e guarde-o num local conveniente como C:\Downloads.

```powershell
$CredsPath = "C:\downloads"
$CredsFilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $Vault1 -Path $CredsPath
```

### <a name="registering-using-the-ps-az-module"></a>Registo utilizando o módulo PS Az

> [!NOTE]
> Um bug com geração de certificado de cofre é fixado em lançamento az 3.5.0. Utilize a versão de lançamento az 3.5.0 ou maior para descarregar um certificado de cofre.

No mais recente módulo Az da PowerShell, devido às limitações subjacentes à plataforma, o download das credenciais do cofre requer um certificado auto-assinado. O exemplo que se segue mostra como fornecer um certificado auto-assinado e baixar as credenciais do cofre.

```powershell
$dt = $(Get-Date).ToString("M-d-yyyy")
$cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -FriendlyName 'test-vaultcredentials' -subject "Windows Azure Tools" -KeyExportPolicy Exportable -NotAfter $(Get-Date).AddHours(48) -NotBefore $(Get-Date).AddHours(-24) -KeyProtection None -KeyUsage None -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2") -Provider "Microsoft Enhanced Cryptographic Provider v1.0"
$certficate = [convert]::ToBase64String($cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pfx))
$CredsFilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $Vault -Path $CredsPath -Certificate $certficate
```

No Windows Server ou na máquina cliente windows, execute o cmdlet [Start-OBRegistration](https://docs.microsoft.com/powershell/module/msonlinebackup/start-obregistration?view=winserver2012-ps) para registar a máquina com o cofre.
Este, e outros cmdlets utilizados para a cópia de segurança, são do módulo MSONLINE, que o Agente Deinstalador de Marte acrescentou como parte do processo de instalação.

O instalador do Agente não atualiza a variável $Env:PSModulePath. Isto significa que a carga automática do módulo falha. Para resolver isto, pode fazer o seguinte:

```powershell
$Env:PSModulePath += ';C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules'
```

Em alternativa, pode carregar manualmente o módulo no seu script da seguinte forma:

```powershell
Import-Module -Name 'C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup'
```

Uma vez que carregue os cmdlets de backup on-line, registe as credenciais do cofre:

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
> Não utilize caminhos relativos para especificar o ficheiro de credenciais do cofre. Deve fornecer um caminho absoluto como entrada para o cmdlet.
>
>

## <a name="networking-settings"></a>Definições de rede

Quando a conectividade da máquina do Windows para a internet é através de um servidor proxy, as definições de procuração também podem ser fornecidas ao agente. Neste exemplo, não existe um servidor proxy, por isso estamos explicitamente a limpar qualquer informação relacionada com procuração.

O uso da largura de banda também pode ser controlado com as opções de `work hour bandwidth` e para um determinado conjunto de dias da `non-work hour bandwidth` semana.

A definição dos detalhes de procuração e largura de banda é feita utilizando o cmdlet [Set-OBMachineSetting:](https://docs.microsoft.com/powershell/module/msonlinebackup/set-obmachinesetting)

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

Os dados de backup enviados para o Azure Backup são encriptados para proteger a confidencialidade dos dados. A frase-passe de encriptação é a "palavra-passe" para desencriptar os dados no momento da restauração.

Deve gerar um pino de segurança selecionando **Generate,** em **Definições**  >  **Properties**  >  **Security PIN** na secção de cofre de Serviços de **Recuperação** do portal Azure. 

>[!NOTE]
> O PIN de segurança só pode ser gerado através do portal Azure.

Em seguida, use isto como `generatedPIN` o comando:

```powershell
$PassPhrase = ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force
Set-OBMachineSetting -EncryptionPassPhrase $PassPhrase -SecurityPin "<generatedPIN>"
```

```Output
Server properties updated successfully
```

> [!IMPORTANT]
> Mantenha as informações de palavra-passe seguras e seguras uma vez definidas. Não se pode restaurar os dados do Azure sem esta frase-passe.
>
>

## <a name="back-up-files-and-folders"></a>Back up ficheiros e pastas

Todas as cópias de segurança dos Servidores windows e clientes para o Azure Backup são regidas por uma política. A política compreende três partes:

1. Um calendário de cópia de **segurança** que especifica quando as cópias de segurança precisam de ser tomadas e sincronizadas com o serviço.
2. Um calendário de **retenção** que especifica quanto tempo para manter os pontos de recuperação em Azure.
3. Uma especificação de **inclusão/exclusão** de ficheiros que dita o que deve ser apoiado.

Neste documento, já que estamos a automatizar reforços, vamos assumir que nada foi configurado. Começamos por criar uma nova política de backup usando o cmdlet [New-OBPolicy.](https://docs.microsoft.com/powershell/module/msonlinebackup/new-obpolicy)

```powershell
$NewPolicy = New-OBPolicy
```

Neste momento a política está vazia e são necessários outros cmdlets para definir quais os itens que serão incluídos ou excluídos, quando as cópias de segurança serão executadas, e onde as cópias de segurança serão armazenadas.

### <a name="configuring-the-backup-schedule"></a>Configurar o calendário de backup

A primeira das três partes de uma apólice é o calendário de backup, que é criado usando o cmdlet [New-OBSchedule.](https://docs.microsoft.com/powershell/module/msonlinebackup/new-obschedule) O calendário de cópias de segurança define quando é preciso receber cópias de segurança. Ao criar um horário, precisa especificar dois parâmetros de entrada:

* **Dias da semana** que o reforço deve correr. Podes fazer o trabalho de reserva num dia, ou todos os dias da semana, ou qualquer combinação entre eles.
* **As horas do dia** em que o reforço deve funcionar. Pode definir até três horas diferentes do dia quando a cópia de segurança será acionada.

Por exemplo, pode configurar uma política de backup que funciona às 16h todos os sábados e domingos.

```powershell
$Schedule = New-OBSchedule -DaysOfWeek Saturday, Sunday -TimesOfDay 16:00
```

O calendário de backup tem de ser associado a uma política, o que pode ser alcançado utilizando o cmdlet [Set-OBSchedule.](https://docs.microsoft.com/powershell/module/msonlinebackup/set-obschedule)

```powershell
Set-OBSchedule -Policy $NewPolicy -Schedule $Schedule
```

```Output
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s) DsList : PolicyName : RetentionPolicy : State : New PolicyState : Valid
```

### <a name="configuring-a-retention-policy"></a>Configurar uma política de retenção

A política de retenção define quanto tempo os pontos de recuperação criados a partir de postos de trabalho de reserva são mantidos. Ao criar uma nova política de retenção utilizando o cmdlet [New-OBRetentionPolicy,](https://docs.microsoft.com/powershell/module/msonlinebackup/new-obretentionpolicy) pode especificar o número de dias que os pontos de recuperação de backup precisam de ser mantidos com o Azure Backup. O exemplo abaixo define uma política de retenção de sete dias.

```powershell
$RetentionPolicy = New-OBRetentionPolicy -RetentionDays 7
```

A política de retenção deve ser associada à política principal utilizando a cmdlet [Set-OBRetentionPolicy:](https://docs.microsoft.com/powershell/module/msonlinebackup/set-obretentionpolicy)

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

Um `OBFileSpec` objeto define os ficheiros a incluir e excluídos numa cópia de segurança. Este é um conjunto de regras que traçam os ficheiros e pastas protegidos numa máquina. Pode ter o número de regras de inclusão ou exclusão de ficheiros necessárias e associá-las a uma política. Ao criar um novo objeto OBFileSpec, pode:

* Especifique os ficheiros e pastas a incluir
* Especifique os ficheiros e pastas a excluir
* Especifique a cópia de segurança recursiva dos dados numa pasta (ou) se apenas os ficheiros de nível superior na pasta especificada devem ser apoiados.

Este último é conseguido utilizando a bandeira -NonRecursive no comando New-OBFileSpec.

No exemplo abaixo, vamos apoiar o volume C: e D: e excluir os binários de OS na pasta Windows e quaisquer pastas temporárias. Para tal, criaremos duas especificações de ficheiro utilizando o cmdlet [New-OBFileSpec](https://docs.microsoft.com/powershell/module/msonlinebackup/new-obfilespec) - um para inclusão e outro para exclusão. Uma vez criadas as especificações de ficheiro, estão associadas à apólice utilizando o cmdlet [Add-OBFileSpec.](https://docs.microsoft.com/powershell/module/msonlinebackup/add-obfilespec)

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

Agora o objeto político está completo e tem um calendário de backup associado, política de retenção, e uma lista de ficheiros de inclusão/exclusão. Esta política pode agora ser comprometida para que o Azure Backup seja utilizado. Antes de aplicar a política recém-criada, certifique-se de que não existem políticas de backup associadas ao servidor utilizando o cmdlet [Remove-OBPolicy.](https://docs.microsoft.com/powershell/module/msonlinebackup/remove-obpolicy) A remoção da apólice será solicitada para confirmação. Para não obter a confirmação, utilize a `-Confirm:$false` bandeira com o cmdlet.

```powershell
Get-OBPolicy | Remove-OBPolicy
```

```Output
Microsoft Azure Backup Are you sure you want to remove this backup policy? This will delete all the backed up data. [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
```

A prática do objeto político é feita utilizando o cmdlet [Set-OBPolicy.](https://docs.microsoft.com/powershell/module/msonlinebackup/set-obpolicy) Isto também pedirá confirmação. Para não obter a confirmação, utilize a `-Confirm:$false` bandeira com o cmdlet.

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

Pode visualizar os detalhes da política de backup existente utilizando o cmdlet [Get-OBPolicy.](https://docs.microsoft.com/powershell/module/msonlinebackup/get-obpolicy) Pode perfurar ainda mais utilizando o cmdlet [Get-OBSchedule](https://docs.microsoft.com/powershell/module/msonlinebackup/get-obschedule) para a programação de backup e o [cmdlet Get-OBRetentionPolicy](https://docs.microsoft.com/powershell/module/msonlinebackup/get-obretentionpolicy) para as políticas de retenção

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

### <a name="performing-an-on-demand-backup"></a>Realizando um backup a pedido

Uma vez definida uma política de backup, os backups ocorrerão de acordo com o horário. O desencadear de uma cópia de segurança a pedido também é possível utilizando o cmdlet [Start-OBBackup:](https://docs.microsoft.com/powershell/module/msonlinebackup/start-obbackup)

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

## <a name="back-up-windows-server-system-state-in-mabs-agent"></a>Back up Windows Server System State in MABS agent

Esta secção cobre o comando PowerShell para configurar o System State no agente MABS

### <a name="schedule"></a>Agenda

```powershell
$sched = New-OBSchedule -DaysOfWeek Sunday,Monday,Tuesday,Wednesday,Thursday,Friday,Saturday -TimesOfDay 2:00
```

### <a name="retention"></a>Retenção

```powershell
$rtn = New-OBRetentionPolicy -RetentionDays 32 -RetentionWeeklyPolicy -RetentionWeeks 13 -WeekDaysOfWeek Sunday -WeekTimesOfDay 2:00  -RetentionMonthlyPolicy -RetentionMonths 13 -MonthDaysOfMonth 1 -MonthTimesOfDay 2:00
```

### <a name="configuring-schedule-and-retention"></a>Configuração do horário e da retenção

```powershell
New-OBPolicy | Add-OBSystemState |  Set-OBRetentionPolicy -RetentionPolicy $rtn | Set-OBSchedule -Schedule $sched | Set-OBSystemStatePolicy
 ```

### <a name="verifying-the-policy"></a>Verificação da política

```powershell
Get-OBSystemStatePolicy
 ```

## <a name="restore-data-from-azure-backup"></a>Restaurar os dados da Cópia de Segurança Azure

Esta secção irá guiá-lo através dos passos para automatizar a recuperação de dados do Azure Backup. Fazê-lo envolve os seguintes passos:

1. Escolha o volume de origem
2. Escolha um ponto de backup para restaurar
3. Especifique um item para restaurar
4. Desencadear o processo de restauro

### <a name="picking-the-source-volume"></a>Colhendo o volume de origem

Para restaurar um item da Azure Backup, primeiro é necessário identificar a origem do item. Uma vez que estamos a executar os comandos no contexto de um Servidor Windows ou de um cliente Windows, a máquina já está identificada. O próximo passo na identificação da fonte é identificar o volume que a contém. Uma lista de volumes ou fontes que estão a ser apoiadas a partir desta máquina pode ser recuperada executando o cmdlet [Get-OBRecoverableSource.](https://docs.microsoft.com/powershell/module/msonlinebackup/get-obrecoverablesource) Este comando devolve uma série de todas as fontes apoiadas a partir deste servidor/cliente.

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

### <a name="choosing-a-backup-point-from-which-to-restore"></a>Escolher um ponto de apoio a partir do qual restaurar

Você recupera uma lista de pontos de backup executando o [cmdlet Get-OBRecoverableItem](https://docs.microsoft.com/powershell/module/msonlinebackup/get-obrecoverableitem) com parâmetros apropriados. No nosso exemplo, vamos escolher o mais recente ponto de backup para o volume de origem *C:* e usá-lo para recuperar um ficheiro específico.

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

O objeto `$Rps` é uma variedade de pontos de apoio. O primeiro elemento é o último ponto e o elemento Nth é o ponto mais antigo. Para escolher o último ponto, `$Rps[0]` usaremos.

### <a name="specifying-an-item-to-restore"></a>Especificando um item para restaurar

Para restaurar um ficheiro específico, especifique o nome do ficheiro relativo ao volume raiz. Por exemplo, para recuperar C:\Test\Cat.job, execute o seguinte comando.

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

Para desencadear o processo de restauro, primeiro precisamos especificar as opções de recuperação. Isto pode ser feito utilizando o cmdlet [New-OBRecoveryOption.](https://docs.microsoft.com/powershell/module/msonlinebackup/new-obrecoveryoption) Para este exemplo, vamos assumir que queremos restaurar os ficheiros para *C:\temp*. Vamos também assumir que queremos ignorar ficheiros que já existem na pasta de destino *C:\temp*. Para criar tal opção de recuperação, utilize o seguinte comando:

```powershell
$RecoveryOption = New-OBRecoveryOption -DestinationPath "C:\temp" -OverwriteType Skip
```

Agora inicie o processo de restauro utilizando o comando [Start-OBRecovery](https://docs.microsoft.com/powershell/module/msonlinebackup/start-obrecovery) no selecionado `$Item` a partir da saída do `Get-OBRecoverableItem` cmdlet:

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

## <a name="uninstalling-the-azure-backup-agent"></a>Desinstalação do agente de backup Azure

A desinstalação do agente de reserva Azure pode ser feita utilizando o seguinte comando:

```powershell
.\MARSAgentInstaller.exe /d /q
```

Desinstalar os binários do agente da máquina tem algumas consequências a considerar:

* Remove o filtro de ficheiros da máquina e o rastreio das alterações é interrompido.
* Todas as informações sobre a política são removidas da máquina, mas a informação da política continua a ser armazenada no serviço.
* Todos os horários de reserva são removidos, e não são tomadas mais cópias de segurança.

No entanto, os dados armazenados em Azure permanecem e são mantidos de acordo com a política de retenção configurada por si. Os pontos mais antigos são automaticamente envelhecidos.

## <a name="remote-management"></a>Gestão remota

Toda a gestão em torno do agente de backup Azure, políticas e fontes de dados podem ser feitas remotamente através do PowerShell. A máquina que será gerida remotamente precisa de ser preparada corretamente.

Por predefinição, o serviço WinRM está configurado para arranque manual. O tipo de arranque deve ser definido para *Automático* e o serviço deve ser iniciado. Para verificar se o serviço WinRM está em funcionamento, o valor da propriedade Status deve estar *em execução.*

```powershell
Get-Service -Name WinRM
```

```Output
Status   Name               DisplayName
------   ----               -----------
Running  winrm              Windows Remote Management (WS-Manag...
```

PowerShell deve ser configurado para remo.

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

A máquina pode agora ser gerida remotamente - a partir da instalação do agente. Por exemplo, o seguinte guião copia o agente para a máquina remota e instala-o.

```powershell
$DLoc = "\\REMOTESERVER01\c$\Windows\Temp"
$Agent = "\\REMOTESERVER01\c$\Windows\Temp\MARSAgentInstaller.exe"
$Args = "/q"
Copy-Item "C:\Downloads\MARSAgentInstaller.exe" -Destination $DLoc -Force

$Session = New-PSSession -ComputerName REMOTESERVER01
Invoke-Command -Session $Session -Script { param($D, $A) Start-Process -FilePath $D $A -Wait } -ArgumentList $Agent, $Args
```

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre o Backup Azure para Windows Server/Cliente:

* [Introdução ao Azure Backup](backup-introduction-to-azure-backup.md)
* [Back up Windows Servers](backup-windows-with-mars-agent.md)
