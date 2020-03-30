---
title: Utilize uma chave gerida pelo cliente para encriptar discos Azure no Serviço Azure Kubernetes (AKS)
description: Traga as suas próprias chaves (BYOK) para encriptar os discos AKS OS e Data.
services: container-service
ms.topic: article
ms.date: 01/12/2020
ms.openlocfilehash: bb6ba5e6dd4ace9e33043079c0f435c10baf5cb2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596509"
---
# <a name="bring-your-own-keys-byok-with-azure-disks-in-azure-kubernetes-service-aks"></a>Traga as suas próprias chaves (BYOK) com discos Azure no Serviço Azure Kubernetes (AKS)

O Azure Storage encripta todos os dados numa conta de armazenamento em repouso. Por padrão, os dados são encriptados com chaves geridas pela Microsoft. Para um controlo adicional sobre as chaves de encriptação, pode fornecer [chaves geridas pelo cliente][customer-managed-keys] para usar para encriptação em repouso tanto para o SISTEMA como para os discos de dados para os seus clusters AKS.

> [!NOTE]
> Os clusters AKS baseados em BYOK Linux e Windows estão disponíveis nas [regiões do Azure][supported-regions] que suportam a encriptação lateral do servidor dos discos geridos pelo Azure.

## <a name="before-you-begin"></a>Antes de começar

* Este artigo assume que está a criar um *novo cluster AKS.*

* Deve ativar uma proteção suave para eliminar e purgar a proteção para *o Cofre de Chaves Azure* ao utilizar o Cofre chave para encriptar discos geridos.

* Você precisa da versão Azure CLI 2.0.79 ou mais tarde e a extensão aks-preview 0.4.26

> [!IMPORTANT]
> As funcionalidades de pré-visualização AKS são opt-in self-service. As pré-visualizações são fornecidas "as-is" e "conforme disponível" e estão excluídas dos acordos de nível de serviço e da garantia limitada. As pré-visualizações aks são parcialmente cobertas pelo apoio ao cliente na melhor base de esforço. Como tal, estas características não se destinam à utilização da produção. Para obter informações adicionais, consulte os seguintes artigos de apoio:
>
> * [Políticas de apoio aks](support-policies.md)
> * [FaQ de suporte azure](faq.md)

## <a name="install-latest-aks-cli-preview-extension"></a>Instale a mais recente extensão de pré-visualização AKS CLI

Para utilizar as chaves geridas pelo cliente, necessita da versão de extensão CLI de *pré-visualização* de aks 0.4.26 ou superior. Instale a extensão Azure CLI de *pré-visualização de aks* utilizando o comando de adição de [extensão az][az-extension-add] e, em seguida, verifique se há atualizações disponíveis utilizando o comando de atualização de [extensão az:][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="create-an-azure-key-vault-instance"></a>Crie uma instância de cofre de chave azure

Utilize uma instância do Cofre de Chaves Azure para armazenar as suas chaves.  Você pode opcionalmente usar o portal Azure para [configurar chaves geridas pelo cliente com cofre][byok-azure-portal] de chave Azure

Crie um novo grupo de *recursos,* em seguida, crie uma nova instância *key vault* e permita eliminar suavemente e purgar a proteção.  Certifique-se de que utiliza os nomes de grupos da mesma região e recursos para cada comando.

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

Substitua o *meu Nome KeyVault* pelo nome do seu cofre chave.  Também necessitará de uma *chave* armazenada no Cofre de Chaves Azure para completar os seguintes passos.  Ou armazena a chave existente no Cofre de Chaves que criou nos passos anteriores, ou [gera uma nova tecla][key-vault-generate] e substitui o *myKeyName* abaixo com o nome da sua chave.
    
```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId=$(az keyvault show --name myKeyVaultName --query [id] -o tsv)

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl=$(az keyvault key show --vault-name myKeyVaultName  --name myKeyName  --query [key.kid] -o tsv)

# Create a DiskEncryptionSet
az disk-encryption-set create -n myDiskEncryptionSetName  -l myAzureRegionName  -g myResourceGroup --source-vault $keyVaultId --key-url $keyVaultKeyUrl 
```

## <a name="grant-the-diskencryptionset-access-to-key-vault"></a>Conceder o acesso do DiskEncryptionSet ao cofre da chave

Utilize o DiskEncryptionSet e os grupos de recursos que criou nos passos anteriores e conceda o acesso ao recurso DiskEncryptionSet para o Cofre de Chaves Azure.

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
desIdentity=$(az disk-encryption-set show -n myDiskEncryptionSetName  -g myResourceGroup --query [identity.principalId] -o tsv)

# Update security policy settings
az keyvault set-policy -n myKeyVaultName -g myResourceGroup --object-id $desIdentity --key-permissions wrapkey unwrapkey get

# Assign the reader role
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId
```

## <a name="create-a-new-aks-cluster-and-encrypt-the-os-disk"></a>Crie um novo cluster AKS e criptografe o disco OS

Crie um **novo grupo** de recursos e um cluster AKS e, em seguida, use a sua chave para encriptar o disco OS. As chaves geridas pelo cliente só são suportadas nas versões Kubernetes superiores a 1.17. 

> [!IMPORTANT]
> Certifique-se de criar um novo grupo de resoruce para o seu cluster AKS

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
diskEncryptionSetId=$(az resource show -n mydiskEncryptionSetName -g myResourceGroup --resource-type "Microsoft.Compute/diskEncryptionSets" --query [id] -o tsv)

# Create a resource group for the AKS cluster
az group create -n myResourceGroup -l myAzureRegionName

# Create the AKS cluster
az aks create -n myAKSCluster -g myResourceGroup --node-osdisk-diskencryptionset-id $diskEncryptionSetId --kubernetes-version 1.17.0 --generate-ssh-keys
```

Quando novos conjuntos de nós são adicionados ao cluster criado acima, a chave gerida pelo cliente fornecida durante a criação é usada para encriptar o disco OS.

## <a name="encrypt-your-aks-cluster-data-disk"></a>Criptografe o seu disco de dados do cluster AKS

Também pode encriptar os discos de dados AKS com as suas próprias chaves.

> [!IMPORTANT]
> Certifique-se de que tem as credenciais AKS adequadas. O diretor do Serviço terá de ter acesso ao grupo de recursos onde o conjunto de encriptação do disco está implantado. Caso contrário, terá um erro que sugere que o diretor de serviço não tem permissões.

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

Crie um ficheiro chamado **byok-azure-disk.yaml** que contenha as seguintes informações.  Substitua o myAzureSubscriptionId, o myResourceGroup e o myDiskEncrptionSetName com os seus valores e aplique o yaml.  Certifique-se de utilizar o grupo de recursos onde o seu DiskCryptonSet está implantado.  Se utilizar a Casca de Nuvem Azure, este ficheiro pode ser criado utilizando vi ou nano como se funcionasse num sistema virtual ou físico:

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
Em seguida, execute esta implantação no seu cluster AKS:
```azurecli-interactive
# Get credentials
az aks get-credentials --name myAksCluster --resource-group myResourceGroup --output table

# Update cluster
kubectl apply -f byok-azure-disk.yaml
```

## <a name="limitations"></a>Limitações

* BYOK só está disponível atualmente em GA e Preview em certas [regiões de Azure][supported-regions]
* Encriptação de disco ossuportado com versão Kubernetes 1.17 e acima   
* Disponível apenas em regiões onde o BYOK é apoiado
* A encriptação com chaves geridas pelo cliente atualmente é apenas para novos clusters AKS, clusters existentes não podem ser atualizados
* Cluster AKS utilizando conjuntos de escala de máquina virtual são necessários, sem suporte para conjuntos de disponibilidade de máquinas virtuais


## <a name="next-steps"></a>Passos seguintes

Rever [as melhores práticas para a segurança do cluster AKS][best-practices-security]

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: /azure/aks/operator-best-practices-cluster-security
[byok-azure-portal]: /azure/storage/common/storage-encryption-keys-portal
[customer-managed-keys]: /azure/virtual-machines/windows/disk-encryption#customer-managed-keys
[key-vault-generate]: /azure/key-vault/key-vault-manage-with-cli2
[supported-regions]: /azure/virtual-machines/windows/disk-encryption#supported-regions
