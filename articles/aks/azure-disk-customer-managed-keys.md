---
title: Usar uma chave gerenciada pelo cliente para criptografar discos do Azure no serviço kubernetes do Azure (AKS)
description: Traga suas próprias chaves (BYOK) para criptografar o sistema operacional AKS e OS discos de dados.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 01/12/2020
ms.author: mlearned
ms.openlocfilehash: 1852ba059d13a8886b739cac439798b4d031a531
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76932626"
---
# <a name="bring-your-own-keys-byok-with-azure-disks-in-azure-kubernetes-service-aks"></a>Traga suas próprias chaves (BYOK) com discos do Azure no serviço kubernetes do Azure (AKS)

O armazenamento do Azure criptografa todos os dados em uma conta de armazenamento em repouso. Por padrão, os dados são criptografados com chaves gerenciadas pela Microsoft. Para um controlo adicional sobre as chaves de encriptação, pode fornecer [chaves geridas pelo cliente][customer-managed-keys] para usar para encriptação em repouso tanto para o SISTEMA como para os discos de dados para os seus clusters AKS.

> [!NOTE]
> Os clusters AKS baseados em Linux e Windows BYOK estão disponíveis em [regiões do Azure][supported-regions] que dão suporte à criptografia do lado do servidor de discos gerenciados do Azure.

## <a name="before-you-begin"></a>Antes de começar

* Este artigo pressupõe que você está criando um *novo cluster AKs*.

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

## <a name="create-an-azure-key-vault-instance"></a>Criar uma instância de Azure Key Vault

Use uma instância de Azure Key Vault para armazenar suas chaves.  Opcionalmente, você pode usar o portal do Azure para [Configurar chaves gerenciadas pelo cliente com Azure Key Vault][byok-azure-portal]

Crie um novo *grupo de recursos*, crie uma nova instância de *Key Vault* e habilite a exclusão reversível e limpe a proteção.  Certifique-se de usar os mesmos nomes de região e grupo de recursos para cada comando.

```azurecli-interactive
# Optionally retrieve Azure region short names for use on upcoming commands
az account list-locations
```

```azurecli-interactive
# Create new resource group in a supported Azure region
az group create -l myAzureRegionName -n myResourceGroup

# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n myKeyVaultName -g myResourceGroup -l myAzureRegionName  --enable-purge-protection true --enable-soft-delete true
```

## <a name="create-an-instance-of-a-diskencryptionset"></a>Criar uma instância de um DiskEncryptionSet

Substitua *myKeyVaultName* pelo nome do cofre de chaves.  Você também precisará de uma *chave* armazenada em Azure Key Vault para concluir as etapas a seguir.  Armazene sua chave existente no Key Vault criado nas etapas anteriores ou [gere uma nova chave][key-vault-generate] e substitua *MyKeyName* abaixo pelo nome da sua chave.
    
```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId=$(az keyvault show --name myKeyVaultName --query [id] -o tsv)

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl=$(az keyvault key show --vault-name myKeyVaultName  --name myKeyName  --query [key.kid] -o tsv)

# Create a DiskEncryptionSet
az disk-encryption-set create -n myDiskEncryptionSetName  -l myAzureRegionName  -g myResourceGroup --source-vault $keyVaultId --key-url $keyVaultKeyUrl 
```

## <a name="grant-the-diskencryptionset-access-to-key-vault"></a>Conceder ao DiskEncryptionSet acesso ao cofre de chaves

Use o DiskEncryptionSet e os grupos de recursos que você criou nas etapas anteriores e conceda ao recurso DiskEncryptionSet acesso ao Azure Key Vault.

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
desIdentity=$(az disk-encryption-set show -n myDiskEncryptionSetName  -g myResourceGroup --query [identity.principalId] -o tsv)

# Update security policy settings
az keyvault set-policy -n myKeyVaultName -g myResourceGroup --object-id $desIdentity --key-permissions wrapkey unwrapkey get

# Assign the reader role
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId
```

## <a name="create-a-new-aks-cluster-and-encrypt-the-os-disk"></a>Criar um novo cluster AKS e criptografar o disco do sistema operacional

Crie um **novo grupo de recursos** e cluster AKs e, em seguida, use sua chave para criptografar o disco do sistema operacional. As chaves gerenciadas pelo cliente só têm suporte em versões kubernetes maiores que 1,17. 

> [!IMPORTANT]
> Certifique-se de criar um novo grupo recurso para o cluster AKS

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
diskEncryptionSetId=$(az resource show -n mydiskEncryptionSetName -g myResourceGroup --resource-type "Microsoft.Compute/diskEncryptionSets" --query [id] -o tsv)

# Create a resource group for the AKS cluster
az group create -n myResourceGroup -l myAzureRegionName

# Create the AKS cluster
az aks create -n myAKSCluster -g myResourceGroup --node-osdisk-diskencryptionset-id $diskEncryptionSetId --kubernetes-version 1.17.0 --generate-ssh-keys
```

Quando novos pools de nós são adicionados ao cluster criado acima, a chave gerenciada pelo cliente fornecida durante a criação é usada para criptografar o disco do sistema operacional.

## <a name="encrypt-your-aks-cluster-data-disk"></a>Criptografar o disco de dados do cluster AKS

Você também pode criptografar os discos de dados AKS com suas próprias chaves.

> [!IMPORTANT]
> Verifique se você tem as credenciais de AKS adequadas. A entidade de serviço precisará ter acesso de colaborador ao grupo de recursos em que o diskencryptionset é implantado. Caso contrário, você receberá um erro sugerindo que a entidade de serviço não tem permissões.

```azurecli-interactive
# Retrieve your Azure Subscription Id from id property as shown below
az account list
```

```
someuser@Azure:~$ az account list
[
  {
    "cloudName": "AzureCloud",
    "id": "666e66d8-1e43-4136-be25-f25bb5de5893",
    "isDefault": true,
    "name": "MyAzureSubscription",
    "state": "Enabled",
    "tenantId": "3ebbdf90-2069-4529-a1ab-7bdcb24df7cd",
    "user": {
      "cloudShellID": true,
      "name": "someuser@azure.com",
      "type": "user"
    }
  }
]
```

Crie um arquivo chamado **byok-Azure-Disk. YAML** que contém as informações a seguir.  Substitua myAzureSubscriptionId, MyResource e myDiskEncrptionSetName por seus valores e aplique o YAML.  Certifique-se de usar o grupo de recursos onde seu DiskEncryptionSet está implantado.  Se você usar o Azure Cloud Shell, esse arquivo poderá ser criado usando vi ou nano como se estiver trabalhando em um sistema físico ou virtual:

```
kind: StorageClass
apiVersion: storage.k8s.io/v1  
metadata:
  name: hdd
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Standard_LRS
  kind: managed
  diskEncryptionSetID: "/subscriptions/{myAzureSubscriptionId}/resourceGroups/{myResourceGroup}/providers/Microsoft.Compute/diskEncryptionSets/{myDiskEncryptionSetName}"
```
Em seguida, execute esta implantação em seu cluster AKS:
```azurecli-interactive
# Get credentials
az aks get-credentials --name myAksCluster --resource-group myResourceGroup --output table

# Update cluster
kubectl apply -f byok-azure-disk.yaml
```

## <a name="limitations"></a>Limitações

* O BYOK só está disponível no GA e na versão prévia em determinadas [regiões do Azure][supported-regions]
* Criptografia de disco do so com suporte com a versão 1,17 e superior do kubernetes   
* Disponível somente em regiões em que há suporte para BYOK
* A criptografia com chaves gerenciadas pelo cliente atualmente é para novos clusters AKS somente, os clusters existentes não podem ser atualizados
* O cluster AKS usando conjuntos de dimensionamento de máquinas virtuais é necessário, não há suporte para conjuntos de disponibilidade de máquina virtual


## <a name="next-steps"></a>Passos seguintes

Examinar [as práticas recomendadas para segurança de cluster AKs][best-practices-security]

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: /azure/aks/operator-best-practices-cluster-security
[byok-azure-portal]: /azure/storage/common/storage-encryption-keys-portal
[customer-managed-keys]: /azure/virtual-machines/windows/disk-encryption#customer-managed-keys
[key-vault-generate]: /azure/key-vault/key-vault-manage-with-cli2
[supported-regions]: /azure/virtual-machines/windows/disk-encryption#supported-regions
