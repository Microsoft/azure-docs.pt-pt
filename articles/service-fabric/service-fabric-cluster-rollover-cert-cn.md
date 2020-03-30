---
title: Rolar sobre um certificado de cluster Azure Service Fabric
description: Aprenda a revestir um certificado de cluster service Fabric identificado pelo nome comum do certificado.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 94cc6841886b1b0eb4271ac0f727a2e3561e0081
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451967"
---
# <a name="manually-roll-over-a-service-fabric-cluster-certificate"></a>Rolar manualmente sobre um certificado de cluster de tecido de serviço
Quando um certificado de cluster service Fabric está perto de expirar, você precisa atualizar o certificado.  A capotação do certificado é simples se o cluster foi [criado para utilizar certificados baseados no nome comum](service-fabric-cluster-change-cert-thumbprint-to-cn.md) (em vez de impressão digital).  Obtenha um novo certificado de uma autoridade de certificados com uma nova data de validade.  Os certificados auto-assinados não são suporte para clusters de tecido de serviço de produção, para incluir certificados gerados durante o fluxo de trabalho de criação do portal Azure Cluster. O novo certificado deve ter o mesmo nome comum que o certificado mais antigo. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

O cluster de tecido de serviço utilizará automaticamente o certificado declarado com uma nova data de validade futura; quando for instalado mais de um certificado de validação no hospedeiro. A melhor prática é usar um modelo de Gestor de Recursos para fornecer Recursos Azure. Para o ambiente de não produção, o seguinte script pode ser usado para carregar um novo certificado para um cofre chave e, em seguida, instala o certificado no conjunto de escala de máquina virtual: 

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
$vmss = Add-AzVmssSecret -VirtualMachineScaleSet $vmss -SourceVaultId $SourceVault -VaultCertificate $certConfig

# Update the VM scale set 
Update-AzVmss -ResourceGroupName $VmssResourceGroupName -Name $VmssName -VirtualMachineScaleSet $vmss  -Verbose
```

>[!NOTE]
> Computes Virtual Machine Scale set Secrets não suportam o mesmo id de recursos para dois segredos separados, uma vez que cada segredo é um recurso exclusivo versão. 

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [a segurança do cluster.](service-fabric-cluster-security.md)
* [Atualizar e Gerir certificados de cluster](service-fabric-cluster-security-update-certs-azure.md)
