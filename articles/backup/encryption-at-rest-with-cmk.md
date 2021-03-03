---
title: Encriptação de dados de cópias de segurança com chaves geridas pelo cliente
description: Saiba como o Azure Backup permite encriptar os seus dados de backup utilizando teclas geridas pelo cliente (CMK).
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: 474f4238276f460abde3d600422e309171875a0c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101716742"
---
# <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Encriptação de dados de cópias de segurança com chaves geridas pelo cliente

O Azure Backup permite-lhe encriptar os seus dados de backup utilizando teclas geridas pelo cliente (CMK) em vez de utilizar teclas geridas pela plataforma, que são ativadas por padrão. As suas chaves que são utilizadas para encriptar os dados de backup devem ser armazenadas no [Cofre da Chave Azure](../key-vault/index.yml).

A chave de encriptação utilizada para encriptar cópias de segurança pode ser diferente da utilizada para a origem. Os dados estão protegidos utilizando uma chave de encriptação de dados baseada em AES 256 (DEK), que, por sua vez, está protegida usando as suas chaves (KEK). Isto dá-lhe controlo total sobre os dados e as chaves. Para permitir a encriptação, é necessário que o cofre dos Serviços de Recuperação tenha acesso à chave de encriptação no Cofre da Chave Azure. Pode alterar a chave conforme e quando necessário.

Este artigo aborda o seguinte:

- Criação de um cofre de Serviços de Recuperação
- Configurar o cofre dos Serviços de Recuperação para encriptar dados de backup utilizando chaves geridas pelo cliente
- Executando backup para cofres encriptados usando chaves geridas pelo cliente
- Restaurar dados de backups

## <a name="before-you-start"></a>Antes de começar

- Esta funcionalidade permite-lhe encriptar **apenas os novos cofres dos Serviços de Recuperação.** Os cofres que contenham itens registados ou tentados registados não são suportados.

- Uma vez ativado para um cofre dos Serviços de Recuperação, a encriptação utilizando chaves geridas pelo cliente não pode ser revertida para usar as teclas geridas pela plataforma (padrão). Pode alterar as chaves de encriptação de acordo com os seus requisitos.

- Atualmente, esta funcionalidade **não suporta cópias de segurança utilizando o agente MARS**, e pode não ser capaz de utilizar um cofre encriptado cmk para o mesmo. O agente MARS utiliza uma encriptação baseada em palavras de utilizador. Esta funcionalidade também não suporta cópias de segurança dos VM clássicos.

- Esta funcionalidade não está relacionada com a [Encriptação do Disco Azure,](../security/fundamentals/azure-disk-encryption-vms-vmss.md)que utiliza encriptação baseada em convidados de discos de um VM utilizando BitLocker (para Windows) e DM-Crypt (para Linux)

- O cofre dos Serviços de Recuperação só pode ser encriptado com chaves armazenadas num Cofre de Chaves Azure, localizado na **mesma região.** Além disso, as chaves devem ser **apenas teclas RSA 2048** e devem estar em estado **ativado.**

- A movimentação do cofre dos Serviços de Recuperação encriptado da CMK através de Grupos de Recursos e Subscrições não é suportada atualmente.
- Quando você mover um cofre de Serviços de Recuperação já encriptado com chaves geridas pelo cliente para um novo inquilino, você precisará atualizar o cofre dos Serviços de Recuperação para recriar e reconfigurar a identidade gerida do cofre e CMK (que deve estar no novo inquilino). Se isto não for feito, as operações de backup e restauro começarão a falhar. Além disso, quaisquer permissões de controlo de acesso baseadas em funções (RBAC) criadas dentro da subscrição terão de ser reconfiguradas.

- Esta funcionalidade pode ser configurada através do portal Azure e PowerShell.

    >[!NOTE]
    >Utilize o módulo Az 5.3.0 ou superior para utilizar as chaves geridas pelo cliente para cópias de segurança no cofre dos Serviços de Recuperação.

Se não criou e configura o cofre dos Serviços de Recuperação, pode [ler como fazê-lo aqui.](backup-create-rs-vault.md)

## <a name="configuring-a-vault-to-encrypt-using-customer-managed-keys"></a>Configurar um cofre para encriptar usando chaves geridas pelo cliente

Esta secção envolve os seguintes passos:

1. Ativar a identidade gerida para o cofre dos Serviços de Recuperação

1. Atribua permissões ao cofre para aceder à chave de encriptação no Cofre da Chave Azure

1. Permitir a eliminação suave e a proteção de purga no Cofre da Chave Azure

1. Atribua a chave de encriptação ao cofre dos Serviços de Recuperação

É necessário que todos estes passos sejam seguidos na ordem acima mencionada para alcançar os resultados pretendidos. Cada passo é discutido em detalhe abaixo.

### <a name="enable-managed-identity-for-your-recovery-services-vault"></a>Ativar a identidade gerida para o cofre dos Serviços de Recuperação

O Azure Backup utiliza o sistema atribuído à identidade gerida para autenticar o cofre dos Serviços de Recuperação para aceder às chaves de encriptação armazenadas no Cofre da Chave Azure. Para ativar a identidade gerida para o cofre dos Serviços de Recuperação, siga os passos abaixo mencionados.

>[!NOTE]
>Uma vez ativada, a identidade gerida **não** deve ser desativada (mesmo temporariamente). Desativar a identidade gerida pode levar a um comportamento inconsistente.

**No portal:**

1. Vá para o cofre dos Serviços de Recuperação -> **Identidade**

    ![Definições de identidade](./media/encryption-at-rest-with-cmk/managed-identity.png)

1. Alterar o **Estado** para **On** e selecionar **Guardar**.

1. Um ID de objeto é gerado, que é a identidade gerida do sistema do cofre.

**Com PowerShell:**

Utilize o comando [Update-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/update-azrecoveryservicesvault) para permitir a identidade gerida atribuída pelo sistema para o cofre dos serviços de recuperação.

Exemplo:

```AzurePowerShell
$vault=Get-AzRecoveryServicesVault -ResourceGroupName "testrg" -Name "testvault"

Update-AzRecoveryServicesVault -IdentityType SystemAssigned -VaultId $vault.ID

$vault.Identity | fl
```

Resultado:

```output
PrincipalId : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
TenantId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Type        : SystemAssigned
```

### <a name="assign-permissions-to-the-recovery-services-vault-to-access-the-encryption-key-in-the-azure-key-vault"></a>Atribua permissões ao cofre dos Serviços de Recuperação para aceder à chave de encriptação no Cofre da Chave Azure

Agora precisa de permitir que o cofre dos Serviços de Recuperação aceda ao Cofre da Chave Azure que contém a chave de encriptação. Isto é feito permitindo que a identidade gerida do cofre dos Serviços de Recuperação aceda ao Cofre de Chaves.

**No portal:**

1. Aceda às suas Políticas de Acesso Azure Key -> **.** Continue a **+Adicionar Políticas de Acesso.**

    ![Adicionar Políticas de Acesso](./media/encryption-at-rest-with-cmk/access-policies.png)

1. Sob **permissões de chave**, selecione **Obter,** **Lista,** **Desembrulhar** as operações da chave de embrulho e **de embrulho.** Isto especifica as ações na chave que serão permitidas.

    ![Atribuir permissões-chave](./media/encryption-at-rest-with-cmk/key-permissions.png)

1. Vá ao **Select Principal** e procure o seu cofre na caixa de pesquisa usando o seu nome ou identidade gerida. Uma vez que apareça, selecione o cofre e escolha **Selecione** na parte inferior do painel.

    ![Selecione principal](./media/encryption-at-rest-with-cmk/select-principal.png)

1. Uma vez feito, **selecione Adicionar** para adicionar a nova política de acesso.

1. **Selecione Guardar** para guardar as alterações feitas na política de acesso do Cofre da Chave Azure.

### <a name="enable-soft-delete-and-purge-protection-on-the-azure-key-vault"></a>Permitir a eliminação suave e a proteção de purga no Cofre da Chave Azure

Tem de ativar a **proteção de eliminação e purga suave** no cofre da chave Azure que armazena a sua chave de encriptação. Pode fazê-lo a partir do Cofre da Chave Azure UI, como mostrado abaixo. (Alternativamente, estas propriedades podem ser definidas enquanto criam o Cofre da Chave). Leia mais sobre estas propriedades do Key Vault [aqui.](../key-vault/general/soft-delete-overview.md)

![Ativar a eliminação recuperável e a remoção da proteção](./media/encryption-at-rest-with-cmk/soft-delete-purge-protection.png)

Também pode ativar a proteção de eliminação e purga suave através do PowerShell utilizando os passos abaixo:

1. Inscreva-se na sua Conta Azure.

    ```azurepowershell
    Login-AzAccount
    ```

1. Selecione a subscrição que contém o seu cofre.

    ```azurepowershell
    Set-AzContext -SubscriptionId SubscriptionId
    ```

1. Ativar a eliminação recuperável

    ```azurepowershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "AzureKeyVaultName").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"
    ```

    ```azurepowershell
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

1. Permitir a proteção da purga

    ```azurepowershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "AzureKeyVaultName").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"
    ```

    ```azurepowershell
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

### <a name="assign-encryption-key-to-the-rs-vault"></a>Atribua chave de encriptação ao cofre RS

>[!NOTE]
> Antes de prosseguir, certifique-se:
>
> - Todos os passos acima mencionados foram concluídos com sucesso:
>   - A identidade gerida do cofre dos Serviços de Recuperação foi ativada, e foram atribuídas permissões necessárias
>   - O Cofre da Chave Azure tem proteção de eliminação e purga ativada
> - O cofre dos Serviços de Recuperação para o qual pretende ativar a encriptação CMK **não tem** quaisquer itens protegidos ou registados

Uma vez assegurados os acima, continue a selecionar a chave de encriptação para o seu cofre.

#### <a name="to-assign-the-key-in-the-portal"></a>Para atribuir a chave no portal

1. Vá ao cofre dos Serviços de Recuperação -> **Propriedades**

    ![Definições de encriptação](./media/encryption-at-rest-with-cmk/encryption-settings.png)

1. Selecione **Atualização** em **Definições de Encriptação**.

1. No painel de Definições de Encriptação, selecione **Use a sua própria tecla** e continue a especificar a chave utilizando uma das seguintes formas. **Certifique-se de que a chave que pretende utilizar é uma chave RSA 2048, que está em estado ativado.**

    1. Introduza o **Key URI** com o qual pretende encriptar os dados neste cofre dos Serviços de Recuperação. Também precisa de especificar a subscrição na qual está presente o Cofre da Chave Azure (que contém esta chave). Esta chave URI pode ser obtida a partir da chave correspondente no seu Cofre de Chave Azure. Certifique-se de que a chave URI é copiada corretamente. Recomenda-se que utilize o botão **Copy para a área de transferência** fornecido com o identificador de chave.

        >[!NOTE]
        >Ao especificar a chave de encriptação utilizando o Key URI, a tecla não será rodada automaticamente. Assim, as atualizações das chaves terão de ser feitas manualmente, especificando a nova chave quando necessário.

        ![Insira a chave URI](./media/encryption-at-rest-with-cmk/key-uri.png)

    1. Navegue e selecione a chave do Cofre de Chaves no painel de recolha de chaves.

        >[!NOTE]
        >Ao especificar a chave de encriptação utilizando o painel de recolha de chaves, a tecla será rodada automaticamente sempre que uma nova versão para a tecla estiver ativada.

        ![Selecione a chave do cofre da chave](./media/encryption-at-rest-with-cmk/key-vault.png)

1. Selecione **Guardar**.

1. **Rastreio do progresso e do estado da atualização da chave de encriptação**: Pode acompanhar o progresso e o estado da atribuição da chave de encriptação utilizando a vista **Backup Jobs** na barra de navegação esquerda. O estado deverá em breve mudar para **Concluído**. O seu cofre irá agora encriptar todos os dados com a chave especificada como KEK.

    ![Estado concluído](./media/encryption-at-rest-with-cmk/status-succeeded.png)

    As atualizações das chaves de encriptação também estão registadas no Registo de Atividade do cofre.

    ![Registo de atividades](./media/encryption-at-rest-with-cmk/activity-log.png)

#### <a name="to-assign-the-key-with-powershell"></a>Para atribuir a chave com PowerShell

Utilize o comando [Set-AzRecoveryServicesVaultProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultproperty) para permitir a encriptação utilizando as teclas geridas pelo cliente e para atribuir ou atualizar a chave de encriptação a utilizar.

Exemplo:

```azurepowershell
$keyVault = Get-AzKeyVault -VaultName "testkeyvault" -ResourceGroupName "testrg" 
$key = Get-AzKeyVaultKey -VaultName $keyVault -Name "testkey" 
Set-AzRecoveryServicesVaultProperty -EncryptionKeyId $key.ID -KeyVaultSubscriptionId "xxxx-yyyy-zzzz"  -VaultId $vault.ID


$enc=Get-AzRecoveryServicesVaultProperty -VaultId $vault.ID
$enc.encryptionProperties | fl
```

Resultado:

```output
EncryptionAtRestType          : CustomerManaged
KeyUri                        : testkey
SubscriptionId                : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx 
LastUpdateStatus              : Succeeded
InfrastructureEncryptionState : Disabled
```

>[!NOTE]
> Este processo permanece o mesmo quando pretende atualizar ou alterar a chave de encriptação. Se desejar atualizar e utilizar uma chave de outro Cofre-Chave (diferente da que está a ser utilizada atualmente), certifique-se de que:
>
> - O cofre está localizado na mesma região que o cofre dos Serviços de Recuperação.
>
> - O cofre-chave tem proteção de eliminação e purga ativada
>
> - O cofre dos Serviços de Recuperação tem as permissões necessárias para aceder ao cofre da chave.

## <a name="backing-up-to-a-vault-encrypted-with-customer-managed-keys"></a>Backup até um cofre encriptado com chaves geridas pelo cliente

Antes de proceder à proteção de configuração, recomendamos vivamente que certifique-se de que a seguinte lista de verificação é respeitada. Isto é importante uma vez que uma vez configurado um item para ser configurado (ou tentado ser configurado) para um cofre encriptado não CMK, a encriptação usando chaves geridas pelo cliente não pode ser ativada e continuará a usar chaves geridas pela plataforma.

>[!IMPORTANT]
> Antes de proceder à proteção de configuração, deve ter concluído **com sucesso** os seguintes passos:
>
>1. Criei o seu cofre de reserva
>1. Habilitado a identidade gerida do cofre de reserva atribuído ao sistema
>1. Permissões atribuídas ao seu Cofre de Reserva para aceder a chaves de encriptação a partir do seu Cofre de Chaves
>1. Proteção de eliminação e purga suave ativada para o seu Cofre de Chaves
>1. Atribuiu uma chave de encriptação válida para o seu cofre de backup
>
>Se todos os passos acima foram confirmados, apenas então proceda com a configuração do backup.

O processo de configuração e realização de backups para um cofre dos Serviços de Recuperação encriptado com chaves geridas pelo cliente é o mesmo que um cofre que utiliza chaves geridas pela plataforma, **sem alterações na experiência.** Isto é válido para [a cópia de segurança dos VMs Azure,](./quick-backup-vm-portal.md) bem como para a cópia de segurança das cargas de trabalho que estão a funcionar dentro de um VM (por exemplo, BASES de dados SAP [HANA,](./tutorial-backup-sap-hana-db.md) [SQL Server).](./tutorial-sql-backup.md)

## <a name="restoring-data-from-backup"></a>Restaurar dados a partir de backup

### <a name="vm-backup"></a>Backup VM

Os dados armazenados no cofre dos Serviços de Recuperação podem ser restaurados de acordo com os passos [descritos aqui.](./backup-azure-arm-restore-vms.md) Ao restaurar a partir de um cofre de Serviços de Recuperação encriptado usando teclas geridas pelo cliente, pode optar por encriptar os dados restaurados com um Conjunto de Encriptação de Disco (DES).

#### <a name="restoring-vm--disk"></a>Restaurar VM / disco

1. Ao recuperar o disco / VM de um ponto de recuperação "Snapshot", os dados restaurados serão encriptados com o DES utilizado para encriptar os discos da origem VM.

1. Ao restaurar o disco / VM de um ponto de recuperação com o Tipo de Recuperação como "Cofre", pode optar por ter os dados restaurados encriptados utilizando um DES, especificado no momento da restauração. Em alternativa, pode optar por continuar com a restauração dos dados sem especificar um DES, caso em que será encriptado utilizando as teclas geridas pela Microsoft.

Pode encriptar o disco restaurado /VM após a restauração estar concluída, independentemente da seleção feita durante o início da restauração.

![Pontos de restauro](./media/encryption-at-rest-with-cmk/restore-points.png)

#### <a name="select-a-disk-encryption-set-while-restoring-from-vault-recovery-point"></a>Selecione um conjunto de encriptação de disco enquanto restaura a partir do ponto de recuperação do cofre

**No portal:**

O Conjunto de Encriptação do Disco é especificado nas Definições de Encriptação no painel de restauro, como mostrado abaixo:

1. **No(s) do (s) disco criptografe utilizando a sua tecla,** selecione **Sim**.

1. A partir do dropdown, selecione o DES que pretende utilizar para o ous disco restaurado. **Certifique-se de ter acesso ao DES.**

>[!NOTE]
>A capacidade de escolher um DES durante a restauração não está disponível se estiver a restaurar um VM que utiliza encriptação do disco Azure.

![Criptografe o disco usando a sua chave](./media/encryption-at-rest-with-cmk/encrypt-disk-using-your-key.png)

**Com PowerShell:**

Utilize o comando [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) com o parâmetro [ `-DiskEncryptionSetId <string>` ] para [especificar o DES](/powershell/module/az.compute/get-azdiskencryptionset) a ser utilizado para encriptar o disco restaurado. Para obter mais informações sobre a restauração de discos a partir de cópias de segurança VM, consulte [este artigo](./backup-azure-vms-automation.md#restore-an-azure-vm).

Exemplo:

```azurepowershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $vault.ID
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM" -VaultId $vault.ID
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -VaultId $vault.ID
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -DiskEncryptionSetId “testdes1” -VaultId $vault.ID
```

#### <a name="restoring-files"></a>Restaurar ficheiros

Ao executar uma restauração de ficheiros, os dados restaurados serão encriptados com a chave utilizada para encriptar a localização do alvo.

### <a name="restoring-sap-hanasql-databases-in-azure-vms"></a>Restaurar bases de dados SAP HANA/SQL em VMs Azure

Ao restaurar a partir de uma base de dados SAP HANA/SQL com rede de apoio que funciona num Azure VM, os dados restaurados serão encriptados utilizando a chave de encriptação utilizada no local de armazenamento do alvo. Pode ser uma chave gerida pelo cliente ou uma chave gerida pela plataforma usada para encriptar os discos do VM.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="can-i-encrypt-an-existing-backup-vault-with-customer-managed-keys"></a>Posso encriptar um cofre de reserva existente com chaves geridas pelo cliente?

Não, a encriptação CMK só pode ser ativada para novos cofres. Então o cofre nunca deve ter protegido itens. De facto, não devem ser feitas quaisquer tentativas de proteção de quaisquer itens no cofre antes de permitir a encriptação utilizando chaves geridas pelo cliente.

### <a name="i-tried-to-protect-an-item-to-my-vault-but-it-failed-and-the-vault-still-doesnt-contain-any-items-protected-to-it-can-i-enable-cmk-encryption-for-this-vault"></a>Tentei proteger um objeto para o meu cofre, mas falhou, e o cofre ainda não contém nenhum objeto protegido. Posso permitir a encriptação cmk para este cofre?

Não, o cofre não deve ter tido nenhuma tentativa de proteger quaisquer objetos no passado.

### <a name="i-have-a-vault-thats-using-cmk-encryption-can-i-later-revert-to-encryption-using-platform-managed-keys-even-if-i-have-backup-items-protected-to-the-vault"></a>Tenho um cofre que está a usar encriptação CMK. Posso mais tarde voltar à encriptação usando chaves geridas pela plataforma, mesmo que tenha itens de reserva protegidos para o cofre?

Não, uma vez ativada a encriptação cmk, não pode ser revertida para usar chaves geridas pela plataforma. Pode alterar as chaves utilizadas de acordo com os seus requisitos.

### <a name="does-cmk-encryption-for-azure-backup-also-apply-to-azure-site-recovery"></a>A encriptação CMK para backup Azure também se aplica à Recuperação do Site Azure?

Não, este artigo discute apenas a encriptação de dados de backup. Para a recuperação do site Azure, você precisa definir a propriedade separadamente como disponível a partir do serviço.

### <a name="i-missed-one-of-the-steps-in-this-article-and-went-on-to-protect-my-data-source-can-i-still-use-cmk-encryption"></a>Perdi um dos passos deste artigo e fui proteger a minha fonte de dados. Ainda posso usar encriptação CMK?

Não seguir os passos do artigo e continuar a proteger os itens pode levar a que o cofre não possa utilizar a encriptação utilizando as teclas geridas pelo cliente. Por isso, recomenda-se que consulte [esta lista de verificação](#backing-up-to-a-vault-encrypted-with-customer-managed-keys) antes de continuar a proteger os itens.

### <a name="does-using-cmk-encryption-add-to-the-cost-of-my-backups"></a>A utilização da encriptação CMK aumenta o custo das minhas cópias de segurança?

A utilização da encriptação CMK para cópia de segurança não incorre em custos adicionais para si. No entanto, pode continuar a incorrer em custos para a utilização do seu Cofre de Chaves Azure, onde a sua chave está armazenada.

## <a name="next-steps"></a>Passos seguintes

- [Visão geral das funcionalidades de segurança no Azure Backup](security-overview.md)
