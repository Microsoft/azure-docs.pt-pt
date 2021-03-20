---
title: Criar um cluster usando o nome comum do certificado
description: Saiba como criar um cluster de tecido de serviço usando o nome comum do certificado a partir de um modelo.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: c852b40d35f936753d3c16420159676da239b6c6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "86246440"
---
# <a name="deploy-a-service-fabric-cluster-that-uses-certificate-common-name-instead-of-thumbprint"></a>Implementar um cluster de tecido de serviço que usa o nome comum do certificado em vez de impressão digital
Nenhum certificado pode ter a mesma impressão digital, o que dificulta a capotamento ou gestão do certificado de cluster. Vários certificados, no entanto, podem ter o mesmo nome comum ou sujeito.  Um cluster usando nomes comuns de certificado torna a gestão de certificados muito mais simples. Este artigo descreve como implantar um cluster de Tecido de Serviço para utilizar o nome comum do certificado em vez da impressão digital do certificado.
 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-a-certificate"></a>Obtenha um certificado
Em primeiro lugar, obtenha um certificado de uma [autoridade de certificados (CA)](https://wikipedia.org/wiki/Certificate_authority).  O nome comum do certificado deve ser para o domínio personalizado que possui, e comprado a partir de um registrador de domínio. Por exemplo, "azureservicefabricbestpractices.com"; aqueles que não são funcionários da Microsoft não podem providenciar certificados para domínios MS, por isso não pode utilizar os nomes DNS do seu LB ou Traffic Manager como nomes comuns para o seu certificado, e terá de providenciar uma [Zona DE DNS Azure](../dns/dns-delegate-domain-azure-dns.md) se o seu domínio personalizado for resolúvel em Azure. Também irá querer declarar o seu domínio personalizado que possui como "managementEndpoint" do seu cluster se quiser que o portal reflita o pseudónimo de domínio personalizado para o seu cluster.

Para efeitos de teste, pode obter um certificado assinado pela AC de uma autoridade de certificados gratuito ou aberto.

> [!NOTE]
> Os certificados auto-assinados, incluindo os gerados ao implantar um cluster de Tecido de Serviço no portal Azure, não são suportados. 

## <a name="upload-the-certificate-to-a-key-vault"></a>Faça o upload do certificado para um cofre chave
Em Azure, um cluster de tecido de serviço é implantado num conjunto de escala de máquina virtual.  Faça o upload do certificado para um cofre de chaves.  Quando o cluster é implantado, o certificado instala-se no conjunto de escala de máquina virtual em que o cluster está em funcionamento.

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

## <a name="download-and-update-a-sample-template"></a>Faça o download e atualize um modelo de amostra
Este artigo utiliza o modelo [de exemplo de cluster seguro de 5 nós](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure) e os parâmetros do modelo. Descarregue o *azuredeploy.js* e *azuredeploy.parameters.jsem* ficheiros para o seu computador.

### <a name="update-parameters-file"></a>Arquivo de parâmetros de atualização
Em primeiro lugar, abra a *azuredeploy.parameters.jsficheiro num* editor de texto e adicione o seguinte valor de parâmetro:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
"certificateIssuerThumbprint": {
    "value": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
},
```

Em seguida, desagreda o *certificadoCommonName*, *sourceVaultValue,* e *certificadoSUrvalue* valores dos valores de parâmetros devolvidos pelo script anterior:
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

### <a name="update-the-template-file"></a>Atualizar o ficheiro de modelo
Em seguida, abra a *azuredeploy.jsem* arquivo num editor de texto e faça três atualizações para suportar o nome comum do certificado.

1. Na secção **de parâmetros,** adicione um *parâmetro certificadoCommonName:*
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

    Considere também a remoção do *certificadoThumbprint,* podendo deixar de ser necessário.

2. Definir o valor da variável *sfrpApiVersion* para "2018-02-01":
    ```json
    "sfrpApiVersion": "2018-02-01",
    ```

3. No recurso **Microsoft.Compute/virtualMachineScaleSets,** atualize a extensão da máquina virtual para utilizar o nome comum nas definições de certificado em vez da impressão digital.  No **virtualMachineProfile** -> **extensõesProfile** -> **extensões** -> **certificado** -> **de definições de propriedades**, -> adicionar 
    ```json
       "commonNames": [
        "[parameters('certificateCommonName')]"
       ],
    ```

    e remover `"thumbprint": "[parameters('certificateThumbprint')]",` .

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

4. No recurso **Microsoft.ServiceFabric/clusters,** atualize a versão API para "2018-02-01".  Adicione também um **certificado Configuração com** uma propriedade **comumNames** e remova a definição do **certificado** (com a propriedade de impressão digital) como no exemplo seguinte:
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
   > O campo 'certificateIssuerThumbprint' permite especificar os emitentes esperados de certificados com um determinado nome comum. Este campo aceita uma enumeração separada por vírgula de impressões digitais SHA1. Note que se trata de um reforço da validação do certificado - no caso de o emitente não estar especificado ou vazio, o certificado será aceite para autenticação se a sua cadeia puder ser construída, e acaba numa raiz fidedigna pelo validador. Se o emitente for especificado, o certificado será aceite se a impressão digital do seu emitente direto corresponder a qualquer um dos valores especificados neste campo - independentemente de a raiz ser ou não fidedigna. Por favor, note que um PKI pode usar diferentes autoridades de certificação para emitir certificados para o mesmo assunto, e por isso é importante especificar todas as impressões digitais emitentes esperadas para um determinado assunto.
   >
   > Especificar o emitente é considerado uma boa prática; ao omiti-lo continuará a funcionar - para certificados acorrentados até uma raiz de confiança - este comportamento tem limitações e pode ser eliminado gradualmente num futuro próximo. Note-se também que os clusters implantados em Azure, e protegidos com certificados X509 emitidos por um PKI privado e declarados por sujeitos podem não ser validados pelo serviço Azure Service Fabric (para comunicação cluster-to-service), se a Política de Certificados do PKI não for detetável, disponível e acessível. 

## <a name="deploy-the-updated-template"></a>Implementar o modelo atualizado
Reimplante o modelo atualizado após efetuar as alterações.

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
* Conheça a [segurança do cluster.](service-fabric-cluster-security.md)
* Saiba como [capotar um certificado de cluster](service-fabric-cluster-rollover-cert-cn.md)
* [Atualizar e Gerir certificados de cluster](service-fabric-cluster-security-update-certs-azure.md)
* Simplificar a Gestão de [Certificados mudando o cluster da impressão digital do certificado para o nome comum](service-fabric-cluster-change-cert-thumbprint-to-cn.md)

[image1]: .\media\service-fabric-cluster-change-cert-thumbprint-to-cn\PortalViewTemplates.png
