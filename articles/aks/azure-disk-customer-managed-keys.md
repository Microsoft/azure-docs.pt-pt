---
title: Usar uma chave gerenciada pelo cliente para criptografar discos do Azure no serviço kubernetes do Azure (AKS)
description: Traga suas próprias chaves (BYOK) para criptografar o sistema operacional AKS e OS discos de dados.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 01/09/2020
ms.author: mlearned
ms.openlocfilehash: 3efb7a6005d862b15beec0f979b67a701a26ba74
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75903967"
---
# <a name="bring-your-own-keys-byok-with-azure-disks-in-azure-kubernetes-service-aks"></a>Traga suas próprias chaves (BYOK) com discos do Azure no serviço kubernetes do Azure (AKS)

O armazenamento do Azure criptografa todos os dados em uma conta de armazenamento em repouso. Por padrão, os dados são criptografados com chaves gerenciadas pela Microsoft. Para obter mais controle sobre as chaves de criptografia, você pode fornecer [chaves gerenciadas pelo cliente][customer-managed-keys] para usar na criptografia do sistema operacional e dos discos de dados para seus clusters AKs.

> [!NOTE]
> Os clusters AKS baseados em Linux e Windows têm suporte.

## <a name="before-you-begin"></a>Antes de começar

* Este artigo pressupõe que você está criando um *novo cluster AKs*.  Você também precisará usar ou criar uma instância do Azure Key Vault para armazenar suas chaves de criptografia.

* Você deve habilitar a exclusão reversível e limpar a proteção para *Azure Key Vault* ao usar Key Vault para criptografar discos gerenciados.

* Você precisa do CLI do Azure versão 2.0.79 ou posterior e a extensão 0.4.26 AKs-Preview

> [!IMPORTANT]
> Os recursos de visualização do AKS são consentimento de autoatendimento. As visualizações são fornecidas "no estado em que se encontram" e "como disponíveis" e são excluídas dos contratos de nível de serviço e da garantia limitada. As visualizações do AKS são parcialmente cobertas pelo suporte ao cliente com base no melhor esforço. Dessa forma, esses recursos não são destinados ao uso em produção. Para obter outras incompatibilidades, consulte os seguintes artigos de suporte:
>
> * [Políticas de suporte do AKS](support-policies.md)
> * [Perguntas frequentes do suporte do Azure](faq.md)

## <a name="install-latest-aks-cli-preview-extension"></a>Instalar a última extensão de visualização da CLI do AKS

Para usar chaves gerenciadas pelo cliente, você precisa da extensão da CLI do *AKs* versão 0.4.26 ou superior. Instale a extensão de CLI do Azure *de AKs-Preview* usando o comando [AZ Extension Add][az-extension-add] e, em seguida, verifique se há atualizações disponíveis usando o comando [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="create-an-azure-key-vault-instance-to-store-your-keys"></a>Criar uma instância de Azure Key Vault para armazenar suas chaves

Opcionalmente, você pode usar o portal do Azure para [Configurar chaves gerenciadas pelo cliente com Azure Key Vault][byok-azure-portal]

Crie um novo *grupo de recursos*, crie uma nova instância de *Key Vault* e habilite a exclusão reversível e limpe a proteção.

```azurecli-interactive
# Optionally retrieve Azure region short names for use on upcoming commands
az account list-locations

# Create new resource group in a supported Azure region
az group create -l myAzureRegionName -n myResourceGroup

# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n myKeyVaultName -g myResourceGroup-l myAzureRegionName  --enable-purge-protection true --enable-soft-delete true
```

## <a name="create-an-instance-of-a-diskencryptionset"></a>Criar uma instância de um DiskEncryptionSet

Você precisará de uma *chave* armazenada em Azure Key Vault para concluir as etapas a seguir.  Armazene sua chave existente no Key Vault que você criou ou [gere uma chave][key-vault-generate]
    
```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId=$(az keyvault show --name myKeyVaultName --query [id] -o tsv)

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl=$(az keyvault key show --vault-name myKeyVaultName  --name myKeyName  --query [key.kid] -o tsv)

# Create a DiskEncryptionSet
az disk-encryption-set create -n myDiskEncryptionSetName  -l myAzureRegionName  -g myResourceGroup--source-vault $keyVaultId --key-url $keyVaultKeyUrl 
```

## <a name="grant-the-diskencryptionset-resource-access-to-the-key-vault"></a>Conceder ao recurso DiskEncryptionSet acesso ao cofre de chaves

Use o DiskEncryptionSet e os grupos de recursos que você criou nas etapas anteriores e conceda ao recurso DiskEncryptionSet acesso ao Azure Key Vault.

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
desIdentity=$(az disk-encryption-set show -n myDiskEncryptionSetName  -g myResourceGroup--query [identity.principalId] -o tsv)

# Update security policy settings
az keyvault set-policy -n myKeyVaultName -g myResourceGroup--object-id $desIdentity --key-permissions wrapkey unwrapkey get

# Assign the reader role
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId
```

## <a name="create-a-new-aks-cluster-and-encrypt-the-os-disk-with-a-customer-manged-key"></a>Criar um novo cluster AKS e criptografar o disco do sistema operacional com uma chave gerenciada pelo cliente

Crie um novo grupo de recursos e cluster AKS e, em seguida, use sua chave para criptografar o disco do sistema operacional.

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
diskEncryptionSetId=$(az resource show -n $diskEncryptionSetName -g ssecmktesting --resource-type "Microsoft.Compute/diskEncryptionSets" --query [id] -o tsv)

# Create a resource group for the AKS cluster
az group create -n myResourceGroup-l myAzureRegionName

# Create the AKS cluster
az aks create -n myAKSCluster -g myResourceGroup --node-osdisk-diskencryptionsetid diskEncryptionId
```

## <a name="add-a-node-pool-to-an-existing-aks-cluster-and-encrypt-the-os-disk-with-a-customer-managed-key"></a>Adicionar um pool de nós a um cluster AKS existente e criptografar o disco do sistema operacional com uma chave gerenciada pelo cliente

Os novos nodepools não usam discos criptografados por padrão.  Você pode adicionar um novo pool de nós a um cluster existente e criptografar o disco do sistema operacional com sua própria chave usando o comando a seguir.

```azurecli-interactive
# Add a nodepool to an existing cluster with BYOK encryption
nodepool add –-cluster-name myAKSCluster -n myNodePoolName -g myResourceGroup --node-osdisk-diskencryptionsetid diskEncryptionId  
```

## <a name="encrypt-your-aks-cluster-data-disk-with-a-customer-managed-key"></a>Criptografar o disco de dados do cluster AKS com uma chave gerenciada pelo cliente

Você também pode criptografar os discos de dados AKS com suas próprias chaves.  Substitua MyResource e myDiskEncryptionSetName por seus valores reais e aplique o YAML.

### <a name="deploy-the-sample-image-from-acr-to-aks"></a>Implantar a imagem de exemplo do ACR para o AKS

Verifique se você tem as credenciais de AKS adequadas

Crie um arquivo chamado **byok-Azure-Disk. YAML** que contém as informações a seguir.  Substitua MyResource e myDiskEncrptionSetName por seus valores.

```
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: hdd
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Standard_LRS
  kind: managed
  diskEncryptionSetID: "/subscriptions/{subs-id}/resourceGroups/{myResourceGroup}/providers/Microsoft.Compute/diskEncryptionSets/{myDiskEncryptionSetName}"
```
Em seguida, execute esta implantação em seu cluster AKS:
```azurecli-interactive
kubectl apply -f byok-azure-disk.yaml
```

## <a name="limitations"></a>Limitações

* Criptografia de disco do so com suporte com a versão 1,17 e superior do kubernetes   
* Disponível somente em regiões em que há suporte para BYOK
* Atualmente, no momento para novos clusters AKS, os clusters existentes não podem ser atualizados
* O cluster AKS usando conjuntos de dimensionamento de máquinas virtuais é necessário, não há suporte para conjuntos de disponibilidade de máquina virtual


## <a name="next-steps"></a>Passos seguintes

Examinar [as práticas recomendadas para segurança de cluster AKs][best-practices-security]

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: /azure/aks/operator-best-practices-cluster-security
[byok-azure-portal]: /azure/storage/common/storage-encryption-keys-portal
[customer-managed-keys]: /azure/virtual-machines/windows/disk-encryption#customer-managed-keys-public-preview
[key-vault-generate]: /azure/key-vault/key-vault-manage-with-cli2
