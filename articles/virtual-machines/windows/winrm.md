---
title: Configurar o acesso do WinRM para uma VM do Azure
description: Configure o acesso WinRM para uso com uma máquina virtual do Azure criada no modelo de implantação do Gerenciador de recursos.
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
ms.openlocfilehash: ca52a458104b4de0f7b3ed2aa3f76109a5623c97
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74067318"
---
# <a name="setting-up-winrm-access-for-virtual-machines-in-azure-resource-manager"></a>Configurando o acesso do WinRM para máquinas virtuais no Azure Resource Manager

Aqui estão as etapas que você precisa executar para configurar uma VM com conectividade de WinRM

1. Criar um Key Vault
2. Criar um certificado autoassinado
3. Carregue seu certificado autoassinado para Key Vault
4. Obtenha a URL para seu certificado autoassinado no Key Vault
5. Referenciar a URL de certificados autoassinados ao criar uma VM

 

## <a name="step-1-create-a-key-vault"></a>Etapa 1: criar um Key Vault
Você pode usar o comando abaixo para criar o Key Vault

```
New-AzKeyVault -VaultName "<vault-name>" -ResourceGroupName "<rg-name>" -Location "<vault-location>" -EnabledForDeployment -EnabledForTemplateDeployment
```

## <a name="step-2-create-a-self-signed-certificate"></a>Etapa 2: criar um certificado autoassinado
Você pode criar um certificado autoassinado usando este script do PowerShell

```
$certificateName = "somename"

$thumbprint = (New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation Cert:\CurrentUser\My -KeySpec KeyExchange).Thumbprint

$cert = (Get-ChildItem -Path cert:\CurrentUser\My\$thumbprint)

$password = Read-Host -Prompt "Please enter the certificate password." -AsSecureString

Export-PfxCertificate -Cert $cert -FilePath ".\$certificateName.pfx" -Password $password
```

## <a name="step-3-upload-your-self-signed-certificate-to-the-key-vault"></a>Etapa 3: carregar seu certificado autoassinado no Key Vault
Antes de carregar o certificado para o Key Vault criado na etapa 1, ele precisa ser convertido em um formato que o provedor de recursos Microsoft. Compute entenderá. O script do PowerShell abaixo permitirá que você faça isso

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

## <a name="step-4-get-the-url-for-your-self-signed-certificate-in-the-key-vault"></a>Etapa 4: obter a URL para o certificado autoassinado no Key Vault
O provedor de recursos Microsoft. Compute precisa de uma URL para o segredo dentro do Key Vault ao provisionar a VM. Isso permite que o provedor de recursos Microsoft. Compute Baixe o segredo e crie o certificado equivalente na VM.

> [!NOTE]
> A URL do segredo precisa incluir a versão também. Uma URL de exemplo é parecida com https:\//contosovault.vault.azure.net:443/secrets/contososecret/01h9db0df2cd4300a20ence585a6s7ve

#### <a name="templates"></a>Modelos
Você pode obter o link para a URL no modelo usando o código abaixo

    "certificateUrl": "[reference(resourceId(resourceGroup().name, 'Microsoft.KeyVault/vaults/secrets', '<vault-name>', '<secret-name>'), '2015-06-01').secretUriWithVersion]"

#### <a name="powershell"></a>PowerShell
Você pode obter essa URL usando o comando do PowerShell abaixo

    $secretURL = (Get-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id

## <a name="step-5-reference-your-self-signed-certificates-url-while-creating-a-vm"></a>Etapa 5: referenciar sua URL de certificados autoassinados ao criar uma VM
#### <a name="azure-resource-manager-templates"></a>Modelos de Azure Resource Manager
Ao criar uma VM por meio de modelos, o certificado é referenciado na seção segredos e na seção winRM, como a seguir:

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

Um modelo de exemplo para o acima pode ser encontrado aqui em [201-VM-WinRM-keyvault-Windows](https://azure.microsoft.com/documentation/templates/201-vm-winrm-keyvault-windows)

O código-fonte para este modelo pode ser encontrado no [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows)

#### <a name="powershell"></a>PowerShell
    $vm = New-AzVMConfig -VMName "<VM name>" -VMSize "<VM Size>"
    $credential = Get-Credential
    $secretURL = (Get-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
    $vm = Set-AzVMOperatingSystem -VM $vm -Windows -ComputerName "<Computer Name>" -Credential $credential -WinRMHttp -WinRMHttps -WinRMCertificateUrl $secretURL
    $sourceVaultId = (Get-AzKeyVault -ResourceGroupName "<Resource Group name>" -VaultName "<Vault Name>").ResourceId
    $CertificateStore = "My"
    $vm = Add-AzVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore $CertificateStore -CertificateUrl $secretURL

## <a name="step-6-connecting-to-the-vm"></a>Etapa 6: conectando-se à VM
Antes de se conectar à VM, você precisará certificar-se de que seu computador está configurado para gerenciamento remoto do WinRM. Inicie o PowerShell como administrador e execute o comando abaixo para certificar-se de que você está configurado.

    Enable-PSRemoting -Force

> [!NOTE]
> Talvez seja necessário verificar se o serviço WinRM está em execução se o anterior não funcionar. Você pode fazer isso usando `Get-Service WinRM`
> 
> 

Quando a instalação for concluída, você poderá se conectar à VM usando o comando abaixo

    Enter-PSSession -ConnectionUri https://<public-ip-dns-of-the-vm>:5986 -Credential $cred -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck) -Authentication Negotiate
