---
title: Utilizar o PowerShell para criar cópias de segurança do Windows Server para o Azure
description: Saiba como implementar e gerir cópias de segurança do Azure com o PowerShell
services: backup
author: pvrk
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 5/24/2018
ms.author: pvrk
ms.openlocfilehash: 0a7a16a43b208bf2d14b86cd5cb23544ec03f9a9
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57877535"
---
# <a name="deploy-and-manage-backup-to-azure-for-windows-serverwindows-client-using-powershell"></a>Implementar e gerir cópias de segurança para o Azure para o Windows Server/cliente Windows com o PowerShell
Este artigo mostra-lhe como utilizar o PowerShell para configurar o Azure Backup no Windows Server ou um cliente do Windows e gestão de cópia de segurança e recuperação.

## <a name="install-azure-powershell"></a>Instalar o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para começar, [de instalar a versão mais recente do PowerShell](/powershell/azure/install-az-ps).

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos serviços de recuperação
Os seguintes passos levá-lo através da criação de um cofre dos serviços de recuperação. Um cofre dos serviços de recuperação é diferente de um cofre de cópia de segurança.

1. Se estiver a utilizar o Azure Backup pela primeira vez, tem de utilizar o **Register-AzResourceProvider** cmdlet para registar o fornecedor de serviços de recuperação do Azure com a sua subscrição.

    ```
    PS C:\> Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
2. O Cofre dos serviços de recuperação é um recurso do ARM, por isso terá de colocá-lo dentro de um grupo de recursos. Pode utilizar um grupo de recursos existente ou crie um novo. Ao criar um novo grupo de recursos, especifique o nome e local para o grupo de recursos.  

    ```
    PS C:\> New-AzResourceGroup –Name "test-rg" –Location "WestUS"
    ```
3. Utilize o **New-AzRecoveryServicesVault** cmdlet para criar o novo cofre. Certifique-se de que especifique a mesma localização do cofre que foi utilizado para o grupo de recursos.

    ```
    PS C:\> New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "WestUS"
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

Execute o comando **Get-AzRecoveryServicesVault**, e a subscrição de todos os cofres estão listados.

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


[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="installing-the-azure-backup-agent"></a>Instalar o agente de cópia de segurança do Azure
Antes de instalar o agente de cópia de segurança do Azure, tem de ter o instalador transferido e presente no Windows Server. Pode obter a versão mais recente do programa de instalação do [Microsoft Download Center](https://aka.ms/azurebackup_agent) ou a partir da página do Dashboard do Cofre de serviços de recuperação. Guardar o instalador para uma localização facilmente acessível como * C:\Downloads\*.

Em alternativa, utilize o PowerShell para obter o dispositivo de transferência da:
 
 ```
 $MarsAURL = 'Http://Aka.Ms/Azurebackup_Agent'
 $WC = New-Object System.Net.WebClient
 $WC.DownloadFile($MarsAURL,'C:\downloads\MARSAgentInstaller.EXE')
 C:\Downloads\MARSAgentInstaller.EXE /q
 ```

Para instalar o agente, execute o seguinte comando numa consola elevada do PowerShell:

```
PS C:\> MARSAgentInstaller.exe /q
```

Esta ação instala o agente com todas as opções padrão. A instalação demora alguns minutos, em segundo plano. Se não especificar a */nu* opção, em seguida, o **Windows Update** será aberta a janela no final da instalação para verificar a existência de quaisquer atualizações. Uma vez instalado, o agente irá mostrar na lista de programas instalados.

Para ver a lista de programas instalados, aceda a **painel de controlo** > **programas** > **programas e funcionalidades**.

![Agente instalado](./media/backup-client-automation/installed-agent-listing.png)

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

## <a name="registering-windows-server-or-windows-client-machine-to-a-recovery-services-vault"></a>Registar o Windows Server ou o computador de cliente do Windows para um cofre dos serviços de recuperação
Depois de criado o Cofre dos serviços de recuperação, transfira o agente mais recente e as credenciais do cofre e armazene-o numa localização conveniente como C:\Downloads.

```
PS C:\> $credspath = "C:\downloads"
PS C:\> $credsfilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $vault1 -Path  $credspath
```

No Windows Server ou na máquina de cliente do Windows, execute o [Start-OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx) cmdlet para registar a máquina com o cofre.
Este e outros cmdlets utilizados para cópia de segurança, são do módulo MSONLINE que o Mars AgentInstaller adicionada como parte do processo de instalação. 

O instalador do agente não atualiza o $Env: PSModulePath variável. Isso significa que o carregamento automático do módulo falha. Para resolver este problema pode fazer o seguinte:

```
PS C:\>  $Env:psmodulepath += ';C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules'
```

Em alternativa, pode carregar manualmente o módulo no seu script da seguinte forma:

```
PS C:\>  Import-Module  'C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup'

```

Depois de carregar os cmdlets de cópia de segurança Online, registra as credenciais do cofre:


```

PS C:\> Start-OBRegistration -VaultCredentials $credsfilename.FilePath -Confirm:$false
CertThumbprint      :7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName: testvault
Region              :WestUS
Machine registration succeeded.
```

> [!IMPORTANT]
> Não utilize caminhos relativos para especificar o ficheiro de credenciais do cofre. Tem de fornecer um caminho absoluto como entrada para o cmdlet.
>
>

## <a name="networking-settings"></a>Definições de rede
Quando a conectividade da máquina Windows à internet através de um servidor proxy, as definições de proxy também podem ser fornecidas para o agente. Neste exemplo, não existe nenhum servidor de proxy, portanto, o são explicitamente a limpar todas as informações relacionadas com o proxy.

Também pode ser controlada a utilização de largura de banda com as opções de ```work hour bandwidth``` e ```non-work hour bandwidth``` para um determinado conjunto de dias da semana.

Definir os detalhes de proxy e de largura de banda é feito com o [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409%28v=wps.630%29.aspx) cmdlet:

```
PS C:\> Set-OBMachineSetting -NoProxy
Server properties updated successfully.

PS C:\> Set-OBMachineSetting -NoThrottle
Server properties updated successfully.
```

## <a name="encryption-settings"></a>Definições de encriptação
Os dados de cópia de segurança enviados para o Azure Backup são encriptados para proteger a confidencialidade dos dados. A frase de acesso de encriptação é a "palavra-passe" para descriptografar os dados no momento do restauro.

```
PS C:\> ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force | Set-OBMachineSetting
PS C:\> $PassPhrase = ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force 
PS C:\> $PassCode   = 'AzureR0ckx'
PS C:\> Set-OBMachineSetting -EncryptionPassPhrase $PassPhrase
Server properties updated successfully
```

> [!IMPORTANT]
> Manter as informações de frase de acesso seguro e fiável, depois de definida. Não é possível restaurar os dados do Azure sem esta frase de acesso.
>
>

## <a name="back-up-files-and-folders"></a>Faça uma cópia de segurança de ficheiros e pastas
Todas as cópias de segurança de clientes e servidores Windows para o Azure Backup são regidas por uma política. A política é composto por três partes:

1. R **agenda de cópia de segurança** que especifica quando as cópias de segurança devem ser tomadas e sincronizados com o serviço.
2. R **agenda de retenção** que especifica o período de tempo para reter os pontos de recuperação no Azure.
3. R **especificação de inclusão/exclusão de ficheiros** que determina o que devem ser submetido a backup.

Neste documento, uma vez que estamos automatizando cópia de segurança, vamos supor que nada tenha sido configurado. Começamos criando uma nova política de cópia de segurança com o [New-OBPolicy](https://technet.microsoft.com/library/hh770416.aspx) cmdlet.

```
PS C:\> $newpolicy = New-OBPolicy
```

Neste momento, a política está vazia e outros cmdlets são necessitam para definir a quais itens que serão incluídos ou excluídos, quando as cópias de segurança será executada, e onde serão armazenadas as cópias de segurança.

### <a name="configuring-the-backup-schedule"></a>Configurar a agenda de cópia de segurança
A primeira das 3 partes de uma política é a agenda de cópia de segurança, que é criada com o [New-OBSchedule](https://technet.microsoft.com/library/hh770401) cmdlet. A agenda de cópia de segurança define quando precisam ser levadas cópias de segurança. Ao criar uma agenda que tem de especificar parâmetros de entrada 2:

* **Dias da semana** que a cópia de segurança deve ser executada. Pode executar a tarefa de cópia de segurança em apenas um dia, ou todos os dias da semana, ou qualquer combinação entre.
* **Horas do dia** quando a cópia de segurança deve ser executada. Pode definir até 3 horas diferentes do dia quando a cópia de segurança será acionada.

Por exemplo, é possível configurar uma política de cópia de segurança que é executada às 16:00 cada Sábado e Domingo.

```
PS C:\> $sched = New-OBSchedule -DaysofWeek Saturday, Sunday -TimesofDay 16:00
```

A agenda de cópia de segurança tem de ser associado a uma política, e isso pode ser obtido utilizando o [Set-OBSchedule](https://technet.microsoft.com/library/hh770407) cmdlet.

```
PS C:> Set-OBSchedule -Policy $newpolicy -Schedule $sched
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s) DsList : PolicyName : RetentionPolicy : State : New PolicyState : Valid
```
### <a name="configuring-a-retention-policy"></a>Configurar uma política de retenção
A política de retenção define quanto tempo os pontos de recuperação criados a partir de cópias de segurança são retidos. Ao criar uma nova política de retenção utilizando o [New-OBRetentionPolicy](https://technet.microsoft.com/library/hh770425) cmdlet, pode especificar o número de dias que os pontos de recuperação de cópia de segurança têm de ser mantidos com o Azure Backup. O exemplo abaixo define uma política de retenção de 7 dias.

```
PS C:\> $retentionpolicy = New-OBRetentionPolicy -RetentionDays 7
```

A política de retenção tem de estar associada com a política de principal com o cmdlet [Set-OBRetentionPolicy](https://technet.microsoft.com/library/hh770405):

```
PS C:\> Set-OBRetentionPolicy -Policy $newpolicy -RetentionPolicy $retentionpolicy

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
### <a name="including-and-excluding-files-to-be-backed-up"></a>Incluir e excluir os ficheiros de cópia de segurança
Um ```OBFileSpec``` objeto define os ficheiros a ser incluído e excluído numa cópia de segurança. Este é um conjunto de regras que definir o âmbito os ficheiros protegidos e pastas num computador. Pode ter como muitas regras de inclusão ou exclusão, conforme necessário de ficheiros e associá-los a uma política. Ao criar um novo objeto de OBFileSpec, pode:

* Especificar os ficheiros e pastas a serem incluídos
* Especificar os ficheiros e pastas a excluir
* Especifique recursiva a cópia de segurança de dados numa pasta (ou) se devem ser submetidos a apenas os ficheiros de nível superior na pasta especificada a cópia de segurança.

O último é obtido utilizando o sinalizador - NonRecursive no comando New-OBFileSpec.

No exemplo a seguir, vamos criar cópias de segurança volume c: e d: e excluir os binários do sistema operacional na pasta Windows e todas as pastas temporárias. Para fazê-lo, vamos criar dois ficheiros especificações utilizando o [New-OBFileSpec](https://technet.microsoft.com/library/hh770408) cmdlet - um para inclusão e outro para exclusão. Quando as especificações de ficheiro tem sido criadas, eles são associados com a política a utilizar o [Add-OBFileSpec](https://technet.microsoft.com/library/hh770424) cmdlet.

```
PS C:\> $inclusions = New-OBFileSpec -FileSpec @("C:\", "D:\")

PS C:\> $exclusions = New-OBFileSpec -FileSpec @("C:\windows", "C:\temp") -Exclude

PS C:\> Add-OBFileSpec -Policy $newpolicy -FileSpec $inclusions

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


PS C:\> Add-OBFileSpec -Policy $newpolicy -FileSpec $exclusions

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

### <a name="applying-the-policy"></a>Aplicar a política
Agora, o objeto de política está concluído e tem uma agenda de cópia de segurança associada, a política de retenção e uma lista de inclusão/exclusão de ficheiros. Agora pode ser consolidada para o Azure Backup utilizar esta política. Antes de aplicar a política criada recentemente Certifique-se de que não existem não existem políticas de cópia de segurança existentes associadas ao servidor utilizando o [Remove-OBPolicy](https://technet.microsoft.com/library/hh770415) cmdlet. Remover a política irá pedir confirmação. Para ignorar a utilização de confirmação a ```-Confirm:$false``` sinalizador com o cmdlet.

```
PS C:> Get-OBPolicy | Remove-OBPolicy
Microsoft Azure Backup Are you sure you want to remove this backup policy? This will delete all the backed up data. [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
```

Ao consolidar o objeto de política é feito com o [Set-OBPolicy](https://technet.microsoft.com/library/hh770421) cmdlet. Isso solicitará também confirmação. Para ignorar a utilização de confirmação a ```-Confirm:$false``` sinalizador com o cmdlet.

```
PS C:> Set-OBPolicy -Policy $newpolicy
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

Pode ver os detalhes da política de cópia de segurança a existente, utilizando o [Get-OBPolicy](https://technet.microsoft.com/library/hh770406) cmdlet. Pode fazer uma busca detalhada utilizando o [Get-OBSchedule](https://technet.microsoft.com/library/hh770423) cmdlet para a agenda de cópia de segurança e o [Get-OBRetentionPolicy](https://technet.microsoft.com/library/hh770427) cmdlet para as políticas de retenção

```
PS C:> Get-OBPolicy | Get-OBSchedule
SchedulePolicyName : 71944081-9950-4f7e-841d-32f0a0a1359a
ScheduleRunDays : {Saturday, Sunday}
ScheduleRunTimes : {16:00:00}
State : Existing

PS C:> Get-OBPolicy | Get-OBRetentionPolicy
RetentionDays : 7
RetentionPolicyName : ca3574ec-8331-46fd-a605-c01743a5265e
State : Existing

PS C:> Get-OBPolicy | Get-OBFileSpec
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

### <a name="performing-an-ad-hoc-backup"></a>Efetuar uma cópia de segurança ad hoc
Assim que tiver sido definida uma política de cópia de segurança as cópias de segurança irão ocorrer pelo agendamento. Acionar uma cópia de segurança ad hoc é também possível utilizar o [Start-OBBackup](https://technet.microsoft.com/library/hh770426) cmdlet:

```
PS C:> Get-OBPolicy | Start-OBBackup
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

## <a name="restore-data-from-azure-backup"></a>Restaurar dados a partir de cópia de segurança do Azure
Esta secção descreve os passos para automatizar a recuperação de dados de cópia de segurança do Azure. Ao fazer isso envolve os seguintes passos:

1. Escolha o volume de origem
2. Escolha um ponto de cópia de segurança para restaurar
3. Selecione um item para restaurar
4. Acionar o processo de restauro

### <a name="picking-the-source-volume"></a>Escolher o volume de origem
Para restaurar um item de cópia de segurança do Azure, terá primeiro identificar a origem do item. Uma vez que o está executando os comandos no contexto de um servidor Windows ou um cliente Windows, a máquina já é identificada. A próxima etapa na identificação de origem é identificar o volume que o contém. Uma lista de volumes ou origens a cópia de segurança desta máquina pode ser obtida ao executar o [Get-OBRecoverableSource](https://technet.microsoft.com/library/hh770410) cmdlet. Este comando devolve uma matriz de todas as origens de cópia de segurança deste servidor/cliente.

```
PS C:> $source = Get-OBRecoverableSource
PS C:> $source
FriendlyName : C:\
RecoverySourceName : C:\
ServerName : myserver.microsoft.com

FriendlyName : D:\
RecoverySourceName : D:\
ServerName : myserver.microsoft.com
```

### <a name="choosing-a-backup-point-from-which-to-restore"></a>Escolher um ponto de cópia de segurança de que pretende restaurar
Obter uma lista de pontos de cópia de segurança ao executar o [Get-OBRecoverableItem](https://technet.microsoft.com/library/hh770399.aspx) cmdlet com parâmetros apropriados. No nosso exemplo, vamos escolher o ponto de cópia de segurança mais recente para o volume de origem *D:* e utilizá-lo a recuperar um arquivo específico.

```
PS C:> $rps = Get-OBRecoverableItem -Source $source[1]
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
O objeto ```$rps``` é uma matriz de pontos de cópia de segurança. O primeiro elemento é o ponto mais recente e o elemento enésimo é o ponto mais antigo. Para escolher o ponto mais recente, utilizamos ```$rps[0]```.

### <a name="choosing-an-item-to-restore"></a>Escolher um item para restaurar
Para identificar o exato ficheiro ou pasta para restaurar, utilize recursivamente a [Get-OBRecoverableItem](https://technet.microsoft.com/library/hh770399.aspx) cmdlet. Dessa forma, a hierarquia de pastas pode ser pesquisada utilizando unicamente o ```Get-OBRecoverableItem```.

Neste exemplo, se quisermos restaurar o ficheiro *finances.xls* pode fazer referência a ele usando o objeto ```$filesFolders[1]```.

```
PS C:> $filesFolders = Get-OBRecoverableItem $rps[0]
PS C:> $filesFolders
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

PS C:> $filesFolders = Get-OBRecoverableItem $filesFolders[0]
PS C:> $filesFolders
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

Também pode procurar itens restaurar com a ```Get-OBRecoverableItem``` cmdlet. No nosso exemplo, para procurar *finances.xls* , foi possível obter um identificador no ficheiro ao executar este comando:

```
PS C:\> $item = Get-OBRecoverableItem -RecoveryPoint $rps[0] -Location "D:\MyData" -SearchString "finance*"
```

### <a name="triggering-the-restore-process"></a>Acionar o processo de restauro
Para acionar o processo de restauração, primeiro é necessário especificar as opções de recuperação. Isso pode ser feito utilizando o [New-OBRecoveryOption](https://technet.microsoft.com/library/hh770417.aspx) cmdlet. Neste exemplo, vamos supor que queremos restaurar os ficheiros para *C:\temp*. Vamos também supor que queremos ignorar ficheiros que já existem na pasta de destino *C:\temp*. Para criar uma opção de recuperação, utilize o seguinte comando:

```
PS C:\> $recovery_option = New-OBRecoveryOption -DestinationPath "C:\temp" -OverwriteType Skip
```

Acionar agora o processo de restauro utilizando o [Start-OBRecovery](https://technet.microsoft.com/library/hh770402.aspx) comando selecionado ```$item``` da saída do ```Get-OBRecoverableItem``` cmdlet:

```
PS C:\> Start-OBRecovery -RecoverableItem $item -RecoveryOption $recover_option
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Job completed.
The recovery operation completed successfully.
```


## <a name="uninstalling-the-azure-backup-agent"></a>Desinstalação do agente de cópia de segurança do Azure
Desinstalação do agente de cópia de segurança do Azure pode ser feito usando o seguinte comando:

```
PS C:\> .\MARSAgentInstaller.exe /d /q
```

Desinstalar os binários do agente da máquina tem algumas conseqüências a considerar:

* Remove o filtro de ficheiros da máquina e rastreamento de alterações está parado.
* Todas as informações de política são removidas da máquina, mas as informações de política continua a ser armazenados no serviço.
* Todas as agendas de cópia de segurança são removidas e não existem cópias de segurança mais direcionadas.

No entanto, os dados armazenados no Azure permanece e são mantidos, de acordo com a configuração de política de retenção, por si. Pontos mais antigos são automaticamente desatualizados.

## <a name="remote-management"></a>Gestão remota
Todo o gerenciamento em todo o agente de cópia de segurança do Azure, políticas e origens de dados pode ser feito remotamente através do PowerShell. O computador que irá ser gerido remotamente precisa estar preparado corretamente.

Por predefinição, o serviço WinRM está configurado para arranque manual. O tipo de arranque tem de ser definido *automática* e o serviço deve ser iniciado. Para verificar que o serviço WinRM está em execução, o valor da propriedade de estado deve ser *em execução*.

```
PS C:\> Get-Service WinRM

Status   Name               DisplayName
------   ----               -----------
Running  winrm              Windows Remote Management (WS-Manag...
```

PowerShell deve ser configurado para comunicação remota.

```
PS C:\> Enable-PSRemoting -force
WinRM is already set up to receive requests on this computer.
WinRM has been updated for remote management.
WinRM firewall exception enabled.

PS C:\> Set-ExecutionPolicy unrestricted -force
```

A máquina pode agora ser gerida remotamente - a partir da instalação do agente. Por exemplo, o script seguinte copia o agente para o computador remoto e instala-o.

```
PS C:\> $dloc = "\\REMOTESERVER01\c$\Windows\Temp"
PS C:\> $agent = "\\REMOTESERVER01\c$\Windows\Temp\MARSAgentInstaller.exe"
PS C:\> $args = "/q"
PS C:\> Copy-Item "C:\Downloads\MARSAgentInstaller.exe" -Destination $dloc - force

PS C:\> $s = New-PSSession -ComputerName REMOTESERVER01
PS C:\> Invoke-Command -Session $s -Script { param($d, $a) Start-Process -FilePath $d $a -Wait } -ArgumentList $agent $args
```

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre o veja de Azure Backup para Windows Server/cliente Windows

* [Introdução ao Azure Backup](backup-introduction-to-azure-backup.md)
* [Fazer backup de servidores do Windows](backup-configure-vault.md)
