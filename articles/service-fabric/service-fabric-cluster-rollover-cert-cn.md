---
title: Sobrepor um certificado de Cluster Service Fabric do Azure
description: Saiba como estender um certificado de Cluster Service Fabric identificado pelo nome comum do certificado.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 94cc6841886b1b0eb4271ac0f727a2e3561e0081
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75451967"
---
# <a name="manually-roll-over-a-service-fabric-cluster-certificate"></a>Reverter manualmente um Service Fabric certificado de cluster
Quando um certificado de Cluster Service Fabric está perto de expirar, você precisa atualizar o certificado.  A substituição de certificado será simples se o cluster tiver sido [configurado para usar certificados com base no nome comum](service-fabric-cluster-change-cert-thumbprint-to-cn.md) (em vez de impressão digital).  Obtenha um novo certificado de uma autoridade de certificação com uma nova data de validade.  Os certificados autoassinados não têm suporte para clusters de Service Fabric de produção, para incluir certificados gerados durante o fluxo de trabalho de criação de cluster portal do Azure. O novo certificado deve ter o mesmo nome comum que o certificado mais antigo. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Service Fabric cluster usará automaticamente o certificado declarado com um adicional na data de validade futura; Quando mais de um certificado de validação estiver instalado no host. Uma prática recomendada é usar um modelo do Resource Manager para provisionar recursos do Azure. Para o ambiente de não produção, o script a seguir pode ser usado para carregar um novo certificado em um cofre de chaves e, em seguida, instalar o certificado no conjunto de dimensionamento de máquinas virtuais: 

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
> Computa os segredos do conjunto de dimensionamento de máquinas virtuais não dão suporte à mesma ID de recurso para dois segredos separados, pois cada segredo é um recurso exclusivo com controle de versão. 

## <a name="next-steps"></a>Próximos Passos

* Saiba mais sobre a [segurança do cluster](service-fabric-cluster-security.md).
* [Atualizar e gerenciar certificados de cluster](service-fabric-cluster-security-update-certs-azure.md)
