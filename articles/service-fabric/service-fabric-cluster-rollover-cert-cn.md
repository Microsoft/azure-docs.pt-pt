---
title: Rolar um certificado de cluster de tecido de serviço Azure
description: Saiba como passar um certificado de cluster de Tecido de Serviço identificado pelo nome comum do certificado.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 65ea4f463073c472ac6a31e62dcfdfd11cb28cc5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88853342"
---
# <a name="manually-roll-over-a-service-fabric-cluster-certificate"></a>Rolar manualmente sobre um certificado de cluster de tecido de serviço
Quando um certificado de cluster de Tecido de Serviço está perto de expirar, você precisa atualizar o certificado.  A cadugação do certificado é simples se o cluster foi [criado para utilizar certificados com base no nome comum](service-fabric-cluster-change-cert-thumbprint-to-cn.md) (em vez de impressão digital).  Obtenha um novo certificado de uma autoridade de certificado com uma nova data de validade.  Os certificados auto-assinados não são suporte para clusters de tecido de serviço de produção, para incluir certificados gerados durante o fluxo de trabalho de criação do portal Azure Cluster. O novo certificado deve ter o mesmo nome comum que o certificado antigo. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

O cluster de tecidos de serviço utilizará automaticamente o certificado declarado com uma nova data de validade futura; quando mais de um certificado validado é instalado no anfitrião. Uma boa prática é usar um modelo de Gestor de Recursos para a provisionar recursos Azure. Para ambientes não produtivos, o seguinte script pode ser usado para carregar um novo certificado para um cofre de chaves e, em seguida, instala o certificado no conjunto de escala de máquina virtual: 

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  <subscription ID>

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "keyvaultgroup"
$VaultName = "cntestvault2"
$certFilename = "C:\users\sfuser\sftutorialcluster20180419110824.pfx"
$certname = "cntestcert"
$Password  = "!P@ssw0rd321"
$VmssResourceGroupName     = "sfclustertutorialgroup"
$VmssName                  = "prnninnxj"

# Create new Resource Group 
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Get the key vault.  The key vault must be enabled for deployment.
$keyVault = Get-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName 
$resourceId = $keyVault.ResourceId  

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzKeyVaultCertificate -VaultName $vaultName -Name $certName  -FilePath $certFilename -Password $PasswordSec

$CertificateThumbprint = $KVSecret.Thumbprint
$CertificateURL = $KVSecret.SecretId
$SourceVault = $resourceId
$CommName    = $KVSecret.Certificate.SubjectName.Name

Write-Host "CertificateThumbprint    :"  $CertificateThumbprint
Write-Host "CertificateURL           :"  $CertificateURL
Write-Host "SourceVault              :"  $SourceVault
Write-Host "Common Name              :"  $CommName    

Set-StrictMode -Version 3
$ErrorActionPreference = "Stop"

$certConfig = New-AzVmssVaultCertificateConfig -CertificateUrl $CertificateURL -CertificateStore "My"

# Get current VM scale set 
$vmss = Get-AzVmss -ResourceGroupName $VmssResourceGroupName -VMScaleSetName $VmssName

# Add new secret to the VM scale set.
$vmss.VirtualMachineProfile.OsProfile.Secrets[0].VaultCertificates.Add($certConfig)

# Update the VM scale set 
Update-AzVmss -ResourceGroupName $VmssResourceGroupName -Name $VmssName -VirtualMachineScaleSet $vmss  -Verbose
```

>[!NOTE]
> Computes Virtual Machine Scale set Secrets não suportam o mesmo id de recursos para dois segredos separados, uma vez que cada segredo é um recurso único em versão. 

## <a name="next-steps"></a>Passos Seguintes

* Conheça a [segurança do cluster.](service-fabric-cluster-security.md)
* [Atualizar e Gerir certificados de cluster](service-fabric-cluster-security-update-certs-azure.md)
