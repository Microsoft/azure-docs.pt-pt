---
title: Criar um cluster usando o nome comum do certificado
description: Saiba como criar um Cluster Service Fabric usando o nome comum do certificado de um modelo.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 4a4448c88fa9493979f075f6b9c669927dd1d39e
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75614558"
---
# <a name="deploy-a-service-fabric-cluster-that-uses-certificate-common-name-instead-of-thumbprint"></a>Implantar um Cluster Service Fabric que usa o nome comum do certificado em vez da impressão digital
Dois certificados não podem ter a mesma impressão digital, o que torna difícil a substituição ou o gerenciamento do certificado do cluster. No entanto, vários certificados podem ter o mesmo nome ou assunto comum.  Um cluster que usa nomes comuns de certificado torna muito mais simples o gerenciamento de certificados. Este artigo descreve como implantar um Cluster Service Fabric para usar o nome comum do certificado em vez da impressão digital do certificado.
 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-a-certificate"></a>Obter um certificado
Primeiro, obtenha um certificado de uma [autoridade de certificação (CA)](https://wikipedia.org/wiki/Certificate_authority).  O nome comum do certificado deve ser para o domínio personalizado que você possui e comprado de um registrador de domínio. Por exemplo, "azureservicefabricbestpractices.com"; aqueles que não são funcionários da Microsoft não podem provisionar certificados para domínios MS, para que você não possa usar os nomes DNS de seu LB ou Gerenciador de tráfego como nomes comuns para seu certificado, e você precisará provisionar uma [zona DNS do Azure](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns) se seu domínio personalizado puder ser resolvido no Azure. Você também desejará declarar seu domínio personalizado que você possui como "managementEndpoint" do seu cluster se desejar que o portal reflita o alias de domínio personalizado para o cluster.

Para fins de teste, você pode obter um certificado assinado por uma autoridade de certificação de uma autoridade de certificação gratuita ou aberta.

> [!NOTE]
> Os certificados autoassinados, incluindo aqueles gerados ao implantar um cluster de Service Fabric no portal do Azure, não têm suporte. 

## <a name="upload-the-certificate-to-a-key-vault"></a>Carregar o certificado em um cofre de chaves
No Azure, um Cluster Service Fabric é implantado em um conjunto de dimensionamento de máquinas virtuais.  Carregue o certificado em um cofre de chaves.  Quando o cluster é implantado, o certificado é instalado no conjunto de dimensionamento de máquinas virtuais no qual o cluster está sendo executado.

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

# Create new Resource Group 
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region -EnabledForDeployment 
$resourceId = $newKeyVault.ResourceId 

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
```

## <a name="download-and-update-a-sample-template"></a>Baixar e atualizar um modelo de exemplo
Este artigo usa o modelo de [exemplo de cluster seguro de 5 nós](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure) e parâmetros de modelo. Baixe os arquivos *azuredeploy. JSON* e *azuredeploy. Parameters. JSON* em seu computador.

### <a name="update-parameters-file"></a>Atualizar arquivo de parâmetros
Primeiro, abra o arquivo *azuredeploy. Parameters. JSON* em um editor de texto e adicione o seguinte valor de parâmetro:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
"certificateIssuerThumbprint": {
    "value": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
},
```

Em seguida, defina os valores de parâmetro *certificateCommonName*, *sourceVaultValue*e *certificateUrlValue* para aqueles retornados pelo script anterior:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
"certificateIssuerThumbprint": {
    "value": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
},
"sourceVaultValue": {
  "value": "/subscriptions/<subscription>/resourceGroups/testvaultgroup/providers/Microsoft.KeyVault/vaults/testvault"
},
"certificateUrlValue": {
  "value": "https://testvault.vault.azure.net:443/secrets/testcert/5c882b7192224447bbaecd5a46962655"
},
```

### <a name="update-the-template-file"></a>Atualizar o arquivo de modelo
Em seguida, abra o arquivo *azuredeploy. JSON* em um editor de texto e faça três atualizações para dar suporte ao nome comum do certificado.

1. Na seção **parâmetros** , adicione um parâmetro *certificateCommonName* :
    ```json
    "certificateCommonName": {
      "type": "string",
      "metadata": {
        "description": "Certificate Commonname"
      }
    },
    "certificateIssuerThumbprint": {
      "type": "string",
      "metadata": {
        "description": "Certificate Authority Issuer Thumpbrint for Commonname cert"
      }
    },
    ```

    Além disso, considere remover a *certificateThumbprint*, ela pode não ser mais necessária.

2. Defina o valor da variável *sfrpApiVersion* como "2018-02-01":
    ```json
    "sfrpApiVersion": "2018-02-01",
    ```

3. No recurso **Microsoft. Compute/virtualMachineScaleSets** , atualize a extensão da máquina virtual para usar o nome comum nas configurações de certificado em vez da impressão digital.  Em **virtualMachineProfile**->**extensionProfile**->**extensões**->**Propriedades**->**configurações**->**certificado**, adicionar 
    ```json
       "commonNames": [
        "[parameters('certificateCommonName')]"
       ],
    ```

    e remova `"thumbprint": "[parameters('certificateThumbprint')]",`.

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

4. No recurso **Microsoft. perfabric/clusters** , atualize a versão da API para "2018-02-01".  Adicione também uma configuração de **certificateCommonNames** com uma propriedade **comumnames** e remova a configuração de **certificado** (com a propriedade de impressão digital), como no exemplo a seguir:
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
               "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprint')]"
           }
           ],
           "x509StoreName": "[parameters('certificateStoreValue')]"
       },
       ...
   ```
   > [!NOTE]
   > O campo ' certificateIssuerThumbprint ' permite especificar os emissores esperados de certificados com um determinado nome comum da entidade. Esse campo aceita uma enumeração separada por vírgulas de impressões digitais SHA1. Observe que isso é um reforço da validação do certificado – no caso de o emissor não ser especificado ou vazio, o certificado será aceito para autenticação se sua cadeia puder ser criada e terminará em uma raiz confiável pelo validador. Se o emissor for especificado, o certificado será aceito se a impressão digital de seu emissor direto corresponder a qualquer um dos valores especificados neste campo – independentemente de a raiz ser confiável ou não. Observe que uma PKI pode usar diferentes autoridades de certificação para emitir certificados para o mesmo assunto e, portanto, é importante especificar todas as impressões digitais do emissor esperado para um determinado assunto.
   >
   > A especificação do emissor é considerada uma prática recomendada; Embora a omissão dele continue a funcionar – para os certificados que se encadeadom a uma raiz confiável, esse comportamento tem limitações e pode ser dividido em um futuro próximo. Observe também que os clusters implantados no Azure e protegidos com certificados X509 emitidos por uma PKI privada e declarados pelo assunto não podem ser validados pelo serviço de Service Fabric do Azure (para comunicação de cluster para serviço), se a política de certificado PKI Não é detectável, disponível e acessível. 

## <a name="deploy-the-updated-template"></a>Implantar o modelo atualizado
Reimplante o modelo atualizado depois de fazer as alterações.

```powershell
# Variables.
$groupname = "testclustergroup"
$clusterloc="southcentralus"  
$id="<subscription ID"

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $id 

# Create a new resource group and deploy the cluster.
New-AzResourceGroup -Name $groupname -Location $clusterloc

New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\AzureDeploy.Parameters.json" -TemplateFile "C:\temp\cluster\AzureDeploy.json" -Verbose
```

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre a [segurança do cluster](service-fabric-cluster-security.md).
* Saiba como [sobrepor um certificado de cluster](service-fabric-cluster-rollover-cert-cn.md)
* [Atualizar e gerenciar certificados de cluster](service-fabric-cluster-security-update-certs-azure.md)
* Simplifique o gerenciamento [de certificados alterando o cluster da impressão digital do certificado para o nome comum](service-fabric-cluster-change-cert-thumbprint-to-cn.md)

[image1]: .\media\service-fabric-cluster-change-cert-thumbprint-to-cn\PortalViewTemplates.png
