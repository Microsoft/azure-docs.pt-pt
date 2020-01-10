---
title: Restaurar chave de Key Vault & segredo para a VM criptografada
description: Saiba como restaurar Key Vault chave e segredo no backup do Azure usando o PowerShell
ms.topic: conceptual
ms.date: 08/28/2017
ms.openlocfilehash: 35bcb919cadd46c603b1f2ad49742c5435f873d2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75450057"
---
# <a name="restore-key-vault-key-and-secret-for-encrypted-vms-using-azure-backup"></a>Restaurar chave e segredo de Key Vault para VMs criptografadas usando o backup do Azure

Este artigo fala sobre como usar o backup de VM do Azure para executar a restauração de VMs do Azure criptografadas, se a chave e o segredo não existirem no cofre de chaves. Essas etapas também podem ser usadas se você quiser manter uma cópia separada da chave (chave de criptografia de chave) e o segredo (chave de criptografia BitLocker) para a VM restaurada.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

* **Backup de VMs criptografadas** -o backup de VMs do Azure criptografadas foi feito usando o backup do Azure. Consulte o artigo [gerenciar o backup e a restauração de VMs do Azure usando o PowerShell](backup-azure-vms-automation.md) para obter detalhes sobre como fazer backup de VMs do Azure criptografadas.
* **Configurar Azure Key Vault** – Verifique se o cofre da chave para o qual as chaves e segredos precisam ser restaurados já está presente. Consulte o artigo [introdução à Azure Key Vault](../key-vault/key-vault-get-started.md) para obter detalhes sobre o gerenciamento do Key Vault.
* **Restaurar disco** – Verifique se você disparou o trabalho de restauração para restaurar discos para a VM criptografada usando [as etapas do PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm). Isso ocorre porque esse trabalho gera um arquivo JSON em sua conta de armazenamento que contém chaves e segredos para a VM criptografada a ser restaurada.

## <a name="get-key-and-secret-from-azure-backup"></a>Obter chave e segredo do backup do Azure

> [!NOTE]
> Depois que o disco tiver sido restaurado para a VM criptografada, verifique se:
>
> * $details é preenchido com os detalhes do trabalho de restauração de disco, conforme mencionado nas [etapas do PowerShell na seção restaurar os discos](backup-azure-vms-automation.md#restore-an-azure-vm)
> * A VM deve ser criada a partir de discos restaurados somente **após a chave e o segredo serem restaurados no cofre de chaves**.

Consulte as propriedades do disco restaurado para obter os detalhes do trabalho.

```powershell
$properties = $details.properties
$storageAccountName = $properties["Target Storage Account Name"]
$containerName = $properties["Config Blob Container Name"]
$encryptedBlobName = $properties["Encryption Info Blob Name"]
```

Defina o contexto de armazenamento do Azure e restaure o arquivo de configuração JSON contendo detalhes de chave e segredo para a VM criptografada.

```powershell
Set-AzCurrentStorageAccount -Name $storageaccountname -ResourceGroupName '<rg-name>'
$destination_path = 'C:\vmencryption_config.json'
Get-AzStorageBlobContent -Blob $encryptedBlobName -Container $containerName -Destination $destination_path
$encryptionObject = Get-Content -Path $destination_path  | ConvertFrom-Json
```

## <a name="restore-key"></a>Restaurar chave

Depois que o arquivo JSON for gerado no caminho de destino mencionado acima, gere o arquivo de blob de chave do JSON e o ligue para o cmdlet Restore Key para colocar a chave (KEK) de volta no cofre de chaves.

```powershell
$keyDestination = 'C:\keyDetails.blob'
[io.file]::WriteAllBytes($keyDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyBackupData))
Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile $keyDestination
```

## <a name="restore-secret"></a>Restaurar segredo

Use o arquivo JSON gerado acima para obter o nome e o valor do segredo e alimentar-o para definir o cmdlet secreto para colocar o segredo (BEK) de volta no cofre de chaves. Use esses cmdlets se sua **VM for criptografada usando Bek e Kek**.

**Use esses cmdlets se sua VM do Windows for criptografada usando BEK e KEK.**

```powershell
$secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = $encryptionObject.OsDiskKeyAndSecretDetails.KeyUrl;'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

**Use esses cmdlets se sua VM do Linux for criptografada usando BEK e KEK.**

```powershell
$secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'LinuxPassPhraseFileName';'DiskEncryptionKeyEncryptionKeyURL' = <Key_url_of_newly_restored_key>;'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

Use o arquivo JSON gerado acima para obter o nome e o valor do segredo e alimentar-o para definir o cmdlet secreto para colocar o segredo (BEK) de volta no cofre de chaves. Use esses cmdlets se sua **VM for criptografada usando apenas Bek** .

```powershell
$secretDestination = 'C:\secret.blob'
[io.file]::WriteAllBytes($secretDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyVaultSecretBackupData))
Restore-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -InputFile $secretDestination -Verbose
  ```

> [!NOTE]
>
> * O valor de $secretname pode ser obtido referindo-se à saída de $encryptionObject. OsDiskKeyAndSecretDetails. SecretUrl e usando o texto após os segredos/por exemplo, a URL secreta de saída é https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163 e o nome do segredo é B3284AAA-DAAA-4AAA-B393-60CAA848AAAA
> * O valor da marca DiskEncryptionKeyFileName é o mesmo que o nome do segredo.
>
>

## <a name="create-virtual-machine-from-restored-disk"></a>Criar máquina virtual do disco restaurado

Se você tiver feito backup da VM criptografada usando o backup de VM do Azure, os cmdlets do PowerShell mencionados acima ajudarão a restaurar a chave e o segredo de volta para o cofre de chaves. Depois de restaurá-los, consulte o artigo [gerenciar o backup e a restauração de VMs do Azure usando o PowerShell](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) para criar VMs criptografadas de disco, chave e segredo restaurados.

## <a name="legacy-approach"></a>Abordagem herdada

A abordagem mencionada acima funcionaria para todos os pontos de recuperação. No entanto, a abordagem mais antiga de obter informações de chave e segredo do ponto de recuperação seria válida para pontos de recuperação anteriores a 11 de julho de 2017 para VMs criptografadas usando BEK e KEK. Depois que o trabalho de restauração de disco for concluído para a VM criptografada usando [as etapas do PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm), verifique se $RP é populado com um valor válido.

### <a name="restore-key"></a>Restaurar chave

Use os cmdlets a seguir para obter informações de chave (KEK) do ponto de recuperação e alimentar-o para restaurar o cmdlet de chave para colocá-lo de volta no cofre de chaves.

```powershell
$rp1 = Get-AzRecoveryServicesBackupRecoveryPoint -RecoveryPointId $rp[0].RecoveryPointId -Item $backupItem -KeyFileDownloadLocation 'C:\Users\downloads'
Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile 'C:\Users\downloads'
```

### <a name="restore-secret"></a>Restaurar segredo

Use os cmdlets a seguir para obter informações secretas (BEK) do ponto de recuperação e alimentar-o para definir o cmdlet secreto para colocá-lo de volta no cofre de chaves.

```powershell
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$secretdata = $rp1.KeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = 'https://mykeyvault.vault.azure.net:443/keys/KeyName/84daaac999949999030bf99aaa5a9f9';'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $secret -Tags $Tags -SecretValue $Secret -ContentType  'Wrapped BEK'
```

> [!NOTE]
>
> * O valor de $secretname pode ser obtido referindo-se à saída de $rp 1. KeyAndSecretDetails. SecretUrl e usando o texto após os segredos/por exemplo, a URL secreta de saída é https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163 e o nome do segredo é B3284AAA-DAAA-4AAA-B393-60CAA848AAAA
> * O valor da marca DiskEncryptionKeyFileName é o mesmo que o nome do segredo.
> * O valor de DiskEncryptionKeyEncryptionKeyURL pode ser obtido do Key Vault após restaurar as chaves de volta e usar o cmdlet [Get-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/get-azurekeyvaultkey)
>
>

## <a name="next-steps"></a>Passos seguintes

Depois de restaurar a chave e o segredo de volta ao cofre de chaves, consulte o artigo [gerenciar o backup e a restauração de VMs do Azure usando o PowerShell](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) para criar VMs criptografadas de disco, chave e segredo restaurados.
