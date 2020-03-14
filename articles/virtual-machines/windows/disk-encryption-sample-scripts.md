---
title: Scripts de amostra de encriptação de disco azure
description: Este artigo é o apêndice da encriptação do disco Microsoft Azure para VMs do Windows.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 50addbec1717c7bb76a248053dd889b09441f6f6
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79266731"
---
# <a name="azure-disk-encryption-sample-scripts"></a>Scripts de amostra de encriptação de disco azure 

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
Se já está familiarizado com os pré-requisitos para a encriptação do disco Azure, pode utilizar o pré-requisito de encriptação do [disco Azure PowerShell](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Para um exemplo de utilização deste script PowerShell, consulte o [Encrypt a VM Quickstart](disk-encryption-powershell-quickstart.md). Pode remover os comentários de uma seção do script, começando na linha 211, para encriptar todos os discos de VMs existentes no grupo de recursos existente. 

A tabela seguinte mostra quais parâmetros podem ser utilizados no script do PowerShell: 

|Parâmetro|Descrição|Obrigatório?|
|------|------|------|
|$resourceGroupName| Nome do grupo de recursos a que o Cofre de chaves pertence.  Será criado um novo grupo de recursos com este nome, caso não exista.| Verdadeiro|
|$keyVaultName|Nome do Cofre de chaves na encriptação de que as chaves devem ser colocados. Será criado um novo cofre com este nome, caso não exista.| Verdadeiro|
|$location|Localização do Cofre de chaves. Certificar-se de que o Cofre de chaves e as VMs a encriptar estão na mesma localização. Obtenha uma lista de localizações com `Get-AzLocation`.|Verdadeiro|
|$subscriptionId|Identificador da subscrição do Azure a ser utilizado.  Pode obter o seu ID de subscrição com `Get-AzSubscription`.|Verdadeiro|
|$aadAppName|Nome da aplicação do Azure AD que será utilizada para escrever segredos no Cofre de chaves. Será criada uma nova aplicação com este nome, caso ainda não exista. Se esta aplicação já existir, transmita o parâmetro de aadClientSecret ao script.|Falso|
|$aadClientSecret|Segredo do cliente de aplicação do Azure AD criado anteriormente.|Falso|
|$keyEncryptionKeyName|Nome da chave de encriptação de chave opcional no Cofre de chaves. Será criada uma nova chave com este nome, caso não exista.|Falso|

## <a name="resource-manager-templates"></a>Modelos do Resource Manager

### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>Encriptar ou desencriptar VMs sem uma aplicação do Azure AD

- [Ativar a encriptação do disco num VM Windows existente ou em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)  
- [Desative a encriptação num VM windows em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad) 

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>Encriptar ou desencriptar VMs com uma aplicação do Azure AD (versão anterior) 
 
- [Ativar a encriptação do disco num VM Windows existente ou em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)    
- [Desative a encriptação num VM windows em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm) 
- [Criar um novo disco gerido encriptado a partir de uma bolha vHD/armazenamento pré-encriptada](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - Cria um novo disco gerido encriptado fornecido de um VHD previamente encriptado e as definições de encriptação correspondente

## <a name="prepare-a-pre-encrypted-windows-vhd"></a>Prepare um VHD Windows pré-encriptado
As secções que se seguem são necessárias para preparar um VHD do Windows encriptadas para a implementação como um VHD encriptado no IaaS do Azure. Utilize as informações para preparar e arranque de uma VM de Windows atualizados (VHD) no Azure Site Recovery ou no Azure. Para obter mais informações sobre como preparar e carregar um VHD, consulte [o Upload de um VHD generalizado e use-o para criar novos VMs em Azure](upload-generalized-managed.md).

### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>Atualizar política de grupo para permitir não-TPM para proteção do sistema operacional
Configure a definição da política do grupo **BitLocker,** que encontrará no âmbito da **política de computador local** > **configuração** de computador > **modelos administrativos** > **componentes do Windows**. Altere esta definição para **unidades do sistema operativo** > **Exigir autenticação adicional no arranque** > permitir o **BitLocker sem um TPM compatível,** como mostra a seguinte figura:

![Microsoft Antimalware no Azure](../media/disk-encryption/disk-encryption-fig8.png)

### <a name="install-bitlocker-feature-components"></a>Instalar componentes de funcionalidades do BitLocker
Para o Windows Server 2012 e posterior, utilize o seguinte comando:

    dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart

Para o Windows Server 2008 R2, utilize o seguinte comando:

    ServerManagerCmd -install BitLockers

### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>Prepare o volume de OS para bitLocker utilizando `bdehdcfg`
Para comprimir a partição de S E e preparar a máquina para o BitLocker, execute o [bdehdcfg](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-basic-deployment) se necessário:

    bdehdcfg -target c: shrink -quiet 

### <a name="protect-the-os-volume-by-using-bitlocker"></a>Proteger o volume do sistema operacional usando o BitLocker
Utilize o comando [`manage-bde`](https://technet.microsoft.com/library/ff829849.aspx) para ativar a encriptação no volume da bota utilizando um protetor de teclas externo. Também coloca a chave externa (ficheiro .bek) no volume ou unidade externa. A encriptação está ativada no volume de arranque do sistema após o reinício seguinte.

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

> [!NOTE]
> Prepare a VM com um dados/recurso separado VHD para obter a chave externa usando o BitLocker.

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
Para configurar o segredo no seu cofre de chaves, use [Set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret). A frase de passe é codificada como uma corda base64 e depois enviada para o cofre da chave. Além disso, certifique-se de que as etiquetas seguintes estão definidas quando cria o segredo no Cofre de chaves.

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


Utilize o `$secretUrl` no próximo passo para [fixar o disco OS sem utilizar](#without-using-a-kek)o KEK .

### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>Segredo de encriptação de disco encriptado com um KEK
Antes de carregar o segredo ao Cofre de chaves, opcionalmente, poderá criptografá-los com uma chave de encriptação de chave. Utilize a [API](https://msdn.microsoft.com/library/azure/dn878066.aspx) de embrulho para encriptar primeiro o segredo utilizando a chave de encriptação da chave. A saída desta operação de embrulho é uma cadeia codificada por URL base64, que pode então carregar como segredo utilizando o [`Set-AzKeyVaultSecret`](/powershell/module/az.keyvault/set-azkeyvaultsecret) cmdlet.

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

Utilize `$KeyEncryptionKey` e `$secretUrl` no próximo passo para [a fixação do disco OS utilizando KEK](#using-a-kek).

##  <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>Especifique um URL secreto quando anexar um disco OS

###  <a name="without-using-a-kek"></a>Sem usar um KEK
Enquanto está a anexar o disco de solo, tem de passar `$secretUrl`. O URL foi gerado na secção "segredo de encriptação de disco não criptografado com uma KEK".
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
Quando fixar o disco OS, passe `$KeyEncryptionKey` e `$secretUrl`. O URL foi gerado na secção "Segredo de encriptação de disco encriptado com uma KEK".
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
