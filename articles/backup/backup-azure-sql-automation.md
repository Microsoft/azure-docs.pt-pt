---
title: SQL DB em backup Azure VM & restaurar via PowerShell
description: Faça backup e restaure as bases de dados SQL em VMs Azure utilizando backup azure e PowerShell.
ms.topic: conceptual
ms.date: 03/15/2019
ms.assetid: 57854626-91f9-4677-b6a2-5d12b6a866e1
ms.openlocfilehash: 9608b02869b1d41d901ec77a42cfaa6d882040e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131819"
---
# <a name="back-up-and-restore-sql-databases-in-azure-vms-with-powershell"></a>Back up e restaurar bases de dados SQL em VMs Azure com PowerShell

Este artigo descreve como usar o Azure PowerShell para fazer backup e recuperar um SQL DB dentro de um Azure VM utilizando o cofre dos Serviços de Recuperação de [Backup Azure.](backup-overview.md)

Este artigo explica como:

> [!div class="checklist"]
>
> * Configurar a PowerShell e registar o Fornecedor de Serviços de Recuperação Azure.
> * Crie um cofre dos Serviços de Recuperação.
> * Configure a cópia de segurança para SQL DB dentro de um VM Azure.
> * Faça um trabalho de reserva.
> * Restaurar um SQL DB apoiado.
> * Monitorize a reserva e restaure os empregos.

## <a name="before-you-start"></a>Antes de começar

* [Saiba mais](backup-azure-recovery-services-vault-overview.md) sobre cofres dos Serviços de Recuperação.
* Leia sobre as capacidades de funcionalidade para [apoiar dBs SQL dentro de VMs Azure](backup-azure-sql-database.md#before-you-start).
* Reveja a hierarquia de objetos PowerShell para Serviços de Recuperação.

### <a name="recovery-services-object-hierarchy"></a>Serviços de Recuperação objetam hierarquia

A hierarquia do objeto é resumida no diagrama seguinte.

![Serviços de Recuperação objetam hierarquia](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Consulte a referência de referência **Az.RecoveryServices** [cmdlet](/powershell/module/az.recoveryservices) na biblioteca Azure.

### <a name="set-up-and-install"></a>Configurar e instalar

Configurar o PowerShell da seguinte forma:

1. [Descarregue a versão mais recente do Az PowerShell.](/powershell/azure/install-az-ps) A versão mínima necessária é de 1.5.0.

2. Encontre os cmdlets PowerShell de backup Azure com este comando:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Reveja os pseudónimos e cmdlets para azure backup e o cofre dos Serviços de Recuperação. Aqui está um exemplo do que pode ver. Não é uma lista completa de cmdlets.

    ![Lista de Serviços de Recuperação cmdlets](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Inscreva-se na sua conta Azure com **o Connect-AzAccount**.
5. Na página web que aparece, é-lhe solicitado que insebe as credenciais da sua conta.

    * Alternadamente, pode incluir as credenciais da sua conta como parâmetro no cmdlet **Connect-AzAccount** com **-Credencial**.
    * Se você é um parceiro csp trabalhando para um inquilino, especifique o cliente como inquilino, usando o seu nome de domínio primário inquilino ou inquilino. Um exemplo é **o Connect-AzAccount -Tenant** fabrikam.com.

6. Associe a subscrição que pretende utilizar com a conta, porque uma conta pode ter várias subscrições.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Se estiver a utilizar o Azure Backup pela primeira vez, utilize o **cmdlet Register-AzResourceProvider** para registar o fornecedor de Serviços de Recuperação Azure com a sua subscrição.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. Verifique se os fornecedores registaram com sucesso:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. Na saída do comando, verifique se o **Estado de Registo** altera-se ao **Registo**. Se não o fizer, volte a executar o **cmdlet Register-AzResourceProvider.**

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 

Siga estes passos para criar um cofre de Serviços de Recuperação.

O cofre dos Serviços de Recuperação é um recurso do Gestor de Recursos, por isso deve colocá-lo dentro de um grupo de recursos. Pode utilizar um grupo de recursos existente, ou pode criar um grupo de recursos com o cmdlet **New-AzResourceGroup.** Quando criar um grupo de recursos, especifique o nome e a localização do grupo de recursos.

1. Um cofre é colocado num grupo de recursos. Se não tiver um grupo de recursos existente, crie um novo com o [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0). Neste exemplo, criamos um novo grupo de recursos na região dos Estados Ocidentais.

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. Utilize o [cmdlet New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) para criar o cofre. Especifique a mesma localização para o cofre que foi usado para o grupo de recursos.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Especifique o tipo de redundância a utilizar para o armazenamento do cofre.

    * Pode utilizar [armazenamento redundante localmente](../storage/common/storage-redundancy-lrs.md) ou [armazenamento geo-redundante.](../storage/common/storage-redundancy-grs.md)
    * O exemplo seguinte define a opção **-BackupStorageStorageRedundancy** para o[set-AzRecoveryServicesBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) cmd para **o testvault** definido para **GeoRedundant**.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

### <a name="view-the-vaults-in-a-subscription"></a>Veja os cofres em uma subscrição

Para ver todos os cofres da subscrição, utilize [o Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0).

```powershell
Get-AzRecoveryServicesVault
```

A saída é semelhante à seguinte. O grupo de recursos associados e a localização são fornecidos.

```output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>Definir o contexto do cofre

Guarde o objeto do cofre numa variável e desemque o contexto do cofre.

* Muitos cmdlets de backup Azure requerem o objeto do cofre dos Serviços de Recuperação como entrada, por isso é conveniente armazenar o objeto do cofre numa variável.
* O contexto do cofre é o tipo de dados protegidos no cofre. Detete-o com [set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0). Após o contexto, aplica-se a todos os cmdlets subsequentes.

O exemplo que se segue define o contexto do cofre para **o testvault**.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Pegue a identificação do cofre

Planeamos depreciar a definição do contexto do cofre de acordo com as diretrizes da Azure PowerShell. Em vez disso, pode armazenar ou obter a identificação do cofre, e passá-la para comandos relevantes, da seguinte forma:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-a-backup-policy"></a>Configurar uma política de backup

Uma política de backup especifica o calendário para backups e quanto tempo devem ser mantidos os pontos de recuperação de backup:

* Uma política de backup está associada a pelo menos uma política de retenção. Uma política de retenção define quanto tempo um ponto de recuperação é mantido antes de ser apagado.
* Ver a retenção da política de backup predefinida utilizando [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0).
* Consulte o calendário de política de backup predefinido utilizando [o Get-AzRecoveryServicesBackupAgendaPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0).
* Utiliza o [cmdlet New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) para criar uma nova política de backup. Insere os objetos de política de agenda e retenção.

Por predefinição, um tempo de início é definido no Objeto de Política de Agenda. Utilize o seguinte exemplo para alterar o tempo de início para a hora de início desejada. A hora de início desejada também deve ser na UTC. O exemplo abaixo pressupõe que a hora de início desejada é 01:00 AM UTC para backups diários.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> Precisa fornecer a hora de início em apenas 30 minutos múltiplos. No exemplo acima, só pode ser "01:00:00" ou "02:30:00". A hora de início não pode ser "01:15:00"

O exemplo seguinte armazena a política de horários e a política de retenção em variáveis. Em seguida, utiliza essas variáveis como parâmetros para uma nova política **(NewSQLPolicy).** **A NewSQLPolicy** recebe uma cópia de segurança diária "Full", mantém-na durante 180 dias e leva um log backup a cada 2 horas

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

## <a name="enable-backup"></a>Ativar a cópia de segurança

### <a name="registering-the-sql-vm"></a>Registo do VM SQL

Para backups De VM Azure e ações azure file, o serviço de backup pode ligar-se a estes recursos do Gestor de Recursos Azure e obter os detalhes relevantes. Uma vez que a SQL é uma aplicação dentro de um VM Azure, o serviço de backup precisa de autorização para aceder à aplicação e recolher os detalhes necessários. Para isso, é necessário *'registar'* o VM Azure que contém a aplicação SQL com um cofre de serviços de recuperação. Assim que registar um VM SQL com um cofre, pode proteger os DBs SQL apenas para o cofre. Utilize o [Register-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) PS cmdlet para registar o VM.

```powershell
 $myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```

O comando devolverá um 'recipiente de reserva' deste recurso e o estado será 'registado'

> [!NOTE]
> Se o parâmetro de força não for dado, o utilizador é solicitado a confirmar com um texto "Pretende desativar a proteção deste recipiente". Por favor, ignore este texto e diga "Y" para confirmar. Trata-se de uma questão conhecida e estamos a trabalhar para remover o texto e a exigência do parâmetro de força.

### <a name="fetching-sql-dbs"></a>Fetching SQL DBs

Uma vez feito o registo, o serviço de backup poderá listar todos os componentes SQL disponíveis dentro do VM. Para ver todos os componentes SQL ainda a serem apoiados até este cofre use [Get-AzRecoveryServicesBackupProtectableItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS cmdlet

```powershell
Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -VaultId $targetVault.ID
```

A saída mostrará todos os componentes SQL desprotegidos em todos os VMs SQL registados neste cofre com O Tipo de Produto e Nome de Servidor. Pode filtrar ainda mais um Determinado VM SQL, passando o parâmetro '-Contentor' ou utilizar a combinação de 'Nome' e 'Nome do Servidor' juntamente com a bandeira do ItemType para chegar a um item SQL único.

```powershell
$SQLDB = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID -Name "<Item Name>" -ServerName "<Server Name>"
```

### <a name="configuring-backup"></a>Configuração de cópia de segurança

Agora que temos o SQL DB necessário e a política com a qual precisa de ser apoiado, podemos usar o [cmdlet Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) para configurar a cópia de segurança para este SQL DB.

```output
Enable-AzRecoveryServicesBackupProtection -ProtectableItem $SQLDB -Policy $NewSQLPolicy
```

O comando aguarda até que a cópia de segurança configurada esteja concluída e desemque a seguinte saída.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 6:00:21 PM      3/18/2019 6:01:35 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="fetching-new-sql-dbs"></a>Buscar novos DBs SQL

Uma vez registado o serviço de backup, o serviço de backup irá buscar os detalhes dos DBs disponíveis então. Se o utilizador adicionar as instâncias SQL DBs/SQL à máquina registada mais tarde, é necessário acionar manualmente o serviço de backup para realizar um novo 'inquérito' para obter todos os DBs desprotegidos (incluindo os recém-adicionados) novamente. Utilize o Cmdlet PS [Initialize-AzRecoveryBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Initialize-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS no SQL VM para realizar um novo inquérito. O comando aguarda até que a operação esteja concluída. Mais tarde use o [Get-AzRecoveryServicesBackupProtectableitem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS cmdlet para obter a lista dos mais recentes componentes SQL desprotegidos

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Initialize-AzRecoveryServicesBackupProtectableItem -Container $SQLContainer -WorkloadType MSSQL -VaultId $targetvault.ID
Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID
```

Uma vez recolhidos os itens protegidos relevantes, ative as cópias de segurança instruídas na [secção acima](#configuring-backup).
Se não quiser detetar manualmente novos DBs, pode optar pela autoproteção, conforme explicado [abaixo](#enable-autoprotection).

## <a name="enable-autoprotection"></a>Ativar a autoproteção

Um utilizador pode configurar a cópia de segurança de modo a que todos os DBs adicionados no futuro sejam automaticamente protegidos com uma determinada política. Para permitir a autoproteção, utilize [o Enable-AzRecoveryServicesBackupAutoProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) PS cmdlet.

Uma vez que a instrução é para apoiar todos os futuros DBs, a operação é feita a um nível SQLInstance.

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Enable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -Policy $NewSQLPolicy -VaultId $targetvault.ID
```

Uma vez dada a intenção de autoproteção, o inquérito à máquina para obter dBs recém-adicionados ocorre como uma tarefa de fundo programada a cada 8 horas.

## <a name="restore-sql-dbs"></a>Restaurar DBs SQL

A Backup Azure pode restaurar as bases de dados do SQL Server que estão a funcionar em VMs Azure da seguinte forma:

* Restaurar para uma data ou hora específica (para a segunda) utilizando cópias de segurança de registo de transações. A Cópia de Segurança Azure determina automaticamente a cópia de segurança diferencial adequada e a cadeia de backups de registo que são necessárias para restaurar com base no tempo selecionado.
* Restaurar uma cópia de segurança completa ou diferencial específica para restaurar um ponto de recuperação específico.

Verifique os pré-requisitos [aqui](restore-sql-database-azure-vm.md#prerequisites) mencionados antes de restaurar os DBs SQL.

Primeiro, obtenha o SQL DB de reserva relevante utilizando o [Cmdlet PS Get-AzRecoveryServicesBackupItem.](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem?view=azps-1.5.0)

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
```

### <a name="fetch-the-relevant-restore-time"></a>Buscar o tempo de restauro relevante

Tal como descrito acima, o utilizador pode restaurar o SQL DB suportado a uma cópia completa/diferencial **OU** a um log point-in-time.

#### <a name="fetch-distinct-recovery-points"></a>Obter pontos de recuperação distintos

Utilize [o Get-AzRecoveryRecoveryRecoveryPoint Para](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryPoint?view=azps-1.5.0) obter pontos de recuperação distintos (Full/diferencial) para um SQL DB apoiado.

```powershell
$startDate = (Get-Date).AddDays(-7).ToUniversalTime()
$endDate = (Get-Date).ToUniversalTime()
Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -StartDate $startdate -EndDate $endDate
```

A saída é semelhante ao seguinte exemplo

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

#### <a name="fetch-point-in-time-recovery-point"></a>Buscar ponto de recuperação ponto-a-tempo

Se o utilizador pretender restaurar o DB a um determinado ponto no tempo, utilize [o Cmdlet PS Get-AzRecoveryBackupRecoveryRecoveryChain.](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryLogChain?view=azps-1.5.0) O cmdlet devolve uma lista de datas que representam tempos de início e fim de uma cadeia de registo sem quebras e contínuas para aquele item de backup SQL. O ponto-a-tempo desejado deve estar dentro deste intervalo.

```powershell
Get-AzRecoveryServicesBackupRecoveryLogChain -Item $bkpItem -Item -VaultId $targetVault.ID
```

A saída será semelhante ao seguinte exemplo.

```output
ItemName                       StartTime                      EndTime
--------                       ---------                      -------
SQLDataBase;MSSQLSERVER;azu... 3/18/2019 8:09:35 PM           3/19/2019 12:08:32 PM
```

A saída acima significa que o utilizador pode restaurar qualquer ponto-a-tempo entre o tempo de início e o tempo de fim apresentados. Os tempos estão na UTC. Construa qualquer ponto no tempo no PS que esteja dentro do intervalo acima indicado.

> [!NOTE]
> Quando um log-in-time selecionado para restaurar, o utilizador não precisa especificar o ponto de partida, ou seja, cópia de segurança completa a partir da qual o DB é restaurado. O serviço de backup Azure cuidará de todo o plano de recuperação, ou seja, qual cópia de segurança completa para escolher, que backups de registo a aplicar etc.

### <a name="determine-recovery-configuration"></a>Determinar a configuração da recuperação

No caso de restabelecimento do SQL DB, são suportados os seguintes cenários de restauro.

* Sobrepondo o SQL DB apoiado com dados de outro ponto de recuperação - OriginalWorkloadRestore
* Restaurar o SQL DB como um novo DB na mesma instância SQL - AlternateWorkloadRestore
* Restaurar o SQL DB como um novo DB em outro caso SQL em outro SQL VM - AlternateWorkloadRestore
* Restaurar o SQL DB como ficheiros .bak -RestoreAsFiles

Depois de obter o ponto de recuperação relevante (distinto ou ponto de registo no tempo), utilize [Get-AzRecoveryBackupRecoveryRecoveryRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) PS cmdlet para obter o objeto de recuperação config de acordo com o plano de recuperação pretendido.

#### <a name="original-workload-restore"></a>Restabelecimento da carga de trabalho original

Para anular o DB apoiado com dados do ponto de recuperação, basta especificar a bandeira certa e o ponto de recuperação relevante, como mostra o exemplo ou exemplo s seguintes.

##### <a name="original-restore-with-distinct-recovery-point"></a>Restauro original com ponto de recuperação distinto

```powershell
$OverwriteWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -OriginalWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="original-restore-with-log-point-in-time"></a>Restauro original com log point-in-time

```powershell
$OverwriteWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem  -OriginalWorkloadRestore -VaultId $targetVault.ID
```

#### <a name="alternate-workload-restore"></a>Restauro de carga de trabalho alternativa

> [!IMPORTANT]
> Um SQL DB apoiado pode ser restaurado como um novo DB para outro SQLInstance apenas, em um Azure VM 'registrado' para este cofre.

Como descrito acima, se o alvo SQLInstance se situa ré si em outro VM Azure, certifique-se de que está [registado neste cofre](#registering-the-sql-vm) e o SQLInstance relevante aparece como um item protectable.

```powershell
$TargetInstance = Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -ItemType SQLInstance -Name "<SQLInstance Name>" -ServerName "<SQL VM name>" -VaultId $targetVault.ID
```

Em seguida, basta passar o ponto de recuperação relevante, alvo SQL instância com a bandeira direita como mostrado abaixo.

##### <a name="alternate-restore-with-distinct-recovery-point"></a>Restauro alternativo com ponto de recuperação distinto

```powershell
$AnotherInstanceWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetItem $TargetInstance -AlternateWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="alternate-restore-with-log-point-in-time"></a>Restauro alternativo com log point-in-time

```powershell
$AnotherInstanceWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem -AlternateWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="restore-as-files"></a>Restaurar como Arquivos

Para restaurar os dados de backup como ficheiros .bak em vez de uma base de dados, escolha a opção **Restaurar como Ficheiros.** O SQL DB apoiado pode ser restaurado a qualquer VM alvo registado neste cofre.

```powershell
$TargetContainer= Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName "VM name" -VaultId $vaultID
```

##### <a name="restore-as-files-with-distinct-recovery-point"></a>Restaurar como ficheiros com ponto de recuperação distinto

```powershell
$FileRestoreWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

##### <a name="restore-as-files-with-log-point-in-time-from-latest-full"></a>Restaurar como ficheiros com log point-in-time a partir do mais recente full

```powershell
$FileRestoreWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

##### <a name="restore-as-files-with-log-point-in-time-from-a-specified-full"></a>Restaurar como ficheiros com log point-in-time a partir de um determinado full

Se quiser dar um completo específico que deve ser utilizado para restaurar, utilize o seguinte comando:

```powershell
$FileRestoreWithLogAndSpecificFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -FromFull $FullRP -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

O objeto de configuração final do ponto de recuperação obtido a partir de [Get-AzRecoveryServicesRecoveryRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) PS cmdlet tem todas as informações relevantes para restauro e é como mostrado abaixo.

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

Pode editar o nome DB restaurado, OverwriteWLIfpresent, NoRecoveryMode e targetPhysicalPath. Obtenha mais detalhes para os caminhos do ficheiro alvo, como mostrado abaixo.

```powershell
$AnotherInstanceWithFullConfig.targetPhysicalPath
```

```output
MappingType SourceLogicalName SourcePath                  TargetPath
----------- ----------------- ----------                  ----------
Data        azurebackup1      F:\Data\azurebackup1.mdf    F:\Data\azurebackup1_1553001753.mdf
Log         azurebackup1_log  F:\Log\azurebackup1_log.ldf F:\Log\azurebackup1_log_1553001753.ldf
```

Desloque as propriedades PS relevantes como valores de cadeia, como mostrado abaixo.

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
> Certifique-se de que o objeto config de recuperação final tem todos os valores necessários e adequados, uma vez que a operação de restauro será baseada no objeto de config.

### <a name="restore-with-relevant-configuration"></a>Restaurar com configuração relevante

Uma vez obtida e verificada a recuperação relevante do objeto Config, utilize o cmdlet PS [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Restore-AzRecoveryServicesBackupItem?view=azps-1.5.0) para iniciar o processo de restauro.

```powershell
Restore-AzRecoveryServicesBackupItem -WLRecoveryConfig $AnotherInstanceWithLogConfig -VaultId $targetVault.ID
```

A operação de restauro devolve um trabalho a ser rastreado.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Restore              InProgress           3/17/2019 10:02:45 AM                                3274xg2b-e4fg-5952-89b4-8cb566gc1748
```

## <a name="manage-sql-backups"></a>Gerir backups SQL

### <a name="on-demand-backup"></a>Backup a pedido

Uma vez ativada a cópia de segurança para um DB, o utilizador também pode desencadear uma cópia de segurança a pedido para o DB utilizando o cmdlet PS [de Backup-AzRecoveryServicesBackupItem.](https://docs.microsoft.com/powershell/module/az.recoveryservices/Backup-AzRecoveryServicesBackupItem?view=azps-1.5.0) O exemplo seguinte desencadeia uma cópia de segurança completa num SQL DB com compressão ativada e a cópia de segurança completa deve ser mantida durante 60 dias.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
Backup-AzRecoveryServicesBackupItem -Item $bkpItem -BackupType Full -EnableCompression -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
```

O comando de reserva a pedido devolve um trabalho a ser rastreado.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Backup               InProgress           3/18/2019 8:41:27 PM                                2516bb1a-d3ef-4841-97a3-9ba455fb0637
```

Se a saída estiver perdida ou se quiser obter o ID de trabalho relevante, [obtenha a lista de empregos](#track-azure-backup-jobs) do serviço de backup Azure e, em seguida, rastreie-o e seus detalhes.

### <a name="change-policy-for-backup-items"></a>Alterar a política de itens de backup

O utilizador pode modificar a política existente ou alterar a política do item back-up da Policy1 para a Policy2. Para mudar as políticas para um item de reserva, pegue a política relevante e faça backup e utilize o comando [Enable-AzRecoveryServices](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) com o item de backup como parâmetro.

```powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName>
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType MSSQL -BackupManagementType AzureWorkload -Name "<BackupItemName>"
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1
```

O comando aguarda até que a cópia de segurança configurada esteja concluída e desemque a seguinte saída.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="re-register-sql-vms"></a>Re-registar VMs SQL

> [!WARNING]
> Certifique-se de ler este [documento](backup-sql-server-azure-troubleshoot.md#re-registration-failures) para entender os sintomas e causas de falha antes de tentar re-registar

Para desencadear o reregisto do VM SQL, pegue o recipiente de reserva relevante e passe-o para o registo.

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Register-AzRecoveryServicesBackupContainer -Container $SQLContainer -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID
```

### <a name="stop-protection"></a>Parar proteção

#### <a name="retain-data"></a>Manter dados

Se o utilizador quiser parar a proteção, pode utilizar o cmdlet [DESactivação-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) PS. Isto irá parar as cópias de segurança programadas, mas os dados apoiados até agora são mantidos para sempre.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
```

#### <a name="delete-backup-data"></a>Eliminar dados de cópia de segurança

Para remover completamente os dados de backup armazenados no cofre, basta adicionar a bandeira/interruptor 'RemoveRecoveryPoints' ao comando de [proteção 'desactivar'.](#retain-data)

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
```

#### <a name="disable-auto-protection"></a>Desativar a proteção automática

Se a autoproteção estiver configurada num SQLInstance, o utilizador pode desativá-lo utilizando o cmdlet PS de proteção ps de [deficientes-AzRecoveryServicesBackupAutoProtection.](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0)

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Disable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetvault.ID
```

#### <a name="unregister-sql-vm"></a>Não registrar SQL VM

Se todos os DBs de um servidor SQL [já não estiverem protegidos e não existirem dados](#delete-backup-data)de backup, o utilizador pode desregistar o VM SQL a partir deste cofre. Só então o utilizador pode proteger os DBs para outro cofre. Utilize [Unregister-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Unregister-AzRecoveryServicesBackupContainer?view=azps-1.5.0) PS cmdlet para desregistar o VM SQL.

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
 Unregister-AzRecoveryServicesBackupContainer -Container $SQLContainer -VaultId $targetvault.ID
```

### <a name="track-azure-backup-jobs"></a>Empregos de backup track Azure

É importante notar que o Azure Backup apenas rastreia o utilizador desencadeado trabalhos em backup SQL. As cópias de segurança programadas (incluindo cópias de segurança de registo) não são visíveis no portal/powershell. No entanto, se algum trabalho programado falhar, um alerta de [backup](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) é gerado e mostrado no portal. Utilize o [Monitor Azure](backup-azure-monitoring-use-azuremonitor.md) para acompanhar todos os trabalhos programados e outras informações relevantes.

Os utilizadores podem rastrear operações a pedido/utilizador desencadeadas com o JobID que é devolvido na [produção](#on-demand-backup) de trabalhos assíncronos, tais como backup. Utilize [get-AzRecoveryServicesBackupJobDetail](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJobDetail) PS cmdlet para rastrear o trabalho e os seus detalhes.

```powershell
 Get-AzRecoveryServicesBackupJobDetails -JobId 2516bb1a-d3ef-4841-97a3-9ba455fb0637 -VaultId $targetVault.ID
```

Para obter a lista de empregos a pedido e seus status do serviço de backup Azure, use [Get-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJob?view=azps-1.5.0) PS cmdlet. O exemplo seguinte devolve todos os empregos em curso da SQL.

```powershell
Get-AzRecoveryServicesBackupJob -Status InProgress -BackupManagementType AzureWorkload
```

Para cancelar um trabalho em curso, utilize o [stop-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Stop-AzRecoveryServicesBackupJob?view=azps-1.5.0) PS cmdlet.

## <a name="managing-sql-always-on-availability-groups"></a>Gerir o SQL sempre em grupos de disponibilidade

Para Grupos SQL Always On Availability, certifique-se de [registar todos os nós](#registering-the-sql-vm) do grupo Disponibilidade (AG). Uma vez que o registo é feito para todos os nós, um objeto de grupo de disponibilidade SQL é logicamente criado sob itens protegidos. As bases de dados sob o SQL AG serão listadas como 'SQLDatabase'. Os nós aparecerão como instâncias autónomas e as bases de dados SQL padrão sob eles serão listadas como bases de dados SQL também.

Por exemplo, vamos assumir que um SQL AG tem dois nós: 'sql-server-0' e 'sql-server-1' e 1 SQL AG DB. Uma vez registados estes dois nós, se o utilizador [listar os itens protegidos,](#fetching-sql-dbs)lista os seguintes componentes

* Um objeto SQL AG - tipo de item protectável como SQLAvailabilityGroup
* Um SQL AG DB - tipo de item protectável como SQLDatabase
* sql-server-0 - tipo de item protectável como SQLInstance
* sql-server-1 - tipo de item protectável como SQLInstance
* Quaisquer DBs SQL padrão (mestre, modelo, msdb) em sql-server-0 - tipo de item protectável como SQLDatabase
* Quaisquer DBs SQL padrão (mestre, modelo, msdb) em sql-server-1 - tipo de item protectável como SQLDatabase

sql-server-0, sql-server-1 também será listado como "AzureVMAppContainer" quando os recipientes de [reserva estiverem listados](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupContainer?view=azps-1.5.0).

Basta buscar a base de dados SQL relevante para permitir a cópia de [segurança](#configuring-backup) e a cópia de segurança a [pedido](#on-demand-backup) e restaurar os [cmdlets PS são idênticos.](#restore-sql-dbs)
