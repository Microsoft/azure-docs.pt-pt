---
title: Back up Ficheiros Azure com PowerShell
description: Neste artigo, aprenda a fazer backup de Ficheiros Azure utilizando o serviço de backup Azure e powerShell.
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: a80589fb45937949b3612e12139ab1615bc1620d
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086937"
---
# <a name="back-up-azure-files-with-powershell"></a>Back up Ficheiros Azure com PowerShell

Este artigo descreve como usar o Azure PowerShell para fazer backup uma partilha de ficheiros Azure Files usando um cofre dos Serviços de Recuperação de [Backup Azure.](backup-overview.md)

Este artigo explica como:

> [!div class="checklist"]
>
> * Configure o PowerShell e registre o provedor de serviços de recuperação do Azure.
> * Crie um cofre dos Serviços de Recuperação.
> * Configure o backup para um compartilhamento de arquivos do Azure.
> * Execute um trabalho de backup.

## <a name="before-you-start"></a>Antes de começar

* [Saiba mais](backup-azure-recovery-services-vault-overview.md) sobre cofres dos Serviços de Recuperação.
* Leia sobre as capacidades de pré-visualização para apoiar as ações de [ficheiros do Azure](backup-afs.md).
* Reveja a hierarquia de objetos PowerShell para Serviços de Recuperação.

## <a name="recovery-services-object-hierarchy"></a>Serviços de Recuperação objetam hierarquia

A hierarquia do objeto é resumida no diagrama seguinte.

![Serviços de Recuperação objetam hierarquia](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Consulte a referência de referência **Az.RecoveryServices** [cmdlet](/powershell/module/az.recoveryservices) na biblioteca Azure.

## <a name="set-up-and-install"></a>Configurar e instalar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Configure o PowerShell da seguinte maneira:

1. [Descarregue a versão mais recente do Az PowerShell.](/powershell/azure/install-az-ps) A versão mínima necessária é a 1.0.0.

> [!WARNING]
> A versão mínima do PS necessária para pré-visualização foi 'Az 1.0.0'. Devido às próximas alterações para a GA, a versão PS mínima necessária será 'Az.RecoveryServices 2.6.0'. É muito importante atualizar todas as versões PS existentes para esta versão. Caso contrário, os scripts existentes quebrar-se-ão após a AG. Instale a versão mínima com os seguintes comandos PS

```powershell
Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
```

2. Localize os cmdlets do PowerShell de backup do Azure com este comando:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Examine os aliases e cmdlets para o backup do Azure, Azure Site Recovery e o cofre dos serviços de recuperação são exibidos. Aqui está um exemplo do que você pode ver. Não é uma lista completa de cmdlets.

    ![Lista de cmdlets dos serviços de recuperação](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Inscreva-se na sua conta Azure com **o Connect-AzAccount**.
5. Na página da Web que aparece, você será solicitado a inserir suas credenciais de conta.

    * Alternadamente, pode incluir as credenciais da sua conta como parâmetro no cmdlet **Connect-AzAccount** com **-Credencial**.
    * Se você for um parceiro CSP trabalhando em nome de um locatário, especifique o cliente como um locatário, usando seu nome de domínio de locatárioid ou de locatário primário. Um exemplo é **o Connect-AzAccount -Tenant** fabrikam.com.

6. Associe a assinatura que você deseja usar com a conta, pois uma conta pode ter várias assinaturas.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Se estiver a utilizar o Azure Backup pela primeira vez, utilize o **cmdlet Register-AzResourceProvider** para registar o fornecedor de Serviços de Recuperação Azure com a sua subscrição.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. Verifique se os provedores foram registrados com êxito:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. Na saída do comando, verifique se o **Estado de Registo** altera-se ao **Registo**. Se não o fizer, volte a executar o **cmdlet Register-AzResourceProvider.**

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

O cofre dos serviços de recuperação é um recurso do Resource Manager, portanto, você deve colocá-lo em um grupo de recursos. Pode utilizar um grupo de recursos existente, ou pode criar um grupo de recursos com o cmdlet **New-AzResourceGroup.** Ao criar um grupo de recursos, especifique o nome e o local do grupo de recursos.

Siga estas etapas para criar um cofre dos serviços de recuperação.

1. Um cofre é colocado em um grupo de recursos. Se não tiver um grupo de recursos existente, crie um novo com o [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0). Neste exemplo, criamos um novo grupo de recursos na região oeste dos EUA.

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "West US"
   ```

2. Utilize o [cmdlet New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) para criar o cofre. Especifique o mesmo local para o cofre que foi usado para o grupo de recursos.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Especifique o tipo de redundância a ser usado para o armazenamento do cofre.

   * Pode utilizar [armazenamento redundante localmente](../storage/common/storage-redundancy-lrs.md) ou [armazenamento geo-redundante.](../storage/common/storage-redundancy-grs.md)
   * O exemplo seguinte define a opção **-BackupStorageStorageRedundancy** para o[Set-AzRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) cmd para **o testvault** definido para **GeoRedundant**.

     ```powershell
     $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
     Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
     ```

### <a name="view-the-vaults-in-a-subscription"></a>Veja os cofres em uma subscrição

Para ver todos os cofres da subscrição, utilize [o Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0).

```powershell
Get-AzRecoveryServicesVault
```

A saída é semelhante à seguinte. Observe que o grupo de recursos e o local associados são fornecidos.

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

Armazene o objeto de cofre em uma variável e defina o contexto do cofre.

* Muitos cmdlets de backup do Azure exigem o objeto de cofre dos serviços de recuperação como uma entrada, portanto, é conveniente armazenar o objeto de cofre em uma variável.
* O contexto do cofre é o tipo de dados protegidos no cofre. Detete-o com [set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0). Depois que o contexto é definido, ele se aplica a todos os cmdlets subsequentes.

O exemplo que se segue define o contexto do cofre para **o testvault**.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Pegue a identificação do cofre

Planeamos depreciar a definição do contexto do cofre de acordo com as diretrizes da Azure PowerShell. Em vez disso, pode armazenar ou obter a identificação do cofre, e passá-la para comandos relevantes. Portanto, se você não tiver definido o contexto do cofre ou desejar especificar o comando a ser executado para um determinado cofre, passe a ID do cofre como "-vaultid" para todo o comando relevante da seguinte maneira:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultID $vaultID
```

## <a name="configure-a-backup-policy"></a>Configurar uma política de backup

Uma política de backup especifica o agendamento de backups e por quanto tempo os pontos de recuperação de backup devem ser mantidos:

* Uma política de backup está associada a pelo menos uma política de retenção. Uma política de retenção define por quanto tempo um ponto de recuperação é mantido antes de ser excluído.
* Ver a retenção da política de backup predefinida utilizando [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0).
* Consulte o calendário de política de backup predefinido utilizando [o Get-AzRecoveryServicesBackupAgendaPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0).
* Utiliza o [cmdlet New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) para criar uma nova política de backup. Você insere os objetos de política de retenção e agendamento.

Por predefinição, um tempo de início é definido no Objeto de Política de Agenda. Utilize o seguinte exemplo para alterar o tempo de início para a hora de início desejada. A hora de início desejada também deve ser na UTC. O exemplo abaixo pressupõe que a hora de início desejada é 01:00 AM UTC para backups diários.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> Precisa fornecer a hora de início em apenas 30 minutos múltiplos. No exemplo acima, só pode ser "01:00:00" ou "02:30:00". A hora de início não pode ser "01:15:00"

O exemplo seguinte armazena a política de horários e a política de retenção em variáveis. Em seguida, utiliza essas variáveis como parâmetros para uma nova política **(NewAFSPolicy).** **A NewAFSPolicy** recebe um backup diário e mantém-na durante 30 dias.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

A saída é semelhante à seguinte.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2019 1:30:00 AM
```

## <a name="enable-backup"></a>Habilitar backup

Depois de definir a política de backup, você pode habilitar a proteção para o compartilhamento de arquivos do Azure usando a política.

### <a name="retrieve-a-backup-policy"></a>Recuperar uma política de backup

Você obtém o objeto de política relevante com [Get-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0). Use este cmdlet para obter uma política específica ou para exibir as políticas associadas a um tipo de carga de trabalho.

#### <a name="retrieve-a-policy-for-a-workload-type"></a>Recuperar uma política para um tipo de carga de trabalho

O exemplo seguinte recupera políticas para o tipo de carga de trabalho **AzureFiles**.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

A saída é semelhante à seguinte.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> O fuso horário do campo **BackupTime** em PowerShell é o Tempo Coordenado Universal (UTC). Quando o horário do backup é mostrado na portal do Azure, a hora é ajustada para o fuso horário local.

### <a name="retrieve-a-specific-policy"></a>Recuperar uma política específica

A seguinte política recupera a política de backup chamada **dailyafs**.

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-backup-and-apply-policy"></a>Habilitar backup e aplicar política

Ativar a proteção com [a Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). Depois que a política estiver associada ao cofre, os backups serão disparados de acordo com o agendamento da política.

O exemplo seguinte permite a proteção para o teste de partilha de ficheiros **AzureFileShare** no teste da conta de **armazenamentoStorageAcct,** com os **diários**de política .

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

O comando aguarda até que o trabalho configurar proteção seja concluído e forneça uma saída semelhante, conforme mostrado.

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

## <a name="important-notice---backup-item-identification-for-afs-backups"></a>Aviso importante - Identificação de artigode cópia de segurança para backups AFS

Esta secção descreve as alterações na recuperação de itens de backup para backups AFS desde a pré-visualização para ga.

Ao mesmo tempo que permite a cópia de segurança para AFS, o utilizador fornece o nome de partilha de ficheiros amigável para o cliente como nome da entidade e é criado um item de backup. O 'nome' do item de reserva é um identificador único criado pelo serviço Azure Backup. Normalmente, o identificador envolve o nome amigável do utilizador. Mas houve uma mudança na forma como os serviços do Azure identificam internamente uma partilha de ficheiros azure de forma única. Isto significa que o nome único do item de reserva para backup AFS será um GUID e não terá qualquer relação com o nome amigável do cliente. Para conhecer o nome único de cada item, basta executar o comando ```Get-AzRecoveryServicesBackupItem``` com os filtros relevantes para backupManagementType e WorkloadType para obter todos os itens relevantes e, em seguida, observar o campo de nome no objeto/resposta PS devolvido. Recomenda-se sempre a listar itens e, em seguida, recuperar o seu nome único a partir do campo 'nome' em resposta. Utilize este valor para filtrar os itens com o parâmetro 'Nome'. Caso contrário, utilize o parâmetro FriendlyName para recuperar o item com o seu nome/identificador amigo do cliente.

> [!WARNING]
> Certifique-se de que a versão PS é atualizada para a versão mínima para 'Az.RecoveryServices 2.6.0' para backups AFS. Com esta versão, o filtro 'friendlyName' está disponível para ```Get-AzRecoveryServicesBackupItem``` comando. Passe o nome da partilha de ficheiros azul para o parâmetro friendlyName. Se passar o nome da partilha de ficheiros azul para o parâmetro 'Nome', esta versão lança um aviso para passar este nome amigável ao parâmetro de nome amigável. A não instalação desta versão mínima pode resultar na falha dos scripts existentes. Instale a versão mínima do PS com o seguinte comando.

```powershell
Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
```

## <a name="trigger-an-on-demand-backup"></a>Desencadear um reforço a pedido

Utilize [backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem?view=azps-1.4.0) para executar uma cópia de segurança a pedido para uma partilha de ficheiros Azure protegida.

1. Recupere a conta de armazenamento do recipiente no cofre que detém os seus dados de backup com [o Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. Para iniciar um trabalho de backup, obtém informações sobre a partilha de ficheiros Azure com [o Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem).
3. Faça uma cópia de segurança a pedido com[backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem).

Execute o backup sob demanda da seguinte maneira:

```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -FriendlyName "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

O comando retorna um trabalho com uma ID a ser rastreada, conforme mostrado no exemplo a seguir.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

Os instantâneos de compartilhamento de arquivos do Azure são usados enquanto os backups são feitos, geralmente o trabalho é concluído no momento em que o comando retorna essa saída.

### <a name="using-on-demand-backups-to-extend-retention"></a>Usando backups sob demanda para estender a retenção

Backups sob demanda podem ser usados para manter seus instantâneos por 10 anos. Os agendadores podem ser usados para executar scripts do PowerShell sob demanda com retenção escolhida e, portanto, tirar instantâneos em intervalos regulares a cada semana, mês ou ano. Ao tirar fotografias regulares, consulte as [limitações das cópias de segurança](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#how-many-on-demand-backups-can-i-take-per-file-share) a pedido utilizando cópias de segurança Azure.

Se estiver à procura de scripts de amostra, pode consultar o script da amostra no GitHub (<https://github.com/Azure-Samples/Use-PowerShell-for-long-term-retention-of-Azure-Files-Backup>), utilizando o livro de execução Azure Automation que lhe permite agendar backups numa base periódica e retê-los até 10 anos.

> [!WARNING]
> Certifique-se de que a versão PS é atualizada para a versão mínima para 'Az.RecoveryServices 2.6.0' para backups AFS nos seus livros de automação. Terá de substituir o antigo módulo 'AzureRM' por módulo 'Az'. Com esta versão, o filtro 'friendlyName' está disponível para ```Get-AzRecoveryServicesBackupItem``` comando. Passe o nome da partilha de ficheiros azul para o parâmetro friendlyName. Se passar o nome da partilha de ficheiros azul para o parâmetro 'Nome', esta versão lança um aviso para passar este nome amigável ao parâmetro de nome amigável.

## <a name="next-steps"></a>Passos seguintes

[Saiba apoiar](backup-afs.md) os Ficheiros Azure no portal Azure.
