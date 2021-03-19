---
title: Faça o back up de uma partilha de ficheiros Azure usando o PowerShell
description: Neste artigo, aprenda a fazer backup de uma partilha de ficheiros Azure Files utilizando o serviço de Backup Azure e o PowerShell.
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 948931764769bc967b88e7942b7e8384b0f93dff
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87077001"
---
# <a name="back-up-an-azure-file-share-by-using-powershell"></a>Faça o back up de uma partilha de ficheiros Azure usando o PowerShell

Este artigo descreve como usar a Azure PowerShell para fazer backup de uma partilha de ficheiros Azure Files através de um cofre dos Serviços de Recuperação [de Backup Azure.](backup-overview.md)

Este artigo explica como:

> [!div class="checklist"]
>
> * Configurar o PowerShell e registar o prestador de serviços de recuperação.
> * Crie um cofre dos Serviços de Recuperação.
> * Configure a cópia de segurança para uma partilha de ficheiros Azure.
> * Faça um trabalho de reserva.

## <a name="before-you-start"></a>Antes de começar

* [Saiba mais](backup-azure-recovery-services-vault-overview.md) sobre cofres dos Serviços de Recuperação.
* Reveja a referência [de referência cmdlet](/powershell/module/az.recoveryservices) Az.RecoveryServices na biblioteca Azure.
* Reveja a seguinte hierarquia de objetos PowerShell para serviços de recuperação:

  ![Serviços de Recuperação hierárquica de objetos](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

## <a name="set-up-powershell"></a>Configurar PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Configurar o PowerShell da seguinte forma:

1. [Descarregue a versão mais recente do Azure PowerShell](/powershell/azure/install-az-ps).

    > [!NOTE]
    > A versão mínima PowerShell necessária para a cópia de segurança das ações de ficheiros Azure é Az.RecoveryServices 2.6.0. A utilização da versão mais recente, ou pelo menos a versão mínima, ajuda-o a evitar problemas com scripts existentes. Instale a versão mínima utilizando o seguinte comando PowerShell:
    >
    > ```powershell
    > Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
    > ```

2. Encontre os cmdlets PowerShell para Azure Backup utilizando este comando:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Reveja os pseudónimos e cmdlets para Azure Backup, Azure Site Recovery e o cofre dos Serviços de Recuperação. Aqui está um exemplo do que pode ver. Não é uma lista completa de comandantes.

    ![Lista de serviços de recuperação](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Inscreva-se na sua conta Azure utilizando **o Connect-AzAccount**.
5. Na página web que aparece, é solicitado que introduza as credenciais da sua conta.

    Em alternativa, pode incluir as suas credenciais de conta como parâmetro no cmdlet **Connect-AzAccount** utilizando **-Credencial**.

    Se você é um parceiro da CSP trabalhando em nome de um inquilino, especifique o cliente como inquilino. Use o nome de identificação do inquilino ou o nome de domínio primário do inquilino. Um exemplo é **Connect-AzAccount -Inquilino "fabrikam.com".**

6. Associe a subscrição que pretende utilizar com a conta, porque uma conta pode ter várias subscrições:

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Se estiver a utilizar o Azure Backup pela primeira vez, utilize o **cmdlet Register-AzResourceProvider** para registar o fornecedor de Serviços de Recuperação Azure com a sua subscrição:

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. Verifique se os fornecedores se registaram com sucesso:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. Na saída de comando, verifique se o **Estado de Registo** altera as alterações **registadas**. Se não o fizer, volte a executar o **cmdlet Register-AzResourceProvider.**

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 

O cofre dos Serviços de Recuperação é um recurso do Gestor de Recursos, pelo que deve colocá-lo num grupo de recursos. Pode utilizar um grupo de recursos existente, ou pode criar um grupo de recursos utilizando o cmdlet **New-AzResourceGroup.** Quando criar um grupo de recursos, especifique o nome e a localização para o mesmo.

Siga estes passos para criar um cofre dos Serviços de Recuperação:

1. Se não tiver um grupo de recursos existente, crie um novo utilizando o cmdlet [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup) Neste exemplo, criamos um grupo de recursos na região oeste dos EUA:

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "West US"
   ```

1. Utilize o [cmdlet New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) para criar o cofre. Especifique o mesmo local para o cofre que usou para o grupo de recursos.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

### <a name="view-the-vaults-in-a-subscription"></a>Ver os cofres numa subscrição

Para visualizar todos os cofres da subscrição, utilize [o Get-AzRecoveryServicesVault:](/powershell/module/az.recoveryservices/get-azrecoveryservicesvault)

```powershell
Get-AzRecoveryServicesVault
```

A saída é semelhante à seguinte. Note que a saída fornece o grupo de recursos associado e a localização.

```powershell
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

Muitos cmdlets de backup Azure requerem o objeto do cofre dos Serviços de Recuperação como uma entrada, por isso é conveniente armazenar o objeto do cofre numa variável.

O contexto do cofre é o tipo de dados protegidos no cofre. Desconte-o utilizando [o Set-AzRecoveryServicesVaultContext](/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext). Após o contexto definido, aplica-se a todos os cmdlets subsequentes.

O exemplo a seguir define o contexto do cofre para **o testvault:**

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Pegue a iD do cofre

Planeamos depreciar a definição do contexto do cofre de acordo com as diretrizes da Azure PowerShell. Em vez disso, pode armazenar ou buscar a identificação do cofre e passá-la para comandos relevantes. Se não definiu o contexto do cofre ou pretende especificar o comando para correr para um determinado cofre, passe o ID do cofre `-vaultID` para todos os comandos relevantes da seguinte forma:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultID $vaultID
```

## <a name="configure-a-backup-policy"></a>Configure uma política de backup

Uma política de backup especifica o calendário para backups e quanto tempo devem ser mantidos pontos de recuperação de backup.

Uma política de apoio está associada a pelo menos uma política de retenção. Uma política de retenção define quanto tempo um ponto de recuperação é mantido antes de ser eliminado. Pode configurar backups com retenção diária, semanal, mensal ou anual.

Aqui estão alguns cmdlets para políticas de backup:

* Ver a retenção de política de backup predefinida utilizando [o Get-AzRecoveryServicesBackupRetentionPolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject).
* Consulte o calendário de política de backup predefinido utilizando [o Get-AzRecoveryServicesBackupSchedulePolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject).
* Crie uma nova política de backup utilizando [a New-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy). Introduza os objetos de política de programação e retenção como entrada.

Por predefinição, uma hora de início é definida no objeto da política de agendamento. Utilize o exemplo a seguir para alterar a hora de início para a hora de início desejada. A hora de início desejada deve estar no Tempo Coordenado Universal (UTC). O exemplo pressupõe que a hora de início desejada é 01:00 AM UTC para backups diários.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> Só precisa de fornecer a hora de início em múltiplos de 30 minutos. No exemplo anterior, pode ser apenas "01:00:00" ou "02:30:00". A hora de início não pode ser "01:15:00".

O exemplo a seguir armazena a política de agendamento e a política de retenção em variáveis. Em seguida, utiliza essas variáveis como parâmetros para uma nova política **(NewAFSPolicy).** **A NewAFSPolicy** recebe um backup diário e mantém-na durante 30 dias.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

O resultado é semelhante ao seguinte:

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2019 1:30:00 AM
```

## <a name="enable-backup"></a>Ativar backup

Depois de definir a política de backup, pode permitir a proteção para a partilha de ficheiros Azure utilizando a política.

### <a name="retrieve-a-backup-policy"></a>Recuperar uma política de backup

Obtém o objeto de política relevante utilizando [a Get-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy). Utilize este cmdlet para visualizar as políticas associadas a um tipo de carga de trabalho ou para obter uma política específica.

#### <a name="retrieve-a-policy-for-a-workload-type"></a>Recupere uma política para um tipo de carga de trabalho

O exemplo a seguir recupera políticas para o tipo de carga de trabalho **AzureFiles:**

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

O resultado é semelhante ao seguinte:

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> O fuso horário do campo **BackupTime** em PowerShell está na UTC. Quando o tempo de reserva é mostrado no portal Azure, o tempo é ajustado para o fuso horário local.

#### <a name="retrieve-a-specific-policy"></a>Recuperar uma política específica

A seguinte política recupera a política de backup denominada **dailyafs:**

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-protection-and-apply-the-policy"></a>Permitir a proteção e aplicar a política

Ativar a proteção utilizando [a Proteção Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection). Depois da apólice estar associada ao cofre, as cópias de segurança são ativadas de acordo com o calendário da apólice.

O exemplo a seguir permite a proteção do teste de partilha de ficheiros **AzureAureFileShare** em teste de conta de **armazenamentoStorageAcct,** com os **dailyafs de** política :

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

O comando aguarda até que o trabalho de proteção de configuração esteja terminado e dá uma saída semelhante ao seguinte exemplo:

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

Para obter mais informações sobre como obter uma lista de ações de ficheiros para uma conta de armazenamento, consulte [este artigo](/powershell/module/az.storage/get-azstorageshare).

## <a name="important-notice-backup-item-identification"></a>Aviso importante: Identificação do item de backup

Esta secção descreve uma mudança importante nas cópias de segurança das ações de ficheiros Azure em preparação para a disponibilidade geral.

Ao permitir uma cópia de segurança para ações de ficheiros Azure, o utilizador dá ao cliente um nome de partilha de ficheiros como nome de entidade, e é criado um item de backup. O nome do item de cópia de segurança é um identificador único que o serviço de backup Azure cria. Normalmente, o identificador é um nome fácil de utilizar. Mas para lidar com o cenário de exclusão suave, onde uma partilha de ficheiros pode ser eliminada e outra partilha de ficheiros pode ser criada com o mesmo nome, a identidade única de uma partilha de ficheiros Azure é agora um ID.

Para conhecer o ID único de cada item, execute o comando **Get-AzRecoveryServicesBackupItem** com os filtros relevantes para **o BackupManagementType** e **WorkloadType** para obter todos os itens relevantes. Em seguida, observe o campo de nomes no objeto/resposta PowerShell devolvido.

Recomendamos que registe itens e, em seguida, recupere o seu nome único no campo de nomes na resposta. Utilize este valor para filtrar os itens com o parâmetro *Nome.* Caso contrário, utilize o parâmetro *FriendlyName* para recuperar o item com o seu ID.

> [!IMPORTANT]
> Certifique-se de que o PowerShell é atualizado para a versão mínima (Az.RecoveryServices 2.6.0) para cópias de segurança de ações de ficheiros Azure. Com esta versão, o filtro *FriendlyName* está disponível para o comando **Get-AzRecoveryServicesBackupItem.**
>
> Passe o nome da partilha de ficheiros Azure para o parâmetro *FriendlyName.* Se passar o nome da partilha de ficheiros para o parâmetro *Nome,* esta versão lança um aviso para passar o nome para o parâmetro *FriendlyName.*
>
> A não instalação da versão mínima pode resultar numa falha dos scripts existentes. Instale a versão mínima do PowerShell utilizando o seguinte comando:
>
>```powershell
>Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
>```

## <a name="trigger-an-on-demand-backup"></a>Desencadear uma cópia de segurança a pedido

Utilize [backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) para executar uma cópia de segurança a pedido para uma partilha de ficheiros Azure protegida:

1. Recupere a conta de armazenamento do recipiente no cofre que detém os seus dados de backup utilizando [o Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. Para iniciar uma tarefa de backup, obtenha informações sobre a partilha de ficheiros Azure utilizando [o Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem).
3. Executar uma cópia de segurança a pedido utilizando [backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem).

Executar a cópia de segurança a pedido da seguinte forma:

```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -FriendlyName "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

O comando devolve um trabalho com uma ID a ser rastreada, como mostra o seguinte exemplo:

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

As fotos de partilha de ficheiros Azure são usadas enquanto as cópias de segurança são tomadas. Normalmente, o trabalho termina quando o comando devolve esta saída.

## <a name="next-steps"></a>Passos seguintes

* Saiba [como fazer backup dos Ficheiros Azure no portal Azure](backup-afs.md).
* Consulte o script da [amostra no GitHub](https://github.com/Azure-Samples/Use-PowerShell-for-long-term-retention-of-Azure-Files-Backup) para utilizar um runbook da Azure Automation para agendar cópias de segurança.
