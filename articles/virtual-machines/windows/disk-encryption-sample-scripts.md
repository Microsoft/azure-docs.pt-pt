---
title: Scripts de exemplo do Azure Disk Encryption
description: Este artigo é o apêndice da encriptação do disco Microsoft Azure para VMs do Windows.
author: msmbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: e5e0a970df680df43a7bd303636b3d81bda3e141
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82085710"
---
# <a name="azure-disk-encryption-sample-scripts"></a>Scripts de exemplo do Azure Disk Encryption 

Este artigo fornece scripts de amostra para a preparação de VHDs pré-encriptados e outras tarefas.

 

## <a name="list-vms-and-secrets"></a>Lista vMs e segredos

Listar todos os VMs encriptados na sua subscrição:

```azurepowershell-interactive
$osVolEncrypted = {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
$dataVolEncrypted= {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
Get-AzVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
```
Enumerar todos os segredos de encriptação do disco usados para encriptar VMs num cofre chave:

```azurepowershell-interactive
Get-AzKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
```

## <a name="the-azure-disk-encryption-prerequisites-scripts"></a>A encriptação do disco azure pré-requisitos scripts
Se já está familiarizado com os pré-requisitos para a encriptação do disco Azure, pode utilizar o pré-requisito de encriptação do [disco Azure PowerShell](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Para um exemplo de utilização deste script PowerShell, consulte o [Encrypt a VM Quickstart](disk-encryption-powershell-quickstart.md). Pode remover os comentários de uma secção do script, a partir da linha 211, para encriptar todos os discos para VMs existentes num grupo de recursos existente. 

A tabela que se segue mostra quais os parâmetros que podem ser utilizados no script PowerShell: 

|Parâmetro|Descrição|Obrigatório?|
|------|------|------|
|$resourceGroupName| Nome do grupo de recursos a que pertence o KeyVault.  Um novo grupo de recursos com este nome será criado se não existir.| Verdadeiro|
|$keyVaultName|Nome do KeyVault em que as chaves de encriptação devem ser colocadas. Um novo cofre com este nome será criado se não existir.| Verdadeiro|
|$location|Localização do KeyVault. Certifique-se de que os KeyVault e VMs a encriptar estão no mesmo local. Obtenha uma lista de localizações com `Get-AzLocation`.|Verdadeiro|
|$subscriptionId|Identificador da assinatura Azure a utilizar.  Pode obter o seu ID de subscrição com `Get-AzSubscription`.|Verdadeiro|
|$aadAppName|Nome da aplicação Azure AD que será usada para escrever segredos para keyVault. Será criada uma nova aplicação com este nome, caso ainda não exista. Se esta aplicação já existir, passe o parâmetro AadClientSecret para o script.|Falso|
|$aadClientSecret|Segredo do cliente da aplicação Azure AD que foi criada anteriormente.|Falso|
|$keyEncryptionKeyName|Nome da chave de encriptação opcional no KeyVault. Uma nova chave com este nome será criada se não existir.|Falso|

## <a name="resource-manager-templates"></a>Modelos do Resource Manager

### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>Criptografe ou desencriptar VMs sem uma aplicação Azure AD

- [Ativar a encriptação do disco num VM Windows existente ou em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)  
- [Desative a encriptação num VM windows em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad) 

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>Criptografe ou desencriptar VMs com uma aplicação Azure AD (versão anterior) 
 
- [Ativar a encriptação do disco num VM Windows existente ou em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)    
- [Desative a encriptação num VM windows em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm) 
- [Criar um novo disco gerido encriptado a partir de uma bolha vHD/armazenamento pré-encriptada](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - Cria um novo disco gerido encriptado fornecido a um VHD pré-encriptado e às respetivas definições de encriptação

## <a name="prepare-a-pre-encrypted-windows-vhd"></a>Prepare um VHD Windows pré-encriptado
As secções que se seguem são necessárias para preparar um VHD Windows pré-encriptado para implementação como um VHD encriptado no Azure IaaS. Utilize as informações para preparar e iniciar um Novo VM do Windows (VHD) na Recuperação do Site Azure ou azure. Para obter mais informações sobre como preparar e carregar um VHD, consulte [o Upload de um VHD generalizado e use-o para criar novos VMs em Azure](upload-generalized-managed.md).

### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>Atualizar a política do grupo para permitir o não-TPM para a proteção do OS
Configure a definição da política do grupo BitLocker,**Windows Components**a **encriptação bitLocker Drive,** que encontrará no âmbito da configuração > de**modelos administrativos** > de**configuração** > do **computador local.** Alterar esta definição para **unidades do** > sistema operativo**Requer autenticação adicional no arranque** > **Permitir O BitLocker sem um TPM compatível,** como mostra a seguinte figura:

![Microsoft Antimalware no Azure](../media/disk-encryption/disk-encryption-fig8.png)

### <a name="install-bitlocker-feature-components"></a>Instalar componentes de funcionalidadebitLocker
Para o Windows Server 2012 e posteriormente, utilize o seguinte comando:

    dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart

Para o Windows Server 2008 R2, utilize o seguinte comando:

    ServerManagerCmd -install BitLockers

### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>Prepare o volume de OS para bitLocker utilizando`bdehdcfg`
Para comprimir a partição de S E e preparar a máquina para o BitLocker, execute o [bdehdcfg](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-basic-deployment) se necessário:

    bdehdcfg -target c: shrink -quiet 

### <a name="protect-the-os-volume-by-using-bitlocker"></a>Proteja o volume de OS utilizando o BitLocker
Utilize [`manage-bde`](https://technet.microsoft.com/library/ff829849.aspx) o comando para ativar a encriptação no volume da bota utilizando um protetor de teclas externo. Coloque também a tecla externa (ficheiro.bek) na unidade ou volume externos. A encriptação está ativada no volume de sistema/arranque após a próxima reinicialização.

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

> [!NOTE]
> Prepare o VM com um VHD de dados/recursos separado para obter a chave externa utilizando o BitLocker.

## <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Faça upload de VHD encriptado para uma conta de armazenamento Azure
Depois de ativada a encriptação DM-Crypt, o VHD encriptado local precisa de ser enviado para a sua conta de armazenamento.
```powershell
    Add-AzVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```

## <a name="upload-the-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>Faça upload do segredo para o VM pré-encriptado para o seu cofre chave
O segredo de encriptação do disco que obteve anteriormente deve ser carregado como um segredo no seu cofre chave.  Isto requer a concessão da permissão secreta definida e a permissão de chave de embrulho para a conta que irá carregar os segredos.

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
Para configurar o segredo no seu cofre de chaves, use [Set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret). A frase de passe é codificada como uma corda base64 e depois enviada para o cofre da chave. Além disso, certifique-se de que as seguintes etiquetas são definidas quando criar o segredo no cofre da chave.

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


Utilize `$secretUrl` o passo seguinte para [fixar o disco OS sem utilizar](#without-using-a-kek)o KEK .

### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>Segredo de encriptação de disco encriptado com um KEK
Antes de enviar o segredo para o cofre da chave, pode encriptar opcionalmente utilizando uma chave de encriptação. Utilize a [API](https://msdn.microsoft.com/library/azure/dn878066.aspx) de embrulho para encriptar primeiro o segredo utilizando a chave de encriptação da chave. A saída desta operação de embrulho é uma cadeia codificada por URL base64, que pode então carregar como segredo utilizando o [`Set-AzKeyVaultSecret`](/powershell/module/az.keyvault/set-azkeyvaultsecret) cmdlet.

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

Utilize `$KeyEncryptionKey` `$secretUrl` e no próximo passo para [a fixação do disco OS utilizando KEK](#using-a-kek).

##  <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>Especifique um URL secreto quando anexar um disco OS

###  <a name="without-using-a-kek"></a>Sem usar um KEK
Enquanto está a prender o disco de `$secretUrl`solo, tem de passar. O URL foi gerado na secção "Segredo de encriptação de disco não encriptado com um KEK".
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
Quando anexar o disco `$KeyEncryptionKey` `$secretUrl`OS, passe e . O URL foi gerado na secção "Segredo de encriptação do disco encriptado com um KEK".
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
