---
title: Atualizar um cluster do Azure Service Fabric para utilizar o nome comum do certificado | Documentos da Microsoft
description: Saiba como mudar de um cluster do Service Fabric da utilização de thumbprints de certificado a utilizar o nome comum do certificado.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: aljo
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/01/2019
ms.author: aljo
ms.openlocfilehash: 7280424c57e2dfab0a64794855cb522bcf69e135
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58500513"
---
# <a name="change-cluster-from-certificate-thumbprint-to-common-name"></a>Alterar o cluster de thumbprint do certificado para o nome comum
Não existem dois certificados podem ter o mesmo thumbprint, o que torna difícil rollover de certificado de cluster ou de gestão. No entanto, vários certificados, podem ter o mesmo nome comum ou assunto.  Mudar de um cluster implementado da utilização de thumbprints de certificado a utilizar nomes comuns do certificado faz a gestão de certificados muito mais simples. Este artigo descreve como atualizar um cluster do Service Fabric em execução para utilizar o nome comum do certificado em vez do thumbprint do certificado.

>[!NOTE]
> Se tiver dois thumbprint declarado no seu modelo, terá de realizar duas implementações.  A primeira implementação é feita antes de seguir os passos neste artigo.  A primeira implementação define sua **thumbprint** propriedade no modelo para o certificado que está a ser utilizado e remove a **thumbprintSecondary** propriedade.  Para a segunda implementação, siga os passos neste artigo.
 
## <a name="get-a-certificate"></a>Obter um certificado
Primeiro, obtenha um certificado de um [autoridade de certificação (AC)](https://wikipedia.org/wiki/Certificate_authority).  O nome comum do certificado deve ser o nome de anfitrião do cluster.  Por exemplo, "myclustername.southcentralus.cloudapp.azure.com".  

Para fins de teste, foi possível obter um certificado assinado de AC de uma autoridade de certificação gratuito ou aberto.

> [!NOTE]
> Certificados autoassinados, inclusive aquelas geradas ao implementar um cluster do Service Fabric no portal do Azure, não são suportados.

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>Carregue o certificado e instalá-lo no conjunto de dimensionamento
No Azure, um cluster do Service Fabric é implementado num conjunto de dimensionamento de máquina virtual.  Carregue o certificado para um cofre de chaves e, em seguida, instale-o no conjunto de dimensionamento de máquina virtual em execução no cluster.

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
Login-AzureRmAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "mykeyvault"
$certFilename = "C:\users\sfuser\myclustercert.pfx"
$certname = "myclustercert"
$Password  = "P@ssw0rd!123"
$VmssResourceGroupName     = "myclustergroup"
$VmssName                  = "prnninnxj"

# Create new Resource Group 
New-AzureRmResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName `
    -Location $region -EnabledForDeployment 
$resourceId = $newKeyVault.ResourceId 

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name $certName `
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

$certConfig = New-AzureRmVmssVaultCertificateConfig -CertificateUrl $CertificateURL -CertificateStore "My"

# Get current VM scale set 
$vmss = Get-AzureRmVmss -ResourceGroupName $VmssResourceGroupName -VMScaleSetName $VmssName

# Add new secret to the VM scale set.
$vmss = Add-AzureRmVmssSecret -VirtualMachineScaleSet $vmss -SourceVaultId $SourceVault `
    -VaultCertificate $certConfig

# Update the VM scale set 
Update-AzureRmVmss -ResourceGroupName $VmssResourceGroupName -Verbose `
    -Name $VmssName -VirtualMachineScaleSet $vmss 
```

>[!NOTE]
> Segredos do conjunto de dimensionamento não suporta o mesmo ID de recurso para dois segredos separados, como cada segredo é um recurso com a versão e exclusivo. 

## <a name="download-and-update-the-template-from-the-portal"></a>Transferir e atualizar o modelo a partir do portal
O certificado foi instalado no conjunto de dimensionamento subjacente, mas também tem de atualizar o cluster do Service Fabric para utilizar esse certificado e o respetivo nome comum.  Agora, transfira o modelo para a sua implementação de cluster.  Inicie sessão para o [portal do Azure](https://portal.azure.com) e navegue para o grupo de recursos que aloja o cluster.  Na **configurações**, selecione **implementações**.  Selecione a implementação mais recente e clique em **modelo de exibição**.

![Modelos de exibição][image1]

Transferir os ficheiros JSON do modelo e parâmetros para o computador local.

Em primeiro lugar, abra o ficheiro de parâmetros num editor de texto e adicione o seguinte valor de parâmetro:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
```

Em seguida, abra o ficheiro de modelo no editor de texto e fazer três atualizações para suportar o nome comum do certificado.

1. Na **parâmetros** secção, adicione um *certificateCommonName* parâmetro:
    ```json
    "certificateCommonName": {
        "type": "string",
        "metadata": {
            "description": "Certificate Commonname"
        }
    },
    ```

    Também considere remover a *certificateThumbprint*, já não poderá ser necessário.

2. Na **Compute/virtualmachinescalesets** recurso, atualize a extensão de máquina virtual para utilizar o nome comum nas definições de certificado em vez do thumbprint.  Na **virtualMachineProfile**->**extensionProfile**->**extensões**->**propriedades** -> **definições**->**certificado**, adicionar `"commonNames": ["[parameters('certificateCommonName')]"],` e remover `"thumbprint": "[parameters('certificateThumbprint')]",`.
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

3.  Na **Microsoft.ServiceFabric/clusters** recurso, a API de atualização de versão "2018-02-01".  Também adicionar uma **certificateCommonNames** definição com um **commonNames** propriedade e remove o **certificado** definição (com a propriedade thumbprint) como a seguir exemplo:
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

## <a name="deploy-the-updated-template"></a>Implementar o modelo atualizado
Reimplemente o modelo atualizado depois de efetuar as alterações.

```powershell
$groupname = "sfclustertutorialgroup"

New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -Verbose `
    -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" 
```

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre [segurança de cluster](service-fabric-cluster-security.md).
* Saiba como [rollover de um certificado de cluster](service-fabric-cluster-rollover-cert-cn.md)
* [Atualizar e gerir certificados de cluster](service-fabric-cluster-security-update-certs-azure.md)

[image1]: ./media/service-fabric-cluster-change-cert-thumbprint-to-cn/PortalViewTemplates.png
