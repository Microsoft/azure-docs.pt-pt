---
title: 'Cópia de segurança do Azure: Criar cópias de segurança e restaurar bases de dados SQL em VMs do Azure com a cópia de segurança do Azure e o PowerShell'
description: Criar cópias de segurança e restaurar bases de dados SQL em VMs do Azure com a cópia de segurança do Azure e PowerShell.
services: backup
author: pvrk
manager: vijayts
keywords: Azure Backup; SQL;
ms.service: backup
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pullabhk
ms.assetid: 57854626-91f9-4677-b6a2-5d12b6a866e1
ms.openlocfilehash: 6469e3b35357867b6b38b00c8b11637ba30b2960
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58287563"
---
# <a name="back-up-and-restore-sql-databases-in-azure--vms-with-powershell"></a>Criar cópias de segurança e restaurar bases de dados SQL em VMs do Azure com o PowerShell

Este artigo descreve como utilizar o Azure PowerShell para criar cópias de segurança e recuperar uma BD SQL dentro de uma VM do Azure, utilizando [Azure Backup](backup-overview.md) cofre dos serviços de recuperação.

Este tutorial explica como:

> [!div class="checklist"]
> * Configurar o PowerShell e registar o fornecedor de serviços de recuperação do Azure.
> * Crie um cofre dos Serviços de Recuperação.
> * Configure cópia de segurança para o SQL DB dentro de uma VM do Azure.
> * Execute uma tarefa de cópia de segurança.
> * Restaure uma cópia de segurança de BD SQL.
> * Monitorize a cópia de segurança e restaurar trabalhos.

## <a name="before-you-start"></a>Antes de começar

* [Saiba mais](backup-azure-recovery-services-vault-overview.md) sobre cofres dos serviços de recuperação.
* Leia sobre as capacidades de funcionalidade para [backup bds SQL dentro de VMs do Azure](backup-azure-sql-database.md#before-you-start).
* Reveja a hierarquia de objetos do PowerShell para serviços de recuperação.

### <a name="recovery-services-object-hierarchy"></a>Hierarquia de objetos de serviços de recuperação

A hierarquia do objeto é resumida no diagrama seguinte.

![Hierarquia de objetos de serviços de recuperação](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Reveja os **Az.RecoveryServices** [referência de cmdlet](/powershell/module/az.recoveryservices) referência na biblioteca do Azure.

### <a name="set-up-and-install"></a>Configurar e instalar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Configure o PowerShell da seguinte forma:

1. [Baixe a versão mais recente do PowerShell de Az](/powershell/azure/install-az-ps). A versão mínima necessária é 1.5.0.

2. Encontre os cmdlets do PowerShell de cópia de segurança do Azure com este comando:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Rever os aliases e os cmdlets de cópia de segurança do Azure e o Cofre dos serviços de recuperação. Eis um exemplo de que vê. Não é uma lista completa dos cmdlets.

    ![Lista de cmdlets de serviços de recuperação](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Inicie sessão na sua conta do Azure com **Connect-AzAccount**.
5. Na página da web que aparece, lhe for pedido para introduzir as credenciais da conta.

    * Em alternativa, pode incluir as credenciais da conta como um parâmetro no **Connect-AzAccount** cmdlet com **-Credential**.
    * Se estiver a funcionar para um inquilino de parceiro CSP, especifica o cliente como um inquilino, utilizando o respetivo nome de domínio primário tenantID ou o inquilino. Um exemplo é **Connect-AzAccount-inquilino** fabrikam.com.

6. Associe a subscrição que pretende utilizar com a conta, uma vez que uma conta pode ter várias subscrições.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Se estiver a utilizar o Azure Backup pela primeira vez, utilize o **Register-AzResourceProvider** cmdlet para registar o fornecedor de serviços de recuperação do Azure com a sua subscrição.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. Certifique-se de que os fornecedores registado com êxito:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. No resultado do comando, certifique-se de que **RegistrationState** é alterado para **registado**. Se não, é executado o **Register-AzResourceProvider** cmdlet novamente.

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 

Siga estes passos para criar um cofre dos serviços de recuperação.

O Cofre dos serviços de recuperação é um recurso do Resource Manager, para que deve colocá-lo dentro de um grupo de recursos. Pode utilizar um grupo de recursos existente ou pode criar um grupo de recursos com o **New-AzResourceGroup** cmdlet. Quando cria um grupo de recursos, especifique o nome e local para o grupo de recursos.

1. Um cofre é colocado num grupo de recursos. Se não tiver um recurso existente de grupo, criar um novo com o [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0). Neste exemplo, vamos criar um novo grupo de recursos na região E.U.A. oeste.

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. Utilize o [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) cmdlet para criar o cofre. Especifique a mesma localização do cofre que foi utilizado para o grupo de recursos.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Especifique o tipo de redundância para utilizar o armazenamento do cofre.

    * Pode usar [armazenamento localmente redundante](../storage/common/storage-redundancy-lrs.md) ou [armazenamento georredundante](../storage/common/storage-redundancy-grs.md).
    * O exemplo seguinte define a **- BackupStorageRedundancy** opção para o[conjunto AzRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperties?view=azps-1.4.0) cmd para **testvault** definido como  **GeoRedundant**.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

### <a name="view-the-vaults-in-a-subscription"></a>Ver os cofres numa subscrição

Para ver todos os cofres na subscrição, utilize [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0).

```powershell
Get-AzRecoveryServicesVault
```

O resultado é semelhante ao seguinte. O grupo de recursos associados e o local são fornecidos.

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>Definir o contexto do Cofre

Store o objeto do cofre numa variável e defina o contexto do cofre.

* Muitos cmdlets de cópia de segurança do Azure requerem o objeto do cofre dos serviços de recuperação como entrada, por isso é conveniente armazenar o objeto do cofre numa variável.
* O contexto do cofre é o tipo de dados protegidos no cofre. Defini-lo com [Set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0). Depois do contexto é definido, aplica-se a todos os cmdlets subsequentes.

O exemplo seguinte define o contexto do cofre para **testvault**.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Obter o ID do Cofre

Planejamos descontinuar o contexto do cofre definição em conformidade com as diretrizes do PowerShell do Azure. Em vez disso, pode armazenar ou obter o ID do cofre e passá-lo para comandos relevantes, da seguinte forma:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-a-backup-policy"></a>Configurar uma política de cópia de segurança

Uma política de cópia de segurança especifica a agenda para cópias de segurança e o período de tempo de pontos de recuperação de cópia de segurança devem ser mantidas:

* Uma política de cópia de segurança está associada a pelo menos uma política de retenção. Uma política de retenção define o tempo que um ponto de recuperação é mantido antes de ser eliminado.
* A através de política de cópia de segurança de retenção do padrão de vista [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0).
* A através de agendamento de política de cópia de segurança do padrão de vista [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0).
* Utilizar o [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) cmdlet para criar uma nova política de cópia de segurança. Introduza os objetos de política de agendamento e retenção.

O exemplo seguinte armazena a política de agendamento e a política de retenção em variáveis. Em seguida, utiliza essas variáveis como parâmetros para uma nova política (**NewSQLPolicy**). **NewSQLPolicy** demora uma diária "completa" cópia de segurança, retém durante 180 dias e usa uma cópia de segurança do registo a cada 2 horas

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "MSSQL"
$NewSQLPolicy = New-AzRecoveryServicesBackupProtectionPolicy -Name "NewSQLPolicy" -WorkloadType "MSSQL" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

O resultado é semelhante ao seguinte.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                Frequency                                IsDifferentialBackup IsLogBackupEnabled
                                                                                                                                Enabled
----                 ------------       -------------------- ----------                ---------                                -------------------- ------------------
NewSQLPolicy         MSSQL              AzureWorkload        3/15/2019 9:00:00 PM      Daily                                    False                True
```

## <a name="enable-backup"></a>Ativar cópia de segurança

### <a name="registering-the-sql-vm"></a>Registar a VM de SQL

Para cópias de segurança de VM do Azure e partilhas de ficheiros do Azure, o serviço de cópia de segurança pode ligar a estes recursos do Azure Resource Manager e obter os detalhes relevantes. Uma vez que o SQL é um aplicativo dentro de uma VM do Azure, o serviço de cópia de segurança tem permissão para aceder à aplicação e obter os detalhes necessários. Para fazer isso, precisa *"Registar"* VM do Azure que contém a aplicação de SQL com um cofre dos serviços de recuperação. Depois de se registar uma VM do SQL com um cofre, pode proteger o bds SQL para esse cofre apenas. Uso [Register-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) PS cmdlet para registar a VM.

````powershell
 $myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
````

O comando irá devolver um "contentor de cópia de segurança" deste recurso e o estado será "registado"

> [!NOTE]
> Se o parâmetro force não for fornecido, é pedido ao utilizador para confirmar a com uma mensagem de texto "que pretende desativar a proteção deste contentor'. . A ignorar este texto e dizer "Y" para confirmar. Este é um problema conhecido e estamos a trabalhar para remover o texto e o requisito para o parâmetro force

### <a name="fetching-sql-dbs"></a>A obter bds SQL

Depois de fazer o registro, o serviço de cópia de segurança será pode listar todos os componentes SQL disponíveis dentro da VM. Para ver todos os componentes do SQL, mas a cópia de segurança para esta utilização do cofre [Get-AzRecoveryServicesBackupProtectableItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) cmdlet de PS

````powershell
Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -VaultId $targetVault.ID
````

O resultado apresentará todos os componentes SQL não protegidos em todas as VMs de SQL registado neste cofre com o tipo de Item e ServerName. Pode filtrar ainda mais a uma VM de SQL específica ao transmitir o "-Container" parâmetro ou utilize a combinação de 'Name' e 'ServerName', juntamente com ItemType sinalizador para chegar a um único item SQL.

````powershell
$SQLDB = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID -Name "<Item Name>" -ServerName "<Server Name>"
````

### <a name="configuring-backup"></a>Configurar a cópia de segurança

Agora que temos o banco de dados de SQL necessários e a política com o que precisa ser efetuada a cópia de segurança, podemos usar o [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) cmdlet para configurar a cópia de segurança para esta DB do SQL.

````powershell
Enable-AzRecoveryServicesBackupProtection -ProtectableItem $SQLDB -Policy $NewSQLPolicy
````

O comando tem de aguardar até que a cópia de segurança de configuração está concluída e devolve o resultado seguinte.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 6:00:21 PM      3/18/2019 6:01:35 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="fetching-new-sql-dbs"></a>A obter novo bds de SQL

Assim que a máquina estiver registrada, o serviço de cópia de segurança irá obter os detalhes do DBs disponíveis, em seguida. Se o usuário adiciona instâncias do DBs do SQL/SQL para a máquina registada mais tarde, é necessário acionar manualmente o serviço de cópia de segurança para executar um consulta para obter todos os dos DBs não protegidos (incluindo aqueles adicionados recentemente) do novo novamente. Utilize o [Initialize AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Initialize-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS cmdlet na VM do SQL para efetuar uma nova consulta. O comando tem de aguardar até que a operação esteja concluída. Utilizar mais tarde a [Get-AzRecoveryServicesBackupProtectableItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS cmdlet para obter a lista de componentes do SQL mais recente não protegidos

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Initialize-AzRecoveryServicesBackupProtectableItem -Container $SQLContainer -WorkloadType MSSQL -VaultId $targetvault.ID
Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID
````

Depois dos itens susceptíveis de proteção relevantes são encontrados, ativar as cópias de segurança com as instruções na [acima secção](#configuring-backup).
Se um não desejar manualmente detetar bds novo, pode optar para autoprotection conforme explicado [abaixo](#enable-autoprotection).

## <a name="enable-autoprotection"></a>Ativar AutoProtection

Um utilizador pode configurar a cópia de segurança, de modo a que todas as bds adicionadas no futuro são automaticamente protegidos com uma determinada política. Para ativar autoprotection, utilize [Enable-AzRecoveryServicesBackupAutoProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) PS cmdlet.

Uma vez que a instrução é fazer backup de todos os bds futuras, a operação é feita num SQLInstance nível.

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Enable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -Policy $targetPolicy -VaultId $targetvault.ID
```

Depois da intenção de autoprotection é fornecida, a consulta na máquina para buscar recentemente adicionado é de DBs efetuada como uma tarefa agendada de em segundo plano a cada 8 horas.

## <a name="restore-sql-dbs"></a>Restaurar bds SQL

Cópia de segurança do Azure, pode restaurar bases de dados do SQL Server em execução em VMs do Azure da seguinte forma:

1. Restaure para uma data específica ou a hora (para o segundo) com os backups de log de transação. O Azure Backup determina automaticamente a cópia de segurança completa, diferencial adequada e a cadeia de cópias de segurança de registo que são necessários para restaurar com base no tempo selecionado.
2. Restaure um backup completo ou diferencial específico para restaurar para um ponto de recuperação específico.

Verifique os pré-requisitos mencionados [aqui](restore-sql-database-azure-vm.md#prerequisites) antes de restaurar bds SQL.

Primeiro obter o relevante cópia de segurança de BD SQL com o [Get-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS cmdlet.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
````

### <a name="fetch-the-relevant-restore-time"></a>O tempo de restauro relevantes de obtenção

Conforme mencionado acima, o usuário poderá restaurar o banco de dados do SQL de uma cópia de segurança para uma cópia completa/diferencial **ou** para um registo ponto anterior no tempo.

#### <a name="fetch-distinct-recovery-points"></a>Obter pontos de recuperação distintos

Uso [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryPoint?view=azps-1.5.0) ao obter pontos de recuperação (completa/diferencial) distintos para uma BD do SQL de uma cópia de segurança.

````powershell
$startDate = (Get-Date).AddDays(-7).ToUniversalTime()
$endDate = Get-Date.ToUniversalTime()
Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -StartDate $startdate -EndDate $endDate
````

O resultado é semelhante ao seguinte exemplo

````powershell
RecoveryPointId    RecoveryPointType  RecoveryPointTime      ItemName                             BackupManagemen
                                                                                                  tType
---------------    -----------------  -----------------      --------                             ---------------
6660368097802      Full               3/18/2019 8:09:35 PM   MSSQLSERVER;model             AzureWorkload
````

Utilize o filtro 'RecoveryPointId' ou um filtro de matriz para obter o ponto de recuperação relevantes.

````powershell
$FullRP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -RecoveryPointId "6660368097802"
````

#### <a name="fetch-point-in-time-recovery-point"></a>Obter ponto de recuperação de ponto no tempo

Se o usuário quer restaurar o banco de dados para um determinado ponto no tempo, utilize [Get-AzRecoveryServicesBackupRecoveryLogChain](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryLogChain?view=azps-1.5.0) PS cmdlet. O cmdlet devolve uma lista de datas que representam as horas de início e fim de uma cadeia de registos singular e contínua para esse item de cópia de segurança de SQL. O ponto no tempo desejado deve ser dentro deste intervalo.

```powershell
Get-AzRecoveryServicesBackupRecoveryLogChain -Item $bkpItem -Item -VaultId $targetVault.ID
```

O resultado será semelhante ao seguinte exemplo.

````powershell
ItemName                       StartTime                      EndTime
--------                       ---------                      -------
SQLDataBase;MSSQLSERVER;azu... 3/18/2019 8:09:35 PM           3/19/2019 12:08:32 PM
````

A saída acima significa que o utilizador pode restaurar para qualquer ponto anterior no tempo entre a hora de início exibida e a hora de fim. Os tempos são em formato UTC. Construa a qualquer ponto anterior no tempo no PS está dentro do intervalo mostrado acima.

> [!NOTE]
> Quando um registo no momento selecionado para restauro, o utilizador não tem de especificar a, ou seja, o ponto de partida, a cópia de segurança completa de que o banco de dados é restaurado. O serviço de cópia de segurança do Azure se encarregará de plano de recuperação completo ou seja, que a cópia de segurança para escolher, o que de completa iniciar cópias de segurança para aplicar etc.

### <a name="determine-recovery-configuration"></a>Determinar a configuração da recuperação

Em caso de restauro da BD SQL, são suportados os seguintes cenários de restauro.

1. Substituir a BD do SQL de uma cópia de segurança com dados a partir de outro ponto de recuperação - OriginalWorkloadRestore
2. Restaurar o banco de dados SQL como uma nova base de dados na mesma instância SQL - AlternateWorkloadRestore
3. Restaurar o banco de dados SQL como uma nova base de dados em outra instância SQL em outra VM do SQL - AlternateWorkloadRestore

Depois de a obter o ponto de recuperação relevantes (distintas ou iniciar sessão no momento), utilize [Get-AzRecoveryServicesBackupWorkloadRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) PS cmdlet para obter o objeto de configuração da recuperação de acordo com o plano de recuperação pretendido.

#### <a name="original-workload-restore"></a>Restauro de carga de trabalho original

Para substituir a BD de cópia de segurança com dados a partir do ponto de recuperação, basta Especifica o sinalizador certo e o ponto de recuperação relevantes conforme mostrado nos exemplos seguintes.

##### <a name="original-restore-with-distinct-recovery-point"></a>Restauro original com o ponto de recuperação distinto

````powershell
$OverwriteWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -OriginalWorkloadRestore -VaultId $targetVault.ID
````

##### <a name="original-restore-with-log-point-in-time"></a>Restauro original com registo ponto anterior no tempo

```powershell
$OverwriteWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem  -OriginalWorkloadRestore -VaultId $targetVault.ID
```

#### <a name="alternate-workload-restore"></a>Restauro de carga de trabalho alternativo

> [!IMPORTANT]
> Uma cópia de segurança de BD SQL pode ser restaurado como uma nova base de dados para outro SQLInstance só, numa VM do Azure registado neste cofre.

Conforme mencionado acima, se o destino SQLInstance está ligada a outra VM do Azure, certificar-se de que é [registado neste cofre](#registering-the-sql-vm) e o SQLInstance relevante é apresentado como um item protegível.

````powershell
$TargetInstance = Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -ItemType SQLInstance -Name "<SQLInstance Name>" -ServerName "<SQL VM name>" -VaultId $targetVault.ID
````

Em seguida, basta passe o ponto de recuperação relevantes, instância SQL de destino com o sinalizador correto conforme apresentado abaixo.

##### <a name="alternate-restore-with-distinct-recovery-point"></a>Restauro alternativo com o ponto de recuperação distinto

````powershell
$AnotherInstanceWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetItem $TargetInstance -AlternateWorkloadRestore -VaultId $targetVault.ID
````

##### <a name="alternate-restore-with-log-point-in-time"></a>Restauro alternativo com registo ponto anterior no tempo

```powershell
$AnotherInstanceWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem -AlternateWorkloadRestore -VaultId $targetVault.ID
```

O objeto de configuração do ponto de recuperação final obtido a partir [Get-AzRecoveryServicesBackupWorkloadRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) PS cmdlet tem todas as informações relevantes para o restauro e é conforme mostrado abaixo.

````powershell
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
````

Pode editar os campos de nome, OverwriteWLIfpresent, NoRecoveryMode e targetPhysicalPath de BD restaurados. Obter mais detalhes para os caminhos de ficheiro de destino, conforme mostrado abaixo.

````powershell
$AnotherInstanceWithFullConfig.targetPhysicalPath

MappingType SourceLogicalName SourcePath                  TargetPath
----------- ----------------- ----------                  ----------
Data        azurebackup1      F:\Data\azurebackup1.mdf    F:\Data\azurebackup1_1553001753.mdf
Log         azurebackup1_log  F:\Log\azurebackup1_log.ldf F:\Log\azurebackup1_log_1553001753.ldf
````

Defina as propriedades de PS relevantes como valores de cadeia de caracteres, conforme mostrado abaixo.

````powershell
$AnotherInstanceWithFullConfig.OverwriteWLIfpresent = "Yes"
$AnotherInstanceWithFullConfig | fl

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
````

> [!IMPORTANT]
> Certifique-se de que o objeto de configuração de recuperação final tem todos os valores necessários e correto, uma vez que a operação de restauro irá basear-se o objeto de configuração.

### <a name="restore-with-relevant-configuration"></a>Restaurar com configuração relevante

Assim que o objeto de configuração da recuperação relevante é obtido e verificado, utilize o [restauro AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Restore-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS cmdlet para iniciar o processo de restauro.

````powershell
Restore-AzRecoveryServicesBackupItem -WLRecoveryConfig $AnotherInstanceWithLogConfig -VaultId $targetVault.ID
````

A operação de restauro retorna uma tarefa a ser monitorizado.

````powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Restore              InProgress           3/17/2019 10:02:45 AM                                3274xg2b-e4fg-5952-89b4-8cb566gc1748
````

## <a name="manage-sql-backups"></a>Gerir cópias de segurança do SQL

### <a name="on-demand-backup"></a>Cópia de segurança a pedido

Assim que a cópia de segurança tiver sido ativada para uma DB, o utilizador também pode acionar uma cópia de segurança a pedido para a DB utilizando [AzRecoveryServicesBackupItem de cópia de segurança](https://docs.microsoft.com/powershell/module/az.recoveryservices/Backup-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS cmdlet. O exemplo seguinte aciona uma cópia de segurança completa numa BD SQL com a compressão ativada e a cópia de segurança completa deve ser mantida durante 60 dias.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
Backup-AzRecoveryServicesBackupItem -Item $bkpItem -BackupType Full -EnableCompression -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
````

O comando de cópia de segurança ad hoc retorna uma tarefa a ser monitorizado.

````powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Backup               InProgress           3/18/2019 8:41:27 PM                                2516bb1a-d3ef-4841-97a3-9ba455fb0637
````

Se a saída for perdida ou se quiser obter o ID da tarefa relevantes [obter a lista de tarefas](#track-azure-backup-jobs) da cópia de segurança do Azure service e, em seguida, controlar a ele e os respetivos detalhes.

### <a name="change-policy-for-backup-items"></a>Alterar a política de itens de cópia de segurança

Utilizador pode modificar a política existente ou alterar a política do item de cópia de segurança de Policy1 para Policy2. Para mudar as políticas para um item de cópia de segurança, simplesmente buscar a política relevante e criar cópias de segurança item e utilizar o [Enable-AzRecoveryServices](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) comando com o item de cópia de segurança como o parâmetro.

````powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName>
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType MSSQL -BackupManagementType AzureWorkload -Name "<BackupItemName>"
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1
````

O comando tem de aguardar até que a cópia de segurança de configuração está concluída e devolve o resultado seguinte.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="stop-protection"></a>Parar proteção

#### <a name="retain-data"></a>Reter dados

Se o utilizador pretende parar a proteção, pode utilizar o [Disable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) PS cmdlet. Isto irá parar as cópias de segurança agendadas, mas os dados de segurança até agora são mantidos para sempre.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
````

#### <a name="delete-backup-data"></a>eliminar dados de cópia de segurança

Para remover completamente os dados de cópia de segurança armazenados no cofre, basta adicionar "-. o sinalizador/mudança dos RemoveRecoveryPoints para o ["desativar"comando proteção](#retain-data).

````powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
````

#### <a name="disable-auto-protection"></a>Desativar a proteção automática

Se autoprotection foi configurada uma SQLInstance, o utilizador pode desativá-lo a utilizar o [Disable-AzRecoveryServicesBackupAutoProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) PS cmdlet.

````powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Disable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetvault.ID
````

#### <a name="unregister-sql-vm"></a>Anular o registo de VM do SQL Server

Se todos os dos DBs do SQL server [já não estão protegidos e cópia de segurança não existem dados](#delete-backup-data), utilizador pode anular o registo da VM de SQL neste cofre. Apenas, em seguida, o utilizador pode proteger bds no outro cofre. Uso [Unregister-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Unregister-AzRecoveryServicesBackupContainer?view=azps-1.5.0) cmdlet de PS para anular o registo a VM do SQL.

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
 Unregister-AzRecoveryServicesBackupContainer -Container $SQLContainer -VaultId $targetvault.ID
````

### <a name="track-azure-backup-jobs"></a>Monitorizar tarefas de cópia de segurança do Azure

É importante observar que cópia de segurança do Azure apenas controla as tarefas do utilizador acionada na cópia de segurança do SQL. Cópias de segurança agendadas (incluindo backups de log) não estão visíveis no portal/powershell. No entanto, se houver agendadas tarefas falhar, uma [alerta de cópia de segurança](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) é gerado e apresentado no portal. [Utilizar o Azure Monitor](backup-azure-monitoring-use-azuremonitor.md) para controlar todas as tarefas agendadas e outras informações relevantes.

Os usuários podem controlar as operações de ad hoc/utilizador acionado com o JobID que é devolvido na [saída](#on-demand-backup) de tarefas assíncronas, tais como cópia de segurança. Uso [Get-AzRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJobDetails?view=azps-1.5.0) PS cmdlet para controlar o trabalho e os respetivos detalhes.

````powershell
 Get-AzRecoveryServicesBackupJobDetails -JobId 2516bb1a-d3ef-4841-97a3-9ba455fb0637 -VaultId $targetVault.ID
````

Para obter a lista de tarefas de ad hoc e os respetivos Estados do serviço de cópia de segurança do Azure, utilize [Get-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJob?view=azps-1.5.0) PS cmdlet. O exemplo seguinte devolve todas as tarefas em curso do SQL.

```powershell
Get-AzRecoveryServicesBackupJob -Status InProgress -BackupManagementType AzureWorkload
```

Para cancelar uma tarefa em curso, utilize o [Stop-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Stop-AzRecoveryServicesBackupJob?view=azps-1.5.0) PS cmdlet.

## <a name="managing-sql-always-on-availability-groups"></a>Gerir SQL sempre em grupos de disponibilidade

Para SQL grupos de Disponibilidade AlwaysOn, certifique-se de que [registar todos os nós](#registering-the-sql-vm) do grupo de disponibilidade (AG). Depois de fazer o registo para todos os nós, um objeto de grupo de disponibilidade SQL logicamente é criado em itens susceptíveis de proteção. As bases de dados sob o SQL AG serão listadas como "SQLDatabase". Os nós aparecerão como instâncias autónomas e as bases de dados do SQL padrão abaixo deles serão listadas como as bases de dados SQL.

Por exemplo, vamos supor que um AG do SQL tem dois nós: "sql-server-0" e "sql-server-1" e 1 DB de AG do SQL. Assim que ambas as estes nós são registrados, se usuário [lista os itens susceptíveis de proteção](#fetching-sql-dbs), ele lista os seguintes componentes

1. Um objeto de AG do SQL - suscetíveis de proteção tipo como SQLAvailabilityGroup de item
2. Um AG do SQL DB - tipo de item protegível como SQLDatabase
3. tipo de item protegível SQL-server-0 - como SQLInstance
4. tipo de item protegível SQL-server-1 - como SQLInstance
5. Tipo de item protegível qualquer padrão bds SQL (master, model, msdb), com a sql-server-0 - como SQLDatabase
6. Tipo de item protegível qualquer padrão bds SQL (master, model, msdb), com a sql-server-1 - como SQLDatabase

SQL-server-0, sql-server-1 será também listada como "AzureVMAppContainer" quando [contentores de cópia de segurança estão listados](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupContainer?view=azps-1.5.0).

Obter apenas a base de dados do SQL relevante para [ativar cópia de segurança](#configuring-backup) e o [cópia de segurança ad hoc](#on-demand-backup) e [restaurar cmdlets do PS](#restore-sql-dbs) são idênticos.
