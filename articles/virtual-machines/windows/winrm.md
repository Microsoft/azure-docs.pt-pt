---
title: Configurar o acesso winrm para um VM Azure
description: Configurar o acesso WinRM para utilização com uma máquina virtual Azure criada no modelo de implementação do Gestor de Recursos.
author: mimckitt
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/16/2016
ms.author: mimckitt
ms.openlocfilehash: ab676e7595a8ccd902eea27612e4c2fd035fae0c
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102555725"
---
# <a name="setting-up-winrm-access-for-virtual-machines-in-azure-resource-manager"></a>Criação de acesso WinRM para Máquinas Virtuais em Gestor de Recursos Azure

Aqui estão os passos que você precisa tomar para configurar um VM com conectividade WinRM

1. Criar um Key Vault
2. Criar um certificado autoassinado
3. Faça o upload do seu certificado auto-assinado para Key Vault
4. Obtenha o URL para o seu certificado auto-assinado no Cofre de Chaves
5. Refira o URL dos certificados auto-assinados enquanto cria um VM

 

## <a name="step-1-create-a-key-vault"></a>Passo 1: Criar um cofre de chaves
Pode utilizar o comando abaixo para criar o Cofre de Chaves

```azurepowershell
New-AzKeyVault -VaultName "<vault-name>" -ResourceGroupName "<rg-name>" -Location "<vault-location>" -EnabledForDeployment -EnabledForTemplateDeployment
```

## <a name="step-2-create-a-self-signed-certificate"></a>Passo 2: Criar um certificado auto-assinado
Pode criar um certificado auto-assinado utilizando este script PowerShell

```azurepowershell
$certificateName = "somename"

$thumbprint = (New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation Cert:\CurrentUser\My -KeySpec KeyExchange).Thumbprint

$cert = (Get-ChildItem -Path cert:\CurrentUser\My\$thumbprint)

$password = Read-Host -Prompt "Please enter the certificate password." -AsSecureString

Export-PfxCertificate -Cert $cert -FilePath ".\$certificateName.pfx" -Password $password
```

## <a name="step-3-upload-your-self-signed-certificate-to-the-key-vault"></a>Passo 3: Faça o upload do certificado auto-assinado para o Cofre de Chaves
Antes de enviar o certificado para o Key Vault criado no passo 1, precisa de ser convertido num formato que o fornecedor de recursos Microsoft.Compute compreenderá. O script abaixo do PowerShell permitir-lhe-á fazer isso

```azurepowershell
$fileName = "<Path to the .pfx file>"
$fileContentBytes = Get-Content $fileName -Encoding Byte
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

[System.Collections.HashTable]$TableForJSON = @{
    "data"     = $filecontentencoded;
    "dataType" = "pfx";
    "password" = "<password>";
}
[System.String]$JSONObject = $TableForJSON | ConvertTo-Json

$secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText –Force
Set-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>" -SecretValue $secret
```

## <a name="step-4-get-the-url-for-your-self-signed-certificate-in-the-key-vault"></a>Passo 4: Obtenha o URL para o seu certificado auto-assinado no Cofre de Chaves
O fornecedor de recursos Microsoft.Compute precisa de um URL para o segredo dentro do Cofre de Chaves enquanto abascia o VM. Isto permite ao fornecedor de recursos Microsoft.Compute descarregar o segredo e criar o certificado equivalente no VM.

> [!NOTE]
> O URL do segredo precisa incluir a versão também. Um EXEMPLO de EXEMPLO parece abaixo https: \/ /contosovault.vault.azure.net:443/secrets/contososecret/01h9db0df2cd4300a20ence585a6s7ve

#### <a name="templates"></a>Modelos
Pode obter o link para o URL no modelo usando o código abaixo

```json
"certificateUrl": "[reference(resourceId(resourceGroup().name, 'Microsoft.KeyVault/vaults/secrets', '<vault-name>', '<secret-name>'), '2015-06-01').secretUriWithVersion]"
```

#### <a name="powershell"></a>PowerShell
Pode obter este URL usando o comando PowerShell abaixo

```azurepowershell
$secretURL = (Get-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
```

## <a name="step-5-reference-your-self-signed-certificates-url-while-creating-a-vm"></a>Passo 5: Referenciar o URL dos certificados auto-assinados enquanto cria um VM
#### <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager
Ao criar um VM através de modelos, o certificado é referenciado na secção segredos e na secção winRM como abaixo:

```json
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
```

Um modelo de amostra para o acima pode ser encontrado aqui em [201-vm-winrm-keyvault-windows](https://azure.microsoft.com/documentation/templates/201-vm-winrm-keyvault-windows)

Código fonte para este modelo pode ser encontrado no [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows)

#### <a name="powershell"></a>PowerShell
```azurepowershell
$vm = New-AzVMConfig -VMName "<VM name>" -VMSize "<VM Size>"
$credential = Get-Credential
$secretURL = (Get-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
$vm = Set-AzVMOperatingSystem -VM $vm -Windows -ComputerName "<Computer Name>" -Credential $credential -WinRMHttp -WinRMHttps -ProvisionVMAgent -WinRMCertificateUrl $secretURL
$sourceVaultId = (Get-AzKeyVault -ResourceGroupName "<Resource Group name>" -VaultName "<Vault Name>").ResourceId
$CertificateStore = "My"
$vm = Add-AzVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore $CertificateStore -CertificateUrl $secretURL
```

## <a name="step-6-connecting-to-the-vm"></a>Passo 6: Ligação ao VM
Antes de poder ligar-se ao VM, terá de se certificar de que a sua máquina está configurada para a gestão remota winrm. Inicie o PowerShell como administrador e execute o comando abaixo para se certificar de que está configurado.

```azurepowershell
Enable-PSRemoting -Force
```

> [!NOTE]
> Talvez seja necessário certificar-se de que o serviço WinRM está a funcionar se o acima não funcionar. Podes fazê-lo usando `Get-Service WinRM`
> 
> 

Uma vez feita a configuração, pode ligar-se ao VM utilizando o comando abaixo

```azurepowershell
Enter-PSSession -ConnectionUri https://<public-ip-dns-of-the-vm>:5986 -Credential $cred -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck) -Authentication Negotiate
```
