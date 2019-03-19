---
title: Criar um cluster de Azure Service Fabric com o nome comum do certificado | Documentos da Microsoft
description: Saiba como criar um cluster do Service Fabric com o nome comum do certificado de um modelo.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/24/2018
ms.author: ryanwi
ms.openlocfilehash: f6f4858740288facb1e206eed3a8cd4ee1854daa
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58111451"
---
# <a name="deploy-a-service-fabric-cluster-that-uses-certificate-common-name-instead-of-thumbprint"></a>Implementar um cluster do Service Fabric que utiliza o nome comum do certificado em vez de thumbprint
Não existem dois certificados podem ter o mesmo thumbprint, o que torna difícil rollover de certificado de cluster ou de gestão. No entanto, vários certificados, podem ter o mesmo nome comum ou assunto.  Um cluster com o nome comum do certificado de faz a gestão de certificados muito mais simples. Este artigo descreve como implementar um cluster do Service Fabric para utilizar o nome comum do certificado em vez do thumbprint do certificado.
 
## <a name="get-a-certificate"></a>Obter um certificado
Primeiro, obtenha um certificado de um [autoridade de certificação (AC)](https://wikipedia.org/wiki/Certificate_authority).  O nome comum do certificado deve ser para o domínio personalizado próprio e comprados numa entidade de registo do domínio. Por exemplo, "azureservicefabricbestpractices.com;" aqueles a quem não são funcionários da Microsoft não podem aprovisionar certificados para os domínios da MS, para que não pode utilizar os nomes DNS da sua LB ou o Gestor de tráfego como nomes comuns para o seu certificado e tem de aprovisionar um [zona de DNS do Azure](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns) se sua personalizado domínio a ser resolvido no Azure. Também deve declarar seu domínio personalizado que é proprietário como o "managementEndpoint" do cluster se pretender que o portal para refletir o domínio personalizado alias para o seu cluster.

Para fins de teste, foi possível obter um certificado assinado de AC de uma autoridade de certificação gratuito ou aberto.

> [!NOTE]
> Certificados autoassinados, inclusive aquelas geradas ao implementar um cluster do Service Fabric no portal do Azure, não são suportados. 

## <a name="upload-the-certificate-to-a-key-vault"></a>Carregue o certificado para um cofre de chaves
No Azure, um cluster do Service Fabric é implementado num conjunto de dimensionamento de máquina virtual.  Carregue o certificado para um cofre de chaves.  Quando implementa o cluster, instala o certificado no conjunto de dimensionamento de máquina virtual que o cluster está a executar.

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

# Create new Resource Group 
New-AzureRmResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region -EnabledForDeployment 
$resourceId = $newKeyVault.ResourceId 

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name $certName  -FilePath $certFilename -Password $PasswordSec

$CertificateThumbprint = $KVSecret.Thumbprint
$CertificateURL = $KVSecret.SecretId
$SourceVault = $resourceId
$CommName    = $KVSecret.Certificate.SubjectName.Name

Write-Host "CertificateThumbprint    :"  $CertificateThumbprint
Write-Host "CertificateURL           :"  $CertificateURL
Write-Host "SourceVault              :"  $SourceVault
Write-Host "Common Name              :"  $CommName    
```

## <a name="download-and-update-a-sample-template"></a>Transferir e atualizar um modelo de exemplo
Este artigo utiliza a [exemplo de 5 nós de cluster seguro](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure) modelo e parâmetros de modelo. Transfira o *azuredeploy. JSON* e *azuredeploy* ficheiros para o seu computador.

### <a name="update-parameters-file"></a>Ficheiro de parâmetros de atualização
Primeiro, abra a *azuredeploy* ficheiro num editor de texto e adicione o seguinte valor de parâmetro:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
```

Em seguida, configure as *certificateCommonName*, *sourceVaultValue*, e *certificateUrlValue* valores de parâmetro às devolvidos pelo script anterior:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
"sourceVaultValue": {
  "value": "/subscriptions/<subscription>/resourceGroups/testvaultgroup/providers/Microsoft.KeyVault/vaults/testvault"
},
"certificateUrlValue": {
  "value": "https://testvault.vault.azure.net:443/secrets/testcert/5c882b7192224447bbaecd5a46962655"
},
```

### <a name="update-the-template-file"></a>Atualizar o ficheiro de modelo
Em seguida, abra a *azuredeploy. JSON* ficheiro num editor de texto e fazer três atualizações para suportar o nome comum do certificado.

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

2. Defina o valor do *sfrpApiVersion* variável "2018-02-01":
    ```json
    "sfrpApiVersion": "2018-02-01",
    ```

3. Na **Compute/virtualmachinescalesets** recurso, atualize a extensão de máquina virtual para utilizar o nome comum nas definições de certificado em vez do thumbprint.  Na **virtualMachineProfile**->**extensionProfile**->**extensões**->**propriedades** -> **definições**->**certificado**, adicionar 
    ```json
       "commonNames": [
        "[parameters('certificateCommonName')]"
       ],
    ```

    e remover `"thumbprint": "[parameters('certificateThumbprint')]",`.

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

4. Na **Microsoft.ServiceFabric/clusters** recurso, a API de atualização de versão "2018-02-01".  Também adicionar uma **certificateCommonNames** definição com um **commonNames** propriedade e remove o **certificado** definição (com a propriedade thumbprint) como a seguir exemplo:
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
   > O campo 'certificateIssuerThumbprint' permite especificar os esperado emissores de certificados com um nome comum de determinado assunto. Este campo aceita uma enumeração separados por vírgulas de thumbprints de SHA1. Tenha em atenção de que este é um fortalecimento da validação do certificado - o caso quando o emissor não for especificado ou vazio, o certificado serão aceites para a autenticação se a sua cadeia pode ser criada e termina numa raiz considerado fidedigno pelo validador. Se o emissor for especificado, será aceite o certificado se o thumbprint do seu emissor direto corresponde a qualquer um dos valores especificados nesse campo - independentemente se a raiz seja confiável ou não. Tenha em atenção que uma PKI pode utilizar autoridades de certificação diferentes para emitir certificados para o mesmo assunto e, portanto, é importante especificar todos os thumbprints do emissor esperado para um determinado assunto.
   >
   > Especificar o emissor é considerada uma prática recomendada; ao omitir-continuarão a funcionar – para certificados de encadeamento de cópia de segurança para uma raiz confiável - esse comportamento tem limitações e pode ser descontinuado em breve. Tenha também em atenção que os clusters implementados no Azure e protegidos com X509 certificados emitidos por uma PKI privada e declarada pelo requerente talvez não consiga ser validadas pelo serviço do Azure Service Fabric (para comunicação de serviço de cluster), se política de certificados do PKI Não é visível, disponível e acessível. 

## <a name="deploy-the-updated-template"></a>Implementar o modelo atualizado
Reimplemente o modelo atualizado depois de efetuar as alterações.

```powershell
# Variables.
$groupname = "testclustergroup"
$clusterloc="southcentralus"  
$id="<subscription ID"

# Sign in to your Azure account and select your subscription
Login-AzureRmAccount -SubscriptionId $id 

# Create a new resource group and deploy the cluster.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\AzureDeploy.Parameters.json" -TemplateFile "C:\temp\cluster\AzureDeploy.json" -Verbose
```

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre [segurança de cluster](service-fabric-cluster-security.md).
* Saiba como [rollover de um certificado de cluster](service-fabric-cluster-rollover-cert-cn.md)
* [Atualizar e gerir certificados de cluster](service-fabric-cluster-security-update-certs-azure.md)
* Simplificar a gestão de certificados por [cluster de alteração de thumbprint do certificado para o nome comum](service-fabric-cluster-change-cert-thumbprint-to-cn.md)

[image1]: .\media\service-fabric-cluster-change-cert-thumbprint-to-cn\PortalViewTemplates.png
