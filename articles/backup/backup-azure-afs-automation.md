---
title: Faça uma parte do ficheiro Azure utilizando a PowerShell
description: Neste artigo, aprenda a fazer backup de uma partilha de ficheiros Azure Files utilizando o serviço de backup Azure e powerShell.
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 53187152802908e94ee4a8a231d3b7874cf42422
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83199344"
---
# <a name="back-up-an-azure-file-share-by-using-powershell"></a>Faça uma parte do ficheiro Azure utilizando a PowerShell

Este artigo descreve como usar o Azure PowerShell para fazer backup uma partilha de ficheiros Azure Files através de um cofre dos Serviços de Recuperação de [Backup Azure.](backup-overview.md)

Este artigo explica como:

> [!div class="checklist"]
>
> * Configurar a PowerShell e registar o fornecedor de Serviços de Recuperação.
> * Crie um cofre dos Serviços de Recuperação.
> * Configure a cópia de segurança para uma partilha de ficheiros Azure.
> * Faça um trabalho de reserva.

## <a name="before-you-start"></a>Antes de começar

* [Saiba mais](backup-azure-recovery-services-vault-overview.md) sobre cofres dos Serviços de Recuperação.
* Consulte a referência de referência Az.RecoveryServices [cmdlet](/powershell/module/az.recoveryservices) na biblioteca Azure.
* Reveja a seguinte hierarquia de objetos PowerShell para Serviços de Recuperação:

  ![Serviços de Recuperação objetam hierarquia](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

## <a name="set-up-powershell"></a>Configurar powerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Configurar o PowerShell da seguinte forma:

1. [Descarregue a versão mais recente do Azure PowerShell.](/powershell/azure/install-az-ps)

    > [!NOTE]
    > A versão powerShell mínima necessária para backup de ações de ficheiros Azure é Az.RecoveryServices 2.6.0. Usar a versão mais recente, ou pelo menos a versão mínima, ajuda-o a evitar problemas com scripts existentes. Instale a versão mínima utilizando o seguinte comando PowerShell:
    >
    > ```powershell
    > Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
    > ```

2. Encontre os cmdlets PowerShell para backup azure utilizando este comando:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Reveja os pseudónimos e cmdlets para Azure Backup, Azure Site Recovery e o cofre dos Serviços de Recuperação. Aqui está um exemplo do que pode ver. Não é uma lista completa de cmdlets.

    ![Lista de Serviços de Recuperação cmdlets](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Inscreva-se na sua conta Azure utilizando o **Connect-AzAccount**.
5. Na página web que aparece, é-lhe pedido que introduza as credenciais da sua conta.

    Em alternativa, pode incluir as credenciais da sua conta como parâmetro no cmdlet **Connect-AzAccount** utilizando **-Credencial**.
   
    Se é um parceiro da CSP a trabalhar em nome de um inquilino, especifique o cliente como inquilino. Use o seu nome de domínio primário de inquilino ou inquilino. Um exemplo é **Connect-AzAccount -Tenant "fabrikam.com".**

6. Associe a subscrição que pretende utilizar com a conta, porque uma conta pode ter várias subscrições:

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Se estiver a utilizar o Azure Backup pela primeira vez, utilize o **cmdlet Register-AzResourceProvider** para registar o fornecedor de Serviços de Recuperação Do Azure com a sua subscrição:

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. Verifique se os fornecedores registaram com sucesso:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. Na saída do comando, verifique se o **Estado de Registo** altera-se ao **Registo**. Se não o fizer, volte a executar o **cmdlet Register-AzResourceProvider.**

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 

O cofre dos Serviços de Recuperação é um recurso do Gestor de Recursos, por isso deve colocá-lo num grupo de recursos. Pode utilizar um grupo de recursos existente, ou pode criar um grupo de recursos utilizando o cmdlet **New-AzResourceGroup.** Quando criar um grupo de recursos, especifique o nome e a localização para o mesmo.

Siga estes passos para criar um cofre de Serviços de Recuperação:

1. Se não tiver um grupo de recursos existente, crie um novo utilizando o cmdlet [New-AzResourceGroup.](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0) Neste exemplo, criamos um grupo de recursos na região oeste dos EUA:

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "West US"
   ```

2. Utilize o [cmdlet New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) para criar o cofre. Especifique a mesma localização para o cofre que usou para o grupo de recursos.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Especifique o tipo de redundância a utilizar para o armazenamento do cofre. Pode utilizar [armazenamento redundante localmente](../storage/common/storage-redundancy-lrs.md) ou [armazenamento geo-redundante.](../storage/common/storage-redundancy-grs.md)
   
   O exemplo seguinte define a opção **-BackupStorageStorageRedundancy** para o [set-AzRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) cmdlet para **o testvault** definido para **GeoRedundant**:

   ```powershell
   $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
   Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
   ```

### <a name="view-the-vaults-in-a-subscription"></a>Veja os cofres em uma subscrição

Para ver todos os cofres da subscrição, utilize [o Get-AzRecoveryServicesVault:](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0)

```powershell
Get-AzRecoveryServicesVault
```

A saída é semelhante à seguinte. Note que a saída fornece o grupo de recursos associados e a localização.

```powershell
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

Muitos cmdlets de backup Azure requerem o objeto do cofre dos Serviços de Recuperação como entrada, por isso é conveniente armazenar o objeto do cofre numa variável.

O contexto do cofre é o tipo de dados protegidos no cofre. Desloque-o utilizando o [Set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0). Após o contexto, aplica-se a todos os cmdlets subsequentes.

O exemplo que se segue define o contexto do cofre para **o testvault:**

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Pegue a identificação do cofre

Planeamos depreter o contexto do cofre de acordo com as diretrizes da Azure PowerShell. Em vez disso, pode armazenar ou obter a identificação do cofre, e passá-la para comandos relevantes. Se não definiu o contexto do cofre ou quiser especificar o comando para correr para um determinado cofre, passe a identificação do cofre `-vaultID` quanto a todos os comandos relevantes da seguinte forma:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultID $vaultID
```

## <a name="configure-a-backup-policy"></a>Configurar uma política de backup

Uma política de backup especifica o calendário dos backups e quanto tempo devem ser mantidos os pontos de recuperação de backup.

Uma política de backup está associada a pelo menos uma política de retenção. Uma política de retenção define quanto tempo um ponto de recuperação é mantido antes de ser apagado. Pode configurar cópias de segurança com retenção diária, semanal, mensal ou anual.

Aqui estão algumas cmdlets para políticas de backup:

* Ver a retenção da política de backup predefinida utilizando [get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0).
* Consulte o calendário de política de backup predefinido utilizando [o Get-AzRecoveryServicesBackupAgendaPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0).
* Crie uma nova política de backup utilizando a [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0). Introduza os objetos da política de agenda e retenção como entrada.

Por padrão, um tempo de início é definido no objeto de política de agenda. Utilize o seguinte exemplo para alterar o tempo de início para a hora de início desejada. O horário de início desejado deve ser no Tempo Coordenado Universal (UTC). O exemplo pressupõe que a hora de início desejada é 01:00 AM UTC para backups diários.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> Precisa de fornecer a hora de início apenas em múltiplos de 30 minutos. No exemplo anterior, só pode ser "01:00:00" ou "02:30:00". A hora de início não pode ser "01:15:00".

O exemplo seguinte armazena a política de horários e a política de retenção em variáveis. Em seguida, utiliza essas variáveis como parâmetros para uma nova política **(NewAFSPolicy).** **A NewAFSPolicy** recebe um backup diário e mantém-na durante 30 dias.

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

## <a name="enable-backup"></a>Ativar a cópia de segurança

Depois de definir a política de backup, pode ativar a proteção da partilha de ficheiros Azure utilizando a política.

### <a name="retrieve-a-backup-policy"></a>Recuperar uma política de backup

Você obtém o objeto de política relevante usando [Get-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0). Utilize este cmdlet para visualizar as políticas associadas a um tipo de carga de trabalho, ou para obter uma política específica.

#### <a name="retrieve-a-policy-for-a-workload-type"></a>Recuperar uma política para um tipo de carga de trabalho

O exemplo seguinte recupera as políticas para o tipo de carga de trabalho **AzureFiles:**

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
> O fuso horário do campo **BackupTime** em PowerShell está na UTC. Quando o tempo de backup é mostrado no portal Azure, o tempo é ajustado ao seu fuso horário local.

#### <a name="retrieve-a-specific-policy"></a>Recuperar uma política específica

A seguinte política recupera a política de backup chamada **dailyafs:**

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-protection-and-apply-the-policy"></a>Permitir a proteção e aplicar a política

Ativar a proteção utilizando a [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). Depois da apólice estar associada ao cofre, os backups são acionados de acordo com o calendário de apólices.

O exemplo seguinte permite a proteção para o teste de partilha de ficheiros **AzureFileShare** no teste da conta de **armazenamentoStorageAcct,** com os **diários**de política:

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

O comando aguarda até que o trabalho de proteção configurada esteja terminado e dê uma saída semelhante ao seguinte exemplo:

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

## <a name="important-notice-backup-item-identification"></a>Aviso importante: Identificação de artigode cópia de segurança

Esta secção descreve uma alteração importante nas cópias de segurança das ações de ficheiros Azure na preparação para a disponibilidade geral.

Ao mesmo tempo que permite uma cópia de segurança para as partilhas de ficheiros Azure, o utilizador dá ao cliente um nome de partilha de ficheiros como nome da entidade, sendo criado um item de backup. O nome do item de reserva é um identificador único que o serviço de backup Azure cria. Normalmente, o identificador é um nome fácil de usar. Mas para lidar com o cenário de eliminação suave, onde uma parte de ficheiro pode ser eliminada e outra partilha de ficheiros pode ser criada com o mesmo nome, a identidade única de uma partilha de ficheiros Azure é agora um ID. 

Para conhecer o ID único de cada item, execute o comando **Get-AzRecoveryServicesBackupItem** com os filtros relevantes para **backupManagementType** e **WorkloadType** para obter todos os itens relevantes. Em seguida, observe o campo de nome no objeto/resposta powerShell devolvido. 

Recomendamos que enumere itens e, em seguida, recupere o seu nome único a partir do campo de nome na resposta. Utilize este valor para filtrar os itens com o parâmetro *Nome.* Caso contrário, utilize o parâmetro *FriendlyName* para recuperar o item com o seu ID.

> [!IMPORTANT]
> Certifique-se de que o PowerShell é atualizado para a versão mínima (Az.RecoveryServices 2.6.0) para cópias de segurança das partilhas de ficheiros Azure. Com esta versão, o filtro *FriendlyName* está disponível para o comando **Get-AzRecoveryServicesBackupItem.** 
>
> Passe o nome da partilha de ficheiros Azure para o parâmetro *FriendlyName.* Se passar o nome da parte do ficheiro para o parâmetro *Nome,* esta versão lança um aviso para passar o nome para o parâmetro *FriendlyName.* 
>
> A não instalação da versão mínima pode resultar numa falha dos scripts existentes. Instale a versão mínima da PowerShell utilizando o seguinte comando:
>
>```powershell
>Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
>```

## <a name="trigger-an-on-demand-backup"></a>Desencadear um reforço a pedido

Utilize [backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem?view=azps-1.4.0) para executar uma cópia de segurança a pedido para uma partilha de ficheiros Azure protegida:

1. Recupere a conta de armazenamento do recipiente no cofre que detém os seus dados de backup utilizando o [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. Para iniciar um trabalho de backup, obtenha informações sobre a partilha de ficheiros Azure utilizando [o Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem).
3. Faça uma cópia de segurança a pedido utilizando [backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem).

Faça o backup a pedido da seguinte forma:

```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -FriendlyName "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

O comando devolve um trabalho com uma identificação a ser rastreada, como mostra o seguinte exemplo:

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

As imagens de partilha de ficheiros Azure são utilizadas enquanto as cópias de segurança são tiradas. Normalmente o trabalho termina quando o comando devolve esta saída.

## <a name="next-steps"></a>Passos seguintes

- Saiba apoiar [os Ficheiros Azure no portal Azure.](backup-afs.md)
- Consulte o [script da amostra no GitHub](https://github.com/Azure-Samples/Use-PowerShell-for-long-term-retention-of-Azure-Files-Backup) para utilizar um livro de execução da Automação Azure para agendar backups.
