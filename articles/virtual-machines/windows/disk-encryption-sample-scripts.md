---
title: Azure Disk Encryption scripts de exemplo
description: Este artigo é o apêndice para Microsoft Azure a criptografia de disco para VMs do Windows.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 50addbec1717c7bb76a248053dd889b09441f6f6
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73749458"
---
# <a name="azure-disk-encryption-sample-scripts"></a>Azure Disk Encryption scripts de exemplo 

Este artigo fornece scripts de exemplo para a preparação de VHDs e outras tarefas criptografados previamente.

 

## <a name="list-vms-and-secrets"></a>Listar VMs e segredos

Listar todas as VMs criptografadas em sua assinatura:

```azurepowershell-interactive
$osVolEncrypted = {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
$dataVolEncrypted= {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
Get-AzVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
```
Lista todos os segredos de criptografia de disco usados para criptografar VMs em um cofre de chaves:

```azurepowershell-interactive
Get-AzKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
```

## <a name="the-azure-disk-encryption-prerequisites-scripts"></a>Os scripts de pré-requisitos do Azure Disk Encryption
Se você já estiver familiarizado com os pré-requisitos para Azure Disk Encryption, poderá usar o script do [PowerShell Azure Disk Encryption pré-requisitos](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Para obter um exemplo de como usar esse script do PowerShell, consulte o guia de [início rápido criptografar uma VM](disk-encryption-powershell-quickstart.md). Você pode remover os comentários de uma seção do script, começando na linha 211, para criptografar todos os discos para VMs existentes em um grupo de recursos existente. 

A tabela a seguir mostra quais parâmetros podem ser usados no script do PowerShell: 

|Parâmetro|Descrição|Obrigatório?|
|------|------|------|
|$resourceGroupName| Nome do grupo de recursos ao qual o keyvault pertence.  Um novo grupo de recursos com esse nome será criado se um não existir.| Verdadeiro|
|$keyVaultName|Nome do keyvault no qual as chaves de criptografia devem ser colocadas. Um novo cofre com esse nome será criado se um não existir.| Verdadeiro|
|$location|Local do keyvault. Verifique se o keyvault e as VMs a serem criptografadas estão no mesmo local. Obtenha uma lista de localizações com `Get-AzLocation`.|Verdadeiro|
|$subscriptionId|Identificador da assinatura do Azure a ser usado.  Pode obter o seu ID de subscrição com `Get-AzSubscription`.|Verdadeiro|
|$aadAppName|Nome do aplicativo do Azure AD que será usado para gravar segredos no keyvault. Será criada uma nova aplicação com este nome, caso ainda não exista. Se esse aplicativo já existir, passe o parâmetro aadClientSecret para o script.|Falso|
|$aadClientSecret|Segredo do cliente do aplicativo do Azure AD que foi criado anteriormente.|Falso|
|$keyEncryptionKeyName|Nome da chave de criptografia de chave opcional no keyvault. Uma nova chave com esse nome será criada se não existir uma.|Falso|

## <a name="resource-manager-templates"></a>Modelos do Resource Manager

### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>Criptografar ou descriptografar VMs sem um aplicativo do Azure AD

- [Habilitar a criptografia de disco em uma VM do Windows existente ou em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)  
- [Desabilitar a criptografia em uma VM do Windows em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad) 

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>Criptografar ou descriptografar VMs com um aplicativo do Azure AD (versão anterior) 
 
- [Habilitar a criptografia de disco em uma VM do Windows existente ou em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)    
- [Desabilitar a criptografia em uma VM do Windows em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm) 
- [Criar um novo disco gerenciado criptografado de um blob de armazenamento/VHD previamente criptografado](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - Cria um novo disco gerenciado criptografado que fornecia um VHD previamente criptografado e suas configurações de criptografia correspondentes

## <a name="prepare-a-pre-encrypted-windows-vhd"></a>Preparar um VHD do Windows previamente criptografado
As seções a seguir são necessárias para preparar um VHD do Windows previamente criptografado para implantação como um VHD criptografado no Azure IaaS. Use as informações para preparar e inicializar uma nova VM do Windows (VHD) no Azure Site Recovery ou no Azure. Para obter mais informações sobre como preparar e carregar um VHD, consulte [carregar um VHD generalizado e usá-lo para criar novas VMs no Azure](upload-generalized-managed.md).

### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>Atualizar a política de grupo para permitir não TPM para proteção do so
Defina a configuração de Política de Grupo do BitLocker **criptografia de unidade de disco BitLocker**, que você encontrará em **política do computador local** > **configuração do computador** > **modelos administrativos** > **Windows Componentes**do. Altere essa configuração para **unidades do sistema operacional** > **exigir autenticação adicional na inicialização** > **permitir o BitLocker sem um TPM compatível**, conforme mostrado na figura a seguir:

![Microsoft Antimalware no Azure](../media/disk-encryption/disk-encryption-fig8.png)

### <a name="install-bitlocker-feature-components"></a>Instalar componentes de recurso do BitLocker
Para o Windows Server 2012 e posterior, use o seguinte comando:

    dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart

Para o Windows Server 2008 R2, use o seguinte comando:

    ServerManagerCmd -install BitLockers

### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>Preparar o volume do sistema operacional para o BitLocker usando `bdehdcfg`
Para compactar a partição do sistema operacional e preparar o computador para o BitLocker, execute o [BdeHdCfg](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-basic-deployment) , se necessário:

    bdehdcfg -target c: shrink -quiet 

### <a name="protect-the-os-volume-by-using-bitlocker"></a>Proteger o volume do sistema operacional usando o BitLocker
Use o comando [`manage-bde`](https://technet.microsoft.com/library/ff829849.aspx) para habilitar a criptografia no volume de inicialização usando um protetor de chave externa. Coloque também a chave externa (arquivo. Bek) na unidade ou volume externo. A criptografia é habilitada no volume de inicialização/sistema após a próxima reinicialização.

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

> [!NOTE]
> Prepare a VM com um VHD de dados/recursos separado para obter a chave externa usando o BitLocker.

## <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Carregar o VHD criptografado em uma conta de armazenamento do Azure
Depois que a criptografia DM-cript estiver habilitada, o VHD criptografado local precisará ser carregado em sua conta de armazenamento.
```powershell
    Add-AzVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```

## <a name="upload-the-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>Carregar o segredo para a VM previamente criptografada para o cofre de chaves
O segredo de criptografia de disco que você obteve anteriormente deve ser carregado como um segredo no cofre de chaves.  Isso requer a concessão da permissão Set Secret e a permissão wrapkey para a conta que carregará os segredos.

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

### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>Segredo de criptografia de disco não criptografado com um KEK
Para configurar o segredo em seu cofre de chaves, use [set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret). A frase secreta é codificada como uma cadeia de caracteres Base64 e, em seguida, carregada no cofre de chaves. Além disso, certifique-se de que as seguintes marcas sejam definidas quando você criar o segredo no cofre de chaves.

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


Use o `$secretUrl` na próxima etapa para [anexar o disco do sistema operacional sem usar o Kek](#without-using-a-kek).

### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>Segredo de criptografia de disco criptografado com um KEK
Antes de carregar o segredo para o cofre de chaves, você pode, opcionalmente, criptografá-lo usando uma chave de criptografia de chave. Use a [API](https://msdn.microsoft.com/library/azure/dn878066.aspx) de encapsulamento para primeiro criptografar o segredo usando a chave de criptografia de chave. A saída dessa operação de encapsulamento é uma cadeia de caracteres codificada de URL base64, que você pode carregar como um segredo usando o cmdlet [`Set-AzKeyVaultSecret`](/powershell/module/az.keyvault/set-azkeyvaultsecret) .

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

Use `$KeyEncryptionKey` e `$secretUrl` na próxima etapa para [anexar o disco do sistema operacional usando Kek](#using-a-kek).

##  <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>Especificar uma URL secreta ao anexar um disco do sistema operacional

###  <a name="without-using-a-kek"></a>Sem usar um KEK
Enquanto estiver anexando o disco do sistema operacional, você precisa passar `$secretUrl`. A URL foi gerada na seção "segredo de criptografia de disco não criptografado com um KEK".
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
Ao anexar o disco do sistema operacional, passe `$KeyEncryptionKey` e `$secretUrl`. A URL foi gerada na seção "segredo de criptografia de disco criptografado com um KEK".
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
