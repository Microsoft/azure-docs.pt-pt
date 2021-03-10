---
title: Scripts de amostra de encriptação de disco Azure para VMs do Windows
description: Este artigo é o apêndice para a Encriptação do Disco Azure do Microsoft Azure para VMs windows.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: how-to
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 413553165fcf74fa4590cb4661212b885a277579
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102550642"
---
# <a name="azure-disk-encryption-sample-scripts"></a>Scripts de exemplo do Azure Disk Encryption 

Este artigo fornece scripts de amostra para preparar VHDs pré-encriptados e outras tarefas.

> [!NOTE]
> Todos os scripts referem-se à versão mais recente, não-AAD da ADE, exceto quando anotado.

## <a name="sample-powershell-scripts-for-azure-disk-encryption"></a>Scripts PowerShell de amostra para encriptação do disco Azure 


- **Listar todos os VMs encriptados na sua subscrição**

  Pode encontrar todos os VMs encriptados a ADE e a versão de extensão, em todos os grupos de recursos presentes numa subscrição, utilizando [este script PowerShell](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/Find_1passAdeVersion_VM.ps1).

  Em alternativa, estes cmdlets mostrarão todos os VMs encriptados a ADE (mas não a versão de extensão):

    ```azurepowershell-interactive
    $osVolEncrypted = {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
    $dataVolEncrypted= {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
    Get-AzVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
    ```

- **Listar todas as instâncias VMSS encriptadas na sua subscrição**
    
    Pode encontrar todas as instâncias VMSS encriptadas pela ADE e a versão de extensão, em todos os grupos de recursos presentes numa subscrição, utilizando [este script PowerShell](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/Find_1passAdeVersion_VMSS.ps1).
 
- **Lista todos os segredos de encriptação do disco usados para encriptar VMs em um cofre chave**

```azurepowershell-interactive
Get-AzKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
```

### <a name="using-the-azure-disk-encryption-prerequisites-powershell-script"></a>Utilizando o encriptação do disco Azure pré-requisitos powerShell script

Se já estiver familiarizado com os pré-requisitos para encriptação do disco Azure, pode utilizar o [script Azure Disk Encryption pré-requisitos powerShell](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Para um exemplo de utilização deste script PowerShell, consulte o [Crycrim a VM Quickstart](disk-encryption-powershell-quickstart.md). Pode remover os comentários de uma secção do script, a partir da linha 211, para encriptar todos os discos para VM existentes num grupo de recursos existente. 

A tabela a seguir mostra quais os parâmetros que podem ser utilizados no script PowerShell: 

|Parâmetro|Descrição|Obrigatório?|
|------|------|------|
|$resourceGroupName| Nome do grupo de recursos a que pertence o KeyVault.  Um novo grupo de recursos com este nome será criado se não existir.| Verdadeiro|
|$keyVaultName|Nome do KeyVault em que as chaves de encriptação devem ser colocadas. Um novo cofre com este nome será criado se um não existir.| Verdadeiro|
|$location|Localização do KeyVault. Certifique-se de que os KeyVault e VMs a encriptar estão no mesmo local. Obtenha uma lista de localizações com `Get-AzLocation`.|Verdadeiro|
|$subscriptionId|Identificador da assinatura Azure a ser usado.  Pode obter o seu ID de subscrição com `Get-AzSubscription`.|Verdadeiro|
|$aadAppName|Nome da aplicação AD AZure que será usada para escrever segredos ao KeyVault. Será criada uma nova aplicação com este nome, caso ainda não exista. Se esta aplicação já existe, passe o parâmetro aadClientSecret para o script.|Falso|
|$aadClientSecret|Segredo de cliente da aplicação AZure AD que foi criada anteriormente.|Falso|
|$keyEncryptionKeyName|Nome da chave de encriptação opcional em KeyVault. Uma nova chave com este nome será criada se não existir.|Falso|

## <a name="resource-manager-templates"></a>Modelos do Resource Manager

### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>Criptografe ou desencriptar VMs sem uma aplicação AD AZure

- [Ativar a encriptação do disco num VM do Windows existente ou em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)  
- [Desative a encriptação num VM do Windows em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad) 

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>Criptografe ou desencriptar VMs com uma aplicação AD Azure (versão anterior) 
 
- [Ativar a encriptação do disco num VM do Windows existente ou em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)    
- [Desative a encriptação num VM do Windows em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm) 
- [Criar um novo disco gerido encriptado a partir de uma bolha VHD/armazenamento pré-encriptado](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - Cria um novo disco gerido encriptado desde um VHD pré-encriptado e as suas definições de encriptação correspondentes

## <a name="prepare-a-pre-encrypted-windows-vhd"></a>Preparar um Windows VHD pré-encriptado
As secções que se seguem são necessárias para preparar um VHD do Windows pré-encriptado para implementação como um VHD encriptado em Azure IaaS. Utilize as informações para preparar e iniciar um VM (VHD) fresco do Windows na Recuperação ou Azure do Site Azure. Para obter mais informações sobre como preparar e carregar um VHD, consulte [o Upload a Generalized VHD e use-o para criar novos VMs em Azure](upload-generalized-managed.md).

### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>Atualizar a política do grupo para permitir a proteção não-TPM para proteção de OS
Configure a definição da política do grupo BitLocker **BitLocker,** que encontrará sob a configuração de modelos de   >  **configuração** de computador  >  **local,**  >  **componentes do Windows .** Alterar esta definição para **Unidades do Sistema Operativo**  >  **Requer a autenticação adicional no arranque** Permitir que o  >  **BitLocker sem um TPM compatível,** como mostra a seguinte figura:

![Microsoft Antimalware no Azure](../media/disk-encryption/disk-encryption-fig8.png)

### <a name="install-bitlocker-feature-components"></a>Instalar componentes de funcionalidade BitLocker
Para o Windows Server 2012 e mais tarde, utilize o seguinte comando:

```console
dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart
```

Para o Windows Server 2008 R2, utilize o seguinte comando:

```console
ServerManagerCmd -install BitLockers
```

### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>Prepare o volume de SO para BitLocker utilizando `bdehdcfg`
Para comprimir a partição de SO e preparar a máquina para BitLocker, execute o [bdehdcfg](/windows/security/information-protection/bitlocker/bitlocker-basic-deployment) se necessário:

```console
bdehdcfg -target c: shrink -quiet 
```

### <a name="protect-the-os-volume-by-using-bitlocker"></a>Proteja o volume de SO utilizando o BitLocker
Utilize o [`manage-bde`](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/ff829849(v=ws.11)) comando para ativar a encriptação no volume de arranque utilizando um protetor de teclas externo. Coloque também a chave externa (.ficheiro bek) na unidade ou volume externos. A encriptação é ativada no volume de sistema/arranque após o próximo reboot.

```console
manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
reboot
```

> [!NOTE]
> Prepare o VM com um VHD de dados/recursos separados para obter a chave externa utilizando o BitLocker.

## <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Faça upload de VHD encriptado para uma conta de armazenamento Azure
Após DM-Crypt encriptação está ativada, o VHD encriptado local precisa de ser carregado para a sua conta de armazenamento.
```powershell
    Add-AzVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```

## <a name="upload-the-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>Faça o upload do segredo para o VM pré-encriptado para o seu cofre de chaves
O segredo de encriptação do disco que obteve anteriormente deve ser enviado como um segredo no cofre da sua chave.  Isto requer a concessão da permissão secreta definida e a permissão de invólucro para a conta que irá enviar os segredos.

```powershell 
# Typically, account Id is the user principal name (in user@domain.com format)
$upn = (Get-AzureRmContext).Account.Id
Set-AzKeyVaultAccessPolicy -VaultName $kvname -UserPrincipalName $acctid -PermissionsToKeys wrapKey -PermissionsToSecrets set

# In cloud shell, the account ID is a managed service identity, so specify the username directly 
# $upn = "user@domain.com" 
# Set-AzKeyVaultAccessPolicy -VaultName $kvname -UserPrincipalName $acctid -PermissionsToKeys wrapKey -PermissionsToSecrets set

# When running as a service principal, retrieve the service principal ID from the account ID, and set access policy to that 
# $acctid = (Get-AzureRmContext).Account.Id
# $spoid = (Get-AzureRmADServicePrincipal -ServicePrincipalName $acctid).Id
# Set-AzKeyVaultAccessPolicy -VaultName $kvname -ObjectId $spoid -BypassObjectIdValidation -PermissionsToKeys wrapKey -PermissionsToSecrets set

```

### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>Segredo de encriptação de disco não encriptado com um KEK
Para configurar o segredo no seu cofre de chaves, utilize [o Set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret). A palavra-passe é codificada como uma corda base64 e depois enviada para o cofre da chave. Além disso, certifique-se de que as seguintes etiquetas são definidas quando criar o segredo no cofre da chave.

```powershell

 # This is the passphrase that was provided for encryption during the distribution installation
 $passphrase = "contoso-password"

 $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
 $secretName = [guid]::NewGuid().ToString()
 $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
 $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

 $secret = Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
 $secretUrl = $secret.Id
```


Utilize o `$secretUrl` passo seguinte para fixar o disco DE SEM utilizar [KEK](#without-using-a-kek).

### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>Segredo de encriptação de disco encriptado com um KEK
Antes de enviar o segredo para o cofre da chave, pode criptografá-lo opcionalmente usando uma chave de encriptação. Utilize a [API](/rest/api/keyvault/wrapkey) de embrulho para encriptar primeiro o segredo utilizando a chave de encriptação. A saída desta operação de embrulho é uma cadeia codificada de URL base64, que pode então carregar como um segredo usando o [`Set-AzKeyVaultSecret`](/powershell/module/az.keyvault/set-azkeyvaultsecret) cmdlet.

```powershell
    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id
```

Utilize `$KeyEncryptionKey` e no passo seguinte para a `$secretUrl` [fixação do disco de sos utilizando KEK](#using-a-kek).

##  <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>Especifique um URL secreto quando anexar um disco DE

###  <a name="without-using-a-kek"></a>Sem usar um KEK
Enquanto está a prender o disco de so, tem de `$secretUrl` passar. O URL foi gerado no "Segredo de encriptação de disco não encriptado com uma secção KEK".
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Windows `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl
```
### <a name="using-a-kek"></a>Usando um KEK
Quando ligar o disco DE, passe `$KeyEncryptionKey` e `$secretUrl` . O URL foi gerado no "Segredo de encriptação do disco encriptado com uma secção KEK".
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Windows `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id
```
