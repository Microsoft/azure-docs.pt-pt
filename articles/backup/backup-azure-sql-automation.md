---
title: SQL DB em Azure VM backup & restaurar via PowerShell
description: Faça backup e restaure bases de dados SQL em VMs Azure usando Azure Backup e PowerShell.
ms.topic: conceptual
ms.date: 03/15/2019
ms.assetid: 57854626-91f9-4677-b6a2-5d12b6a866e1
ms.openlocfilehash: 0b3b943a53c1da0f6f1e938b5b234dc82541b46d
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92901672"
---
# <a name="back-up-and-restore-sql-databases-in-azure-vms-with-powershell"></a>Fazer o back up e restaurar as bases de dados SQL em VMs Azure com PowerShell

Este artigo descreve como usar a Azure PowerShell para fazer backup e recuperar um SQL DB dentro de um Azure VM usando o cofre dos Serviços de Recuperação [de Backup Azure.](backup-overview.md)

Este artigo explica como:

> [!div class="checklist"]
>
> * Confiúdo PowerShell e registe o Fornecedor de Serviços de Recuperação Azure.
> * Crie um cofre dos Serviços de Recuperação.
> * Configure a cópia de segurança para o SQL DB dentro de um Azure VM.
> * Faça um trabalho de reserva.
> * Restaurar um SQL DB apoiado.
> * Monitorizem os trabalhos de apoio e restaurámos os trabalhos.

## <a name="before-you-start"></a>Antes de começar

* [Saiba mais](backup-azure-recovery-services-vault-overview.md) sobre cofres dos Serviços de Recuperação.
* Leia sobre as capacidades de funcionalidade para [fazer backup de DBs SQL dentro de VMs Azure](backup-azure-sql-database.md#before-you-start).
* Reveja a hierarquia de objetos PowerShell para serviços de recuperação.

### <a name="recovery-services-object-hierarchy"></a>Serviços de Recuperação hierárquica de objetos

A hierarquia do objeto é resumida no diagrama seguinte.

![Serviços de Recuperação hierárquica de objetos](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Reveja a referência [de referência cmdlet](/powershell/module/az.recoveryservices) **Az.RecoveryServices** na biblioteca Azure.

### <a name="set-up-and-install"></a>Configurar e instalar

Configurar o PowerShell da seguinte forma:

1. [Descarregue a versão mais recente do Az PowerShell.](/powershell/azure/install-az-ps) A versão mínima necessária é de 1.5.0.

2. Encontre os cmdlets PowerShell de backup Azure com este comando:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Reveja os pseudónimos e cmdlets para Azure Backup e o cofre dos Serviços de Recuperação. Aqui está um exemplo do que pode ver. Não é uma lista completa de comandantes.

    ![Lista de serviços de recuperação](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Inscreva-se na sua conta Azure com **o Connect-AzAccount** .
5. Na página web que aparece, é solicitado que insira as credenciais da sua conta.

    * Alternadamente, pode incluir as suas credenciais de conta como parâmetro no cmdlet **Connect-AzAccount** com **-Credencial** .
    * Se você é um parceiro CSP que trabalha para um inquilino, especifique o cliente como inquilino, usando o seu nome de domínio principal inquilino ou inquilino. Um exemplo é **Connect-AzAccount -Tenant** fabrikam.com.

6. Associe a subscrição que pretende utilizar na conta, porque uma conta pode ter várias subscrições.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Se estiver a utilizar o Azure Backup pela primeira vez, utilize o **cmdlet Register-AzResourceProvider** para registar o fornecedor de Serviços de Recuperação Azure com a sua subscrição.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. Verifique se os fornecedores se registaram com sucesso:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. Na saída de comando, verifique se o **Estado de Registo** altera as alterações **registadas** . Se não o fizer, volte a executar o **cmdlet Register-AzResourceProvider.**

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 

Siga estes passos para criar um cofre dos Serviços de Recuperação.

O cofre dos Serviços de Recuperação é um recurso do Gestor de Recursos, pelo que deve colocá-lo dentro de um grupo de recursos. Pode utilizar um grupo de recursos existente, ou pode criar um grupo de recursos com o cmdlet **New-AzResourceGroup.** Quando criar um grupo de recursos, especifique o nome e a localização do grupo de recursos.

1. Um cofre é colocado num grupo de recursos. Se não tiver um grupo de recursos existente, crie um novo com o [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Neste exemplo, criamos um novo grupo de recursos na região oeste dos EUA.

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. Utilize o [cmdlet New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) para criar o cofre. Especifique o mesmo local para o cofre que foi usado para o grupo de recursos.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Especifique o tipo de redundância a utilizar para o armazenamento do cofre.

    * Pode utilizar [armazenamento localmente redundante,](../storage/common/storage-redundancy.md#locally-redundant-storage) [armazenamento geo-redundante](../storage/common/storage-redundancy.md#geo-redundant-storage) ou [armazenamento redundante de zona.](../storage/common/storage-redundancy.md#zone-redundant-storage)
    * O exemplo a seguir define a opção **-BackupStorageRedundancy** para o [Set-AzRecoveryServicesBackupProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) cmd para **testvault** definido para **GeoRedundant** .

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

### <a name="view-the-vaults-in-a-subscription"></a>Ver os cofres numa subscrição

Para visualizar todos os cofres da subscrição, utilize [o Get-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/get-azrecoveryservicesvault).

```powershell
Get-AzRecoveryServicesVault
```

A saída é semelhante à seguinte. O grupo de recursos associado e a localização são fornecidos.

```output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>Desa parte para o contexto do cofre

Guarde o objeto do cofre numa variável e desaje no contexto do cofre.

* Muitos cmdlets de backup Azure requerem o objeto do cofre dos Serviços de Recuperação como uma entrada, por isso é conveniente armazenar o objeto do cofre numa variável.
* O contexto do cofre é o tipo de dados protegidos no cofre. Desaperte-o com [o Set-AzRecoveryServicesVaultContext](/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext). Após o contexto definido, aplica-se a todos os cmdlets subsequentes.

O exemplo a seguir define o contexto do cofre para **o testvault** .

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Pegue a iD do cofre

Planeamos depreciar a definição do contexto do cofre de acordo com as diretrizes da Azure PowerShell. Em vez disso, pode armazenar ou buscar a ID do cofre e passá-la para comandos relevantes, da seguinte forma:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-a-backup-policy"></a>Configure uma política de backup

Uma política de backup especifica o calendário para backups e quanto tempo devem ser mantidos pontos de recuperação de backup:

* Uma política de apoio está associada a pelo menos uma política de retenção. Uma política de retenção define quanto tempo um ponto de recuperação é mantido antes de ser eliminado.
* Consulte a retenção de política de backup padrão usando [Get-AzRecoveryServicesBackupRetentionPolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject).
* Consulte o calendário de política de backup predefinido usando [Get-AzRecoveryServicesBackupSchedulePolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject).
* Utiliza o [cmdlet New-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy) para criar uma nova política de backup. Introduz os objetos de política de programação e retenção.

Por predefinição, é definida uma hora de início no Objeto de Política de Agendamento. Utilize o exemplo a seguir para alterar a hora de início para a hora de início desejada. A hora de início desejada também deve estar na UTC. O exemplo a seguir pressupõe que a hora de início desejada é 01:00 AM UTC para backups diários.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> Só precisa de fornecer a hora de início em múltiplos de 30 minutos. No exemplo acima, pode ser apenas "01:00:00" ou "02:30:00". A hora de início não pode ser "01:15:00".

O exemplo a seguir armazena a política de agendamento e a política de retenção em variáveis. Em seguida, utiliza essas variáveis como parâmetros para uma nova política **(NewSQLPolicy).** **NewSQLPolicy** recebe uma cópia de segurança diária "Full", retém-na durante 180 dias e faz uma cópia de segurança a cada 2 horas

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "MSSQL"
$NewSQLPolicy = New-AzRecoveryServicesBackupProtectionPolicy -Name "NewSQLPolicy" -WorkloadType "MSSQL" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

A saída é semelhante à seguinte.

```output
Name                 WorkloadType       BackupManagementType BackupTime                Frequency                                IsDifferentialBackup IsLogBackupEnabled
                                                                                                                                Enabled
----                 ------------       -------------------- ----------                ---------                                -------------------- ------------------
NewSQLPolicy         MSSQL              AzureWorkload        3/15/2019 01:30:00 AM      Daily                                    False                True
```

## <a name="enable-backup"></a>Ativar backup

### <a name="registering-the-sql-vm"></a>Registo do SQL VM

Para cópias de segurança Azure VM e ações do Azure File, o serviço de backup pode ligar-se a estes recursos do Azure Resource Manager e obter os detalhes relevantes. Uma vez que o SQL é uma aplicação dentro de um Azure VM, o serviço de backup precisa de permissão para aceder à aplicação e obter os detalhes necessários. Para isso, é necessário *"registar"* o Azure VM que contém a aplicação SQL com um cofre dos Serviços de Recuperação. Uma vez registado um SQL VM com um cofre, pode proteger os SQL DBs apenas para aquele cofre. Utilize [o Cmdlet Register-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/register-azrecoveryservicesbackupcontainer) PowerShell para registar o VM.

```powershell
 $myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```

O comando devolverá um 'contentor de reserva' deste recurso e o estado será 'registado'

> [!NOTE]
> Se o parâmetro de força não for dado, é-lhe pedido que confirme com um texto "Deseja desativar a proteção deste recipiente". Por favor, ignore este texto e diga "Y" para confirmar. Este é um problema conhecido e estamos trabalhando para remover o texto e a exigência para o parâmetro de força.

### <a name="fetching-sql-dbs"></a>Buscar DBs SQL

Uma vez feito o registo, o serviço de backup poderá listar todos os componentes SQL disponíveis dentro do VM. Para visualizar todos os componentes SQL ainda a ser apoiados até este cofre use [Get-AzRecoveryServicesBackupProtectableItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectableitem) PowerShell cmdlet

```powershell
Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -VaultId $targetVault.ID
```

A saída mostrará todos os componentes SQL desprotegidos em todos os VMs SQL registados neste cofre com Item Type e ServerName. Pode filtrar ainda mais um VM SQL específico, passando o parâmetro '-Contentor' ou utilizar a combinação de 'Nome' e 'Nome do Servidor' juntamente com a bandeira itemType para chegar a um item SQL único.

```powershell
$SQLDB = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID -Name "<Item Name>" -ServerName "<Server Name>"
```

### <a name="configuring-backup"></a>Configurar backup

Agora que temos o DB SQL necessário e a política com a qual precisa de ser apoiado, podemos usar o [cmdlet Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) para configurar a cópia de segurança para este DB SQL.

```output
Enable-AzRecoveryServicesBackupProtection -ProtectableItem $SQLDB -Policy $NewSQLPolicy
```

O comando aguarda até que a cópia de segurança de configuração esteja concluída e retorne a seguinte saída.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 6:00:21 PM      3/18/2019 6:01:35 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="fetching-new-sql-dbs"></a>Buscar novos DBs SQL

Assim que a máquina estiver registada, o serviço de backup irá buscar os detalhes dos DBs disponíveis. Se os DBs SQL ou as instâncias SQL forem adicionados à máquina registada mais tarde, é necessário acionar manualmente o serviço de cópia de segurança para efetuar um novo 'inquérito' para que **todos os** DBs desprotegidos (incluindo os recém-adicionados) voltem a ser adicionados. Utilize o cmdlet [Initialize-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/initialize-azrecoveryservicesbackupprotectableitem) PowerShell no SQL VM para efetuar um novo inquérito. O comando aguarda até que a operação esteja concluída. Utilize mais tarde o [cmdlet Get-AzRecoveryServicesBackupProtectableItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectableitem) PowerShell para obter a lista dos mais recentes componentes SQL desprotegidos.

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Initialize-AzRecoveryServicesBackupProtectableItem -Container $SQLContainer -WorkloadType MSSQL -VaultId $targetvault.ID
Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID
```

Assim que forem recolhidos os itens protectíveis relevantes, ative as cópias de segurança conforme indicado na [secção acima](#configuring-backup).
Se não quiser detetar manualmente novos DBs, pode optar pela autoproteção, como explicado [abaixo](#enable-autoprotection).

## <a name="enable-autoprotection"></a>Ativar a autoproteção

Pode configurar a cópia de segurança para que todos os DBs adicionados no futuro sejam automaticamente protegidos com uma determinada política. Para ativar a autoproteção, utilize [o comandante Enable-AzRecoveryServicesBackupAutoProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupautoprotection) PowerShell.

Uma vez que a instrução é para apoiar todos os futuros DBs, a operação é feita a um nível DE SQLInstance.

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Enable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -Policy $NewSQLPolicy -VaultId $targetvault.ID
```

Uma vez dada a intenção de autoproteção, o inquérito à máquina para obter DBs recém-adicionados ocorre como uma tarefa de fundo programada a cada 8 horas.

## <a name="restore-sql-dbs"></a>Restaurar DBs SQL

O Azure Backup pode restaurar as bases de dados do SQL Server que estão a ser executadas em VMs Azure da seguinte forma:

* Restaurar a data ou hora específicas (para a segunda) utilizando cópias de segurança de registo de transações. O Azure Backup determina automaticamente a cópia de segurança diferencial adequada e a cadeia de backups de registos que são necessárias para restaurar com base no tempo selecionado.
* Restaurar uma cópia de segurança completa ou diferencial específica para restaurar um ponto de recuperação específico.

Consulte os pré-requisitos [aqui](restore-sql-database-azure-vm.md#prerequisites) mencionados antes de restaurar os DBs SQL.

Em primeiro lugar, obtenha o DB SQL apoiado relevante utilizando o [cmdlet Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) PowerShell.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
```

### <a name="fetch-the-relevant-restore-time"></a>Buscar o tempo de restauro relevante

Como descrito acima, pode restaurar o SQL DB com cópia completa/diferencial **ou** para um ponto de registo no tempo.

#### <a name="fetch-distinct-recovery-points"></a>Obter pontos de recuperação distintos

Utilize [o Get-AzRecoveryServicesBackupRecoveryPoint](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) para obter pontos de recuperação distintos (Full/diferenciais) para um SQL DB apoiado.

```powershell
$startDate = (Get-Date).AddDays(-7).ToUniversalTime()
$endDate = (Get-Date).ToUniversalTime()
Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -StartDate $startdate -EndDate $endDate
```

A saída é semelhante ao exemplo seguinte

```output
RecoveryPointId    RecoveryPointType  RecoveryPointTime      ItemName                             BackupManagemen
                                                                                                  tType
---------------    -----------------  -----------------      --------                             ---------------
6660368097802      Full               3/18/2019 8:09:35 PM   MSSQLSERVER;model             AzureWorkload
```

Utilize o filtro 'RecoveryPointId' ou um filtro de matriz para obter o ponto de recuperação relevante.

```powershell
$FullRP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -RecoveryPointId "6660368097802"
```

#### <a name="fetch-point-in-time-recovery-point"></a>Buscar ponto de recuperação no tempo

Se pretender restaurar o DB num determinado ponto a tempo, utilize [o cmdlet Get-AzRecoveryServicesBackupRecoveryLogChain](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverylogchain) PowerShell. O cmdlet devolve uma lista de datas que representam tempos de início e fim de uma cadeia de registos contínua e ininterrupta para aquele item de backup SQL. O ponto de tempo desejado deve estar dentro deste alcance.

```powershell
Get-AzRecoveryServicesBackupRecoveryLogChain -Item $bkpItem -VaultId $targetVault.ID
```

A saída será semelhante ao seguinte exemplo.

```output
ItemName                       StartTime                      EndTime
--------                       ---------                      -------
SQLDataBase;MSSQLSERVER;azu... 3/18/2019 8:09:35 PM           3/19/2019 12:08:32 PM
```

A saída acima indica que pode restabelecer qualquer ponto no tempo entre a hora de início e o fim. Os tempos estão na UTC. Construa qualquer ponto no tempo no PowerShell que esteja dentro do alcance acima indicado.

> [!NOTE]
> Quando um ponto de registo selecionado para restauro, não precisa de especificar o ponto de partida, isto é, a cópia de segurança completa a partir da qual o DB é restaurado. O serviço de Backup Azure cuidará de todo o plano de recuperação, isto é, que cópia de segurança completa escolher, que backups de registo a aplicar, e assim por diante.

### <a name="determine-recovery-configuration"></a>Determinar a configuração de recuperação

No caso de um restauro do SQL DB, são suportados os seguintes cenários de restauro.

* Sobrevam o DB SQL com dados de outro ponto de recuperação - OriginalWorkloadRestore
* Restaurar o SQL DB como um novo DB no mesmo exemplo SQL - AlternateWorkloadRestore
* Restaurar o SQL DB como um novo DB em outro exemplo SQL em outro SQL VM - AlternateWorkloadRestore
* Restaurar o SQL DB como ficheiros .bak -RestoreAsFiles

Depois de obter o ponto de recuperação relevante (ponto-a-tempo distinto ou log-in-time), utilize [o Get-AzRecoveryServicesBackworkloadRecoveryConfig](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupworkloadrecoveryconfig) PowerShell para obter o objeto config de recuperação de acordo com o plano de recuperação pretendido.

#### <a name="original-workload-restore"></a>Restauro da carga de trabalho original

Para anular o DB apoiado com os dados do ponto de recuperação, basta especificar a bandeira certa e o ponto de recuperação relevante, como mostrado no(s) exemplo(s) seguintes exemplos.

##### <a name="original-restore-with-distinct-recovery-point"></a>Restauro original com ponto de recuperação distinto

```powershell
$OverwriteWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -OriginalWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="original-restore-with-log-point-in-time"></a>Restauro original com log-point-in-time

```powershell
$OverwriteWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem  -OriginalWorkloadRestore -VaultId $targetVault.ID
```

#### <a name="alternate-workload-restore"></a>Restauração alternativa da carga de trabalho

> [!IMPORTANT]
> Um DB SQL apoiado pode ser restaurado como um novo DB apenas para outro SQLInstance, em um Azure VM 'registrado' neste cofre.

Como descrito acima, se o SQLInstance alvo se encontra dentro de outro Azure VM, certifique-se de que está [registado neste cofre](#registering-the-sql-vm) e o SQLInstance relevante aparece como um item protetor.

```powershell
$TargetInstance = Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -ItemType SQLInstance -Name "<SQLInstance Name>" -ServerName "<SQL VM name>" -VaultId $targetVault.ID
```

Em seguida, basta passar o ponto de recuperação relevante, direcione a placa SQL com a bandeira certa como mostrado abaixo.

##### <a name="alternate-restore-with-distinct-recovery-point"></a>Restauro alternativo com ponto de recuperação distinto

```powershell
$AnotherInstanceWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetItem $TargetInstance -AlternateWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="alternate-restore-with-log-point-in-time"></a>Restauro alternativo com log-point-in-time

```powershell
$AnotherInstanceWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem -AlternateWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="restore-as-files"></a>Restaurar como Ficheiros

Para restaurar os dados de cópia de segurança como ficheiros .bak em vez de uma base de dados, escolha a opção **Restaurar como Ficheiros.** O SQL DB apoiado pode ser restaurado a qualquer VM alvo que esteja registado neste cofre.

```powershell
$TargetContainer= Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName "VM name" -VaultId $vaultID
```

##### <a name="restore-as-files-with-distinct-recovery-point"></a>Restaurar como ficheiros com ponto de recuperação distinto

```powershell
$FileRestoreWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

##### <a name="restore-as-files-with-log-point-in-time-from-latest-full"></a>Restaurar como ficheiros com log-point-in-time a partir da mais recente completa

```powershell
$FileRestoreWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

##### <a name="restore-as-files-with-log-point-in-time-from-a-specified-full"></a>Restaurar como ficheiros com log-point-in-time a partir de um especificado completo

Se pretender dar um conjunto específico que deve ser utilizado para restauro, utilize o seguinte comando:

```powershell
$FileRestoreWithLogAndSpecificFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -FromFull $FullRP -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

O objeto de configuração final do ponto de recuperação obtido a partir de [Get-AzRecoveryServicesBackupWorkloadRecoveryConfig](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupworkloadrecoveryconfig) PowerShell tem todas as informações relevantes para restauro e está como mostrado abaixo.

```output
TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : No
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
```

Pode editar o nome DB restaurado, OverwriteWLIfpresent, NoRecoveryMode e os campos targetPhysicalPath. Obtenha mais detalhes sobre os caminhos do ficheiro alvo, como mostrado abaixo.

```powershell
$AnotherInstanceWithFullConfig.targetPhysicalPath
```

```output
MappingType SourceLogicalName SourcePath                  TargetPath
----------- ----------------- ----------                  ----------
Data        azurebackup1      F:\Data\azurebackup1.mdf    F:\Data\azurebackup1_1553001753.mdf
Log         azurebackup1_log  F:\Log\azurebackup1_log.ldf F:\Log\azurebackup1_log_1553001753.ldf
```

Desatrei as propriedades relevantes do PowerShell como valores de cadeia como mostrado abaixo.

```powershell
$AnotherInstanceWithFullConfig.OverwriteWLIfpresent = "Yes"
$AnotherInstanceWithFullConfig | fl
```

```output
TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : Yes
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
```

> [!IMPORTANT]
> Certifique-se de que o objeto config de recuperação final tem todos os valores necessários e adequados, uma vez que a operação de restauro será baseada no objeto config.

### <a name="restore-with-relevant-configuration"></a>Restaurar com configuração relevante

Assim que o objeto Config de recuperação relevante for obtido e verificado, utilize o cmdlet De restaurar o [Restore-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) PowerShell para iniciar o processo de restauro.

```powershell
Restore-AzRecoveryServicesBackupItem -WLRecoveryConfig $AnotherInstanceWithLogConfig -VaultId $targetVault.ID
```

A operação de restauro devolve um trabalho a ser seguido.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Restore              InProgress           3/17/2019 10:02:45 AM                                3274xg2b-e4fg-5952-89b4-8cb566gc1748
```

## <a name="manage-sql-backups"></a>Gerir backups SQL

### <a name="on-demand-backup"></a>Backup a pedido

Uma vez ativada a cópia de segurança para um DB, também pode acionar uma cópia de segurança a pedido para o DB utilizando o [cmdlet De backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) PowerShell. O exemplo a seguir aciona uma cópia de segurança completa num SQL DB com compressão ativada e a cópia de segurança completa deve ser mantida durante 60 dias.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
Backup-AzRecoveryServicesBackupItem -Item $bkpItem -BackupType Full -EnableCompression -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
```

O comando de reserva a pedido devolve um trabalho a ser seguido.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Backup               InProgress           3/18/2019 8:41:27 PM                                2516bb1a-d3ef-4841-97a3-9ba455fb0637
```

Se a saída for perdida ou se quiser obter o ID de emprego relevante, [obtenha a lista de empregos](#track-azure-backup-jobs) do serviço Azure Backup e, em seguida, rastreiá-lo e seus detalhes.

### <a name="change-policy-for-backup-items"></a>Alterar política para artigos de backup

Pode alterar a política do item back-up da *Policy1* para *a Policy2* . Para mudar as políticas para um item de reserva, pegue na política relevante e faça backup do item e utilize o comando [Enable-AzRecoveryServices](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) com o item de backup como parâmetro.

```powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName>
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType MSSQL -BackupManagementType AzureWorkload -Name "<BackupItemName>"
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1
```

O comando aguarda até que a cópia de segurança de configuração esteja concluída e retorne a seguinte saída.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="edit-an-existing-backup-policy"></a>Editar uma política de backup existente

Para editar uma política existente, utilize o comando [Set-AzRecoveryServicesBackupProtectionPolicy.](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy)

```powershell
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $Pol -SchedulePolicy $SchPol -RetentionPolicy $RetPol
```

Verifique os trabalhos de reserva depois de algum tempo passado para rastrear quaisquer falhas. Se houver, tens de resolver os problemas. Em seguida, reexame o comando de política de edição com o parâmetro **FixForInconsistentItems** para voltar a tentar editar a política em todos os itens de backup para os quais a operação falhou anteriormente.

```powershell
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $Pol -FixForInconsistentItems
```

### <a name="re-register-sql-vms"></a>Re-registrar VMs SQL

> [!WARNING]
> Certifique-se de ler este [documento](backup-sql-server-azure-troubleshoot.md#re-registration-failures) para entender os sintomas e causas de avaria antes de tentar re-registrar

Para desencadear o re-registo do SQL VM, pegue no recipiente de reserva relevante e passe-o para o cmdlet de registo.

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Register-AzRecoveryServicesBackupContainer -Container $SQLContainer -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID
```

### <a name="stop-protection"></a>Parar proteção

#### <a name="retain-data"></a>Manter dados

Se desejar parar a proteção, pode utilizar o [cmdlet PowerShell de Disable-AzRecoveryServicesReupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) PowerShell. Isto irá parar as cópias de segurança programadas, mas os dados até agora são mantidos para sempre.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
```

#### <a name="delete-backup-data"></a>Eliminar dados de cópia de segurança

Para remover completamente os dados de cópia de segurança armazenados no cofre, basta adicionar a bandeira /switch 'RemoveRecoveryPoints' ao [comando de proteção 'desactivar'.](#retain-data)

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
```

#### <a name="disable-auto-protection"></a>Desativar a proteção automática

Se a autoproteção foi configurada numa SQLInstance, pode desativá-la utilizando o cmdlet de proteção de [disable-AzRecoveryServicesBackupAutoProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupautoprotection) PowerShell.

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Disable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetvault.ID
```

#### <a name="unregister-sql-vm"></a>SQL VM nãoregister

Se todos os DBs de um servidor SQL [já não estiverem protegidos e não existirem dados de backup,](#delete-backup-data)pode não registar o SQL VM a partir deste cofre. Só assim pode proteger os DBs para outro cofre. Utilize o cmdlet [Unregister-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer) PowerShell para desregiscer o SQL VM.

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
 Unregister-AzRecoveryServicesBackupContainer -Container $SQLContainer -VaultId $targetvault.ID
```

### <a name="track-azure-backup-jobs"></a>Track Azure Backup jobs

É importante notar que o Azure Backup apenas rastreia os trabalhos despoletadores de trabalhos na cópia de segurança do SQL. As cópias de segurança programadas (incluindo cópias de segurança de registo) não são visíveis no portal ou no PowerShell. No entanto, se algum trabalho programado falhar, um [alerta de backup](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) é gerado e mostrado no portal. [Utilize o Azure Monitor](backup-azure-monitoring-use-azuremonitor.md) para rastrear todos os trabalhos programados e outras informações relevantes.

Os utilizadores podem rastrear operações ativas/utilizadores desencadeadas com o JobID que é devolvido na [produção](#on-demand-backup) de trabalhos assíncronos, como backup. Utilize [o cmdlet Get-AzRecoveryServicesBackupJobDetail](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjobdetail) PowerShell para acompanhar o trabalho e os seus detalhes.

```powershell
 Get-AzRecoveryServicesBackupJobDetails -JobId 2516bb1a-d3ef-4841-97a3-9ba455fb0637 -VaultId $targetVault.ID
```

Para obter a lista de empregos a pedido e seus estatutos do serviço Azure Backup, use o cmdlet [Get-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) PowerShell. O exemplo a seguir devolve todos os empregos SQL em curso.

```powershell
Get-AzRecoveryServicesBackupJob -Status InProgress -BackupManagementType AzureWorkload
```

Para cancelar um trabalho em curso, utilize o [cmdlet Stop-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob) PowerShell.

## <a name="managing-sql-always-on-availability-groups"></a>Gerir sql sempre em grupos de disponibilidade

Para os Grupos SQL Always On Availability, certifique-se de [registar todos os nós](#registering-the-sql-vm) do grupo Disponibilidade (AG). Uma vez que o registo é feito para todos os nós, um objeto de grupo de disponibilidade SQL é logicamente criado em itens protegidos. As bases de dados no âmbito do SQL AG serão listadas como 'SQLDatabase'. Os nós aparecerão como instâncias autónomas e as bases de dados SQL padrão sob eles também serão listadas como bases de dados SQL.

Por exemplo, vamos supor que um SQL AG tem dois nós: *sql-server-0* e *sql-server-1* e 1 SQL AG DB. Uma vez registados estes dois nós, se [listar os itens protegidos,](#fetching-sql-dbs)lista os seguintes componentes

* Um objeto SQL AG - tipo de item protetor como SQLAvailabilityGroup
* Um SQL AG DB - tipo de item protetor como SQLDatabase
* sql-server-0 - tipo de item protetor como SQLInstance
* sql-server-1 - tipo de item protetor como SQLInstance
* Quaisquer DBs SQL predefinidos (master, modelo, msdb) sob o sql-server-0 - tipo de item protetor como SQLDatabase
* Quaisquer DBs SQL predefinidos (master, modelo, msdb) sob o sql-server-1 - tipo de item protetor como SQLDatabase

sql-server-0, sql-server-1 também será listado como "AzureVMAppContainer" quando [os recipientes de backup estiverem listados](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer).

Basta buscar a base de dados relevante para permitir a cópia de [segurança](#configuring-backup) e a cópia de segurança a [pedido](#on-demand-backup) e restaurar [os cmdlets PowerShell são idênticos.](#restore-sql-dbs)
