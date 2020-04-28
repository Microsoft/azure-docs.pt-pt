---
title: Restaurar chave chave cofre & segredo para VM encriptado
description: Saiba como restaurar a chave do cofre e o segredo em Backup Azure usando powerShell
ms.topic: conceptual
ms.date: 08/28/2017
ms.openlocfilehash: 826b3917fda820ed02cde425ca5a394bffec0df1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82186826"
---
# <a name="restore-key-vault-key-and-secret-for-encrypted-vms-using-azure-backup"></a>Restaurar a chave do Key Vault e o segredo para VMs encriptadas utilizando o Azure Backup

Este artigo fala sobre a utilização de Backup Azure VM para realizar o restauro de VMs Azure encriptados, se a sua chave e segredo não existirem no cofre chave. Estes passos também podem ser usados se pretender manter uma cópia separada da chave (Chave de Encriptação) e secreto (Chave de Encriptação BitLocker) para o VM restaurado.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

* **VMs encriptados** de backup - VMs Azure encriptados foram apoiados usando Backup Azure. Consulte o artigo Gerir a cópia de [segurança e restaurar os VMs Azure utilizando](backup-azure-vms-automation.md) o PowerShell para obter detalhes sobre como fazer backup de VMs Azure encriptados.
* **Configure Cofre chave Azure** – Certifique-se de que o cofre chave para o qual as chaves e segredos precisam de ser restaurados já está presente. Consulte o artigo [Get Started with Azure Key Vault](../key-vault/key-vault-get-started.md) para mais detalhes sobre a gestão do cofre chave.
* **Restaurar o disco** - Certifique-se de que desencadeou uma obra de restauro para restaurar os discos para vM encriptado utilizando [passos PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm). Isto porque este trabalho gera um ficheiro JSON na sua conta de armazenamento contendo chaves e segredos para o VM encriptado ser restaurado.

## <a name="get-key-and-secret-from-azure-backup"></a>Obtenha a chave e segredo do Azure Backup

> [!NOTE]
> Uma vez restaurado o disco para o VM encriptado, certifique-se de que:
>
> * $details é povoada com detalhes de trabalho de restauro do disco, como mencionado nos [passos powerShell em Restaurar a secção Discos](backup-azure-vms-automation.md#restore-an-azure-vm)
> * O VM só deve ser criado a partir de discos restaurados após a chave e o **segredo ser restaurado para o cofre da chave**.

Consultar as propriedades restauradas do disco para os detalhes do trabalho.

```powershell
$properties = $details.properties
$storageAccountName = $properties["Target Storage Account Name"]
$containerName = $properties["Config Blob Container Name"]
$encryptedBlobName = $properties["Encryption Info Blob Name"]
```

Delineie o contexto de armazenamento Azure e restaure o ficheiro de configuração JSON contendo detalhes chave e secretos para VM encriptado.

```powershell
Set-AzCurrentStorageAccount -Name $storageaccountname -ResourceGroupName '<rg-name>'
$destination_path = 'C:\vmencryption_config.json'
Get-AzStorageBlobContent -Blob $encryptedBlobName -Container $containerName -Destination $destination_path
$encryptionObject = Get-Content -Path $destination_path  | ConvertFrom-Json
```

## <a name="restore-key"></a>Restaurar a chave

Uma vez que o ficheiro JSON é gerado no caminho de destino acima mencionado, gere o ficheiro blob chave do JSON e alimente-o para restaurar o cmdlet chave para colocar a chave (KEK) de volta no cofre chave.

```powershell
$keyDestination = 'C:\keyDetails.blob'
[io.file]::WriteAllBytes($keyDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyBackupData))
Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile $keyDestination
```

## <a name="restore-secret"></a>Restaurar segredo

Use o ficheiro JSON gerado acima para obter nome e valor secretos e alimentá-lo para definir cmdlet secreto para colocar o segredo (BEK) de volta no cofre da chave.Utilize estes cmdlets se o seu **VM estiver encriptado utilizando BEK e KEK**.

**Utilize estes cmdlets se o seu Windows VM estiver encriptado utilizando BEK e KEK.**

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

Use o ficheiro JSON gerado acima para obter nome e valor secretos e alimentá-lo para definir cmdlet secreto para colocar o segredo (BEK) de volta no cofre da chave.Utilize estes cmdlets se o seu **VM estiver encriptado usando** apenas BEK.

```powershell
$secretDestination = 'C:\secret.blob'
[io.file]::WriteAllBytes($secretDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyVaultSecretBackupData))
Restore-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -InputFile $secretDestination -Verbose
  ```

> [!NOTE]
>
> * O valor para $secretname pode ser obtido referindo-se à saída de $encryptionObject.OsDiskKeyAndSecretDetails.SecretUrl e usando `https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163` texto após segredos/ por exemplo, URL secreto de saída é e nome secreto é B3284AAA-DAAAA-B393-60CAA848AAAAAAAAAAAAA
> * O valor da etiqueta DiskEncryptionKeyFileName é o mesmo que o nome secreto.
>
>

## <a name="create-virtual-machine-from-restored-disk"></a>Criar máquina virtual a partir de disco restaurado

Se tiver apoiado o VM encriptado utilizando o Backup Azure VM, os cmdlets PowerShell mencionados acima ajudam-no a restaurar a chave e o segredo de volta ao cofre da chave. Depois de os restaurar, consulte o artigo Gerir a cópia de [segurança e restaurar os VMs Azure usando o PowerShell](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) para criar VMs encriptados a partir de discos, chaves e segredo restaurados.

## <a name="legacy-approach"></a>Abordagem do legado

A abordagem acima referida funcionaria para todos os pontos de recuperação. No entanto, a abordagem mais antiga de obter informações fundamentais e secretas do ponto de recuperação, seria válida para pontos de recuperação mais antigos do que 11 de julho de 2017 para VMs encriptados usando BEK e KEK. Uma vez que o trabalho de restauro do disco esteja completo para VM encriptado utilizando [passos PowerShell,](backup-azure-vms-automation.md#restore-an-azure-vm)certifique-se de que $rp é povoada com um valor válido.

### <a name="restore-key"></a>Restaurar a chave

Utilize os seguintes cmdlets para obter informações chave (KEK) do ponto de recuperação e alimentá-la para restaurar o cmdlet chave para colocá-lo de volta no cofre da chave.

```powershell
$rp1 = Get-AzRecoveryServicesBackupRecoveryPoint -RecoveryPointId $rp[0].RecoveryPointId -Item $backupItem -KeyFileDownloadLocation 'C:\Users\downloads'
Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile 'C:\Users\downloads'
```

### <a name="restore-secret"></a>Restaurar segredo

Utilize os seguintes cmdlets para obter informações secretas (BEK) do ponto de recuperação e alimentá-las para definir cmdlet secreto para colocá-lo de volta no cofre chave.

```powershell
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$secretdata = $rp1.KeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = 'https://mykeyvault.vault.azure.net:443/keys/KeyName/84daaac999949999030bf99aaa5a9f9';'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $secret -Tags $Tags -SecretValue $Secret -ContentType  'Wrapped BEK'
```

> [!NOTE]
>
> * O valor para $secretname pode ser obtido referindo-se à saída de $rp1. KeyAndSecretDetails.SecretUrl e usando texto após segredos/ por `https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163` exemplo, URL secreto de saída é e nome secreto é B3284AAAA-DAAA-4AAA-B393-60CAA848AAAAAAAA
> * O valor da etiqueta DiskEncryptionKeyFileName é o mesmo que o nome secreto.
> * Valor para DiskEncryptionKeyEncryptionKeyURL pode ser obtido a partir do cofre da chave depois de restaurar as chaves de volta e usando o cmdlet [Get-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/get-azurekeyvaultkey)
>
>

## <a name="next-steps"></a>Passos seguintes

Depois de restaurar a chave e o segredo de volta ao cofre chave, consulte o artigo Gerir a cópia de [segurança e restaurar os VMs Azure usando o PowerShell](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) para criar VMs encriptados a partir de discos restaurados, chave e segredo.
