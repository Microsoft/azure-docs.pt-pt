---
title: Restaurar chave do cofre & segredo para VM encriptado
description: Saiba como restaurar a chave do Cofre e o segredo em Azure Backup usando PowerShell
ms.topic: conceptual
ms.date: 08/28/2017
ms.openlocfilehash: 456ce18f253ffa02cd6b13826a7839f18beecba7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88827091"
---
# <a name="restore-key-vault-key-and-secret-for-encrypted-vms-using-azure-backup"></a>Restaurar a chave do Key Vault e o segredo para VMs encriptadas utilizando o Azure Backup

Este artigo fala sobre a utilização do Azure VM Backup para executar a restauração de VMs Azure encriptados, se a sua chave e segredo não existirem no cofre da chave. Estes passos também podem ser utilizados se pretender manter uma cópia separada da chave (Chave de encriptação de chaves) e secreta (Chave de encriptação BitLocker) para o VM restaurado.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

* **VMs encriptados de backup** - VMs Azure encriptados foram apoiados usando Azure Backup. Consulte o artigo [Gerencie a cópia de segurança e a restauração dos VMs Azure utilizando o PowerShell](backup-azure-vms-automation.md) para obter detalhes sobre como fazer backup de VMs Azure encriptados.
* **Configure Azure Key Vault** – Certifique-se de que o cofre-chave para o qual as chaves e segredos precisam de ser restaurados já está presente. Consulte o artigo [Começar com a Azure Key Vault](../key-vault/general/overview.md) para obter detalhes sobre a gestão do cofre chave.
* **Restaurar o disco** - Certifique-se de que desencadeou o trabalho de restauro para restaurar discos para VM encriptados utilizando [passos PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm). Isto porque este trabalho gera um ficheiro JSON na sua conta de armazenamento contendo chaves e segredos para que o VM encriptado seja restaurado.

## <a name="get-key-and-secret-from-azure-backup"></a>Obtenha a chave e o segredo do Azure Backup

> [!NOTE]
> Uma vez restaurado o disco para o VM encriptado, certifique-se de que:
>
> * $details é povoada com detalhes de trabalho de disco restaurado, como mencionado nos [passos powerShell na secção Restaurar os Discos](backup-azure-vms-automation.md#restore-an-azure-vm)
> * O VM só deve ser criado a partir de discos restaurados **depois de a chave e o segredo serem restaurados para o cofre da chave.**

Consultar as propriedades restauradas do disco para os detalhes do trabalho.

```powershell
$properties = $details.properties
$storageAccountName = $properties["Target Storage Account Name"]
$containerName = $properties["Config Blob Container Name"]
$encryptedBlobName = $properties["Encryption Info Blob Name"]
```

Desconfiem do contexto de armazenamento Azure e restaure o ficheiro de configuração JSON contendo detalhes chave e secretos para VM encriptado.

```powershell
Set-AzCurrentStorageAccount -Name $storageaccountname -ResourceGroupName '<rg-name>'
$destination_path = 'C:\vmencryption_config.json'
Get-AzStorageBlobContent -Blob $encryptedBlobName -Container $containerName -Destination $destination_path
$encryptionObject = Get-Content -Path $destination_path  | ConvertFrom-Json
```

## <a name="restore-key"></a>Chave de restauro

Uma vez gerado o ficheiro JSON no caminho de destino acima mencionado, gere o ficheiro blob chave do JSON e alimente-o para restaurar o cmdlet da chave para colocar a chave (KEK) de volta no cofre da chave.

```powershell
$keyDestination = 'C:\keyDetails.blob'
[io.file]::WriteAllBytes($keyDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyBackupData))
Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile $keyDestination
```

## <a name="restore-secret"></a>Restaurar o segredo

Use o ficheiro JSON gerado acima para obter nome e valor secretos e alimente-o para definir o cmdlet secreto para colocar o segredo (BEK) de volta no cofre da chave. Utilize estes cmdlets se o seu **VM estiver encriptado utilizando BEK e KEK**.

**Utilize estes cmdlets se o seu VM do Windows estiver encriptado utilizando BEK e KEK.**

```powershell
$secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = $encryptionObject.OsDiskKeyAndSecretDetails.KeyUrl;'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

**Utilize estes cmdlets se o seu VM Linux estiver encriptado utilizando BEK e KEK.**

```powershell
$secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'LinuxPassPhraseFileName';'DiskEncryptionKeyEncryptionKeyURL' = <Key_url_of_newly_restored_key>;'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

Use o ficheiro JSON gerado acima para obter nome e valor secretos e alimente-o para definir o cmdlet secreto para colocar o segredo (BEK) de volta no cofre da chave. Utilize estes cmdlets se o seu **VM estiver encriptado apenas com BEK.**

```powershell
$secretDestination = 'C:\secret.blob'
[io.file]::WriteAllBytes($secretDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyVaultSecretBackupData))
Restore-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -InputFile $secretDestination -Verbose
  ```

> [!NOTE]
>
> * O valor para $secretname pode ser obtido referindo-se à saída de $encryptionObject.OsDiskKeyAndSecretDetails.SecretUrl e usando texto após segredos/ Por exemplo, o URL secreto de saída é `https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163` e o nome secreto é B3284AAA-DAAA-4AAA-B393-60CAA848AAAAA
> * O valor da etiqueta DiskEncryptionKeyFileName é o mesmo que o nome secreto.
>
>

## <a name="create-virtual-machine-from-restored-disk"></a>Criar máquina virtual a partir de disco restaurado

Se tiver feito cópias de segurança encriptadas com recurso a Cópia de Segurança VM do Azure VM, os cmdlets PowerShell mencionados acima ajudam-no a restaurar a chave e o segredo de volta ao cofre da chave. Depois de restaurá-los, consulte o artigo [Gerencie a cópia de segurança e a restauração dos VMs Azure utilizando o PowerShell](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) para criar VMs encriptados a partir de disco, chave e segredo restaurados.

## <a name="legacy-approach"></a>Abordagem do legado

A abordagem acima referida funcionaria para todos os pontos de recuperação. No entanto, a abordagem mais antiga de obter informações chave e secretas do ponto de recuperação, seria válida para pontos de recuperação mais antigos que 11 de julho de 2017 para VMs encriptados usando BEK e KEK. Uma vez concluída a utilização do disco de restauro para VM encriptado utilizando [os passos PowerShell,](backup-azure-vms-automation.md#restore-an-azure-vm)certifique-se de que $rp é povoado com um valor válido.

### <a name="restore-key-legacy-approach"></a>Chave de restauro (abordagem do legado)

Utilize os cmdlets seguintes para obter informações de chave (KEK) do ponto de recuperação e alimente-o para restaurar o cmdlet da chave para colocá-lo de volta no cofre da chave.

```powershell
$rp1 = Get-AzRecoveryServicesBackupRecoveryPoint -RecoveryPointId $rp[0].RecoveryPointId -Item $backupItem -KeyFileDownloadLocation 'C:\Users\downloads'
Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile 'C:\Users\downloads'
```

### <a name="restore-secret-legacy-approach"></a>Restaurar o segredo (abordagem do legado)

Utilize os seguintes cmdlets para obter informações secretas (BEK) do ponto de recuperação e alimente-a para definir o cmdlet secreto para colocá-lo de volta no cofre da chave.

```powershell
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$secretdata = $rp1.KeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = 'https://mykeyvault.vault.azure.net:443/keys/KeyName/84daaac999949999030bf99aaa5a9f9';'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $secret -Tags $Tags -SecretValue $Secret -ContentType  'Wrapped BEK'
```

> [!NOTE]
>
> * O valor para $secretname pode ser obtido referindo-se à produção de $rp 1. KeyAndSecretDetails.SecretUrl e usando texto após segredos/ Por exemplo, o URL secreto de saída é `https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163` e o nome secreto é B3284AAAA-DAAA-4AAA-B393-60CAA848AAAAA
> * O valor da etiqueta DiskEncryptionKeyFileName é o mesmo que o nome secreto.
> * Valor para DiskEncryptionKeyEncryptionKeyURL pode ser obtido a partir do cofre de chaves depois de restaurar as chaves de volta e usar o cmdlet [Get-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/get-azurekeyvaultkey)
>
>

## <a name="next-steps"></a>Passos seguintes

Depois de restaurar a chave e o segredo de volta ao cofre de chaves, consulte o artigo [Gerencie o backup e o restauro dos VMs Azure usando PowerShell](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) para criar VMs encriptados a partir de disco, chave e segredo restaurados.
