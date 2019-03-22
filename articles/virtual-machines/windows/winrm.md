---
title: Configurar o acesso ao WinRM para uma VM do Azure | Documentos da Microsoft
description: Configure o acesso de WinRM para utilização com uma máquina virtual do Azure criada no modelo de implementação do Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 9718e85b-d360-4621-90b8-0b0b84a21208
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/16/2016
ms.author: kasing
ms.openlocfilehash: 89739aa51748e7bc69fc42b8b745994bbe50e39d
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58309806"
---
# <a name="setting-up-winrm-access-for-virtual-machines-in-azure-resource-manager"></a>Como configurar o acesso de WinRM para máquinas virtuais no Azure Resource Manager

Eis os passos que precisa de efetuar para configurar uma VM com a conectividade de WinRM

1. Criar um Key Vault
2. Criar um certificado autoassinado
3. Carregue o certificado autoassinado para o Key Vault
4. Obter o URL para o seu certificado autoassinado no Cofre de chaves
5. Referenciar o URL de certificados autoassinados durante a criação de uma VM

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="step-1-create-a-key-vault"></a>Passo 1: Criar um Key Vault
Pode utilizar o comando para criar o Key Vault abaixo

```
New-AzKeyVault -VaultName "<vault-name>" -ResourceGroupName "<rg-name>" -Location "<vault-location>" -EnabledForDeployment -EnabledForTemplateDeployment
```

## <a name="step-2-create-a-self-signed-certificate"></a>Passo 2: Criar um certificado autoassinado
Pode criar um certificado autoassinado com este script do PowerShell

```
$certificateName = "somename"

$thumbprint = (New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation Cert:\CurrentUser\My -KeySpec KeyExchange).Thumbprint

$cert = (Get-ChildItem -Path cert:\CurrentUser\My\$thumbprint)

$password = Read-Host -Prompt "Please enter the certificate password." -AsSecureString

Export-PfxCertificate -Cert $cert -FilePath ".\$certificateName.pfx" -Password $password
```

## <a name="step-3-upload-your-self-signed-certificate-to-the-key-vault"></a>Passo 3: Carregue o certificado autoassinado para o Cofre de chaves
Antes de carregar o certificado para o Cofre de chaves que criou no passo 1, ele precisa convertida para um formato entenderá o fornecedor de recursos Microsoft. Compute. O PowerShell abaixo script permitirá que faz isso

```
$fileName = "<Path to the .pfx file>"
$fileContentBytes = Get-Content $fileName -Encoding Byte
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

$jsonObject = @"
{
  "data": "$filecontentencoded",
  "dataType" :"pfx",
  "password": "<password>"
}
"@

$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

$secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText –Force
Set-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>" -SecretValue $secret
```

## <a name="step-4-get-the-url-for-your-self-signed-certificate-in-the-key-vault"></a>Passo 4: Obter o URL para o seu certificado autoassinado no Cofre de chaves
O fornecedor de recursos Microsoft. Compute tem um URL para o segredo no interior do Cofre de chaves durante o aprovisionamento da VM. Isto permite que o fornecedor de recursos Microsoft. Compute transferir o segredo e criar o certificado equivalente na VM.

> [!NOTE]
> O URL do segredo do deve incluir também a versão. Um exemplo de URL é semelhante a abaixo https:\//contosovault.vault.azure.net:443/secrets/contososecret/01h9db0df2cd4300a20ence585a6s7ve

#### <a name="templates"></a>Modelos
Pode obter a ligação para o URL no modelo, utilizando o abaixo código

    "certificateUrl": "[reference(resourceId(resourceGroup().name, 'Microsoft.KeyVault/vaults/secrets', '<vault-name>', '<secret-name>'), '2015-06-01').secretUriWithVersion]"

#### <a name="powershell"></a>PowerShell
Pode obter este URL a utilizar o comando do PowerShell abaixo

    $secretURL = (Get-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id

## <a name="step-5-reference-your-self-signed-certificates-url-while-creating-a-vm"></a>Passo 5: Referenciar o URL de certificados autoassinados durante a criação de uma VM
#### <a name="azure-resource-manager-templates"></a>Modelos Azure Resource Manager
Ao criar uma VM através de modelos, o certificado é referenciado a secção de segredos e na secção de winRM conforme mostrado a seguir:

    "osProfile": {
          ...
          "secrets": [
            {
              "sourceVault": {
                "id": "<resource id of the Key Vault containing the secret>"
              },
              "vaultCertificates": [
                {
                  "certificateUrl": "<URL for the certificate you got in Step 4>",
                  "certificateStore": "<Name of the certificate store on the VM>"
                }
              ]
            }
          ],
          "windowsConfiguration": {
            ...
            "winRM": {
              "listeners": [
                {
                  "protocol": "http"
                },
                {
                  "protocol": "https",
                  "certificateUrl": "<URL for the certificate you got in Step 4>"
                }
              ]
            },
            ...
          }
        },

Um modelo de exemplo acima pode ser encontrado aqui em [201-vm-winrm-Cofre de chaves-windows](https://azure.microsoft.com/documentation/templates/201-vm-winrm-keyvault-windows)

Código-fonte para este modelo pode ser encontrado no [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows)

#### <a name="powershell"></a>PowerShell
    $vm = New-AzVMConfig -VMName "<VM name>" -VMSize "<VM Size>"
    $credential = Get-Credential
    $secretURL = (Get-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
    $vm = Set-AzVMOperatingSystem -VM $vm -Windows -ComputerName "<Computer Name>" -Credential $credential -WinRMHttp -WinRMHttps -WinRMCertificateUrl $secretURL
    $sourceVaultId = (Get-AzKeyVault -ResourceGroupName "<Resource Group name>" -VaultName "<Vault Name>").ResourceId
    $CertificateStore = "My"
    $vm = Add-AzVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore $CertificateStore -CertificateUrl $secretURL

## <a name="step-6-connecting-to-the-vm"></a>Passo 6: Ligar à VM
Antes de poder ligar à VM, terá de certificar-se a máquina está configurada para a gestão remota do WinRM. Inicie o PowerShell como administrador e execute o comando certificar-se abaixo configurá-lo.

    Enable-PSRemoting -Force

> [!NOTE]
> Poderá ter de certificar-se de que o serviço WinRM está em execução se o procedimento acima não funciona. Pode fazê-lo `Get-Service WinRM`
> 
> 

Depois de fazer a configuração, pode ligar para a VM com o comando abaixo

    Enter-PSSession -ConnectionUri https://<public-ip-dns-of-the-vm>:5986 -Credential $cred -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck) -Authentication Negotiate
