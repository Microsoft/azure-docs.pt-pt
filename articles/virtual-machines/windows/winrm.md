---
title: Configurar o acesso winRM para um VM Azure
description: Configurar o acesso WinRM para utilização com uma máquina virtual Azure criada no modelo de implementação do Gestor de Recursos.
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 9718e85b-d360-4621-90b8-0b0b84a21208
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 06/16/2016
ms.author: kasing
ms.openlocfilehash: 3dd56682022bfb5dc620beb06d401910726894b9
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631532"
---
# <a name="setting-up-winrm-access-for-virtual-machines-in-azure-resource-manager"></a>Configuração do acesso winRM para máquinas virtuais em Gestor de Recursos Azure

Aqui estão os passos que você precisa tomar para configurar um VM com conectividade WinRM

1. Criar um Key Vault
2. Criar um certificado autoassinado
3. Faça upload do seu certificado auto-assinado para key vault
4. Obtenha o URL para o seu certificado auto-assinado no Cofre chave
5. Referenciar o seu URL de certificados auto-assinados enquanto cria um VM

 

## <a name="step-1-create-a-key-vault"></a>Passo 1: Criar um cofre chave
Você pode usar o comando abaixo para criar o Cofre chave

```
New-AzKeyVault -VaultName "<vault-name>" -ResourceGroupName "<rg-name>" -Location "<vault-location>" -EnabledForDeployment -EnabledForTemplateDeployment
```

## <a name="step-2-create-a-self-signed-certificate"></a>Passo 2: Criar um certificado auto-assinado
Pode criar um certificado auto-assinado usando este script PowerShell

```
$certificateName = "somename"

$thumbprint = (New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation Cert:\CurrentUser\My -KeySpec KeyExchange).Thumbprint

$cert = (Get-ChildItem -Path cert:\CurrentUser\My\$thumbprint)

$password = Read-Host -Prompt "Please enter the certificate password." -AsSecureString

Export-PfxCertificate -Cert $cert -FilePath ".\$certificateName.pfx" -Password $password
```

## <a name="step-3-upload-your-self-signed-certificate-to-the-key-vault"></a>Passo 3: Faça upload do seu certificado auto-assinado para o Cofre chave
Antes de enviar o certificado para o Key Vault criado no passo 1, precisa de ser convertido num formato que o fornecedor de recursos Microsoft.Compute compreenderá. O script abaixo do PowerShell permitir-lhe-á fazê-lo

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
Set-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>" -SecretValue $secret
```

## <a name="step-4-get-the-url-for-your-self-signed-certificate-in-the-key-vault"></a>Passo 4: Obtenha o URL para o seu certificado auto-assinado no Cofre chave
O fornecedor de recursos Microsoft.Compute precisa de um URL para o segredo dentro do Cofre chave enquanto aprovisiona o VM. Isto permite ao fornecedor de recursos Microsoft.Compute descarregar o segredo e criar o certificado equivalente no VM.

> [!NOTE]
> O URL do segredo também precisa de incluir a versão. Um URL de exemplo\/parece abaixo https: /contosovault.vault.azure.net:443/secrets/contososecret/01h9db0df2cd4300a20ence585a6s7ve

#### <a name="templates"></a>Modelos
Você pode obter o link para o URL no modelo usando o código abaixo

    "certificateUrl": "[reference(resourceId(resourceGroup().name, 'Microsoft.KeyVault/vaults/secrets', '<vault-name>', '<secret-name>'), '2015-06-01').secretUriWithVersion]"

#### <a name="powershell"></a>PowerShell
Pode obter este URL utilizando o comando abaixo da PowerShell

    $secretURL = (Get-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id

## <a name="step-5-reference-your-self-signed-certificates-url-while-creating-a-vm"></a>Passo 5: Referenciar o url de certificados auto-assinados enquanto cria um VM
#### <a name="azure-resource-manager-templates"></a>Modelos de gestor de recursos azure
Ao criar um VM através de modelos, o certificado é referenciado na secção de segredos e na secção winRM como abaixo:

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

Um modelo de amostra para o acima pode ser encontrado aqui em [201-vm-winrm-keyvault-windows](https://azure.microsoft.com/documentation/templates/201-vm-winrm-keyvault-windows)

Código fonte para este modelo pode ser encontrado no [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows)

#### <a name="powershell"></a>PowerShell
    $vm = New-AzVMConfig -VMName "<VM name>" -VMSize "<VM Size>"
    $credential = Get-Credential
    $secretURL = (Get-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
    $vm = Set-AzVMOperatingSystem -VM $vm -Windows -ComputerName "<Computer Name>" -Credential $credential -WinRMHttp -WinRMHttps -ProvisionVMAgent -WinRMCertificateUrl $secretURL
    $sourceVaultId = (Get-AzKeyVault -ResourceGroupName "<Resource Group name>" -VaultName "<Vault Name>").ResourceId
    $CertificateStore = "My"
    $vm = Add-AzVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore $CertificateStore -CertificateUrl $secretURL

## <a name="step-6-connecting-to-the-vm"></a>Passo 6: Ligação ao VM
Antes de se ligar ao VM, terá de se certificar de que a sua máquina está configurada para a gestão remota WinRM. Inicie a PowerShell como administrador e execute o comando abaixo para se certificar de que está configurado.

    Enable-PSRemoting -Force

> [!NOTE]
> Pode ser necessário certificar-se de que o serviço WinRM está em funcionamento se o acima não funcionar. Pode sê-lo usando`Get-Service WinRM`
> 
> 

Uma vez feita a configuração, pode ligar-se ao VM utilizando o comando abaixo

    Enter-PSSession -ConnectionUri https://<public-ip-dns-of-the-vm>:5986 -Credential $cred -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck) -Authentication Negotiate
