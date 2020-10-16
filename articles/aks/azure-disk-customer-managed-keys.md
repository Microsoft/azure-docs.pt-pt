---
title: Utilize uma chave gerida pelo cliente para encriptar discos Azure no Serviço Azure Kubernetes (AKS)
description: Traga as suas próprias chaves (BYOK) para encriptar os discos AKS OS e Data.
services: container-service
ms.topic: article
ms.date: 09/01/2020
ms.openlocfilehash: 8d0f66d034bb0566674e521ced3e3887e29e3aaa
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92071732"
---
# <a name="bring-your-own-keys-byok-with-azure-disks-in-azure-kubernetes-service-aks"></a>Traga as suas próprias chaves (BYOK) com discos Azure no Serviço Azure Kubernetes (AKS)

O Azure Storage encripta todos os dados numa conta de armazenamento em repouso. Por predefinição, os dados são encriptados com as teclas geridas pela Microsoft. Para um controlo adicional sobre as chaves de encriptação, pode fornecer chaves geridas pelo cliente para usar para encriptação em repouso tanto para o SISTEMA como para os discos de dados para os seus clusters AKS. Saiba mais sobre as chaves geridas pelo cliente no [Linux][customer-managed-keys-linux] e [windows][customer-managed-keys-windows].

## <a name="limitations"></a>Limitações
* O suporte de encriptação do disco de dados está limitado a clusters AKS que executam a versão 1.17 e acima de Kubernetes.
* A encriptação do SISTEMA e do disco de dados com chaves geridas pelo cliente só pode ser ativada na criação de um cluster AKS.

## <a name="prerequisites"></a>Pré-requisitos
* Tem de ativar a proteção para a eliminação e purga suave do *Cofre da Chave Azure* quando utilizar o Cofre de Chaves para encriptar discos geridos.
* Precisa da versão 2.11.1 ou posterior do Azure CLI.

## <a name="create-an-azure-key-vault-instance"></a>Criar uma instância Azure Key Vault

Utilize uma instância Azure Key Vault para guardar as suas chaves.  Pode utilizar opcionalmente o portal Azure para [configurar chaves geridas pelo cliente com cofre de chaves Azure][byok-azure-portal]

Crie um novo *grupo de recursos,* em seguida, crie uma nova instância *Key Vault* e permita a proteção de eliminação e purga suave.  Certifique-se de que utiliza os mesmos nomes de grupos de recursos e região para cada comando.

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

## <a name="create-an-instance-of-a-diskencryptionset"></a>Criar um exemplo de um DiskEncrypationSet

Substitua *o meu Nome ChaveVault* pelo nome do seu cofre de chaves.  Também necessitará de uma *chave* armazenada no Cofre da Chave Azure para completar os seguintes passos.  Ou armazena a chave existente no Cofre de Chaves que criou nos passos anteriores, ou [gera uma nova chave][key-vault-generate] e substitui o *myKeyName* abaixo pelo nome da sua chave.
    
```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId=$(az keyvault show --name myKeyVaultName --query [id] -o tsv)

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl=$(az keyvault key show --vault-name myKeyVaultName  --name myKeyName  --query [key.kid] -o tsv)

# Create a DiskEncryptionSet
az disk-encryption-set create -n myDiskEncryptionSetName  -l myAzureRegionName  -g myResourceGroup --source-vault $keyVaultId --key-url $keyVaultKeyUrl 
```

## <a name="grant-the-diskencryptionset-access-to-key-vault"></a>Conceder ao DiskEncrypationSet acesso ao cofre de chaves

Utilize o DiskEncryptionSet e os grupos de recursos que criou nos passos anteriores e conceda ao diskEncrypationSet acesso ao Cofre da Chave Azure.

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
desIdentity=$(az disk-encryption-set show -n myDiskEncryptionSetName  -g myResourceGroup --query [identity.principalId] -o tsv)

# Update security policy settings
az keyvault set-policy -n myKeyVaultName -g myResourceGroup --object-id $desIdentity --key-permissions wrapkey unwrapkey get
```

## <a name="create-a-new-aks-cluster-and-encrypt-the-os-disk"></a>Crie um novo cluster AKS e criptografe o disco OS

Crie um **novo grupo de recursos** e um cluster AKS e, em seguida, use a sua chave para encriptar o disco OS. As chaves geridas pelo cliente só são suportadas nas versões Kubernetes superiores a 1.17. 

> [!IMPORTANT]
> Certifique-se de criar um novo grupo de resoruce para o seu cluster AKS

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
diskEncryptionSetId=$(az disk-encryption-set show -n mydiskEncryptionSetName -g myResourceGroup --query [id] -o tsv)

# Create a resource group for the AKS cluster
az group create -n myResourceGroup -l myAzureRegionName

# Create the AKS cluster
az aks create -n myAKSCluster -g myResourceGroup --node-osdisk-diskencryptionset-id $diskEncryptionSetId --kubernetes-version KUBERNETES_VERSION --generate-ssh-keys
```

Quando novos conjuntos de nós são adicionados ao cluster criado acima, a chave gerida pelo cliente fornecida durante a criação é usada para encriptar o disco de SO.

## <a name="encrypt-your-aks-cluster-data-diskoptional"></a>Criptografe o seu disco de dados de cluster AKS (opcional)
A chave de encriptação do disco de OS será usada para encriptar o disco de dados se não for fornecida a chave para o disco de dados a partir de v1.17.2, e também pode encriptar discos de dados AKS com as outras teclas.

> [!IMPORTANT]
> Certifique-se de que tem as credenciais AKS adequadas. O diretor do Serviço terá de ter acesso ao grupo de recursos onde o diskencrypationset é implantado. Caso contrário, terá um erro sugerindo que o principal do serviço não tem permissões.

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

Crie um ficheiro chamado **byok-azure-disk.yaml** que contenha as seguintes informações.  Substitua o myAzureSubscriptionId, myResourceGroup e myDiskEncrptionSetName pelos seus valores e aplique o yaml.  Certifique-se de que utiliza o grupo de recursos onde o seu DiskEncrypationSet está implantado.  Se utilizar o Azure Cloud Shell, este ficheiro pode ser criado usando vi ou nano como se trabalhasse num sistema virtual ou físico:

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
Em seguida, execute esta implementação no seu cluster AKS:
```azurecli-interactive
# Get credentials
az aks get-credentials --name myAksCluster --resource-group myResourceGroup --output table

# Update cluster
kubectl apply -f byok-azure-disk.yaml
```

## <a name="next-steps"></a>Passos seguintes

Rever [as melhores práticas para a segurança do cluster AKS][best-practices-security]

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: ./operator-best-practices-cluster-security.md
[byok-azure-portal]: ../storage/common/customer-managed-keys-configure-key-vault.md
[customer-managed-keys-windows]: ../virtual-machines/windows/disk-encryption.md#customer-managed-keys
[customer-managed-keys-linux]: ../virtual-machines/linux/disk-encryption.md#customer-managed-keys
[key-vault-generate]: ../key-vault/general/manage-with-cli2.md
[supported-regions]: ../virtual-machines/windows/disk-encryption.md#supported-regions