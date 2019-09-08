---
title: Atualizar um cluster de Service Fabric do Azure para usar o nome comum do certificado | Microsoft Docs
description: Saiba como alternar um Cluster Service Fabric usando impressões digitais de certificado para usar o nome comum do certificado.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/06/2019
ms.author: atsenthi
ms.openlocfilehash: 3618339349d618b371a40d3b37ebc30192c067ca
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70764830"
---
# <a name="change-cluster-from-certificate-thumbprint-to-common-name"></a>Alterar o cluster da impressão digital do certificado para o nome comum
Dois certificados não podem ter a mesma impressão digital, o que torna difícil a substituição ou o gerenciamento do certificado do cluster. No entanto, vários certificados podem ter o mesmo nome ou assunto comum.  Alternar um cluster implantado do uso de impressões digitais de certificado para usar nomes comuns de certificado torna o gerenciamento de certificados muito mais simples. Este artigo descreve como atualizar um Cluster Service Fabric em execução para usar o nome comum do certificado em vez da impressão digital do certificado.

>[!NOTE]
> Se você tiver duas impressões digitais declaradas em seu modelo, precisará executar duas implantações.  A primeira implantação é feita antes de seguir as etapas neste artigo.  A primeira implantação define sua propriedade de **impressão digital** no modelo para o certificado que está sendo usado e remove a propriedade **thumbprintSecondary** .  Para a segunda implantação, siga as etapas neste artigo.
 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-a-certificate"></a>Obter um certificado
Primeiro, obtenha um certificado de uma [autoridade de certificação (CA)](https://wikipedia.org/wiki/Certificate_authority).  O nome comum do certificado deve ser o nome do host do cluster.  Por exemplo, "myclustername.southcentralus.cloudapp.azure.com".  

Para fins de teste, você pode obter um certificado assinado por uma autoridade de certificação de uma autoridade de certificação gratuita ou aberta.

> [!NOTE]
> Os certificados autoassinados, incluindo aqueles gerados ao implantar um cluster de Service Fabric no portal do Azure, não têm suporte.

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>Carregar o certificado e instalá-lo no conjunto de dimensionamento
No Azure, um Cluster Service Fabric é implantado em um conjunto de dimensionamento de máquinas virtuais.  Carregue o certificado em um cofre de chaves e, em seguida, instale-o no conjunto de dimensionamento de máquinas virtuais no qual o cluster está sendo executado.

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "mykeyvault"
$certFilename = "C:\users\sfuser\myclustercert.pfx"
$certname = "myclustercert"
$Password  = "P@ssw0rd!123"
$VmssResourceGroupName     = "myclustergroup"
$VmssName                  = "prnninnxj"

# Create new Resource Group 
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName `
    -Location $region -EnabledForDeployment 
$resourceId = $newKeyVault.ResourceId 

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzKeyVaultCertificate -VaultName $vaultName -Name $certName `
    -FilePath $certFilename -Password $PasswordSec

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
$vmss = Add-AzVmssSecret -VirtualMachineScaleSet $vmss -SourceVaultId $SourceVault `
    -VaultCertificate $certConfig

# Update the VM scale set 
Update-AzVmss -ResourceGroupName $VmssResourceGroupName -Verbose `
    -Name $VmssName -VirtualMachineScaleSet $vmss 
```

>[!NOTE]
> Os segredos do conjunto de dimensionamento não dão suporte à mesma ID de recurso para dois segredos separados, pois cada segredo é um recurso com versão e exclusivo. 

## <a name="download-and-update-the-template-from-the-portal"></a>Baixar e atualizar o modelo no portal
O certificado foi instalado no conjunto de dimensionamento subjacente, mas você também precisa atualizar o cluster de Service Fabric para usar esse certificado e seu nome comum.  Agora, baixe o modelo para a implantação do cluster.  Entre no [portal do Azure](https://portal.azure.com) e navegue até o grupo de recursos que hospeda o cluster.  Em **configurações**, selecione **implantações**.  Selecione a implantação mais recente e clique em **Exibir modelo**.

![Exibir modelos][image1]

Baixe os arquivos JSON de modelo e parâmetros para o computador local.

Primeiro, abra o arquivo de parâmetros em um editor de texto e adicione o seguinte valor de parâmetro:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
```

Em seguida, abra o arquivo de modelo em um editor de texto e faça três atualizações para dar suporte ao nome comum do certificado.

1. Na seção **parâmetros** , adicione um parâmetro *certificateCommonName* :
    ```json
    "certificateCommonName": {
        "type": "string",
        "metadata": {
            "description": "Certificate Commonname"
        }
    },
    ```

    Além disso, considere remover o *certificateThumbprint*, ele não poderá mais ser referenciado no modelo do Resource Manager.

2. No recurso **Microsoft. Compute/virtualMachineScaleSets** , atualize a extensão da máquina virtual para usar o nome comum nas configurações de certificado em vez da impressão digital.  Em->**Propriedades**->**de extensões do virtualMachineProfile**extensionProfileconfiguraçõesdo->**certificado**, adicionar->-> `"commonNames": ["[parameters('certificateCommonName')]"],` e remova `"thumbprint": "[parameters('certificateThumbprint')]",`.
    ```json
        "virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
                    "properties": {
                        "type": "ServiceFabricNode",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                            "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                            "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
                        },
                        "publisher": "Microsoft.Azure.ServiceFabric",
                        "settings": {
                            "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                            "nodeTypeRef": "[variables('vmNodeType0Name')]",
                            "dataPath": "D:\\SvcFab",
                            "durabilityLevel": "Bronze",
                            "enableParallelJobs": true,
                            "nicPrefixOverride": "[variables('subnet0Prefix')]",
                            "certificate": {
                                "commonNames": [
                                    "[parameters('certificateCommonName')]"
                                ],
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            }
                        },
                        "typeHandlerVersion": "1.0"
                    }
                },
    ```

3.  No recurso **Microsoft. perfabric/clusters** , atualize a versão da API para "2018-02-01".  Adicione também uma configuração de **certificateCommonNames** com uma propriedade **comumnames** e remova a configuração de **certificado** (com a propriedade de impressão digital), como no exemplo a seguir:
    ```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
        ],
        "properties": {
            "addonFeatures": [
                "DnsService",
                "RepairManager"
            ],
            "certificateCommonNames": {
                "commonNames": [
                    {
                        "certificateCommonName": "[parameters('certificateCommonName')]",
                        "certificateIssuerThumbprint": ""
                    }
                ],
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
        ...
    ```

## <a name="deploy-the-updated-template"></a>Implantar o modelo atualizado
Reimplante o modelo atualizado depois de fazer as alterações.

```powershell
$groupname = "sfclustertutorialgroup"

New-AzResourceGroupDeployment -ResourceGroupName $groupname -Verbose `
    -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" 
```

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre a [segurança do cluster](service-fabric-cluster-security.md).
* Saiba como [sobrepor um certificado de cluster](service-fabric-cluster-rollover-cert-cn.md)
* [Atualizar e gerenciar certificados de cluster](service-fabric-cluster-security-update-certs-azure.md)

[image1]: ./media/service-fabric-cluster-change-cert-thumbprint-to-cn/PortalViewTemplates.png
