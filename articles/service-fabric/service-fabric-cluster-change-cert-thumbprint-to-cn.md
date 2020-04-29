---
title: Atualizar um cluster para usar o nome comum do certificado
description: Aprenda a mudar um cluster de tecido de serviço de usar impressões digitais de certificado para usar o nome comum do certificado.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 1926b0501766eb0a5fe086ceada0c9bf45e3dcf6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81272632"
---
# <a name="change-cluster-from-certificate-thumbprint-to-common-name"></a>Alterar o cluster do thumbprint do certificado para o nome comum
Nenhum certificado pode ter a mesma impressão digital, o que dificulta a capotamento ou a gestão do certificado de cluster. Vários certificados, no entanto, podem ter o mesmo nome ou assunto comum.  Trocar um cluster implantado de usar impressões digitais de certificado para usar nomes comuns de certificado torna a gestão de certificados muito mais simples. Este artigo descreve como atualizar um cluster de tecido de serviço em execução para usar o nome comum do certificado em vez da impressão digital do certificado.

>[!NOTE]
> Se tiver duas impressões digitais declaradas no seu modelo, terá de realizar duas implementações.  A primeira implementação é feita antes de seguir os passos deste artigo.  A primeira implementação define a sua propriedade de **impressão digital** no modelo para o certificado que está a ser usado e remove a propriedade **thumbprintSecondary.**  Para a segunda implementação, siga os passos deste artigo.
 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-a-certificate"></a>Obter um certificado
Primeiro, obtenha um certificado de uma autoridade de [certificados (CA)](https://wikipedia.org/wiki/Certificate_authority).  O nome comum do certificado deve ser para o domínio personalizado que possui, e comprado a partir de um registo de domínio. Por exemplo, "azureservicefabricbestpractices.com"; aqueles que não são empregados da Microsoft não podem fornecer certificados para domínios EmS, pelo que não pode utilizar os nomes DNS do seu LB ou Traffic Manager como nomes comuns para o seu certificado, e terá de fornecer uma [Zona DNS Azure](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns) se o seu domínio personalizado for resolúvel em Azure. Também vai querer declarar o seu domínio personalizado que possui como "managementEndpoint" do seu cluster se quiser que o portal reflita o pseudónimo de domínio personalizado para o seu cluster.

Para efeitos de teste, pode obter um certificado assinado pela AC de uma autoridade de certificados gratuitos ou abertos.

> [!NOTE]
> Os certificados auto-assinados, incluindo os gerados na implementação de um cluster de tecido de serviço no portal Azure, não são suportados. 

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>Faça upload do certificado e instale-o no conjunto de escala
Em Azure, um cluster de tecido de serviço é implantado num conjunto de escala de máquina virtual.  Faça o upload do certificado para um cofre chave e instale-o no conjunto de escala virtual da máquina em que o cluster está a funcionar.

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
> Os segredos conjuntos de escala não suportam o mesmo ID de recurso para dois segredos separados, uma vez que cada segredo é um recurso exclusivo e versão. 

## <a name="download-and-update-the-template-from-the-portal"></a>Descarregue e atualize o modelo a partir do portal
O certificado foi instalado no conjunto de escala subjacente, mas também precisa atualizar o cluster Service Fabric para utilizar esse certificado e o seu nome comum.  Agora, descarregue o modelo para a sua implementação do cluster.  Inscreva-se no [portal Azure](https://portal.azure.com) e navegue para o grupo de recursos que acolhe o cluster.  Em **Definições,** selecione **Implementações**.  Selecione a implementação mais recente e clique no **modelo 'Ver'.**

![Ver modelos][image1]

Descarregue o modelo e os parâmetros dos ficheiros JSON para o seu computador local.

Em primeiro lugar, abra o ficheiro de parâmetros num editor de texto e adicione o seguinte valor parâmetro:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
```

Em seguida, abra o ficheiro do modelo num editor de texto e faça três atualizações para apoiar o nome comum do certificado.

1. Na secção **de parâmetros,** adicione um *parâmetro de nome comum* certificado:
    ```json
    "certificateCommonName": {
        "type": "string",
        "metadata": {
            "description": "Certificate Commonname"
        }
    },
    ```

    Considere também a remoção do *certificadoImpressão Polegar,* pode deixar de ser referenciado no modelo de Gestor de Recursos.

2. No recurso **Microsoft.Compute/virtualMachineScaleSets,** atualize a extensão da máquina virtual para utilizar o nome comum nas definições do certificado em vez da impressão digital.  No**properties**->**settings**-> **virtualMachineProfile**->**extensões De definições**->de `"thumbprint": "[parameters('certificateThumbprint')]",`**extensões,**->adicione**certificate** `"commonNames": ["[parameters('certificateCommonName')]"],` e remova .
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

3.  No recurso **Microsoft.ServiceFabric/clusters,** atualize a versão API para "2018-02-01".  Adicione também uma definição de **certificateCommonNames** com uma propriedade **commonNames** e remova a definição do **certificado** (com a propriedade de impressão digital) como no seguinte exemplo:
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

Para obter informações adicionais consulte [A implantação de um cluster de tecido de serviço que utilize o nome comum do certificado em vez da impressão digital.](https://docs.microsoft.com/azure/service-fabric/service-fabric-create-cluster-using-cert-cn)

## <a name="deploy-the-updated-template"></a>Implementar o modelo atualizado
Reutilizar o modelo atualizado depois de efazer as alterações.

```powershell
$groupname = "sfclustertutorialgroup"

New-AzResourceGroupDeployment -ResourceGroupName $groupname -Verbose `
    -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" 
```

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [a segurança do cluster.](service-fabric-cluster-security.md)
* Saiba como [reverter um certificado](service-fabric-cluster-rollover-cert-cn.md) de cluster
* [Atualizar e Gerir certificados de cluster](service-fabric-cluster-security-update-certs-azure.md)

[image1]: ./media/service-fabric-cluster-change-cert-thumbprint-to-cn/PortalViewTemplates.png
